---
title: Hantera fel i Durable Functions – Azure
description: Lär dig hur du hanterar fel i Durable Functions-tillägget för Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277861"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Hantera fel i Durable Functions (Azure Functions)

Varaktiga funktions dirigering implementeras i kod och kan använda programmeringsspråkets inbyggda fel hanterings funktioner. Det finns egentligen inga nya koncept som du behöver lära dig för att lägga till fel hantering och kompensation till dina dirigeringar. Det finns dock några beteenden som du bör vara medveten om.

## <a name="errors-in-activity-functions"></a>Fel i aktivitets funktioner

Alla undantag som genereras i en aktivitets funktion konverteras tillbaka till Orchestrator-funktionen och utlöstes som `FunctionFailedException`. Du kan skriva fel hantering och kompensations kod som passar dina behov i Orchestrator-funktionen.

Anta till exempel följande Orchestrator-funktion som överför fonder från ett konto till ett annat:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

---

Om det första funktions anropet för **CreditAccount** Miss lyckas kompenserar Orchestrator-funktionen genom att kreditera pengarna tillbaka till käll kontot.

## <a name="automatic-retry-on-failure"></a>Automatiskt försök vid fel

När du anropar aktivitets funktioner eller under Orchestration-funktioner kan du ange en princip för automatisk återförsök. Följande exempel försöker anropa en funktion upp till tre gånger och väntar 5 sekunder mellan varje nytt försök:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

Aktivitets funktions anropet i föregående exempel tar en parameter för att konfigurera en princip för automatisk återförsök. Det finns flera alternativ för att anpassa principen för automatiskt återförsök:

* **Maximalt antal försök**: maximalt antal återförsök.
* **Första**återförsöksintervall: vänte tiden innan det första försöket.
* **Backoff-koefficient**: den koefficient som används för att fastställa frekvensen av ökningen av backoff. Standardvärdet är 1.
* **Högsta återförsöksintervall**: den maximala vänte tiden mellan återförsök.
* **Timeout för återförsök**: maximal tid att spendera på nya försök. Standard beteendet är att försöka på obestämd tid.
* **Referens**: en användardefinierad motringning kan anges för att avgöra om en funktion ska provas igen.

## <a name="function-timeouts"></a>Funktions tids gränser

Du kanske vill överge ett funktions anrop i en Orchestrator-funktion om det tar för lång tid att slutföra. Det är ett bra sätt att göra detta idag genom att skapa en [varaktig timer](durable-functions-timers.md) med `context.CreateTimer` (.net `context.df.createTimer` ) eller (Java Script) `Task.WhenAny` tillsammans med (.net `context.df.Task.any` ) eller (Java Script), som i följande exempel:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Föregående C#-exempel är för Durable Functions 2. x. För Durable Functions 1. x måste du använda `DurableOrchestrationContext` i stället för `IDurableOrchestrationContext`. Mer information om skillnaderna mellan versioner finns i artikeln [Durable Functions versioner](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

---

> [!NOTE]
> Den här mekanismen avslutar egentligen inte körningen av aktivitets funktionen. I stället tillåter det bara att Orchestrator-funktionen ignorerar resultatet och går vidare. Mer information finns i dokumentationen för [timers](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Ohanterade undantag

Om en Orchestrator-funktion Miss lyckas med ett ohanterat undantag loggas detaljerna för undantaget och instansen slutförs med en `Failed` status.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Eternal-dirigeringar](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Lär dig hur du diagnostiserar problem](durable-functions-diagnostics.md)
