---
title: Beheben von Problemen bei der Bing-Spracheingabe | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Probleme bei der Verwendung der Bing-Spracheingabe beheben.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e70e7b79be7dd4ea55c56898eaf8007d25732366
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60513986"
---
# <a name="troubleshooting-bing-speech"></a>Beheben von Problemen bei der Bing-Spracheingabe

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Fehler `HTTP 403 Forbidden`

Bei Verwendung der Spracherkennungs-API wird der Fehler `HTTP 403 Forbidden` zurückgegeben.

### <a name="cause"></a>Ursache

Dieser Fehler wird häufig durch Authentifizierungsprobleme verursacht. Verbindungsanforderungen ohne gültigen Header vom Typ `Authorization` oder `HTTP 403 Forbidden` werden vom Dienst mit der Antwort `Ocp-Apim-Subscription-Key` abgelehnt.

Wenn Sie einen Abonnementschlüssel für die Authentifizierung verwenden, kann eine der folgenden Ursachen vorliegen:

- Der Abonnementschlüssel fehlt oder ist ungültig.
- Das Nutzungskontingent des Abonnementschlüssels wurde überschritten.
- Bei REST-API-Aufrufen wurde das Feld `Ocp-Apim-Subscription-Key` im Anforderungsheader nicht festgelegt.

Wenn Sie ein Autorisierungstoken für die Authentifizierung verwenden, kann eine der folgenden Fehlerursachen vorliegen:

- Der Header `Authorization` ist in der Anforderung nicht vorhanden (bei Verwendung von REST).
- Das im Autorisierungsheader angegebene Autorisierungstoken ist ungültig.
- Das Autorisierungstoken ist abgelaufen. Das Zugriffstoken ist nur zehn Minuten lang gültig.

Weitere Informationen zur Authentifizierung finden Sie [hier](How-to/how-to-authentication.md).

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

#### <a name="verify-that-your-subscription-key-is-valid"></a>Überprüfen der Gültigkeit des Abonnementschlüssels

Für die Überprüfung können Sie den folgenden Befehl ausführen. Ersetzen Sie dabei *YOUR_SUBSCRIPTION_KEY* durch Ihren eigenen Abonnementschlüssel. Wenn Ihr Abonnementschlüssel gültig ist, erhalten Sie in der Antwort das Autorisierungstoken als JSON-Webtoken (JWT). Andernfalls wird ein Fehler zurückgegeben.

> [!NOTE]
> Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` durch Ihren eigenen Abonnementschlüssel.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Das Beispiel verwendet curl unter Linux mit Bash. Sollte curl auf Ihrer Plattform nicht verfügbar sein, müssen Sie es möglicherweise installieren. Das Beispiel sollte auch mit Cygwin unter Windows, Git Bash, zsh und mit anderen Shells funktionieren.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Vergewissern Sie sich, dass Sie in Ihrer Anwendung oder in der REST-Anforderung den gleichen Abonnementschlüssel verwenden wie weiter oben.

#### <a name="verify-the-authorization-token"></a>Überprüfen des Authentifizierungstokens

Dieser Schritt ist nur erforderlich, wenn Sie zur Authentifizierung ein Autorisierungstoken verwenden. Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass das Autorisierungstoken noch gültig ist. Der Befehl sendet eine POST-Anforderung an den Dienst und erwartet eine Antwortnachricht. Sollte weiterhin ein HTTP-Fehler vom Typ `403 Forbidden` zurückgegeben werden, vergewissern Sie sich, dass das Zugriffstoken korrekt festgelegt und noch nicht abgelaufen ist.

> [!IMPORTANT]
> Das Token ist zehn Minuten lang gültig.
> [!NOTE]
> Ersetzen Sie `YOUR_AUDIO_FILE` durch den Pfad zu Ihrer aufgezeichneten Audiodatei und `YOUR_ACCESS_TOKEN` durch das im vorherigen Schritt zurückgegebene Autorisierungstoken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>Fehler `HTTP 400 Bad Request`

Dieser Fehler ist häufig auf ungültige Audiodaten im Anforderungstext zurückzuführen. Aktuell werden nur WAV-Dateien unterstützt.

## <a name="error-http-408-request-timeout"></a>Fehler `HTTP 408 Request Timeout`

Der Fehler ist wahrscheinlich darauf zurückzuführen, dass keine Audiodaten an den Dienst gesendet wurden, und wird nach dem Timeout zurückgegeben. Bei Verwendung der REST-API müssen sich die Audiodaten im Anforderungstext befinden.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` in der Antwort lautet `InitialSilenceTimeout`.

Das Problem ist in der Regel auf Audiodaten zurückzuführen. Beispiel:

- Am Anfang der Audiodaten ist es lange still. Der Dienst beendet die Erkennung nach einigen Sekunden und gibt `InitialSilenceTimeout` zurück.
- Die Audiodaten werden aufgrund eines nicht unterstützten Codec-Formats als Stille behandelt.
