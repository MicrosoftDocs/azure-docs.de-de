---
title: 'Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und Java'
description: Verwenden Sie die Freihanderkennungs-REST-API, um Freihandstriche zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 04f2ac17871bbaf0506fe18122507167b23869a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060936"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Schnellstart: Erkennen von Freihandeingaben mit der Freihanderkennungs-REST-API und Java

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

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt, und importieren Sie die folgenden Bibliotheken.

    ```java
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.StringEntity;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und Ihren Endpunkt. Den folgenden URI können Sie für die Freihanderkennung verwenden. Er wird später dem Dienstendpunkt angefügt, um die URL für API-Anforderungen zu erstellen.

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
    // Replace the dataPath string with a path to the JSON formatted ink stroke data file.
    static final String dataPath = "PATH_TO_INK_STROKE_DATA";
    
    static final String endpoint = "https://api.cognitive.microsoft.com";
    static final String inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Erstellen einer Funktion zum Senden von Anforderungen

1. Erstellen Sie eine neuen Funktion namens `sendRequest()`, die die oben erstellten Variablen verwendet. Führen Sie anschließend die folgenden Schritte aus.

2. Erstellen Sie ein `CloseableHttpClient`-Objekt, das Anforderungen an die API senden kann. Senden Sie die Anforderung an ein `HttpPut`-Anforderungsobjekt, indem Sie Ihren Endpunkt und die URL der Freihanderkennung kombinieren.

3. Verwenden Sie die Funktion `setHeader()` der Anforderung, um den Header `Content-Type` auf `application/json` festzulegen, und fügen Sie Ihren Abonnementschlüssel dem Header `Ocp-Apim-Subscription-Key` hinzu.

4. Verwenden Sie die Funktion `setEntity()` der Anforderung für die zu sendenden Daten.   

5. Verwenden Sie die Funktion `execute()` des Clients, um die Anforderung zu senden, und speichern Sie sie in einem `CloseableHttpResponse`-Objekt. 

6. Erstellen Sie ein `HttpEntity`-Objekt, um den Inhalt der Antwort zu speichern. Rufen Sie den Inhalt mit `getEntity()` ab. Wenn die Antwort nicht leer ist, geben Sie sie zurück.
    
    ```java
    static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            HttpPut request = new HttpPut(endpoint + apiAddress);
            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.setEntity(new StringEntity(requestData));
            try (CloseableHttpResponse response = client.execute(request)) {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    return EntityUtils.toString(respEntity, "utf-8");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            }
        } catch (IOException ex) {
            System.err.println("Exception recognizing ink: " + ex.getMessage());
            ex.printStackTrace();
        }
        return null;
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Senden einer Freihanderkennungsanforderung

Erstellen Sie eine Methode namens `recognizeInk()` zur Erkennung Ihrer Freihandstrichdaten. Rufen Sie die oben erstellte Methode `sendRequest()` mit dem Endpunkt, der URL, dem Abonnementschlüssel und den JSON-Daten auf. Rufen Sie das Ergebnis ab, und geben Sie es in der Konsole aus.

```java
static void recognizeInk(String requestData) {
    System.out.println("Sending an ink recognition request");
    String result = sendRequest(inkRecognitionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Laden der digitalen Freihanddaten und Senden der Anforderung

1. Lesen Sie in der Hauptmethode Ihrer Anwendung die JSON-Datei mit den Daten ein, die den Anforderungen hinzugefügt werden.

2. Rufen Sie die oben erstellte Freihanderkennungsfunktion auf.
    
    ```java
    public static void main(String[] args) throws Exception {
        String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "UTF-8");
        recognizeInk(requestData);
    }
    ```

## <a name="run-the-application-and-view-the-response"></a>Ausführen der Anwendung und Anzeigen der Antwort

Führen Sie die Anwendung aus. Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Sie finden die JSON-Antwort auch auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](https://go.microsoft.com/fwlink/?linkid=2089907)


Sehen Sie sich in den folgenden Beispielanwendungen auf GitHub an, wie die Freihanderkennungs-API in einer Freihandschriftinhalte-App funktioniert:
* [C# und universelle Windows-Plattform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# und Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-Webbrowser-App](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobile Java- und Android-App](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobile Swift- und iOS-App](https://go.microsoft.com/fwlink/?linkid=2089805)
