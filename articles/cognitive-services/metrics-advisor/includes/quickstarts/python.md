---
title: 'Metrics Advisor: Python-Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 68dfa0564575a503bd721a6b4a0489ea5c3b3baa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356408"
---
[Referenzdokumentation](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [Paket (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [Beispiele](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Wenn Sie über ein Azure-Abonnement verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihre Metrics Advisor-Instanz bereitzustellen.  
* Eigene SQL-Datenbank mit Zeitreihendaten.
  
> [!TIP]
> * Python-Beispiele für Metrics Advisor finden Sie auf [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * Es kann zwischen 10 und 30 Minuten dauern, bis Ihre Metrics Advisor-Ressource Ihnen eine Dienstinstanz zur Verwendung bereitstellt. Klicken Sie nach erfolgreicher Bereitstellung auf **Zu Ressource wechseln**. Nach Abschluss der Bereitstellung können Sie Ihre Metrics Advisor-Instanz sowohl über das Webportal als auch per REST-API verwenden.
> * Die URL für die REST-API finden Sie im Azure-Portal im Abschnitt **Übersicht** Ihrer Ressource. Das sieht folgendermaßen aus:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Einrichten

### <a name="install-the-client-library"></a>Installieren der Clientbibliothek

Nach der Installation von Python, können Sie die Clientbibliothek mit Folgendem installieren:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Erstellen einer neuen Python-Anwendung

Erstellen Sie eine neue Python-Datei, und importieren Sie die folgenden Bibliotheken:

```python
import os
import datetime
```

Erstellen Sie Variablen für den Azure-Endpunkt und -Schlüssel Ihrer Ressource.

> [!IMPORTANT]
> Öffnen Sie das Azure-Portal. Wenn die im Abschnitt **Voraussetzungen** erstellte Metrics Advisor-Ressource erfolgreich bereitgestellt wurde, klicken Sie unter **Nächste Schritte** auf die Schaltfläche **Zu Ressource wechseln**. Ihre Abonnementschlüssel und den Endpunkt finden Sie auf der Seite mit dem **Schlüssel und dem Endpunkt** der Ressource unter **Ressourcenverwaltung**. <br><br>Navigieren Sie zu [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net), um Ihren API-Schlüssel abzurufen. Wählen Sie die entsprechenden Optionen aus: **Verzeichnis**, **Abonnements** und **Arbeitsbereich** für Ihre Ressource. Wählen Sie anschließend **Erste Schritte** aus. Anschließend können Sie Ihre API-Schlüssel unter [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) abrufen.   
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../../../cognitive-services-security.md).

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Objektmodell

Mit den folgenden Klassen werden einige Hauptfunktionen des Metrics Advisor Python SDK verarbeitet.

|Name|Beschreibung|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Verwendung**: <br> - Auflisten von Vorfällen <br> - Auflisten der Grundursache von Vorfällen <br> - Abrufen ursprünglicher Zeitreihendaten und vom Dienst angereicherter Zeitreihendaten <br> - Auflisten von Warnungen <br> - Hinzufügen von Feedback zur Optimierung Ihres Modells |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Zweck:** <br> - Verwalten von Datenfeeds <br> - Erstellen, Konfigurieren, Abrufen, Auflisten und Löschen von Konfigurationen für die Anomalieerkennung <br> - Erstellen, Konfigurieren, Abrufen, Auflisten und Löschen von Konfigurationen für Anomaliewarnungen <br> - Verwalten von Hooks  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Erfassung von Daten Ihrer Datenquelle durch Metrics Advisor. Ein `DataFeed`-Element enthält Zeilen mit:** <br> - Zeitstempeln <br> - Null oder mehr Dimensionen <br> - Einem oder mehreren Measures  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | Eine Metrik (`DataFeedMetric`) ist ein quantifizierbares Measure, das zum Überwachen und Bewerten des Status eines bestimmten Geschäftsprozesses verwendet wird. Hierbei kann es sich um eine Kombination aus mehreren Zeitreihen handeln, die in Dimensionen unterteilt sind. Eine Metrik vom Typ Webintegrität kann beispielsweise Dimensionen für die Benutzeranzahl und den US-amerikanischen Markt enthalten. |

## <a name="code-examples"></a>Codebeispiele

Diese Codeausschnitte veranschaulichen, wie die folgenden Vorgänge mit der Metrics Advisor-Clientbibliothek für Python ausgeführt werden:

* [Authentifizieren des Clients](#authenticate-the-client)
* [Hinzufügen eines Datenfeeds](#add-a-data-feed)
* [Überprüfen des Erfassungsstatus](#check-the-ingestion-status)
* [Einrichten der Konfiguration für die Erkennung und Warnungen](#configure-anomaly-detection)
* [Erstellen einer Warnungskonfiguration](#create-an-alert-configuration)
* [Abfragen von Anomalieerkennungsergebnissen](#query-the-alert)

## <a name="authenticate-the-client"></a>Authentifizieren des Clients

Der Client besteht in diesem Beispiel aus einem `MetricsAdvisorAdministrationClient`-Objekt, das Ihren Endpunkt nutzt, und einem `MetricsAdvisorKeyCredential`-Objekt, das Ihre Schlüssel enthält. Sie müssen dieses Codebeispiel nicht kopieren. Mit den Methoden, die Sie zu einem späteren Zeitpunkt erstellen, wird der Client instanziiert. Der alternative Client hat den Namen `MetricsAdvisorClient`. Weitere Informationen zu diesem Client finden Sie in der [Referenzdokumentation](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Hinzufügen eines Datenfeeds

Erstellen Sie in einer neuen Methode Importanweisungen, z. B. wie im Beispiel unten. Ersetzen Sie `sql_server_connection_string` durch Ihre eigene SQL Server-Verbindungszeichenfolge und `query` durch eine Abfrage, mit der Ihre Daten für einen bestimmten Zeitstempel zurückgegeben werden. Darüber hinaus müssen Sie die Werte `DataFeedmetric` und `DataFeedDimension` basierend auf Ihren benutzerdefinierten Daten anpassen.

> [!IMPORTANT]
> Die Abfrage sollte zu jedem Zeitstempel höchstens einen Datensatz für jede Dimensionskombination zurückgeben. Alle von der Abfrage zurückgegebenen Datensätze müssen dieselben Zeitstempel aufweisen. Metrics Advisor führt diese Abfrage für jeden Zeitstempel aus, um die Daten zu erfassen. Weitere Informationen und Beispiele finden Sie im Abschnitt mit [häufig gestellten Fragen zu Abfragen](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

Erstellen Sie einen Client mit Ihren Schlüsseln und Ihrem Endpunkt, und verwenden Sie `client.create_data_feed()` zum Konfigurieren von Name, Quelle, Granularität und Schema. Außerdem können Sie den Erfassungszeitpunkt, die Rollupeinstellungen und mehr festlegen.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Überprüfen des Erfassungsstatus

Erstellen Sie in einer neuen Methode eine Importanweisung, z. B. wie im Beispiel unten. Ersetzen Sie `data_feed_id` durch die ID für den von Ihnen erstellten Datenfeed. Erstellen Sie einen Client mit Ihren Schlüsseln und Ihrem Endpunkt, und verwenden Sie `client.list_data_feed_ingestion_status()` zum Abrufen des Erfassungsstatus. Drucken Sie die Details aus, z. B. die letzten und erfolgreichen Zeitstempel.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Konfigurieren der Anomalieerkennung

Erstellen Sie in einer neuen Methode Importanweisungen, z. B. wie im Beispiel unten. Ersetzen Sie `metric_id` durch die ID für die Metrik, die Sie konfigurieren möchten. Erstellen Sie einen Client mit Ihren Schlüsseln und Ihrem Endpunkt, und verwenden Sie `client.create_detection_configuration` zum Erstellen einer neuen Konfiguration für die Erkennung. Mit den Schwellenwertbedingungen werden die Parameter für die Anomalieerkennung angegeben.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Erstellen eines Hooks

Erstellen Sie in einer neuen Methode Importanweisungen, z. B. wie im Beispiel unten. Erstellen Sie einen Client mit Ihren Schlüsseln und Ihrem Endpunkt, und verwenden Sie `client.create_hook()` zum Erstellen eines Hooks. Geben Sie eine Beschreibung, eine Liste mit E-Mail-Adressen, an die die Warnung gesendet werden soll, und einen externen Link für den Erhalt der Warnung ein.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Erstellen einer Warnungskonfiguration

Erstellen Sie in einer neuen Methode Importanweisungen, z. B. wie im Beispiel unten. Ersetzen Sie `detection_configuration_id` durch die ID für Ihre Konfiguration der Anomalieerkennung und `hook_id` durch den Hook, den Sie weiter oben erstellt haben. Erstellen Sie einen Client mit Ihren Schlüsseln und Ihrem Endpunkt, und verwenden Sie `client.create_alert_configuration()` zum Erstellen einer neuen Konfiguration für die Warnungen. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Abfragen der Warnung

Erstellen Sie in einer neuen Methode eine Importanweisung, z. B. wie im Beispiel unten. Ersetzen Sie `alert_id` durch die ID für Ihre Warnung und `alert_config_id` durch die ID für die Warnungskonfiguration. Erstellen Sie einen Client mit Ihren Schlüsseln und Ihrem Endpunkt, und verwenden Sie `client.list_anomalies` zum Auflisten der Anomalien für eine Warnung. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung mit dem Befehl `python` für die Schnellstartdatei aus.

```console
python quickstart-file.py
```