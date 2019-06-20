---
title: Streamen von Azure-Diagnoseprotokollen an Event Hubs
description: Hier erfahren Sie, wie Sie Azure-Diagnoseprotokolle an Event Hubs streamen.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: b5299af375646e7759d0770139df2cd6d7ce105c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237698"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Streamen von Azure-Diagnoseprotokollen an Event Hubs
**[Azure-Diagnoseprotokolle](diagnostic-logs-overview.md)** können nahezu in Echtzeit an eine beliebige Anwendung gestreamt werden – entweder mithilfe der integrierten Portaloption „In Event Hubs exportieren“ oder durch Aktivieren der Event Hub-Autorisierungsregel-ID in einer Diagnoseeinstellung (über die Azure PowerShell-Cmdlets oder über die Azure CLI).

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Verwendungsmöglichkeiten für Diagnoseprotokolle und Event Hubs
Im Anschluss finden Sie eine kleine Auswahl von Verwendungsmöglichkeiten für das Streamen von Diagnoseprotokollen:

* **Streamen von Protokollen an Protokollierungs- und Telemetriesysteme von Drittanbietern**: Sie können alle Diagnoseprotokolle an einen einzelnen Event Hub streamen, um Protokolldaten an ein SIEM- oder Protokollanalysetool eines Drittanbieters weiterzureichen.
* **Anzeigen der Dienstintegrität durch Streamen von Daten zum langsamsten Pfad an Power BI**: Mithilfe von Event Hubs, Stream Analytics und Power BI können Sie sich anhand Ihrer Diagnosedaten problemlos und nahezu in Echtzeit einen Einblick in Ihre Azure-Dienste verschaffen. [Dieser Dokumentationsartikel bietet eine gute Übersicht über die Einrichtung von Event Hubs, die Verarbeitung von Daten mit Stream Analytics und die Verwendung von Power BI als Ausgabe](../../stream-analytics/stream-analytics-power-bi-dashboard.md). Hier finden Sie einige Tipps für die Einrichtung von Diagnoseprotokollen:

  * Ein Event Hub wird automatisch für eine Kategorie von Diagnoseprotokollen erstellt, wenn Sie die entsprechende Option im Portal oder über PowerShell aktivieren. Es empfiehlt sich daher, den Event Hub im Namespace mit dem Namen auszuwählen, der mit **insights-** beginnt.
  * Der folgende SQL-Code ist ein Beispiel für eine Stream Analytics-Abfrage, mit der Sie alle Protokolldaten in einer Power BI-Tabelle analysieren können:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Erstellen einer benutzerdefinierten Telemetrie- und Protokollierungsplattform:** Event Hubs ermöglicht dank des hochgradig skalierbaren Veröffentlichen/Abonnieren-Konzepts eine flexible Erfassung von Diagnoseprotokollen. Dies ist interessant, wenn Sie bereits über eine benutzerdefinierte Telemetrieplattform verfügen oder eine solche Plattform erstellen möchten. [Informationen zur Verwendung von Event Hubs für eine globale Telemetrieplattform finden Sie in der Anleitung von Dan Rosanova.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-diagnostic-logs"></a>Aktivieren des Streamens von Diagnoseprotokollen

Das Streamen von Diagnoseprotokollen kann programmgesteuert, über das Portal oder mithilfe der [Azure Monitor-REST-APIs](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)aktiviert werden. In beiden Fällen erstellen Sie eine Diagnoseeinstellung, in der Sie einen Event Hubs-Namespace und die Protokollkategorien und Metriken angeben, die an den Namespace gesendet werden sollen. Für jede aktivierte Protokollkategorie wird eine Event Hub-Instanz erstellt. Eine **Diagnoseprotokollkategorie** ist ein Protokolltyp, der von einer Ressource erfasst werden kann.

> [!WARNING]
> Zum Aktivieren und Streamen von Diagnoseprotokollen aus Computeressourcen (beispielsweise virtuelle Computer oder Service Fabric) müssen [andere Schritte](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)ausgeführt werden.

Der Event Hubs-Namespace muss sich nicht in demselben Abonnement wie die Ressource befinden, die Protokolle ausgibt – sofern der Benutzer, der die Einstellung konfiguriert, über den entsprechenden RBAC-Zugriff auf beide Abonnements verfügt und beide Abonnements Teil desselben AAD-Mandanten sind.

> [!NOTE]
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel*: Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Streamen von Diagnoseprotokollen mithilfe des Portals

1. Navigieren Sie im Portal zu Azure Monitor, und klicken Sie auf **Diagnoseeinstellungen**.

    ![Abschnitt „Überwachung“ von Azure Monitor](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. Filtern Sie optional die Liste nach Ressourcengruppe oder Ressourcentyp, und klicken Sie auf die Ressource, für die Sie eine Diagnoseeinstellung festlegen möchten.

3. Wenn keine Einstellungen für die Ressource vorhanden sind, die Sie ausgewählt haben, werden Sie aufgefordert, eine Einstellung zu erstellen. Klicken Sie auf „Diagnose aktivieren“.

   ![Diagnoseeinstellung hinzufügen – keine Einstellungen vorhanden](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   Wenn Einstellungen für die Ressource vorhanden sind, sehen Sie eine Liste der Einstellungen, die bereits für diese Ressource konfiguriert sind. Klicken Sie auf „Diagnoseeinstellung hinzufügen“.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. Geben Sie Ihrer Einstellung einen Namen, aktivieren Sie das Kontrollkästchen für **An einen Event Hub streamen**, und wählen Sie einen Event Hubs-Namespace aus.

   ![Diagnoseeinstellung hinzufügen – Einstellungen vorhanden](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   Der ausgewählte Namespace fungiert als Ziel für die Event Hub-Erstellung (falls Sie erstmals Diagnoseprotokolle streamen) oder für das Streaming (falls bereits Ressourcen vorhanden sind, die diese Protokollkategorie an diesen Namespace streamen), und die Richtlinie definiert die Berechtigungen für den Streamingmechanismus. Für das Streaming an einen Event Hub werden aktuell Berechtigungen für das Verwalten, Senden und Lauschen benötigt. Sie können freigegebene Zugriffsrichtlinien für Ihren Event Hubs-Namespace im Portal auf der Registerkarte „Konfigurieren“ für Ihren Namespace erstellen oder ändern. Zum Aktualisieren einer dieser Diagnoseeinstellungen muss der Client in der Event Hubs-Autorisierungsregel über die „ListKey“-Berechtigung verfügen. Sie können optional auch einen Event Hub-Namen angeben. Wenn Sie einen Event Hub-Namen angeben, werden Protokolle nicht an einen neu erstellten Event Hub gemäß Protokollkategorie, sondern an diesen Event Hub geroutet.

4. Klicken Sie auf **Speichern**.

Nach einigen Augenblicken wird die neue Einstellung in Ihrer Liste der Einstellungen für diese Ressource angezeigt, und Diagnoseprotokolle werden an diesen Event Hub gestreamt, sobald neue Ereignisdaten generiert werden.

### <a name="via-powershell-cmdlets"></a>Verwenden von PowerShell-Cmdlets

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie das Streaming über die [Azure PowerShell-Cmdlets](../../azure-monitor/platform/powershell-quickstart-samples.md) aktivieren möchten, können Sie das Cmdlet `Set-AzDiagnosticSetting` mit folgenden Parametern verwenden:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

Die Event Hub-Autorisierungsregel-ID ist eine Zeichenfolge im folgenden Format: `{Event Hub namespace resource ID}/authorizationrules/{key name}`. Beispiel: `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Sie können zurzeit mit PowerShell keinen bestimmten Event Hub-Namen auswählen.

### <a name="via-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Zum Ermöglichen des Streamings per [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest) können Sie den Befehl [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) verwenden.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Sie können das Diagnoseprotokoll um weitere Kategorien ergänzen, indem Sie dem JSON-Array, das als `--logs`-Parameter übergeben wird, Wörterbücher hinzufügen.

Für das Argument `--event-hub-rule` wird das gleiche Format wie für die Event Hub-Autorisierungsregel-ID verwendet. Dies wurde für das PowerShell-Cmdlet bereits erläutert.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Nutzen der Protokolldaten aus Event Hubs

Hier sehen Sie ein Beispiel für eine Datenausgabe aus Event Hubs:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Elementname | BESCHREIBUNG |
| --- | --- |
| records |Ein Array sämtlicher Protokollereignisse in dieser Nutzlast. |
| time |Der Zeitpunkt, zu dem das Ereignis aufgetreten ist. |
| category |Die Protokollkategorie für dieses Ereignis. |
| resourceId |Die Ressourcen-ID der Ressource, die dieses Ereignis generiert hat. |
| operationName |Der Name des Vorgangs. |
| level |Optional. Gibt die Protokollereignisebene an. |
| properties |Die Eigenschaften des Ereignisses. |

Eine Liste mit allen Ressourcenanbietern, die das Streamen an Event Hubs unterstützen, finden Sie [hier](diagnostic-logs-overview.md).

## <a name="stream-data-from-compute-resources"></a>Streamen von Daten von Computeressourcen

Mithilfe des Azure-Diagnose-Agents können Sie auch Diagnoseprotokolle von Computeressourcen streamen. Informationen zum Einrichten finden Sie in [diesem Artikel](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

## <a name="next-steps"></a>Nächste Schritte

* [Streamen von Azure Active Directory-Protokollen mit Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Informieren Sie sich ausführlicher über Azure-Diagnoseprotokolle.](diagnostic-logs-overview.md)
* [Erste Schritte mit Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

