---
title: Konfigurera hantering av autentiseringsprinciper – Azure Active Directory
description: Anpassa konfigurationen av Azure AD-autentisering för autentisering inklusive användarens inloggnings frekvens och beständighet för webbläsarsessionen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bba37b5d72bd9dca2e10c699f4ba259319a6c71
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995109"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurera hantering av autentisering med villkorlig åtkomst

I komplexa distributioner kan organisationer ha behov av att begränsa sessionerna för autentisering. Vissa scenarier kan vara:

* Resurs åtkomst från en ohanterad eller delad enhet
* Åtkomst till känslig information från ett externt nätverk
* Användare med hög påverkan
* Kritiska företags program

Med villkorliga åtkomst kontroller kan du skapa principer som riktar sig mot specifika användnings fall i organisationen utan att påverka alla användare.

Innan du simhopp till information om hur du konfigurerar principen ska vi kontrol lera standard konfigurationen.

## <a name="user-sign-in-frequency"></a>Användar inloggnings frekvens

Inloggnings frekvensen definierar den tids period innan en användare uppmanas att logga in igen vid försök att komma åt en resurs.

Standard konfigurationen av Azure Active Directory (Azure AD) för användar inloggnings frekvens är ett rullande fönster på 90 dagar. Att be användare om autentiseringsuppgifter verkar ofta vara lämpligat att göra, men det kan vara refire: användare som har tränats att ange sina autentiseringsuppgifter utan att fundera på att oavsiktligt ange dem till en fråga om obehöriga autentiseringsuppgifter.

