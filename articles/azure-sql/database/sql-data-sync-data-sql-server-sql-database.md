---
title: Vad är SQL Data Sync för Azure?
description: I den här översikten introduceras SQL Data Sync för Azure, vilket gör att du kan synkronisera data i flera molnbaserade och lokala databaser.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: f4bec780fcf40b4fbee7ccf6c59463a5efb1adc0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048779"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Vad är SQL Data Sync för Azure?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL Data Sync är en tjänst som bygger på Azure SQL Database som låter dig synkronisera data som du väljer i båda riktningarna i flera databaser, både lokalt och i molnet. 

> [!IMPORTANT]
> Azure SQL Data Sync har inte stöd för Azure SQL-hanterad instans för tillfället.


## <a name="overview"></a>Översikt 

Datasynkronisering baseras på begreppet Sync-grupp. En Sync-grupp är en grupp databaser som du vill synkronisera.

Datasynkroniseringen använder en nav-och eker-topologi för att synkronisera data. Du definierar en av databaserna i Sync-gruppen som Hub-databasen. Resten av databaserna är medlems databaser. Synkronisering sker bara mellan hubben och enskilda medlemmar.

- **Hub-databasen** måste vara en Azure SQL Database.
- **Medlems databaserna** kan vara antingen Azure SQL Database, lokala SQL Server databaser eller SQL Server instanser på virtuella Azure-datorer.
- **Sync-databasen** innehåller metadata och logg för datasynkronisering. Sync-databasen måste vara en Azure SQL Database som finns i samma region som Hub-databasen. Sync-databasen är en kund som har skapats och kunden äger.

