---
title: Avgiftsbelagd fakturering för hanterade program med hjälp av tjänsten Marketplace-avläsning | Azure Marketplace
description: 'Den här dokumentationen är en guide för ISV: er som publicerar Azure-program med flexibla fakturerings modeller.'
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 180015a6bab576c02dcfaaea589628e40c6adde4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736180"
---
# <a name="managed-application-metered-billing"></a>Förvaltad fakturering för program 

Med Marketplace-mätnings tjänsten kan du skapa hanterade program planer för Azure Application erbjudanden som debiteras enligt andra enheter än standard enheter. Innan du publicerar det här erbjudandet definierar du de fakturerings dimensioner som bandbredd, biljetter eller e-postmeddelanden som bearbetas. Kunderna betalar sedan enligt deras användning av dessa dimensioner.  Ditt system informerar Microsoft via API: et för avgiftsbelagda avläsning av fakturerbara händelser när de inträffar.

## <a name="prerequisites-for-metered-billing"></a>Krav för fakturering av mätare

För att en hanterad program plan ska kunna använda avgiftsbelagd fakturering måste den:

* Uppfyller alla krav på erbjudandet som beskrivs i [skapa ett erbjudande för Azure-program](create-new-azure-apps-offer.md).
* Konfigurera **priser** för att debitera kunder per månad för din tjänst. Priset kan vara noll om du inte vill debitera en fast avgift och i stället förlita dig på avläsnings fakturering.
* Ange **fakturerings dimensioner** för de mätnings händelser som kunden betalar för ovanpå den fasta taxan.
* Integrera med [API: erna för Marketplace för avläsning](./marketplace-metering-service-apis.md) av program vara för att informera Microsoft om fakturerbara händelser.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hur avgiftsbelagd fakturering passar in med priser

När det kommer att definiera erbjudandet tillsammans med pris sättnings modellerna är det viktigt att förstå erbjudandet.

* Varje Azure Application erbjudande kan ha lösnings mal len eller hanterade program planer.
* Den avgiftsbelagda faktureringen implementeras bara med hanterade program planer.
* Varje hanterad program plan har en associerad pris modell. 
* Pris sättnings modellen har en månatlig återkommande avgift som kan ställas in på $0.
* Utöver den återkommande avgiften kan planen även omfatta valfria dimensioner som används för att debitera kunder för användning som inte ingår i den fasta taxan. Varje dimension representerar en fakturerbar enhet som din tjänst kommer att kommunicera med Microsoft via [API: t för avläsning av marknads](marketplace-metering-service-apis.md)plats.

## <a name="sample-offer"></a>Exempel erbjudande

Contoso är till exempel en utgivare med en hanterad program tjänst som heter Contoso Analytics (CoA). CoA gör det möjligt för kunder att analysera stora mängder data för rapportering och data lager. Contoso registreras som en utgivare i Partner Center för att publicera erbjudanden till Azure-kunder. Det finns två planer kopplade till CoA, som beskrivs nedan:

* Bas plan
    * Analysera 100 GB och generera 100-rapporter för $0/månad
    * Utöver 100 GB betalar du $10 för var 1 GB
    * Utöver 100-rapporterna betalar du $1 för varje rapport
* Premiumplan
    * Analysera 1000 GB och generera 1000-rapporter för $350/månad
    * Utöver 1000 GB betalar du $100 för var 1 TB
    * Utöver 1000-rapporterna betalar du $0,5 för varje rapport

En Azure-kund som prenumererar på äkthets tjänsten kan analysera och generera rapporter per månad baserat på den valda planen. Contoso mäter användningen upp till den inkluderade mängden utan att skicka några användnings händelser till Microsoft. När kunderna förbrukar mer än den mängd som ingår behöver de inte ändra några planer eller göra något annat. Contoso mäter överförbrukningen utöver den mängd som ingår och startar sändning av användnings händelser till Microsoft för ytterligare användning med [API: et för avläsning](./marketplace-metering-service-apis.md)av program vara i Marketplace. Microsoft kommer att debitera kunden för den ytterligare användningen som anges av utgivaren.

## <a name="billing-dimensions"></a>Fakturerings dimensioner

Fakturerings dimensioner används för att kommunicera med kunden om hur de kommer att faktureras för användning av program varan.  Dessa dimensioner används också för att kommunicera användnings händelser till Microsoft. De definieras enligt följande:

* **Dimensions-ID**: den oåterkalleliga identifierare som refereras vid sändning av användnings händelser.
* **Dimensions namn**: det visnings namn som är associerat med dimensionen, till exempel "textmeddelanden har skickats".
* Mått **enhet**: en beskrivning av fakturerings enheten, till exempel "per textmeddelande" eller "per 100-e-post".
* **Pris per enhet**: priset för en dimensions enhet.
* **Inkluderad kvantitet för månatlig period**: den mängd dimension som ingår per månad för kunder som betalar den återkommande månads avgiften måste vara ett heltal.

