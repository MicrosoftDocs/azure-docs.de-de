---
title: 'Tutorial: Erstellen einer einseitigen Web-App – Bing-Bildersuche-API'
titleSuffix: Azure cognitive services
description: Mit der Bing-Bildersuche-API können Sie im Web nach relevanten Bildern in hoher Qualität suchen. Erstellen Sie mithilfe dieses Tutorials eine einseitige Webanwendung, die Suchabfragen an die API senden und die Ergebnisse auf der Webseite anzeigen kann.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: bcb82ffcf4c4ad861c6523f507c816c666a71772
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351383"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Tutorial: Erstellen einer einseitigen Web-App mit der Bing-Bildersuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mit der Bing-Bildersuche-API können Sie im Web nach relevanten Bildern in hoher Qualität suchen. Erstellen Sie mithilfe dieses Tutorials eine einseitige Webanwendung, die Suchabfragen an die API senden und die Ergebnisse auf der Webseite anzeigen kann. Dieses Tutorial ähnelt dem [entsprechenden Tutorial für die Bing-Websuche](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md).

In dieser Tutorial-App wird Folgendes veranschaulicht:

> [!div class="checklist"]
> * Aufrufen der Bing-Bildersuche-API in JavaScript
> * Verbessern von Suchergebnissen mithilfe von Suchoptionen
> * Anzeigen und Durchblättern von Suchergebnissen
> * Anfordern und Verwalten eines API-Abonnementschlüssels und einer Bing-Client-ID

Den vollständigen Quellcode für dieses Tutorial finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Voraussetzungen

* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Das [Express.js](https://expressjs.com/)-Framework für Node.js. Die Installationsanweisungen für den Quellcode finden Sie in der Infodatei des GitHub-Beispiels.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Verwalten und Speichern von Benutzerabonnementschlüsseln

Diese Anwendung verwendet den beständigen Speicher des Webbrowsers zum Speichern von API-Abonnementschlüsseln. Wenn kein Schlüssel gespeichert ist, fordert die Webseite den Benutzer zur Eingabe seines Schlüssels auf. Dieser wird dann für die spätere Verwendung gespeichert. Wenn der Schlüssel später von der API abgelehnt wird, entfernt die App ihn aus dem Speicher. In diesem Beispiel wird der globale Endpunkt verwendet. Sie können auch den Endpunkt der [benutzerdefinierten Unterdomäne](../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.


Definieren Sie die Funktionen `storeValue` und `retrieveValue`, um das `localStorage`-Objekt (sofern vom Browser unterstützt) oder ein Cookie zu verwenden.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

Die Funktion `getSubscriptionKey()` versucht, einen zuvor gespeicherten Schlüssel mit `retrieveValue` abzurufen. Wenn kein Schlüssel gefunden wird, wird der Benutzer aufgefordert, seinen Schlüssel einzugeben. Dieser wird mit `storeValue` gespeichert.

```javascript

// Get the stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Das `onsubmit`-Attribut des HTML-Tags `<form>` ruft die `bingWebSearch`-Funktion auf, um die Suchergebnisse zurückzugeben. `bingWebSearch` verwendet `getSubscriptionKey` zur Authentifizierung aller Abfragen. Wie in der obigen Definition gezeigt, fordert `getSubscriptionKey` den Benutzer zur Eingabe des Schlüssels auf, falls dieser noch nicht eingegeben wurde. Der Schlüssel wird anschließend gespeichert und kann von der Anwendung auch später verwendet werden.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Senden von Suchanforderungen

Diese Anwendung verwendet ein HTML-`<form>`-Objekt, um anfangs Suchanforderungen von Benutzern zu senden, und ruft mit dem Attribut `onsubmit``newBingImageSearch()` auf.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

Der `onsubmit`-Handler gibt standardmäßig `false` zurück, wodurch verhindert wird, dass das Formular gesendet wird.

## <a name="select-search-options"></a>Auswählen von Suchoptionen

![[Bing-Bildersuche-Formular]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Die Bing-Bildersuche-API bietet mehrere [Filterabfrageparameter](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters), um Suchergebnisse einzuschränken und zu filtern. Das HTML-Formular in dieser Anwendung verwendet die folgenden Parameteroptionen und zeigt sie an:

| Option | BESCHREIBUNG |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | Ein Dropdownmenü zur Auswahl des Markts (Ort und Sprache), der für die Suche verwendet wird.                                                                                             |
| `query`      | Das Textfeld, in das die Suchbegriffe eingegeben werden.                                                                                                                                 |
| `aspect`     | Optionsfelder, mit denen die Proportionen der gefundenen Bilder ausgewählt werden können: ungefähr quadratisch, breit oder hoch.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Dropdownmenü, mit dem die Suche optional auf den letzten Tag, die letzte Woche oder den letzten Monat eingeschränkt werden kann.                                                                                          |
| `safe`       | Ein Kontrollkästchen, mit dem festgelegt wird, ob das SafeSearch-Feature von Bing verwendet werden soll. Mit diesem werden Inhalte herausgefiltert, die für Minderjährige nicht geeignet sind.                                                                                      |
| `count`      | Verborgenes Feld. Die Anzahl der Suchergebnisse, die bei jeder Anforderung zurückgegeben werden. Damit lässt sich die Anzahl der angezeigten Ergebnisse pro Seite festlegen.                                                            |
| `offset`     | Verborgenes Feld. Der Offset für das erste Suchergebnis der Anforderung. Dieser wird zur Unterteilung der Suchergebnisse in mehrere Seiten verwendet. Der Offset wird bei einer neuen Anforderung auf `0` zurückgesetzt.                                                           |
| `nextoffset` | Verborgenes Feld. Nachdem ein Suchergebnis empfangen wurde, wird dieses Feld auf den Wert von `nextOffset` in der Antwort festgelegt. Durch Verwendung dieses Felds werden überlappende Ergebnisse auf aufeinander folgenden Seiten vermieden. |
| `stack`      | Verborgenes Feld. Eine JSON-codierte Liste der Offsets vorhergehender Seiten mit Suchergebnissen, um zu vorherigen Seiten zurück zu navigieren.                                                      |

Die Funktion `bingSearchOptions()` formatiert diese Optionen in eine partielle Abfragezeichenfolge, die in den API-Anforderungen der App verwendet werden kann.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>Ausführen der Anforderung

Anhand der Suchabfrage, der Optionszeichenfolge und des API-Schlüssels verwendet die Funktion `BingImageSearch()` ein XMLHttpRequest-Objekt, um die Anforderung an den Endpunkt der Bing-Bildersuche zu senden.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // event handler for successful response
    request.addEventListener("load", handleBingResponse);

    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Beim erfolgreichen Abschluss der HTTP-Anforderung ruft JavaScript den „Load“-Ereignishandler `handleBingResponse()` auf, um eine erfolgreiche HTTP-GET-Anforderung zu verarbeiten.

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Erfolgreiche HTTP-Anforderungen können fehlerhafte Suchinformationen enthalten. Wenn beim Suchvorgang ein Fehler auftritt, gibt die Bing-Bildersuche-API einen HTTP-Statuscode zurück, der vom Code 200 abweicht und Fehlerinformationen in der JSON-Antwort enthält. Wenn die Anzahl von Anforderungen begrenzt war, gibt die API außerdem eine leere Antwort zurück.

## <a name="display-the-search-results"></a>Anzeigen der Suchergebnisse

Die Suchergebnisse werden von der Funktion `renderSearchResults()` angezeigt, die das vom Bing-Bildersuche-Dienst zurückgegebene JSON verwendet und eine entsprechende Rendererfunktion für alle zurückgegebenen Bilder und verwandten Suchen aufruft.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Die Ergebnisse der Bildersuche sind im `value`-Objekt auf der obersten Ebene in der JSON-Antwort enthalten. Diese werden an das `renderImageResults()`-Objekt übergeben, das die Ergebnisse durchläuft und jedes Element in HTML umwandelt.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

Die Bing-Bildersuche-API kann vier Typen von Suchvorschlägen zurückgeben, um die Suchabfrage der Benutzer im jeweiligen Objekt auf oberster Ebene zu steuern:

| Vorschlag         | BESCHREIBUNG                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Abfragen, bei denen ein Pivot-Wort in der ursprünglichen Suche durch ein anderes ersetzt wird. Wenn Sie beispielsweise nach „rote Blumen“ suchen, kann ein Pivot-Wort „rote“ sein, und ein Pivot-Vorschlag kann „gelbe Blumen“ sein. |
| `queryExpansions`  | Abfragen, bei denen die ursprüngliche Suche durch Hinzufügen weiterer Begriffe eingegrenzt wird. Wenn Sie beispielsweise nach „Microsoft Surface“ suchen, kann eine Abfrageerweiterung „Microsoft Surface Pro“ sein.                                   |
| `relatedSearches`  | Abfragen, die ebenfalls von anderen Benutzern eingegeben wurden, die auch die ursprüngliche Suche eingegeben haben. Wenn Sie z.B. nach „Mount Rainier“ suchen, kann eine verwandte Suche „Mount Saint Helens“ lauten.                       |
| `similarTerms`     | Abfragen, die von der Bedeutung her der ursprünglichen Suche ähneln. Wenn Sie beispielsweise nach „Kätzchen“ suchen, kann ein ähnlicher Begriff „süß“ sein.                                                                   |

Diese Anwendung rendert nur die `relatedItems`-Vorschläge und fügt die resultierenden Links in der Randleiste der Seite ein.

## <a name="rendering-search-results"></a>Rendern der Suchergebnisse

In dieser Anwendung enthält das `searchItemRenderers`-Objekt Rendererfunktionen, die für jeden Suchergebnistyp HTML-Code generieren.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

Für diese Rendererfunktionen können die folgenden Parameter angegeben werden:

| Parameter         | BESCHREIBUNG                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | Das JavaScript-Objekt mit Eigenschaften des Elements, z.B. seine URL und Beschreibung. |
| `index` | Der Index des Ergebniselements innerhalb der Auflistung.                                          |
| `count` | Die Anzahl der Elemente in der Auflistung der Suchergebniselemente.                                  |

Die Parameter `index` und `count` werden verwendet, um Ergebnisse zu nummerieren, HTML für Auflistungen zu generieren und Inhalte zu organisieren. Diese Parameter:

* Berechnet die Miniaturbildgröße (die Breite variiert und weist einen Mindestwert von 120 Pixel auf, die Höhe ist auf 90 Pixel festgelegt).
* Erstellt das HTML-Tag `<img>`, mit dem das Miniaturbild angezeigt wird.
* Erstellt die `<a>`-HTML-Tags, die eine Verknüpfung mit dem Bild und der Seite, die das Bild enthält, darstellen.
* Erstellt die Beschreibung, die Informationen über das Bild und die Website, auf der sich das Bild befindet, angibt.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Die Eigenschaften `height` und `width` der Miniaturansicht werden sowohl im `<img>`-Tag als auch in den Feldern `h` und `w` in der URL der Miniaturansicht verwendet. So kann Bing [ein Miniaturbild](../bing-web-search/resize-and-crop-thumbnails.md) genau dieser Größe zurückgeben.

## <a name="persisting-client-id"></a>Persistentes Speichern der Client-ID

Antworten, die von Bing-Suche-APIs gesendet werden, können einen `X-MSEdge-ClientID`-Header enthalten, der mit den nachfolgenden Anforderungen an die API zurückgesendet werden sollte. Wenn mehrere Bing-Suche-APIs genutzt werden, sollte nach Möglichkeit dieselbe Client-ID für alle APIs verwendet werden.

Durch das Bereitstellen des `X-MSEdge-ClientID`-Headers können die Bing-Suche-APIs alle Suchvorgänge eines Benutzers zuordnen, was entscheidende Vorteile mit sich bringt:

Erstens kann die Bing-Suchmaschine auf diese Weise Kontextinformationen aus vorherigen Suchvorgängen nutzen, um den Benutzern relevantere Ergebnisse anzuzeigen. Wenn ein Benutzer in der Vergangenheit beispielsweise nach Segelbegriffen gesucht hat, können bei einer Suche nach „Knoten“ möglicherweise Informationen zu Segelknoten zurückgegeben werden.

Zweitens wählt Bing möglicherweise Benutzer zufällig aus, die die Möglichkeit haben, neue Features zu testen, bevor diese allen Benutzern zur Verfügung gestellt werden. Indem Sie bei jeder Anforderung dieselbe Client-ID bereitstellen, stellen Sie sicher, dass Benutzer, die zur Nutzung eines neuen Features ausgewählt wurden, dieses Feature dauerhaft nutzen können. Ohne die Client-ID wird das Feature in den Suchergebnissen möglicherweise scheinbar willkürlich aus- oder eingeblendet.

Durch Browsersicherheitsrichtlinien (CORS) kann der `X-MSEdge-ClientID`-Header möglicherweise nicht von JavaScript verwendet werden. Diese Einschränkung tritt auf, wenn sich der Ursprung der Suchantwort von dem der Seite unterscheidet, die den Suchvorgang angefordert hat. In einer Produktionsumgebung sollten Sie zum Umgang mit dieser Richtlinie ein serverseitiges Skript hosten, das den API-Aufruf für die Domain durchführt, die auch für die Webseite genutzt wird. Da der Ursprung des Skripts mit dem Ursprung der Webseite übereinstimmt, kann der `X-MSEdge-ClientID`-Header von JavaScript verwendet werden.

> [!NOTE]
> In einer Webanwendung für eine Produktionsumgebung sollten Sie die Anforderung in jedem Fall serverseitig ausführen. Andernfalls muss der Schlüssel der Bing-Suche-API auf der Webseite hinterlegt werden, wo er im Quelltext für alle Personen zugänglich ist. Dies müssen Sie vermeiden, da ansonsten unbefugte Dritte Anforderungen unter Verwendung Ihres API-Abonnementschlüssels Anforderungen senden können, die Ihnen in Rechnung gestellt werden.

In der Entwicklungsphase können Sie die Bing-Websuche-API-Anforderung über einen CORS-Proxy senden. In der Antwort eines solchen Proxys befindet sich ein `Access-Control-Expose-Headers`-Header, der Antwortheader zulässt und für JavaScript zur Verfügung stellt.

Die Installation eines CORS-Proxys, mit dem die Tutorial-App auf den Client-ID-Header zugreifen kann, ist schnell und unkompliziert. [Installieren Sie Node.js](https://nodejs.org/en/download/), falls Sie dies noch nicht getan haben. Geben Sie anschließend folgenden Befehl in ein Befehlsfenster ein:

```console
npm install -g cors-proxy-server
```

Passen Sie den Endpunkt der Bing-Websuche-API in der HTML-Datei wie folgt an:
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Starten Sie abschließend den CORS-Proxy mit folgendem Befehl:

```console
cors-proxy-server
```

Lassen Sie das Fenster während der Nutzung der Tutorial-App geöffnet. Wenn Sie das Fenster schließen, wird auch die Ausführung des Proxys beendet. Im Bereich mit den erweiterbaren HTTP-Headern unter den Suchergebnissen wird nun u.a. der `X-MSEdge-ClientID`-Header angezeigt. Hier können Sie überprüfen, ob dieser für alle Anforderungen identisch ist.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Extrahieren von Bilddetails mit der Bing-Bildersuche-API](tutorial-image-post.md)

## <a name="see-also"></a>Weitere Informationen

* [Referenz zur Bing-Bildersuche-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)