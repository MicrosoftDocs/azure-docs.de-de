---
title: Ausführen von Bildvorgängen – JavaScript
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über eine grundlegende JavaScript-App, die die Maschinelles Sehen-API in Azure Cognitive Services verwendet. Führen Sie eine optische Zeichenerkennung durch, erstellen Sie Miniaturansichten, und arbeiten Sie mit visuellen Merkmalen in einem Bild.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 91af70406590ab8e65a5d4a4b53835e9e4d4ed2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231663"
---
# <a name="use-computer-vision-features-with-the-rest-api-and-javascript"></a>Verwenden von Features des maschinellen Sehens mit der REST-API und JavaScript

Dieser Leitfaden zeigt die Features der Maschinelles Sehen-REST-API von Azure Cognitive Services.

Erfahren Sie mehr über eine JavaScript-Anwendung, die die Maschinelles Sehen-REST-API verwendet, um die optische Zeichenerkennung (OCR) auszuführen, Miniaturansichten mithilfe der Funktion „Intelligentes Zuschneiden“ zu erstellen, sowie visuelle Merkmale zu erkennen, zu kategorisieren, zu markieren und zu beschreiben. In diesem Beispiel können Sie eine Bild-URL zur Analyse oder Verarbeitung einreichen. Sie können dieses Open-Source-Beispiel als Vorlage für die Erstellung Ihrer eigenen JavaScript-App verwenden, um die Maschinelles Sehen-REST-API zu verwenden.

Die JavaScript-Formularanwendung wurde bereits geschrieben, hat jedoch keine Maschinelles Sehen-Funktion. In diesem Leitfaden fügen Sie den spezifischen Code der Maschinelles Sehen-REST-API hinzu, um die Funktionalität der Anwendung zu vervollständigen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="platform-requirements"></a>Plattformanforderungen

Sie können die Schritte in diesem Leitfaden mit einem einfachen Text-Editor ausführen.

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Abonnieren der Maschinelles Sehen-API und Anfordern eines Abonnementschlüssels

