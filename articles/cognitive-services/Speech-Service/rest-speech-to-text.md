---
title: 'Spracherkennungs-API-Referenz (REST): Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Spracherkennungs-REST-API verwenden. In diesem Artikel erfahren Sie mehr über Autorisierungs- und Abfrageoptionen sowie darüber, wie Sie eine Anforderung strukturieren und eine Antwort erhalten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: c746666d58e21c2705a2ef1d6a17d0d1196f7590
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504473"
---
# <a name="speech-to-text-rest-api"></a>Spracherkennungs-REST-API

Für die Spracherkennung gibt es zwei unterschiedliche REST-APIs. Jede API dient einem speziellen Zweck und verwendet unterschiedliche Gruppen von Endpunkten.

Es gibt folgende REST-APIs für die Spracherkennung:
- Die [Spracherkennungs-REST-API 3.0](#speech-to-text-rest-api-v30) wird für die [Batchtranskription](batch-transcription.md) und [Custom Speech](custom-speech-overview.md) eingesetzt. Version 3.0 ist die [Nachfolgeversion von Version 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- Die [Spracherkennungs-REST-API für kurze Audiodaten](#speech-to-text-rest-api-for-short-audio) wird für die Onlinetranskription als Alternative zum [Speech-SDK](speech-sdk.md) eingesetzt. Anforderungen, die diese API verwenden, können pro Anforderung nur bis zu 60 Sekunden Audiodaten übertragen. 

## <a name="speech-to-text-rest-api-v30"></a>Spracherkennungs-REST-API 3.0

Die Spracherkennungs-REST-API 3.0 wird für die [Batchtranskription](batch-transcription.md) und [Custom Speech](custom-speech-overview.md) eingesetzt. Wenn Sie mit der Onlinetranskription über REST kommunizieren müssen, verwenden Sie die [Spracherkennungs-REST-API für kurze Audiodaten](#speech-to-text-rest-api-for-short-audio).

Verwenden Sie die REST-API 3.0 für Folgendes:
- Kopieren von Modellen in andere Abonnements, wenn Sie möchten, dass Kollegen Zugriff auf ein von Ihnen erstelltes Modell haben, oder wenn Sie ein Modell in mehreren Regionen bereitstellen möchten
- Transkribieren von Daten aus einem Container (Massentranskription) sowie Bereitstellen mehrerer Audiodatei-URLs
- Hochladen von Daten aus Azure Storage-Konten durch Verwendung eines SAS-URI
- Abrufen von Protokollen pro Endpunkt, wenn Protokolle für diesen Endpunkt angefordert wurden
- Anfordern des Manifests der Modelle, die Sie erstellen, um lokale Container einzurichten

Die REST-API 3.0 enthält unter anderem folgende Features:
- **Benachrichtigungen für Webhooks:** Alle laufenden Prozesse des Diensts unterstützen jetzt Webhookbenachrichtigungen. Die REST-API 3.0 stellt die Aufrufe bereit, mit denen Sie die Webhooks registrieren können, für die Benachrichtigungen gesendet werden.
- **Aktualisieren von Modellen hinter Endpunkten** 
- **Modellanpassung mit mehreren Datasets:** Passen Sie ein Modell mit mehreren Datasetkombinationen aus Akustik-, Sprach- und Aussprachedaten an.
- **Bring Your Own Storage:** Verwenden Sie Ihre eigenen Speicherkonten für Protokolle, Transkriptionsdateien und andere Daten.

Beispiele zur Verwendung der REST-API 3.0 mit der Batchtranskription finden Sie in [diesem Artikel](batch-transcription.md).

Wenn Sie die Spracherkennungs-REST-API 2.0 verwenden, finden Sie weitere Informationen zur Migration zu Version 3.0 in [diesem Leitfaden](/azure/cognitive-services/speech-service/migrate-v2-to-v3).

Die gesamte Referenz zur Spracherkennungs-REST-API 3.0 finden Sie [hier](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

## <a name="speech-to-text-rest-api-for-short-audio"></a>Spracherkennungs-REST-API für kurze Audiodaten

Als Alternative zum [Speech-SDK](speech-sdk.md) ermöglicht der Speech-Dienst das Konvertieren von Sprache in Text mithilfe einer REST-API. Jeder zugängliche Endpunkt ist einer Region zugeordnet. Ihre Anwendung benötigt einen Abonnementschlüssel für den Endpunkt, den Sie verwenden möchten. Die REST-API für kurze Audiodaten ist stark eingeschränkt. Daher sollte sie nur verwendet werden, wenn das [Speech-SDK](speech-sdk.md) nicht verwendet werden kann.

Beachten Sie Folgendes, bevor Sie die Spracherkennungs-REST-API für kurze Audiodaten verwenden:

* Anforderungen, die die REST-API für kurze Audiodaten verwenden und Audiodaten direkt übertragen, können nur bis zu 60 Sekunden Audiodaten enthalten.
* Die Spracherkennungs-REST-API für kurze Audiodaten gibt nur Endergebnisse zurück. Teilergebnisse werden nicht bereitgestellt.

Wenn für Ihre Anwendung das Senden von längeren Audiodaten erforderlich ist, ziehen Sie die Verwendung des [Speech-SDK](speech-sdk.md) oder der [Spracherkennungs-REST-API 3.0](#speech-to-text-rest-api-v30) in Betracht.

> [!TIP]
> Informationen zu Endpunkten zur Government-Cloud (FairFax) finden Sie in der [Azure Government-Dokumentation](../../azure-government/compare-azure-government-global-azure.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Regionen und Endpunkte

Der Endpunkt für die REST-API für kurze Audiodaten weist das folgende Format auf:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersetzen Sie `<REGION_IDENTIFIER>` durch den Bezeichner aus der folgenden Tabelle, der mit der Region Ihres Abonnements übereinstimmt:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Der Sprachparameter muss an die URL angefügt werden, um HTTP Fehler des Typs „4xx“ zu vermeiden. Das folgende Beispiel zeigt die Spracheinstellung „Englisch (USA)“ bei Verwendung des Endpunkt „USA, Westen“: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="query-parameters"></a>Abfrageparameter

Diese Parameter können in der Abfragezeichenfolge der REST-Anforderung enthalten sein.

| Parameter | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `language` | Identifiziert die gesprochene Sprache, die erkannt wird. Siehe [Unterstützte Sprachen](language-support.md#speech-to-text). | Erforderlich |
| `format` | Gibt das Ergebnisformat an. Zulässige Werte sind `simple` und `detailed`. Einfache Ergebnisse enthalten `RecognitionStatus`, `DisplayText`, `Offset` und `Duration`. Ausführliche Antworten enthalten vier verschiedene Darstellungen des Anzeigetexts. Die Standardeinstellung ist `simple`. | Optional |
| `profanity` | Gibt den Umgang mit Obszönitäten in Erkennungsergebnissen an. Zulässige Werte sind `masked` (Obszönitäten werden durch Sternchen ersetzt), `removed` (Obszönitäten werden aus dem Ergebnis entfernt) und `raw` (Obszönitäten sind im Ergebnis enthalten). Die Standardeinstellung ist `masked`. | Optional |
| `cid` | Wenn Sie das [Custom Speech-Portal](./custom-speech-overview.md) zum Erstellen von benutzerdefinierten Modellen verwenden, können Sie benutzerdefinierte Modelle über ihre **Endpunkt-ID** verwenden, die Sie auf der Seite **Bereitstellung** finden. Verwenden Sie die **Endpunkt-ID** als Argument für den Parameter `cid` der Abfragezeichenfolge. | Optional |

### <a name="request-headers"></a>Anforderungsheader

In der folgenden Tabelle sind die erforderlichen und optionalen Header für Spracherkennungsanforderungen aufgeführt.

|Header| BESCHREIBUNG | Erforderlich/optional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Ihr Abonnementschlüssel des Spracherkennungsdiensts. | Entweder dieser Header oder `Authorization` ist erforderlich. |
| `Authorization` | Ein Autorisierungstoken, dem das Wort `Bearer` vorangestellt ist. Weitere Informationen finden Sie unter [Authentifizierung](#authentication). | Entweder dieser Header oder `Ocp-Apim-Subscription-Key` ist erforderlich. |
| `Pronunciation-Assessment` | Gibt die Parameter für das Anzeigen von Aussprachebewertungen in Erkennungsergebnissen an, mit denen die Aussprachequalität der Spracheingabe mit Indikatoren für Genauigkeit, Flüssigkeit, Vollständigkeit usw. bewertet wird. Dieser Parameter ist base64-codierter JSON-Code, der viele ausführliche Parameter enthält. Informationen zum Erstellen dieses Headers finden Sie unter [Parameter für die Aussprachebewertung](#pronunciation-assessment-parameters). | Optional |
| `Content-type` | Beschreibt das Format und den Codec der bereitgestellten Audiodaten. Zulässige Werte sind `audio/wav; codecs=audio/pcm; samplerate=16000` und `audio/ogg; codecs=opus`. | Erforderlich |
| `Transfer-Encoding` | Gibt an, dass segmentierte Audiodaten anstatt einer einzelnen Datei gesendet werden. Verwenden Sie diesen Header nur, wenn Sie Audiodaten segmentieren. | Optional |
| `Expect` | Wenn Sie segmentierte Übertragung verwenden, senden Sie `Expect: 100-continue`. Der Spracherkennungsdienst bestätigt die ursprüngliche Anforderung und wartet auf weitere Daten.| Erforderlich, wenn segmentierte Audiodaten gesendet werden. |
| `Accept` | Wenn angegeben, muss der Wert `application/json` entsprechen. Der Speech-Dienst übermittelt Ergebnisse im JSON-Format. Einige Anforderungsframeworks bieten einen inkompatiblen Standardwert. Es ist eine bewährte Methode, `Accept` immer einzubeziehen. | Optional, wird jedoch empfohlen. |

### <a name="audio-formats"></a>Audioformate

Audiodaten werden im Text der HTTP-`POST`-Anforderung gesendet. Sie müssen in einem der in der folgenden Tabelle aufgeführten Formate vorliegen:

| Format | Codec | Bitrate | Samplingrate  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 KBit/s | 16 kHz, mono |
| OGG    | OPUS  | 256 KBit/s | 16 kHz, mono |

>[!NOTE]
>Die oben aufgeführten Formate werden durch die REST-API für kurze Audiodaten und WebSocket im Speech-Dienst unterstützt. Das [Speech-SDK](speech-sdk.md) unterstützt gegenwärtig das WAV-Format mit dem PCM-Codec sowie [weitere Formate](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parameter für die Aussprachebewertung

In dieser Tabelle sind die erforderlichen und optionalen Parameter für die Aussprachebewertung aufgeführt.

| Parameter | BESCHREIBUNG | Erforderlich? |
|-----------|-------------|---------------------|
| ReferenceText | Der Text, für den die Aussprache ausgewertet wird. | Erforderlich |
| GradingSystem | Das Punktesystem zur Kalibrierung der Bewertung. Das `FivePoint`-System gibt eine 0-5-Gleitkommabewertung und `HundredMark` eine 0-100-Gleitkommabewertung aus. Standardwert: `FivePoint`. | Optional |
| Granularität | Die Granularität der Auswertung. Akzeptierte Werte sind `Phoneme`, für das die Bewertung auf Volltext-, Wort- und Phonemebene, `Word`, für das die Bewertung auf Volltext und Wortebene und `FullText`, für das nur die Bewertung auf der Volltextebene angezeigt wird. Die Standardeinstellung ist `Phoneme`. | Optional |
| Dimension | Definiert die Ausgabekriterien. Die akzeptierten Werte sind `Basic`, das nur die Genauigkeitsbewertung anzeigt, und `Comprehensive`, für das Bewertungen in weiteren Dimensionen angezeigt wird (z. B. Flüssigkeitsbewertung und Vollständigkeitsbewertung auf der Volltextebene, Fehlertyp auf der Wortebene). In den [Antwortparametern](#response-parameters) finden Sie Definitionen verschiedener Bewertungsdimensionen und Wortfehlertypen. Die Standardeinstellung ist `Basic`. | Optional |
| EnableMiscue | Aktiviert die Fehlschlagsberechnung. Wenn diese Option aktiviert ist, werden die ausgesprochenen Wörter mit dem Referenztext verglichen und auf der Grundlage des Vergleichs mit Auslassung/Einfügung gekennzeichnet. Zulässige Werte sind `False` und `True`. Die Standardeinstellung ist `False`. | Optional |
| ScenarioId | Eine GUID, die ein benutzerdefiniertes Punktesystem angibt. | Optional |

Unten finden Sie ein JSON-Beispiel mit den Parametern für die Aussprachebewertung:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Der folgende Beispielcode zeigt, wie die Parameter für die Aussprachebewertung in den Header `Pronunciation-Assessment` integriert werden:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Für die Veröffentlichung von Audiodaten wird dringend das Hochladen per Streaming (segmentiert) empfohlen, da damit die Latenz deutlich reduziert werden kann. Weitere Informationen zum Aktivieren des Streamings finden Sie im [Beispielcode in verschiedenen Programmiersprachen](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment).

>[!NOTE]
>Das Feature für die Aussprachebewertung ist derzeit nur in den Regionen `westus`, `eastasia` und `centralindia` verfügbar. Es steht außerdem zurzeit nur für die Sprache `en-US` zur Verfügung.

### <a name="sample-request"></a>Beispiel für eine Anforderung

Das folgende Beispiel enthält den Hostnamen und die erforderlichen Header. Beachten Sie, dass der Dienst auch Audiodaten erwartet, die in diesem Beispiel nicht enthalten sind. Wie bereits erwähnt, wird die Segmentierung empfohlen, ist aber nicht erforderlich.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

Sie können den unten angegebenen Header hinzufügen, um die Aussprachebewertung zu aktivieren. Informationen zum Erstellen dieses Headers finden Sie unter [Parameter für die Aussprachebewertung](#pronunciation-assessment-parameters).

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>HTTP-Statuscodes

Der HTTP-Statuscode jeder Antwort zeigt den Erfolg oder allgemeine Fehler an.

| HTTP-Statuscode | BESCHREIBUNG | Mögliche Ursache |
|------------------|-------------|-----------------|
| `100` | Continue | Die ursprüngliche Anforderung wurde akzeptiert. Mit dem Senden der restlichen Daten fortfahren. (Wird bei segmentierter Übertragung verwendet) |
| `200` | OK | Die Anforderung war erfolgreich. Der Antworttext ist ein JSON-Objekt. |
| `400` | Ungültige Anforderung | Der Sprachcode wurde nicht bereitgestellt, ist keine unterstützte Sprache, eine ungültige Audiodatei usw. |
| `401` | Nicht autorisiert | Der Abonnementschlüssel oder das Autorisierungstoken ist in der angegebenen Region ungültig oder ungültiger Endpunkt. |
| `403` | Verboten | Fehlender Abonnementschlüssel oder fehlendes Autorisierungstoken. |

### <a name="chunked-transfer"></a>Segmentierte Übertragung

Mithilfe der segmentierten Übertragung (`Transfer-Encoding: chunked`) kann die Erkennungslatenz verringert werden. Es ermöglicht dem Speech-Dienst, mit der Verarbeitung der Audiodatei zu beginnen, während sie übertragen wird. Die REST-API für kurze Audiodaten gibt keine Teil- oder Zwischenergebnisse zurück.

Dieses Codebeispiel zeigt, wie Sie Audio in Blöcken senden. Nur der erste Block sollte den Header der Audiodatei enthalten. `request` ist ein `HttpWebRequest`-Objekt, das mit dem entsprechenden REST-Endpunkt verbunden ist. `audioFile` ist der Pfad zu einer Audiodatei auf dem Datenträger.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Antwortparameter

Ergebnisse werden im JSON-Format bereitgestellt. Das `simple`-Format schließt diese Felder auf oberster Ebene ein.

| Parameter | BESCHREIBUNG  |
|-----------|--------------|
|`RecognitionStatus`|Status, z.B. `Success` für erfolgreiche Erkennung. Siehe nächste Tabelle.|
|`DisplayText`|Der erkannte Text nach Großschreibung, Interpunktion, inverser Textnormalisierung (Umwandlung von gesprochenem Text in kürzere Formen, z.B. 200 für „zweihundert“ oder „Dr. Smith“ für „doctor smith“) und Obszönitätenmaskierung. Nur bei Erfolg vorhanden.|
|`Offset`|Die Zeit (in Einheiten von 100 Nanosekunden), zu der die erkannte Sprache im Audiostream beginnt.|
|`Duration`|Die Dauer (in Einheiten von 100 Nanosekunden) der erkannten Sprache im Audiostream.|

Das `RecognitionStatus`-Feld kann diese Werte enthalten:

| Status | BESCHREIBUNG |
|--------|-------------|
| `Success` | Die Erkennung war erfolgreich, und das `DisplayText`-Feld ist vorhanden. |
| `NoMatch` | Im Audiodatenstrom wurde Sprache erkannt, aber es wurde keine Übereinstimmung mit Wörtern aus der Zielsprache festgestellt. Normalerweise bedeutet dies, dass die Erkennungssprache eine andere Sprache ist als die, die der Benutzer spricht. |
| `InitialSilenceTimeout` | Der Anfang des Audiodatenstroms enthielt nur Stille, und beim Warten auf Sprache wurde das Timeout des Diensts aktiviert. |
| `BabbleTimeout` | Der Anfang des Audiodatenstroms enthielt nur Rauschen, und beim Warten auf Sprache wurde das Timeout des Diensts aktiviert. |
| `Error` | Der Erkennungsdienst hat einen internen Fehler erkannt und konnte nicht fortgesetzt werden. Versuchen Sie es noch mal, wenn möglich. |

> [!NOTE]
> Wenn die Audiodaten nur aus Obszönitäten bestehen und der `profanity`-Abfrageparameter auf `remove` festgelegt ist, gibt der Dienst kein Sprachergebnis zurück.

Das Format `detailed` enthält zusätzliche Formen erkannter Ergebnisse.
Bei Verwendung des Formats `detailed` wird `DisplayText` als `Display` für jedes Ergebnis in der `NBest`-Liste angegeben.

Das Objekt in der `NBest`-Liste kann Folgendes enthalten:

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| `Confidence` | Die Zuverlässigkeitsbewertung des Eintrags von 0,0 (keine Zuverlässigkeit) bis 1,0 (volle Zuverlässigkeit) |
| `Lexical` | Die lexikalische Form des erkannten Texts: die tatsächlich erkannten Wörter. |
| `ITN` | Die inverse Textnormalisierung („kanonische Form“) des erkannten Texts mit Telefonnummern, Zahlen, Abkürzungen („doctor smith“ in „dr. smith“) und anderen angewendeten Transformationen. |
| `MaskedITN` | Die ITN-Form mit angewendeter Obszönitätenmaskierung, wenn angefordert. |
| `Display` | Die Anzeigeform des erkannten Texts mit hinzugefügten Satzzeichen und Großschreibung. Dieser Parameter ist derselbe wie `DisplayText`, wenn das Format `simple` entspricht. |
| `AccuracyScore` | Genauigkeit der Aussprache des Texts. Genauigkeit heißt dabei, wie exakt Phoneme der Aussprache eines Muttersprachlers entsprechen. Der Score für Genauigkeit auf Wort- und Volltextebene wird aus dem Score der Genauigkeit auf Phonemebene aggregiert. |
| `FluencyScore` | Redefluss eines gegebenen Texts. Der Redefluss bedeutet, wie exakt der ausgegebene Text mit den Pausen zwischen Wörtern eines Muttersprachlers übereinstimmt. |
| `CompletenessScore` | Vollständigkeit des Texts. Dafür wird das Verhältnis ausgesprochener Wörter zur Texteingabe berechnet. |
| `PronScore` | Der Gesamtscore, der die Aussprachequalität des übergebenen Sprachtexts angibt. Dieser wird aus `AccuracyScore`, `FluencyScore` und `CompletenessScore` mit Gewichtung aggregiert. |
| `ErrorType` | Dieser Wert gibt an, ob ein Wort im Vergleich zu `ReferenceText`ausgelassen, eingefügt oder schlecht ausgesprochen wird. Die möglichen Werte sind `None` (d. h. kein Fehler in diesem Wort), `Omission`, `Insertion` und `Mispronunciation`. |

### <a name="sample-responses"></a>Beispielantworten

Eine typische Antwort für die `simple` Erkennung:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Eine typische Antwort für die `detailed` Erkennung:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

Eine typische Antwort für Erkennung mit Bewertung der Aussprache:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie ein kostenloses Azure-Konto.](https://azure.microsoft.com/free/cognitive-services/)
- [Tutorial: Erstellen eines benutzerdefinierten Akustikmodells](./how-to-custom-speech-train-model.md)
- [Tutorial: Erstellen eines benutzerdefinierten Sprachmodells](./how-to-custom-speech-train-model.md)
- [Machen Sie sich mit der Batchtranskription vertraut.](batch-transcription.md)