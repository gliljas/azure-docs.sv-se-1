---
title: 'Självstudie: Azure Active Directory integrering med Kantega SSO för BitBucket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099212"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Självstudie: Azure Active Directory integrering med Kantega SSO för BitBucket

I den här självstudien får du lära dig att integrera Kantega SSO för BitBucket med Azure Active Directory (Azure AD).
Genom att integrera Kantega SSO för BitBucket med Azure AD får du följande fördelar:

* Du kan styra Azure AD som har åtkomst till Kantega SSO för Bitbucket.
* Du kan göra det möjligt för användarna att logga in automatiskt till Kantega SSO för BitBucket (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Kantega SSO för BitBucket behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Kantega SSO för BitBucket enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Kantega SSO för BitBucket stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Lägga till Kantega SSO för BitBucket från galleriet

Om du vill konfigurera integrationen av Kantega SSO för BitBucket i Azure AD måste du lägga till Kantega SSO för BitBucket från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kantega SSO för BitBucket från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **KANTEGA SSO för BitBucket**, väljer **Kantega SSO för BitBucket** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

    ![Kantega SSO för BitBucket i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Kantega SSO för BitBucket baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i Kantega SSO för BitBucket upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Kantega SSO för BitBucket måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera KANTEGA SSO för BitBucket enkel inloggning](#configure-kantega-sso-for-bitbucket-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa KANTEGA SSO för BitBucket test User](#create-kantega-sso-for-bitbucket-test-user)** – om du vill ha en motsvarighet till Britta Simon i Kantega SSO för BitBucket som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning för Azure AD med Kantega SSO för BitBucket:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Kantega SSO för BitBucket** program integration väljer du **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning för Kantega SSO för BitBucket-domän och URL: er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för Kantega SSO för BitBucket-domän och URL: er](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av BitBucket-plugin-programmet som beskrivs senare i självstudien.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera KANTEGA SSO för BitBucket** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Konfigurera Kantega SSO för enkel inloggning med BitBucket

1. Logga in på din BitBucket-administratörs portal som administratör i ett annat webbläsarfönster.

1. Klicka på kugg hjuls och klicka på **Sök efter nya tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Sök i **KANTEGA SSO för BITBUCKET SAML & Kerberos** och klicka på knappen **Installera** för att installera det nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Plugin-installationen startar.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. I **SAML** -avsnittet. Välj **Azure Active Directory (Azure AD)** i list rutan **Lägg till identitets leverantör** .

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Välj prenumerations nivå som **Basic**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. I avsnittet **app Properties** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopiera **app-ID-URI** -värdet och Använd det som **identifierare, svars-URL och INLOGGNINGs-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    b. Klicka på **Nästa**.

1. I avsnittet **metadata-import** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Välj **metadatafil på den här datorn**och ladda upp metadatafilen, som du har laddat ned från Azure Portal.

    b. Klicka på **Nästa**.

1. I avsnittet **namn och SSO-plats** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Lägg till namnet på identitets leverantören i text rutan för **identitets leverantörs namn** (t. ex. Azure AD).

    b. Klicka på **Nästa**.

1. Verifiera signerings certifikatet och klicka på **Nästa**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Utför följande steg i avsnittet **BitBucket User Accounts** :

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Välj **skapa användare i BitBucket interna katalog om det behövs** och ange rätt namn på gruppen för användare (kan vara flera). av grupper åtskilda med kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Utför följande steg på avsnittet **kända domäner för Azure AD** :

    ![Konfigurera enkel inloggning](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Välj **kända domäner** i den vänstra panelen på sidan.

    b. Ange domän namn i text rutan för **kända domäner** .

    c. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Kantega SSO för Bitbucket.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Kantega SSO för BitBucket**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **KANTEGA SSO för BitBucket**.

    ![Länken Kantega SSO för BitBucket i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Skapa Kantega SSO för BitBucket test User

Om du vill att Azure AD-användare ska kunna logga in på BitBucket måste de tillhandahållas i Bitbucket. I händelse av Kantega SSO för BitBucket är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din BitBucket-företags webbplats som administratör.

1. Klicka på inställnings ikonen.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. Under fliken **Administration** klickar du på **användare**.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klicka på **skapa användare**.

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Utför följande steg på dialog sidan **skapa användare** :

    ![Lägga till medarbetare](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Skriv e-postadressen för användaren i textrutan **Användarnamn** som Brittasimon@contoso.com.

    b. Skriv det fullständiga namnet för användaren, t.ex. Britta Simon, i textrutan **Fullständigt namn**.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress som Brittasimon@contoso.com.

    d. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    e. Ange lösen ordet för användaren i text rutan **Bekräfta lösen ord** .

    f. Klicka på **skapa användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Kantega SSO för BitBucket på åtkomst panelen, bör du loggas in automatiskt på Kantega SSO för BitBucket som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

