---
title: 'Tutorial: Erstellen einer Single-Page-Webanwendung – Bing-Websuche-API'
titleSuffix: Azure Cognitive Services
description: Dieses Tutorial zum Erstellen einer einseitigen App zeigt, wie Sie mit der Bing-Websuche-API relevante Suchergebnisse in einer einseitigen App abrufen, analysieren und anzeigen können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ff54d605fd81fa640314d99359f1aabacf7a469e
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350380"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-web-search-api"></a>Tutorial: Erstellen einer Single-Page-Webanwendung mit der Bing-Websuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Diese einseitige App zeigt, wie Sie Suchergebnisse aus der Bing-Websuche-API abrufen, analysieren und anzeigen. Das Tutorial verwendet Standard-HTML und -CSS und konzentriert sich auf den JavaScript-Code. HTML-, CSS- und JS-Dateien sind in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) mit Schnellstartanweisungen verfügbar.

Diese Beispiel-App kann:

> [!div class="checklist"]
> * Die Bing-Websuche-API mit Suchoptionen aufrufen
> * Ergebnisse aus Web, Bildern, Nachrichten und Videos anzeigen
> * Ergebnisse paginieren
> * Abonnementschlüssel verwalten
> * Fehlerbehandlung

Zum Verwenden dieser App wird ein [Azure Cognitive Services-Konto](../cognitive-services-apis-create-account.md) mit Bing-Suche-APIs benötigt.

## <a name="prerequisites"></a>Voraussetzungen

Folgende Voraussetzungen müssen erfüllt sein, damit die App ausgeführt werden kann:

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie über <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Erstellen einer Ressource für die Bing-Suche"  target="_blank"> im Azure-Portal eine Ressource für die Bing-Suche <span class="docon docon-navigate-external x-hidden-focus"></span></a> erstellen, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

* Node.js 8 oder höher

Der erste Schritt besteht darin, das Repository mit dem Quellcode der Beispiel-App zu klonen.

```console
git clone https://github.com/Azure-Samples/cognitive-services-REST-api-samples.git
```

Führen Sie dann `npm install` aus. In diesem Tutorial ist „Express.js“ die einzige Abhängigkeit.

```console
cd <path-to-repo>/cognitive-services-REST-api-samples/Tutorials/Bing-Web-Search
npm install
```

## <a name="app-components"></a>App-Komponenten

Die hier erstellte Beispiel-App hat vier Bestandteile:

* `bing-web-search.js`: Die „Express.js“-App. Sie verarbeitet die Anforderung/Antwort-Logik und das Routing.
* `public/index.html`: Die Struktur der App. Sie definiert, wie Daten Benutzern angezeigt werden.
* `public/css/styles.css`: Definiert Seitenformate, z.B. Schriftarten, Farben und Textgröße.
* `public/js/scripts.js`: Enthält die Logik, um Anforderungen an die Bing-Websuche-API zu senden, Abonnementschlüssel zu verwalten, Antworten zu verarbeiten und zu analysieren sowie Ergebnisse anzuzeigen.

Dieses Tutorial konzentriert sich auf `scripts.js` und die erforderliche Logik, um die Bing-Websuche-API aufzurufen und die Antwort zu verarbeiten.

## <a name="html-form"></a>HTML-Formular

`index.html` enthält ein Formular, mit dem Benutzer Suchvorgänge ausführen und Suchoptionen auswählen können. Das Attribut `onsubmit` wird ausgelöst, wenn das Formular gesendet wird, wobei die in `scripts.js` definierte `bingWebSearch()`-Methode aufgerufen wird. Es verwendet drei Argumente:

* Suchabfrage
* Ausgewählte Optionen
* Abonnementschlüssel

