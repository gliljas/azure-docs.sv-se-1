---
title: Windows Virtual Desktop-miljö – Azure
description: De grundläggande elementen i en Windows Virtual Desktop-miljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad535dd18b89cbe2fceab90f73789180ad332b57
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612376"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop-miljö

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/environment-setup-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop är en tjänst som ger användare enkel och säker åtkomst till sina virtualiserade skriv bord och RemoteApp-datorer. I det här avsnittet får du veta mer om den allmänna strukturen i Windows Virtual Desktop-miljön.

## <a name="host-pools"></a>Värdar för pooler

En adresspool är en samling virtuella Azure-datorer som registreras på Windows Virtual Desktop som värdar för Windows Virtual Desktop-agenten. Alla virtuella datorer i en värdstat i en adresspool måste ha en konsekvent användar upplevelse från samma avbildning.

En värdbaserad pool kan vara en av två typer:

- Personligt, där varje sessions värd tilldelas enskilda användare.
- I pooler, där sessions-värdar kan acceptera anslutningar från alla användare som har behörighet till en app-grupp i poolen värd.

Du kan ange ytterligare egenskaper för poolen för att ändra dess belastnings Utjämnings beteende, hur många sessioner varje sessions värd kan ta och vad användaren kan göra för att agera värdar i värddatorn när de loggat in på sina Windows-sessioner med virtuella skriv bord. Du styr vilka resurser som publiceras till användare via app-grupper.

## <a name="app-groups"></a>Appgrupper

En app-grupp är en logisk gruppering av program som är installerade på värdarna i värd gruppen. En app-grupp kan vara en av två typer:

- RemoteApp, där användarna får åtkomst till de RemoteAppar du individuellt väljer och publicerar i app-gruppen
- Skriv bord, där användare kommer åt hela Skriv bordet

Som standard skapas en Skriv bords grupp (med namnet "Skriv bords program grupp") automatiskt när du skapar en adresspool. Du kan när som helst ta bort den här program gruppen. Du kan dock inte skapa en annan grupp för Skriv bords appar i poolen värd när en grupp app-grupp finns. Om du vill publicera RemoteApp-program måste du skapa en RemoteApp-gruppgrupp. Du kan skapa flera RemoteApp-appar för att hantera olika arbets scenarier. Olika RemoteApp-app-grupper kan också innehålla överlappande RemoteApp-program.

Om du vill publicera resurser till användare måste du tilldela dem till app-grupper. Tänk på följande när du tilldelar användare till app-grupper:

- En användare kan tilldelas både en Skriv bords grupp och en RemoteApp-app grupp i samma adresspool. Användare kan dock bara starta en typ av app-grupp per session. Användare kan inte starta båda typerna av app-grupper samtidigt i en enda session.
- En användare kan tilldelas flera app-grupper inom samma adresspool och deras flöde är en ackumulering av båda app-grupperna.

## <a name="workspaces"></a>Arbetsytor

En arbets yta är en logisk gruppering av program grupper i Windows Virtual Desktop. Varje Windows-programgrupp för virtuella skriv bord måste vara kopplad till en arbets yta för att användarna ska kunna se de fjärrappar och skriv bord som publicerats till dem.  

## <a name="end-users"></a>Slutanvändare

När du har tilldelat användare till sina app-grupper kan de ansluta till en Windows-distribution med virtuella skriv bord med någon av de virtuella Windows-klienterna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om delegerad åtkomst och hur du tilldelar roller till användare i [delegerad åtkomst i Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Information om hur du konfigurerar en Windows-pool för virtuella skriv bord finns i [skapa en adresspool med Azure Portal](create-host-pools-azure-marketplace.md).

Information om hur du ansluter till virtuella Windows-datorer finns i någon av följande artiklar:

- [Anslut med Windows 10 eller Windows 7](connect-windows-7-and-10.md)
- [Ansluta till en webbläsare](connect-web.md)
- [Ansluta med Android-klienten](connect-android.md)
- [Ansluta med macOS-klienten](connect-macos.md)
- [Ansluta med iOS-klienten](connect-ios.md)