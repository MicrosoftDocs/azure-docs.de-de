---
title: Problembehandlung beim Starten und Beenden von VMs mit Azure Automation
description: Dieser Artikel bietet Informationen zur Problembehandlung beim Starten und Beenden von VMs mit Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03bad12b7fcba5a247e05884aa0eb0493163a5c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578996"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Problembehandlung beim Starten/Beenden von VMs außerhalb der Geschäftszeiten

## <a name="deployment-failure"></a>Szenario: Fehler bei der ordnungsgemäßen Bereitstellung der Lösung zum Starten/Beenden von VMs

### <a name="issue"></a>Problem

Bei der Bereitstellung der [Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten](../automation-solution-vm-management.md) erhalten Sie eine der folgenden Fehlermeldungen:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>Ursache

Fehler bei Bereitstellungen können aus einem der folgenden Gründe auftreten:

1. Es ist bereits ein Automation-Konto mit dem gleichen Namen in der ausgewählten Region vorhanden.
2. Es ist eine Richtlinie vorhanden, die die Bereitstellung der Lösung zum Starten/Beenden von VMs nicht zulässt.
3. Die `Microsoft.OperationsManagement`-, `Microsoft.Insights`- oder `Microsoft.Automation`-Ressourcentypen sind nicht registriert.
4. Ihr Log Analytics-Arbeitsbereich ist mit einer Sperre versehen.

### <a name="resolution"></a>Lösung

Überprüfen Sie die folgende Liste auf mögliche Lösungen für Ihr Problem oder Stellen zum Nachschlagen:

1. Automation-Konten müssen innerhalb einer Azure-Region eindeutig sein, auch wenn sie sich in unterschiedlichen Ressourcengruppen befinden. Überprüfen Sie Ihre vorhandenen Automation-Konten in der Zielregion.
2. Eine vorhandene Richtlinie verhindert, dass eine Ressource, die für die Lösung zum Starten/Beenden von VMs erforderlich ist, bereitgestellt werden kann. Navigieren Sie zu Ihren Richtlinienzuweisungen im Azure-Portal, und überprüfen Sie, ob Sie eine Richtlinienzuweisung verwenden, die die Bereitstellung dieser Ressource nicht zulässt. Weitere Informationen hierzu finden Sie unter [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Um die Lösung zum Starten/Beenden von VMs bereitzustellen, muss Ihr Abonnement in den folgenden Azure-Ressourcennamespaces registriert werden:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Weitere Informationen zu Fehlern beim Registrieren von Anbietern finden Sie unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../../azure-resource-manager/resource-manager-register-provider-errors.md).
4. Wenn eine Sperre für Ihren Log Analytics-Arbeitsbereich vorhanden ist, navigieren Sie im Azure-Portal zu Ihrem Arbeitsbereich, und entfernen Sie alle Sperren für die Ressource.

## <a name="all-vms-fail-to-startstop"></a>Szenario: Alle virtuellen Computer können nicht gestartet/beendet werden

### <a name="issue"></a>Problem

Sie haben die Starten/Beenden-VM-Lösung konfiguriert, aber alle konfigurierten virtuellen Computer werden nicht gestartet/beendet.

### <a name="cause"></a>Ursache

Dieser Fehler kann einen der folgenden Gründe haben:

1. Ein Zeitplan ist nicht ordnungsgemäß konfiguriert.
2. Das RunAs-Konto wurde möglicherweise nicht ordnungsgemäß konfiguriert.
3. Bei der Ausführung eines Runbooks sind möglicherweise Fehler aufgetreten.
4. Die virtuellen Computer wurden möglicherweise ausgeschlossen.

### <a name="resolution"></a>Lösung

Überprüfen Sie die folgende Liste auf mögliche Lösungen für Ihr Problem oder Stellen zum Nachschlagen:

* Überprüfen Sie, ob Sie einen Zeitplan für die Starten/Beenden-VM-Lösung ordnungsgemäß konfiguriert haben. Wie Sie einen Zeitplan konfigurieren, erfahren Sie im Artikel [Planen eines Runbooks in Azure Automation](../automation-schedules.md).