```html
<form name="bing" onsubmit="return bingWebSearch(this.query.value,
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="query-options"></a>Abfrageoptionen

Das HTML-Formular enthält Optionen, die Abfrageparametern in der [Bing-Websuche-API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) zugeordnet sind. Diese Tabelle zeigt eine Aufschlüsselung, wie Benutzer Suchergebnisse mithilfe der Beispiel-App filtern können:

| Parameter | BESCHREIBUNG |
|-----------|-------------|
| `query` | Ein Textfeld zur Eingabe einer Abfragezeichenfolge. |
| `where` | Ein Dropdownmenü zur Auswahl des Markts (Ort und Sprache). |
| `what` | Kontrollkästchen, um bestimmte Typen höherzustufen. Beispielsweise ändert das Höherstufen von Bildern ihre Relevanz in den Suchergebnissen. |
| `when` | Ein Dropdownmenü, mit dem der Benutzer die Suchergebnisse auf heute, diese Woche oder diesen Monat einschränken kann. |
| `safe` | Ein Kontrollkästchen, um Bing SafeSearch zu aktivieren, wodurch jugendgefährdender Inhalt herausgefiltert wird. |
| `count` | Verborgenes Feld. Die Anzahl der Suchergebnisse, die bei jeder Anforderung zurückgegeben werden. Bearbeiten Sie dieses Feld, um die Anzahl von angezeigten Ergebnisse pro Seite festzulegen. |
| `offset` | Verborgenes Feld. Der Offset für das erste Suchergebnis der Anforderung. Dieser wird zur Unterteilung der Suchergebnisse in mehrere Seiten verwendet. Der Offset wird für jede neue Anforderung auf `0` zurückgesetzt. |

> [!NOTE]
> Die Bing-Websuche-API bietet zusätzliche Abfrageparameter, mit denen Sie die Suchergebnisse verfeinern können. Dieses Beispiel verwendet nur einige davon. Eine vollständige Liste der verfügbaren Parameter finden Sie in der [Referenz für die Bing-Websuche-API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters).

Die Funktion `bingSearchOptions()` konvertiert diese Optionen in das für die Bing-Suche-API erforderliche Format.

```javascript
// Build query options from selections in the HTML form.
function bingSearchOptions(form) {

    var options = [];
    // Where option.
    options.push("mkt=" + form.where.value);
    // SafeSearch option.
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "moderate"));
    // Freshness option.
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++)
        if (form.what[i].checked) what.push(form.what[i].value);
    // Promote option.
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    // Count option.
    options.push("count=" + form.count.value);
    // Offset option.
    options.push("offset=" + form.offset.value);
    // Hardcoded text decoration option.
    options.push("textDecorations=true");
    // Hardcoded text format option.
    options.push("textFormat=HTML");
    return options.join("&");
}
```

`SafeSearch` kann auf `strict`, `moderate` oder `off` festgelegt werden, wobei `moderate` die Standardeinstellung für die Bing-Websuche ist. Dieses Formular verwendet ein Kontrollkästchen mit zwei Zuständen: `strict` oder `moderate`.

Wenn eines der Kontrollkästchen **Hochstufen** aktiviert ist, wird der Abfrage der `answerCount`-Parameter hinzugefügt. Bei der Verwendung des Parameters `promote` ist `answerCount` erforderlich. In diesem Ausschnitt wird der Wert auf `9` gesetzt, um alle verfügbaren Ergebnistypen zurückzugeben.
> [!NOTE]
> Das Höherstufen eines Ergebnistyps *garantiert nicht*, dass er in den Suchergebnissen enthalten ist. Vielmehr erhöht sich dadurch die Relevanz dieser Ergebnistypen ausgehend von den üblichen Relevanzwerten. Wenn Sie Suchergebnisse auf bestimmte Ergebnisse einschränken möchten, können Sie dazu den `responseFilter`-Abfrageparameter verwenden oder einen anderen Endpunkt wie die Bing-Bildersuche-API oder die Bing-News-Suche-API verwenden.

Die Abfrageparameter `textDecoration` und `textFormat` sind im Skript hartcodiert und bewirken, dass der Suchbegriff in den Suchergebnissen fett gedruckt wird. Diese Parameter sind nicht erforderlich.

## <a name="manage-subscription-keys"></a>Abonnementschlüssel verwalten

Um eine Hartcodierung des Bing-Suche-API-Abonnementschlüssels zu vermeiden, verwendet diese Beispiel-App den beständigen Speicher eines Browsers zum Speichern des Abonnementschlüssels. Wenn kein Abonnementschlüssel gespeichert ist, wird der Benutzer aufgefordert, einen einzugeben. Wenn der Abonnementschlüssel von der API abgelehnt wird, wird der Benutzer aufgefordert, den Abonnementschlüssel erneut einzugeben.

Die Funktion `getSubscriptionKey()` verwendet die Funktionen `storeValue` und `retrieveValue` zum Speichern und Abrufen des Abonnementschlüssels eines Benutzers. Diese Funktionen verwenden das `localStorage`-Objekt (sofern unterstützt) oder Cookies.

```javascript
// Cookie names for stored data.
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// See source code for storeValue and retrieveValue definitions.

