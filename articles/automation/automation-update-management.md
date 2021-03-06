---
title: Översikt över Azure Automation Uppdateringshantering
description: Den här artikeln innehåller en översikt över den Uppdateringshantering funktionen som implementerar uppdateringar för dina Windows-och Linux-datorer.
services: automation
ms.subservice: update-management
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: 4c27fa26b19b870f90f2e7d6ecd34f1f3c083323
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847336"
---
# <a name="update-management-overview"></a>Översikt över Uppdateringshantering

Du kan använda Uppdateringshantering i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-datorer i Azure, i lokala miljöer och i andra moln miljöer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agent datorer och hantera processen för att installera nödvändiga uppdateringar för servrar.

Du kan aktivera Uppdateringshantering för virtuella datorer på följande sätt:

* Från ditt [Azure Automation-konto](automation-onboard-solutions-from-automation-account.md) för en eller flera Azure-datorer.
* Manuellt för datorer som inte är Azure-datorer.
* För en enskild virtuell Azure-dator från sidan virtuell dator i Azure Portal. Det här scenariot är tillgängligt för virtuella [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) -och [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) -datorer.
* För [flera virtuella Azure-datorer](manage-update-multi.md) genom att välja dem från sidan virtuella datorer i Azure Portal.

> [!NOTE]
> Uppdateringshantering måste länka en Log Analytics arbets yta till ditt Automation-konto. En slutgiltig lista över regioner som stöds finns i [mappningar för Azure-arbetsytor](how-to/region-mappings.md). Region mappningarna påverkar inte möjligheten att hantera virtuella datorer i en separat region från ditt Automation-konto.

En [Azure Resource Manager mall](automation-update-management-deploy-template.md) är tillgänglig för att hjälpa dig att distribuera uppdateringshantering till ett nytt eller befintligt Automation-konto och Log Analytics arbets yta i din prenumeration.

> [!NOTE]
> Du kan inte använda en dator som kon figurer ATS med Uppdateringshantering för att köra anpassade skript från Azure Automation. Den här datorn kan bara köra det Microsoft-signerade uppdaterings skriptet. 

## <a name="about-update-management"></a>Om Uppdateringshantering

Datorer som hanteras med Uppdateringshantering använder följande konfigurationer för att utföra utvärderingen och för att uppdatera distributioner:

* Log Analytics agent för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation Hybrid Runbook Worker
* Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

I följande diagram illustreras hur Uppdateringshantering utvärderar och tillämpar säkerhets uppdateringar på alla anslutna Windows Server-och Linux-datorer på en arbets yta:

![Uppdateringshantering arbets flöde](./media/automation-update-management/update-mgmt-updateworkflow.png)

Uppdateringshantering kan användas för att distribuera datorer internt i flera prenumerationer i samma klient organisation.

När ett paket har frigjorts tar det 2 till 3 timmar innan korrigeringen visas för Linux-datorer för utvärdering. För Windows-datorer tar det 12 till 15 timmar innan korrigeringen visas för utvärderingen när den har släppts.

När en dator har slutfört en sökning efter uppdateringens efterlevnad vidarebefordrar agenten informationen i bulk till Azure Monitor loggar. På en Windows-dator körs kompatibilitetskontroll var 12: e timme som standard.

Förutom genomsöknings schemat startas genomsökningen efter uppdateringens kompatibilitet inom 15 minuter från det Log Analytics agenten startas om, innan installationen installeras och efter installationen av uppdateringen.

För en Linux-dator utförs genomsökningen varje timme som standard. Om den Log Analytics agenten startas om startas en kompatibilitetskontroll inom 15 minuter.

Uppdateringshantering rapporterar hur upp datorn är baserad på vilken källa du är konfigurerad att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till WSUS, beroende på när WSUS senast synkroniserades med Microsoft Update, kan resultatet skilja sig från vad Microsoft Update visar. Detta är detsamma för Linux-datorer som kon figurer ATS för att rapportera till en lokal lagrings platsen i stället för till en offentlig lagrings platsen.

