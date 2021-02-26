---
title: 'Speech-Dienst: Sprachsynthese-API-Referenz (REST)'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Sprachsynthese-REST-API verwenden. In diesem Artikel erfahren Sie mehr über Autorisierungs- und Abfrageoptionen sowie darüber, wie Sie eine Anforderung strukturieren und eine Antwort erhalten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: b4a4fa8a207320a5867b6cdf1dc347bbb2f453af
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525737"
---
# <a name="text-to-speech-rest-api"></a>Text-to-Speech-REST-API

Die Speech-Dienste ermöglichen es Ihnen, [Text in synthetisierte Sprache umzuwandeln](#convert-text-to-speech) und [eine Liste der unterstützten Stimmen](#get-a-list-of-voices) für eine Region unter Verwendung einer Reihe von REST-APIs zu erhalten. Jeder verfügbare Endpunkt ist einer Region zugeordnet. Es ist ein Abonnementschlüssel für den Endpunkt bzw. die Region erforderlich, den/die Sie verwenden möchten.

Die Text-to-Speech-REST-API unterstützt neuronale und Standardstimmen für die Sprachsynthese. Jede dieser Stimmen steht für eine bestimmte Sprache und einen bestimmten Dialekt und wird durch das Gebietsschema identifiziert.

* Eine vollständige Liste der Stimmen finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech).
* Informationen zur regionalen Verfügbarkeit finden Sie unter [Regionen](regions.md#text-to-speech).

> [!IMPORTANT]
> Die Kosten sind für Standardstimmen, benutzerdefinierte und neuronale Stimmen unterschiedlich. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Bevor Sie diese API verwenden, müssen Sie Folgendes wissen:

* Die Text-to-Speech-REST-API erfordert einen Autorisierungsheader. Das bedeutet, dass Sie einen Tokenaustausch ausführen müssen, um auf den Dienst zuzugreifen. Weitere Informationen finden Sie unter [Authentifizierung](#authentication).

> [!TIP]
> In [diesem Artikel](sovereign-clouds.md) finden Sie Informationen zu Azure Government- und Azure China-Endpunkten.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Abrufen einer Liste von Stimmen

Der Endpunkt `voices/list` ermöglicht es Ihnen, eine vollständige Liste der Stimmen für eine bestimmte Region bzw. für einen Endpunkt zu erhalten.

### <a name="regions-and-endpoints"></a>Regionen und Endpunkte

| Region | Endpunkt |
|--------|----------|
| Australien (Osten) | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brasilien Süd | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kanada, Mitte | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA (Mitte) | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asien, Osten | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| East US | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA (Ost) 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Frankreich, Mitte | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Indien, Mitte | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan, Osten | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea, Mitte | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Nord Mitte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Nordeuropa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Südafrika, Norden | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Süd Mitte | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asien, Südosten | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| UK, Süden | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, Westen-Mitte | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa, Westen | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA (Westen) | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA, Westen 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Die sich in der Vorschau befindlichen Stimmen](language-support.md#neural-voices-in-preview) sind nur in den folgenden drei Regionen verfügbar: „USA, Osten“, „Europa, Westen“ und „Asien, Südosten“.

### <a name="request-headers"></a>Anforderungsheader

Diese Tabelle führt die erforderlichen und optionalen Header für Text-to-Speech-Anforderungen auf.

| Header | BESCHREIBUNG | Erforderlich/optional |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Ihr Abonnementschlüssel des Spracherkennungsdiensts. | Entweder dieser Header oder `Authorization` ist erforderlich. |
| `Authorization` | Ein Autorisierungstoken, dem das Wort `Bearer` vorangestellt ist. Weitere Informationen finden Sie unter [Authentifizierung](#authentication). | Entweder dieser Header oder `Ocp-Apim-Subscription-Key` ist erforderlich. |



### <a name="request-body"></a>Anforderungstext

Ein Text ist nicht erforderlich für `GET`-Anforderungen an diesen Endpunkt.

### <a name="sample-request"></a>Beispiel für eine Anforderung

Diese Anforderung erfordert nur einen Autorisierungsheader.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Beispiel für eine Antwort

Diese Antwort wurde abgeschnitten, um die Struktur einer Antwort zu veranschaulichen.

> [!NOTE]
> Die Verfügbarkeit von Stimmen variiert je nach Region/Endpunkt.

```json
[
  {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...
      
    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },
  
  ...
    
     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },
  
  ...
    
   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...
]
```

### <a name="http-status-codes"></a>HTTP-Statuscodes

Der HTTP-Statuscode jeder Antwort zeigt den Erfolg oder allgemeine Fehler an.

| HTTP-Statuscode | BESCHREIBUNG | Mögliche Ursache |
|------------------|-------------|-----------------|
| 200 | OK | Die Anforderung wurde erfolgreich gesendet. |
| 400 | Ungültige Anforderung | Ein erforderlicher Parameter fehlt, ist leer oder Null. Oder der an einen erforderlichen oder optionalen Parameter übergebene Wert ist ungültig. Ein häufiges Problem sind zu lange Kopfzeilen. |
| 401 | Nicht autorisiert | Die Anforderung ist nicht autorisiert. Stellen Sie sicher, dass Ihr Abonnementschlüssel oder -token gültig ist und sich in der richtigen Region befindet. |
| 429 | Zu viele Anforderungen | Sie haben das Kontingent oder die Rate der Anforderungen überschritten, das bzw. die für Ihr Abonnement zulässig ist. |
| 502 | Ungültiges Gateway    | Netzwerk- oder serverseitiges Problem. Kann auch auf ungültige Header hinweisen. |


## <a name="convert-text-to-speech"></a>Konvertieren von Text in Sprache

Der Endpunkt `v1` ermöglicht es Ihnen, Text-to-Speech mit der [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) zu konvertieren.

### <a name="regions-and-endpoints"></a>Regionen und Endpunkte

Diese Regionen werden für die Konvertierung von Text in Sprache über die REST-API unterstützt. Achten Sie darauf, dass Sie den Endpunkt für Ihre Abonnementregion auswählen.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Anforderungsheader

Diese Tabelle führt die erforderlichen und optionalen Header für Text-to-Speech-Anforderungen auf.

| Header | BESCHREIBUNG | Erforderlich/optional |
|--------|-------------|---------------------|
| `Authorization` | Ein Autorisierungstoken, dem das Wort `Bearer` vorangestellt ist. Weitere Informationen finden Sie unter [Authentifizierung](#authentication). | Erforderlich |
| `Content-Type` | Gibt den Inhaltstyp des angegebenen Texts an. Zulässiger Wert: `application/ssml+xml`. | Erforderlich |
| `X-Microsoft-OutputFormat` | Gibt das Audioausgabeformat an. Eine vollständige Liste der zulässigen Werte finden Sie unter [Audioausgaben](#audio-outputs). | Erforderlich |
| `User-Agent` | Der Anwendungsname. Der angegebene Wert muss kürzer als 255 Zeichen sein. | Erforderlich |

### <a name="audio-outputs"></a>Audioausgaben

Dies ist eine Liste der unterstützten Audioformate, die in jeder Anforderung als `X-Microsoft-OutputFormat`-Header gesendet werden. Es wird jeweils eine Bitrate und ein Codierungstyp angegeben. Der Speech-Dienst unterstützt Audioausgaben mit 24 kHz, 16 kHz und 8 kHz.

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
```

> [!NOTE]
> Wenn die ausgewählte Stimme und das ausgewählte Ausgabeformat unterschiedliche Bitraten aufweisen, wird das Audio nach Bedarf neu gesampelt. ogg-24khz-16bit-mono-opus kann mit [Opus-Codec](https://opus-codec.org/downloads/) decodiert werden.

### <a name="request-body"></a>Anforderungstext

Der Text jeder `POST`-Anforderung wird als [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) gesendet. SSML ermöglicht es Ihnen, die Stimme und Sprache der synthetisierten Sprachausgabe auszuwählen, die vom Text-zu-Sprache-Dienst zurückgegeben wird. Eine vollständige Liste der unterstützten Stimmen finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech).

> [!NOTE]
> Wenn Sie eine benutzerdefinierte Stimme verwenden, kann der Text der Anforderung als Nur-Text (ASCII oder UTF-8) gesendet werden.

### <a name="sample-request"></a>Beispiel für eine Anforderung

Diese HTTP-Anforderung gibt mit SSML die Stimme und die Sprache an. Wenn der Text lang ist und die resultierende Audiodatei zehn Minuten überschreitet, wird sie auf zehn Minuten gekürzt. Das heißt, die Audiodatei darf nicht länger als zehn Minuten sein.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP-Statuscodes

Der HTTP-Statuscode jeder Antwort zeigt den Erfolg oder allgemeine Fehler an.

| HTTP-Statuscode | BESCHREIBUNG | Mögliche Ursache |
|------------------|-------------|-----------------|
| 200 | OK | Die Anforderung war erfolgreich. Der Antworttext ist eine Audiodatei. |
| 400 | Ungültige Anforderung | Ein erforderlicher Parameter fehlt, ist leer oder Null. Oder der an einen erforderlichen oder optionalen Parameter übergebene Wert ist ungültig. Ein häufiges Problem sind zu lange Kopfzeilen. |
| 401 | Nicht autorisiert | Die Anforderung ist nicht autorisiert. Stellen Sie sicher, dass Ihr Abonnementschlüssel oder -token gültig ist und sich in der richtigen Region befindet. |
| 415 | Nicht unterstützter Medientyp | Möglicherweise wurde der falsche `Content-Type`-Wert bereitgestellt. `Content-Type` sollte auf `application/ssml+xml` festgelegt sein. |
| 429 | Zu viele Anforderungen | Sie haben das Kontingent oder die Rate der Anforderungen überschritten, das bzw. die für Ihr Abonnement zulässig ist. |
| 502 | Ungültiges Gateway    | Netzwerk- oder serverseitiges Problem. Kann auch auf ungültige Header hinweisen. |

Wenn der HTTP-Status `200 OK` ist, enthält der Text der Antwort eine Audiodatei im angeforderten Format. Diese Datei kann bei der Übertragung abgespielt sowie in einem Puffer oder in einer Datei gespeichert werden.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
- [Asynchrone Synthese für lange Audioinhalte](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)
- [Erste Schritte mit Custom Voice](how-to-custom-voice.md)