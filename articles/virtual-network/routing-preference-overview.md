---
title: Flödes inställningar i Azure
description: Läs om hur du kan välja hur trafiken dirigeras mellan Azure och Internet med cirkulations inställningar.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 0ae06a1c3d486b5d5998b4c6d050d86f50910a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598040"
---
# <a name="what-is-routing-preference-preview"></a>Vad är cirkulations inställningar (för hands version)?

Med Azure routing-inställningen kan du välja hur trafiken ska dirigeras mellan Azure och Internet. Du kan välja att dirigera trafik antingen via Microsoft-nätverket eller via Internet leverantörs nätverket (offentligt Internet). De här alternativen kallas även för *kall källroutning* och routning av *aktiva potatis* . Priset för utgående data överföring varierar beroende på valet av routning. Du kan välja alternativet routning när du skapar en offentlig IP-adress. Den offentliga IP-adressen kan associeras med resurser, till exempel virtuell dator, skalnings uppsättningar för virtuella datorer, belastnings utjämning på Internet, osv. Du kan också ange cirkulations inställningar för Azure Storage-resurser som blobbar, filer, webb och Azure-DataLake. Trafiken dirigeras som standard via Microsofts globala nätverk för alla Azure-tjänster.

> [!IMPORTANT]
> Dirigerings inställningen är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="routing-via-microsoft-global-network"></a>Routning via Microsoft globalt nätverk

När du dirigerar din trafik via *Microsofts globala nätverk*, levereras trafiken över ett av de största nätverken i världen som sträcker sig över 160 000 mil fibrer med över 165 Edge Point of Presence (pop). Nätverket är väl etablerad med flera redundanta fiber vägar för att säkerställa hög tillförlitlighet och tillgänglighet. Trafik teknikerna hanteras av en programdefinierad WAN-styrenhet som garanterar en låg latens Sök vägs val för din trafik och ger förstklassig nätverks prestanda.

