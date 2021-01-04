---
title: 'Schnellstart: Python-Clientbibliothek für die Anomalieerkennung'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: ccfb6f767a977ed9af1019d736aa23c5f8e9950c
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356175"
---
Hier erfahren Sie etwas über die ersten Schritte mit der Anomalieerkennungs-Clientbibliothek für Python. Führen Sie diese Schritte aus, um das Paket zu installieren und mit der Verwendung der vom Dienst zur Verfügung gestellten Algorithmen zu beginnen. Mit dem Anomalieerkennungsdienst können Sie Anomalien in Zeitreihendaten ermitteln, da unabhängig von der Branche, dem Szenario oder der Datenmenge automatisch die am besten geeigneten Modelle für Ihre Daten angewandt werden.

Mit der Anomalieerkennungs-Clientbibliothek für Python ist Folgendes möglich:

* Erkennen von Anomalien in Ihrem gesamten Zeitreihendataset als Batchanforderung
* Erkennen des Anomaliestatus des letzten Datenpunkts in Ihrer Zeitreihe
* Erkennen von Trendänderungspunkten in Ihrem Dataset

[Bibliotheksreferenzdokumentation](https://go.microsoft.com/fwlink/?linkid=2090370) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Paket (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [Codebeispiele auf GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>Voraussetzungen

* [Python 3.x](https://www.python.org/)
* Die [Pandas-Bibliothek für Datenanalyse](https://pandas.pydata.org/)
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.


## <a name="setting-up"></a>Einrichten

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

 Erstellen Sie eine neue Python-Datei, und importieren Sie die folgenden Bibliotheken:

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

Erstellen Sie Variablen für Ihren Schlüssel als Umgebungsvariable, den Pfad zu einer Zeitreihendatendatei und den Azure-Speicherort Ihres Abonnements. Beispiel: `westus2`.

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit Folgendem installieren:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Objektmodell

Der Anomalieerkennungsclient ist ein [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py)-Objekt, das sich mit Ihrem Schlüssel bei Azure authentifiziert. Der Client kann die Anomalieerkennung mithilfe von [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26) für ein gesamtes Dataset bzw. mithilfe von [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87) für den letzten Datenpunkt ausführen. Mit der Funktion [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) werden Punkte erkannt, die Änderungen in einem Trend markieren.

Zeitreihendaten werden als eine Reihe von [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370)-Objekten gesendet. Das `DetectRequest`-Objekt enthält Eigenschaften zum Beschreiben der Daten (z. B. `TimeGranularity`) sowie Parameter für die Anomalieerkennung.

Die Antwort der Anomalieerkennung ist je nach der verwendeten Methode ein [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python)-, [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)- oder [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107)-Objekt.

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der Anomalieerkennungs-Clientbibliothek für Python ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Laden eines Zeitreihendatasets aus einer Datei](#load-time-series-data-from-a-file)
* [Erkennen von Anomalien im gesamten Dataset](#detect-anomalies-in-the-entire-data-set)
* [Erkennen des Anomaliestatus des letzten Datenpunkts](#detect-the-anomaly-status-of-the-latest-data-point)
* [Erkennen der Änderungspunkte im Dataset](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Fügen Sie Ihre Azure-Speicherortvariable dem Endpunkt hinzu, und authentifizieren Sie den Client mit Ihrem Schlüssel.

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>Laden von Zeitreihendaten aus einer Datei

Laden Sie die Beispieldaten für diesen Schnellstart von [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) herunter:
1. Klicken Sie im Browser mit der rechten Maustaste auf **Raw**.
2. Klicken Sie auf **Link speichern unter**.
3. Speichern Sie die Datei als CSV-Datei in Ihrem Anwendungsverzeichnis.

Die Zeitreihendaten werden als CSV-Datei formatiert und an die Anomalieerkennungs-API gesendet.

Laden Sie die Datendatei mit der `read_csv()`-Methode der Pandas-Bibliothek, und erstellen Sie eine leere Listenvariable zum Speichern der Datenreihe. Durchlaufen Sie die Datei, und fügen Sie die Daten als `TimeSeriesPoint`-Objekt an. Dieses Objekt enthält den Zeitstempel und den numerischen Wert aus den Zeilen der CSV-Datendatei.

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

Erstellen Sie ein `DetectRequest`-Objekt mit der Zeitreihe und der `TimeGranularity` (oder Periodizität) der zugehörigen Datenpunkte. Beispiel: `TimeGranularity.daily`.

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>Erkennen von Anomalien im gesamten Dataset

Rufen Sie die API zum Erkennen von Anomalien in den gesamten Zeitreihendaten mit der `detect_entire_series`-Methode des Clients auf. Speichern Sie das zurückgegebene [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)-Objekt. Durchlaufen Sie die `is_anomaly`-Liste der Antwort, und geben Sie den Index aller `true`-Werte aus. Diese Werte stimmen mit dem Index der anomalen Datenpunkte überein, sofern welche gefunden wurden.

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Erkennen des Anomaliestatus des letzten Datenpunkts

Rufen Sie mithilfe der `detect_last_point`-Methode des Clients die Anomalieerkennungs-API auf, um festzustellen, ob Ihr letzter Datenpunkt eine Anomalie ist, und speichern Sie das zurückgegebene `LastDetectResponse`-Objekt. Der `is_anomaly`-Wert der Antwort ist ein Boolescher Wert, der den Anomaliestatus dieses Punktes angibt.  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>Erkennen von Änderungspunkten im Dataset

Rufen Sie die API auf, um Änderungspunkte in den Zeitreihendaten mit der Methode `detect_change_point` des Clients zu erkennen. Speichern Sie das zurückgegebene `ChangePointDetectResponse`-Objekt. Durchlaufen Sie die `is_change_point`-Liste der Antwort, und geben Sie den Index aller `true`-Werte aus. Diese Werte stimmen mit den Indizes der Trendänderungspunkte überein, sofern welche gefunden wurden.

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `python` und Ihrem Dateinamen aus.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]