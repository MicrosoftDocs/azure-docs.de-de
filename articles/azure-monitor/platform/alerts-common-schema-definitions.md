---
title: Definitionen des allgemeinen Warnungsschemas für Webhooks/Logic Apps/Azure Functions/Automation Runbooks
description: Grundlagen der Definitionen des allgemeinen Warnungsschemas für Webhooks/Logic Apps/Azure Functions/Automation Runbooks
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: e29a1f5d1e258ab66540010dc12f9326b8fd57a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775745"
---
# <a name="common-alert-schema-definitions"></a>Definitionen des allgemeinen Warnungsschemas

In diesem Artikel werden die [Definitionen des allgemeinen Warnungsschemas](https://aka.ms/commonAlertSchemaDocs) für Webhooks/Logic Apps/Azure Functions/Automation Runbooks beschrieben. 

## <a name="overview"></a>Übersicht

Mit jeder Warnungsinstanz werden **die betroffene Ressource** und **die Ursache der Warnung** beschrieben. Diese Instanzen werden im allgemeinen Schema in den folgenden Abschnitten beschrieben:
* **Zusammenfassung**: Eine Gruppe **standardisierter Felder** (für alle Warnungstypen gleich), die beschreiben, auf **welcher Ressource** sich die Warnung befindet, sowie zusätzliche allgemeine Warnungsmetadaten (z. B. Schweregrad oder Beschreibung). 
* **Warnungskontext**: Eine Gruppe von Feldern, mit denen die **Ursache der Warnung** beschrieben wird. Die Felder variieren **basierend auf dem Warnungstyp**. Eine Metrikwarnung enthält im Warnungskontext beispielsweise Felder wie den Metriknamen und -wert, während eine Aktivitätsprotokollwarnung Informationen zum Ereignis enthält, von dem die Warnung generiert wurde. 

##### <a name="sample-alert-payload"></a>Beispielnutzlast einer Warnung
```json
{
  "schemaId": "azureMonitorCommonAlertSchema",
  "data": {
    "essentials": {
      "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
      "alertRule": "WCUS-R2-Gen2",
      "severity": "Sev3",
      "signalType": "Metric",
      "monitorCondition": "Resolved",
      "monitoringService": "Platform",
      "alertTargetIDs": [
        "/subscriptions/<subscription ID>/resourcegroups/pipelinealertrg/providers/microsoft.compute/virtualmachines/wcus-r2-gen2"
      ],
      "originAlertId": "3f2d4487-b0fc-4125-8bd5-7ad17384221e_PipeLineAlertRG_microsoft.insights_metricAlerts_WCUS-R2-Gen2_-117781227",
      "firedDateTime": "2019-03-22T13:58:24.3713213Z",
      "resolvedDateTime": "2019-03-22T14:03:16.2246313Z",
      "description": "",
      "essentialsVersion": "1.0",
      "alertContextVersion": "1.0"
    },
    "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 7.727
          }
        ]
      }
    }
  }
}
```

## <a name="essentials-fields"></a>Felder in „Essentials“

| Feld | BESCHREIBUNG|
|:---|:---|
| alertId | GUID, die diese Warnungsinstanz eindeutig identifiziert. |
| alertRule | Name der Warnungsregel, die die Warnungsinstanz generiert hat. |
| Severity | Schweregrad der Warnung Mögliche Werte: Sev0, Sev1, Sev2, Sev3, Sev4 |
| signalType | Identifiziert das Signal, für das die Warnungsregel definiert wurde. Mögliche Werte: Metrik, Protokoll, Aktivitätsprotokoll |
| monitorCondition | Wenn eine Warnung ausgelöst wird, wird die Überwachungsbedingung der Warnung auf „Ausgelöst“ festgelegt. Wenn die zugrunde liegende Bedingung gelöscht wurde, die die Warnung ausgelöst hat, wird die Überwachungsbedingung auf „Behoben“ festgelegt.   |
| monitoringService | Der Überwachungsdienst oder die Lösung, von dem bzw. der die Warnung generiert wurde. Die Felder für den Warnungskontext werden vom Überwachungsdienst vorgegeben. |
| alertTargetIds | Liste der ARM-IDs aller betroffenen Ziele einer Warnung. Für eine Protokollwarnung, die in einem Log Analytics-Arbeitsbereich oder einer Application Insights-Instanz definiert ist, ist es der jeweilige Arbeitsbereich bzw. die jeweilige Anwendung. |
| originAlertId | ID der Warnungsinstanz, wie sie vom überwachenden Dienst generiert wird, der sie generiert. |
| firedDateTime | Datum/Uhrzeit der Auslösung der Warnungsinstanz in UTC |
| resolvedDateTime | Datum/Uhrzeit der Festlegung der Überwachungsbedingung für die Warnungsinstanz auf „Behoben“ in UTC. Gilt derzeit nur für Metrikwarnungen.|
| description | Die Beschreibung entsprechend der Definition in der Warnungsregel |
|essentialsVersion| Versionsnummer für den Abschnitt „essentials“|
|alertContextVersion | Versionsnummer für den Abschnitt „alertContext“ |

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "essentials": {
    "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
    "alertRule": "Contoso IT Metric Alert",
    "severity": "Sev3",
    "signalType": "Metric",
    "monitorCondition": "Fired",
    "monitoringService": "Platform",
    "alertTargetIDs": [
      "/subscriptions/<subscription ID>/resourceGroups/aimon-rg/providers/Microsoft.Insights/components/ai-orion-int-fe"
    ],
    "originAlertId": "74ff8faa0c79db6084969cf7c72b0710e51aec70b4f332c719ab5307227a984f",
    "firedDateTime": "2019-03-26T05:25:50.4994863Z",
    "description": "Test Metric alert",
    "essentialsVersion": "1.0",
    "alertContextVersion": "1.0"
  }
}
```

## <a name="alert-context-fields"></a>Warnungskontextfelder

### <a name="metric-alerts"></a>Metrikwarnungen

#### <a name="monitoringservice--platform"></a>monitoringService = 'Platform'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 31.1105
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="log-alerts"></a>Protokollwarnungen

> [!NOTE]
> Wenn Sie für Ihre vorhandenen Protokollwarnungen die benutzerdefinierte JSON-Option verwenden, wird die Anpassung im allgemeinen Schema nicht beibehalten.

#### <a name="monitoringservice--log-analytics"></a>monitoringService = 'Log Analytics'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
      "SearchQuery": "search * \n| where Type == \"Heartbeat\" \n| where Category == \"Direct Agent\" \n| where TimeGenerated > ago(30m) ",
      "SearchIntervalStartTimeUtc": "3/22/2019 1:36:31 PM",
      "SearchIntervalEndtimeUtc": "3/22/2019 1:51:31 PM",
      "ResultCount": 15,
      "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2Fpipelinealertrg%2Fproviders%2FMicrosoft.OperationalInsights%2Fworkspaces%2FINC-OmsAlertRunner%22%7D%5D%7D/query/search%20%2A%20%0A%7C%20where%20Type%20%3D%3D%20%22Heartbeat%22%20%0A%7C%20where%20Category%20%3D%3D%20%22Direct%20Agent%22%20%0A%7C%20where%20TimeGenerated%20%3E%20%28datetime%282019-03-22T13%3A51%3A31.0000000%29%20-%2030m%29%20%20/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a31.0000000Z%2f2019-03-22T13%3a51%3a31.0000000Z",
      "SeverityDescription": "Warning",
      "WorkspaceId": "2a1f50a7-ef97-420c-9d14-938e77c2a929",
      "SearchIntervalDurationMin": "15",
      "AffectedConfigurationItems": [
        "INC-Gen2Alert"
      ],
      "SearchIntervalInMinutes": "15",
      "Threshold": 10000,
      "Operator": "Less Than"
    }
}
```

