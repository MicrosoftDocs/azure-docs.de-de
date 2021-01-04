---
title: 'Metrics Advisor: C#-Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: c0f2c9a6a9b17ce1979143840b0647e9af2183e7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356391"
---
[Referenzdokumentation](/dotnet/api/overview/azure/ai.metricsadvisor-readme-pre) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Wenn Sie über ein Azure-Abonnement verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihre Metrics Advisor-Instanz bereitzustellen.  
* Eigene SQL-Datenbank mit Zeitreihendaten.
   
> [!TIP]
> * .NET-Beispiele für Metrics Advisor finden Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md).
> * Es kann zwischen 10 und 30 Minuten dauern, bis Ihre Metrics Advisor-Ressource Ihnen eine Dienstinstanz zur Verwendung bereitstellt. Klicken Sie nach erfolgreicher Bereitstellung auf **Zu Ressource wechseln**. Nach Abschluss der Bereitstellung können Sie Ihre Metrics Advisor-Instanz sowohl über das Webportal als auch per REST-API verwenden. 
> * Die URL für die REST-API finden Sie im Azure-Portal im Abschnitt **Übersicht** Ihrer Ressource. Das sieht folgendermaßen aus:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Installieren Sie nach der Erstellung eines neuen Projekts die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, aktivieren Sie das Kontrollkästchen **Vorabversion einbeziehen**, und suchen Sie nach `Azure.AI.MetricsAdvisor`. Wählen Sie die Version `1.0.0-beta.2` und anschließend **Installieren** aus. 

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `metrics-advisor-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*. 

```console
dotnet new console -n metrics-advisor-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek 

Falls Sie eine andere IDE als Visual Studio verwenden, können Sie die Metrics Advisor-Clientbibliothek für .NET mit dem folgenden Befehl installieren:

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.2
```

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Öffnen Sie über das Projektverzeichnis die Datei *program.cs*, und fügen Sie die folgenden `using`-Direktiven hinzu:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor;
using Azure.AI.MetricsAdvisor.Models;
```

Fügen Sie in der `Main()`-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Die Erstellung führen Sie zu einem späteren Zeitpunkt durch.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige Hauptfunktionen des Metrics Advisor C# SDK verarbeitet.

