---
title: Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle
description: In diesem Artikel wird erläutert, wie Auftragsstatus und Runbookauftragsstreams an Azure Monitor-Protokolle gesendet werden.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 2e7e798967541748b5572994d48cb5bdf7474cb1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182868"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle

Azure Automation kann Runbookauftragsstatus und Auftragsdatenströme an Ihren Log Analytics-Arbeitsbereich senden. Dieser Prozess beinhaltet nicht die Arbeitsbereichsverknüpfung. Er ist vollkommen unabhängig. Auftragsprotokolle und -streams werden im Azure-Portal oder mit PowerShell für einzelne Aufträge angezeigt, d.h., Sie können einfache Untersuchen durchführen. Mit Azure Monitor-Protokollen können Sie jetzt:

* Gewinnen Sie Erkenntnisse über den Status Ihrer Automation-Aufträge.
* Lösen Sie basierend auf Ihrem Runbookauftragsstatus (beispielsweise „Fehler“ oder „Angehalten“) das Senden einer E-Mail oder einer Warnung aus.
* Schreiben Sie erweiterte Abfragen für Ihre Auftragsdatenströme.
* Korrelieren Sie Aufträge über Automation-Konten hinweg.
* Benutzerdefinierte Ansichten und Suchabfragen zum Visualisieren von Runbookergebnissen, Runbookauftragsstatus und anderer wichtiger Schlüsselindikatoren oder Metriken verwenden

## <a name="prerequisites"></a>Voraussetzungen

Zum Senden Ihrer Automation-Protokolle an Azure Monitor-Protokolle benötigen Sie:

* Das aktuelle Release von [Azure PowerShell](/powershell/azure/)

* Einen Log Analytics-Arbeitsbereich und seine Ressourcen-ID Weitere Informationen finden Sie unter [Erste Schritte mit Azure Monitor-Protokolle](../azure-monitor/overview.md).

* Die Ressourcen-ID für Ihr Azure Automation-Konto

## <a name="how-to-find-resource-ids"></a>Ermitteln von Ressourcen-IDs

1. Verwenden Sie den folgenden Befehl, um die Ressourcen-ID für Ihr Azure Automation-Konto zu ermitteln:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Kopieren Sie den Wert für **ResourceID**.

3. Verwenden Sie den folgenden Befehl, um die Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs zu ermitteln:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Kopieren Sie den Wert für **ResourceID**.

Um Ergebnisse aus einer bestimmten Ressourcengruppe zurückzugeben, schließen Sie den Parameter `-ResourceGroupName` ein. Weitere Informationen finden Sie unter [Get-AzResource](/powershell/module/az.resources/get-azresource).

