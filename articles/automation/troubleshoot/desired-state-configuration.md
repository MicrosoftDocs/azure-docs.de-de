---
title: Problembehandlung bei Azure Automation State Configuration
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit Azure Automation State Configuration beheben.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.openlocfilehash: e6caf3fed708e89b55a88719ca5358f6174c2ac8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896527"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Problembehandlung bei Azure Automation State Configuration

Dieser Artikel enthält Informationen zur Behebung von Problemen, die beim Kompilieren oder Bereitstellen von Konfigurationen in Azure Automation State Configuration auftreten können. Allgemeine Informationen zum State Configuration-Feature finden Sie unter [Übersicht über Azure Automation State Configuration](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Ein Problem diagnostizieren

Wenn Sie einen Kompilierungs- oder Bereitstellungsfehler für die Konfiguration erhalten, finden Sie im Folgenden einige Schritte, mit denen Sie das Problem diagnostizieren können.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Sicherstellen, dass Ihre Konfiguration auf dem lokalen Computer erfolgreich kompiliert wird

Azure Automation State Configuration basiert auf PowerShell Desired State Configuration (DSC). Die Dokumentation zur DSC-Sprache und -Syntax finden Sie in der [PowerShell DSC-Dokumentation](/powershell/scripting/overview).

Durch das Kompilieren einer DSC auf Ihrem lokalen Computer können Sie unter anderem die folgenden allgemeinen Fehler ermitteln und beheben:

   - Fehlende Module.
   - Syntaxfehler.
   - Logikfehler.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Anzeigen der DSC-Protokolle auf Ihrem Knoten

Wenn Ihre Konfiguration erfolgreich kompiliert wird, aber nicht auf einen Knoten angewendet werden kann, finden Sie in den DSC-Protokollen ausführliche Informationen. Informationen zum Speicherort dieser Protokolle finden Sie unter [Wo befinden sich die DSC-Ereignisprotokolle?](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Das [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)-Modul kann Ihnen helfen, die Informationen in den DSC-Protokollen analysieren. Wenn Sie sich an den Support wenden, fordert der diese Protokolle zur Diagnose des Problems an.

Sie können das `xDscDiagnostics`-Modul mithilfe der Anleitungen unter [Installation der stabilen Modulversion](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module) auf Ihrem lokalen Computer installieren.

Um das `xDscDiagnostics`-Modul auf Ihrem Azure-Computer zu installieren, verwenden Sie [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand). Sie können auch die Option **Befehl ausführen** im Azure-Portal verwenden, indem Sie die Schritte unter [Ausführen von PowerShell-Skripts in Ihrer Windows-VM mit „Befehl ausführen“](../../virtual-machines/windows/run-command.md) verwenden.

Informationen zur Verwendung von **xDscDiagnostics** finden Sie unter [Verwenden von „xDscDiagnostics“ zum Analysieren von DSC-Protokollen](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Siehe auch [xDscDiagnostics-Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Sicherstellen, dass Knoten und der Automation-Arbeitsbereich über die erforderlichen Module verfügen

DSC ist von den Modulen abhängig, die auf dem Knoten installiert sind. Wenn Sie Azure Automation State Configuration verwenden, importieren Sie alle benötigten Module in Ihr Automation-Konto, indem Sie die Schritte unter [Importieren von Modulen](../shared-resources/modules.md#import-modules) ausführen. Konfigurationen können auch von bestimmten Modulversionen abhängen. Weitere Informationen finden Sie unter [Problembehandlung bei Modulen](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Szenario: Eine Konfiguration mit speziellen Zeichen kann nicht aus dem Portal gelöscht werden

### <a name="issue"></a>Problem

Wenn Sie versuchen, eine DSC-Konfiguration aus dem Portal zu löschen, wird der folgende Fehler angezeigt:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Ursache

Der Fehler ist ein vorübergehendes Problem, dessen Behebung geplant ist.

### <a name="resolution"></a>Lösung

Verwenden Sie das Cmdlet [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration), um die Konfiguration zu löschen.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Szenario: Fehler beim Registrieren des DSC-Agents

### <a name="issue"></a>Problem

Bei [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) oder einem anderes DSC-Cmdlet wird folgender Fehler zurückgegeben:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Ursache

Dieser Fehler wird normalerweise durch eine Firewall, durch einen Computer hinter einem Proxyserver oder durch andere Netzwerkfehler verursacht.

### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass Ihr Computer Zugriff auf die richtigen Endpunkte für DSC hat, und wiederholen Sie den Vorgang. Eine Liste der erforderlichen Ports und Adressen finden Sie unter [Netzwerkplanung](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Szenario: Antwortcode „Nicht autorisiert“ in Statusberichten

### <a name="issue"></a>Problem

Wenn Sie einen Knoten mit Azure Automation State Configuration registrieren, erhalten Sie eine der folgenden Fehlermeldungen:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Ursache

Dieses Problem wird durch ein ungültiges oder abgelaufenes Zertifikat verursacht. Weitere Informationen finden Sie unter [Erneutes Registrieren eines Knotens](../automation-dsc-onboarding.md#re-register-a-node).

Dieses Problem kann auch durch eine Proxykonfiguration verursacht werden, die den Zugriff auf **_.azure-automation.net_* nicht zulässt. Weitere Informationen finden Sie unter [Konfiguration privater Netzwerke](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Lösung

Führen Sie die folgenden Schritte aus, um den fehlerhaften DSC-Knoten neu zu registrieren.

#### <a name="step-1-unregister-the-node"></a>Schritt 1: Aufheben der Registrierung des Knotens

1. Wechseln Sie im Azure-Portal zu **Startseite** > **Automation-Konten** > [Ihr Automation-Konto] > **State Configuration (DSC)** .
1. Wählen Sie **Knoten** aus, und klicken Sie anschließend auf den fehlerhaften Knoten.
1. Wählen Sie **Registrierung aufheben** aus, um die Registrierung des Knotens aufzuheben.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Schritt 2: Deinstallieren der DSC-Erweiterung vom Knoten

1. Wechseln Sie im Azure-Portal zu **Startseite** > **Virtueller Computer** > (fehlerhafter Knoten) > **Erweiterungen**.
1. Wählen Sie **Microsoft.Powershell.DSC** aus, die PowerShell DSC-Erweiterung.
1. Wählen Sie **Deinstallieren** aus, um die Erweiterung zu deinstallieren.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Schritt 3: Entfernen aller ungültigen oder abgelaufenen Zertifikate vom Knoten

Führen Sie auf dem fehlerhaften Knoten die folgenden Befehle an einer PowerShell-Eingabeaufforderung mit erhöhten Rechten aus:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>Schritt 4: Erneutes Registrieren des fehlerhaften Knotens

1. Wechseln Sie im Azure-Portal zu **Startseite** > **Automation-Konten** > [Ihr Automation-Konto] > **State Configuration (DSC)** .
1. Wählen Sie **Knoten** aus.
1. Wählen Sie **Hinzufügen**.
1. Wählen Sie den fehlerhaften Knoten aus.
1. Wählen Sie **Verbinden** aus, und wählen Sie die gewünschten Optionen aus.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Szenario: Knoten hat den Fehlerstatus „Nicht gefunden“

### <a name="issue"></a>Problem

Der Knoten hat den Status „Fehler“ mit folgender Meldung zurückgegeben:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt normalerweise auf, wenn der Knoten einem Konfigurationsnamen (z. B. **ABC**) anstatt einem Knotenkonfigurationsnamen (z. B. **ABC.WebServer**) zugewiesen ist.

### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass Sie den Knoten mit dem Knotenkonfigurationsnamen zuweisen, nicht mit dem Konfigurationsnamen zuweisen.
* Einem Knoten können Sie über das Azure-Portal oder mit einem PowerShell-Cmdlet eine Knotenkonfiguration zuweisen.

  * Wechseln Sie im Azure-Portal zu **Startseite** > **Automation-Konten** > [Ihr Automation-Konto] > **State Configuration (DSC)** . Wählen Sie dann einen Knoten aus, und wählen Sie **Knotenkonfiguration zuweisen** aus.
  * Verwenden Sie das Cmdlet [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode).

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Szenario: Bei der Kompilierung einer Konfiguration wurden keine Knotenkonfigurationen (MOF-Dateien) erstellt

### <a name="issue"></a>Problem

Ihr DSC-Kompilierungsauftrag wird mit folgendem Fehler abgebrochen:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Ursache

Wenn der Ausdruck nach dem Schlüsselwort `Node` in der DSC-Konfiguration als `$null` ausgewertet wird, werden keine Knotenkonfigurationen erstellt.

### <a name="resolution"></a>Lösung

Beheben Sie dieses Problem mit einer der folgenden Lösungen:

* Stellen Sie sicher, dass der Ausdruck neben dem Schlüsselwort `Node` in der Konfigurationsdefinition nicht als NULL ausgewertet wird.
* Wenn Sie bei der Kompilierung der Konfiguration [ConfigurationData](../automation-dsc-compile.md) übergeben, stellen Sie sicher, dass Sie die Werte übergeben, die von der Konfiguration aus den Konfigurationsdaten erwartet werden.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Szenario: Der DSC-Knotenbericht bleibt im Status „In Bearbeitung“ hängen

### <a name="issue"></a>Problem

Der DSC-Agent gibt Folgendes aus:

```error
No instance found with given property values
```

### <a name="cause"></a>Ursache

Sie haben ein Upgrade Ihrer WMF-Version (Windows Management Framework) durchgeführt, und dabei wurde die Windows-Verwaltungsinstrumentation (Windows Management Instrumentation, WMI) beschädigt.

### <a name="resolution"></a>Lösung

Befolgen Sie die Anweisungen unter [Bekannte Probleme und Einschränkungen in DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Szenario: In einer DSC-Konfiguration können keine Anmeldeinformationen verwendet werden

### <a name="issue"></a>Problem

Ihr DSC-Kompilierungsauftrag wurde mit folgendem Fehler abgebrochen:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Ursache

Sie haben Anmeldeinformationen in einer Konfiguration verwendet, aber keine ordnungsgemäßen `ConfigurationData` angegeben, mit denen `PSDscAllowPlainTextPassword` für jede Knotenkonfiguration auf „true“ festgelegt wird.

### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass Sie die ordnungsgemäßen `ConfigurationData` übergeben, mit denen `PSDscAllowPlainTextPassword` für jede in der Konfiguration angegebene Knotenkonfiguration auf „true“ festgelegt wird. Weitere Informationen finden Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Szenario: „Fehler beim Verarbeiten der Erweiterung“ beim Aktivieren eines Computers über eine DSC-Erweiterung

### <a name="issue"></a>Problem

Beim Aktivieren eines Computers mithilfe einer DSC-Erweiterung tritt der folgende Fehler auf:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt in der Regel auf, wenn dem Knoten ein Knotenkonfigurationsname zugewiesen wurde, der im Dienst nicht vorhanden ist.

### <a name="resolution"></a>Lösung

* Stellen Sie sicher, dass Sie dem Knoten einen Namen zuweisen, der genau mit dem im Dienst vorhandenen Namen übereinstimmt.
* Sie können festlegen, dass kein Knotenkonfigurationsname angegeben werden soll. In diesem Fall erfolgt eine Aktivierung des Knotens, aber keine Zuweisung einer Knotenkonfiguration.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Szenario: Fehler „Es ist mindestens ein Fehler aufgetreten“ beim Registrieren eines Knotens mit PowerShell

### <a name="issue"></a>Problem

Wenn Sie einen Knoten mithilfe von [Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) oder [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) registrieren, erhalten Sie folgenden Fehler:

```error
One or more errors occurred.
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn Sie versuchen, einen Knoten in einem anderen als dem vom Automation-Konto verwendeten Abonnement zu registrieren.

### <a name="resolution"></a>Lösung

Behandeln Sie den Knoten aus dem anderen Abonnement so, als wäre er für eine separate Cloud oder als lokaler Knoten definiert. Registrieren Sie den Knoten mit einer der folgenden Optionen für die Aktivierung von Computern:

* Windows: [Physische/virtuelle Windows-Computer, lokal oder in einer anderen Cloud als Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [Physische/virtuelle Linux-Computer, lokal oder in einer anderen Cloud als Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Szenario: Fehlermeldung „Fehler beim Bereitstellen“

### <a name="issue"></a>Problem

Beim Registrieren eines Knotens tritt der folgende Fehler auf:

```error
Provisioning has failed
```

### <a name="cause"></a>Ursache

Diese Meldung ist auf ein Konnektivitätsproblem zwischen dem Knoten und Azure zurückzuführen.

### <a name="resolution"></a>Lösung

Ermitteln Sie, ob sich Ihr Knoten in einem virtuellen privaten Netzwerk (VPN) befindet oder ob andere Probleme beim Herstellen einer Verbindung mit Azure vorliegen. Weitere Informationen finden Sie unter [Beheben von Problemen bei der Featurebereitstellung](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Szenario: Allgemeiner Fehler beim Anwenden einer Konfiguration in Linux

### <a name="issue"></a>Problem

Wenn Sie eine Konfiguration in Linux anwenden, bei der der folgende Fehler auftritt:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Ursache

Wenn der Speicherort **/tmp** auf `noexec` festgelegt ist, kann die aktuelle DSC-Version keine Konfigurationen anwenden.

### <a name="resolution"></a>Lösung

Entfernen Sie die Option `noexec` aus dem Speicherort **/tmp**.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Szenario: Sich überschneidende Knotenkonfigurationsnamen können zu einem fehlerhaften Release führen

### <a name="issue"></a>Problem

Wenn Sie zum Generieren mehrerer Knotenkonfigurationen ein einzelnes Skript verwenden und einige der Knotenkonfigurationsnamen in anderen Namen enthalten sind, kann es passieren, dass der Kompilierungsdienst die falsche Konfiguration zuweist. Dieses Problem tritt nur dann auf, wenn ein einzelnes Skript zum Generieren von Konfigurationen mit Konfigurationsdaten pro Knoten verwendet wird und die Namensüberschneidung am Anfang der Zeichenfolge auftritt. Beispiel: Ein einzelnes Konfigurationsskript wird zum Generieren von Konfigurationen auf der Grundlage von Knotendaten verwendet, die mit Cmdlets als Hashtabelle übergeben werden, und die Knotendaten enthalten Server namens **server** und **1server**.

### <a name="cause"></a>Ursache

Dies ist ein bekanntes Problem beim Kompilierungsdienst.

### <a name="resolution"></a>Lösung

Dieses Problem lässt sich am besten umgehen, indem Sie die Kompilierung lokal oder in einer CI/CD-Pipeline ausführen und die MOF-Dateien mit der Knotenkonfiguration direkt in den Dienst hochladen. Muss die Kompilierung im Dienst erfolgen, besteht die zweitbeste Problemumgehung darin, die Kompilierungsaufträge aufzuteilen, sodass es keine Überschneidungen bei den Namen gibt.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Szenario: Gatewaytimeoutfehler beim Hochladen der DSC-Konfiguration

#### <a name="issue"></a>Problem

Wenn Sie eine DSC-Konfiguration hochladen, erhalten Sie einen `GatewayTimeout`-Fehler. 

### <a name="cause"></a>Ursache

Bei DSC-Konfigurationen, deren Kompilierung lange Zeit in Anspruch nimmt, kann dieser Fehler verursacht werden.

### <a name="resolution"></a>Lösung

Sie können die Analyse Ihrer DSC-Konfigurationen beschleunigen, indem Sie den `ModuleName`-Parameter explizit für alle [Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource)-Aufrufe einschließen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem hier nicht aufgeführt wird, oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet die Azure-Community mit Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
