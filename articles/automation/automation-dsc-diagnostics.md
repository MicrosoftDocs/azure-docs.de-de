---
title: Integration in Azure Monitor-Protokolle
description: Dieser Artikel beschreibt, wie Desired State Configuration-Berichtsdaten von Azure Automation State Configuration an Azure Monitor-Protokolle gesendet werden.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f66c710901b129cb6b138fff126e67615c790714
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183667"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integration in Azure Monitor-Protokolle

Azure Automation State Configuration behält den Knotenstatus 30 Tage lang bei. Sie können die Knotenstatusdaten an Ihren Log Analytics-Arbeitsbereich senden, wenn Sie diese Daten für einen längeren Zeitraum aufbewahren möchten. Der Konformitätsstatus kann im Azure-Portal oder mit PowerShell angezeigt werden, und zwar für Knoten und einzelne DSC-Ressourcen in Knotenkonfigurationen. 

Azure Monitor-Protokolle bietet eine höhere operative Transparenz für Ihre Automation State Configuration-Daten, sodass schneller auf Incidents reagiert werden kann. Mit Azure Monitor-Protokolle können Sie jetzt:

- Complianceinformationen für verwaltete Knoten und einzelne Ressourcen abrufen
- Eine E-Mail oder Warnung basierend auf dem Compliancestatus auslösen
- Erweiterte Abfragen für Ihre verwalteten Knoten schreiben
- Den Compliancestatus über Automation-Konten korrelieren
- Benutzerdefinierte Ansichten und Suchabfragen zum Visualisieren von Runbookergebnissen, Runbookauftragsstatus und anderer wichtiger Schlüsselindikatoren oder Metriken verwenden

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Senden von Automation State Configuration-Berichten an Azure Monitor-Protokolle benötigen Sie Folgendes:

