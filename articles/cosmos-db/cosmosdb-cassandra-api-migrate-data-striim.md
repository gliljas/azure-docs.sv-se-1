---
title: Migrera data till Azure Cosmos DB API för Cassandra konto med Striims
description: Lär dig hur du använder Striims för att migrera data från en Oracle-databas till ett Azure Cosmos DB API för Cassandra konto.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 50028e81c4ca130aa3266c164a431dc935a271cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730042"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrera data till Azure Cosmos DB API för Cassandra konto med Striims

Striims-avbildningen på Azure Marketplace erbjuder kontinuerlig data förflyttning i real tid från informations lager och databaser till Azure. När du flyttar data kan du utföra en rad avnormalisering, data omvandling, aktivera real tids analys och data rapporterings scenarier. Det är enkelt att komma igång med Striims för att kontinuerligt flytta företags data till Azure Cosmos DB API för Cassandra. Azure tillhandahåller ett Marketplace-erbjudande som gör det enkelt att distribuera Striims och migrera data till Azure Cosmos DB. 

Den här artikeln visar hur du använder Striims för att migrera data från en **Oracle-databas** till ett **Azure Cosmos DB API för Cassandra konto**.

## <a name="prerequisites"></a>Krav

* Om du inte har en [Azure-prenumeration](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

* En Oracle-databas som körs lokalt med vissa data i den.

## <a name="deploy-the-striim-marketplace-solution"></a>Distribuera Striims Marketplace-lösningen

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **skapa en resurs** och Sök efter **striims** på Azure Marketplace. Välj det första alternativet och **skapa**.

   ![Hitta Striims Marketplace-objekt](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Ange sedan konfigurations egenskaperna för Striims-instansen. Striims-miljön har distribuerats på en virtuell dator. I fönstret **grundläggande** anger du **användar namnet för den virtuella datorn**, **lösen ord för virtuell dator** (detta lösen ord används för att använda SSH i den virtuella datorn). Välj din **prenumeration**, **resurs grupp**och **plats information** där du vill distribuera striims. När du är klar väljer du **OK**.

   ![Konfigurera grundläggande inställningar för Striims](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. I fönstret **kluster inställningar för striims** väljer du typ av striims-distribution och storlek på virtuell dator.

   |Inställning | Värde | Beskrivning |
   | ---| ---| ---|
   |Distributions typ för striims |Fristående | Striims kan köras i en **fristående** eller **kluster** distributions typ. Fristående läge distribuerar Striims-servern på en enskild virtuell dator och du kan välja storleken på de virtuella datorerna beroende på vilken data volym som används. Kluster läget distribuerar Striims-servern på två eller flera virtuella datorer med den valda storleken. Kluster miljöer med fler än två noder erbjuder automatisk hög tillgänglighet och redundans.</br></br> I den här självstudien kan du välja fristående alternativ. Använd standard storleken "Standard_F4s" för den virtuella datorn. | 
   | Namnet på Striims-klustret|    <Striim_cluster_Name>|  Namnet på Striims-klustret.|
   | Striims kluster lösen ord|   <Striim_cluster_password>|  Lösen ord för klustret.|

   När du har fyllt formuläret väljer du **OK** för att fortsätta.

1. I fönstret **åtkomst inställningar för striims** konfigurerar du den **offentliga IP-adressen** (Välj standardvärden), **domän namn för striims**, **Administratörs lösen ord** som du vill använda för att logga in på striims-användargränssnittet. Konfigurera ett VNET och undernät (Välj standardvärden). När du har fyllt i informationen väljer du **OK** för att fortsätta.

   ![Åtkomst inställningar för striims](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure kommer att validera distributionen och se till att allt ser bra ut. Det tar några minuter att utföra verifieringen. När verifieringen är klar väljer du **OK**.
  
1. Granska slutligen användnings villkoren och välj **skapa** för att skapa din striims-instans. 

## <a name="configure-the-source-database"></a>Konfigurera käll databasen

I det här avsnittet konfigurerar du Oracle-databasen som källa för data förflyttning.  Du behöver [Oracle JDBC-drivrutinen](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) för att ansluta till Oracle. Om du vill läsa ändringar från din käll-Oracle-databas kan du antingen använda [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) -eller [XStream-API: erna](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). Oracle JDBC-drivrutinen måste finnas i Striimss Java-classpath för att läsa, skriva eller spara data från Oracle Database.

Ladda ned [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) -drivrutinen till din lokala dator. Du kommer att installera det i Striims-klustret senare.

## <a name="configure-target-database"></a>Konfigurera mål databas

I det här avsnittet ska du konfigurera Azure Cosmos DB API för Cassandra-kontot som mål för data förflyttning.

1. Skapa ett [Azure Cosmos DB API för Cassandra konto](create-cassandra-dotnet.md#create-a-database-account) med hjälp av Azure Portal.

1. Navigera till fönstret **datautforskaren** i ditt Azure Cosmos-konto. Välj **ny tabell** för att skapa en ny behållare. Anta att du migrerar *produkter* och *ordnar* data från Oracle Database till Azure Cosmos dB. Skapa ett nytt disk utrymme med namnet **StriimDemo** med en container-behållare. Etablera behållaren med **1000 ru: er**(det här exemplet använder 1000 ru: er, men du bör använda data flödet som beräknas för din arbets belastning) och **/ORDER_ID** som primär nyckel. Dessa värden varierar beroende på dina källdata. 

   ![Skapa API för Cassandra konto](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurera Oracle för att Azure Cosmos DB data flöde

1. Nu ska vi gå tillbaka till Striims. Innan du interagerar med Striims installerar du den Oracle JDBC-drivrutin som du laddade ned tidigare.

1. Navigera till Striims-instansen som du distribuerade i Azure Portal. Välj knappen **Anslut** i den övre meny raden och på fliken **SSH** kopierar du URL: en i **inloggning med fältet lokalt konto för virtuell dator** .

   ![Hämta SSH-URL: en](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Öppna ett nytt terminalfönster och kör SSH-kommandot som du kopierade från Azure Portal. I den här artikeln används Terminal i en MacOS. du kan följa liknande instruktioner med hjälp av SparaTillFil eller en annan SSH-klient på en Windows-dator. När du uppmanas till det skriver du **Ja** för att fortsätta och ange **lösen ordet** som du har angett för den virtuella datorn i föregående steg.

   ![Anslut till Striims VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Öppna sedan en ny terminal-flik för att kopiera filen **ojdbc8. jar** som du laddade ned tidigare. Använd följande SCP-kommando för att kopiera jar-filen från den lokala datorn till mappen tmp i Striims-instansen som körs i Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Kopiera jar-filen från plats datorn till Striims](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Gå sedan tillbaka till det fönster där du gjorde SSH till Striims-instansen och logga in som sudo. Flytta filen **ojdbc8. jar** från katalogen **katalogen/tmp** till **lib** -katalogen för striims-instansen med följande kommandon:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Flytta jar-filen till mappen lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Starta om Striims-servern från samma terminalfönster genom att köra följande kommandon:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striims tar en minut att starta. Om du vill se statusen kör du följande kommando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Nu går du tillbaka till Azure och kopierar den offentliga IP-adressen för den virtuella Striims-datorn. 

   ![Kopiera Striims VM IP-adress](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Om du vill navigera till Striims-webbgränssnittet öppnar du en ny flik i en webbläsare och kopierar den offentliga IP-adressen följt av: 9080. Logga in med **Administratörs** användar namnet, tillsammans med administratörs lösen ordet som du angav i Azure Portal.

   ![Logga in på Striims](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Nu kommer du till start sidan för Striims. Det finns tre olika fönster – **instrument paneler**, **appar**och **SourcePreview**. I fönstret instrument paneler kan du flytta data i real tid och visualisera dem. Fönstret appar innehåller pipelines för strömmande data eller data flöden. Till höger på sidan finns SourcePreview där du kan förhandsgranska dina data innan du flyttar dem.

1. Välj fönstret **appar** , vi fokuserar på det här fönstret för tillfället. Det finns en mängd olika exempel på appar som du kan använda för att lära dig mer om Striims, men i den här artikeln skapar du vår egen. Välj knappen **Lägg till app** i det övre högra hörnet.

   ![Lägg till Striims-appen](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Det finns flera olika sätt att skapa Striims-program. Välj **börja från början** för det här scenariot.

   ![Starta appen från grunden](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Ge ditt program ett eget namn, något som **oraToCosmosDB** och välj **Spara**.

   ![Skapa ett nytt program](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Du kommer till Flow-designern där du kan dra och släppa från Box-kopplingarna för att skapa dina strömmande program. Skriv **Oracle** i Sök fältet och dra och släpp **Oracle CDC** -källan på App-arbetsytan.  

   ![Oracle CDC-källa](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Ange egenskaper för käll konfiguration för din Oracle-instans. Käll namnet är bara en namngivnings konvention för Striims-programmet, du kan använda ett namn som **src_onPremOracle**. Ange även annan information som korttyp, anslutnings-URL, användar namn, lösen ord, tabell namn. Fortsätt genom att välja **Spara** .

   ![Konfigurera käll parametrar](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Klicka nu på Wave-ikonen för strömmen för att ansluta mål Azure Cosmos DBs instansen. 

   ![Anslut till mål](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Innan du konfigurerar målet måste du kontrol lera att du har lagt till ett [Baltimore rot certifikat i Striims Java-miljö](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store).

1. Ange konfigurations egenskaperna för mål Azure Cosmos DBs instansen och välj **Spara** för att fortsätta. Följande är de viktigaste parametrarna för att Observera:

   * **Adapter** -Använd **DatabaseWriter**. När du skriver till Azure Cosmos DB API för Cassandra krävs DatabaseWriter. Cassandra driv rutin 3.6.0 paketeras med Striims. Om DatabaseWriter överskrider antalet ru: er som har allokerats på din Azure Cosmos-behållare kraschar programmet.

   * **Anslutnings-URL** – ange Azure Cosmos DB JDBC-anslutningens URL. URL: en har formatet`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **Användar namn** – ange namnet på ditt Azure Cosmos-konto.
   
   * **Password** – ange primär nyckeln för ditt Azure Cosmos-konto.

   * **Tabeller** -mål tabeller måste ha primär nycklar och primär nycklar kan inte uppdateras.

   ![Konfigurera mål egenskaper](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Konfigurera mål egenskaper](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Nu ska vi gå vidare och köra Striims-programmet. I den övre meny raden väljer du **skapat**och **distribuerar sedan appen**. I fönstret distribution kan du ange om du vill köra vissa delar av programmet på specifika delar av distributions miljön. Eftersom vi kör i en enkel distributions topologi via Azure använder vi standard alternativet.

   ![Distribuera appen](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Nu ska vi gå vidare och förhandsgranska strömmen för att se data som passerar genom Striims. Klicka på våg ikonen och klicka på ögon ikonen bredvid den. När du har distribuerat kan du förhandsgranska data strömmen för att se data som passerar. Välj **våg** ikonen och **ögonglobsikonen** bredvid den. Välj knappen **distribuerad** i det övre meny fältet och välj **Starta App**.

   ![Starta appen](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. Genom att använda en CDC-läsare **(Change data Capture)** hämtar striims endast nya ändringar i databasen. Om du har data som flödar genom käll tabellerna visas den. Men eftersom det är en exempel tabell är källan som inte är ansluten till något program. Om du använder en exempel data Generator kan du infoga en händelse kedja i Oracle-databasen.

1. Du ser data som passerar genom Striims-plattformen. Striims hämtar även alla metadata som är kopplade till din tabell, vilket är till hjälp för att övervaka data och se till att data hamnar på rätt mål.

   ![Konfigurera CDC-pipeline](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Slutligen ska vi logga in på Azure och navigera till ditt Azure Cosmos-konto. Uppdatera Datautforskaren och du kan se att data har anlänt. 

Genom att använda Striims-lösningen i Azure kan du kontinuerligt migrera data till Azure Cosmos DB från olika källor som Oracle, Cassandra, MongoDB och flera andra att Azure Cosmos DB. Om du vill veta mer går du till [striims-webbplatsen](https://www.striim.com/), [laddar ned en kostnads fri 30-dagars utvärderings version av striims](https://go2.striim.com/download-free-trial)och för eventuella problem när du konfigurerar sökvägen för migrering med striims, så skicka en supportbegäran till en [support förfrågan.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Nästa steg

* Om du migrerar data till Azure Cosmos DB SQL API, se [migrera data till API för Cassandra konto med striims](cosmosdb-sql-api-migrate-data-striim.md)

* [Övervaka och felsöka dina data med Azure Cosmos DB mått](use-metrics.md)
