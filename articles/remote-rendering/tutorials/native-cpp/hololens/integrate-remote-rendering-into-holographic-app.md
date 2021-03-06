---
title: Integrera fjärrrendering i en C++/DirectX11 Holographic-app
description: Förklarar hur du integrerar fjärrrendering i en vanlig C++/DirectX11 Holographic-app som skapats med Visual Studio Project-guiden
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: 9db32912e86079875ad382fb23e521c720fc7fbd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83776887"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>Självstudie: integrera fjärrrendering i en HoloLens Holographic-app

I den här självstudien lär du dig:

> [!div class="checklist"]
>
> * Använda Visual Studio för att skapa en Holographic-app som kan distribueras till HoloLens
> * Lägg till nödvändiga kodfragment och projekt inställningar för att kombinera lokal åter givning med fjärrrenderat innehåll

Den här självstudien fokuserar på att lägga till nödvändiga bitar i ett inbyggt `Holographic App` exempel för att kombinera lokal åter givning med Azure Remote rendering. Den enda typen av status kommentarer i den här appen går via panelen för fel söknings utdata i Visual Studio, så vi rekommenderar att du startar exemplet inifrån Visual Studio. Att lägga till korrekt feedback i appen ligger utanför omfånget för det här exemplet, eftersom att skapa en dynamisk text panel från grunden innebär mycket kod. En start punkt är en klass `StatusDisplay` som är en del av [exempel projektet för fjärr styrning på GitHub](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content). Den förordnade versionen av den här själv studie kursen använder i själva verket en lokal kopia av klassen.

