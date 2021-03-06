---
title: VLAN och undernät i Azure VMware-lösning av CloudSimple
description: Läs om VLAN och undernät i CloudSimple privata moln
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024899"
---
# <a name="vlans-and-subnets-overview"></a>Översikt över VLAN och undernät

CloudSimple tillhandahåller ett nätverk per region där din CloudSimple-tjänst har distribuerats.  Nätverket är ett enskilt adress utrymme för TCP-skikt 3 med Routning aktiverat som standard.  Alla privata moln och undernät som skapats i den här regionen kan kommunicera med varandra utan ytterligare konfiguration.  Du kan skapa distribuerade Port grupper på vCenter med hjälp av VLAN.

![CloudSimple nätverk sto pol Ogin](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

Ett VLAN (Layer 2-nätverk) skapas för varje privat moln.  Layer 2-trafiken ligger kvar inom gränserna för ett privat moln, så att du kan isolera den lokala trafiken i det privata molnet.  Ett VLAN som skapats i det privata molnet kan bara användas för att skapa distribuerade Port grupper i det privata molnet.  Ett VLAN som skapats i ett privat moln konfigureras automatiskt på alla växlar som är anslutna till värdarna i ett privat moln.

## <a name="subnets"></a>Undernät

Du kan skapa ett undernät när du skapar ett VLAN genom att definiera adress utrymmet för under nätet. En IP-adress från adress utrymmet tilldelas som en under näts Gateway. Ett enskilt privat lager 3-adressutrymme tilldelas per kund och region. Du kan konfigurera alla RFC 1918 icke-överlappande adress utrymmen, med ditt lokala nätverk eller Azure Virtual Network, i din nätverks region.

Alla undernät kan kommunicera med varandra som standard, vilket minskar konfigurationen för routning mellan privata moln. Öst-väst-data mellan datorer i samma region förblir i samma nivå 3-nätverk och överföringar över den lokala nätverks infrastrukturen i regionen. Ingen utgående krävs för kommunikation mellan privata moln i en region. Den här metoden eliminerar alla WAN/utgångs prestanda straff vid distribution av olika arbets belastningar i olika privata moln.

## <a name="vspherevsan-subnets-cidr-range"></a>CIDR-intervall för vSphere/virtuellt San-undernät

Ett privat moln skapas som en isolerad VMware-stack (ESXi-värdar, vCenter-, virtuellt San-och NSX)-miljö som hanteras av en vCenter-Server.  Hanterings komponenter distribueras i det nätverk som valts för vSphere/virtuellt San-undernät CIDR.  Nätverks-CIDR-intervallet är indelat i olika undernät under distributionen.

* Minsta vSphere/virtuellt San-undernät CIDR-intervall prefix: **/24**
* Maximalt vSphere/virtuellt San-undernät CIDR-intervall prefix: **/21**

> [!CAUTION]
> IP-adresser i CIDR-intervallet vSphere/virtuellt San är reserverade för användning av den privata moln infrastrukturen.  Använd inte IP-adressen i det här intervallet på någon virtuell dator.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/virtuellt San-undernät CIDR-intervall gränser

Valet av vSphere/virtuellt San-undernät CIDR-intervall storleken påverkar storleken på ditt privata moln.  Följande tabell visar det maximala antalet noder som du kan ha baserat på storleken på vSphere/virtuellt San-undernät CIDR.

| Angivna vSphere/virtuellt San-undernät CIDR-prefix längd | Maximalt antal noder |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Hanterings under nät som skapats i ett privat moln

Följande hanterings under nät skapas när du skapar ett privat moln.

* **System hantering**. VLAN och undernät för ESXi värd hanterings nätverk, DNS-server, vCenter-Server.
* **VMotion**. VLAN och undernät för ESXi-värdar vMotion-nätverk.
* **Virtuellt San**. VLAN och undernät för ESXi-värdar virtuellt SAN-nätverk.
* **NsxtEdgeUplink1**. VLAN och undernät för VLAN länkar till ett externt nätverk.
* **NsxtEdgeUplink2**. VLAN och undernät för VLAN länkar till ett externt nätverk.
* **NsxtEdgeTransport**. VLAN och undernät för transport zoner styr räckvidden för Layer 2-nätverk i NSX-T.
* **NsxtHostTransport**. VLAN och undernät för värd transport zon.

### <a name="management-network-cidr-range-breakdown"></a>Hantering av CIDR-intervall i hanterings nätverk

det angivna CIDR-intervallet för vSphere/virtuellt San-undernät delas upp i flera undernät.  I följande tabell visas ett exempel på en analys av tillåtna prefix.  Exemplet använder 192.168.0.0 som CIDR-intervall.

Exempel:

| Angivet vSphere/virtuellt San-undernät CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| System hantering | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| Virtuellt San | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T-värd-transport | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T-Edge-Transport | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX – T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX – T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera virtuella lokala nätverk och undernät](create-vlan-subnet.md)
