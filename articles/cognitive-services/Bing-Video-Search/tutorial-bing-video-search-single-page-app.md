---
title: 'Tutorial: Erstellen einer Single-Page-Webanwendung für die Bing-Videosuche'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird erläutert, wie Sie die Bing-Videosuche-API in einer Single-Page-Webanwendung verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 76d7f4e31934480be57cd5ac133c1c7e6a9dd364
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349604"
---
# <a name="tutorial-single-page-video-search-app"></a>Tutorial: Single-Page-Webanwendung für die Videosuche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
Mit der Bing-Videosuche-API können Sie das Web durchsuchen und für eine Suchabfrage relevante Videoergebnisse abrufen. In diesem Tutorial wird eine Single-Page-Webanwendung erstellt, die unter Verwendung der Bing-Suche-API Suchergebnisse auf der Seite anzeigt. Die Anwendung enthält HTML-, CSS- und JavaScript-Komponenten.

<!-- Remove until it can be replaced with a sanitized version.
![Single-page Bing Video Search app](./media/video-search-singlepage.png)
-->

> [!NOTE]
> Wenn Sie auf die JSON- und HTTP-Überschriften im unteren Bereich der Seite klicken, werden die JSON-Antwort und HTTP-Anforderungsinformationen angezeigt. Diese Details können hilfreich sein, wenn Sie sich genauer mit dem Dienst befassen.

![JSON- und HTTP-Rohergebnisse](./media/json-http-raw-results.png)

Mit dieser Tutorial-App wird folgendes veranschaulicht:
> [!div class="checklist"]
> * Aufrufen der Bing-Videosuche-API in JavaScript
> * Übergeben von Suchoptionen an die Bing-Suche-API
> * Anzeigen von Ergebnissen der Videosuche oder optionales Einschließen von Webseiten, News oder Bildern
> * Suchzeitrahmen von 24 Stunden, der letzten Woche, dem letzten Monat oder aller verfügbaren Zeit
> * Seitenweises Anzeigen von Suchergebnissen
> * Verarbeiten der Bing-Client-ID und des API-Abonnementschlüssels
> * Behandeln von möglicherweise auftretenden Fehlern

Die Tutorialseite ist unabhängig von anderen Komponenten und verwendet keine externen Frameworks, Stylesheets oder Bilddateien. Die Seite greift nur auf die am häufigsten unterstützten Features für JavaScript zurück und kann in aktuellen Versionen aller gängigen Webbrowser ausgeführt werden.

In diesem Tutorial werden ausgewählte Teile des Quellcodes erläutert. Der vollständige [Quellcode]() steht zur Verfügung. Zum Ausführen des Beispiels kopieren Sie den Quellcode, fügen Sie ihn in einen Text-Editor ein, und speichern Sie die Datei als `bing.html`.

## <a name="app-components"></a>App-Komponenten
Diese Tutorial-App setzt sich ebenso wie alle anderen Single-Page-Web-Apps aus drei Teilen zusammen:

> [!div class="checklist"]
> * HTML: Definiert die Struktur und der Inhalt der Seite
> * CSS: Definiert das Layout der Seite
> * JavaScript: Definiert das Verhalten der Seite

Bei HTML und CSS handelt es sich zum Großteil um konventionellen Code, der in diesem Tutorial nicht erläutert wird. Der HTML-Teil enthält das Suchformular, in dem der Benutzer eine Abfrage eingibt und Suchoptionen auswählt. Das Formular wird vom JavaScript-Teil ausgewertet, der die Suche mithilfe des `onsubmit`-Attributs des `<form>`-Tags ausführt:

```html
<form name="bing" onsubmit="return bingWebSearch(this)">
```
Der `onsubmit`-Handler gibt `false` zurück, wodurch verhindert wird, dass das Formular an den Server gesendet wird. Der JavaScript-Code ist dafür verantwortlich, die erforderlichen Informationen aus dem Formular abzurufen und die Suche durchzuführen.

Der HTML-Teil enthält auch die Bereiche (`<div>`-HTML-Tags), in denen die Suchergebnisse angezeigt werden.

## <a name="managing-subscription-key"></a>Verwalten des Abonnementschlüssels

Zum Speichern des Schlüssels wird der persistente Browserspeicher verwendet. Auf diese Weise müssen Sie nicht den Abonnementschlüssel der Bing-Suche-API im Code hinterlegen. Vor dem Speichern des Schlüssels wird der Benutzer zur Eingabe des Schlüssels aufgefordert. Wird der Schlüssel zu einem späteren Zeitpunkt von der API zurückgewiesen, wird der gespeicherte Schlüssel für ungültig erklärt, und der Benutzer wird zur erneuten Eingabe aufgefordert.

