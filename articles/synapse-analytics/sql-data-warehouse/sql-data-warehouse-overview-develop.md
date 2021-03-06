---
title: Resurser för att utveckla en Synapse SQL-pool i Azure Synapse Analytics
description: Utvecklings begrepp, design beslut, rekommendationer och kodnings metoder för SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411649"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Design beslut och kod teknik för en Synapse SQL-pool i Azure Synapse Analytics 
 I den här artikeln hittar du ytterligare resurser som hjälper dig att få bättre förståelse för viktiga design beslut, rekommendationer och kodnings metoder för en SQL-pool i Azure Synapse.

## <a name="key-design-decisions"></a>Viktiga design beslut
Följande artiklar fokuserar på begrepp och design beslut för att utveckla ett distribuerat informations lager med SQL-poolens funktion i Azure Synapse:

* [anslutning](../sql/connect-overview.md)
* [samtidighet](resource-classes-for-workload-management.md)
* [transaktioner](sql-data-warehouse-develop-transactions.md)
* [användardefinierade scheman](sql-data-warehouse-develop-user-defined-schemas.md)
* [tabell distribution](sql-data-warehouse-tables-distribute.md)
* [tabell index](sql-data-warehouse-tables-index.md)
* [Table-partitioner](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [uppgifterna](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Utvecklings rekommendationer och kodnings metoder
I följande artiklar används olika kodnings tekniker, tips och rekommendationer för att utveckla en SQL-pool:

* [lagrade procedurer](sql-data-warehouse-develop-stored-procedures.md)
* [Etiketter](sql-data-warehouse-develop-label.md)
* [vyer](sql-data-warehouse-develop-views.md)
* [temporära tabeller](sql-data-warehouse-tables-temporary.md)
* [dynamisk SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [loopning](sql-data-warehouse-develop-loops.md)
* [Gruppera efter alternativ](sql-data-warehouse-develop-group-by-options.md)
* [variabel tilldelning](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Nästa steg
Mer referensinformation finns i [T-SQL-uttryck](sql-data-warehouse-reference-tsql-statements.md).
