---
title: 'Schnellstart: .NET-Clientbibliothek für die Anomalieerkennung'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.openlocfilehash: 7969e2011a242152e27d7c1aa67b36d99d92adfe
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371869"
---
Erfahren Sie etwas über die ersten Schritte mit der Anomalieerkennungs-Clientbibliothek für .NET. Führen Sie diese Schritte aus, um das Paket zu installieren und mit der Verwendung der vom Dienst zur Verfügung gestellten Algorithmen zu beginnen. Mit dem Anomalieerkennungsdienst können Sie Anomalien in Zeitreihendaten ermitteln, da unabhängig von der Branche, dem Szenario oder der Datenmenge automatisch die am besten geeigneten Modelle für Ihre Daten angewandt werden.

Mit der Anomalieerkennungs-Clientbibliothek für .NET ist Folgendes möglich:

* Erkennen von Anomalien in Ihrem gesamten Zeitreihendataset als Batchanforderung
* Erkennen des Anomaliestatus des letzten Datenpunkts in Ihrer Zeitreihe
* Erkennen von Trendänderungspunkten in Ihrem Dataset

[Bibliotheksreferenzdokumentation](https://aka.ms/anomaly-detector-dotnet-ref) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.2) | [Den Code finden Sie auf GitHub.](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Sobald Sie über Ihr Azure-Abonnement verfügen, erstellen Sie über <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Erstellen einer Anomalieerkennungsressource"  target="_blank"> eine Anomalieerkennungsressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Warten Sie ihre Bereitstellung ab, und klicken Sie auf die Schaltfläche **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Anomalieerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="setting-up"></a>Einrichten

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>Erstellen einer neuen .NET Core-Anwendung

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `anomaly-detector-quickstart`. Dieser Befehl erstellt ein einfaches „Hallo Welt“-Projekt mit einer einzigen C#-Quelldatei: *Program.cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```dotnetcli
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Anomalieerkennungs-Clientbibliothek für .NET:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector
```

Öffnen Sie über das Projektverzeichnis die Datei *program.cs* , und fügen Sie mithilfe von `directives` Folgendes hinzu:

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

Erstellen Sie in der `main()`-Methode der Anwendung Variablen für den Azure-Standort der Ressource und den Schlüssel als Umgebungsvariable. Wenn Sie die Umgebungsvariable nach dem Start der Anwendung erstellen, müssen der Editor, die IDE oder die Shell, in denen sie ausgeführt wird, geschlossen und erneut geladen werden, damit der Zugriff auf die Variable möglich ist.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>Objektmodell

Der Anomalieerkennungs-Client ist ein [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient)-Objekt, das mithilfe der [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)-Klasse, die Ihren Schlüssel enthält, bei Azure authentifiziert wird. Der Client kann die Anomalieerkennung mithilfe von [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) für ein gesamtes Dataset bzw. mithilfe von [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) für den letzten Datenpunkt ausführen. Mit der Methode [ChangePointDetectAsync](https://aka.ms/anomaly-detector-dotnet-ref) werden Punkte erkannt, die Änderungen in einem Trend markieren.

Zeitreihendaten werden als eine Reihe von [Points](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series) in einem [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request)-Objekt gesendet. Das `Request`-Objekt enthält Eigenschaften zum Beschreiben der Daten (z.B. [Granularity](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)) sowie Parameter für die Anomalieerkennung.

Die Antwort der Anomalieerkennung ist je nach der verwendeten Methode ein [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse)-, [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse)- oder [changePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref)-Objekt.

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie folgende Vorgänge mit der Anomalieerkennungs-Clientbibliothek für .NET durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Laden eines Zeitreihendatasets aus einer Datei](#load-time-series-data-from-a-file)
* [Erkennen von Anomalien im gesamten Dataset](#detect-anomalies-in-the-entire-data-set)
* [Erkennen des Anomaliestatus des letzten Datenpunkts](#detect-the-anomaly-status-of-the-latest-data-point)
* [Erkennen der Änderungspunkte im Dataset](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Instanziieren Sie in einer neuen Methode einen Client mit Ihrem Endpunkt und Schlüssel. Erstellen Sie ein [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview)-Objekt mit Ihrem Schlüssel, und verwenden Sie es mit Ihrem Endpunkt, um ein [AnomalyDetectorClient](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview)-Objekt zu erstellen.

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>Laden von Zeitreihendaten aus einer Datei

Laden Sie die Beispieldaten für diesen Schnellstart von [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) herunter:
1. Klicken Sie im Browser mit der rechten Maustaste auf **Raw**.
2. Klicken Sie auf **Link speichern unter**.
3. Speichern Sie die Datei als CSV-Datei in Ihrem Anwendungsverzeichnis.

Die Zeitreihendaten werden als CSV-Datei formatiert und an die Anomalieerkennungs-API gesendet.

Erstellen Sie eine neue Methode zum Lesen der Zeitreihendaten, und fügen Sie sie dem [Request](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview)-Objekt hinzu. Rufen Sie `File.ReadAllLines()` mit dem Dateipfad auf, erstellen Sie eine Liste der [Point](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview)-Objekte, und entfernen Sie alle Neue-Zeile-Zeichen. Extrahieren Sie die Werte, trennen Sie den Datumsstempel vom numerischen Wert, und fügen Sie sie einem neuen `Point`-Objekt hinzu.

Erstellen Sie ein `Request`-Objekt mit der Datenpunktreihe und `Granularity.Daily` für die [Granularität](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) (oder Periodizität) der Datenpunkte.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Erkennen von Anomalien im gesamten Dataset

Erstellen Sie eine Methode zum Aufrufen der [EntireDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_)-Methode des Clients mit dem `Request`-Objekt, und warten Sie auf die Antwort als [EntireDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview)-Objekt. Wenn die Zeitreihe Anomalien enthält, durchlaufen Sie die [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview)-Werte der Antwort, und geben Sie alle Werte aus, die `true` sind. Diese Werte stimmen mit dem Index der anomalen Datenpunkte überein, sofern welche gefunden wurden.

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Erkennen des Anomaliestatus des letzten Datenpunkts

Erstellen Sie eine Methode zum Aufrufen der [LastDetectAsync()](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_)-Methode des Clients mit dem `Request`-Objekt, und warten Sie auf die Antwort als [LastDetectResponse](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview)-Objekt. Überprüfen Sie das [IsAnomaly](/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview)-Attribut der Antwort, um zu ermitteln, ob der letzte gesendete Datenpunkt als Anomalie erkannt wurde.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="detect-change-points-in-the-data-set"></a>Erkennen von Änderungspunkten im Dataset

Erstellen Sie eine Methode zum Aufrufen der [DetectChangePointAsync](https://aka.ms/anomaly-detector-dotnet-ref)-Methode des Clients mit dem `Request`-Objekt, und warten Sie auf die Antwort als [ChangePointDetectResponse](https://aka.ms/anomaly-detector-dotnet-ref)-Objekt. Überprüfen Sie die IsChangePoint-Werte der Antwort, und geben Sie alle aus, die den Wert `true` aufweisen. Diese Werte stimmen mit den Trendänderungspunkten überein, sofern welche gefunden wurden.

[!code-csharp[DetectChangePoint() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=changePointExample)]

## <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]