---
title: 'Platser och anslutningsleverantörer: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter plats.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: cherylmc
ms.openlocfilehash: bfd5d1bfed89d069cbdb74c4df723409ec1f1e77
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797984"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partners och peeringplatser

> [!div class="op_single_selector"]
> * [Platser efter leverantör](expressroute-locations.md)
> * [Leverantörer efter plats](expressroute-locations-providers.md)


Tabellerna i den här artikeln innehåller information om ExpressRoute geografisk täckning och platser, ExpressRoute Connectivity-providers och ExpressRoute system integrators (SIs).

> [!Note]
> Azure-regioner och ExpressRoute-platser är två distinkta och olika koncept, så att skillnaden mellan de två är avgörande för att utforska Azure Hybrid nätverks anslutning. 
>
>

## <a name="azure-regions"></a>Azure-regioner
Azure-regioner är globala data Center där Azure Compute, nätverk och lagrings resurser finns. När du skapar en Azure-resurs måste kunden välja en resurs plats. Resurs platsen avgör vilken Azure-datacenter (eller tillgänglighets zon) som resursen skapas i.

## <a name="expressroute-locations"></a>ExpressRoute-platser
ExpressRoute-platser (kallas ibland peering-platser eller uppfyller platser) är samplacerings anläggningar där Microsoft Enterprise Edge-enheter (MSEE: N) finns. ExpressRoute platser är start punkten till Microsofts nätverk – och distribueras globalt, vilket ger kunderna möjlighet att ansluta till Microsofts nätverk runtom i världen. Dessa platser är där ExpressRoute partners och ExpressRoute Direct-kunder utfärdar kors anslutningar till Microsofts nätverk. I allmänhet behöver ExpressRoute-platsen inte matcha Azure-regionen. En kund kan till exempel skapa en ExpressRoute-krets med resurs platsen *östra USA*, på peer-platsen i *Seattle* .

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-regioner till ExpressRoute-platser inom en regional politisk region
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Australiensiska myndigheter** | Australien, centrala, Australien, centrala 2 |Canberra, Canberra2 |
| **Europa** | Frankrike, centrala, Frankrike, södra, Tyskland, norra, Tyskland, västra centrala, norra Europa, östra Norge, västra Norge, Schweiz, norra, Schweiz, västra, Storbritannien, västra, Storbritannien, södra, Västeuropa |Amsterdam, Amsterdam2, Berlin, köpen hamn, Dublin, Frankfurt, Genève, London, London2, Marseille, Milano, München, Newport (Wales), Oslo, Paris, Stavanger, Stockholm, Zürich, München |
| **Nordamerika** | USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA, västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Borås, Denver, Las Vegas, Los Angeles, Miami, New York, San-Antonio, Seattle, Silicon dal, Silicon Valley2, Washington DC, Washington DC2, Montrealprotokollet, Quebec City, Toronto, Vancouver |
| **Asien** | Asien, östra, Sydostasien | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei, |
| **Indien** | Indien, västra, Indien, centrala, Indien, södra |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan, västra, Japan, östra |Osaka, Tokyo, Tokyo2 |
| **Oceanien** | Australien, sydöstra, Australien, östra |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Sydkorea** | Sydkorea, centrala; Sydkorea, södra |Busan, Söul|
| **UAE** | Förenade Arabemiraten Central, Förenade Arabemiraten Nord | Dubai, Dubai2 |
| **Sydafrika** | Sydöstra Sydafrika, norra Sydafrika |Kapstaden, Johannesburg |
| **Sydamerika** | Brasilien, södra |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Azure-regioner och politiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |US Gov, Arizona; USA Gov, Iowa; USA Gov, Texas; USA Gov, Virginia; USA DoD, centrala; USA DoD, östra  |Atlanta, Chicago, Borås, New York, Phoenix, San-Antonio, Seattle, Silicon dal, Washington DC |
| **Kina, östra** |Kina, östra, Kina, östra 2 |Shanghai, Shanghai2 |
| **Kina, norra** |Kina, norra, Kina, norra 2 |Beijing, Beijing2 |
| **Tyskland** |Tyskland, centrala, Tyskland, östra |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-anslutningsleverantörer

Följande tabell innehåller anslutningsplatser och tjänstleverantörerna för varje plats. Om du vill visa leverantörer och de platser där de kan tillhandahålla service går du till [Platser efter tjänsteleverantör](expressroute-locations.md).

* **Lokala Azure-regioner** är de som [ExpressRoute lokala](expressroute-faqs.md) på varje peering-plats kan komma åt. **n/a** anger att ExpressRoute Local inte är tillgängligt på peering-platsen.

