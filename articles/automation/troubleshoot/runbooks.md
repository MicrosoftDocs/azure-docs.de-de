---
title: Beheben von Fehlern bei Azure Automation-Runbooks
description: Erfahren Sie, wie Sie Probleme mit Azure Automation-Runbooks beheben.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 65de80004dd05e3eb29f3313bc17405c40450d7a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397125"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Beheben von Fehlern bei Runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Authentifizierungsfehler beim Verwenden von Azure Automation-Runbooks

### <a name="sign-in-failed"></a>Szenario: Fehler beim Anmelden beim Azure-Konto

#### <a name="issue"></a>Problem

Beim Arbeiten mit den Cmdlets `Add-AzureAccount` oder `Connect-AzureRmAccount` wird der folgende Fehler angezeigt.
:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn der Objektname für die Anmeldeinformationen ungültig ist. Er kann auch auftreten, wenn Benutzername und Kennwort, die Sie zur Einrichtung des Automation-Anmeldeinformationsobjekts verwendet haben, nicht gültig sind.

#### <a name="resolution"></a>Lösung

Führen Sie die folgenden Schritte aus, um zu ermitteln, wo der Fehler liegt:  

1. Stellen Sie sicher, dass Sie keine Sonderzeichen eingegeben haben. Dazu zählt auch das **\@** -Zeichen im Namen des Automation-Anmeldeinformationsobjekts, mit dem Sie die Verbindung zu Azure herstellen.  
2. Überprüfen Sie, ob Sie den Benutzernamen und das Kennwort aus den Azure Automation-Anmeldeinformationen in Ihrem lokalen PowerShell ISE-Editor verwenden können. Sie können überprüfen, ob Benutzername und Kennwort korrekt sind, indem Sie die folgenden Cmdlets in der PowerShell ISE ausführen:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Wenn die Authentifizierung lokal fehlschlägt, bedeutet dies, dass Sie Ihre Azure Active Directory-Anmeldeinformationen nicht richtig eingerichtet haben. Informationen zur richtigen Einrichtung des Azure Active Directory-Kontos finden Sie im Blogbeitrag [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) (Authentifizieren in Azure mit Azure Active Directory).  

4. Wenn es sich um einen vorübergehenden Fehler zu handeln scheint, versuchen Sie, eine Wiederholungslogik zu Ihrer Authentifizierungsroutine hinzuzufügen, um die Authentifizierung zuverlässiger zu gestalten.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Szenario: Azure-Abonnement nicht gefunden

#### <a name="issue"></a>Problem

Beim Arbeiten mit den Cmdlets `Select-AzureSubscription` oder `Select-AzureRmSubscription` wird der folgende Fehler angezeigt:

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

Mögliche Ursachen für diesen Fehler:

* Der Abonnementname ist ungültig

* Der Azure Active Directory-Benutzer, der die Abonnementdetails abrufen möchte, ist nicht als Administrator des Abonnements konfiguriert.

#### <a name="resolution"></a>Lösung

Führen Sie die folgenden Schritte aus, um zu ermitteln, ob die Authentifizierung in Azure erfolgt ist und ob Sie auf das gewünschte Abonnement zugreifen können:  

1. Testen Sie Ihr Skript außerhalb von Azure Automation, um sicherzustellen, dass es eigenständig verwendet werden kann.
2. Achten Sie darauf, dass Sie das Cmdlet `Add-AzureAccount` vor dem Cmdlet `Select-AzureSubscription` ausführen. 
3. Fügen Sie am Anfang Ihres Runbooks `Disable-AzureRmContextAutosave –Scope Process` hinzu. Dieses Cmdlet stellt sicher, dass alle Anmeldeinformationen nur für die Ausführung des aktuellen Runbooks gelten.
4. Wenn diese Fehlermeldung weiterhin angezeigt wird, sollten Sie Ihren Code ändern, indem Sie den Parameter **AzureRmContext** nach dem Cmdlet `Add-AzureAccount` hinzufügen und dann den Code ausführen.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Szenario: Fehler beim Authentifizieren bei Azure, da die mehrstufige Authentifizierung aktiviert ist

#### <a name="issue"></a>Problem

Sie erhalten bei der Authentifizierung bei Azure mit Ihrem Azure-Benutzernamen und -Kennwort die folgende Fehlermeldung:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Ursache

