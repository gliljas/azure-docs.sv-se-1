---
title: Skapa funktioner för UI-definition
description: Beskriver de funktioner som används när du konstruerar GRÄNSSNITTs definitioner för Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980821"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition-funktioner
Det här avsnittet innehåller signaturerna för alla funktioner som stöds i en CreateUiDefinition.

Om du vill använda en funktion omger du anropet med hakparenteser. Ett exempel:

```json
"[function()]"
```

Strängar och andra funktioner kan refereras till som parametrar för en funktion, men strängarna måste omges av enkla citat tecken. Ett exempel:

```json
"[fn1(fn2(), 'foobar')]"
```

I förekommande fall kan du referera till egenskaperna för resultatet av en funktion med hjälp av punkt operatorn. Ett exempel:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Referenser till funktioner
Dessa funktioner kan användas för att referera till utdata från egenskaperna eller kontexten för en CreateUiDefinition.

### <a name="basics"></a>om
Returnerar värdena för ett element som definieras i steget grundläggande.

I följande exempel returneras utdata från elementet med namnet `foo` i det grundläggande steget:

```json
"[basics('foo')]"
```

### <a name="steps"></a>steg
Returnerar värdena för ett element som har definierats i det angivna steget. Använd `basics()` i stället om du vill hämta utdata för elementen i Basic-steget.

I följande exempel returneras utdata från elementet med `bar` namnet i steget: `foo`

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Returnerar den plats som valts i det grundläggande steget eller den aktuella kontexten.

Följande exempel kan returnera `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Strängfunktioner
Dessa funktioner kan bara användas med JSON-strängar.

### <a name="concat"></a>concat
Sammanfogar en eller flera strängar.

Om till exempel utdata för `element1` IF `"bar"`, returnerar det här exemplet strängen: `"foobar!"`

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>under sträng
Returnerar del strängen för den angivna strängen. Under strängen börjar vid det angivna indexet och har den angivna längden.

Följande exempel returnerar `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>bytt
Returnerar en sträng i vilken alla förekomster av den angivna strängen i den aktuella strängen ersätts med en annan sträng.

Följande exempel returnerar `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Genererar en globalt unik sträng (GUID).

Följande exempel kan returnera `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Returnerar en sträng som har konverterats till gemener.

Följande exempel returnerar `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Returnerar en sträng konverterad till versaler.

Följande exempel returnerar `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Samlings funktioner
Dessa funktioner kan användas med samlingar, t. ex. JSON-strängar, matriser och objekt.

### <a name="contains"></a>innehåller
Returnerar `true` om en sträng innehåller den angivna under strängen, om en matris innehåller det angivna värdet, eller om ett objekt innehåller den angivna nyckeln.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel returnerar `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Returnerar antalet tecken i en sträng, antalet värden i en matris eller antalet nycklar i ett objekt.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel returnerar `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>tomt
Returnerar `true` om strängen, matrisen eller objektet är null eller tomt.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel returnerar `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Exempel 4: null och odefinierat
Antag `element1` är `null` eller är odefinierat. Följande exempel returnerar `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>förstagångskörningen
Returnerar det första tecken i den angivna strängen. första värdet för den angivna matrisen; eller den första nyckeln och värdet för det angivna objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Följande exempel returnerar `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>pågå
Returnerar det sista tecken i den angivna strängen, det sista värdet för den angivna matrisen eller den sista nyckeln och värdet för det angivna objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel returnerar `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>gå
Returnerar ett angivet antal sammanhängande tecken från början av strängen, ett angivet antal sammanhängande värden från början av matrisen, eller ett angivet antal sammanhängande nycklar och värden från början av objektet.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Följande exempel returnerar `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>hoppa över
Hoppar över ett angivet antal element i en samling och returnerar sedan de återstående elementen.

#### <a name="example-1-string"></a>Exempel 1: sträng
Följande exempel returnerar `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Exempel 2: matris
Antag `element1` returnerar `[1, 2, 3]`. Följande exempel returnerar `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Exempel 3: objekt
Antag `element1` returnerar:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Följande exempel returnerar `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logiska funktioner
Dessa funktioner kan användas i villkor. Vissa funktioner kanske inte stöder alla typer av JSON-data.

### <a name="equals"></a>lika med
Returnerar `true` om båda parametrarna har samma typ och värde. Den här funktionen stöder alla JSON-datatyper.

Följande exempel returnerar `true`:

```json
"[equals(0, 0)]"
```

Följande exempel returnerar `true`:

```json
"[equals('foo', 'foo')]"
```

Följande exempel returnerar `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>mindre än
Returnerar `true` om den första parametern är strikt mindre än den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `true`:

```json
"[less(1, 2)]"
```

Följande exempel returnerar `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>mindre än eller lika med
Returnerar `true` om den första parametern är mindre än eller lika med den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>större än
Returnerar `true` om den första parametern är exakt större än den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `false`:

```json
"[greater(1, 2)]"
```

Följande exempel returnerar `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>större än eller lika med
Returnerar `true` om den första parametern är större än eller lika med den andra parametern. Den här funktionen stöder endast parametrar av typen Number och String.

Följande exempel returnerar `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>och
Returnerar `true` om alla parametrar utvärderas till `true`. Den här funktionen stöder minst två parametrar av typen Boolean.