Wenn Sie über mehrere Automation-Konten oder einen Arbeitsbereich in der Ausgabe der vorherigen Befehle verfügen, können Sie folgendermaßen den Namen und andere zugehörige Eigenschaften ermitteln, die Teil der vollständigen Ressourcen-ID Ihres Automation-Kontos sind:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Azure-Portal auf der Seite **Automation-Konten** Ihr Automation-Konto aus.
1. Wählen Sie auf der Seite des ausgewählten Automation-Kontos unter **Kontoeinstellungen** die Option **Eigenschaften** aus.
1. Beachten Sie auf der Seite **Eigenschaften** die unten aufgeführten Details.

    ![Automation-Konto – Eigenschaften](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Konfigurieren von Diagnoseeinstellungen

Automatisierungsdiagnoseeinstellungen unterstützen die Weiterleitung der folgenden Plattformprotokolle und Metrikdaten:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metriken: Aufträge gesamt, Updateausführungen von Bereitstellungscomputern gesamt, Updatebereitstellungsausführungen gesamt

Um mit dem Senden Ihrer Automatisierungsprotokolle an Azure Monitor-Protokolle zu beginnen, lesen Sie [Erstellen von Diagnoseeinstellungen](../azure-monitor/platform/diagnostic-settings.md), um die Funktion und Methoden zu verstehen, die zur Konfiguration von Diagnoseeinstellungen zum Senden von Plattformprotokollen zur Verfügung stehen.

## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor

Die Diagnose von Azure Automation erstellt zwei Typen von Datensätzen in Azure Monitor-Protokollen, gekennzeichnet als `AzureDiagnostics`. Die Tabellen in den nächsten Abschnitten sind Beispiele für Datensätze, die von Azure Automation generiert werden, und für die Datentypen, die in Protokollsuchergebnissen angezeigt werden.

### <a name="job-logs"></a>Auftragsprotokolle

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Ausführungsdatum und -uhrzeit des Runbookauftrags. |
| RunbookName_s |Der Name des Runbooks. |
| Caller_s |Der Aufrufer, der den Vorgang initiiert hat. Mögliche Werte sind entweder eine E-Mail-Adresse oder, bei geplanten Aufträgen, ein System. |
| Tenant_g | Die GUID, die den Mandanten für den Aufrufer identifiziert. |
| JobId_g |Die GUID, der den Runbookauftrag identifiziert. |
| ResultType |Der Status des Runbookauftrags. Mögliche Werte:<br>- Neu<br>- Erstellt<br>- Gestartet<br>- Beendet<br>- Ausgesetzt<br>- Fehler<br>- Abgeschlossen |
| Category | Klassifizierung des Datentyps. Für Automation lautet der Wert „JobLogs“. |
| Vorgangsname | Der Typ des in Azure ausgeführten Vorgangs. Für Automation lautet der Wert „Job“. |
| Resource | Den Namen des Automation-Kontos. |
| SourceSystem | Das System, das Azure Monitor-Protokolle verwenden, um die Daten zu erfassen. Für Azure-Diagnose ist der Wert immer „Azure“. |
| ResultDescription |Der Ergebniszustand des Runbookauftrags. Mögliche Werte:<br>- Auftrag gestartet<br>- Fehler beim Ausführen des Auftrags<br>- Auftrag abgeschlossen |
| CorrelationId |Die Korrelations-GUID des Runbookauftrags. |
| resourceId |Die Ressourcen-ID für das Azure Automation-Konto des Runbooks. |
| SubscriptionId | Die Azure-Abonnement-GUID für das Automation-Konto. |
| ResourceGroup | Der Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | Der Ressourcenanbieter. Der Wert lautet „MICROSOFT.AUTOMATION“. |
| ResourceType | Der Ressourcentyp. Der Wert lautet „AUTOMATIONACCOUNTS“. |

### <a name="job-streams"></a>Auftragsdatenströme
| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Ausführungsdatum und -uhrzeit des Runbookauftrags. |
| RunbookName_s |Der Name des Runbooks. |
| Caller_s |Der Aufrufer, der den Vorgang initiiert hat. Mögliche Werte sind entweder eine E-Mail-Adresse oder, bei geplanten Aufträgen, ein System. |
| StreamType_s |Der Typ des Auftragsstreams. Mögliche Werte:<br>\- Status<br>- Ausgabe<br>- Warnung<br>- Fehler<br>- Debuggen<br>- Ausführlich |
| Tenant_g | Die GUID, die den Mandanten für den Aufrufer identifiziert. |
| JobId_g |Die GUID, der den Runbookauftrag identifiziert. |
| ResultType |Der Status des Runbookauftrags. Mögliche Werte:<br>– In Bearbeitung |
| Category | Klassifizierung des Datentyps. Für Automation lautet der Wert „JobStreams“. |
| Vorgangsname | Der Typ des in Azure ausgeführten Vorgangs. Für Automation lautet der Wert „Job“. |
| Resource | Der Namen des Automation-Kontos. |
| SourceSystem | Das System, das Azure Monitor-Protokolle verwenden, um die Daten zu erfassen. Für Azure-Diagnose ist der Wert immer „Azure“. |
| ResultDescription |Die Beschreibung, die den Ausgabedatenstrom des Runbooks enthält. |
| CorrelationId |Die Korrelations-GUID des Runbookauftrags. |
| resourceId |Die Ressourcen-ID für das Azure Automation-Konto des Runbooks. |
| SubscriptionId | Die Azure-Abonnement-GUID für das Automation-Konto. |
| ResourceGroup | Der Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | Der Ressourcenanbieter. Der Wert lautet „MICROSOFT.AUTOMATION“. |
| ResourceType | Der Ressourcentyp. Der Wert lautet „AUTOMATIONACCOUNTS“. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Anzeigen von Automation-Protokollen in Azure Monitor-Protokollen

Nachdem Sie damit begonnen haben, Ihre Automation-Auftragsstreams und -protokolle an Azure Monitor-Protokolle zu senden, sehen wir uns nun an, wie Sie diese Protokolle in Azure Monitor-Protokollen verwenden können.

Führen Sie die folgende Abfrage aus, um die Protokolle anzuzeigen: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Senden einer E-Mail, wenn ein Runbookauftrag mit einem Fehler abgebrochen oder angehalten wird

Die folgenden Schritte zeigen, wie Sie Warnungen in Azure Monitor einrichten, damit Sie benachrichtigt werden, wenn bei einem Runbookauftrag ein Fehler auftritt.

Zum Erstellen einer Warnungsregel erstellen Sie zunächst eine Protokollsuche für die Datensätze des Runbookauftrags, die die Warnung auslösen sollen. Klicken Sie auf die Schaltfläche **Warnung**, um die Warnungsregel zu erstellen und zu konfigurieren.

1. Klicken Sie auf der Seite „Übersicht“ für den Log Analytics-Arbeitsbereich auf **Protokolle anzeigen**.

2. Erstellen Sie eine Protokollsuchabfrage für Ihre Warnung, indem Sie folgenden Text in das Abfragefeld eingeben: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Sie können auch eine Gruppierung nach Runbookname vornehmen, indem Sie Folgendes eingeben: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Wenn Sie Protokolle von mehreren Automation-Konten oder Abonnements in Ihrem Arbeitsbereich eingerichtet haben, können Sie Ihre Warnungen nach Abonnement und Automation-Konto gruppieren. Der Name des Automation-Kontos kann dem `Resource`-Feld in der `JobLogs`-Suche entnommen werden.

3. Klicken Sie oben auf der Seite auf **Neue Warnungsregel**, um den Bildschirm **Regel erstellen** zu öffnen. Weitere Informationen zu den Konfigurationsoptionen für Warnungen finden Sie unter [Protokollwarnungen in Azure Monitor – Warnungen](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Durchführen einer Suche nach allen Aufträgen, die mit Fehlern abgeschlossen wurden

Neben dem Erstellen von Warnungen für Fehler können Sie auch ermitteln, wenn ein Runbook-Auftrag einen Fehler ohne Abbruch aufweist. In diesen Fällen erzeugt PowerShell einen Fehlerdatenstrom, die Fehler ohne Abbruch führen jedoch nicht dazu, dass der Auftrag unterbrochen wird oder fehlschlägt.

1. Klicken Sie in Ihrem Log Analytics-Arbeitsbereich auf **Protokolle**.

2. Geben Sie in das Abfragefeld `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` ein.

3. Klicken Sie auf die Schaltfläche **Suchen**.

### <a name="view-job-streams-for-a-job"></a>Anzeigen von Auftragsdatenströmen für einen Auftrag

Wenn Sie einen Auftrag debuggen, sollten Sie ggf. auch einen Blick in die Auftragsdatenströme werfen. Mit der folgenden Abfrage werden alle Datenströme für einen einzelnen Auftrag mit der GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` abgerufen:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Anzeigen des Auftragsstatusverlaufs

Abschließend sollten Sie Ihren Auftragsverlauf visualisieren. Sie können die folgende Abfrage verwenden, um nach dem Statusverlauf Ihrer Aufträge zu suchen.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Diagramm zum Auftragsstatusverlauf in Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtern der Auftragsstatusausgabe, die in eine JSON-Objekt konvertiert wurde

Kürzlich haben wir das Verhalten geändert, wie die Automation-Protokolldaten in die `AzureDiagnostics`-Tabelle im Log Analytics-Dienst geschrieben werden, in der die JSON-Eigenschaften nicht mehr in separate Felder aufgeschlüsselt werden. Wenn Sie das Runbook so konfiguriert haben, dass Objekte im Ausgabestream im JSON-Format als separate Spalten formatiert werden, müssen Sie Ihre Abfragen so neu konfigurieren, dass dieses Feld in ein JSON-Objekt analysiert wird, um auf diese Eigenschaften zuzugreifen. Dies erfolgt mithilfe von [parsejson](/azure/data-explorer/kusto/query/samples?pivots=#parsejson), um auf ein bestimmtes JSON-Element in einem bekannten Pfad zuzugreifen.

Ein Runbook formatiert z. B. die *ResultDescription*-Eigenschaft im Ausgabestream im JSON-Format mit mehreren Feldern. Um nach dem Status Ihrer Aufträge zu suchen, die sich in einem fehlerhaften Zustand befinden, der in einem Feld namens **Status** angegeben wird, verwenden Sie diese Beispielabfrage, um *ResultDescription* mit einem Status von **Failed** zu durchsuchen:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Auftragsstatusverlauf im JSON-Format von Log Analytics](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen von Suchabfragen und zur Überprüfung der Automation-Auftragsprotokolle mit Azure Monitor-Protokolle finden Sie unter [Protokollsuchen in Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md).
* Unter [Runbookausgabe und -meldungen in Azure Automation](automation-runbook-output-and-messages.md) erfahren Sie, wie Sie die Ausgabe und Fehlermeldungen von Runbooks erstellen und abrufen.
* Weitere Informationen zum Ausführen von Runbooks und zum Überwachen von Runbookaufträgen sowie andere technische Details finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).
* Weitere Informationen zu Azure Monitor-Protokolle und Datensammlungsquellen finden Sie unter [Sammeln von Azure Storage-Daten in Azure Monitor-Protokolle – Übersicht](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* Informationen zur Problembehandlung in Log Analytics finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).