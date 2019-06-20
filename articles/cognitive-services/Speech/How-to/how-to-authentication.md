---
title: Authentifizieren bei der Bing-Spracheingabe | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Anfordern einer Authentifizierung für die Verwendung der Bing-Spracheingabe-API
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 11d6256fb63452b849a80abab181876d14b3b6a6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515054"
---
# <a name="authenticate-to-the-speech-api"></a>Authentifizieren bei der Sprach-API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Die Bing-Spracheingabe unterstützt folgende Authentifizierungsmethoden:

- Abonnementschlüssel
- Autorisierungstoken

## <a name="use-a-subscription-key"></a>Verwenden eines Abonnementschlüssels

Um den Speech-Dienst verwenden zu können, müssen Sie zunächst die Sprach-API aus Cognitive Services (ehemals Projekt Oxford) abonnieren. Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) können Sie Schlüssel für ein kostenloses Probeabonnement abrufen. Wählen Sie zunächst die Option für die Sprach-API aus, und klicken Sie anschließend auf **API-Schlüssel abrufen**, um den Schlüssel abzurufen. Daraufhin werden ein Primär- und ein Sekundärschlüssel zurückgegeben. Beide Schlüssel sind an das gleiche Kontingent gebunden. Es spielt daher keine Rolle, welchen Schlüssel Sie verwenden.

Registrieren Sie sich für ein [Azure-Konto](https://azure.microsoft.com/free/), um Cognitive Services langfristig zu verwenden oder das Kontingent zu erhöhen.

Um die Sprach-REST-API verwenden zu können, müssen Sie den Abonnementschlüssel im Feld `Ocp-Apim-Subscription-Key` des Anforderungsheaders übergeben.

NAME| Format| BESCHREIBUNG
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Hier sehen Sie ein Beispiel für einen Anforderungsheader:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Wenn Sie in Ihrer Anwendung [Clientbibliotheken](../GetStarted/GetStartedClientLibraries.md) verwenden, vergewissern Sie sich, dass Sie das Autorisierungstoken wie im folgenden Abschnitt beschrieben mit Ihrem Abonnementschlüssel abrufen können. Die Clientbibliotheken verwenden den Abonnementschlüssel, um ein Autorisierungstoken für die Authentifizierung zu erhalten.

## <a name="use-an-authorization-token"></a>Verwenden eines Autorisierungstokens

Alternativ können Sie für die Authentifizierung ein Autorisierungstoken als Authentifizierungs-/Autorisierungsbeleg verwenden. Um dieses Token abzurufen, müssen Sie zunächst einen Abonnementschlüssel von der Sprach-API beziehen, wie im [vorherigen Abschnitt](#use-a-subscription-key) beschrieben.

### <a name="get-an-authorization-token"></a>Abrufen eines Autorisierungstokens

Wenn Sie über einen gültigen Abonnementschlüssel verfügen, senden Sie eine POST-Anforderung an den Tokendienst von Cognitive Services. In der Antwort erhalten Sie das Autorisierungstoken als JSON-Webtoken (JWT).

> [!NOTE]
> Das Token ist zehn Minuten lang gültig. Informationen zum Verlängern der Tokengültigkeit finden Sie im folgenden Abschnitt.

Der Tokendienst-URI befindet sich hier:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Das folgende Codebeispiel veranschaulicht die Vorgehensweise zum Abrufen eines Zugriffstokens. Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` durch Ihren eigenen Abonnementschlüssel:

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

Das Beispiel verwendet curl unter Linux mit Bash. Sollte curl auf Ihrer Plattform nicht verfügbar sein, müssen Sie es möglicherweise installieren. Das Beispiel kann auch mit Cygwin unter Windows, Git Bash, zsh und mit anderen Shells verwendet werden.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Das folgende Beispiel zeigt eine exemplarische POST-Anforderung:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Falls Sie vom Tokendienst kein Autorisierungstoken abrufen können, überprüfen Sie die Gültigkeit Ihres Abonnementschlüssels. Klicken Sie bei Verwendung eines kostenlosen Testschlüssels auf der Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/) auf „Anmelden“, melden Sie sich mit dem Konto an, das Sie bei der Anforderung des kostenlosen Testschlüssels verwendet haben, und überprüfen Sie, ob der Abonnementschlüssel abgelaufen ist oder das Kontingent überschritten wurde.

### <a name="use-an-authorization-token-in-a-request"></a>Verwenden eines Autorisierungstokens in einer Anforderung

Das Autorisierungstoken muss bei jedem Aufruf der Sprach-API im Header `Authorization` übergeben werden. Der Header `Authorization` muss ein JWT-Zugriffstoken enthalten.

Das folgende Beispiel veranschaulicht die Verwendung eines Autorisierungstokens beim Aufrufen der Sprach-REST-API.

> [!NOTE]
> Ersetzen Sie `YOUR_AUDIO_FILE` durch den Pfad Ihrer aufgezeichneten Audiodatei. Ersetzen Sie `YOUR_ACCESS_TOKEN` durch das Autorisierungstoken, das Sie im vorherigen Schritt ([Abrufen eines Autorisierungstokens](#get-an-authorization-token)) erhalten haben.

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

### <a name="renew-an-authorization-token"></a>Verlängern der Gültigkeit eines Autorisierungstokens

Das Autorisierungstoken ist nur für einen bestimmten Zeitraum (aktuell zehn Minuten) gültig. Die Gültigkeit des Autorisierungstokens muss vor Ablauf dieses Zeitraums verlängert werden.

Der folgende Code ist eine in C# geschriebene Beispielimplementierung zur Verlängerung der Gültigkeit des Autorisierungstokens:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
