---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 9cad860b8808dd2682995768c282d8376ab5d9be
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178378"
---
## <a name="authentication"></a>Authentication

Jede Anforderung erfordert einen Autorisierungsheader. Diese Tabelle zeigt, welche Header für welchen Dienst unterstützt werden:

| Unterstützte Autorisierungsheader | Spracherkennung | Text-zu-Sprache |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ja | Nein |
| Autorisierung: Bearer | Ja | Ja |

Wenn Sie den Header `Ocp-Apim-Subscription-Key` verwenden, müssen Sie nur Ihren Abonnementschlüssel angeben. Beispiel:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Wenn Sie den Header `Authorization: Bearer` verwenden, müssen Sie eine Anforderung an den Endpunkt `issueToken` stellen. In dieser Anforderung tauschen Sie Ihren Abonnementschlüssel gegen ein Zugriffstoken, der 10 Minuten lang gültig ist. In den nächsten Abschnitten erfahren Sie, wie Sie ein Token abrufen, verwenden und aktualisieren können.


### <a name="how-to-get-an-access-token"></a>Abrufen eines Zugriffstokens

Um ein Zugriffstoken abzurufen, müssen Sie eine Anforderung an den `issueToken`-Endpunkt mit dem Header `Ocp-Apim-Subscription-Key` und Ihrem Abonnementschlüssel senden.

Diese Regionen und Endpunkte werden unterstützt:

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

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

```Powershell
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

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C#-Beispiel

Diese C#-Klasse veranschaulicht, wie Sie ein Zugriffstoken abrufen. Übergeben Sie Ihren Abonnementschlüssel für den Speech-Dienst beim Instanziieren der Klasse. Wenn Ihr Abonnement sich nicht in der Region „USA, Westen“ befindet, ändern Sie den Wert von `FetchTokenUri` so, dass er der Region Ihres Abonnements entspricht.

```cs
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

Hier ist eine Beispiel-HTTP-Anforderung an die Text-to-Speech-REST-API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
