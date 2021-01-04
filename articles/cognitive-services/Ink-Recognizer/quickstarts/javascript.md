---
title: 'Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und Node.js'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie in dieser Schnellstartanleitung die Freihanderkennungs-REST-API und JavaScript, um Freihandstriche zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: ca1c18fd1ea8507c8b6a58b5f786b9686b1f3a34
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369073"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und JavaScript

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

In diesem Schnellstart werden die ersten Schritte zur Verwendung der Freihanderkennungs-API zum Erkennen von Freihandstrichen erläutert. Die hier verwendete JavaScript-Anwendung sendet eine API-Anforderung, die JSON-formatierte Freihandstrichdaten enthält, und zeigt die Antwort an.

Diese Anwendung ist zwar in JavaScript geschrieben und wird im Webbrowser ausgeführt, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Normalerweise würden Sie die API über eine App für Freihandeingabe aufrufen. In diesem Schnellstart werden Freihandstrichdaten für das folgende handschriftliche Beispiel aus einer JSON-Datei gesendet.

![Abbildung, die handschriftlichen Text zeigt](../media/handwriting-sample.jpg)

Den Quellcode für diese Schnellstartanleitung finden Sie auf [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Voraussetzungen

- Aus einem Webbrowser
- Die Beispiel-Freihandstrichdaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Erstellen einer Freihanderkennungsressource

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie in Ihrer bevorzugten integrierten Entwicklungsumgebung (Integrated Devlopment Environment, IDE) oder Ihrem bevorzugten Editor eine neue `.html`-Datei. Fügen Sie anschließend eine grundlegende HTML-Struktur hinzu. Den Code fügen wir später hinzu.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. Fügen Sie innerhalb des `<body>`-Tags den folgenden HTML-Code hinzu:
    1. Zwei Textbereiche für die Anzeige der JSON-Anforderung und -Antwort
    2. Eine Schaltfläche zum Aufrufen der `recognizeInk()`-Funktion, die später erstellt wird
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Laden der JSON-Beispieldaten

1. Erstellen Sie innerhalb des `<script>`-Tags eine Variable für die JSON-Beispieldaten (sampleJson). Erstellen Sie dann eine JavaScript-Funktion mit dem Namen `openFile()`, die einen Datei-Explorer öffnet, in dem Sie die JSON-Datei auswählen können. Wenn auf die Schaltfläche `Recognize ink` geklickt wird, wird diese Funktion aufgerufen und die Datei gelesen.
2. Verwenden Sie die `onload()`-Funktion eines `FileReader`-Objekts, um die Datei asynchron zu verarbeiten. 
    1. Ersetzen Sie alle `\n`- oder `\r`-Zeichen in der Datei durch eine leere Zeichenfolge. 
    2. Konvertieren Sie den Text mithilfe von `JSON.parse()` in ein gültiges JSON-Format.
    3. Aktualisieren Sie das Textfeld `request` in der Anwendung. Formatieren Sie die JSON-Zeichenfolge mit `JSON.stringify()`. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Senden einer Anforderung an die Freihanderkennungs-API

1. Erstellen Sie innerhalb des `<script>`-Tags eine Funktion mit dem Namen `recognizeInk()`. Diese Funktion ruft später die API auf und aktualisiert die Seite mit der Antwort. Fügen Sie in der Funktion den Code aus den folgenden Schritten hinzu. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Erstellen Sie Variablen für Ihre Endpunkt-URL, Ihren Abonnementschlüssel und die JSON-Beispieldaten. Erstellen Sie dann ein `XMLHttpRequest`-Objekt, um die API-Anforderung senden. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Erstellen Sie die Rückgabefunktion für das `XMLHttpRequest`-Objekt. Diese Funktion analysiert die API-Antwort einer erfolgreichen Anforderung und zeigt sie in der Anwendung an. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Erstellen Sie die Fehlerfunktion für das Anforderungsobjekt. Diese Funktion protokolliert den Fehler in der Konsole. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Erstellen Sie eine Funktion für die `onreadystatechange`-Eigenschaft des Anforderungsobjekts. Wenn sich der Bereitschaftszustand des Anforderungsobjekts ändert, werden die obigen Rückgabe- und Fehlerfunktionen angewendet.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Senden Sie die API-Anforderung. Fügen Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu, und legen Sie `content-type` auf `application/json` fest.
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Ausführen der Anwendung und Anzeigen der Antwort

Diese Anwendung kann in Ihrem Webbrowser ausgeführt werden. Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Sie finden die JSON-Antwort auch auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)

Sehen Sie sich in den folgenden Beispielanwendungen auf GitHub an, wie die Freihanderkennungs-API in einer Freihandschriftinhalte-App funktioniert:
* [C# und universelle Windows-Plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# und Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-Webbrowser-App](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobile Java- und Android-App](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobile Swift- und iOS-App](https://go.microsoft.com/fwlink/?linkid=2089805)