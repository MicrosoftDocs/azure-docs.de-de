---
title: 'Metrics Advisor: Java-Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 17c8de41f6c1df4a54ec6bd564df733291a707f7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356425"
---
[Referenzdokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [Artefakt (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Aktuelle Version des [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-Buildtool](https://gradle.org/install/) oder einen anderen Abhängigkeit-Manager
* Wenn Sie über ein Azure-Abonnement verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihre Metrics Advisor-Instanz bereitzustellen.  
* Eigene SQL-Datenbank mit Zeitreihendaten.
  
  
> [!TIP]
> * Java-Beispiele für Metrics Advisor finden Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples).
> * Es kann zwischen 10 und 30 Minuten dauern, bis Ihre Metrics Advisor-Ressource Ihnen eine Dienstinstanz zur Verwendung bereitstellt. Klicken Sie nach erfolgreicher Bereitstellung auf **Zu Ressource wechseln**. Nach Abschluss der Bereitstellung können Sie Ihre Metrics Advisor-Instanz sowohl über das Webportal als auch per REST-API verwenden. 
> * Die URL für die REST-API finden Sie im Azure-Portal im Abschnitt **Übersicht** Ihrer Ressource. Das sieht folgendermaßen aus:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-gradle-project"></a>Erstellen eines neuen Gradle-Projekts

In dieser Schnellstartanleitung wird der Gradle-Abhängigkeits-Manager verwendet. Weitere Informationen zur Clientbibliothek finden Sie im [Maven Central Repository](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `gradle init` in Ihrem Arbeitsverzeichnis aus. Mit diesem Befehl werden grundlegende Builddateien für Gradle erstellt, u. a. die Datei *build.gradle.kts*. Diese Datei wird zur Laufzeit zum Erstellen und Konfigurieren Ihrer Anwendung verwendet.

```console
gradle init --type basic
```

Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Navigieren Sie zur Datei *build.gradle.kts*, und öffnen Sie sie in Ihrer bevorzugten IDE bzw. Ihrem bevorzugten Text-Editor. Kopieren Sie anschließend diese Buildkonfiguration. Achten Sie darauf, dass Sie die Projektabhängigkeiten einbinden.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.2")
}
```

### <a name="create-a-java-file"></a>Erstellen einer Java-Datei

Erstellen Sie einen Ordner für Ihre Beispiel-App. Führen Sie in Ihrem Arbeitsverzeichnis den folgenden Befehl aus:

```console
mkdir -p src/main/java
```

Navigieren Sie zum neuen Ordner, und erstellen Sie eine Datei mit dem Namen *MetricsAdvisorQuickstarts.java*. Öffnen Sie sie in Ihrem bevorzugten Editor bzw. Ihrer bevorzugten IDE, und fügen Sie die folgenden `import`-Anweisungen hinzu:

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie in der `MetricsAdvisorQuickstarts`-Klasse der Anwendung Variablen für den Schlüssel und Endpunkt Ihrer Ressource.


> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Metrics Advisor-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre Abonnementschlüssel und den Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. <br><br>Navigieren Sie zu [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net), um Ihren API-Schlüssel abzurufen. Wählen Sie die entsprechenden Optionen aus: **Verzeichnis**, **Abonnements** und **Arbeitsbereich** für Ihre Ressource. Wählen Sie anschließend **Erste Schritte** aus. Anschließend können Sie Ihre API-Schlüssel unter [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) abrufen.   
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

Fügen Sie in der `Main()`-Methode der Anwendung Aufrufe für die Methoden hinzu, die in dieser Schnellstartanleitung verwendet werden. Die Erstellung führen Sie zu einem späteren Zeitpunkt durch.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige der Hauptfunktionen des Metrics Advisor Java SDK verarbeitet.

|Name|Beschreibung|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **Verwendung**: <br> - Auflisten von Anomalieincidents <br> - Auflisten der Grundursache von Vorfällen <br> - Abrufen ursprünglicher Zeitreihendaten und vom Dienst angereicherter Zeitreihendaten <br> - Auflisten von Warnungen <br> - Hinzufügen von Feedback zur Optimierung Ihres Modells |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **Zweck:** <br> - Verwalten von Datenfeeds <br> - Festlegen der Konfigurationen für die Anomalieerkennung <br> - Festlegen der Konfigurationen für Anomaliewarnungen <br> - Verwalten von Hooks  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeed.html) | **Erfassung von Daten Ihrer Datenquelle durch Metrics Advisor. Ein `DataFeed`-Element enthält Zeilen mit:** <br> - Zeitstempeln <br> - Null oder mehr Dimensionen <br> - Einem oder mehreren Measures  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.2/com/azure/ai/metricsadvisor/models/DataFeedMetric.html) | Eine Metrik (`DataFeedMetric`) ist ein quantifizierbares Measure, das zum Überwachen und Bewerten des Status eines bestimmten Geschäftsprozesses verwendet wird. Hierbei kann es sich um eine Kombination aus mehreren Zeitreihen handeln, die in Dimensionen unterteilt sind. Eine Metrik vom Typ Webintegrität kann beispielsweise Dimensionen für die Benutzeranzahl und den US-amerikanischen Markt enthalten. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Aufgaben mit der Metrics Advisor-Clientbibliothek für Java durchgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Hinzufügen eines Datenfeeds](#add-a-data-feed)
* [Überprüfen des Erfassungsstatus](#check-the-ingestion-status)
* [Konfigurieren der Anomalieerkennung](#configure-anomaly-detection)
* [Erstellen eines Hooks](#create-a-hook)
* [Erstellen einer Warnungskonfiguration](#create-an-alert-configuration)
* [Abfragen der Warnung](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Erstellen eines Metrics Advisor-Clients mit „MetricsAdvisorKeyCredential“

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Erstellen eines Clients für die Metrikverwaltung mit „MetricsAdvisorKeyCredential“

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Hinzufügen eines Datenfeeds

Ersetzen Sie `sql_server_connection_string` durch Ihre eigene SQL Server-Verbindungszeichenfolge und `query` durch eine Abfrage, mit der Ihre Daten für einen bestimmten Zeitstempel zurückgegeben werden. Darüber hinaus müssen Sie die Werte `DataFeedMetric` und `DataFeedDimension` basierend auf Ihren benutzerdefinierten Daten anpassen.

> [!IMPORTANT]
> Die Abfrage sollte zu jedem Zeitstempel höchstens einen Datensatz für jede Dimensionskombination zurückgeben. Alle von der Abfrage zurückgegebenen Datensätze müssen dieselben Zeitstempel aufweisen. Metrics Advisor führt diese Abfrage für jeden Zeitstempel aus, um die Daten zu erfassen. Weitere Informationen und Beispiele finden Sie im Abschnitt mit [häufig gestellten Fragen zu Abfragen](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

```java
DataFeed dataFeed = new DataFeed()
    .setName("dataFeedName")
    .setSource(new MySqlDataFeedSource("sql_server_connection_string", "query"))
    .setGranularity(new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY))
    .setSchema(new DataFeedSchema(
        Arrays.asList(
            new DataFeedMetric().setName("cost"),
            new DataFeedMetric().setName("revenue")
        )).setDimensions(
        Arrays.asList(
            new DataFeedDimension().setName("city"),
            new DataFeedDimension().setName("category")
        ))
    )
    .setIngestionSettings(new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")))
    .setOptions(new DataFeedOptions()
        .setDescription("data feed description")
        .setRollupSettings(new DataFeedRollupSettings()
            .setRollupType(DataFeedRollupType.AUTO_ROLLUP)));
final DataFeed createdSqlDataFeed = metricsAdvisorAdminClient.createDataFeed(dataFeed);

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(System.out::println);
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());

if (SQL_SERVER_DB == createdSqlDataFeed.getSourceType()) {
    System.out.printf("Data feed sql server query: %s%n",
        ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
}
```

## <a name="check-the-ingestion-status"></a>Überprüfen des Erfassungsstatus

In diesem Beispiel wird der Erfassungsstatus einer zuvor bereitgestellten Datenfeedquelle überprüft.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdminClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Konfigurieren der Anomalieerkennung 

Dieses Beispiel veranschaulicht, wie ein Benutzer für seine Daten eine Konfiguration für die Anomalieerkennung festlegen kann.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdminClient.createMetricAnomalyDetectionConfig(
        metricId,
        new AnomalyDetectionConfiguration("My dataPoint anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Erstellen eines Hooks

In diesem Beispiel wird ein E-Mail-Hook erstellt, über den Warnungen zu Anomalieincidents empfangen werden.

```java
NotificationHook emailNotificationHook = new EmailNotificationHook("email Hook")
    .setDescription("my email Hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://example.com/handleAlerts"); // you must enter a valid webhook url to post the alert payload

final NotificationHook notificationHook = metricsAdvisorAdminClient.createHook(emailNotificationHook);
EmailNotificationHook createdEmailHook = (EmailNotificationHook) notificationHook;
System.out.printf("Email Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Email Hook name: %s%n", createdEmailHook.getName());
System.out.printf("Email Hook description: %s%n", createdEmailHook.getDescription());
System.out.printf("Email Hook external Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Email Hook emails to alert: %s%n",
    String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Erstellen einer Warnungskonfiguration

In diesem Beispiel wird veranschaulicht, wie ein Benutzer eine Warnungskonfiguration für erkannte Anomalien in seinen Daten festlegen kann.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdminClient.createAnomalyAlertConfig(
        new AnomalyAlertConfiguration("My anomaly alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityRangeCondition(new SeverityCondition()
                                .setMaxAlertSeverity(AnomalySeverity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>Abfragen der Warnung

In diesem Beispiel wird veranschaulicht, wie ein Benutzer Warnungen abfragen kann, die für eine Konfiguration der Warnungserkennung ausgelöst wurden, und Anomalien zu dieser Warnung abrufen kann.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
final OffsetDateTime startTime = OffsetDateTime.parse("2020-01-01T00:00:00Z");
final OffsetDateTime endTime = OffsetDateTime.parse("2020-09-09T00:00:00Z");
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
        startTime, endTime)
    .forEach(alert -> {
        System.out.printf("AnomalyAlert Id: %s%n", alert.getId());
        System.out.printf("AnomalyAlert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("DataPoint Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("DataPoint Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("DataPoint Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("DataPoint Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

Sie können die App mit folgendem Befehl erstellen:

```console
gradle build
```
### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Ziel `run` aus:

```console
gradle run
```