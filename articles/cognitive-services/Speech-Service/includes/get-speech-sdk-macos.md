---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: d9c6062e7106f764058d6c96c6f730d235b2ef0c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673041"
---
När du utvecklar för macOS finns det tre tal-SDK: er tillgängliga.

- Mål-C-talet SDK är internt tillgängligt som ett CocoaPod-paket
- .NET Speech SDK kan användas med **Xamarin. Mac** eftersom det implementerar .net standard 2,0
- Python Speech SDK är tillgänglig som en PyPI-modul

> [!TIP]
> Mer information om hur du använder mål-C tal-SDK med Swift finns i <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importera mål- <span class="docon docon-navigate-external x-hidden-focus"></span> c till SWIFT </a>.

### <a name="system-requirements"></a>Systemkrav

- MacOS version 10,13 eller senare

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        MacOS CocoaPod-paketet finns tillgängligt för hämtning och användning med <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode-9.4.1 (eller senare) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Integrated Development Environment (IDE). Börja <a href="https://aka.ms/csspeech/macosbinary" target="_blank">med att hämta binärfilen <span class="docon docon-navigate-external x-hidden-focus"></span> CocoaPod </a>. Extrahera Pod i samma katalog för den avsedda användningen, skapa en *Podfile* och lista `pod` som `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.12.0'
end
```

# <a name="xamarinmac"></a>[Xamarin. Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. Mac visar hela macOS SDK för .NET-utvecklare för att bygga inbyggda Mac-program med C#. Mer information finns i <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. Mac <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS tal SDK snabb start mål – C källkod<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS tal SDK snabb starts källa kod<span class="docon docon-navigate-external x-hidden-focus"></span></a>