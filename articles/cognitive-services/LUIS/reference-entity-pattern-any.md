---
title: Pattern. all entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: Mönster. det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75979162"
---
# <a name="patternany-entity"></a>Entiteten Pattern.any

Mönster. det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.  

Mönster. alla entiteter måste markeras i exempel på [mönster](luis-how-to-model-intent-pattern.md) mal len, inte avsikten med användar exempel.

**Entiteten passar bra när:**

* Entitetens slut kan förväxlas med den återstående texten i uttryck.

## <a name="usage"></a>Användning

Ett klient program som söker efter böcker som baseras på rubrik, mönstret. alla extraherar den fullständiga titeln. En mall som uttryck använder mönster. alla för den här bok `Was {BookTitle} written by an American this year[?]`sökningen är.

I följande tabell har varje rad två versioner av uttryck. Det främsta uttryck är hur LUIS för första gången ser uttryck. Det är inte tydligt var bok rubriken börjar och slutar. Den nedre uttryck använder ett mönster. en entitet för att markera början och slutet av entiteten.

|Uttryck med entiteten i fetstil|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Var **det man som feltagit sin fru för en hatt och andra kliniska** visare som skrevs av ett amerikanskt år?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Var **hälften av ström spar läge i Frog Pajamas** Skrivet av ett amerikanskt år?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Var **den specifika ledsenhet av citron tårta: en nya** skriven av ett amerikanskt år?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Fanns **det en Wocket i min ficka!** Skrivet av ett amerikanskt år?|
||



## <a name="example-json"></a>Exempel-JSON

Överväg följande fråga:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Med det inbäddade formulär namnet som ska extraheras som ett mönster. alla:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)

Detta är JSON om `verbose=false` anges i frågesträngen:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Detta är JSON om `verbose=true` anges i frågesträngen:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-tutorial-pattern.md)använder du **mönstret. en** entitet för att extrahera data från yttranden där yttranden är välformaterade och där data slutet kan lätt förväxlas med de återstående orden i uttryck.
