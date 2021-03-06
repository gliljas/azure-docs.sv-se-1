---
title: Kopiera data i Dynamics (Common Data Service)
description: Lär dig hur du kopierar data från Microsoft Dynamics CRM eller Microsoft Dynamics 365 (Common Data Service) till mottagar data lager som stöds eller från käll data lager som stöds till Dynamics CRM eller Dynamics 365 genom att använda en kopierings aktivitet i en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: 255c39eac2285a23403da2db893d9de8835f7d2c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891535"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiera data från och till Dynamics 365 (Common Data Service) eller Dynamics CRM genom att använda Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Microsoft Dynamics 365 eller Microsoft Dynamics CRM. Den bygger på [översikts artikeln om kopierings aktiviteten](copy-activity-overview.md) som visar en översikt över kopierings aktiviteten.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Söknings aktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Dynamics 365 (Common Data Service) eller Dynamics CRM till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till Dynamics 365 (Common Data Service) eller Dynamics CRM. En lista över data lager som stöds som källor eller handfat av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Den här Dynamics Connector stöder Dynamics version 7. x till 9. x för både online eller lokalt. Mer specifikt,

- Version 7. x mappar till Dynamics CRM 2015
- Version 8. x mappar till Dynamics CRM 2016 och den tidiga versionen av Dynamics 365
- Version 9. x mappar till den senare versionen av Dynamics 365

Se följande tabell på de autentiseringstyper och konfigurationer som stöds för respektive Dynamics-version/-produkter. (IFD är kort för distribution på Internet.)

