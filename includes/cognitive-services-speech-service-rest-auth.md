---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 410b73d3f9011f9384fafa18394d9318e3eafa67
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504490"
---
## <a name="authentication"></a>Authentication

Jede Anforderung erfordert einen Autorisierungsheader. Diese Tabelle zeigt, welche Header für welchen Dienst unterstützt werden:

| Unterstützte Autorisierungsheader | Spracherkennung | Text-zu-Sprache |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ja | Nein |
| Authorization: Bearer | Ja | Ja |

Wenn Sie den Header `Ocp-Apim-Subscription-Key` verwenden, müssen Sie nur Ihren Abonnementschlüssel angeben. Beispiel:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Wenn Sie den Header `Authorization: Bearer` verwenden, müssen Sie eine Anforderung an den Endpunkt `issueToken` stellen. In dieser Anforderung tauschen Sie Ihren Abonnementschlüssel gegen ein Zugriffstoken, der 10 Minuten lang gültig ist. In den nächsten Abschnitten erfahren Sie, wie Sie ein Token abrufen und verwenden können.

### <a name="how-to-get-an-access-token"></a>Abrufen eines Zugriffstokens

Um ein Zugriffstoken abzurufen, müssen Sie eine Anforderung an den `issueToken`-Endpunkt mit dem Header `Ocp-Apim-Subscription-Key` und Ihrem Abonnementschlüssel senden.

Der `issueToken`-Endpunkt hat folgendes Format:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Ersetzen Sie `<REGION_IDENTIFIER>` durch den Bezeichner aus der folgenden Tabelle, der mit der Region Ihres Abonnements übereinstimmt:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Verwenden Sie diese Beispiele, um Ihre Zugriffstokenanforderung zu erstellen.

#### <a name="http-sample"></a>HTTP-Beispiel

Dieses Beispiel stellt eine einfache HTTP-Anforderung zum Abrufen eines Tokens dar. Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` durch Ihren Abonnementschlüssel für den Speech-Dienst. Wenn sich Ihr Abonnement nicht in der Region „USA, Westen“ befindet, ersetzen Sie den `Host`-Header durch den Hostnamen für Ihre Region.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Der Antworttext enthält das Zugriffstoken im JWT-Format (JSON Web Token).

#### <a name="powershell-sample"></a>PowerShell-Beispiel

Dieses Beispiel stellt ein einfaches PowerShell-Skript zum Abrufen eines Zugriffstokens dar. Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` durch Ihren Abonnementschlüssel für den Speech-Dienst. Achten Sie darauf, dass Sie den richtigen Endpunkt für die Region Ihres Abonnements verwenden. In diesem Beispiel ist das „USA, Westen“.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL-Beispiel

cURL ist ein Befehlszeilentool, das in Linux (und im Windows-Subsystem für Linux) zur Verfügung steht. Dieser cURL-Befehl veranschaulicht, wie Sie ein Zugriffstoken abrufen. Ersetzen Sie `YOUR_SUBSCRIPTION_KEY` durch Ihren Abonnementschlüssel für den Speech-Dienst. Achten Sie darauf, dass Sie den richtigen Endpunkt für die Region Ihres Abonnements verwenden. In diesem Beispiel ist das „USA, Westen“.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-Beispiel

Diese C#-Klasse veranschaulicht, wie Sie ein Zugriffstoken abrufen. Übergeben Sie Ihren Abonnementschlüssel für den Speech-Dienst beim Instanziieren der Klasse. Wenn Ihr Abonnement sich nicht in der Region „USA, Westen“ befindet, ändern Sie den Wert von `FetchTokenUri` so, dass er der Region Ihres Abonnements entspricht.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python-Beispiel

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Verwenden eines Zugriffstokens

Das Zugriffstoken sollte als `Authorization: Bearer <TOKEN>`-Header an den Dienst gesendet werden. Jedes Zugriffstoken ist 10 Minuten lang gültig. Sie können jederzeit ein neues Token abrufen, allerdings wird empfohlen, das gleiche Token 9 Minuten lang zu verwenden, um den Datenverkehr und die Wartezeit zu minimieren.

Hier finden Sie eine HTTP-Beispielanforderung von kurzem Audiomaterial an die Sprache-in-Text-REST-API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
