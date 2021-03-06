---
title: 'Snabb start: identifiera tal från en mikrofon, C# (Xamarin) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du ett plattforms oberoende C# Xamarin-program för Universell Windows-plattform (UWP), Android och iOS med hjälp av Cognitive Services Speech SDK. Du kan skriva tal i text i real tid från enhetens eller Simulatorns mikrofon. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: c9bcd301b19252cedd9ac9a1867ccf132a537587
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275576"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

Om du redan har gjort detta är det bra. Vi fortsätter.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Lägg till exempel kod för det vanliga HelloWorld-projektet

Det vanliga projektet HelloWorld innehåller plattforms oberoende implementeringar för ditt plattforms oberoende program. Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C#-koden bakom implementeringen.

1. I **Solution Explorer**går du till det vanliga HelloWorld-projektet `MainPage.xaml`och öppnar.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen `<StackLayout>` mellan `</StackLayout>`och:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. Öppna **Solution Explorer**käll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. Den är grupperad `MainPage.xaml`under.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Leta upp strängen `YourSubscriptionKey`i käll `OnRecognitionButtonClicked` filens hanterare och ersätt den med din prenumerations nyckel.


1. Leta upp `OnRecognitionButtonClicked` strängen `YourServiceregion`i hanteraren och ersätt den med **regions-ID: n** från den [region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration. (Använd `westus` till exempel för den kostnads fria prov prenumerationen.)

1. Därefter måste du skapa en [Xamarin-tjänst](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)som används för att fråga mikrofon behörigheter från olika plattforms projekt, till exempel UWP, Android och iOS. Det gör du genom att lägga till en ny mapp med namnet *tjänster* i projektet HelloWorld och skapa en ny C#-källfil under den. Du kan högerklicka på mappen *tjänster* och välja **Lägg till** > **ny objekt** > **kod fil**. Byt namn på `IMicrophoneService.cs`filen och placera all kod från följande kodfragment i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Lägg till exempel kod för `helloworld.Android` projektet

Lägg nu till C#-koden som definierar den Android-specifika delen av programmet.

1. I **Solution Explorer**under HelloWorld. Android-projekt, `MainActivity.cs`öppna.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Lägg sedan till Android-speciell implementering för `MicrophoneService` genom att skapa de nya *mapparna i mappen* HelloWorld. Android-projekt. Därefter skapar du en ny C#-källfil under den. Byt namn på `MicrophoneService.cs`filen. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Efter det öppnar `AndroidManifest.xml` du under mappen *Egenskaper* . Lägg till följande användning – behörighets inställning för mikrofonen `<manifest>` mellan `</manifest>`och:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Lägg till exempel kod för `helloworld.iOS` projektet

Lägg nu till C#-koden som definierar den iOS-specifika delen av programmet. Skapa även Apple-enhetsspecifika konfigurationer i projektet HelloWorld. iOS.

1. I **Solution Explorer**, under projektet HelloWorld. iOS, öppnar `AppDelegate.cs`du.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Lägg sedan till iOS-speciell implementering för `MicrophoneService` genom att skapa de nya *mapparna* i HelloWorld.io-projektet. Därefter skapar du en ny C#-källfil under den. Byt namn på `MicrophoneService.cs`filen. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Öppna `Info.plist` under projektet HelloWorld. iOS i text redigeraren. Lägg till följande nyckel värde par under avsnittet Dictation:

   <key>NSMicrophoneUsageDescription</key>
   <string>den här exempel appen kräver åtkomst till mikrofonen</string>

   > [!NOTE]
   > Om du skapar för en iPhone-enhet måste du kontrol `Bundle Identifier` lera att matchar enhetens etablerings profil app-ID. Annars Miss kommer versionen. Med iPhoneSimulator kan du lämna den som den är.

1. Om du bygger på en Windows-dator upprättar du en anslutning till Mac-enheten för att bygga via **Tools** > **iOS** > -**paret till Mac**. Följ anvisningarna i Visual Studio för att aktivera anslutningen till Mac-enheten.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempel kod för `helloworld.UWP` projektet

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempel kod för HelloWorld. UWP-projekt

Nu ska du lägga till C#-koden som definierar UWP-specifik del av programmet.

1. I **Solution Explorer**under HelloWorld. UWP-projekt, `MainPage.xaml.cs`öppna.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Lägg sedan till en UWP implementering för `MicrophoneService` genom att skapa de nya Folder- *tjänsterna* under HelloWorld. UWP-projekt. Därefter skapar du en ny C#-källfil under den. Byt namn på `MicrophoneService.cs`filen. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dubbelklicka sedan på `Package.appxmanifest` filen under HelloWorld. UWP-projekt i Visual Studio. Under **funktioner**kontrollerar du att **mikrofon** är markerat och sparar filen.

1. Nästa `Package.appxmanifest` dubbel klicknings fil under `helloworld.UWP` projektet i Visual Studio och under **funktioner** > **mikrofonen** är markerat och sparar filen.
   > Obs: om du ser varning: certifikat filen finns inte: HelloWorld. UWP_TemporaryKey. pfx, kontrol lera [tal till text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) -exemplet för mer information.

1. I meny raden väljer du **Arkiv** > **Spara alla** för att spara ändringarna.

## <a name="build-and-run-the-uwp-application"></a>Skapa och köra UWP-programmet

1. Ange HelloWorld. UWP som ett start projekt. Högerklicka på HelloWorld. UWP-projekt och välj **build** för att bygga programmet.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller Välj **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP tal igenkännings program i C# – snabb start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Välj **aktivera mikrofon**. När begäran om åtkomst behörighet visas väljer du **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Välj **Starta tal igenkänning**och tala en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

   ![Användar gränssnitt för tal igenkänning](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Skapa och kör Android-och iOS-program

Att skapa och köra Android-och iOS-program i enheten eller simulatorn sker på ett liknande sätt som UWP. Se till att alla SDK: er installeras korrekt enligt kraven i avsnittet "krav" i den här artikeln.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
