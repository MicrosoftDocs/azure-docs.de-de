---
title: Senden von Anforderungen an die Bing-Rechtschreibprüfungs-API
titlesuffix: Azure Cognitive Services
description: Informationen zu den Modi und Einstellungen der Bing-Rechtschreibprüfung sowie weitere Informationen im Zusammenhang mit der API
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: e9f691147b5dcb12a48a632ff01880f3fdff89c5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390262"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Senden von Anforderungen an die Bing-Rechtschreibprüfungs-API

Um eine Textzeichenfolge auf Rechtschreib- und Grammatikfehler zu überprüfen, senden Sie eine GET-Anforderung an den folgenden Endpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
Die Anforderung muss das HTTPS-Protokoll verwenden.

Alle Anforderungen sollten von einem Server stammen. Das Verteilen eines Schlüssels als Teil einer Clientanwendung birgt ein größeres Risiko, dass eine böswillige dritte Partei darauf zugreift. Ein Server stellt zudem einen einzelnen Upgradepunkt für zukünftige Versionen der API dar.

Die Anforderung muss den [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text)-Abfrageparameter angeben, der die zu korrigierende Textzeichenfolge enthält. Obwohl er optional ist, sollte die Anforderung auch den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt)-Abfrageparameter angeben, wodurch der Markt identifiziert wird, von dem die Ergebnisse stammen sollen. Unter [Abfrageparameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters) finden Sie eine Liste optionaler Abfrageparameter wie `mode`. Alle Abfrageparameterwerte müssen URL-codiert sein.  
  
Die Anforderung muss den [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey)-Header angeben. Auch wenn dies optional ist, sollten Sie die folgenden Header ebenfalls angeben. Mit diesen Headern kann die Bing-Rechtschreibprüfungs-API genauere Ergebnisse zurückgeben:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Eine Liste aller Anforderungs- und Antwortheader finden Sie unter [Header](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

Beim Aufrufen der Bing-Rechtschreibprüfungs-API über JavaScript verhindern jedoch die Sicherheitsfunktionen Ihres Browsers möglicherweise den Zugriff auf die Werte dieser Header.

Um dieses Problem zu lösen, können Sie die Anforderung der Bing-Rechtschreibprüfungs-API über einen CORS-Proxy senden. In der Antwort eines solchen Proxys befindet sich ein `Access-Control-Expose-Headers`-Header. Dieser enthält eine Whitelist mit Antwortheadern und stellt diese JavaScript zur Verfügung.

Die Installation eines CORS-Proxys, mit dem die [Tutorial-App](../tutorials/spellcheck.md) auf die optionalen Clientheader zugreifen kann, ist schnell und unkompliziert. [Installieren Sie Node.js](https://nodejs.org/en/download/), falls Sie dies noch nicht getan haben. Geben Sie dann an einer Eingabeaufforderung den folgenden Befehl ein.

    npm install -g cors-proxy-server

Passen Sie den Endpunkt der Bing-Rechtschreibprüfungs-API in der HTML-Datei wie folgt an:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Starten Sie abschließend den CORS-Proxy mit folgendem Befehl:

    cors-proxy-server

Lassen Sie das Befehlsfenster während der Nutzung der Tutorial-App geöffnet. Wenn Sie das Fenster schließen, wird auch die Ausführung des Proxys beendet. Im Abschnitt mit den erweiterbaren HTTP-Headern unterhalb der Suchergebnisse wird nun u.a. der `X-MSEdge-ClientID`-Header angezeigt. Hier können Sie überprüfen, ob dieser für alle Anforderungen identisch ist.

## <a name="example-api-request"></a>Beispiel für API-Anforderung

Im Folgenden ist eine Anforderung dargestellt, die alle vorgeschlagenen Abfrageparameter und -header enthält. Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat. 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Im Beispiel sind auch die Bing-spezifischen Antwortheader aufgeführt.

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

# <a name="next-steps"></a>Nächste Schritte

- [Worum handelt es sich bei der Bing-Rechtschreibprüfungs-API?](../overview.md)
- [Referenz zur Bing-Rechtschreibprüfungs-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