Bevor Sie das Beispiel erstellen, müssen Sie die Maschinelles Sehen-API abonnieren, die zum Funktionsumfang von Azure Cognitive Services gehört. Informationen zu Abonnements und zur Schlüsselverwaltung finden Sie unter [Abonnements](https://azure.microsoft.com/try/cognitive-services/). Primäre und sekundäre Schlüssel können in diesem Leitfaden verwendet werden.

## <a name="acquire-incomplete-tutorial-project"></a>Abrufen eines unvollständigen Tutorialprojekts

### <a name="download-the-project"></a>Herunterladen des Projekts

Klonen Sie das [Tutorial zur Machinelles-Sehen-JavaScript-API der Cognitive Services](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial), oder laden Sie die ZIP-Datei herunter und extrahieren Sie sie in einem leeren Verzeichnis.

Wenn Sie lieber das fertige Projekt mit dem gesamten hinzugefügten Tutorialcode verwenden möchten, können Sie die Dateien im **Completed**-Ordner verwenden.

## <a name="add-tutorial-code-to-the-project"></a>Hinzufügen von Tutorialcode zum Projekt

Die JavaScript-Anwendung ist mit sechs HTML-Dateien eingerichtet, eine für jede Funktion. Jede Datei veranschaulicht eine andere Funktion des maschinellen Sehens (Analysieren, OCR usw.). Die sechs Abschnitte sind nicht voneinander abhängig, sodass Sie den Code des Tutorials einer Datei, allen sechs Dateien oder nur ein paar Dateien hinzufügen können. Sie können den Tutorialcode außerdem in beliebiger Reihenfolge zu den Dateien hinzufügen.

### <a name="analyze-an-image"></a>Analysieren von Bildern

Die Analysefunktion des maschinellen Sehens scannt ein Bild für Tausende von erkennbaren Objekten, Lebewesen, Landschaften und Aktionen. Sobald die Analyse abgeschlossen ist, gibt die Funktion ein JSON-Objekt zurück, das das Bild mit entsprechenden Tags, einer Farbanalyse, Beschriftungen und mehr beschreibt.

Um die Analysefunktion der Anwendung ausführen zu können, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-analyze-button"></a>Hinzufügen des Ereignishandlercodes für die Analyseschaltfläche

Öffnen Sie die Datei **analyze.html** in einem Text-Editor, und suchen Sie die Funktion **AnalyzeButtonClick** im unteren Bereich der Datei.

Die Ereignishandlerfunktion **AnalyzeButtonClick** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Funktion **AnalyzeImage** auf, um das Bild zu analysieren. Kopieren Sie den folgenden Code in die Funktion **analyzeButtonClick**.

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Funktion **AnalyzeImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Nach erfolgreicher Rückgabe wird die formatierte JSON-Analyse im angegebenen Textbereich angezeigt, und die Beschriftung wird im angegebenen Bereich angezeigt. Nach erfolgreicher Rückkehr wird die formatierte JSON-Analyse im angegebenen Textbereich angezeigt, und die Beschriftung wird im angegebenen Bereich angezeigt.

Kopieren Sie den Funktionscode **AnalyzeImage** und fügen Sie ihn unterhalb der Funktion **AnalyzeButtonClick** ein.

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-analyze-function"></a>Ausführen der Analysefunktion

Speichern Sie die Datei **analyze.html**, und öffnen Sie sie in einem Webbrowser. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem zu analysierenden Bild ein, und klicken Sie dann auf die Schaltfläche **Bild analysieren**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="recognize-a-landmark"></a>Erkennen von Wahrzeichen

Die Funktion „Wahrzeichen“ für Maschinelles Sehen analysiert Bilder hinsichtlich Naturdenkmäler oder künstliche Wahrzeichen, wie z.B. Berge oder berühmte Gebäude. Sobald die Analyse abgeschlossen ist, gibt die Funktion ein JSON-Objekt zurück, das die im Bild gefundenen Wahrzeichen identifiziert.

Um die Wahrzeichenfunktion der Anwendung ausführen zu können, führen Sie folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-landmark-button"></a>Hinzufügen des Ereignishandlercodes für die Wahrzeichenschaltfläche

Öffnen Sie die Datei **landmark.html** in einem Text-Editor, und suchen Sie die Funktion **landmarkButtonClick** im unteren Bereich der Datei.

Die Ereignishandlerfunktion **landmarkButtonClick** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Funktion **IdentifyLandmarks** auf, um das Bild zu analysieren. Kopieren Sie den folgenden Code in die Funktion **landmarkButtonClick**.

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Funktion **IdentifyLandmarks** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Nach erfolgreicher Rückgabe wird die formatierte JSON-Analyse im angegebenen Textbereich angezeigt, und die Beschriftung wird im angegebenen Bereich angezeigt. Nach erfolgreicher Rückkehr wird die formatierte JSON-Analyse im angegebenen Textbereich angezeigt, und die Beschriftung wird im angegebenen Bereich angezeigt.

Kopieren Sie den Funktionscode **IdentifyLandmarks** und fügen Sie ihn unterhalb der Funktion **landmarkButtonClick** ein.

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-landmark-function"></a>Ausführen der Wahrzeichenfunktion

Speichern Sie die Datei **landmark.html**, und öffnen Sie sie in einem Webbrowser. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem zu analysierenden Bild ein, und klicken Sie dann auf die Schaltfläche **Bild analysieren**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="recognize-celebrities"></a>Erkennen von Prominenten

Die Funktion „Prominente“ für Maschinelles Sehen analysiert Bilder hinsichtlich berühmter Persönlichkeiten. Sobald die Analyse abgeschlossen ist, gibt die Funktion ein JSON-Objekt zurück, das die im Bild gefundenen Prominenten identifiziert.

Um die Prominentenfunktion der Anwendung ausführen zu können, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-celebrities-button"></a>Hinzufügen des Ereignishandlercodes für die Prominentenschaltfläche

Öffnen Sie die Datei **celebrities.html** in einem Text-Editor, und suchen Sie die Funktion **celebritiesButtonClick** im unteren Bereich der Datei.

Die Ereignishandlerfunktion **celebritiesButtonClick** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Funktion **IdentifyCelebrities** auf, um das Bild zu analysieren. Kopieren Sie den folgenden Code in die Funktion **celebritiesButtonClick**.

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

#### <a name="run-the-celebrities-function"></a>Ausführen der Prominentenfunktion

Speichern Sie die Datei **celebrities.html**, und öffnen Sie sie in einem Webbrowser. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem zu analysierenden Bild ein, und klicken Sie dann auf die Schaltfläche **Bild analysieren**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="intelligently-generate-a-thumbnail"></a>Intelligentes Generieren einer Miniaturansicht

Die Funktion „Miniaturansicht“ für Maschinelles Sehen generiert Miniaturansichten aus Bildern. Mithilfe der Funktion **Intelligentes Zuschneiden** identifiziert die Funktion „Miniaturansicht“ den für ein Bild relevanten Bereich, und zentriert das Miniaturbild auf diesem Bereich, um optisch ansprechendere Miniaturbilder zu generieren.

Um die Miniaturansichtsfunktion der Anwendung ausführen zu können, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-thumbnail-button"></a>Hinzufügen des Ereignishandlercodes für die Miniaturansichtsschaltfläche

Öffnen Sie die Datei **thumbnail.html** in einem Text-Editor, und suchen Sie die Funktion **thumbnailButtonClick** im unteren Bereich der Datei.

Die Ereignishandlerfunktion **thumbnailButtonClick** löscht das Formular, zeigt das in der URL angegebene Bild an, und ruft dann die Funktion **getThumbnail** zweimal auf, um zwei Miniaturansichten zu erstellen – einmal mit der Funktion „Intelligentes Zuschneiden“ und einmal ohne diese Funktion. Kopieren Sie den folgenden Code in die Funktion **thumbnailButtonClick**.

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Funktion **getThumbnail** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Nach erfolgreicher Rückgabe wird die Miniaturansicht im angegebenen img-Element angezeigt.

Kopieren Sie die folgende **getThumbnail**-Funktion und fügen Sie sie genau unterhalb der Funktion **thumbnailButtonClick** ein.

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumbnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

#### <a name="run-the-thumbnail-function"></a>Ausführen der Miniaturansichtsfunktion

Speichern Sie die Datei **thumbnail.html**, und öffnen Sie sie in einem Webbrowser. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem zu analysierenden Bild ein, und klicken Sie dann auf die Schaltfläche **Miniaturansichten generieren**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="read-printed-text-ocr"></a>Auslesen von gedrucktem Text (OCR)

Die OCR-Funktion (Optical Character Recognition) für Maschinelles Sehen analysiert Bilder hinsichtlich gedrucktem Text. Nach Abschluss der Analyse gibt OCR ein JSON-Objekt zurück, das den Text und die Position des Textes im Bild enthält.

Um die OCR-Funktion der Anwendung ausführen zu können, führen Sie die folgenden Schritte aus:

### <a name="add-the-event-handler-code-for-the-ocr-button"></a>Hinzufügen des Ereignishandlercodes für die OCR-Schaltfläche

Öffnen Sie die Datei **ocr.html** in einem Text-Editor, und suchen Sie die Funktion **ocrButtonClick** im unteren Bereich der Datei.

Die Ereignishandlerfunktion **ocrButtonClick** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Funktion **ReadOcrImage** auf, um das Bild zu analysieren. Kopieren Sie den folgenden Code in die Funktion **ocrButtonClick**.

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Funktion **ReadOcrImage** dient als Wrapper für den REST-API-Aufruf zur Analyse von Bildern. Bei erfolgreicher Rückgabe wird das formatierte JSON-Objekt, das den Text und die Position des Textes beschreibt, im angegebenen Textbereich angezeigt.

Kopieren Sie die folgende **ReadOcrImage**-Funktion und fügen Sie sie genau unterhalb der Funktion **ocrButtonClick** ein.

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-ocr-function"></a>Ausführen der OCR-Funktion

Speichern Sie die Datei **ocr.html**, und öffnen Sie sie in einem Webbrowser. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem auszulesenden Bild ein, und klicken Sie dann auf die Schaltfläche **Bild auslesen**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

### <a name="read-handwritten-text-handwriting-recognition"></a>Lesen von handgeschriebenem Text (Schrifterkennung)

Die Funktion „Schrifterkennung“ für Maschinelles Sehen analysiert Bilder hinsichtlich handgeschriebenem Text. Nach Abschluss der Analyse gibt die Schrifterkennung ein JSON-Objekt zurück, das den Text und die Position des Textes im Bild enthält.

Um die Schrifterkennungsfunktion der Anwendung ausführen zu können, führen Sie die folgenden Schritte aus:

#### <a name="add-the-event-handler-code-for-the-handwriting-button"></a>Hinzufügen des Ereignishandlercodes für die Schrifterkennungsschaltfläche

Öffnen Sie die Datei **handwriting.html** in einem Text-Editor, und suchen Sie die Funktion **handwritingButtonClick** im unteren Bereich der Datei.

Die Ereignishandlerfunktion **handwritingButtonClick** löscht das Formular, zeigt das in der URL angegebene Bild an und ruft dann die Funktion **HandwritingImage** auf, um das Bild zu analysieren.

Kopieren Sie den folgenden Code in die Funktion **handwritingButtonClick**.

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Hinzufügen des Wrappers für den REST-API-Aufruf

Die Funktion **ReadHandwrittenImage** dient als Wrapper für die beiden REST-API-Aufrufe, die zur Analyse eines Bildes erforderlich sind. Da die Handschrifterkennung viel Zeit in Anspruch nimmt, wird ein zweistufiger Prozess verwendet. Beim ersten Aufruf wird das Bild zur Verarbeitung gesendet; der zweite Aufruf ruft den erkannten Text ab, wenn die Bearbeitung abgeschlossen ist.

Nach erfolgreichem Abrufen des Texts wird das formatierte JSON-Objekt, das den Text und die Position des Textes beschreibt, im angegebenen Textbereich angezeigt.

Kopieren Sie die folgende **ReadHandwrittenImage**-Funktion und fügen Sie sie genau unterhalb der Funktion **handwritingButtonClick** ein.

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

#### <a name="run-the-handwriting-function"></a>Ausführen der Schrifterkennungsfunktion

Speichern Sie die Datei **handwriting.html**, und öffnen Sie sie in einem Webbrowser. Geben Sie Ihren Abonnementschlüssel in das Feld **Abonnementschlüssel** ein und vergewissern Sie sich, dass Sie die richtige Region unter **Abonnementregion** ausgewählt haben. Geben Sie die URL zu dem auszulesenden Bild ein, und klicken Sie dann auf die Schaltfläche **Bild auslesen**, um das Bild zu analysieren und das Ergebnis anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie die Maschinelles Sehen-REST-API mit JavaScript verwendet, um viele der verfügbaren Features zur Bildanalyse zu testen. In der Referenzdokumentation finden Sie weitere Informationen zu den beteiligten APIs.

- [Maschinelles Sehen-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)
