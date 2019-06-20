---
title: 'Schnellstart: Erkennen von Anomalien als Batch mithilfe der Anomalieerkennungs-REST-API und Python | Microsoft-Dokumentation'
description: Verwenden Sie die Anomalieerkennungs-API, um Anomalien in Ihren Datenreihen als Batch oder in Streamingdaten zu erkennen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: cd37638b9bac2a1b48f0ef0abb7b18da440049f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080741"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Schnellstart: Erkennen von Anomalien in Zeitreihendaten mithilfe der Anomalieerkennungs-REST-API und Python

Dieser Schnellstart unterstützt Sie bei den ersten Schritten in den zwei Erkennungsmodi der Anomalieerkennungs-API, um Anomalien in Ihren Zeitreihendaten zu erkennen. Diese Python-Anwendung sendet zwei API-Anforderungen mit Zeitreihendaten im JSON-Format und erhält entsprechende Antworten.

| API-Anforderung                                        | Anwendungsausgabe                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Erkennen von Anomalien als Batch                        | Die JSON-Antwort enthält den Anomaliestatus (und weitere Daten) für jeden Datenpunkt in den Zeitreihendaten sowie die Positionen aller erkannten Anomalien. |
| Erkennen des Anomaliestatus des letzten Datenpunkts | Die JSON-Antwort enthält den Anomaliestatus (und weitere Daten) für den letzten Datenpunkt in den Zeitreihendaten.                                                                                                                                         |

 Diese Anwendung ist zwar in Python geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist.

## <a name="prerequisites"></a>Voraussetzungen

- [Python 2.x oder 3.x](https://www.python.org/downloads/)

- Die [Anforderungsbibliothek](http://docs.python-requests.org) für Python

- Eine JSON-Datei mit Datenpunkten auf einer Zeitreihe Die Beispieldaten für diesen Schnellstart finden Sie auf [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Erstellen einer neuen Anwendung

1. Erstellen Sie in Ihrem bevorzugten Editor oder Ihrer bevorzugten IDE eine neue Python-Datei. Fügen Sie die folgenden Importe hinzu.

    ```python
    import requests
    import json
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel und Ihren Endpunkt. Unten sehen Sie die URIs, die Sie für die Anomalieerkennung verwenden können. Diese werden später dem Dienstendpunkt angefügt, um URLs für API-Anforderungen zu erstellen.

    |Erkennungsmethode  |URI  |
    |---------|---------|
    |Batcherkennung    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Erkennung am letzten Datenpunkt     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Lesen Sie in der JSON-Datendatei, indem Sie sie öffnen und `json.load()` verwenden.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Erstellen einer Funktion zum Senden von Anforderungen

1. Erstellen Sie eine neuen Funktion namens `send_request()`, die die oben erstellten Variablen verwendet. Führen Sie anschließend die folgenden Schritte aus.

2. Erstellen Sie ein Wörterbuch für die Anforderungsheader. Setzen Sie `Content-Type` auf `application/json`, und fügen Sie dem Header `Ocp-Apim-Subscription-Key` Ihren Abonnementschlüssel hinzu.

3. Senden Sie die Anforderung mit `requests.post()`. Kombinieren Sie den Endpunkt und die Anomalieerkennungs-URL zu einer vollständigen Anforderungs-URL, und schließen Sie die Header und JSON-Anforderungsdaten ein. Und geben Sie dann die Antwort zurück.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Erkennen von Anomalien als Batch

1. Erstellen Sie eine Methode namens `detect_batch()`, um Anomalien in allen Daten als Batch zu erkennen. Rufen Sie die oben erstellte Methode `send_request()` mit dem Endpunkt, der URL, dem Abonnementschlüssel und den JSON-Daten auf.

2. Rufen Sie für das Ergebnis `json.dumps()` auf, um es zu formatieren, und geben Sie es in der Konsole aus.

3. Wenn die Antwort das Feld `code` enthält, geben Sie den Fehlercode und die Fehlermeldung aus.

4. Andernfalls suchen Sie nach den Positionen der Anomalien im Dataset. Das Feld `isAnomaly` der Antwort enthält einen booleschen Wert, der angibt, ob es sich bei einem bestimmten Datenpunkt um eine Anomalie handelt. Gehen Sie die Liste durch, und geben Sie den Index aller `True`-Werte aus. Diese Werte stimmen mit dem Index der anomalen Datenpunkte überein, sofern welche gefunden wurden.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Erkennen des Anomaliestatus des letzten Datenpunkts

1. Erstellen Sie eine Methode namens `detect_latest()`, um zu ermitteln, ob der letzte Datenpunkt in der Zeitreihe eine Anomalie ist. Rufen Sie die oben erstellte Methode `send_request()` mit dem Endpunkt, der URL, dem Abonnementschlüssel und den JSON-Daten auf. 

2. Rufen Sie für das Ergebnis `json.dumps()` auf, um es zu formatieren, und geben Sie es in der Konsole aus.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Laden der Zeitreihendaten und Senden der Anforderung

1. Laden Sie die JSON-Zeitreihendaten in einem Dateihandler, und wenden Sie `json.load()` darauf an. Rufen Sie dann die oben erstellte Methode zur Anomalieerkennung auf.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Beispielantwort

Eine erfolgreiche Antwort wird im JSON-Format zurückgegeben. Klicken Sie unten auf die Links, um die JSON-Antwort auf GitHub anzuzeigen:
* [Beispielantwort für die Batcherkennung](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Beispielantwort für die Erkennung des letzten Punkts](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [REST-API-Referenz](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