Fakturerings dimensioner delas i alla planer för ett erbjudande. Vissa attribut gäller för dimensionen i alla planer, och andra attribut är plan-/regionsspecifika.

Attributen, som definierar själva dimensionen, delas i alla planer för ett erbjudande. Innan du publicerar erbjudandet påverkar en ändring av dessa attribut från kontexten för varje plan dimensions definitionen för alla planer. När du har publicerat erbjudandet kan dessa attribut inte längre redige ras. Attributen är:

* Identifierare
* Name
* Måttenhet

De andra attributen för en dimension är speciella för varje plan och kan ha olika värden för att planera.  Innan du publicerar planen kan du redigera dessa värden och endast den här planen kommer att påverkas. När du publicerar planen går dessa attribut inte längre att redige ras. Attributen är:

* Pris per enhet
* Inkluderad kvantitet för månads kunder 
* Inkluderad kvantitet för års kunder 

Dimensioner har också två särskilda begrepp, "aktiverade" och "oändliga":

* **Aktiverat** anger att den här planen ingår i den här dimensionen.  Du kanske vill lämna det här alternativet omarkerat om du skapar en ny plan som inte skickar användnings händelser baserat på den här dimensionen. Dessutom visas nya dimensioner som lagts till efter att en plan först publicerats som "inte aktive rad" i den redan publicerade planen.  En inaktive rad dimension visas inte i några listor med dimensioner för en plan som visas av kunderna.
* **Oändligt**, representerat av oändlighets symbolen "∞", anger att den här planen ingår i den här dimensionen, utan mätning av användning mot den här dimensionen. Om du vill ange för dina kunder att de funktioner som representeras av den här dimensionen ingår i planen, men utan någon begränsad användning.  En dimension med oändlig användning visas i listor över dimensioner för en plan som visas av kunderna.  Den här planen debiteras aldrig för en avgift.

>[!Note] 
>Följande scenarier stöds uttryckligen:  <br> – Du kan lägga till en ny dimension till en ny plan.  Den nya dimensionen kommer inte att aktive ras för redan publicerade planer. <br> – Du kan publicera en plan med en fast månads avgift och utan några dimensioner och sedan lägga till en ny plan och konfigurera en ny dimension för den planen. Den nya dimensionen kommer inte att aktive ras för redan publicerade planer.

## <a name="constraints"></a>Villkor

### <a name="locking-behavior"></a>Låsnings beteende

En dimension som används med Marketplace-avläsning är en förståelse för hur en kund betalar för tjänsten.  All information om en dimension kan inte längre redige ras när ett erbjudande publiceras.  Innan du publicerar erbjudandet är det viktigt att du har definierat dina dimensioner fullständigt.

När ett erbjudande publiceras med en dimension kan informationen om erbjudande nivå för den dimensionen inte längre ändras:

* Identifierare
* Name
* Måttenhet

När en plan har publicerats kan informationen på Plans nivå inte längre ändras:

* Pris per enhet
* Inkluderad kvantitet för månatlig period
* Om dimensionen är aktive rad för planen

>[!Note]
>Det finns ännu inte stöd för att mäta fakturering med Marketplace-avläsning i Azure Government molnet.

### <a name="upper-limits"></a>Övre gränser

Det maximala antalet dimensioner som kan konfigureras för ett enda erbjudande är 18 unika dimensioner.

## <a name="get-support"></a>Få support

Om något av följande fall gäller kan du öppna ett support ärende.

* Tekniska problem med API för avläsning av tjänst i Marketplace.
* Ett problem som måste eskaleras på grund av ett fel eller fel på sidan (t. ex. fel användnings händelse).
* Eventuella andra problem som rör mätning av fakturering.

Följ stegen nedan för att skicka in ditt support ärende:

1. Gå till [support Sidan](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). De första menyerna fylls i automatiskt. För Marketplace-support identifierar du produkt familjen som **moln-och online tjänster**, produkten som **Marketplace-utgivare**. Ändra inte List rutorna för förifyllda menyer.
2. Under "Välj produkt version" väljer du **hantering av Live-erbjudandet**.
3. Under "Välj en kategori som bäst beskriver problemet" väljer du **erbjudande för Azure-program**.
4. Under "Välj ett problem som bäst beskriver problemet" väljer du **avgiftsbelagd fakturering**.
5. Genom att välja knappen **Nästa** kommer du till sidan **ärende information** , där du kan ange mer information om problemet.

Fler support alternativ för utgivare finns i [Support för programmet för kommersiella marknads platser i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [API: er för API för avläsning av tjänst](./marketplace-metering-service-apis.md) .
