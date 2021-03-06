---
title: Vad är Delta Lake
description: Översikt över delta Lake och hur det fungerar som en del av Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429205"
---
# <a name="what-is-delta-lake"></a>Vad är delta i sjö?

Azure Synapse Analytics är kompatibelt med Linux Foundation delta Lake. Delta Lake är ett lagrings lager med öppen källkod som ger syre (atomiska, konsekvens, isolering och tålighet) transaktioner för att Apache Spark och Big data arbets belastningar.

## <a name="key-features"></a>Huvudfunktioner

| Funktion | Beskrivning |
| --- | --- |
| **SYRA transaktioner** | Data sjöar fylls vanligt vis via flera processer och pipeliner, varav vissa skriver data samtidigt med läsningar. Före delta Lake och tillägg av transaktioner var data teknikerna tvungen att gå igenom en manuell fel känslig process för att säkerställa data integriteten. Delta Lake hämtar välkända syror-transaktioner till data sjöar. Den ger möjlighet till serialisering, den starkaste nivån av isolerings nivå. Läs mer på [simhopp i delta Lake: packa upp transaktions loggen](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Skalbar hantering av metadata** | I Big data kan även själva metadata vara "Big data". Delta Lake behandlar metadata precis som data, och använder Spark: s distribuerade bearbetnings kraft för att hantera alla metadata. Det innebär att delta sjö kan hantera petabyte-tabeller med miljard tals partitioner och filer på ett enkelt sätt. |
| **Res tid (data versions hantering)** | Möjligheten att "ångra" en ändring eller gå tillbaka till en tidigare version är en av de viktigaste funktionerna i transaktioner. Delta Lake tillhandahåller ögonblicks bilder av data som gör att du kan återgå till tidigare versioner av data för granskningar, återställningar eller återskapa experiment. Läs mer i [Introduktion till delta Lake-tid för stora skalnings data sjöar](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Öppna format** | Apache Parquet är bas linje formatet för delta Lake, så att du kan utnyttja de effektiva komprimerings-och kodnings scheman som är inbyggda i formatet. |
| **Enhetlig batch och strömmande källa och mottagare** | En tabell i delta Lake är både en batch-tabell och en strömmande källa och mottagare. Strömning av data inmatning, batch historisk bakfyllning och interaktiva frågor allt fungerar bara direkt från lådan. |
| **Schema tillämpning** | Schema tvång hjälper till att säkerställa att data typerna är korrekta och att nödvändiga kolumner är tillgängliga, vilket förhindrar att felaktiga data orsakar inkonsekventa data. Mer information finns i [simhopp i delta Lake: schema tillämpning & utveckling](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Schema utveckling** | Med delta Lake kan du göra ändringar i ett tabell schema som kan tillämpas automatiskt, utan att behöva skriva till DDL för migrering. Mer information finns i [simhopp i delta Lake: schema tillämpning & utveckling](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Gransknings historik** | I delta Lake Transaction-loggen registreras information om varje ändring av data som ger en fullständig granskning av ändringarna. |
| **Uppdateringar och borttagningar** | Delta Lake stöder Scala/Java/python och SQL API: er för en rad olika funktioner. Stöd för sammanfognings-, uppdaterings-och borttagnings åtgärder hjälper dig att uppfylla kraven på efterlevnad. Mer information finns i avsnittet om [att delta i delta Lake 0.4.0-versionen](https://delta.io/news/delta-lake-0-4-0-released/) och de [enkla, pålitliga upsertar och Deletes i delta Lake-tabeller med python API: er](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), inklusive kodfragment för kommandona Merge, Update och Delete DML. |
| **100% kompatibel med Apache Spark-API** | Utvecklare kan använda delta Lake med befintliga datapipeliner med minimala ändringar eftersom de är helt kompatibla med befintliga Spark-implementeringar. |

Fullständig dokumentation finns på sidan om [delta Lake-dokumentation](https://docs.delta.io/latest/delta-intro.html)

Mer information finns i [delta Lake Project](https://lfprojects.org).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om .NET för Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
