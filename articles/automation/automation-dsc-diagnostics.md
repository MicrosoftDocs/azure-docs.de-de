---
title: Weiterleiten von Berichtsdaten von Azure Automation State Configuration an Azure Monitor-Protokolle
description: Dieser Artikel beschreibt, wie DSC-Berichtsdaten (Desired State Configuration) von Azure Automation State Configuration an Azure Monitor-Protokolle gesendet werden, um zusätzliche Erkenntnisse zu gewinnen und Verwaltungsoptionen zu erhalten.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 50779f8a37713bda8b27c1cfd2ca37eed4edbd11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054716"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Weiterleiten von Berichtsdaten von Azure Automation State Configuration an Azure Monitor-Protokolle

Azure Automation State Configuration behält den Knotenstatus 30 Tage lang bei.
Sie können die Knotenstatusdaten an Ihren Log Analytics-Arbeitsbereich senden, wenn Sie diese Daten für einen längeren Zeitraum aufbewahren möchten.
Der Konformitätsstatus kann im Azure-Portal oder mit PowerShell angezeigt werden, und zwar für Knoten und einzelne DSC-Ressourcen in Knotenkonfigurationen.
Mit Azure Monitor-Protokolle können Sie jetzt:

- Abrufen von Konformitätsinformationen für verwaltete Knoten und einzelne Ressourcen
- Auslösen einer E-Mail oder Warnung basierend auf dem Konformitätsstatus
- Schreiben erweiterter Abfragen für Ihre verwalteten Knoten
- Korrelieren des Konformitätsstatus über Automation-Konten
- Visualisieren des Konformitätsverlaufs von Knoten über einen Zeitraum

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Senden von Automation State Configuration-Berichten an Azure Monitor-Protokolle benötigen Sie Folgendes:

- Die [Azure PowerShell](/powershell/azure/overview)-Version von November 2016 (v2.3.0) oder höher.
- Ein Azure Automation-Konto. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Automation](automation-offering-get-started.md).
- Einen Log Analytics-Arbeitsbereich mit dem Dienstangebot **Automation & Control**. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Monitor-Protokolle](../log-analytics/log-analytics-get-started.md).
- Mindestens einen Azure Automation DSC-Knoten. Weitere Informationen finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Einrichten der Integration in Azure Monitor-Protokolle

Zum Importieren von Daten aus Azure Automation DSC in Azure Monitor-Protokolle müssen Sie die folgenden Schritte ausführen:

1. Melden Sie sich in PowerShell bei Ihrem Azure-Konto an. Weitere Informationen finden Sie unter [Anmelden mit Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Rufen Sie die _ResourceId_ Ihres Automation-Kontos ab, indem Sie den folgenden PowerShell-Befehl ausführen: (Wenn Sie über mehrere Automation-Konten verfügen, wählen Sie die _ResourceID_ für das Konto aus, das Sie konfigurieren möchten.)

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Rufen Sie die _ResourceId_ Ihres Log Analytics-Arbeitsbereichs ab, indem Sie den folgenden PowerShell-Befehl ausführen: (Wenn Sie über mehrere Arbeitsbereiche verfügen, wählen Sie die _ResourceID_ für den Arbeitsbereich aus, den Sie konfigurieren möchten.)

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Führen Sie den folgenden PowerShell-Befehl aus, und ersetzen Sie `<AutomationResourceId>` und `<WorkspaceResourceId>` durch die _ResourceId_-Werte aus den vorherigen Schritten:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Wenn Sie das Importieren von Daten aus Azure Automation State Configuration in Azure Monitor-Protokolle beenden möchten, führen Sie den folgenden PowerShell-Befehl aus:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Anzeigen der DSC-Protokolle

Nachdem Sie die Integration in Azure Monitor-Protokolle für Ihre Automation State Configuration-Daten eingerichtet haben, wird in Ihrem Automation-Konto auf dem Blatt **DSC-Knoten** die Schaltfläche **Protokollsuche** angezeigt. Klicken Sie auf die Schaltfläche **Protokollsuche**, um die Protokolle für DSC-Knotendaten anzuzeigen.

![Schaltfläche „Protokollsuche“](media/automation-dsc-diagnostics/log-search-button.png)

Das Blatt **Protokollsuche** wird geöffnet. Dort wird ein Vorgang **DscNodeStatusData** für jeden DSC-Knoten und ein Vorgang **DscResourceStatusData** für jede [DSC-Ressource](/powershell/dsc/resources) angezeigt, die in der Knotenkonfiguration aufgerufen wird.

Der Vorgang **DscResourceStatusData** enthält Fehlerinformationen für fehlerhafte DSC-Ressourcen.

Klicken Sie auf jeden Vorgang in der Liste, um die Daten für diesen Vorgang anzuzeigen.

Sie können die Protokolle auch anzeigen, indem Sie in Azure Monitor-Protokolle eine Suche durchführen.
Weitere Informationen finden Sie unter [Suchen von Daten mithilfe der Protokollsuche](../log-analytics/log-analytics-log-searches.md).
Geben Sie die folgende Abfrage ein, um die DSC-Protokolle zu suchen: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Sie können die Abfrage auch anhand des Vorgangsnamens eingrenzen. Beispiel: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Senden einer E-Mail bei einer fehlgeschlagenen DSC-Konformitätsprüfung

Eine der häufigsten Anfragen unserer Kunden betrifft die Möglichkeit, eine E-Mail oder Textnachricht zu senden, wenn bei einer DSC-Konfiguration ein Problem auftritt.

Um eine Warnungsregel zu erstellen, erstellen Sie zuerst eine Protokollsuche für die DSC-Berichtsdatensätze, die die Warnung aufrufen sollen. Klicken Sie auf die Schaltfläche **+ Neue Warnungsregel**, um die Warnungsregel zu erstellen und zu konfigurieren.

1. Klicken Sie auf der Seite „Übersicht“ für den Log Analytics-Arbeitsbereich auf **Protokolle**.
1. Erstellen Sie eine Protokollsuchabfrage für Ihre Warnung, indem Sie folgenden Text in das Abfragefeld eingeben: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`.

   Wenn Sie Protokolle von mehreren Automation-Konten oder Abonnements in Ihrem Arbeitsbereich eingerichtet haben, können Sie Ihre Warnungen nach Abonnement oder Automation-Konto gruppieren.  
   Der Name des Automation-Kontos kann vom Ressourcenfeld in der DscNodeStatusData-Suche abgeleitet werden.  
1. Klicken Sie oben auf der Seite auf **Neue Warnungsregel**, um den Bildschirm **Regel erstellen** zu öffnen. Weitere Informationen zu den Konfigurationsoptionen für Warnungen finden Sie unter [Erstellen von Warnungsregeln](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Suchen von Fehlern bei DSC-Ressourcen in allen Knoten

Ein Vorteil der Verwendung von Azure Monitor-Protokolle ist, dass Sie nach Fehlern bei der Überprüfung der Knoten suchen können.
So finden Sie alle Instanzen von DSC-Ressourcen mit Fehlern

1. Klicken Sie auf der Seite „Übersicht“ für den Log Analytics-Arbeitsbereich auf **Protokolle**.
1. Erstellen Sie eine Protokollsuchabfrage für Ihre Warnung, indem Sie folgenden Text in das Abfragefeld eingeben: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`.

### <a name="view-historical-dsc-node-status"></a>Anzeigen von Verlaufsdaten zum DSC-Knotenstatus

Abschließend möchten Sie möglicherweise den Statusverlauf Ihre DSC-Knotens visualisieren.  
Sie können die folgende Abfrage verwenden, um nach dem Statusverlauf Ihres DSC-Knotens zu suchen.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Dadurch wird ein Diagramm mit dem Knotenstatus über einen Zeitraum angezeigt.

## <a name="azure-monitor-logs-records"></a>Datensätze in Azure Monitor-Protokolle

Die Diagnose von Azure Automation erstellt zwei Kategorien von Datensätzen in Azure Monitor-Protokolle.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Datum und Uhrzeit der Durchführung der Konformitätsprüfung. |
| OperationName |DscNodeStatusData |
| ResultType |Gibt an, ob der Knoten konform ist |
| NodeName_s |Der Name des verwalteten Knotens. |
| NodeComplianceStatus_s |Gibt an, ob der Knoten konform ist |
| DscReportStatus |Gibt an, ob die Konformitätsüberprüfung erfolgreich ausgeführt wurde |
| ConfigurationMode | Gibt an, wie die Konfiguration auf den Knoten angewendet wird. Mögliche Werte sind __ApplyOnly__, __ApplyandMonitior__ und __ApplyandAutoCorrect__. <ul><li>__ApplyOnly__: DSC wendet die Konfiguration an und führt nur dann weitere Schritte durch, wenn eine neue Konfiguration per Push an den Zielknoten übertragen oder eine neue Konfiguration von einem Server abgerufen wird. Nach der ersten Anwendung einer neuen Konfiguration führt DSC keine Überprüfung auf Abweichungen von einem zuvor konfigurierten Zustand durch. DSC versucht, die Konfiguration anzuwenden, bis der Vorgang erfolgreich abgeschlossen wurde und bevor __ApplyOnly__ in Kraft tritt. </li><li> __ApplyAndMonitor__: Dies ist der Standardwert. Der LCM wendet alle neuen Konfigurationen an. Nach der ersten Anwendung einer neuen Konfiguration meldet DSC Abweichungen in Protokollen, wenn der Zielknoten vom gewünschten Zustand abweicht. DSC versucht, die Konfiguration anzuwenden, bis der Vorgang erfolgreich abgeschlossen wurde und bevor __ApplyAndMonitor__ in Kraft tritt.</li><li>__ApplyAndAutoCorrect__: DSC wendet alle neuen Konfigurationen an. Nach der ersten Anwendung einer neuen Konfiguration meldet DSC Abweichungen in Protokollen, wenn der Zielknoten vom gewünschten Zustand abweicht, und wendet dann die aktuelle Konfiguration erneut an.</li></ul> |
| HostName_s | Der Name des verwalteten Knotens. |
| IPAddress | Die IPv4-Adresse des verwalteten Knotens. |
| Category (Kategorie) | DscNodeStatus |
| Resource | Der Name des Azure Automation-Kontos. |
| Tenant_g | GUID, die den Mandanten für den Aufrufer identifiziert. |
| NodeId_g |Eindeutiger Bezeichner (GUID), der den verwalteten Knoten identifiziert. |
| DscReportId_g |Eindeutiger Bezeichner (GUID), der den Bericht identifiziert. |
| LastSeenTime_t |Datum und Uhrzeit der letzten Anzeige des Berichts. |
| ReportStartTime_t |Datum und Uhrzeit des Berichtsstarts. |
| ReportEndTime_t |Datum und Uhrzeit des Berichtsabschlusses. |
| NumberOfResources_d |Die Anzahl der DSC-Ressourcen, die bei der Anwendung der Konfiguration auf den Knoten abgerufen wurden. |
| SourceSystem | So erfasst Azure Monitor-Protokolle die Daten Immer *Azure* für Azure-Diagnose. |
| ResourceId |Gibt das Azure Automation-Konto an. |
| ResultDescription | Die Beschreibung für diesen Vorgang. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Eindeutiger Bezeichner (GUID), bei dem es sich um die Korrelations-ID des Konformitätsberichts handelt. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Datum und Uhrzeit der Durchführung der Konformitätsprüfung. |
| OperationName |DscResourceStatusData|
| ResultType |Gibt an, ob die Ressource konform ist. |
| NodeName_s |Der Name des verwalteten Knotens. |
| Category (Kategorie) | DscNodeStatus |
| Resource | Der Name des Azure Automation-Kontos. |
| Tenant_g | GUID, die den Mandanten für den Aufrufer identifiziert. |
| NodeId_g |Eindeutiger Bezeichner (GUID), der den verwalteten Knoten identifiziert. |
| DscReportId_g |Eindeutiger Bezeichner (GUID), der den Bericht identifiziert. |
| DscResourceId_s |Der Name der DSC-Ressourceninstanz. |
| DscResourceName_s |Der Name der DSC-Ressource. |
| DscResourceStatus_s |Gibt an, ob die DSC-Ressource konform ist. |
| DscModuleName_s |Der Name des PowerShell-Moduls, das die DSC-Ressource enthält. |
| DscModuleVersion_s |Die Version des PowerShell-Moduls, das die DSC-Ressource enthält. |
| DscConfigurationName_s |Der Name der Konfiguration, die auf den Knoten angewendet wird. |
| ErrorCode_s | Der Fehlercode, wenn die Ressource fehlerhaft ist. |
| ErrorMessage_s |Die Fehlermeldung, wenn die Ressource fehlerhaft ist. |
| DscResourceDuration_d |Die Zeit in Sekunden, für die die DSC-Ressource ausgeführt wurde. |
| SourceSystem | So erfasst Azure Monitor-Protokolle die Daten Immer *Azure* für Azure-Diagnose. |
| ResourceId |Gibt das Azure Automation-Konto an. |
| ResultDescription | Die Beschreibung für diesen Vorgang. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Eindeutiger Bezeichner (GUID), bei dem es sich um die Korrelations-ID des Konformitätsberichts handelt. |

## <a name="summary"></a>Zusammenfassung

Wenn Sie Ihre Automation State Configuration-Daten an Azure Monitor-Protokolle senden, erhalten Sie bessere Erkenntnisse über Ihre Automation State Configuration-Knoten, indem Sie:

- Warnungen einrichten, um bei Problemen benachrichtigt zu werden
- Benutzerdefinierte Ansichten und Suchabfragen zum Anzeigen von Runbook-Ergebnissen, Runbook-Auftragsstatus und anderer wichtiger Schlüsselindikatoren oder Metriken verwenden.  

Azure Monitor-Protokolle bietet eine höhere operative Transparenz für Ihre Automation State Configuration-Daten, sodass schneller auf Incidents reagiert werden kann.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md).
- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation DSC](automation-dsc-compile.md).
- Eine PowerShell-Cmdlet-Referenz ist unter [Azure Automation State Configuration-Cmdlets](/powershell/module/azurerm.automation/#automation) verfügbar.
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Verwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Continuous Deployment mit Azure Automation State Configuration und Chocolatey](automation-dsc-cd-chocolatey.md).
- Weitere Informationen zum Erstellen verschiedener Suchabfragen und zur Überprüfung der Automation State Configuration-Protokolle mit Azure Monitor-Protokolle finden Sie unter [Protokollsuchen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-searches.md).
- Weitere Informationen zu Azure Monitor-Protokolle und Datensammlungsquellen finden Sie unter [Sammeln von Azure Storage-Daten in Azure Monitor-Protokolle – Übersicht](../azure-monitor/platform/collect-azure-metrics-logs.md).
