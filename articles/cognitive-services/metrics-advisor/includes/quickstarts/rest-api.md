---
title: 'Metrics Advisor-REST-API: Schnellstart'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/23/2020
ms.author: mbullwin
ms.openlocfilehash: 42ea166119d3cc405b3d73e184c44dbfd6708a97
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96231479"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Wenn Sie über ein Azure-Abonnement verfügen, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Erstellen einer Metrics Advisor-Ressource"  target="_blank">erstellen Sie eine Metrics Advisor-Ressource <span class="docon docon-navigate-external x-hidden-focus"></span></a> im Azure-Portal, um Ihre Metrics Advisor-Instanz bereitzustellen.  
* Aktuelle Version von [cURL](https://curl.haxx.se/). In diesem Artikel werden mehrere Befehlszeilenoptionen verwendet. Diese sind in der [cURL-Dokumentation](https://curl.haxx.se/docs/manpage.html) angegeben.
    * In den folgenden Bash-Beispielen wird das Zeilenfortsetzungszeichen `\` verwendet. Verwenden Sie dieses Zeichen, falls in Ihrer Konsole oder Ihrem Terminal ein anderes Zeilenfortsetzungszeichen genutzt wird.

> [!TIP]
> * Ein Python-Beispiel, das die REST-API aufruft, finden Sie [auf GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/MetricsAdvisor).
> * Es kann zwischen 10 und 30 Minuten dauern, bis Ihre Metrics Advisor-Ressource Ihnen eine Dienstinstanz zur Verwendung bereitstellt. Klicken Sie nach erfolgreicher Bereitstellung auf **Zu Ressource wechseln**. Nach Abschluss der Bereitstellung können Sie Ihre Metrics Advisor-Instanz sowohl über das Webportal als auch per REST-API verwenden. 
> * Die URL für die REST-API finden Sie im Azure-Portal im Abschnitt **Übersicht** Ihrer Ressource. Sie sieht wie folgt aus:
>    * `https://<instance-name>.cognitiveservices.azure.com/`

Sie benötigen zwei Schlüssel, um mit der Verwendung der REST-API zu beginnen:

* Den Schlüssel für Ihre Metrics Advisor-Ressource. Sie finden ihn im Azure-Portal im Abschnitt **Schlüssel und Endpunkt** für Ihre Ressource.
    * Im weiteren Verlauf ersetzen Sie `Ocp-Apim-Subscription-Key` in den Beispielen durch diesen Schlüssel. 
* Der API-Schlüssel für Ihre Metrics Advisor-Instanz. Diesen finden Sie im Webportal für den Metrics Advisor im linken Navigationsmenü unter **API-Schlüssel**.
    * Im weiteren Verlauf ersetzen Sie `x-api-key` in den Beispielen durch diesen Schlüssel.

## <a name="add-a-data-feed-from-a-sample-or-data-source"></a>Hinzufügen eines Datenfeeds aus einem Beispiel oder aus einer Datenquelle

Fügen Sie einen Datenfeed hinzu, um mit der Überwachung Ihrer Zeitreihendaten zu beginnen. Wenn Sie einen Datenfeed hinzufügen möchten, müssen Sie ein dem Datenquellentyp entsprechendes Datenschema sowie entsprechende Parameter angeben. Speichern Sie den folgenden JSON-Anforderungstext in einer Datei mit dem Namen *body.json*, und führen Sie den cURL-Befehl aus.

```json
{
        "dataSourceType": "SqlServer",
        "dataFeedName": "test_data_feed_00000001",
        "dataFeedDescription": "",
        "dataSourceParameter": {
            "connectionString": "Server=ad-sample.database.windows.net,1433;Initial Catalog=ad-sample;Persist Security Info=False;User ID=adreadonly;Password=Readonly@2020;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
            "query": "select * from adsample3 where Timestamp = @StartTime"
        },
        "granularityName": "Daily",
        "granularityAmount": 0,
        "metrics": [
            {
                "metricName": "revenue",
                "metricDisplayName": "revenue",
                "metricDescription": ""
            },
            {
                "metricName": "cost",
                "metricDisplayName": "cost",
                "metricDescription": ""
            }
        ],
        "dimension": [
            {
                "dimensionName": "city",
                "dimensionDisplayName": "city"
            },
            {
                "dimensionName": "category",
                "dimensionDisplayName": "category"
            }
        ],
        "timestampColumn": "timestamp",
        "dataStartFrom": "2020-06-01T00:00:00.000Z",
        "startOffsetInSeconds": 0,
        "maxConcurrency": -1,
        "minRetryIntervalInSeconds": -1,
        "stopRetryAfterInSeconds": -1,
        "needRollup": "NoRollup",
        "fillMissingPointType": "SmartFilling",
        "fillMissingPointValue": 0,
        "viewMode": "Private",
        "admins": [
            "xxx@microsoft.com"
        ],
        "viewers": [
        ],
        "actionLinkTemplate": ""
}
```

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie in diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie Ihre eigenen JSON-Werte ein.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/datafeeds \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Beispielantwort

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/datafeeds/b5921405-8001-42b2-8746-004ddeeb780d
x-envoy-upstream-service-time: 564
apim-request-id: 4e4fe70b-d663-4fb7-a804-b9dc14ba02a3
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Thu, 03 Sep 2020 18:29:27 GMT
```
In der obigen Antwort ist der **Adressheader** die URL des von Ihnen erstellten Datenfeeds und enthält **dataFeedID**. 

Über die obige URL können Sie ausführliche Informationen zu dem im vorherigen Schritt erstellten Datenfeed abfragen. (In den folgenden Schritten wird **metricID** in Datenfeedinformationen verwendet.)

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/datafeeds/REPLACE-WITH-YOUR-DATA-FEED-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Beispielantwort

```json
{
   "dataFeedId":"90919c03-be13-4efa-86e5-aa9dc72764ce",
   "dataFeedName":"test_data_feed_00000007",
   "metrics":[
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "metricName":"cost",
         "metricDisplayName":"cost",
         "metricDescription":""
      },
      {
         "metricId":"82bbc63d-3739-4d57-b190-accb69721b6a",
         "metricName":"revenue",
         "metricDisplayName":"revenue",
         "metricDescription":""
      }
   ],
   "dimension":[
      {
         "dimensionName":"category",
         "dimensionDisplayName":"category"
      },
      {
         "dimensionName":"city",
         "dimensionDisplayName":"city"
      }
   ],
   "dataStartFrom":"2020-06-01T00:00:00Z",
   "dataSourceType":"SqlServer",
   "timestampColumn":"timestamp",
   "startOffsetInSeconds":0,
   "maxQueryPerMinute":30.0,
   "granularityName":"Daily",
   "granularityAmount":null,
   "allUpIdentification":null,
   "needRollup":"NoRollup",
   "fillMissingPointType":"SmartFilling",
   "fillMissingPointValue":0.0,
   "rollUpMethod":"None",
   "rollUpColumns":[
      
   ],
   "dataFeedDescription":"",
   "stopRetryAfterInSeconds":-1,
   "minRetryIntervalInSeconds":-1,
   "maxConcurrency":-1,
   "viewMode":"Private",
   "admins":[
      "xyz@microsoft.com"
   ],
   "viewers":[
      
   ],
   "creator":"xyz@microsoft.com",
   "status":"Active",
   "createdTime":"2020-09-08T08:39:28Z",
   "isAdmin":true,
   "actionLinkTemplate":"",
   "dataSourceParameter":{
      "connectionString":"Server=ad-sample.database.windows.net,1433;Initial Catalog=ad-sample;Persist Security Info=False;User ID=adreadonly;Password=Readonly@2020;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
      "query":"select * from adsample3 where Timestamp = @StartTime"
   }
}
```



## <a name="check-ingestion-status"></a>Überprüfen des Erfassungsstatus

Wenn Sie nach dem Hinzufügen eines Datenfeeds den Fortschritt eines Erfassungsauftrags ermitteln möchten, können Sie den Status des Erfassungsauftrags überprüfen. Speichern Sie den folgenden JSON-Anforderungstext in einer Datei mit dem Namen *body.json*, und führen Sie den cURL-Befehl aus.

```json
{
  "startTime": "2020-01-01T00:00:00.0000000+00:00",
  "endTime": "2020-01-04T00:00:00.0000000+00:00"
}
```

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.


```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/datafeeds/REPLACE-WITH-YOUR-DATA-FEED-ID/ingestionStatus/query \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```


#### <a name="example-response"></a>Beispielantwort

```json
{
  "@nextLink": "https://demo.example.com/datafeeds/01234567-8901-2345-6789-012345678901/ingestionStatus/query?$skip=3&$top=1",
  "value": [
    {
      "timestamp": "2020-09-03T00:00:00.0000000+00:00",
      "status": "Running",
      "message": ""
    },
    {
      "timestamp": "2020-09-02T00:00:00.0000000+00:00",
      "status": "Succeeded",
      "message": ""
    },
    {
      "timestamp": "2020-09-01T00:00:00.0000000+00:00",
      "status": "Failed",
      "message": "No valid record pulled from source for current timestamp 2020-01-01T00:00:00Z"
    }
  ]
}
```

##  <a name="configure-anomaly-detection-configuration"></a>Konfigurieren der Anomalieerkennung

Auf jede Metrik wird automatisch eine Standardkonfiguration angewendet. Sie können jedoch die für Ihre Daten verwendeten Erkennungsmodi optimieren. Speichern Sie den folgenden JSON-Anforderungstext in einer Datei mit dem Namen *body.json*, und führen Sie den cURL-Befehl aus.

```json
{
        "name": "test_detection_config0000000001",
        "description": "string",
        "metricId": "8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
        "wholeMetricConfiguration": {
            "smartDetectionCondition": {
                "sensitivity": 100,
                "anomalyDetectorDirection": "Both",
                "suppressCondition": {
                    "minNumber": 1,
                    "minRatio": 1
                }
            }
        },
        "dimensionGroupOverrideConfigurations": [
        ],
        "seriesOverrideConfigurations": [
        ]
}
```

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/enrichment/anomalyDetection/configurations \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Beispielantwort

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/enrichment/anomalyDetection/configurations/6a977d61-f0f5-488a-a162-2feb4643ae09
x-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
x-envoy-upstream-service-time: 253
apim-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 09:50:38 GMT
```