> [!NOTE]
> Om du använder en lokal databas som en medlems databas måste du [Installera och konfigurera en lokal Sync-agent](sql-data-sync-sql-server-configure.md#add-on-prem).

![Synkronisera data mellan databaser](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

En Sync-grupp har följande egenskaper:

- **Synkroniseringsschemat** beskriver vilka data som synkroniseras.
- **Sync-riktningen** kan vara dubbelriktad eller kan endast flöda i en riktning. Det vill säga att synkroniseringen kan vara *hubb till medlem*eller *medlem i hubben*eller både och.
- **Intervallet för synkronisering** beskriver hur ofta synkronisering sker.
- **Konflikt lösnings principen** är en princip på grup nivå som kan vara en *hubb-WINS* eller *-medlem*.

## <a name="when-to-use"></a>När du ska använda detta

Datasynkronisering är användbart i fall där data måste uppdateras i flera databaser i Azure SQL Database eller SQL Server. Här är de viktigaste användnings fallen för datasynkronisering:

- **Synkronisering av hybrid data:** Med datasynkronisering kan du hålla data synkroniserade mellan databaserna i SQL Server och Azure SQL Database för att aktivera hybrid program. Den här funktionen kan överklaga kunder som överväger att flytta till molnet och som vill placera en del av deras program i Azure.
- **Distribuerade program:** I många fall är det bra att separera olika arbets belastningar i olika databaser. Om du till exempel har en stor produktions databas, men du också behöver köra en rapporterings-eller analys arbets belastning för dessa data, är det bra att ha en andra databas för den här ytterligare arbets belastningen. Den här metoden minimerar prestanda påverkan i produktions arbets belastningen. Du kan använda datasynkronisering för att hålla dessa två databaser synkroniserade.
- **Globalt distribuerade program:** Många företag sträcker sig över flera regioner och till och med flera länder/regioner. För att minimera nätverks fördröjningen är det bäst att ha dina data i en region nära dig. Med datasynkronisering kan du enkelt lagra databaser i regioner runt om i världen.

Datasynkronisering är inte den bästa lösningen i följande scenarier:

| Scenario | Några rekommenderade lösningar |
|----------|----------------------------|
| Haveriberedskap | [Azure geo-redundanta säkerhets kopieringar](automated-backups-overview.md) |
| Läs skala | [Använd skrivskyddade repliker för att belastningsutjämna skrivskyddade arbets belastningar för frågor (för hands version)](read-scale-out.md) |
| ETL (OLTP till OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) eller [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migrering från lokala SQL Server till Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||



## <a name="how-it-works"></a>Så här fungerar det

- **Spårar data ändringar:** Datasynkronisering spårar ändringar med hjälp av INSERT-, Update-och Delete-utlösare. Ändringarna registreras i en sido tabell i användar databasen. Observera att BULK INSERT inte utlösa utlösare som standard. Om FIRE_TRIGGERS inte anges körs inga infognings utlösare. Lägg till alternativet FIRE_TRIGGERS så att datasynkronisering kan spåra dessa infogningar. 
- **Synkroniserar data:** Datasynkronisering är utformad i en nav-och eker-modell. Hubben synkroniseras med varje medlem individuellt. Ändringar från hubben laddas ned till medlemmen och ändringar från medlemmen överförs till hubben.
- **Lösa konflikter:** Datasynkronisering innehåller två alternativ för konflikt lösning, *hubb-WINS* eller *medlems-WINS*.
  - Om du väljer *hubben WINS*skrivs ändringarna i hubben alltid över ändringar i medlemmen.
  - Om du väljer *medlem WINS*skriver ändringarna i medlemmen över ändringarna i hubben. Om det finns fler än en medlem beror det sista värdet på vilka medlemmar som synkroniseras först.

## <a name="compare-with-transactional-replication"></a>Jämför med transaktionell replikering

| | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | – Stöd för aktiv-aktiv<br/>– Dubbelriktad mellan lokala och Azure SQL Database | -Nedre latens<br/>– Transaktionell konsekvens<br/>-Återanvänd befintlig topologi efter migrering <br/>– Stöd för Azure SQL Managed instance |
| Nackdelar | – 5 min eller mer svars tid<br/>– Ingen transaktionell konsekvens<br/>-Högre prestanda påverkan | -Det går inte att publicera från Azure SQL Database <br/>– Kostnad för hög underhåll |

## <a name="get-started"></a>Kom igång 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurera datasynkronisering i Azure Portal

- [Konfigurera Azure SQL Data Sync](sql-data-sync-sql-server-configure.md)
- Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurera datasynkronisering med PowerShell

- [Använd PowerShell för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Använda PowerShell för att synkronisera mellan en databas i Azure SQL Database och en databas i en SQL Server instans](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Granska metod tips för datasynkronisering

- [Metodtips för Azure SQL Data Sync](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Har något gå fel

- [Felsöka problem med Azure SQL Data Sync](../../sql-database/sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Konsekvens och prestanda

### <a name="eventual-consistency"></a>Slutlig konsekvens

Eftersom datasynkroniseringen är utlöst är inte transaktionell konsekvens garanterad. Microsoft garanterar att alla ändringar görs till och med att datasynkroniseringen inte leder till data förlust.

### <a name="performance-impact"></a>Prestanda påverkan

Data Sync använder INSERT-, Update-och Delete-utlösare för att spåra ändringar. Det skapar sido tabeller i användar databasen för ändrings spårning. Dessa ändrings spårnings aktiviteter påverkar databasens arbets belastning. Utvärdera tjänst nivån och uppgradera om det behövs.

Etablering och avetablering när du skapar, uppdaterar och tar bort grupper kan också påverka databasens prestanda.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Krav och begränsningar

### <a name="general-requirements"></a>Allmänna krav

- Varje tabell måste ha en primär nyckel. Ändra inte värdet för primär nyckeln i valfri rad. Om du behöver ändra ett primär nyckel värde tar du bort raden och återskapar den med det nya värdet för primär nyckel.

> [!IMPORTANT]
> Att ändra värdet för en befintlig primär nyckel leder till följande fel beteende:
> - Data mellan hubb och medlem kan gå förlorade även om synkroniseringen inte rapporterar några problem.
> - Det går inte att synkronisera eftersom spårnings tabellen inte har en befintlig rad från källan på grund av den primära nyckel ändringen.

- Ögonblicksbildisolering måste vara aktiverat. Mer information finns i [Ögonblicksbildisolering i SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Allmänna begränsningar

- En tabell kan inte ha en identitets kolumn som inte är primär nyckel.
- En primär nyckel kan inte ha följande data typer: sql_variant, Binary, varbinary, image, XML.
- Var försiktig när du använder följande data typer som primär nyckel, eftersom den precision som stöds endast är för den andra: Time, DateTime, datetime2, DateTimeOffset.
- Namn på objekt (databaser, tabeller och kolumner) får inte innehålla de utskrivbara tecken perioderna (.), vänster hak paren tes ([) eller höger hak paren tes (]).
- Azure Active Directory autentisering stöds inte.
- Tabeller med samma namn men olika schema (till exempel dbo. kunder och Sales. Customers) stöds inte.
- Kolumner med användardefinierade data typer stöds inte
- Det finns inte stöd för att flytta servrar mellan olika prenumerationer. 

#### <a name="unsupported-data-types"></a>Data typer som inte stöds

- -
- SQL/CLR UDT
- XMLSchemaCollection (XML stöds)
- Markör, RowVersion, tidsstämpel, hierarchyid

#### <a name="unsupported-column-types"></a>Kolumn typer som inte stöds

Datasynkronisering kan inte synkronisera skrivskyddade eller systemgenererade kolumner. Ett exempel:

- Beräknade kolumner.
- Systemgenererade kolumner för temporala tabeller.

#### <a name="limitations-on-service-and-database-dimensions"></a>Begränsningar för tjänst-och databas dimensioner

| **Dimensioner**                                                  | **Gräns**              | **Korrigera**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximalt antal Sync-grupper som alla databaser kan tillhöra.       | 5                      |                             |
| Maximalt antal slut punkter i en enskild Sync-grupp              | 30                     |                             |
| Maximalt antal lokala slut punkter i en enda Sync-grupp. | 5                      | Skapa flera Sync-grupper |
| Databas-, tabell-, schema-och kolumn namn                       | 50 tecken per namn |                             |
| Tabeller i en Sync-grupp                                          | 500                    | Skapa flera Sync-grupper |
| Kolumner i en tabell i en Sync-grupp                              | 1000                   |                             |
| Data rad storlek för en tabell                                        | 24 MB                  |                             |
| Minsta synkroniseringstillstånd                                           | 5 minuter              |                             |

> [!NOTE]
> Det kan finnas upp till 30 slut punkter i en enda Sync-grupp om det bara finns en Sync-grupp. Om det finns fler än en Sync-grupp får det totala antalet slut punkter i alla Sync-grupper inte överstiga 30. Om en databas tillhör flera Sync-grupper räknas den som flera slut punkter, inte en.

## <a name="faq-about-sql-data-sync"></a>Vanliga frågor och svar om SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hur mycket kostar SQL Data Sync tjänsten

Det kostar inget att själva SQL Data Sync själva tjänsten. Du kan dock fortfarande samla in data överförings avgifter för data förflyttning in och ut ur din SQL Database-instans. Mer information finns i [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Vilka regioner stöder datasynkronisering

SQL Data Sync är tillgängligt i alla regioner.

### <a name="is-a-sql-database-account-required"></a>Är ett SQL Database konto obligatoriskt

Ja. Du måste ha ett SQL Database-konto för att vara värd för Hub-databasen.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Kan jag endast använda datasynkronisering för att synkronisera mellan SQL Server lokala databaser

Inte direkt. Du kan synkronisera mellan SQL Server lokala databaser indirekt, genom att skapa en nav databas i Azure och sedan lägga till de lokala databaserna i Sync-gruppen.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Kan jag använda Data Sync för att synkronisera mellan SQL-databaser som tillhör olika prenumerationer

Ja. Du kan synkronisera mellan SQL-databaser som tillhör resurs grupper som ägs av olika prenumerationer.

- Om prenumerationerna tillhör samma klient organisation och du har behörighet till alla prenumerationer kan du konfigurera Sync-gruppen i Azure Portal.
- Annars måste du använda PowerShell för att lägga till de synkroniserade medlemmar som tillhör olika prenumerationer.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan jag använda datasynkronisering för att synkronisera mellan SQL-databaser som tillhör olika moln (t. ex. Azures offentliga moln och Azure Kina 21Vianet)

Ja. Du kan synkronisera mellan SQL-databaser som tillhör olika moln, du måste använda PowerShell för att lägga till de synkroniserade medlemmar som hör till de olika prenumerationerna.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan jag använda datasynkronisering för att dirigera data från min produktions databas till en tom databas och sedan synkronisera dem

Ja. Skapa schemat manuellt i den nya databasen genom att skriva skript från originalet. När du har skapat schemat lägger du till tabellerna i en Sync-grupp för att kopiera data och hålla den synkroniserad.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Bör jag använda SQL Data Sync för att säkerhetskopiera och återställa mina databaser

Vi rekommenderar inte att du använder SQL Data Sync för att skapa en säkerhets kopia av dina data. Du kan inte säkerhetskopiera och återställa till en viss tidpunkt eftersom SQL Data Sync-synkronisering inte är versions hantering. Dessutom säkerhetskopierar SQL Data Sync inte andra SQL-objekt, t. ex. lagrade procedurer, och utför inte motsvarigheten till en återställnings åtgärd snabbt.

En rekommenderad säkerhets kopierings teknik finns i [Kopiera en Azure SQL-databas](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan synkronisera krypterade tabeller och kolumner med datasynkronisering

- Om en databas använder Always Encrypted kan du bara synkronisera de tabeller och kolumner som *inte* är krypterade. Du kan inte synkronisera de krypterade kolumnerna eftersom datasynkroniseringen inte kan dekryptera data.
- Om en kolumn använder kryptering på kolumn nivå (CLE) kan du synkronisera kolumnen så länge rad storleken är mindre än den maximala storleken på 24 MB. Datasynkroniseringen behandlar kolumnen som krypteras av nyckeln (CLE) som normala binära data. Om du vill dekryptera data på andra synkroniserade medlemmar måste du ha samma certifikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Stöds sortering i SQL Data Sync

Ja. SQL Data Sync stöder sortering i följande scenarier:

- Om de valda synkroniseringsschemat inte redan finns i din hubb eller medlems databaser, skapar tjänsten automatiskt motsvarande tabeller och kolumner med sorterings inställningarna markerade i de tomma mål databaserna när du distribuerar synkroniseringsresursen.
- Om de tabeller som ska synkroniseras redan finns i både din hubb och medlems databaser, kräver SQL Data Sync att primär nyckel kolumnerna har samma sortering mellan hubben och medlems databaserna för att kunna distribuera Sync-gruppen. Det finns inga sorterings begränsningar för andra kolumner än primär nyckel kolumnerna.

### <a name="is-federation-supported-in-sql-data-sync"></a>Stöds Federation i SQL Data Sync

Federations rot databasen kan användas i SQL Data Sync tjänsten utan någon begränsning. Du kan inte lägga till den federerade databas slut punkten till den aktuella versionen av SQL Data Sync.

## <a name="next-steps"></a>Nästa steg

### <a name="update-the-schema-of-a-synced-database"></a>Uppdatera schemat för en synkroniserad databas

Behöver du uppdatera schemat för en databas i en Sync-grupp? Schema ändringar replikeras inte automatiskt. Vissa lösningar finns i följande artiklar:

- [Automatisera replikeringen av schema ändringar med SQL Data Sync i Azure](../../sql-database/sql-database-update-sync-schema.md)
- [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Övervaka och felsök

Är SQL Data Sync att göra som det ska? Information om hur du övervakar aktiviteter och felsöker problem finns i följande artiklar:

- [Övervaka SQL Data Sync med Azure Monitor loggar](../../sql-database/sql-database-sync-monitor-oms.md)
- [Felsöka problem med Azure SQL Data Sync](../../sql-database/sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Läs mer om Azure SQL Database

Mer information om Azure SQL Database finns i följande artiklar:

- [Översikt över SQL Database](sql-database-paas-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
