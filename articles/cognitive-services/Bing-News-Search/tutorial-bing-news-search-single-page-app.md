---
title: 'Tutorial: Erstellen einer Single-Page-Web-App mit der Bing-News-Suche-API'
titleSuffix: Azure Cognitive Services
description: Erstellen Sie mithilfe dieses Tutorials eine Single-Page-Webanwendung, die Suchabfragen an die Bing-News-API senden und die Ergebnisse auf der Webseite anzeigen kann.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: c3d486a32f4a926ed42f3d67a1fa96d3da397beb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351213"
---
# <a name="tutorial-create-a-single-page-web-app"></a>Tutorial: Erstellen einer Single-Page-Web-App

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mit der Bing-News-Suche-API können Sie das Web durchsuchen und Ergebnisse für verschiedene Arten von News finden, die für eine Suchabfrage relevant sind. In diesem Tutorial wird eine Single-Page-Web-App erstellt, die unter Verwendung der Bing-News-Suche-API Suchergebnisse auf der Seite anzeigt. Die Anwendung enthält HTML-, CSS- und JavaScript-Komponenten. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingNewsSearchApp.html).

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> Wenn Sie auf die JSON- und HTTP-Überschriften im unteren Bereich der Seite klicken, werden die JSON-Antwort und HTTP-Anforderungsinformationen angezeigt. Diese Details können hilfreich sein, wenn Sie sich genauer mit dem Dienst befassen.

In dieser Tutorial-App wird Folgendes veranschaulicht:
> [!div class="checklist"]
> * Aufrufen der Bing-News-Suche-API in JavaScript
> * Übergeben von Suchoptionen an die Bing-News-Suche-API
> * Zeigen Sie Suchergebnisse für News für vier Kategorien an: Alle, Business, Gesundheit oder Politik. Die möglichen Zeiträume sind 24 Stunden, die letzte Woche, der letzte Monat oder der gesamte verfügbare Zeitraum.
> * Seitenweises Anzeigen von Suchergebnissen
> * Verarbeiten der Bing-Client-ID und des API-Abonnementschlüssels
> * Behandeln von möglicherweise auftretenden Fehlern

Die Tutorialseite ist unabhängig von anderen Komponenten und verwendet keine externen Frameworks, Stylesheets oder Bilddateien. Die Seite greift nur auf die am häufigsten unterstützten Features für JavaScript zurück und kann in aktuellen Versionen aller gängigen Webbrowser ausgeführt werden.


## <a name="prerequisites"></a>Voraussetzungen

Um dem Tutorial folgen zu können, benötigen Sie Abonnementschlüssel für die Bing-Suche-API. Wenn Sie über diese nicht verfügen, müssen Sie sie erstellen:

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Erstellen einer Ressource für die Bing-Suche"  target="_blank"> im Azure-Portal eine Ressource für die Bing-Suche <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

## <a name="app-components"></a>App-Komponenten
Diese Tutorial-App setzt sich ebenso wie alle anderen Single-Page-Web-Apps aus drei Teilen zusammen:

> [!div class="checklist"]
> * HTML: Definiert die Struktur und der Inhalt der Seite
> * CSS: Definiert das Layout der Seite
> * JavaScript: Definiert das Verhalten der Seite

Bei HTML und CSS handelt es sich zum Großteil um konventionellen Code, der in diesem Tutorial nicht erläutert wird. Der HTML-Teil enthält das Suchformular, in dem der Benutzer eine Abfrage eingibt und Suchoptionen auswählt. Das Formular wird vom JavaScript-Teil ausgewertet, in dem die Suche mithilfe des `onsubmit`-Attributs des `<form>`-Tags ausgeführt wird:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
Der `onsubmit`-Handler gibt `false` zurück, wodurch verhindert wird, dass das Formular an den Server gesendet wird. Der JavaScript-Code ist dafür verantwortlich, die erforderlichen Informationen aus dem Formular abzurufen und die Suche durchzuführen.

Der HTML-Teil enthält auch die Bereiche (`<div>`-HTML-Tags), in denen die Suchergebnisse angezeigt werden.

## <a name="managing-subscription-key"></a>Verwalten des Abonnementschlüssels

Zum Speichern des Schlüssels wird der persistente Browserspeicher verwendet. Auf diese Weise müssen Sie nicht den Abonnementschlüssel der Bing-Suche-API im Code hinterlegen. Vor dem Speichern des Schlüssels wird der Benutzer zur Eingabe des Schlüssels aufgefordert. Wird der Schlüssel zu einem späteren Zeitpunkt von der API zurückgewiesen, wird der gespeicherte Schlüssel für ungültig erklärt, und der Benutzer wird zur erneuten Eingabe aufgefordert.

In den Funktionen `storeValue` und `retrieveValue` wird entweder das `localStorage`-Objekt (nicht von allen Browsern unterstützt) oder ein Cookie verwendet. Diese Funktionen werden in der `getSubscriptionKey()`-Funktion zum Speichern und Abrufen des Benutzerschlüssels verwendet. Sie können den unten angegebenen globalen Endpunkt oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
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

![Optionen der Bing-News-Suche](media/news-search-categories.png)

