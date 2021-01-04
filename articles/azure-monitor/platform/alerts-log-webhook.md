---
title: Webhookaktionen für Protokollwarnungen in Azure-Warnungen
description: Beschreibt das Konfigurieren eines Protokollwarnungspushes mit Webhookaktion und verfügbaren Anpassungen.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 97c3ead4dbdf0608c6b7cd1be6a9edf242ecd2b5
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510762"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhookaktionen für Protokollwarnungsregeln

[Protokollwarnungen](alerts-log.md) unterstützen das [Konfigurieren von Webhookaktionsgruppen](action-groups.md#webhook). In diesem Artikel wird beschrieben, welche Eigenschaften verfügbar sind und wie Sie einen benutzerdefinierten JSON-Webhook konfigurieren.

> [!NOTE]
> Der benutzerdefinierte JSON-Webhook wird derzeit in der API-Version `2020-05-01-preview` nicht unterstützt.

> [!NOTE]
> Sie sollten das [allgemeine Warnungsschema](alerts-common-schema.md) für Ihre Webhookintegrationen verwenden. Das allgemeine Warnungsschema bietet den Vorteil einer einzelnen, erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor. Für Protokollwarnungsregeln, für die eine benutzerdefinierte JSON-Nutzlast definiert ist, wird das Nutzlastschema durch die Aktivierung des allgemeinen Schemas auf das [hier](alerts-common-schema-definitions.md#log-alerts) beschriebene zurückgesetzt. Für Warnungen mit aktiviertem allgemeinem Schema gilt für die Größe ein oberer Grenzwert von 256 KB pro Warnung. Größere Warnungen enthalten keine Suchergebnisse. Wenn die Suchergebnisse nicht enthalten sind, sollten Sie unter Verwendung von `LinkToFilteredSearchResultsAPI` oder `LinkToSearchResultsAPI` mit der Log Analytics-API auf die Suchergebnisse zugreifen.

## <a name="webhook-payload-properties"></a>Webhook-Nutzlasteigenschaften

Mit Webhookaktionen können Sie eine einzelne HTTP POST-Anforderung aufrufen. Der aufgerufene Dienst sollte Webhooks unterstützen und wissen, wie er die empfangene Nutzlast verwenden soll.

Standardmäßige Webhookaktionseigenschaften und deren benutzerdefinierte JSON-Parameternamen:

| Parameter | Variable | BESCHREIBUNG |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Der Name der Warnungsregel. |
| *Severity* |#severity |Festgelegter Schweregrad für die erste ausgelöste Protokollwarnung |
| *AlertThresholdOperator* |#thresholdoperator |Schwellenwertoperator für die Warnungsregel |
| *AlertThresholdValue* |#thresholdvalue |Wert des Schwellenwerts für die Warnungsregel |
| *LinkToSearchResults* |#linktosearchresults |Link zum Analyseportal, das die Datensätze aus der Abfrage zurückgibt, mit der die Warnung erstellt wurde. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Link zur Analytics-API, die die Datensätze aus der Abfrage zurückgibt, mit der die Warnung erstellt wurde |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Link zum Analyticsportal, das die Datensätze aus der nach Dimensionswertkombinationen gefilterten Abfrage zurückgibt, mit der die Warnung erstellt wurde |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Link zur Analytics-API, die die Datensätze aus der nach Dimensionswertkombinationen gefilterten Abfrage zurückgibt, mit der die Warnung erstellt wurde |
| *ResultCount* |#searchresultcount |Anzahl von Datensätzen in den Suchergebnissen |
| *Endzeit für das Suchintervall* |#searchintervalendtimeutc |Abschlusszeit der Abfrage im UTC-Format, mit dem Format mm/tt/jjjj HH:mm:ss AM/PM. |
| *Suchintervall* |#searchinterval |Zeitfenster für die Warnungsregel im Format HH:mm:ss. |
| *Startzeit für das Suchintervall* |#searchintervalstarttimeutc |Startzeit der Abfrage im UTC-Format, mit dem Format mm/tt/jjjj HH:mm:ss AM/PM. 
| *SearchQuery* |#searchquery |Von der Warnungsregel verwendete Protokollsuchabfrage |
| *SearchResults* |"IncludeSearchResults": true|Hierbei handelt es sich um die von der Abfrage als JSON-Tabelle zurückgegebenen Datensätze, beschränkt auf die ersten 1.000 Datensätze. "IncludeSearchResults": true wird in einer benutzerdefinierten JSON-Webhookdefinition als Eigenschaft der obersten Ebene hinzugefügt. |
| *Dimensionen* |"IncludeDimensions": true|Hierbei handelt es sich um Dimensionswertkombinationen, die diese Warnung ausgelöst haben, als JSON-Abschnitt. "IncludeDimensions": true wird in einer benutzerdefinierten JSON-Webhookdefinition als Eigenschaft der obersten Ebene hinzugefügt. |
| *Warnungstyp*| #alerttype | Der Typ der konfigurierten Protokollwarnungsregel als [metrische Maßeinheit oder Anzahl von Ergebnissen](alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID Ihres Log Analytics-Arbeitsbereichs. |
| *Anwendungs-ID* |#applicationid |ID Ihrer Application Insights-App. |
| *Abonnement-ID* |#subscriptionid |ID des von Ihnen verwendeten Azure-Abonnements. |

## <a name="custom-webhook-payload-definition"></a>Nutzlastdefinition für benutzerdefinierten Webhook

Mit **Benutzerdefinierte JSON-Nutzdaten für Webhook einschließen** können Sie eine benutzerdefinierte JSON-Nutzlast mit den oben genannten Parametern erhalten. Sie können auch zusätzliche Eigenschaften generieren.
Sie können beispielsweise die folgende benutzerdefinierte Nutzlast angeben, die einen einzelnen Parameter wie *text* enthält. Der Dienst, der von diesem Webhook aufgerufen wird, erwartet diesen Parameter:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Diese Beispielnutzlast wird ähnlich wie hier dargestellt aufgelöst, wenn sie an den Webhook gesendet wird:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Variablen in einem benutzerdefinierten Webhook müssen in einer JSON-Struktur angegeben werden. Die Ausgabe zu dem Verweis „#searchresultcount“ im obigen Webhookbeispiel hängt z. B. von den Warnungsergebnissen ab.

Fügen Sie zum Einschließen von Suchergebnissen **IncludeSearchResults** als Eigenschaft der obersten Ebene im benutzerdefinierten JSON-Code hinzu. Die Suchergebnisse werden als JSON-Struktur eingeschlossen, sodass nicht auf Ergebnisse in benutzerdefinierten Feldern verwiesen werden kann. 

> [!NOTE]
> Die Schaltfläche **Webhook anzeigen** neben der Option **Benutzerdefinierte JSON-Nutzlast für Webhook einschließen** zeigt eine Vorschau der bereitgestellten Informationen an. Es sind keine tatsächlichen Daten enthalten, aber sie ist repräsentativ für das verwendete JSON-Schema. 

## <a name="sample-payloads"></a>Beispielnutzlasten
Dieser Abschnitt zeigt Beispielnutzlasten für Webhooks für Protokollwarnungen. Die Beispielnutzlasten enthalten Beispiele dafür, wenn die Nutzlast Standard ist und wenn sie benutzerdefiniert ist.

### <a name="log-alert-for-log-analytics"></a>Protokollwarnung für Log Analytics
Die folgende Beispielnutzlast ist für eine Standardwebhookaktion gedacht, die für Warnungen verwendet wird, die auf Log Analytics basieren:

> [!NOTE]
> Der Wert des Felds „Severity“ ändert sich, wenn Sie [von der älteren Log Analytics-Warnungs-API](api-alerts.md) zur [aktuellen scheduledQueryRules-API gewechselt haben](alerts-log-api-switch.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Protokollwarnung für Application Insights
Die folgende Beispielnutzlast ist für einen Standardwebhook gedacht, wenn er für Protokollwarnungen verwendet wird, die auf Application Insights-Ressourcen basieren:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Protokollwarnung für andere Ressourcenprotokolle (von API-Version `2020-05-01-preview`)

> [!NOTE]
> Für die API-Version `2020-05-01-preview` und ressourcenbezogene Protokollwarnungen fallen aktuell keine zusätzlichen Gebühren an.  Die Preise für Previewfunktionen werden später bekannt gegeben, und vor Abrechnungsbeginn erhalten Sie eine entsprechende Benachrichtigung. Falls Sie sich dafür entscheiden, die neue API-Version und ressourcenbezogene Protokollwarnungen über den Benachrichtigungszeitraum hinaus zu verwenden, wird Ihnen der entsprechende Tarif in Rechnung gestellt.

Die folgende Beispielnutzlast ist für einen Standardwebhook gedacht, wenn er für Protokollwarnungen verwendet wird, die auf anderen Ressourcenprotokollen (mit Ausnahme von Arbeitsbereichen und Application Insights) basieren:

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Protokollwarnung mit einer benutzerdefinierten JSON-Nutzlast
Sie können beispielsweise diese Konfiguration verwenden, um eine benutzerdefinierte Nutzlast zu erstellen, die nur den Warnungsnamen und die Suchergebnisse enthält: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Die folgende Beispielnutzlast ist für eine benutzerdefinierte Webhookaktion für eine beliebige Protokollwarnung gedacht:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Protokollwarnungen in Azure-Warnungen](alerts-unified-log.md).
- Grundlegendes zum [Verwalten von Protokollwarnungen in Azure](alerts-log.md).
- Erstellen und Verwalten von [Aktionsgruppen in Azure](action-groups.md).
- Erfahren Sie mehr über [Application Insights](../log-query/log-query-overview.md).
- Weitere Informationen zum [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md). 
