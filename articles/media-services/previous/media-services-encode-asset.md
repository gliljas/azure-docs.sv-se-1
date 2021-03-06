---
title: Översikt över Azure on-demand Media Encoder | Microsoft Docs
description: Azure Media Services tillhandahåller flera alternativ för kodning av media i molnet. Den här artikeln ger en översikt över Azure on-demand Media Encoder.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251107"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Översikt över Azure on-demand Media Encoder 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [vägledning för migrering från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Azure Media Services tillhandahåller flera alternativ för kodning av media i molnet.

När du börjar med Media Services är det viktigt att förstå skillnaden mellan codec-och fil format.
Codecenheter är program varan som implementerar algoritmerna för komprimering/expandering, medan fil format är behållare som innehåller den komprimerade videon.

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera din anpassningsbara bit hastighets-MP4 eller Smooth Streaming kodat innehåll i strömnings format som stöds av Media Services (MPEG-streck, HLS Smooth Streaming) utan att du behöver paketera om till dessa strömnings format.

När ditt Media Services-konto skapas, läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. Faktureringen av slut punkter för direkt uppspelning sker när slut punkten är i ett **körnings** tillstånd.

Media Services stöder följande kodare på begäran som beskrivs i den här artikeln:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Arbetsflöde för Media Encoder Premium](media-services-encode-asset.md#media-encoder-premium-workflow)

Den här artikeln ger en kort översikt över medier för medie kodare på begäran och innehåller länkar till artiklar som ger mer detaljerad information. Avsnittet innehåller också jämförelse av kodare.

Som standard kan varje Media Services konto ha en aktiv kodnings aktivitet i taget. Du kan reservera kodnings enheter som gör att du kan köra flera kodnings aktiviteter samtidigt, en för varje encoding-reserverad enhet som du köper. Mer information finns i [skala kodnings enheter](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Använd så här
[Så här kodar du med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Format
[Format och codecenheter](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Hands
Media Encoder Standard konfigureras med hjälp av en av kodarens för inställningar som beskrivs [här](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadata för indata och utdata
Kodarens indata-metadata beskrivs [här](media-services-input-metadata-schema.md).

Kodarens utdata metadata beskrivs [här](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Skapa miniatyrbilder
Mer information finns i [så här skapar du miniatyr bilder med hjälp av Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Trimma videor (Urklipp)
Mer information finns i [så här trimmar du videor med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Skapa överlägg
Mer information finns i [så här skapar du överlägg med Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Se även
[Media Services blogg](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Arbetsflöde för Media Encoder Premium
### <a name="overview"></a>Översikt
[Vi presenterar Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Använd så här
Media Encoder Premium Workflow konfigureras med komplexa arbets flöden. Du kan skapa och uppdatera arbetsflödes filer med hjälp av [arbetsflödesdesigner](media-services-workflow-designer.md) -verktyget.

[Så här använder du Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Kända problem
Om din indata-video inte innehåller dold textning innehåller utmatnings till gången fortfarande en tom TTML-fil.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodnings uppgifter genom att anpassa Media Encoder Standard för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
