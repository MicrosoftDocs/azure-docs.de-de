---
title: " Erstellen einer Single-Page-Web-App – Visuelle Bing-Suche"
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die API für die visuelle Bing-Suche in eine Single-Page-Webanwendung integrieren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b1ca88cd654b7bf373bee60b1e9b7a2e7a129fa2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499015"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Tutorial: Erstellen einer Single-Page-Web-App für die visuelle Suche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst eintritt).
> Eine Anleitung zur Migration finden Sie unter [Erstellen einer Ressource für die Bing-Suche über Azure Marketplace](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die API für die visuelle Bing-Suche gibt Erkenntnisse für ein Bild zurück. Sie können entweder ein Bild hochladen oder eine URL für ein Bild eingeben. Erkenntnisse sind visuell ähnliche Bilder, Einkaufsquellen, Webseiten, auf denen das Bild vorkommt, und Ähnliches. Die von der API für die visuelle Bing-Suche zurückgegebenen Erkenntnisse ähneln den Ergebnissen auf „Bing.com/images“.

In diesem Tutorial erfahren Sie, wie Sie eine Single-Page-Web-App für die Bing-Bildersuche-API erweitern. Das Tutorial und den hier verwendeten Quellcode finden Sie unter [Tutorial: Erstellen einer einseitigen Web-App mit der Bing-Bildersuche-API](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Der vollständige Quellcode für diese Anwendung (nach Erweiterung für die Verwendung der API für die visuelle Bing-Suche) steht auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html) zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Aufrufen der API für die visuelle Bing-Suche und Verarbeiten der Antwort

Bearbeiten Sie das Tutorial für die Bing-Bildersuche, und fügen Sie am Ende des Elements `<script>` (und vor dem schließenden Tag `</script>`) den folgenden Code hinzu. Der folgende Code verarbeitet die Antwort einer visuellen Suche von der API, durchläuft die Ergebnisse und zeigt sie an:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Der folgende Code sendet eine Suchanforderung an die API und verwendet einen Ereignislistener, um `handleVisualSearchResponse()` aufzurufen:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Erfassen des Auswertungstokens

Fügen Sie den folgenden Code zum Objekt `searchItemsRenderer` hinzu. Dieser Code fügt einen Link **find similar** hinzu, mit dem durch Klicken die Funktion `bingVisualSearch` aufgerufen wird. Die Funktion empfängt `imageInsightsToken` als Argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Anzeigen ähnlicher Bilder

Fügen Sie den folgenden HTML-Code in Zeile 601 hinzu. Dieser Markupcode fügt ein Element hinzu, mit dem die Ergebnisse des Aufrufs der API für die visuelle Bing-Suche angezeigt werden:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Mit dem neuen JavaScript-Code und den HTML-Elementen werden Suchergebnisse mit einem Link **find similar** angezeigt. Klicken Sie auf den Link, um den Abschnitt **Similar** mit Bildern zu füllen, die dem ausgewählten Bild ähneln. Möglicherweise müssen Sie den Abschnitt **Similar** erweitern, um die Bilder anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Crop an image with the Bing Visual Search SDK for C# (Zuschneiden eines Bilds mithilfe des SDK für die Visuelle Bing-Suche für C#)](tutorial-visual-search-crop-area-results.md)