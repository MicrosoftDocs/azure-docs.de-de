---
title: Behandeln von Problemen mit Azure Automation-Runbooks
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit Azure Automation-Runbooks beheben.
services: automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.service: automation
ms.custom: has-adal-ref
ms.openlocfilehash: c7ab093f601ebcd33d184b9a9008f9de447534a1
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368082"
---
# <a name="troubleshoot-runbook-issues"></a>Beheben von Runbookproblemen

 In diesem Artikel werden Probleme mit Runbooks beschrieben, die auftreten können, und wie diese zu beheben sind. Allgemeine Informationen finden Sie unter [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnose von Runbookproblemen

Wenn während der Ausführung von Runbooks in Azure Automation Fehler auftreten, können Sie die folgenden Schritte verwenden, um die Probleme zu diagnostizieren:

1. Stellen Sie sicher, dass Ihr Runbookskript auf Ihrem lokalen Computer erfolgreich ausgeführt wird.

    Sprachreferenz- und Lernmodule finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/overview) oder der [Python-Dokumentation](https://docs.python.org/3/). Wenn Sie Ihr Skript lokal ausführen, können Sie häufige Fehler erkennen und beheben, z. B.:

      * Fehlende Module
      * Syntaxfehler
      * Logikfehler

1. Untersuchen Sie Runbook-[Fehlerstreams](../automation-runbook-output-and-messages.md#runbook-output).

    Untersuchen Sie diese Streams auf bestimmte Meldungen, und vergleichen Sie sie mit den in diesem Artikel erläuterten Fehlern.

1. Stellen Sie sicher, dass Ihre Knoten und der Automation-Arbeitsbereich über die erforderlichen Module verfügen.

    Wenn in Ihr Runbook Module importiert werden, überprüfen Sie, ob diese für Ihr Automation-Konto verfügbar sind, indem Sie die unter [Importieren von Modulen](../shared-resources/modules.md#import-modules) aufgeführten Schritte befolgen. Aktualisieren Sie Ihre PowerShell-Module auf die neueste Version, indem Sie die Anleitungen unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](../automation-update-azure-modules.md) befolgen. Weitere Problembehandlungsinformationen finden Sie unter [Module](shared-resources.md#modules).

1. Wenn das Runbook angehalten wurde oder ein unerwarteter Fehler aufgetreten ist:

    * [Erneuern Sie das Zertifikat](../manage-runas-account.md#cert-renewal), wenn das ausführende Konto abgelaufen ist.
    * [Erneuern Sie den Webhook](../automation-webhooks.md#renew-a-webhook), wenn Sie versuchen, einen abgelaufenen Webhook zum Starten des Runbooks zu verwenden.
    * [Überprüfen Sie die Auftragsstatuswerte](../automation-runbook-execution.md#job-statuses), um den aktuellen Runbookstatus und einige mögliche Ursachen des Problems zu ermitteln.
    * [Fügen Sie dem Runbook eine zusätzliche Ausgabe hinzu](../automation-runbook-output-and-messages.md#working-with-message-streams), um zu ermitteln, was passiert, bevor das Runbook angehalten wird.
    * [Behandeln Sie alle Ausnahmen](../automation-runbook-execution.md#exceptions), die von Ihrem Auftrag ausgelöst werden.

1. Führen Sie diesen Schritt aus, wenn der Runbookauftrag oder die Umgebung auf dem Hybrid Runbook Worker nicht reagiert.

    Wenn Sie Ihre Runbooks nicht in Azure Automation, sondern auf einem Hybrid Runbook Worker ausführen, ist möglicherweise eine [Problembehandlung für den Hybrid Worker selbst](hybrid-runbook-worker.md) erforderlich.

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Szenario: Fehler beim Runbook. Fehler „Keine Berechtigung“ oder „Unzulässig 403“ wird angezeigt.

### <a name="issue"></a>Problem

Bei Ihrem Runbook tritt der Fehler „Keine Berechtigung“ oder „Unzulässig 403“ bzw. ein ähnlicher Fehler auf.

### <a name="cause"></a>Ursache

Ausführende Konten verfügen unter Umständen nicht über die gleichen Berechtigungen für Azure-Ressourcen wie Ihr aktuelles Automation-Konto. 

### <a name="resolution"></a>Lösung

Vergewissern Sie sich, dass Ihr ausführendes Konto über [Berechtigungen für den Zugriff auf alle Ressourcen](../../role-based-access-control/role-assignments-portal.md) verfügt, die in Ihrem Skript verwendet werden.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Szenario: Fehler beim Anmelden beim Azure-Konto

### <a name="issue"></a>Problem

Beim Arbeiten mit dem Cmdlet `Connect-AzAccount` wird eine der folgenden Fehlermeldungen angezeigt:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Ursache

Diese Fehler treten auf, wenn der Name des Anmeldeinformationsobjekts ungültig ist. Sie treten möglicherweise auch auf, wenn Benutzername und Kennwort, die Sie zur Einrichtung des Automation-Anmeldeinformationsobjekts verwendet haben, nicht gültig sind.

### <a name="resolution"></a>Lösung

Führen Sie die folgenden Schritte aus, um den Fehler zu ermitteln:

1. Stellen Sie sicher, dass Sie keine Sonderzeichen eingegeben haben. Dazu zählt auch das `\@`-Zeichen im Namen des Automation-Anmeldeinformationsobjekts, mit dem Sie die Verbindung zu Azure herstellen.
1. Überprüfen Sie, ob Sie den Benutzernamen und das Kennwort aus den Azure Automation-Anmeldeinformationen in Ihrem lokalen PowerShell ISE-Editor verwenden können. Führen Sie die folgenden Cmdlets in der PowerShell ISE aus.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Wenn die Authentifizierung lokal fehlschlägt, haben Sie Ihre Azure Active Directory (Azure AD)-Anmeldeinformationen nicht ordnungsgemäß eingerichtet. Informationen zur richtigen Einrichtung des Azure AD-Kontos finden Sie im Artikel [Authentifizieren bei Azure mithilfe von Azure AD](../automation-use-azure-ad.md).

1. Wenn es sich um einen vorübergehenden Fehler zu handeln scheint, versuchen Sie, eine Wiederholungslogik zu Ihrer Authentifizierungsroutine hinzuzufügen, um die Authentifizierung zuverlässiger zu gestalten.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Szenario: Ausführen von Login-AzureRMAccount zum Anmelden

### <a name="issue"></a>Problem

Beim Ausführen eines Runbooks wird die folgende Fehlermeldung angezeigt:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Ursache

Dieser Fehler kann auftreten, wenn Sie kein ausführendes Konto verwenden oder wenn das ausführende Konto abgelaufen ist. Weitere Informationen finden Sie unter [Verwalten von ausführenden Azure Automation-Konten](../manage-runas-account.md).

Dieser Fehler hat zwei Hauptursachen:

* Es sind verschiedene Versionen des AzureRM- oder Az-Moduls vorhanden.
* Sie versuchen, auf Ressourcen in einem separaten Abonnement zuzugreifen.

### <a name="resolution"></a>Lösung

Wenn diese Fehlermeldung nach dem Aktualisieren eines AzureRM- oder Az-Moduls angezeigt wird, aktualisieren Sie alle Ihre Module auf dieselbe Version.

Wenn Sie versuchen, auf Ressourcen in einem anderen Abonnement zuzugreifen, führen Sie die folgenden Schritte aus, um Berechtigungen zu konfigurieren:

1. Wechseln Sie zum ausführenden Automation-Konto, und kopieren Sie die **Anwendungs-ID** und den **Fingerabdruck**.

    ![Kopieren der Anwendungs-ID und des Fingerabdrucks](../media/troubleshoot-runbooks/collect-app-id.png)

1. Wechseln Sie zur **Zugriffssteuerung** des Abonnements, in dem das Automation-Konto *nicht* gehostet wird, und fügen Sie eine neue Rollenzuweisung hinzu.

    ![Zugriffssteuerung](../media/troubleshoot-runbooks/access-control.png)

1. Fügen Sie die zuvor kopierte **Anwendungs-ID** hinzu. Wählen Sie die Berechtigungen für **Mitwirkender** aus.

    ![Rollenzuweisung hinzufügen](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopieren Sie den Namen des Abonnements.

1. Sie können nun den folgenden Runbookcode verwenden, um die Berechtigungen Ihres Automation-Kontos für das andere Abonnement zu testen. Ersetzen Sie `"\<CertificateThumbprint\>"` durch den Wert, den Sie in Schritt 1 kopiert haben. Ersetzen Sie `"\<SubscriptionName\>"` durch den Wert, den Sie in Schritt 4 kopiert haben.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Szenario: Azure-Abonnement nicht gefunden

### <a name="issue"></a>Problem

Beim Arbeiten mit dem Cmdlet `Select-AzureSubscription`, `Select-AzureRMSubscription` oder `Select-AzSubscription` wird folgende Fehlermeldung angezeigt:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fehler

Dieser Fehler kann in den folgenden Fällen auftreten:

* Der Abonnementname ist ungültig.
* Der Azure AD-Benutzer, der die Abonnementdetails abrufen möchte, ist nicht als Administrator des Abonnements konfiguriert.
* Das Cmdlet ist nicht verfügbar.

### <a name="resolution"></a>Lösung

Führen Sie die folgenden Schritte aus, um zu ermitteln, ob die Authentifizierung in Azure erfolgt ist und Sie auf das gewünschte Abonnement zugreifen können:

1. Testen Sie Ihr Skript außerhalb von Azure Automation, um sicherzustellen, dass es eigenständig verwendet werden kann.
1. Stellen Sie sicher, dass Ihr Skript das Cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) vor dem Cmdlet `Select-*` ausführt.
1. Fügen Sie am Anfang Ihres Runbooks `Disable-AzContextAutosave –Scope Process` hinzu. Dieses Cmdlet stellt sicher, dass alle Anmeldeinformationen nur für die Ausführung des aktuellen Runbooks gelten.
1. Wenn die Fehlermeldung weiterhin angezeigt wird, ändern Sie Ihren Code, indem Sie den Parameter `AzContext` für `Connect-AzAccount` hinzufügen und dann den Code ausführen.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Szenario: Fehler bei Runbooks, wenn mehrere Abonnements verwendet werden

### <a name="issue"></a>Problem

Beim Ausführen von Runbooks mit kann das Runbook Azure-Ressourcen nicht verwalten.

### <a name="cause"></a>Ursache

Das Runbook verwendet beim Ausführen nicht den richtigen Kontext.

### <a name="resolution"></a>Lösung

Der Abonnementkontext geht möglicherweise verloren, wenn ein Runbook mehrere Runbooks aufruft. Stellen Sie sicher, dass der Abonnementkontext an die Runbooks übergeben wird, indem Sie dafür sorgen, dass das Clientrunbook den Kontext im Parameter `AzureRmContext` an das Cmdlet `Start-AzureRmAutomationRunbook` übergibt. Verwenden Sie das Cmdlet `Disable-AzureRmContextAutosave` mit dem Wert `Process` für den Parameter `Scope`, um sicherzustellen, dass die angegebenen Anmeldeinformationen nur für das aktuelle Runbook verwendet werden. Weitere Informationen finden Sie unter [Abonnements](../automation-runbook-execution.md#subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Szenario: Fehler beim Authentifizieren bei Azure, da die mehrstufige Authentifizierung aktiviert ist

### <a name="issue"></a>Problem

Sie erhalten bei der Authentifizierung bei Azure mit Ihrem Azure-Benutzernamen und -Kennwort die folgende Fehlermeldung:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Ursache

Falls für Ihr Azure-Konto die mehrstufige Authentifizierung eingerichtet ist, können Sie für die Authentifizierung bei Azure keinen Azure Active Directory-Benutzer verwenden. Stattdessen müssen Sie ein Zertifikat oder einen Dienstprinzipal für die Authentifizierung verwenden.

### <a name="resolution"></a>Lösung

Informationen zur Verwendung eines klassischen ausführenden Kontos mit Cmdlets für das klassische Azure-Bereitstellungsmodell finden Sie unter [Erstellen eines klassischen ausführenden Kontos](../automation-create-standalone-account.md#create-a-classic-run-as-account). Informationen zum Verwenden eines Dienstprinzipals mit Azure Resource Manager-Cmdlets finden Sie unter [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../../active-directory/develop/howto-create-service-principal-portal.md) und [Gewusst wie: Verwenden von Azure PowerShell zum Erstellen eines Dienstprinzipals mit einem Zertifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Szenario: Runbookfehler mit der Meldung „Eine Aufgabe wurde abgebrochen“

### <a name="issue"></a>Problem

Für Ihr Runbook tritt ein Fehler auf, der dem im folgenden Beispiel ähnelt:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Ursache

Dieser Fehler kann durch veraltete Azure-Module verursacht werden.

### <a name="resolution"></a>Lösung

Sie können diesen Fehler beheben, indem Sie Ihre Azure-Module auf die neueste Version aktualisieren:

1. Wählen Sie in Ihrem Automation-Konto **Module** und anschließend **Azure-Module aktualisieren** aus.
1. Das Update dauert ungefähr 15 Minuten. Führen Sie anschließend das Runbook erneut aus, bei dem der Fehler aufgetreten ist.

Weitere Informationen zum Aktualisieren Ihrer Module finden Sie unter [Aktualisieren von Azure-Modulen in Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Szenario: Eine Benennung wird nicht als Name eines Cmdlets, einer Funktion oder eines Skripts erkannt.

### <a name="issue"></a>Problem

Für Ihr Runbook tritt ein Fehler auf, der dem im folgenden Beispiel ähnelt:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Ursache

Dieser Fehler kann aus einem der folgenden Gründe auftreten:

* Das Modul, das das Cmdlet enthält, wird nicht in das Automation-Konto importiert.
* Das Modul, das das Cmdlet enthält, wird zwar importiert, ist jedoch veraltet.

### <a name="resolution"></a>Lösung

Führen Sie eine der folgenden Aktionen aus, um diesen Fehler zu beheben:

* Wenn es sich bei dem Modul um ein Azure-Modul handelt, finden Sie weitere Informationen zum Aktualisieren Ihrer Module in Ihrem Automation-Konto unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](../automation-update-azure-modules.md).
* Wenn es sich nicht um ein Azure-Modul handelt, stellen Sie sicher, dass das Modul in Ihr Automation-Konto importiert wurde.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Szenario: Cmdlet-Fehler in PnP-PowerShell-Runbook auf Azure Automation

### <a name="issue"></a>Problem

Wenn ein Runbook ein von PnP-PowerShell generiertes Objekt direkt in die Azure Automation-Ausgabe schreibt, kann die Cmdlet-Ausgabe nicht in Automation zurück gestreamt werden.

### <a name="cause"></a>Ursache

Dieses Problem tritt am häufigsten auf, wenn Azure Automation Runbooks verarbeitet, die PnP-PowerShell-Cmdlets aufrufen, z. B. `add-pnplistitem`, ohne dass die Rückgabeobjekte abgefangen werden.

### <a name="resolution"></a>Lösung

Bearbeiten Sie die Skripts, um den Variablen beliebige Rückgabewerte zuzuweisen, sodass die Cmdlets nicht versuchen, ganze Objekte in die Standardausgabe zu schreiben. Ein Skript kann den Ausgabestream wie hier dargestellt an ein Cmdlet umleiten.

```azurecli
  $null = add-pnplistitem
```

Wenn das Skript die Cmdletausgabe analysiert, muss das Skript die Ausgabe in einer Variablen speichern und die Variable bearbeiten, anstatt einfach die Ausgabe zu streamen.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Szenario: Cmdlet wird beim Ausführen eines Runbooks nicht erkannt

### <a name="issue"></a>Problem

Ihr Runbookauftrag schlägt mit dem folgenden Fehler fehl:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Ursache

Dieser Fehler wird verursacht, wenn die PowerShell-Engine das Cmdlet nicht findet, das Sie in Ihrem Runbook verwenden. Dies kann der Fall sein, weil das Modul mit dem Cmdlet in dem entsprechenden Konto nicht vorhanden ist, ein Namenskonflikt mit einem Runbooknamen besteht oder das Cmdlet auch in einem anderen Modul vorhanden ist und Automation den Namen nicht auflösen kann.

### <a name="resolution"></a>Lösung

Beheben Sie dieses Problem mit einer der folgenden Lösungen:

* Überprüfen Sie, ob Sie den Cmdlet-Namen richtig eingegeben haben.
* Stellen Sie sicher, dass das Cmdlet in Ihrem Automation-Konto vorhanden ist und dass keine Konflikte bestehen. Überprüfen Sie wie folgt, ob das Cmdlet vorhanden ist: Öffnen Sie ein Runbook im Bearbeitungsmodus, und suchen Sie in der Bibliothek nach dem gewünschten Cmdlet, oder führen Sie `Get-Command <CommandName>` aus. Wenn Sie festgestellt haben, dass das Cmdlet für das Konto verfügbar ist und keine Namenskonflikte mit anderen Cmdlets oder Runbooks bestehen, fügen Sie es der Canvas hinzu. Stellen Sie sicher, dass Sie in Ihrem Runbook einen gültigen Parametersatz verwenden.
* Falls ein Namenskonflikt vorliegt und das Cmdlet in zwei unterschiedlichen Modulen verfügbar ist, beheben Sie das Problem, indem Sie den vollqualifizierten Namen für das Cmdlet verwenden. Sie können z. B. `ModuleName\CmdletName` verwenden.
* Wenn Sie das Runbook lokal in einer Hybrid Worker-Gruppe ausführen, stellen Sie sicher, dass das Modul und das Cmdlet auf dem Computer installiert sind, auf dem der Hybrid Worker gehostet wird.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Szenario: Falscher Objektverweis beim Aufrufen von „Add-AzAccount“

### <a name="issue"></a>Problem

Dieser Fehler wird angezeigt, wenn Sie mit `Add-AzAccount` arbeiten, das einen Alias für das Cmdlet `Connect-AzAccount` darstellt:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Ursache

Dieser Fehler kann auftreten, wenn das Runbook vor dem Aufruf von `Add-AzAccount` zum Hinzufügen des Automation-Kontos nicht die richtigen Schritte durchführt. Ein Beispiel für einen der erforderlichen Schritte ist die Anmeldung mit einem ausführenden Konto. Die richtigen Vorgänge zur Verwendung in Ihrem Runbook finden Sie unter [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Szenario: Objektverweis wurde nicht auf eine Objektinstanz festgelegt

### <a name="issue"></a>Problem

Die folgende Fehlermeldung wird angezeigt, wenn Sie ein untergeordnetes Runbook mit dem Parameter `Wait` aufrufen und der Ausgabestream ein Objekt enthält:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Ursache

Wenn der Stream Objekte enthält, wird der Ausgabestream von `Start-AzAutomationRunbook` nicht ordnungsgemäß verarbeitet.

### <a name="resolution"></a>Lösung

Implementieren Sie eine Abruflogik, und verwenden Sie das Cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput), um die Ausgabe abzurufen. Der folgende Code ist ein Beispiel für eine solche Logik:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Szenario: Runbookfehler aufgrund eines deserialisierten Objekts

### <a name="issue"></a>Problem

Ihr Runbook schlägt mit dem folgenden Fehler fehl:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Ursache

Wenn es sich bei Ihrem Runbook um einen PowerShell-Workflow handelt, werden komplexe Objekte in einem deserialisierten Format gespeichert, um den Runbookstatus beizubehalten, sobald der Workflow angehalten wird.

### <a name="resolution"></a>Lösung

Beheben Sie dieses Problem mit einer der folgenden Lösungen:

* Wenn Sie komplexe Objekte von einem Cmdlet an ein anderes übergeben, umschließen Sie diese Cmdlets mit einer `InlineScript`-Aktivität.
* Übergeben Sie den Namen oder Wert, den Sie aus dem komplexen Objekt benötigen, anstatt das gesamte Objekt zu übergeben.
* Verwenden Sie anstelle eines PowerShell-Workflow-Runbooks ein PowerShell-Runbook.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Szenario: Status 400 (ungültige Anforderung) beim Aufrufen eines Webhooks

### <a name="issue"></a>Problem

Beim Versuch, einen Webhook für ein Azure Automation-Runbook aufzurufen, tritt der folgende Fehler auf:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Ursache

Der Webhook, den Sie aufrufen möchten, ist deaktiviert oder abgelaufen. 

### <a name="resolution"></a>Lösung

Falls der Webhook deaktiviert ist, können Sie ihn über das Azure-Portal wieder aktivieren. Wenn der Webhook abgelaufen ist, müssen Sie ihn löschen und dann erneut erstellen. Das [Verlängern eines Webhooks](../automation-webhooks.md#renew-a-webhook) ist nur möglich, solange er noch nicht abgelaufen ist. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Szenario: 429: The request rate is currently too large. (Die Anforderungsrate ist zurzeit zu hoch.)

### <a name="issue"></a>Problem

Beim Ausführen des Cmdlets `Get-AzAutomationJobOutput` wird folgende Fehlermeldung angezeigt:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Ursache

Dieser Fehler kann beim Abrufen der Auftragsausgabe von einem Runbook auftreten, das über zahlreiche [ausführliche Streams](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream) verfügt.

### <a name="resolution"></a>Lösung

Führen Sie eine der folgenden Aktionen aus, um diesen Fehler zu beheben:

* Bearbeiten Sie das Runbook, und verringern Sie die Anzahl ausgegebener Auftragsdatenströme.
* Verringern Sie die Anzahl von Datenströmen, die beim Ausführen des Cmdlets abgerufen werden. Hierzu können Sie den Wert des Parameters `Stream` für das Cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) so festlegen, dass ausschließlich Ausgabedatenströme abgerufen werden. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Szenario: Fehler beim Runbookauftrag, da das zugeordnete Kontingent überschritten wurde

### <a name="issue"></a>Problem

Ihr Runbookauftrag schlägt mit dem folgenden Fehler fehl:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, wenn die Auftragsausführung das kostenlose Kontingent von 500 Minuten für Ihr Konto überschreitet. Dieses Kontingent gilt für alle Arten von Auftragsausführungsaufgaben. Zu diesen Aufgaben zählen unter anderem: Testen eines Auftrags, Starten eines Auftrags im Portal, Ausführen eines Auftrags per Webhook und Planen der Ausführung eines Auftrags über das Azure-Portal oder in Ihrem Rechenzentrum. Weitere Informationen zu den Preisen für Automation finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Lösung

Wenn Sie mehr als 500 Minuten Verarbeitungszeit pro Monat nutzen möchten, müssen Sie Ihr Abonnement vom Free-Tarif auf den Basic-Tarif umstellen:

1. Melden Sie sich bei Ihrem Azure-Abonnement an.
1. Wählen Sie das Automation-Konto aus, das Sie aktualisieren möchten.
1. Wählen Sie **Einstellungen** und dann **Preise** aus.
1. Wählen Sie unten auf der Seite **Aktivieren** aus, um für Ihr Konto ein Upgrade auf den Basic-Tarif durchzuführen.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Szenario: Runbookausgabestream größer als 1 MB

### <a name="issue"></a>Problem

Bei Ihrem in der Azure-Sandbox ausgeführten Runbook tritt der folgende Fehler auf:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt auf, weil vom Runbook zu viele Ausnahmedaten in den Ausgabestream geschrieben wurden.

### <a name="resolution"></a>Lösung

Für den Auftragsausgabestream gilt ein Grenzwert von 1 MB. Stellen Sie sicher, dass Ihr Runbook Aufrufe einer ausführbaren Datei oder eines Teilprozesses in `try`- und `catch`-Blöcke einschließt. Wenn diese Vorgänge eine Ausnahme auslösen, lassen Sie den Code die Nachricht dieser Ausnahme in eine Automation-Variable schreiben. Mit dieser Technik wird verhindert, dass die Nachricht in den Auftragsausgabestream geschrieben wird. Für ausgeführte Hybrid Runbook Worker-Aufträge wird der auf 1 MB abgeschnittene Ausgabestream ohne Fehlermeldung angezeigt.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Szenario: Es wurde dreimal erfolglos versucht, den Runbookauftrag zu starten

### <a name="issue"></a>Problem

Bei Ihrem Runbook tritt ein Fehler mit der folgenden Fehlermeldung auf:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Ursache

Dieser Fehler tritt aufgrund eines der folgenden Probleme auf:

* **Arbeitsspeicherlimit**. Für einen Auftrag kann ein Fehler auftreten, wenn dafür mehr als 400 MB Arbeitsspeicher verwendet werden. Die dokumentierten Grenzwerte, die für das Zuordnen von Arbeitsspeicher zu einer Sandbox gelten, finden Sie unter [Grenzwerte für den Automation-Dienst](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* **Netzwerksockets**. Azure-Sandboxes sind auf 1.000 gleichzeitige Netzwerksockets beschränkt. Weitere Informationen finden Sie unter [Grenzwerte für den Automation-Dienst](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **Modul inkompatibel**. Die Modulabhängigkeiten sind möglicherweise nicht korrekt. In diesem Fall gibt Ihr Runbook üblicherweise die Fehlermeldung `Command not found` oder `Cannot bind parameter` aus.

* **Die Sandbox wurde nicht mit Active Directory authentifiziert**. Ihr Runbook versucht, eine ausführbare Datei oder einen Teilprozess aufzurufen, die bzw. der in einer Azure-Sandbox ausgeführt wird. Das Konfigurieren von Runbooks für die Authentifizierung mit Azure AD mithilfe der Azure Active Directory-Authentifizierungsbibliothek (ADAL) wird nicht unterstützt.

### <a name="resolution"></a>Lösung

* **Arbeitsspeicherlimit, Netzwerksockets**. Die empfohlenen Vorgehensweisen für die Einhaltung der Arbeitsspeichergrenzwerte bestehen darin, die Workload auf mehrere Runbooks zu verteilen, weniger Daten im Arbeitsspeicher zu verarbeiten, das Schreiben von unnötigen Ausgaben aus den Runbooks zu vermeiden und zu überprüfen, wie viele Prüfpunkte Sie in Ihre PowerShell-Workflowrunbooks schreiben. Verwenden Sie die clear-Methode (z.B. `$myVar.clear`), um Variablen zu löschen, und verwenden Sie dann `[GC]::Collect`, um sofort die Garbage Collection durchzuführen. Durch diese Aktionen wird der Speicherbedarf Ihres Runbooks während der Laufzeit reduziert.

* **Modul inkompatibel**. Aktualisieren Sie Ihre Azure-Module, indem Sie die Schritte unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation](../automation-update-azure-modules.md) befolgen.

* **Die Sandbox wurde nicht mit Active Directory authentifiziert**. Stellen Sie beim Authentifizieren bei Azure AD mit einem Runbook sicher, dass das Azure AD-Modul in Ihrem Automation-Konto verfügbar ist. Vergewissern Sie sich, dass Sie dem ausführenden Konto die erforderlichen Berechtigungen zum Ausführen der vom Runbook automatisierten Aufgaben erteilen.

  Verwenden Sie das Runbook auf einem [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md), wenn Ihr Runbook eine ausführbare Datei oder einen Teilprozess nicht aufrufen kann, die bzw. der in einer Azure Sandbox ausgeführt wird. Hybrid Worker unterliegen nicht den vom Arbeitsspeicher und Netzwerk vorgegebenen Grenzwerten, die für Azure-Sandboxes gelten.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Szenario: PowerShell-Auftragsfehler mit der Meldung, dass die Methode nicht aufgerufen werden kann

### <a name="issue"></a>Problem

Beim Starten eines PowerShell-Auftrags in einem in Azure ausgeführten Runbook wird die folgende Fehlermeldung angezeigt:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Ursache

Dieser Fehler weist möglicherweise darauf hin, dass Runbooks, die in einer Azure-Sandbox ausgeführt werden, nicht im [Modus „FullLanguage“](/powershell/module/microsoft.powershell.core/about/about_language_modes) ausgeführt werden können.

### <a name="resolution"></a>Lösung

Es gibt zwei Möglichkeiten, diesen Fehler zu beheben:

* Anstelle von [Start-Job](/powershell/module/microsoft.powershell.core/start-job) verwenden Sie [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook), um das Runbook zu starten.
* Führen Sie das Runbook auf einem Hybrid Runbook Worker aus.

Weitere Informationen zu diesem Verhalten und anderen von Azure Automation-Runbooks finden Sie unter [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Szenario: Ein Runbook mit langer Ausführungszeit kann nicht abgeschlossen werden

### <a name="issue"></a>Problem

Ihr Runbook befindet sich nach drei Stunden Laufzeit im Zustand „Angehalten“. Möglicherweise wird auch diese Fehlermeldung angezeigt:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Dies ist das in Azure-Sandboxes vorgesehene Verhalten aufgrund der Überwachung der Prozesse in Azure Automation auf [gleichmäßige Verteilung](../automation-runbook-execution.md#fair-share). Wenn ein Prozess länger als drei Stunden ausgeführt wird, wird das Runbook von der gleichmäßigen Verteilung automatisch beendet. Der Status eines Runbooks, der das Zeitlimit für die gleichmäßigen Verteilung überschreitet, ist je nach Runbooktyp unterschiedlich. PowerShell- und Python-Runbooks werden auf den Zustand „Angehalten“ festgelegt. PowerShell-Workflow-Runbooks werden auf „Fehlerhaft“ festgelegt.

### <a name="cause"></a>Ursache

Das Runbook hat den von der gleichmäßigen Verteilung in einer Azure-Sandbox vorgegebenen Grenzwert von drei Stunden überschritten.

### <a name="resolution"></a>Lösung

Die empfohlene Lösung ist das Ausführen des Runbooks in einem [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Worker unterliegen nicht dem von der gleichmäßigen Verteilung in einer Azure-Sandbox vorgegebenen Grenzwert von drei Stunden. Runbooks, die auf Hybrid Runbook Workern ausgeführt werden, sollten weiterentwickelt werden, um das Neustartverhalten bei unerwarteten lokalen Infrastrukturproblemen zu unterstützen.

Eine weitere Lösung ist das Optimieren des Runbooks durch die Erstellung von [untergeordneten Runbooks](../automation-child-runbooks.md). Wenn Ihr Runbook für mehrere Ressourcen eine Schleife durch die gleiche Funktion durchführt, z. B. für einen Datenbankvorgang in mehreren Datenbanken, können Sie diese Funktion in ein untergeordnetes Runbook verschieben. Jedes dieser untergeordneten Runbooks wird parallel in separaten Prozessen ausgeführt. Dieses Verhalten führt dazu, dass die Gesamtdauer der Verarbeitung für das übergeordnete Runbook verringert wird.

Die PowerShell-Cmdlets für dieses Szenario, die das untergeordnete Runbook aktivieren, sind:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Mit diesem Cmdlet können Sie ein Runbook starten und Parameter an das Runbook übergeben.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Mit diesem Cmdlet können Sie den Auftragsstatus für jedes untergeordnete Element überprüfen, wenn Vorgänge vorhanden sind, die nach Abschluss des untergeordneten Runbooks durchgeführt werden müssen.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Szenario: Fehler zur get_SerializationSettings-Methode wird in Auftragsdatenströmen angezeigt

### <a name="issue"></a>Problem

In Ihren Auftragsdatenströmen für ein Runbook wird der folgende Fehler angezeigt:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Ursache

Dieser Fehler wird wahrscheinlich durch die Verwendung einer unvollständigen Migration von AzureRM- zu Az-Modulen in Ihrem Runbook verursacht. Diese Situation kann dazu führen, dass Azure Automation einen Runbookauftrag nur mit AzureRM-Modulen startet und dann einen anderen Auftrag nur mit Az-Modulen startet, was zu einem Sandboxabsturz führt.

### <a name="resolution"></a>Lösung

Die Verwendung von Az- und AzureRM-Cmdlets in demselben Runbook wird nicht empfohlen. Weitere Informationen zur richtigen Verwendung dieser Module finden Sie unter [Migrieren zu Az-Modulen](../shared-resources/modules.md#migrate-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Szenario: Zugriff wird verweigert beim Verwenden einer Azure-Sandbox für ein Runbook oder eine Anwendung

### <a name="issue"></a>Problem

Wenn Ihr Runbook oder Ihre Anwendung versucht, in einer Azure-Sandbox ausgeführt zu werden, verweigert die Umgebung den Zugriff.

### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, da eine Azure-Sandbox den Zugriff auf alle Out-of-Process-COM-Server verhindert. Sandkastenanwendungen oder -runbooks können beispielsweise die Windows-Verwaltungsinstrumentation (Windows Management Instrumentation, WMI) oder den Windows Installer-Dienst (msiserver.exe) nicht aufrufen.

### <a name="resolution"></a>Lösung

Weitere Informationen zur Verwendung der Azure-Sandboxes finden Sie unter [Runbook-Ausführungsumgebung](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Szenario: Ungültiger Statuscode „Nicht zulässig“ bei Verwendung von Key Vault in einem Runbook

### <a name="issue"></a>Problem

Bei dem Versuch, über ein Azure Automation-Runbook auf Azure Key Vault zuzugreifen, tritt der folgende Fehler auf:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Ursache

Mögliche Ursachen für dieses Problem:

* Fehlende Verwendung eines ausführenden Kontos.
* Unzureichende Berechtigungen.

### <a name="resolution"></a>Lösung

#### <a name="not-using-a-run-as-account"></a>Fehlende Verwendung eines ausführenden Kontos

Führen Sie [Schritt 5: Hinzufügen von Authentifizierungsfunktionen für die Verwaltung von Azure-Ressourcen](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) aus, um sicherzustellen, dass Sie ein ausführendes Konto für den Zugriff auf Key Vault verwenden.

#### <a name="insufficient-permissions"></a>Unzureichende Berechtigungen

[Fügen Sie Berechtigungen zu Key Vault hinzu](../manage-runas-account.md#add-permissions-to-key-vault), um sicherzustellen, dass Ihr ausführendes Konto über ausreichende Berechtigungen für den Zugriff auf Key Vault verfügt.

## <a name="recommended-documents"></a>Empfohlene Dokumente

* [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md)
* [Starten eines Runbooks in Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, nutzen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet Sie mit der Azure-Community, die Antworten, Unterstützung und Expertenrat bietet.
* Wenn Sie weitere Hilfe benötigen, können Sie einen Azure-Supportvorgang anlegen. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
