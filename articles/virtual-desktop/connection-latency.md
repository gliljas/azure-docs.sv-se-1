---
title: Användar anslutnings fördröjning för Windows Virtual Desktop – Azure
description: Anslutnings fördröjning för användare av virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607409"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Fastställa svars tid för användar anslutning i virtuellt Windows-skrivbord

Det virtuella Windows-skrivbordet är globalt tillgängligt. Administratörer kan skapa virtuella datorer i alla Azure-regioner som de vill. Anslutnings fördröjningen varierar beroende på var användarna och de virtuella datorerna finns. Windows Virtual Desktop-tjänster distribueras kontinuerligt till nya geografiska områden för att förbättra svars tiden. 
 
[Verktyget Windows Virtual Desktop Experiences uppskattnings verktyg](https://azure.microsoft.com/services/virtual-desktop/assessment/) kan hjälpa dig att avgöra den bästa platsen för att optimera de virtuella datorernas svars tider. Vi rekommenderar att du använder verktyget varannan till tre månader för att se till att den optimala platsen inte har ändrats när Windows Virtual Desktop samlar in till nya områden. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows Virtual Desktop använder Azure-Traffic Manager, som kontrollerar platsen för användarens DNS-server för att hitta den närmaste Windows Virtual Desktop-tjänstinstansen. Vi rekommenderar administratörer att granska platsen för användarens DNS-server innan de väljer plats för de virtuella datorerna.

## <a name="next-steps"></a>Nästa steg

- Om du vill kontrol lera den bästa platsen för optimal svars tid kan du läsa [verktyget Windows Virtual Desktop Experience uppskattnings verktyg](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- För pris listor, se [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Om du vill komma igång med distributionen av virtuella Windows-datorer kan du kolla in [vår självstudie](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).