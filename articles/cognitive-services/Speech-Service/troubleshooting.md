---
title: Beheben von Problemen mit dem Speech SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Informationen zur Lösung von Problemen, die bei Verwendung des Spracherkennungs-SDK auftreten können.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: a3741cb351b11b8cfd7c5d38713bb71232cf010e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515452"
---
# <a name="troubleshoot-the-speech-sdk"></a>Problembehandlung für das Speech SDK

Dieser Artikel enthält Informationen zur Lösung von Problemen, die bei Verwendung des Spracherkennungs-SDK auftreten können.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fehler Authentifizierungsfehler beim WebSocket-Upgrade (403)

Sie verwenden möglicherweise den falschen Endpunkt für Ihre Region oder den Dienst. Prüfen Sie den URI, um sicherzustellen, dass er korrekt ist.

Außerdem kann ein Problem mit Ihrem Abonnementschlüssel oder Autorisierungstoken auftreten. Weitere Informationen finden Sie im nächsten Abschnitt.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Fehler „HTTP 403 Verboten“ oder „HTTP 401 Nicht autorisiert“

Dieser Fehler wird häufig durch Authentifizierungsprobleme verursacht. Verbindungsanforderungen ohne gültigen `Ocp-Apim-Subscription-Key` oder `Authorization`-Header werden mit dem Status 403 oder 401 abgelehnt.

* Wenn Sie einen Abonnementschlüssel für die Authentifizierung verwenden, wird der Fehler möglicherweise aus folgendem Grund angezeigt:

    - Der Abonnementschlüssel fehlt oder ist ungültig.
    - Sie haben das Nutzungskontingent Ihres Abonnements überschritten.

* Wenn Sie ein Authentifizierungstoken für die Authentifizierung verwenden, wird der Fehler möglicherweise aus folgendem Grund angezeigt:

    - Das Autorisierungstoken ist ungültig.
    - Das Autorisierungstoken ist abgelaufen.

### <a name="validate-your-subscription-key"></a>Überprüfen des Abonnementschlüssels

Sie können überprüfen, ob Sie über einen gültigen Abonnementschlüssel verfügen, indem Sie einen der unten aufgeführten Befehle ausführen.

> [!NOTE]
> Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` und `YOUR_REGION` durch den eigenen Abonnementschlüssel bzw. die zugehörige Region.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Wenn Sie einen gültigen Abonnementschlüssel eingegeben haben, gibt der Befehl ein Autorisierungstoken zurück, andernfalls wird ein Fehler zurückgegeben.

### <a name="validate-an-authorization-token"></a>Überprüfen eines Autorisierungstokens

Wenn Sie ein Autorisierungstoken für die Authentifizierung verwenden, führen Sie einen der folgenden Befehle aus, um sicherzustellen, dass das Autorisierungstoken noch gültig ist. Token sind 10 Minuten lang gültig.

> [!NOTE]
> Ersetzen Sie `YOUR_AUDIO_FILE` durch den Pfad Ihrer aufgezeichneten Audiodatei. Ersetzen Sie `YOUR_ACCESS_TOKEN` durch das im vorherigen Schritt zurückgegebene Autorisierungstoken. Ersetzen Sie `YOUR_REGION` durch die richtige Region.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Wenn Sie ein gültiges Authentifizierungstoken eingegeben haben, gibt der Befehl das Transkript Ihrer Audiodatei zurück, andernfalls wird ein Fehler zurückgegeben.

---

## <a name="error-http-400-bad-request"></a>Fehler HTTP 400 Ungültige Anforderung

Dieser Fehler tritt gewöhnlich auf, wenn der Anforderungstext ungültige Audiodaten enthält. Nur das WAV-Format wird unterstützt. Prüfen Sie auch die Anforderungsheader, um sicherzustellen, dass geeignete Werte für `Content-Type` und `Content-Length` angegeben sind.

## <a name="error-http-408-request-timeout"></a>Fehler HTTP 408 Anforderungstimeout

Der Fehler tritt wahrscheinlich auf, weil keine Audiodaten an den Dienst gesendet werden. Dieser Fehler kann auch durch Netzwerkprobleme verursacht werden.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>„RecognitionStatus“ in der Antwort ist „InitialSilenceTimeout“

Dieses Problem wird in der Regel durch Audiodaten verursacht. Dieser Fehler wird möglicherweise aus folgenden Gründen angezeigt:

* Zu Beginn einer Audiodatei ist es über einen längeren Zeitraum still. In diesem Fall beendet der Dienst die Erkennung nach einigen Sekunden und gibt `InitialSilenceTimeout` zurück.

* Die Audiodatei verwendet ein nicht unterstütztes Codec-Format, wodurch die Audiodaten als Stille behandelt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Lesen Sie die Anmerkungen zu dieser Version](releasenotes.md)
