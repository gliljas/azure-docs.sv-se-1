---
title: Planera och felsöka ändringar i Azure User princip namn (UPN)
description: Förstå kända problem och begränsningar vid ändringar i UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743336"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planera och Felsök ändringar av UPN-namn i Azure Active Directory

Ett UPN (User Principal Name) är ett attribut som är en Internet kommunikations standard för användar konton. Ett UPN består av ett UPN-prefix (användar kontots namn) och ett UPN-suffix (ett DNS-domännamn). Prefixet ansluter till suffixet med hjälp av symbolen "@". Till exempel someone@example.com. Ett UPN måste vara unikt bland alla säkerhets objekt i en katalog skog. 

> [!NOTE]
> För utvecklare rekommenderar vi att du använder användaren objectID som den oföränderliga identifieraren, i stället för UPN. Om dina program för närvarande använder UPN rekommenderar vi att du ställer in UPN så att det matchar användarens primära e-postadress för att förbättra sin upplevelse.<br> **I en hybrid miljö är det viktigt att UPN för en användare är identisk i den lokala katalogen och i Azure Active Directory**.

**I den här artikeln förutsätter vi att du använder UPN som användar-ID. IT-adresser planerar för ändringar i UPN och återställer från problem som kan uppstå vid ändringar i UPN.**

## <a name="learn-about-upns-and-upn-changes"></a>Läs om UPN-och UPN-ändringar
Inloggnings sidor uppmanas ofta användarna att ange sin e-postadress när det obligatoriska värdet faktiskt är deras UPN. Därför bör du vara noga med att ändra användarens UPN när deras primära e-postadress ändras.

Användarens primära e-postadresser kan ändras av många orsaker:

* omanpassning av företaget

* anställda flyttar till olika företags indelningar 

* sammanslagningar och förvärv

* ändringar av medarbetares namn

### <a name="types-of-upn-changes"></a>Typer av UPN-ändringar

Du kan ändra ett UPN genom att ändra prefix, suffix eller både och.

* **Ändrar prefixet**.

   *  Om till exempel en persons namn har ändrats kan du ändra kontots namn:  
BSimon@contoso.com tillBJohnson@contoso.com

   * Du kan också ändra företags standarden för prefix:  
Bsimon@contoso.com tillBritta.Simon@contoso.com

* **Ändra suffixet**. <br>

    Om till exempel en person har ändrat divisioner kan du ändra deras domän: 

   * Britta.Simon@contoso.comattBritta.Simon@contosolabs.com <br>
     Eller<br>
    * Britta.Simon@corp.contoso.comattBritta.Simon@labs.contoso.com 

Ändra användarens UPN varje gång den primära e-postadressen för en användare uppdateras. Oavsett anledningen till e-poständringen måste UPN alltid uppdateras för att matcha.

Under den inledande synkroniseringen från Active Directory till Azure AD ser du till att användarnas e-postmeddelanden är identiska med sina UPN.

### <a name="upns-in-active-directory"></a>UPN i Active Directory

I Active Directory är standard UPN-suffixet DNS-namnet på den domän där du skapade användar kontot. I de flesta fall är detta det domän namn som du registrerar som företags domän på Internet. Om du skapar användar kontot i contoso.com-domänen är standard-UPN

username@contoso.com

 Du kan dock [lägga till fler UPN-suffix](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) genom att använda Active Directory domäner och förtroenden. 

Till exempel kanske du vill lägga till labs.contoso.com och användarens UPN-och e-postmeddelanden visar att. De blir då

username@labs.contoso.com.

>[!IMPORTANT]
> Om UPN i Active Directory och Azure Active Directory inte matchar uppstår problem. Om du [ändrar suffixet i Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)måste du se till att ett matchande anpassat domän namn har [lagts till och verifierats i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain). 

