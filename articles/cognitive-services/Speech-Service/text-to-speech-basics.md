---
title: Grunder för tal syntes – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal-SDK för att konvertera text till tal. I den här artikeln får du lära dig om objekt konstruktion, format för ljudutdata som stöds och anpassade konfigurations alternativ för tal syntes.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: b44c1d722e6acbcf3fbe8f1ac6e05b15c2a0676b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125527"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>Lär dig grunderna för tal syntes

I den här artikeln får du lära dig vanliga design mönster för att skapa text till tal-Sammanfattning med hjälp av tal-SDK. Du börjar med att utföra grundläggande konfiguration och syntes och går vidare till mer avancerade exempel för anpassad program utveckling, inklusive:

* Få svar som minnes strömmar
* Anpassa samplings frekvens och bit hastighet för utdata
* Skicka syntes begär Anden med SSML (tal syntes Markup Language)
* Använda neurala-röster

> [!TIP]
> Om du inte har haft möjlighet att slutföra en av våra snabb starter, rekommenderar vi att du använder däcken och provar tal igenkänningen för dig själv.
> * [Identifiera tal från en mikrofon](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Custom Voice](how-to-custom-voice.md)
* [Förbättra syntesen med SSML](speech-synthesis-markup.md)