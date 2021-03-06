---
title: SQL-hanterad instans säkerhet med Azure AD server-Huvudkonton (inloggningar)
description: Lär dig mer om tekniker och funktioner för att skydda den hanterade Azure SQL-instansen och Använd Azure AD server-Huvudkonton (inloggningar)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 54195652cfdf476f364a31bf710852dab33fb154
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053485"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Självstudie: säkerhet i Azure SQL-hanterad instans med hjälp av Azure AD server-huvudobjekt (inloggningar)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL-hanterad instans innehåller nästan alla säkerhetsfunktioner som den senaste SQL Server lokala (Enterprise Edition) databas motorn har:

- Begränsa åtkomst i en isolerad miljö
- Använd autentiseringsmekanismer som kräver identitet (Azure AD, SQL-autentisering)
- Använda auktorisering med rollbaserade medlemskap och behörigheter
- Aktivera säkerhetsfunktioner

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa en Azure Active Directory (AD) Server huvud namn (inloggning) för en SQL-hanterad instans
> - Bevilja behörigheter till Azure AD server-Huvudkonton (inloggningar) i en SQL-hanterad instans
> - Skapa Azure AD-användare från Azure AD-serverhuvudkonton (inloggningar)
> - Tilldela behörigheter till Azure AD-användare och hantera databassäkerhet
> - Använda personifiering med Azure AD-användare
> - Använda frågor över flera databaser med Azure AD-användare
> - Läs om säkerhetsfunktioner som hotskydd, granskning, datamaskering och kryptering

