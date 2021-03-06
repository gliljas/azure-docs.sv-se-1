---
title: Snabb start för Microsoft Identity Platform Windows Desktop
description: Lär dig hur ett Windows Desktop .NET-program (XAML) kan hämta en åtkomsttoken och anropa ett API som skyddas av en Microsoft Identity Platform-slutpunkt
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e3674f7686679c27ad732fcaa92620703b91b5fc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82112617"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows-skrivbordsapp

I den här snabbstarten får du lära dig att skriva ett Windows-skrivbordsprogram med .NET (WPF) som kan logga in personliga konton och arbets- och skolkonton, hämta en åtkomsttoken samt anropa Microsoft Graph API. (Se [hur exemplet fungerar](#how-the-sample-works) för en illustration.)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till den nya [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till bladet [Appregistreringar](https://aka.ms/MobileAppReg) för Azure Active Directory i Azure Portal.
> 1. Välj **ny registrering**.
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `Win-App-calling-MsGraph`.
>      - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
>      - Välj **Registrera** för att skapa programmet.
> 1. I listan över sidor för appen väljer du **Autentisering**.
> 1. I avsnittet **omdirigerings-URI: er** | **föreslagna omdirigerings-URI: er för offentliga klienter (mobil, stationär)** använder **https://login.microsoftonline.com/common/oauth2/nativeclient**du.
> 1. Välj **Spara**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Steg 1: Konfigurera din app i Azure-portalen
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till en svars **https://login.microsoftonline.com/common/oauth2/nativeclient**-URL som.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-windows-desktop/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

> [!div renderon="docs"]
> [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: appen har kon figurer ATS och är redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper och är redo att köras.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt
> 1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
> 1. Öppna projektet i Visual Studio.
> 1. Redigera **App.Xaml.cs** och ersätt värdena för fälten `ClientId` och `Tenant` med följande kod:
>
>    ```csharp
>    private static string ClientId = "Enter_the_Application_Id_here";
>    private static string Tenant = "Enter_the_Tenant_Info_Here";
>    ```
>
> Där:
> - `Enter_the_Application_Id_here` – är **program-ID (klient)** för programmet som du har registrerat.
> - `Enter_the_Tenant_Info_Here` – är inställt på något av följande alternativ:
>   - Om ditt program stöder **Endast konton i den här organisationskatalogen** ska du ersätta värdet med **klient-ID** eller **klientnamn** (till exempel contoso.microsoft.com)
>   - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>   - Om ditt program stöder **Konton i en valfri organisationskatalog och personliga Microsoft-konton** ersätter du värdet med `common`
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

## <a name="more-information"></a>Mer information

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

### <a name="msalnet"></a>MSAL.NET
MSAL ([Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan installera MSAL genom att köra följande kommando i **Package Manager-konsolen** i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Initiera sedan MSAL med hjälp av följande kod:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Där: ||
> |---------|---------|
> | `ClientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder för hämtning av token: `AcquireTokenInteractive` och `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver tvingande användare att interagera med slut punkten för Microsoft Identity Platform via ett popup-fönster för att antingen verifiera sina autentiseringsuppgifter eller ge samtycke. Några exempel är:

- Första gången användaren loggar in på programmet
- När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- När programmet begär åtkomst till en resurs som användaren behöver ge sitt medgivande för
- När tvåfaktorsautentisering krävs

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Där:||
> |---------|---------|
> | `_scopes` | Innehåller de omfattningar som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API: er. |

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Du vill inte kräva att användarna verifierar sina autentiseringsuppgifter varje gång de behöver komma åt en resurs. I de flesta fall vill du ha hämtning och förnyelse av token utan någon användarinteraktion. Du kan använda metoden `AcquireTokenSilent` för att hämta token för att komma åt skyddade resurser efter den inledande metoden `AcquireTokenInteractive`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Där: ||
> |---------|---------|
> | `scopes` | Innehåller de omfattningar som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API: er. |
> | `firstAccount` | Anger den första användaren i cachen (MSAL stöder flera användare i en enda app). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Prova att använda självstudien om Windows-skrivbordet för en komplett stegvis guide till att skapa appar och nya funktioner, däribland en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [Självstudie om att anropa Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)
