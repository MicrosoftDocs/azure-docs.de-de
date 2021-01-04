---
title: Senden von Anforderungen an die Bing-Vorschlagssuche-API
titleSuffix: Azure Cognitive Services
description: Die Bing-Vorschlagssuche-API gibt eine Liste vorgeschlagener Abfragen basierend auf der unvollständigen Abfragezeichenfolge im Suchfeld zurück. Erfahren Sie mehr über das Senden von Anforderungen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: dd845c0fb877afa76b84eb5c2d86392f763eccf7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353390"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Senden von Anforderungen an die Bing-Vorschlagssuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Wenn Ihre Anwendung Abfragen an eine der Bing-Suche-APIs senden, können Sie die Bing-Vorschlagssuche-API verwenden, um die Suchumgebung des Benutzers zu verbessern. Die Bing-Vorschlagssuche-API gibt eine Liste vorgeschlagener Abfragen basierend auf der unvollständigen Abfragezeichenfolge im Suchfeld zurück. Bei der Eingabe von Zeichen in das Suchfeld Ihrer Anwendung können Sie Vorschläge in einer Dropdownliste anzeigen. In diesem Artikel erfahren Sie mehr über das Senden von Anforderungen an diese API. 

## <a name="bing-autosuggest-api-endpoint"></a>Endpunkt der Bing-Vorschlagssuche-API

Die **Bing-Vorschlagssuche-API** enthält einen Endpunkt, der für einen Teil des Suchbegriffs eine Liste mit vorgeschlagenen Abfragen zurückgibt.

Um mithilfe der Bing-API vorgeschlagene Abfragen zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt. Verwenden Sie die Header und die URL-Parameter, um Spezifikationen genauer zu definieren.

**Endpunkt:** Gibt Suchvorschläge als JSON-Ergebnisse zurück, die für die mit `?q=""` definierte Eingabe des Benutzers relevant sind.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie in der Referenz [Bing-Vorschlagssuche-API V7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference).

Die **Bing**-APIs unterstützen Suchaktionen, die Ergebnisse gemäß ihrem Typ zurückgeben.  Alle Suchendpunkte geben Ergebnisse als JSON-Antwortobjekte zurück.
Alle Endpunkte unterstützen Abfragen, die eine bestimmte Sprache und/oder einen bestimmten Ort nach Längengrad, Breitengrad und Suchradius zurückgeben.

Die umfassenden Informationen zu den Parametern, die von jedem Endpunkt unterstützt werden, finden Sie auf den Referenzseiten des jeweiligen Typs.
Beispiele für grundlegende Anforderungen, die die Vorschlagssuche-API verwenden, finden Sie unter [Schnellstarts für die Vorschlagssuche](/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Anforderungen für die Bing-Vorschlagssuche-API

> [!NOTE]
> * Bei Anforderungen für die Bing-Vorschlagssuche-API muss das HTTPS-Protokoll verwendet werden.

Alle Anforderungen sollten von einem Server stammen. Wird der Schlüssel zusammen mit einer Clientanwendung verteilt, erhöht sich das Risiko, dass eine böswillige dritte Partei darauf zugreift. Aufrufe über einen Server ermöglichen zudem die Verwendung eines einzelnen Upgradepunkts für zukünftige Updates.

Die Anforderung muss den Abfrageparameter [q](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) angeben, der den partiellen Suchbegriff des Benutzers enthält. Obwohl er optional ist, sollte die Anforderung auch den Abfrageparameter [mkt](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) angeben, wodurch der Markt identifiziert wird, von dem die Ergebnisse stammen sollen. Unter [Abfrageparameter](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters) finden Sie eine Liste optionaler Abfrageparameter. Alle Abfrageparameterwerte müssen URL-codiert sein.

Die Anforderung muss den [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey)-Header angeben. Auch wenn dies optional ist, sollten Sie die folgenden Header ebenfalls angeben:

- [User-Agent](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Die Client-IP- und Adressheader sind für die Rückgabe von standortorientiertem Inhalt wichtig.

Eine Liste aller Anforderungs- und Antwortheader finden Sie unter [Header](/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> Wenn Sie die Bing-Vorschlagssuche-API über JavaScript aufrufen, verhindern die integrierten Sicherheitsfunktionen Ihres Browsers möglicherweise den Zugriff auf die Werte dieser Header.

Um dieses Problem zu lösen, können Sie die Anforderung der Bing-Vorschlagssuche-API über einen CORS-Proxy senden. In der Antwort eines solchen Proxys befindet sich ein `Access-Control-Expose-Headers`-Header, mit dem die Antwortheader gefiltert und für JavaScript verfügbar gemacht werden.

Die Installation eines CORS-Proxys, mit dem die [Tutorial-App](../tutorials/autosuggest.md) auf die optionalen Clientheader zugreifen kann, ist schnell und unkompliziert. [Installieren Sie Node.js](https://nodejs.org/en/download/), falls Sie dies noch nicht getan haben. Geben Sie dann an einer Eingabeaufforderung den folgenden Befehl ein.

```console
npm install -g cors-proxy-server
```

Passen Sie den Endpunkt der Bing-Vorschlagssuche-API in der HTML-Datei wie folgt an:

```http
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
```

Starten Sie abschließend den CORS-Proxy mit folgendem Befehl:

```console
cors-proxy-server
```

Lassen Sie das Fenster während der Nutzung der Tutorial-App geöffnet. Wenn Sie das Fenster schließen, wird auch die Ausführung des Proxys beendet. Im Bereich mit den erweiterbaren HTTP-Headern unter den Suchergebnissen wird nun u.a. der `X-MSEdge-ClientID`-Header angezeigt. Hier können Sie überprüfen, ob dieser für alle Anforderungen identisch ist.

Anforderungen sollten alle vorgeschlagenen Abfrageparameter und Header enthalten. 

Im folgenden Beispiel wird eine Anforderung gezeigt, die die vorgeschlagene Abfragezeichenfolgen für *sail* zurückgibt.

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie den Client-ID-Header nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat.

Die folgende Webvorschlagsgruppe ist eine Antwort auf die oben genannte Anforderung. Die Gruppe enthält eine Liste von Vorschlägen für Suchanfragen, wobei jeder Vorschlag ein `displayText`-, `query`- und `url`-Feld enthält.

Das `displayText`-Feld enthält die vorgeschlagene Abfrage, die Sie zum Auffüllen der Dropdownliste des Suchfelds verwenden. Sie müssen alle in der Antwort enthaltenen Vorschläge in der angegebenen Reihenfolge anzeigen.  

Wenn der Benutzer eine Abfrage aus der Dropdownliste auswählt, können Sie damit eine der [Bing-Suche-API](../../bing-web-search/bing-api-comparison.md?bc=%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fen-us%2fazure%2fcognitive-services%2fbing-autosuggest%2ftoc.json) aufrufen und die Ergebnisse selbst anzeigen oder den Benutzer unter Verwendung des zurückgegebenen Felds `url` zur Bing-Ergebnisseite weiterleiten.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Worum handelt es sich bei der Bing-Vorschlagssuche?](../get-suggested-search-terms.md)
- [Referenz für die Bing-Vorschlagssuche-API V7](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Abrufen vorgeschlagener Suchbegriffe über die Bing-Vorschlagssuche-API](get-suggestions.md)