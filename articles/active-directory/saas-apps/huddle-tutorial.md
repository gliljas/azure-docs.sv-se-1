---
title: 'Självstudie: Azure Active Directory integrering med Huddle | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1faf1c1fcdefb0c93d36c195f0cf44626a555f44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73158999"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Självstudie: Azure Active Directory integrering med Huddle

I den här självstudien lär du dig att integrera Huddle med Azure Active Directory (AD Azure).
När du integrerar Huddle med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till Huddle från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Huddle (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Huddle behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Huddle-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Huddle stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-huddle-from-the-gallery"></a>Lägga till Huddle från galleriet

För att konfigurera integreringen av Huddle i Azure AD måste du lägga till Huddle från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Huddle från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Huddle**, väljer **Huddle** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Huddle i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Huddle baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Huddle upprättas.

För att kunna konfigurera och testa Azure AD enkel inloggning med Huddle slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Huddle](#configure-huddle-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Huddle-testanvändare](#create-huddle-test-user)** – för att ha en motsvarighet för Britta Simon i Huddle som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Huddle:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Huddle** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    > [!NOTE]
    > Din instans av Huddle identifieras automatiskt från den domän som du anger nedan.

    ![Huddle-domän och webbadresser med information om enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL:

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. Skriv en URL i textrutan **Svars-URL**: 

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Huddle-domän och webbadresser med information om enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta det här värdet genom att kontakta [supportteamet för Huddle-klienten](https://huddle.zendesk.com).

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Huddle** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-huddle-single-sign-on"></a>Konfigurera enkel inloggning för Huddle

För att konfigurera enkel inloggning på **Huddle**-sidan behöver du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Huddle-supportteamet](https://huddle.zendesk.com/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!NOTE]
> Enkel inloggning måste aktiveras av Huddle-supportteamet. Du får ett meddelande när konfigurationen har slutförts.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Huddle.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Huddle**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Huddle**.

    ![Huddle-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-huddle-test-user"></a>Skapa Huddle-testanvändare

För att Azure AD-användare ska kunna logga in i Huddle måste de vara etablerade i Huddle. När det gäller Huddle är etablering en manuell aktivitet.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Huddle**-företagswebbplats som administratör.

2. Klicka på **Arbetsyta**.

3. Klicka på **Personer \>Bjud in personer**.

    ![People](./media/huddle-tutorial/ic787838.png "People")

4. I avsnittet **Skapa en ny inbjudan** utför du följande steg:
  
    ![Ny inbjudan](./media/huddle-tutorial/ic787839.png "Ny inbjudan")
  
    a. I listan för att **välja ett team för att bjuda in personer** väljer du **team**.

    b. Skriv **e-postadressen** till ett giltigt Azure AD-konto som du vill etablera textrutan **Enter email address for people you'd like to invite** (Ange e-postadress för personer du vill bjuda in).

    c. Klicka på **Bjud in**.

    > [!NOTE]
    > Azure AD-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för Huddle-kontoskapande som tillhandahålls av Huddle för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Huddle-panelen i åtkomstpanelen bör du automatiskt loggas in på Huddle som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

