---
title: Punkt końcowy kontenera kwerendy zamiany tekstu na mowę
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491096"
---
Kontener udostępnia [interfejsy API oparte na rest.](../rest-text-to-speech.md) Dostępnych jest wiele [przykładowych projektów kodu źródłowego](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) dla dostępnych odmian platformy, struktury i języka.

W kontenerze *Standardowy tekst na mowę* należy polegać na ustawieniach regionalnych i głosach pobranego tagu obrazu. Na przykład, jeśli pobrałeś `latest` tag, `en-US` domyślne `JessaRUS` ustawienia regionalne są i głos. Argument `{VOICE_NAME}` będzie wtedy [`en-US-JessaRUS`](../language-support.md#standard-voices). Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Jednak w przypadku *niestandardowego tekstu na mowę* musisz uzyskać **głos / model** z [niestandardowego portalu głosowego](https://aka.ms/custom-voice-portal). Nazwa modelu niestandardowego jest synonimem nazwy głosowej. Przejdź do strony **Szkolenia** i skopiuj `{VOICE_NAME}` głos / **model,** aby użyć go jako argumentu.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Niestandardowy model głosu - nazwa głosowa":::

Zobacz przykład SSML poniżej:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Skonstruujmy żądanie HTTP POST, zapewniając kilka nagłówków i ładunek danych. Zastąp symbol zastępczy `{VOICE_NAME}` własną wartością.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

To polecenie:

* Konstruuje żądanie HTTP `speech/synthesize/cognitiveservices/v1` POST dla punktu końcowego.
* Określa `Accept` nagłówek`audio/*`
* Określa `Content-Type` nagłówek `application/ssml+xml`, aby uzyskać więcej informacji, zobacz treść [żądania](../rest-text-to-speech.md#request-body).
* Określa `X-Microsoft-OutputFormat` nagłówek `riff-16khz-16bit-mono-pcm`, aby uzyskać więcej opcji, zobacz wyjście [audio](../rest-text-to-speech.md#audio-outputs).
* Wysyła żądanie [języka znaczników syntezy mowy (SSML)](../speech-synthesis-markup.md) podane `{VOICE_NAME}` do punktu końcowego.