---
title: Metriken, Warnungen und Diagnoseprotokolle – Azure Batch | Microsoft-Dokumentation
description: Zeichnen Sie Diagnoseprotokollereignisse für Azure Batch-Kontoressourcen wie Pools und Tasks auf, und analysieren Sie sie.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: e1fc405951789305b0df86fd0f7b91890fb45c06
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242621"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-Metriken, -Warnungen und -Protokolle für die Diagnoseauswertung und -überwachung

 
In diesem Artikel wird erläutert, wie Sie ein Batch-Konto mithilfe der Features von [Azure Monitor](../azure-monitor/overview.md) überwachen. Azure Monitor erfasst [Metriken](../azure-monitor/platform/data-platform-metrics.md) und [Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md) für Ressourcen in Ihrem Batch-Konto. Sie können mithilfe verschiedener Methoden diese Daten sammeln und nutzen, um Ihr Batch-Konto zu überwachen und Probleme zu diagnostizieren. Sie können auch [Metrikwarnungen](../azure-monitor/platform/alerts-overview.md) konfigurieren, um Benachrichtigungen zu erhalten, wenn eine Metrik einen angegebenen Wert erreicht. 

## <a name="batch-metrics"></a>Batch-Metriken

Bei Metriken handelt es sich um von Azure-Ressourcen ausgegebene Azure-Telemetriedaten (auch als Leistungsindikatoren bezeichnet), die vom Azure Monitor-Dienst verwendet werden. Zu den Beispielmetriken in einem Batch-Konto zählen: Poolerstellungsereignisse, Anzahl der Knoten mit niedriger Priorität und Taskabschlussereignisse. 

Sehen Sie sich die [Liste der unterstützten Batch-Metriken](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts) an.

Für Metriken gilt Folgendes:

* Sie sind in jedem Batch-Konto ohne zusätzliche Konfiguration standardmäßig aktiviert.
* Sie werden minütlich generiert.
* Sie werden nicht automatisch dauerhaft gespeichert, sondern decken jeweils nur die letzten 30 Tage ab. Sie können Aktivitätsmetriken im Rahmen der Diagnoseprotokollierung dauerhaft speichern.

### <a name="view-metrics"></a>Anzeigen von Metriken

Zeigen Sie Metriken für Ihr Batch-Konto im Azure-Portal an. Auf der Seite **Übersicht** für das Konto werden standardmäßig wichtige Metriken zu Knoten, Kernen und Aufgaben angezeigt. 

So zeigen Sie alle Metriken für Batch-Konten an 

1. Klicken Sie im Portal auf **Alle Dienste** > **Batch-Konten** und anschließend auf den Namen Ihres Batch-Kontos.
2. Klicken Sie unter **Überwachung** auf **Metriken**.
3. Wählen Sie eine oder mehrere Metriken aus. Wählen Sie bei Bedarf mithilfe der Dropdownmenüs **Abonnements**, **Ressourcengruppe**, **Ressourcentyp** und **Ressource** zusätzliche Ressourcenmetriken aus.

    ![Batch-Metriken](media/batch-diagnostics/metrics-portal.png)

Verwenden Sie zum programmgesteuerten Abrufen von Metriken die Azure Monitor-APIs. Sehen Sie sich beispielsweise die Informationen unter [Retrieve Azure Monitor metrics with .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) (Abrufen von Azure Monitor-Metriken mit .NET) an.

## <a name="batch-metric-reliability"></a>Zuverlässigkeit von Batch-Metriken

