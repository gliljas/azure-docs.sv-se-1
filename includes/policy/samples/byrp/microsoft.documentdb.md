---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 963cf77635e215a09bf1de6412aab1515108c4a7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659476"
---
|Name |Beskrivning |Påverkan (ar) |Version |GitHub |
|---|---|---|---|---|
|[Azure Cosmos DB tillåtna platser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |Med den här principen kan du begränsa vilka platser din organisation kan ange när du distribuerar Azure Cosmos DB-resurser. Den används för att genomdriva kraven på geo-efterlevnad. |[parameters (' policyEffect ')] |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Den här principen granskar alla Cosmos DB som inte har kon figurer ATS för att använda en tjänst slut punkt för virtuellt nätverk. |Granskning, inaktive rad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Distribuera Avancerat skydd för Cosmos DB-konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |Den här principen aktiverar Avancerat skydd mellan Cosmos DB-konton. |DeployIfNotExists, inaktiverat |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
