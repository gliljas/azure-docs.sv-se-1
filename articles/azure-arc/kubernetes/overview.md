---
title: Azure Arc – aktiverade Kubernetes-översikt
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666260"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Vad är Azure Arc-aktiverade Kubernetes (för hands version)

Du kan ansluta och konfigurera Kubernetes-kluster i eller utanför Azure med Azure Arc Enabled Kubernetes (för hands version). När ett Kubernetes-kluster är kopplat till Azure-bågen visas det i Azure-portalen, har ett Azure Resource Manager-ID och en hanterad identitet. Kluster är anslutna till Azures standard prenumerationer, Live i en resurs grupp och kan ta emot Taggar precis som andra Azure-resurser. 


Att ansluta ett Kubernetes-kluster till Azure kräver att en kluster administratör distribuerar agenter. Dessa agenter körs i ett Kubernetes-namnområde med namnet `azure-arc` och är vanliga Kubernetes-distributioner. Agenterna ansvarar för anslutning till Azure, samlar in Azure Arc-loggar och-mått och tittar efter konfigurations begär Anden.  
 
 > [!NOTE]
> Azure Arc-aktiverade Kubernetes är i för hands version och rekommenderas inte för produktions arbets belastningar. 


## <a name="supported-scenarios"></a>Scenarier som stöds 

Azure Arc-aktiverade Kubernetes har stöd för följande scenarier: 

* Ansluta Kubernetes som körs utanför Azure för inventering, gruppering och taggning 

* Distribuera program och tillämpa konfigurationen med GitOps konfigurations hantering 

* Använd Azure Monitor för behållare för att visa och övervaka dina kluster 

* Tillämpa principer med Azure Policy för Kubernetes 

 
## <a name="supported-regions"></a>Regioner som stöds 

Azure Arc-aktiverade Kubernetes stöds för närvarande i följande regioner: 

* USA, östra 
* Europa, västra 


## <a name="next-steps"></a>Nästa steg

* [Anslut ett kluster](./connect-cluster.md)
