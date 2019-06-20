---
title: Konfigurieren von Pre- und Post-Skripts für die Bereitstellung mit der Updateverwaltung in Azure
description: In diesem Artikel wird beschrieben, wie Sie Pre- und Post-Skripts für Updatebereitstellungen konfigurieren und verwalten.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7317b634ee4c8886ce5c99bb2b3395d7d1f646d5
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913864"
---
# <a name="manage-pre-and-post-scripts"></a>Verwalten von Pre- und Post-Skripts

Pre- und Post-Skripts (vor bzw. nach der Bereitstellung auszuführende Skripts) ermöglichen es Ihnen, vor (Pre-Aufgabe) und nach (Post-Aufgabe) einer Updatebereitstellung PowerShell-Runbooks in Ihrem Automation-Konto auszuführen. Pre- und Post-Skripts werden im Azure-Kontext und nicht lokal ausgeführt. Pre-Skripts werden zu Beginn der Updatebereitstellung ausgeführt. Post-Skripts werden am Ende der Bereitstellung sowie nach allen konfigurierten Neustarts ausgeführt.

## <a name="runbook-requirements"></a>Anforderungen für Runbooks

Damit ein Runbook als Pre- oder Post-Skript verwendet werden kann, muss es in Ihr Automation-Konto importiert und veröffentlicht werden. Weitere Informationen zu diesem Prozess finden Sie unter [Veröffentlichen eines Runbooks](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Verwenden eines Pre-/Post-Skripts

Um ein Pre- und/oder Post-Skript bei einer Updatebereitstellung zu verwenden, erstellen Sie zunächst eine Updatebereitstellung. Wählen Sie **Pre-Skripts und Post-Skripts** aus. Daraufhin wird die Seite **Vor und nach dem Vorgang auszuführende Skripts auswählen** angezeigt.  

![Auswählen der Skripts](./media/pre-post-scripts/select-scripts.png)

Wählen Sie das gewünschte Skript aus. In diesem Beispiel wurde das Runbook **UpdateManagement-TurnOnVms** verwendet. Wenn Sie das Runbook auswählen, wird die Seite **Skript konfigurieren** geöffnet. Wählen Sie **Skript vor Vorgang**. Klicken Sie nach Abschluss des Vorgangs auf **OK**.

Wiederholen Sie diese Schritte für das Skript **UpdateManagement-TurnOffVms**. Wählen Sie dieses Mal jedoch unter **Skripttyp** die Option **Skript nach Vorgang** aus.

Im Abschnitt **Ausgewählte Elemente** werden jetzt die beiden ausgewählten Skripts angezeigt, von denen eines ein Pre-Skript (vor dem Vorgang) und das andere ein Post-Skript (nach dem Vorgang) ist.

![Ausgewählte Elemente](./media/pre-post-scripts/selected-items.png)

Schließen Sie die Konfiguration der Updatebereitstellung ab.

Wenn die Updatebereitstellung abgeschlossen ist, können Sie unter **Updatebereitstellungen** die Ergebnisse anzeigen. Wie Sie sehen, wird der Status des Pre-Skripts und Post-Skripts angezeigt.

![Updateergebnisse](./media/pre-post-scripts/update-results.png)

Wenn Sie in die Updatebereitstellungsausführung klicken, werden weitere Details zu den Pre- und Post-Skripts angezeigt. Dort finden Sie auch einen Link zur Skriptquelle zum Zeitpunkt der Ausführung.

![Ergebnisse der Bereitstellungsausführung](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Übergeben von Parametern

Beim Konfigurieren von Pre- und Post-Skripts können Sie wie beim Planen eines Runbooks Parameter übergeben. Parameter werden beim Erstellen der Updatebereitstellung definiert. Pre- und Post-Skripts unterstützen die folgenden Typen:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Wenn Sie einen anderen Objekttyp benötigen, können Sie eine Typumwandlung in einen anderen Typ mit Ihrer eigenen Logik im Runbook durchführen.

Neben den Standardrunbookparametern ist ein zusätzlicher Parameter verfügbar. **SoftwareUpdateConfigurationRunContext**. Dieser Parameter ist eine JSON-Zeichenfolge, und wenn Sie ihn in Ihrem Pre- oder Post-Skript definieren, wird er automatisch von der Updatebereitstellung übergeben. Der Parameter enthält Informationen zur Updatebereitstellung, bei denen es sich um eine Teilmenge der von der [SoftwareUpdateconfigurations-API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) zurückgegebenen Informationen handelt. In der folgenden Tabelle sind die Eigenschaften aufgeführt, die in der Variablen bereitgestellt werden:

## <a name="stopping-a-deployment"></a>Beenden einer Bereitstellung

Wenn Sie eine Bereitstellung basierend auf einem vorbereitenden Skript stoppen möchten, müssen Sie eine Ausnahme [auslösen](automation-runbook-execution.md#throw). Wenn Sie keine Ausnahme auslösen, werden die Bereitstellung und das nachbereitende Skript weiterhin ausgeführt. Das [Beispiel-Runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) im Katalog zeigt, wie Sie dazu vorgehen können. Das folgende ist ein Codeausschnitt aus diesem Runbook.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.  
        throw $summary.Summary
    }
}
```

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext-Eigenschaften

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Der Name der Softwareupdatekonfiguration        |
|SoftwareUpdateConfigurationRunId     | Die eindeutige ID für die Ausführung        |
|SoftwareUpdateConfigurationSettings     | Eine Sammlung von Eigenschaften im Zusammenhang mit der Softwareupdatekonfiguration         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Die Betriebssysteme, für die die Updatebereitstellung ausgeführt werden soll         |
|SoftwareUpdateConfigurationSettings.duration     | Die maximale Dauer der Updatebereitstellungsausführung ausgedrückt als `PT[n]H[n]M[n]S` gemäß ISO 8601 (das so genannte „Wartungsfenster“)          |
|SoftwareUpdateConfigurationSettings.Windows     | Eine Sammlung von Eigenschaften im Zusammenhang mit Windows-Computern         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Eine Liste von KB-Nummern, die von der Bereitstellung ausgeschlossen werden        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Für die Updatebereitstellung ausgewählte Updateklassifizierungen        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Neustarteinstellungen für die Updatebereitstellung        |
|azureVirtualMachines     | Eine Liste der Ressourcen-IDs (resourceIds) für die Azure-VMs in der Updatebereitstellung        |
|nonAzureComputerNames|Eine Liste der FQDNs der Nicht-Azure-Computer in der Updatebereitstellung|

Das folgende Beispiel ist eine JSON-Zeichenfolge, die an den **SoftwareUpdateConfigurationRunContext**-Parameter übergeben wird:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Ein vollständiges Beispiel mit allen Eigenschaften finden Sie unter: [Softwareupdatekonfigurationen – Abrufen nach Namen](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> Das `SoftwareUpdateConfigurationRunContext`-Objekt kann doppelte Einträge für Computer enthalten. Dies kann dazu führen, dass Pre- und Post-Skripts mehrmals auf demselben Computer ausgeführt werden. Zur Umgehung dieses Verhaltens verwenden Sie `Sort-Object -Unique`, um nur eindeutige VM-Namen in Ihrem Skript auszuwählen.

## <a name="samples"></a>Beispiele

Beispiele für Pre- und Post-Skripts können aus dem [Script Center-Katalog](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) heruntergeladen oder über das Azure-Portal importiert werden. Um Skripts über das Portal zu importieren, klicken Sie in Ihrem Automation-Konto unter **Prozessautomatisierung** auf **Runbookkatalog**. Wählen Sie **Updateverwaltung** als Filter aus.

![Katalogliste](./media/pre-post-scripts/runbook-gallery.png)

Alternativ können Sie anhand der Namen in der folgenden Liste nach den Skripts suchen:

* Update Management – Turn On VMs (Updateverwaltung – VMs aktivieren)
* Update Management – Turn Off VMs (Updateverwaltung – VMs deaktivieren)
* Update Management – Run Script Locally (Updateverwaltung – Skript lokal ausführen)
* Update Management – Template for Pre/Post Scripts (Updateverwaltung – Vorlage für Pre-/Post-Skripts)
* Update Management – Run Script with Run Command (Updateverwaltung – Skript mit Ausführungsbefehl ausführen)

> [!IMPORTANT]
> Nachdem Sie die Runbooks importiert haben, müssen Sie sie **veröffentlichen**, bevor sie verwendet werden können. Suchen Sie dazu in Ihrem Automation-Konto nach dem gewünschten Runbook, klicken Sie auf **Bearbeiten** und dann auf **Veröffentlichen**.

Die Beispiele beruhen alle auf der einfachen Vorlage im folgenden Beispiel. Mit dieser Vorlage können Sie Ihr eigenes Runbook zur Verwendung mit Pre- und Post-Skripts erstellen. Die erforderliche Logik für die Authentifizierung bei Azure und die Behandlung des `SoftwareUpdateConfigurationRunContext`-Parameters ist in der Vorlage enthalten.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
  It requires a RunAs account. 
 
.PARAMETER SoftwareUpdateConfigurationRunContext 
  This is a system variable which is automatically passed in by Update Management during a deployment. 
#> 
 
param( 
    [string]$SoftwareUpdateConfigurationRunContext 
) 
#region BoilerplateAuthentication 
#This requires a RunAs account 
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection' 
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
#endregion BoilerplateAuthentication 
 
#If you wish to use the run context, it must be converted from JSON 
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext 
#Access the properties of the SoftwareUpdateConfigurationRunContext 
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId 
 
Write-Output $context 
 
#Example: How to create and write to a variable using the pre-script: 
<# 
#Create variable named after this run so it can be retrieved 
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-machines"></a>Interaktion mit Computern

Pre- und Post-Aufgaben werden als Runbook in Ihrem Automatisierungskonto ausgeführt und nicht direkt auf den Computern in Ihrer Bereitstellung. Pre- und Post-Aufgaben werden auch im Azure-Kontext ausgeführt und haben keinen Zugriff auf Nicht-Azure-Computer. Die folgenden Abschnitte zeigen, wie Sie direkt mit den Computern interagieren können, unabhängig davon, ob es sich um eine Azure-VM oder einen Nicht-Azure-Computer handelt:

### <a name="interacting-with-azure-machines"></a>Interaktion mit Azure-Computern

Pre- und Post-Aufgaben werden als Runbooks ausgeführt und laufen nicht nativ auf Ihren Azure-VMs in Ihrer Bereitstellung. Für die Interaktion mit den Azure-VMs benötigen Sie die folgenden Elemente:

* Ein ausführendes Konto
* Ein Runbook, das Sie ausführen möchten

Um mit Azure-Computern interagieren zu können, sollten Sie das Cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) für die Interaktion mit Ihren Azure-VMs verwenden. Ein Beispiel dafür finden Sie im Runbook-Beispiel [Update Management – Run Script with Run Command (Updateverwaltung – Skript mit Ausführungsbefehl ausführen)](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interaktion mit Nicht-Azure-Computern

Pre- und Post-Aufgaben werden im Azure-Kontext ausgeführt und haben keinen Zugriff auf Nicht-Azure-Computer. Für die Interaktion mit den Nicht-Azure-Computern benötigen Sie die folgenden Elemente:

* Ein ausführendes Konto
* Einen auf dem Computer installierten Hybrid Runbook Worker
* Ein Runbook, das Sie lokal ausführen möchten
* Das übergeordnete Runbook

Um mit Nicht-Azure-Computern zu interagieren, wird ein übergeordnetes Runbook im Azure-Kontext ausgeführt. Dieses Runbook ruft ein untergeordnetes Runbook mit dem Cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) auf. Sie müssen den `-RunOn`-Parameter und den Namen des Hybrid Runbook Workers, auf dem das Skript ausgeführt werden soll, angeben. Ein Beispiel dafür finden Sie im Runbook-Beispiel [Update Management – Run Script Locally (Updateverwaltung – Skript lokal ausführen)](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Abbrechen der Bereitstellung von Patches

Wenn Ihr vorbereitendes Skript einen Fehler zurückgibt, sollten Sie die Bereitstellung möglicherweise abbrechen. Dazu müssen Sie in Ihrem Skript für jede Logik, die einen Fehler darstellen würde, einen Fehler [auslösen](/powershell/module/microsoft.powershell.core/about/about_throw).

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Bekannte Probleme

* Sie können keine Objekte oder Arrays an Parameter übergeben, wenn Sie Pre- und Post-Skripts verwenden. Andernfalls tritt bei der Ausführung des Runbooks ein Fehler auf.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem Tutorial fort, um zu erfahren, wie Updates für Ihre virtuellen Windows-Computer verwaltet werden.

> [!div class="nextstepaction"]
> [Verwalten von Updates und Patches für Ihre virtuellen Azure Windows-Computer](automation-tutorial-update-management.md)

