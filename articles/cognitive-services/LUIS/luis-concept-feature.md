---
title: Funktioner – LUIS
description: Lägg till funktioner i en språk modell för att ge tips om hur du identifierar indatatyper som du vill etikettera eller klassificera.
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: c4f19ceed2e48f3f6ec2ed0958bccb7a85cff44f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742717"
---
# <a name="machine-learning-ml-features"></a>Funktioner för Machine Learning (ML)

I Machine Learning är en **funktion**   en särskiljande egenskaps-eller dataattribut som systemet iakttar.

Machine Learning-funktioner ger LUIS viktiga stackar för var du ska leta efter saker som särskiljer ett koncept. De är tips som LUIS kan använda, men inte hårda regler.  Dessa tips används tillsammans med etiketterna för att hitta data.

 LUIS stöder både fras listor och använder andra entiteter som-funktioner:
* Funktionen fras lista
* Modell (avsikt eller entitet) som en funktion

Funktioner bör anses vara en nödvändig del av schema designen.

## <a name="a-phrase-list-for-a-particular-concept"></a>En fras lista för ett visst koncept

En fras lista är en lista med ord eller fraser som kapslar in ett visst begrepp.

När du lägger till en fras lista kan du ställa in funktionen som:
* **[Global](#global-features)**. En global funktion gäller hela appen.

### <a name="when-to-use-a-phrase-list"></a>När du ska använda en fras lista

Använd en fras lista när du behöver LUIS-appen för att kunna generalisera och identifiera nya objekt för konceptet. Fras listor är som domänbaserad vokabulär som hjälper till att förbättra kvaliteten på både avsikter och entiteter.

### <a name="how-to-use-a-phrase-list"></a>Så här använder du en fras lista

Med en fras lista anser LUIS kontext och generalizes för att identifiera objekt som liknar, men inte en exakt text matchning.

Steg för att använda en fras lista:
* Börja med en enhet för maskin inlärning
    * Lägga till exempelyttranden
    * Etikett med en enhet för maskin inlärning
* Lägg till en fras lista
    * Lägg till ord med liknande betydelse – Lägg **inte** till varje möjligt ord eller fras. Lägg i stället till några ord eller fraser i taget, och sedan träna och publicera.
    * Granska och Lägg till föreslagna ord

### <a name="a-typical-scenario-for-a-phrase-list"></a>Ett typiskt scenario för en fras lista

Ett typiskt scenario för en fras lista är att förstärka ord som är relaterade till en bestämd idé.

Ett exempel på ord som kan behöva en fras lista för att öka betydelsen är medicinska villkor. Villkoren kan ha en speciell fysisk, kemisk, terapeutisk eller abstrakt betydelse. LUIS vet inte villkoren som är viktiga för din ämnes domän utan någon fras lista.

Om du vill extrahera de medicinska villkoren:
* Börja med att skapa exempel yttranden och etikettera medicinska villkor inom dessa yttranden.
* Skapa sedan en fras lista med exempel på villkoren i ämnes domänen. Den här fras listan ska innehålla den faktiska termen som du har märkt och andra termer som beskriver samma koncept.
* Lägg till fras listan i entiteten eller underentiteten som extraherar det koncept som används i fras listan. Det vanligaste scenariot är en komponent (underordnad) till en enhet för maskin inlärning. Om fras listan ska användas för alla syften eller entiteter markerar du fras listan som en global fras lista. `enabledForAllModels`Flaggan styr det här modell omfånget i API: et.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>En modell som en funktion hjälper en annan modell

Du kan lägga till en modell (avsikt eller entitet) som en funktion till en annan modell (avsikt eller entitet). Genom att lägga till en befintlig avsikts-eller entitet som funktion lägger du till ett väldefinierat begrepp med märkta exempel.

När du lägger till en modell som en funktion kan du ställa in funktionen som:
* **[Obligatoriskt](#required-features)**. En nödvändig funktion måste finnas för att modellen ska returneras från förutsägelse slut punkten.
* **[Global](#global-features)**. En global funktion gäller hela appen.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>När du ska använda en entitet som en funktion i avsikten

Lägg till en entitet som en funktion i avsikt när identifieringen av den enheten är signifikant för avsikten.

Om avsikten t. ex. är att boka en flygning, `BookFlight` och entiteten är biljett information (till exempel antalet platser, ursprung och mål), ska du söka efter entiteten med biljett information genom att lägga till en betydande vikt för förutsägelsen av `BookFlight` avsikten.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>När du ska använda en entitet som en funktion till en annan entitet

En entitet (A) bör läggas till som en funktion till en annan entitet (B) när entitetens identifiering (A) är signifikant för förutsägelsen av entiteten (B).

Om n till exempel enhet för leverans adress innehåller en gatuadress för gatuadress, lägger sedan till underentiteten gata i en större vikt för förutsägelsen för entiteten leverans adress.

* Leverans adress (enheten har lärts)
    * Gatu nummer (underentitet)
    * Gatuadress (underentitet)
    * Ort (underentitet)
    * Delstat eller provins (underentitet)
    * Land/region (underentitet)
    * Post nummer (underentitet)

## <a name="nested-subentities-with-features"></a>Kapslade underentiteter med funktioner

En dator som är förhandlad underordnad visar att det finns ett begrepp för den överordnade entiteten, om överordnad är en annan underordnad entitet eller den översta Värdet för underentiteten fungerar som en funktion till dess överordnade.

En underentitet kan ha både en fras lista som en funktion och en modell (en annan entitet) som en funktion.

När underentiteten har en fras lista höjer detta konceptets vokabulär, men kommer inte att lägga till någon information till JSON-svaret för förutsägelsen.

När underentiteten har en funktion i en annan entitet innehåller JSON-svaret de extraherade data för den andra entiteten.

## <a name="required-features"></a>Nödvändiga funktioner

En nödvändig funktion måste finnas för att modellen ska returneras från förutsägelse slut punkten. Använd en nödvändig funktion när du vet att inkommande data måste matcha funktionen.

Om uttryck-texten inte matchar den nödvändiga funktionen extraheras den inte.

**En obligatorisk funktion använder en entitet som inte är en enhet**:
* Entitet för reguljära uttryck
* Lista entitet
* Fördefinierad entitet

Vad är en bra funktion för att markera som krävs? Om du är säker på att din modell finns i datan, ställer du in funktionen efter behov. En nödvändig funktion returnerar inte något, om den inte hittas.

Du fortsätter med exemplet på leverans adressen:
* Leverans adress (enheten har lärts)
    * Gatu nummer (underentitet)
    * Gatuadress (underentitet)
    * Gatu namn (underentitet)
    * Ort (underentitet)
    * Delstat eller provins (underentitet)
    * Land/region (underentitet)
    * Post nummer (underentitet)

### <a name="required-feature-using-prebuilt-entities"></a>Nödvändig funktion med fördefinierade entiteter

Ort, delstat och land/region är vanligt vis en stängd uppsättning listor, vilket innebär att de inte ändras mycket över tid. Dessa entiteter kan ha relevanta rekommenderade funktioner och dessa funktioner kan markeras som obligatoriska. Det innebär att hela leverans adressen inte returneras, men det går inte att hitta entiteterna med de funktioner som krävs.

Vad händer om ort, delstat eller land/region finns i uttryck, men antingen på en plats eller slang som LUIS inte förväntar sig? Om du vill ge en del post bearbetning för att hjälpa till att lösa entiteten, på grund av en låg exakthet från LUIS, ska du inte markera funktionen som krävs.

Ett annat exempel på en nödvändig funktion för leverans adressen är att skapa ett obligatoriskt [fördefinierat](luis-reference-prebuilt-entities.md) nummer för gatan. Detta gör att användaren kan ange "1 Microsoft Way" eller "One Microsoft Way". Båda kommer att matcha ett antal "1" för underentiteten gata nummer.

### <a name="required-feature-using-list-entities"></a>Nödvändig funktion med hjälp av list-entiteter

En [lista entitet](reference-entity-list.md) används som en lista över kanoniska namn tillsammans med deras synonymer. Som en nödvändig funktion, om uttryck inte inkluderar det kanoniska namnet eller en synonym, returneras inte entiteten som en del av förutsägelse slut punkten.

Om du fortsätter med leverans adress exemplet antar vi att företaget endast levererar till en begränsad uppsättning länder/regioner. Du kan skapa en lista med entiteter som innehåller flera olika sätt som kunden kan referera till i landet. Om LUIS inte hittar en exakt matchning i texten för uttryck, returneras inte entiteten (som har den nödvändiga funktionen i list entiteten) i förutsägelsen.

|Kanoniskt namn|Synonymer|
|--|--|
|USA|USA<br>U. S. A<br>USA<br>USA<br>0|

Klient programmet, till exempel en chatt-robot, kan ställa en uppföljnings fråga, så att Kunden förstår att valet land/region är begränsat och _obligatoriskt_.

### <a name="required-feature-using-regular-expression-entities"></a>Nödvändig funktion med hjälp av reguljära uttrycks enheter

En [entitet för reguljära uttryck](reference-entity-regular-expression.md) som används som en obligatorisk funktion innehåller funktioner för text matchning med RTF-format.

Om du fortsätter med leverans adressen kan du skapa ett reguljärt uttryck som samlar in insamlings regler för post nummer i landet/regionen.

## <a name="global-features"></a>Globala funktioner

Den vanligaste användningen är att använda en funktion för en specifik modell, men du kan konfigurera funktionen som en **Global funktion** för att tillämpa den på hela programmet.

Den vanligaste användningen av en global funktion är att lägga till ytterligare en ord lista, till exempel ord från ett annat språk, till appen. Om dina kunder använder ett primärt språk, men förväntar sig att kunna använda ett annat språk inom samma uttryck, kan du lägga till en funktion som innehåller ord från det sekundära språket.

Eftersom användaren förväntas använda det andra språket för ett avsikts-eller entitets-objekt ska det läggas till i en fras lista med fras listan konfigurerad som en global funktion.

## <a name="best-practices"></a>Bästa praxis
Lär dig [metod tips](luis-concept-best-practices.md).

## <a name="next-steps"></a>Nästa steg

* [Utöka](schema-change-prediction-runtime.md) dina program modeller vid förutsägelse körning
* Se [Lägg till funktioner](luis-how-to-add-features.md) för att lära dig mer om hur du lägger till funktioner i Luis-appen.
