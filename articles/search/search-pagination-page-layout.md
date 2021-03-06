---
title: Så här arbetar du med Sök Resultat
titleSuffix: Azure Cognitive Search
description: Strukturera och sortera Sök resultat, hämta ett dokument antal och Lägg till innehålls navigering till Sök resultat i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: da01d0f7d2313b9700c5aae08edbda9e355b3774
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82801781"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Så här arbetar du med Sök resultat i Azure Kognitiv sökning

I den här artikeln förklaras hur du får ett svar på frågan som kommer tillbaka med ett totalt antal matchande dokument, rad brytnings resultat, sorterade resultat och träff markerade termer.

Strukturen för ett svar bestäms av parametrarna i frågan: [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) i REST API-eller [DocumentSearchResult-klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) i .NET SDK.

## <a name="result-composition"></a>Resultat sammansättning

Ett sökdokument kan bestå av ett stort antal fält, vanligt vis krävs det bara några få för att representera varje dokument i resultat uppsättningen. I en fråge förfrågan lägger `$select=<field list>` du till för att ange vilka fält som ska visas i svaret. Ett fält måste ha attributet kan **hämtas** i indexet för att inkluderas i ett resultat. 

Fält som fungerar bäst är sådana som kontrasterar och skiljer sig mellan dokument, och ger tillräckligt med information för att kunna bjuda in svars tid för en del av användaren. På en e-handelsplats kan det vara ett produkt namn, beskrivning, varumärke, färg, storlek, pris och klassificering. För det inbyggda exempel indexet Hotels-Sample, kan det vara fält i följande exempel:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Om du vill ta med bildfiler i ett resultat, t. ex. ett produkt foto eller en logo typ, kan du lagra dem utanför Azure Kognitiv sökning, men inkludera ett fält i indexet för att referera till bild-URL: en i Sök dokumentet. Exempel index som har stöd för bilder i resultatet inkluderar **realestate-exempel-se-** demonstrationen, som finns i den här [snabb](search-create-app-portal.md)starten och i [New York City-appen](https://aka.ms/azjobsdemo).

## <a name="paging-results"></a>Växla resultat

Som standard returnerar sökmotorn upp till de första 50 matchningarna, vilket bestäms av Sök poängen om frågan är full texts ökning eller i en godtycklig ordning för exakta matchnings frågor.

Om du vill returnera ett annat antal matchande dokument lägger `$top` du `$skip` till och parametrar i förfrågan. I följande lista beskrivs logiken.

+ Lägg `$count=true` till för att få en räkning av det totala antalet matchande dokument i ett index.

+ Returnera den första uppsättningen av 15 matchande dokument plus totalt antal matchningar:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Returnera den andra uppsättningen, hoppa över de första 15 för att få nästa 15: `$top=15&$skip=15`. Gör samma sak för den tredje uppsättningen 15:`$top=15&$skip=30`

Resultatet av sid brytnings frågor är inte garanterat stabilt om det underliggande indexet ändras. Växling ändrar värdet för `$skip` för varje sida, men varje fråga är oberoende och fungerar på den aktuella vyn av data som finns i indexet vid tidpunkten (med andra ord finns det ingen cachelagring eller ögonblicks bild av resultat, till exempel de som finns i en databas för generell användning).
 
Följande är ett exempel på hur du kan få dubbletter. Anta ett index med fyra dokument:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Anta nu att du vill att resultat returnerades två i taget, sorterade efter klassificering. Du kör den här frågan för att få den första sidan med resultat `$top=2&$skip=0&$orderby=rating desc`:, som producerar följande resultat:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
I tjänsten antar vi att ett femte dokument läggs till i indexet i mellan fråga-anrop `{ "id": "5", "rating": 4 }`:.  Strax därefter kör du en fråga för att hämta den andra sidan: `$top=2&$skip=2&$orderby=rating desc`och får följande resultat:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Observera att dokument 2 hämtas två gånger. Detta beror på att det nya dokumentet 5 har ett större värde för klassificering, så det sorteras före dokument 2 och på den första sidan. Även om det här beteendet kan vara oväntat, är det vanligt av hur en sökmotor beter sig.

## <a name="ordering-results"></a>Ordna resultaten

För fullständiga texts öknings frågor rangordnas resultaten automatiskt efter en Sök poäng, beräknad baserat på term frekvens och närhet i ett dokument, med högre resultat till dokument som har fler eller starkare matchningar på en Sök term. 

Sök Poäng förmedla allmän känsla av relevans, vilket återspeglar styrkan hos matchning jämfört med andra dokument i samma resultat uppsättning. Poängen är inte alltid konsekventa från en fråga till nästa, så när du arbetar med frågor kan du lägga märke till små skillnader i hur Sök dokumenten beställs. Det finns flera förklaringar till varför detta kan inträffa.

| Orsak | Beskrivning |
|-----------|-------------|
| Data flyktiga | Index innehållet varierar när du lägger till, ändrar eller tar bort dokument. Termen frekvens kommer att ändras när index uppdateringar bearbetas över tid, vilket påverkar Sök resultaten för matchande dokument. |
| Flera repliker | För tjänster som använder flera repliker utfärdas frågor till varje replik parallellt. Index statistiken som används för att beräkna ett Sök Resultat beräknas per replik, med resultat som sammanfogas och beställs i fråge svaret. Repliker är oftast speglar varandra, men statistik kan skilja sig på grund av små skillnader i tillstånd. En replik kan till exempel ha borttagna dokument som bidrar till sin statistik, som sammanfogades från andra repliker. Normalt sett är skillnader i statistik per replik mer märkbart i mindre index. |
| Identiska Poäng | Om flera dokument har samma resultat kan något av dem visas först.  |

### <a name="consistent-ordering"></a>Konsekvent sortering

Med hänsyn till Flex i resultat ordningen kanske du vill utforska andra alternativ om konsekvens är ett program krav. Den enklaste metoden är att sortera efter ett fält värde, t. ex. klassificering eller datum. För scenarier där du vill sortera efter ett visst fält, till exempel en klassificering eller ett datum, kan du uttryckligen definiera ett [ `$orderby` uttryck](query-odata-filter-orderby-syntax.md)som kan användas för alla fält som är indexerade som **sorterbara**.

Ett annat alternativ är att använda en [anpassad bedömnings profil](index-add-scoring-profiles.md). Med bedömnings profiler får du mer kontroll över rankningen av objekt i Sök resultaten, med möjligheten att öka matchningar som finns i vissa fält. Den ytterligare bedömnings logiken kan hjälpa till att åsidosätta mindre skillnader mellan repliker eftersom Sök poängen för varje dokument ligger längre ifrån varandra. Vi rekommenderar [rangordnings algoritmen](index-ranking-similarity.md) för den här metoden.

## <a name="hit-highlighting"></a>Träffmarkering

Träff markering avser textformatering (till exempel fetstil eller gula högdagrar) som tillämpas på matchande termer i ett resultat, vilket gör det lätt att hitta matchningen. Träff markerings instruktioner finns i [förfrågan](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Om du vill aktivera träff markering, `highlight=[comma-delimited list of string fields]` Lägg till för att ange vilka fält som ska använda markering. Markering är användbart för längre innehålls fält, till exempel ett beskrivnings fält där matchningen inte är direkt uppenbar. Endast fält definitioner som attributas som **sökbara** kvalificerare för träff markering.

Som standard returnerar Azure Kognitiv sökning upp till fem höjd punkter per fält. Du kan justera det här talet genom att lägga till i fältet ett streck följt av ett heltal. `highlight=Description-10` Returnerar till exempel upp till 10 för att matcha innehåll i fältet Beskrivning.

Formatering tillämpas på frågor på hela termen. Typen av formatering bestäms av taggar, `highlightPreTag` `highlightPostTag`och koden hanterar svaret (till exempel att använda fetstil eller gul bakgrund).

I följande exempel är begreppen "sandbrun", "sand", "stränder", "strand" som påträffas i beskrivnings fältet taggas för markering. Frågor som utlöser frågor om expandering i motorn, till exempel Fuzzy-och jokertecken, har begränsat stöd för träff markering.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Nytt beteende (från 15 juli)

Tjänster som skapats efter den 15 juli 2020 får en annan markerings upplevelse. Tjänster som skapats före detta datum ändras inte i markerings beteendet. 

Med det nya beteendet:

* Endast fraser som matchar den fullständiga fras frågan kommer att returneras. Frågan "Super Bowl" kommer att returnera högdagrar som detta:

    ```html
    '<em>super bowl</em> is super awesome with a bowl of chips'
    ```
  Observera att termen *Bowl av chips* inte har någon markering eftersom den inte matchar den fullständiga frasen.
  
* Det går att ange den fragment storlek som returneras för markeringen. Fragmentering av fragment anges som antal tecken (högst 1000 tecken).

När du skriver klient kod som implementerar träff markering, bör du vara medveten om den här ändringen. Observera att detta inte påverkar dig om du inte skapar en helt ny Sök tjänst.

## <a name="next-steps"></a>Nästa steg

Om du snabbt vill skapa en Sök sida för klienten bör du tänka på följande alternativ:

+ [Application Generator](search-create-app-portal.md), i portalen, skapar en HTML-sida med ett sökfält, en fasett med en navigering och ett resultat område som innehåller bilder.
+ [Skapa din första app i C#](tutorial-csharp-create-first-app.md) är en självstudie som skapar en fungerande klient. Exempel kod visar sid brytnings frågor, träff markering och sortering.

Flera kod exempel innehåller ett klient dels gränssnitt som du hittar här: [New York-jobb demonstrations app](https://aka.ms/azjobsdemo), [JavaScript-exempel kod med en Live Demo webbplats](https://github.com/liamca/azure-search-javascript-samples)och [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
