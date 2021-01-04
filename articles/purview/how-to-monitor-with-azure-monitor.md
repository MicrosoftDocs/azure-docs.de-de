---
title: Überwachen von Azure Purview
description: Erfahren Sie, wie Sie Azure Purview-Metriken, -Warnungen und -Diagnoseeinstellungen mit Azure Monitor konfigurieren.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 2c21f84b9a10db504afb8ead67ae479518a0afba
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603041"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure Purview-Metriken in Azure Monitor

In diesem Artikel wird beschrieben, wie Sie Metriken, Warnungen und Diagnoseeinstellungen für Azure Purview mithilfe von Azure Monitor konfigurieren.

## <a name="monitor-azure-purview"></a>Überwachen von Azure Purview

Azure Purview-Administratoren können mithilfe von Azure Monitor den Betriebsstatus des Purview-Kontos nachverfolgen. Es werden Metriken gesammelt, um Ihnen Datenpunkte zur Verfügung zu stellen, mit denen Sie potenzielle Probleme nachverfolgen, Probleme beheben und die Zuverlässigkeit des Purview-Kontos verbessern können. Die Metriken werden für Ereignisse in Azure Purview an Azure Monitor gesendet.

## <a name="aggregated-metrics"></a>Aggregierte Metriken

Auf die Metriken kann über das Azure-Portal für ein Purview-Konto zugegriffen werden. Der Zugriff auf die Metriken wird durch die Rollenzuweisung des Purview-Kontos gesteuert. Benutzer müssen der Rolle „Überwachungsleser“ in Azure Purview zugewiesen sein, um Metriken anzeigen zu können. Weitere Informationen zu den Zugriffsebenen der einzelnen Rollen finden Sie unter den [Berechtigungen für die Rolle „Überwachungsleser“](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles).

Die Person, die das Purview-Konto erstellt hat, erhält automatisch die Berechtigungen zum Anzeigen von Metriken. Wenn Benutzer andere Metriken anzeigen möchten, fügen Sie sie mithilfe der folgenden Schritte der Rolle **Überwachungsleser** hinzu:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Hinzufügen eines Benutzers zur Rolle „Überwachungsleser“

Zum Hinzufügen eines Benutzers zur Rolle **Überwachungsleser** kann der Besitzer des Purview-Kontos oder des Abonnements die folgenden Schritte ausführen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und suchen Sie den Namen des Azure Purview-Kontos.

2. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Screenshot des Zugriffs auf IAM":::

3. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Screenshot des Hinzufügens einer Rollenzuweisung":::

4. Wählen Sie die Rolle **Überwachungsleser** aus, und legen Sie „Zugriff zuweisen zu“ auf **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** fest. Weisen Sie außerdem dem AAD-Konto Zugriff auf die Metriken zu.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Screenshot des Hinzufügens der Rolle „Überwachungsleser“":::

## <a name="metrics-visualization"></a>Metrikvisualisierung

Benutzer der Rolle **Überwachungsleser** können die aggregierten Metriken und Diagnoseprotokolle anzeigen, die an Azure Monitor gesendet werden. Die Metriken werden im Azure-Portal für das entsprechende Purview-Konto aufgelistet. Wählen Sie im Azure-Portal den Abschnitt „Metriken“ aus, um die Liste aller verfügbaren Metriken anzuzeigen.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Screenshot des Abschnitts mit den verfügbaren Purview-Metriken" lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure Purview-Benutzer können auch direkt vom Verwaltungscenter des Azure Purview-Kontos auf die Seite „Metriken“ zugreifen. Wählen Sie auf der Hauptseite des Purview-Verwaltungscenters Azure Monitor aus, um das Azure-Portal zu starten.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Screenshot des Startens von Purview-Metriken aus dem Verwaltungscenter" lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Verfügbare Metriken

Informationen zur Verwendung des Abschnitts „Metriken“ im Azure-Portal finden Sie in den folgenden beiden Dokumenten: [Erste Schritte mit dem Azure-Metrik-Explorer](../azure-monitor/platform/metrics-getting-started.md) und [Erweiterte Funktionen von Azure Metrik-Explorer](../azure-monitor/platform/metrics-charts.md).

In der folgenden Tabelle finden Sie eine Liste der verfügbaren Metriken, die im Azure-Portal untersucht werden können:

| Metrikname | Metriknamespace | Aggregationstyp | BESCHREIBUNG |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Überprüfung abgebrochen | Automatisierte Überprüfung | SUM <br> Anzahl | Aggregat der abgebrochenen Überprüfungen der Datenquelle im Zeitraum |
| Überprüfung abgeschlossen | Automatisierte Überprüfung | SUM <br> Anzahl | Aggregat der abgeschlossenen Überprüfungen der Datenquellen im Zeitraum |
| Fehler bei der Überprüfung | Automatisierte Überprüfung | SUM <br> Anzahl | Aggregat der fehlerhaften Überprüfungen der Datenquelle im Zeitraum |
| Überprüfungsdauer | Automatisierte Überprüfung | Min <br> Max <br> SUM <br> Avg | Aggregat der Gesamtzeit für Überprüfungen im Zeitraum |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Diagnoseprotokolle im Azure Storage-Konto

Die Rohdaten der Telemetrieereignisse werden an Azure Monitor ausgegeben. Die Ereignisse können zur weiteren Analyse in einem Kundenspeicherkonto Ihrer Wahl protokolliert werden. Das Exportieren von Protokollen erfolgt über die Diagnoseeinstellungen für das Purview-Konto im Azure-Portal.

Führen Sie die Schritte zum Erstellen einer Diagnoseeinstellung für Ihr Azure Purview-Konto aus.

1. Erstellen Sie eine neue Diagnoseeinstellung zum Sammeln von Plattformprotokollen und -metriken. Nutzen Sie dazu folgenden Artikel: [Erstellen Sie Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../azure-monitor/platform/diagnostic-settings.md). Wählen Sie als Ziel nur das Azure Storage-Konto aus.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Screenshot der Erstellung eines Diagnoseprotokolls" lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Protokollieren Sie die Ereignisse in einem Speicherkonto. Zum Archivieren der Diagnoseprotokolle wird ein dediziertes Speicherkonto empfohlen. Befolgen Sie dazu die Anweisungen im Artikel [ Erstellen eines Speicherkontos](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Screenshot des Zuweisens eines Speicherkontos für das Diagnoseprotokoll" lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Es kann bis zu 15 Minuten dauern, bis die ersten Protokolle im neu erstellten Speicherkonto empfangen werden. [Informieren Sie sich über die Datenaufbewahrung und das Schema von Ressourcenprotokollen in Azure Storage-Konten.](../azure-monitor/platform/resource-logs-collect-storage.md) Nachdem die Diagnoseprotokolle konfiguriert wurden, werden die Ereignisse an das Speicherkonto übertragen.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

Das Ereignis verfolgt den Lebenszyklus der Überprüfung nach. Ein Überprüfungsvorgang durchläuft eine Sequenz von Zuständen: von „In Warteschlange“ über „Wird ausgeführt“ bis zu einem der abschließenden Zustände „Erfolgreich“, „Fehler“ oder „Abgebrochen“. Bei jeder Zustandsänderung wird ein Ereignis protokolliert. Das Schema des Ereignisses weist die folgenden Eigenschaften auf.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Ein Beispielprotokoll für eine Ereignisinstanz wird im folgenden Abschnitt gezeigt.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Nächste Schritte

[Anzeigen von Ressourcenerkenntnissen](asset-insights.md)
