---
title: Kryptering i vila i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB tillhandahåller kryptering av data i vila och hur de implementeras.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: 541c6f4a6c728844524af794f5e2063f4e352cce
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592142"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Datakryptering i Azure Cosmos DB 

Kryptering i vila är en fras som vanligt vis avser kryptering av data på ej beständiga lagrings enheter, till exempel solid state-enheter (SSD) och hård diskar (HDD). Cosmos DB lagrar dess primära databaser på SSD. Dess medie bilagor och säkerhets kopior lagras i Azure Blob Storage, som vanligt vis backas upp av hård diskar. Med den här versionen av kryptering i vila för Cosmos DB krypteras alla databaser, medie bilagor och säkerhets kopior. Dina data krypteras nu i överföring (via nätverket) och i vila (icke-flyktig lagring), vilket ger dig en kryptering från slut punkt till slut punkt.

Som PaaS-tjänst är Azure Cosmos DB mycket lätt att använda. Eftersom alla användar data som lagras i Azure Cosmos DB krypteras i vila och under transport behöver du inte vidta några åtgärder. Ett annat sätt att göra detta är att kryptering i vila är "on" som standard. Det finns inga kontroller att inaktivera eller sätta på. Azure Cosmos DB använder AES-256-kryptering i alla regioner där kontot körs. Vi tillhandahåller den här funktionen medan vi fortsätter att uppfylla våra [service avtal för tillgänglighet och prestanda](https://azure.microsoft.com/support/legal/sla/cosmos-db). Data som lagras i ditt Azure Cosmos-konto är automatiskt och sömlöst krypterade med nycklar som hanteras av Microsoft (tjänst hanterade nycklar). Du kan också välja att lägga till ett andra lager av kryptering med dina egna nycklar enligt beskrivningen i artikeln [Kundhanterade nycklar](how-to-setup-cmk.md) .

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementering av kryptering i vila för Azure Cosmos DB

Kryptering i vila implementeras med hjälp av ett antal säkerhets tekniker, inklusive säkra nyckel lagrings system, krypterade nätverk och kryptografiska API: er. System som dekrypterar och bearbetar data måste kommunicera med system som hanterar nycklar. Diagrammet visar hur lagring av krypterade data och hanteringen av nycklar separeras. 

![Design diagram](./media/database-encryption-at-rest/design-diagram.png)

Basic-flödet för en användar förfrågan är följande:
- Användar databas kontot görs klart och lagrings nycklar hämtas via en begäran till hanterings tjänstens resurs leverantör.
- En användare skapar en anslutning till Cosmos DB via HTTPS/säker transport. (SDK: erna sammanfattar detaljerna.)
- Användaren skickar ett JSON-dokument som ska lagras över den tidigare skapade säkra anslutningen.
- JSON-dokumentet indexeras om inte användaren har inaktiverat indexering.
- Både JSON-dokumentet och index data skrivs till säker lagring.
- Data läses regelbundet från den säkra lagringen och säkerhets kopie ras till Azure-krypterade blob-lagring.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: hur mycket mer kostar Azure Storage kostnad om Kryptering för lagringstjänst har Aktiver ATS?
A: det kostar inget extra.

### <a name="q-who-manages-the-encryption-keys"></a>F: vem hanterar krypterings nycklarna?
A: nycklarna hanteras av Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: hur ofta är krypterings nycklar roterade?
A: Microsoft har en uppsättning interna rikt linjer för rotation av krypterings nyckel, som Cosmos DB följande. De speciella rikt linjerna publiceras inte. Microsoft publicerar [livs cykeln för säkerhets utveckling (SDL)](https://www.microsoft.com/sdl/default.aspx), som visas som en delmängd av intern vägledning och har användbara metod tips för utvecklare.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: kan jag använda mina egna krypterings nycklar?
A: Ja, nu är den här funktionen tillgänglig för de nya Cosmos-kontona och detta bör göras när kontot skapas. Mer information finns i dokumentet med [hanterade nycklar för kund](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk) .

### <a name="q-what-regions-have-encryption-turned-on"></a>F: vilka regioner har kryptering aktiverat?
A: alla Azure Cosmos DB regioner har kryptering aktiverat för alla användar data.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: påverkar kryptering prestanda latens och data flödes service avtal?
A: det finns ingen inverkan eller ändringar i prestanda service avtal nu som kryptering i vila är aktiverat för alla befintliga och nya konton. Du kan läsa mer på sidan [SLA för Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) för att se de senaste garantierna.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: har den lokala emulatorn stöd för kryptering i vila?
A: emulatorn är ett fristående verktyg för utveckling/testning och använder inte de nyckel hanterings tjänster som används av tjänsten för hanterad Cosmos DB. Vi rekommenderar att du aktiverar BitLocker på enheter där du lagrar känsliga test data för emulatorn. [Emulatorn har stöd för att ändra standard data katalogen](local-emulator.md) och på en välkänd plats.

## <a name="next-steps"></a>Nästa steg

* Du kan välja att lägga till ett sekundärt krypterings lager med dina egna nycklar, Läs mer i artikeln [kund hanterade nycklar](how-to-setup-cmk.md) .
* En översikt över Cosmos DB säkerhet och de senaste förbättringarna finns i [säkerhet för Azure Cosmos Database](database-security.md).
* Mer information om Microsoft-certifieringar finns i [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/).
