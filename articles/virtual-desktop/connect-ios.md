---
title: Ansluta till virtuella Windows-datorer från iOS – Azure
description: Ansluta till virtuella Windows-datorer med iOS-klienten.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4239f5271e4df282cf667b4bf1eff5f55f6f6323
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611712"
---
# <a name="connect-with-the-ios-client"></a>Ansluta med iOS-klienten

> Gäller för: iOS 13,0 eller senare. Kompatibel med iPhone, iPad och iPod touch.

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/connect-ios-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan komma åt Windows-resurser för virtuella skriv bord från din iOS-enhet med vår nedladdnings bara klient. I den här guiden får du lära dig hur du konfigurerar iOS-klienten.

## <a name="install-the-ios-client"></a>Installera iOS-klienten

Kom igång genom att [Ladda ned](https://aka.ms/rdios) och installera-klienten på din iOS-enhet.

## <a name="subscribe-to-a-feed"></a>Prenumerera på en feed

Prenumerera på den feed som administratören har fått för att hämta listan över hanterade resurser som du har åtkomst till på din iOS-enhet.

Så här prenumererar du på en feed:

1. I anslutnings Center trycker du på **+** och sedan på **Lägg till arbets yta**.
2. Ange feed-URL: en i fältet **feed URL** . Feed-URL: en kan vara antingen en URL eller en e-postadress.
   - Om du använder en URL kan du använda den som din administratör fick. Normalt är <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>URL: en.
   - Ange din e-postadress om du vill använda e-post. Detta instruerar klienten att söka efter en URL som är kopplad till din e-postadress om administratören har konfigurerat servern på det sättet.
3. Tryck på **Nästa**.
4. Ange dina autentiseringsuppgifter när du uppmanas till det.
   - Ge användar namnet behörighet att komma åt resurser för **användar namn**.
   - Ange lösen ordet som är associerat med användar namnet för **lösen ord**.
   - Du kan också uppmanas att ange ytterligare faktorer om din administratör har konfigurerat autentiseringen på det sättet.
5. Tryck på **Spara**.

Därefter bör anslutnings centret Visa fjär resurserna.

När du prenumererar på en feed uppdateras feedens innehåll automatiskt regelbundet. Resurser kan läggas till, ändras eller tas bort utifrån ändringar som gjorts av administratören.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder iOS-klienten finns i avsnittet [komma igång med iOS-klientprogramvaran](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
