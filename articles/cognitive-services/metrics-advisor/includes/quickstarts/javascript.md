---
title: 'Metrics Advisor: JavaScript-Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 9222098f719241a6b3d6e50d4181106101c6d912
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356374"
---
[Referenzdokumentation](/javascript/api/overview/azure/ai-metrics-advisor-readme-pre?preserve-view=true&view=azure-node-preview) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md) | [Paket (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Die aktuelle Version von [Node.js](https://nodejs.org/)
* Wenn Sie über ein Azure-Abonnement verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihre Metrics Advisor-Instanz bereitzustellen.  
* Eigene SQL-Datenbank mit Zeitreihendaten.
  
> [!TIP]
> * JavaScript-Beispiele für Metrics Advisor finden Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples).
> * Es kann zwischen 10 und 30 Minuten dauern, bis Ihre Metrics Advisor-Ressource Ihnen eine Dienstinstanz zur Verwendung bereitstellt. Klicken Sie nach erfolgreicher Bereitstellung auf **Zu Ressource wechseln**. Nach Abschluss der Bereitstellung können Sie Ihre Metrics Advisor-Instanz sowohl über das Webportal als auch per REST-API verwenden. 
> * Die URL für die REST-API finden Sie im Azure-Portal im Abschnitt **Übersicht** Ihrer Ressource. Das sieht folgendermaßen aus:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-nodejs-application"></a>Erstellen einer neuen Node.js-Anwendung

Erstellen Sie in einem Konsolenfenster (etwa cmd, PowerShell oder Bash) ein neues Verzeichnis für Ihre App, und rufen Sie es auf. 

```console
mkdir myapp && cd myapp
```

Führen Sie den Befehl `npm init` aus, um eine Knotenanwendung mit der Datei `package.json` zu erstellen. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Installieren Sie das NPM-Paket `@azure/ai-metrics-advisor`:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

Die Datei `package.json` Ihrer App wird mit den Abhängigkeiten aktualisiert.

Erstellen Sie eine Datei namens `index.js`, und importieren Sie die folgenden Bibliotheken:

> [!TIP]
> Möchten Sie sich sofort die gesamte Codedatei für die Schnellstartanleitung ansehen? Die Datei steht [auf GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript) zur Verfügung. Dort finden Sie die Codebeispiele aus dieser Schnellstartanleitung.

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource. 

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Metrics Advisor-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre Abonnementschlüssel und den Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. <br><br>Navigieren Sie zu [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net), um Ihren API-Schlüssel abzurufen. Wählen Sie die entsprechenden Optionen aus: **Verzeichnis**, **Abonnements** und **Arbeitsbereich** für Ihre Ressource. Wählen Sie anschließend **Erste Schritte** aus. Anschließend können Sie Ihre API-Schlüssel unter [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) abrufen.   
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen und Schnittstellen werden einige Hauptfunktionen des Metrics Advisor JavaScript SDK verarbeitet.

|Name|Beschreibung|
|---|---|
| MetricsAdvisorClient | **Verwendung**: <br> - Auflisten von Vorfällen <br> - Auflisten der Grundursache von Vorfällen <br> - Abrufen ursprünglicher Zeitreihendaten und vom Dienst angereicherter Zeitreihendaten <br> - Auflisten von Warnungen <br> - Hinzufügen von Feedback zur Optimierung Ihres Modells |
| MetricsAdvisorAdministrationClient | **Zweck:** <br> - Verwalten von Datenfeeds <br> - Erstellen, Konfigurieren, Abrufen, Auflisten und Löschen von Konfigurationen für Anomaliewarnungen <br> - Verwalten von Hooks  |
| DataFeed | **Erfassung von Daten Ihrer Datenquelle durch Metrics Advisor. Ein `DataFeed`-Element enthält Zeilen mit:** <br> - Zeitstempeln <br> - Null oder mehr Dimensionen <br> - Einem oder mehreren Measures  |
| DataFeedMetric | Eine Metrik (`DataFeedMetric`) ist ein quantifizierbares Measure, das zum Überwachen und Bewerten des Status eines bestimmten Geschäftsprozesses verwendet wird. Hierbei kann es sich um eine Kombination aus mehreren Zeitreihen handeln, die in Dimensionen unterteilt sind. Eine Metrik vom Typ Webintegrität kann beispielsweise Dimensionen für die Benutzeranzahl und den US-amerikanischen Markt enthalten. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Vorgänge mit der Metrics Advisor-Clientbibliothek für JavaScript ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Hinzufügen eines Datenfeeds](#add-a-data-feed)
* [Überprüfen des Erfassungsstatus](#check-ingestion-status)
* [Konfigurieren der Anomalieerkennung](#configure-anomaly-detection)
* [Erstellen eines Hooks](#create-a-hook)
* [Erstellen einer Warnungskonfiguration](#create-an-alert-configuration)
* [Abfragen der Warnung](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Wenn Sie über die beiden Schlüssel und die Endpunktadresse verfügen, können Sie die „MetricsAdvisorKeyCredential“-Klasse verwenden, um die Authentifizierung für die Clients wie folgt durchzuführen:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Hinzufügen eines Datenfeeds

Metrics Advisor unterstützt die Verbindung von unterschiedlichen Typen von Datenquellen. Hier ist ein Beispiel für die Erfassung von Daten aus SQL Server angegeben.

Ersetzen Sie `sql_server_connection_string` durch Ihre eigene SQL Server-Verbindungszeichenfolge und `query` durch eine Abfrage, mit der Ihre Daten für einen bestimmten Zeitstempel zurückgegeben werden. Darüber hinaus müssen Sie die Werte `metric` und `dimension` basierend auf Ihren benutzerdefinierten Daten anpassen.

> [!IMPORTANT]
> Die Abfrage sollte zu jedem Zeitstempel höchstens einen Datensatz für jede Dimensionskombination zurückgeben. Alle von der Abfrage zurückgegebenen Datensätze müssen dieselben Zeitstempel aufweisen. Metrics Advisor führt diese Abfrage für jeden Zeitstempel aus, um die Daten zu erfassen. Weitere Informationen und Beispiele finden Sie im Abschnitt mit [häufig gestellten Fragen zu Abfragen](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>Überprüfen des Erfassungsstatus

Nach dem Starten der Datenerfassung können wir den Erfassungsstatus überprüfen.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurieren der Anomalieerkennung

Wir benötigen eine Konfiguration für die Anomalieerkennung, um zu bestimmen, ob es sich bei einem Punkt der Zeitreihe um eine Anomalie handelt. Auf jede Metrik wird automatisch eine Standardkonfiguration für die Erkennung angewendet. Sie können die für Ihre Daten verwendeten Erkennungsmodi aber optimieren, indem Sie eine angepasste Konfiguration für die Anomalieerkennung erstellen.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>Erstellen eines Hooks

Wir verwenden Hooks, um Echtzeitwarnungen zu abonnieren. In diesem Beispiel erstellen wir einen Webhook für den Metrics Advisor-Dienst, an den die Warnung per POST-Vorgang gesendet werden kann.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Erstellen einer Warnungskonfiguration

Mit diesem Beispiel wird veranschaulicht, wie Sie Bedingungen für die Auslösung einer Warnung konfigurieren und welche Hooks als Ziel für eine Warnung verwendet werden sollen.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>Abfragen der Warnung

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `node` für die Schnellstartdatei aus.

```console
node index.js
```