Metriken dienen zum Erstellen von Trends und zum Durchführen von Datenanalysen. Die Metrikbereitstellung ist nicht garantiert und unterliegt der außerordentlichen Bereitstellung und dem Verlust bzw. der Duplizierung von Daten. Es wird nicht empfohlen, einzelne Ereignisse zu verwenden, um Warnungen anzuzeigen oder Funktionen auszulösen. Im Abschnitt [Batch-Metrikwarnungen](#batch-metric-alerts) finden Sie weitere Details zum Festlegen von Schwellenwerten für Warnungen.

Metriken, die innerhalb der letzten drei Minuten ausgegeben wurden, werden ggf. noch aggregiert. Innerhalb dieses Zeitraums werden die Metrikwerte unter Umständen zu niedrig angegeben.

## <a name="batch-metric-alerts"></a>Batch-Metrikwarnungen

Konfigurieren Sie optional *Metrikwarnungen* nahezu in Echtzeit, die ausgelöst werden, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Die Warnung generiert eine [Benachrichtigung](../monitoring-and-diagnostics/insights-alerts-portal.md), wenn die Warnung den Status „Aktiviert“ (nach Überschreitung des Schwellenwerts und Erfüllung der Warnungsbedingung) oder „Aufgelöst“ (nach der erneuten Überschreitung des Schwellenwerts und Nichterfüllung der Bedingung) hat. Das Bereitstellen von Warnungen basierend auf einzelnen Datenpunkten wird nicht empfohlen, da Metriken der außerordentlichen Bereitstellung und dem Verlust bzw. der Duplizierung von Daten unterliegen. Beim Bereitstellen von Warnungen sollten Schwellenwerte genutzt werden, um diese Inkonsistenzen zu berücksichtigen.

Beispiel: Es empfiehlt sich, eine Metrikwarnung für den Fall zu konfigurieren, dass die Anzahl für Kerne mit niedriger Priorität auf einen bestimmten Wert sinkt, sodass Sie die Zusammensetzung der Pools anpassen können. Es wird empfohlen, einen Zeitraum von zehn oder mehr Minuten festzulegen, in dem Warnungen ausgelöst werden, wenn die durchschnittliche Anzahl von Kernen mit niedriger Priorität unter den Schwellenwert für den gesamten Zeitraum fällt. Es wird nicht empfohlen, Warnungen für einen Zeitraum von ein bis fünf Minuten bereitzustellen, da Metriken ggf. noch aggregiert werden.

So konfigurieren Sie eine Metrikwarnung im Portal:

1. Klicken Sie auf **Alle Dienste** > **Batch-Konten** und anschließend auf den Namen Ihres Batch-Kontos.
2. Klicken Sie unter **Überwachung** auf **Warnungsregeln** > **Metrikwarnung hinzufügen**.
3. Wählen Sie eine Metrik, eine Warnungsbedingung (etwa für den Fall, dass eine Metrik während eines Zeitraums einen bestimmten Wert überschreitet) und mindestens eine Benachrichtigung aus.

Sie können auch mithilfe der [REST-API](https://docs.microsoft.com/rest/api/monitor/) eine Warnung nahezu in Echtzeit konfigurieren. Weitere Informationen finden Sie unter [Übersicht über Warnungen](../azure-monitor/platform/alerts-overview.md).

## <a name="batch-diagnostics"></a>Batch-Diagnose

Diagnoseprotokolle enthalten von Azure-Ressourcen ausgegebene Informationen, die die Vorgänge der einzelnen Ressourcen beschreiben. In Batch können Sie die folgenden Protokolle erfassen:

* **Dienstprotokollereignisse**, die vom Azure Batch-Dienst während der Lebensdauer einer einzelnen Batch-Ressource wie eines Pools oder einer Task ausgegeben werden 

* **Metrikprotokolle** auf Kontoebene 

Einstellungen zum Aktivieren der Erfassung von Diagnoseprotokollen sind nicht standardmäßig aktiviert. Aktivieren Sie explizit die Diagnoseeinstellungen für jedes Batch-Konto, das Sie überwachen möchten.

### <a name="log-destinations"></a>Protokollziele

Ein häufiges Szenario ist die Auswahl eines Azure Storage-Kontos als Protokollziel. Erstellen Sie zum Speichern von Protokollen in Azure Storage das Konto, bevor Sie die Erfassung von Protokollen erstellen. Wenn Sie Ihrem Batch-Konto ein Speicherkonto zugewiesen haben, können Sie das Konto als Protokollziel auswählen. 

Andere optionale Ziele für Diagnoseprotokolle:

* Streamen Sie Batch-Diagnoseprotokolle an einen [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs können mit einem beliebigen Echtzeitanalyse-Anbieter Millionen Ereignisse pro Sekunde erfassen und anschließend transformieren und speichern. 

* Senden Sie Diagnoseprotokolle an [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md). Hier können Sie sie analysieren oder zur Analyse in Power BI oder Excel exportieren.

> [!NOTE]
> Beim Speichern oder Verarbeiten von Diagnoseprotokolldaten mit Azure-Diensten fallen unter Umständen zusätzliche Kosten an. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivieren der Erfassung von Batch-Diagnoseprotokollen

1. Klicken Sie im Portal auf **Alle Dienste** > **Batch-Konten** und anschließend auf den Namen Ihres Batch-Kontos.
2. Klicken Sie unter **Überwachung** auf **Diagnoseprotokolle** > **Diagnose aktivieren**.
3. Geben Sie unter **Diagnoseeinstellungen** einen Namen für die Einstellung ein, und wählen Sie ein Protokollziel aus (vorhandenes Storage-Konto, Event Hub oder Azure Monitor-Protokolle). Aktivieren Sie **ServiceLog** oder **AllMetrics** oder beide Optionen.

    Wenn Sie ein Speicherkonto auswählen, legen Sie optional eine Aufbewahrungsrichtlinie fest. Wenn Sie keine Anzahl von Tagen für die Aufbewahrung angeben, werden die Daten für die Lebensdauer des Speicherkontos aufbewahrt.

4. Klicken Sie auf **Speichern**.

    ![Batch-Diagnose](media/batch-diagnostics/diagnostics-portal.png)

Andere Optionen zum Aktivieren der Protokollerfassung: Konfigurieren von Diagnoseeinstellungen mithilfe von Azure Monitor im Portal, Verwenden einer [Resource Manager-Vorlage](../azure-monitor/platform/diagnostic-logs-stream-template.md) oder Verwenden von Azure PowerShell bzw. der Azure-Befehlszeilenschnittstelle. Informationen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../azure-monitor/platform/diagnostic-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Zugreifen auf Diagnoseprotokolle im Speicher

Wenn Sie Batch-Diagnoseprotokolle in einem Speicherkonto archivieren, wird ein Speichercontainer im Speicherkonto erstellt, sobald ein verwandtes Ereignis auftritt. Blobs werden gemäß dem folgenden Benennungsmuster erstellt:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Beispiel:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Jede Blobdatei vom Typ „PT1H.json“ enthält Ereignisse im JSON-Format, die innerhalb der in der Blob-URL angegebenen Stunde (Beispiel: h=12) aufgetreten sind. Während der aktuellen Stunde werden Ereignisse an die Datei „PT1H.json“ angefügt, sobald sie auftreten. Der Minutenwert (m=00) ist immer „00“, da Diagnoseprotokollereignisse stundenweise in einzelne Blobs unterteilt werden. (Alle Zeitangaben sind in UTC.)


Weitere Informationen zum Schema der Diagnoseprotokolle im Speicherkonto finden Sie unter [Archivieren von Azure-Diagnoseprotokollen](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Verwenden Sie zum programmgesteuerten Zugriff auf die Protokolle in Ihrem Speicherkonto die Storage-APIs. 

### <a name="service-log-events"></a>Dienstprotokollereignisse
Azure Batch-Dienstprotokolle (sofern sie erfasst werden) enthalten Ereignisse, die während der Lebensdauer einer einzelnen Batch-Ressource wie eines Pools oder einer Task ausgegeben werden. Jedes von Batch ausgegebene Ereignis wird im JSON-Format protokolliert. Dies ist beispielsweise der Text eines Beispiel für ein **Poolerstellungsereignis**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Der Batch-Dienst gibt derzeit folgende Dienstprotokollereignisse aus. Diese Liste ist unter Umständen nicht vollständig, da seit der letzten Aktualisierung dieses Artikels möglicherweise weitere Ereignisse hinzugefügt wurden.

| **Dienstprotokollereignisse** |
| --- |
| [Erstellung eines Pools](batch-pool-create-event.md) |
| [Start des Löschvorgangs eines Pools](batch-pool-delete-start-event.md) |
| [Abschluss des Löschvorgangs eines Pools](batch-pool-delete-complete-event.md) |
| [Start der Größenänderung eines Pools](batch-pool-resize-start-event.md) |
| [Abschluss der Größenänderung eines Pools](batch-pool-resize-complete-event.md) |
| [Taskstart](batch-task-start-event.md) |
| [Taskabschluss](batch-task-complete-event.md) |
| [Taskfehler](batch-task-fail-event.md) |



## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Batch-APIs und Tools](batch-apis-tools.md), die für die Erstellung von Batch-Lösungen verfügbar sind.
* Weitere Informationen finden Sie unter [Monitor Batch solutions](monitoring-overview.md) (Überwachen von Batch-Lösungen).
