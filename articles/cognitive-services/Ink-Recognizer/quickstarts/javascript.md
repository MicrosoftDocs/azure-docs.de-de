---
title: 'Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und Node.js'
description: Verwenden Sie die Freihanderkennungs-REST-API, um Freihandstriche zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 651474fd538123e760022ac59efbbaf0b9b83d70
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519675"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und JavaScript

In diesem Schnellstart werden die ersten Schritte zur Verwendung der Freihanderkennungs-API zum Erkennen von Freihandstrichen erläutert. Die hier verwendete JavaScript-Anwendung sendet eine API-Anforderung, die JSON-formatierte Freihandstrichdaten enthält, und zeigt die Antwort an.

Diese Anwendung ist zwar in JavaScript geschrieben und wird im Webbrowser ausgeführt, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Normalerweise würden Sie die API über eine App für Freihandeingabe aufrufen. In diesem Schnellstart werden Freihandstrichdaten für das folgende handschriftliche Beispiel aus einer JSON-Datei gesendet.

![Abbildung, die handschriftlichen Text zeigt](../media/handwriting-sample.jpg)

Den Quellcode für diese Schnellstartanleitung finden Sie auf [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Voraussetzungen

- Aus einem Webbrowser
- Die Beispiel-Freihandstrichdaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

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
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
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

## Run the application and view the response

This application can be run within your web browser. A successful response is returned in JSON format. You can also find the JSON response on [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## Next steps

> [!div class="nextstepaction"]
> [REST API reference](https://go.microsoft.com/fwlink/?linkid=2089907)

To see how the Ink Recognition API works in a digital inking app, take a look at the following sample applications on GitHub:
* [C# and Universal Windows Platform(UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# and Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript web-browser app](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java and Android mobile app](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift and iOS mobile app](https://go.microsoft.com/fwlink/?linkid=2089805)