* **Zon** avser [prissättning](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Global kommersiell Azure
| **Position** | **Adress** | **Fraktzon** | **Lokala Azure-regioner** | **ER direkt** | **Tjänst leverantörer** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa, västra | 10G, 100G | Aryaka Networks, på&T netobligation, British Telecom, Colt, Equinix, euNetworks, GÉANT, intercloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, orange, Tata Communications, telefon ICA, Telenor, Telia transport, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa, västra | 10G, 100G | CenturyLink Cloud Connect, Colt, L'-CIX, euNetworks, Interxion, orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | saknas | saknas | Equinix, Megaport |
| **Auckland** | [Vocus-grupp NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | saknas | 10 G | Devoli, Kordia, Megaport, Spark NZ, Vocus-grupp NZ |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | saknas | 10 G | AIS |
| **Stockholm** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Tyskland, norra | 10 G | NTT globala data Center EMEA|
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Sydkorea, södra | saknas | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australien, centrala | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australien, centrala 2| 10G, 100G | CDC |
| **Kapstaden** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Sydafrika, västra | 10 G | BCX, Internet-lösningar – moln anslutning, flytande telekom, Teraco |
| **Chennai** | Tata Communications | 2 | Indien, södra | 10 G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Indien, södra | saknas | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA, norra centrala | 10G, 100G | Aryaka Networks, på&T netobligation, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW global Limited, Sprint, Telia transport, Verizon, Zayo |
| **Hamn** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | saknas | 10 G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | saknas | 10G, 100G | Aryaka-nätverk, på&T netobligation, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA, västra centrala | saknas | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Förenade Arabemiraten, norra | saknas | Etisalat Förenade Arabemiraten |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Förenade Arabemiraten, norra | saknas | du datamena, Megaport, orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa, norra | 10G, 100G | Colt, EIR, Equinix, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Tyskland, västra centrala | 10G, 100G | PÅ&T netobligation, CenturyLink Cloud Connect, Colt, L'-CIX, Equinix, GEANT, Interxion, Megaport, orange, Telia Carrier |
| **Genèvekonventionen** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Schweiz, västra | 10G, 100G | Equinix, Megaport |
| **Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Asien, östra | saknas | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, Kina, Storbritannien global, Equinix, intercloud, Megaport, NTT Communications, orange, PCCW global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hong Kong2** | [MEGAPIXLAR-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | saknas | 10 G | Kina, telekom, global, PCCW global Limited |
| **Jakarta** | Telkom Indonesien | 4 | saknas | 10 G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Sydafrika, norra | 10 G | BCX, British Telecom, Internet Solutions – Cloud Connect, flytande telekom, orange, Teraco |
| **Kuala Lumpur** | [TID för dotCom Menara-mål](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | saknas | saknas | TIME dotCom |
| **Las Vegas** | [Byt LV](https://www.switch.com/las-vegas) | 1 | saknas | saknas | CenturyLink Cloud Connect, Megaport |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Storbritannien, södra | 10G, 100G | PÅ&T netobligation, British Telecom, Colt, Equinix, euNetworks, intercloud, Internet Solutions – Cloud Connect, Interxion, JISC, Level 3 Communications, Megaport, MTN, NTT Communications, orange, PCCW global Limited, Tata Communications, f House-KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Östra huset, norra två](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Storbritannien, södra | 10G, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Nethouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | saknas | 10 G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Frankrike, södra | saknas | DE CIX, GEANT, Interxion, Jaguar Network, Ooredoo-molnet Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australien, sydöstra | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | saknas | 10 G | Claro, C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milano** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | saknas | 10 G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | saknas | 10G, 100G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Indien, västra | 10 G | Global CloudXchange (GCX), är beroende jio, sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Indien, västra | saknas | Airtel, Sify, Vodafone Idea |
| **München** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | saknas | 10G, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | saknas | saknas | CenturyLink Cloud Connect, Colt, Coresite, Equinix, molnbaserad, Megaport, Packet, Zayo |
| **Newport (Wales)** | [Nästa generations data](https://www.nextgenerationdata.co.uk) | 1 | Storbritannien, västra | saknas | Brittiska telekom, Colt, nivå 3-kommunikation, nästa generations data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japan, västra | 10G, 100G | Colt, Equinix, Internet-initiativet Japan Inc.-IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Östra Norge | 10G, 100G | Megaport, Telenor, Telia-bärvåg |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Frankrike, centrala | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, intercloud, Interxion, orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | saknas | 10 G | Megaport, NextDC |
| **Quebec City** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kanada, östra | saknas | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA, södra centrala | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brasilien, södra | saknas | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA, västra 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Söul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Sydkorea, centrala | 10G, 100G | KINX, KT, LG CNS, Sejong telekom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA, västra | 10G, 100G | Aryaka Networks, på&T netobligation, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, intercloud, Internet2, IX Reach, paket, PacketFabric, Level 3 Communications, Megaport, orange, Sprint, Tata kommunikation, Telia-bärvåg, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA, västra | 10G, 100G | Colt, Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Sydostasien | 10G, 100G | Aryaka Networks, på&T netobligation, British Telecom, Kina Mobile International, Epsilon global kommunikation, Equinix, intercloud, Level 3 Communications, Megaport, NTT Communications, orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global växel i Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Sydostasien | 10G, 100G | Kina Unicom global, Colt, Epsilon Global Communications, Megaport, PCCW global Limited, SingTel |
| **Stavanger** | [Grön mountainbike DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norge, väst | 10G, 100G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | saknas | 10 G | Equinix, Telia-bärvåg |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australien, östra | 10G, 100G | AARNet, på&T netobligation, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus-grupp NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australien, östra | 10G, 100G | Megaport, NextDC |
| **Taipei** | Chief Telecom | 2 | saknas | 10 G | Chief Telecom, Chunghwa Telecom, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japan, östra | 10G, 100G | Aryaka-nätverk, på&T netobligation, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet-initiativet Japan Inc.-IIJ, Megaport, NTT Communications, NTT öst, orange, Softbank, Verizon |
| **Tokyo2** | [I TOKYO](https://www.attokyo.com/) | 2 | Japan, östra | 10G, 100G | I TOKYO |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Kanada, centrala | 10G, 100G | PÅ&T netobligation, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | saknas | 10 G | Cologix |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Östra USA, östra USA 2 | 10G, 100G | Aryaka Networks, på&T netobligation, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, intercloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, orange, PacketFabric, SES, Sprint, Tata Communications, Telia transport, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Östra USA, östra USA 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, INTELSAT, Viasat, Zayo | 
| **Zürich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Schweiz, norra | 10G, 100G | Mellan moln, Interxion, Megaport, Swisscom |

 **+** kommer snart

### <a name="national-cloud-environments"></a>Nationella molnmiljöer

De nationella Azure-molnen är isolerade från varandra och från globala affärs Azure. ExpressRoute för ett Azure-moln kan inte ansluta till Azure-regionerna i de andra.

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Position** | **Adress** | **Lokala Azure-regioner**| **ER direkt** | **Tjänst leverantörer** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | saknas | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | saknas | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | saknas | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | saknas | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov, Arizona | saknas | PÅ&T netobligation, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov, Texas | saknas | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | saknas | 10G, 100G | PÅ&T, Equinix, nivå 3-kommunikation, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | saknas | saknas | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD, östra US Gov, Virginia | 10G, 100G | PÅ&T netobligation, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kina
| **Position** | **Tjänst leverantörer** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | Kina, telekom, Kina, Unicom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | Kina, telekom, Kina, Unicom, GDS |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Tyskland
| **Position** | **Tjänst leverantörer** |
| --- | --- |
| **Stockholm** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Anslutning via Exchange-leverantörer
Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="connectivity-through-satellite-operators"></a>Anslutning via satellit operatörer
Om du är fjärran sluten och inte har fiber anslutning eller om du vill utforska andra anslutnings alternativ kan du kontrol lera följande satellit operatörer. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Anslutning via ytterligare tjänst leverantörer
| **Position** | **Exchange** | **Anslutningsleverantörer** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, nivå 3-kommunikation | BICS, CloudXpress, euro fiber, Fastweb S. p. A, Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, POST telekom Luxembourg, Proximus, RETN, TDC Erhverv, telekom Italia gnistrande, telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Kron Castle
| **Kapstaden** | Teraco | MTN |
| **Chicago** | Equinix| Kron Castle, spektrum Enterprise, windstream |
| **Dallas** | Equinix, Megaport | AXTEL, C3ntro Telecom, Cox Business, kron Castle, data Funna, spektrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Folkrepubliken Kinas särskilda administrativa region Hongkong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon, Epsilon-begränsad, exponentiell E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Kron Castle, spektrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Aptum Technologies, Inc. teknik, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, kron Castle, spektrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon, Epsilon Limited, LGA telekom, United information motorväg (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Luftgrinds teknikerna Inc., Beanfield MetroConnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, kron Castle, GTT Communications Inc, Epsilon-Limited Limited, Masergy, windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Kontinent** | **Systemintegratörer** |
| --- | --- |
| **Asien** |Avanade Inc., OneAs1a |
| **Australien** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Nordamerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Sydamerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
