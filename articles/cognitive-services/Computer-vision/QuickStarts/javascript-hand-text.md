---
title: 'Schnellstart: Extrahieren von handschriftlichem Text – JavaScript'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung extrahieren Sie handschriftlichen Text aus einem Bild mit der Maschinelles Sehen-API und JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c93271fcd74d1369112a2c5d2bba98fd14ff7c84
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357210"
---
# <a name="quickstart-extract-handwritten-text-using-the-rest-api-and-javascript-in-computer-vision"></a>Schnellstart: Extrahieren von handschriftlichem Text mit der REST-API und JavaScript in der Maschinelles Sehen-API

In dieser Schnellstartanleitung extrahieren Sie handschriftlichen Text aus einem Bild, indem Sie die REST-API von Maschinelles Sehen verwenden. Mit den APIs [Batch Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) und [Read Operation Result](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) können Sie in einem Bild handschriftlichen Text erkennen und erkannte Zeichen als computerlesbare Zeichenfolge extrahieren.

> [!IMPORTANT]
> Im Gegensatz zur [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)-Methode wird die [Batch Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)-Methode asynchron ausgeführt. Diese Methode gibt keine Informationen im Text einer erfolgreichen Antwort zurück. Die Batch Read-Methode gibt stattdessen einen URI im Wert des Antwortheaderfelds `Operation-Content` zurück. Anschließend können Sie diesen URI aufrufen, der die Methode [Read Operation Result](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/5be108e7498a4f9ed20bf96d) darstellt, um sowohl den Status zu überprüfen als auch die Ergebnisse des Batch Read-Methodenaufrufs zurückzugeben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen einen Abonnementschlüssel für maschinelles Sehen. Über die Seite [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) können Sie einen Schlüssel für eine kostenlose Testversion abrufen. Oder gehen Sie wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) beschrieben vor, um „Maschinelles Sehen“ zu abonnieren und Ihren Schlüssel zu erhalten.

## <a name="create-and-run-the-sample"></a>Erstellen und Ausführen des Beispiels

Führen Sie zum Erstellen und Ausführen des Beispiels die folgenden Schritte aus:

1. Kopieren Sie den folgenden Code in einen Text-Editor.
1. Nehmen Sie bei Bedarf die folgenden Änderungen im Code vor:
    1. Ersetzen Sie den `subscriptionKey`-Wert durch Ihren Abonnementschlüssel.
    1. Ersetzen Sie den Wert von `uriBase` durch die Endpunkt-URL für die [Batch Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)-Methode in der Azure-Region, in der Sie Ihre Abonnementschlüssel bezogen haben (sofern erforderlich).
    1. Ersetzen Sie optional den Wert des Attributs `value` für das Steuerelement `inputImage` durch die URL eines anderen Bilds, aus dem Sie handschriftlichen Text extrahieren möchten.