![En skärm bild av verifierade domäner](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN i Azure Active Directory

Användarna loggar in på Azure AD med värdet i sitt userPrincipalName-attribut. 

När du använder Azure AD tillsammans med din lokala Active Directory, synkroniseras användar konton med hjälp av tjänsten Azure AD Connect. Som standard använder guiden för Azure AD Connect attributet userPrincipalName från den lokala Active Directory som UPN i Azure AD. Du kan ändra den till ett annat attribut i en anpassad installation.

Det är viktigt att du har en definierad process när du uppdaterar ett UPN (User Principal Name) för en enskild användare eller för hela organisationen. 

Se kända problem och lösningar i det här dokumentet.

När du synkroniserar användar konton från Active Directory till Azure AD kontrollerar du att UPN i Active Directory mappar till verifierade domäner i Azure AD.

![Skärm bild av verifierade domäner](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Om värdet för userPrincipalName-attributet inte motsvarar en verifierad domän i Azure AD, ersätter synkroniseringsprocessen suffixet med värdet default. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Distribuera Mass ändringar i UPN

Följ de [rekommenderade tipsen för en pilot](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) för Mass ändringar av UPN. Du kan också få en testad återställnings plan för att återställa UPN om du upptäcker problem som inte kan lösas snabbt. När piloten har körts kan du börja rikta in små uppsättningar med användare med olika organisatoriska roller och deras specifika uppsättningar av appar eller enheter.

Genom att gå igenom den första delen av användarna får du en bra uppfattning om vad användarna ska förvänta sig som en del av ändringen. Ta med den här informationen om din användar kommunikation.

Skapa en definierad procedur för att ändra UPN för enskilda användare som en del av normal drift. Vi rekommenderar att du har en testad procedur som innehåller dokumentation om kända problem och lösningar.

Följande avsnitt innehåller information om potentiella kända problem och lösningar när UPN-namn ändras.

## <a name="apps-known-issues-and-workarounds"></a>Kända problem och lösningar för appar

[Program vara som en tjänst (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) och branschspecifika program (LOB) är ofta beroende av UPN för att hitta användare och lagra användar profil information, inklusive roller. Program som använder [just-in-Time-etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) för att skapa en användar profil när användare loggar in på appen för första gången kan påverkas av UPN-ändringar.

**Känt problem**<br>
Om du ändrar en användares UPN kan relationen mellan Azure AD-användaren och användar profilen som skapades i programmet brytas. Om programmet använder [just vid tids etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)kan det skapa en helt ny användar profil. Detta kräver att program administratören gör manuella ändringar för att åtgärda den här relationen.

**Korrigera**<br>
Med [Automatisk användar etablering i Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) kan du automatiskt skapa, underhålla och ta bort användar identiteter i moln program som stöds. Konfiguration av automatisk användar etablering i dina program uppdaterar UPN-data automatiskt i programmen. Testa programmen som en del av den Progressive distributionen för att kontrol lera att de inte påverkas av UPN-ändringar.
Om du är utvecklare kan du överväga att [lägga till scim-stöd i programmet](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) för att aktivera automatisk användar etablering från Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Kända problem och lösningar för hanterade enheter

Genom [att ta med dina enheter till Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview)kan du maximera användarnas produktivitet genom enkel inloggning (SSO) i molnet och lokala resurser.

### <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

[Azure AD-anslutna](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) enheter är direkt anslutna till Azure AD och gör att användarna kan logga in på enheten med deras organisations identitet.

**Kända problem** <br>
Användare kan stöta på problem med enkel inloggning med program som är beroende av Azure AD för autentisering.

**Korrigera** <br>
Tillåt tillräckligt med tid för att UPN-ändringen ska synkroniseras med Azure AD. När du har kontrollerat att det nya UPN visas på Azure AD-portalen ber du användaren att välja panelen "annan användare" för att logga in med sitt nya UPN. Du kan också kontrol lera genom [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). När du har loggat in med sitt nya UPN kan referenser till det gamla UPN fortfarande visas i Windows-inställningen "åtkomst till arbets plats eller skola".

![Skärm bild av verifierade domäner](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

[Hybrid Azure AD-anslutna](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) enheter är anslutna till Active Directory och Azure AD. Du kan implementera hybrid Azure AD-anslutning om din miljö har en lokal Active Directory plats och du även vill dra nytta av de funktioner som tillhandahålls av Azure AD.

**Kända problem** 

Windows 10 hybrid Azure AD-anslutna enheter upplever förmodligen oväntade omstarter och åtkomst problem.

Om användarna loggar in på Windows innan det nya UPN har synkroniserats till Azure AD, eller om du fortsätter att använda en befintlig Windows-session, kan det uppstå problem med enkel inloggning med program som använder Azure AD för autentisering om villkorlig åtkomst har kon figurer ATS för att framtvinga användning av hybrid anslutna enheter för att få åtkomst till resurser. 

Dessutom visas följande meddelande, och en omstart görs efter en minut. 

"Datorn kommer att startas om automatiskt om en minut. Windows stötte på ett problem och måste startas om. Du bör stänga det här meddelandet nu och spara ditt arbete.

**Korrigera** 

Enheten måste vara frånkopplad från Azure AD och startas om. Efter omstarten kommer enheten automatiskt att ansluta till Azure AD igen och användaren måste logga in med det nya UPN: en genom att välja panelen "annan användare". Om du vill koppla från en enhet från Azure AD kör du följande kommando i en kommando tolk:

**dsregcmd /leave**

Användaren måste registrera för Windows Hello för företag [igen](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) om den används. Windows 7-och 8,1-enheter påverkas inte av det här problemet när UPN-ändringar har ändrats.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator kända problem och lösningar

Din organisation kan kräva att [Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) används för att logga in och få åtkomst till organisationens program och data. Även om ett användar namn kan visas i appen är kontot inte konfigurerat att fungera som en verifierings metod förrän användaren har slutfört registrerings processen.

[Microsoft Authenticator-appen](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) har fyra huvud funktioner:

* Multi-Factor Authentication via ett push-meddelande eller verifierings kod

* Agera som en autentiseringsprovider på iOS-och Android-enheter för att tillhandahålla enkel inloggning för program som använder [Broker-autentisering](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Enhets registrering (kallas även Workplace Join) till Azure AD, vilket är ett krav för andra funktioner som Intune-appskydd och enhets registrerings/hantering.

* Telefon inloggning, som kräver MFA och enhets registrering.

### <a name="multi-factor-authentication-with-android-devices"></a>Multi-Factor Authentication med Android-enheter

Microsoft Authenticator-appen erbjuder ett alternativ för out-of-band-verifiering. I stället för att placera ett automatiserat telefonsamtal eller SMS till användaren vid inloggningen skickar [Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) ett meddelande till Microsoft Authenticator-appen på användarens smartphone eller surfplatta. Användaren trycker bara på Godkänn (eller anger en PIN-kod eller bio metrisk och trycker på "autentisera") i appen för att slutföra sin inloggning.

**Kända problem** 

När du ändrar en användares UPN visas fortfarande det gamla UPN på användar kontot och ett meddelande kanske inte tas emot. [Verifierings koderna](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) fortsätter att fungera.

**Korrigera**

Om ett meddelande tas emot instruerar du användaren att stänga meddelandet, öppna Authenticator-appen, trycka på alternativet "kontrol lera aviseringar" och godkänna MFA-prompten. Därefter kommer det UPN som visas på kontot att uppdateras. Observera att det uppdaterade UPN kan visas som ett nytt konto, detta beror på andra funktioner för autentisering som används. Mer information hittar du i de ytterligare kända problemen i den här artikeln.

### <a name="brokered-authentication"></a>Brokered Authentication

På Android-och iOS-mäklare som Microsoft Authenticator aktivera:

* Enkel inloggning (SSO) – användarna behöver inte logga in på varje program.

* Enhets identifiering – koordinatorn använder enhets certifikatet som skapades på enheten när den anslöts till arbets platsen.

* Verifiering av program identifiering – när ett program anropar koordinatorn, skickas dess omdirigerings-URL och Service Broker verifierar den.

Dessutom gör det möjligt för program att delta i mer avancerade funktioner, till exempel [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/), och stöder [Microsoft Intune scenarier](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Kända problem**<br>
Användaren visas med fler interaktiva autentiserings-prompter på nya program som använder Broker-assisterad inloggning på grund av ett matchnings fel mellan login_hint som skickats av programmet och det UPN som är lagrat i koordinatorn.

**Korrigera** <br> Användaren måste ta bort kontot manuellt från Microsoft Authenticator och starta en ny inloggning från ett Service Broker-program. Kontot läggs automatiskt till efter den första autentiseringen.

### <a name="device-registration"></a>Enhetsregistrering

Microsoft Authenticator-appen ansvarar för att registrera enheten i Azure AD. Enhets registrering gör att enheten kan autentisera till Azure AD och är ett krav för följande scenarier:

* Intune App Protection

* Registrering av Intune-enheter

* Telefonin loggning

**Kända problem**<br>
När du ändrar UPN visas ett nytt konto med det nya UPN som visas i Microsoft Authenticator-appen, medan kontot med det gamla UPN fortfarande visas. Dessutom visas det gamla UPN i avsnittet enhets registrering på app-inställningarna. Det finns ingen förändring i den normala funktionen för enhets registrering eller beroende scenarier.

**Korrigera** <br> Om du vill ta bort alla referenser till det gamla UPN i Microsoft Authenticator-appen, instruerar du användaren att manuellt ta bort de gamla och nya kontona från Microsoft Authenticator, registrera om för MFA och ansluta enheten igen.

### <a name="phone-sign-in"></a>Telefonin loggning

Med telefonin loggning kan användare logga in på Azure AD utan lösen ord. Om du vill aktivera telefonin loggning måste användaren registrera sig för MFA med hjälp av Authenticator-appen och sedan aktivera telefonin loggning direkt på autentiseraren. Som en del av konfigurationen registreras enheten med Azure AD.

**Kända problem** <br>
Användarna kan inte använda telefonin loggning eftersom de inte får några meddelanden. Om användaren trycker på Sök efter meddelanden får de ett fel meddelande.

**Korrigera**<br>
Användaren måste välja den nedrullningsbara menyn för det konto som har Aktiver ATS för telefonin loggning och välja Inaktivera telefonin loggning. Om du vill kan du aktivera telefonin loggning igen.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Kända problem och lösningar för säkerhets nyckel (FIDO2)

**Kända problem** <br>
När flera användare registreras på samma nyckel, visar inloggnings skärmen en konto val sida där det gamla UPN visas. Inloggningar med säkerhets nycklar påverkas inte av UPN-ändringar.  

**Korrigera**<br>
Om du vill ta bort referenser till gamla UPN måste användarna [återställa säkerhets nyckeln och registrera den igen](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Kända problem och lösningar i OneDrive

OneDrive-användare är kända för att få problem efter ändringar i UPN. Mer information finns i [hur UPN-ändringar påverkar OneDrive-URL och OneDrive-funktioner](https://docs.microsoft.com/onedrive/upn-changes).

## <a name="next-steps"></a>Nästa steg

Se följande resurser:
* [Azure AD Connect: utforma begrepp](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Ifyllnad av UserPrincipalName för Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft Identity Platform ID-token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