* Überprüfen Sie die [Auftragsdatenströme](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) auf Fehler. Gehen Sie im Portal zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Aufträge** aus. Suchen Sie auf der Seite **Aufträge** nach Aufträgen aus einem der folgenden Runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Überprüfen Sie, ob Ihr [RunAs-Konto](../manage-runas-account.md) über die erforderlichen Berechtigungen für die virtuellen Computer verfügt, die Sie starten oder beenden möchten. Wie Sie die Berechtigungen auf einer Ressource überprüfen, erfahren Sie unter [Schnellstart: Anzeigen der zugewiesenen Rollen von Benutzern mit dem Azure-Portal](../../role-based-access-control/check-access.md). Sie müssen die Anwendungs-ID für den Dienstprinzipal angeben, der vom RunAs-Konto verwendet wird. Sie können diesen Wert abrufen, indem Sie zu Ihrem Automation-Konto im Azure-Portal gehen, **RunAs-Konten** unter **Kontoeinstellungen** auswählen und auf das entsprechende RunAs-Konto klicken.

* Virtuelle Computer können nicht gestartet oder beendet werden, wenn sie explizit ausgeschlossen sind. Ausgeschlossene VMs werden im Automation-Konto, das der Lösung bereitgestellt wird, in der **External_ExcludeVMNames**-Variablen festgelegt. Das folgende Beispiel zeigt, wie Sie diesen Wert mit PowerShell abfragen können.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Szenario: Einige meiner virtuellen Computer können nicht gestartet oder beendet werden

### <a name="issue"></a>Problem

Sie haben die Starten/Beenden-VM-Lösung konfiguriert, aber einige der konfigurierten virtuellen Computer werden nicht gestartet/beendet.

### <a name="cause"></a>Ursache

Dieser Fehler kann einen der folgenden Gründe haben:

1. Wenn Sie das Sequenzszenario verwenden, könnte ein Tag fehlen oder fehlerhaft sein.
2. Der virtuelle Computer könnte ausgeschlossen sein.
3. Das RunAs-Konto verfügt möglicherweise auf dem virtuellen Computer nicht über genügend Berechtigungen.
4. Bei dem virtuellen Computer könnte etwas vorliegen, das sein Starten oder Beenden verhindert hat.

### <a name="resolution"></a>Lösung

Überprüfen Sie die folgende Liste auf mögliche Lösungen für Ihr Problem oder Stellen zum Nachschlagen:

* Bei Verwendung des [Sequenzszenarios](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) der Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten müssen Sie sicherstellen, dass jeder virtuelle Computer, den Sie starten oder beenden möchten, über das richtige Tag verfügt. Stellen Sie sicher, dass die VMs, die Sie starten möchten, über das `sequencestart`-Tag verfügen, und die virtuellen Computer, die Sie beenden möchten, über das `sequencestop`-Tag. Beide Tags müssen einen positiven Ganzzahlwert haben. Sie können eine Abfrage wie im folgenden Beispiel verwenden, um nach allen virtuellen Computern mit den Tags und deren Werten zu suchen.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuelle Computer können nicht gestartet oder beendet werden, wenn sie explizit ausgeschlossen sind. Ausgeschlossene VMs werden im Automation-Konto, das der Lösung bereitgestellt wird, in der **External_ExcludeVMNames**-Variablen festgelegt. Das folgende Beispiel zeigt, wie Sie diesen Wert mit PowerShell abfragen können.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Um VMs zu starten und zu beenden, muss das RunAs-Konto für das Automation-Konto über die entsprechenden Berechtigungen auf dem virtuellen Computer verfügen. Wie Sie die Berechtigungen auf einer Ressource überprüfen, erfahren Sie unter [Schnellstart: Anzeigen der zugewiesenen Rollen von Benutzern mit dem Azure-Portal](../../role-based-access-control/check-access.md). Sie müssen die Anwendungs-ID für den Dienstprinzipal angeben, der vom RunAs-Konto verwendet wird. Sie können diesen Wert abrufen, indem Sie zu Ihrem Automation-Konto im Azure-Portal gehen, **RunAs-Konten** unter **Kontoeinstellungen** auswählen und auf das entsprechende RunAs-Konto klicken.