In den Funktionen `storeValue` und `retrieveValue` wird entweder das `localStorage`-Objekt (nicht von allen Browsern unterstützt) oder ein Cookie verwendet. Diese Funktionen werden in der `getSubscriptionKey()`-Funktion zum Speichern und Abrufen des Benutzerschlüssels verwendet.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// ... omitted definitions of store value and retrieve value
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or prompt if it's not found.
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
Das `onsubmit`-Attribut des HTML-Tags `<form>` ruft die `bingWebSearch`-Funktion auf, um die Suchergebnisse zurückzugeben. `bingWebSearch` verwendet `getSubscriptionKey()` zur Authentifizierung aller Abfragen. Wie in der obigen Definition gezeigt, fordert `getSubscriptionKey` den Benutzer zur Eingabe des Schlüssels auf, falls dieser noch nicht eingegeben wurde. Der Schlüssel wird anschließend gespeichert und kann von der Anwendung auch später verwendet werden.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Auswählen der Suchoptionen
Die folgende Abbildung zeigt das Abfragetextfeld und Optionen, die eine Suche definieren.

![Optionen der Bing-News-Suche](media/video-search-options.png)

Das HTML-Formular enthält folgende Elemente:

|Element|BESCHREIBUNG|
|-|-|
| `where` | Ein Dropdownmenü zur Auswahl des Markts (Ort und Sprache), der für die Suche verwendet wird. |
| `query` | Das Textfeld, in das Suchbegriffe eingegeben werden. |
| `modules` | Kontrollkästchen, mit denen bestimmte Module der Ergebnisse, alle Ergebnisse oder zugehörige Videos heraufgestuft werden können. |
| `when` | Dropdownmenü, mit dem die Suche optional auf den letzten Tag, die letzte Woche oder den letzten Monat eingeschränkt werden kann. |
| `safe` | Ein Kontrollkästchen, mit dem festgelegt wird, ob das SafeSearch-Feature von Bing verwendet werden soll. Mit diesem werden Inhalte herausgefiltert, die für Minderjährige nicht geeignet sind. |
| `count` | Verborgenes Feld. Die Anzahl der Suchergebnisse, die bei jeder Anforderung zurückgegeben werden. Damit lässt sich die Anzahl der angezeigten Ergebnisse pro Seite festlegen. |
| `offset`|  Verborgenes Feld. Der Offset für das erste Suchergebnis der Anforderung. Dieser wird zur Unterteilung der Suchergebnisse in mehrere Seiten verwendet. Der Offset wird bei einer neuen Anforderung auf `0` zurückgesetzt. |

> [!NOTE]
> Die Bing-Websuche bietet noch andere Abfrageparameter. Hier werden nur einige davon verwendet.

``` javascript
// build query options from the HTML form
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));

    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("modules=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Der Parameter `SafeSearch` kann bei einem tatsächlichen API-Aufruf beispielsweise den Wert `strict` oder `moderate` haben, wobei `moderate` der Standardwert ist.

## <a name="performing-the-request"></a>Ausführen der Anforderung
Die `BingWebSearch`-Funktion verwendet auf der Grundlage der Abfrage, der Optionszeichenfolge und des API-Schlüssels ein `XMLHttpRequest`-Objekt, um die Anforderung an den Bing-Suche-API-Endpunkt zu senden. Sie können den unten angegebenen globalen Endpunkt oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

```javascript
// Search on the query, using search options, authenticated by the key.
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_headers", "paging1", "paging2", "error");

    var endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search";
    var request = new XMLHttpRequest();
    var queryurl = endpoint + "?q=" + encodeURIComponent(query) + "&" + options;

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
    request.addEventListener("load", handleOnLoad);

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
Nach erfolgreichem Abschluss der HTTP-Anforderung ruft JavaScript den `load`-Ereignishandler `handleOnLoad()` auf, um eine erfolgreiche HTTP-GET-Anforderung an die API zu verarbeiten. 

