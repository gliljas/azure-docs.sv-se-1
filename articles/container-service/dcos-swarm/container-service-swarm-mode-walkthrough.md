---
title: FÖRÅLDRAD Snabb start – Azure Docker CE-kluster för Linux
description: Lär dig snabbt att skapa ett Docker CE-kluster på Linux-behållare i Azure Container Service med Azure CLI.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: d4bbd5560681aa73709019e87c6c22470a64ad78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481746"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>FÖRÅLDRAD Distribuera Docker CE-kluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här snabb starten distribueras ett Docker CE-kluster med hjälp av Azure CLI. Därefter distribuerar vi och kör ett flercontainerprogram som består av en webbklientdel och en Redis-instans i klustret. När vi har gjort det kan programmet nås via Internet.

Docker CE på Azure Container Service är en förhandsversion och **bör inte användas för produktionsarbete**.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på *westus2* -platsen.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Resultat:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Skapa Docker Swarm-kluster

Skapa ett Docker CE-kluster i Azure Container Service med kommandot [az acs create](/cli/azure/acs#az-acs-create). Information om regions tillgänglighet för Docker CE finns i [ACS-regioner för Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md)

I följande exempel skapas ett kluster med namnet *mySwarmCluster* med en Linux-huvudnod och tre Linux-agentnoder.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

I vissa fall, som vid en begränsad utvärderingsversion, har en Azure-prenumeration begränsad åtkomst till Azure-resurser. Om distributionen misslyckas på grund av begränsade tillgängliga kärnor minskar du antalet standardagenter genom att lägga till `--agent-count 1` till kommandot [az acs create](/cli/azure/acs#az-acs-create). 

Efter flera minuter slutförs kommandot och returnerar JSON-formaterad information om klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

I den här snabb starten behöver du FQDN för både Docker Swarm-huvudplatsen och Docker-agentens pool. Kör följande kommando för att returnera både huvudnods- och agent-FQDN.

```azurecli
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Resultat:

```output
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Skapa en SSH-tunnel till Swarm-huvudnoden. Ersätt `MasterFQDN` med FQDN-adressen för Swarm-huvudnoden.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Ange miljövariabeln `DOCKER_HOST`. På detta sätt kan du köra Docker-kommandon mot Docker Swarm utan att behöva ange värdnamnet.

```bash
export DOCKER_HOST=localhost:2374
```

Du är nu redo att köra Docker-tjänster på Docker Swarm.


## <a name="run-the-application"></a>Köra appen

Skapa en fil med namnet `azure-vote.yaml` och kopiera följande innehåll till den.


```yaml
version: '3'
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Kör kommandot [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) för att skapa Azure Vote-tjänsten.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Resultat:

```output
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Använd kommandot [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/) för att returnera distributionsstatusen för programmet.

```bash
docker stack ps azure-vote
```

När `CURRENT STATE` för varje tjänst är `Running` är programmet redo.

```output
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testa programmet

Gå till FQDN för Swarm-agentpoolen för att testa Azure Vote-programmet.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Ta bort klustret
När klustret inte längre behövs kan du använda kommandot [az group delete](/cli/azure/group#az-group-delete) för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Hämta koden

I den här snabb starten har tidigare skapade behållar avbildningar använts för att skapa en Docker-tjänst. Tillhörande programkod, Dockerfile och Compose-fil finns på GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett Docker Swarm-kluster och distribuerat ett program med flera behållare till det.

Om du vill lära dig att integrera Docker-Swarm med Azure DevOps kan du fortsätta till CI/CD med Docker Swarm och Azure DevOps.

> [!div class="nextstepaction"]
> [CI/CD med Docker Swarm och Azure DevOps](./container-service-docker-swarm-setup-ci-cd.md)
