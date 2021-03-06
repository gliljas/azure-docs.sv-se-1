---
title: Konfigurera belastnings utjämning för Windows Virtual Desktop – Azure
description: Så här konfigurerar du belastnings Utjämnings metoden för en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 447de339d3ceef7aeb1c232605b0e30bbbb1e7d8
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612443"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop

Genom att konfigurera belastnings Utjämnings metoden för en värddator kan du justera Windows Virtual Desktop-miljön så att den bättre passar dina behov.

>[!NOTE]
> Detta gäller inte för en beständig Skriv bords värd eftersom användarna alltid har en 1:1-mappning till en sessionsnyckel i poolen.

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du har följt anvisningarna i [Konfigurera Windows Virtual Desktop PowerShell-modulen](powershell-module.md) för att ladda ned och installera PowerShell-modulen och logga in på ditt Azure-konto.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurera bredd – första belastnings utjämning

Bredd – första belastnings utjämning är standard konfigurationen för nya icke-permanent värdbaserade pooler. Bredd – den första belastnings utjämningen distribuerar nya användarsessioner över alla tillgängliga sessionsbaserade värdar i poolen. När du konfigurerar den första belastnings utjämningen kan du ange en högsta sessionsgräns per session-värd i värd gruppen.

Kör följande PowerShell-cmdlet för att konfigurera en adresspool för att utföra bredd-första belastnings utjämning utan att justera den högsta tillåtna sessionsgränsen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' 
```

För att se till att du har ställt in den bredd-första belastnings Utjämnings metoden kör du följande cmdlet: 

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType 

Name             : hostpoolname 
LoadBalancerType : BreadthFirst
```

Kör följande PowerShell-cmdlet för att konfigurera en adresspool för att utföra bredd-första belastnings utjämning och för att använda en ny maxgräns för sessionen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurera djup-första belastnings utjämning

Djup – den första belastnings utjämningen distribuerar nya användarsessioner till en tillgänglig sessionsvariabel med det högsta antalet anslutningar men har inte uppnått gränsen för högsta antal sessioner. När du konfigurerar djup-första belastnings utjämning, måste du ange en högsta sessionsgräns per session-värd i poolen.

Kör följande PowerShell-cmdlet för att konfigurera en adresspool för att utföra djup-första belastnings utjämning:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ### 
```

Kör denna cmdlet för att se till att inställningen har uppdaterats:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit 

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Konfigurera belastnings utjämning med Azure Portal

Du kan också konfigurera belastnings utjämning med Azure Portal.

Konfigurera belastnings utjämning:

1. Logga in på Azure Portal på https://portal.azure.com. 
2. Sök efter och välj **Windows Virtual Desktop** under tjänster. 
3. På sidan Windows Virtual Desktop väljer du **värdar för pooler**.
4. Välj namnet på den värddator som du vill redigera.
5. Välj **Egenskaper**.
6. Ange **Max gränsen för sessioner** i fältet och välj den **algoritm för belastnings utjämning** som du vill använda för den här poolen i den nedrullningsbara menyn.
7. Välj **Spara**. Detta använder de nya inställningarna för belastnings utjämning.