```javascript
// handle Bing search request results
function handleOnLoad() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
    }

    // show raw JSON and headers
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "Videos") {//"SearchResponse" && "rankingResponse" in jsobj) {
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
> Wenn bei einem Suchvorgang ein Fehler auftritt, gibt die Bing-News-Suche-API einen HTTP-Statuscode zurück, der vom Code 200 abweicht und Fehlerinformationen in der JSON-Antwort enthält. Wenn die Anzahl der Anforderungen begrenzt war, gibt die API zusätzlich eine leere Antwort zurück.
Eine erfolgreiche HTTP-Anforderung ist *nicht* zwangsläufig mit einer erfolgreichen Suche gleichzusetzen. 

Ein Großteil des Codes in den beiden vorangehenden Funktionen ist für die Fehlerbehandlung zuständig. In folgenden Phasen können Fehler auftreten:

|Phase|Mögliche Fehler|Verarbeitet durch|
|-|-|-|
|Erstellen des JavaScript-Anforderungsobjekts|Ungültige URL|`try`/`catch`-Block|
|Senden der Anforderung|Netzwerkfehler, abgebrochene Verbindungen|Ereignishandler `error` und `abort`|
|Durchführen der Suche|Ungültige Anforderung, ungültiger JSON-Code, Beschränkung der Anforderungsanzahl|Tests im `load`-Ereignishandler|

Fehler werden behandelt, indem `renderErrorMessage()` zusammen mit allen bekannten Fehlerinformationen aufgerufen wird. Wenn die Antwort alle Fehlertests besteht, wird `renderSearchResults()` zur Anzeige der Suchergebnisse auf der Seite aufgerufen.

## <a name="displaying-search-results"></a>Anzeigen der Suchergebnisse
Die Hauptfunktion zum Anzeigen der Suchergebnisse ist `renderSearchResults()`. Diese Funktion verwendet den vom Bing-News-Suche-Dienst zurückgegebenen JSON-Code und rendert die News-Ergebnisse sowie verwandte Suchvorgänge (falls vorhanden).

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results to the mainline section
    for (section in { mainline: 0 }) {
         showDiv(section, renderResultsItems(section, results));
    }
}
```
Die Suchergebnisse werden als `value`-Objekt der obersten Ebene in der JSON-Antwort zurückgegeben. Diese werden dann an die `renderResultsItems()`-Funktion übergeben, die diese durchläuft und eine Funktion zum Rendern der einzelnen Elemente in HTML aufruft. Der resultierende HTML-Code wird an `renderSearchResults()` zurückgegeben, wo er in den Bereich `results` auf der Seite eingefügt wird.

```javascript
// render search results
    function renderResultsItems(section, results) {   

        var items = results.value;
        var html = [];
        for (var i = 0; i < items.length; i++) { 
            var item = items[i];
            // collection name has lowercase first letter
            var type = "videos";
            var render = searchItemRenderers[type];
            html.push(render(item, section));  
        }
    return html.join("\n\n");
}
```

Die Bing-News-Suche-API gibt bis zu vier verschiedene Arten verwandter Ergebnisse zurück, und zwar jeweils in einem eigenen Objekt der obersten Ebene. Sie lauten wie folgt:

|Beziehung|BESCHREIBUNG|
|-|-|
|`pivotSuggestions`|Abfragen, bei denen ein Pivot-Wort in der ursprünglichen Suche durch ein anderes ersetzt wird. Wenn Sie beispielsweise nach „rote Blumen“ suchen, kann ein Pivot-Wort „rote“ sein, und ein Pivot-Vorschlag kann „gelbe Blumen“ sein.|
|`queryExpansions`|Abfragen, bei denen die ursprüngliche Suche durch Hinzufügen weiterer Begriffe eingegrenzt wird. Wenn Sie beispielsweise nach „Microsoft Surface“ suchen, kann eine Abfrageerweiterung „Microsoft Surface Pro“ sein.|
|`relatedSearches`|Abfragen, die ebenfalls von anderen Benutzern eingegeben wurden, die auch die ursprüngliche Suche eingegeben haben. Wenn Sie z.B. nach „Mount Rainier“ suchen, kann eine verwandte Suche „Mount Saint Helens“ lauten.|
|`similarTerms`|Abfragen, die von der Bedeutung her der ursprünglichen Suche ähneln. Wenn Sie beispielsweise nach „Schulen“ suchen, kann ein ähnlicher Begriff „Ausbildung“ sein.|

Wie bereits bei `renderSearchResults()` gezeigt, werden nur die `relatedItems`-Vorschläge gerendert und die resultierenden Links auf der Randleiste der Seite eingefügt.

## <a name="rendering-result-items"></a>Rendern von Ergebniselementen