> [!TIP]
> [Databasen arr samples](https://github.com/Azure/azure-remote-rendering) innehåller resultatet av den här självstudien som ett Visual Studio-projekt som är klart att använda. Det är också berikat med korrekt fel-och status rapportering via användar gränssnitts klass `StatusDisplay` . I självstudien är alla arr-speciella tillägg begränsade `#ifdef USE_REMOTE_RENDERING`  /  `#endif` till, så det är enkelt att identifiera tillägg för fjärrrendering.

## <a name="prerequisites"></a>Krav

För den här självstudien behöver du:

* Din konto information (konto-ID, konto nyckel, prenumerations-ID). [Skapa ett konto](../../../how-tos/create-an-account.md)om du inte har något konto.
* Windows SDK 10.0.18362.0 [(Hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk).
* Den senaste versionen av Visual Studio 2019 [(Hämta)](https://visualstudio.microsoft.com/vs/older-downloads/).
* App-mallarna för Windows Mixed Reality for Visual Studio [(Hämta)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX).

## <a name="create-a-new-holographic-app-sample"></a>Skapa ett nytt Holographic app-exempel

Som ett första steg skapar vi ett aktie exempel som utgör grunden för integrering av fjärrrendering. Öppna Visual Studio och välj "skapa ett nytt projekt" och Sök efter "Holographic DirectX 11 app (Universal Windows) (C++/WinRT)"

![Skapa nytt projekt](media/new-project-wizard.png)

Ange ett valfritt projekt namn, Välj en sökväg och välj knappen "skapa".
I det nya projektet växlar du konfigurationen till **"debug/arm64"**. Nu bör du kunna kompilera och distribuera den till en ansluten HoloLens 2-enhet. Om du kör det på HoloLens bör du se en roterande kub framför dig.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>Lägg till fjärrstyrda beroenden via NuGet

Det första steget i att lägga till funktioner för fjärrrendering är att lägga till beroenden på klient sidan. Relevanta beroenden är tillgängliga som ett NuGet-paket.
I Solution Explorer högerklickar du på projektet och väljer **Hantera NuGet-paket...** på snabb menyn.

I dialog rutan uppmanas Bläddra efter NuGet-paketet med namnet **"Microsoft. Azure. RemoteRendering. cpp"**:

![Bläddra efter NuGet-paket](media/add-nuget.png)

och Lägg till det i projektet genom att välja paketet och sedan trycka på knappen Installera.

NuGet-paketet lägger till fjärrrendering-beroenden i projektet. Mer specifikt:
* Länka till klient biblioteket (RemoteRenderingClient. lib).
* Konfigurera. dll-beroenden.
* Ange rätt sökväg till katalogen include.

## <a name="project-preparation"></a>Projekt förberedelse

Vi behöver göra små ändringar i det befintliga projektet. Dessa ändringar är diskreta, men utan att de fjärrrenderar fungerar.

### <a name="enable-multithread-protection-on-directx-device"></a>Aktivera flertrådstestning-skydd på DirectX-enhet
`DirectX11`Flertrådstestning-skyddet måste vara aktiverat på enheten. Ändra detta genom att öppna filen DeviceResources. cpp i mappen "common" och infoga följande kod i slutet av-funktionen `DeviceResources::CreateDeviceResources()` :

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>Aktivera nätverksfunktioner i app-manifestet
Nätverksfunktioner måste aktive ras explicit för den distribuerade appen. Om du inte konfigurerar, kommer anslutnings frågor att resultera i timeout. Aktivera genom att dubbelklicka på `package.appxmanifest` objektet i Solution Explorer. I nästa användar gränssnitt går du till fliken **funktioner** och väljer:
* Internet (klient & Server)
* Internet (Klient)

![Nätverksfunktioner](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Integrera fjärrrendering

Nu när projektet är för berett kan vi börja med koden. En väl ingångs punkt i programmet är klassen `HolographicAppMain` (File HolographicAppMain. h/cpp) eftersom den har alla nödvändiga hookar för initiering, avinitiering och åter givning.

### <a name="includes"></a>Inkluderar

Vi börjar med att lägga till nödvändiga inkluderingar. Lägg till följande inkludera i filen HolographicAppMain. h:

```cpp
#include <AzureRemoteRendering.h>
```

... och det här ytterligare `include` direktivet till filen HolographicAppMain. cpp:

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

För enkelhetens skull definierar vi följande namn områdes gen väg överst i filen HolographicAppMain. h, efter `include` direktivet:

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

Den här genvägen är användbar så vi behöver inte skriva ut hela namn området överallt, men kan fortfarande identifiera ARR-/regionsspecifika data strukturer. Vi kan naturligtvis också använda `using namespace...` direktivet.

### <a name="remote-rendering-initialization"></a>Initiering av fjärrrendering
 
Vi behöver lagra några objekt för sessionen osv. under programmets livs längd. Livs längden sammanfaller med programmets livs längd `HolographicAppMain` , så vi lägger till våra objekt som medlemmar i klassen `HolographicAppMain` . Nästa steg är att lägga till följande klass medlemmar i filen HolographicAppMain. h:

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

En bra plats för att utföra den faktiska implementeringen är konstruktören av klassen `HolographicAppMain` . Vi måste utföra tre typer av initiering där:
1. Initieringen vid ett tillfälle av systemet för fjärrrendering
1. Skapa klient Sidan
1. Skapa session

Vi gör allt det sekventiellt i konstruktorn. I verkliga användnings fall kan det dock vara lämpligt att utföra dessa steg separat.

Lägg till följande kod i början av konstruktorn i filen HolographicAppMain. cpp:

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
Inuti lokal funktion `SetNewSession` registrerar vi ett återanrop som utlöses när anslutnings statusen för den aktuella sessionen ändras. Vi omdirigerar det anropet till vår egen funktion `OnConnectionStatusChanged` . Vi deklarerar och implementerar det (och resten av status dator koden) i nästa stycke. Observera också att autentiseringsuppgifterna är hårdkodade i exemplet och måste fyllas i på plats ([konto-ID, konto nyckel](../../../how-tos/create-an-account.md#retrieve-the-account-information)och [domän](../../../reference/regions.md)).

Vi gör avinitieringen symmetriskt och i omvänd ordning i slutet av destruktorn-texten:

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>Tillstånds dator

I fjärrrendering är nyckel funktionerna för att skapa en session och för att läsa in en modell asynkrona funktioner. För att kunna göra detta behöver vi en enkel tillstånds dator som huvudsakligen övergår genom följande tillstånd automatiskt:

*Initiering – > skapande av session – inläsning av >s modell (med förlopp)*

I nästa steg lägger vi till en bit av tillstånds dator hantering i klassen. Vi deklarerar vår egen Fasttext `AppConnectionStatus` för de olika tillstånd som programmet kan ha. Det liknar `RR::ConnectionStatus` , men har ett ytterligare tillstånd för misslyckad anslutning.

Lägg till följande medlemmar och funktioner i klass deklarationen:

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

På implementerings sidan i. cpp-filen lägger du till följande funktions kroppar:

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
}
```

### <a name="per-frame-update"></a>Uppdatering per ram

Vi måste ticka klienten en gång per simulerings Tick. Klass `HolographicApp1Main` ger en fungerande Hook för uppdateringar per ram, så Lägg till följande kod i funktionen Body `HolographicApp1Main::Update` :

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>Rendering

Det sista du gör är att anropa åter givningen av fjärrinnehållet. Vi måste göra detta samtal i den exakta högra positionen i åter givnings pipelinen när åter givnings målet är klart. Infoga följande kodfragment i `UseHolographicCameraResources` funktionen lock inuti `HolographicAppMain::Render` :

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>Kör exemplet

Exemplet bör nu vara i ett tillstånd där det kompileras och körs.

När exemplet fungerar som det ska, visar det den roterande kuben precis framför dig, och när en session har skapats och inläsning av modellen återges motor modellen på den aktuella huvud platsen. Det kan ta upp till några minuter att skapa sessioner och att inläsning av modeller sker. Aktuell status skrivs bara till Visual Studios utdatapanelen. Vi rekommenderar därför att du startar exemplet inifrån Visual Studio.

> [!CAUTION]
> Klienten kopplar från servern när Ticket-funktionen inte anropas under några sekunder. Det är mycket enkelt att utlösa Bryt punkter, vilket gör att programmet kan koppla från.

För korrekt status visning med en text panel, se den för hands versionen av den här själv studie kursen på GitHub.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig alla steg som krävs för att lägga till fjärrrendering i ett **Holographic app** C++/DirectX11-exempel.
Om du vill konvertera din egen modell, se följande snabb start:

> [!div class="nextstepaction"]
> [Snabb start: konvertera en modell för åter givning](../../../quickstarts/convert-model.md)
