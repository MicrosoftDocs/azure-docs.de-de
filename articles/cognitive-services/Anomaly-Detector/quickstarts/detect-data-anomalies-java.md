---
title: 'Schnellstart: Erkennen von Anomalien in Zeitreihendaten mithilfe der Anomalieerkennungs-REST-API und Java | Microsoft-Dokumentation'
description: Verwenden Sie die Anomalieerkennungs-API, um Anomalien in Ihren Datenreihen als Batch oder in Streamingdaten zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04ace16559a6f5b747bc735aa89265d2962a32b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073222"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-java"></a>Schnellstart: Erkennen von Anomalien in Zeitreihendaten mithilfe der Anomalieerkennungs-REST-API und Java

Dieser Schnellstart unterstützt Sie bei den ersten Schritten in den zwei Erkennungsmodi der Anomalieerkennungs-API, um Anomalien in Ihren Zeitreihendaten zu erkennen. Diese Java-Anwendung sendet zwei API-Anforderungen mit Zeitreihendaten im JSON-Format und erhält entsprechende Antworten.

| API-Anforderung                                        | Anwendungsausgabe                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Erkennen von Anomalien als Batch                        | Die JSON-Antwort enthält den Anomaliestatus (und weitere Daten) für jeden Datenpunkt in den Zeitreihendaten sowie die Positionen aller erkannten Anomalien. |
| Erkennen des Anomaliestatus des letzten Datenpunkts | Die JSON-Antwort enthält den Anomaliestatus (und weitere Daten) für den letzten Datenpunkt in den Zeitreihendaten.                                                                                                                                         |

 Diese Anwendung ist in Java geschrieben, die API ist jedoch ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

- Das [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) oder höher

