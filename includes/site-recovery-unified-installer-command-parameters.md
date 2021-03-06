---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187049"
---
|Parameternamn| Typ | Beskrivning| Möjliga värden|
|-|-|-|-|
| /ServerMode|Obligatorisk|Anger om både konfigurations- och processervrar eller endast processervern ska installeras|CS<br>PS|
|/InstallLocation|Obligatorisk|Den mapp där komponenterna installeras| Vilken mapp på datorn som helst|
|/MySQLCredsFilePath|Obligatorisk|Filsökvägen till platsen där autentiseringsuppgifterna för MySQL-servern lagras|Filen ska vara i det format som anges nedan|
|/VaultCredsFilePath|Obligatorisk|Sökvägen för valvautentiseringsfilen|Giltig sökväg|
|/EnvType|Obligatorisk|Typ av miljö som du vill skydda |VMware<br>NonVMware|
|/PSIP|Obligatorisk|Nätverkskortets IP-adress används för överföring av replikeringsdata| Vilken giltig IP-adress som helst|
|/CSIP|Obligatorisk|Nätverkskortets IP-adress som konfigurationsservern lyssnar på| Vilken giltig IP-adress som helst|
|/PassphraseFilePath|Obligatorisk|Den fullständiga sökvägen till platsen för lösenfrasfilen|Giltig sökväg|
|/BypassProxy|Valfri|Anger att konfigurationsservern ansluter till Azure utan en proxyserver|För att få det här värdet från Venu|
|/ProxySettingsFilePath|Valfri|Proxy-inställningar (standardproxy kräver autentisering, eller en anpassad proxy)|Filen ska vara i det format som anges nedan|
|DataTransferSecurePort|Valfri|Portnumret på PSIP ska användas för replikeringsdata| Giltigt portnummer (standardvärdet är 9433)|
|/SkipSpaceCheck|Valfri|Hoppa över utrymmeskontroll för cachedisk| |
|/AcceptThirdpartyEULA|Obligatorisk|När du flaggar innebär det att du godkänner licensavtalet från tredje part| |
|/ShowThirdpartyEULA|Valfri|Visar licensavtalet (EULA) från tredje part. Om detta anges som indata ignoreras alla andra parametrar| |
