---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med EasySSO för JIRA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EasySSO för Jira.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f50fb15f-db09-4a36-b89d-0f5444e2ddca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3226ef8d739df6902a96cff336762ce4425c5de
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740390"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med EasySSO för JIRA

I den här självstudien får du lära dig hur du integrerar EasySSO för JIRA med Azure Active Directory (Azure AD). När du integrerar EasySSO för JIRA med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EasySSO för Jira.
* Gör det möjligt för användarna att logga in automatiskt till EasySSO för JIRA med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EasySSO för JIRA-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EasySSO för JIRA stöder **SP-och IDP** -INITIERAd SSO
* EasySSO för JIRA stöder **just-in-Time** User-etablering
* När du har konfigurerat EasySSO för JIRA kan du framtvinga kontroll av sessionen, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-jira-from-the-gallery"></a>Lägga till EasySSO för JIRA från galleriet

Om du vill konfigurera integreringen av EasySSO för JIRA i Azure AD måste du lägga till EasySSO för JIRA från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **EasySSO för JIRA** i sökrutan.
1. Välj **EasySSO för JIRA** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>Konfigurera och testa enkel inloggning med Azure AD för EasySSO för JIRA

Konfigurera och testa Azure AD SSO med EasySSO för JIRA med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EasySSO för Jira.

Om du vill konfigurera och testa Azure AD SSO med EasySSO för JIRA slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EasySSO för JIRA SSO](#configure-easysso-for-jira-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa EasySSO för JIRA test User](#create-easysso-for-jira-test-user)** -om du vill ha en motsvarighet till B. Simon i EasySSO för JIRA som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **EasySSO för JIRA** . Leta reda på avsnittet **Hantera** och välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:`https://<server-base-url>/jirasso/login.jsp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [EasySSO för JIRA-klientens support team](mailto:support@techtime.co.nz) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EasySSO för JIRA-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig EasySSO för JIRA-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  |  Källattribut|
    | ---------------| --------------- | --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | | user.userprincipalname |
    | urn: OID: 2.16.840.1.113730.3.1.241 | | user.displayname |
    | urn:oid:2.5.4.4 | | user.surname |
    | urn:oid:2.5.4.42 | | user.givenname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EasySSO för Jira.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **EasySSO för JIRA**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-easysso-for-jira-sso"></a>Konfigurera EasySSO för JIRA SSO

1. Logga in på Atlassian JIRA-instansen med administratörs behörighet och navigera till avsnittet **Hantera appar** .

    ![Hantera appar](./media/easysso-for-jira-tutorial/jira-admin-1.png)

1. Klicka på **EasySSO**.

    ![Enkel inloggning](./media/easysso-for-jira-tutorial/jira-admin-2.png)

1. Välj **SAML** -alternativ. Detta tar dig till avsnittet om SAML-konfiguration.

    ![SAML](./media/easysso-for-jira-tutorial/jira-admin-3.png)

1. Fliken Välj **certifikat** överst och du kommer att visas på följande skärm och letar reda på **certifikat (base64)** eller **metadatafilen** som du har sparat i de tidigare stegen i **Azure AD SSO** -konfigurationen. Du har följande alternativ för hur du går vidare:

    ![Metadata-URL](./media/easysso-for-jira-tutorial/jira-admin-4.png)

    a. Använd den app Federation- **metadatafil** som du laddade ned till en lokal fil på din dator. Välj **Ladda upp** alternativ knapp och följ dialog rutan Ladda upp fil som är unik för ditt operativ system

    **ELLER**

    b. Öppna appens Federations **ETA data** för att se innehållet (i valfri text redigerare) i filen och kopiera den till Urklipp. Välj **inmatat** alternativ och klistra in innehåll i Urklipp i textfältet.

    **ELLER**

    c. Helt manuell konfiguration. Öppna appens Federations **certifikat (base64)** om du vill se innehållet (i valfri text redigerare) i filen och kopiera det till Urklipp. Klistra in det i textfältet **IDP token Signature certificates** . Gå sedan till fliken **Allmänt** och fyll **i bindnings-URL** och **entitets-ID** med respektive värden för **inloggnings-URL** och **Azure AD-identifierare** som du sparade tidigare.

1. Klicka på knappen **Spara** längst ned på sidan. Du kan se innehållet i metadata eller certifikatfiler parsas i konfigurations fälten. EasySSO för JIRA-konfigurationen har slutförts.

1. För bästa test upplevelse går du till fliken **titta & känsla** och markerar knappen för **SAML-inloggning** på. Detta aktiverar separat knapp på JIRA-inloggnings skärmen för att testa att Azure AD SAML-integration slutar till slutet. Du kan lämna den här knappen och konfigurera dess placering, färg och översättning för produktions läget.

    ![Titta & känsla](./media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Kontakta [EasySSO support team](mailto:support@techtime.co.nz)om du har några problem.

### <a name="create-easysso-for-jira-test-user"></a>Skapa EasySSO för JIRA test användare

I det här avsnittet skapas en användare som heter B. Simon i Jira. EasySSO för JIRA har stöd för just-in-Time User-etablering, som är **inaktive rad** som standard. Om du vill aktivera användar etablering måste du markera kryss rutan **skapa användare på ett lyckat inloggnings** alternativ i avsnittet Allmänt i konfiguration av EasySSO-plugin-programmet. Om en användare inte redan finns i JIRA skapas en ny efter autentiseringen.

Men om du inte vill aktivera automatisk användar etablering för användaren första inloggningen måste användarna finnas i backend-användargrupper som JIRA-instansen använder, till exempel LDAP eller Atlassian Fully.

![Användaretablering](./media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen EasySSO för JIRA på åtkomst panelen, bör du loggas in automatiskt på EasySSO för JIRA som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa EasySSO för JIRA med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du EasySSO för JIRA med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
