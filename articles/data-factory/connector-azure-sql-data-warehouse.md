---
title: Kopiera och transformera data i Azure Synapse Analytics
description: Lär dig hur du kopierar data till och från Azure Synapse Analytics och transformerar data i Azure Synapse Analytics med hjälp av Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: c72a49d938bbf89b727d3f3e5896359df3c3911d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84019032"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera och transformera data i Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Aktuell version](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Azure Synapse Analytics och använda data flöden för att transformera data i Azure Data Lake Storage Gen2. Läs den [inledande artikeln](introduction.md)om du vill veta mer om Azure Data Factory.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här Azure Synapse Analytics-anslutningen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [mat ris tabell med stöd för källa/mottagare](copy-activity-overview.md)
- [Mappa data flöde](concepts-data-flow-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)

För kopierings aktivitet stöder den här Azure Synapse Analytics-anslutaren dessa funktioner:

- Kopiera data med hjälp av SQL-autentisering och Azure Active Directory (Azure AD) Application token-autentisering med tjänstens huvud namn eller hanterade identiteter för Azure-resurser.
- Som källa hämtar du data med hjälp av en SQL-fråga eller lagrad procedur.
- Läs in data med hjälp av [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) -eller [copy-instruktionen](#use-copy-statement) (för hands version) eller Mass infogning i som mottagare. Vi rekommenderar PolyBase-eller COPY-instruktion (för hands version) för bättre kopierings prestanda.

> [!IMPORTANT]
> Om du kopierar data genom att använda Azure Data Factory Integration Runtime konfigurerar du en [brand Väggs regel på server nivå](../azure-sql/database/firewall-configure.md) så att Azure-tjänster kan komma åt den [logiska SQL-servern](../azure-sql/database/logical-servers.md).
> Om du kopierar data med hjälp av en lokal integration runtime konfigurerar du brand väggen för att tillåta lämpligt IP-intervall. Detta intervall inkluderar datorns IP-adress som används för att ansluta till Azure Synapse Analytics.

## <a name="get-started"></a>Kom igång

> [!TIP]
> Använd PolyBase för att läsa in data i Azure Synapse Analytics för att uppnå bästa prestanda. Mer information finns i avsnittet [använda PolyBase för att läsa in data i avsnittet om Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) . För en genom gång med ett användnings fall läser du [läsa in 1 TB i Azure Synapse Analytics under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory entiteter som är speciella för en Azure Synapse Analytics-anslutning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för en länkad Azure Synapse Analytics-tjänst:

| Egenskap            | Beskrivning                                                  | Obligatorisk                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| typ                | Egenskapen Type måste anges till **AzureSqlDW**.             | Ja                                                          |
| Begär    | Ange den information som krävs för att ansluta till Azure Synapse Analytics-instansen för egenskapen **ConnectionString** . <br/>Markera det här fältet som en SecureString för att lagra det på ett säkert sätt i Data Factory. Du kan också ange lösen ord/tjänstens huvud namn i Azure Key Vault, och om det är SQL-autentisering hämtar du `password` konfigurationen från anslutnings strängen. Se JSON-exemplet under tabellen och [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md) artikel med mer information. | Ja                                                          |
| servicePrincipalId  | Ange programmets klient-ID.                         | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten. |
| tenant              | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja, när du använder Azure AD-autentisering med ett huvud namn för tjänsten. |
| connectVia          | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Du kan använda Azure Integration Runtime eller en egen värd för integration Runtime (om ditt data lager finns i ett privat nätverk). Om inget värde anges används standard Azure Integration Runtime. | Nej                                                           |

För olika typer av autentiseringar, se följande avsnitt om krav respektive JSON-exempel:

- [SQL-autentisering](#sql-authentication)
- Azure AD Application token-autentisering: [tjänstens huvud namn](#service-principal-authentication)
- Azure AD Application token-autentisering: [hanterade identiteter för Azure-resurser](#managed-identity)

>[!TIP]
>Om du stöter på fel med felkoden "UserErrorFailedToConnectToSqlServer" och "Message" som "sessionsgränsen för databasen är XXX och har nåtts.", Lägg till `Pooling=false` i anslutnings strängen och försök igen.

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Exempel på länkade tjänster som använder SQL-autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Lösen ord i Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda tjänstens huvud-baserade Azure AD Application token-autentisering:

1. **[Skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** från Azure Portal. Anteckna program namnet och följande värden som definierar den länkade tjänsten:

   - Program-ID
   - Program nyckel
   - Klientorganisations-ID

2. **[Etablera en Azure Active Directory administratör](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** för servern i Azure Portal om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören får fullständig åtkomst till databasen.

3. **[Skapa inneslutna databas användare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** för tjänstens huvud namn. Anslut till det data lager från eller till vilket du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ändra användar behörighet. Kör följande T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Ge tjänstens huvud namn nödvändiga behörigheter** som du vanligt vis använder för SQL-användare eller andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurera en länkad Azure Synapse Analytics-tjänst** i Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Exempel på länkad tjänst som använder autentisering av tjänstens huvud namn

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Hanterade identiteter för Azure-resurser-autentisering

En data fabrik kan associeras med en [hanterad identitet för Azure-resurser](data-factory-service-identity.md) som representerar den aktuella fabriken. Du kan använda den här hanterade identiteten för Azure Synapse Analytics-autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till ditt data lager med hjälp av den här identiteten.

Följ dessa steg om du vill använda hanterad identitets autentisering:

1. **[Etablera en Azure Active Directory administratör](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** för servern på Azure Portal om du inte redan har gjort det. Azure AD-administratören kan vara en Azure AD-användare eller Azure AD-grupp. Hoppa över steg 3 och 4 om du beviljar gruppen med hanterad identitet en administratörs roll. Administratören får fullständig åtkomst till databasen.

2. **[Skapa inneslutna databas användare](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** för den Data Factory hanterade identiteten. Anslut till det data lager från eller till vilket du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ändra användar behörighet. Kör följande T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Ge den Data Factory hanterade identiteten nödvändiga behörigheter** som du vanligt vis använder för SQL-användare och andra. Kör följande kod eller referera till fler alternativ [här](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Om du vill använda PolyBase för att läsa in data kan du läsa om den [nödvändiga databas behörigheten](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Konfigurera en länkad Azure Synapse Analytics-tjänst** i Azure Data Factory.

**Exempel:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) .

Följande egenskaper stöds för data uppsättningen för Azure Synapse Analytics:

| Egenskap  | Beskrivning                                                  | Obligatorisk                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Data uppsättningens **typ** -egenskap måste anges till **AzureSqlDWTable**. | Ja                         |
| schema | Schemats namn. |Nej för källa, Ja för mottagare  |
| tabell | Namnet på tabellen/vyn. |Nej för källa, Ja för mottagare  |
| tableName | Namnet på tabellen/vyn med schemat. Den här egenskapen stöds för bakåtkompatibilitet. Använd och för ny arbets `schema` belastning `table` . | Nej för källa, Ja för mottagare |

### <a name="dataset-properties-example"></a>Exempel på data uppsättnings egenskaper

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera aktivitets egenskaper

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Azure Synapse Analytics-källan och mottagare.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics som källa

Om du vill kopiera data från Azure Synapse Analytics ställer du in egenskapen **Type** i källan för kopierings aktiviteten till **SqlDWSource**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **källa** :

| Egenskap                     | Beskrivning                                                  | Obligatorisk |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | **Typ** egenskapen för kopierings aktivitets källan måste anges till **SqlDWSource**. | Ja      |
| sqlReaderQuery               | Använd den anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej       |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från käll tabellen. Den sista SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej       |
| storedProcedureParameters    | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn-eller värdepar. Namn och Skift läge för parametrar måste matcha namn och Skift läge för parametrarna för den lagrade proceduren. | Nej       |
| isolationLevel | Anger transaktions låsnings beteendet för SQL-källan. Tillåtna värden är: **ReadCommitted** (standard), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Mer information finns i [det här dokumentet](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | Nej |

**Exempel: använda SQL-fråga**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel: använda lagrad procedur**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Exempel på lagrad procedur:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Azure Synapse Analytics som mottagare

Azure Data Factory stöder tre sätt att läsa in data i SQL Data Warehouse.

![Kopierings alternativ för SQL DW-mottagare](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Använd PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [Använd COPY-instruktion (förhands granskning)](#use-copy-statement)
- Använd Mass infogning

Det snabbaste och mest skalbara sättet att läsa in data är genom [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) eller [kopierings instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version).

Om du vill kopiera data till Azure SQL Data Warehouse anger du mottagar typen i kopierings aktivitet till **SqlDWSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** :

| Egenskap          | Beskrivning                                                  | Obligatorisk                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Egenskapen **Type** för kopierings aktivitetens Sink måste anges till **SqlDWSink**. | Ja                                           |
| allowPolyBase     | Anger om PolyBase ska användas för att läsa in data i SQL Data Warehouse. `allowCopyCommand`och `allowPolyBase` kan inte båda vara sant. <br/><br/>Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnitt för begränsningar och information.<br/><br/>Tillåtna värden är **True** och **false** (standard). | Nej.<br/>Använd när du använder PolyBase.     |
| polyBaseSettings  | En grupp egenskaper som kan anges när `allowPolybase` egenskapen har angetts till **True**. | Nej.<br/>Använd när du använder PolyBase. |
| allowCopyCommand | Anger om [kopierings instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version) ska användas för att läsa in data i SQL Data Warehouse. `allowCopyCommand`och `allowPolyBase` kan inte båda vara sant. <br/><br/>Se [använda Copy-instruktionen för att läsa in data i Azure SQL Data Warehouse](#use-copy-statement) avsnittet för begränsningar och information.<br/><br/>Tillåtna värden är **True** och **false** (standard). | Nej.<br>Använd när du använder COPY. |
| copyCommandSettings | En grupp egenskaper som kan anges när `allowCopyCommand` egenskapen har angetts till true. | Nej.<br/>Använd när du använder COPY. |
| writeBatchSize    | Antal rader som ska infogas i SQL-tabellen **per batch**.<br/><br/>Det tillåtna värdet är **Integer** (antal rader). Som standard bestämmer Data Factory dynamiskt rätt batchstorlek baserat på rad storleken. | Nej.<br/>Använd när du använder Mass infogning.     |
| writeBatchTimeout | Vänte tid för att slutföra batch-infogningen innan tids gränsen uppnåddes.<br/><br/>Det tillåtna värdet är **TimeSpan**. Exempel: "00:30:00" (30 minuter). | Nej.<br/>Använd när du använder Mass infogning.        |
| preCopyScript     | Ange en SQL-fråga för kopierings aktivitet som ska köras innan data skrivs till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen för att rensa de förinstallerade data. | Nej                                            |
| tableOption | Anger om mottagar tabellen ska skapas automatiskt om den inte finns, baserat på käll schemat. Det går inte att skapa en automatisk tabell när mellanlagrad kopiering har kon figurer ATS i kopierings aktiviteten. Tillåtna värden är: `none` (standard), `autoCreate` . |Nej |
| disableMetricsCollection | Data Factory samlar in mått som SQL Data Warehouse DWU: er för att kopiera prestanda optimering och rekommendationer. Om du är orolig för det här beteendet anger `true` du för att inaktivera det. | Nej (standard är `false` ) |

#### <a name="sql-data-warehouse-sink-example"></a>Exempel på SQL Data Warehouse mottagare

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data i Azure SQL Data Warehouse

Att använda [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in stora mängder data i Azure Synapse Analytics med högt data flöde. Du ser en stor ökning i data flödet med hjälp av PolyBase i stället för standard mekanismen för BULKINSERT. En genom gång med ett användnings fall finns i [läsa in 1 TB i Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Om dina källdata finns i **Azure Blob, Azure Data Lake Storage gen1 eller Azure Data Lake Storage Gen2**och **formatet är PolyBase-kompatibelt**, kan du använda kopierings aktivitet för att direkt anropa PolyBase för att låta Azure SQL Data Warehouse hämta data från källan. Mer information finns i **[direkt kopiering med hjälp av PolyBase](#direct-copy-by-using-polybase)**.
- Om käll data lagret och formatet inte ursprungligen stöds av PolyBase använder du den **[mellanlagrade kopian med PolyBase](#staged-copy-by-using-polybase)** -funktionen i stället. Funktionen för mellanlagrad kopiering ger också bättre data flöde. Den konverterar automatiskt data till PolyBase-kompatibelt format, lagrar data i Azure Blob Storage. sedan anropar PolyBase för att läsa in data i SQL Data Warehouse.

> [!TIP]
> Lär dig mer om [metod tips för att använda PolyBase](#best-practices-for-using-polybase).

Följande PolyBase-inställningar stöds under `polyBaseSettings` kopierings aktivitet:

| Egenskap          | Beskrivning                                                  | Obligatorisk                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Anger antalet rader eller procent av rader som kan avvisas innan frågan Miss lyckas.<br/><br/>Läs mer om polybases avvisnings alternativ i avsnittet arguments i [skapa en extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Tillåtna värden är 0 (standard), 1, 2 osv. | Nej                                            |
| rejectType        | Anger om alternativet **rejectValue** är ett litteralt värde eller en procents ATS.<br/><br/>Tillåtna värden är **värde** (standard) och **procent**. | Nej                                            |
| rejectSampleValue | Anger det antal rader som ska hämtas innan PolyBase beräknar om procent andelen avvisade rader.<br/><br/>Tillåtna värden är 1, 2 osv. | Ja, om **rejectType** är **procent andelen**. |
| useTypeDefault    | Anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen.<br/><br/>Lär dig mer om den här egenskapen från avsnittet argument i [Skapa externt fil format (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **True** och **false** (standard).<br><br> | Nej                                            |

### <a name="direct-copy-by-using-polybase"></a>Direkt kopia med hjälp av PolyBase

SQL Data Warehouse PolyBase stöder direkt Azure Blob, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet använder du PolyBase för att kopiera direkt från käll data lagret till Azure Synapse Analytics. Annars använder du [mellanlagrad kopiering med PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Om du vill kopiera data på ett effektivt sätt till SQL Data Warehouse kan du läsa mer från [Azure Data Factory gör det ännu enklare och bekvämt att få insikter från data när du använder data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls kontrollerar Azure Data Factory inställningarna och återgår automatiskt till BULKINSERT-mekanismen för data förflyttning.

1. Den **länkade käll tjänsten** har följande typer och autentiseringsmetoder:

    | Typ av käll data lager som stöds                             | Typ av källtyp som stöds                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure-blobb](connector-azure-blob-storage.md)                | Autentisering av konto nyckel, hanterad identitets autentisering |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autentisering av tjänstens huvudnamn                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autentisering av konto nyckel, hanterad identitets autentisering |

    >[!IMPORTANT]
    >Om din Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig mer om de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitets verifiering](connector-azure-blob-storage.md#managed-identity) och [Azure Data Lake Storage Gen2-hanterad identitets verifiering](connector-azure-data-lake-storage.md#managed-identity) .

2. **Käll data formatet** är av **Parquet**, **Orc**eller **avgränsad text**, med följande konfigurationer:

   1. Mappsökvägen innehåller inte något filter för jokertecken.
   2. Fil namnet är tomt eller pekar på en enskild fil. Om du anger jokertecken som fil namn i kopierings aktiviteten, kan det bara vara `*` eller `*.*` .
   3. `rowDelimiter`är **standard**, **\n**, **\r\n**eller **\r**.
   4. `nullValue`är kvar som standard eller inställt på **tom sträng** ("") och `treatEmptyAsNull` är kvar som standard eller anges till sant.
   5. `encodingName`är kvar som standard eller anges till **UTF-8**.
   6. `quoteChar`, `escapeChar` , och har `skipLineCount` inte angetts. Huvud support över huvud raden, som kan konfigureras som `firstRowAsHeader` i ADF.
   7. `compression`kan vara **Ingen komprimering**, **gzip**eller **DEFLATE**.

3. Om din källa är en mapp `recursive` måste i kopierings aktiviteten vara inställd på sant.

4. `wildcardFolderPath`, `wildcardFilename` , `modifiedDateTimeStart` , `modifiedDateTimeEnd` och `additionalColumns` har inte angetts.

>[!NOTE]
>Om din källa är en mapp hämtas filer från mappen och alla dess undermappar, och data hämtas inte från filer vars fil namn börjar med en understrykning (_) eller en punkt (.), enligt beskrivningen [här – plats argument](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Mellanlagrad kopia med PolyBase

När dina källdata inte är internt kompatibla med PolyBase kan du aktivera data kopiering via en tillfällig mellanlagrings instans av Azure Blob Storage (den kan inte vara Azure Premium Storage). I det här fallet konverterar Azure Data Factory automatiskt data så att de uppfyller data format kraven för PolyBase. Sedan anropar den PolyBase för att läsa in data i SQL Data Warehouse. Slutligen rensar den temporära data från blob-lagringen. Se [mellanlagrad kopia](copy-activity-performance-features.md#staged-copy) för information om hur du kopierar data via en mellanlagringsplats av Azure Blob Storage-instansen.

Om du vill använda den här funktionen skapar du en [länkad Azure Blob Storage-tjänst](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure Storage-kontot med Interim Blob Storage. Ange sedan `enableStaging` egenskaperna och `stagingSettings` för kopierings aktiviteten som det visas i följande kod.

>[!IMPORTANT]
>Om din mellanlagrings Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitetsautentisering](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Metod tips för att använda PolyBase

Följande avsnitt innehåller metod tips utöver de som nämns i [metod tips för Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Nödvändig databas behörighet

Om du vill använda PolyBase måste användaren som läser in data i SQL Data Warehouse ha [behörigheten "kontroll"](https://msdn.microsoft.com/library/ms191291.aspx) för mål databasen. Ett sätt att åstadkomma detta är att lägga till användaren som en medlem i **db_owner** -rollen. Lär dig hur du gör i [SQL Data Warehouse översikt](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Rad storlek och data typs gränser

PolyBase-belastningarna är begränsade till rader som är mindre än 1 MB. Den kan inte användas för att läsa in till VARCHR (MAX), NVARCHAR (MAX) eller VARBINARY (MAX). Mer information finns i [SQL Data Warehouse tjänst kapacitets gränser](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När dina källdata har rader som är större än 1 MB, kanske du vill dela käll tabellerna lodrätt i flera små. Kontrol lera att den största storleken på varje rad inte överskrider gränsen. De mindre tabellerna kan sedan läsas in med PolyBase och sammanfogas tillsammans i Azure Synapse Analytics.

Du kan också använda icke-PolyBase för att läsa in data med hjälp av ADF genom att inaktivera inställningen Tillåt PolyBase för data med sådana breda kolumner.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resurs klass

För att uppnå bästa möjliga data flöde tilldelar du en större resurs klass till användaren som läser in data i SQL Data Warehouse via PolyBase.

#### <a name="polybase-troubleshooting"></a>PolyBase-fel sökning

**Läser in till decimal kolumn**

Om dina källdata är i text format eller andra icke-PolyBase-kompatibla lager (med mellanlagrad kopia och PolyBase) och den innehåller ett tomt värde som ska läsas in i SQL Data Warehouse decimal kolumn, kan du trycka på följande fel:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Lösningen är att avmarkera alternativet**Använd standard**alternativet (som falskt) i kopierings aktiviteten handfat-> PolyBase-inställningar. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" är en PolyBase-inbyggd konfiguration som anger hur du ska hantera saknade värden i avgränsade textfiler när PolyBase hämtar data från text filen.

**`tableName`i Azure Synapse Analytics**

Följande tabell innehåller exempel på hur du anger egenskapen **TableName** i JSON-datauppsättningen. Det visar flera kombinationer av schema-och tabell namn.

| DB-schema | Tabellnamn | **TableName** JSON-egenskap               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | Min tabell eller dbo. Min tabell eller [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. Min tabell eller [dbo1]. MyTable          |
| dbo       | My. table   | [My. table] eller [dbo]. [My. table]            |
| dbo1      | My. table   | [dbo1]. [My. table]                         |

Om du ser följande fel kan problemet vara det värde som du har angett för egenskapen **TableName** . I tabellen ovan finns det korrekta sättet att ange värden för JSON-egenskapen **TableName** .

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumner med standardvärden**

För närvarande accepterar PolyBase-funktionen i Data Factory bara samma antal kolumner som i mål tabellen. Ett exempel är en tabell med fyra kolumner där en av dem definieras med ett standardvärde. Indata behöver fortfarande ha fyra kolumner. En data uppsättning med tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en särskild form av standardvärdet. Om kolumnen kan vara null kan indata i bloben för den kolumnen vara tomma. Men det kan inte saknas i indata-datauppsättningen. PolyBase infogar NULL för saknade värden i Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Använda COPY-instruktionen för att läsa in data i Azure SQL Data Warehouse (för hands version)

SQL Data Warehouse [copy-instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (för hands version) stöder direkt inläsning av data från **Azure blob och Azure Data Lake Storage Gen2**. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet, kan du välja att använda COPY-instruktionen i ADF för att läsa in data i Azure SQL Data Warehouse. Azure Data Factory kontrollerar inställningarna och det går inte att köra kopierings aktiviteten om villkoren inte är uppfyllda.

>[!NOTE]
>För närvarande finns Data Factory bara stöd för kopiering från COPY Statement-kompatibla källor som anges nedan.

Att använda COPY-instruktionen har stöd för följande konfiguration:

1. Den **länkade käll tjänsten och formatet** har följande typer och autentiseringsmetoder:

    | Typ av käll data lager som stöds                             | Format som stöds           | Typ av källtyp som stöds                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure-blobb](connector-azure-blob-storage.md)                | [Avgränsad text](format-delimited-text.md)             | Autentisering av konto nyckel, autentisering av signatur för delad åtkomst, autentisering av tjänstens huvud konto, hanterad identitetsautentisering |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autentisering av konto nyckel, autentisering av delad åtkomst för signaturer |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autentisering av konto nyckel, autentisering av delad åtkomst för signaturer |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Avgränsad text](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autentisering av konto nyckel, autentisering av tjänstens huvud konto, hanterad identitets autentisering |

    >[!IMPORTANT]
    >Om din Azure Storage har kon figurer ATS med VNet-tjänstens slut punkt måste du använda hanterad identitets autentisering – Se [effekten av att använda VNet-tjänstens slut punkter med Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Lär dig mer om de konfigurationer som krävs i Data Factory från [Azure Blob-hanterad identitets verifiering](connector-azure-blob-storage.md#managed-identity) och [Azure Data Lake Storage Gen2-hanterad identitets verifiering](connector-azure-data-lake-storage.md#managed-identity) .

2. Format inställningarna är med följande:

   1. För **Parquet**: `compression` kan vara **Ingen komprimering**, **fästfunktionen**eller **gzip**.
   2. För **Orc**: `compression` kan vara **Ingen komprimering**, **```zlib```** eller **fästning**.
   3. För **avgränsad text**:
      1. `rowDelimiter`anges explicit som **enstaka** eller "**\r\n**", vilket är standardvärdet stöds inte.
      2. `nullValue`är kvar som standard eller inställd på **tom sträng** ("").
      3. `encodingName`är kvar som standard eller anges till **UTF-8 eller UTF-16**.
      4. `escapeChar`måste vara samma som `quoteChar` och är inte tomt.
      5. `skipLineCount`är kvar som standard eller anges till 0.
      6. `compression`kan vara **Ingen komprimering** eller **gzip**.

3. Om din källa är en mapp `recursive` måste i kopierings aktiviteten vara inställt på sant och `wildcardFilename` måste vara `*` . COPY-instruktionen hämtar alla filer från mappen och alla dess undermappar, och ignorerar dolda mappar och de filer som börjar med en understrykning (_) eller en punkt (.) som inte uttryckligen anges i sökvägen. 

4. `wildcardFolderPath`, `wildcardFilename` (förutom `*` ), `modifiedDateTimeStart` `modifiedDateTimeEnd` och `additionalColumns` har inte angetts.

Följande KOPIERINGs instruktions inställningar stöds under `allowCopyCommand` kopierings aktivitet:

| Egenskap          | Beskrivning                                                  | Obligatorisk                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Anger standardvärden för varje mål kolumn i SQL DW.  Standardvärdena i egenskapen skriver över standard begränsnings uppsättningen i data lagret och identitets kolumnen kan inte ha ett standardvärde. | Nej |
| additionalOptions | Ytterligare alternativ som skickas till SQL DW COPY-instruktionen direkt i With-satsen i [copy-instruktionen](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Citera värdet efter behov för att passa med KOPIERINGs instruktionens krav. | Nej |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Egenskaper för GetMetadata-aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [getMetaData-aktivitet](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Data typs mappning för Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mappa data flödes egenskaper

När du transformerar data i mappnings data flödet kan du läsa och skriva till tabeller från Azure Synapse Analytics. Mer information finns i omvandling av [käll omvandling](data-flow-source.md) och [mottagare](data-flow-sink.md) i mappnings data flöden.

### <a name="source-transformation"></a>Käll omvandling

Inställningar som är särskilt tillgängliga för Azure Synapse Analytics finns på fliken **käll alternativ** i käll omvandlingen.

**Inmatade:** Välj om du vill peka källan på en tabell (motsvarande ```Select * from <table-name>``` ) eller ange en anpassad SQL-fråga.

**Fråga**: om du väljer fråga i fältet inmatat anger du en SQL-fråga för källan. Den här inställningen åsidosätter alla tabeller som du har valt i data uppsättningen. **Order by** -satser stöds inte här, men du kan ange en fullständig Select from-instruktion. Du kan också använda användardefinierade tabell funktioner. **Select * from udfGetData ()** är en UDF i SQL som returnerar en tabell. Med den här frågan skapas en käll tabell som du kan använda i ditt data flöde. Att använda frågor är också ett bra sätt att minska antalet rader för testning eller sökning.

SQL-exempel:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Batchstorlek**: Ange en batchstorlek för att segmentera stora data till läsningar. I data flöden använder ADF den här inställningen för att ange Spark kolumns-cachelagring. Detta är ett alternativ fält som ska använda Spark-standardvärden om det lämnas tomt.

**Isolerings nivå**: standardvärdet för SQL-källor i mappnings data flödet är Read uncommitted. Du kan ändra isolerings nivån här till något av följande värden:

- Läs bekräftad
- Läs ej allokerad
- Upprepnings bar läsning
- Serializable *-None (ignorera isolerings nivå)

![Isoleringsnivå](media/data-flow/isolationlevel.png "Isoleringsnivå")

### <a name="sink-transformation"></a>Omvandling av mottagare

Inställningar som är tillgängliga för Azure Synapse Analytics finns på fliken **Inställningar** i omvandlingen för mottagare.

**Uppdaterings metod:** Avgör vilka åtgärder som tillåts på databas målet. Standardvärdet är att endast tillåta infogningar. Om du vill uppdatera, upsert eller ta bort rader krävs en Alter-Row-omvandling för att tagga rader för dessa åtgärder. För uppdateringar, upsertar och borttagningar måste en nyckel kolumn eller kolumner anges för att avgöra vilken rad som ska ändras.

**Tabell åtgärd:** Bestämmer om du vill återskapa eller ta bort alla rader från mål tabellen innan du skriver.

- Ingen: ingen åtgärd utförs i tabellen.
- Återskapa: tabellen tas bort och återskapas. Krävs om du skapar en ny tabell dynamiskt.
- Trunkera: alla rader från mål tabellen tas bort.

**Aktivera mellanlagring:** Bestämmer om [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) ska användas vid skrivning till Azure Synapse Analytics

**Batchstorlek**: styr hur många rader som skrivs i varje Bucket. Större batch-storlekar förbättrar komprimeringen och minnes optimeringen, men riskerar att ta bort minnes undantag när data cachelagras.

**För-och post-SQL-skript**: Ange SQL-skript med flera rader som ska köras före (för bearbetning) och efter (efter bearbetning)-data skrivs till din mottagar databas

![skript för SQL-bearbetning före och efter bearbetning](media/data-flow/prepost1.png "Skript för SQL-bearbetning")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Data typs mappning för Azure Synapse Analytics

När du kopierar data från eller till Azure Synapse Analytics används följande mappningar från Azure Synapse Analytics-datatyper för att Azure Data Factory interimistiska data typer. Se [mappningar av schema och data typer](copy-activity-schema-and-type-mapping.md) för att lära dig hur kopierings aktivitet mappar käll schema och datatyp till mottagaren.

>[!TIP]
>Se [tabell data typer i Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) -artikeln om data typer som stöds av SQL DW och lösningar för de som inte stöds.

| Data typen Azure Synapse Analytics    | Data Factory data typen Interim |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte []                         |
| bit                                   | Boolesk                        |
| char                                  | Sträng, char []                 |
| date                                  | DateTime                       |
| Datumtid                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM-attribut (varbinary (max)) | Byte []                         |
| Float                                 | Double                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | Sträng, char []                 |
| numeric                               | Decimal                        |
| nvarchar                              | Sträng, char []                 |
| real                                  | Enkel                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte []                         |
| varchar                               | Sträng, char []                 |

## <a name="next-steps"></a>Nästa steg

En lista över data lager som stöds som källor och mottagare efter kopierings aktivitet i Azure Data Factory finns i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
