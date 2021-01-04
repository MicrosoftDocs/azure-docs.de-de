---
title: Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen | Microsoft-Dokumentation
description: Erfahren Sie mehr über PsExec, PowerShell-Skripts und andere Remotetools, die Sie zum Behandeln von Remoteproblemen mit virtuellen Azure-Computern ohne RDP verwenden können.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: c5974388c096c9bc8693c5fc2cf918989c6eadd3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488730"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen

Bei der Behandlung von Problemen auf einem virtuellen Azure-Computer (VM) können Sie eine Verbindung mit dem virtuellen Computer mithilfe der in diesem Artikel erörterten Remotetools herstellen, statt das Remotedesktopprotokoll (RDP) zu verwenden.

## <a name="serial-console"></a>Serielle Konsole

Verwenden Sie eine [serielle Konsole für virtuelle Azure-Computer](serial-console-windows.md), um Befehle auf dem virtuellen Azure-Remotecomputer auszuführen.

## <a name="remote-cmd"></a>Remote-CMD

Laden Sie [PsExec](/sysinternals/downloads/psexec) herunter. Stellen Sie eine Verbindung mit der VM her, indem Sie folgenden Befehl ausführen:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Der Befehl muss auf einem Computer ausgeführt werden, der sich im selben virtuellen Netzwerk befindet.
>* DIP oder HostName kann als Wert für \<computer> verwendet werden.
>* Durch den Parameter „-s“ wird sichergestellt, dass der Befehl mithilfe des Systemkontos (Administratorberechtigungen) aufgerufen wird.
>* PsExec verwendet die TCP-Ports 135 und 445. Somit müssen die zwei Ports in der Firewall geöffnet sein.

## <a name="run-command"></a>Befehl ausführen

Weitere Informationen zum Verwenden der Funktion „Skriptausführung“ zum Ausführen von Skripts auf dem virtuellen Computer finden Sie unter [Ausführen von PowerShell-Skripts in Ihrer Windows-VM mit „Befehl ausführen“](../windows/run-command.md).

## <a name="custom-script-extension"></a>Benutzerdefinierte Skripterweiterung

Sie können das Feature „Benutzerdefinierte Skripterweiterung“ verwenden, um auf der Ziel-VM ein benutzerdefiniertes Skript auszuführen. Zur Verwendung dieses Features müssen die folgenden Bedingungen erfüllt sein:

* Die VM verfügt über Konnektivität.
* Der Agent für virtuelle Azure-Computer ist auf dem virtuellen Computer installiert und funktioniert ordnungsgemäß.
* Die Erweiterung war zuvor nicht auf dem virtuellen Computer installiert.
 
  Die Erweiterung fügt das Skript nur bei der erstmaligen Verwendung ein. Wenn Sie diese Funktion zu einem späteren Zeitpunkt verwenden, erkennt die Erweiterung, dass sie bereits verwendet wurde, und lädt das neue Skript nicht hoch.

Laden Sie das Skript in ein Speicherkonto hoch, und erstellen Sie dafür einen separaten Container. Führen Sie anschließend das folgende Skript in Azure PowerShell auf einem Computer aus, der Konnektivität mit der VM aufweist.

### <a name="for-classic-deployment-model-vms"></a>Für mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Für über Azure Resource Manager bereitgestellte virtuelle Computer

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Remote-PowerShell

>[!NOTE]
>Der TCP-Port 5986 (HTTPS) muss geöffnet sein, damit Sie diese Option verwenden können.
>
>Für über Azure Resource Manager bereitgestellte virtuelle Computer müssen Sie Port 5986 in der Netzwerksicherheitsgruppe (NSG) öffnen. Weitere Informationen finden Sie bei den Sicherheitsgruppen. 
>
>Für RDFE-VMs benötigen Sie einen Endpunkt mit einem privaten Port (5986) und einem öffentlichen Port. Anschließend müssen Sie auch den entsprechenden öffentlichen Port in der NSG öffnen.

### <a name="set-up-the-client-computer"></a>Einrichten des Clientcomputers

Um PowerShell zum Herstellen von Remoteverbindungen mit der VM zu verwenden, müssen Sie zunächst den Clientcomputer so einrichten, dass der die Verbindung zulässt. Fügen Sie dazu die VM der PowerShell-Liste mit vertrauenswürdigen Hosts hinzu, indem Sie den folgenden Befehl ausführen.

So fügen Sie der Liste der vertrauenswürdigen Hosts einen virtuellen Computer hinzu

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

So fügen Sie der Liste der vertrauenswürdigen Hosts mehrere virtuelle Computer hinzu

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

