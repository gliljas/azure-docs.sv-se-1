---
title: Azure-front dörr | Microsoft Docs
description: Den här artikeln innehåller en översikt för Azure Front Door. Ta reda på om det är rätt val för belastnings utjämning av användar trafik för ditt program.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 4574597c0b93f2985953bfbc815cca220ecc4f28
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515603"
---
# <a name="configure-your-rules-engine"></a>Konfigurera din regel motor 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="configure-rules-engine-in-azure-portal"></a>Konfigurera regel motor i Azure Portal 
1. [Skapa en frontend-dörr](quickstart-create-front-door.md)innan du skapar en regel Motors konfiguration.

2. I din frontend-resurs går du till **Inställningar** och väljer **regel motor konfiguration**. Klicka på **Lägg till**, ge konfigurationen ett namn och börja skapa din första regel motor konfiguration. 

![Hitta regel motor](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

3. Klicka på **Lägg till regel** för att skapa din första regel. Sedan kan du definiera regeln genom att klicka på **Lägg till villkor** eller **Lägg till åtgärd** . 
    
    *Noter*
    - Om du vill ta bort ett villkor eller en åtgärd från en regel kan du använda pappers korgen till höger om det aktuella villkoret eller åtgärden.
    - Om du vill skapa en regel som gäller för all inkommande trafik ska du inte ange några villkor. 
    - Om du vill sluta utvärdera reglerna när det första matchnings villkoret är uppfyllt kontrollerar du regeln för att **utvärdera**. 

![Hitta regel motor](./media/front-door-rules-engine/rules-engine-tutorial-4.png)

4. Fastställ prioriteten för reglerna i konfigurationen med knapparna Flytta upp, flytta ned och flytta till överst. Prioriteten är i stigande ordning, vilket innebär att regeln först anges är den viktigaste regeln. 

5. När du har skapat en eller flera regler trycker du på **Spara**. Den här åtgärden skapar en regel motor konfiguration. 

6. När du har skapat en eller flera konfigurationer associerar du en regel motor konfiguration med en väg regel. Även om en enskild konfiguration kan tillämpas på många väg regler får en väg regel bara innehålla en regel motor konfiguration. Om du vill skapa en Association går du till**Dirigerings reglerna**för din **front dörr design** > . Välj den flödes regel som du vill lägga till regel motor konfigurationen till, gå till **flödes information** > **regel motor konfiguration**och välj den konfiguration som du vill koppla. 

![Hitta regel motor](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Konfigurera regel motor i Azure CLI 

1. Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)om du inte redan gjort det. Lägg till tillägget "frontend-dörr":-AZ Extension Add--Name front-dörr. Logga sedan in och växla till prenumerationen AZ Account set--Subscription <name_or_Id>. 

2. Börja med att skapa en regel motor – det här exemplet visar en regel med en rubrik-baserad åtgärd och ett matchnings villkor. 

```azurecli-interactive
az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
```

2.  Visa en lista med alla regler. 

```azurecli-interactive
az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
```

3.  Lägg till en åtgärd för åsidosättning av vidarebefordrande vägar. 

```azurecli-interactive
az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
```

4.  Visa en lista med alla åtgärder i en regel. 

```azurecli-interactive
az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
```

5. Länka en regel motor konfiguration till en regel för routning.  

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
```

6. Ta bort länk till regel motor. 

```azurecli-interactive
az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
```

För mer information, finns en fullständig lista över AFD-regler för motor kommandon som du hittar [här](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest).   

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [AFD-regel motorn](front-door-rules-engine.md). 
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
- Läs mer i AFD Rules Engine [CLI-referens](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest). 
- Läs mer i AFD-regel motor [PowerShell-referens](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
