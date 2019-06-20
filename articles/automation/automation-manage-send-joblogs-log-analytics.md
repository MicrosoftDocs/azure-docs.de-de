---
title: Weiterleiten von Azure Automation-Auftragsdaten an Azure Monitor-Protokolle
description: In diesem Artikel wird erläutert, wie Auftragsstatus und Runbookauftrags-Datenströme an Azure Monitor-Protokolle gesendet werden, um zusätzliche Einblicke und Verwaltungsoptionen zu erhalten.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e0f2d3491db24ecbb49c189232dbc7f698e09fb1
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430770"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Weiterleiten von Auftragsstatus und Auftragsdatenströmen von Automation an Azure Monitor-Protokolle

Automation kann Runbookauftragsstatus und Auftragsdatenströme an Ihren Log Analytics-Arbeitsbereich senden. Dieser Prozess beinhaltet nicht die Arbeitsbereichsverknüpfung. Er ist vollkommen unabhängig. Auftragsprotokolle und -streams werden im Azure-Portal oder mit PowerShell für einzelne Aufträge angezeigt, d.h., Sie können einfache Untersuchen durchführen. Mit Azure Monitor-Protokollen können Sie jetzt:

* Gewinnen Sie Einblicke in Ihre Automation-Aufträge.
* Lösen Sie basierend auf Ihrem Runbookauftragsstatus (beispielsweise „Fehler“ oder „Angehalten“) das Senden einer E-Mail oder einer Warnung aus.
* Schreiben Sie erweiterte Abfragen für Ihre Auftragsdatenströme.
* Korrelieren Sie Aufträge über Automation-Konten hinweg.
* Visualisieren Sie Ihren Auftragsverlauf.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Voraussetzungen und Überlegungen zur Bereitstellung

Zum Senden Ihrer Automation-Protokolle an Azure Monitor-Protokolle benötigen Sie:

* Das aktuelle Release von [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)
* Einen Log Analytics-Arbeitsbereich Weitere Informationen finden Sie unter [Erste Schritte mit Azure Monitor-Protokollen](../log-analytics/log-analytics-get-started.md). 
* Die Ressourcen-ID für Ihr Azure Automation-Konto

So ermitteln Sie die Ressourcen-ID für Ihr Azure Automation-Konto:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Führen Sie zum Ermitteln der Ressourcen-ID für Ihren Log Analytics-Arbeitsbereich den folgenden PowerShell-Befehl aus:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Sollten Sie über mehrere Automation-Konten (oder über mehrere Arbeitsbereiche) verfügen, suchen Sie in der Ausgabe der vorherigen Befehle den *Namen*, den Sie konfigurieren müssen, und kopieren Sie den Wert für *ResourceId*.