Det kan bero på att användaren inte behöver be om en användare att logga in igen, i verkligheten att en överträdelse av IT-principerna återkallar sessionen. Några exempel är (men är inte begränsade till) en lösen ords ändring, en inkompatibel enhet eller inaktivt konto. Du kan även uttryckligen [återkalla användarnas sessioner med hjälp av PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Standard konfigurationen för Azure AD kommer inte att be användarna att ange sina autentiseringsuppgifter om säkerhets position inte har ändrats i sina sessioner.

Inställningen för inloggnings frekvens fungerar med appar som har implementerat OAUTH2-eller OIDC-protokoll enligt standarderna. De flesta inbyggda Microsoft-appar för Windows, Mac och Mobile, inklusive följande webb program, följer inställningen.

- Word, Excel, PowerPoint Online
- OneNote online
- Office.com
- O365 administrations Portal
- exchange online
- SharePoint och OneDrive
- Team webb klient
- Dynamics CRM Online
- Azure Portal

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Användar inloggnings frekvens och Multi-Factor Authentication

Inloggnings frekvensen har tidigare endast tillämpats på den första Factor Authentication på enheter som var Azure AD-ansluten, en hybrid Azure AD-anslutning och Azure AD har registrerats. Det fanns inget enkelt sätt för våra kunder att genomdriva Multi Factor Authentication (MFA) på dessa enheter. Beroende på kundfeedback kommer inloggnings frekvensen även gälla för MFA.

### <a name="user-sign-in-frequency-and-device-identities"></a>Användar inloggnings frekvens och enhets identiteter

Om du har en Azure AD-ansluten, en hybrid Azure AD-anslutning eller registrerade Azure AD-enheter, kommer den här händelsen att uppfylla principen för inloggnings frekvens även när en användare låser upp sin enhet eller loggar i interaktivt. I följande 2 exempel anges användar inloggnings frekvensen till 1 timme:

Exempel 1:

- Vid 00:00 loggar användaren in på Windows 10 Azure AD-anslutna enheter och börjar arbeta med ett dokument som är lagrat på SharePoint Online.
- Användaren fortsätter att arbeta med samma dokument på sina enheter i en timme.
- Vid 01:00 uppmanas användaren att logga in igen baserat på kravet på inloggnings frekvens i principen för villkorlig åtkomst som kon figurer ATS av administratören.

Exempel 2:

- Vid 00:00 loggar användaren in på Windows 10 Azure AD-anslutna enheter och börjar arbeta med ett dokument som är lagrat på SharePoint Online.
- Vid 00:30 hämtar användaren upp och tar en paus låsning av enheten.
- Vid 00:45 återgår användaren från sin paus och låser upp enheten.
- Vid 01:45 uppmanas användaren att logga in igen baserat på kravet på inloggnings frekvens i principen för villkorlig åtkomst som kon figurer ATS av administratören sedan den senaste inloggningen skedde vid 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistence för att bläddra i sessioner

Med en beständig webbläsarsession kan användarna vara inloggade när de har stängt och öppnat sina webbläsarfönster igen.

Med Azure AD-beständighet för webbläsarsessionen kan användare på personliga enheter välja om de vill spara sessionen genom att visa en "förbli inloggad?" prompt efter slutförd autentisering. Om webb läsar persisten har kon figurer ATS i AD FS med hjälp av vägledningen i artikeln [AD FS inställningar för enkel inloggning](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), kommer vi att följa principen och även behålla Azure AD-sessionen. Du kan också konfigurera om användare i din klient organisation ska se "förbli inloggad?" genom att ändra lämplig inställning i rutan för företags anpassning i Azure Portal att använda vägledningen i artikeln [Anpassa din inloggnings sida för Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurera kontroller för autentisering av sessionen

Villkorlig åtkomst är en Azure AD Premium funktion och kräver en Premium-licens. Om du vill veta mer om villkorlig åtkomst, se [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Observera att vi inte har stöd för att skapa två olika principer för samma användar-eller app-kombination om du använder den [konfigurerbara livs längden för token](../develop/active-directory-configurable-token-lifetimes.md) i en offentlig för hands version. Microsoft planerar att dra tillbaka den konfigurerbara livs längden för token den 1 maj 2020 och ersätta den med funktionen för hantering av autentisering med villkorlig åtkomst.  

### <a name="policy-1-sign-in-frequency-control"></a>Princip 1: kontroll för inloggnings frekvens

1. Skapa ny princip
1. Välj alla nödvändiga villkor för kundens miljö, inklusive mål molnappar.

   > [!NOTE]
   > Vi rekommenderar att du anger samma frekvens för autentiserings-och körnings frekvensen för viktiga Microsoft Office appar som Exchange Online och SharePoint Online för bästa möjliga användar upplevelse.

1. Gå till **Access Controls**-  >  **sessionen** och klicka på **inloggnings frekvens**
1. Ange det obligatoriska värdet för dagar och timmar i den första text rutan
1. Välj ett värde för **timmar** eller **dagar** från List rutan
1. Spara principen

![Princip för villkorlig åtkomst har kon figurer ATS för inloggnings frekvens](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

På Azure AD-registrerade Windows-enheter loggar du in på enheten som en uppfråga. Om du till exempel har konfigurerat inloggnings frekvensen till 24 timmar för Office-appar, så uppfyller användare i Azure AD-registrerade Windows-enheter inloggnings frekvens principen genom att logga in på enheten och kommer inte att tillfrågas igen när Office-appar öppnas.

Om du har konfigurerat olika inloggnings frekvenser för olika webbappar som körs i samma webbläsarsession, kommer den striktaste principen att tillämpas på båda apparna eftersom alla appar som körs i samma webbläsarsession delar en token för en session.

### <a name="policy-2-persistent-browser-session"></a>Princip 2: beständig webbläsarsession

1. Skapa ny princip
1. Välj alla obligatoriska villkor.

   > [!NOTE]
   > Observera att den här kontrollen kräver att du väljer alla molnappar som ett villkor. Persistence för webbläsarsessionen styrs av autentiseringstoken för autentisering. Alla flikar i en webbläsarsession delar en token för en session och därför måste alla dela beständiga tillstånd.

1. Gå till **Access Controls**-  >  **sessionen** och klicka på **beständig** webbläsarsession
1. Välj ett värde i list rutan
1. Spara principen

![Princip för villkorlig åtkomst har kon figurer ATS för beständig webbläsare](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Beständig konfiguration av webbläsarsessionen i Azure AD villkorlig åtkomst kommer skriva över "förbli inloggad?" inställningen i rutan för företags anpassning i Azure Portal för samma användare om du har konfigurerat båda principerna.

## <a name="validation"></a>Validering

Använd verktyget för att simulera en inloggning från användaren till mål programmet och andra villkor baserat på hur du har konfigurerat principen. Kontrollerna för hantering av autentisering visas i resultatet av verktyget.

![Resultat för What If verktyget för villkorlig åtkomst](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribution av princip

För att se till att principen fungerar som förväntat, är den rekommenderade rekommenderade metoden att testa den innan den distribueras till produktion. Vi rekommenderar att du använder en test klient för att kontrol lera om den nya principen fungerar som den ska. Mer information finns i [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst kan du läsa artikeln [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
* Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa artikeln [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md).
