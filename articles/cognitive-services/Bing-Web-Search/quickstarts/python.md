---
title: 'Snabbstart: Utföra en sökning med Python – API för webbsökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten om du vill skicka begäranden till REST-API:et för webbsökning i Bing med hjälp av Python och få ett JSON-svar
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 998558192891e1e7cbd24acd229f963925d3715f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873766"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Snabbstart: Använda Python för att anropa API för webbsökning i Bing  

Använd den här snabb starten för att göra ditt första anrop till API för webbsökning i Bing. Det här python-programmet skickar en Sök förfrågan till API: et och visar JSON-svaret. Även om det här programmet är skrivet i python är API: et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Det här exemplet körs som en Jupyter-anteckningsbok på [MyBinder](https://mybinder.org). Om du vill köra den väljer du Start binder-märket:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Krav

* [Python 2. x eller 3. x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definiera variabler

1. Ersätt värdet `subscription_key` med en giltig prenumerationsnyckel från ditt Azure-konto.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Deklarera slutpunkten för API för webbsökning i Bing. Du kan använda den globala slut punkten i följande kod eller använda den [anpassade slut domänen](../../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. Du kan också anpassa Sök frågan genom att ersätta värdet för `search_term` .

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Skapa en begäran

I den här koden används `requests` biblioteket för att anropa API för webbsökning i Bing och returnera resultaten som ett JSON-objekt. API-nyckeln skickas i `headers`-ordlistan, och söktermen samt frågeparametrarna skickas i `params`-ordlistan. 

En fullständig lista över alternativ och parametrar finns i [API för webbsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatera och visa svaret

`search_results`Objektet innehåller Sök resultaten och sådana metadata som relaterade frågor och sidor. Den här koden använder `IPython.display`-biblioteket för att formatera och visa svaret i webbläsaren.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Exempelkod på GitHub

Om du vill köra den här koden lokalt, se det fullständiga [exemplet som finns på GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Själv studie kurs om API för webbsökning i Bing enkel sida](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
