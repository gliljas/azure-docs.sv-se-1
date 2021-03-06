---
title: Kolumn mönster i Azure Data Factory mappa data flöde
description: Skapa generaliserade data omvandlings mönster med kolumn mönster i Azure Data Factory mappa data flöden
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606123"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Använda kolumn mönster i mappnings data flödet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med flera mappnings data flödes omvandlingar kan du referera till mall-kolumner baserat på mönster i stället för hårdkodade kolumn namn. Den här matchningen kallas för *kolumn mönster*. Du kan definiera mönster för att matcha kolumner baserat på namn, datatyp, ström eller position i stället för att kräva exakta fält namn. Det finns två scenarier där kolumn mönster är användbara:

* Om inkommande käll fält ändras ofta, till exempel vid ändring av kolumner i textfiler eller NoSQL-databaser. Det här scenariot kallas [schema avvikelse](concepts-data-flow-schema-drift.md).
* Om du vill göra en gemensam åtgärd i en stor grupp kolumner. Om du till exempel vill omvandla varje kolumn som har "total" i dess kolumn namn till ett dubbelt värde.

Kolumn mönster är för närvarande tillgängliga i omvandlingarna härledd kolumn, mängd, Välj och mottagare.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolumn mönster i härledd kolumn och mängd

Om du vill lägga till ett kolumn mönster i en härledd kolumn eller fliken Aggregator i en mängd omvandling, klickar du på plus ikonen till höger om en befintlig kolumn. Välj **Lägg till kolumn mönster**. 

![kolumn mönster](media/data-flow/columnpattern.png "Kolumnmönster")

Använd [uttrycks verktyget](concepts-data-flow-expression-builder.md) för att ange matchnings villkor. Skapa ett booleskt uttryck som matchar kolumner baserat på `name`kolumnens `type`, `stream`, och `position` . Mönstret påverkar alla kolumner, som anges eller definieras, där villkoret returnerar true.

De två uttrycks rutorna under matchnings villkoret anger de nya namnen och värdena för de berörda kolumnerna. Används `$$` för att referera till det befintliga värdet för det matchade fältet. Rutan till vänster-uttryck definierar det namn och den högra resultat rutan som definierar värdet.

![kolumn mönster](media/data-flow/columnpattern2.png "Kolumnmönster")

Mönstret ovan matchar alla kolumner av typen Double och skapar en sammanställd kolumn per matchning. Namnet på den nya kolumnen är den matchade kolumnens namn sammanfogat med _total. Värdet för den nya kolumnen är den rundade, aggregerade summan av det befintliga dubbla värdet.

För att kontrol lera att ditt matchnings villkor är korrekt kan du validera schemat för definierade kolumner på fliken **Granska** eller hämta en ögonblicks bild av data på fliken **data förhands granskning** . 

![kolumn mönster](media/data-flow/columnpattern3.png "Kolumnmönster")

## <a name="rule-based-mapping-in-select-and-sink"></a>Regel baserad mappning i SELECT och Sink

När du mappar kolumner i källa och väljer omvandlingar kan du lägga till antingen fast mappning eller regelbaserade mappningar. Matchning baserat på `name`kolumnerna `type`, `stream`, och `position` . Du kan ha en kombination av fasta och regelbaserade mappningar. Som standard är alla projektioner med fler än 50 kolumner som standard en regelbaserade mappning som matchar i varje kolumn och som utvärderar det angivna namnet. 

Om du vill lägga till en regelbaserade mappning klickar du på **Lägg till mappning** och väljer **regel baserad mappning**.

![regel baserad mappning](media/data-flow/rule2.png "Regel baserad mappning")

Varje regelbaserade mappning kräver två indata: det villkor som ska matchas med och vad som ska namnge varje mappad kolumn. Båda värdena anges via [uttrycks verktyget](concepts-data-flow-expression-builder.md). Ange ditt booleska matchnings villkor i rutan till vänster-uttryck. I rutan till höger uttryck anger du vad den matchade kolumnen ska mappas till.

![regel baserad mappning](media/data-flow/rule-based-mapping.png "Regel baserad mappning")

Använd `$$` syntax för att referera till Indataporten för en matchad kolumn. Använd bilden ovan som ett exempel, säg att en användare vill matcha i alla sträng kolumner vars namn är kortare än sex tecken. Om en inkommande kolumn har namngetts `test`, kommer `$$ + '_short'` uttrycket att byta namn `test_short`på kolumnen. Om det är den enda mappning som finns, kommer alla kolumner som inte uppfyller villkoret att tas bort från de data som returneras.

Mönster matchar både inkompatibla och definierade kolumner. Om du vill se vilka definierade kolumner som mappas av en regel klickar du på glasögon-ikonen bredvid regeln. Verifiera dina utdata med data förhands granskning.

### <a name="regex-mapping"></a>Regex-mappning

Om du klickar på ikonen för nedåtriktadt läge kan du ange ett regex-mappnings villkor. Ett regex-mappnings villkor matchar alla kolumn namn som matchar det angivna regex-villkoret. Detta kan användas tillsammans med standard regelbaserade mappningar.

![regel baserad mappning](media/data-flow/regex-matching.png "Regel baserad mappning")

Ovanstående exempel matchar i regex-mönster `(r)` eller kolumn namn som innehåller gemener r. På samma sätt som med standard regelbaserade mappningar ändras alla matchade kolumner av villkoret till höger med `$$` syntax.

### <a name="rule-based-hierarchies"></a>Regelbaserade hierarkier

Om din definierade projektion har en hierarki kan du använda regelbaserade mappningar för att mappa under kolumnerna hierarkier. Ange ett matchande villkor och den komplexa kolumn vars under kolumner du vill mappa. Varje matchad under kolumn kommer att returneras med regeln "name as".

![regel baserad mappning](media/data-flow/rule-based-hierarchy.png "Regel baserad mappning")

Ovanstående exempel matchar på alla under kolumner i komplex kolumn `a`. `a`innehåller två under kolumner `b` och. `c` Utdata-schemat kommer att innehålla två `b` kolumner `c` och som villkoret ' name as ' `$$`.

## <a name="pattern-matching-expression-values"></a>Mönster matchnings uttrycks värden.

* `$$`översätts till namnet eller värdet för varje matchning vid körning
* `name`representerar namnet på varje inkommande kolumn
* `type`representerar data typen för varje inkommande kolumn
* `stream`representerar namnet som är kopplat till varje data ström eller omvandling i ditt flöde
* `position`är ordnings punkten för kolumner i ditt data flöde

## <a name="next-steps"></a>Nästa steg
* Läs mer om data flödes [uttrycks språket](data-flow-expression-functions.md) för data omvandlingar
* Använd kolumn mönster i [omvandling av mottagare](data-flow-sink.md) och [Välj omvandling](data-flow-select.md) med regelbaserade mappning