So fügen Sie der Liste der vertrauenswürdigen Hosts alle Computer hinzu:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Aktivieren von RemotePS auf der VM

Verwenden Sie für mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer die benutzerdefinierte Skripterweiterung, um das folgende Skript auszuführen:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Verwenden Sie für über Azure Resource Manager bereitgestellte virtuelle Computer die Funktion „Befehl ausführen“ im Portal, um das Skript „EnableRemotePS“ auszuführen:

![Befehl ausführen](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Führen Sie den folgenden Befehl basierend auf dem Standort des Clientcomputers aus:

* Außerhalb des virtuellen Netzwerks oder der Bereitstellung

  * Führen Sie für mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer den folgenden Befehl aus:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Fügen Sie für über Azure Resource Manager bereitgestellte virtuelle Computer der öffentlichen IP-Adresse zunächst einen DNS-Namen hinzu. Detaillierte Schritte dazu finden Sie unter [Erstellen eines vollqualifizierten Domänennamens im Azure-Portal für eine Windows-VM](../create-fqdn.md). Führen Sie dann den folgenden Befehl aus:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Führen Sie innerhalb des virtuellen Netzwerks oder der Bereitstellung den folgenden Befehl aus:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Durch das Festlegen des Flags „SkipCaCheck“ wird beim Starten der Sitzung die Anforderung zum Importieren eines Zertifikats in die VM umgangen.

Sie können außerdem das Cmdlet „Invoke-Command“ verwenden, um ein Skript remote auf dem virtuellen Computer auszuführen.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Remoteregistrierung

>[!NOTE]
>Zur Nutzung dieser Option muss TCP-Port 135 oder 445 geöffnet sein.
>
>Für über Azure Resource Manager bereitgestellte virtuelle Computer müssen Sie Port 5986 in der NSG öffnen. Weitere Informationen finden Sie bei den Sicherheitsgruppen. 
>
>Für RDFE-VMs benötigen Sie einen Endpunkt mit einem privaten Port 5986 und einem öffentlichen Port. Sie müssen auch den entsprechenden öffentlichen Port in der NSG öffnen.

1. Öffnen Sie über einen anderen virtuellen Computer im selben virtuellen Netzwerk den Registrierungs-Editor („regedit.exe“).

2. Wählen Sie **Datei** > **Mit Netzwerkregistrierung verbinden** aus.

   ![Registrierungs-Editor](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Suchen Sie den virtuellen Zielcomputer anhand von **Hostname** oder **dynamischer IP** (vorzugsweise), indem Sie den entsprechenden Wert im Feld **Namen des auszuwählenden Objekts eingeben** eingeben.

   ![Namen des auszuwählenden Objekts eingeben](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Geben Sie die Anmeldeinformationen für die Ziel-VM ein.

5. Nehmen Sie alle erforderlichen Änderungen an der Registrierung vor.

## <a name="remote-services-console"></a>Remotedienste-Konsole

>[!NOTE]
>Zur Nutzung dieser Option muss TCP-Port 135 oder 445 geöffnet sein.
>
>Für über Azure Resource Manager bereitgestellte virtuelle Computer müssen Sie Port 5986 in der NSG öffnen. Weitere Informationen finden Sie bei den Sicherheitsgruppen. 
>
>Für RDFE-VMs benötigen Sie einen Endpunkt mit einem privaten Port 5986 und einem öffentlichen Port. Sie müssen auch den entsprechenden öffentlichen Port in der NSG öffnen.

1. Öffnen Sie über einen anderen virtuellen Computer im selben virtuellen Netzwerk eine Instanz von **Services.msc**.

2. Klicken Sie mit der rechten Maustaste auf **Dienste (lokal)** .

3. Wählen Sie **Verbindung mit einem anderen Computer herstellen** aus.

   ![Remotedienst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Geben Sie die dynamische IP-Adresse des virtuellen Zielcomputers ein.

   ![Eingeben der dynamischen IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Nehmen Sie alle notwendigen Änderungen an den Diensten vor.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Cmdlet Enter-PSSession finden Sie unter [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-5.1).
- Weitere Informationen zur benutzerdefinierten Skripterweiterung für Windows unter Verwendung des klassischen Bereitstellungsmodells finden Sie unter [Benutzerdefinierte Skripterweiterung für Windows](../extensions/custom-script-windows.md).
- PsExec ist Teil der [PSTools-Suite](https://download.sysinternals.com/files/PSTools.zip).
- Weitere Informationen zur PSTools-Suite finden Sie unter [PSTools](/sysinternals/downloads/pstools).