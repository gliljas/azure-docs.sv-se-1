---
title: Hämtnings-och viktig information för Azure Cosmos-emulatorn
description: Hämta versions anteckningar för Azure Cosmos-emulatorn för olika versioner och information om hämtning.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 6dad625bbbcb8279e83ac42e1492c251d5b0299c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746996"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos-emulator – viktig information och information om hämtning

Den här artikeln visar viktig information om Azure Cosmos-emulatorn med en lista över funktions uppdateringar som har gjorts i varje version. Den visar även den senaste versionen av emulatorn för att hämta och använda.

## <a name="download"></a>Ladda ned

| | |
|---------|---------|
|**Ladda ned MSI**|[Microsoft Download Center](https://aka.ms/cosmosdb-emulator)|
|**Kom igång**|[Utveckla lokalt med Azure Cosmos-emulatorn](local-emulator.md)|

## <a name="release-notes"></a>Viktig information

### <a name="2110"></a>2.11.0

- I den här versionen införs stöd för autoskalning av allokerat data flöde. De här nya funktionerna är möjligheten att ange en anpassad högsta insamlad data flödes nivå i enheter för programbegäran (RU/s), aktivera autoskalning på befintliga databaser och behållare, samt programmerings stöd via Azure Cosmos DB SDK: er.
- Åtgärda ett problem när du skickar frågor till en stor mängd dokument (över 1 GB). emulatorn kommer inte att fungera med intern fel status kod 500.

### <a name="292"></a>2.9.2

- Den här versionen åtgärdar ett fel vid aktivering av stöd för MongoDb-slutpunkt version 3,2. Den lägger också till stöd för att generera ETL-spårningar för fel söknings användning med WPR i stället för LOGMAN.

### <a name="291"></a>2.9.1

- I den här versionen åtgärdas några problem i API-stöd för frågor och en återställning av kompatibilitet med äldre OSs som Windows Server 2012.

### <a name="290"></a>2.9.0

- Den här versionen lägger till alternativet för att ställa in konsekvens till konsekvent prefix och öka Max gränsen för användare och behörigheter.

### <a name="272"></a>2.7.2

- Den här versionen lägger till MongoDB version 3,6-Server stöd för Cosmos-emulatorn. Starta en MongoDB-slutpunkt som är mål version 3,6 av tjänsten genom att starta emulatorn från en administratörs kommando rad med alternativet "/EnableMongoDBEndpoint = 3.6".

### <a name="270"></a>2.7.0

- Den här versionen åtgärdar en regression som hindrar användare från att köra frågor mot SQL API-kontot från emulatorn vid användning av .NET Core-eller x86 .NET-baserade klienter.

### <a name="246"></a>2.4.6

- Den här versionen innehåller paritet med funktionerna i Azure Cosmos-tjänsten från och med den 2019 juli, med undantag som anges i [utveckla lokalt med Azure Cosmos-emulatorn](local-emulator.md). Den åtgärdar också flera buggar som är relaterade till emulatorn stängs av när de anropas via kommando raden och interna IP-adresser åsidosätter för SDK-klienter som använder anslutning med direkt läge.

### <a name="243"></a>2.4.3

- Inaktive rad startar MongoDB-tjänsten som standard. Endast SQL-slutpunkten är aktive rad som standard. Användaren måste starta slut punkten manuellt med hjälp av kommando rads alternativet "/EnableMongoDbEndpoint" i emulatorn. Nu är det som alla andra tjänst slut punkter, till exempel Gremlin, Cassandra och Table.
- En bugg har åtgärd ATS i emulatorn vid start med "/AllowNetworkAccess" där Gremlin, Cassandra och table-slutpunkterna inte korrekt hanterade begär Anden från externa klienter.
- Lägg till direkta anslutnings portar i brand Väggs reglernas inställningar.

### <a name="240"></a>2.4.0

- Ett problem har åtgärd ATS med att emulatorn inte kan starta när appar för nätverks övervakning, till exempel Pulse client, finns på värddatorn.
