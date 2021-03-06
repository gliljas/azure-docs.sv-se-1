---
title: Parametrisera mappningsdataflöden
description: Lär dig hur du Parameterisera ett mappnings data flöde från Data Factory-pipeliner
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8e88e5e8a9fbe1881959c5183dc01b11ac681bdf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780425"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrisera mappningsdataflöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Att mappa data flöden i Azure Data Factory stödja användning av parametrar. Definiera parametrar i data flödes definitionen och Använd dem i alla uttryck. Parametervärdena anges av anrops pipelinen via aktiviteten kör data flöde. Det finns tre alternativ för att ställa in värden i uttryck för data flödes aktivitet:

* Använd språket för flödes kontroll flödets uttryck för att ange ett dynamiskt värde
* Använd data flödets uttrycks språk för att ange ett dynamiskt värde
* Använd antingen uttrycks språk för att ange ett statiskt litteralt värde

Använd den här funktionen för att göra dina data flöden generella, flexibla och återanvändbara. Du kan Parameterisera data flödes inställningar och uttryck med dessa parametrar.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Skapa parametrar i ett data flöde för mappning

Om du vill lägga till parametrar till ditt data flöde, klickar du på den tomma delen av data flödes arbets ytan för att se de allmänna egenskaperna. I fönstret inställningar visas en flik som heter **parameter**. Välj **ny** för att skapa en ny parameter. För varje parameter måste du tilldela ett namn, välja en typ och alternativt ange ett standardvärde.

![Skapa data flödes parametrar](media/data-flow/create-params.png "Skapa data flödes parametrar")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Använda parametrar i ett data flöde för mappning 

Det går att referera till parametrar i ett data flödes uttryck. Parametrarna börjar med $ och är oföränderliga. Du hittar listan över tillgängliga parametrar inuti uttrycks verktyget under fliken **parametrar** .

![Parameter uttryck för data flöde](media/data-flow/parameter-expression.png "Parameter uttryck för data flöde")

Du kan snabbt lägga till ytterligare parametrar genom att välja **ny parameter** och ange namn och typ.

![Parameter uttryck för data flöde](media/data-flow/new-parameter-expression.png "Parameter uttryck för data flöde")

## <a name="assign-parameter-values-from-a-pipeline"></a>Tilldela parameter värden från en pipeline

När du har skapat ett data flöde med parametrar kan du köra det från en pipeline med aktiviteten kör data flöde. När du har lagt till aktiviteten till din pipeline-arbetsyta visas de tillgängliga data flödes parametrarna på fliken **parametrar** i aktiviteten.

När du tilldelar parameter värden kan du använda antingen [uttrycks språket för pipelinen](control-flow-expression-language-functions.md) eller [data flödets uttrycks språk](data-flow-expression-functions.md) baserat på Spark-typer. Varje mappnings data flöde kan ha valfri kombination av parametrar för pipeline och data flödes uttryck.

![Ange en data flödes parameter](media/data-flow/parameter-assign.png "Ange en data flödes parameter")

### <a name="pipeline-expression-parameters"></a>Uttrycks parametrar för pipeline

Med parametrarna för pipeline-uttryck kan du referera till systemvariabler, funktioner, pipeline-parametrar och variabler som liknar andra pipeline-aktiviteter. När du klickar på **pipeline-uttryck**öppnas ett sido navigerings läge där du kan ange ett uttryck med uttrycks verktyget.

![Ange en data flödes parameter](media/data-flow/parameter-pipeline.png "Ange en data flödes parameter")

När det hänvisas till utvärderas pipeliniska parametrar och sedan används deras värde i data flödets uttrycks språk. Uttrycks typen för pipelinen behöver inte matcha data flödets parameter typ. 

#### <a name="string-literals-vs-expressions"></a>Sträng litteraler vs-uttryck

När du tilldelar en uttrycks parameter för pipeline av typen sträng, läggs standard citat tecken till och värdet utvärderas som en literal. Om du vill läsa parameter värdet som ett data flödes uttryck, markerar du rutan uttryck bredvid parametern.

![Ange en data flödes parameter](media/data-flow/string-parameter.png "Ange en data flödes parameter")

Om data flödes `stringParam` parametern refererar till en pipeline-parameter `upper(column1)`med värde. 

- Om uttrycket är markerat `$stringParam` utvärderas värdet för column1 alla versaler.
- Om uttrycket inte är markerat (standard beteende) `$stringParam` utvärderas till`'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Överför i tidsstämplar

I pipeline-uttryckets språk kan systemvariabler, `pipeline().TriggerTime` till exempel och `utcNow()` funktioner som returnerar tidsstämplar som strängar i formatet åååå-mm-dd\'T\'hh: mm: SS. SSSSSSZ'. Om du vill konvertera dessa till data flödes parametrar av typen timestamp, använder du String-interpolation för `toTimestamp()` att inkludera önskad tidstämpel i en funktion. Om du till exempel vill konvertera utlösnings tiden för pipelinen till en data flödes parameter `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`kan du använda. 

![Ange en data flödes parameter](media/data-flow/parameter-timestamp.png "Ange en data flödes parameter")

> [!NOTE]
> Data flöden kan bara stödja upp till 3 millisekunder. `left()` Funktionen används för att trimma ytterligare siffror.

#### <a name="pipeline-parameter-example"></a>Exempel på pipeline-parameter

Anta att du har en heltals parameter `intParam` som refererar till en pipeline-parameter av typen sträng `@pipeline.parameters.pipelineParam`,. 

![Ange en data flödes parameter](media/data-flow/parameter-pipeline-2.png "Ange en data flödes parameter")

`@pipeline.parameters.pipelineParam`tilldelas värdet `abs(1)` vid körning.

![Ange en data flödes parameter](media/data-flow/parameter-pipeline-4.png "Ange en data flödes parameter")

När `$intParam` refereras till i ett uttryck, till exempel en härledd kolumn, kommer `abs(1)` det `1`att utvärdera RETUR. 

![Ange en data flödes parameter](media/data-flow/parameter-pipeline-3.png "Ange en data flödes parameter")

### <a name="data-flow-expression-parameters"></a>Uttrycks parametrar för data flöde

Genom att välja **data flödes uttryck** öppnas data flödets uttrycks verktyg. Du kommer att kunna referera till funktioner, andra parametrar och en definierad schema kolumn i hela data flödet. Det här uttrycket kommer att utvärderas som det är när det refereras.

> [!NOTE]
> Om du skickar ett ogiltigt uttryck eller en referens till en schema kolumn som inte finns i omvandlingen, kommer parametern att utvärderas till null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Skicka in ett kolumn namn som en parameter

Ett vanligt mönster är att skicka in ett kolumn namn som ett parameter värde. Om kolumnen definieras i data flödes schemat kan du referera till den direkt som ett sträng uttryck. Om kolumnen inte är definierad i schemat använder du `byName()` funktionen. Kom ihåg att omvandla kolumnen till lämplig typ med en gjutning, till exempel `toString()`.

Om du till exempel vill mappa en sträng kolumn baserat på en parameter `columnName`kan du lägga till en härledd kolumn omvandling som är lika `toString(byName($columnName))`med.

![Skicka in ett kolumn namn som en parameter](media/data-flow/parameterize-column-name.png "Skicka in ett kolumn namn som en parameter")

## <a name="next-steps"></a>Nästa steg
* [Kör data flödes aktivitet](control-flow-execute-data-flow-activity.md)
* [Kontroll flödes uttryck](control-flow-expression-language-functions.md)