1. Speichern Sie den Code als Datei mit der Erweiterung `.html`. Beispiel: `get-handwriting.html`.
1. Öffnen Sie ein Browserfenster.
1. Ziehen Sie die Datei in das Browserfenster, und legen Sie sie dort ab.
1. Wenn die Webseite im Browser angezeigt wird, klicken Sie auf die Schaltfläche **Bild lesen**.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Handwriting Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace <Subscription Key> with your valid subscription key.
        var subscriptionKey = "<Subscription Key>";

        // You must use the same Azure region in your REST API method as you used to
        // get your subscription keys. For example, if you got your subscription keys
        // from the West US region, replace "westcentralus" in the URL
        // below with "westus".
        //
        // Free trial subscription keys are generated in the "westus" region.
        // If you use a free trial subscription key, you shouldn't need to change
        // this region.
        var uriBase =
            "https://westus.api.cognitive.microsoft.com/vision/v2.0/read/core/asyncBatchAnalyze";

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // This operation requires two REST API calls. One to submit the image
        // for processing, the other to retrieve the text found in the image.
        //
        // Make the first REST API call to submit the image for processing.
        $.ajax({
            url: uriBase,

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data, textStatus, jqXHR) {
            // Show progress.
            $("#responseTextArea").val("Handwritten text submitted. " +
                "Waiting 10 seconds to retrieve the recognized text.");

            // Note: The response may not be immediately available. Handwriting
            // recognition is an asynchronous operation that can take a variable
            // amount of time depending on the length of the text you want to
            // recognize. You may need to wait or retry the GET operation.
            //
            // Wait ten seconds before making the second REST API call.
            setTimeout(function () {
                // "Operation-Location" in the response contains the URI
                // to retrieve the recognized text.
                var operationLocation = jqXHR.getResponseHeader("Operation-Location");

                // Make the second REST API call and get the response.
                $.ajax({
                    url: operationLocation,

                    // Request headers.
                    beforeSend: function(jqXHR){
                        jqXHR.setRequestHeader("Content-Type","application/json");
                        jqXHR.setRequestHeader(
                            "Ocp-Apim-Subscription-Key", subscriptionKey);
                    },

                    type: "GET",
                })

                .done(function(data) {
                    // Show formatted JSON on webpage.
                    $("#responseTextArea").val(JSON.stringify(data, null, 2));
                })

                .fail(function(jqXHR, textStatus, errorThrown) {
                    // Display error message.
                    var errorString = (errorThrown === "") ? "Error. " :
                        errorThrown + " (" + jqXHR.status + "): ";
                    errorString += (jqXHR.responseText === "") ? "" :
                        (jQuery.parseJSON(jqXHR.responseText).message) ?
                            jQuery.parseJSON(jqXHR.responseText).message :
                            jQuery.parseJSON(jqXHR.responseText).error.message;
                    alert(errorString);
                });
            }, 10000);
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Put the JSON description into the text area.
            $("#responseTextArea").val(JSON.stringify(jqXHR, null, 2));

            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " :
                errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>
<h1>Read handwritten image:</h1>
Enter the URL to an image of handwritten text, then click
the <strong>Read image</strong> button.
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/d/dd/Cursive_Writing_on_Notebook_paper.jpg/800px-Cursive_Writing_on_Notebook_paper.jpg" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Untersuchen der Antwort

Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Die Beispielwebseite analysiert eine Antwort und zeigt diese bei erfolgreicher Ausführung im Browserfenster an. Im Folgenden finden Sie ein Beispiel dafür:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 349.59,
      "width": 3200,
      "height": 3200,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [202,618,2047,643,2046,840,200,813],
          "text": "Our greatest glory is not",
          "words": [
            {
              "boundingBox": [204,627,481,628,481,830,204,829],
              "text": "Our"
            },
            {
              "boundingBox": [519,628,1057,630,1057,832,518,830],
              "text": "greatest"
            },
            {
              "boundingBox": [1114,630,1549,631,1548,833,1114,832],
              "text": "glory"
            },
            {
              "boundingBox": [1586,631,1785,632,1784,834,1586,833],
              "text": "is"
            },
            {
              "boundingBox": [1822,632,2115,633,2115,835,1822,834],
              "text": "not"
            }
          ]
        },
        {
          "boundingBox": [420,1273,2954,1250,2958,1488,422,1511],
          "text": "but in rising every time we fall",
          "words": [
            {
              "boundingBox": [423,1269,634,1268,635,1507,424,1508],
              "text": "but"
            },
            {
              "boundingBox": [667,1268,808,1268,809,1506,668,1507],
              "text": "in"
            },
            {
              "boundingBox": [874,1267,1289,1265,1290,1504,875,1506],
              "text": "rising"
            },
            {
              "boundingBox": [1331,1265,1771,1263,1772,1502,1332,1504],
              "text": "every"
            },
            {
              "boundingBox": [1812, 1263, 2178, 1261, 2179, 1500, 1813, 1502],
              "text": "time"
            },
            {
              "boundingBox": [2219, 1261, 2510, 1260, 2511, 1498, 2220, 1500],
              "text": "we"
            },
            {
              "boundingBox": [2551, 1260, 3016, 1258, 3017, 1496, 2552, 1498],
              "text": "fall"
            }
          ]
        },
        {
          "boundingBox": [1612, 903, 2744, 935, 2738, 1139, 1607, 1107],
          "text": "in never failing ,",
          "words": [
            {
              "boundingBox": [1611, 934, 1707, 933, 1708, 1147, 1613, 1147],
              "text": "in"
            },
            {
              "boundingBox": [1753, 933, 2132, 930, 2133, 1144, 1754, 1146],
              "text": "never"
            },
            {
              "boundingBox": [2162, 930, 2673, 927, 2674, 1140, 2164, 1144],
              "text": "failing"
            },
            {
              "boundingBox": [2703, 926, 2788, 926, 2790, 1139, 2705, 1140],
              "text": ",",
              "confidence": "Low"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Datei nicht mehr benötigen, löschen Sie sie.

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie eine JavaScript-Anwendung kennen, die maschinelles Sehen verwendet, um eine optische Zeichenerkennung (Optical Character Recognition, OCR) durchzuführen, intelligent zugeschnittene Miniaturansichten zu erstellen sowie visuelle Merkmale (einschließlich Gesichter) in einem Bild zu erkennen, zu kategorisieren, zu markieren und zu beschreiben. Um schnell mit der Maschinelles Sehen-API zu experimentieren, probieren Sie die [Open API-Testkonsole](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console) aus.

> [!div class="nextstepaction"]
> [Tutorial zur Maschinelles Sehen-API mit JavaScript](../Tutorials/javascript-tutorial.md)
