---
title: Synapse SQL-rekommendationer
description: Lär dig mer om Synapse SQL-rekommendationer och hur de genereras
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17b8ce04cb5029d1bea11344617bf65718ca579c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653026"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL-rekommendationer

I den här artikeln beskrivs de Synapse-SQL-rekommendationer som hanteras via Azure Advisor.  

Synapse SQL ger rekommendationer för att säkerställa att arbets belastningen för data lagret är konsekvent optimerad för prestanda. Rekommendationerna är nära integrerade med [Azure Advisor](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att ge dig bästa praxis direkt i [Azure Portal](https://aka.ms/Azureadvisor). Synapse SQL samlar in telemetri-och underlag rekommendationer för din aktiva arbets belastning på en daglig takt. De rekommendations scenarier som stöds beskrivs nedan tillsammans med hur du använder rekommenderade åtgärder.

Du kan [kontrol lera dina rekommendationer](https://aka.ms/Azureadvisor) idag! 

## <a name="data-skew"></a>Data skevning

Dataskevning kan orsaka ytterligare data flytt eller resurs Flask halsar när du kör arbets belastningen. I följande dokumentation beskrivs hur du identifierar data skevning och förhindrar att den kan utföras genom att välja en optimal distributions nyckel.

- [Identifiera och ta bort skevning](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Ingen eller inaktuell statistik

Om du har en underoptimerad statistik kan du kraftigt påverka frågeresultatet eftersom det kan orsaka att SQL Query Optimering genererar underoptimala fråge planer. I följande dokumentation beskrivs de bästa metoderna för att skapa och uppdatera statistik:

- [Skapa och uppdatera tabell statistik](sql-data-warehouse-tables-statistics.md)

Om du vill se en lista över berörda tabeller med dessa rekommendationer kör du följande [T-SQL-skript](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Advisor kör kontinuerligt samma T-SQL-skript för att skapa rekommendationerna.

## <a name="replicate-tables"></a>Replikera tabeller

För replikerade tabell rekommendationer identifierar Advisor tabell kandidater baserat på följande fysiska egenskaper:

- Replikerad tabell storlek
- Antal kolumner
- Tabell distributions typ
- Antal partitioner

Advisor utnyttjar kontinuerligt arbets belastnings heuristik, till exempel tabell åtkomst frekvens, rader som returneras i genomsnitt och tröskelvärden kring storlek och aktivitet för data lagret för att säkerställa att rekommendationerna skapas med hög kvalitet.

I följande avsnitt beskrivs arbets belastnings heuristik som du kan hitta i Azure Portal för varje replikerad tabell rekommendation:

- I genomsnitt används den genomsnittliga procent andelen av rader som returnerades från tabellen för varje tabell åtkomst under de senaste sju dagarna
- Frekvent läsning, ingen uppdatering – visar att tabellen inte har uppdaterats under de senaste sju dagarna vid visning av åtkomst aktivitet
- Läs-/uppdaterings kvot – förhållandet mellan hur ofta tabellen användes i relation till när den uppdaterades de senaste sju dagarna
- Aktivitet – mäter användningen baserat på åtkomst aktivitet. Den här aktiviteten jämför tabell åtkomst aktiviteten i förhållande till den genomsnittliga tabell åtkomst aktiviteten i data lagret under de senaste sju dagarna.

För närvarande kan Advisor bara visa högst fyra replikerade tabell kandidater samtidigt med grupperade columnstore-index som prioriterar den högsta aktiviteten.

> [!IMPORTANT]
> Den replikerade tabell rekommendationen är inte full och tar inte hänsyn till data flyttnings åtgärder. Vi arbetar med att lägga till detta som en tumregel, men under tiden bör du alltid validera din arbets belastning när du har tillämpat rekommendationen. Mer information om replikerade tabeller finns i följande [dokumentation](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).


## <a name="adaptive-gen2-cache-utilization"></a>Adaptiv (Gen2) cache-användning
När du har en stor arbets uppsättning kan du uppleva en låg cacheträffar och hög användning av cacheminnet. I det här scenariot ska du skala upp för att öka kapaciteten för cacheminnet och köra arbets belastningen igen. Mer information finns i följande [dokumentation](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache). 

## <a name="tempdb-contention"></a>TempDB-konkurrens

Frågans prestanda kan försämras när det finns hög tempdb-konkurrens.  TempDB-konkurrens kan ske via användardefinierade temporära tabeller eller när det finns stora mängder data förflyttningar. I det här scenariot kan du skala för mer tempdb-allokering och [Konfigurera resurs klasser och arbets belastnings hantering](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management) för att ge mer minne till dina frågor. 
