---
title: Rotera certifikat i Azure Kubernetes service (AKS)
description: Lär dig hur du roterar dina certifikat i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549075"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotera certifikat i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) används certifikat för autentisering med många av dess komponenter. Med jämna mellanrum kan du behöva rotera dessa certifikat för säkerhets-eller princip orsaker. Du kan till exempel ha en princip för att rotera alla dina certifikat var 90 dag.

Den här artikeln visar hur du roterar certifikaten i ditt AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.77 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-certifikat, certifikat utfärdare och tjänst konton

AKS genererar och använder följande certifikat, certifikat utfärdare och tjänst konton:

* AKS-API-servern skapar en certifikat utfärdare (CA) som kallas för kluster certifikat utfärdaren.
* API-servern har en kluster certifikat utfärdare som signerar certifikat för enkelriktad kommunikation från API-servern till kubelets.
* Varje kubelet skapar också en certifikat signerings förfrågan, som är signerad av kluster certifikat utfärdaren för kommunikation från kubelet till API-servern.
* Nyckel värdes arkivet etcd har ett certifikat som signerats av kluster certifikat utfärdaren för kommunikation från etcd till API-servern.
* Nyckel värdes arkivet etcd skapar en certifikat utfärdare som signerar certifikat för att autentisera och auktorisera datareplikering mellan etcd-replikeringar i AKS-klustret.
* API Aggregator använder kluster certifikat utfärdaren för att utfärda certifikat för kommunikation med andra API: er. API Aggregator kan också ha sin egen certifikat utfärdare för att utfärda dessa certifikat, men för närvarande används kluster certifikat utfärdaren.
* Varje nod använder en tjänst konto-token (SA) som är signerad av kluster certifikat utfärdaren.
* `kubectl` Klienten har ett certifikat för att kommunicera med AKS-klustret.

> [!NOTE]
> AKS-kluster som skapats före mars 2019 har certifikat som upphör att gälla efter två år. Alla kluster som skapats efter mars 2019 eller ett kluster som har sina certifikat roterade har kluster certifikat utfärdarens certifikat som upphör att gälla efter 30 år. Alla andra certifikat upphör att gälla efter två år. Om du vill kontrol lera när klustret har skapats `kubectl get nodes` kan du använda för att visa *ålder* för dina nodkonfigurationer.
> 
> Dessutom kan du kontrol lera förfallo datumet för klustrets certifikat. Följande kommando visar till exempel certifikat informationen för *myAKSCluster* -klustret.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Rotera dina kluster certifikat

> [!WARNING]
> Att rotera dina certifikat `az aks rotate-certs` med kan orsaka upp till 30 minuters stillestånds tid för ditt AKS-kluster.

Använd [AZ AKS get-credentials][az-aks-get-credentials] för att logga in på ditt AKS-kluster. Det här kommandot hämtar också och konfigurerar `kubectl` klient certifikatet på den lokala datorn.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Använd `az aks rotate-certs` för att rotera alla certifikat, certifikat utfärdare och SAS i klustret.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Det kan ta upp till 30 minuter `az aks rotate-certs` att slutföra. Om kommandot Miss lyckas innan du är klar använder `az aks show` du för att kontrol lera att klustrets status är ett certifikat som ska *roteras*. Om klustret är i ett felaktigt tillstånd, kör `az aks rotate-certs` om för att rotera dina certifikat igen.

Kontrol lera att de gamla certifikaten inte längre är giltiga genom `kubectl` att köra ett kommando. Eftersom du inte har uppdaterat certifikaten som används `kubectl`av visas ett fel meddelande.  Ett exempel:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Uppdatera certifikatet som används av `kubectl` genom att `az aks get-credentials`köra.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Kontrol lera att certifikaten har uppdaterats genom `kubectl` att köra ett kommando som nu kommer att lyckas. Ett exempel:

```console
kubectl get no
```

> [!NOTE]
> Om du har några tjänster som körs ovanpå AKS, till exempel [Azure dev Spaces][dev-spaces], kan du behöva [uppdatera certifikat som är relaterade till dessa tjänster][dev-spaces-rotate] också.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du automatiskt roterar ditt klusters certifikat, certifikat utfärdare och SAs. Du kan se [metod tips för kluster säkerhet och uppgraderingar i Azure Kubernetes service (AKS)][aks-best-practices-security-upgrades] om du vill ha mer information om rekommenderade säkerhets metoder för AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
