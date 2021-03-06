---
title: Ge åtkomst till Azure App konfiguration med Azure Active Directory
description: Aktivera RBAC för att bevilja åtkomst till din Azure App konfigurations instans
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: c2812219e689cb42fd871f85300239a10ab0da0e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116727"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Ge åtkomst till Azure App konfiguration med Azure Active Directory
Förutom att använda hash-baserade Message Authentication Code (HMAC), kan Azure App konfiguration använda Azure Active Directory (Azure AD) för att auktorisera begär anden till konfigurations instanser för appar.  Med Azure AD kan du använda rollbaserad åtkomst kontroll (RBAC) för att bevilja behörighet till ett säkerhets objekt.  Ett säkerhets objekt kan vara en användare, en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) eller ett [huvud namn för program tjänsten](../active-directory/develop/app-objects-and-service-principals.md).  Mer information om roller och roll tilldelningar finns i [förstå olika roller](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
Begär Anden som görs av ett säkerhets objekt för att få åtkomst till en app Configuration-resurs måste vara auktoriserade. Med Azure AD är åtkomst till en resurs en två stegs process:
1. Säkerhets objektets identitet autentiseras och en OAuth 2,0-token returneras.  Resurs namnet för att begära en token är det `https://login.microsoftonline.com/{tenantID}` som `{tenantID}` matchar Azure Active Directory klient-ID som tjänstens huvud namn tillhör.
2. Token skickas som en del av en begäran till appens konfigurations tjänst för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning.  Om ett program körs i en Azure-entitet, till exempel en Azure Functions app, en Azure-webbapp eller en virtuell Azure-dator, kan den använda en hanterad identitet för att få åtkomst till resurserna.  Information om hur du autentiserar begär Anden som görs av en hanterad identitet för att Azure App konfiguration finns i [autentisera åtkomst till Azure App konfigurations resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](howto-integrate-azure-managed-service-identity.md).

Auktoriserings steget kräver att en eller flera RBAC-roller tilldelas säkerhets objekt. Azure App-konfigurationen tillhandahåller RBAC-roller som omfattar uppsättningar med behörigheter för konfigurations resurser för appar. Rollerna som tilldelas ett säkerhets objekt avgör vilka behörigheter som har angetts för huvud kontot. Mer information om RBAC-roller finns i [Inbyggda RBAC-roller för Azure App konfiguration](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/overview.md).

När en RBAC-roll tilldelas till ett säkerhets objekt i Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomsten är begränsad till appens konfigurations resurs. Ett säkerhets objekt i Azure AD kan vara en användare eller ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Inbyggda RBAC-roller för Azure App konfiguration
Azure tillhandahåller följande inbyggda RBAC-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:

- **Data ägare för app Configuration**: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.
- **Konfigurations data läsare för appar**: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.
- **Deltagare**: Använd den här rollen för att hantera appens konfigurations resurs. Även om konfigurations data för appar kan nås med hjälp av åtkomst nycklar ger den här rollen ingen direkt åtkomst till data med hjälp av Azure AD.
- **Läsare**: Använd den här rollen för att ge Läs behörighet till appens konfigurations resurs. Detta ger inte åtkomst till resursens åtkomst nycklar eller data som är lagrade i app-konfigurationen.

> [!NOTE]
> För närvarande stöder Azure Portal och CLI endast HMAC-autentisering för att komma åt konfigurations data för appar. Azure AD-autentisering stöds inte. Därför kräver användare av Azure Portal och CLI rollen *deltagare* för att hämta åtkomst nycklar för appens konfigurations resurs. Det finns ingen inverkan på åtkomst via portalen och CLI för att bevilja *konfigurations data för program konfigurations data läsare* eller *program konfigurations data* .

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du använder [hanterade identiteter](howto-integrate-azure-managed-service-identity.md) för att administrera konfigurations tjänsten för appar.
