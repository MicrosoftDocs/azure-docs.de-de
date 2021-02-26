---
title: Behandeln von Problemen beim Azure Automation-Feature „VMs außerhalb der Geschäftszeiten starten/beenden“
description: In diesem Artikel erfahren Sie, wie Sie Probleme behandeln, die bei der Verwendung des Features „VMs außerhalb der Geschäftszeiten starten/beenden“ auftreten.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: ff2ef8970afa21c0218da20a5b79ea2fb782dd5c
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053583"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Troubleshooting für „VMs außerhalb der Geschäftszeiten starten/beenden“

Dieser Artikel enthält Informationen zur Behandlung von Problemen, die auftreten können, wenn Sie das Azure Automation-Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ bereitstellen. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Szenario: Fehler bei der ordnungsgemäßen Bereitstellung von „VMs außerhalb der Geschäftszeiten starten/beenden“

### <a name="issue"></a>Problem

Bei der Bereitstellung von [VMs außerhalb der Geschäftszeiten starten/beenden](../automation-solution-vm-management.md) erhalten Sie eine der folgenden Fehlermeldungen:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
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

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Ursache

Fehler bei Bereitstellungen können aus einem der folgenden Gründe auftreten:

- Es ist bereits ein Automation-Konto mit demselben Namen in der ausgewählten Region vorhanden.
- Eine Richtlinie lässt die Bereitstellung von „VMs außerhalb der Geschäftszeiten starten/beenden“ nicht zu.
- Der Ressourcentyp `Microsoft.OperationsManagement`, `Microsoft.Insights` oder `Microsoft.Automation` ist nicht registriert.
- Ihr Log Analytics-Arbeitsbereich ist gesperrt.
- Sie verwenden eine veraltete Version der AzureRM-Module oder des Features „VMs außerhalb der Geschäftszeiten starten/beenden“.

### <a name="resolution"></a>Lösung

Sehen Sie sich die folgenden möglichen Lösungen an:

* Automation-Konten müssen innerhalb einer Azure-Region einzigartig sein, auch wenn sie sich in unterschiedlichen Ressourcengruppen befinden. Überprüfen Sie Ihre vorhandenen Automation-Konten in der Zielregion.
* Eine vorhandene Richtlinie verhindert, dass eine Ressource, die für „VMs außerhalb der Geschäftszeiten starten/beenden“ erforderlich ist, bereitgestellt werden kann. Navigieren Sie zu Ihren Richtlinienzuweisungen im Azure-Portal, und überprüfen Sie, ob Sie eine Richtlinienzuweisung verwenden, die die Bereitstellung dieser Ressource nicht zulässt. Weitere Informationen hierzu finden Sie unter [RequestDisallowedByPolicy-Fehler](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Damit Sie „VMs außerhalb der Geschäftszeiten starten/beenden“ bereitstellen können, muss Ihr Abonnement in den folgenden Azure-Ressourcennamespaces registriert sein:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Weitere Informationen zu Fehlern beim Registrieren von Anbietern finden Sie unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Wenn eine Sperre für Ihren Log Analytics-Arbeitsbereich vorhanden ist, navigieren Sie im Azure-Portal zu Ihrem Arbeitsbereich, und entfernen Sie alle Sperren für die Ressource.
* Wenn diese Lösungen Ihr Problem nicht beheben, befolgen Sie die Anleitungen unter [Aktualisieren des Features](../automation-solution-vm-management.md#update-the-feature), um „VMs außerhalb der Geschäftszeiten starten/beenden“ erneut bereitzustellen.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Szenario: Keine VM kann gestartet oder beendet werden

### <a name="issue"></a>Problem

Sie haben das Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ konfiguriert, aber es startet oder beendet nicht alle VMs.

### <a name="cause"></a>Ursache

Dieser Fehler kann einen der folgenden Gründe haben:

- Ein Zeitplan ist nicht ordnungsgemäß konfiguriert.
- Das ausführende Konto wurde möglicherweise nicht ordnungsgemäß konfiguriert.
- Bei der Ausführung eines Runbooks sind möglicherweise Fehler aufgetreten.
- Die VMs wurden möglicherweise ausgeschlossen.

### <a name="resolution"></a>Lösung

Sehen Sie sich die folgende Liste mit möglichen Lösungen an:

* Überprüfen Sie, ob der Zeitplan für „VMs außerhalb der Geschäftszeiten starten/beenden“ ordnungsgemäß konfiguriert wurde. Informationen zum Konfigurieren eines Zeitplans finden Sie unter [Zeitpläne](../shared-resources/schedules.md).

* Überprüfen Sie die [Auftragsdatenströme](../automation-runbook-execution.md#job-statuses) auf Fehler. Suchen Sie nach Aufträgen von einem der folgenden Runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Überprüfen Sie, ob Ihr [ausführendes Konto](../automation-security-overview.md#run-as-accounts) über die erforderlichen Berechtigungen für die VMs verfügt, die Sie starten oder beenden möchten. Wie Sie die Berechtigungen auf einer Ressource überprüfen, erfahren Sie unter [Schnellstart: Anzeigen der zugewiesenen Rollen von Benutzern mit dem Azure-Portal](../../role-based-access-control/check-access.md). Sie müssen die Anwendungs-ID für den Dienstprinzipal angeben, der vom ausführenden Konto verwendet wird. Sie können diesen Wert abrufen, indem Sie Ihr Automation-Konto im Azure-Portal aufrufen. Klicken Sie dann unter **Kontoeinstellungen** auf **Ausführende Konten**, und wählen Sie dann das entsprechende ausführende Konto aus.

* VMs können nicht gestartet oder beendet werden, wenn sie explizit ausgeschlossen wurden. Ausgeschlossene VMs sind in dem Automation-Konto, in dem das Feature bereitgestellt wird, in der Variable `External_ExcludeVMNames` festgelegt. Das folgende Beispiel zeigt, wie Sie diesen Wert mit PowerShell abfragen können.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Szenario: Einige meiner virtuellen Computer können nicht gestartet oder beendet werden

### <a name="issue"></a>Problem

Sie haben „VMs außerhalb der Geschäftszeiten starten/beenden“ konfiguriert, aber einige der konfigurierten VMs werden nicht gestartet bzw. beendet.

### <a name="cause"></a>Ursache

Dieser Fehler kann einen der folgenden Gründe haben:

- Im Sequenzszenario könnte ein Tag fehlen oder falsch sein.
- Die VM könnte ausgeschlossen sein.
- Das ausführende Konto verfügt möglicherweise nicht über ausreichende Berechtigungen auf der VM.
- Möglicherweise verhindert ein Problem auf der VM das Starten oder Beenden der VM.

### <a name="resolution"></a>Lösung

Sehen Sie sich die folgende Liste mit möglichen Lösungen an:

* Wenn Sie das [Sequenzszenario](../automation-solution-vm-management.md) von „VMs außerhalb der Geschäftszeiten starten/beenden“ verwenden, müssen Sie sicherstellen, dass jede VM, die Sie starten oder beenden möchten, über das richtige Tag verfügt. Stellen Sie sicher, dass die VMs, die Sie starten möchten, über das `sequencestart`-Tag verfügen, und die virtuellen Computer, die Sie beenden möchten, über das `sequencestop`-Tag. Beide Tags müssen einen positiven Ganzzahlwert haben. Sie können eine Abfrage wie im folgenden Beispiel verwenden, um nach allen virtuellen Computern mit den Tags und deren Werten zu suchen.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VMs können nicht gestartet oder beendet werden, wenn sie explizit ausgeschlossen wurden. Ausgeschlossene VMs sind in dem Automation-Konto, in dem das Feature bereitgestellt wird, in der Variable `External_ExcludeVMNames` festgelegt. Das folgende Beispiel zeigt, wie Sie diesen Wert mit PowerShell abfragen können.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Um VMs zu starten und zu beenden, muss das ausführende Konto für das Automation-Konto über die entsprechenden Berechtigungen für die VM verfügen. Wie Sie die Berechtigungen auf einer Ressource überprüfen, erfahren Sie unter [Schnellstart: Anzeigen der zugewiesenen Rollen von Benutzern mit dem Azure-Portal](../../role-based-access-control/check-access.md). Sie müssen die Anwendungs-ID für den Dienstprinzipal angeben, der vom ausführenden Konto verwendet wird. Sie können diesen Wert abrufen, indem Sie Ihr Automation-Konto im Azure-Portal aufrufen. Klicken Sie dann unter **Kontoeinstellungen** auf **Ausführende Konten**, und wählen Sie dann das entsprechende ausführende Konto aus.
* Wenn beim Starten oder Aufheben der Zuordnung der VM Probleme auftreten, kann ein Problem auf der VM selbst vorliegen. Zum Beispiel könnte unter anderem ein Update angewendet werden, wenn die VM versucht, herunterzufahren, oder ein Dienst reagiert nicht mehr. Navigieren Sie zu Ihrer VM-Ressource, und überprüfen Sie die **Aktivitätsprotokolle**, um festzustellen, ob Fehler in den Protokollen vorliegen. Sie können auch versuchen, sich bei der VM anzumelden, um zu überprüfen, ob Fehler in den Ereignisprotokollen vorliegen. Weitere Informationen zur Problembehandlung bei Ihrer VM finden Sie unter [Problembehandlung von virtuellen Azure-Computern](../../virtual-machines/troubleshooting/index.yml).
* Überprüfen Sie die [Auftragsdatenströme](../automation-runbook-execution.md#job-statuses) auf Fehler. Wechseln Sie im Portal zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Aufträge** aus.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Szenario: Mein benutzerdefiniertes Runbook startet oder beendet meine virtuellen Computer nicht

### <a name="issue"></a>Problem

Sie haben ein benutzerdefiniertes Runbook erstellt oder aus dem PowerShell-Katalog heruntergeladen, das nicht ordnungsgemäß funktioniert.

### <a name="cause"></a>Ursache

Dieser Fehler kann vielfältige Ursachen haben. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto, und klicken Sie unter **Prozessautomatisierung** auf **Aufträge**. Suchen Sie auf der Seite **Aufträge** nach Aufträgen aus Ihrem Runbook, um ggf. fehlerhafte Aufträge anzuzeigen.

### <a name="resolution"></a>Lösung

Wir empfehlen Folgendes:

* Verwenden Sie [VMs außerhalb der Geschäftszeiten starten/beenden](../automation-solution-vm-management.md), um virtuelle Computer in Azure Automation zu starten und zu beenden. 
* Beachten Sie, dass Microsoft keine benutzerdefinierten Runbooks unterstützt. Möglicherweise finden Sie eine Lösung für Ihr benutzerdefiniertes Runbook unter [Beheben von Runbookproblemen](runbooks.md). Überprüfen Sie die [Auftragsdatenströme](../automation-runbook-execution.md#job-statuses) auf Fehler. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Szenario: Virtuelle Computer werden nicht in der richtigen Reihenfolge gestartet oder beendet

### <a name="issue"></a>Problem

Die virtuellen Computer, die Sie für das Feature aktiviert haben, werden nicht in der richtigen Reihenfolge gestartet oder beendet.

### <a name="cause"></a>Ursache

Dieses Problem wird durch falsche Tags auf den VMs verursacht.

### <a name="resolution"></a>Lösung

Führen Sie die folgenden Schritte aus, um sicherzustellen, dass das Feature ordnungsgemäß aktiviert wurde:

1. Stellen sicher, dass alle zu startenden oder beendenden virtuellen Computer je nach Situation mit einem `sequencestart`- oder `sequencestop`-Tag versehen sind. Diese Tags benötigen eine positive ganze Zahl als Wert. VMs werden basierend auf diesem Wert in aufsteigender Reihenfolge verarbeitet.
1. Stellen Sie sicher, dass sich die Ressourcengruppen für die zu startenden oder beendenden virtuellen Computer je nach Situation in der Variable `External_Start_ResourceGroupNames` oder `External_Stop_ResourceGroupNames` befinden.
1. Testen Sie Ihre Änderungen durch Ausführen des Runbooks **SequencedStartStop_Parent** mit auf TRUE festgelegtem Parameter `WHATIF`, um eine Vorschau der Änderungen anzuzeigen.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Szenario: Fehler „403 – verboten“ bei Aufträgen von „VMs außerhalb der Geschäftszeiten starten/beenden“

### <a name="issue"></a>Problem

Sie finden Aufträge, bei denen in den Runbooks von „VMs außerhalb der Geschäftszeiten starten/beenden“ der Fehler `403 forbidden` aufgetreten ist.

### <a name="cause"></a>Ursache

Dieses Problem kann durch ein nicht ordnungsgemäß konfiguriertes oder abgelaufenes ausführendes Konto verursacht werden. Unzureichende Berechtigungen des ausführenden Kontos für die VM-Ressourcen kommen auch als Ursache infrage.

### <a name="resolution"></a>Lösung

Um zu überprüfen, ob Ihr ausführendes Konto ordnungsgemäß konfiguriert ist, wechseln Sie im Azure-Portal zu Ihrem Automation-Konto, und wählen Sie unter **Kontoeinstellungen** die Option **Ausführende Konten** aus. Wenn ein ausführendes Konto abgelaufen oder nicht ordnungsgemäß konfiguriert ist, wird diese Bedingung im Status angezeigt.

Wenn Ihr ausführendes Konto falsch konfiguriert ist, löschen Sie es, und erstellen Sie es neu. Weitere Informationen finden Sie unter [Ausführende Azure Automation-Konten](../automation-security-overview.md#run-as-accounts).

Wenn das Zertifikat für Ihr ausführendes Konto abgelaufen ist, befolgen Sie die Schritte unter [Erneuerung eines selbstsignierten Zertifikat](../manage-runas-account.md#cert-renewal) zur Erneuerung des Zertifikats.

Wenn Berechtigungen fehlen, finden Sie weitere Informationen unter [Schnellstart: Anzeigen der zugewiesenen Rollen von Benutzern mit dem Azure-Portal](../../role-based-access-control/check-access.md). Sie müssen die Anwendungs-ID für den Dienstprinzipal angeben, der vom ausführenden Konto verwendet wird. Sie können diesen Wert abrufen, indem Sie Ihr Automation-Konto im Azure-Portal aufrufen. Klicken Sie dann unter **Kontoeinstellungen** auf **Ausführende Konten**, und wählen Sie dann das entsprechende ausführende Konto aus.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Szenario: Mein Problem ist hier nicht aufgeführt

### <a name="issue"></a>Problem

Ihr Problem oder unerwartetes Ergebnis bei Verwendung von „VMs außerhalb der Geschäftszeiten starten/beenden“ ist auf dieser Seite nicht aufgeführt.

### <a name="cause"></a>Ursache

Oft können Fehler durch Verwendung einer alten und veralteten Version des Features verursacht werden.

> [!NOTE]
> Das Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ wurde mit den Azure-Modulen getestet, die bei der Bereitstellung des Features auf VMs in Ihr Azure Automation-Konto importiert werden. Das Feature funktioniert derzeit nicht mit neueren Versionen des Azure-Moduls. Diese Einschränkung betrifft nur das Automation-Konto, mit dem Sie „VMs außerhalb der Geschäftszeiten starten/beenden“ ausführen. In Ihren anderen Automation-Konten können Sie wie unter [Aktualisieren von Azure PowerShell-Modulen](../automation-update-azure-modules.md) beschrieben weiterhin neuere Versionen des Azure-Moduls verwenden.

### <a name="resolution"></a>Lösung

Sie können viele Fehler beheben, indem Sie [„VMs außerhalb der Geschäftszeiten starten/beenden“ entfernen und aktualisieren](../automation-solution-vm-management.md#update-the-feature). Sie können auch die [Auftragsdatenströme](../automation-runbook-execution.md#job-statuses) auf Fehler überprüfen. 

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem hier nicht aufgeführt wird, oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet die Azure-Community mit Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
