---
title: Instrument panelen för användning i Microsofts marknads plats analys
description: Lär dig hur du kommer åt alla virtuella datorer erbjuder användning och mätning av mät värden. Gå till användnings instrument panelen i Partner Center under kommersiell marknads plats.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: b60475c56b18e81b082e7552fec789b06adeff27
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746667"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Instrument panelen för användning i Microsofts marknads plats analys

Den här artikeln innehåller information om användnings instrument panelen i Partner Center. Den här instrument panelen visar alla virtuella datorer som erbjuder användnings mått och mätnings mått i två separata flikar: VM-användning och avgiftsbelagd fakturerings användning.

Öppna instrument panelen för användning genom att öppna **[analys](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** instrument panelen under **kommersiell marknads**plats.

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i [vanliga frågor och terminologi för affärs platsers analys](./faq-terminology.md).

## <a name="usage-dashboard"></a>Instrumentpanelen Användning

Användnings instrument panelen representerar måtten för all virtuell dator (VM) erbjuder användning och mätning av fakturerings användning. Dessa mått finns i två separata flikar: användning av virtuella datorer och avgiftsbelagd fakturerings användning.

På fliken VM-användning finns grafiska representationer av följande objekt:

- [Användnings översikt](#usage-summary)
- [Användning efter geografi](#usage-by-geography)
- [Användning efter erbjudanden](#usage-by-offers)
- [Användnings trend per erbjudanden och SKU: er](#usage-trend-by-offers-and-skus)
- [Användning per erbjudande typ](#usage-by-offer-type)
- [Användning efter VM-storlek](#usage-by-vm-size)
- [Användning per försäljnings kanal](#usage-by-sales-channel)
- [Detaljerade användnings data](#detailed-usage-data)

> [!NOTE]
> Analys rapporter visas på olika sätt i Cloud Partner Portal (CPP) och partner Center. **Säljare insikter** i cpp har fliken beställningar och användning, som visar data för både användnings erbjudanden och icke-användnings erbjudanden. Användnings måtten visas på en separat sida i Partner Center.

### <a name="usage-summary"></a>Användningssammanfattning

I tabellen användnings översikt visas kund förbruknings timmar för alla erbjudanden som de har köpt.

- Normaliserad användnings tid definieras som de användnings timmar som normaliseras för antalet virtuella dator kärnor ([antal VM-kärnor] x [timmar RAW Usage]). Virtuella datorer som anges som "SHAREDCORE" använder 1/6 (eller 0,1666) som [antal VM-kärnor] multiplikatorn.
- Rå användnings timmar definieras som antalet virtuella datorer som har körts i timmar.
- Procentvärdet representerar användnings öknings ändringar för det valda datum intervallet ([senaste månads användning – första månads användning])/första månads användning).
- Gröna trianglar som pekar uppåt visar en tillväxt ändring.
- Röd triangel som pekar nedåt indikerar en negativ tillväxt ändring i förhållande till föregående månad.
- Micro bar-diagram representerar månads värden. Du kan visa värdet för varje månad genom att hovra över kolumnerna i diagrammet.

### <a name="usage-by-geography"></a>Användning efter geografi

Den **normaliserade användningen av geografi** -termisk karta visar användnings timmar som mappats enligt kundens land/region. Färg variationen för land/region representerar normaliserad användnings koncentration. Återgå till den ursprungliga vyn genom att trycka på knappen **Start** på kartan.

### <a name="usage-by-offers"></a>Användning efter erbjudanden

- I cirkel diagrammet **normalisera användning per erbjudande** visas en analys av normaliserade användnings timmar per erbjudanden enligt det valda datum intervallet. De fem främsta erbjudandena visas i Graph, medan resten grupperas i resten av **alla** kategorier.
- Stapeldiagrammet visar en tillväxt trend i månaden för det valda datum intervallet. Månads kolumnerna representerar användnings timmar från erbjudandena med högst användnings timmar för respektive månad. Linje diagrammet visar trenden för tillväxt procent ritad på den sekundära Y-axeln.
- Använd skjutreglaget överst i diagrammet för att rulla från höger till vänster längs x-axeln och/eller fokusera på vissa data punkter.

### <a name="usage-trend-by-offers-and-skus"></a>Användnings trend per erbjudanden och SKU: er

Det här diagrammet visar trenden för normaliserad användning för de valda SKU: erna för ett erbjudande. Erbjudandet ranknings lista visar de översta 50-erbjudandenna med högsta användning, sorterade efter användnings timmar. SKU-ranknings lista visar de översta 50 SKU: erna med den högsta användningen för det valda erbjudandet.

### <a name="usage-by-offer-type"></a>Användning per erbjudande typ

- Cirkel diagrammet **användning per erbjudande typ** organiserar användningen enligt erbjudande typen.
- De främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som "rest alla".
- **Trend** diagrammet visar trender i månads tillväxt. Kolumnen månad representerar de vanligaste erbjudande typerna i den månaden.

### <a name="usage-by-vm-size"></a>Användning efter VM-storlek

Det här diagrammet representerar användnings trenden för valda VM-storlekar (högst 5) av alla dina erbjudanden/SKU: er. Stapeldiagrammet staplas med användnings tiderna för de valda VM-storlekarna.

Ranknings lista visar de virtuella 50 VM-storlekarna med högsta användning och sorterat efter användnings timmar.

### <a name="usage-by-sales-channel"></a>Användning per försäljnings kanal

- Cirkel diagrammet användning per försäljnings kanal ordnar användningen enligt försäljnings kanalen
- Den översta försäljnings kanalen med högsta användning visas i diagrammet och resten av försäljnings kanalen grupperas som "rest all".
- Kolumnen månad representerar den främsta försäljnings kanalen för den månaden.
- Funktionerna i det här diagrammet är desamma som i diagrammet användning av erbjudanden

### <a name="detailed-usage-data"></a>Detaljerade användnings data

**Tabellen användnings information** visar en numrerad lista över de översta och de 1000 användnings posterna sorterade efter användning.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en CSV-fil om antalet poster är mindre än 1000.
- Om antalet poster är över 1000 placeras export data asynkront på en nedladdnings sida som kommer att vara tillgänglig under de närmaste 30 dagarna.
- Filter kan tillämpas på **detaljerad användnings information** för att visa endast de data som du är intresse rad av. Data kan filtreras efter land/region, försäljnings kanal, Marketplace-licens typ, användnings typ, erbjudande namn, erbjudande typ, kostnads fri utvärderings prenumerations-ID, kund-ID och företags namn.

> [!NOTE]
> Välj **användnings typen** i sid filtret för att visa diagram på sidan i antingen "normaliserad vy" eller "rå vy". Standardvyn för de här diagrammen är "normaliserad vy".

**Användnings sidans filter** tillämpas på sidnivå. Du kan välja flera filter för att återge diagrammet för de kriterier du väljer att visa och de data som du vill ska visas i rutnätet/exporten "detaljerade användnings data". Filter tillämpas på de data som extraheras för det data intervall som du har valt i det övre högra hörnet på sidan beställningar.

- **Erbjudande typer** och **erbjudande namn** visas bara för de erbjudanden du har köpt under det valda datum intervallet. Erbjudande namnen i listan visas för de erbjudande typer som väljs i listan.
- Standard valet är "alla" för varje filter alternativ, förutom **användnings typ**. Standard valet för **användnings typ** är normaliserad användning. Välj "rå användning" om du vill visa rå data i diagrammen.
- Tillämpade filter visar antalet val för de filter val som har gjorts. Tillämpade filter visas inte för standard markeringarna.

> [!NOTE]
> En detaljerad definition av varje fält i rutnätet "detaljerade order data", sid filter och alla möjliga val definieras i avsnittet data ord lista i artikeln [FAQ och terminologi](link needed) .

På fliken **avgiftsbelagd fakturerings användning** visas användnings information för erbjudande typer där användningen mäts per mätnings dimension. Skriv överanvändning av SaaS visas för närvarande. På fliken visas grafiska representationer av överförbruknings trender för SaaS-avläsning av fakturerings användning:

- **Överförbruknings trend per mätnings dimension**: visar den månatliga överförbruknings trenden för den valda mätar dimensionen för ett erbjudande. X-axeln representerar månaden och Y-axeln representerar förbruknings antalet. Mått enheten för den anpassade mätaren visas också på Y-axeln.
- Överförbruknings **trend per SKU**: representerar trenden för användnings antalet för den valda mätar dimensionen enligt SKU: er. De SKU: er som visas visar de 5 främsta SKU: erna med högsta användnings nivå för det valda erbjudandet.
- Överförbruknings **trend med de främsta 50 kunderna**: de översta 50-erbjudandena med högst användnings timmar visas på en ***ledare*** och rangordnas med den högsta användningen av den anpassade mätaren. Välj en kund i ranknings lista om du vill visa användnings trenden för en vald mätnings dimension.
- **Överförbruknings trend per främsta kunder**: visar de främsta kund percentilerna som bidrar till% av den övergripande användningen. Den främsta kund percentilen visas längs X-axeln och bestäms av kundens användnings antal. Y-axeln visar användnings antalet. Du kan visa information genom att hovra över punkter längs linje diagrammet.

> [!NOTE]
> Användnings informationen och alla diagram på den här sidan visas för den valda mätnings dimensionen för sid filtret.

## <a name="next-steps"></a>Efterföljande moment

- En översikt över analys rapporter som är tillgängliga i partner Centers kommersiella marknads platser finns i [analys för den kommersiella Marketplace i Partner Center](./analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](./customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Microsoft AppSource och Azure Marketplace, se [instrument panelen för klassificering och granskning i marknads plats analys](./ratings-reviews.md).
- Vanliga frågor och svar om den kommersiella Marketplace-analysen och en omfattande ord lista med data termer finns i [vanliga frågor och termer för att få en kommersiell Marketplace-analys](./faq-terminology.md).
