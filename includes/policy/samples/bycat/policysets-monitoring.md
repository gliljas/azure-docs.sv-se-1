---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a1e58ebf1ec22bf05b07cbbb95e335b85e3690f4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639428"
---
|Name |Beskrivning |Principer |Version |
|---|---|---|---|
|[Aktivera Azure Monitor för Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Aktivera Azure Monitor för Virtual Machine Scale Sets i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. Obs! Om din skalnings uppsättnings upgradePolicy är inställd på manuell, måste du tillämpa tillägget på alla virtuella datorer i uppsättningen genom att anropa uppgraderingen på dem. I CLI är detta AZ VMSS Update-instances. |6 |1.0.1 |
|[Aktivera Azure Monitor for VMs](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Aktivera Azure Monitor för Virtual Machines (VM) i det angivna omfånget (hanterings grupp, prenumeration eller resurs grupp). Tar Log Analytics-arbetsyta som parameter. |6 |1.0.1 |
