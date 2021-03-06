---
title: 'Snabb start: skapa avsikt med REST-API: er – LUIS'
description: I den här REST API snabb starten använder du en tillgänglig offentlig LUIS-app för att fastställa en användares avsikt från konversations text.
ms.topic: quickstart
ms.date: 05/18/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 90d98e56e53e28991fb5aada9eab5a7e9c2e69c3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654344"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Snabb start: skapa avsikt med REST API: er

I den här snabb starten ska du använda en LUIS-app för att fastställa en användares avsikt från konversations text. Skicka användarens avsikt som text till pizza-appens HTTP förutsägelse-slutpunkt. Vid slut punkten tillämpar LUIS pizza-appens modell för att analysera den naturliga språk texten, vilket avgör övergripande avsikt och extrahering av data som är relevanta för appens ämnes domän.

I den här snabbstarten används slutpunktens REST-API. Mer information finns i [dokumentationen om slutpunkts-API:er](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

I den här artikeln behöver du ett kostnadsfritt [LUIS](https://www.luis.ai)-konto.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