Im JavaScript-Code kann das `searchItemRenderers`-Objekt *Renderer* enthalten, also Funktionen, die für jeden Suchergebnistyp HTML-Code generieren. Die Seite für die Videosuche verwendet nur `videos`. Informationen zu verschiedenen Typen von Renderern finden Sie in anderen Tutorials.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    videos: function (item, section, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
Für eine Funktion zum Rendern können die folgenden Parameter angegeben werden:

|Parameter|BESCHREIBUNG|
|-|-|
|`item`| Das JavaScript-Objekt mit Eigenschaften des Elements, z.B. seine URL und Beschreibung.|
|`index`| Der Index des Ergebniselements innerhalb der Auflistung.|
|`count`| Die Anzahl der Elemente in der Auflistung der Suchergebniselemente.|

Die Parameter `index` und `count` können beispielsweise zum Nummerieren von Ergebnissen, zum Generieren von speziellem HTML-Code für den Anfang oder das Ende einer Collection oder zum Einfügen von Zeilenumbrüchen nach einer bestimmten Anzahl von Elementen verwendet werden. Wenn ein Renderer diese Funktionalität nicht benötigt, muss er diese beiden Parameter nicht akzeptieren.

Der `video`-Renderer wird im folgenden JavaScript-Auszug gezeigt. Bei Verwendung des Videos-Endpunkts sind alle Ergebnisse vom Typ `Videos`. Die `searchItemRenderers` werden im folgenden Codesegment gezeigt.

```javascript
// render functions for various types of search results
    searchItemRenderers = {

    videos: function (item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];

        html.push("<p class='images'>");
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escapeQuotes(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='" + item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Video page source</a> - ");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }
}
```

Die Funktion zum Rendern führt folgende Vorgänge aus:
> [!div class="checklist"]
> * Erstellt ein Absatztag, weist es der `images`-Klasse zu und übergibt es mit einem Pushvorgang an den HTML-Array.
> * Berechnet die Größe von Miniaturbildern (die Breite ist auf 60 Pixel festgelegt, die Höhe wird proportional berechnet).
> * Erstellt das HTML-Tag `<img>`, mit dem das Miniaturbild angezeigt wird. 
> * Erstellt die `<a>`-HTML-Tags, die eine Verknüpfung mit dem Bild und der Seite, die das Bild enthält, darstellen.
> * Erstellt die Beschreibung, die Informationen über das Bild und die Website, auf der sich das Bild befindet, angibt.

Die Größe des Miniaturbilds wird sowohl im `<img>`-Tag als auch in den Feldern `h` und `w` in der URL des Miniaturbilds verwendet. Bing gibt ein [Miniaturbild](../bing-web-search/resize-and-crop-thumbnails.md) genau dieser Größe zurück.

## <a name="persisting-client-id"></a>Persistentes Speichern der Client-ID
Antworten, die von Bing-Suche-APIs gesendet werden, können einen `X-MSEdge-ClientID`-Header enthalten, der mit den nachfolgenden Anforderungen an die API zurückgesendet werden sollte. Wenn mehrere Bing-Suche-APIs genutzt werden, sollte nach Möglichkeit dieselbe Client-ID für alle APIs verwendet werden.

Durch die Bereitstellung des `X-MSEdge-ClientID`-Headers können die Bing-Suche-APIs alle Suchvorgänge eines Benutzers zuordnen, was zwei entscheidende Vorteile mit sich bringt.

Erstens kann die Bing-Suchmaschine auf diese Weise Kontextinformationen aus vorherigen Suchvorgängen nutzen, um den Benutzern relevantere Ergebnisse anzuzeigen. Wenn ein Benutzer in der Vergangenheit beispielsweise nach Segelbegriffen gesucht hat, können bei einer Suche nach „Knoten“ möglicherweise Informationen zu Segelknoten zurückgegeben werden.

Zweitens wählt Bing möglicherweise Benutzer zufällig aus, die die Möglichkeit haben, neue Features zu testen, bevor diese allen Benutzern zur Verfügung gestellt werden. Indem Sie bei jeder Anforderung dieselbe Client-ID bereitstellen, stellen Sie sicher, dass Benutzer, denen das Feature angezeigt wird, dieses Feature dauerhaft nutzen können. Ohne die Client-ID wird das Feature in den Suchergebnissen möglicherweise scheinbar willkürlich aus- oder eingeblendet.

Durch Browsersicherheitsrichtlinien (CORS) kann der `X-MSEdge-ClientID`-Header möglicherweise nicht von JavaScript verwendet werden. Diese Einschränkung tritt auf, wenn sich der Ursprung der Suchantwort von dem der Seite unterscheidet, die den Suchvorgang angefordert hat. In einer Produktionsumgebung sollten Sie zum Umgang mit dieser Richtlinie ein serverseitiges Skript hosten, das den API-Aufruf für die Domain durchführt, die auch für die Webseite genutzt wird. Da der Ursprung des Skripts mit dem Ursprung der Webseite übereinstimmt, kann der `X-MSEdge-ClientID`-Header von JavaScript verwendet werden.

> [!NOTE]
> In einer Web-App für eine Produktionsumgebung sollten Sie die Anforderung serverseitig ausführen. Andernfalls muss der Schlüssel der Bing-Suche-API auf der Webseite hinterlegt werden, wo er im Quelltext für alle Personen zugänglich ist. Dies müssen Sie vermeiden, da ansonsten unbefugte Dritte Anforderungen unter Verwendung Ihres API-Abonnementschlüssels Anforderungen senden können, die Ihnen in Rechnung gestellt werden.

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
> [Referenz zur Bing-Videosuche-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference)