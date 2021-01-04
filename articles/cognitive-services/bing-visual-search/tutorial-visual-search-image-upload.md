---
title: 'Tutorial: Hochladen von Bildern mit der API für die visuelle Bing-Suche'
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie ein Bild in Bing hochladen, Erkenntnisse dazu gewinnen und die Antwort anzeigen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.custom: devx-track-js
ms.openlocfilehash: 96a4b13d11e40e24e78d3aed8dfebcc88b41c525
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486877"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>Tutorial: Hochladen von Bildern in die API für die visuelle Bing-Suche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Mit der API für die visuelle Bing-Suche können Sie das Web nach Bildern durchsuchen, die den von Ihnen hochgeladenen Bildern ähneln. Erstellen Sie mithilfe dieses Tutorials eine Webanwendung, die ein Bild an die API senden und die zurückgegebenen Erkenntnisse auf der Webseite anzeigen kann. Beachten Sie, dass diese Anwendung nicht allen [Verwendungs- und Anzeigeanforderungen für Bing](../bing-web-search/use-display-requirements.md) zur Verwendung der API entspricht.

Der vollständige Quellcode für dieses Beispiel steht mit zusätzlichen Fehlerbehandlungen und Anmerkungen auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html) bereit.

In dieser Tutorial-App wird Folgendes veranschaulicht:

> [!div class="checklist"]
> * Hochladen eines Bilds in die API für die visuelle Bing-Suche
> * Anzeigen der Suchergebnisse für Bilder in einer Webanwendung
> * Untersuchen der verschiedenen Erkenntnisse, die von der API bereitgestellt werden

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>Erstellen und Strukturieren der Webseite

Erstellen Sie eine HTML-Seite, die ein Bild an die API für die visuelle Bing-Suche sendet, Erkenntnisse empfängt und diese anzeigt. Erstellen Sie in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE eine Datei namens „uploaddemo.html“. Fügen Sie der Datei die folgende grundlegende HTML-Struktur hinzu:

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

Unterteilen Sie die Seite in einen Anforderungsabschnitt, in dem der Benutzer alle für die Anforderung erforderlichen Informationen angibt, und einen Antwortabschnitt, in dem die Erkenntnisse angezeigt werden. Fügen Sie dem Element `<body>` die folgenden `<div>`-Tags hinzu. Das `<hr>`-Tag trennt den Anforderungsabschnitt visuell vom Antwortabschnitt:

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

Fügen Sie ein `<script>`-Tag zum `<head>`-Tag hinzu, das den JavaScript-Code für die Anwendung enthalten soll:

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>Abrufen der Datei für den Upload

Damit der Benutzer ein hochzuladendes Bild auswählen kann, verwendet die Anwendung das `<input>`-Tag, wobei das Typattribut auf `file` festgelegt ist. Auf der Benutzeroberfläche muss deutlich werden, dass die Anwendung Bing verwendet, um die Suchergebnisse zu erhalten.

Fügen Sie `<div>` wie folgt zu `requestSection` `<div>` hinzu. Als Dateieingabe wird eine einzelne Datei mit einem beliebigen Bildtyp (z.B. JPG, GIF, PNG) akzeptiert. Das `onchange`-Ereignis gibt den Handler an, der aufgerufen wird, wenn ein Benutzer eine Datei auswählt.

Das `<output>`-Tag wird verwendet, um eine Miniaturansicht des ausgewählten Bilds anzuzeigen:

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>Erstellen eines Dateihandlers

Erstellen Sie eine Handlerfunktion, mit der das Bild, das Sie hochladen möchten, eingelesen werden kann. Während die Dateien im `FileList`-Objekt durchlaufen werden, sollte der Handler sicherstellen, dass die ausgewählte Datei eine Bilddatei ist und ihre Größe 1 MB nicht übersteigt. Wenn das Bild größer ist, müssen Sie die Größe vor dem Upload verringern. Der Handler zeigt schließlich eine Miniaturansicht des Bilds an:

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>Hinzufügen und Speichern eines Abonnementschlüssels

Die Anwendung benötigt einen Abonnementschlüssel, um die API für die visuelle Bing-Suche aufzurufen. In diesem Tutorial stellen Sie ihn auf der Benutzeroberfläche bereit. Fügen Sie das folgende `<input>`-Tag (mit dem Typattribut „text“) im `<body>`-Abschnitt direkt unterhalb des `<output>`-Tags der Datei hinzu:

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

Mit dem Bild und dem Abonnementschlüssel können Sie die visuelle Bing-Suche aufrufen, um Erkenntnisse über das Bild zu erhalten. In diesem Tutorial werden für den Aufruf der Standardmarkt (`en-us`) und ein Wert für die sichere Suche (`moderate`) verwendet.

