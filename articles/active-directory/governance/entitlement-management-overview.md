---
title: Vad är berättigandehantering? – Azure AD
description: Få en översikt över Azure Active Directory hantering av rättigheter och hur du kan använda den för att hantera åtkomst till grupper, program och SharePoint Online-webbplatser för interna och externa användare.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f4ce292ad507eb0208633db7743b881508a8e58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144439"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Vad är berättigandehantering i Azure AD?

Azure Active Directory (Azure AD) hantering av rättigheter är en funktion för [identitets styrning](identity-governance-overview.md) som gör det möjligt för organisationer att hantera identitets-och åtkomst livs cykeln i stor skala, genom att automatisera åtkomst till begär ande arbets flöden, åtkomst tilldelningar, recensioner och förfallo datum.

Anställda i organisationer behöver åtkomst till olika grupper, program och webbplatser för att utföra sitt arbete. Att hantera den här åtkomsten är utmanande, som krav ändring – nya program läggs till eller användare behöver ytterligare åtkomst behörighet.  Det här scenariot blir mer komplicerat när du samarbetar med externa organisationer. du kanske inte vet vem i den andra organisationen som behöver åtkomst till organisationens resurser, och de vet inte vilka program, grupper eller webbplatser som organisationen använder.

Hantering av Azure AD-behörighet kan hjälpa dig att effektivt hantera åtkomst till grupper, program och SharePoint Online-webbplatser för interna användare och även för användare utanför organisationen som behöver åtkomst till dessa resurser.

## <a name="why-use-entitlement-management"></a>Varför ska jag använda hantering av rättigheter?

Företags organisationer är ofta inriktade på utmaningar när de hanterar personal åtkomst till resurser som:

- Användare kanske inte vet vilken åtkomst de ska ha, och även om de gör det kan de ha svårt att hitta rätt personer för att godkänna deras åtkomst
- När användarna hittar och får åtkomst till en resurs, kan de vänta på att få åtkomst längre än vad som krävs i affärs syfte

Dessa problem är sammansatta för användare som behöver åtkomst från en annan organisation, t. ex. externa användare som är från att tillhandahålla kedjas organisationer eller andra affärs partner. Ett exempel:

- Ingen person vet att alla enskilda personer i andra organisations kataloger kan bjuda in dem
- Även om de kunde bjuda in dessa användare kan ingen i organisationen komma ihåg att hantera alla användares åtkomst konsekvent