Der obige **Adressheader** enthält die URL der neuen erstellten Ressource (Erkennungskonfiguration). 

Mit der obigen URL im **Adressheader** können Sie die von Ihnen erstellte Erkennungskonfiguration abfragen. (In den folgenden Schritten wird **anomalyDetectionConfigurationId** im Antwortinhalt verwendet.)

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/enrichment/anomalyDetection/configurations/REPLACE-WITH-YOUR-DETECTION-CONFIGURATION-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Beispielantwort

```json
{
   "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
   "name":"test_detection_config0000000001",
   "description":"string",
   "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
   "wholeMetricConfiguration":{
      "smartDetectionCondition":{
         "sensitivity":100.0,
         "anomalyDetectorDirection":"Both",
         "suppressCondition":{
            "minNumber":1,
            "minRatio":1.0
         }
      }
   },
   "dimensionGroupOverrideConfigurations":[
      
   ],
   "seriesOverrideConfigurations":[
      
   ]
}
```



### <a name="configure-alert-configuration"></a>Konfigurieren von Warnungen

Bevor Sie eine Warnung konfigurieren, müssen Sie einen Hook für die Warnungsbenachrichtigung erstellen. Es gibt zwei Möglichkeiten, eine Benachrichtigung zu erhalten, wenn eine Warnung ausgelöst wird: per Webhook oder per E-Mail. Bei der Hookerstellung kann eine der beiden Optionen in der Hookkonfiguration als Hooktyp angegeben werden.