| Dynamics-versioner | Autentiseringstyper | Länkade tjänst exempel |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | AAD-tjänstens huvud namn <br> Office365 | [Dynamics Online + AAD-tjänstens huvud namn eller Office365-autentisering](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD + IFD-autentisering](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 är det specifikt att följande program typer stöds:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Andra program typer, t. ex. ekonomi och drift, personal osv. stöds inte av den här anslutningen.

Den här Dynamics Connector bygger på [Dynamics XRM-verktyg](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Om du vill kopiera data från **ekonomi och åtgärder i dynamics 365**kan du använda [Dynamics AX-anslutningen](connector-dynamics-ax.md).

## <a name="prerequisites"></a>Krav

Om du vill använda den här anslutningen med AAD-tjänstens huvud namns autentisering måste du ställa in server-till-Server-autentisering (S2S) i Common Data Service eller Dynamics. Läs [den här artikeln](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) om detaljerade anvisningar.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Dynamics.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade Dynamics-tjänsten.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Dynamics**, **DynamicsCrm**eller **CommonDataServiceForApps**. | Ja |
| deploymentType | Dynamics-instansens distributions typ. Det måste vara **"online"** för Dynamics Online. | Ja |
| serviceUri | Tjänst-URL för din Dynamics-instans, t `https://adfdynamics.crm.dynamics.com`. ex.. | Ja |
| authenticationType | Autentiseringstypen för att ansluta till en Dynamics-Server. Tillåtna värden är: **AADServicePrincipal** eller **"Office365"**. | Ja |
| servicePrincipalId | Ange det Azure Active Directory programmets klient-ID. | Ja när du `AADServicePrincipal` använder autentisering |
| servicePrincipalCredentialType | Ange vilken typ av autentiseringsuppgift som ska användas för autentisering av tjänstens huvud namn. Tillåtna värden är: **ServicePrincipalKey** eller **ServicePrincipalCert**. | Ja när du `AADServicePrincipal` använder autentisering |
| servicePrincipalCredential | Ange autentiseringsuppgifterna för tjänstens huvud namn. <br>När du `ServicePrincipalKey` använder som autentiseringstyp `servicePrincipalCredential` kan vara en sträng (ADF krypterar den vid länkad tjänst distribution) eller en referens till en hemlighet i akv. <br>När du `ServicePrincipalCert` använder som autentiseringsuppgift `servicePrincipalCredential` bör vara en referens till ett certifikat i akv. | Ja när du `AADServicePrincipal` använder autentisering | 
| användarnamn | Ange användar namnet för att ansluta till Dynamics. | Ja när du `Office365` använder autentisering |
| password | Ange lösen ordet för det användar konto som du har angett för användar namn. Markera det här fältet som SecureString för att lagra det på ett säkert sätt i Data Factory eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja när du `Office365` använder autentisering |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Om inget värde anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare om den länkade käll tjänsten inte har en integrerings körning |

>[!NOTE]
>Dynamics-anslutningen som används för att använda valfri "företags namn"-egenskap för att identifiera din Dynamics CRM/365 Online-instans. Medan det fungerar, rekommenderar vi att du anger den nya egenskapen "serviceUri" i stället för att få bättre prestanda för instans identifiering.

**Exempel: Dynamics Online med AAD-tjänstens huvud namn + nyckel-autentisering**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Exempel: Dynamics Online med AAD-tjänstens huvud namn och certifikatautentisering**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Exempel: Dynamics Online med Office365-autentisering**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 och Dynamics CRM lokalt med IFD

*Ytterligare egenskaper som jämförs med Dynamics Online är "värdnamn" och "Port".*

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **Dynamics**, **DynamicsCrm**eller **CommonDataServiceForApps**. | Ja |
| deploymentType | Dynamics-instansens distributions typ. Det måste vara **"OnPremisesWithIfd"** för Dynamics-lokal med IFD.| Ja |
| Värdnamn | Värd namnet för den lokala Dynamics-servern. | Ja |
| port | Porten för den lokala Dynamics-servern. | Nej, standard är 443 |
| Databasen | Organisations namnet för Dynamics-instansen. | Ja |
| authenticationType | Autentiseringstypen för att ansluta till Dynamics-servern. Ange **"IFD"** för Dynamics lokalt med IFD. | Ja |
| användarnamn | Ange användar namnet för att ansluta till Dynamics. | Ja |
| password | Ange lösen ordet för det användar konto som du har angett för användar namn. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta kopierings aktiviteten hämta från där när data kopieringen ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | [Integrerings körningen](concepts-integration-runtime.md) som ska användas för att ansluta till data lagret. Om inget värde anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare |

**Exempel: Dynamics lokalt med IFD med IFD-autentisering**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera data uppsättningar finns i artikeln [data uppsättningar](concepts-datasets-linked-services.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics DataSet.

Följande egenskaper stöds för att kopiera data från och till Dynamics.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Data uppsättningens typ-egenskap måste anges till **DynamicsEntity**, **DynamicsCrmEntity**eller **CommonDataServiceForAppsEntity**. |Ja |
| Entitetsnamnet | Det logiska namnet på den entitet som ska hämtas. | Nej för källa (om "fråga" i aktivitets källan har angetts), Ja för mottagare |

**Exempel:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln om [pipeliner](concepts-pipelines-activities.md) . Det här avsnittet innehåller en lista över egenskaper som stöds av typerna Dynamics-källa och mottagare.

### <a name="dynamics-as-a-source-type"></a>Dynamics som typ av källa

Om du vill kopiera data från Dynamics stöds följande egenskaper i avsnittet Kopiera aktivitets **källa** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typ egenskapen för kopierings aktivitets källan måste anges till **DynamicsSource**, **DynamicsCrmSource**eller **CommonDataServiceForAppsSource**. | Ja |
| DocumentDB | FetchXML är ett patentskyddat frågespråk som används i Dynamics (online och lokalt). Se följande exempel. Mer information finns i [Bygg frågor med FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nej (om "entityName" i data uppsättningen har angetts) |

>[!NOTE]
>Kolumnen PK kommer alltid att kopieras, även om den kolumn projektion som du konfigurerar i FetchXML-frågan inte innehåller den.

> [!IMPORTANT]
>- När du kopierar data från Dynamics, är explicit kolumn mappning från Dynamics till Sink valfri, men hög omkommandoering för att säkerställa ett deterministiskt kopierings resultat.
>- När du importerar schemat i redigerings gränssnittet, härleder ADF schemat genom att skriva över de översta raderna från exemplet på Dynamics-frågeresultatet för att initiera listan med käll kolumner, i vilka kolumner utan värden i de översta raderna kommer att utelämnas. Samma sak gäller för kopierings körningar om det inte finns någon explicit mappning. Du kan granska och lägga till fler kolumner i mappningen, som kommer att användas under kopierings körningen.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Exempel på FetchXML-fråga

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics som mottagar typ

För att kunna kopiera data till Dynamics stöds följande egenskaper i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type för kopierings aktivitetens Sink måste anges till **DynamicsSink**, **DynamicsCrmSink**eller **CommonDataServiceForAppsSink**. | Ja |
| writeBehavior | Åtgärdens Skriv funktion.<br/>Tillåtet värde är **"upsert"**. | Ja |
| alternateKeyName | Ange det alternativa nyckel namnet som definierats i entiteten för att utföra "upsert". | Inga |
| writeBatchSize | Rad antalet data som skrivs till Dynamics i varje batch. | Nej (standard är 10) |
| ignoreNullValues | Anger om null-värden ska ignoreras från indata (förutom nyckel fält) under en Skriv åtgärd.<br/>Tillåtna värden är **True** och **false**.<br>- **True**: lämna kvar data i målobjektet oförändrade när du gör en upsert/uppdatering-åtgärd. Infoga ett definierat standardvärde när du infogar en åtgärd.<br/>- **False**: uppdatera data i MÅLOBJEKTET till null när du gör en upsert/uppdatering-åtgärd. Infoga ett NULL-värde när du gör en infognings åtgärd. | Nej (standard är falskt) |

>[!NOTE]
>Standardvärdet för sinken "**writeBatchSize**" och kopierings aktiviteten "**[ParallelCopies](copy-activity-performance-features.md#parallel-copy)**" för Dynamics-Sink är båda 10. Därför skickas 100 poster till Dynamics samtidigt.

För Dynamics 365 online finns det en gräns på [2 samtidiga batch-anrop per organisation](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Om den här gränsen överskrids genereras ett "Server upptagen"-fel innan den första begäran någonsin körs. Att hålla "writeBatchSize" mindre eller lika med 10 skulle förhindra att samtidiga samtal begränsas.

Den optimala kombinationen av "**writeBatchSize**" och "**parallelCopies**" är beroende av schemat för entiteten, t. ex. antal kolumner, rad storlek, antal plugin-program/arbets flöden/arbets flödes aktiviteter som kopplats samman till dessa anrop osv. Standardinställningen 10 writeBatchSize * 10 parallelCopies är rekommendationen enligt Dynamics-tjänsten, som skulle fungera för de flesta Dynamics-entiteter, men det kanske inte är bästa prestanda. Du kan justera prestanda genom att justera kombinationen i inställningarna för kopierings aktiviteten.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Data typs mappning för Dynamics

När du kopierar data från Dynamics används följande mappningar från data typer i Dynamics för att Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

Konfigurera motsvarande Data Factory data typ i en data uppsättnings struktur baserat på data typen för din käll-Dynamics med hjälp av följande mappnings tabell.

| Data typen Dynamics | Data Factory data typen Interim | Stöds som källa | Stöds som mottagare |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode. Boolean | Boolesk | ✓ | ✓ |
| AttributeType. kund | GUID | ✓ | |
| AttributeType. DateTime | Datumtid | ✓ | ✓ |
| AttributeType. decimal | Decimal | ✓ | ✓ |
| AttributeType. Double | Double | ✓ | ✓ |
| AttributeType. EntityName | Sträng | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. lookup | GUID | ✓ | ✓ (med associerat enskilt mål) |
| AttributeType.ManagedProperty | Boolesk | ✓ | |
| AttributeType. PM | Sträng | ✓ | ✓ |
| AttributeType. Money | Decimal | ✓ | ✓ |
| AttributeType. owner | GUID | ✓ | |
| AttributeType. listruta | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Sträng | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> Det finns inte stöd för Dynamics data typerna AttributeType. CalendarRules, AttributeType. MultiSelectPicklist och AttributeType. PartyList.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över data lager som stöds som källor och mottagare av kopierings aktiviteten i Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
