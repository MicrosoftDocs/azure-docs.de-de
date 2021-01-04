---
title: Behandeln von Problemen bei Hybrid Runbook Workern in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Probleme beheben, die bei Hybrid Runbook Workern in Azure Automation auftreten.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1386dd820b10b63862ddab38c441f251bea1d83d
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428399"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Beheben von Hybrid Runbook Worker-Problemen

Dieser Artikel enthält Informationen zum Troubleshooting für Hybrid Runbook Worker in Azure Automation. Allgemeine Informationen finden Sie unter [Übersicht über Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Allgemein

Der Hybrid Runbook Worker benötigt einen Agent, um mit Ihrem Azure Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbookaufträge zu erhalten und den Status zu melden. Für Windows ist dieser Agent der Log Analytics-Agents für Windows. Für Linux handelt es sich um den Log Analytics-Agent für Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Szenario: Fehler bei der Runbookausführung

#### <a name="issue"></a>Problem

Bei der Ausführung eines Runbooks tritt ein Fehler mit der folgenden Fehlermeldung auf:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Kurze Zeit nach drei Ausführungsversuchen wird Ihr Runbook angehalten. Es gibt Bedingungen, die den Abschluss des Runbooks unterbrechen können. Die dafür angezeigte Fehlermeldung enthält möglicherweise keine zusätzlichen Informationen.

#### <a name="cause"></a>Ursache

Folgende Ursachen kommen in Betracht:

* Die Runbooks können nicht bei lokalen Ressourcen authentifiziert werden.
* Der Hybrid Worker befindet sich hinter einem Proxy oder einer Firewall.
* Der für die Ausführung des Hybrid Runbook Workers konfigurierte Computer erfüllt die Hardwaremindestanforderungen nicht.

#### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass der Computer an Port 443 über ausgehenden Zugriff auf **\*.azure-automation.net** verfügt.

Computer, auf denen der Hybrid Runbook Worker ausgeführt werden soll, müssen die Mindestanforderungen an die Hardware erfüllen, damit sie als Host für dieses Feature konfiguriert werden können. Runbooks und die von ihnen verwendeten Hintergrundprozesse führen möglicherweise zu einer Überlastung des Systems sowie zu Verzögerungen und Timeouts bei Runbookaufträgen.

Vergewissern Sie sich, dass der für die Ausführung des Hybrid Runbook Worker-Features vorgesehene Computer die Hardwaremindestanforderungen erfüllt. Wenn dies der Fall ist, überwachen Sie die CPU- und Arbeitsspeicherauslastung, um Zusammenhänge zwischen der Leistung der Hybrid Runbook Worker-Prozesse und Windows zu erkennen. Wenn der Arbeitsspeicher oder die CPU stark ausgelastet sind, kann dies auf die Notwendigkeit eines Upgrades von Ressourcen hindeuten. Sie können auch eine andere Computeressource auswählen, die die Mindestanforderungen erfüllt. Bei entsprechend großer Workload skalieren Sie sie nach Bedarf.

Prüfen Sie, ob im Ereignisprotokoll **Microsoft-SMA** ein entsprechendes Ereignis mit der Beschreibung `Win32 Process Exited with code [4294967295]` vorhanden ist. Die Ursache dieses Fehlers liegt darin, dass Sie in Ihren Runbooks die Authentifizierung nicht konfiguriert haben oder dass Sie die „Ausführen als“-Anmeldeinformationen für die Hybrid Runbook Worker-Gruppe nicht angegeben haben. Überprüfen Sie die Runbookberechtigungen in [Ausführen von Runbooks auf einem Hybrid Runbook Worker](../automation-hrw-run-runbooks.md), und vergewissern Sie sich, dass Sie die Authentifizierung für Ihre Runbooks ordnungsgemäß konfiguriert haben.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Szenario: Ereignis 15011 im Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Der Hybrid Runbook Worker empfängt das Ereignis 15011, das darauf hinweist, dass ein Abfrageergebnis nicht gültig ist. Die folgende Fehlermeldung wird angezeigt, wenn der Worker versucht, eine Verbindung mit dem [SignalR-Server](/aspnet/core/signalr/introduction?view=aspnetcore-3.1) zu öffnen.

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Ursache

Der Hybrid Runbook Worker wurde nicht ordnungsgemäß für die automatisierte Featurebereitstellung (z. B. die Updateverwaltung) konfiguriert. Die Bereitstellung enthält einen Teil, der die VM mit dem Log Analytics-Arbeitsbereich verbindet. Das PowerShell-Skript sucht in dem Abonnement mit dem angegebenen Namen nach dem Arbeitsbereich. In diesem Fall befindet sich der Log Analytics-Arbeitsbereich in einem anderen Abonnement. Das Skript kann den Arbeitsbereich nicht finden und versucht, einen zu erstellen, doch der Name ist bereits vergeben. Daher tritt bei der Bereitstellung ein Fehler auf.

#### <a name="resolution"></a>Lösung

Sie haben zwei Möglichkeiten, dieses Problem zu beheben:

* Ändern Sie das PowerShell-Skript so, dass es in einem anderen Abonnement nach dem Log Analytics-Arbeitsbereich sucht. Dies ist eine gute Lösung, wenn Sie planen, in Zukunft viele Hybrid Runbook Worker-Computer bereitzustellen.

* Konfigurieren Sie den Workercomputer manuell so, dass er in einer Orchestrator-Sandbox ausgeführt wird. Führen Sie dann ein im Azure Automation-Konto erstelltes Runbook auf dem Worker aus, um dessen Funktionalität zu testen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Szenario: Microsoft Azure-VMs wurden automatisch aus der Hybrid-Worker-Gruppe gelöscht.

#### <a name="issue"></a>Problem

Hybrid Runbook Worker oder VMs werden Ihnen nicht angezeigt, wenn der Workercomputer lange Zeit ausgeschaltet war.

#### <a name="cause"></a>Ursache

Der Hybrid Runbook Worker-Computer hat Azure Automation länger als 30 Tage nicht gepingt. Daher hat Automation die Hybrid Runbook Worker-Gruppe oder die System Worker-Gruppe gelöscht. 

#### <a name="resolution"></a>Lösung

Starten Sie den Workercomputer, und registrieren Sie ihn erneut in Azure Automation. Anweisungen zur Installation der Runbookumgebung und dem Herstellen einer Verbindung mit Azure Automation finden Sie unter [Bereitstellen eines Windows Hybrid Runbook Workers](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Szenario: Es wurde kein Zertifikat im Zertifikatspeicher auf dem Hybrid Runbook Worker gefunden.

#### <a name="issue"></a>Problem

Ein Runbook, das auf einem Hybrid Runbook Worker ausgeführt wird, schlägt mit der folgenden Fehlermeldung fehl:

```error
Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```
#### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn Sie versuchen, ein [ausführendes Konto](../manage-runas-account.md) in einem Runbook zu verwenden, das auf einem Hybrid Runbook Worker ausgeführt wird, auf dem das Zertifikat des ausführenden Kontos nicht vorhanden ist. Der Hybrid Runbook Worker enthält das Zertifikatobjekt nicht standardmäßig lokal. Dieses Objekt ist aber für die ordnungsgemäße Funktion des ausführenden Kontos erforderlich.

#### <a name="resolution"></a>Lösung

Wenn es sich bei Ihrem Hybrid Runbook Worker um eine Azure-VM handelt, können Sie stattdessen die [Runbook-Authentifizierung mit verwalteten Identitäten](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) verwenden. Dieses Szenario gestattet Ihnen die Authentifizierung bei Azure-Ressourcen mithilfe der verwalteten Identität der Azure-VM anstelle des „Ausführen als“-Kontos, was die Authentifizierung vereinfacht. Wenn der Hybrid Runbook Worker ein lokaler Computer ist, müssen Sie das Zertifikat des „Ausführen als“-Kontos auf dem Computer installieren. Sehen Sie sich die Schritte an, die für die Ausführung des PowerShell-Runbooks **Export-RunAsCertificateToHybridWorker** erforderlich sind und in [Ausführen von Runbooks auf einem Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) erläutert werden, wenn Sie Informationen zum Installieren des Zertifikats erhalten möchten.

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Szenario: Fehler 403 während der Registrierung eines Hybrid Runbook Workers

#### <a name="issue"></a>Problem

Die anfängliche Registrierungsphase des Workers schlägt fehlt, und der folgende Fehler (403) wird zurückgegeben:

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Ursache

Folgende Probleme kommen als Ursache in Betracht:

* In den Einstellungen des Agents wurde eine Arbeitsbereich-ID oder ein (primärer) Arbeitsbereichsschlüssel fehlerhaft eingegeben. 
* Der Hybrid Runbook Worker kann die Konfiguration nicht herunterladen, was zu einem Fehler bei der Kontoverknüpfung führt. Wenn Azure Features auf Computern in Azure aktiviert sind, werden nur bestimmte Regionen zum Verknüpfen eines Log Analytics-Arbeitsbereichs und eines Automation-Kontos unterstützt. Es ist auch möglich, dass auf dem Computer ein falsches Datum oder eine falsche Uhrzeit festgelegt wurde. Wenn die Zeit um ca. 15 Minuten von der aktuellen Uhrzeit abweicht, tritt bei der Featurebereitstellung ein Fehler auf.

#### <a name="resolution"></a>Lösung

##### <a name="mistyped-workspace-id-or-key"></a>Fehlerhaft eingegebene Arbeitsbereichs-ID oder fehlerhaft eingegebener Arbeitsbereichsschlüssel
Sehen Sie sich im Artikel „Verwalten und Warten des Log Analytics-Agents für Windows und Linux“ die Informationen im Abschnitt zum Hinzufügen oder Entfernen von Arbeitsbereichen für den [Windows-Agent](../../azure-monitor/platform/agent-manage.md#windows-agent) bzw. [Linux-Agent](../../azure-monitor/platform/agent-manage.md#linux-agent) an, um zu überprüfen, ob die Arbeitsbereichs-ID oder der Arbeitsbereichsschlüssel des Agents fehlerhaft eingegeben wurde. Stellen Sie sicher, dass Sie die Zeichenfolge im Azure-Portal vollständig markieren, und gehen Sie beim Kopieren und Einfügen sorgfältig vor.

##### <a name="configuration-not-downloaded"></a>Konfiguration nicht heruntergeladen

Ihr Log Analytics-Arbeitsbereich und Ihr Automation-Konto müssen sich in einer verknüpften Region befinden. Eine Liste unterstützter Regionen finden Sie unter [Arbeitsbereichzuordnungen](../how-to/region-mappings.md).

Möglicherweise müssen Sie auch das Datum oder die Zeitzone auf Ihrem Computer aktualisieren. Wenn Sie eine benutzerdefinierte Zeitzone auswählen, sollten Sie sich vergewissern, dass die Zone der koordinierten Weltzeit entspricht, die sich von Ihrer lokalen Zeitzone unterscheiden kann.

## <a name="linux"></a>Linux

Der Linux Hybrid Runbook Worker ist abhängig vom [Log Analytics-Agent für Linux](../../azure-monitor/platform/log-analytics-agent.md), um mit Ihrem Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbook-Aufträge zu erhalten und den Status zu melden. Wenn bei der Registrierung des Workers ein Fehler auftritt, kommen hierfür mehrere Gründe infrage.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Szenario: Beim Signieren eines Runbooks erhält der Linux-Hybrid Runbook Worker eine Aufforderung zum Eingeben eines Kennworts

#### <a name="issue"></a>Problem

Wenn Sie den Befehl `sudo` für einen Linux-Hybrid Runbook Worker ausführen, wird eine unerwartete Aufforderung für die Eingabe eines Kennworts abgerufen.

#### <a name="cause"></a>Ursache

Das **nxautomationuser** -Konto für den Log Analytics-Agent für Linux ist in der **sudoers** -Datei nicht ordnungsgemäß konfiguriert. Der Hybrid Runbook Worker benötigt die entsprechende Konfiguration der Kontobenachrichtigungen und anderer Daten, damit Runbooks auf dem Linux-Runbook Worker signiert werden können.

#### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass der Hybrid Runbook Worker auf dem Computer über die ausführbare Datei „GnuPG (GPG)“ verfügt.

* Überprüfen Sie die Konfiguration des **nxautomationuser** -Kontos in der **sudoers** -Datei. Lesen Sie sich den Artikel [Ausführen von Runbooks auf einem Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) durch.

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Szenario: Der Log Analytics-Agent für Linux wird nicht ausgeführt

#### <a name="issue"></a>Problem

Der Log Analytics-Agent für Linux wird nicht ausgeführt.

#### <a name="cause"></a>Ursache

Wenn der Agent nicht ausgeführt wird, verhindert er die Kommunikation zwischen dem Linux Hybrid Runbook Worker und Azure Automation. Es kann verschiedene Gründe geben, warum der Agent nicht ausgeführt werden kann.

#### <a name="resolution"></a>Lösung

 Überprüfen Sie, ob der Agent ausgeführt wird, indem Sie den Befehl `ps -ef | grep python` eingeben. Eine Ausgabe ähnlich der folgenden sollte angezeigt werden. Der python-Befehl wird unter Verwendung des Benutzerkontos **nxautomation** verarbeitet. Wenn das Azure Automation-Feature nicht aktiviert ist, wird keiner der folgenden Prozesse ausgeführt.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Die folgende Liste enthält die Prozesse, die für einen Linux Hybrid Runbook Worker gestartet werden. Sie befinden sich alle im Verzeichnis /var/opt/microsoft/omsagent/state/automationworker/.

* **oms.conf:** Dies ist der Worker-Manager-Prozess. Er wird direkt vom DSC gestartet.
* **worker.conf:** Dies ist der automatisch registrierte Hybrid-Worker-Prozess. Er wird vom Worker-Manager gestartet. Dieser Prozess dient der Updateverwaltung und ist für den Benutzer transparent. Dieser Prozess ist nicht vorhanden, wenn die Updateverwaltung auf dem Computer nicht aktiviert wurde.
* **diy/worker.conf:** Dies ist der selbst erstellte Hybrid-Worker-Prozess. Der eigenständige Hybrid Worker-Prozess wird verwendet, um Benutzerrunbooks auf dem Hybrid Runbook Worker auszuführen. Er unterscheidet sich vom automatisch registrierten Hybrid Worker-Prozess nur in dem wichtigen Detail, dass eine andere Konfiguration verwendet wird. Dieser Prozess ist nicht vorhanden, wenn Azure Automation deaktiviert und der eigenständige Linux-Hybrid Worker nicht registriert ist.

Wenn der Agent nicht ausgeführt wird, führen Sie zum Starten des Diensts den folgenden Befehl aus: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Szenario: Die angegebene Klasse ist nicht vorhanden.

Wenn im Protokoll **/var/opt/microsoft/omsconfig/omsconfig.log** die Fehlermeldung `The specified class does not exist..` (Die angegebene Klasse ist nicht vorhanden..) auftaucht, muss der Log Analytics-Agent für Linux aktualisiert werden. Führen Sie den folgenden Befehl aus, um den Agent neu zu installieren.

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Der Windows Hybrid Runbook Worker ist abhängig vom [Log Analytics-Agent für Windows](../../azure-monitor/platform/log-analytics-agent.md), um mit Ihrem Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbook-Aufträge zu erhalten und den Status zu melden. Für den Fall, dass bei der Registrierung des Workers ein Fehler auftritt, werden in diesem Abschnitt einige mögliche Gründe hierfür erläutert.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Szenario: Der Log Analytics-Agent für Windows wird nicht ausgeführt.

#### <a name="issue"></a>Problem

Auf dem Computer mit dem Hybrid Runbook Worker wird der `healthservice` nicht ausgeführt.

#### <a name="cause"></a>Ursache

Wenn der Log Analytics-Agent für Windows-Dienst nicht ausgeführt wird, kann der Hybrid Runbook Worker nicht mit Azure Automation kommunizieren.

#### <a name="resolution"></a>Lösung

Überprüfen Sie, ob der Agent ausgeführt wird, indem Sie den folgenden Befehl in PowerShell eingeben: `Get-Service healthservice`. Wenn der Dienst beendet wurde, geben Sie den folgenden Befehl in PowerShell ein, um ihn wieder zu starten: `Start-Service healthservice`.

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Szenario: Ereignis 4502 im Operations Manager-Protokoll

#### <a name="issue"></a>Problem

Im Ereignisprotokoll unter **Anwendungs- und Dienstprotokolle\Operations Manager** werden das Ereignis 4502 und eine Ereignisnachricht, die `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` enthält, mit der folgenden Beschreibung angezeigt:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn Ihr Proxy oder Ihre Netzwerkfirewall die Kommunikation mit Microsoft Azure blockiert. Stellen Sie sicher, dass der Computer an Port 443 über ausgehenden Zugriff auf **\*.azure-automation.net** verfügt.

#### <a name="resolution"></a>Lösung

Protokolle werden lokal auf jedem Hybridworker unter "C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes" gespeichert. Sie können überprüfen, ob in den Ereignisprotokollen unter **Anwendungs- und Dienstprotokolle\Microsoft-SMA\Operations** und **Anwendungs- und Dienstprotokolle\Operations Manager** Warnungs- oder Fehlerereignisse vorhanden sind. Diese Protokolle zeigen Verbindungsprobleme oder andere Probleme an, die Auswirkungen auf das Aktivieren der Rolle in Azure Automation haben, oder Probleme, die im normalen Betrieb auftreten. Weitere Hilfe bei der Behandlung von Problemen mit dem Log Analytics-Agent finden Sie unter [Behandeln von Problemen mit dem Log Analytics Windows-Agent](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Hybrid Worker senden [Runbookausgabe und -meldungen](../automation-runbook-output-and-messages.md) an Azure Automation auf dieselbe Art, auf die in der Cloud ausgeführte Runbookaufträge Ausgabe und Meldungen versenden. Wie bei Runbooks können Sie den ausführlichen Datenstrom und den Fortschrittsdatenstrom aktivieren.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Szenario: „Orchestrator.Sandbox.exe“ kann keine Verbindung mit Microsoft 365 über den Proxy herstellen.

#### <a name="issue"></a>Problem

Ein auf einem Windows-Hybrid Runbook Worker ausgeführtes Skript kann nicht erwartungsgemäß mit Microsoft 365 in einer Orchestrator-Sandbox verbunden werden. Das Skript verwendet für die Verbindung [Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0). 

Wenn Sie **Orchestrator.Sandbox.exe.config** anpassen und den Proxy und die Umgehungsliste festlegen, wird weiterhin keine ordnungsgemäße Verbindung mit der Sandbox hergestellt. Eine Datei **Powershell_ise.exe.config** mit denselben Einstellungen für Proxy und Umgehungsliste scheint erwartungsgemäß zu funktionieren. SMA- (Service Management Automation) und PowerShell-Protokolle bieten keine Informationen zum Proxy.

#### <a name="cause"></a>Ursache

Die Verbindung mit den Active Directory-Verbunddiensten (AD FS) auf dem Server kann den Proxy nicht umgehen. Denken Sie daran, dass eine PowerShell-Sandbox als der protokollierte Benutzer ausgeführt wird. Eine Orchestrator-Sandbox ist jedoch stark angepasst und ignoriert die Einstellungen in der Datei **Orchestrator.Sandbox.exe.config** möglicherweise. Sie weist speziellen Code für die Verarbeitung von Computer- oder Log Analytics-Agent-Proxyeinstellungen auf, jedoch nicht für den Umgang mit anderen benutzerdefinierten Proxyeinstellungen. 

#### <a name="resolution"></a>Lösung

Sie können das Problem für die Orchestrator-Sandbox beheben, indem Sie Ihr Skript migrieren, sodass die Azure Active Directory-Module anstelle des MSOnline-Moduls für PowerShell-Cmdlets verwendet werden. Weitere Informationen finden Sie unter [Migrieren von Orchestrator zu Azure Automation (Beta)](../automation-orchestrator-migration.md).

Wenn Sie die Cmdlets des MSOnline-Moduls weiterhin verwenden möchten, ändern Sie das Skript so, dass [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) verwendet wird. Geben Sie Werte für die Parameter `ComputerName` und `Credential` an. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Diese Codeänderung startet eine vollständig neue PowerShell-Sitzung im Kontext der angegebenen Anmeldeinformationen. Dadurch sollte der Datenverkehr über einen Proxyserver geleitet werden können, der den aktiven Benutzer authentifiziert.

>[!NOTE]
>Mit dieser Lösung ist es nicht erforderlich, die Konfigurationsdatei der Sandbox zu bearbeiten. Selbst wenn die Konfigurationsdatei erfolgreich mit Ihrem Skript funktioniert, wird sie bei jeder Aktualisierung des Hybrid Runbook Worker-Agents gelöscht.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Szenario: Keine Meldungen vom Hybrid Runbook Worker

#### <a name="issue"></a>Problem

Der Computer mit dem Hybrid Runbook Worker wird zwar ausgeführt, im Arbeitsbereich werden jedoch keine Heartbeatdaten für den Computer angezeigt.

Die folgende Beispielabfrage zeigt die Computer in einem Arbeitsbereich und ihren jeweils letzten Heartbeat an:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Ursache

Dieses Problem kann durch einen beschädigten Cache für den Hybrid Runbook Worker verursacht werden.

#### <a name="resolution"></a>Lösung

Melden Sie sich zur Behebung dieses Problems beim Hybrid Runbook Worker an, und führen Sie das folgende Skript aus. Dieses Skript beendet den Log Analytics-Agent für Windows, entfernt den dazugehörigen Cache und startet den Dienst neu. Dadurch muss der Hybrid Runbook Worker seine Konfiguration erneut aus Azure Automation herunterladen.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Szenario: Sie können keinen Windows Hybrid Runbook Worker hinzufügen

#### <a name="issue"></a>Problem

Die folgende Nachricht wird angezeigt, wenn Sie versuchen, einen Hybrid Runbook Worker mithilfe des Cmdlets `Add-HybridRunbookWorker` hinzuzufügen:

```error
Machine is already registered
```

#### <a name="cause"></a>Ursache

Dies kann dadurch verursacht werden, dass der Computer bereits bei einem anderen Automation-Konto registriert ist oder Sie versuchen, den Hybrid Runbook Worker nach dem Entfernen vom Computer wieder hinzuzufügen.

#### <a name="resolution"></a>Lösung

Entfernen Sie den folgenden Registrierungsschlüssel, starten Sie `HealthService` neu, und versuchen Sie, das Cmdlet `Add-HybridRunbookWorker` noch mal auszuführen, um dieses Problem zu lösen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Szenario: Sie können keinen Linux Hybrid Runbook Worker hinzufügen

#### <a name="issue"></a>Problem

Die folgende Nachricht wird angezeigt, wenn Sie versuchen, einen Hybrid Runbook Worker mithilfe des Python-Skripts `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` hinzuzufügen:

```error
Unable to register, an existing worker was found. Please deregister any existing worker and try again.
```

Darüber hinaus wird versucht, die Registrierung eines Hybrid Runbook Worker mithilfe des Python-Skripts `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` aufzuheben:

```error
Failed to deregister worker. [response_status=404]
```

#### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn der Computer bereits bei einem anderen Automation-Konto registriert ist, die Azure Hybrid Worker-Gruppe gelöscht wurde oder Sie versuchen, den Hybrid Runbook Worker nach dem Entfernen vom Computer wieder hinzuzufügen.

#### <a name="resolution"></a>Lösung

So lösen Sie das Problem:

1. Entfernen Sie den Agent `sudo sh onboard_agent.sh --purge`.

1. Führen Sie diese Befehle aus:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Integrieren Sie den Agent `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` wieder.

1. Warten Sie, bis der Ordner `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` aufgefüllt wurde.

1. Versuchen Sie erneut, das Python-Skript `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` auszuführen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem hier nicht aufgeführt wird, oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet die Azure-Community mit Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