![Routning via Microsoft globalt nätverk](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Ingress trafik:** Det globala BGP anycast-meddelandet säkerställer att inkommande trafik går in i det Microsoft-nätverk som är närmast användaren. Till exempel, om en användare från Singapore får åtkomst till Azure-resurser som finns i Chicago, kommer USA att ingå i Microsoft globalt nätverk i Singapore Edge POP och reser i Microsoft Network till den tjänst som finns i Chicago.

**Utgående trafik:** Den utgående trafiken följer samma princip. Trafiken reser merparten av sin resa på Microsofts globala nätverk och avslutas närmast användaren. Om till exempel trafik från Azure Chicago är avsedd för en användare från Singapore, överförs trafiken i Microsoft Network från Chicago till Singapore, och Microsoft-nätverket i Singapore Edge-POP avslutas.

Både ingångs-och utgångs trafik förblir Mass av resan i Microsofts globala nätverk. Detta kallas även för *kall potatis-routning*.


## <a name="routing-over-public-internet-isp-network"></a>Routning över offentligt Internet (Internet leverantörs nätverk)

Det nya Dirigerings alternativet *Internet routning* minimerar resor i Microsofts globala nätverk, och använder ett ÖVERFÖRINGs leverantörs nätverk för att dirigera trafiken. Det här alternativet för kostnads optimerad routning ger nätverks prestanda som är jämförbar med andra moln leverantörer.

![Routning över offentligt Internet](media/routing-preference-overview/route-via-isp-network.png)

**Ingress trafik:** Sökvägen för ingångs punkt använder frekvent *källroutning* , vilket innebär att trafiken går in i Microsoft-nätverket som är närmast den värdbaserade tjänst regionen. Till exempel, om en användare från Singapore får åtkomst till Azure-resurser som finns i Chicago, överförs trafiken via det offentliga Internet och går in i Microsofts globala nätverk i Chicago.

**Utgående trafik:** Den utgående trafiken följer samma princip. Trafiken avslutar Microsoft Network i samma region som tjänsten finns i. Om till exempel trafik från din tjänst i Azure Chicago är avsedd för en användare från Singapore, avslutar trafiken Microsoft Network i Chicago och reser via det offentliga Internet till användaren i Singapore.

## <a name="supported-services"></a>Tjänster som stöds

Alternativet för offentlig IP-adress med Routning val "Microsoft globalt nätverk" kan associeras med alla Azure-tjänster. Offentliga IP-adresser med inställnings alternativ för **routning kan dock** vara kopplade till följande Azure-resurser:

* Virtuell dator
* Skaluppsättning för virtuella datorer
* Azure Kubernetes Service (AKS)
* Belastningsutjämnare mot Internet
* Application Gateway
* Azure Firewall

För lagring använder primära slut punkter alltid **Microsofts globala nätverk**. Du kan aktivera sekundära slut punkter med **Internet** som du väljer för trafik dirigering. De lagrings tjänster som stöds är:

* Blobar
* Files
* Webb
* Azure DataLake

## <a name="pricing"></a>Priser
Pris skillnaden mellan båda alternativen återspeglas i priset för utgående data överföring i Internet. Routning via **Microsofts globala nätverks** data överförings pris är detsamma som aktuellt pris för utgående Internet. Besök [pris sidan för Azure bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/) för den senaste pris informationen. Routning via **offentlig Internet** priss ätts lägre enligt tabellen nedan:

| Region för utgående källa | 0-5 GB/månad | 5 GB – 10 TB/månad | 10-50 TB/månad | 50-150 TB/månad | 150-500 TB/månad |
| --- | --- | --- | --- | --- | --- |
| Zon 1 | $0/GB | $0.085/GB | $0.065/GB | $0.06/GB | $0.04/GB |
| Zon 2 | $0/GB | $0,11/GB | $0.075/GB | $0,07/GB | $0.06/GB  |

[Kontakta oss](https://azure.microsoft.com/overview/sales-number/) för en månads volym över 500 TB.
* Zon 1 – Australien, centrala Australien, centrala 2, centrala Kanada, Östra Kanada, norra Europa, västra Europa, Frankrike, södra, Tyskland, norra (offentlig), Tyskland, västra centrala (offentlig), Norge, östra, Norge, västra, Schweiz, norra, Schweiz, västra, Storbritannien, södra, Storbritannien, västra, centrala USA, östra USA, östra USA 2, norra centrala USA, södra centrala USA, västra USA, västra USA 2, västra centrala USA.

* Zon 2 – Asien, östra, Sydostasien, östra Australien, sydöstra Australien, centrala Indien, södra Indien, västra Indien, Östra Japan, västra Japan, Korea, centrala och Sydkorea, södra.

* Zon 3 – södra Brasilien, södra Afrika, sydöstra Sydafrika, västra USA, Förenade Arabemiraten Central och Förenade Arabemiraten Nord.

## <a name="availability"></a>Tillgängligt

Stöd för routnings inställningar är tillgängligt i följande regioner för tjänster som virtuell dator och Internetbaserad belastningsutjämnare som använder en offentlig IP-adress för Internet utgående Europa, Västeuropa, Västeuropa, södra, Storbritannien, södra, östra USA, norra centrala USA, södra centrala USA, västra USA, västra centrala USA, Sydostasien, Tyskland, västra centrala, Schweiz, västra, Japan, östra och Japan, västra.

Stöd för routning av inställningar för lagrings konto är tillgängligt i följande Azure-regioner – Frankrike, norra centrala USA och västra centrala USA.
## <a name="limitations"></a>Begränsningar

* Dirigerings inställningen är endast kompatibel med standard-SKU: n för offentlig IP-adress. Grundläggande SKU för offentlig IP-adress stöds inte.
* Inställningen routning stöder för närvarande endast IPv4 offentliga IP-adresser. Offentliga IPv6-IP-adresser stöds inte.
* Virtuella datorer med flera nätverkskort kan bara ha en typ av cirkulations inställningar.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure CLI](configure-routing-preference-virtual-machine-cli.md)