Wenn Sie den *Namen* Ihres Automation-Kontos suchen müssen, wählen Sie im Azure-Portal auf dem Blatt **Automation-Konto** Ihr Automation-Konto aus, und wählen Sie dann **Alle Einstellungen**. Wählen Sie auf dem Blatt **Alle Einstellungen** unter **Kontoeinstellungen** die Option **Eigenschaften** aus.  Die Werte werden auf dem Blatt **Eigenschaften** angezeigt.<br> ![Automation-Konto – Eigenschaften](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Einrichten der Integration in Azure Monitor-Protokolle

1. Starten Sie auf Ihrem Computer **Windows PowerShell** über den **Startbildschirm**.
2. Führen Sie das folgende PowerShell-Skript aus, und bearbeiten Sie den Wert für `[your resource id]` und `[resource id of the log analytics workspace]` mit den Werten aus dem vorherigen Schritt.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Nach dem Ausführen dieses Skripts kann es eine Stunde dauern, bis in Azure Monitor-Protokolle Datensätze neu geschriebener Auftragsprotokolle oder -streams angezeigt werden.

Um die Protokolle anzuzeigen, führen Sie die folgende Abfrage in der Log Analytics-Protokollsuche durch: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Überprüfen der Konfiguration

Vergewissern Sie sich, dass Ihr Automation-Konto Protokolle an Ihren Log Analytics-Arbeitsbereich sendet. Überprüfen Sie hierzu mithilfe des folgenden PowerShell-Befehls, ob die Diagnose für das Automation-Konto ordnungsgemäß konfiguriert ist:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Überprüfen Sie in der Ausgabe Folgendes:

* Unter *Protokolle* muss *Aktiviert* den Wert *True* haben.
* Der Wert für *WorkspaceId* muss der Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs entsprechen.

## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor

Die Diagnose von Azure Automation erstellt zwei Arten von Datensätzen in Azure Monitor-Protokolle und kennzeichnet sie als **AzureDiagnostics**. In den folgenden Abfragen wird die aktualisierte Abfragesprache für Azure Monitor-Protokolle verwendet. Informationen zu allgemeinen Abfragen zwischen der alten Abfragesprache und der neuen Azure Kusto-Abfragesprache finden Sie unter [Legacy to new Azure Kusto Query Language cheat sheet](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) (Cheat Sheet: Gegenüberstellung von alter Abfragesprache und Azure Kusto-Abfragesprache).

### <a name="job-logs"></a>Auftragsprotokolle

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Ausführungsdatum und -uhrzeit des Runbookauftrags. |
| RunbookName_s |Der Name des Runbooks. |
| Caller_s |Der Benutzer oder das System, von dem der Vorgang initiiert wurde. Mögliche Werte sind entweder eine E-Mail-Adresse oder, bei geplanten Aufträgen, ein System. |
| Tenant_g | GUID, die den Mandanten für den Aufrufer identifiziert. |
| JobId_g |Die GUID, bei der es sich um die ID des Runbookauftrags handelt. |
| ResultType |Der Status des Runbookauftrags. Mögliche Werte:<br>- Neu<br>- Erstellt<br>- Gestartet<br>- Beendet<br>- Ausgesetzt<br>- Fehler<br>- Abgeschlossen |
| Category (Kategorie) | Klassifizierung des Datentyps. Für Automation lautet der Wert „JobLogs“. |
| OperationName | Gibt den Typ des in Azure ausgeführten Vorgangs an. Für Automation lautet der Wert „Job“. |
| Resource | Name des Automation-Kontos |
| SourceSystem | So erfasst Azure Monitor-Protokolle die Daten Immer *Azure* für Azure-Diagnose. |
| ResultDescription |Beschreibt den resultierenden Zustand des Runbookauftrags. Mögliche Werte:<br>- Auftrag gestartet<br>- Fehler beim Ausführen des Auftrags<br>- Auftrag abgeschlossen |
| CorrelationId |Die GUID, bei der es sich um die Korrelations-ID des Runbookauftrags handelt. |
| resourceId |Gibt die Ressourcen-ID des Runbooks für das Azure Automation-Konto an. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Auftragsdatenströme
| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Ausführungsdatum und -uhrzeit des Runbookauftrags. |
| RunbookName_s |Der Name des Runbooks. |
| Caller_s |Der Benutzer oder das System, von dem der Vorgang initiiert wurde. Mögliche Werte sind entweder eine E-Mail-Adresse oder, bei geplanten Aufträgen, ein System. |
| StreamType_s |Der Typ des Auftragsstreams. Mögliche Werte:<br>\- Status<br>- Ausgabe<br>- Warnung<br>- Fehler<br>- Debuggen<br>- Ausführlich |
| Tenant_g | GUID, die den Mandanten für den Aufrufer identifiziert. |
| JobId_g |Die GUID, bei der es sich um die ID des Runbookauftrags handelt. |
| ResultType |Der Status des Runbookauftrags. Mögliche Werte:<br>– In Bearbeitung |
| Category (Kategorie) | Klassifizierung des Datentyps. Für Automation lautet der Wert „JobStreams“. |
| OperationName | Gibt den Typ des in Azure ausgeführten Vorgangs an. Für Automation lautet der Wert „Job“. |
| Resource | Name des Automation-Kontos |
| SourceSystem | So erfasst Azure Monitor-Protokolle die Daten Immer *Azure* für Azure-Diagnose. |
| ResultDescription |Enthält den Ausgabedatenstrom des Runbooks. |
| CorrelationId |Die GUID, bei der es sich um die Korrelations-ID des Runbookauftrags handelt. |
| resourceId |Gibt die Ressourcen-ID des Runbooks für das Azure Automation-Konto an. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Anzeigen von Automation-Protokollen in Azure Monitor-Protokolle

Nachdem Sie damit begonnen haben, Ihre Automation-Auftragsprotokolle an Azure Monitor-Protokolle zu senden, sehen wir uns nun an, wie Sie diese Protokolle in Azure Monitor-Protokolle verwenden können.

Führen Sie die folgende Abfrage aus, um die Protokolle anzuzeigen: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Senden einer E-Mail, wenn ein Runbookauftrag mit einem Fehler abgebrochen oder angehalten wird
Einer der häufigsten Kundenwünsche ist die Möglichkeit, eine E-Mail oder SMS zu senden, wenn bei einem Runbookauftrag ein Problem auftritt.   

Zum Erstellen einer Warnungsregel erstellen Sie zunächst eine Protokollsuche für die Datensätze des Runbookauftrags, die die Warnung auslösen sollen. Klicken Sie auf die Schaltfläche **Warnung**, um die Warnungsregel zu erstellen und zu konfigurieren.

1. Klicken Sie auf der Seite „Übersicht“ für den Log Analytics-Arbeitsbereich auf **Protokolle anzeigen**.
2. Erstellen Sie eine Protokollsuchabfrage für Ihre Warnung, indem Sie folgenden Text in das Abfragefeld eingeben: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` Sie können auch eine Gruppierung nach RunbookName vornehmen, indem Sie Folgendes eingeben: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Wenn Sie Protokolle von mehreren Automation-Konten oder Abonnements in Ihrem Arbeitsbereich eingerichtet haben, können Sie Ihre Warnungen nach Abonnement und Automation-Konto gruppieren. Der Name des Automation-Kontos kann dem Ressourcenfeld in der JobLogs-Suche entnommen werden.
3. Klicken Sie oben auf der Seite auf **Neue Warnungsregel**, um den Bildschirm **Regel erstellen** zu öffnen. Weitere Informationen zu den Konfigurationsoptionen für Warnungen finden Sie unter [Protokollwarnungen in Azure Monitor – Warnungen](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Durchführen einer Suche nach allen Aufträgen, die mit Fehlern abgeschlossen wurden
Neben dem Erstellen von Warnungen für Fehler können Sie auch ermitteln, wenn ein Runbook-Auftrag einen Fehler ohne Abbruch aufweist. In diesen Fällen erzeugt PowerShell einen Fehlerdatenstrom, die Fehler ohne Abbruch führen jedoch nicht dazu, dass der Auftrag unterbrochen wird oder fehlschlägt.    

1. Klicken Sie in Ihrem Log Analytics-Arbeitsbereich auf **Protokolle**.
2. Geben Sie in das Abfragefeld `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` ein, und klicken Sie dann auf die Schaltfläche **Suchen**.

### <a name="view-job-streams-for-a-job"></a>Anzeigen von Auftragsdatenströmen für einen Auftrag
Wenn Sie einen Auftrag debuggen, sollten Sie ggf. auch einen Blick in die Auftragsdatenströme werfen. Mit der folgenden Abfrage werden alle Datenströme für einen einzelnen Auftrag mit der GUID „2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0“ abgerufen:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Anzeigen des Auftragsstatusverlaufs
Abschließend möchten Sie möglicherweise Ihren Auftragsverlauf visualisieren. Sie können die folgende Abfrage verwenden, um nach dem Statusverlauf Ihrer Aufträge zu suchen.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Diagramm zum Auftragsstatusverlauf in Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Entfernen von Diagnoseeinstellungen

Führen Sie zum Entfernen der Diagnoseeinstellung aus dem Automation-Konto die folgenden Befehle aus:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Zusammenfassung

Sie erhalten einen besseren Einblick in den Status Ihrer Automation-Aufträge, wenn Sie den Status und die Streamdaten Ihres Automation-Auftrags an Azure Monitor-Protokolle senden und:
+ Warnungen einrichten, um bei Problemen benachrichtigt zu werden.
+ Benutzerdefinierte Ansichten und Suchabfragen zum Anzeigen von Runbook-Ergebnissen, Runbook-Auftragsstatus und anderer wichtiger Schlüsselindikatoren oder Metriken verwenden.  

Azure Monitor-Protokolle bietet eine höhere operative Transparenz für Ihre Automation-Aufträge, sodass schneller auf Vorfälle reagiert werden kann.  

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Erstellen verschiedener Suchabfragen und zur Überprüfung der Automation-Auftragsprotokolle mit Azure Monitor-Protokolle finden Sie unter [Protokollsuchen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-searches.md).
* Unter [Runbookausgabe und -meldungen](automation-runbook-output-and-messages.md) erfahren Sie, wie Sie die Ausgabe und Fehlermeldungen von Runbooks erstellen und abrufen.
* Weitere Informationen zum Ausführen von Runbooks, zum Überwachen von Runbookaufträgen sowie andere technische Details finden Sie unter [Verfolgen eines Runbookauftrags](automation-runbook-execution.md).
* Weitere Informationen zu Azure Monitor-Protokolle und Datensammlungsquellen finden Sie unter [Sammeln von Azure Storage-Daten in Azure Monitor-Protokolle – Übersicht](../azure-monitor/platform/collect-azure-metrics-logs.md).

