---
title: Konfiguration med enkel inloggning (SSO) för ditt Azure Marketplace-program | Microsoft Docs
description: Steg för konfiguration med enkel klickning för ditt program från Azure Marketplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "67872432"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Konfiguration med ett klick för enkel inloggning

 I den här självstudien får du lära dig hur du utför en enkel inloggning (SSO) med enkel inloggning (SSO) för SAML-stöd, Azure Active Directory (Azure AD)-program från Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Introduktion till enkel klickning

Funktionen SSO med enkel klickning är utformad för att konfigurera enkel inloggning för Azure Marketplace-appar som stöder SAML-protokollet. På sidan konfiguration av Azure AD SSO kan du med det här alternativet Konfigurera Azure AD-metadata automatiskt på program sidan. På så sätt kan du snabbt konfigurera SSO med minimal manuell ansträngning.

## <a name="advantages-of-one-click-sso"></a>Fördelar med enkel klickning

- Snabb SSO-konfiguration av Azure Marketplace-program som kräver manuell konfiguration på program sidan.
- Mer effektiv och korrekt SSO-konfiguration.
- Ingen partner kommunikation eller support krävs för installationen. Programmet innehåller användar gränssnittet för SAML-konfigurationen.

## <a name="prerequisites"></a>Krav

- En aktiv prenumeration av programmet som ska konfigureras med SSO. Du måste också ha administratörs behörighet.
- **Mina appar säker inloggnings tillägg** från Microsoft installerat i webbläsaren. Mer information finns i [komma åt och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="one-click-sso-configuration-steps"></a>Konfigurations steg för SSO med enkel klickning

1. Lägg till programmet från Azure Marketplace.

2. Välj **enkel inloggning**.

3. Välj **aktivera enkel inloggning**.

4. Fyll i de obligatoriska konfigurations värdena i avsnittet **grundläggande SAML-konfiguration** .

    > [!NOTE]
    > Om programmet har anpassade anspråk som du måste konfigurera, kan du hantera dem innan du utför ett enkel klickning.

5. Om SSO-funktionen för enkel klickning är tillgänglig för ditt Azure Marketplace-program ser du följande skärm bild. Du kanske måste installera **webb läsar tillägget Mina appar med säker inloggning** genom att välja **installera tillägget**.

   ![Installera Mina appar säker inloggnings webb läsar tillägg](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. När du har lagt till tillägget i webbläsaren väljer du **installations \<programmets namn\>**. När du har omdirigerats till program administrations portalen loggar du in som administratör.

   ![Installations programmets namn](./media/one-click-sso-tutorial/setup-sso.png)

7. Webb läsar tillägget konfigurerar automatiskt SSO i programmet. Bekräfta genom att välja **Ja**.

   ![Spara de automatiskt ifyllda data](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Om SSO-konfigurationen för programmet kräver ytterligare steg, följer du anvisningarna för att utföra stegen.

8. När konfigurationen är färdig väljer du **OK** för att spara ändringarna.

   ![Spara de automatiskt ifyllda data](./media/one-click-sso-tutorial/save-data.png)

9. Ett bekräftelse fönster visar att du kan se att SSO-inställningarna har kon figurer ATS.

   ![SSO har kon figurer ATS](./media/one-click-sso-tutorial/sso-configured.png)

10. När konfigurationen är klar loggas du ut från programmet och återgår till Azure Portal.

11. Du kan välja **test** för att testa enkel inloggning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Vad är webb läsar tillägget Mina appar säker inloggning?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 
