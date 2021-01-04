---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8a442b5de7a256dd8bcf47da741bd41894709c3a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561253"
---
Für die Diagnose von Problemen mit einem Microsoft Azure-Clouddienst müssen die Protokolldateien des Diensts auf virtuellen Computern gesammelt werden, wenn die Probleme auftreten. Sie können die AzureLogCollector-Erweiterung bedarfsgesteuert verwenden, um eine einmalige Sammlung von Protokollen von einer oder mehreren Clouddienst-VMs (von Web- und Workerrollen aus) durchzuführen und die gesammelten Dateien an ein Azure-Speicherkonto zu übertragen – alles ohne Remoteanmeldung bei den virtuellen Computern.

> [!NOTE]
> Beschreibungen für die meisten der protokollierten Informationen finden Sie unter https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/.
> 
> 

Es gibt zwei Modi für die Sammlung, die von den Typen der zu sammelnden Dateien abhängig sind.

* **Nur Protokolle von Azure-Gast-Agents (GA):** Dieser Sammlungsmodus umfasst alle Protokolle im Zusammenhang mit Azure-Gast-Agents und anderen Azure-Komponenten.
* **Alle Protokolle (vollständig):** Dieser Sammlungsmodus sammelt alle Dateien im GA-Modus und zusätzlich:
  
  * System- und Anwendungsereignisprotokolle
  * HTTP-Fehlerprotokolle
  * IIS-Protokolle
  * Setupprotokolle
  * Sonstige Systemprotokolle

In beiden Sammlungsmodi können zusätzliche Ordner für die Datensammlung angegeben werden, indem eine Sammlung mit der folgenden Struktur verwendet wird:

* **Name**: Der Name der Sammlung, der als Name des Unterordners in der ZIP-Datei mit den gesammelten Dateien verwendet wird.
* **Speicherort**: Der Pfad des Ordners auf dem virtuellen Computer, in dem sich die zu sammelnden Datei befinden.
* **Suchmuster**: Das Muster der Namen der zu sammelnden Dateien. Standardwert: \*.
* **Rekursiv**: Falls sich die zu sammelnden Dateien rekursiv unter dem angegebenen Speicherort befinden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Ein Speicherkonto für die Erweiterung, um generierte ZIP-Dateien zu speichern.
* Azure PowerShell. Anweisungen zur Installation finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="add-the-extension"></a>Hinzufügen der Erweiterung
Sie können [Microsoft Azure PowerShell](/previous-versions/azure/dn495240(v=azure.100))-Cmdlets oder [REST-APIs für die Dienstverwaltung](/previous-versions/azure/ee460799(v=azure.100)) verwenden, um die AzureLogCollector-Erweiterung hinzuzufügen.

Für Cloud Services kann das vorhandene Azure PowerShell-Cmdlet **Set AzureServiceExtension** verwendet werden, um die Erweiterung für Clouddienst-Rolleninstanzen zu aktivieren. Jedes Mal, wenn diese Erweiterung durch dieses Cmdlet aktiviert wird, wird für die ausgewählten Rolleninstanzen der ausgewählten Rollen die Protokollsammlung ausgelöst.

Für virtuelle Computer kann das vorhandene Azure PowerShell-Cmdlet **Set-AzureVMExtension** verwendet werden, um die Erweiterung auf virtuellen Computern zu aktivieren. Jedes Mal, wenn diese Erweiterung durch die Cmdlets aktiviert wird, wird für jede Instanz die Protokollsammlung ausgelöst.

Intern verwendet diese Erweiterung die JSON-basierten Argumente „PublicConfiguration“ und „PrivateConfiguration“. Im Folgenden finden Sie das Layout eines JSON-Beispiels für die öffentliche und die private Konfiguration.

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> Für diese Erweiterung ist **PrivateConfiguration** nicht erforderlich. Sie können einfach eine leere Struktur für das Argument **–PrivateConfiguration** angeben.
> 
> 