// Get stored subscription key, or prompt if it isn't found.
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // Always set the cookie in order to update the expiration date.
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

Wie Sie bereits gesehen haben, wird beim Übermitteln des Formulars `onsubmit` ausgelöst und `bingWebSearch` aufgerufen. Diese Funktion initialisiert und sendet die Anforderung. `getSubscriptionKey` wird bei jeder Übermittlung aufgerufen, um die Anforderung zu authentifizieren.

## <a name="call-bing-web-search"></a>Aufrufen der Bing-Websuche

Die `BingWebSearch`-Funktion erstellt auf Grundlage der Abfrage, der Optionszeichenfolge und des Abonnementschlüssels ein `XMLHttpRequest`-Objekt, um den Bing-Websuche-API-Endpunkt aufzurufen.

```javascript
// Perform a search constructed from the query, options, and subscription key.
function bingWebSearch(query, options, key) {
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // Initialize the request.
    try {
        request.open("GET", queryurl);
    }
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // Add request headers.
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);

    // Event handler for successful response.
    request.addEventListener("load", handleBingResponse);

    // Event handler for errors.
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // Event handler for an aborted request.
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // Send the request.
    request.send();
    return false;
}
```

Nach einer erfolgreichen Anforderung wird der `load`-Ereignishandler ausgelöst und die `handleBingResponse`-Funktion aufgerufen. `handleBingResponse` analysiert das Ergebnisobjekt, zeigt die Ergebnisse an und enthält die Fehlerlogik für fehlgeschlagene Anforderungen.