#### <a name="monitoringservice--application-insights"></a>monitoringService = 'Application Insights'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
      "SearchQuery": "search *",
      "SearchIntervalStartTimeUtc": "3/22/2019 1:36:33 PM",
      "SearchIntervalEndtimeUtc": "3/22/2019 1:51:33 PM",
      "ResultCount": 0,
      "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2FPipeLineAlertRG%2Fproviders%2Fmicrosoft.insights%2Fcomponents%2FWEU-AIRunner%22%7D%5D%7D/query/search%20%2A/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a33.0000000Z%2f2019-03-22T13%3a51%3a33.0000000Z",
      "SearchIntervalDurationMin": "15",
      "SearchIntervalInMinutes": "15",
      "Threshold": 10000,
      "Operator": "Less Than",
      "ApplicationId": "8e20151d-75b2-4d66-b965-153fb69d65a6"
    }
}
```

### <a name="activity-log-alerts"></a>Aktivitätsprotokollwarnungen

#### <a name="monitoringservice--activity-log---administrative"></a>monitoringService = 'Activity Log – Administrative'

##### <a name="sample-values"></a>Beispielwerte
```json
{
  "alertContext": {
      "authorization": {
        "action": "Microsoft.Compute/virtualMachines/restart/action",
        "scope": "/subscriptions/<subscription ID>/resourceGroups/PipeLineAlertRG/providers/Microsoft.Compute/virtualMachines/WCUS-R2-ActLog"
      },
      "channels": "Operation",
      "claims": "{\"aud\":\"https://management.core.windows.net/\",\"iss\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"iat\":\"1553260826\",\"nbf\":\"1553260826\",\"exp\":\"1553264726\",\"aio\":\"42JgYNjdt+rr+3j/dx68v018XhuFAwA=\",\"appid\":\"e9a02282-074f-45cf-93b0-50568e0e7e50\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\"uti\":\"v5wYC9t9ekuA2rkZSVZbAA\",\"ver\":\"1.0\"}",
      "caller": "9778283b-b94c-4ac6-8a41-d5b493d03aa3",
      "correlationId": "8ee9c32a-92a1-4a8f-989c-b0ba09292a91",
      "eventSource": "Administrative",
      "eventTimestamp": "2019-03-22T13:56:31.2917159+00:00",
      "eventDataId": "161fda7e-1cb4-4bc5-9c90-857c55a8f57b",
      "level": "Informational",
      "operationName": "Microsoft.Compute/virtualMachines/restart/action",
      "operationId": "310db69b-690f-436b-b740-6103ab6b0cba",
      "status": "Succeeded",
      "subStatus": "",
      "submissionTimestamp": "2019-03-22T13:56:54.067593+00:00"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zum allgemeinen Warnungsschema](https://aka.ms/commonAlertSchemaDocs)


