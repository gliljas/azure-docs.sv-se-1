---
title: Hantera medgivande till program och utvärdera medgivande begär Anden – Azure AD
description: Lär dig hur du hanterar medgivande begär anden när användar tillstånd är inaktiverat eller begränsat och hur du utvärderar en begäran om klient organisations administratörs medgivande till ett program.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/27/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0451fe18629a572c9b49f14924bfa50293f42a2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77367846"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Hantera medgivande till program och utvärdera medgivande begär Anden

Microsoft [rekommenderar](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#restrict-user-consent-operations) att du inaktiverar slut användar medgivande till program. Detta kommer att centralisera processen för besluts fattande med din organisations administratörs-och identitets administratörs grupp.

När slut användar medgivande är inaktiverat eller begränsat finns det flera viktiga överväganden för att se till att din organisation förblir säker samtidigt som du fortfarande tillåter att affärs kritiska program används. De här stegen är viktiga för att minimera påverkan på organisationens support team och IT-administratörer, samtidigt som du förhindrar användning av ohanterade konton i program från tredje part.

## <a name="process-changes-and-education"></a>Bearbeta ändringar och utbildning

 1. Överväg att aktivera [arbets flödet för administratörs medgivande (för hands version)](configure-admin-consent-workflow.md) så att användarna kan begära administratörs godkännande direkt från medgivande skärmen.

 2. Se till att alla administratörer förstår [behörigheterna och medgivande ramverket](../develop/consent-framework.md), hur [medgivande frågan](../develop/application-consent-experience.md) fungerar och hur du [utvärderar en begäran om administratörs medgivande för hela klienten](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Granska organisationens befintliga processer för användare för att begära administratörs godkännande för ett program och gör uppdateringar om det behövs. Om processer ändras:
    * Uppdatera relevant dokumentation, övervakning, automatisering och så vidare.
    * Förmedla process ändringar till alla berörda användare, utvecklare, support team och IT-administratörer.

## <a name="auditing-and-monitoring"></a>Granskning och övervakning

1. [Granska appar och beviljade behörigheter](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#audit-apps-and-consented-permissions) i din organisation för att säkerställa att inga oberättigade eller misstänkta program tidigare har beviljats åtkomst till data.

2. Granska [identifiera och åtgärda illegala medgivande bidrag i Office 365](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) för ytterligare bästa praxis och skydd mot misstänkta program som begär OAuth-medgivande.

3. Om din organisation har lämplig licens:

    * Använd ytterligare [gransknings funktioner för OAuth-program i Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth).
    * Använd [Azure Monitor arbets böcker för att övervaka behörigheter och medgivande](../reports-monitoring/howto-use-azure-monitor-workbooks.md) relaterad aktivitet. I arbets boken med *medgivande insikter* får du en vy över appar efter antal misslyckade medgivande begär Anden. Detta kan vara till hjälp för att prioritera program som administratörer kan granska och bestämma om de vill ge dem administratörs medgivande.

### <a name="additional-considerations-for-reducing-friction"></a>Ytterligare överväganden för att minska friktionen

För att minimera påverkan på betrodda, verksamhets kritiska program som redan används, kan du proaktivt bevilja administratörs medgivande till program som har ett stort antal användar medgivande bidrag:

1. Ta en inventering av appar som redan har lagts till i din organisation med hög användning, baserat på inloggnings loggar eller aktiviteten medgivande beviljande. Ett PowerShell- [skript](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) kan användas för att snabbt och enkelt upptäcka program med ett stort antal användar medgivande bidrag.

2. Utvärdera de viktigaste programmen som ännu inte har beviljats administratörs medgivande.

   > [!IMPORTANT]
   > Utvärdera noggrant ett program innan du beviljar administrativt medgivande, även om många användare i organisationen redan har samtyckt till sig själva.

3. För varje program som godkänns beviljar du klient organisationens administratörs medgivande med någon av metoderna som beskrivs nedan.

4. Överväg att [begränsa användar åtkomsten](configure-user-consent.md)för varje godkänt program.

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Utvärdering av en begäran om administratörs medgivande för hela klienten

Att bevilja administratörs tillåtelse för hela klienten är en känslig åtgärd.  Behörigheter beviljas för hela organisationens räkning och kan omfatta behörigheter för att försöka utföra mycket privilegierade åtgärder. Till exempel roll hantering, fullständig åtkomst till alla post lådor eller alla platser och fullständig användar personifiering.

Innan du beviljar administrativt medgivande måste du se till att du litar på programmet och program utgivaren för den åtkomst nivå som du beviljar. Om du inte är säker på att du förstår vem som kontrollerar programmet och varför programmet begär behörigheter, *ska du inte bevilja medgivande*.

I följande lista finns några rekommendationer att tänka på när du utvärderar en begäran om att bevilja administrativt medgivande.

* **Förstå [behörighets-och medgivande ramverket](../develop/consent-framework.md) i Microsoft Identity Platform.**

* **Förstå skillnaden mellan [delegerade behörigheter och program behörigheter](../develop/v2-permissions-and-consent.md#permission-types).**

   Med program behörigheter får programmet åtkomst till data för hela organisationen, utan användar interaktion. Delegerade behörigheter gör det möjligt för programmet att agera för en användares räkning på en annan plats har loggat in i programmet.

* **Förstå de behörigheter som begärs.**

   Behörigheterna som begärs av programmet visas i [frågan om medgivande](../develop/application-consent-experience.md). Om behörighets rubriken expanderas visas behörighetens beskrivning. Beskrivningen för program behörigheter upphör vanligt vis i "utan en inloggad användare". Beskrivningen för delegerade behörigheter upphör vanligt vis med "för den inloggade användarens räkning." Behörigheter för Microsoft Graph API beskrivs i [Microsoft Graph referens referens] – Se dokumentationen för andra API: er för att förstå vilka behörigheter de visar.

   Om du inte förstår att en behörighet begärs ska du *inte bevilja medgivande*.

* **Förstå vilket program som begär behörigheter och vem som har publicerat programmet.**

   Försiktig skadliga program försöker titta på samma sätt som andra program.

   Om du tvekar giltighet för ett program eller dess utgivare ska du *inte bevilja medgivande*. I stället kan du söka ytterligare en bekräftelse (till exempel direkt från program utgivaren).

* **Se till att de begärda behörigheterna är justerade med de funktioner som du förväntar dig av programmet.**

   Till exempel kan ett program som erbjuder hantering av SharePoint-plats kräva delegerad åtkomst för att läsa alla webbplats samlingar, men inte nödvändigt vis ha fullständig åtkomst till alla post lådor eller fullständiga personifieringsparametrar i katalogen.

   Om du misstänker att programmet begär fler behörigheter än det behöver ska *du inte bevilja medgivande*. Kontakta program utgivaren om du vill ha mer information.

## <a name="granting-consent-as-an-administrator"></a>Bevilja medgivande som administratör

### <a name="granting-tenant-wide-admin-consent"></a>Bevilja administratörs medgivande för hela klienten

Se [bevilja klient organisations medgivande till ett program](grant-admin-consent.md) med stegvisa instruktioner för att bevilja administratörs behörighet för hela klienten från Azure Portal, med hjälp av Azure AD PowerShell eller från själva frågan om medgivande.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Bevilja medgivande för en speciell användares räkning

I stället för att bevilja medgivande för hela organisationen kan en administratör också använda [Microsft-Graph API](https://docs.microsoft.com/graph/use-the-api) för att bevilja medgivande till delegerade behörigheter för en enskild användares räkning. Mer information finns i [få åtkomst för en användares räkning](https://docs.microsoft.com/graph/auth-v2-user).

## <a name="limiting-user-access-to-applications"></a>Begränsa användar åtkomsten till program

Användares åtkomst till program kan fortfarande begränsas även när klient organisationens administratörs medgivande har beviljats. Mer information om hur du kräver användar tilldelning till ett program finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

Mer en bredare översikt, inklusive hur du hanterar ytterligare komplexa scenarier, finns i [använda Azure AD för program åtkomst hantering](what-is-access-management.md).

## <a name="next-steps"></a>Nästa steg

[Fem steg för att skydda din identitets infrastruktur](https://docs.microsoft.com/azure/security/fundamentals/steps-secure-identity#before-you-begin-protect-privileged-accounts-with-mfa)

[Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)

[Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)

[Behörigheter och medgivande i Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)