* Wenn beim Starten oder Aufheben der Zuordnung des virtuellen Computers Probleme auftreten, kann dieses Verhalten durch ein Problem auf dem virtuellen Computer selbst verursacht worden sein. Einige Beispiele oder mögliche Probleme: Ein Update wird während des Versuchs angewendet, herunterzufahren, ein Dienst reagiert nicht mehr und Sonstiges. Navigieren Sie zu Ihrer VM-Ressource, und überprüfen Sie die **Aktivitätsprotokolle**, um festzustellen, ob Fehler in den Protokollen vorhanden sind. Sie können auch versuchen, sich bei dem virtuellen Computer anzumelden, um festzustellen, ob Fehler im Ereignisprotokoll vorhanden sind. Weitere Informationen zur Problembehandlung Ihrer VM finden Sie unter [Problembehandlung von virtuellen Azure-Computern](../../virtual-machines/troubleshooting/index.md).

* Überprüfen Sie die [Auftragsdatenströme](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) auf Fehler. Gehen Sie im Portal zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Aufträge** aus.

## <a name="custom-runbook"></a>Szenario: Mein benutzerdefiniertes Runbook startet oder beendet meine virtuellen Computer nicht

### <a name="issue"></a>Problem

Sie haben ein benutzerdefiniertes Runbook erstellt oder eines aus dem PowerShell-Katalog heruntergeladen, und es funktioniert nicht ordnungsgemäß.

### <a name="cause"></a>Ursache

Es kommen viele Ursachen für diesen Fehler infrage. Gehen Sie zu Ihrem Automation-Konto im Azure-Portal, und wählen Sie unter **Prozessautomatisierung** die Option **Aufträge** aus. Suchen Sie auf der Seite **Aufträge** nach Aufträgen aus Ihrem Runbook, um ggf. fehlerhafte Aufträge anzuzeigen.

### <a name="resolution"></a>Lösung

Sie sollten die [Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten in Azure Automation](../automation-solution-vm-management.md) zum Starten und Beenden von virtuellen Computern in Azure Automation verwenden. Diese Lösung stammt von Microsoft. Benutzerdefinierte Runbooks werden von Microsoft nicht unterstützt. Möglicherweise finden Sie eine Lösung für Ihr benutzerdefiniertes Runbook in dem Artikel [Beheben von Fehlern bei Runbooks](runbooks.md). Dieser Artikel enthält allgemeine Richtlinien und Problembehandlung für Runbooks aller Typen. Überprüfen Sie die [Auftragsdatenströme](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) auf Fehler. Gehen Sie im Portal zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Aufträge** aus.

## <a name="dont-start-stop-in-sequence"></a>Szenario: Virtuelle Computer werden nicht in der richtigen Reihenfolge gestartet oder beendet

### <a name="issue"></a>Problem

Die virtuellen Computer, die Sie in der Lösung konfiguriert haben, werden nicht in der richtigen Reihenfolge gestartet oder beendet.

### <a name="cause"></a>Ursache

Dies wird durch falsche Tags der virtuellen Computer verursacht.

### <a name="resolution"></a>Lösung

Stellen Sie mit den folgenden Schritten sicher, dass die Lösung ordnungsgemäß konfiguriert ist.

