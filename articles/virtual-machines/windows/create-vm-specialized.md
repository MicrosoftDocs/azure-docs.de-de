---
title: Erstellen einer Windows-VM auf Grundlage einer speziellen VHD in Azure
description: Erstellen Sie eine neue Windows-VM, indem Sie über das Resource Manager-Bereitstellungsmodell einen speziellen verwalteten Datenträger als Betriebssystemdatenträger anfügen.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: cddc7f4f453f22b0cb36b1d3a1e9c2fba2dcabaf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455102"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Erstellen einer Windows-VM von einem speziellen Datenträger mithilfe von PowerShell

Erstellen Sie eine neue VM, indem Sie einen speziellen verwalteten Datenträger als Betriebssystemdatenträger anfügen. Ein spezieller Datenträger ist eine Kopie einer virtuellen Festplatte (VHD) einer bereits vorhandenen VM, die die Benutzerkonten, Anwendungen und andere Statusdaten Ihrer ursprünglichen VM enthält. 

Sie haben mehrere Möglichkeiten:
* [Verwenden eines vorhandenen verwalteten Datenträgers](#option-1-use-an-existing-disk). Diese Option ist nützlich, wenn eine VM nicht ordnungsgemäß funktioniert. Sie können die VM löschen und den verwalteten Datenträger dann zum Erstellen einer neuen VM wiederverwenden. 
* [Hochladen einer VHD-Datei](#option-2-upload-a-specialized-vhd) 
* [Kopieren einer vorhandenen Azure VM mithilfe von Momentaufnahmen](#option-3-copy-an-existing-azure-vm)

Sie können außerdem das Azure-Portal verwenden, um [eine neue VM aus einer speziellen VHD zu erstellen](create-vm-specialized-portal.md).

In diesem Artikel wird die Verwendung verwalteter Datenträger gezeigt. Wenn Sie über eine Legacybereitstellung verfügen, die ein Speicherkonto erfordert, finden Sie weitere Informationen unter [Erstellen eines virtuellen Computers auf der Grundlage einer spezialisierten VHD in einem Speicherkonto](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized).

> [!IMPORTANT]
> 
> Wenn Sie zur Erstellung einer neuen VM einen speziellen Datenträger verwenden, behält die neue VM den Computernamen der ursprünglichen VM bei. Andere computerspezifische Informationen (z. B. die CMID) werden ebenfalls beibehalten. In einigen Fällen können diese doppelten Informationen Probleme verursachen. Achten Sie beim Kopieren einer VM darauf, auf welche Arten von computerspezifischen Informationen Ihre Anwendungen angewiesen sind.  
> Verwenden Sie daher keinen speziellen Datenträger, wenn Sie mehrere VMs erstellen möchten. Für größere Bereitstellungen sollten Sie stattdessen [ein Image erstellen](capture-image-resource.md) und dann [dieses Image zum Erstellen mehrerer VMs verwenden](create-vm-generalized-managed.md).

Es wird empfohlen, die Anzahl gleichzeitiger Bereitstellungen auf Grundlage einer einzelnen VHD oder Momentaufnahme auf 20 VMs zu beschränken. 

## <a name="option-1-use-an-existing-disk"></a>Option 1: Verwenden eines vorhandenen Datenträgers

Wenn Sie einen virtuellen Computer gelöscht haben und den Betriebssystemdatenträger erneut verwenden möchten, um einen neuen virtuellen Computer zu erstellen, verwenden Sie [Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Sie können dann diesen Datenträger als Betriebssystemdatenträger an eine [neue VM](#create-the-new-vm) anfügen.

## <a name="option-2-upload-a-specialized-vhd"></a>Option 2: Hochladen einer speziellen VHD

Sie können die VHD über eine spezielle VM hochladen, die mit einem lokalen Virtualisierungstool (z.B. Hyper-V) erstellt wurde, oder über eine VM, die aus einer anderen Cloud exportiert wurde.

### <a name="prepare-the-vm"></a>Vorbereiten des virtuellen Computers
Verwenden Sie die VHD unverändert, um einen neuen virtuellen Computer zu erstellen. 
  
  * [Vorbereiten einer Windows-VHD für das Hochladen in Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Generalisieren Sie den virtuellen Computer **nicht** mithilfe von Sysprep.
  * Entfernen Sie alle auf dem virtuellen Computer installierten Gastvirtualisierungstools und Agents (z.B. VMware-Tools).
  * Stellen Sie sicher, dass der virtuelle Computer so konfiguriert ist, dass die IP-Adressen und DNS-Einstellungen über DHCP abgerufen werden. Dadurch wird sichergestellt, dass der Server beim Starten eine IP-Adresse innerhalb des virtuellen Netzwerks bezieht. 


### <a name="upload-the-vhd"></a>Hochladen der VHD

Sie können nun eine VHD direkt in einen verwalteten Datenträger hochladen. Eine Anleitung hierzu finden Sie unter [Hochladen einer VHD in Azure mithilfe von Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Option 3: Kopieren einer vorhandenen Azure-VM

Sie können eine Kopie einer VM mit verwalteten Datenträgern erstellen, indem Sie eine Momentaufnahme von der VM erstellen und dann mit diesem einen neuen verwalteten Datenträger und eine neue VM erstellen.

Wenn Sie einen vorhandenen virtuellen Computer in eine andere Region kopieren möchten, können Sie unter Verwendung von AzCopy [eine Kopie eines Datenträgers in einer anderen Region erstellen](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Erstellen einer Momentaufnahme des Betriebssystemdatenträgers

Sie können eine Momentaufnahme einer gesamten VM (einschließlich aller Datenträger) oder nur von einem einzelnen Datenträger erstellen. Die folgenden Schritte zeigen, wie Sie mit dem Cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) eine Momentaufnahme von nur dem Betriebssystemdatenträger Ihrer VM erstellen. 

Legen Sie zuerst einige Parameter fest. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Rufen Sie das VM-Objekt ab.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Rufen Sie den Namen des Betriebssystemdatenträgers ab.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Erstellen Sie die Momentaufnahmekonfiguration. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Erstellen Sie die Momentaufnahme.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Um die Momentaufnahme zum Erstellen einer auf hohe Leistung ausgelegten VM zu nutzen, fügen Sie dem Befehl New-AzSnapshotConfig den `-AccountType Premium_LRS`-Parameter hinzu. Dieser Parameter erstellt die Momentaufnahme so, dass sie als verwalteter Premium-Datenträger gespeichert wird. Premium-Managed Disks-Datenträger sind teurer als Standard, daher sollten Sie unbedingt prüfen, ob Sie Premium benötigen, bevor Sie diesen Parameter verwenden.

### <a name="create-a-new-disk-from-the-snapshot"></a>Erstellen eines neuen Datenträgers über die Momentaufnahme

Erstellen Sie mit [New-AzDisk](/powershell/module/az.compute/new-azdisk) einen verwalteten Datenträger aus der Momentaufnahme. Dieses Beispiel verwendet *myOSDisk* für den Namen des Datenträgers an.

Erstellen Sie eine neue Ressourcengruppe für die neue VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Legen Sie den Namen des Betriebssystemdatenträgers fest. 

```powershell
$osDiskName = 'myOsDisk'
```

Erstellen Sie den verwalteten Datenträger.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Erstellen des neuen virtuellen Computers 

Erstellen Sie Netzwerkressourcen und andere VM-Ressourcen für die neue VM.

### <a name="create-the-subnet-and-virtual-network"></a>Erstellen des Subnetzes und des virtuellen Netzwerks

Erstellen Sie das [virtuelle Netzwerk](../../virtual-network/virtual-networks-overview.md) und das Subnetz für den virtuellen Computer.

1. Erstellen Sie das Subnetz. In diesem Beispiel wird ein Subnetz mit dem Namen *mySubNet* in der Ressourcengruppe *myDestinationResourceGroup* erstellt und das Adresspräfix des Subnetzes auf *10.0.0.0/24* festgelegt.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Erstellen des virtuellen Netzwerks Dieses Beispiel legt den Namen des virtuellen Netzwerks auf *myVnetName*, den Standort auf *USA, Westen* und das Adresspräfix für das virtuelle Netzwerk auf *10.0.0.0/16* fest. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Erstellen einer Netzwerksicherheitsgruppe und einer RDP-Regel
Damit Sie sich mit dem Remotedesktopprotokoll (RDP) bei Ihrem virtuellen Computer anmelden können, benötigen Sie eine Sicherheitsregel, die RDP-Zugriff an Port 3389 zulässt. In unserem Beispiel wurde die VHD für die neue VM aus einer vorhandenen spezialisierten VM erstellt, daher können Sie für das RDP ein Konto des virtuellen Quellcomputers verwenden.

In diesem Beispiel werden der Name der Netzwerksicherheitsgruppe auf *myNsg* und der Name der RDP-Regel auf *myRdpRule* festgelegt.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Weitere Informationen zu Endpunkten und Netzwerksicherheitsgruppen finden Sie unter [Öffnen von Ports für einen virtuellen Computer in Azure mithilfe von PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Erstellen einer öffentlichen IP-Adresse und einer NIC
Sie benötigen eine [öffentliche IP-Adresse](../../virtual-network/public-ip-addresses.md) und eine Netzwerkschnittstelle, um die Kommunikation mit dem virtuellen Computer im virtuellen Netzwerk zu ermöglichen.

1. Erstellen Sie die öffentliche IP-Adresse. In diesem Beispiel ist der Name der öffentlichen IP-Adresse auf *myIP* festgelegt.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Erstellen der NIC In diesem Beispiel ist der Name der NIC auf *myNicName* festgelegt.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Festlegen von Name und Größe des virtuellen Computers

In diesem Beispiel wird der VM-Name auf *myVM* und die VM-Größe auf *Standard_A2* festgelegt.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Hinzufügen der Netzwerkkarte
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Hinzufügen des Betriebssystemdatenträgers 

Fügen Sie der Konfiguration mit [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) den Betriebssystemdatenträger hinzu. In diesem Beispiel wird die Größe des Datenträgers auf *128 GB* festgelegt und der verwaltete Datenträger als *Windows*-Betriebssystem-Datenträger angefügt.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Fertigstellen der VM 

Erstellen Sie die VM über [New-AzVM](/powershell/module/az.compute/new-azvm) mithilfe der soeben erstellten Konfigurationen.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Bei erfolgreicher Ausführung des Befehls wird eine Ausgabe wie diese angezeigt:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Stellen Sie sicher, dass der virtuelle Computer erstellt wurde
Der neu erstellte virtuelle Computer sollte nun im [Azure-Portal](https://portal.azure.com) unter **Durchsuchen** > **Virtuelle Computer** oder über die folgenden PowerShell-Befehlen angezeigt werden.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Nächste Schritte
Melden Sie sich bei Ihrem neuen virtuellen Computer an. Anweisungen dazu finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden bei diesem Computer](connect-logon.md).