Diese Anwendung verfügt über eine Option zum Ändern dieser Werte. Fügen Sie den folgenden `<div>`-Abschnitt unter dem Abonnementschlüssel `<div>` hinzu. Die Anwendung verwendet ein `<select>`-Tag, um eine Dropdownliste mit Werten für den Markt und die sichere Suche bereitzustellen. Beide Listen zeigen den Standardwert.

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>Hinzufügen von Suchoptionen zur Webseite

Die Anwendung blendet die Listen in einem reduzierbaren `<div>`-Abschnitt aus, der durch den Link für Abfrageoptionen gesteuert wird. Wenn Sie auf den Link für Abfrageoptionen klicken, wird der `<div>`-Abschnitt erweitert, sodass Sie die Abfrageoptionen sehen und ändern können. Wenn Sie erneut auf den Link für Abfrageoptionen klicken, wird der `<div>`-Abschnitt reduziert und ausgeblendet. Im folgenden Codeausschnitt wird der `onclick`-Handler des Links für Abfrageoptionen dargestellt. Der Handler steuert, ob der `<div>`-Abschnitt erweitert oder reduziert wird. Fügen Sie diesen Handler dem `<script>`-Abschnitt hinzu. Der Handler wird von allen reduzierbaren `<div>`-Abschnitten in der Demo verwendet.

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>Aufrufen des `onclick`-Handlers

Fügen Sie die folgende Schaltfläche `"Get insights"` unterhalb des `<div>`-Abschnitts mit den Optionen im Textkörper hinzu. Mit der Schaltfläche können Sie den Aufruf initiieren. Beim Klicken auf die Schaltfläche wird der Cursor in den sich drehenden Wartecursor geändert, und der `onclick`-Handler wird aufgerufen.

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

Fügen Sie den `onclick`-Handler `handleQuery()` der Schaltfläche zum `<script>`-Tag hinzu.

## <a name="handle-the-query"></a>Verarbeiten der Abfrage

Der Handler `handleQuery()` stellt sicher, dass der Abonnementschlüssel vorhanden und 32 Zeichen lang ist und dass ein Bild ausgewählt wurde. Er löscht zudem alle Erkenntnisse einer vorherigen Abfrage. Danach ruft er die Funktion `sendRequest()` auf, um den Aufruf durchzuführen.

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>Senden der Suchanforderung

Die Funktion `sendRequest()` formatiert die Endpunkt-URL, legt den Header `Ocp-Apim-Subscription-Key` auf den Abonnementschlüssel fest, fügt die Binärdatei des hochzuladenden Bilds an, gibt den Antworthandler an und führt den Aufruf durch:

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>Abrufen und Verarbeiten der API-Antwort

Die Funktion `handleResponse()` verarbeitet die Antwort des Aufrufs der visuellen Bing-Suche. Wenn der Aufruf erfolgreich war, zergliedert sie die JSON-Antwort in die einzelnen Tags, die die Erkenntnisse enthalten. Als Nächstes fügt sie der Seite die Suchergebnisse hinzu. Die Anwendung erstellt dann einen reduzierbaren `<div>`-Abschnitt für jedes Tag, um zu steuern, wie viele Daten angezeigt werden. Fügen Sie den Handler dem `<script>`-Abschnitt hinzu.

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>Analysieren der Antwort

Die Funktion `parseResponse` konvertiert die JSON-Antwort in ein Wörterbuchobjekt, indem `json.tags` durchlaufen wird.

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>Erstellen eines Tagabschnitts

Die Funktion `buildTagSections()` durchläuft die analysierten JSON-Tags und ruft die Funktion `buildDiv()` auf, um einen `<div>`-Abschnitt für jedes Tag zu erstellen. Jedes Tag wird als Link angezeigt. Beim Klicken auf den Link wird das Tag erweitert, und die mit dem Tag verbundenen Erkenntnisse werden angezeigt. Beim erneuten Klicken auf den Link wird der Abschnitt reduziert.

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>Anzeigen der Suchergebnisse auf der Webseite

Die Funktion `buildDiv()` ruft die Funktion `addDivContent` auf, um den Inhalt der reduzierbaren `<div>`-Abschnitte der einzelnen Tags zu erstellen.

Zum Inhalt eines Tags gehört der JSON-Code aus der Antwort für das Tag. Zunächst werden nur die ersten 100 Zeichen des JSON-Codes angezeigt, aber Sie können auf die JSON-Zeichenfolge klicken, um den gesamten JSON-Code anzuzeigen. Wenn Sie erneut darauf klicken, wird die JSON-Zeichenfolge wieder auf 100 Zeichen reduziert.

