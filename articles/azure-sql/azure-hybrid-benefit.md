---
title: Azure Hybrid-förmån
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Använd befintliga SQL Server licenser för Azure SQL Database och SQL-hanterade instans rabatter.
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=4
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d57eb2718f79e9243426a59245abc44c98dc6249
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118613"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid-förmån-Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

I den etablerade beräknings nivån för den vCore-baserade inköps modellen kan du byta ut dina befintliga licenser för rabatterade priser på Azure SQL Database och Azure SQL-hanterad instans med hjälp av [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). Med den här Azure-förmånen kan du Spara upp till 30 procent eller ännu högre på SQL Database & SQL-hanterad instans med hjälp av dina lokala SQL Server licenser med Software Assurance. Sidan [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) har en kalkylator för att hjälpa till att avgöra besparingar.  Observera att Azure Hybrid-förmån inte gäller för Azure SQL Database utan server.

> [!NOTE]
> Att ändra till Azure Hybrid-förmån kräver ingen stillestånds tid.

![priset](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Välj en licens modell

Med Azure Hybrid-förmån kan du välja att bara betala för den underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för själva SQL Database-motorn (bas beräknings pris) eller så kan du betala för både den underliggande infrastrukturen och SQL Server licens (licens pris).

Du kan välja eller ändra licensierings modellen med hjälp av Azure Portal eller genom att använda någon av följande API: er:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ange eller uppdatera licens typen med hjälp av PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ange eller uppdatera licens typen med hjälp av Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi-uppdatering](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST-API](#tab/rest)

Ange eller uppdatera licens typen genom att använda REST API:

- [Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)
- [Databaser – uppdatera](/rest/api/sql/databases/update)
- [Hanterade instanser – skapa eller uppdatera](/rest/api/sql/managedinstances/createorupdate)
- [Hanterade instanser – uppdatera](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid-förmån frågor

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Finns det dubbla användnings rättigheter med Azure Hybrid-förmån för SQL Server?

Du har 180 dagar med dubbla användnings rättigheter för licensen för att säkerställa att migreringen körs sömlöst. Efter den 180-dagars perioden kan du bara använda SQL Server-licensen i molnet i SQL Database. Du har inte längre dubbla användnings rättigheter lokalt och i molnet.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hur skiljer sig Azure Hybrid-förmån för SQL Server från License Mobility?

Vi erbjuder License Mobility-förmåner för att SQL Server kunder med Software Assurance. Detta gör det möjligt att omtilldela licenser till en partners delade servrar. Du kan använda den här förmånen på Azure IaaS och AWS EC2.

Azure Hybrid-förmån för SQL Server skiljer sig från licens mobilitet i två viktiga områden:

- Det ger ekonomiska fördelar för att flytta hög virtualiserade arbets belastningar till Azure. SQL Server Enterprise Edition-kunder kan få fyra kärnor i Azure i Generell användning SKU för varje kärna som de äger lokalt för mycket virtualiserade program. License Mobility tillåter inte några särskilda kostnads besparingar för att flytta virtualiserade arbets belastningar till molnet.
- Det tillhandahåller ett PaaS-mål på Azure (SQL Database Hanterad instans) som är mycket kompatibel med SQL Server lokalt.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Vilka är de speciella rättigheterna för Azure Hybrid-förmån för SQL Server?

SQL Database kunder har följande rättigheter som är kopplade till Azure Hybrid-förmån för SQL Server:

|Licens utrymme|Vad får Azure Hybrid-förmån för SQL Server dig?|
|---|---|
|SQL Server Enterprise Edition Core-kunder med SA|<li>Kan betala bas priset antingen Generell användning eller Affärskritisk SKU</li><br><li>1 kärna lokalt = 4 kärnor i Generell användning SKU</li><br><li>1 kärna lokalt = 1 kärna i Affärskritisk SKU</li>|
|SQL Server Standard Edition Core-kunder med SA|<li>Kan betala bas taxan endast för Generell användning SKU</li><br><li>1 kärna lokalt = 1 kärna i Generell användning SKU</li>|
|||


## <a name="next-steps"></a>Nästa steg

- Information om hur du väljer ett alternativ för Azure SQL-distribution finns i [välja rätt distributions alternativ i Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- En jämförelse mellan SQL Database-och SQL-hanterade instans funktioner finns i [SQL Database & SQL-hanterade instans funktioner](database/features-comparison.md).