|Name|Beschreibung|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Verwendung**: <br> - Auflisten von Vorfällen <br> - Auflisten der Grundursache von Vorfällen <br> - Abrufen ursprünglicher Zeitreihendaten und vom Dienst angereicherter Zeitreihendaten <br> - Auflisten von Warnungen <br> - Hinzufügen von Feedback zur Optimierung Ihres Modells |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Zweck:** <br> - Verwalten von Datenfeeds <br> - Festlegen der Konfigurationen für die Anomalieerkennung <br> - Festlegen der Konfigurationen für Anomaliewarnungen <br> - Verwalten von Hooks  |
| [DataFeed](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **Erfassung von Daten Ihrer Datenquelle durch Metrics Advisor. Ein `DataFeed`-Element enthält Zeilen mit:** <br> - Zeitstempeln <br> - Null oder mehr Dimensionen <br> - Einem oder mehreren Measures  |
| [DataFeedMetric](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeedMetric.cs)| Eine Metrik (`DataFeedMetric`) ist ein quantifizierbares Measure, das zum Überwachen und Bewerten des Status eines bestimmten Geschäftsprozesses verwendet wird. Hierbei kann es sich um eine Kombination aus mehreren Zeitreihen handeln, die in Dimensionen unterteilt sind. Eine Metrik vom Typ Webintegrität kann beispielsweise Dimensionen für die Benutzeranzahl und den US-amerikanischen Markt enthalten. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Metrics Advisor-Clientbibliothek für .NET durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Hinzufügen eines Datenfeeds](#add-a-data-feed)
* [Überprüfen des Erfassungsstatus](#check-the-ingestion-status)
* [Konfigurieren der Anomalieerkennung](#configure-anomaly-detection)
* [Erstellen eines Hooks](#create-a-hook)
* [Erstellen einer Warnungskonfiguration](#create-an-alert-configuration)
* [Abfragen der Warnung](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Erstellen Sie in der Klasse `Program` der Anwendung Variablen für die Schlüssel und den Endpunkt Ihrer Ressource.

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Metrics Advisor-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre Abonnementschlüssel und den Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. <br><br>Navigieren Sie zu [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net), um Ihren API-Schlüssel abzurufen. Wählen Sie die entsprechenden Optionen aus: **Verzeichnis**, **Abonnements** und **Arbeitsbereich** für Ihre Ressource. Wählen Sie anschließend **Erste Schritte** aus. Anschließend können Sie Ihre API-Schlüssel unter [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) abrufen.   
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

Wenn Sie über die Abonnement- und API-Schlüssel verfügen, können Sie das „MetricsAdvisorKeyCredential“-Element erstellen. Mit dem Endpunkt und den Schlüsselanmeldeinformationen können Sie das Element [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) erstellen:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

Darüber hinaus können Sie das Element [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) erstellen, um Verwaltungsvorgänge durchzuführen:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Hinzufügen eines Datenfeeds

Metrics Advisor unterstützt mehrere Arten von Datenquellen. In diesem Beispiel wird veranschaulicht, wie Sie ein `DataFeed`-Element erstellen, mit dem Daten aus einer SQL Server-Instanz extrahiert werden. Ersetzen Sie `connection_String` durch Ihre eigene SQL Server-Verbindungszeichenfolge und `query` durch eine Abfrage, mit der Ihre Daten für einen bestimmten Zeitstempel zurückgegeben werden. Darüber hinaus müssen Sie die Werte `DataFeedMetric` und `DataFeedDimension` basierend auf Ihren benutzerdefinierten Daten anpassen.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new SqlServerDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<DataFeedDimension>()
{
    new DataFeedDimension("category"),
    new DataFeedDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

var dataFeed = new DataFeed(dataFeedName, dataFeedSource, dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

Response<string> response = await adminClient.CreateDataFeedAsync(dataFeed);

string dataFeedId = response.Value;

Console.WriteLine($"Data feed ID: {dataFeedId}");

// Note that only the ID of the data feed is known at this point. You can perform another
// service call to GetDataFeedAsync or GetDataFeed to get more information, such as status,
// created time, the list of administrators, or the metric IDs.

Response<DataFeed> response = await adminClient.GetDataFeedAsync(dataFeedId);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>Überprüfen des Erfassungsstatus

Überprüfen Sie den Erfassungsstatus eines zuvor erstellten `DataFeed`-Elements.

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime)
{
    TopCount = 5
};

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 5 statuses.
    if (++statusCount >= 5)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurieren der Anomalieerkennung 

Erstellen Sie eine Konfiguration für die Anomalieerkennung, um dem Dienst mitzuteilen, welche Datenpunkte als Anomalien angesehen werden sollten.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<string> response = await adminClient.CreateDetectionConfigurationAsync(detectionConfiguration);

string detectionConfigurationId = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfigurationId}");
```

### <a name="create-a-hook"></a>Erstellen eines Hooks

Metrics Advisor unterstützt die Klassen `EmailNotificationHook` und `WebNotificationHook` zum Abonnieren von Warnungsbenachrichtigungen. In diesem Beispiel wird veranschaulicht, wie Sie das Element `EmailNotificationHook` erstellen. Sie müssen den Hook an eine Konfiguration für Anomaliewarnungen übergeben, damit Sie Benachrichtigungen erhalten. Weitere Informationen finden Sie unter dem Beispiel zur [Erstellung einer Konfiguration für Anomaliewarnungen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration).

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<string> response = await adminClient.CreateHookAsync(emailHook);

string hookId = response.Value;

Console.WriteLine($"Hook ID: {hookId}");
```

##  <a name="create-an-alert-configuration"></a>Erstellen einer Warnungskonfiguration

Erstellen Sie das Element `AnomalyAlertConfiguration`, um dem Dienst mitzuteilen, für welche Anomalien Warnungen ausgelöst werden sollen.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<string> response = await adminClient.CreateAlertConfigurationAsync(alertConfiguration);

string alertConfigurationId = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfigurationId}");
```

### <a name="query-the-alert"></a>Abfragen der Warnung

Überprüfen Sie die Warnungen, die durch eine bestimmte Anomaliewarnungskonfiguration erstellt wurden.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, AlertQueryTimeMode.AnomalyTime)
{
    TopCount = 5
};

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert created at: {alert.CreatedTime}");
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine();

    // Print at most 5 alerts.
    if (++alertCount >= 5)
    {
        break;
    }
}
```

Wenn Sie die ID einer Warnung kennen, können Sie die [Anomalien](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/README.md#data-point-anomaly) auflisten, die diese Warnung ausgelöst haben.

```csharp
string alertConfigurationId = "<alertConfigurationId>";
string alertId = "<alertId>";

var options = new GetAnomaliesForAlertOptions() { TopCount = 3 };

int anomalyCount = 0;

await foreach (DataPointAnomaly anomaly in client.GetAnomaliesAsync(alertConfigurationId, alertId, options))
{
    Console.WriteLine($"Anomaly detection configuration ID: {anomaly.AnomalyDetectionConfigurationId}");
    Console.WriteLine($"Metric ID: {anomaly.MetricId}");
    Console.WriteLine($"Anomaly at timestamp: {anomaly.Timestamp}");
    Console.WriteLine($"Anomaly detected at: {anomaly.CreatedTime}");
    Console.WriteLine($"Status: {anomaly.Status}");
    Console.WriteLine($"Severity: {anomaly.Severity}");
    Console.WriteLine("Series key:");

    foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
    {
        Console.WriteLine($"  Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
    }

    Console.WriteLine();

    // Print at most 3 anomalies.
    if (++anomalyCount >= 3)
    {
        break;
    }
}
```


### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```dotnet
dotnet run
```