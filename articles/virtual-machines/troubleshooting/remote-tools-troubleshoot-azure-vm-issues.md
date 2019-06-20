---
title: Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen | Microsoft-Dokumentation
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 513ce98703e67053ab0bcac3e6fc7a3e959f6870
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717272"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen

Bei der Behandlung von Problemen auf einem virtuellen Azure-Computer (VM) können Sie eine Verbindung mit der VM mithilfe der in diesem Artikel erörterten Remotetools herstellen, statt das Remotedesktopprotokoll (RDP) zu verwenden.

## <a name="serial-console"></a>Serielle Konsole

Verwenden Sie die [Serielle Konsole für virtuelle Computer](serial-console-windows.md), um Befehle auf der Azure-Remote-VM auszuführen.

## <a name="remote-cmd"></a>Remote-CMD

Laden Sie [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) herunter. Stellen Sie eine Verbindung mit der VM her, indem Sie folgenden Befehl ausführen:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Der Befehl muss auf einem Computer ausgeführt werden, der sich im gleichen VNET befindet.
>* „DIP“ oder „HostName“ kann als Wert für \<computer> verwendet werden.
>* Durch den Parameter „-s“ wird sichergestellt, dass der Befehl mithilfe des Systemkontos (Administratorberechtigungen) aufgerufen wird.
>* PsExec verwendet die TCP-Ports 135 und 445. Daher müssen die zwei Ports in der Firewall geöffnet sein.

## <a name="run-commands"></a>Befehle ausführen

Weitere Informationen zum Verwenden der Funktion „Befehle ausführen“ zum Ausführen von Skripts auf der VM finden Sie unter [Ausführen von PowerShell-Skripts in Ihrer Windows-VM mit „Befehl ausführen“](../windows/run-command.md).

## <a name="customer-script-extension"></a>Benutzerdefinierte Skripterweiterung

Sie können das Feature „Benutzerdefinierte Skripterweiterung“ verwenden, um auf der Ziel-VM ein benutzerdefiniertes Skript auszuführen. Zur Verwendung dieses Features müssen die folgenden Bedingungen erfüllt sein:

* Die VM verfügt über Konnektivität.

* Der Azure-Agent ist auf der VM installiert und funktioniert ordnungsgemäß.

* Die Erweiterung war zuvor nicht auf dem virtuellen Computer installiert.
 
  Die Erweiterung fügt das Skript nur bei der erstmaligen Verwendung ein. Wenn Sie dieses Feature zu einem späteren Zeitpunkt verwenden, erkennt die Erweiterung, dass sie bereits verwendet wurde, und lädt das neue Skript nicht hoch.

Sie müssen Ihr Skript in ein Speicherkonto hochladen und dafür einen eigenen Container erstellen. Führen Sie anschließend das folgende Skript in Azure PowerShell auf einem Computer aus, der Konnektivität mit der VM aufweist.

### <a name="for-v1-vms"></a>Für V1-VMs

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>Für V2-VMs

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Remote-PowerShell

>[!Note]
>Der TCP-Port 5986 (HTTPS) muss geöffnet sein, damit Sie diese Option verwenden können.
>
>Für ARM-VMs müssen Sie den Port 5986 in der Netzwerksicherheitsgruppe (NSG) öffnen. Weitere Informationen finden Sie bei den Sicherheitsgruppen. 
>
>Für RDFE-VMs benötigen Sie einen Endpunkt mit einem privaten Port (5986) und einem öffentlichen Port. Anschließend müssen Sie auch den entsprechenden öffentlichen Port in der NSG öffnen.

### <a name="set-up-the-client-computer"></a>Einrichten des Clientcomputers

Um PowerShell zum Herstellen von Remoteverbindungen mit der VM zu verwenden, müssen Sie zunächst den Clientcomputer so einrichten, dass der die Verbindung zulässt. Fügen Sie dazu die VM der PowerShell-Liste mit vertrauenswürdigen Hosts hinzu, indem Sie den folgenden Befehl ausführen.

So fügen Sie der Liste der vertrauenswürdigen Hosts eine VM hinzu:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

