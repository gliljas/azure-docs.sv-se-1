---
title: Hantera fel rader med data mappnings flöden i Azure Data Factory
description: Lär dig hur du hanterar fel i SQL-trunkering i Azure Data Factory att använda mappnings data flöden.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732694"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Hantera fel rader för SQL-trunkering i Data Factory mappa data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ett vanligt scenario i Data Factory när du använder mappnings data flöden är att skriva transformerade data till en Azure SQL-databas. I det här scenariot, ett vanligt fel tillstånd som du måste förhindra mot är möjlig kolumn trunkering. Följ de här stegen för att tillhandahålla loggning av kolumner som inte ryms i en mål Strängs kolumn, så att ditt data flöde fortsätter i dessa scenarier.

## <a name="scenario"></a>Scenario

1. Vi har en Azure SQL Database-tabell som innehåller en ```nvarchar(5)``` kolumn med namnet "name".

2. I vårt data flöde vill vi mappa film titlar från vår mottagare till den målets "namn"-kolumn.

    ![Film data flöde 1](media/data-flow/error4.png)
    
3. Problemet är att film rubriken inte passar in i en mottagar kolumn som bara får innehålla 5 tecken. När du kör det här data flödet visas ett fel meddelande som liknar detta:```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

Den här videon visar ett exempel på hur du ställer in fel rad hanterings logik i ditt data flöde:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>Så här utformar du det här tillståndet

1. I det här scenariot är den maximala längden för kolumnen "namn" fem tecken. Nu ska vi lägga till en villkorlig delnings omvandling som gör att vi kan logga rader med "titlar" som innehåller fler än fem tecken samtidigt som resten av raderna som kan rymmas i det här utrymmet skrivs till databasen.

    ![villkorlig delning](media/data-flow/error1.png)

2. Den här villkorliga Split-omvandlingen definierar den maximala längden för "title" som fem. Alla rader som är mindre än eller lika med fem hamnar i ```GoodRows``` strömmen. Alla rader som är större än fem hamnar i ```BadRows``` strömmen.

3. Nu måste vi logga raderna som misslyckades. Lägg till en Sink-omvandling ```BadRows``` i strömmen för loggning. Här kommer vi att "automatiskt mappa" alla fält så att vi kan logga in hela transaktions posten. Detta är en text-avgränsad CSV-fil till en enda fil i Blob Storage. Vi anropar logg filen "badrows. csv".

    ![Felaktiga rader](media/data-flow/error3.png)
    
4. Det slutförda data flödet visas nedan. Vi kan nu dela upp fel rader för att undvika fel i SQL-trunkering och föra dessa poster i en loggfil. På varandra kan lyckade rader fortsätta att skriva till vår mål databas.

    ![Slutför data flöde](media/data-flow/error2.png)

## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandlingar](concepts-data-flow-overview.md)av data flöden.
