---
title: 'Schnellstart: Erkennen von Anomalien in Zeitreihendaten mithilfe der Anomalieerkennungs-REST-API und C# | Microsoft-Dokumentation'
description: Verwenden Sie die Anomalieerkennungs-API, um Anomalien in Ihren Datenreihen als Batch oder in Streamingdaten zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 2a02b56c2fa0f99166cfde0f0089273ed2af4cb9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073208"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Schnellstart: Erkennen von Anomalien in Zeitreihendaten mithilfe der Anomalieerkennungs-REST-API und C# 

Dieser Schnellstart unterstützt Sie bei den ersten Schritten in den zwei Erkennungsmodi der Anomalieerkennungs-API, um Anomalien in Ihren Zeitreihendaten zu erkennen. Diese C#-Anwendung sendet zwei API-Anforderungen mit Zeitreihendaten im JSON-Format und erhält entsprechende Antworten.

| API-Anforderung                                        | Anwendungsausgabe                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Erkennen von Anomalien als Batch                        | Die JSON-Antwort enthält den Anomaliestatus (und weitere Daten) für jeden Datenpunkt in den Zeitreihendaten sowie die Positionen aller erkannten Anomalien. |
| Erkennen des Anomaliestatus des letzten Datenpunkts | Die JSON-Antwort enthält den Anomaliestatus (und weitere Daten) für den letzten Datenpunkt in den Zeitreihendaten.                                                                                                                                         |

 Die Anwendung ist zwar in C# geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

- Eine beliebige Edition von [Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/downloads/)

- Das [Json.NET](https://www.newtonsoft.com/json)-Framework, das als NuGet-Paket verfügbar ist So installieren Sie Newtonsoft.Json als NuGet-Paket in Visual Studio
    
    1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt.
    2. Wählen Sie **NuGet-Pakete verwalten** aus.
    3. Suchen Sie nach *Newtonsoft.Json*, und installieren Sie das Paket.

- Unter Linux/macOS kann diese Anwendung mit [Mono](https://www.mono-project.com/) ausgeführt werden.

- Eine JSON-Datei mit Datenpunkten auf einer Zeitreihe. Die Beispieldaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie in Visual Studio eine neue Konsolenprojektmappe, und fügen Sie die folgenden Pakete hinzu. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und Ihren Endpunkt. Unten sehen Sie die URIs, die Sie für die Anomalieerkennung verwenden können. Diese werden später dem Dienstendpunkt angefügt, um URLs für API-Anforderungen zu erstellen.

    |Erkennungsmethode  |URI  |
    |---------|---------|
    |Batcherkennung    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Erkennung am letzten Datenpunkt     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Erstellen einer Funktion zum Senden von Anforderungen

1. Erstellen Sie eine neuen asynchronen Funktion namens `Request`, die die oben erstellten Variablen verwendet.

2. Legen Sie das Sicherheitsprotokoll und die Headerinformationen des Clients mithilfe eines `HttpClient`-Objekts fest. Achten Sie darauf, dass Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzufügen. Erstellen Sie dann ein `StringContent`-Objekt für die Anforderung.

3. Senden Sie die Anforderung mit `PostAsync()`, und geben Sie dann die Antwort zurück.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Erkennen von Anomalien als Batch

1. Erstellen Sie eine neue Funktion namens `detectAnomaliesBatch()`. Erstellen Sie die Anforderung und senden Sie sie, indem Sie die Funktion `Request()` mit Ihrem Endpunkt, Ihrem Abonnementschlüssel, der URL für die Batcherkennung von Anomalien und den Zeitreihendaten aufrufen.

2. Deserialisieren Sie das JSON-Objekt, und schreiben Sie es in die Konsole.

3. Wenn die Antwort das Feld `code` enthält, geben Sie den Fehlercode und die Fehlermeldung aus. 

4. Andernfalls suchen Sie nach den Positionen der Anomalien im Dataset. Das Feld `isAnomaly` der Antwort enthält ein Array von booleschen Werten, von denen jeder angibt, ob ein Datenpunkt eine Anomalie darstellt. Konvertieren Sie diese in mit der Funktion `ToObject<bool[]>()` des Antwortobjekts in ein Zeichenfolgenarray. Gehen Sie das Array durch, und geben Sie den Index aller `true`-Werte aus. Diese Werte stimmen mit dem Index der anomalen Datenpunkte überein, sofern welche gefunden wurden.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Erkennen des Anomaliestatus des letzten Datenpunkts

1. Erstellen Sie eine neue Funktion namens `detectAnomaliesLatest()`. Erstellen Sie die Anforderung und senden Sie sie, indem Sie die Funktion `Request()` mit Ihrem Endpunkt, Ihrem Abonnementschlüssel, der URL für die letzte Erkennung von Punktanomalien und den Zeitreihendaten aufrufen.

2. Deserialisieren Sie das JSON-Objekt, und schreiben Sie es in die Konsole.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Laden der Zeitreihendaten und Senden der Anforderung

1. Laden Sie in der Hauptmethode Ihrer Anwendung Ihre JSON-Zeitreihendaten mit `File.ReadAllText()`. 

2. Rufen Sie die oben erstellten Funktionen zur Erkennung von Anomalien auf. Verwenden Sie `System.Console.ReadKey()`, damit das Konsolenfenster nach dem Ausführen der Anwendung geöffnet bleibt.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Beispielantwort

Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Klicken Sie unten auf die Links, um die JSON-Antwort auf GitHub anzuzeigen:
* [Beispielantwort für die Batcherkennung](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Beispielantwort für die Erkennung des letzten Punkts](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
