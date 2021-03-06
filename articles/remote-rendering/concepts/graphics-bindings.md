---
title: Grafik bindning
description: Installation av grafik bindningar och användnings fall
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 4854d5ff9d697a2bf082a788c0e761a2152b0294
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758715"
---
# <a name="graphics-binding"></a>Grafik bindning

För att kunna använda Azures fjärrrendering i ett anpassat program måste den integreras i programmets åter givnings pipeline. Den här integrationen är ansvaret för grafik bindningen.

När grafik bindningen har kon figurer ATS ger den till gång till olika funktioner som påverkar den åter givnings avbildningen. Dessa funktioner kan delas in i två kategorier: allmänna funktioner som alltid är tillgängliga och vissa funktioner som endast är relevanta för den valda `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Grafik bindning i Unity

I Unity hanteras hela bindningen av de strukturer som `RemoteUnityClientInit` skickas till `RemoteManagerUnity.InitializeManager` . Om du vill ställa in grafik läget måste `GraphicsApiType` fältet ställas in på den valda bindningen. Fältet fylls i automatiskt beroende på om en XRDevice finns. Beteendet kan åsidosättas manuellt med följande beteenden:

* **HoloLens 2**: grafik bindningen för [Windows Mixed Reality](#windows-mixed-reality) används alltid.
* **Flat UWP Desktop-app**: [simulering](#simulation) används alltid. Om du vill använda det här läget måste du följa stegen i [självstudien: Konfigurera ett Unity-projekt från grunden](../tutorials/unity/project-setup.md).
* **Unity Editor**: [simuleringen](#simulation) används alltid om inte ett WMR VR-headset är anslutet i vilket fall arr kommer att inaktive ras för att felsöka icke-arr-relaterade delar av programmet. Se även [Holographic fjärr kommunikation](../how-tos/unity/holographic-remoting.md).

Den enda andra relevanta delen för Unity har åtkomst till den [grundläggande bindningen](#access). alla andra avsnitt nedan kan hoppas över.

## <a name="graphics-binding-setup-in-custom-applications"></a>Grafik bindnings konfiguration i anpassade program

Om du vill välja en grafik bindning gör du följande två steg: först måste grafik bindningen vara statiskt initierad när programmet initieras:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

Anropet ovan krävs för att initiera Azures fjärrrendering i Holographic-API: erna. Den här funktionen måste anropas innan något Holographic-API anropas och innan andra API: er för fjärrrendering används. På samma sätt ska motsvarande avinitierings funktion `RemoteManagerStatic.ShutdownRemoteRendering();` anropas när inga Holographic-API: er anropas längre.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Åtkomst till grafik bindning

När en klient har kon figurer ATS kan den grundläggande grafik bindningen nås med get- `AzureSession.GraphicsBinding` metoden. Till exempel kan den senaste ram statistiken hämtas så här:

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafik-API: er

Det finns för närvarande två grafik-API: er som kan väljas `WmrD3D11` och `SimD3D11` . `Headless`Det finns ännu en tredjedel av klient sidan som inte stöds.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`är standard bindningen som ska köras på HoloLens 2. `GraphicsBindingWmrD3d11`Bindningen skapas. I det här läget är Azure-fjärrrendering hookar direkt i Holographic-API: erna.

För att komma åt de härledda grafik bindningarna måste basen `GraphicsBinding` omvandlas.
Det finns två saker som måste utföras för att använda WMR-bindningen:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Meddela fjärrrendering av det använda koordinatsystemet

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


Där ovanstående `ptr` måste vara en pekare till ett ursprungligt `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` objekt som definierar det världs utrymmes koordinatsystem där koordinaterna i API: et uttrycks i.

#### <a name="render-remote-image"></a>Återge fjärran sluten avbildning

I början av varje ram måste fjär ramen återges i den bakre bufferten. Detta görs genom att anropa `BlitRemoteFrame` , vilket fyller både färg-och djupgående information i det aktuella bindnings målet. Det är därför viktigt att detta görs när du har kopplat den bakre bufferten som ett Render-mål.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulering

`GraphicsApiType.SimD3D11`är simulerings bindningen och om den väljs skapas `GraphicsBindingSimD3d11` grafik bindningen. Det här gränssnittet används för att simulera huvud förflyttning, till exempel i ett Skriv bords program och återger en monoscopic-avbildning.
Installationen är lite mer engagerad och fungerar på följande sätt:

#### <a name="create-proxy-render-target"></a>Skapa Proxy för rendering mål

Fjärrinnehållet och det lokala innehållet måste återges för att rendera målet för färg-och djupet som heter proxy med hjälp av proxy-kamerans data som tillhandahålls av `GraphicsBindingSimD3d11.Update` funktionen. Proxyservern måste matcha back buffertens upplösning. När en session är klar `GraphicsBindingSimD3d11.InitSimulation` måste anropas före anslutning till den:

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Funktionen init måste tillhandahållas med pekare till den interna D3D-enheten samt färg-och djup texturen för proxyns åter givnings mål. När den har initierats `AzureSession.ConnectToRuntime` och `DisconnectFromRuntime` kan anropas flera gånger, men när du växlar till en annan session, `GraphicsBindingSimD3d11.DeinitSimulation` måste anropas först på den gamla sessionen innan den `GraphicsBindingSimD3d11.InitSimulation` kan anropas i en annan session.

#### <a name="render-loop-update"></a>Uppdatera upprepnings slinga

Uppdateringen rendera loop består av flera steg:

1. Varje ram, innan en åter givning sker, `GraphicsBindingSimD3d11.Update` anropas med den aktuella Camera-transformeringen som skickas över till den server som ska renderas. Samtidigt bör den returnerade proxysammansättningen tillämpas på den proxyserver som ska renderas i proxyns åter givnings mål.
Om den returnerade proxykonfigurationen `SimulationUpdate.frameId` är null finns det inga fjärrdata än. I det här fallet i stället för att rendera in i proxyns åter givnings mål, ska allt lokalt innehåll återges till den bakre bufferten direkt med hjälp av aktuella kamera data och nästa två steg hoppas över.
1. Programmet bör nu binda proxyns åter givnings mål och anropa `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Då fylls information om fjärrfärg och djup i proxyns åter givnings mål. Alla lokala innehåll kan nu återges på proxyservern med hjälp av proxy-kamera omvandlingen.
1. Sedan måste den bakre bufferten vara kopplad till ett åter givnings mål och `GraphicsBindingSimD3d11.ReprojectProxy` anropas med den punkt där den bakre bufferten kan visas.

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Konfigurera ett Unity-projekt från grunden](../tutorials/unity/project-setup.md)
