---
title: Hochladen einer generalisierten VHD zum Erstellen mehrerer virtueller Computer in Azure | Microsoft-Dokumentation
description: Laden Sie eine generalisierte virtuelle Festplatte (VHD) in ein Azure Storage-Konto hoch, um einen virtuellen Windows-Computer für die Verwendung mit dem Resource Manager-Bereitstellungsmodell zu erstellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 5b38d022d372e7d35ba2dbeaef90660ce95f73fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715113"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Hochladen einer generalisierten VHD in Azure und Erstellen einer neuen VM

Dieses Thema beschreibt das Hochladen eines nicht verwalteten, generalisierten Datenträgers in ein Speicherkonto, um damit anschließend einen neuen virtuellen Computer zu erstellen. Auf einem generalisierten VHD-Image wurden alle Ihre persönlichen Kontoinformationen mit Sysprep entfernt. 

Informationen darüber, wie Sie einen virtuellen Computer aus einer speziellen VHD in einem Speicherkonto erstellen, finden Sie unter [Erstellen eines virtuellen Computers aus einer speziellen VHD](sa-create-vm-specialized.md).

In diesem Thema wird die Verwendung von Speicherkonten erläutert, es wird jedoch empfohlen, für das Verschieben stattdessen Managed Disks zu verwenden. Eine vollständige exemplarische Vorgehensweise zum Vorbereiten, Hochladen und Erstellen eines neuen virtuellen Computers mithilfe von Managed Disks finden Sie unter [Erstellen virtueller Computer aus einer generalisierten VHD, die mit Managed Disks in Azure hochgeladen wurde](upload-generalized-managed.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prepare-the-vm"></a>Vorbereiten des virtuellen Computers

Bei einer generalisierten VHD wurden alle Ihre persönlichen Kontoinformationen mit Sysprep entfernt. Wenn Sie die VHD als Image zum Erstellen neuer virtueller Computer verwenden möchten, sollten Sie folgende Schritte ausführen:
  
  * [Vorbereiten einer Windows-VHD für das Hochladen in Azure.](prepare-for-upload-vhd-image.md) 
  * Generalisieren virtueller Computer mithilfe von Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Verallgemeinern eines virtuellen Windows-Computers mit Sysprep
In diesem Abschnitt wird erläutert, wie Sie den virtuellen Windows-Computer für die Verwendung als Image verallgemeinern. Sysprep entfernt unter anderem alle persönlichen Kontoinformationen, und bereitet den Computer darauf vor, als Image verwendet zu werden. Weitere Informationen zu Sysprep finden Sie unter [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Verwenden von Sysprep: Eine Einführung).

Stellen Sie sicher, dass die auf dem Computer ausgeführten Serverrollen von Sysprep unterstützt werden. Weitere Informationen finden Sie unter [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Wenn Sie Sysprep vor dem Hochladen der virtuellen Festplatte in Azure zum ersten Mal ausführen, stellen Sie sicher, dass Sie vor dem Ausführen von Sysprep [Ihren virtuellen Computer vorbereitet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) haben. 
> 
> 

1. Melden Sie sich bei dem virtuellen Windows-Computer an.
2. Öffnen Sie das Eingabeaufforderungsfenster als Administrator. Wechseln Sie in das Verzeichnis **%windir%\system32\sysprep**, und führen Sie anschließend `sysprep.exe` aus.
3. Wählen Sie unter **Systemvorbereitungsprogramm** die Option **Out-of-Box-Experience (OOBE) für System aktivieren**, und vergewissern Sie sich, dass das Kontrollkästchen **Verallgemeinern** aktiviert ist.
4. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren** aus.
5. Klicken Sie auf **OK**.
   
    ![Starten von Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Nach Abschluss von Sysprep wird die virtuelle Maschine heruntergefahren. 

> [!IMPORTANT]
> Starten Sie den virtuellen Computer nicht neu, bis Sie mit dem Hochladen der VHD in Azure oder dem Erstellen eines Images des virtuellen Computers fertig sind. Wenn der virtuelle Computer versehentlich neu gestartet wird, führen Sie Sysprep aus, um ihn erneut zu generalisieren.
> 
> 


## <a name="upload-the-vhd"></a>Hochladen der VHD

Laden Sie die VHD in ein Azure Storage-Konto hoch.

### <a name="log-in-to-azure"></a>Anmelden an Azure
Wenn Sie PowerShell 1.4 oder eine höhere Version noch nicht installiert haben, finden Sie entsprechende Informationen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

1. Öffnen Sie Azure PowerShell, und melden Sie sich bei Ihrem Azure-Konto an. Ein Popupfenster wird geöffnet, in das Sie die Anmeldeinformationen für Ihr Azure-Konto eingeben können.
   
    ```powershell
    Connect-AzAccount
    ```
2. Rufen Sie die Abonnement-IDs für Ihre verfügbaren Abonnements ab.
   
    ```powershell
    Get-AzSubscription
    ```
3. Legen Sie das richtige Abonnement mit der Abonnement-ID fest. Ersetzen Sie `<subscriptionID>` durch die ID des richtigen Abonnements.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Abrufen des Speicherkontos
Sie benötigen ein Speicherkonto in Azure, in dem das hochgeladene VM-Image gespeichert wird. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen. 

Geben Sie zum Anzeigen der verfügbaren Speicherkonten Folgendes ein:

```powershell
Get-AzStorageAccount
```

Wenn Sie ein vorhandenes Speicherkonto verwenden möchten, fahren Sie mit dem Abschnitt „Hochladen des VM-Images“ fort.

Wenn Sie ein neues Speicherkonto erstellen möchten, gehen Sie wie folgt vor:

1. Sie benötigen den Namen der Ressourcengruppe, in der das Speicherkonto erstellt werden soll. Geben Sie den folgenden Befehl ein, um alle in Ihrem Abonnement enthaltenen Ressourcengruppen zu ermitteln:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Zum Erstellen einer Ressourcengruppe mit dem Namen **myResourceGroup** in der Region **USA, Westen** geben Sie den folgenden Befehl ein:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Erstellen Sie mit dem Cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) das Speicherkonto **mystorageaccount** in dieser Ressourcengruppe:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Starten des Uploads 

Verwenden Sie das Cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), um das Image in einen Container in Ihrem Speicherkonto hochzuladen. In diesem Beispiel wird die Datei **myVHD.vhd** aus `"C:\Users\Public\Documents\Virtual hard disks\"` in das Speicherkonto **mystorageaccount** in der Ressourcengruppe **myResourceGroup** hochgeladen. Die Datei wird im Container **mycontainer** abgelegt. Der neue Dateiname lautet **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Bei erfolgreicher Ausführung erhalten Sie eine Antwort, die etwa wie folgt aussieht:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Abhängig von Ihrer Netzwerkverbindung und der Größe der VHD-Datei kann die Ausführung dieses Befehls einige Zeit in Anspruch nehmen.


## <a name="create-a-new-vm"></a>Erstellen eines neuen virtuellen Computers 

Sie können jetzt die hochgeladene VHD zum Erstellen eines neuen virtuellen Computers verwenden. 

### <a name="set-the-uri-of-the-vhd"></a>Festlegen des URI der virtuellen Festplatte

Der URI für die zu verwendende virtuelle Festplatte liegt in folgendem Format vor: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. In diesem Beispiel befindet sich die virtuelle Festplatte mit dem Namen **myVHD** im Speicherkonto **mystorageaccount** im Container **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Erstellen Sie das VNet und das Subnetz des [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md).

1. Erstellen Sie das Subnetz. Das folgende Beispiel erstellt ein Subnetz mit dem Namen **mySubnet** in der Ressourcengruppe **myResourceGroup** mit dem Adresspräfix **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Erstellen des virtuellen Netzwerks Das folgende Beispiel erstellt ein virtuelles Netzwerk mit dem Namen **myVnet** im Speicherort **USA, Westen** mit dem Adresspräfix **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Erstellen einer öffentlichen IP-Adresse und einer Netzwerkschnittstelle
Sie benötigen eine [öffentliche IP-Adresse](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) und eine Netzwerkschnittstelle, um die Kommunikation mit dem virtuellen Computer im virtuellen Netzwerk zu ermöglichen.

1. Erstellen einer öffentlichen IP-Adresse Dieses Beispiel erstellt eine öffentliche IP-Adresse mit dem Namen **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Erstellen der NIC Dieses Beispiel erstellt eine NIC mit dem Namen **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Erstellen einer Netzwerksicherheitsgruppe und einer RDP-Regel
Damit Sie sich mit RDP an Ihrem virtuellen Computer anmelden können, benötigen Sie eine Sicherheitsregel, die RDP-Zugriff auf Port 3389 zulässt. 

Dieses Beispiel erstellt eine NSG namens **myNsg**, das eine Regel namens **myRdpRule** enthält, die RDP-Datenverkehr über Port 3389 zulässt. Weitere Informationen zu NSGs finden Sie unter [Öffnen von Ports für einen virtuellen Computer in Azure mithilfe von PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Erstellen einer Variablen für das virtuelle Netzwerk
Erstellen einer Variablen für das abgeschlossene virtuelle Netzwerk 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Erstellen des virtuellen Computers
Das folgende PowerShell-Skript veranschaulicht, wie Sie die Konfiguration für den virtuellen Computer einrichten und das hochgeladene VM-Image als Quelle für die neue Installation verwenden.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Stellen Sie sicher, dass der virtuelle Computer erstellt wurde
Anschließend müsste der neu erstellte virtuelle Computer im [Azure-Portal](https://portal.azure.com) unter **Durchsuchen** > **Virtuelle Computer** angezeigt werden. Alternativ können Sie ihn auch mit den folgenden PowerShell-Befehlen anzeigen:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Verwalten des neuen virtuellen Computers mit Azure PowerShell finden Sie unter [Verwalten von virtuellen Computern mit Azure Resource Manager und PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


