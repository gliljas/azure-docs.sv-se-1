---
title: Fördefinierade dimensions entiteter – LUIS
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller fördefinierad enhets information för dimension i Language Understanding (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270637"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Fördefinierad dimensions enhet för en LUIS-app
Den fördefinierade dimensions enheten identifierar olika typer av dimensioner, oavsett LUIS-programkultur. Eftersom entiteten redan har tränats behöver du inte lägga till exempel yttranden som innehåller dimensioner i program avsikterna. Entiteten dimension stöds i [många kulturer](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Dimensions typer

Dimensionen hanteras från GitHub-lagringsplatsen för [identifierare – text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) .

## <a name="resolution-for-dimension-entity"></a>Lösning för dimensions enhet

Följande enhets objekt returneras för frågan:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[V3-svar](#tab/V3)

Följande JSON- `verbose` parameter har angetts till `false`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 utförlig Response](#tab/V3-verbose)
Följande JSON- `verbose` parameter har angetts till `true`:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[V2-svar](#tab/V2)

I följande exempel visas upplösningen för entiteten **Builtin. dimension** .

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).

Lär dig mer om entiteterna [e-post](luis-reference-prebuilt-email.md), [nummer](luis-reference-prebuilt-number.md)och [ordnings tal](luis-reference-prebuilt-ordinal.md) .
