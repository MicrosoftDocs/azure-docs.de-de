---
title: Überwachen von Data Factorys mit Azure Monitor | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Data Factory-Pipelines mit Azure Monitor überwachen können, indem Sie Diagnoseprotokolle mit Informationen aus Azure Data Factory aktivieren.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: e96e462709ab0c715c831bd10c628869d5c617fe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013315"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Benachrichtigen und Überwachen von Data Factorys mithilfe von Azure Monitor
Cloudanwendungen sind komplexe Systeme mit zahlreichen Variablen. Die Überwachung stellt Daten bereit, auf deren Grundlage die ordnungsgemäße Ausführung der Anwendung sichergestellt werden kann. Sie trägt auch zur Vermeidung potenzieller Probleme bei und hilft bei der Behandlung bereits aufgetretener Probleme. Darüber hinaus können Sie auf der Grundlage von Überwachungsdaten umfassende Erkenntnisse über Ihre Anwendung gewinnen. Mithilfe dieser Kenntnisse können Sie die Leistung oder Wartungsfreundlichkeit der Anwendung verbessern oder Aktionen automatisieren, die andernfalls manuell ausgeführt werden müssten.

Azure Monitor stellt Infrastrukturmetriken auf Basisebene und Protokolle für die meisten Dienste in Microsoft Azure bereit. Weitere Informationen finden Sie in der [Übersicht über Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Azure-Diagnoseprotokolle sind von einer Ressource ausgegebene Protokolle mit umfangreichen, in kurzen Abständen erfassten Betriebsdaten der Ressource. Data Factory gibt die Diagnoseprotokolle in Azure Monitor.

## <a name="persist-data-factory-data"></a>Dauerhaftes Speichern von Data Factory-Daten
Data Factory speichert nur Pipelineausführungsdaten 45 Tage lang. Wenn Sie Pipelineausführungsdaten bei Verwendung von Azure Monitor länger als 45 Tage speichern möchten, können Sie nicht nur Diagnoseprotokolle zu Analysezwecken weiterleiten, sondern sie auch in einem Speicherkonto speichern, um Factoryinformationen über einen Zeitraum Ihrer Wahl aufzubewahren.

## <a name="diagnostic-logs"></a>Diagnoseprotokolle

* Diagnoseprotokolle können zur Überwachung oder manuellen Überprüfung in einem **Speicherkonto** gespeichert werden. In den Diagnoseeinstellungen können Sie eine Aufbewahrungsdauer (in Tagen) angeben.
* Sie können Diagnoseprotokolle zur Erfassung durch einen Drittanbieterdienst oder durch eine benutzerdefinierte Analyselösung wie Power BI an **Event Hubs** streamen.
* Analysieren Sie sie mit **Log Analytics**.

Sie können ein Speicherkonto oder einen Event Hub-Namespace verwenden, das/der sich nicht im gleichen Abonnement befindet wie die Ressource, die Protokolle ausgibt. Der Benutzer, der die Einstellung konfiguriert, benötigt den entsprechenden Zugriff gemäß der rollenbasierten Zugriffssteuerung auf beide Abonnements.

## <a name="set-up-diagnostic-logs"></a>Einrichten von Diagnoseprotokollen

### <a name="diagnostic-settings"></a>Diagnoseeinstellungen
Diagnoseprotokolle für Nicht-Computeressourcen werden mithilfe von Diagnoseeinstellungen konfiguriert. Diagnoseeinstellungen für ein Ressourcensteuerelement:

* Zielort von Diagnoseprotokollen (Speicherkonto, Event Hubs oder Azure Monitor-Protokolle).
* Welche Protokollkategorien gesendet werden sollen
* Wie lange die einzelnen Protokollkategorien in einem Speicherkonto beibehalten werden sollen.
* Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 2.147.483.647) festgelegt werden.
* Wenn Aufbewahrungsrichtlinien festgelegt wurden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist (wenn z.B. nur die Optionen „Event Hubs“ oder „Azure Monitor-Protokolle“ ausgewählt sind), werden die Aufbewahrungsrichtlinien ignoriert.
* Aufbewahrungsrichtlinien werden pro Tag angewendet, sodass Protokolle am Ende eines Tages (UTC) ab dem Tag, der nun außerhalb der Aufbewahrungsrichtlinie liegt, gelöscht werden. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Aktivieren von Diagnoseprotokollen über die REST-API

