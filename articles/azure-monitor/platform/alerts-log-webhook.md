---
title: Webhookaktionen für Protokollwarnungen in Azure-Warnungen
description: In diesem Artikel wird beschrieben, wie eine Protokollwarnungsregel mit einem Log Analytics-Workspace oder mit Application Insights per Pushvorgang Daten als HTTP-Webhook überträgt, und es werden Details zu den verschiedenen möglichen Anpassungen erläutert.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 809c98c1e2e51ae51d7fe03f2165a5d9eecb05cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681815"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhookaktionen für Protokollwarnungsregeln
Wenn eine [Protokollwarnung in Azure erstellt](alerts-log.md) wird, haben Sie die Möglichkeit [mithilfe von Aktionsgruppen die Ausführung von Aktionen zu konfigurieren](action-groups.md).  Dieser Artikel beschreibt die verschiedenen verfügbaren Webhookaktionen und Details zur Konfiguration des benutzerdefinierten JSON-basierten Webhooks.

> [!NOTE]
> Für Ihre Webhook-Integrationen können Sie auch das [allgemeine Warnungsschema](https://aka.ms/commonAlertSchemaDocs) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet. [Hier finden Sie Informationen zu den Definitionen des allgemeinen Warnungsschemas](https://aka.ms/commonAlertSchemaDefinitions).

## <a name="webhook-actions"></a>Webhookaktionen

Mit Webhookaktionen können Sie einen externen Prozess über eine HTTP POST-Anforderung aufrufen.  Der aufgerufene Dienst sollte Webhooks unterstützen, und es sollte festgelegt werden, wie empfangene Nutzlasten verwendet werden.    

Für Webhookaktionen sind die in der folgenden Tabelle aufgeführten Eigenschaften erforderlich:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| Webhook-URL |Die URL des Webhooks. |
| Benutzerdefinierte JSON-Nutzlast |Benutzerdefinierte Nutzlast zum Senden mit dem Webhook, wenn diese Option während der Warnungserstellung ausgewählt wird. Unter [Protokollwarnungen verwalten](alerts-log.md) verfügbare Details |

> [!NOTE]
> Die Schaltfläche „Webhook anzeigen“ neben der Option *Benutzerdefinierte JSON-Nutzlast für Webhook einschließen* für die Protokollwarnung zeigt eine Beispielnutzlast für Webhook für die bereitgestellte Anpassung an. Es sind keine tatsächlichen Daten enthalten, und der Vorgang ist für ein JSON-Schema für Protokollwarnungen repräsentativ. 

Webhooks enthalten eine URL und eine Nutzlast im JSON-Format, wobei es sich um die an den externen Dienst gesendeten Daten handelt.  Die Nutzlast enthält standardmäßig die Werte in der folgenden Tabelle:  Sie können diese Nutzlast auch durch eine benutzerdefinierte eigene Nutzlast ersetzen.  In diesem Fall können Sie die Variablen in der Tabelle für die einzelnen Parameter verwenden, um deren Wert in die benutzerdefinierte Nutzlast einzubinden.


| Parameter | Variable | BESCHREIBUNG |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Der Name der Warnungsregel. |
| Severity |#severity |Festgelegter Schweregrad für die erste ausgelöste Protokollwarnung |
| AlertThresholdOperator |#thresholdoperator |Schwellenwertoperator für die Warnungsregel  *Größer als* oder *Kleiner als* |
| AlertThresholdValue |#thresholdvalue |Wert des Schwellenwerts für die Warnungsregel |
| LinkToSearchResults |#linktosearchresults |Link zum Analyseportal, das die Datensätze aus der Abfrage zurückgibt, mit der die Warnung erstellt wurde |
| ResultCount |#searchresultcount |Anzahl von Datensätzen in den Suchergebnissen |
| Endzeit für das Suchintervall |#searchintervalendtimeutc |Abschlusszeit der Abfrage im UTC-Format – mm/tt/jjjj HH:mm:ss AM/PM. |
| Suchintervall |#searchinterval |Zeitfenster für die Warnungsregel im Format – HH:mm:ss. |
| Startzeit für das Suchintervall |#searchintervalstarttimeutc |Startzeit der Abfrage im UTC-Format – mm/tt/jjjj HH:mm:ss AM/PM. 
| SearchQuery |#searchquery |Von der Warnungsregel verwendete Protokollsuchabfrage |
| SearchResults |"IncludeSearchResults": true|Von der Abfrage als JSON-Tabelle zurückgegebene Datensätze, die auf die ersten 1.000 Datensätze beschränkt sind; wenn "IncludeSearchResults": true in der benutzerdefinierten JSON-Webhookdefinition als Eigenschaft der obersten Ebene hinzugefügt wird |
| WorkspaceID |#workspaceid |ID Ihres Log Analytics-Arbeitsbereichs. |
| Anwendungs-ID |#applicationid |ID Ihrer Application Insight-App |
| Abonnement-ID |#subscriptionid |ID Ihres Azure-Abonnements, das für Application Insights verwendet wird 

> [!NOTE]
> „LinkToSearchResults“ übergibt Parameter wie „SearchQuery“, „Startzeit für das Suchintervall“ und „Endzeit für das Suchintervall“ in der URL an das Azure-Portal, damit sie im Analytics-Abschnitt angezeigt werden können. Für das Azure-Portal gilt eine URI-Größenbeschränkung von etwa 2.000 Zeichen. In Warnungen bereitgestellte Links werden *nicht* geöffnet, wenn Parameterwerte diese Beschränkung überschreiten. Benutzer können Details manuell eingeben, um die Ergebnisse im Analytics-Portal anzuzeigen. Sie können auch die [Application Insights Analytics-REST-API](https://dev.applicationinsights.io/documentation/Using-the-API) oder die [Log Analytics-REST-API](/rest/api/loganalytics/) zum programmgesteuerten Abrufen der Ergebnisse verwenden. 

Sie können beispielsweise die folgende benutzerdefinierte Nutzlast angeben, die einen einzelnen Parameter wie *text*enthält.  Der Dienst, der von diesem Webhook aufgerufen wird, erwartet diesen Parameter.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Diese Beispielnutzlast wird ähnlich wie hier dargestellt aufgelöst, wenn sie an den Webhook gesendet wird.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Da alle Variablen in einem benutzerdefinierten Webhook innerhalb einer JSON-Umschließung wie "#searchinterval" angegeben werden müssen, weist auch der resultierende Webhook Variablendaten in einer Umschließung auf, etwa "00:05:00".

Um Suchergebnisse in eine benutzerdefinierte Nutzlast einzuschließen, muss **IncudeSearchResults** als Eigenschaft der obersten Ebene in der JSON-Nutzlast festgelegt werden. 

## <a name="sample-payloads"></a>Beispielnutzlasten
Dieser Abschnitt zeigt eine Beispielnutzlast für den Webhook für Protokollwarnungen, einschließlich Fällen mit Standardnutzlast und benutzerdefinierter Nutzlast.

### <a name="standard-webhook-for-log-alerts"></a>Standardwebhook für Protokollwarnungen 
In beiden Beispielen wurde eine Pseudonutzlast mit nur zwei Spalten und zwei Zeilen angegeben.

#### <a name="log-alert-for-azure-log-analytics"></a>Protokollwarnung für Azure Log Analytics
Im Folgenden wird eine Beispielnutzlast für eine Standard-Webhookaktion *ohne benutzerdefinierte JSON-Nutzlast* für Log Analytics-basierte Warnungen verwendet.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
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
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> Der Wert des Schweregradfelds könnte sich ändern, wenn Sie [Wechseln der API-Einstellung für Protokollwarnungen](alerts-log-api-switch.md) für Protokollwarnungen in Log Analytics aktiviert haben.


#### <a name="log-alert-for-azure-application-insights"></a>Protokollwarnung für Azure Application Insights
Im Folgenden sehen Sie eine Beispielnutzlast für einen Standardwebhook *ohne benutzerdefinierte JSON-Nutzlast* bei der Verwendung für Application Insights-basierte Protokollwarnungen.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
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
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Protokollwarnung mit benutzerdefinierter JSON-Nutzlast
Sie können beispielsweise Folgendes verwenden, um eine benutzerdefinierte Nutzlast zu erstellen, die nur den Warnungsnamen und die Suchergebnisse enthält: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Im Folgenden sehen Sie eine Beispielnutzlast für eine benutzerdefinierte Webhookaktion für eine beliebige Protokollwarnung.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
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
- Grundlegendes zur [Verwaltung von Protokollwarnungen in Azure](alerts-log.md)
- Erstellen und Verwalten von [Aktionsgruppen in Azure](action-groups.md)
- Weitere Informationen zu [Application Insights](../../azure-monitor/app/analytics.md)
- Weitere Informationen zum [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md). 