Das HTML-Formular enthält folgende Elemente:

|Element|BESCHREIBUNG|
|-|-|
| `where` | Ein Dropdownmenü zur Auswahl des Markts (Ort und Sprache), der für die Suche verwendet wird. |
| `query` | Das Textfeld, in das Suchbegriffe eingegeben werden. |
| `category` | Kontrollkästchen, mit denen bestimmte Ergebnisse weiter oben in der Liste der Suchergebnisse angezeigt werden. Wenn beispielsweise das Kontrollkästchen für „Gesundheit“ angeklickt wird, wird der Rang für News zum Thema „Gesundheit“ erhöht. |
| `when` | Dropdownmenü, mit dem die Suche optional auf den letzten Tag, die letzte Woche oder den letzten Monat eingeschränkt werden kann. |
| `safe` | Ein Kontrollkästchen, mit dem festgelegt wird, ob das SafeSearch-Feature von Bing verwendet werden soll. Mit diesem werden Inhalte herausgefiltert, die für Minderjährige nicht geeignet sind. |
| `count` | Verborgenes Feld. Die Anzahl der Suchergebnisse, die bei jeder Anforderung zurückgegeben werden. Damit lässt sich die Anzahl der angezeigten Ergebnisse pro Seite festlegen. |
| `offset`|  Verborgenes Feld. Der Offset für das erste Suchergebnis der Anforderung. Dieser wird zur Unterteilung der Suchergebnisse in mehrere Seiten verwendet. Der Offset wird bei einer neuen Anforderung auf `0` zurückgesetzt. |

> [!NOTE]
> Die Bing-Websuche bietet noch andere Abfrageparameter. Hier werden nur einige davon verwendet.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Der Parameter `SafeSearch` kann bei einem API-Aufruf beispielsweise die Werte `strict`, `moderate` oder `off` annehmen, wobei `moderate` der Standardwert ist. In unserem Formular wird hingegen ein Kontrollkästchen verwendet, das nur über zwei Zustände verfügt. Der JavaScript-Code konvertiert diese Einstellung entweder in `strict` oder `off` (`moderate` wird nicht verwendet).

## <a name="performing-the-request"></a>Ausführen der Anforderung
Die `BingNewsSearch`-Funktion verwendet auf Grundlage der Abfrage, der Optionszeichenfolge und des API-Schlüssels ein `XMLHttpRequest`-Objekt, um die Anforderung an den Endpunkt der Bing-Suche-API zu senden.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

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
Nach erfolgreichem Abschluss der HTTP-Anforderung ruft JavaScript den `load`-Ereignishandler und damit die `handleBingResponse()`-Funktion auf, um eine erfolgreiche HTTP GET-Anforderung an die API zu verarbeiten. 

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
            if (jsobj._type === "News") {
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
> Eine erfolgreiche HTTP-Anforderung ist *nicht* zwangsläufig mit einer erfolgreichen Suche gleichzusetzen. Wenn bei einem Suchvorgang ein Fehler auftritt, gibt die Bing-News-Suche-API einen HTTP-Statuscode zurück, der vom Code 200 abweicht und Fehlerinformationen in der JSON-Antwort enthält. Wenn die Anzahl der Anforderungen begrenzt war, gibt die API zusätzlich eine leere Antwort zurück.

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

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
Die Suchergebnisse werden als `value`-Objekt der obersten Ebene in der JSON-Antwort zurückgegeben. Die Ergebnisse werden dann an die `renderResults()`-Funktion übergeben, die diese durchläuft und eine separate Funktion zum Rendern der einzelnen Elemente in HTML aufruft. Der resultierende HTML-Code wird an `renderSearchResults()` zurückgegeben, wo er in den Bereich `results` auf der Seite eingefügt wird.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
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

Im JavaScript-Code kann das `searchItemRenderers`-Objekt *Renderer* enthalten, also Funktionen, die für jeden Suchergebnistyp HTML-Code generieren.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
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

Der `news`-Renderer wird im folgenden JavaScript-Auszug gezeigt:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Die Funktion zum Rendern von News führt folgende Vorgänge aus:
> [!div class="checklist"]
> * Erstellt ein Absatztag, weist es der `news`-Klasse zu und übergibt es mit einem Pushvorgang an den HTML-Array.
> * Berechnet die Größe von Miniaturbildern (die Breite ist auf 60 Pixel festgelegt, die Höhe wird proportional berechnet).
> * Erstellt das HTML-Tag `<img>`, mit dem das Miniaturbild angezeigt wird. 
> * Erstellt die `<a>`-HTML-Tags, die eine Verknüpfung mit dem Bild und der Seite, die das Bild enthält, darstellen.
> * Erstellt die Beschreibung, die Informationen über das Bild und die Website, auf der sich das Bild befindet, angibt.

Die Größe des Miniaturbilds wird sowohl im `<img>`-Tag als auch in den Feldern `h` und `w` in der URL des Miniaturbilds verwendet. Der [Bing-Dienst für Miniaturbilder](../bing-web-search/resize-and-crop-thumbnails.md) übermittelt anschließend ein Miniaturbild in genau dieser Größe.

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
> [Referenz zur Bing News-Suche-API](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)