Erstellen oder Aktualisieren einer Diagnoseeinstellung in der REST-API von Azure Monitor

**Anforderung**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Header**
* Ersetzen Sie `{api-version}` durch `2016-09-01`.
* Ersetzen Sie `{resource-id}` durch die Ressourcen-ID der Ressource, deren Diagnoseeinstellungen Sie bearbeiten möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/manage-resource-groups-portal.md).
* Legen Sie den Header `Content-Type` auf `application/json` fest.
* Legen Sie den Autorisierungsheader auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abgerufen haben. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen](../active-directory/develop/authentication-scenarios.md).

**Text**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Eigenschaft | Typ | BESCHREIBUNG |
| --- | --- | --- |
| storageAccountId |Zeichenfolge | Die Ressourcen-ID des Speicherkontos, an das Diagnoseprotokolle gesendet werden sollen. |
| serviceBusRuleId |Zeichenfolge | Die Service Bus-Regel-ID des Service Bus-Namespace, in dem Event Hubs für das Streaming von Diagnoseprotokollen erstellt werden sollen. Die Regel-ID hat folgendes Format: "{Service Bus-Ressourcen-ID}/authorizationrules/{Schlüsselname}".|
| workspaceId | Komplexer Typ | Array metrischer Zeiteinheiten und ihrer Aufbewahrungsrichtlinien. Diese Eigenschaft ist derzeit leer. |
|metrics| Parameterwerte der Pipelineausführung, die an die aufgerufene Pipeline übergeben werden sollen| Ein JSON-Objekt, das Parameternamen Argumentwerten zuordnet |
| logs| Komplexer Typ| Der Name einer Diagnoseprotokollkategorie für einen Ressourcentyp. Um die Liste der Diagnoseprotokollkategorien für eine Ressource zu erhalten, führen Sie zuerst einen GET-Vorgang zum Abrufen von Diagnoseeinstellungen aus. |
| category| Zeichenfolge| Array von Protokollkategorien und ihrer Aufbewahrungsrichtlinien |
| timeGrain | Zeichenfolge | Die Granularität von Metriken, die im Zeitformat ISO 8601 erfasst werden. Muss „PT1M“ (eine Minute) sein|
| enabled| Boolescher Wert | Gibt an, ob die Sammlung dieser Metrik- oder Protokollkategorie für diese Ressource aktiviert ist|
| retentionPolicy| Komplexer Typ| Beschreibt die Aufbewahrungsrichtlinie für eine Metrik- oder Protokollkategorie. Wird nur für die Speicherkonto-Option verwendet.|
| days| Int| Anzahl der Tage, die Metriken oder Protokolle aufbewahrt werden sollen. Beim Wert 0 werden die Protokolle dauerhaft gespeichert. Wird nur für die Speicherkonto-Option verwendet. |

**Antwort**

200 – OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Abrufen von Informationen zu einer Diagnoseeinstellung in der REST-API von Azure Monitor

**Anforderung**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Header**
* Ersetzen Sie `{api-version}` durch `2016-09-01`.
* Ersetzen Sie `{resource-id}` durch die Ressourcen-ID der Ressource, deren Diagnoseeinstellungen Sie bearbeiten möchten. Weitere Informationen finden Sie unter „Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen“.
* Legen Sie den Header `Content-Type` auf `application/json` fest.
* Legen Sie den Autorisierungsheader auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory abgerufen haben. Weitere Informationen finden Sie unter „Authentifizieren von Anforderungen“.

**Antwort**

200 – OK

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[Weitere Informationen](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schema von Protokollen und Ereignissen

### <a name="activity-run-logs-attributes"></a>Attribute der Protokolle von Aktivitätsausführungen

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Eigenschaft | Typ | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| Level |Zeichenfolge | Ebene der Diagnoseprotokolle. Bei Protokollen von Aktivitätsausführungen ist dies stets Ebene 4. | `4`  |
| correlationId |Zeichenfolge | Eindeutige ID zum durchgängigen Nachverfolgen einer bestimmten Anforderung | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Zeichenfolge | Zeitpunkt des Ereignisses im Zeitraum, UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Zeichenfolge| ID der Aktivitätsausführung | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Zeichenfolge| ID der Pipelineausführung | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Zeichenfolge | Mit der Data Factory-Ressource verknüpfte Ressourcen-ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Zeichenfolge | Kategorie der Diagnoseprotokolle. Legen Sie diese Eigenschaft auf „ActivityRuns“ fest | `ActivityRuns` |
|level| Zeichenfolge | Ebene der Diagnoseprotokolle. Legen Sie diese Eigenschaft auf „Informational“ fest | `Informational` |
|operationName| Zeichenfolge |Der Name der Aktivität mit Status. Bei Status „start heartbeat“ `MyActivity -`. Bei Status „end heartbeat“ `MyActivity - Succeeded` mit Endstatus | `MyActivity - Succeeded` |
|pipelineName| Zeichenfolge | Name der Pipeline | `MyPipeline` |
|activityName| Zeichenfolge | Der Name der Aktivität | `MyActivity` |
|start| Zeichenfolge | Start der Aktivitätsausführung im Zeitraum, UTC-Format | `2017-06-26T20:55:29.5007959Z`|
|end| Zeichenfolge | Ende der Aktivitätsausführung im Zeitraum, UTC-Format Wenn die Aktivität noch nicht beendet ist (Diagnoseprotokoll für eine startende Aktivität), wird der Standardwert `1601-01-01T00:00:00Z` festgelegt.  | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-logs-attributes"></a>Attribute der Protokolle von Pipelineausführungen

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Eigenschaft | Typ | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| Level |Zeichenfolge | Ebene der Diagnoseprotokolle. Bei Protokollen von Aktivitätsausführungen ist dies Ebene 4. | `4`  |
| correlationId |Zeichenfolge | Eindeutige ID zum durchgängigen Nachverfolgen einer bestimmten Anforderung | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Zeichenfolge | Zeitpunkt des Ereignisses im Zeitraum, UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Zeichenfolge| ID der Pipelineausführung | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Zeichenfolge | Mit der Data Factory-Ressource verknüpfte Ressourcen-ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Zeichenfolge | Kategorie der Diagnoseprotokolle. Legen Sie diese Eigenschaft auf „PipelineRuns“ fest | `PipelineRuns` |
|level| Zeichenfolge | Ebene der Diagnoseprotokolle. Legen Sie diese Eigenschaft auf „Informational“ fest | `Informational` |
|operationName| Zeichenfolge |Name der Pipeline mit Status. „Pipeline - Succeeded“ mit Endstatus nach Abschluss der Pipelineausführung| `MyPipeline - Succeeded` |
|pipelineName| Zeichenfolge | Name der Pipeline | `MyPipeline` |
|start| Zeichenfolge | Start der Aktivitätsausführung im Zeitraum, UTC-Format | `2017-06-26T20:55:29.5007959Z`|
|end| Zeichenfolge | Ende der Aktivitätsausführungen im Zeitraum, UTC-Format Wenn die Aktivität noch nicht beendet ist (Diagnoseprotokoll für eine startende Aktivität), wird der Standardwert `1601-01-01T00:00:00Z` festgelegt.  | `2017-06-26T20:55:29.5007959Z` |
|status| Zeichenfolge | Der Endstatus der Pipelineausführung („Succeeded“ oder „Failed“) | `Succeeded`|

### <a name="trigger-run-logs-attributes"></a>Attribute der Protokolle von Triggerausführungen

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Eigenschaft | Typ | BESCHREIBUNG | Beispiel |
| --- | --- | --- | --- |
| Level |Zeichenfolge | Ebene der Diagnoseprotokolle. Legen Sie diese für Protokolle von Aktivitätsausführungen auf 4 fest. | `4`  |
| correlationId |Zeichenfolge | Eindeutige ID zum durchgängigen Nachverfolgen einer bestimmten Anforderung | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Zeichenfolge | Zeitpunkt des Ereignisses im Zeitraum, UTC-Format: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Zeichenfolge| ID der Triggerausführung | `08587023010602533858661257311` |
|resourceId| Zeichenfolge | Mit der Data Factory-Ressource verknüpfte Ressourcen-ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Zeichenfolge | Kategorie der Diagnoseprotokolle. Legen Sie diese Eigenschaft auf „PipelineRuns“ fest | `PipelineRuns` |
|level| Zeichenfolge | Ebene der Diagnoseprotokolle. Legen Sie diese Eigenschaft auf „Informational“ fest | `Informational` |
|operationName| Zeichenfolge |Name des Triggers mit Endstatus, ob dieser erfolgreich ausgelöst wurde. „MyTrigger - Succeeded“, wenn der Heartbeat erfolgreich war| `MyTrigger - Succeeded` |
|triggerName| Zeichenfolge | Name des Triggers | `MyTrigger` |
|triggerType| Zeichenfolge | Typ des Triggers (manueller Trigger oder Zeitplantrigger) | `ScheduleTrigger` |
|triggerEvent| Zeichenfolge | Ereignis des Triggers | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Zeichenfolge | Beginn der Triggerauslösung im Zeitraum, UTC-Format | `2017-06-26T20:55:29.5007959Z`|
|status| Zeichenfolge | Endstatus, der angibt, ob der Trigger erfolgreich ausgelöst wurde („Succeeded“ oder „Failed“) | `Succeeded`|

## <a name="metrics"></a>Metriken

Mit Azure Monitor können Sie Telemetriedaten verwenden, um sich einen Überblick über Leistung und Integrität Ihrer Workloads in Azure zu verschaffen. Die wichtigsten Typen von Telemetriedaten sind Metriken (auch Leistungsindikatoren genannt), die von den meisten Azure-Ressourcen ausgegeben werden. Azure Monitor bietet Ihnen verschiedene Möglichkeiten, diese Metriken für die Überwachung und Problembehandlung zu konfigurieren und zu nutzen.

ADFV2 gibt die folgenden Metriken aus.

| **Metrik**           | **Metrikanzeigename**         | **Einheit** | **Aggregationstyp** | **Beschreibung**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Metriken zu erfolgreichen Pipelineausführungen | Count    | Gesamt                | Insgesamt erfolgreiche Pipelineausführungen in einem Zeitfenster von einer Minute |
| PipelineFailedRuns   | Metriken zu fehlerhaften Pipelineausführungen    | Count    | Gesamt                | Insgesamt fehlerhafte Pipelineausführungen in einem Zeitfenster von einer Minute    |
| ActivitySucceededRuns | Metriken zu erfolgreichen Aktivitätsausführungen | Count    | Gesamt                | Insgesamt erfolgreiche Aktivitätsausführungen in einem Zeitfenster von einer Minute  |
| ActivityFailedRuns   | Metriken zu fehlerhaften Aktivitätsausführungen    | Count    | Gesamt                | Insgesamt fehlerhafte Aktivitätsausführungen in einem Zeitfenster von einer Minute     |
| TriggerSucceededRuns | Metriken zu erfolgreichen Triggerausführungen  | Count    | Gesamt                | Insgesamt erfolgreiche Triggerausführungen in einem Zeitfenster von einer Minute   |
| TriggerFailedRuns    | Metriken zu fehlerhaften Triggerausführungen     | Count    | Gesamt                | Insgesamt fehlerhafte Triggerausführungen in einem Zeitfenster von einer Minute      |

Eine Anleitung für den Zugriff auf die Metriken finden Sie im folgenden Artikel: https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Überwachen von Data Factory-Metriken mit Azure Monitor

Sie können die Azure Data Factory-Integration in Azure Monitor zur Weiterleitung von Daten an Azure Monitor verwenden. Diese Integration ist in den folgenden Szenarien nützlich:

1.  Sie möchten komplexe Abfragen für einen umfangreichen Satz von Metriken schreiben, die von Data Factory an Azure Monitor veröffentlicht werden. Sie können auch eigene Benachrichtigungen zu diesen Abfragen über Azure Monitor erstellen.

2.  Sie möchten Data Factorys übergreifend überwachen. Sie können Daten aus mehreren Data Factorys an einen einzigen Azure Monitor-Arbeitsbereich weiterleiten.

Das folgende Video enthält eine siebenminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurieren von Diagnoseeinstellungen und Arbeitsbereich

Aktivieren Sie die Diagnoseeinstellungen für Ihre Data Factory.

1.  Wählen Sie **Azure Monitor** -> **Diagnoseeinstellungen** -> Wählen Sie die Data Factory aus -> Aktivieren Sie die Diagnose.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Geben Sie die Diagnoseeinstellungen einschließlich der Konfiguration des Arbeitsbereichs an.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installieren von Azure Data Factory-Analysen aus dem Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Klicken Sie auf **Erstellen**, und wählen Sie den Arbeitsbereich und die Arbeitsbereichseinstellungen aus.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Überwachen von Data Factory-Metriken

Durch die Installation von **Azure Data Factory-Analysen** wird ein Standardsatz von Ansichten für die folgenden Metriken erstellt:

- ADF-Ausführungen – 1) Pipelineausführungen nach Data Factory