Befolgen Sie einen der beiden folgenden Schritte, um den AzureLogCollector einer oder mehreren Instanzen eines Clouddiensts oder virtuellen Computers der ausgewählten Rollen hinzuzufügen. Dadurch wird die Ausführung der Sammlungen auf den einzelnen virtuellen Computern ausgelöst, und die gesammelten Dateien werden an das angegebene Azure-Konto gesendet.

## <a name="adding-as-a-service-extension"></a>Hinzufügen als Diensterweiterung
1. Führen Sie die Anweisungen zum Verbinden von Azure PowerShell mit Ihrem Abonnement aus.
2. Geben Sie den Dienstnamen, den Slot, die Rollen und die Rolleninstanzen an, für den bzw. die Sie die AzureLogCollector-Erweiterung hinzufügen und aktivieren möchten.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Geben Sie den zusätzlichen Datenordner an, für den Dateien gesammelt werden (dieser Schritt ist optional).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > Sie können das Token `%roleroot%` verwenden, um das Stammlaufwerk der Rolle anzugeben, da kein Festplattenlaufwerk verwendet wird.
   > 
   > 
4. Geben Sie den Namen und den Schlüssel des Azure-Speicherkontos an, in das die gesammelten Dateien hochgeladen werden.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Rufen Sie „SetAzureServiceLogCollector.ps1“ (am Ende dieses Artikels enthalten) wie folgt auf, um die AzureLogCollector-Erweiterung für einen Clouddienst zu aktivieren. Nachdem die Ausführung abgeschlossen wurde, finden Sie die hochgeladene Datei unter `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Im Folgenden sehen Sie die Definition der Parameter, die an das Skript übergeben werden. (Eine Kopie ist ebenfalls unten enthalten.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: Der Name des Clouddiensts.
* **Roles**: Eine Liste der Rollen, z.B. „WebRole1“ oder „WorkerRole1“.
* **Instances**: Eine Liste der Namen von Rolleninstanzen, durch Komma getrennt. Verwenden Sie das Platzhalterzeichen („*“) für alle Rolleninstanzen.
* **Slot**: Slotname. „Production“ oder „Staging“.
* **Mode**: Sammlungsmodus. „Full“ oder „GA“.
* **StorageAccountName**: Name des Azure-Speicherkontos zum Speichern der gesammelten Daten.
* **StorageAccountKey**: Name des Azure-Speicherkontenschlüssels.
* **AdditionalDataLocationList**: Eine Liste mit der folgenden Struktur:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Hinzufügen als VM-Erweiterung
Führen Sie die Anweisungen zum Verbinden von Azure PowerShell mit Ihrem Abonnement aus.

1. Geben Sie den Namen des Diensts, die VM und den Sammlungsmodus an.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Geben Sie den Namen und den Schlüssel des Azure-Speicherkontos an, in das die gesammelten Dateien hochgeladen werden.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Rufen Sie „SetAzureVMLogCollector.ps1“ (am Ende dieses Artikels enthalten) wie folgt auf, um die AzureLogCollector-Erweiterung für einen Clouddienst zu aktivieren. Nachdem die Ausführung abgeschlossen wurde, finden Sie die hochgeladene Datei unter `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Im Folgenden sehen Sie die Definition der Parameter, die an das Skript übergeben werden. (Eine Kopie ist ebenfalls unten enthalten.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: Der Name des Clouddiensts.
* **VMName**: Der Name des virtuellen Computers.
* **Mode**: Sammlungsmodus. „Full“ oder „GA“.
* **StorageAccountName**: Name des Azure-Speicherkontos zum Speichern der gesammelten Daten.
* **StorageAccountKey**: Name des Azure-Speicherkontenschlüssels.
* **AdditionalDataLocationList**: Eine Liste mit der folgenden Struktur:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>PowerShell-Skriptdateien der Erweiterung
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Nächste Schritte
Nun können Sie Ihre Protokolle an einem einfachen Ort untersuchen oder kopieren.