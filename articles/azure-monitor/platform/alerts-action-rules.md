---
title: Åtgärds regler för Azure Monitor aviseringar
description: Att förstå vilka åtgärds regler i Azure Monitor är och hur du konfigurerar och hanterar dem.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6585890395d7656f239ac3098cd374ecd4757842
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618977"
---
# <a name="action-rules-preview"></a>Åtgärds regler (förhands granskning)

Med åtgärds regler kan du definiera eller ignorera åtgärder i Azure Resource Manager omfång (Azure-prenumeration, resurs grupp eller mål resurs). De har olika filter som hjälper dig att begränsa den specifika delmängd av varnings instanser som du vill vidta åtgärder för.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Varför och när bör du använda åtgärds regler?

### <a name="suppression-of-alerts"></a>Under tryckning av aviseringar

Det finns många scenarier där det är praktiskt att ignorera de meddelanden som genereras av aviseringar. De här scenarierna sträcker sig från under tryckning under en planerad underhålls period som ska undertryckas under icke-kontors tid. Teamet som ansvarar för **ContosoVM** vill t. ex. Ignorera aviserings aviseringar för den kommande helgen, eftersom **ContosoVM** har planerat underhåll. 

Även om teamet kan inaktivera varje varnings regel som är konfigurerad på **ContosoVM** manuellt (och aktivera den igen efter underhåll), är det inte en enkel process. Med åtgärds regler kan du definiera under tryckning av aviseringar i skala med möjligheten att flexibelt konfigurera under trycknings perioden. I föregående exempel kan teamet definiera en åtgärds regel på **ContosoVM** som förhindrar alla aviserings aviseringar för helgen.


### <a name="actions-at-scale"></a>Åtgärder i skala

Även om aviserings regler hjälper dig att definiera den åtgärds grupp som utlöser när aviseringen genereras, har kunderna ofta en gemensam åtgärds grupp för deras omfattning. Till exempel definierar ett team som ansvarar för resurs gruppen **conto sorg** förmodligen samma åtgärds grupp för alla varnings regler som definierats i **conto sorg**. 

Med åtgärds regler kan du förenkla processen. Genom att definiera åtgärder i skala kan en åtgärds grupp aktive ras för alla aviseringar som genereras i det konfigurerade omfånget. I det tidigare exemplet kan teamet definiera en åtgärds regel på **conto sorg** som utlöser samma åtgärds grupp för alla aviseringar som genereras i den.

> [!NOTE]
> Åtgärds regler gäller för närvarande inte för Azure Service Health aviseringar.

## <a name="configuring-an-action-rule"></a>Konfigurera en åtgärdsregel

Du kan få åtkomst till funktionen genom att välja **Hantera åtgärder** från sidan **aviserings** landning i Azure Monitor. Välj sedan **Åtgärds regler (förhands granskning)**. Du kan komma åt reglerna genom att välja **Åtgärds regler (förhands granskning)** från instrument panelen för landnings sidan för aviseringar.

![Åtgärds regler från Azure Monitor landnings sida](media/alerts-action-rules/action-rules-landing-page.png)

Välj **+ ny åtgärds regel**. 

![Lägg till ny åtgärds regel](media/alerts-action-rules/action-rules-new-rule.png)

Du kan också skapa en åtgärds regel när du konfigurerar en varnings regel.

![Lägg till ny åtgärds regel](media/alerts-action-rules/action-rules-alert-rule.png)

Nu bör du se sidan Flow för att skapa åtgärds regler. Konfigurera följande element: 

