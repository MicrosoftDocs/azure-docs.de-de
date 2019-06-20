---
title: Beheben von Fehlern mit Updateverwaltung
description: Erfahren Sie, wie Sie Fehler mit Updateverwaltung beheben können.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9bcc871ecc9413f02545e6aec4caa6342d563b44
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474570"
---
# <a name="troubleshooting-issues-with-update-management"></a>Behandeln von Problemen mit Updateverwaltung

In diesem Artikel werden Lösungen zum Beheben von Problemen beschrieben, die bei Verwendung der Updateverwaltung auftreten können.

Es gibt eine Agent-Problembehandlung für den Hybrid Worker-Agent, mit dem das zugrunde liegende Problem ermittelt werden kann. Informationen zur Problembehandlung finden Sie unter [Beheben von Problemen mit dem Update-Agent](update-agent-issues.md). Bei allen anderen Problemen finden Sie weiter unten ausführliche Informationen zu möglichen Problemen.

## <a name="general"></a>Allgemein

### <a name="components-enabled-not-working"></a>Szenario: Die Komponenten für die Lösung „Updateverwaltung“ wurden aktiviert, und diese VM wird nun konfiguriert.

#### <a name="issue"></a>Problem

Die folgende Meldung wird 15 Minuten nach dem Onboarding auf einer VM angezeigt:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Ursache

Dieser Fehler kann die folgenden Gründe haben:

1. Die Kommunikation an das Automation-Konto wird blockiert.
2. Die VM, die integriert wird, stammt möglicherweise von einem geklonten Cloudcomputer, dessen System nicht mit dem installierten Microsoft Monitoring Agent vorbereitet wurde.

#### <a name="resolution"></a>Lösung

1. Sehen Sie sich den Abschnitt [Konfigurieren des Netzwerks](../automation-hybrid-runbook-worker.md#network-planning) an, um zu erfahren, welche Adressen und Ports zugelassen werden müssen, damit die Updateverwaltung funktioniert.
2. Wenn Sie ein geklontes Image verwenden:
   1. Entfernen Sie in Ihrem Log Analytics-Arbeitsbereich den virtuellen Computer aus der gespeicherten Suche für die Bereichskonfiguration `MicrosoftDefaultScopeConfig-Updates`, sofern er angezeigt wird. Gespeicherte Suchen finden Sie unter **Allgemein** in Ihrem Arbeitsbereich.
   2. Führen Sie `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` aus.
   3. Führen Sie `Restart-Service HealthService` aus, um `HealthService` neu zu starten. Der Schlüssel wird erneut erstellt, und eine neue UUID wird generiert.
   4. Wenn dies nicht funktioniert, bereiten Sie mit Sysprep das Image zunächst vor, und installieren Sie den MMA-Agent anschließend.

### <a name="multi-tenant"></a>Szenario: Sie erhalten einen Fehler aufgrund eines verknüpften Abonnements, wenn Sie eine Updatebereitstellung für Computer auf einem anderen Azure-Mandanten erstellen.

#### <a name="issue"></a>Problem

Sie erhalten beim Versuch, eine Updatebereitstellung für Computer auf einem anderen Azure-Mandanten zu erstellen, die folgende Fehlermeldung:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt bei der Erstellung einer Updatebereitstellung auf, bei der virtuelle Azure-Computer eines anderen Mandanten in eine Updatebereitstellung einbezogen werden.

#### <a name="resolution"></a>Lösung

Sie müssen die folgende Problemumgehung nutzen, um die Planung durchzuführen. Verwenden Sie das Cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) mit dem Schalter `-ForUpdate`, um einen Zeitplan zu erstellen, und das Cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
), um die Computer aus dem anderen Mandanten an den Parameter `-NonAzureComputer` zu übergeben. Dies wird anhand des folgenden Beispiels veranschaulicht:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Szenario: Im Portal werden unter „Updateverwaltung“ keine Computer angezeigt

#### <a name="issue"></a>Problem

Sie können auf die folgenden Szenarien stoßen:

* In der Ansicht „Updateverwaltung“ einer VM wird Ihr Computer als **Nicht konfiguriert** angezeigt

* Ihre Computer fehlen in der Ansicht „Updateverwaltung“ Ihres Automation-Kontos

* Bei einigen Ihrer Computer wird **Nicht bewertet** unter **Compliance** angezeigt, es werden aber Heartbeatdaten in Azure Monitor-Protokolle für den Hybrid Runbook Worker, jedoch nicht für die Updateverwaltung angezeigt.

#### <a name="cause"></a>Ursache

