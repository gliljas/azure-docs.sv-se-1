---
title: Anslutningstyper
titleSuffix: Azure SQL Managed Instance
description: Läs om anslutnings typer för Azure SQL Managed instance
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: cee913e846ebfef174a3cd6383401eace89187f0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044341"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Anslutnings typer för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln förklarar hur klienter ansluter till en hanterad Azure SQL-instans beroende på anslutnings typen. Skript exempel för att ändra anslutnings typer anges nedan, tillsammans med överväganden om hur du ändrar standard anslutnings inställningarna.

## <a name="connection-types"></a>Anslutningstyper

Azure SQL Managed instance stöder följande två anslutnings typer:

- **Omdirigera (rekommenderas):** Klienter upprättar anslutningar direkt till noden som är värd för databasen. Om du vill aktivera anslutning med hjälp av omdirigering måste du öppna brand väggar och nätverks säkerhets grupper (NSG) för att tillåta åtkomst på portarna 1433 och 11000-11999. Paket går direkt till databasen och därför finns det fördröjningar och data flödes prestanda förbättringar som använder omdirigering via proxy.
- **Proxy (standard):** I det här läget använder alla anslutningar en proxy-Gateway-komponent. Om du vill aktivera anslutning måste endast port 1433 för privata nätverk och port 3342 för offentlig anslutning öppnas. Att välja det här läget kan resultera i högre latens och lägre data flöde, beroende på arbets Belastningens natur. Vi rekommenderar starkt att du omdirigerar anslutnings principen via proxy-anslutningen för lägsta latens och högsta data flöde.

## <a name="redirect-connection-type"></a>Anslutnings typ för omdirigering

Anslutnings typ för omdirigering innebär att när TCP-sessionen har upprättats till SQL-motorn hämtar klientsessionen den virtuella mål-IP-adressen för den virtuella klusternoden från belastningsutjämnaren. Efterföljande paket flödar direkt till den virtuella klusternoden och kringgår gatewayen. Följande diagram illustrerar det här trafikflödet.

![omdirigera. png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> Anslutnings typen för omdirigering fungerar för närvarande endast för privat slut punkt. Oavsett inställningen för anslutnings typen, kommer anslutningar via den offentliga slut punkten att ske via en proxy.

## <a name="proxy-connection-type"></a>Typ av anslutnings proxy

Anslutnings typ för proxy innebär att TCP-sessionen upprättas med hjälp av gatewayen och alla efterföljande paket flödar genom den. Följande diagram illustrerar det här trafikflödet.

![proxy. png](./media/connection-types-overview/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Skript för att ändra inställningar för Anslutnings typ med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följande PowerShell-skript visar hur du ändrar anslutnings typen för en SQL-hanterad instans till `Redirect` .

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your SQL Managed Instance, and replace {mi-name} with the name of your SQL Managed Instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Nästa steg

- [Återställa en databas till en SQL-hanterad instans](restore-sample-database-quickstart.md)
- Lär dig hur du [konfigurerar en offentlig slut punkt på SQL-hanterad instans](public-endpoint-configure.md)
- Läs mer om [anslutnings arkitekturen för SQL-hanterade instanser](connectivity-architecture-overview.md)