Speichern Sie den folgenden JSON-Anforderungstext in einer Datei mit dem Namen *body.json*, und führen Sie den cURL-Befehl aus.

```json
{
        "hookType": "Webhook",
        "hookName": "test_web_hook000001",
        "description": "",
        "externalLink": "",
        "hookParameter": {
            "endpoint": "https://www.xxx.com/aaa",
            "username": "",
            "password": ""
        }
}
```

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/hooks \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Beispielantwort

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/hooks/34d677bd-0875-4760-8bf6-24d48abde7c3
x-request-id: 7b6cc1a6-02cb-405b-bee3-174fdae0a7d2
x-envoy-upstream-service-time: 1640
apim-request-id: 7b6cc1a6-02cb-405b-bee3-174fdae0a7d2
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 10:37:59 GMT
```

Mit der obigen URL im **Adressheader** können Sie den von Ihnen erstellten Webhook abfragen.

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/hooks/REPLACE-WITH-YOUR-HOOK-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Beispielantwort

```json
{
   "hookId":"34d677bd-0875-4760-8bf6-24d48abde7c3",
   "hookName":"test_web_hook000001",
   "hookType":"Webhook",
   "externalLink":"",
   "description":"",
   "admins":[
      "bowgong@microsoft.com"
   ],
   "hookParameter":{
      "endpoint":"https://www.xxx.com/aaa",
      "username":"",
      "password":"",
      "headers":{
         

      },
      "certificateKey":"",
      "certificatePassword":""

   }
}
```

Im Zuge der Warnungskonfiguration können Sie die Erkennungsbedingung angeben, durch die die Warnung ausgelöst werden kann. Speichern Sie den folgenden JSON-Anforderungstext in einer Datei mit dem Namen *body.json*, und führen Sie den cURL-Befehl aus.

```json
{
        "name": "test_alert_config00000001",
        "description": "",
        "crossMetricsOperator": "AND",
        "hookIds": [
           "34d677bd-0875-4760-8bf6-24d48abde7c3" 
        ],
        "metricAlertingConfigurations": [
            {
                "anomalyDetectionConfigurationId": "6a977d61-f0f5-488a-a162-2feb4643ae09",
                "anomalyScopeType": "All",
                "severityFilter": {
                    "minAlertSeverity": "Low",
                    "maxAlertSeverity": "High"
                },
                "snoozeFilter": {
                    "autoSnooze": 0,
                    "snoozeScope": "Metric",
                    "onlyForSuccessive": true
                },
            }
        ]
}
```

Der cURL-Befehl wird über eine Bash-Shell ausgeführt. Fügen Sie für diesen Befehl Ihren eigenen Ressourcennamen und Ressourcenschlüssel sowie die JSON-Werte und die JSON-Größe ein.

```bash
curl -i https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY" \
-H "Content-Type:application/json" \
-d @body.json
```

#### <a name="example-response"></a>Beispielantwort

```
HTTP/1.1 201 Created
Content-Length: 0
Location: https://gualala-beta-0617.cognitiveservices.azure.com/metricsadvisor/v1.0/alert/anomaly/configurations/40004c91-6996-47c0-b8c8-fd20a8f4f0ab
x-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
x-envoy-upstream-service-time: 253
apim-request-id: 17752fcc-9085-46d5-ad37-c4e9e9ba6a5a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Date: Tue, 08 Sep 2020 09:50:38 GMT
```

Der obige **Adressheader** enthält die URL der neuen erstellten Ressource (Erkennungskonfiguration). 

Mit der obigen URL im **Adressheader** können Sie die von Ihnen erstellte Warnungskonfiguration abfragen. (In den folgenden Schritten wird **anomalyAlertingConfigurationId** in der Warnungskonfiguration verwendet.)

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Beispielantwort

```json
{
   "anomalyAlertingConfigurationId":"40004c91-6996-47c0-b8c8-fd20a8f4f0ab",
   "name":"test_alert_config00000001",
   "description":"",
   "hookIds":[
      "34d677bd-0875-4760-8bf6-24d48abde7c3"
   ],
   "metricAlertingConfigurations":[
      {
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "anomalyScopeType":"All",
         "negationOperation":false,
         "severityFilter":{
            "minAlertSeverity":"Low",
            "maxAlertSeverity":"High"
         },
         "snoozeFilter":{
            "autoSnooze":0,
            "snoozeScope":"Metric",
            "onlyForSuccessive":true
         }
      }
   ]
}
```

### <a name="query-anomaly-detection-results"></a>Abfragen von Anomalieerkennungsergebnissen

Erkennungsergebnisse können auf unterschiedliche Weise abgerufen werden. So können Sie das Erkennungsergebnis beispielsweise in regelmäßigen Abständen aktiv unter Verwendung der von Ihnen erstellten Erkennungskonfiguration abfragen oder sich per Warnung benachrichtigen lassen und diese Warnung nutzen, um entsprechende Anomalien abzufragen.

Im folgenden Beispiel wird gezeigt, wie Sie eine Warnung abfragen und diese Warnung verwenden, um Anomalien im Zusammenhang mit dieser Warnung abzufragen:

#### <a name="query-alert"></a>Abfragen einer Warnung

Zum Abfragen der Warnung können Sie die im obigen Schritt erstellte Warnungskonfiguration verwenden.

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID/alerts/query \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Beispielantwort

```json
{
   "value":[
      {
         "alertId":"17465dcc000",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.532Z",
         "modifiedTime":"2020-09-08T19:12:46.588Z"
      }
   ],
   "@nextLink":null
}
```

In der obigen Antwort haben wir eine Warnung erhalten. Mithilfe dieser Warnungs-ID (**alertID**) können wir alle zugehörigen Anomalien abfragen, durch die diese Warnung ausgelöst wurde.

(Eine weitere Möglichkeit, eine Warnung zu erhalten, besteht darin, einen Webhook zu konfigurieren und die Warnung passiv zu erhalten, sobald er gefunden wird.)

#### <a name="query-anomalies-using-alertid"></a>Abfragen von Anomalien mithilfe der Warnungs-ID

```bash
curl https://REPLACE-WITH-YOUR-ENDPOINT/metricsadvisor/v1.0/alert/anomaly/configurations/REPLACE-WITH-YOUR-ANOMALY-ALERTING-CONFIGURATION-ID/alerts/REPLACE-WITH-YOUR-ALERTID/anomalies \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "x-api-key: REPLACE-WITH-YOUR-API-KEY"
```

#### <a name="example-response"></a>Beispielantwort

```json
{
   "value":[
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Caucasian Fir"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Animals by number of neurons",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Animals by number of neurons",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Chickadee",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Amphibian",
            "category":"Common Lime"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      },
      {
         "metricId":"8d03e541-a56d-4c28-8d9c-09ce91c6d95f",
         "anomalyDetectionConfigurationId":"6a977d61-f0f5-488a-a162-2feb4643ae09",
         "timestamp":"2020-09-07T00:00:00Z",
         "createdTime":"2020-09-08T19:12:46.566Z",
         "modifiedTime":"2020-09-08T19:12:46.566Z",
         "dimension":{
            "city":"Chickadee",
            "category":"Crack willow"
         },
         "property":{
            "anomalySeverity":"High",
            "anomalyStatus":"Active"
         }
      }
   ],
   "@nextLink":null
}
```
