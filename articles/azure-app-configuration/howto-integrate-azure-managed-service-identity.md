---
title: Autentisera med hjälp av Azure Managed Identities
titleSuffix: Azure App Configuration
description: Autentisera till Azure App konfiguration med hjälp av Azure Managed Identities
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 2/25/2020
ms.openlocfilehash: bf97a1eae758778efc8d800666af4a5fcb574429
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056829"
---
# <a name="integrate-with-azure-managed-identities"></a>Integrera med hanterade Azure-identiteter

Azure Active Directory [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) fören klar hemligheter-hanteringen för ditt moln program. Med en hanterad identitet kan din kod använda tjänstens huvud namn som skapats för den Azure-tjänst som den körs på. Du använder en hanterad identitet i stället för en separat autentiseringsuppgift som lagras i Azure Key Vault eller en lokal anslutnings sträng. 

Azure App konfiguration och dess .NET Core-, .NET Framework-och Java våren-klient bibliotek har hanterat identitets stöd inbyggt i dem. Även om du inte behöver använda den, eliminerar den hanterade identiteten behovet av en åtkomsttoken som innehåller hemligheter. Din kod kan komma åt appens konfigurations Arkiv enbart med tjänstens slut punkt. Du kan bädda in den här URL: en i koden direkt utan att exponera någon hemlighet.

Den här artikeln visar hur du kan dra nytta av den hanterade identiteten för att komma åt appens konfiguration. Den bygger på den webbapp som introducerades i snabbstarterna. Innan du fortsätter skapar du först [en ASP.net Core-app med app-konfigurationen](./quickstart-aspnet-core-app.md) .

Den här artikeln visar också hur du kan använda den hanterade identiteten tillsammans med appens konfigurations Key Vault referenser. Med en enda hanterad identitet kan du sömlöst komma åt båda hemligheterna från Key Vault och konfigurations värden från App-konfigurationen. Om du vill utforska den här funktionen kan du avsluta [använda Key Vault referenser med ASP.net Core](./use-key-vault-references-dotnet-core.md) först.