Mer information finns i [Översikt över Azure SQL Managed instance](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- En SQL-hanterad instans
  - Följ den här artikeln: [snabb start: skapa en SQL-hanterad instans](instance-create-quickstart.md)
- Ha åtkomst till din SQL-hanterade instans och [etablerad en Azure AD-administratör för SQL-hanterad instans](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Du kan läsa mer här:
  - [Anslut ditt program till en SQL-hanterad instans](connect-application-instance.md)
  - [Anslutnings arkitektur för SQL-hanterad instans](connectivity-architecture-overview.md)
  - [Konfigurera och hantera Azure Active Directory-autentisering med SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Begränsa åtkomst 

SQL-hanterade instanser kan nås via en privat IP-adress. Ungefär som en isolerad SQL Server lokal miljö behöver program eller användare åtkomst till SQL Managed instance Network (VNet) innan en anslutning kan upprättas. Mer information finns i följande artikel: [Anslut ditt program till en SQL-hanterad instans](connect-application-instance.md).

Det är också möjligt att konfigurera en tjänst slut punkt på en SQL-hanterad instans, vilket möjliggör offentliga anslutningar på samma sätt som för Azure SQL Database.
Mer information finns i följande artikel [Konfigurera en offentlig slut punkt i Azure SQL-hanterad instans](public-endpoint-configure.md).

> [!NOTE]
> Även om du har aktiverat tjänstens slut punkter gäller inte [Azure SQL Database brand Väggs regler](../database/firewall-configure.md) . Den hanterade Azure SQL-instansen har sin egen [inbyggda brand vägg](management-endpoint-verify-built-in-firewall.md) för att hantera anslutningen.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Skapa ett Azure AD server-huvudobjekt (inloggning) med SSMS

Det första Azure AD server-huvudobjektet (inloggning) kan skapas av standard kontot för SQL-administratörer (icke-Azure AD) som är a `sysadmin` , eller Azure AD-administratören för den SQL-hanterade instans som skapades under etablerings processen. Mer information finns i [etablera en Azure Active Directory administratör för din SQL-hanterade instans](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

I följande artiklar finns exempel på hur du ansluter till din SQL-hanterade instans:

- [Snabb start: Konfigurera virtuell Azure-dator för att ansluta till en SQL-hanterad instans](connect-vm-instance-configure.md)
- [Snabb start: Konfigurera en punkt-till-plats-anslutning till en SQL-hanterad instans från den lokala platsen](point-to-site-p2s-configure.md)

1. Logga in på SQL-hanterad instans med ett standard konto för SQL-inloggning (icke-Azure AD) som är en `sysadmin` Azure AD-administratör för mi, med hjälp av [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

3. Använd följande syntax i frågefönstret för att skapa en inloggning för ett lokalt Azure AD-konto:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Det här exemplet skapar en inloggning för kontot nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Välj **Kör** i verktygsfältet för att skapa inloggningen.

5. Kontrollera den nyligen tillagda inloggningen genom att köra följande T-SQL-kommando:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](./media/aad-security-configure-tutorial/native-login.png)

Mer information finns i [SKAPA INLOGGNING](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="grant-permissions-to-create-logins"></a>Bevilja behörighet att skapa inloggningar

För att andra Azure AD-serverhuvudkonton (inloggningar) ska kunna skapas måste SQL Server-roller eller -behörigheter tilldelas till huvudkontot (SQL eller Azure AD).

### <a name="sql-authentication"></a>SQL-autentisering

- Om inloggningen är ett SQL-huvudkonto kan inloggningar som ingår i rollen `sysadmin` använda kommandot create för att skapa inloggningar för en Azure AD-konto.

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

- För att ge det nyligen skapade Azure AD-serverhuvudkontot (inloggning) möjlighet att skapa andra inloggningar för andra Azure AD-användare, grupper eller program beviljar du inloggningen serverrollen `sysadmin` eller `securityadmin`.
- Som minst måste behörigheten **ALTER ANY LOGIN** ges till Azure AD-serverhuvudkontot (inloggning) för att skapa andra Azure AD-serverhuvudkonton (inloggningar).
- Som standard är standard behörigheten som beviljats nyligen skapade Azure AD server-Huvudkonton (inloggningar) i huvud servern: **Anslut SQL** och **Visa alla databaser**.
- `sysadmin`Server rollen kan beviljas till många Azure AD server-Huvudkonton (inloggningar) i en SQL-hanterad instans.

Så här lägger du till inloggningen till serverrollen `sysadmin`:

1. Logga in på den SQL-hanterade instansen igen eller Använd den befintliga anslutningen till Azure AD-administratören eller SQL-huvudobjektet som är en `sysadmin` .

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Bevilja Azure AD-serverhuvudkontot (inloggning) serverrollen `sysadmin` med hjälp av följande T-SQL-syntax:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    I följande exempel beviljas serverrollen `sysadmin` till inloggningen nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Skapa ytterligare Azure AD-serverhuvudkonton (inloggningar) med hjälp av SSMS

När Azure AD-serverhuvudkontot (inloggning) har skapats och har getts `sysadmin`-privilegier kan den inloggningen skapa ytterligare inloggningar med hjälp av satsen **FROM EXTERNAL PROVIDER** (Från extern provider) med **CREATE LOGIN** (Skapa inloggning).

1. Anslut till den SQL-hanterade instansen med Azure AD server-huvudobjektet (inloggning) med hjälp av SQL Server Management Studio. Ange värd namnet för SQL-hanterad instans. Det finns tre alternativ att välja mellan när du loggar in med ett Azure AD-konto för autentisering i SSMS:

   - Active Directory – Universell med stöd för MFA
   - Active Directory – lösenord
   - Active Directory – integrerad </br>

     ![ssms-login-prompt.png](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Mer information finns i följande artikel: [Universal Authentication (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md)

1. Välj **Active Directory – Universell med stöd för MFA**. Sedan öppnas ett inloggningsfönster för Multi-Factor Authentication (MFA). Logga in med ditt Azure AD-lösenord.

    ![mfa-login-prompt.png](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. I SSMS **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. Använd följande syntax i frågefönstret för att skapa en inloggning för ytterligare ett Azure AD-konto:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Det här exemplet skapar en inloggning för Azure AD-användaren bob@aadsqlmi.net, vars domän aadsqlmi.net är federerad med Azure AD:s aadsqlmi.onmicrosoft.com.

    Kör följande T-SQL-kommando. Federerade Azure AD-konton är ersättningar för SQL-hanterade instanser för lokala Windows-inloggningar och användare.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Skapa en databas i SQL-hanterad instans med hjälp av syntaxen [create Database](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) . Den här databasen används för att testa användarinloggningar i nästa avsnitt.
    1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
    1. Använd följande syntax i frågefönstret för att skapa en databas med namnet **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Skapa en SQL-hanterad instans inloggning för en grupp i Azure AD. Gruppen måste finnas i Azure AD innan du kan lägga till inloggningen till SQL-hanterad instans. Läs [Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Skapa en grupp _mygroup_ och lägg till medlemmar i den här gruppen.

1. Öppna ett nytt frågefönster i SQL Server Management Studio.

    Det här exemplet förutsätter att det finns en grupp som heter _mygroup_ i Azure AD. Kör följande kommando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Som ett test loggar du in på den SQL-hanterade instansen med den nyligen skapade inloggningen eller gruppen. Öppna en ny anslutning till SQL-hanterad instans och Använd den nya inloggningen vid autentisering.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga** för den nya anslutningen.
1. Kontrollera serverbehörigheter för det nyligen skapade Azure AD-serverhuvudkontot (inloggning) genom att köra följande kommando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Azure AD-gäst användare stöds för inloggningar med SQL-hanterade instanser, endast när de läggs till som en del av en Azure AD-grupp. En Azure AD-gäst användare är ett konto som är inbjuden till den Azure AD som den SQL-hanterade instansen tillhör, från en annan Azure AD. Till exempel kan joe@contoso.com (Azure AD-konto) eller steve@outlook.com (MSA-konto) läggas till en grupp i Azure AD aadsqlmi. När användarna har lagts till i en grupp kan en inloggning skapas i **huvud** databasen för SQL-hanterad instans för gruppen med hjälp av syntaxen för att **Skapa inloggning** . Gäst användare som är medlemmar i den här gruppen kan ansluta till den SQL-hanterade instansen med hjälp av sina aktuella inloggningar (till exempel joe@contoso.com eller steve@outlook.com ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Skapa en Azure AD-användare från Azure AD server-huvudobjektet (inloggning)

Auktorisering till enskilda databaser fungerar ungefär på samma sätt i SQL-hanterad instans på samma sätt som med SQL Server lokalt. En användare kan skapas från en befintlig inloggning i en databas och få behörigheter för databasen eller läggas till i en databasroll.

Nu när vi har skapat en databas som heter **MyMITestDB**, och en inloggning som endast har standardbehörigheterna, är nästa steg att skapa en användare från den inloggningen. Vid det tillfället kan inloggningen ansluta till SQL-hanterad instans och se alla databaser, men de kan inte samverka med databaserna. Om du loggar in med Azure AD-kontot som har standardbehörigheterna och försöker expandera den nyligen skapade databasen visas följande fel:

![ssms-db-not-accessible.png](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Läs mer om hur du beviljar databasbehörigheter i [Getting Started with Database Engine Permissions](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions) (Komma igång med behörigheter för databasmotorn).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Skapa en Azure AD-användare och skapa en exempeltabell

1. Logga in på den SQL-hanterade instansen med ett `sysadmin` konto med hjälp av SQL Server Management Studio.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. I frågefönstret använder du följande syntax för att skapa en Azure AD-användare från ett Azure AD-serverhuvudkonto (inloggning):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    I följande exempel skapas en användare bob@aadsqlmi.net från inloggningen bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Det finns även stöd för att skapa en Azure AD-användare från ett Azure AD-serverhuvudkonto (inloggning) som är en grupp.

    I följande exempel skapas en inloggning för Azure AD-gruppen _mygroup_ som finns i din Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Alla användare som tillhör **mygroup** kan komma åt databasen **MyMITestDB**.

    > [!IMPORTANT]
    > När du skapar en **USER** (Användare) från ett Azure AD-serverhuvudkonto (inloggning) anger du samma user_name som login_name från **LOGIN** (Inloggning).

    Mer information finns i [SKAPA ANVÄNDARE](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Skapa en testtabell med följande T-SQL-kommando i ett nytt frågefönster:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Skapa en anslutning i SSMS med användaren som har skapats. Lägg märke till att du inte kan se tabellen **TestTable** som skapades av `sysadmin` tidigare. Vi behöver ge användaren behörigheter att läsa data från databasen.

1. Du kan kontrollera användarens aktuella behörighet genom att köra följande kommando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Lägga till användare till roller på databasnivå

För att användaren ska kunna se data i databasen ger vi användaren [roller på databasnivå](/sql/relational-databases/security/authentication-access/database-level-roles).

1. Logga in på den SQL-hanterade instansen med ett `sysadmin` konto med hjälp av SQL Server Management Studio.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Bevilja Azure AD-användaren `db_datareader`-databasrollen med hjälp av följande T-SQL-syntax:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    I följande exempel får användaren bob@aadsqlmi.net och gruppen _mygroup_`db_datareader` behörigheter för **MyMITestDB**-databasen:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Kontrollera att Azure AD-användaren som har skapats i databasen finns genom att köra följande kommando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Skapa en ny anslutning till den SQL-hanterade instansen med användaren som har lagts till i `db_datareader` rollen.
1. Expandera databasen i **Object Explorer** du vill se tabellen.

    ![ssms-test-table.png](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Öppna ett nytt frågefönster och kör följande SELECT-sats:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Kan du se data från tabellen? Du bör se de kolumner som returneras.

    ![ssms-test-table-query.png](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Personifiera Azure AD-säkerhetsobjekt på server nivå (inloggningar)

SQL-hanterad instans stöder personifiering av Azure AD-säkerhetsobjekt på server nivå (inloggningar).

### <a name="test-impersonation"></a>Testa personifiering

1. Logga in på den SQL-hanterade instansen med ett `sysadmin` konto med hjälp av SQL Server Management Studio.

1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.

1. Kör följande kommando i frågefönstret för att skapa en lagrad procedur:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Använd följande kommando för att se att den användare som du personifierar när du kör den lagrade proceduren är **bob \@ aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Testa personifieringen med hjälp av instruktionen KÖRA SOM INLOGGNING:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Endast SQL-huvudkonton på servernivå (inloggningar) som ingår i rollen `sysadmin` kan utföra följande åtgärder som riktar in sig på Azure AD-huvudkonton:
>
> - KÖRA SOM ANVÄNDARE
> - KÖRA SOM INLOGGNING

## <a name="use-cross-database-queries"></a>Använda frågor över databaser

Databasöverskridande frågor stöds för Azure AD-konton med Azure AD-serverhuvudkonton (inloggningar). Vi måste skapa en annan databas och tabell för att testa en databasöverskridande fråga med en Azure AD-grupp. Du behöver inte skapa en annan databas och tabell om det redan finns.

1. Logga in på den SQL-hanterade instansen med ett `sysadmin` konto med hjälp av SQL Server Management Studio.
1. I **Object Explorer** högerklickar du på servern och väljer **Ny fråga**.
1. I frågefönstret använder du följande kommando för att skapa en databas med namnet **MyMITestDB2** och en tabell med namnet **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. I ett nytt frågefönster kör du följande kommando för att skapa användaren _mygroup_ i den nya databasen **MyMITestDB2**, och beviljar SELECT-behörigheter för databasen för _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Logga in på den SQL-hanterade instansen med SQL Server Management Studio som medlem i _gruppen Azure AD Group._ Öppna ett nytt frågefönster och kör instruktionen SELECT över flera databaser:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Du bör se tabellresultaten från **TestTable2**.

## <a name="additional-supported-scenarios"></a>Ytterligare scenarier som stöds

- SQL Agent-hantering och -jobbkörningar stöds för Azure AD-serverhuvudkonton (inloggningar).
- Åtgärder för databassäkerhetskopiering och -återställning kan köras av Azure AD-serverhuvudkonton (inloggningar).
- [Granskning](auditing-configure.md) av alla instruktioner som är relaterade till Azure AD-serverhuvudkonton (inloggningar) och autentiseringshändelser.
- Dedikerad administratörsanslutning för Azure AD-serverhuvudkonton (inloggningar) som är medlemmar i serverrollen `sysadmin`.
- Azure AD-serverhuvudkonton (inloggningar) stöds med hjälp av den [sqlcmd-verktyget](/sql/tools/sqlcmd-utility) och [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)-verktyget.
- Inloggningsutlösare stöds för inloggningshändelser som kommer från Azure AD-serverhuvudkonton (inloggningar).
- Service Broker och DBMail kan konfigureras med hjälp av Azure AD-serverhuvudkonton (inloggningar).

## <a name="next-steps"></a>Nästa steg

### <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

I artikeln om [säkerhets funktioner för SQL-hanterade instanser](sql-managed-instance-paas-overview.md#security-features) finns en omfattande lista över olika sätt att skydda databasen. Följande säkerhetsfunktioner diskuteras:

- [SQL-hanterad instans granskning](auditing-configure.md)
- [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Hotidentifiering](threat-detection-configure.md)
- [Dynamisk datamaskning](/sql/relational-databases/security/dynamic-data-masking)
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security)
- [Transparent data kryptering (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Hanterade instansfunktioner

En fullständig översikt över funktioner i SQL-hanterad instans finns i:

> [!div class="nextstepaction"]
> [SQL-hanterade instans funktioner](sql-managed-instance-paas-overview.md)
