---
title: Anslutnings arkitektur – Azure Database for MariaDB
description: Beskriver anslutnings arkitekturen för din Azure Database for MariaDB-Server.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 38b6f797541ef07fa3fb5e1dc71029a4cbcf5d22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546293"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Anslutnings arkitektur i Azure Database for MariaDB
Den här artikeln förklarar Azure Database for MariaDB anslutnings arkitektur och hur trafiken dirigeras till din Azure Database for MariaDB-instans från klienter både inom och utanför Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Anslutning till din Azure Database for MariaDB upprättas via en gateway som ansvarar för att dirigera inkommande anslutningar till serverns fysiska plats i våra kluster. Följande diagram illustrerar trafikflödet.

![Översikt över anslutnings arkitekturen](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

När klienten ansluter till databasen får de en anslutnings sträng som ansluter till gatewayen. Den här gatewayen har en offentlig IP-adress som lyssnar på port 3306. I databas klustret vidarebefordras trafiken till lämpliga Azure Database for MariaDB. För att kunna ansluta till servern, t. ex. från företags nätverk, är det därför nödvändigt att öppna brand väggen på klient sidan så att utgående trafik kan komma åt våra gateways. Nedan hittar du en fullständig lista över de IP-adresser som används av våra gatewayer per region.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Azure Database for MariaDB Gateway-IP-adresser

I följande tabell visas de primära och sekundära IP-adresserna för Azure Database for MariaDB Gateway för alla data områden. Den primära IP-adressen är den aktuella IP-adressen för gatewayen och den andra IP-adressen är en IP-adress för redundans vid fel i den primära. Som nämnts bör kunderna tillåta utgående till båda IP-adresserna. Den andra IP-adressen lyssnar inte på några tjänster förrän den har Aktiver ATS av Azure Database for MariaDB att acceptera anslutningar.

| **Regionsnamn** | **IP-adresser för gateway** |
|:----------------|:-------------|
| Australien, centrala| 20.36.105.0     |
| Australien, Central2     | 20.36.113.0   |
| Australien, östra | 13.75.149.87, 40.79.161.1     |
| Sydöstra Australien |191.239.192.109, 13.73.109.251   |
| Brasilien, södra | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Kanada, centrala |40.85.224.249  |
| Kanada, östra | 40.86.226.166    |
| USA, centrala | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| Kina, östra | 139.219.130.35    |
| Kina, östra 2 | 40.73.82.1  |
| Kina, norra | 139.219.15.17    |
| Kina, norra 2 | 40.73.50.0     |
| Asien, östra | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA, östra | 40.121.158.30, 191.238.6.43  |
| USA, östra 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Frankrike, centrala | 40.79.137.0, 40.79.129.1  |
| Tyskland, centrala | 51.4.144.100     |
| Tyskland, norra öst | 51.5.144.179  |
| Indien, centrala | 104.211.96.159     |
| Södra Indien | 104.211.224.146  |
| Indien, västra | 104.211.160.80    |
| Japan, östra | 13.78.61.196, 191.237.240.43  |
| Japan, västra | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Sydkorea, centrala | 52.231.32.42   |
| Sydkorea, södra | 52.231.200.86    |
| USA, norra centrala | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa, norra | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Sydafrika, norra  | 102.133.152.0    |
| Sydafrika, västra | 102.133.24.0   |
| USA, södra centrala |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Sydostasien | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Förenade Arabemiraten Central | 20.37.72.64  |
| Förenade Arabemiraten, norra | 65.52.248.0    |
| Storbritannien, södra | 51.140.184.11   |
| Storbritannien, västra | 51.141.8.11  |
| USA, västra centrala | 13.78.145.25     |
| Europa, västra | 40.68.37.158, 191.237.232.75, 13.69.105.208  |
| USA, västra | 104.42.238.205, 23.99.34.75  |
| USA, västra 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-portal.md)
* [Skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-cli.md)