Du kan använda valfri kod redigerare för att utföra stegen i den här självstudien. [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, MacOS-och Linux-plattformarna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Bevilja en hanterad identitets åtkomst till app-konfigurationen.
> * Konfigurera appen så att den använder en hanterad identitet när du ansluter till app-konfigurationen.
> * Du kan också konfigurera appen så att den använder en hanterad identitet när du ansluter till Key Vault via en app-konfiguration Key Vault referens.

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna slutföra den här självstudiekursen:

* [.Net Core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud Shell kon figurer ATS](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Lägga till en hanterad identitet

Om du vill konfigurera en hanterad identitet i portalen skapar du först ett program och aktiverar sedan funktionen.

1. Skapa en App Services-instans i [Azure Portal](https://portal.azure.com) som du vanligt vis gör. Gå till den i portalen.

1. Rulla ned till **inställnings** gruppen i det vänstra fönstret och välj **identitet**.

1. Växla **status** till **på på** fliken **systemtilldelad** och välj **Spara**.

1. Svara **Ja** när du uppmanas att aktivera systemtilldelad hanterad identitet.

    ![Ange hanterad identitet i App Service](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Bevilja åtkomst till App Configuration

1. I [Azure Portal](https://portal.azure.com)väljer du **alla resurser** och väljer det konfigurations Arkiv för app som du skapade i snabb starten.

1. Välj **åtkomst kontroll (IAM)**.

1. På fliken **kontrol lera åtkomst** väljer du **Lägg till** i användar gränssnittet **Lägg till Roll tilldelnings** kort.

1. Välj **app Configuration Data Reader**under **roll**. Under **tilldela åtkomst till**väljer du **App Service** under **systemtilldelad hanterad identitet**.

1. Under **prenumeration**väljer du din Azure-prenumeration. Välj App Service resurs för din app.

1. Välj **Spara**.

    ![Lägga till en hanterad identitet](./media/add-managed-identity.png)

1. Valfritt: om du vill ge åtkomst till Key Vault också följer du anvisningarna i [tillhandahålla Key Vault autentisering med en hanterad identitet](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Använda en hanterad identitet

1. Lägg till en referens till *Azure. Identity* -paketet:

    ```cli
    dotnet add package Azure.Identity
    ```

1. Hitta slut punkten för konfigurations lagringen för appen. URL: en visas på fliken **åtkomst nycklar** för butiken i Azure Portal.

1. Öppna *appSettings. JSON*och Lägg till följande skript. Ersätt * \<service_endpoint>*, inklusive hakparenteser, med URL: en till appens konfigurations lager. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Öppna *program.cs*och Lägg till en referens till namn `Azure.Identity` områdena `Microsoft.Azure.Services.AppAuthentication` och:

    ```csharp-interactive
    using Azure.Identity;
    ```

1. Om du bara vill komma åt värden som lagras direkt i appens konfiguration uppdaterar `CreateWebHostBuilder` du metoden genom att `config.AddAzureAppConfiguration()` ersätta-metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder`ersätter `CreateWebHostBuilder` i .net Core 3,0.  Välj rätt syntax baserat på din miljö.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Uppdatera *program.cs* så som visas nedan om du vill använda både konfigurations värden för appen och Key Vault referenser. Den här koden skapar en `KeyVaultClient` ny med `AzureServiceTokenProvider` en och skickar den här referensen till ett `UseAzureKeyVault` anrop till metoden.

    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        var credentials = new ManagedIdentityCredential();

                        config.AddAzureAppConfiguration(options =>
                        {
                            options.Connect(new Uri(settings["AppConfig:Endpoint"]), credentials)
                                    .ConfigureKeyVault(kv =>
                                    {
                                        kv.SetCredential(credentials);
                                    });
                        });
                    })
                    .UseStartup<Startup>());
    ```
    ---

    Du kan nu komma åt Key Vault referenser precis som andra konfigurations nycklar för appar. Config-providern kommer att `KeyVaultClient` använda den som du konfigurerade för att autentisera till Key Vault och hämta värdet.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Distribuera från lokal Git

Det enklaste sättet att aktivera lokal Git-distribution för din app med kudu build-servern är att använda [Azure Cloud Shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivera lokal Git med Kudu
Om du inte har en lokal git-lagringsplats för din app måste du initiera en. Om du vill initiera en lokal git-lagringsplats kör du följande kommandon från appens projekt katalog:

```cmd
git init
git add .
git commit -m "Initial version"
```

Om du vill aktivera lokal Git-distribution för din app med kudu build- [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) servern kör du i Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Det här kommandot ger dig något liknande följande utdata:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Distribuera projektet

I det _lokala terminalfönstret_lägger du till en Azure-fjärrkontroll till din lokala git-lagringsplats. Ersätt _ \<URL>_ med URL: en för den git-fjärrdatabasen som du fick från [att aktivera lokal git med kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När du uppmanas att ange ett lösen ord anger du det lösen ord som du skapade i [Konfigurera en distributions användare](#configure-a-deployment-user). Använd inte det lösen ord du använder för att logga in på Azure Portal.

```bash
git push azure master
```

Du kan se körnings viss automatisering i utdata, till exempel MSBuild för ASP.NET, `npm install` för Node. js och `pip install` för python.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till din webbapp med hjälp av en webbläsare för att kontrol lera att innehållet har distribuerats.

```bash
http://<app_name>.azurewebsites.net
```

## <a name="use-managed-identity-in-other-languages"></a>Använd hanterad identitet på andra språk

App Configuration-providrar för .NET Framework och Java Spring har också inbyggt stöd för hanterad identitet. Du kan använda butikens URL-slutpunkt i stället för dess fullständiga anslutnings sträng när du konfigurerar en av dessa providers. 

Du kan till exempel uppdatera .NET Framework-konsolen som skapats i snabb starten för att ange följande inställningar i filen *app. config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lagt till en Azure-hanterad identitet för att effektivisera åtkomst till app-konfigurationen och förbättra hantering av autentiseringsuppgifter för din app. Om du vill veta mer om hur du använder app-konfiguration kan du fortsätta till Azure CLI-exemplen.

> [!div class="nextstepaction"]
> [CLI-exempel](./cli-samples.md)