- ADF-Ausführungen – 2) Aktivitätsausführungen nach Data Factory

- ADF-Ausführungen – 3) Triggerausführungen nach Data Factory

- ADF-Fehler – 1) Top 10-Pipelinefehler nach Data Factory

- ADF-Fehler – 2) Top 10-Aktivitätsfehler nach Data Factory

- ADF-Fehler – 3) Top 10-Triggerfehler nach Data Factory

- ADF-Statistik – 1) Aktivitätsausführungen nach Typ

- ADF-Statistik – 2) Triggerausführungen nach Typ

- ADF-Statistik – 3) Höchstdauer der Pipelineausführungen

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Sie können u.a. die obigen Metriken visualisieren, die Abfragen hinter diesen Metriken betrachten, die Abfragen bearbeiten oder Benachrichtigungen erstellen.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alerts

Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Überwachen -&gt; Warnungen**, um Warnungen zu erstellen.

![Warnungen im Portalmenü](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Erstellen von Warnungen

1.  Klicken Sie auf **+ Neue Warnungsregel**, um eine neue Warnung zu erstellen.

    ![Neue Warnungsregel](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definieren Sie die **Warnungsbedingung**.

    > [!NOTE]
    > Achten Sie darauf, dass unter **Nach Ressourcentyp filtern** die Option **Alle** ausgewählt ist.

    ![Warnungsbedingung, Bildschirm 1 von 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Warnungsbedingung, Bildschirm 2 von 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Warnungsbedingung, Bildschirm 3 von 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definieren Sie die **Warnungsdetails**.

    ![Warnungsdetails](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definieren Sie die **Aktionsgruppe**.

    ![Aktionsgruppe, Bildschirm 1 von 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Aktionsgruppe, Bildschirm 2 von 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Aktionsgruppe, Bildschirm 3 von 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Aktionsgruppe, Bildschirm 4 von 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Nächste Schritte

Im Artikel [Programmgesteuertes Überwachen einer Azure Data Factory](monitor-programmatically.md) erfahren Sie mehr über das Überwachen und Verwalten von Pipelines mit Code.