Dies kann durch potenzielle lokale Konfigurationsprobleme oder eine falsch konfigurierte Bereichskonfiguration verursacht werden.

Der Hybrid Runbook Worker muss eventuell erneut registriert und installiert werden.

Möglicherweise haben Sie in Ihrem Arbeitsbereich ein Kontingent festgelegt, das erreicht ist und verhindert, dass Daten gespeichert werden.

#### <a name="resolution"></a>Lösung

* Vergewissern Sie sich, dass Ihr Computer dem richtigen Arbeitsbereich Bericht erstattet. Überprüfen Sie, welchem Arbeitsbereich Ihr Computer Berichte erstattet. Entsprechende Anweisungen dazu finden Sie unter [Überprüfen der Agent-Konnektivität mit Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Vergewissern Sie sich anschließend, dass dies der Arbeitsbereich ist, der mit Ihrem Azure Automation-Konto verknüpft ist. Um dies zu bestätigen, navigieren Sie zu Ihrem Automation-Konto und klicken Sie unter **Verwandte Ressourcen** auf **Verknüpfter Arbeitsbereich**.

* Prüfen Sie, ob die Computer in Ihrem Log Analytics-Arbeitsbereich angezeigt werden. Führen Sie in Ihrem Log Analytics-Arbeitsbereich, der mit Ihrem Automation-Konto verknüpft ist, die folgende Abfrage aus. Wenn Ihr Computer nicht in den Suchergebnissen zu sehen ist, sendet Ihr Computer kein Taktsignal, was bedeutet, dass höchstwahrscheinlich ein lokales Konfigurationsproblem vorliegt. Sie können je nach Betriebssystem die Problembehandlung für [Windows](update-agent-issues.md#troubleshoot-offline) oder [Linux](update-agent-issues-linux.md#troubleshoot-offline) ausführen oder den [Agent neu installieren](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Wenn Ihr Computer in den Suchergebnissen aufgeführt ist, müssen Sie die im folgenden Aufzählungspunkt angegebene Bereichskonfiguration überprüfen.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Suchen Sie nach Bereichskonfigurationsproblemen. Die [Bereichskonfiguration](../automation-onboard-solutions-from-automation-account.md#scope-configuration) bestimmt, welche Computer für die Lösung konfiguriert werden. Wenn Ihr Computer zwar in Ihrem Arbeitsbereich, aber sonst nicht angezeigt wird, müssen Sie die Bereichskonfiguration entsprechend den Computern konfigurieren. Informationen hierzu finden Sie unter [Integrieren von Computern in den Arbeitsbereich](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Wenn Sie das Problem nicht anhand der obigen Schritte beheben können, führen Sie die Schritte unter [Bereitstellen eines Windows Hybrid Runbook Workers](../automation-windows-hrw-install.md) aus, um den Hybrid Worker für Windows neu zu installieren (bzw. [Bereitstellen eines Linux Hybrid Runbook Workers](../automation-linux-hrw-install.md) für Linux).

* Führen Sie in Ihrem Arbeitsbereich die folgende Abfrage aus. Wenn Sie das Ergebnis `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` sehen, haben Sie ein Kontingent für Ihren Arbeitsbereich festgelegt, das erreicht ist, wodurch das Speichern von Daten verhindert wird. Wechseln Sie in Ihrem Arbeitsbereich zu **Nutzung und geschätzte Kosten** > **Datenvolumenverwaltung**. Überprüfen Sie das eingerichtete Kontingent, oder heben Sie es auf.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Sollten Sie Probleme beim Integrieren der Lösung in einen virtuellen Computers haben, suchen Sie auf dem lokalen Computer im Ereignisprotokoll **Operations Manager** unter **Anwendungs- und Dienstprotokolle** nach Ereignissen mit der Ereignis-ID **4502** und einer Ereignismeldung, die **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** enthält.

Der folgende Abschnitt enthält spezifische Fehlermeldungen und passende Lösungsvorschläge. Informationen zu anderen Integrationsproblemen finden Sie unter [Problembehandlung bei der Integration von Lösungen](onboarding.md).

### <a name="machine-already-registered"></a>Szenario: Computer ist bereits bei einem anderen Konto registriert.

#### <a name="issue"></a>Problem

Sie erhalten die folgende Fehlermeldung:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Ursache

Der Computer ist bereits in einen anderen Arbeitsbereich für Updateverwaltung integriert.

#### <a name="resolution"></a>Lösung

Bereinigen Sie alte Artefakte auf dem Computer durch [Löschen der Hybrid-Runbook-Gruppe](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), und wiederholen Sie den Vorgang.

### <a name="machine-unable-to-communicate"></a>Szenario: Computer kann nicht mit dem Dienst kommunizieren.

#### <a name="issue"></a>Problem

Sie erhalten eine der folgenden Fehlermeldungen:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Ursache

Es gibt möglicherweise einen Proxy, ein Gateway oder eine Firewall, durch den bzw. die die Netzwerkkommunikation blockiert wird.

#### <a name="resolution"></a>Lösung

Überprüfen Sie Ihr Netzwerk, und stellen Sie sicher, dass die entsprechenden Ports und Adressen zulässig sind. Unter [Netzwerkanforderungen](../automation-hybrid-runbook-worker.md#network-planning) finden Sie eine Liste der Ports und Adressen, die von Updateverwaltung und Hybrid Runbook Workers benötigt werden.

### <a name="unable-to-create-selfsigned-cert"></a>Szenario: Selbstsigniertes Zertifikat kann nicht erstellt werden.

#### <a name="issue"></a>Problem

Sie erhalten eine der folgenden Fehlermeldungen:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Ursache

Der Hybrid Runbook Worker konnte kein selbstsigniertes Zertifikat generieren.

#### <a name="resolution"></a>Lösung

Überprüfen Sie, ob das Systemkonto über Lesezugriff auf den Ordner **C:\ProgramData\Microsoft\Crypto\RSA** verfügt, und versuchen Sie es erneut.

### <a name="failed-to-start"></a>Szenario: Beim Starten eines Computers in einer Updatebereitstellung wird die Fehlermeldung „Fehler beim Starten“ zurückgegeben

#### <a name="issue"></a>Problem

Für einen Computer wird der Status **Fehler beim Starten** angezeigt. Wenn Sie sich die Detailinformationen für den Computer ansehen, wird der folgende Fehler angezeigt:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Ursache

Dieser Fehler kann folgende Ursachen haben:

* Der Computer ist nicht mehr vorhanden.
* Der Computer ist ausgeschaltet und nicht erreichbar.
* Für den Computer besteht ein Netzwerkverbindungsproblem, und der Hybrid Worker auf dem Computer ist nicht erreichbar.
* Ein Update von Microsoft Monitoring Agent hat zu einer Änderung von SourceComputerId geführt.
* Die Ausführung des Updates wurde möglicherweise gedrosselt, wenn das Limit von 2.000 gleichzeitigen Aufträgen in einem Automation-Konto erreicht wurde. Jede Bereitstellung kann als Auftrag betrachtet werden, und jeder Computer in einer Updatebereitstellung zählt als Auftrag. Jeder andere Automatisierungsauftrag oder jede Updatebereitstellung, der bzw. die in Ihrem Automation-Konto ausgeführt wird, wird auf das Limit für gleichzeitige Aufträge angerechnet.

#### <a name="resolution"></a>Lösung

Verwenden Sie [dynamische Gruppen](../automation-update-management.md#using-dynamic-groups) (falls vorhanden) für Ihre Updatebereitstellungen.

* Stellen Sie sicher, dass der Computer noch vorhanden und erreichbar ist. Wenn er nicht mehr vorhanden ist, bearbeiten Sie Ihre Bereitstellung und entfernen Sie den Computer.
* Im Abschnitt zu [Netzwerkplanung](../automation-update-management.md#ports) finden Sie eine Liste der Ports und Adressen, die für die Updateverwaltung erforderlich sind. Stellen Sie sicher, dass Ihr Computer diese Anforderungen erfüllt.
* Führen Sie die folgende Abfrage in Log Analytics aus, um nach Computern in Ihrer Umgebung zu suchen, bei denen sich `SourceComputerId` geändert hat. Suchen Sie nach Computern, die denselben `Computer`-Wert, aber einen anderen `SourceComputerId`-Wert haben. Sobald Sie die betroffenen Computer gefunden haben, müssen Sie die Updatebereitstellungen für diese Computer bearbeiten und die Computer entfernen und dann erneut hinzufügen, damit `SourceComputerId` den richtigen Wert aufweist.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Szenario: Computer wird als nicht bewertet mit einer HResult-Ausnahme angezeigt.

#### <a name="issue"></a>Problem

Für Ihren Computer wird **Nicht bewertet** unter **Konformität** und darunter wird eine Ausnahmemeldung angezeigt.

#### <a name="cause"></a>Ursache

Windows Update oder WSUS wurde auf dem Computer nicht richtig konfiguriert. Für die Updateverwaltung wird Windows Update oder WSUS benötigt, um die erforderlichen Updates, den Status des Patches und die Ergebnisse der bereitgestellten Patches bereitzustellen. Ohne diese Informationen kann die Updateverwaltung keine richtigen Meldungen für die Patches zurückgeben, die benötigt bzw. installiert werden.

#### <a name="resolution"></a>Lösung

Doppelklicken Sie auf die rot angezeigte Ausnahme, um die vollständige Ausnahmemeldung anzuzeigen. Suchen Sie in der folgenden Tabelle nach möglichen Lösungen oder Aktionen:

|Ausnahme  |Lösung oder Aktion  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Suchen Sie den entsprechenden Fehlercode in der [Windows Update-Fehlercodeliste](https://support.microsoft.com/help/938205/windows-update-error-code-list), um weitere Details zur Ursache der Ausnahme zu erfahren.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Diese Fehler deuten auf Netzwerkkonnektivitätsprobleme hin. Stellen Sie sicher, dass Ihr Computer über die richtige Netzwerkkonnektivität für die Updateverwaltung verfügt. Im Abschnitt [Netzwerkplanung](../automation-update-management.md#ports) finden Sie eine Liste der erforderlichen Ports und Adressen.        |
|`0x8024001E`| Der Updatevorgang konnte nicht abgeschlossen werden, da der Dienst oder das System heruntergefahren wurden.|
|`0x8024002E`| Der Windows Update-Dienst ist deaktiviert.|
|`0x8024402C`     | Wenn Sie einen WSUS-Server verwenden, stellen Sie sicher, dass die Registrierungswerte für `WUServer` und `WUStatusServer` unter dem Registrierungsschlüssel `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` den richtigen WSUS-Server aufweisen.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Stellen Sie sicher, dass der Windows Update-Dienst (wuauserv) ausgeführt wird und nicht deaktiviert ist.        |
|Weitere generische Ausnahmen     | Suchen Sie im Internet nach möglichen Lösungen, und arbeiten Sie mit Ihrem lokalen IT-Support zusammen.         |

Die Überprüfung von `windowsupdate.log` kann Ihnen dabei helfen, auch die mögliche Ursache zu ermitteln. Weitere Informationen zum Lesen des Protokolls finden Sie unter [Lesen der Datei „Windowsupdate.log“](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Darüber hinaus können Sie die [Windows Update-Problembehandlung](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) herunterladen und ausführen, um zu überprüfen, ob auf dem Computer Probleme mit Windows Update bestehen.

> [!NOTE]
> Die [Windows Update-Problembehandlung](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) ist für Windows-Clients vorgesehen, sie funktioniert aber auch unter Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Szenario: Ausführung eines Updates kann nicht gestartet werden.

#### <a name="issue"></a>Problem

Die Ausführung eines Updates kann auf einem Linux-Computer nicht gestartet werden.

#### <a name="cause"></a>Ursache

Der Linux Hybrid Worker ist fehlerhaft.

#### <a name="resolution"></a>Lösung

Erstellen Sie eine Kopie der folgenden Protokolldatei, und speichern Sie sie für Problembehandlungszwecke:

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Szenario: Ausführung des Updates wird gestartet, aber es treten Fehler auf.

#### <a name="issue"></a>Problem

Die Ausführung eines Updates wird gestartet, wobei jedoch Fehler auftreten.

#### <a name="cause"></a>Ursache

Mögliche Ursachen:

* Der Paket-Manager ist fehlerhaft.
* Spezifische Pakete können cloudbasiertes Patchen beeinträchtigen.
* Andere Gründe

#### <a name="resolution"></a>Lösung

Wenn während der Ausführung eines Updates nach dem erfolgreichen Start unter Linux Fehler auftreten, überprüfen Sie die Auftragsausgabe des von der Ausführung betroffenen Computers. Möglicherweise finden Sie spezifische Fehlermeldungen des Paket-Managers auf Ihrem Computer, die Sie untersuchen können, um dann Maßnahmen zu ergreifen. Updateverwaltung setzt voraus, dass der Paket-Manager für eine erfolgreiche Bereitstellung von Updates fehlerfrei ist.

In einigen Fällen können Paketupdates Updateverwaltung beeinträchtigen und verhindern, dass die Bereitstellung eines Updates abgeschlossen wird. Wenn dies der Fall ist, müssen Sie diese Pakete entweder von künftigen Ausführungen von Updates ausschließen oder sie manuell installieren.

Wenn Sie ein Patchproblem nicht beheben können, erstellen Sie eine Kopie der folgenden Protokolldatei, und bewahren Sie sie zu Problembehandungszwecken auf, **bevor** die nächste Bereitstellung von Updates beginnt:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Mit [@AzureSupport](https://twitter.com/azuresupport) verbinden – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