![Nytt flöde för skapande av åtgärds regel](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Omfång

Välj först omfånget (Azure-prenumeration, resurs grupp eller mål resurs). Du kan också välja flera-Välj en kombination av omfång i en enda prenumeration.

![Åtgärds regel omfång](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filter villkor

Du kan också definiera filter för att begränsa dem till en viss delmängd av aviseringarna. 

De tillgängliga filtren är: 

* **Allvarlighets grad**: möjlighet att välja en eller flera aviserings allvarlighets grader. **Allvarlighets grad = Sev1** innebär att åtgärds regeln gäller för alla aviseringar som angetts till Sev1.
* **Övervaka tjänst**: ett filter som baseras på den ursprungliga övervaknings tjänsten. Det här filtret är också flera-Välj. Exempel: **Monitor Service = "Application Insights"** innebär att åtgärds regeln gäller för alla Application Insights-baserade aviseringar.
* **Resurs typ**: ett filter baserat på en speciell resurs typ. Det här filtret är också flera-Välj. Till exempel innebär **resurs typ = "Virtual Machines"** att åtgärds regeln gäller för alla virtuella datorer.
* **Varnings regel-ID**: ett alternativ för att filtrera efter vissa aviserings regler med hjälp av Resource Manager-ID för varnings regeln.
* **Övervaknings villkor**: ett filter för varnings instanser med antingen **utlöst** eller **löst** som övervaknings villkor.
* **Beskrivning**: ett regex-matchning (reguljärt uttryck) som definierar en sträng matchning mot beskrivningen, definierad som en del av varnings regeln. Beskrivningen innehåller t. ex. **"Prod"** att matcha alla aviseringar som innehåller strängen "Prod" i deras beskrivningar.
* **Aviserings kontext (nytto Last)**: en regex-matchning som definierar en sträng matchning mot aviserings kontext fälten i en aviserings nytto Last. Till exempel **innehåller aviserings kontext (nytto Last) "Computer-01"** och matchar alla aviseringar vars nytto laster innehåller strängen "Computer-01".

Dessa filter används tillsammans med varandra. Om du till exempel anger **resurs typen ' = Virtual Machines** och **allvarlighets grad ' = Sev0**har du filtrerat för alla **Sev0** -aviseringar endast på dina virtuella datorer. 

![Åtgärds regel filter](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Under tryckning eller konfiguration av åtgärds grupp

Konfigurera sedan åtgärds regeln för antingen aviserings under tryckning eller stöd för åtgärds grupp. Du kan inte välja båda. Konfigurationen fungerar på alla aviserings instanser som matchar det tidigare definierade omfånget och filtren.

#### <a name="suppression"></a>Under tryckning

Om du väljer under **tryckning**konfigurerar du varaktigheten för under tryckning av åtgärder och aviseringar. Välj ett av följande alternativ:
* **Från nu (alltid)**: ignorerar alla meddelanden på obestämd tid.
* **Vid en schemalagd tidpunkt**: undertrycker meddelanden inom en gränsad varaktighet.
* **Med en upprepning**: ignorerar meddelanden på ett återkommande schema för varje dag, varje vecka eller månad.

![Under tryckning av åtgärds regel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Åtgärdsgrupp

Om du väljer **Åtgärds grupp** i växlingen, lägger du till en befintlig åtgärds grupp eller skapar en ny. 

> [!NOTE]
> Du kan bara associera en åtgärds grupp med en åtgärds regel.

![Lägg till eller skapa en ny åtgärds regel genom att välja åtgärds grupp](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Åtgärds regel information

Konfigurera senast följande information för åtgärds regeln:
* Name
* Resurs grupp där den sparas
* Beskrivning 

## <a name="example-scenarios"></a>Exempelscenarier

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: under tryckning av aviseringar baserat på allvarlighets grad

Contoso vill ignorera aviseringar för alla Sev4-aviseringar på alla virtuella datorer i prenumerationens **ContosoSub** varje helg.

**Lösning:** Skapa en åtgärds regel med:
* Omfång = **ContosoSub**
* Filter
    * Allvarlighets grad = **Sev4**
    * Resurs typ = **Virtual Machines**
* Under tryckning med upprepning inställd på veckovis, och **lördag** och **söndag** har marker ATS

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: under tryckning av aviseringar baserat på aviserings kontext (nytto Last)

Contoso vill undertrycka aviseringar för alla logg aviseringar som genererats för **dator-01** i **ContosoSub** i den oändliga som det går genom att underhålla.

**Lösning:** Skapa en åtgärds regel med:
* Omfång = **ContosoSub**
* Filter
    * Övervaka tjänst = **Log Analytics**
    * Aviserings kontext (nytto Last) innehåller **dator-01**
* Under tryckning har angetts till **från nu (alltid)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: åtgärds grupp definierad i en resurs grupp

Contoso har definierat [en mått avisering på en prenumerations nivå](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Men det vill definiera de åtgärder som utlöser specifikt för aviseringar som genereras från resurs gruppen **conto sorg**.

**Lösning:** Skapa en åtgärds regel med:
* Omfång = **conto sorg**
* Inga filter
* Åtgärds grupp inställt på **ContosoActionGroup**

> [!NOTE]
> *Åtgärds grupper som definieras i åtgärds regler och varnings regler fungerar oberoende, utan deduplicering.* I det scenario som beskrivs tidigare, om en åtgärds grupp har definierats för varnings regeln, utlöses den tillsammans med den åtgärds grupp som definierats i åtgärds regeln. 

## <a name="managing-your-action-rules"></a>Hantera dina åtgärds regler

Du kan visa och hantera dina åtgärds regler från List visningen:

![Listvy för åtgärds regler](media/alerts-action-rules/action-rules-list-view.png)

Härifrån kan du aktivera, inaktivera eller ta bort åtgärds regler i skala genom att markera kryss rutan bredvid dem. När du väljer en åtgärds regel öppnas konfigurations sidan. Sidan hjälper dig att uppdatera åtgärds regelns definition och aktivera eller inaktivera den.

## <a name="best-practices"></a>Bästa praxis

Logg aviseringar som du skapar med alternativet [antal resultat](alerts-unified-log.md) genererar en enskild varnings instans genom att använda hela Sök resultatet (som kan sträcka sig över flera datorer). I det här scenariot, om en åtgärds regel använder filtret för **aviserings kontext (nytto Last)** , fungerar det på varnings instansen så länge det finns en matchning. I scenario 2, som beskrivs tidigare, om Sök resultaten för den genererade logg aviseringen innehåller både **dator-01** och **dator-02**, ignoreras hela meddelandet. Ingen avisering har genererats för **dator-02** alls.

![Åtgärds regler och logg aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Om du vill använda logg aviseringar med åtgärds regler skapar du logg aviseringar med alternativet [mått mått](alerts-unified-log.md) . Separata varnings instanser skapas med det här alternativet baserat på dess definierade grupp fält. I scenario 2 genereras separata varnings instanser för **dator-01** och **dator-02**. På grund av åtgärds regeln som beskrivs i scenariot ignoreras endast meddelandet för **dator-01** . Meddelandet för **dator-02** fortsätter att utlösa som vanligt.

![Åtgärds regler och logg aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>När jag konfigurerar en åtgärds regel skulle jag vilja se alla eventuella överlappande åtgärds regler så att jag undviker dubbla meddelanden. Är det möjligt att göra detta?

När du har definierat ett omfång när du konfigurerar en åtgärds regel kan du se en lista över åtgärds regler som överlappar samma omfång (om det finns några). Denna överlappning kan vara något av följande alternativ:

* En exakt matchning: till exempel den åtgärds regel som du definierar och den överlappande åtgärds regeln finns i samma prenumeration.
* En delmängd: den åtgärds regel som du definierar är en prenumeration och den överlappande åtgärds regeln finns i en resurs grupp i prenumerationen.
* En supermängd: till exempel är åtgärds regeln som du definierar i en resurs grupp och den överlappande åtgärds regeln finns i den prenumeration som innehåller resurs gruppen.
* Ett snitt: till exempel är den åtgärds regel som du definierar för **VM1** och **VM2**, och den överlappande åtgärds regeln är på **VM2** och **VM3**.

![Överlappande åtgärds regler](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>När jag konfigurerar en varnings regel är det möjligt att veta om det redan finns definierade åtgärds regler som kan agera på den varnings regel Jag definierar?

När du har definierat mål resursen för aviserings regeln kan du se en lista över åtgärds regler som agerar på samma omfång (om det finns några) genom att välja **Visa konfigurerade åtgärder** under avsnittet **åtgärder** . Den här listan fylls i baserat på följande scenarier för omfånget:

* En exakt matchning: till exempel den varnings regel som du definierar och åtgärds regeln är i samma prenumeration.
* En delmängd: den varnings regel du definierar är en prenumeration och åtgärds regeln finns i en resurs grupp i prenumerationen.
* En supermängd: till exempel är den varnings regel du definierar en resurs grupp och åtgärds regeln finns i den prenumeration som innehåller resurs gruppen.
* Ett snitt: till exempel är varnings regeln du definierar på **VM1** och **VM2**och åtgärds regeln finns på **VM2** och **VM3**.
    
![Överlappande åtgärds regler](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan jag se de aviseringar som har ignorerats av en åtgärds regel?

På [sidan aviserings lista](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)kan du välja ytterligare en kolumn som heter **undertrycks status**. Om meddelandet för en varnings instans ignoreras visas denna status i listan.

![Undertryckta varnings instanser](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Vad händer om det finns en åtgärds regel med en åtgärds grupp och en annan med undertrycket aktiv i samma omfattning?

Under tryckning prioriteras alltid i samma omfång.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som övervakas i två separata åtgärds regler? Får jag ett eller två meddelanden? Till exempel **VM2** i följande scenario:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

För varje varning på VM1 och VM3 utlöses åtgärds grupp AG1 en gång. För varje varning på **VM2**skulle åtgärds gruppen AG1 utlösas två gånger, eftersom åtgärds regler inte deduplicerar åtgärder. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som övervakas i två separata åtgärds regler och ett anrop för åtgärd medan en annan för under tryckning? Till exempel **VM2** i följande scenario:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

För varje varning på VM1 skulle åtgärds gruppen AG1 utlösas en gång. Åtgärder och aviseringar för varje varning på VM2 och VM3 ignoreras. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Vad händer om jag har en varnings regel och en åtgärds regel som definierats för samma resurs som anropar olika åtgärds grupper? Till exempel **VM1** i följande scenario:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
För varje varning på VM1 skulle åtgärds gruppen AG1 utlösas en gång. När varnings regeln "regel 1" utlöses, kommer den också att utlösa AG2. Åtgärds grupper som definieras i åtgärds regler och varnings regler fungerar oberoende, utan deduplicering. 

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om aviseringar i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