Hantering av Azure AD-berättigande kan hjälpa dig att lösa dessa utmaningar.  Om du vill veta mer om hur kunder har använt hantering av Azure AD-hantering kan du läsa fallstudien om [Avanade](https://customers.microsoft.com/story/avanade-professional-services-azure-canada) och den [centrerade](https://customers.microsoft.com/story/757467-centrica-energy-azure)fallstudien.  Den här videon ger en översikt över hantering av rättigheter och dess värde:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Vad kan jag göra med hantering av rättigheter?

Här följer några funktioner för hantering av rättigheter:

- Delegera till icke-administratörer möjligheten att skapa åtkomst paket. Dessa åtkomst paket innehåller resurser som användarna kan begära, och de delegerade paket ansvariga kan definiera principer med regler för vilka användare kan begära, vem som måste godkänna åtkomsten och när åtkomsten upphör att gälla.
- Välj anslutna organisationer vars användare kan begära åtkomst.  När en användare som ännu inte befinner sig i din katalog begär åtkomst och har godkänts, bjuds de automatiskt in till din katalog och tilldelad åtkomst.  Om de inte har några andra paket tilldelningar för åtkomst upphör deras B2B-konto i din katalog automatiskt att tas bort.

Du kan komma igång med vår [självstudie för att skapa ditt första Access-paket](entitlement-management-access-package-first.md). Du kan också läsa [vanliga scenarier](entitlement-management-scenarios.md)eller titta på videor, inklusive

- [Så här distribuerar du hantering av Azure AD-rättigheter i din organisation](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Övervaka och skala din användning av hantering av Azure AD-rättigheter](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Så här delegerar du hantering av rättigheter](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Vad är åtkomst paket och vilka resurser kan jag hantera med dem?

Hantering av rättigheter ger Azure AD konceptet för ett *Access-paket*. Ett Access-paket är ett paket med alla resurser som har åtkomst till en användare som behöver arbeta med ett projekt eller utföra sina uppgifter. Åtkomst paket används för att styra åtkomsten till dina interna anställda och även användare utanför organisationen.

 Här följer de typer av resurser som du kan hantera användarens åtkomst till med hantering av rättigheter:

- Medlemskap i Azure AD-säkerhetsgrupper
- Medlemskap i Office 365-grupper och-team
- Tilldelning till Azure AD Enterprise-program, inklusive SaaS-program och anpassade integrerade program som stöder Federation/enkel inloggning och/eller etablering
- Medlemskap i SharePoint Online-webbplatser

Du kan också styra åtkomsten till andra resurser som förlitar sig på Azure AD-säkerhetsgrupper eller Office 365-grupper.  Ett exempel:

- Du kan ge användarna licenser för Microsoft Office 365 med hjälp av en Azure AD-säkerhetsgrupp i ett Access-paket och konfigurera [gruppbaserad licensiering](../users-groups-roles/licensing-groups-assign.md) för gruppen
- Du kan ge användarna åtkomst till att hantera Azure-resurser med hjälp av en Azure AD-säkerhetsgrupp i ett Access-paket och skapa en [Azure-roll tilldelning](../../role-based-access-control/role-assignments-portal.md) för gruppen

## <a name="how-do-i-control-who-gets-access"></a>Hur gör jag för att kontroll som får åtkomst?

Med ett Access-paket visar en administratör eller delegerad åtkomst paket hanterare resurserna (grupper, appar och platser) och de roller som användarna behöver för dessa resurser.

Åtkomst paket innehåller också en eller flera *principer*. En princip definierar regler eller guardrails för tilldelning för att komma åt paketet. Varje princip kan användas för att säkerställa att endast lämpliga användare kan begära åtkomst, att det finns god kännare för begäran och att deras åtkomst till dessa resurser är tidsbegränsad och upphör om den inte förnyas.

![Komma åt paket och principer](./media/entitlement-management-overview/elm-overview-access-package.png)

I varje princip definierar en administratör eller Access Package Manager

- Antingen redan befintliga användare (vanligt vis anställda eller redan inbjudna gäster) eller partner organisationer för externa användare som är berättigade att begära åtkomst
- Godkännande processen och användare som kan godkänna eller neka åtkomst
- Varaktigheten för en användares åtkomst tilldelning när den har godkänts innan tilldelningen upphör att gälla

I följande diagram visas ett exempel på de olika elementen i hantering av rättigheter. Den visar en katalog med två exempel på åtkomst paket.

- **Access paket 1** innehåller en enda grupp som en resurs. Åtkomst definieras med en princip som gör det möjligt för en uppsättning användare i katalogen att begära åtkomst.
- **Access paket 2** innehåller en grupp, ett program och en SharePoint Online-webbplats som resurser. Åtkomst definieras med två olika principer. Den första principen gör det möjligt för en uppsättning användare i katalogen att begära åtkomst. Den andra principen gör det möjligt för användare i en extern katalog att begära åtkomst.

![Översikt över rättighets hantering](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>När ska jag använda Access-paket?

Åtkomst paket ersätter inte andra mekanismer för åtkomst tilldelning.  De passar bäst i situationer som följande:

- Anställda behöver tidsbegränsad åtkomst för en viss uppgift.  Du kan till exempel använda gruppbaserad licensiering och en dynamisk grupp för att se till att alla anställda har en Exchange Online-postlåda och sedan använda åtkomst paket för situationer där anställda behöver ytterligare åtkomst, till exempel för att läsa avdelnings resurser från en annan avdelning.
- Åtkomst måste godkännas av en medarbetares chef eller andra utsedda individer.
- Avdelningar vill hantera sina egna åtkomst principer för sina resurser utan den inblandning.  
- Två eller flera organisationer samarbetar i ett projekt, och därför måste flera användare från en organisation tas i bruk via Azure AD B2B för att få åtkomst till en annan organisations resurser.

## <a name="how-do-i-delegate-access"></a>Hur gör jag för att delegera åtkomst?

 Åtkomst paket definieras i behållare som kallas *kataloger*.  Du kan ha en enda katalog för alla dina åtkomst paket, eller så kan du ange att enskilda personer ska kunna skapa och äga sina egna kataloger. En administratör kan lägga till resurser i valfri katalog, men en icke-administratör kan bara lägga till i en katalog de resurser som de äger. En katalog ägare kan lägga till andra användare som katalog medägare eller som åtkomst paket hanterare.  De här scenarierna beskrivs ytterligare i artikeln [delegering och roller i hantering av Azure AD-rättigheter](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Översikt över terminologi

För att bättre förstå hantering av rättigheter och dess dokumentation kan du gå tillbaka till följande lista över villkor.

| Period | Beskrivning |
| --- | --- |
| åtkomst paket | En samling resurser som ett team eller projekt behöver och som styrs av principer. Ett Access-paket finns alltid i en katalog. Du skapar ett nytt Access-paket för ett scenario där användare måste begära åtkomst.  |
| åtkomstbegäran | En begäran om åtkomst till resurserna i ett Access-paket. En begäran går vanligt vis igenom ett arbets flöde för godkännande.  Om det godkänns får användaren som begär en åtkomst paket tilldelning. |
| platstilldelning | En tilldelning av ett Access-paket till en användare ser till att användaren har alla resurs roller för det åtkomst paketet.  Access Package-tilldelningar har vanligt vis en tids gräns innan de upphör att gälla. |
| katalogen | En behållare för relaterade resurser och åtkomst paket.  Kataloger används för delegering, så att icke-administratörer kan skapa egna åtkomst paket. Katalog ägare kan lägga till resurser som de äger i en katalog. |
| Katalog skapare | En samling användare som har behörighet att skapa nya kataloger.  När en icke-administratörs användare som har behörighet att bli en katalog skapare skapar en ny katalog blir de automatiskt ägare till den katalogen. |
| ansluten organisation | En extern Azure AD-katalog eller domän som du har en relation med. Användare från en ansluten organisation kan anges i en princip som tillåts begära åtkomst. |
| policy | En uppsättning regler som definierar åtkomst livs cykeln, till exempel hur användare får åtkomst, vem som kan godkänna och hur länge användare har åtkomst via en tilldelning. En princip är länkad till ett Access-paket. Ett åtkomst paket kan till exempel ha två principer – en för anställda att begära åtkomst och en sekund för externa användare för att begära åtkomst. |
| resource | En till gång, till exempel en Office-grupp, en säkerhets grupp, ett program eller en SharePoint Online-webbplats, med en roll som en användare kan beviljas behörighet till. |
| resurs katalog | En katalog som har en eller flera resurser som ska delas. |
| resurs roll | En samling behörigheter som är kopplade till och som definieras av en resurs. En grupp har två roller – medlem och ägare. SharePoint-webbplatser har normalt tre roller, men kan ha ytterligare anpassade roller. Program kan ha anpassade roller. |


## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specialiserade moln, till exempel Azure Tyskland och Azure Kina, är för närvarande inte tillgängliga för användning.

### <a name="how-many-licenses-must-you-have"></a>Hur många licenser måste du ha?

Se till att katalogen har minst så många Azure AD Premium P2-licenser som du har:

- Medlems användare som **kan** begära ett åtkomst paket.
- Medlem och gäst användare som begär ett åtkomst paket.
- Medlem och gäst användare som godkänner begär Anden för ett Access-paket.
- Medlem och gäst användare som har en direkt tilldelning till ett Access-paket.

Azure AD Premium P2-licenser krävs **inte** för följande uppgifter:

- Inga licenser krävs för användare med rollen global administratör som konfigurerar de första katalogerna, åtkomst paket och principer, samt delegerar administrativa uppgifter till andra användare.
- Inga licenser krävs för användare som har delegerats administrativa uppgifter, t. ex. katalog skapare, katalog ägare och Access Package Manager.
- Det krävs inga licenser för gäster som **kan** begära åtkomst paket, men begär **inte** ett Access-paket.

För varje betalat Azure AD Premium P2-licens som du köper för medlems användare (anställda) kan du använda Azure AD B2B för att bjuda in upp till fem gäst användare. Dessa gäst användare kan också använda Azure AD Premium P2-funktioner. Mer information finns i [rikt linjer för Azure AD B2B-samarbets licensiering](../b2b/licensing-guidance.md).

Mer information om licenser finns i [tilldela eller ta bort licenser med hjälp av Azure Active Directory portalen](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exempel på licens scenarier

Här följer några exempel på licens scenarier som hjälper dig att fastställa antalet licenser som du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| En global administratör på Sparbanken skapar inledande kataloger och delegerar administrativa uppgifter till 6 andra användare. En av principerna anger att **alla anställda** (2 000 anställda) kan begära en speciell uppsättning åtkomst paket. 150 anställda begär åtkomst paketen. | 2 000 anställda som **kan** begära åtkomst paket | 2 000 |
| En global administratör på Sparbanken skapar inledande kataloger och delegerar administrativa uppgifter till 6 andra användare. En av principerna anger att **alla anställda** (2 000 anställda) kan begära en speciell uppsättning åtkomst paket. En annan princip anger att vissa användare från **användare från partner contoso** (gäster) kan begära samma åtkomst paket som omfattas av godkännande. Contoso har 30 000 användare. 150 anställda begär åtkomst paketen och 10 500-användare från contoso begär åtkomst. | 2 000 anställda + 500 gäst användare från Contoso som överstiger 1:5-förhållandet (10 500-(2 000 * 5)) | 2 500 |

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa ditt första Access-paket](entitlement-management-access-package-first.md)
- [Vanliga scenarier](entitlement-management-scenarios.md)
