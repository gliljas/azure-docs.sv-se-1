---
title: Övervakning och diagnostik i Azure Service Fabric nätappar
description: Lär dig mer om att övervaka och diagnostisera program i Service Fabric nät på Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75498155"
---
# <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik
Azure Service Fabric Mesh är en fullständigt hanterad tjänst som gör att utvecklare kan distribuera mikrotjänstprogram utan att hantera virtuella datorer, lagring eller nätverk. Övervakning och diagnostik för Service Fabric nät kategoriseras i tre huvud typer av diagnostikdata:

- Program loggar – dessa definieras som loggar från dina behållares program, baserat på hur du har instrumenterat ditt program (t. ex. Docker-loggar)
- Plattforms händelser – händelser från den nät plattform som är relevant för din behållar åtgärd, inklusive behållar aktivering, inaktive ring och avslutning.
- Container mått – resursutnyttjande och prestanda mått för dina behållare (Docker stats statistik)

I den här artikeln beskrivs alternativ för övervakning och diagnostik för den senaste för hands versionen som är tillgänglig.

## <a name="application-logs"></a>Program loggar

Du kan visa dina Docker-loggar från dina distribuerade behållare på en per container-basis. I programmets modell för Service Fabric nät är varje behållare ett kod paket i ditt program. Om du vill se de associerade loggarna med ett kod paket använder du följande kommando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Du kan använda kommandot "AZ nät service-Replica" för att hämta replik namnet. Replik namn ökar heltalen från 0.

Det här ser ut så här för att se loggarna från VotingWeb. Code-behållaren från röstnings programmet:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Container mått 

Nät miljön visar en fåtal av mått som anger hur dina behållare presterar. Följande mått är tillgängliga via Azure Portal och Azure Monitor CLI:

| Mått | Beskrivning | Enheter|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu som en procent andel | % |
| MemoryUtilization | ActualMem/AllocatedMem som en procent andel | % |
| AllocatedCpu | Allokerad processor per Azure Resource Manager mall | Millicores |
| AllocatedMemory | Allokerat minne per Azure Resource Manager mall | MB |
| ActualCpu | CPU-användning | Millicores |
| ActualMemory | Minnes användning | MB |
| Container status | 0 – ogiltig: behållar statusen är okänd <br> 1 – väntar: behållaren har schemalagts att starta <br> 2-start: behållaren håller på att starta <br> 3-startad: behållaren har startats <br> 4-stopp: behållaren stoppas <br> 5-stoppad: behållaren har stoppats | Ej tillämpligt |
| ApplicationStatus | 0-okänd: statusen kan inte hämtas <br> 1 – klart: programmet körs <br> 2 – uppgradering: en pågående uppgradering pågår <br> 3 – skapar: programmet skapas <br> 4-tar bort: programmet tas bort <br> 5 – misslyckades: det gick inte att distribuera programmet | Ej tillämpligt |
| ServiceStatus | 0 – ogiltig: tjänsten har för närvarande ingen hälso status <br> 1 – OK: tjänsten är felfri  <br> 2-varning: det kan vara något fel som kräver undersökning <br> 3-fel: det är något fel som kräver undersökning <br> 4-okänd: statusen kan inte hämtas | Ej tillämpligt |
| ServiceReplicaStatus | 0 – ogiltig: repliken har för närvarande ingen hälso status <br> 1 – OK: tjänsten är felfri  <br> 2-varning: det kan vara något fel som kräver undersökning <br> 3-fel: det är något fel som kräver undersökning <br> 4-okänd: statusen kan inte hämtas | Ej tillämpligt | 
| RestartCount | Antal omstarter av behållare | Ej tillämpligt |

> [!NOTE]
> Värdena för ServiceStatus och ServiceReplicaStatus är desamma som för [hälso](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) tillstånd i Service Fabric. 

Varje mått är tillgängligt på olika dimensioner så att du kan se agg regeringar på olika nivåer. Den aktuella listan med dimensioner är följande:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName-dimensionen är inte tillgänglig för Linux-program. 

Varje dimension motsvarar olika komponenter i [Service Fabric program modellen](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

En fullständig lista över kommandon finns i [Azure Monitor CLI-dokument](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , men vi har inkluderat några användbara exempel nedan 

I varje exempel är resurs-ID: t följande mönster

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-användning för behållarna i ett program

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Minnes användning för varje tjänst replik
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Startar om för varje behållare i ett 1 timme-fönster 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Genomsnittlig CPU-belastning mellan tjänster med namnet "VotingWeb" i ett 1 timmes fönster
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Mått Utforskaren

Metrics Explorer är ett blad i portalen där du kan visualisera alla mät värden för ditt nätprogram. Det här bladet är tillgängligt på programmets sida i portalen och på bladet Azure Monitor, som du kan använda för att visa mått för alla dina Azure-resurser som stöder Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Nästa steg
* Mer information om Service Fabric Mesh finns i [översikten över Service Fabric Mesh](service-fabric-mesh-overview.md).
* Mer information om kommandon för Azure Monitor mått finns i [Azure Monitor CLI-dokument](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