```javascript
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // Try to parse results object.
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // Show raw JSON and the HTTP request.
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " +
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // If the HTTP response is 200 OK, try to render the results.
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is considered an error.
    else {
        // 401 is unauthorized; force a re-prompt for the user's subscription
        // key on the next request.
        if (this.status === 401) invalidateSubscriptionKey();

        // Some error responses don't have a top-level errors object, if absent
        // create one.
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // Display the HTTP status code.
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // Add all fields from all error responses.
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // Display Bing Trace ID if it isn't blocked by CORS.
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // Display the error message.
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> Eine erfolgreiche HTTP-Anforderung *bedeutet nicht*, dass die Suche erfolgreich war. Wenn bei einem Suchvorgang ein Fehler auftritt, gibt die Bing-Websuche-API einen HTTP-Statuscode zurück, der vom Code 200 abweicht und Fehlerinformationen in der JSON-Antwort enthält. Wenn die Anzahl von Anforderungen begrenzt war, gibt die API eine leere Antwort zurück.

Ein Großteil des Codes in den beiden vorangehenden Funktionen ist für die Fehlerbehandlung zuständig. In folgenden Phasen können Fehler auftreten:

| Phase | Mögliche Fehler | Verarbeitet durch |
|-------|--------------------|------------|
| Erstellen des Anforderungsobjekts | Ungültige URL | `try` / `catch`-Block |
| Senden der Anforderung | Netzwerkfehler, abgebrochene Verbindungen | Ereignishandler `error` und `abort` |
| Durchführen der Suche | Ungültige Anforderung, ungültiger JSON-Code, Beschränkung der Anforderungsanzahl | Tests im `load`-Ereignishandler |

Fehler werden durch Aufrufen von `renderErrorMessage()` verarbeitet. Wenn die Antwort alle Fehlertests besteht, wird `renderSearchResults()` aufgerufen, um die Suchergebnisse anzuzeigen.

## <a name="display-search-results"></a>Anzeigen von Suchergebnissen

Es gibt [Verwendungs- und Anzeigeanforderungen](./use-display-requirements.md) für Ergebnisse, die von der Bing-Websuche-API zurückgegeben werden. Da eine Antwort verschiedene Ergebnistypen beinhalten kann, reicht es nicht aus, die `WebPages`-Auflistung auf der obersten Ebene zu durchlaufen. Die Beispiel-App verwendet stattdessen `RankingResponse` zum Sortieren der anzugebenden Ergebnisse.

> [!NOTE]
> Wenn Sie nur einen einzigen Ergebnistyp wünschen, verwenden Sie den Abfrageparameter `responseFilter` oder einen anderen Endpunkt für die Bing-Suche, z.B. die Bing-Bildersuche.

Jede Antwort hat ein `RankingResponse`-Objekt, das bis zu drei Auflistungen enthalten kann: `pole`, `mainline` und `sidebar`. `pole` (falls vorhanden) ist das relevanteste Suchergebnis und muss deutlich sichtbar angezeigt werden. `mainline` enthält den Großteil der Suchergebnisse und wird direkt nach `pole` angezeigt. `sidebar` umfasst Hilfssuchergebnisse. Diese Ergebnisse sollten nach Möglichkeit in der Seitenleiste angezeigt werden. Wenn sich eine Seitenleiste aufgrund der Bildschirmgröße als unpraktisch erweist, sollten diese Ergebnisse nach den `mainline`-Ergebnissen angezeigt werden.

Jedes `RankingResponse`-Objekt enthält ein `RankingItem`-Array, das die Reihenfolge der Ergebnisse angibt. Die Beispiel-App verwendet die Parameter `answerType` und `resultIndex` zum Identifizieren des Ergebnisses.

> [!NOTE]
> Es gibt weitere Möglichkeiten zum Identifizieren und Sortieren von Ergebnissen. Weitere Informationen finden Sie unter [Verwenden der Rangfolge zum Anzeigen von Ergebnissen](rank-results.md).

Sehen wir uns den Code an:

```javascript
// Render the search results from the JSON response.
function renderSearchResults(results) {

    // If spelling was corrected, update the search field.
    if (results.queryContext.alteredQuery)
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // Add Prev / Next links with result count.
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    // Render the results for each section.
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

Die Funktion `renderResultsItems()` durchläuft die Elemente in jeder `RankingResponse`-Auflistung, ordnet mithilfe der Werte für `answerType` und `resultIndex` jedem Rangfolgeergebnis ein Suchergebnis zu und ruft die entsprechende Renderingfunktion auf, um den HTML-Code zu generieren. Wenn `resultIndex` für kein Element angegeben wird, durchläuft `renderResultsItems()` alle Ergebnisse dieses Typs und ruft die Renderingfunktion für jedes Element auf. Der erzeugte HTML-Code wird in das entsprechende `<div>`-Element in `index.html` eingefügt.

```javascript
// Render search results from the RankingResponse object per rank response and
// use and display requirements.
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // Collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` RankingResult type is in the `webPages` top-level collection.
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // This ranking item refers to ONE result of the specified type.
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // This ranking item refers to ALL results of the specified type.
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="review-renderer-functions"></a>Überprüfen von Rendererfunktionen

In der Beispiel-App enthält das `searchItemRenderers`-Objekt Funktionen, die HTML für jeden Suchergebnistyp generieren.

```javascript
// Render functions for each result type.
searchItemRenderers = {
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!IMPORTANT]
> Die Beispiel-App nutzt Renderer für Webseiten, Nachrichten, Bilder, Videos und verwandte Suchvorgänge. Ihre Anwendung benötigt Renderer für alle Ergebnistypen, die möglicherweise empfangen werden. Dazu gehören beispielsweise Berechnungen, Rechtschreibvorschläge, Entitäten, Zeitzonen und Definitionen.

Einige Renderingfunktionen akzeptieren nur den Parameter `item`. Andere Funktionen akzeptieren zusätzliche Parameter, mit denen Elemente je nach Kontext unterschiedlich gerendert werden. Ein Renderer, der diese Informationen nicht verwendet, muss diese Parameter nicht akzeptieren.

Folgende Kontextargumente sind verfügbar:

| Parameter  | BESCHREIBUNG |
|------------|-------------|
| `section` | Der Ergebnisbereich (`pole`, `mainline` oder `sidebar`), in dem das Element angezeigt wird. |
| `index`<br>`count` | Verfügbar, wenn durch das `RankingResponse`-Element angegeben wird, dass alle Ergebnisse einer Collection angezeigt werden sollen. Andernfalls wird `undefined` verwendet. Der Index des Elements innerhalb seiner Auflistung und die Gesamtzahl von Elementen in dieser Auflistung. Sie können mit diesen Informationen beispielsweise die Ergebnisse nummerieren oder für das erste und letzte Ergebnis unterschiedlichen HTML-Code erzeugen. |

In der Beispiel-App nutzen die Renderer `images` und `relatedSearches` Kontextargumente, um den erzeugten HTML-Code anzupassen. Der Renderer `images` soll nun genauer betrachtet werden:

```javascript
searchItemRenderers = {
    // Render image result with thumbnail.
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    },
    // Other renderers are omitted from this sample...
}
```

Bildrenderer:

* Berechnen der Miniaturbildgröße (die Breite variiert, die Höhe ist auf 60 Pixel festgelegt)
* Kontextabhängiges Einfügen des HTML-Codes, der dem Bildergebnis vorausgeht
* Erstellen des HTML-`<a>`-Tags, das einen Link zur Seite mit dem Bild erstellt
* Erstellt das HTML-Tag `<img>`, mit dem das Miniaturbild angezeigt wird.

Der Bildrenderer verwendet die Variablen `section` und `index`, um die Ergebnisse je nach Anzeigeposition unterschiedlich darzustellen. Zwischen den Bildergebnissen in der Seitenleiste wird ein Zeilenumbruch (`<br>`-Tag) eingefügt, damit in der Seitenleiste eine Spalte mit Bildern angezeigt wird. In anderen Bereichen geht dem ersten Bildergebnis `(index === 0)` ein `<p>`-Tag voran.

Die Größe des Miniaturbilds wird sowohl im `<img>`-Tag als auch in den Feldern `h` und `w` in der URL des Miniaturbilds verwendet. Die Attribute `title` und `alt` zur Beschreibung des Bilds werden aus dem Bildnamen und dem Hostnamen aus der URL erstellt.

Hier ist ein Beispiel dafür, wie Bilder in der Beispiel-App angezeigt werden:

![[Bing-Bilderergebnisse]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persist-the-client-id"></a>Beibehalten der Client-ID

Antworten von Bing-Suche-APIs können einen `X-MSEdge-ClientID`-Header enthalten, der mit jeder nachfolgenden Anforderung an die API zurückgesendet werden sollte. Wenn mehr als eine der Bing-Suche-APIs von Ihrer App verwendet wird, stellen Sie sicher, dass bei jeder Anforderung die gleiche Client-ID dienstübergreifend gesendet wird.

Durch das Bereitstellen des `X-MSEdge-ClientID`-Headers können die Bing-Suche-APIs die Suchvorgänge eines Benutzers zuordnen. Erstens kann die Bing-Suchmaschine auf diese Weise Kontextinformationen aus vorherigen Suchvorgängen nutzen, um Ergebnisse anzuzeigen, die der Anforderung besser entsprechen. Wenn ein Benutzer in der Vergangenheit beispielsweise nach Segelbegriffen gesucht hat, können bei einer Suche nach „Knoten“ möglicherweise Informationen zu Segelknoten zurückgegeben werden. Zweitens wählt Bing möglicherweise Benutzer zufällig aus, die die Möglichkeit haben, neue Features zu testen, bevor diese allen Benutzern zur Verfügung gestellt werden. Wenn Sie bei jeder Anforderung dieselbe Client-ID bereitstellen, stellen Sie sicher, dass Benutzer, die zur Nutzung eines neuen Features ausgewählt wurden, dieses Feature dauerhaft nutzen können. Ohne die Client-ID wird das Feature in den Suchergebnissen möglicherweise scheinbar willkürlich aus- oder eingeblendet.

Browsersicherheitsrichtlinien, z. B. CORS (Cross-Origin Resource Sharing), können verhindern, dass die Beispiel-App auf den `X-MSEdge-ClientID`-Header zugreift. Diese Einschränkung tritt auf, wenn sich der Ursprung der Suchantwort von dem der Seite unterscheidet, die den Suchvorgang angefordert hat. In einer Produktionsumgebung sollten Sie zum Umgang mit dieser Richtlinie ein serverseitiges Skript hosten, das den API-Aufruf für die Domain durchführt, die auch für die Webseite genutzt wird. Da der Ursprung des Skripts mit dem Ursprung der Webseite übereinstimmt, kann der `X-MSEdge-ClientID`-Header von JavaScript verwendet werden.

> [!NOTE]
> In einer Webanwendung für eine Produktionsumgebung sollten Sie die Anforderung in jedem Fall serverseitig ausführen. Andernfalls muss der Abonnementschlüssel der Bing-Suche-API auf der Webseite hinterlegt werden, wo er im Quelltext für alle Benutzer zugänglich ist. Dies müssen Sie vermeiden, da ansonsten unbefugte Dritte Anforderungen unter Verwendung Ihres API-Abonnementschlüssels Anforderungen senden können, die Ihnen in Rechnung gestellt werden.

Zu Entwicklungszwecken können Sie eine Anforderung über ein CORS-Proxy senden. In der Antwort eines solchen Proxytyps befindet sich ein `Access-Control-Expose-Headers`-Header. Dieser filtert Antwortheader, die JavaScript zur Verfügung gestellt werden.

Die Installation eines CORS-Proxys, mit dem die Beispiel-App auf den Client-ID-Header zugreifen kann, ist schnell und unkompliziert. Führen Sie den folgenden Befehl aus:

```console
npm install -g cors-proxy-server
```

Ändern Sie den Endpunkt der Bing-Websuche in `script.js` wie folgt:

```javascript
http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search
```

Starten Sie den CORS-Proxy mit diesem Befehl:

```console
cors-proxy-server
```

Lassen Sie das Befehlsfenster während der Nutzung der Beispiel-App geöffnet. Wenn Sie das Fenster schließen, wird auch die Ausführung des Proxys beendet. Im Bereich des erweiterbaren HTTP-Headers unter den Suchergebnissen sollte der `X-MSEdge-ClientID`-Header angezeigt werden. Überprüfen Sie, ob dieser für jede Anforderung identisch ist.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Referenz zur Bing-Websuche-API v7](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)