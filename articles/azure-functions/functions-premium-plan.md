---
title: Azure Functions Premium-plan
description: Information och konfigurations alternativ (VNet, ingen kall start, obegränsad körnings tid) för Azure Functions Premium-planen.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276912"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions Premium-plan

Azure Functions Premium-planen (kallas ibland elastisk Premium-plan) är ett värd alternativ för Function-appar. Premium-prenumerationen innehåller funktioner som VNet-anslutning, ingen kall start och förstklassig maskin vara.  Flera Function-appar kan distribueras till samma Premium plan och med planen kan du konfigurera storlek på beräknings instanser, bas Plans storlek och maximal schema storlek.  En jämförelse av Premium-planen och andra plan-och värd typer finns i [funktions skala och värd alternativ](functions-scale.md).

## <a name="create-a-premium-plan"></a>Skapa en Premium-plan

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Du kan också skapa en Premium-plan med hjälp av [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) i Azure CLI. I följande exempel skapas en nivå plan för _elastisk Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

I det här exemplet ersätter `<RESOURCE_GROUP>` du med din resurs grupp `<PLAN_NAME>` och med ett namn för din plan som är unik i resurs gruppen. Ange ett [stöd `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions). Om du vill skapa en Premium-plan som stöder Linux `--is-linux` inkluderar du alternativet.

När planen har skapats kan du använda [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) för att skapa din Function-app. I portalen skapas både planen och appen på samma tidpunkt. Ett exempel på ett fullständigt Azure CLI-skript finns i [skapa en Function-app i en Premium-plan](scripts/functions-cli-create-premium-plan.md).

## <a name="features"></a>Funktioner

Följande funktioner är tillgängliga för Function-appar som distribueras till en Premium-plan.

### <a name="pre-warmed-instances"></a>Förvärmade instanser

Om inga händelser och körningar inträffar idag i förbruknings planen kan din app skalas upp till noll instanser. När nya händelser kommer in måste en ny instans vara anpassad med din app som körs på den.  Det kan ta lite tid att utföra särskilda nya instanser beroende på appen.  Den ytterligare svars tiden för det första anropet kallas ofta app kall start.

I Premium-planen kan du ha din app förvärmad på ett angivet antal instanser, upp till din minsta schema storlek.  Förvärmade instanser gör det också möjligt för dig att förskala en app innan hög belastning. När appen skalas ut skalas den först till de förvärmade instanserna. Ytterligare instanser fortsätter att buffras och värmas omedelbart i förberedelser inför nästa skalnings åtgärd. Genom att ha en buffert för förvärmade instanser kan du effektivt förhindra kall start fördröjning.  Förvärmade instanser är en funktion i Premium-planen och du måste behålla minst en instans som körs och som är tillgänglig hela tiden när planen är aktiv.

Du kan konfigurera antalet förvärmade instanser i Azure Portal genom att välja **Funktionsapp**, gå till fliken **plattforms funktioner** och välja alternativen för **skala ut** . I redigerings fönstret för Function-appen är de förvärmade instanserna speciella för den appen, men minimi-och Max instanserna gäller hela planen.

![Inställningar för elastisk skalning](./media/functions-premium-plan/scale-out.png)

Du kan också konfigurera förvärmade instanser för en app med Azure CLI.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Anslutning till privat nätverk

Azure Functions som distribueras till en Premium-plan drar nytta av [ny VNet-integrering för webbappar](../app-service/web-sites-integrate-with-vnet.md).  När appen har kon figurer ATS kan den kommunicera med resurser i ditt VNet eller skyddas via tjänst slut punkter.  IP-begränsningar är också tillgängliga i appen för att begränsa inkommande trafik.

När du tilldelar ett undernät till din Function-app i en Premium-plan behöver du ett undernät med tillräckligt med IP-adresser för varje möjlig instans. Vi kräver ett IP-block med minst 100 tillgängliga adresser.

Mer information finns i [integrera din Function-app med ett VNet](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Snabb elastisk skalning

Ytterligare beräknings instanser läggs automatiskt till för din app med samma snabbt skalnings logik som förbruknings planen.  Mer information om hur skalning fungerar finns i [funktions skala och värd](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="longer-run-duration"></a>Varaktighet för längre körning

Azure Functions i en förbruknings plan är begränsad till 10 minuter för en enda körning.  I Premium-planen är varaktigheten för körningen standardvärdet 30 minuter för att förhindra överkörningar. Du kan dock [ändra Host. JSON-konfigurationen](./functions-host-json.md#functiontimeout) för att göra detta obundet för appar för Premium plan (garanterat 60 minuter).

## <a name="plan-and-sku-settings"></a>Planera och SKU-inställningar

När du skapar planen konfigurerar du två inställningar: det minsta antalet instanser (eller plan storlek) och den maximala burst-gränsen.  Minimi instanserna är reserverade och körs alltid.

> [!IMPORTANT]
> Du debiteras för varje instans som allokeras i minsta instans antal oavsett om funktionerna körs eller inte.

Om din app kräver instanser som ligger utanför din plan storlek, kan den fortsätta att skala ut tills antalet instanser träffar den maximala burst-gränsen.  Du debiteras för instanser utöver din plan storlek bara när de är igång och hyr till dig.  Vi kommer att få bästa möjliga prestanda när du skalar din app till den definierade Max gränsen, medan de minsta plan instanserna är garanterade för din app.

Du kan konfigurera plan storlek och Max belopp i Azure Portal genom att välja alternativen för **skala ut** i planen eller en Function-app som distribueras till den planen (under **plattforms funktioner**).

Du kan också öka den maximala burst-gränsen från Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Tillgängliga instanser SKU: er

När du skapar eller skalar planen kan du välja mellan tre instans storlekar.  Du debiteras för det totala antalet kärnor och använt minne per sekund.  Din app kan automatiskt skala ut till flera instanser efter behov.  

|SKU|Kärnor|Minne|Storage|
|--|--|--|--|
|EP1|1|3,5 GB|GB|
|–|2|7GB|GB|
|EP3|4|14 GB|GB|

### <a name="memory-utilization-considerations"></a>Överväganden för minnes användning
Att köra på en dator med mer minne innebär inte alltid att din Function-app kommer att använda allt tillgängligt minne.

Till exempel begränsas en JavaScript Function-app av standard minnes gränsen i Node. js. Om du vill öka den här fasta minnes gränsen lägger du `languageWorkers:node:arguments` till inställningen för appen `--max-old-space-size=<max memory in MB>`med värdet.

## <a name="region-max-scale-out"></a>Region, Max skala ut

Nedan visas de maximala skalnings värden som stöds för närvarande för en enskild plan i varje region och OS-konfiguration. Om du vill begära en ökning öppnar du ett support ärende.

Se den fullständiga regionala tillgängligheten för funktioner här: [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|Region| Windows | Linux |
|--| -- | -- |
|Australien, centrala| 20 | Inte tillgängligt |
|Australien, centrala 2| 20 | Inte tillgängligt |
|Australien, östra| 100 | 20 |
|Australien, sydöstra | 100 | 20 |
|Brasilien, södra| 60 | 20 |
|Kanada, centrala| 100 | 20 |
|USA, centrala| 100 | 20 |
|Asien, östra| 100 | 20 |
|USA, östra | 100 | 20 |
|USA, östra 2| 100 | 20 |
|Frankrike, centrala| 100 | 20 |
|Tyskland, västra centrala| 100 | Inte tillgängligt |
|Japan, östra| 100 | 20 |
|Japan, västra| 100 | 20 |
|Sydkorea, centrala| 100 | 20 |
|USA, norra centrala| 100 | 20 |
|Europa, norra| 100 | 20 |
|Östra Norge| 20 | 20 |
|USA, södra centrala| 100 | 20 |
|Indien, södra | 100 | Inte tillgängligt |
|Sydostasien| 100 | 20 |
|Storbritannien, södra| 100 | 20 |
|Storbritannien, västra| 100 | 20 |
|Europa, västra| 100 | 20 |
|Indien, västra| 100 | 20 |
|USA, västra centrala| 20 | 20 |
|USA, västra| 100 | 20 |
|USA, västra 2| 100 | 20 |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förstå Azure Functions skalnings-och värd alternativ](functions-scale.md)
