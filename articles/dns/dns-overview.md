---
title: Vad är Azure DNS?
description: Översikt över DNS-värdtjänst på Microsoft Azure. Värd för din domän på Microsoft Azure.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: rohink
ms.openlocfilehash: 1543c0daae7d637730a5f8f9da2305423ba7f84e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76932408"
---
# <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Azure DNS är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

Du kan inte använda Azure DNS för att köpa ett domännamn. För en årlig avgift så kan du köpa ett domännamn med hjälp av [App Service-domäner](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) eller en domännamnsregistrator från tredje part. Dina domäner kan sedan hanteras i Azure DNS för posthantering. Mer information finns i [delegera en domän till Azure DNS](dns-domain-delegation.md).

Följande funktioner ingår med Azure DNS.

## <a name="reliability-and-performance"></a>Tillförlitlighet och prestanda

DNS-domäner i Azure DNS finns på Azures globala nätverk med DNS-namnservrar. Azure DNS använder anycast-nätverk. Varje DNS-fråga besvaras av den närmast tillgängliga DNS-servern, vilket ger både snabb prestanda och hög tillgänglighet för din domän.

## <a name="security"></a>Säkerhet

 Azure DNS bygger på Azure Resource Manager, erbjuder bland annat följande funktioner:

* [Rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) för att kontrollera vem som har åtkomst till specifika åtgärder för din organisation.

* [Aktivitetsloggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) för att övervaka hur en användare i organisationen ändrar en resurs eller hitta ett fel vid felsökning.

* [Resurslåsning](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) för att låsa en prenumeration, resursgrupp eller resurs. Låsning förhindrar att andra användare i organisationen tar bort eller ändrar viktiga resurser av misstag.

Mer information finns i [Så skyddar du DNS-zoner och -poster](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS stöder för närvarande inte DNSSEC. I de flesta fall kan du minska behovet av DNSSEC genom att konsekvent använda HTTPS/TLS i dina program. Om DNSSEC är ett kritiskt krav för dina DNS-zoner kan du vara värd för dessa zoner med DNS-leverantörer från tredje part.

## <a name="ease-of-use"></a>Användbarhet

 Azure DNS kan hantera DNS-poster för dina Azure-tjänster och tillhandahåller DNS för dina externa resurser. Azure DNS är integrerat i Azure-portalen och använder samma autentiseringsuppgifter, supportkontrakt och fakturering som dina andra Azure-tjänster. 

Debiteringen i DNS baseras på antalet DNS-zoner som tillhandahålls i Azure och antalet DNS-frågor som mottagits. Mer information om prissättning finns på sidan om [prisinformation för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

Dina domäner och poster kan hanteras med hjälp av Azure-portalen, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med tjänsten med hjälp av REST API och SDK:er.

## <a name="customizable-virtual-networks-with-private-domains"></a>Anpassningsbara virtuella nätverk med privata domäner

Azure DNS stöder också privata DNS-domäner. Med den här funktionen kan du använda dina egna anpassade domännamn i dina privata virtuella nätverk i stället för de namn som tillhandahålls av Azure och är tillgängliga i dag.

Mer information finns på sidan om att [använda Azure DNS för privata domäner](private-dns-overview.md).

## <a name="alias-records"></a>Aliasposter

Azure DNS stöder aliaspostuppsättningar. Du kan använda en aliasuppsättning för att referera till en Azure-resurs, till exempel en offentlig Azure-IP-adress, en Azure Traffic Manager-profil eller en Azure Content Delivery Network-slutpunkt (CDN). Om IP-adressen för den underliggande resursen ändras uppdateras aliaspostuppsättningen sömlöst under DNS-matchningen. Aliaspostuppsättningen pekar på tjänstinstansen och tjänstinstansen associeras med en IP-adress.

Du kan nu peka din Apex eller en blott-domän till en Traffic Manager profil eller CDN-slutpunkt med hjälp av en aliasresurspost. Ett exempel kan vara contoso.com.

Mer information finns i [översikten över Azure DNS-aliasposter](dns-alias.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig om DNS-zoner och poster i [översikten över DNS-zoner och poster](dns-zones-records.md).

* Lär dig hur du skapar en zon i Azure DNS i [Skapa en DNS-zon](./dns-getstarted-create-dnszone-portal.md).

* Vanliga frågor om Azure DNS finns i [vanliga frågor och svar för Azure DNS](dns-faq.md).