1. Stellen sicher, dass alle zu startenden oder beendenden virtuellen Computer je nach Situation mit einem `sequencestart`- oder `sequencestop`-Tag versehen sind. Diese Tags benötigen eine positive ganze Zahl als Wert. VMs werden basierend auf diesem Wert in aufsteigender Reihenfolge verarbeitet.
2. Stellen Sie sicher, dass die Ressourcengruppen für die zu startenden oder beendenden virtuellen Computer sich je nach Situation in der Variablen `External_Start_ResourceGroupNames` oder `External_Stop_ResourceGroupNames` befinden.
3. Testen Sie Ihre Änderungen durch Ausführung des `SequencedStartStop_Parent`-Runbooks mit auf „true“ festgelegtem WHATIF-Parameter, um Ihre Änderungen in der Vorschau anzuzeigen.

Ausführliche und zusätzliche Anleitungen zum Verwenden der Lösung zum Starten und Beenden von VMs in einer Sequenz finden Sie unter [Szenario 2: Starten/Beenden von VMs der Reihe nach mithilfe von Tags](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Szenario: Beim Starten/Beenden des VM-Auftrags tritt ein Fehler mit dem „403 Verboten“-Status auf 

### <a name="issue"></a>Problem

Sie finden Aufträge, bei denen ein `403 forbidden`-Fehler bei den Runbooks zum Starten/Beenden von VMs außerhalb der Geschäftszeiten aufgetreten ist.

### <a name="cause"></a>Ursache

Dieses Problem kann durch ein nicht ordnungsgemäß konfiguriertes oder abgelaufenes RunAs-Konto verursacht werden. Unzureichende Berechtigungen des RunAs-Kontos der Automation-Konten für die VM-Ressourcen kommen auch als Ursache infrage.

### <a name="resolution"></a>Lösung

Um zu überprüfen, ob Ihr RunAs-Konto ordnungsgemäß konfiguriert ist, wechseln Sie zu Ihrem Automation-Konto im Azure-Portal, und wählen Sie **RunAs-Konten** unter **Kontoeinstellungen** aus. Hier sehen Sie den Status Ihrer RunAs-Konten; wenn ein RunAs-Konto falsch konfiguriert oder abgelaufen ist, zeigt dies der Status.

Wenn Ihr RunAs-Konto [falsch konfiguriert](../manage-runas-account.md#misconfiguration) ist, sollten Sie es löschen und neu erstellen.

Wenn das Zertifikat für Ihr RunAs-Konto abgelaufen ist, befolgen Sie die Schritte unter [Erneuerung eines selbstsignierten Zertifikat](../manage-runas-account.md#cert-renewal) zur Erneuerung des Zertifikats.

Das Problem wird möglicherweise durch fehlende Berechtigungen verursacht. Wie Sie die Berechtigungen auf einer Ressource überprüfen, erfahren Sie unter [Schnellstart: Anzeigen der zugewiesenen Rollen von Benutzern mit dem Azure-Portal](../../role-based-access-control/check-access.md). Sie müssen die Anwendungs-ID für den Dienstprinzipal angeben, der vom RunAs-Konto verwendet wird. Sie können diesen Wert abrufen, indem Sie zu Ihrem Automation-Konto im Azure-Portal gehen, **RunAs-Konten** unter **Kontoeinstellungen** auswählen und auf das entsprechende RunAs-Konto klicken.

## <a name="other"></a>Szenario: Mein Problem ist oben nicht aufgeführt

### <a name="issue"></a>Problem

Ihr Problem oder unerwartetes Ergebnis bei Verwendung der Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten ist auf dieser Seite nicht aufgeführt.

### <a name="cause"></a>Ursache

Oft können Fehler durch Verwendung einer alten und veralteten Version der Lösung verursacht werden.

### <a name="resolution"></a>Lösung

Zum Beheben vieler Fehler sollten Sie die Lösung entfernen und aktualisieren. Wie Sie die Lösung aktualisieren, erfahren Sie unter [Aktualisieren der Lösung](../automation-solution-vm-management.md#update-the-solution). Überprüfen Sie außerdem die [Auftragsdatenströme](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) auf Fehler. Gehen Sie im Portal zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Aufträge** aus.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Mit [@AzureSupport](https://twitter.com/azuresupport) verbinden – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
