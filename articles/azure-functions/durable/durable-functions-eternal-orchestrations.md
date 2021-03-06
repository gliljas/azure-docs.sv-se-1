---
title: Eternal-dirigering i Durable Functions – Azure
description: Lär dig hur du implementerar Eternal-dirigering med hjälp av Durable Functions-tillägget för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478818"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal-dirigeringar i Durable Functions (Azure Functions)

*Eternal-dirigering* är Orchestrator-funktioner som aldrig slutar. De är användbara när du vill använda [Durable Functions](durable-functions-overview.md) för agg regeringar och alla scenarier som kräver en oändlig loop.

## <a name="orchestration-history"></a>Orchestration-historik

Som förklaras i avsnittet om [Orchestration-historik](durable-functions-orchestrations.md#orchestration-history) , håller det varaktiga aktivitets ramverket att spåra historiken för varje funktions dirigering. Den här historiken växer kontinuerligt så länge Orchestrator-funktionen fortsätter att schemalägga nytt arbete. Om Orchestrator-funktionen hamnar i en oändlig slinga och kontinuerligt schemalägger arbete, kan den här historiken växa mycket stor och orsaka betydande prestanda problem. *Eternal Orchestration* -konceptet har utformats för att minimera dessa typer av problem för program som behöver oändliga slingor.

## <a name="resetting-and-restarting"></a>Återställa och starta om

I stället för att använda oändliga slingor återställs deras tillstånd genom att `ContinueAsNew` anropa metoden (.net) eller `continueAsNew` (Java Script) i [bindningen för Dirigerings utlösaren](durable-functions-bindings.md#orchestration-trigger). Den här metoden tar en enda JSON-serialiserbar parameter, som blir den nya ingången för nästa generations funktion i Orchestrator.

När `ContinueAsNew` anropas, kommer instansen att köa ett meddelande innan det avslutas. Meddelandet startar om instansen med det nya indatavärdet. Samma instans-ID behålls, men Orchestrator-funktionens historik trunkeras effektivt.

> [!NOTE]
> Det beständiga aktivitets ramverket upprätthåller samma instans-ID men skapar internt ett nytt *körnings-ID* för Orchestrator- `ContinueAsNew`funktionen som återställs av. Detta körnings-ID visas normalt inte externt, men det kan vara användbart att känna till vid fel sökning av Orchestration-körning.

## <a name="periodic-work-example"></a>Exempel på periodiskt arbete

Ett användnings fall för Eternal-dirigering är kod som behöver göra periodiskt arbete på obestämd tid.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

---

Skillnaden mellan det här exemplet och en timer-utlöst funktion är att rensnings utlösare här inte baseras på ett schema. Ett CRON-schema som kör en funktion varje timme kommer till exempel att köra den på 1:00, 2:00, 3:00 osv. och kan eventuellt leda till överlappande problem. I det här exemplet, men om rensningen tar 30 minuter, kommer den att schemaläggas till 1:00, 2:30, 4:00 osv. det finns ingen risk för överlappande.

## <a name="starting-an-eternal-orchestration"></a>Starta en Eternal-dirigering

Använd metoden `StartNewAsync` (.net) eller `startNew` (Java Script) för att starta en Eternal-dirigering, precis som med andra Orchestration-funktioner.  

> [!NOTE]
> Om du behöver se till att en singleton-Eternal-dirigering körs är det viktigt att du underhåller samma `id` instans när du startar dirigeringen. Mer information finns i [instans hantering](durable-functions-instance-management.md).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Föregående kod är för Durable Functions 2. x. För Durable Functions 1. x måste du `OrchestrationClient` använda attribut i stället för `DurableClient` attributet och du måste använda `DurableOrchestrationClient` parameter typen i stället för. `IDurableOrchestrationClient` Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Avsluta från en Eternal-dirigering

Om en Orchestrator-funktion behöver slutföras måste du *inte* anropa `ContinueAsNew` och låta funktionen avslutas.

Om en Orchestrator-funktion finns i en oändlig slinga och måste stoppas, använder du `TerminateAsync` (.net) eller `terminate` (Java Script) metoden för [Dirigerings klient bindningen](durable-functions-bindings.md#orchestration-client) för att stoppa den. Mer information finns i [instans hantering](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du implementerar singleton-dirigeringar](durable-functions-singletons.md)
