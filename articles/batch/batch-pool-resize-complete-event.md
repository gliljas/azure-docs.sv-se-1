---
title: Händelse för storleks ändring i Azure Batch pool
description: Referens för slutförd händelse för storleks ändring i batch-pool. Se ett exempel på en pool som har ökat i storlek och slutförts.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 94301f29fb6e7968dbe0389754fcf2a3b105d7ef
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83723824"
---
# <a name="pool-resize-complete-event"></a>Sluthändelse för storleksändring av pool

 Den här händelsen genereras när en storleks ändring för poolen har slutförts eller misslyckats.

 I följande exempel visas innehållet i en storleks ändring i en pool för en pool som har ökat i storlek och slutförts.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Element|Typ|Anteckningar|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
|`nodeDeallocationOption`|Sträng|Anger när noder kan tas bort från poolen, om poolens storlek minskar.<br /><br /> Möjliga värden:<br /><br /> **köa** om – avsluta aktiviteter som körs och köa om dem. Aktiviteterna kommer att köras igen när jobbet är aktiverat. Ta bort noder så snart som aktiviteter har avslut ATS.<br /><br /> **Avsluta** – avsluta aktiviteter som körs. Aktiviteterna kommer inte att köras igen. Ta bort noder så snart som aktiviteter har avslut ATS.<br /><br /> **taskcompletion** – Tillåt att aktiviteter som körs för tillfället slutförs. Schemalägg inga nya aktiviteter under väntan. Ta bort noder när alla aktiviteter har slutförts.<br /><br /> **Retaineddata** – Tillåt att aktiviteter som körs för tillfället slutförs och vänta sedan på att alla tids perioder för uppgifts data upphör att gälla. Schemalägg inga nya aktiviteter under väntan. Ta bort noder när alla aktiviteter för kvarhållning av aktiviteter har upphört att gälla.<br /><br /> Standardvärdet är Queue.<br /><br /> Om poolens storlek ökar, anges värdet **ogiltigt**.|
|`currentDedicatedNodes`|Int32|Antalet dedikerade datornoder som för närvarande är kopplade till poolen.|
|`targetDedicatedNodes`|Int32|Antalet dedikerade datornoder som har begärts för poolen.|
|`currentLowPriorityNodes`|Int32|Antalet Compute-noder med låg prioritet som för närvarande är kopplade till poolen.|
|`targetLowPriorityNodes`|Int32|Antalet Compute-noder med låg prioritet som begärs för poolen.|
|`enableAutoScale`|Bool|Anger om poolens storlek justeras automatiskt över tid.|
|`isAutoPool`|Bool|Anger om poolen har skapats via en jobbs mekanism för autopool.|
|`startTime`|DateTime|Tiden då storleken på poolen startades.|
|`endTime`|DateTime|Tiden då storleken på poolen har ändrats.|
|`resultCode`|Sträng|Resultatet av storleks ändring.|
|`resultMessage`|Sträng| Ett detaljerat meddelande om resultatet.<br /><br /> Om storleken har slutförts anger det att åtgärden lyckades.|