> [!NOTE]
> För att rapporten ska kunna rapporteras till tjänsten måste vissa URL: er och portar vara aktiverade för Uppdateringshantering. Mer information om dessa krav finns i [nätverks konfiguration](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker#network-planning).

Du kan distribuera och installera program uppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som valfria ingår inte i distributions omfånget för Windows-datorer. Endast nödvändiga uppdateringar ingår i distributions omfånget.

Den schemalagda distributionen definierar vilka mål datorer som får tillämpliga uppdateringar. Det gör det antingen genom att uttryckligen ange vissa datorer eller genom att välja en [dator grupp](https://docs.microsoft.com/azure/azure-monitor/platform/computer-groups) som baseras på loggs ökningar av en viss uppsättning datorer (eller på en [Azure-fråga](automation-update-management-query-logs.md) som dynamiskt väljer virtuella Azure-datorer baserat på angivna kriterier). Dessa grupper skiljer sig från [omfattnings konfigurationen](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting), som används för att styra målet för datorer som tar emot konfigurationen för att aktivera uppdateringshantering. Detta förhindrar att de utför och rapporterar uppdaterings efterlevnad och installerar godkända uppdateringar som krävs.

När du definierar en distribution anger du även ett schema för att godkänna och ange en tids period under vilken uppdateringar kan installeras. Den här perioden kallas underhålls perioden. En 20 minuters period i underhålls perioden är reserverad för omstarter, förutsatt att en krävs och du har valt lämpligt alternativ för omstart. Om korrigeringen tar längre tid än förväntat och det finns mindre än 20 minuter i underhålls perioden görs ingen omstart.

Uppdateringar installeras av runbooks i Azure Automation. Du kan inte visa dessa Runbooks och de kräver inte någon konfiguration. När en uppdaterings distribution skapas, skapar den ett schema som startar en huvud uppdaterings-Runbook vid den angivna tiden för de datorer som ingår. Huvud-Runbook startar en underordnad Runbook på varje agent för att installera nödvändiga uppdateringar.

Vid det datum och den tid som anges i uppdaterings distributionen kör mål datorerna distributionen parallellt. Före installationen körs en genomsökning för att verifiera att uppdateringarna fortfarande krävs. För WSUS-klientdatorer, om uppdateringarna inte är godkända i WSUS, Miss lyckas uppdaterings distributionen.
En dator som är registrerad för Uppdateringshantering i fler än en Log Analytics arbets yta (kallas även multihoming) stöds inte.

## <a name="clients"></a>Klienter

### <a name="supported-client-types"></a>Klient typer som stöds

I följande tabell visas de operativ system som stöds för utvärdering av uppdateringar. Uppdatering kräver en Hybrid Runbook Worker. Information om Hybrid Runbook Worker krav finns i [distribuera en Windows-hybrid Runbook Worker](automation-windows-hrw-install.md) och [distribuera en Linux-hybrid Runbook Worker](automation-linux-hrw-install.md).

> [!NOTE]
> Uppdaterings utvärdering av Linux-datorer stöds bara i vissa regioner enligt listan i Automation-kontot och Log Analytics [mappnings tabellen](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)för arbets ytan. 

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center Core/standard)<br><br>Windows Server 2016 (Data Center/Data Center Core/standard)<br><br>Windows Server 2012 R2 (Data Center/standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM och SP1 standard)| Uppdateringshantering stöder endast utvärderingar för det här operativ systemet. Uppdatering stöds inte eftersom [hybrid Runbook Worker](automation-windows-hrw-install.md) inte stöds för Windows Server 2008 R2. |
|CentOS 6 (x86/x64) och 7 (x64)      | Linux-agenter kräver åtkomst till ett uppdaterings lager. Klassificerings-baserad uppdatering kräver `yum` att returnera säkerhets data som CentOS inte har i sina RTM-versioner. Mer information om klassificerings-baserad uppdatering på CentOS finns i [uppdaterings klassificeringar i Linux](automation-view-update-assessments.md#linux-2).          |
|Red Hat Enterprise 6 (x86/x64) och 7 (x64)     | Linux-agenter kräver åtkomst till ett uppdaterings lager.        |
|SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)     | Linux-agenter kräver åtkomst till ett uppdaterings lager.        |
|Ubuntu 14,04 LTS, 16,04 LTS och 18,04 (x86/x64)      |Linux-agenter kräver åtkomst till ett uppdaterings lager.         |

> [!NOTE]
> Skalnings uppsättningar för virtuella Azure-datorer kan hanteras via Uppdateringshantering. Uppdateringshantering fungerar på själva instanserna och inte på bas avbildningen. Du måste schemalägga uppdateringarna på ett stegvist sätt så att alla VM-instanser inte uppdateras samtidigt. Du kan lägga till noder för skalnings uppsättningar för virtuella datorer genom att följa stegen under [Lägg till en icke-Azure-dator i ändringsspårning och inventering](automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory).

### <a name="unsupported-client-types"></a>Klient typer som inte stöds

I följande tabell visas operativ system som inte stöds:

|Operativsystem  |Anteckningar  |
|---------|---------|
|Windows-klient     | Klient operativ system (t. ex. Windows 7 och Windows 10) stöds inte.<br> Den rekommenderade metoden för Azure Windows Virtual Desktop (WVD)<br> för att hantera uppdateringar är [Windows Update för Business](https://docs.microsoft.com/windows/deployment/update/waas-manage-updates-wufb) för hantering av klient datorer för Windows 10. |
|Windows Server 2016 Nano Server     | Stöds inte.       |
|Azure Kubernetes service-noder | Stöds inte. Använd korrigerings processen som beskrivs i [tillämpa säkerhets-och kernel-uppdateringar på Linux-noder i Azure Kubernetes service (AKS)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Klientkrav

Följande information beskriver särskilda klient krav för operativ systemet. Ytterligare vägledning finns i [nätverks planering](#ports).

#### <a name="windows"></a>Windows

Windows-agenter måste konfigureras för att kommunicera med en WSUS-server, eller de kräver åtkomst till Microsoft Update. Information om hur du installerar Log Analytics agent för Windows finns i [ansluta Windows-datorer till Azure Monitor](../log-analytics/log-analytics-windows-agent.md).

Du kan använda Uppdateringshantering med Microsoft Endpoint Configuration Manager. Mer information om integrations scenarier finns i [integrera uppdateringshantering med Windows-slutpunkt Configuration Manager](updatemgmt-mecmintegration.md). [Log Analytics agent för Windows](../azure-monitor/platform/agent-windows.md) krävs för Windows-servrar som hanteras av-platser i din Configuration Manager-miljö. 

Som standard är virtuella Windows-datorer som distribueras från Azure Marketplace inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det här beteendet ändras inte när du lägger till virtuella Windows-datorer i din arbets yta. Om du inte aktivt hanterar uppdateringar med hjälp av Uppdateringshantering gäller standard beteendet (för att tillämpa uppdateringar automatiskt).

> [!NOTE]
> Du kan ändra grupprincip så att omstarter av datorn bara kan utföras av användaren, inte av systemet. Hanterade datorer kan fastna om Uppdateringshantering inte har behörighet att starta om datorn utan manuell interaktion från användaren. Mer information finns i [konfigurera grupprincip inställningar för automatiska uppdateringar](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

För Linux kräver datorn åtkomst till ett uppdaterings lager, antingen privat eller offentlig. TLS 1,1 eller TLS 1,2 krävs för att interagera med Uppdateringshantering. Uppdateringshantering har inte stöd för en Log Analytics-agent för Linux som är konfigurerad att rapportera till fler än en Log Analytics arbets yta. Datorn måste också ha python 2. x installerat.

> [!NOTE]
> Uppdaterings utvärdering av Linux-datorer stöds bara i vissa regioner. Se tabellen Automation-konto och Log Analytics [mappningar](https://docs.microsoft.com/azure/automation/how-to/region-mappings#supported-mappings)för arbets ytor. 

Information om hur du installerar Log Analytics-agenten för Linux och laddar ned den senaste versionen finns i [Log Analytics agent för Linux](../azure-monitor/platform/agent-linux.md). 

Virtuella datorer som skapats från RHEL-avbildningar (på begäran Red Hat Enterprise Linux) som är tillgängliga på Azure Marketplace är registrerade för att få åtkomst till den [Red Hat-RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md) som distribueras i Azure. Alla andra Linux-distributioner måste uppdateras från distributionens online-fillagringsplats genom att använda de metoder som stöds av distributionen.

## <a name="permissions"></a>Behörigheter

Om du vill skapa och hantera uppdaterings distributioner måste du ha vissa behörigheter. Mer information om dessa behörigheter finns i [rollbaserad åtkomst – uppdateringshantering](automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Uppdateringshantering-komponenter

Uppdateringshantering använder resurserna som beskrivs i det här avsnittet. De här resurserna läggs automatiskt till ditt Automation-konto när du aktiverar Uppdateringshantering.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker grupper

När du har aktiverat Uppdateringshantering konfigureras en Windows-dator som är direkt ansluten till din Log Analytics arbets yta automatiskt som en Hybrid Runbook Worker för att stödja Runbooks som stöder Uppdateringshantering.

Varje Windows-dator som hanteras av Uppdateringshantering visas i rutan hybrid Worker-grupper som en system hybrid Worker-grupp för Automation-kontot. I grupperna används `Hostname FQDN_GUID` namngivnings konventionen. Du kan inte rikta de här grupperna med Runbooks i ditt konto. Om du försöker, Miss lyckas försöket. De här grupperna är endast avsedda att stödja Uppdateringshantering.

Du kan lägga till Windows-datorn i en Hybrid Runbook Worker grupp i ditt Automation-konto för att stödja Automation-runbooks om du använder samma konto för Uppdateringshantering och medlemskapet i Hybrid Runbook Worker gruppen. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din Operations Manager hanterings grupp är [ansluten till en Log Analytics arbets yta](../azure-monitor/platform/om-agents.md)installeras följande hanterings paket i Operations Manager. Dessa hanterings paket installeras också för Uppdateringshantering på direkt anslutna Windows-datorer. Du behöver inte konfigurera eller hantera dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

> [!NOTE]
> Om du har en Operations Manager 1807-eller 2019-hanteringsserver ansluten till en Log Analytics arbets yta med agenter som kon figurer ATS i hanterings gruppen för att samla in loggdata måste du åsidosätta parametern `IsAutoRegistrationEnabled` och ange den som sann i regeln **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Mer information om uppdateringar av hanterings paket finns i [anslut Operations Manager till Azure Monitor loggar](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> För att Uppdateringshantering helt hantera datorer med Log Analytics-agenten måste du uppdatera till Log Analytics agent för Windows eller Log Analytics agent för Linux. Information om hur du uppdaterar agenten finns i [så här uppgraderar du en Operations Manager-agent](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). I miljöer som använder Operations Manager måste du köra System Center Operations Manager 2012 R2 UR 14 eller senare.

## <a name="data-collection"></a>Datainsamling

### <a name="supported-sources"></a>Källor som stöds

I följande tabell beskrivs de anslutna källor som Uppdateringshantering stöder:

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Yes |Uppdateringshantering samlar in information om system uppdateringar från Windows-agenter och startar sedan installationen av nödvändiga uppdateringar. |
| Linux-agenter |Yes |Uppdateringshantering samlar in information om system uppdateringar från Linux-agenter och startar sedan installationen av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Yes |Uppdateringshantering samlar in information om system uppdateringar från agenter i en ansluten hanterings grupp.<br/><br/>En direkt anslutning från Operations Manager agent till Azure Monitor loggar krävs inte. Data vidarebefordras från hanterings gruppen till Log Analytics-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

Uppdateringshantering söker igenom hanterade datorer efter data med hjälp av följande regler. Det kan ta mellan 30 minuter och 6 timmar för instrument panelen att Visa uppdaterade data från hanterade datorer.

* Varje Windows-dator – Uppdateringshantering görs en genomsökning två gånger per dag för varje dator. Var 15: e minut frågar den den senaste uppdaterings tiden till Windows-API: n för att fastställa om statusen har ändrats. Om statusen har ändrats startar Uppdateringshantering en sökning efter kompatibiliteten.

* Varje Linux-dator – Uppdateringshantering genomsöks varje timme.

Den genomsnittliga data användningen per Azure Monitor loggar för en dator som använder Uppdateringshantering är cirka 25 MB per månad. Det här värdet är bara en uppskattning och kan komma att ändras, beroende på din miljö. Vi rekommenderar att du övervakar din miljö för att hålla koll på din exakta användning. Mer information om hur du analyserar data användningen finns i [Hantera användning och kostnad](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planera för nätverk

Följande adresser krävs specifikt för Uppdateringshantering. Kommunikationen med de här adresserna sker via port 443.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

För Windows-datorer måste du också tillåta trafik till alla slut punkter som krävs av Windows Update. Du hittar en uppdaterad lista med nödvändiga slut punkter i [problem som rör http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Om du har en lokal [Windows Update-Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)måste du också tillåta trafik till servern som anges i [WSUS-nyckeln](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

För Red Hat Linux-datorer, se [IP-adresser för RHUI Content Delivery servers](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) för obligatoriska slut punkter. Andra Linux-distributioner finns i leverantörens dokumentation.

Mer information om vilka portar som krävs för Hybrid Runbook Worker finns i [uppdateringshantering adresser för Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Vi rekommenderar att du använder de adresser som anges när du definierar undantag. För IP-adresser kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och den återspeglar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen.

Följ anvisningarna i [ansluta datorer utan Internet åtkomst](../azure-monitor/platform/gateway.md) för att konfigurera datorer som inte har Internet åtkomst.

## <a name="update-classifications"></a>Klassificering av uppdateringar

I följande tabell definieras de klassificeringar som Uppdateringshantering stöder för Windows-uppdateringar. 

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett särskilt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service Pack     | En kumulativ uppsättning snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus-eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som är installerad för närvarande.        |

Nästa tabell definierar de klassificeringar som stöds för Linux-uppdateringar.

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska eller som inte är av säkerhets uppdateringar.        |

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet och Visa utvärderings data på grund av data berikning i molnet. Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i RTM-versionen. Om du har CentOS-datorer som har kon figurer ATS för att returnera säkerhets data för följande kommando kan Uppdateringshantering korrigeras baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För närvarande tillhandahålls kunder som kanske har aktiverat den här funktionen på egen hand. 

Om du vill klassificera uppdateringar i Red Hat Enterprise version 6 måste du installera plugin-programmet yum-Security. På Red Hat Enterprise Linux 7 är plugin-programmet redan en del av yum och det finns inget behov av att installera något. Mer information finns i följande artiklar om Red Hat- [kunskap](https://access.redhat.com/solutions/10021).

## <a name="integrate-update-management-with-configuration-manager"></a>Integrera Uppdateringshantering med Configuration Manager

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av styrka och mognad för Configuration Manager för att hantera program uppdateringar. Information om hur du integrerar Uppdateringshantering med Configuration Manager finns i [integrera uppdateringshantering med Windows-slutpunkt Configuration Manager](updatemgmt-mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Uppdateringar från tredje part på Windows

Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att uppdatera Windows-system som stöds, antingen WSUS eller Windows Update. Med verktyg som [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) kan du importera och publicera anpassade uppdateringar med WSUS. Med det här scenariot kan Uppdateringshantering uppdatera datorer som använder Configuration Manager som uppdaterings lagrings plats med program vara från tredje part. Information om hur du konfigurerar Updates Publisher finns i [Installera Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

En Azure [Resource Manager-mall](automation-update-management-deploy-template.md) är tillgänglig för att hjälpa dig att distribuera uppdateringshantering till ett nytt eller befintligt Automation-konto och Azure Monitor Log Analytics arbets ytan i din prenumeration. Den konfigurerar inte omfånget för datorer som ska hanteras. Detta utförs som ett separat steg när du har använt mallen.

Här är hur du kan aktivera Uppdateringshantering och välja datorer som ska hanteras:

* [Från en virtuell dator](automation-onboard-solutions-from-vm.md)
* [Från att bläddra flera datorer](automation-onboard-solutions-from-browse.md)
* [Från ett Azure Automation konto](automation-onboard-solutions.md)

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med Uppdateringshantering finns i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).

* Läs vanliga frågor om Uppdateringshantering i [Azure Automation vanliga frågor och svar](automation-faq.md).
