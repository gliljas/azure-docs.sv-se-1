---
title: Tillgängliga funktioner som stöds i Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över tjänster som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: memildin
ms.openlocfilehash: 22f66a7be27f42bfd0305ad1eaf297ca8c8029fa
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996762"
---
# <a name="feature-coverage-for-machines"></a>Funktions täckning för datorer

Tabellerna nedan visar Azure Security Center funktioner som är tillgängliga för virtuella datorer och servrar.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funktioner som stöds för virtuella datorer och servrar<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows-datorer](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funktion**|**Azure-Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Datorer som inte är Azure-datorer**|**Prissättning**
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Standard|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](threat-protection.md)|✔|✔|✔|Rekommendationer (kostnads fri) </br></br> Säkerhets aviseringar (standard)|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Nätverksbaserade säkerhets aviseringar](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-Time VM-åtkomst](security-center-just-in-time.md)|✔|-|-|Standard|
|[Ursprunglig sårbarhets bedömning](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Övervakning av fil integritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Anpassningsbara nätverks kontroller|✔|✔|-|Standard|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|-|-|-|Standard|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Kostnadsfri|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Kostnadsfri|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Kostnadsfri|
|Disk krypterings bedömning|✔|✔|-|Kostnadsfri|
|Sårbarhets bedömning från tredje part|✔|-|-|Kostnadsfri|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Kostnadsfri|


### <a name="linux-machines"></a>[Linux-datorer](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funktion**|**Azure-Virtual Machines**|**Azure Virtual Machine Scale Sets**|**Datorer som inte är Azure-datorer**|**Prissättning**
|[Microsoft Defender ATP-integrering](security-center-wdatp.md)|-|-|-|Standard|
|[Beteende analys av virtuella datorer (och säkerhets aviseringar)](security-center-alerts-iaas.md)|✔</br>(i versioner som stöds)|✔</br>(i versioner som stöds)|✔|Rekommendationer (kostnads fri) </br></br> Säkerhets aviseringar (standard)|
|[Filbaserad säkerhets aviseringar](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Nätverksbaserade säkerhets aviseringar](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Just-in-Time VM-åtkomst](security-center-just-in-time.md)|✔|-|-|Standard|
|[Ursprunglig sårbarhets bedömning](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Övervakning av fil integritet](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Anpassningsbara programkontroller](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Nätverks karta](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Anpassningsbar nätverkshärdning](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Anpassningsbara nätverks kontroller|✔|✔|-|Standard|
|[Instrument panel för regelefterlevnad & rapporter](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Rekommendationer och hot skydd på Docker-värdbaserade IaaS-behållare|✔|✔|✔|Standard|
|Utvärdering av OS-korrigeringsfiler som saknas|✔|✔|✔|Kostnadsfri|
|Utvärdering av felkonfigurationer för säkerhet|✔|✔|✔|Kostnadsfri|
|[Endpoint Protection-utvärdering](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Kostnadsfri|
|Disk krypterings bedömning|✔|✔|-|Kostnadsfri|
|Sårbarhets bedömning från tredje part|✔|-|-|Kostnadsfri|
|[Nätverks säkerhets utvärdering](security-center-network-recommendations.md)|✔|✔|-|Kostnadsfri|

--- 


> [!TIP]
>För att experimentera med funktioner som bara är tillgängliga på standard pris nivån kan användare på den kostnads fria nivån registreras i en 30-dagars utvärderings version. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Slut punkts skydds lösningar som stöds<a name="endpoint-supported"></a>

Följande tabell innehåller en matris med:

 - Om du kan använda Azure Security Center för att installera varje lösning åt dig.
 - Vilka lösningar för slut punkts skydd Security Center kan identifiera. Om en Endpoint Protection-lösning från den här listan identifieras rekommenderar Security Center inte att installera en.

Information om när rekommendationer genereras för vart och ett av dessa skydd finns i [Endpoint Protection utvärdering och rekommendationer](security-center-endpoint-protection.md).

| Slutpunktsskydd| Plattformar | Installation av Security Center | Security Center Discovery |
|------|------|-----|-----|
| Windows Defender (Microsoft-programvara mot skadlig kod)| Windows Server 2016| Nej, inbyggd i OS| Ja |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 (se anmärkning nedan) | Via tillägg | Ja |
| Trend Micro – djup säkerhet | Windows Server-familjen  | Nej | Ja |
| Symantec v12.1.1100+| Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Windows Server-familjen  | Nej | Ja |
| McAfee v10 + | Linux Server-serien  | Nej | Ja**\*** |
| Sophos v9 +| Linux Server-serien  | Nej | Ja**\***  |

 **\*** Täcknings tillstånd och kompletterande data är för närvarande bara tillgängligt i Log Analytics arbets ytan som är kopplad till dina skyddade prenumerationer. Den visas inte i Azure Security Center portalen.

> [!NOTE]
> Identifiering av System Center Endpoint Protection (SCEP) på en virtuell dator med Windows Server 2008 R2 kräver SCEP för att installeras efter PowerShell 3,0 (eller en övre version).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data och Log Analytics agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Granska de [plattformar som har stöd för Security Center](security-center-os-coverage.md).