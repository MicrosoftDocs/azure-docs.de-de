---
title: 'Schnellstart: JavaScript-Clientbibliothek für die Anomalieerkennung'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: b970b099d87148d169b2be3b7e72d32c159f5046
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371571"
---
Hier erfahren Sie etwas über die ersten Schritte mit der Anomalieerkennungs-Clientbibliothek für JavaScript. Führen Sie diese Schritte aus, um das Paket zu installieren und mit der Verwendung der vom Dienst zur Verfügung gestellten Algorithmen zu beginnen. Mit dem Anomalieerkennungsdienst können Sie Anomalien in Zeitreihendaten ermitteln, da unabhängig von der Branche, dem Szenario oder der Datenmenge automatisch die am besten geeigneten Modelle für Ihre Daten angewandt werden.

Mit der Anomalieerkennungs-Clientbibliothek für JavaScript ist Folgendes möglich:

* Erkennen von Anomalien in Ihrem gesamten Zeitreihendataset als Batchanforderung
* Erkennen des Anomaliestatus des letzten Datenpunkts in Ihrer Zeitreihe
* Erkennen von Trendänderungspunkten in Ihrem Dataset

[Bibliotheksreferenzdokumentation](https://go.microsoft.com/fwlink/?linkid=2090788) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Paket (npm)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector) | [Den Code finden Sie auf GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

Erstellen Sie eine Datei namens `index.js`, und importieren Sie die folgenden Bibliotheken:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. Wenn Sie die Umgebungsvariable nach dem Start der Anwendung erstellt haben, müssen der Editor, die IDE oder die Shell, in dem bzw. der sie ausgeführt wird, geschlossen und erneut geöffnet werden, damit der Zugriff auf die Variable möglich ist. Erstellen Sie eine weitere Variable für die Beispieldatendatei, die Sie in einem späteren Schritt herunterladen, und eine leere Liste für die Datenpunkte. Erstellen Sie anschließend ein `ApiKeyCredentials`-Objekt, das den Schlüssel enthält.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie die npm-Pakete `ms-rest-azure` und `azure-cognitiveservices-anomalydetector`. Die Bibliothek „csv-parse“ wird auch in diesem Schnellstart verwendet:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

## <a name="object-model"></a>Objektmodell

Der Anomalieerkennungsclient ist ein [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest)-Objekt, das sich mit Ihrem Schlüssel bei Azure authentifiziert. Der Client kann die Anomalieerkennung mithilfe von [entireDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--) für ein gesamtes Dataset bzw. mithilfe von [LastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) für den letzten Datenpunkt ausführen. Mit der Methode [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) werden Punkte erkannt, die Änderungen in einem Trend markieren. 

Zeitreihendaten werden als eine Reihe von [Punkten](/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) in einem [Request](/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest)-Objekt gesendet. Das `Request`-Objekt enthält Eigenschaften zum Beschreiben der Daten (z.B. [Granularity](/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity)) sowie Parameter für die Anomalieerkennung. 

Die Antwort der Anomalieerkennung ist je nach der verwendeten Methode ein [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest)-, [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest)- oder [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788)-Objekt. 

## <a name="code-examples"></a>Codebeispiele 

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der Anomalieerkennungs-Clientbibliothek für Node.js durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Laden eines Zeitreihendatasets aus einer Datei](#load-time-series-data-from-a-file)
* [Erkennen von Anomalien im gesamten Dataset](#detect-anomalies-in-the-entire-data-set) 
* [Erkennen des Anomaliestatus des letzten Datenpunkts](#detect-the-anomaly-status-of-the-latest-data-point)
* [Erkennen der Änderungspunkte im Dataset](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie ein [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest)-Objekt mit ihrem Endpunkt und Ihren Anmeldeinformationen.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Laden von Zeitreihendaten aus einer Datei

Laden Sie die Beispieldaten für diesen Schnellstart von [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv) herunter:
1. Klicken Sie im Browser mit der rechten Maustaste auf **Raw**.
2. Klicken Sie auf **Link speichern unter**.
3. Speichern Sie die Datei als CSV-Datei in Ihrem Anwendungsverzeichnis.

Die Zeitreihendaten werden als CSV-Datei formatiert und an die Anomalieerkennungs-API gesendet.

Lesen Sie die Datendatei mit der `readFileSync()`-Methode der Bibliothek „csv-parse“, und analysieren Sie die Datei mit `parse()`. Pushen Sie für jede Zeile ein [Point](/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest)-Objekt, das den Zeitstempel und den numerischen Wert enthält.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Erkennen von Anomalien im gesamten Dataset 

Rufen Sie die API zum Erkennen von Anomalien in der gesamten Zeitreihe als Batch mit der [entireDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-)-Methode des Clients auf. Speichern Sie das zurückgegebene [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest)-Objekt. Durchlaufen Sie die `isAnomaly`-Liste der Antwort, und geben Sie den Index aller `true`-Werte aus. Diese Werte stimmen mit dem Index der anomalen Datenpunkte überein, sofern welche gefunden wurden.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Erkennen des Anomaliestatus des letzten Datenpunkts

Rufen Sie mithilfe der [lastDetect()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)-Methode des Clients die Anomalieerkennungs-API auf, um festzustellen, ob Ihr letzter Datenpunkt eine Anomalie ist, und speichern Sie das zurückgegebene [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest)-Objekt. Der `isAnomaly`-Wert der Antwort ist ein Boolescher Wert, der den Anomaliestatus dieses Punktes angibt.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Erkennen von Änderungspunkten im Dataset

Rufen Sie die API auf, um Änderungspunkte in der Zeitreihe mit der Methode [detectChangePoint()](https://go.microsoft.com/fwlink/?linkid=2090788) des Clients zu erkennen. Speichern Sie das zurückgegebene [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788)-Objekt. Durchlaufen Sie die `isChangePoint`-Liste der Antwort, und geben Sie den Index aller `true`-Werte aus. Diese Werte stimmen mit den Indizes der Trendänderungspunkte überein, sofern welche gefunden wurden.

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]