Falls für Ihr Azure-Konto die mehrstufige Authentifizierung eingerichtet ist, können Sie für die Authentifizierung bei Azure keinen Azure Active Directory-Benutzer verwenden. Stattdessen müssen Sie ein Zertifikat oder einen Dienstprinzipal für die Authentifizierung gegenüber Azure verwenden.

#### <a name="resolution"></a>Lösung

Informationen zum Verwenden eines Zertifikats mit den klassischen Azure-Bereitstellungsmodell finden Sie unter [Managing Azure Services with the Microsoft Azure Automation Preview Service](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) (Verwalten von Azure Services mit dem Microsoft Azure Automation Preview-Dienst). Informationen zum Verwenden eines Dienstprinzipals mit Azure Resource Manager-Cmdlets finden Sie unter [Erstellen eines Dienstprinzipals mit dem Azure-Portal](../../active-directory/develop/howto-create-service-principal-portal.md) und [Authentifizieren eines Dienstprinzipals mit dem Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="common-errors-when-working-with-runbooks"></a>Häufige Fehler beim Verwenden von Runbooks

### <a name="child-runbook-object"></a>Das untergeordnete Runbook gibt Fehler zurück, wenn der Ausgabestream statt einfachen Datentypen Objekte enthält

#### <a name="issue"></a>Problem

Die folgende Fehlermeldung wird angezeigt, wenn Sie ein untergeordnetes Runbook mit dem `-Wait`-Switch aufrufen und der Ausgabestream ein Objekt enthält:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Ursache

Es gibt ein bekanntes Problem, bei dem [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) den Ausgabestream nicht ordnungsgemäß behandelt, wenn er Objekte enthält.

#### <a name="resolution"></a>Lösung

Implementieren Sie eine Abruflogik, und verwenden Sie das Cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput), um die Ausgabe abzurufen. Nachfolgend sehen Sie ein Beispiel für diese Logik.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Szenario: In Ihren Auftragsdatenströmen wird ein Fehler zur get_SerializationSettings-Methode angezeigt

#### <a name="issue"></a>Problem

Sie sehen einen Fehler in Ihren Auftragsdatenströmen für ein Runbook mit der folgenden Nachricht:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Ursache

Dieser Fehler wird durch die kombinierte Verwendung von AzureRM- und Az-Cmdlets in einem Runbook verursacht. Er tritt auf, wenn Sie `Az` vor dem Importieren von `AzureRM` importieren.

#### <a name="resolution"></a>Lösung

Az- und AzureRM-Cmdlets können nicht im gleichen Runbook importiert und verwendet werden. Weitere Informationen zur Az-Unterstützung in Azure Automation finden Sie unter [Unterstützung für Az-Module in Azure Automation](../az-modules.md).

### <a name="task-was-cancelled"></a>Szenario: Beim Runbook tritt folgender Fehler auf: Eine Aufgabe wurde abgebrochen.

#### <a name="issue"></a>Problem

Für Ihr Runbook tritt ein Fehler auf, der dem im folgenden Beispiel ähnelt:

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Ursache

Dieser Fehler kann durch veraltete Azure-Module verursacht werden.

#### <a name="resolution"></a>Lösung

Dieser Fehler kann behoben werden, indem Sie Ihre Azure-Module auf die neueste Version aktualisieren.

Klicken Sie in Ihrem Automation-Konto auf **Module** und anschließend auf **Azure-Module aktualisieren**. Das Update dauert ungefähr 15 Minuten. Führen Sie anschließend das Runbook, bei dem der Fehler aufgetreten ist, erneut aus. Weitere Informationen zum Aktualisieren Ihrer Module finden Sie unter [Aktualisieren von Azure-Modulen in Azure Automation](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Szenario: Fehler bei Runbooks, wenn mehrere Abonnements verwendet werden

#### <a name="issue"></a>Problem

Beim Ausführen von Runbooks mit `Start-AzureRmAutomationRunbook` kann das Runbook Azure-Ressourcen nicht verwalten.

#### <a name="cause"></a>Ursache

Das Runbook verwendet beim Ausführen nicht den richtigen Kontext.

#### <a name="resolution"></a>Lösung

Wenn Sie mit mehreren Abonnements arbeiten, geht beim Aufrufen von Runbooks unter Umständen der Abonnementkontext verloren. Um sicherzustellen, dass der Abonnementkontext an die Runbooks übergeben wird, fügen Sie dem Cmdlet den Parameter `AzureRmContext` hinzu und übergeben darin den Kontext. Außerdem wird empfohlen, das Cmdlet `Disable-AzureRmContextAutosave` mit dem Bereich **Prozess** zu verwenden. So wird sichergestellt, dass die von Ihnen genutzten Anmeldeinformationen nur für das aktuelle Runbook verwendet werden.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Szenario: Runbookfehler aufgrund eines fehlenden Cmdlets

#### <a name="issue"></a>Problem

Für Ihr Runbook tritt ein Fehler auf, der dem im folgenden Beispiel ähnelt:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Ursache

Mögliche Ursachen für diesen Fehler:

1. Das Modul, das das Cmdlet enthält, wird nicht in das Automationkonto importiert.
2. Das Modul, das das Cmdlet enthält, wird zwar importiert, ist jedoch veraltet.

#### <a name="resolution"></a>Lösung

Dieser Fehler kann behoben werden, indem Sie eine der folgenden Aufgaben ausführen:

Wenn es sich bei dem Modul um ein Azure-Modul handelt, finden Sie weitere Informationen zum Aktualisieren Ihrer Module in Ihrem Automation-Konto unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](../automation-update-azure-modules.md).

