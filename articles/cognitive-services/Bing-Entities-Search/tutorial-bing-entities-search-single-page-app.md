---
title: 'Tutorial: Single-Page-Webanwendung für die Bing-Entitätssuche'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird beschrieben, wie Sie die Bing-Entitätssuche-API in einer Single-Page-Webanwendung verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: f725a4095103a7dcfc3dcdbdcefdc84d16501632
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366532"
---
# <a name="tutorial-single-page-web-app"></a>Tutorial: Einzelseiten-Web-App

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mit der Bing-Entitätssuche-API können Sie das Web nach Informationen zu *Entitäten* und *Orten* durchsuchen. Sie können in einer Abfrage einen oder auch beide Ergebnistypen anfordern. Orte und Entitäten sind wie folgt definiert:

| Ergebnis | BESCHREIBUNG |
|-|-|
|Entitäten|Bekannte Personen, Orte und Objekte, die durch die Angabe eines Namens gefunden werden können|
|Orte|Restaurants, Hotels und andere lokale Unternehmen, die durch die Angabe eines Namens *oder* eines Typs (beispielsweise „Italienische Restaurants“) gefunden werden können|

In diesem Tutorial wird eine Single-Page-Webanwendung erstellt, die unter Verwendung der Bing-Entitätssuche-API Suchergebnisse direkt auf der Seite anzeigt. Die Anwendung enthält HTML-, CSS- und JavaScript-Komponenten.

Mit der API lässt sich die Anzeige der Ergebnisse nach Standort priorisieren. In einer mobilen App können Sie ein Gerät dazu auffordern, seinen Standort zu übermitteln. In einer Webanwendung können Sie die `getPosition()`-Funktion verwenden. Dieser Aufruf ist allerdings nur in sicheren Kontexten möglich, und die Angabe des Standorts ist möglicherweise unpräzise. Des Weiteren sucht der Benutzer möglicherweise nach Entitäten in der Nähe eines Standorts, der nicht mit seinem eigenen übereinstimmt.

Die Tutorial-App ruft daher den Bing Karten-Dienst auf, um den Breiten- und Längengrad eines Standorts abzurufen, der vom Benutzer bereitgestellt wurde. Der Benutzer kann anschließend den Namen eines markanten Gebäudes (z.B. „Space Needle“) oder eine vollständige Adresse oder Teiladresse (beispielsweise „New York City“) eingeben. Die Bing Karten-API stellt daraufhin die Koordinaten bereit.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> Die JSON-Antwort und HTTP-Anforderungsinformationen werden über die JSON- und HTTP-Header am unteren Rand der Seite angezeigt, wenn Sie auf diese klicken. Diese Details sind hilfreich, wenn Sie sich genauer mit dem Dienst befassen.

In dieser Tutorial-App wird Folgendes veranschaulicht:

> [!div class="checklist"]
> * Aufrufen der Bing-Entitätssuche-API in JavaScript
> * Aufrufen der Bing Karten-`locationQuery`-API in JavaScript
> * Übergeben von Suchoptionen an API-Aufrufe
> * Anzeigen von Suchergebnissen
> * Verarbeiten der Bing-Client-ID und der API-Abonnementschlüssel
> * Behandeln von möglicherweise auftretenden Fehlern

Die Tutorialseite ist unabhängig von anderen Komponenten und verwendet keine externen Frameworks oder Stylesheets. Auch Bilddateien kommen nicht zum Einsatz. Die Seite greift nur auf die am häufigsten unterstützten Features für JavaScript zurück und kann in aktuellen Versionen aller gängigen Webbrowser ausgeführt werden.

In diesem Tutorial werden nur ausgewählte Teile des Quellcodes erläutert. Der vollständige Quellcode ist [auf einer separaten Seite]() verfügbar. Kopieren Sie diesen Code, fügen Sie ihn in einen Text-Editor ein, und speichern Sie die Datei als `bing.html`.

> [!NOTE]
> Dieses Tutorial ähnelt dem [Tutorial zum Verwenden der Bing-Websuche-API in Single-Page-Apps](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) sehr stark, befasst sich aber nur mit Entitätssuchergebnissen.

## <a name="prerequisites"></a>Voraussetzungen

Um dem Tutorial folgen zu können, benötigen Sie Abonnementschlüssel für die Bing-Suche-API und die Bing Karten-API. 