Följande exempel returnerar `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Följande exempel returnerar `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>eller
Returnerar `true` om minst en av parametrarna utvärderas till `true`. Den här funktionen stöder minst två parametrar av typen Boolean.

Följande exempel returnerar `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Följande exempel returnerar `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>inte
Returnerar `true` om parametern utvärderas till `false`. Den här funktionen stöder endast parametrar av typen Boolean.

Följande exempel returnerar `true`:

```json
"[not(false)]"
```

Följande exempel returnerar `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Returnerar värdet för den första parametern som inte är null. Den här funktionen stöder alla JSON-datatyper.

Antag `element1` och `element2` är odefinierade. Följande exempel returnerar `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

Den här funktionen är särskilt användbar i samband med valfritt anrop som inträffar på grund av användar åtgärd efter att sidan har lästs in. Ett exempel är om begränsningarna som placeras på ett fält i användar gränssnittet är beroende av det valda värdet för ett annat, **från början icke-synligt** fält. I det här fallet `coalesce()` kan användas för att tillåta funktionen att vara syntaktiskt giltig vid sid inläsning och ha önskad påverkan när användaren interagerar med fältet.

Överväg detta `DropDown`, vilket gör att användaren kan välja mellan flera olika databas typer:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Om du vill ange ett annat fälts åtgärd på det aktuella valda värdet för det här `coalesce()`fältet använder du, som visas här:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Detta är nödvändigt eftersom `databaseType` är inlednings vis inte visas och därför inte har något värde. Detta gör att hela uttrycket inte utvärderas korrekt.

## <a name="conversion-functions"></a>Konverterings funktioner
Dessa funktioner kan användas för att konvertera värden mellan JSON-datatyper och-kodningar.

### <a name="int"></a>int
Konverterar parametern till ett heltal. Den här funktionen stöder parametrar av typen Number och String.

Följande exempel returnerar `1`:

```json
"[int('1')]"
```

Följande exempel returnerar `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Konverterar parametern till en svävande punkt. Den här funktionen stöder parametrar av typen Number och String.

Följande exempel returnerar `1.0`:

```json
"[float('1.0')]"
```

Följande exempel returnerar `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>sträng
Konverterar parametern till en sträng. Den här funktionen stöder parametrar av alla JSON-datatyper.

Följande exempel returnerar `"1"`:

```json
"[string(1)]"
```

Följande exempel returnerar `"2.9"`:

```json
"[string(2.9)]"
```

Följande exempel returnerar `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

Följande exempel returnerar `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konverterar parametern till ett booleskt värde. Den här funktionen stöder parametrar av typen Number, String och Boolean. Liknar booleska värden i Java Script, alla värden utom `0` eller `'false'` returnerar `true`.

Följande exempel returnerar `true`:

```json
"[bool(1)]"
```

Följande exempel returnerar `false`:

```json
"[bool(0)]"
```

Följande exempel returnerar `true`:

```json
"[bool(true)]"
```

Följande exempel returnerar `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Konverterar parametern till en ursprunglig typ. Funktionen är med andra ord inversen till `string()`. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `1`:

```json
"[parse('1')]"
```

Följande exempel returnerar `true`:

```json
"[parse('true')]"
```

Följande exempel returnerar `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

Följande exempel returnerar `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Kodar parametern till en Base-64-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Avkodar parametern från en Base-64-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Kodar parametern till en URL-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Avkodar parametern från en URL-kodad sträng. Den här funktionen stöder endast parametrar av typen sträng.

Följande exempel returnerar `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematikfunktioner
### <a name="add"></a>add
Adderar två tal och returnerar resultatet.

Följande exempel returnerar `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Build
Subtraherar den andra siffran från det första talet och returnerar resultatet.

Följande exempel returnerar `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multiplicerar två tal och returnerar resultatet.

Följande exempel returnerar `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Dividerar det första talet med det andra talet och returnerar resultatet. Resultatet är alltid ett heltal.

Följande exempel returnerar `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>rest
Dividerar det första talet med det andra talet och returnerar resten.

Följande exempel returnerar `0`:

```json
"[mod(6, 3)]"
```

Följande exempel returnerar `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Returnerar det lilla talet av de två talen.

Följande exempel returnerar `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>bekräftat
Returnerar det större talet av de två talen.

Följande exempel returnerar `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>intervall
Genererar en sekvens med heltal inom det angivna intervallet.

Följande exempel returnerar `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>slump
Returnerar ett slumptal inom det angivna intervallet. Den här funktionen genererar inte kryptografiskt säkra slumptal.

Följande exempel kan returnera `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>maximal
Returnerar det största heltal som är mindre än eller lika med det angivna talet.

Följande exempel returnerar `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Returnerar det största heltal som är större än eller lika med det angivna talet.

Följande exempel returnerar `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datumfunktioner
### <a name="utcnow"></a>utcNow
Returnerar en sträng i ISO 8601-formatet för aktuellt datum och aktuell tid på den lokala datorn.

Följande exempel kan returnera `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Lägger till ett heltals antal sekunder till den angivna tidsstämpeln.

Följande exempel returnerar `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Lägger till ett integral antal minuter i den angivna tidsstämpeln.

Följande exempel returnerar `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Lägger till ett heltals antal timmar för den angivna tidsstämpeln.

Följande exempel returnerar `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till Azure Resource Manager finns i [Azure Resource Manager översikt](../management/overview.md).