Wenn es ein separates Modul ist, sollten Sie sicherstellen, dass es in Ihr Automationkonto importiert wird.

### <a name="job-attempted-3-times"></a>Szenario: Es wurde dreimal erfolglos versucht, den Runbookauftrag zu starten

#### <a name="issue"></a>Problem

Ihr Runbook schlägt mit dem folgenden Fehler fehl:

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Ursache

Mögliche Ursachen für diesen Fehler:

1. Arbeitsspeicherlimit. Die Dokumentation der Grenzwerte, die für das Zuordnen von Arbeitsspeicher zu einer Sandbox gelten, finden Sie unter [Automatisierungsgrenzwerte](../../azure-subscription-service-limits.md#automation-limits). Für einen Auftrag kann ein Fehler auftreten, wenn dafür mehr als 400 MB Arbeitsspeicher verwendet werden.

2. Netzwerksockets. Azure-Sandboxes sind auf 1.000 gleichzeitige Netzwerksockets beschränkt, wie unter [Automatisierungsgrenzwerte](../../azure-subscription-service-limits.md#automation-limits) beschrieben.

3. Modul inkompatibel. Dieser Fehler kann auftreten, wenn Modulabhängigkeiten nicht korrekt sind. Wenn dies der Fall ist, gibt Ihr Runbook in der Regel die Benachrichtigung „Befehl wurde nicht gefunden“ oder „Der Parameter kann nicht gebunden werden“.

4. Ihr Runbook versucht, eine ausführbare Datei oder einen Unterprozess in einem Runbook aufzurufen, das in einer Azure-Sandbox ausgeführt wird. Dieses Szenario wird in Azure-Sandboxes nicht unterstützt.

5. Vom Runbook wurden zu viele Ausnahmedaten in den Ausgabestream geschrieben.

#### <a name="resolution"></a>Lösung

Sie können dieses Problem mit jeder der folgenden Lösungen beheben:

* Die empfohlenen Methoden für die Einhaltung des Arbeitsspeichergrenzwerts sind die Aufteilung der Workload zwischen mehreren Runbooks und die Verarbeitung von weniger Daten im Arbeitsspeicher, die Vermeidung des Schreibens von unnötigen Ausgaben aus den Runbooks oder die Ermittlung, wie viele Prüfpunkte Sie in Ihre PowerShell-Workflow-Runbooks schreiben. Sie können die clear-Methode verwenden, z.B. `$myVar.clear()`, um die Variable zu löschen, und dann `[GC]::Collect()` verwenden, um sofort die Garbage Collection durchzuführen. Durch diese Aktionen wird der Speicherbedarf Ihres Runbooks während der Laufzeit reduziert.

* Aktualisieren Sie Ihre Azure-Module, indem Sie die Schritte unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](../automation-update-azure-modules.md) befolgen.  

* Eine andere Lösung ist das Ausführen des Runbooks in einem [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker unterliegen nicht den vom Arbeitsspeicher und Netzwerk vorgegebenen Grenzwerten, die für Azure-Sandboxes gelten.

* Wenn Sie einen Prozess (z.B. .exe oder subprocess.call) in einem Runbook aufrufen müssen, müssen Sie das Runbook auf einem [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md) ausführen.

* Für den Auftragsausgabestream gilt ein Grenzwert von 1 MB. Achten Sie darauf, Aufrufe einer ausführbaren Datei oder eines Teilprozesses in einen Try/Catch-Block einzuschließen. Wenn diese eine Ausnahme auslösen, schreiben Sie den Meldungstext dieser Ausnahme in eine Automation-Variable. Dadurch wird verhindert, dass sie in den Auftragsausgabestream geschrieben wird.

### <a name="fails-deserialized-object"></a>Szenario: Runbookfehler aufgrund eines deserialisierten Objekts

#### <a name="issue"></a>Problem

Ihr Runbook schlägt mit dem folgenden Fehler fehl:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Ursache

Wenn es sich bei Ihrem Runbook um einen PowerShell-Workflow handelt, werden komplexe Objekte in einem deserialisierten Format gespeichert, um den Runbookstatus beizubehalten, sobald der Workflow angehalten wird.

#### <a name="resolution"></a>Lösung

Sie können dieses Problem mit einer der folgenden drei Lösungen beheben:

1. Wenn Sie komplexe Objekte von einem Cmdlet an ein anderes übergeben, sollten Sie diese Cmdlets mit einem InlineScript umschließen.
2. Übergeben Sie den Namen oder Wert, den Sie aus dem komplexen Objekt benötigen, anstatt das gesamte Objekt zu übergeben.
3. Verwenden Sie anstelle eines PowerShell-Workflow-Runbooks ein PowerShell-Runbook.

### <a name="runbook-fails"></a>Szenario: Beim Runbook tritt ein Fehler auf. Es funktioniert jedoch, wenn es lokal ausgeführt wird.

#### <a name="issue"></a>Problem

Beim Skript tritt ein Fehler auf, wenn als Runbook ausgeführt wird. Es funktioniert jedoch, wenn es lokal ausgeführt wird.

#### <a name="cause"></a>Ursache

Mögliche Ursachen für den Fehler beim Ausführen des Skripts als Runbook:

1. Authentifizierungsprobleme
2. Die erforderlichen Module wurden nicht importiert oder sind veraltet.
3. Das Skript fordert möglicherweise zu einer Benutzerinteraktion auf.
4. Einige Module treffen Annahmen über Bibliotheken, die auf Windows-Computern vorhanden sind. Diese Bibliotheken sind in einer Sandbox möglicherweise nicht vorhanden.
5. Einige Module erfordern eine andere .NET Version, als in der Sandbox verfügbar ist.

#### <a name="resolution"></a>Lösung

Mögliche Lösungen für dieses Problem:

1. Prüfen Sie, ob Sie die [Authentifizierung bei Azure](../manage-runas-account.md) ordnungsgemäß durchgeführt haben.
2. Stellen Sie sicher, dass Ihre [Azure-Module importiert wurden und auf dem neuesten Stand sind](../automation-update-azure-modules.md).
3. Vergewissern Sie sich, dass keines Ihrer Cmdlets Informationen anfordert. Dieses Verhalten wird in Runbooks nicht unterstützt.
4. Überprüfen Sie, ob ein Element Ihres Moduls von etwas abhängt, das nicht im Modul enthalten ist.
5. Azure Sandboxes verwenden .NET Framework 4.7.2, wenn ein Modul eine höhere Version verwendet, die nicht funktioniert. In diesem Fall sollten Sie einen [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) verwenden.

Wenn keine dieser Lösungen Ihr Problem behebt, prüfen Sie die [Auftragsprotokolle](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) auf mögliche Ursachen für den Fehler des Runbooks.

### <a name="quota-exceeded"></a>Szenario: Fehler beim Runbookauftrag, da das zugeordnete Kontingent überschritten wurde

#### <a name="issue"></a>Problem

Ihr Runbookauftrag schlägt mit dem folgenden Fehler fehl:

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn die Auftragsausführung das kostenlose Kontingent von 500 Minuten für Ihr Konto überschreitet. Dieses Kontingent gilt für alle Arten von Auftragsausführungsaufgaben. Zu diesen Aufgaben zählen: Testen eines Auftrags, Starten eines Auftrags im Portal, Ausführen eines Auftrags per Webhook und Planen der Ausführung eines Auftrags per Azure-Portal oder in Ihrem Rechenzentrum. Weitere Informationen zu den Preisen für Automation finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Lösung

Wenn Sie mehr als 500 Minuten an Verarbeitungszeit pro Monat nutzen möchten, müssen Sie Ihr Abonnement vom Tarif „Free“ auf den Tarif „Basic“ umstellen. Sie können das Upgrade auf den Tarif „Basic“ mit den folgenden Schritten durchführen:  

1. Melden Sie sich bei Ihrem Azure-Abonnement an.  
2. Wählen Sie das Automation-Konto aus, das Sie aktualisieren möchten.  
3. Klicken Sie auf **Einstellungen** > **Preise**.
4. Klicken Sie am unteren Seitenrand auf **Aktivieren**, um Ihr Konto mit dem Tarif **Basic** zu aktualisieren.

### <a name="cmdlet-not-recognized"></a>Szenario: Cmdlet wird beim Ausführen eines Runbooks nicht erkannt

#### <a name="issue"></a>Problem

Ihr Runbookauftrag schlägt mit dem folgenden Fehler fehl:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Ursache

Dieser Fehler wird verursacht, wenn die PowerShell-Engine das Cmdlet nicht findet, das Sie in Ihrem Runbook verwenden. Das kann daran liegen, weil das Modul mit dem Cmdlet unter dem Konto nicht vorhanden ist, ein Namenskonflikt mit einem Runbooknamen besteht oder das Cmdlet auch in einem anderen Modul vorhanden ist und Automation den Namen nicht auflösen kann.

#### <a name="resolution"></a>Lösung

Sie können dieses Problem mit jeder der folgenden Lösungen beheben:  

* Überprüfen Sie, ob Sie den Cmdlet-Namen richtig eingegeben haben.  
* Stellen Sie sicher, dass das Cmdlet unter Ihrem Automation-Konto vorhanden ist und dass keine Konflikte bestehen. Überprüfen Sie wie folgt, ob das Cmdlet vorhanden ist: Öffnen Sie ein Runbook im Bearbeitungsmodus, und suchen Sie in der Bibliothek nach dem gewünschten Cmdlet, oder führen Sie `Get-Command <CommandName>` aus. Nachdem Sie überprüft haben, dass das Cmdlet für das Konto verfügbar ist und dass keine Namenskonflikte mit anderen Cmdlets oder Runbooks bestehen, sollten Sie es der Canvas hinzufügen und sicherstellen, dass Sie in Ihrem Runbook einen gültigen Parametersatz verwenden.  
* Falls ein Namenskonflikt vorliegt und das Cmdlet in zwei unterschiedlichen Modulen verfügbar ist, können Sie das Problem beheben, indem Sie den vollqualifizierten Namen für das Cmdlet verwenden. Sie können beispielsweise **ModuleName\CmdletName** verwenden.  
* Wenn Sie das Runbook lokal in einer Hybrid Worker-Gruppe ausführen, stellen Sie sicher, dass das Modul und das Cmdlet auf dem Computer installiert sind, auf dem der Hybrid Worker gehostet wird.

### <a name="long-running-runbook"></a>Szenario: Ein Runbook mit langer Ausführungszeit kann nicht abgeschlossen werden

#### <a name="issue"></a>Problem

Ihr Runbook befindet sich nach 3 Stunden Laufzeit im Status **Angehalten**. Möglicherweise wird diese Fehlermeldung angezeigt:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Dies ist das in Azure-Sandboxes vorgesehene Verhalten aufgrund der Überwachung der Prozesse in Azure Automation auf gleichmäßige Verteilung. Wenn es länger als drei Stunden ausgeführt wird, wird das Runbook von der gleichmäßigen Verteilung automatisch beendet. Der Status eines Runbooks, der das Zeitlimit für die gleichmäßigen Verteilung überschreitet, ist je nach Runbooktyp unterschiedlich. PowerShell- und Python-Runbooks werden auf einen Status **Angehalten** festgelegt. PowerShell-Workflow-Runbooks werden auf **Fehlerhaft** festgelegt.

#### <a name="cause"></a>Ursache

Das Runbook hat den von der gleichmäßigen Verteilung in einer Azure-Sandbox vorgegebenen Grenzwert von 3 Stunden überschritten.

#### <a name="resolution"></a>Lösung

Die empfohlene Lösung ist das Ausführen des Runbooks in einem [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybrid Worker unterliegen nicht dem von der [gleichmäßigen Verteilung](../automation-runbook-execution.md#fair-share) in einer Azure-Sandbox vorgegebenen Grenzwert von 3 Stunden. Runbooks, die auf Hybrid Runbook Workers ausgeführt werden, sollten weiterentwickelt werden, um das Neustartverhalten bei unerwarteten lokalen Infrastrukturproblemen zu unterstützen.

Eine weitere Möglichkeit ist das Optimieren des Runbooks durch die Erstellung von [untergeordneten Runbooks](../automation-child-runbooks.md). Wenn Ihr Runbook für mehrere Ressourcen eine Schleife durch die gleiche Funktion durchführt, z.B. für einen Datenbankvorgang in mehreren Datenbanken, können Sie diese Funktion in ein untergeordnetes Runbook verschieben. Jedes dieser untergeordneten Runbooks wird parallel in separaten Prozessen ausgeführt. Dieses Verhalten führt dazu, dass die Gesamtdauer der Verarbeitung für das übergeordnete Runbook verringert wird.

Die PowerShell-Cmdlets für dieses Szenario, die das untergeordnete Runbook aktivieren, sind:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – Mit diesem Cmdlet können Sie ein Runbook starten und Parameter an das Runbook übergeben.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – Wenn Vorgänge nach Abschluss des untergeordneten Runbooks ausgeführt werden müssen, können Sie mit diesem Cmdlet den Auftragsstatus für jedes untergeordnete Element überprüfen.

### <a name="expired webhook"></a>Szenario: Status: 400 (ungültige Anforderung) beim Aufrufen eines Webhooks

#### <a name="issue"></a>Problem

Beim Versuch, einen Webhook für ein Azure Automation-Runbook aufzurufen, tritt der folgende Fehler auf:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Ursache

Der Webhook, den Sie aufrufen möchten, ist deaktiviert oder abgelaufen.

#### <a name="resolution"></a>Lösung

Falls der Webhook deaktiviert ist, können Sie ihn über das Azure-Portal wieder aktivieren. Wenn ein Webhook abgelaufen ist, muss er gelöscht und neu erstellt werden. Das [Verlängern eines Webhooks](../automation-webhooks.md#renew-webhook) ist nur möglich, solange er noch nicht abgelaufen ist.

### <a name="429"></a>Szenario: 429: The request rate is currently too large. Please try again (Die Anforderungsrate ist derzeit zu hoch. Versuchen Sie es noch mal.)

#### <a name="issue"></a>Problem

Beim Ausführen des Cmdlets `Get-AzureRmAutomationJobOutput` wird folgende Fehlermeldung angezeigt:

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Ursache

Dieser Fehler kann beim Abrufen der Auftragsausgabe eines Runbooks auftreten, das über zahlreiche [ausführliche Datenströme](../automation-runbook-output-and-messages.md#verbose-stream) verfügt.

#### <a name="resolution"></a>Lösung

Es gibt zwei Möglichkeiten, diesen Fehler zu beheben:

* Bearbeiten Sie das Runbook, und verringern Sie die Anzahl ausgegebener Auftragsdatenströme.
* Verringern Sie die Anzahl von Datenströmen, die beim Ausführen des Cmdlets abgerufen werden. Wenn Sie dieses Verhalten nutzen möchten, können Sie den Parameter `-Stream Output` für das Cmdlet `Get-AzureRmAutomationJobOutput` angeben, um ausschließlich Ausgabedatenströme abzurufen. 

### <a name="cannot-invoke-method"></a>Szenario: Bei einem PowerShell-Auftrag wird folgender Fehler ausgegeben: Cannot invoke method (Methode kann nicht aufgerufen werden)

#### <a name="issue"></a>Problem

Beim Starten eines PowerShell-Auftrags in einem in Azure ausgeführten Runbook wird die folgende Fehlermeldung angezeigt:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Ursache

Dieser Fehler kann auftreten, wenn Sie einen PowerShell-Auftrag in einem in Azure ausgeführten Runbook starten. Dieses Verhalten kann auftreten, da Runbooks, die in einer Azure-Sandbox ausgeführt werden, möglicherweise nicht im [vollständigen Sprachmodus](/powershell/module/microsoft.powershell.core/about/about_language_modes) ausgeführt werden.

#### <a name="resolution"></a>Lösung

Es gibt zwei Möglichkeiten, diesen Fehler zu beheben:

* Verwenden Sie `Start-AzureRmAutomationRunbook` anstelle von `Start-Job` zum Starten eines Runbooks.
* Wenn diese Fehlermeldung für Ihr Runbook angezeigt wird, führen Sie es in einem Hybrid Runbook Worker aus.

Weitere Informationen zu diesem Verhalten und anderen Verhaltensmerkmalen von Azure Automation-Runbooks finden Sie unter [Runbook-Verhalten](../automation-runbook-execution.md#runbook-behavior).

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Mit [@AzureSupport](https://twitter.com/azuresupport) verbinden – das offizielle Microsoft Azure-Konto zur Verbesserung der Benutzerfreundlichkeit durch Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus.
