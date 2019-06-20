---
title: Problembehandlung für Hybrid Runbook Worker von Azure Automation
description: Dieser Artikel bietet Informationen zur Problembehandlung für Hybrid Runbook Worker von Azure Automation.
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ea6599152d3cbf1f50132f5b207c19148401f798
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564258"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Problembehandlung für Hybrid Runbook Worker

Dieser Artikel bietet Informationen zur Problembehandlung für Hybrid Runbook Worker.

## <a name="general"></a>Allgemein

Der Hybrid Runbook Worker ist von einem Agent abhängig, um mit Ihrem Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbookaufträge zu erhalten und den Status zu melden. Bei Windows ist dies der Microsoft Monitoring Agent. Bei Linux ist dies der OMS-Agent für Linux.

### <a name="runbook-execution-fails"></a>Szenario: Fehler bei der Runbookausführung

#### <a name="issue"></a>Problem

Die Runbook-Ausführung wird mit folgender Fehlermeldung abgebrochen:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Kurz nachdem es versucht hat, sich dreimal auszuführen, wird Ihr Runbook angehalten. Es gibt Bedingungen, die den Abschluss des Runbooks unterbrechen können. In diesem Fall enthält die entsprechende Fehlermeldung nicht unbedingt zusätzliche Informationen, aus denen Sie den Grund ersehen können.

#### <a name="cause"></a>Ursache

Dafür gibt es drei mögliche Ursachen:

* Die Runbooks können nicht bei lokalen Ressourcen authentifiziert werden

* Der Hybrid Worker befindet sich hinter einem Proxy oder einer Firewall.

* Die Runbooks können nicht bei lokalen Ressourcen authentifiziert werden

* Der für die Ausführung des Hybrid Runbook Worker-Features konfigurierte Computer erfüllt die Hardwaremindestanforderungen nicht.

#### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass der Computer über einen ausgehenden Zugriff auf „*.azure-automation.net“ über Port 443 verfügt.

Computer, auf denen der Hybrid Runbook Worker ausgeführt werden soll, müssen die Mindestanforderungen an die Hardware erfüllen, damit sie als Host für dieses Feature konfiguriert werden können. Runbooks und die Hintergrundprozesse, die sie verwenden, führen möglicherweise zu einer starken Auslastung des Systems sowie zu Verzögerungen und Timeouts des Runbook-Auftrags.

Vergewissern Sie sich, dass der für die Ausführung des Hybrid Runbook Worker-Features vorgesehene Computer die Hardwaremindestanforderungen erfüllt. Wenn dies der Fall ist, überwachen Sie die CPU- und Arbeitsspeicherauslastung, um Zusammenhänge zwischen der Leistung der Hybrid Runbook Worker-Prozesse und Windows zu erkennen. Wenn der Arbeitsspeicher oder die CPU stark ausgelastet sind, kann dies auf die Notwendigkeit für ein Upgrade von Ressourcen hindeuten. Sie können auch eine andere Computeressource auswählen, die die Mindestanforderungen erfüllt. Bei entsprechend großem Workload skalieren Sie sie nach Bedarf.

