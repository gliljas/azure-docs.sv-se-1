---
title: Självstudie för Mass inbjudan av B2B-samarbets användare – Azure AD
description: I den här självstudien lär du dig hur du använder PowerShell och en CSV-fil för att skicka massinbjudningar till externa Azure AD B2B-samarbetsanvändare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926941"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Självstudier: Massinbjuda Azure AD B2B-samarbetsanvändare

Om du använder Azure Active Directory (Azure AD) B2B-samarbete för att arbeta med externa partners, kan du bjuda in flera gästanvändare till din organisation samtidigt. I den här självstudien får du lära dig hur du använder Azure Portal för att skicka mass inbjudningar till externa användare. Närmare bestämt kan du göra följande:

> [!div class="checklist"]
> * Använd **massredigera användare** för att förbereda en fil med kommaavgränsade värden (. csv) med användar information och Inbjudnings inställningar
> * Ladda upp CSV-filen till Azure AD
> * Verifiera att användarna har lagts till i katalogen

Om du inte har Azure Active Directory skapar du ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="understand-the-csv-template"></a>Förstå CSV-mallen

Hämta och fyll i mallen för Mass uppladdning av CSV så att du kan bjuda in Azure AD-gäst användare i grupp. Den CSV-mall som du hämtar kan se ut som i det här exemplet:

![Kalkyl blad för uppladdning och vidarekoppling förklarar syfte och värden för varje rad och kolumn](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>Struktur för CSV-mall

Raderna i en Hämtad CSV-mall är följande:

- **Versions nummer**: den första raden som innehåller versions numret måste inkluderas i överförings-CSV-filen.
- **Kolumn rubriker**: kolumn rubrikernas format är &lt; *objekt namnet* &gt; [PropertyName] &lt; *obligatoriskt eller tomt*&gt;. Till exempel `Email address to invite [inviteeEmail] Required`. Vissa äldre versioner av mallen kan ha små variationer.
- **Exempel rad**: vi har inkluderat i mallen en rad exempel på acceptabla värden för varje kolumn. Du måste ta bort exempel raden och ersätta den med dina egna poster.

### <a name="additional-guidance"></a>Mer information

- De två första raderna i uppladdnings mal len får inte tas bort eller ändras, eller så går det inte att bearbeta överföringen.
- De obligatoriska kolumnerna visas först.
- Vi rekommenderar inte att du lägger till nya kolumner i mallen. Eventuella ytterligare kolumner som du lägger till ignoreras och bearbetas inte.
- Vi rekommenderar att du laddar ned den senaste versionen av CSV-mallen så ofta som möjligt.

## <a name="prerequisites"></a>Krav

Du behöver två eller flera test-e-postkonton att skicka inbjudningar till. Kontona måste finnas utanför din organisation. Du kan använda vilken typ av konto som helst, t.ex. konton i sociala medier som gmail.com- eller outlook.com-adresser.

## <a name="invite-guest-users-in-bulk"></a>Bjud in gäst användare i bulk

1. Logga in på Azure Portal med ett konto som är en användar administratör i organisationen.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Under **Hantera**väljer **du användare** > **Mass inbjudan**.
4. På sidan **massredigera användare** väljer du **Hämta** för att hämta en giltig CSV-mall med Inbjudnings egenskaper.

    ![Hämtnings knapp för Mass inbjudan](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Öppna CSV-mallen och Lägg till en rad för varje gäst användare. Obligatoriska värden är:

   * **E-postadress att bjuda in** – användaren som får en inbjudan

   * **URL för omdirigering** – URL: en dit den inbjudna användaren vidarebefordras efter att inbjudan har accepterats

    ![Exempel på en CSV-fil med gäst användare angivna](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Använd inte kommatecken i det **anpassade Inbjudnings meddelandet** eftersom de hindrar meddelandet från att kunna parsas.

6. Spara filen.
7. På sidan **gruppbjuda in användare** går du till **överför CSV-filen**och bläddrar till filen. När du väljer filen startar valideringen av. csv-filen. 
8. När fil innehållet verifieras visas **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
9. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som lägger till inbjudningarna. 
10. Om du vill visa jobb statusen väljer **du klicka här för att visa status för varje åtgärd**. Du kan också välja **Mass åtgärds resultat** i avsnittet **aktivitet** . Om du vill ha mer information om varje rad objekt i Mass åtgärden väljer du värdena under kolumnerna **# lyckades**, **# Failure**eller **Totalt antal förfrågningar** . Om fel inträffar visas orsaken till felet.

    ![Exempel på Mass åtgärds resultat](media/tutorial-bulk-invite/bulk-operation-results.png)

11. När jobbet har slutförts visas ett meddelande om att Mass åtgärden har slutförts.

## <a name="verify-guest-users-in-the-directory"></a>Verifiera gäst användare i katalogen

Kontrol lera att gäst användare som du har lagt till finns i katalogen antingen i Azure Portal eller med PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Visa gäst användare i Azure Portal

1. Logga in på Azure Portal med ett konto som är en användar administratör i organisationen.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Under **Hantera** väljer du **Användare**.
4. Under **Visa**väljer du **gäst användare** och verifiera att de användare som du har lagt till visas.

### <a name="view-guest-users-with-powershell"></a>Visa gäst användare med PowerShell

Kör följande kommando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Du bör se de användare som du har bjudit in i listan, med en User Principal Name (UPN) i formatet *EmailAddress*#EXT #\@*Domain*. Till exempel *lstokes_fabrikam. com # ext #\@contoso.onmicrosoft.com*, där contoso.onmicrosoft.com är organisationen som du skickade inbjudningarna från.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort test användar kontona i katalogen i Azure Portal på sidan användare genom att markera kryss rutan bredvid gäst användaren och sedan välja **ta bort**. 

Du kan också köra följande PowerShell-kommando för att ta bort ett användar konto:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Exempelvis: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skickat massinbjudningar till gästanvändare utanför organisationen. Härnäst ska du få lära dig hur inlösningsprocessen för inbjudningar ser ut.

> [!div class="nextstepaction"]
> [Läs mer om inlösningsprocessen för Azure AD B2B-samarbetsinbjudningar](redemption-experience.md)