* Azure-Abonnement: [Kostenloses Azure-Konto](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über ein Azure-Abonnement verfügen:
  * <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Erstellen Sie eine Ressource für die Bing-Suche"  target="_blank">Erstellen einer Ressource für die Bing-Suche<span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
  * <a href="https://www.microsoft.com/maps/create-a-bing-maps-key.aspx"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank">Erstellen Sie eine Bing Maps-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.

## <a name="app-components"></a>App-Komponenten

Die Tutorialanwendung setzt sich ebenso wie alle anderen Single-Page-Webanwendungen aus drei Teilen zusammen:

> [!div class="checklist"]
> * HTML: Definiert die Struktur und der Inhalt der Seite
> * CSS: Definiert das Layout der Seite
> * JavaScript: Definiert das Verhalten der Seite

In diesem Tutorial wird ein Großteil des HTML- und CSS-Codes nicht ausführlich behandelt, da dieser selbsterklärend ist.

Der HTML-Teil enthält das Suchformular, in dem der Benutzer eine Abfrage eingibt und Suchoptionen auswählt. Das Formular wird vom JavaScript-Teil ausgewertet, der die Suche mithilfe des `onsubmit`-Attributs des `<form>`-Tags ausführt:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Der `onsubmit`-Handler gibt `false` zurück, wodurch verhindert wird, dass das Formular an den Server gesendet wird. Der JavaScript-Code ist dafür verantwortlich, die erforderlichen Informationen aus dem Formular abzurufen und die Suche durchzuführen.

Die Suche wird in zwei Schritten durchgeführt. Falls der Benutzer seinen Standort eingeschränkt hat, wird eine Bing Karten-Abfrage durchgeführt, mit der der Standort in Koordinaten umgewandelt wird. Der Rückruf für diese Abfrage startet anschließend die Abfrage für die Bing-Entitätssuche-API.

Der HTML-Teil enthält auch die Bereiche (`<div>`-HTML-Tags), in denen die Suchergebnisse angezeigt werden.

## <a name="managing-subscription-keys"></a>Verwalten von Abonnementschlüsseln

> [!NOTE]
> Zur Nutzung der Bing-Suche-API und der Bing Karten-API sind Abonnementschlüssel für die App erforderlich.

Zum Speichern der Schlüssel wird der persistente Browserspeicher verwendet. Auf diese Weise müssen Sie nicht die Abonnementschlüssel der Bing-Suche-API und der Bing Karten-API im Code hinterlegen. Wenn einer der beiden Schlüssel nicht gespeichert wurde, wird der Benutzer zu dessen Eingabe aufgefordert. Der Schlüssel wird dann zur späteren Verwendung gespeichert. Wird der Schlüssel zu einem späteren Zeitpunkt von der API zurückgewiesen, wird der gespeicherte Schlüssel für ungültig erklärt, und der Benutzer wird bei der nächsten Suche zur Eingabe aufgefordert.

In den Funktionen `storeValue` und `retrieveValue` wird entweder das `localStorage`-Objekt (sofern vom Browser unterstützt) oder ein Cookie verwendet. Diese Funktionen werden in der `getSubscriptionKey()`-Funktion zum Speichern und Abrufen des Benutzerschlüssels verwendet. Sie können den unten angegebenen globalen Endpunkt oder den Endpunkt der [benutzerdefinierten Unterdomäne](../../cognitive-services/cognitive-services-custom-subdomains.md) verwenden, der im Azure-Portal für Ihre Ressource angezeigt wird.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "https://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

Der HTML-Tag `<body>` enthält ein `onload`-Attribut, das nach dem Laden der Seite `getSearchSubscriptionKey()` und `getMapsSubscriptionKey()` aufruft. Durch diese Aufrufe wird der Benutzer unmittelbar dazu aufgefordert, seine Schlüssel einzugeben, falls dies noch nicht geschehen ist.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Auswählen der Suchoptionen

![[Bing-Entitätssuche-API-Formular]](media/entity-search-spa-form.png)

Das HTML-Formular enthält die folgenden Steuerelemente:

| Control | BESCHREIBUNG |
|-|-|
|`where`|Ein Dropdownmenü zur Auswahl des Markts (Ort und Sprache), der für die Suche verwendet wird.|
|`query`|Das Textfeld, in das die Suchbegriffe eingegeben werden.|
|`safe`|Ein Kontrollkästchen, mit dem festgelegt wird, ob das SafeSearch-Feature von Bing verwendet werden soll. Mit diesem werden Inhalte herausgefiltert, die für Minderjährige nicht geeignet sind.|
|`what`|Ein Menü, in dem festgelegt werden kann, ob nach Entitäten, Orten oder beidem gesucht werden soll.|
|`mapquery`|Das Textfeld, in das der Benutzer z.B. eine vollständige Adresse, Teiladresse oder den Namen eines markanten Gebäudes eingeben kann. Dadurch gibt die Bing-Entitätssuche-API relevantere Ergebnisse zurück.|

> [!NOTE]
> Ortsergebnisse sind aktuell nur in den USA verfügbar. Die Menüs `where` und `what` enthalten Code, mit dem diese Einschränkung umgesetzt wird. Wenn Sie einen Markt außerhalb der USA auswählen und „Places“ (Orte) im `what`-Menü ausgewählt ist, wird in `what` der Eintrag „Anything“ (Alle) ausgewählt. Wenn Sie „Places“ auswählen und ein Markt außerhalb der USA im `where`-Menü ausgewählt ist, wird in `where` der Eintrag „US“ ausgewählt.

Die JavaScript-Funktion `bingSearchOptions()` konvertiert diese Felder in eine Teilabfragezeichenfolge für die Bing-Suche-API.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Für das SafeSearch-Feature können beispielsweise die Werte `strict`, `moderate` oder `off` festgelegt werden, wobei `moderate` der Standardwert ist. In unserem Formular wird hingegen ein Kontrollkästchen verwendet, das nur über zwei Zustände verfügt. Der JavaScript-Code konvertiert diese Einstellung entweder in `strict` oder `off` (`moderate` wird nicht verwendet).

Das `mapquery`-Feld wird nicht in `bingSearchOptions()` behandelt, da es nur für die Bing Karten-Standortabfrage und nicht für die Bing-Entitätssuche-API verwendet wird.

## <a name="obtaining-a-location"></a>Abrufen eines Standorts

Die Bing Karten-API stellt eine [`locationQuery`-Methode](//msdn.microsoft.com/library/ff701711.aspx) bereit, mit der der Breiten- und Längengrad eines Standorts abgerufen wird, der vom Benutzer angegeben wurde. Diese Koordinaten werden anschließend zusammen mit der Benutzeranforderung der Bing-Entitätssuche-API übergeben. In den Suchergebnisse werden die Entitäten und Orte priorisiert, die sich in der Nähe des angegebenen Standorts befinden.

Ein Zugriff auf die Bing Karten-API mit einer normalen `XMLHttpRequest`-Abfrage in einer Web-App ist nicht möglich, da der Dienst keine herkunftsübergreifenden Abfragen unterstützt. Dafür wird allerdings JSONP (JSON mit Padding) unterstützt. Eine JSONP-Antwort ist eine normale JSON-Antwort, die von einem Funktionsaufruf umschlossen ist. Die Anforderung erfolgt durch Einfügen des `<script>`-Tags in ein Dokument (das Laden von Skripts wird nicht von Browsersicherheitsrichtlinien unterbunden).

Die `bingMapsLocate()`-Funktion erstellt das `<script>`-Tag und fügt es für die Abfrage ein. Im `jsonp=bingMapsCallback`-Segment der Abfragezeichenfolge wird der Name der Funktion angegeben, die mit der Antwort aufgerufen wird.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Wenn die Bing Karten-API nicht antwortet, wird die `bingMapsCallBack()`-Funktion nie aufgerufen. In diesem Fall würde `bingEntitySearch()` nicht aufgerufen werden, und die Entitätssuchergebnisse würden nicht angezeigt. Dieses Szenario wird vermieden, indem `bingMapsLocate()` zusätzlich einen Timer festlegt, der nach fünf Sekunden `bingEntitySearch()` aufruft. Durch die Logik in der Rückruffunktion wird verhindert, dass die Entitätssuche zweimal ausgeführt wird.

Wenn die Abfrage abgeschlossen ist, wird die `bingMapsCallback()`-Funktion entsprechend der Anforderung aufgerufen.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Die Abfrage für die Bing-Entitätssuche-API erfordert neben Breiten- und Längengrad auch einen *Radius* , der die Genauigkeit der Standortinformationen angibt. Der Radius wird mithilfe des *umgebenden Rechtecks* berechnet, das von der Bing Karten-Antwort bereitgestellt wird. Dabei handelt es sich um ein Rechteck, das den Standort umgibt. Wenn der Benutzer z.B. `NYC` eingibt, enthält das Ergebnis die ungefähren Koordinaten für das Zentrum von New York City und ein umgebendes Rechteck, das die Stadt einschließt. 

Zuerst werden die Abstände der primären Koordinaten zu allen vier Ecken des umgebenden Rechtecks verwendet. Dazu wird die Funktion `haversineDistance()` (hier nicht dargestellt) genutzt. Als Radius wird dann der größte der vier Abstände verwendet. Der Mindestradius beträgt ein Kilometer. Dieser Wert wird auch als Standard verwendet, wenn kein umgebendes Rechteck in der Antwort bereitgestellt wird.

Nach Abruf der Koordinaten und des Radius wird die `bingEntitySearch()`-Funktion aufgerufen, die die eigentliche Suche ausführt.

## <a name="performing-the-search"></a>Durchführen der Suche

Die `BingEntitySearch()`-Funktion sendet die Bing-Entitätssuche-API-Anforderung auf der Grundlage der Abfrage, des Standorts, der Optionszeichenfolge und des API-Schlüssels.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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

Nach erfolgreichem Abschluss der HTTP-Anforderung ruft JavaScript den `load`-Ereignishandler und damit die `handleBingResponse()`-Funktion auf, um eine erfolgreiche HTTP-GET-Anforderung an die API zu verarbeiten. 

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> Eine erfolgreiche HTTP-Anforderung ist *nicht* zwangsläufig mit einer erfolgreichen Suche gleichzusetzen. Wenn bei einem Suchvorgang ein Fehler auftritt, gibt die Bing-Entitätssuche-API einen HTTP-Statuscode zurück, der vom Code 200 abweicht und Fehlerinformationen in der JSON-Antwort enthält. Wenn die Anzahl der Anforderungen begrenzt war, gibt die API zusätzlich eine leere Antwort zurück.

Ein Großteil des Codes in den beiden vorangehenden Funktionen ist für die Fehlerbehandlung zuständig. In folgenden Phasen können Fehler auftreten:

|Phase|Mögliche Fehler|Verarbeitet durch|
|-|-|-|
|Erstellen eines JavaScript-Anforderungsobjekts|Ungültige URL|`try`/`catch`-Block|
|Senden der Anforderung|Netzwerkfehler, abgebrochene Verbindungen|Ereignishandler `error` und `abort`|
|Durchführen der Suche|Ungültige Anforderung, ungültiger JSON-Code, Beschränkung der Anforderungsanzahl|Tests im `load`-Ereignishandler|

Fehler werden behandelt, indem `renderErrorMessage()` zusammen mit allen bekannten Fehlerinformationen aufgerufen wird. Wenn die Antwort alle Fehlertests besteht, wird `renderSearchResults()` zur Anzeige der Suchergebnisse auf der Seite aufgerufen.

## <a name="displaying-search-results"></a>Anzeigen der Suchergebnisse

Die Bing-Entitätssuche-API setzt voraus, dass Sie sich die Ergebnisse in einer [festgelegten Reihenfolge anzeigen lassen](../bing-web-search/use-display-requirements.md). Da die API zwei Antworttypen zurückgegeben kann, ist zur Anzeige der Ergebnisse mehr erforderlich als das Durchlaufen der übergeordneten `Entities`- oder `Places`-Sammlung in der JSON-Antwort. Wenn Sie nur ein Ergebnistyp empfangen möchten, können Sie den `responseFilter`-Abfrageparameter verwenden.

In diesem Tutorial wird allerdings die `rankingResponse`-Collection verwendet, um die Reihenfolge der Suchergebnisse festzulegen und diese anzuzeigen. Dieses Objekt verweist auf Elemente in der Sammlung `Entitiess` bzw. `Places`.

`rankingResponse` kann bis zu drei Suchergebnisse-Collections mit den Bezeichnungen `pole`, `mainline` und `sidebar` enthalten. 

Bei `pole` handelt es sich um das relevanteste Suchergebnis. Wenn dieses vorhanden ist, wird es in der Liste der Suchergebnisse oben angezeigt. `mainline` verweist auf den Hauptanteil der Suchergebnisse. Die Ergebnisse im Hauptbereich sollten unmittelbar nach `pole` oder ganz oben angezeigt werden, falls `pole` nicht vorhanden ist. 

Die letzte Collection `sidebar` verweist auf Hilfssuchergebnisse. Diese können in einer Seitenleiste oder unter den Hauptbereichsergebnissen angezeigt werden. Für dieses Tutorial wird die letztgenannte Anzeigeoption verwendet.

Jedes Element in einer `rankingResponse`-Collection verweist auf die tatsächlichen Suchergebnisse. Dieser Verweis kann auf zwei Arten erfolgen.

| Element | BESCHREIBUNG |
|-|-|
|`id`|`id` ähnelt einer URL, sollte allerdings nicht für Links verwendet werden. Der `id`-Typ eines Rangfolgenergebnisses stimmt mit der `id` eines Suchergebniselements in einer Antwortcollection *oder* einer gesamten Antwortcollection (beispielsweise `Entities`) überein.
|`answerType`<br>`resultIndex`|`answerType` verweist auf die übergeordnete Antwortcollection, die das Ergebnis enthält (beispielsweise `Entities`). `resultIndex` verweist auf den Ergebnisindex innerhalb dieser Collection. Wenn `resultIndex` nicht angegeben wird, verweist das Rangfolgenergebnis auf die gesamte Collection.

> [!NOTE]
> Weitere Informationen zu diesem Teil der Suchantwort finden Sie unter [Sortieren von Ergebnissen nach Rangfolge](rank-results.md).

Sie können bei der Suche nach dem Suchergebniselement, auf das verwiesen wird, die für Ihre Anwendung am besten geeignete Methode verwenden. In diesem Tutorialcode werden `answerType` und `resultIndex` zum Finden der Suchergebnisse verwendet.

Abschließend soll nun die `renderSearchResults()`-Funktion betrachtet werden. Die Funktion durchläuft die drei `rankingResponse`-Collections, die die drei Bereiche der Suchergebnisse darstellen. Zum Rendern der Bereichsergebnisse wird `renderResultsItems()` für jeden Bereich aufgerufen.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // for each possible section, render the results from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Rendern von Ergebniselementen

Im JavaScript-Code dieses Tutorials enthält das `searchItemRenderers`-Objekt *Renderer* , also Funktionen, die für jeden Suchergebnistyp HTML-Code generieren.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

Für eine Rendererfunktion können die folgenden Parameter angegeben werden:

| Parameter | BESCHREIBUNG |
|-|-|
|`item`|Das JavaScript-Objekt mit Eigenschaften des Elements, z.B. seine URL und Beschreibung.|
|`index`|Der Index des Ergebniselements innerhalb der Auflistung.|
|`count`|Die Anzahl der Elemente in der Auflistung der Suchergebniselemente.|

Die Parameter `index` und `count` können beispielsweise zum Nummerieren von Ergebnissen, zum Generieren von speziellem HTML-Code für den Anfang oder das Ende einer Collection oder zum Einfügen von Zeilenumbrüchen nach einer bestimmten Anzahl von Elementen verwendet werden. Wenn ein Renderer diese Funktionalität nicht benötigt, muss er diese beiden Parameter nicht akzeptieren. Auch in den Renderern für die Tutorial-App werden diese nicht verwendet.

Der Renderer `entities` soll nun genauer betrachtet werden:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Die Funktion zum Rendern von Entitäten führt folgende Vorgänge aus:

> [!div class="checklist"]
> * Erstellen des HTML-`<img>`-Tags, mit dem das Miniaturbild angezeigt wird, falls ein solches vorhanden ist 
> * Erstellen des HTML-`<a>`-Tags, das einen Link zur Seite mit dem Bild erstellt
> * Erstellt die Beschreibung, die Informationen über das Bild und die Website, auf der sich das Bild befindet, angibt.
> * Einbinden der Entitätsklassifizierung unter Verwendung der Anzeigehinweise, falls solche vorhanden sind
> * Einbinden eines Link zu einer Bing-Suche, mit der weitere Informationen zur Entität abgerufen werden können
> * Anzeigen von Lizenz- oder Zuordnungsinformationen, die für Datenquellen erforderlich sind

## <a name="persisting-client-id"></a>Persistentes Speichern der Client-ID

Antworten, die von Bing-Suche-APIs gesendet werden, können einen `X-MSEdge-ClientID`-Header enthalten, der mit den nachfolgenden Anforderungen an die API zurückgesendet werden sollte. Wenn mehrere Bing-Suche-APIs genutzt werden, sollte nach Möglichkeit dieselbe Client-ID für alle APIs verwendet werden.

Durch die Bereitstellung des `X-MSEdge-ClientID`-Headers können die Bing-APIs alle Suchvorgänge eines Benutzers zuordnen, was zwei entscheidende Vorteile mit sich bringt.

Erstens kann die Bing-Suchmaschine auf diese Weise Kontextinformationen aus vorherigen Suchvorgängen nutzen, um den Benutzern relevantere Ergebnisse anzuzeigen. Wenn ein Benutzer in der Vergangenheit beispielsweise nach Segelbegriffen gesucht hat, können bei einer Suche nach „Anlegestellen“ möglicherweise Informationen zu Anlegestellen für Segelboote zurückgegeben werden.

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
> [Bing Entity Search API reference (Referenzdokumentation zur Bing-Entitätssuche-API)](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Bing Maps API documentation (Dokumentation zur Bing Karten-API)](//msdn.microsoft.com/library/dd877180.aspx)