Prüfen Sie, ob im Ereignisprotokoll **Microsoft-SMA** ein entsprechendes Ereignis mit der Beschreibung *Win32-Prozess wurde mit dem Code [4294967295] beendet*vorhanden ist. Die Ursache dieses Fehlers liegt darin, dass Sie in Ihren Runbooks die Authentifizierung nicht konfiguriert haben oder dass Sie die „Ausführen als“-Anmeldeinformationen für die Hybrid Worker-Gruppe nicht angegeben haben. Überprüfen Sie die [Runbookberechtigungen](../automation-hrw-run-runbooks.md#runbook-permissions), und vergewissern Sie sich, dass Sie die Authentifizierung für Ihre Runbooks ordnungsgemäß konfiguriert haben.

### <a name="no-cert-found"></a>Szenario: Es wurde kein Zertifikat im Zertifikatspeicher auf dem Hybrid Runbook Worker gefunden.

#### <a name="issue"></a>Problem

Ein Runbook, das auf einem Hybrid Runbook Worker ausgeführt wird, schlägt mit der folgenden Fehlermeldung fehl:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn Sie versuchen, ein [„Ausführen als“-Konto](../manage-runas-account.md) in einem Runbook zu verwenden, das auf einem Hybrid Runbook Worker ausgeführt wird, auf dem das Zertifikat des „Ausführen als“-Kontos nicht vorhanden ist. Hybrid Runbook Worker enthält nicht standardmäßig das Zertifikatobjekt lokal, das für die ordnungsgemäße Funktion des „Ausführen als“-Kontos erforderlich ist.

#### <a name="resolution"></a>Lösung

Wenn Ihr Hybrid Runbook Worker ein Azure VM ist, können Sie stattdessen [verwaltete Identitäten für Azure-Ressourcen](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) verwenden. Dieses Szenario gestattet Ihnen die Authentifizierung bei Azure-Ressourcen mithilfe der verwalteten Identität der Azure-VM anstelle des „Ausführen als“-Kontos, was die Authentifizierung vereinfacht. Wenn der Hybrid Runbook Worker ein lokaler Computer ist, müssen Sie das Zertifikat des „Ausführen als“-Kontos auf dem Computer installieren. Informationen zum Installieren des Zertifikats finden Sie in den Schritten zum Ausführen des [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script)-Runbooks.

## <a name="linux"></a>Linux

Der Linux Hybrid Runbook Worker ist abhängig vom OMS-Agent für Linux, um mit Ihrem Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbook-Aufträge zu erhalten und den Status zu melden. Wenn bei der Registrierung des Workers ein Fehler auftritt, gibt es mehrere mögliche Gründe dafür:

### <a name="oms-agent-not-running"></a>Szenario: Der OMS-Agent für Linux wird nicht ausgeführt.

#### <a name="issue"></a>Problem

Der OMS-Agent für Linux wird nicht ausgeführt

#### <a name="cause"></a>Ursache

Wenn der OMS-Agent für Linux nicht ausgeführt wird, verhindert er die Kommunikation zwischen dem Linux Hybrid Runbook Worker und Azure Automation. Der Agent kann aus verschiedenen Gründen nicht ausgeführt werden.

#### <a name="resolution"></a>Lösung

 Überprüfen Sie, dass der Agent ausgeführt wird, indem Sie den folgenden Befehl eingeben: `ps -ef | grep python`. Die Ausgabe sollte in etwa folgendermaßen aussehen, die Python-Prozesse mit dem **nxautomation**-Benutzerkonto. Wenn die Updateverwaltung oder Azure Automation nicht aktiviert ist, wird keiner der folgenden Prozesse ausgeführt.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Die folgende Liste enthält die Prozesse, die für einen Linux Hybrid Runbook Worker gestartet werden. Sie befinden sich alle im Verzeichnis `/var/opt/microsoft/omsagent/state/automationworker/`.


* **oms.conf**: Dieser Wert ist der Worker-Manager-Prozess. Er wird direkt vom DSC gestartet.

* **worker.conf**: Der automatisch registrierte Hybrid Worker-Prozess. Er wird vom Worker-Manager gestartet. Dieser Prozess dient der Updateverwaltung und ist für den Benutzer transparent. Dieser Prozess ist nicht vorhanden, wenn die Updateverwaltung auf dem Computer nicht aktiviert ist.

* **diy/worker.conf**: Dieser Prozess ist der eigenständige Hybrid Worker-Prozess. Der eigenständige Hybrid Worker-Prozess wird verwendet, um Benutzerrunbooks auf dem Hybrid Runbook Worker auszuführen. Er unterscheidet sich vom automatisch registrierten Hybrid Worker-Prozess nur in einem wichtigsten Detail, nämlich, dass eine andere Konfiguration verwendet wird. Dieser Prozess ist nicht vorhanden, wenn Azure Automation deaktiviert und der eigenständige Linux Hybrid Worker nicht registriert ist.

Wenn der OMS-Agent für Linux nicht ausgeführt wird, führen Sie zum Starten des Diensts den folgenden Befehl aus: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Szenario: Die angegebene Klasse ist nicht vorhanden

Bei folgendem Fehler: **Die angegebene Klasse ist nicht vorhanden.** in `/var/opt/microsoft/omsconfig/omsconfig.log` angezeigt wird, muss der OMS-Agent für Linux aktualisiert werden. Führen Sie den folgenden Befehl aus, um den OMS-Agent neu zu installieren:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Der Windows Hybrid Runbook Worker ist abhängig vom Microsoft Monitoring Agent, um mit Ihrem Automation-Konto für die Registrierung des Workers zu kommunizieren, Runbook-Aufträge zu empfangen und den Status zu melden. Wenn bei der Registrierung des Workers ein Fehler auftritt, gibt es mehrere mögliche Gründe dafür:

### <a name="mma-not-running"></a>Szenario: Microsoft Monitoring Agent wird nicht ausgeführt.

#### <a name="issue"></a>Problem

Auf dem Computer mit dem Hybrid Runbook Worker wird der Dienst `healthservice` nicht ausgeführt.

#### <a name="cause"></a>Ursache

Wenn der Microsoft Monitoring Agent-Dienst von Windows nicht ausgeführt wird, verhindert dieser Zustand die Kommunikation zwischen Hybrid Runbook Worker und Azure Automation.

#### <a name="resolution"></a>Lösung

Überprüfen Sie, dass der Agent ausgeführt wird, indem Sie den folgenden Befehl in PowerShell eingeben: `Get-Service healthservice`. Wenn der Dienst beendet wurde, geben Sie den folgenden Befehl in PowerShell ein, um ihn wieder zu starten: `Start-Service healthservice`.

### <a name="event-4502"></a> Ereignis 4502 im Operations Manager-Protokoll

#### <a name="issue"></a>Problem

Im Ereignisprotokoll **Anwendungs- und Dienstprotokolle\Operations Manager** werden das Ereignis 4502 und eine Ereignisnachricht mit **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** und folgender Beschreibung angezeigt: *Das durch den Dienst „\<wsid\>.oms.opinsights.azure.com“ vorgelegte Zertifikat wurde nicht durch eine für Microsoft-Dienste verwendete Zertifizierungsstelle ausgestellt. Wenden Sie sich bitte an Ihren Netzwerkadministrator, um zu erfahren, ob ein Proxy ausgeführt wird, das die TLS/SSL-Kommunikation abfängt. Im Artikel KB3126513 sind weitere Informationen zur Behandlung von Verbindungsproblemen enthalten.*

#### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn Ihr Proxy oder Ihre Netzwerkfirewall die Kommunikation mit Microsoft Azure blockiert. Stellen Sie sicher, dass der Computer über einen ausgehenden Zugriff auf *.azure-automation.net über Port 443 verfügt.

#### <a name="resolution"></a>Lösung

Protokolle werden lokal auf jedem Hybridworker unter "C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes" gespeichert. Sie können überprüfen, ob die Ereignisprotokolle **Anwendungs- und Dienstprotokolle\Microsoft-SMA\Operations** und **Anwendungs- und Dienstprotokolle\Operations Manager** Warnungen oder Fehlerereignisse enthalten, die darauf hindeuten, dass ein Verbindungsproblem oder ein anderes Problem das Onboarding der Rolle in Azure Automation beeinträchtigt oder ein Problem bei der Ausführung regulärer Vorgänge vorliegt.

[Runbookausgabe und -meldungen](../automation-runbook-output-and-messages.md) werden von Hybridworkern an Azure Automation gesendet (genau wie Runbookaufträge, die in der Cloud ausgeführt werden). Sie können auch die Verbose- und Progress-Streams auf dieselbe Weise aktivieren wie für andere Runbooks.

### <a name="corrupt-cache"></a> Keine Meldungen vom Hybrid Runbook Worker

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

Melden Sie sich zur Behebung dieses Problems beim Hybrid Runbook Worker an, und führen Sie das folgende Skript aus. Dieses Skript beendet den Microsoft Monitoring Agent, entfernt den dazugehörigen Cache und startet den Dienst neu. Dadurch muss der Hybrid Runbook Worker seine Konfiguration erneut aus Azure Automation herunterladen.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Szenario: Sie können keinen Hybrid Runbook Worker hinzufügen

#### <a name="issue"></a>Problem

Sie empfangen die folgende Nachricht bei dem Versuch, einen Hybrid Runbook Worker mithilfe des `Add-HybridRunbookWorker`-Cmdlets hinzuzufügen.

```error
Machine is already registered
```

#### <a name="cause"></a>Ursache

Dies kann dadurch verursacht werden, dass der Computer bereits bei einem anderen Automation-Konto registriert ist oder Sie versuchen, den Hybrid Runbook Worker nach dem Entfernen vom Computer wieder hinzuzufügen.

#### <a name="resolution"></a>Lösung

Um dieses Problem zu lösen, entfernen Sie den folgenden Registrierungsschlüssel und starten den `HealthService` erneut, und versuchen Sie erneut, das `Add-HybridRunbookWorker`-Cmdlet auszuführen:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Mit [@AzureSupport](https://twitter.com/azuresupport) verbinden – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.

