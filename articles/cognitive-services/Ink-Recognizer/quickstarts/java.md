---
title: 'Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und Java'
titleSuffix: Azure Cognitive Services
description: Verwenden Sie in dieser Schnellstartanleitung die Freihanderkennungs-REST-API und Java, um Freihandstriche zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: dea46a07a7357be6079c52634be8ea2ff79cc8f3
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369119"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und Java

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

In diesem Schnellstart werden die ersten Schritte zur Verwendung der Freihanderkennungs-API zum Erkennen von Freihandstrichen erläutert. Diese Java-Anwendung sendet eine API-Anforderung, die JSON-formatierte Freihandstrichdaten enthält, und erhält die Antwort.

Diese Anwendung ist in Java geschrieben, die API ist jedoch ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

Normalerweise würden Sie die API über eine App für Freihandeingabe aufrufen. In diesem Schnellstart werden Freihandstrichdaten für das folgende handschriftliche Beispiel aus einer JSON-Datei gesendet.

![Abbildung, die handschriftlichen Text zeigt](../media/handwriting-sample.jpg)

Den Quellcode für diese Schnellstartanleitung finden Sie auf [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Voraussetzungen

- Das [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) oder höher

- Importieren dieser Bibliotheken aus dem Maven-Repository
    - Paket [JSON in Java](https://mvnrepository.com/artifact/org.json/json)
    - Paket [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Die Beispiel-Freihandstrichdaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Erstellen einer Freihanderkennungsressource

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt, und importieren Sie die folgenden Bibliotheken.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel, den Endpunkt und die JSON-Datei. Der Endpunkt wird später an den Freihanderkennungs-URI angehängt.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Erstellen einer Funktion zum Senden von Anforderungen

1. Erstellen Sie eine neuen Funktion namens `sendRequest()`, die die oben erstellten Variablen verwendet. Führen Sie anschließend die folgenden Schritte aus.

2. Erstellen Sie ein `CloseableHttpClient`-Objekt, das Anforderungen an die API senden kann. Senden Sie die Anforderung an ein `HttpPut`-Anforderungsobjekt, indem Sie Ihren Endpunkt und die URL der Freihanderkennung kombinieren.

3. Verwenden Sie die Funktion `setHeader()` der Anforderung, um den Header `Content-Type` auf `application/json` festzulegen, und fügen Sie Ihren Abonnementschlüssel dem Header `Ocp-Apim-Subscription-Key` hinzu.

4. Verwenden Sie die Funktion `setEntity()` der Anforderung für die zu sendenden Daten.   

5. Verwenden Sie die Funktion `execute()` des Clients, um die Anforderung zu senden, und speichern Sie sie in einem `CloseableHttpResponse`-Objekt. 

6. Erstellen Sie ein `HttpEntity`-Objekt, um den Inhalt der Antwort zu speichern. Rufen Sie den Inhalt mit `getEntity()` ab. Wenn die Antwort nicht leer ist, geben Sie sie zurück.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Senden einer Freihanderkennungsanforderung

Erstellen Sie eine Methode namens `recognizeInk()` zur Erkennung Ihrer Freihandstrichdaten. Rufen Sie die oben erstellte Methode `sendRequest()` mit dem Endpunkt, der URL, dem Abonnementschlüssel und den JSON-Daten auf. Rufen Sie das Ergebnis ab, und geben Sie es in der Konsole aus.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Laden der digitalen Freihanddaten und Senden der Anforderung

1. Lesen Sie in der Hauptmethode Ihrer Anwendung die JSON-Datei mit den Daten ein, die den Anforderungen hinzugefügt werden.

2. Rufen Sie die oben erstellte Freihanderkennungsfunktion auf.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Ausführen der Anwendung und Anzeigen der Antwort

Führen Sie die Anwendung aus. Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Sie finden die JSON-Antwort auch auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)


Sehen Sie sich in den folgenden Beispielanwendungen auf GitHub an, wie die Freihanderkennungs-API in einer Freihandschriftinhalte-App funktioniert:
* [C# und universelle Windows-Plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# und Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-Webbrowser-App](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobile Java- und Android-App](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobile Swift- und iOS-App](https://go.microsoft.com/fwlink/?linkid=2089805)