- Importieren dieser Bibliotheken aus dem Maven-Repository
    - Paket [JSON in Java](https://mvnrepository.com/artifact/org.json/json)
    - Paket [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Eine JSON-Datei mit Datenpunkten auf einer Zeitreihe. Die Beispieldaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

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
    import org.json.JSONArray;
    import org.json.JSONObject;
    import java.io.IOException;
    import java.nio.file.Files;
    import java.nio.file.Paths;
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und Ihren Endpunkt. Unten sehen Sie die URIs, die Sie für die Anomalieerkennung verwenden können. Diese werden später dem Dienstendpunkt angefügt, um URLs für API-Anforderungen zu erstellen.

    |Erkennungsmethode  |URI  |
    |---------|---------|
    |Batcherkennung    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Erkennung am letzten Datenpunkt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```java
    // Replace the subscriptionKey string value with your valid subscription key.
    static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    //replace the endpoint URL with the correct one for your subscription. Your endpoint can be found in the Azure portal. 
    //For example: https://westus2.api.cognitive.microsoft.com
    static final String endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    static final String dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    static final String latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    static final String batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

3. Einlesen der JSON-Datendatei

    ```java
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    ```

## <a name="create-a-function-to-send-requests"></a>Erstellen einer Funktion zum Senden von Anforderungen

1. Erstellen Sie eine neuen Funktion namens `sendRequest()`, die die oben erstellten Variablen verwendet. Führen Sie anschließend die folgenden Schritte aus.

2. Erstellen Sie ein `CloseableHttpClient`-Objekt, das Anforderungen an die API senden kann. Senden Sie die Anforderung an ein `HttpPost`-Anforderungsobjekt, indem Sie Ihren Endpunkt und eine URL der Anomalieerkennung kombinieren.

3. Verwenden Sie die Funktion `setHeader()` der Anforderung, um den Header `Content-Type` auf `application/json` festzulegen, und fügen Sie Ihren Abonnementschlüssel dem Header `Ocp-Apim-Subscription-Key` hinzu.

4. Verwenden Sie die Funktion `setEntity()` der Anforderung für die zu sendenden Daten.

5. Verwenden Sie die Funktion `execute()` des Clients, um die Anforderung zu senden, und speichern Sie sie in einem `CloseableHttpResponse`-Objekt.

6. Erstellen Sie ein `HttpEntity`-Objekt, um den Inhalt der Antwort zu speichern. Rufen Sie den Inhalt mit `getEntity()` ab. Wenn die Antwort nicht leer ist, geben Sie sie zurück.

```java
static String sendRequest(String apiAddress, String endpoint, String subscriptionKey, String requestData) {
    try (CloseableHttpClient client = HttpClients.createDefault()) {
        HttpPost request = new HttpPost(endpoint + apiAddress);
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
        System.err.println("Exception on Anomaly Detector: " + ex.getMessage());
        ex.printStackTrace();
    }
    return null;
}
```

## <a name="detect-anomalies-as-a-batch"></a>Erkennen von Anomalien als Batch

1. Erstellen Sie eine Methode namens `detectAnomaliesBatch()`, um Anomalien in allen Daten als Batch zu erkennen. Rufen Sie die oben erstellte Methode `sendRequest()` mit dem Endpunkt, der URL, dem Abonnementschlüssel und den JSON-Daten auf. Rufen Sie das Ergebnis ab, und geben Sie es in der Konsole aus.

2. Wenn die Antwort das Feld `code` enthält, geben Sie den Fehlercode und die Fehlermeldung aus.

3. Andernfalls suchen Sie nach den Positionen der Anomalien im Dataset. Das Feld `isAnomaly` der Antwort enthält einen booleschen Wert, der angibt, ob es sich bei einem bestimmten Datenpunkt um eine Anomalie handelt. Rufen Sie das JSON-Array ab und durchlaufen Sie es, indem Sie den Index aller `true`-Werte ausgeben. Diese Werte stimmen mit dem Index der anomalen Datenpunkte überein, sofern welche gefunden wurden.

```java
static void detectAnomaliesBatch(String requestData) {
    System.out.println("Detecting anomalies as a batch");
    String result = sendRequest(batchDetectionUrl, endpoint, subscriptionKey, requestData);
    if (result != null) {
        System.out.println(result);
        JSONObject jsonObj = new JSONObject(result);
        if (jsonObj.has("code")) {
            System.out.println(String.format("Detection failed. ErrorCode:%s, ErrorMessage:%s", jsonObj.getString("code"), jsonObj.getString("message")));
        } else {
            JSONArray jsonArray = jsonObj.getJSONArray("isAnomaly");
            System.out.println("Anomalies found in the following data positions:");
            for (int i = 0; i < jsonArray.length(); ++i) {
                if (jsonArray.getBoolean(i))
                    System.out.print(i + ", ");
            }
            System.out.println();
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Erkennen des Anomaliestatus des letzten Datenpunkts

* Erstellen Sie eine Methode namens `detectAnomaliesLatest()`, um den Anomaliestatus des letzten Datenpunkts im Dataset zu erkennen. Rufen Sie die oben erstellte Methode `sendRequest()` mit dem Endpunkt, der URL, dem Abonnementschlüssel und den JSON-Daten auf. Rufen Sie das Ergebnis ab, und geben Sie es in der Konsole aus.

```java
static void detectAnomaliesLatest(String requestData) {
    System.out.println("Determining if latest data point is an anomaly");
    String result = sendRequest(latestPointDetectionUrl, endpoint, subscriptionKey, requestData);
    System.out.println(result);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Laden der Zeitreihendaten und Senden der Anforderung

1. Lesen Sie in der Hauptmethode Ihrer Anwendung die JSON-Datei mit den Daten ein, die den Anforderungen hinzugefügt werden.

2. Rufen Sie die beiden oben erstellten Funktionen zur Erkennung von Anomalien auf.

```java
public static void main(String[] args) throws Exception {
    String requestData = new String(Files.readAllBytes(Paths.get(dataPath)), "utf-8");
    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);
}
```

### <a name="example-response"></a>Beispielantwort

Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Klicken Sie unten auf die Links, um die JSON-Antwort auf GitHub anzuzeigen:
* [Beispielantwort für die Batcherkennung](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Beispielantwort für die Erkennung des letzten Punkts](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
