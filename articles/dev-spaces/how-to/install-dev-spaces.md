---
title: Aktivera Azure dev Spaces på AKS & installera verktyg på klient Sidan
services: azure-dev-spaces
ms.date: 07/24/2019
ms.topic: conceptual
description: Lär dig hur du aktiverar Azure dev Spaces i ett AKS-kluster och installerar verktyg på klient sidan.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s
ms.openlocfilehash: b62c4a4861529c19363f159b8cc64a32a0ba11e8
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996269"
---
# <a name="enable-azure-dev-spaces-on-an-aks-cluster-and-install-the-client-side-tools"></a>Aktivera Azure dev Spaces i ett AKS-kluster och installera verktyg på klient Sidan

Den här artikeln visar flera olika sätt att aktivera Azure dev Spaces på ett AKS-kluster samt installera verktyg på klient sidan.

## <a name="enable-or-remove-azure-dev-spaces-using-the-cli"></a>Aktivera eller ta bort Azure dev Spaces med CLI

Innan du kan aktivera dev Spaces med hjälp av CLI behöver du:
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto][az-portal-create-account].
* [Azure CLI installerat][install-cli].
* [Ett AKS-kluster][create-aks-cli] i en [region som stöds][supported-regions].

Använd `use-dev-spaces` kommandot för att aktivera dev Spaces på ditt AKS-kluster och följ anvisningarna.

```azurecli
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

Kommandot ovan aktiverar dev Spaces i *myAKSCluster* -klustret i gruppen *myResourceGroup* och skapar ett *standard* dev-utrymme.

```console
'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

`use-dev-spaces`Kommandot installerar även Azure dev Spaces cli.

Använd kommandot för att ta bort Azure dev Spaces från ditt AKS-kluster `azds remove` . Ett exempel:

```azurecli
$ azds remove -g MyResourceGroup -n MyAKS
Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup' will be deleted. This will remove Azure Dev Spaces instrumentation from the target resource for new workloads. Continue? (y/N): y

Deleting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAks' in resource group 'MyResourceGroup' (takes a few minutes)...
```

Kommandot ovan tar bort Azure dev Spaces från *MyAKS* -klustret i *MyResourceGroup*. Alla namn områden som du har skapat med Azure dev Spaces är kvar tillsammans med deras arbets belastningar, men nya arbets belastningar i dessa namn områden kommer inte att instrumenteras med Azure dev Spaces. Om du startar om alla befintliga poddar som är instrumenterade med Azure dev Spaces kan du dessutom se fel. Dessa poddar måste distribueras om utan Azure dev Spaces-verktyg. Om du vill ta bort alla Azure dev-utrymmen fullständigt från klustret tar du bort alla poddar i alla namn områden där Azure dev Spaces har Aktiver ATS.

## <a name="install-the-client-side-tools"></a>Installera verktyg på klient Sidan

Du kan använda Azure dev Spaces-verktyg på klient sidan för att interagera med dev-utrymmen i ett AKS-kluster från den lokala datorn. Det finns flera sätt att installera verktyg på klient sidan:

* I [Visual Studio Code][vscode]installerar du [tillägget Azure dev Spaces][vscode-extension].
* Installera arbets belastningen Azure Development i [Visual Studio 2019][visual-studio].
* Ladda ned och installera [Windows][cli-win], [Mac][cli-mac]eller [Linux][cli-linux] cli.

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Grupp utveckling i Azure dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