Als Nächstes fügen Sie die Aktionstypen aus dem Tag hinzu. Rufen Sie für jeden Aktionstyp die entsprechenden Funktionen auf, um deren Erkenntnisse hinzuzufügen:

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>Anzeigen von Erkenntnissen für verschiedene Aktionen

Die folgenden Funktionen zeigen Erkenntnisse für verschiedene Aktionen an. Die Funktionen stellen entweder ein klickbares Bild oder einen klickbaren Link bereit, mit dem Sie zu einer Webseite mit weiteren Informationen geleitet werden. Diese Seite wird entweder auf Bing.com oder der ursprünglichen Website des Bilds gehostet. Es werden nicht alle Daten im Zusammenhang mit den Erkenntnissen in dieser Anwendung angezeigt. Informationen zum Anzeigen aller verfügbaren Felder für Erkenntnisse finden Sie in der [Referenz zur visuellen Suche](/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch).

> [!NOTE]
> Es gibt eine Mindestmenge an Informationen im Zusammenhang mit Erkenntnissen, die Sie auf der Seite anzeigen müssen. Weitere Informationen finden Sie unter [Anforderungen für die Verwendung und Anzeige der Bing-Suche-API](../bing-web-search/use-display-requirements.md).

### <a name="relatedimages-insights"></a>Erkenntnisse vom Typ „RelatedImages“

Die Funktion `addRelatedImages()` erstellt einen Titel für jede Website, auf der das zugehörige Bild gehostet wird. Dazu wird die Liste der `RelatedImages`-Aktionen durchlaufen und ein `<img>`-Tag an den jeweiligen äußeren `<div>`-Abschnitt angefügt:

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>Erkenntnisse vom Typ „PagesIncluding“

Die Funktion `addPagesIncluding()` erstellt einen Link für jede Website, auf der das hochgeladene Bild gehostet wird. Dazu wird die Liste der `PagesIncluding`-Aktionen durchlaufen und ein `<img>`-Tag an den jeweiligen äußeren `<div>`-Abschnitt angefügt:

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>Erkenntnisse vom Typ „RelatedSearches“

Die Funktion `addRelatedSearches()` erstellt einen Link für die Website, auf der das Bild gehostet wird. Dazu wird die Liste der `RelatedSearches`-Aktionen durchlaufen und ein `<img>`-Tag an den jeweiligen äußeren `<div>`-Abschnitt angefügt:

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Erkenntnisse vom Typ „Recipes“

Die Funktion `addRecipes()` erstellt einen Link für jedes zurückgegebene Rezept. Dazu wird die Liste der `Recipes`-Aktionen durchlaufen und ein `<img>`-Tag an den jeweiligen äußeren `<div>`-Abschnitt angefügt:

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Erkenntnisse vom Typ „Shopping“

Die Funktion `addShopping()` erstellt einen Link für alle zurückgegebenen Einkaufsquellenergebnisse. Dazu wird die Liste der `RelatedImages`-Aktionen durchlaufen und ein `<img>`-Tag an den jeweiligen äußeren `<div>`-Abschnitt angefügt:

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Erkenntnisse vom Typ „Products“

Die Funktion `addProducts()` erstellt einen Link für alle zurückgegebenen Produktergebnisse. Dazu wird die Liste der `Products`-Aktionen durchlaufen und ein `<img>`-Tag an den jeweiligen äußeren `<div>`-Abschnitt angefügt:

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>Erkenntnisse vom Typ „TextResult“

Die Funktion `addTextResult()` zeigt einen beliebigen Text an, der im Bild erkannt wurde:

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

Die Funktion `addEntity()` zeigt einen Link an, mit dem der Benutzer zu Bing.com gelangt, wo Details zum Entitätstyp im Bild abgerufen werden können (sofern einer erkannt wurde):

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

Die Funktion `addImageWithWebSearchUrl()` zeigt ein klickbares Bild für den `<div>`-Abschnitt an, mit dem der Benutzer zu den Suchergebnissen auf Bing.com geleitet wird:

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>Hinzufügen einer CSS-Formatvorlage

Fügen Sie den folgenden `<style>`-Abschnitt zum `<head>`-Tag hinzu, um das Layout der Webseite zu organisieren:

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
> [Tutorial: Suchen ähnlicher Bilder in vorherigen Suchvorgängen mithilfe von „ImageInsightsToken“](./tutorial-visual-search-insights-token.md)