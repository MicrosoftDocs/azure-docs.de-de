---
title: Erste Schritte mit der Bing-Spracheingabe-API unter Verwendung von REST | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Greifen Sie mithilfe von REST auf die Spracherkennungs-API in Microsoft Cognitive Services zu, um Sprache in Text umzuwandeln.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ead4026ecec4878c69bc21a9ebc989eaf3d69a13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515136"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Schnellstart: Verwenden der REST-API der Bing-Spracheingabe-API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Mit dem cloudbasierten Bing-Spracheingabe-Dienst können Sie Anwendungen entwickeln und dabei die REST-API nutzen, um Sprache in Text umzuwandeln.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Abonnieren der Sprach-API und Abrufen eines Schlüssels für ein kostenloses Probeabonnement

Die Sprach-API ist Teil von Cognitive Services (ehemals Project Oxford). Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

> [!IMPORTANT]
>* Rufen Sie einen Abonnementschlüssel ab. Für den Zugriff auf die REST-API benötigen Sie einen [Abonnementschlüssel](https://azure.microsoft.com/try/cognitive-services/).
>
>* Verwenden Sie Ihren Abonnementschlüssel. Ersetzen Sie „YOUR_SUBSCRIPTION_KEY“ in den folgenden REST-Beispielen durch Ihren eigenen Abonnementschlüssel.
>
>* Eine Anleitung zum Abrufen eines Abonnementschlüssels finden Sie auf der Seite [Authentifizierung](../how-to/how-to-authentication.md).

### <a name="prerecorded-audio-file"></a>Vorab aufgezeichnete Audiodatei

In diesem Beispiel wird die Verwendung der REST-API anhand einer aufgezeichneten Audiodatei veranschaulicht. Zeichnen Sie eine Audiodatei auf mit einem kurzen gesprochenen Satz auf. Sagen Sie beispielsweise: „Wie ist das Wetter heute?“ Oder: „Suche nach lustigen Filmen.“ Die Spracherkennungs-API unterstützt auch Eingaben über ein externes Mikrofon.

> [!NOTE]
> Das aufgezeichnete Audio muss als WAV-Datei mit **PCM (einzelner Kanal/Mono) und 16 kHz** vorliegen.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Erstellen einer Erkennungsanforderung und Senden der Anforderung an den Spracherkennungsdienst

Im nächsten Schritt für die Spracherkennung wird eine POST-Anforderung mit korrektem Anforderungsheader und Hauptteil an die Speech-HTTP-Endpunkte gesendet.

### <a name="service-uri"></a>Dienst-URI

Der Spracherkennungsdienst-URI wird auf der Grundlage von [Erkennungsmodi](../concepts.md#recognition-modes) und [Erkennungssprachen](../concepts.md#recognition-languages) definiert:

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` gibt den Erkennungsmodus an und muss einen der folgenden Werte haben: `interactive`, `conversation` oder `dictation`. Hierbei handelt es sich um einen erforderlichen Ressourcenpfad im URI. Weitere Informationen finden Sie unter [Recognition modes](../concepts.md#recognition-modes) (Erkennungsmodi).

`<LANGUAGE_TAG>` ist ein erforderlicher Parameter in der Abfragezeichenfolge. Er definiert die Zielsprache für die Audiokonvertierung (beispielsweise `en-US` für „Englisch (USA)“). Weitere Informationen finden Sie unter [Recognition languages](../concepts.md#recognition-languages) (Erkennungssprachen).

`<OUTPUT_FORMAT>` ist ein optionaler Parameter in der Abfragezeichenfolge. Zulässige Werte sind `simple` und `detailed`. Standardmäßig gibt der Dienst Ergebnisse im `simple`-Format zurück. Weitere Informationen finden Sie unter [Output format](../concepts.md#output-format) (Ausgabeformat).

Die folgende Tabelle enthält einige Beispiele für Dienst-URIs:

| Erkennungsmodus  | Sprache | Ausgabeformat | Dienst-URI |
|---|---|---|---|
| `interactive` | pt-BR | Standard | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Detailliert | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Einfach | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Der Dienst-URI ist nur erforderlich, wenn Ihre Anwendung REST-APIs verwendet, um den Spracherkennungsdienst aufzurufen. Bei Verwendung einer der [Clientbibliotheken](GetStartedClientLibraries.md) müssen Sie in der Regel nicht wissen, welcher URI verwendet wird. Die Clientbibliotheken verwenden möglicherweise unterschiedliche Dienst-URIs, die jeweils nur für eine bestimmte Clientbibliothek relevant sind. Weitere Informationen finden Sie in der Clientbibliothek Ihrer Wahl.

### <a name="request-headers"></a>Anforderungsheader

Folgende Felder müssen im Anforderungsheader festgelegt werden:

- `Ocp-Apim-Subscription-Key`: Bei jedem Aufruf des Diensts müssen Sie im Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel übergeben. Der Speech-Dienst unterstützt auch die Übergabe von Autorisierungstoken anstelle von Abonnementschlüsseln. Weitere Informationen finden Sie unter [Authentifizierung](../How-to/how-to-authentication.md).
- `Content-type`: Das Feld `Content-type` beschreibt das Format und den Codec des Audiostreams. Aktuell werden nur WAV-Dateien und die Codierung „PCM Mono 16.000“ unterstützt. Der Content-type-Wert für dieses Format lautet `audio/wav; codec=audio/pcm; samplerate=16000`.

Das Feld `Transfer-Encoding` ist optional. Wenn Sie dieses Feld auf `chunked` festlegen, können Sie die Audiodaten in kleine Segmente aufteilen. Weitere Informationen finden Sie unter [Chunked transfer encoding](../How-to/how-to-chunked-transfer.md) (Segmentierte Übertragungscodierung).

Das folgende Beispiel zeigt einen exemplarischen Anforderungsheader:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Senden einer Anforderung an den Dienst

Das folgende Beispiel zeigt, wie Sie eine Spracherkennungsanforderung an Speech-REST-Endpunkte senden. In dem Beispiel wird der Erkennungsmodus `interactive` verwendet.

> [!NOTE]
> Ersetzen Sie `YOUR_AUDIO_FILE` durch den Pfad Ihrer aufgezeichneten Audiodatei. Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` durch Ihren eigenen Abonnementschlüssel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Das Beispiel verwendet curl unter Linux mit Bash. Sollte curl auf Ihrer Plattform nicht verfügbar sein, müssen Sie es möglicherweise installieren. Das Beispiel kann auch mit Cygwin unter Windows, Git Bash, zsh und mit anderen Shells verwendet werden.

> [!NOTE]
> Behalten Sie das Zeichen `@` vor dem Namen der Audiodatei bei, wenn Sie `YOUR_AUDIO_FILE` durch den Pfad Ihrer aufgezeichneten Audiodatei ersetzen. Dadurch wird angegeben, dass es sich bei dem Wert von `--data-binary` nicht um Daten, sondern um einen Dateinamen handelt.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>Verarbeiten der Spracherkennungsantwort

Nach der Verarbeitung der Anforderung gibt der Speech-Dienst die Ergebnisse in einer Antwort im JSON-Format zurück.

> [!NOTE]
> Sollte der vorherige Code einen Fehler zurückgeben, lesen Sie den Abschnitt zur [Problembehandlung](../troubleshooting.md), um die mögliche Ursache zu ermitteln.

Der folgende Codeausschnitt zeigt anhand eines Beispiels, wie Sie die Antwort aus dem Datenstrom lesen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

In diesem Beispiel gibt curl die Antwortnachricht direkt in einer Zeichenfolge zurück. Wenn Sie sie im JSON-Format anzeigen möchten, können Sie zusätzliche Tools verwenden (beispielsweise jq).

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

Das folgende Beispiel zeigt eine JSON-Antwort:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Einschränkungen

Für die REST-API gelten bestimmte Einschränkungen:

- Sie unterstützt nur Audiostreams mit einer Länge von maximal 15 Sekunden.
- Zwischenergebnisse während der Erkennung werden nicht unterstützt. Benutzer erhalten nur das Endergebnis der Erkennung.

Bei Verwendung der [Clientbibliotheken](GetStartedClientLibraries.md) von Speech gelten diese Einschränkungen nicht. Alternativ können Sie direkt das [WebSocket-Protokoll des Speech-Diensts](../API-Reference-REST/websocketprotocol.md) verwenden.

## <a name="whats-next"></a>Nächste Schritte

- Informationen zur Verwendung der REST-API in C#, Java usw. finden Sie in [diesen Beispielanwendungen](../samples.md).
- Informationen zur Fehlersuche und -behebung finden Sie unter [Problembehandlung](../troubleshooting.md).
- Informationen zur Verwendung erweiterter Funktionen finden Sie in den [ersten Schritten mit Clientbibliotheken des Speech-Diensts](GetStartedClientLibraries.md).

### <a name="license"></a>Lizenz

Alle Cognitive Services-SDKs und -Beispiele sind mit der MIT-Lizenz lizenziert. Weitere Informationen finden Sie [hier](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
