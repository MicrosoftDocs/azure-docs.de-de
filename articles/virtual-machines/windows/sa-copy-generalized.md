---
title: Erstellen eines nicht verwalteten Images eines generalisierten virtuellen Computers in Azure | Microsoft-Dokumentation
description: Erstellen Sie ein nicht verwaltetes Image eines generalisierten virtuellen Windows-Computers, um damit mehrere Kopien eines virtuellen Computers in Azure zu erstellen.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: e1ed419892412c1fb9334fed74b82c53154723ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708420"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Erstellen nicht verwalteter VM-Images aus virtuellen Azure-Computern

In diesem Artikel wird die Verwendung von Speicherkonten behandelt. Es wird empfohlen, anstelle eines Speicherkontos verwaltete Datenträger und verwaltete Images zu verwenden. Weitere Informationen finden Sie unter [Erstellen verwalteter Images generalisierter virtueller Computer in Azure](capture-image-resource.md).

In diesem Artikel wird gezeigt, wie Sie Azure PowerShell zum Erstellen eines Images eines generalisierten virtuellen Azure-Computers mithilfe eines Speicherkontos verwenden. Sie können das Image dann nutzen, um eine andere VM zu erstellen. Das Image umfasst den Betriebssystemdatenträger und die an den virtuellen Computer angefügten Datenträger. Das Image enthält nicht die Ressourcen des virtuellen Netzwerks. Sie müssen diese Ressourcen also einrichten, wenn Sie die neue VM erstellen. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-vm"></a>Generalisieren des virtuellen Computers 
In diesem Abschnitt wird erläutert, wie Sie den virtuellen Windows-Computer für die Verwendung als Image verallgemeinern. Beim Generalisieren einer VM werden u.a. alle persönlichen Kontoinformationen entfernt, und der Computer wird für die Verwendung als Image vorbereitet. Weitere Informationen zu Sysprep finden Sie unter [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Verwenden von Sysprep: Eine Einführung).

Stellen Sie sicher, dass die auf dem Computer ausgeführten Serverrollen von Sysprep unterstützt werden. Weitere Informationen finden Sie unter [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Wenn Sie die virtuelle Festplatte (VHD) zum ersten Mal in Azure hochladen, stellen Sie sicher, dass Sie vor dem Ausführen von Sysprep [Ihren virtuellen Computer vorbereitet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) haben. 
> 
> 

Sie können auch einen virtuellen Linux-Computer unter Verwendung von `sudo waagent -deprovision+user` generalisieren und dann mithilfe von PowerShell erfassen. Informationen zur Verwendung der Befehlszeilenschnittstelle zum Erfassen eines virtuellen Computers finden Sie unter [Generalisieren und Erfassen eines virtuellen Linux-Computers mithilfe der Azure-Befehlszeilenschnittstelle](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Anmelden an Azure PowerShell
1. Öffnen Sie Azure PowerShell, und melden Sie sich bei Ihrem Azure-Konto an.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Ein Popupfenster wird geöffnet, in das Sie die Anmeldeinformationen für Ihr Azure-Konto eingeben können.
2. Rufen Sie die Abonnement-IDs für Ihre verfügbaren Abonnements ab.
   
    ```powershell
    Get-AzSubscription
    ```
3. Legen Sie das richtige Abonnement mit der Abonnement-ID fest.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Aufheben der VM-Zuordnung und Festlegen des Status auf „Generalisiert“

> [!IMPORTANT] 
> Für einen als generalisiert markierten virtuellen Computer können keine Tags hinzugefügt, bearbeitet oder entfernt werden. Wenn Sie dem virtuellen Computer ein Tag hinzufügen möchten, muss dieser Schritt ausgeführt werden, bevor der Computer als generalisiert markiert wird.
> 

1. Heben Sie die Zuordnung der VM-Ressourcen auf.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Der *Status* der VM im Azure-Portal ändert sich von **Beendet** in **Beendet (Zuordnung aufgehoben)** .
2. Legen Sie den Status des virtuellen Computers auf **Generalisiert**fest. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Überprüfen Sie den Status der VM. Im Abschnitt **OSState/generalized** der VM sollte **DisplayStatus** auf **VM generalized** festgelegt sein.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Erstellen des Images

Erstellen Sie mithilfe des folgenden Befehls ein nicht verwaltetes Image des virtuellen Computers im Zielspeichercontainer. Das Image wird im demselben Speicherkonto wie der ursprüngliche virtuelle Computer erstellt. Der `-Path`-Parameter speichert eine Kopie der JSON-Vorlage für den virtuellen Quellcomputer auf dem lokalen Computer. Der Parameter `-DestinationContainerName` ist der Name des Containers, in dem Ihre Images aufbewahrt werden sollen. Falls der Container noch nicht vorhanden ist, wird er erstellt.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Sie können die URL Ihres Image aus der JSON-Dateivorlage abrufen. Wechseln Sie zum Abschnitt **resources** > **storageProfile** > **osDisk** > **image** > **uri**, um den vollständigen Pfad zu Ihrem Image zu finden. Die URL des Image sieht wie folgt aus: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Sie können den URI auch im Portal überprüfen. Das Image wird in einen Container mit dem Namen **system** in Ihrem Speicherkonto kopiert. 

## <a name="create-a-vm-from-the-image"></a>Erstellen virtueller Computer aus dem Image

Sie können nun einen oder mehrere virtuelle Computer aus dem nicht verwalteten Image erstellen.

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
    $location = "West US"
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
Das folgende PowerShell-Skript schließt die Konfigurationen für den virtuellen Computer ab und verwendet das nicht verwaltete Image als Quelle für die neue Installation.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Stellen Sie sicher, dass der virtuelle Computer erstellt wurde
Anschließend müsste der neu erstellte virtuelle Computer im [Azure-Portal](https://portal.azure.com) unter **Durchsuchen** > **Virtuelle Computer** angezeigt werden. Alternativ können Sie ihn auch mit den folgenden PowerShell-Befehlen anzeigen:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Verwalten des neuen virtuellen Computers mit Azure PowerShell finden Sie unter [Verwalten von virtuellen Computern mit Azure Resource Manager und PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