So fügen Sie der Liste der vertrauenswürdigen Hosts mehrere VMs hinzu:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

So fügen Sie der Liste der vertrauenswürdigen Hosts alle Computer hinzu:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Aktivieren von RemotePS auf der VM

Verwenden Sie für klassische VMs die benutzerdefinierte Skripterweiterung, um das folgende Skript auszuführen:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Verwenden Sie für ARM-VMs „Befehle ausführen“ im Portal, um das Skript „EnableRemotePS“ auszuführen:

![Befehl ausführen](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

Führen Sie den folgenden Befehl aus, abhängig vom Standort des Clientcomputers:

* Außerhalb des VNETs oder der Bereitstellung

  * Führen Sie für eine klassische VM den folgenden Befehl aus:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Fügen Sie für eine ARM-VM der öffentlichen IP-Adresse zunächst einen DNS-Namen hinzu. Detaillierte Schritte dazu finden Sie unter [Erstellen eines vollqualifizierten Domänennamens im Azure-Portal für eine Windows-VM](../windows/portal-create-fqdn.md). Führen Sie dann den folgenden Befehl aus:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Führen Sie innerhalb des VNETs oder der Bereitstellung den folgenden Befehl aus:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Durch das Festlegen des Flags „SkipCaCheck“ wird beim Starten der Sitzung die Anforderung zum Importieren eines Zertifikats in die VM umgangen.

Sie können außerdem das Cmdlet „Invoke-Command“ verwenden, um remote ein Skript auf der VM auszuführen:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Remoteregistrierung

>[!Note]
>Zur Nutzung dieser Option muss TCP-Port 135 oder 445 geöffnet sein.
>
>Für ARM-VMs müssen Sie Port 5986 in der NSG öffnen. Weitere Informationen finden Sie bei den Sicherheitsgruppen. 
>
>Für RDFE-VMs benötigen Sie einen Endpunkt mit einem privaten Port 5986 und einem öffentlichen Port. Ferner müssen Sie auch den entsprechenden öffentlichen Port in der NSG öffnen.

1. Öffnen Sie in einer anderen VM im gleichen VNET den Registrierungs-Editor (regedit.exe).

2. Wählen Sie **Datei** >**Mit Netzwerkregistrierung verbinden** aus.

   ![Remoteoption](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Suchen Sie die Ziel-VM anhand von **Hostname** oder **dynamischer IP** (vorzugsweise), indem Sie den entsprechenden Wert im Feld "Enter the object name to select" (Namen des auszuwählenden Objekts eingeben) eingeben.

   ![Remoteoption](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Geben Sie die Anmeldeinformationen für die Ziel-VM ein.

5. Nehmen Sie alle erforderlichen Änderungen an der Registrierung vor.

## <a name="remote-services-console"></a>Remotedienste-Konsole

>[!Note]
>Zur Nutzung dieser Option muss TCP-Port 135 oder 445 geöffnet sein.
>
>Für ARM-VMs müssen Sie Port 5986 in der NSG öffnen. Weitere Informationen finden Sie bei den Sicherheitsgruppen. 
>
>Für RDFE-VMs benötigen Sie einen Endpunkt mit einem privaten Port 5986 und einem öffentlichen Port. Anschließend müssen Sie auch den entsprechenden öffentlichen Port in der NSG öffnen.

1. Öffnen Sie in einer anderen VM im gleichen VNET eine Instanz von **Services.msc**.

2. Klicken Sie mit der rechten Maustaste auf **Dienste (lokal)** .

3. Wählen Sie **Verbindung mit einem anderen Computer herstellen** aus.

   ![Remotedienst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Geben Sie die dynamische IP der Ziel-VM ein.

   ![Eingeben der DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Nehmen Sie alle notwendigen Änderungen an den Diensten vor.

## <a name="next-steps"></a>Nächste Schritte

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Benutzerdefinierte Skripterweiterung für Windows im Rahmen des klassischen Bereitstellungsmodells](../extensions/custom-script-classic.md)

PsExec ist Teil der [PSTools-Suite](https://download.sysinternals.com/files/PSTools.zip).

Weitere Informationen zur PSTools-Suite finden Sie unter [PSTools-Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


