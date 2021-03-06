---
title: Vad är nytt i API för textanalys
titleSuffix: Text Analytics - Azure Cognitive Services
description: Den här artikeln innehåller information om nya versioner och funktioner för Azure Cognitive Services Textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: aahi
ms.openlocfilehash: 32dc7e86d5cd737533a4a6c8b3d9ce7d00795c65
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140521"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Vad är nytt i API:et för textanalys?

API för textanalys uppdateras regelbundet. Den här artikeln innehåller information om nya versioner och funktioner för att hålla dig uppdaterad med den senaste utvecklingen.

## <a name="may-2020"></a>Maj 2020

### <a name="text-analytics-api-v3-general-availability"></a>API för textanalys v3 allmän tillgänglighet

Text Analysis API v3 är nu allmänt tillgänglig med följande uppdateringar:

* Modell version`2020-04-01`
* Nya [data begränsningar](concepts/data-limits.md) för varje funktion
* [Språk stöd](language-support.md) för [Attitydanalys (sa) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) har uppdaterats
* Separat slut punkt för att länka entiteten 
* Ny "Address"-kategori i [namngiven entitets igenkänning (ner) v3](how-tos/text-analytics-how-to-entity-linking.md).
* Nya under Kategorier i NER v3:
   * Plats-geografisk
   * Plats-strukturellt
   * Organisation – börs kurs
   * Organisation – medicin
   * Organisation – idrotts
   * Event-kultur
   * Händelse – naturlig
   * Händelse-idrotts

Följande egenskaper i JSON-svaret har lagts till:
   * `SentenceText`i Attitydanalys
   * `Warnings`för varje dokument 

Namnen på följande egenskaper i JSON-svaret har ändrats, i tillämpliga fall:

* `score` har bytt namn till `confidenceScore`
    * `confidenceScore`har två decimal punkter av precision. 
* `type` har bytt namn till `category`
* `subtype` har bytt namn till `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [Läs mer om API för textanalys v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>API för textanalys v 3.1 offentlig för hands version
   * Ny Attitydanalys funktion – [Utanser utvinning](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Nytt [personligt ( `PII` ) domän filter](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) för skyddad hälso information ( `PHI` ).
   * Nya personliga ( `PII` ) kategorier:
      * Internationell klassificering av sjukdomar (ICD-9-CM)
      * Internationell klassificering av sjukdomar (ICD-10-CM)

> [!div class="nextstepaction"]
> [Läs mer om för hands versionen av API för textanalys v 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>Februari 2020

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-stöd för API för textanalys v3 offentlig för hands version

Som en del av den [enhetliga Azure SDK-versionen](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)är API för TEXTANALYS v3 SDK nu tillgängligt som en offentlig för hands version för följande programmeringsspråk:
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [Java Script (Node. js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [Läs mer om API för textanalys v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Namngiven enhets igenkänning v3 offentlig för hands version

Ytterligare entitetstyper är nu tillgängliga i den namngivna enhets igenkännings tjänsten (NER) v3 offentlig för hands version eftersom vi expanderar identifieringen av allmänna och personliga informations enheter i text. Den här uppdateringen introducerar [modell versionen](concepts/model-versioning.md) `2020-02-01` , som innehåller:

* Igenkänning av följande allmänna entitetstyper (endast engelska):
    * PersonType
    * Produkt
    * Händelse
    * GPE (politisk entitet) som en undertyp under plats
    * Kvalifikation

* Igenkänning av följande entitets typer för personlig information (endast engelska):
    * Person
    * Organisation
    * Ålder som underordnad kvantitet
    * Datum som en undertyp under DateTime
    * E-post 
    * Telefonnummer (endast USA)
    * URL
    * IP-adress

> [!div class="nextstepaction"]
> [Läs mer om namngiven enhets igenkänning v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>Oktober 2019

#### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii) för att identifiera entitets typer för personlig information (endast engelska)

* Separata slut punkter för [entitets igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral) och [entitet länkning](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking).

* [Modell version](concepts/model-versioning.md) `2019-10-01` , som innehåller:
    * Utökad identifiering och kategorisering av entiteter som finns i text. 
    * Igenkänning av följande nya enhets typer:
        * Telefonnummer
        * IP-adress

Enhets länkning stöder engelska och spanska. Språk stöd för NER varierar beroende på enhets typen.

#### <a name="sentiment-analysis-v3-public-preview"></a>Attitydanalys v3 offentlig för hands version

* En [ny slut punkt](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) för att analysera sentiment.
* [Modell version](concepts/model-versioning.md) `2019-10-01` , som innehåller:

    * Betydande förbättringar av precisionen och detaljerna i API: ns text kategorisering och poängsättning.
    * Automatisk etikettering för olika sentiment i text.
    * Sentiment analys och utdata på en dokument-och menings nivå. 

Det stöder engelska ( `en` ), japanska ( `ja` ), kinesiska (förenklad) `zh-Hans` , kinesiska (traditionell), `zh-Hant` franska (), `fr` italienska ( `it` ), spanska ( `es` ), nederländska ( `nl` ), portugisiska () och tyska (), `pt` `de` och är tillgängliga i följande regioner: `Australia East` , `Central Canada` , `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,,, och. 

> [!div class="nextstepaction"]
> [Läs mer om Attitydanalys v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Nästa steg

* [Vad är API för textanalys?](overview.md)  
* [Exempel på användarscenarier](text-analytics-user-scenarios.md)
* [Sentiment-analys](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Språk identifiering](how-tos/text-analytics-how-to-language-detection.md)
* [Enhets igenkänning](how-tos/text-analytics-how-to-entity-linking.md)
* [Extrahering av nyckel fraser](how-tos/text-analytics-how-to-keyword-extraction.md)
