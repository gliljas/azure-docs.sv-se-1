---
title: Azure Media Player fullständig installation
description: Lär dig hur du konfigurerar Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727247"
---
# <a name="azure-media-player-full-setup"></a>Fullständig installation av Azure Media Player #

Azure Media Player är enkelt att konfigurera. Det tar bara en liten stund att få en grundläggande uppspelning av medie innehåll direkt från ditt Azure Media Services-konto. [Exempel](https://github.com/Azure-Samples/azure-media-player-samples) finns också i katalogen samples i versionen.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Steg 1: inkludera JavaScript-och CSS-filerna i sidhuvudet på din sida ##

Med Azure Media Player kan du komma åt skripten från den värdbaserade CDN-versionen. Vi rekommenderar ofta att du lägger till java script före taggen `<body>` end Body i stället för `<head>`, men Azure Media Player innehåller en HTML5-Shiv, som måste finnas i huvudet för äldre IE-versioner för att det ska gå att följa video tag gen som ett giltigt element.

> [!NOTE]
> Om du redan använder en HTML5-Shiv som [Modernizr](http://modernizr.com/) kan du ta med Azure Media Player Java Script var som helst. Se dock till att din version av Modernizr innehåller Shiv för video.

### <a name="cdn-version"></a>CDN-version ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> Du bör **inte** använda `latest` versionen i produktion eftersom det kan komma att ändras på begäran. Ersätt `latest` med en version av Azure Media Player. Ersätt `latest` till exempel med `2.1.1`. Azure Media Player versioner kan frågas [härifrån.](azure-media-player-changelog.md)

> [!NOTE]
> Sedan `1.2.0` versionen är inte längre nödvändig för att inkludera platsen för reserv teknikerna (den hämtar automatiskt platsen från den relativa sökvägen för azuremediaplayer. min. js-filen). Du kan ändra platsen för reserv teknikerna genom att lägga till följande skript i `<head>` efter ovanstående skript.

> [!NOTE]
> På grund av typen av plugin-program och Silverlight-plugin-program bör SWF-och XAP-filerna finnas på en domän utan känslig information eller data – detta tar automatiskt hand om dig med den Azure CDN värdbaserade versionen.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Steg 2: Lägg till en HTML5-video-tagg på sidan ##

Med Azure Media Player kan du använda en HTML5-video tag för att bädda in en video. Azure Media Player kommer sedan att läsa taggen och göra den fungerar i alla webbläsare, inte bara för de som stöder HTML5-video. Utöver Basic-koden behöver Azure Media Player några extra delar.

1. `<data-setup>` Attributet i `<video>` instruerar Azure Media Player att automatiskt konfigurera videon när sidan är klar och läsa eventuella (i JSON-format) från attributet.
1. `id` Attributet: ska användas och vara unikt för varje video på samma sida.
1. `class` Attributet innehåller två klasser:
    - `azuremediaplayer`använder format som krävs för Azure Media Player UI-funktioner
    - `amp-default-skin`tillämpar standard skalet på HTML5-kontrollerna
1. `<source>` Innehåller två obligatoriska attribut
    - `src`attribut kan innehålla en **. ISM/manifest-* fil från Azure Media Services läggs till, Azure Media Player lägger automatiskt till URL: er för streck, mjuk och HLS i spelaren
    - `type`attribut är den nödvändiga MIME-typen för data strömmen. MIME-typen som är associerad med *". ISM/manifest"* är *"Application/VND. MS-sstr + XML"*
1. Det *valfria* `<data-setup>` attributet på `<source>` visar Azure Media Player om det finns några unika leverans principer för strömmen från Azure Media Services, inklusive, men inte begränsat till, krypterings typ (AES eller PlayReady, Widevine eller Fairplay) och token.

Inkludera/exkludera attribut, inställningar, källor och spår exakt som för HTML5-video.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Som standard är den stora uppspelnings knappen i det övre vänstra hörnet så att den inte tar upp några intressanta delar av affischen. Om du föredrar att centrera den stora uppspelnings knappen kan du lägga till ytterligare `amp-big-play-centered` `class` en till ditt `<video>` -element.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Alternativ inställning för dynamiskt inläst HTML ###

Om din webb sida eller ditt program läser in video tag gen dynamiskt (Ajax, appendChild osv.), så att den kanske inte finns när sidan läses in, vill du konfigurera spelaren manuellt i stället för att förlita dig på attributet data-setup. För att göra detta måste du först ta bort attributet data-setup från taggen, så att det inte finns någon förvirring kring när spelaren initieras. Kör sedan följande Java Script lite tid efter att Azure Media Player Java Script har lästs in och när video tag gen har lästs in i DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Det första argumentet i `amp` funktionen är ID: t för video tag gen. Ersätt det med ditt eget.

Det andra argumentet är ett alternativ-objekt. Det gör att du kan ange ytterligare alternativ som du kan med attributet data konfiguration.

Det tredje argumentet är en Ready-motringning. När Azure Media Player har initierats anropas den här funktionen. I det redo motringning refererar objektet till Player-instansen.

I stället för att använda ett element-ID kan du också skicka en referens till själva elementet.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)