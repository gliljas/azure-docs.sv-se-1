---
title: Översikt över migrering – LUIS
description: Förstå vad som finns på en migrerings Sök väg
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: a27945b5ac3dc2625c9520a8dd413b774b5d7adf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837459"
---
# <a name="migration-in-luis"></a>Migrering i LUIS

Flera objekt finns på en migrerings Sök väg. Använd följande tabell för att förstå vad som påverkas och när du behöver fullständig migrering.

|Område|Beskrivning|Slut för ande datum för migrering|
|--|--|--|
|[Förutsägelse-API: er](luis-migration-api-v3.md)|Migrera till v3-API.|TBD|
|[Redigera API:er](luis-migration-authoring-entities.md)|Migrera till v3-API.|TBD|
|[Skapar resurs](luis-migration-authoring.md)|Migrera från ingen redigerings resurs till att använda en LUIS Authoring-resurs i LUIS-portalen.|30 augusti 2020 |
|[Nödvändig funktion](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|Den här ändringen gjordes i maj 2020 på Build-konferensen och gäller endast v3-redigerings-API: erna där en app använder en begränsad funktion.|19 juni 2020|
|[Sammansatt entitet](migrate-from-composite-entity.md)|Uppgradera den sammansatta entiteten till en enhet för maskin inlärning för att bygga en entitet som tar emot mer kompletta förutsägelser med bättre dekomposition för fel sökning av entiteten.|TBD|