- Die [Azure PowerShell](/powershell/azure/)-Version von November 2016 (v2.3.0) oder höher.
- Ein Azure Automation-Konto. Weitere Informationen finden Sie unter [Einführung in Azure Automation](automation-intro.md).
- Einen Log Analytics-Arbeitsbereich mit dem Dienstangebot „Automatisierung und Steuerung“. Weitere Informationen finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).
- Mindestens einen Azure Automation DSC-Knoten. Weitere Informationen finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).
- Modul [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0), Version 2.7.0.0 oder höher. Installationsschritte finden Sie unter [Problembehandlung der Desired State Configuration in Azure Automation](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Einrichten der Integration in Azure Monitor-Protokolle

Führen Sie zum Importieren von Daten aus Azure Automation State Configuration in Azure Monitor-Protokolle die folgenden Schritte aus:

1. Melden Sie sich in PowerShell bei Ihrem Azure-Konto an. Siehe [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Rufen Sie die Ressourcen-ID Ihres Automation-Kontos mit dem folgenden PowerShell-Cmdlet ab. Wenn Sie über mehrere Automation-Konten verfügen, wählen Sie die Ressourcen-ID für das Konto aus, das Sie konfigurieren möchten.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Rufen Sie die Ressourcen-ID Ihres Log Analytics-Arbeitsbereichs mit dem folgenden PowerShell-Cmdlet ab. Wenn Sie über mehrere Arbeitsbereiche verfügen, wählen Sie die Ressourcen-ID für den Arbeitsbereich aus, den Sie konfigurieren möchten.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Führen Sie das folgende PowerShell-Cmdlet aus, und ersetzen Sie `<AutomationResourceId>` und `<WorkspaceResourceId>` durch die `ResourceId`-Werte aus den vorherigen Schritten.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Wenn Sie das Importieren von Daten aus Azure Automation State Configuration in Azure Monitor-Protokolle beenden möchten, führen Sie das folgende PowerShell-Cmdlet aus.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Anzeigen der DSC-Protokolle

Nachdem Sie die Integration mit Azure Monitor-Protokollen für Ihre Automation-State Configuration-Daten eingerichtet haben, können Sie diese anzeigen, indem Sie im linken Bereich der State Configuration-Seite (DSC) im Abschnitt **Überwachung** die Option **Protokolle** auswählen.

![Protokolle](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

Der Bereich „Protokollsuche“ wird mit einem Abfragebereich geöffnet, der auf Ihre Automation-Kontoressource bezogen ist. Sie können die State Configuration-Protokolle nach DSC-Vorgängen durchsuchen, indem Sie in Azure Monitor Protokolle suchen. Die Datensätze für DSC-Vorgänge werden in der Tabelle `AzureDiagnostics` gespeichert. Um z. B. nicht kompatible Knoten zu suchen, geben Sie die folgende Abfrage ein.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Filterdetails:

* Filtern Sie nach `DscNodeStatusData`, um für jeden State Configuration-Knoten Vorgänge zurückzugeben.
* Filtern Sie nach `DscResourceStatusData`, um für jede DSC-Ressource Vorgänge zurückzugeben, die in der auf diese Ressource angewandten Knotenkonfiguration aufgerufen wurden. 
* Filtern Sie nach `DscResourceStatusData`, um Fehlerinformationen zu fehlerhaften DSC-Ressourcen zu erhalten.

Weitere Informationen zum Erstellen von Protokollabfragen, um Daten zu finden, finden Sie unter [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Senden einer E-Mail bei einer fehlgeschlagenen DSC-Konformitätsprüfung

Eine der häufigsten Anfragen unserer Kunden betrifft die Möglichkeit, eine E-Mail oder Textnachricht zu senden, wenn bei einer DSC-Konfiguration ein Problem auftritt.

Um eine Warnungsregel zu erstellen, erstellen Sie zuerst eine Protokollsuche für die State Configuration-Berichtsdatensätze, die die Warnung aufrufen sollen. Klicken Sie auf die Schaltfläche **Neue Warnungsregel**, um die Warnungsregel zu erstellen und zu konfigurieren.

1. Klicken Sie auf der Seite „Übersicht“ für den Log Analytics-Arbeitsbereich auf **Protokolle**.
1. Erstellen Sie eine Protokollsuchabfrage für Ihre Warnung, indem Sie folgenden Text in das Abfragefeld eingeben: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Wenn Sie Protokolle von mehreren Automation-Konten oder Abonnements in Ihrem Arbeitsbereich eingerichtet haben, können Sie Ihre Warnungen nach Abonnement oder Automation-Konto gruppieren. Leiten Sie den Namen des Automation-Kontos vom Feld `Resource` in der Suche nach `DscNodeStatusData`-Datensätzen ab.
1. Klicken Sie oben auf der Seite auf **Neue Warnungsregel**, um den Bildschirm **Regel erstellen** zu öffnen. 

Weitere Informationen zu den Konfigurationsoptionen für Warnungen finden Sie unter [Erstellen von Warnungsregeln](../azure-monitor/platform/alerts-metric.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Suchen von Fehlern bei DSC-Ressourcen in allen Knoten

Ein Vorteil der Verwendung von Azure Monitor-Protokolle ist, dass Sie nach Fehlern bei der Überprüfung der Knoten suchen können. So finden Sie alle Instanzen von DSC-Ressourcen mit Fehlern:

1. Klicken Sie auf der Seite „Übersicht“ für den Log Analytics-Arbeitsbereich auf **Protokolle**.
1. Erstellen Sie eine Protokollsuchabfrage für Ihre Warnung, indem Sie folgenden Text in das Abfragefeld eingeben: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`.

### <a name="view-historical-dsc-node-status"></a>Anzeigen von Verlaufsdaten zum DSC-Knotenstatus

Mit dieser Abfrage können Sie den Statusverlauf Ihres DSC-Knotens visualisieren:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Mit dieser Abfrage wird ein Diagramm mit dem Knotenstatus über einen Zeitraum angezeigt.

## <a name="azure-monitor-logs-records"></a>Datensätze in Azure Monitor-Protokolle

Die Diagnose von Azure Automation erstellt zwei Kategorien von Datensätzen in Azure Monitor-Protokollen:

* Daten zum Knotenstatus (`DscNodeStatusData`)
* Daten zum Ressourcenstatus (`DscResourceStatusData`)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Datum und Uhrzeit der Durchführung der Konformitätsprüfung. |
| Vorgangsname |`DscNodeStatusData`. |
| ResultType |Ein Wert, der angibt, ob der Knoten konform ist. |
| NodeName_s |Der Name des verwalteten Knotens. |
| NodeComplianceStatus_s |Statuswert, der angibt, ob der Knoten konform ist. |
| DscReportStatus |Statuswert, der angibt, ob die Complianceüberprüfung erfolgreich ausgeführt wurde. |
| ConfigurationMode | Der Modus, der zum Anwenden der Konfiguration auf den Knoten verwendet wird. Mögliche Werte: <ul><li>`ApplyOnly`: DSC wendet die Konfiguration an und führt nur dann weitere Schritte durch, wenn eine neue Konfiguration per Push an den Zielknoten übertragen oder eine neue Konfiguration von einem Server abgerufen wird. Nach der ersten Anwendung einer neuen Konfiguration führt DSC keine Überprüfung auf Abweichungen von einem zuvor konfigurierten Zustand durch. DSC versucht, die Konfiguration anzuwenden, bis der Vorgang erfolgreich abgeschlossen wurde und bevor der Wert `ApplyOnly` in Kraft tritt. </li><li>`ApplyAndMonitor`: Dies ist der Standardwert. Der LCM wendet alle neuen Konfigurationen an. Nach der ersten Anwendung einer neuen Konfiguration meldet DSC Abweichungen in Protokollen, wenn der Zielknoten vom gewünschten Zustand abweicht. DSC versucht, die Konfiguration anzuwenden, bis der Vorgang erfolgreich abgeschlossen wurde und bevor der Wert `ApplyAndMonitor` in Kraft tritt.</li><li>`ApplyAndAutoCorrect`: DSC wendet neue Konfigurationen an. Nach der ersten Anwendung einer neuen Konfiguration meldet DSC Abweichungen in Protokollen, wenn der Zielknoten vom gewünschten Zustand abweicht, und wendet dann die aktuelle Konfiguration erneut an.</li></ul> |
| HostName_s | Der Name des verwalteten Knotens. |
| IPAddress | Die IPv4-Adresse des verwalteten Knotens. |
| Category | `DscNodeStatus`. |
| Resource | Der Name des Azure Automation-Kontos. |
| Tenant_g | Die GUID, die den Mandanten für den Aufrufer identifiziert. |
| NodeId_g | Eindeutiger Bezeichner (GUID), der den verwalteten Knoten identifiziert. |
| DscReportId_g | Eindeutiger Bezeichner (GUID), der den Bericht identifiziert. |
| LastSeenTime_t | Datum und Uhrzeit der letzten Anzeige des Berichts. |
| ReportStartTime_t | Datum und Uhrzeit des Berichtsstarts. |
| ReportEndTime_t | Datum und Uhrzeit des Berichtsabschlusses. |
| NumberOfResources_d | Die Anzahl der DSC-Ressourcen, die bei der Anwendung der Konfiguration auf den Knoten abgerufen wurden. |
| SourceSystem | Das Quellsystem, das angibt, wie die Daten in Azure Monitor-Protokollen gesammelt wurden. Immer `Azure` für Azure-Diagnose. |
| resourceId |Der Ressourcenbezeichner des Azure Automation-Kontos. |
| ResultDescription | Die Ressourcenbeschreibung für diesen Vorgang. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Der Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId | Ein eindeutiger Bezeichner (GUID), bei dem es sich um die Korrelations-ID des Complianceberichts handelt. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| TimeGenerated |Datum und Uhrzeit der Durchführung der Konformitätsprüfung. |
| Vorgangsname |`DscResourceStatusData`.|
| ResultType |Gibt an, ob die Ressource konform ist. |
| NodeName_s |Der Name des verwalteten Knotens. |
| Category | DscNodeStatus. |
| Resource | Der Name des Azure Automation-Kontos. |
| Tenant_g | Die GUID, die den Mandanten für den Aufrufer identifiziert. |
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
| SourceSystem | So erfasst Azure Monitor-Protokolle die Daten Immer `Azure` für Azure-Diagnose. |
| resourceId |Der Bezeichner des Azure Automation-Kontos. |
| ResultDescription | Die Beschreibung für diesen Vorgang. |
| SubscriptionId | Die Azure-Abonnement-ID (GUID) für das Automation-Konto. |
| ResourceGroup | Der Name der Ressourcengruppe für das Automation-Konto. |
| ResourceProvider | MICROSOFT.AUTOMATION. |
| ResourceType | AUTOMATIONACCOUNTS. |
| CorrelationId |Ein eindeutiger Bezeichner (GUID), bei dem es sich um die Korrelations-ID des Complianceberichts handelt. |

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md).
- Eine Einführung finden Sie unter [Erste Schritte mit Azure Automation State Configuration](automation-dsc-getting-started.md).
- Wie Sie DSC-Konfigurationen kompilieren und sie anschließend Zielknoten zuweisen, erfahren Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Ein Anwendungsbeispiel für Azure Automation State Configuration in einer Continuous Deployment-Pipeline finden Sie unter [Einrichten von Continuous Deployment mit Chocolatey](automation-dsc-cd-chocolatey.md).
- Weitere Informationen zum Erstellen verschiedener Suchabfragen und zur Überprüfung der Automation State Configuration-Protokolle mit Azure Monitor-Protokolle finden Sie unter [Protokollsuchen in Azure Monitor-Protokollen](../azure-monitor/log-query/log-query-overview.md).
- Weitere Informationen zu Azure Monitor-Protokolle und Datensammlungsquellen finden Sie unter [Sammeln von Azure Storage-Daten in Azure Monitor-Protokolle – Übersicht](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).