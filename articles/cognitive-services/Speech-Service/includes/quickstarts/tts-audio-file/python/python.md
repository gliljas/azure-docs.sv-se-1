---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 4429a03709d0f565d5fcf98f6999c812c0d43561
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673092"
---
## <a name="prerequisites"></a>Krav

* En Azure-prenumerationsnyckel för tjänsten Speech. [Skaffa en kostnadsfritt](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3,5 till 3,8](https://www.python.org/downloads/).
* Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
    * Windows: x64 och x86.
    * Mac: Mac OS X version 10.12 eller senare.
    * Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 på x64.
* Kör de här kommandona i Linux för att installera de nödvändiga paketen:

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> - På RHEL/CentOS 7 följer du anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

* I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Det här kommandot installerar Python-paketet från [PyPI](https://pypi.org/) för Speech SDK:

```Bash
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet distribueras via PyPI och visas i [Viktig information](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du ta en titt på [alternativen för support och hjälp](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Skapa ett Python-program som använder Speech SDK

### <a name="run-the-sample"></a>Kör exemplet

Du kan kopiera [exempelkoden](#sample-code) till en källfil från den här snabbstarten `quickstart.py` och köra den i din IDE eller i konsolen:

```Bash
python quickstart.py
```

Eller så kan du ladda ned den här snabbstartssjälvstudien som en [Jupyter](https://jupyter.org) Notebook från [exempellagringsplatsen för Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) och köra den som en notebook-fil.

### <a name="sample-code"></a>Exempelkod

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

1. Hämta och installera en 64-bitars version av [python](https://www.python.org/downloads/), 3,5 till 3,8, på din dator.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **fil**  >  **Inställningar**  >  **tillägg** på menyn. Sök efter **Python**.

   ![Installera Python-tillägget](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Skapa en mapp att lagra projektet i. Ett exempel är med hjälp av Windows Explorer.
1. Från Visual Studio Code väljer du ikonen **Arkiv**. Öppna sedan den mapp du skapade.

   ![Öppna en mapp](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Skapa en ny Python-källfil `speechsdk.py` genom att välja den nya filikonen.

   ![Skapa en fil](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Kopiera, klistra in och spara [Python-koden](#sample-code) till den nyskapade filen.
1. Infoga prenumerationsinformationen för taltjänsten.
1. Om en Python-tolk har valts visas den på statusfältets vänstra sida, längst ner i fönstret.
   Annars kan du ta fram en lista över tillgängliga Python-tolkar. Öppna kommando paletten (<kbd>Ctrl + Shift + P</kbd>) och ange **python: Välj tolk**. Välj en som passar.
1. Du kan installera Speech SDK Python-paketet i Visual Studio Code. Gör det om det inte är installerat än för Python-tolken som du valt.
   Öppna en terminal för att installera Speech SDK-paketet. Öppna kommando rads verktyget igen (<kbd>Ctrl + Shift + P</kbd>) och ange **Terminal: skapa ny integrerad Terminal**.
   I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`, eller lämpligt kommando för ditt system.
1. Kör exempelkoden genom att högerklicka någonstans i redigeraren. Välj **Kör Python-fil i Terminal**.
   Texten konverteras till tal och sparas i de ljuddata som anges.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Om det finns problem med att följa dessa anvisningar finns den mer omfattande [självstudien Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
