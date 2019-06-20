---
title: Erstellen einer VM auf der Grundlage eines spezialisierten Datenträgers in Azure | Microsoft-Dokumentation
description: Erstellen Sie einen neuen virtuellen Computer, indem Sie im Resource Manager-Bereitstellungsmodell einen spezialisierten nicht verwalteten Datenträger anfügen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: f2110a749c41f59b11a6d400faa2e42e751305fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693814"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Erstellen eines virtuellen Computers auf der Grundlage einer spezialisierten VHD in einem Speicherkonto

Erstellen Sie einen neuen virtuellen Computer, indem Sie mit PowerShell einen spezialisierten nicht verwalteten Datenträger als Betriebssystem-Datenträger anfügen. Ein spezialisierter Datenträger ist eine Kopie einer VHD eines bereits vorhandenen virtuellen Computers, bei der die Benutzerkonten, Anwendungen und andere Statusdaten Ihres ursprünglichen virtuellen Computers erhalten bleiben. 

Sie haben zwei Möglichkeiten:
* [Hochladen einer VHD-Datei](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopieren der VHD eines vorhandenen virtuellen Azure-Computers](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Option 1: Hochladen einer speziellen VHD

Sie können die VHD über eine spezielle VM hochladen, die mit einem lokalen Virtualisierungstool (z.B. Hyper-V) erstellt wurde, oder über eine VM, die aus einer anderen Cloud exportiert wurde.

### <a name="prepare-the-vm"></a>Vorbereiten des virtuellen Computers
Sie können eine spezialisierte VHD hochladen, die mit einem lokalen virtuellen Computer erstellt wurde, oder eine VHD, die aus einer anderen Cloud exportiert wurde. Auf einer spezialisierten VHD werden die Benutzerkonten, Anwendungen und andere Zustandsdaten Ihres ursprünglichen virtuellen Computers beibehalten. Wenn Sie die VHD unverändert zum Erstellen eines neuen virtuellen Computers verwenden möchten, führen Sie auf jeden Fall die folgenden Schritte aus. 
  
  * [Vorbereiten einer Windows-VHD für das Hochladen in Azure.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Generalisieren Sie den virtuellen Computer **nicht** mithilfe von Sysprep.
  * Entfernen Sie alle auf dem virtuellen Computer installierten Gast-Virtualisierungstools und Agents (d.h. VMware-Tools).
  * Stellen Sie sicher, dass der virtuelle Computer so konfiguriert ist, dass die zugehörigen IP-Adressen und DNS-Einstellungen per Pullvorgang über DHCP übertragen werden. Dadurch wird sichergestellt, dass der Server beim Starten eine IP-Adresse innerhalb des VNET bezieht. 


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
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Hochladen der VHD in Ihr Speicherkonto
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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Option 2: Kopieren der VHD eines vorhandenen virtuellen Azure-Computers

Sie können eine VHD in ein anderes Speicherkonto kopieren, das beim Erstellen eines neuen VM-Duplikats verwendet werden soll.

### <a name="before-you-begin"></a>Voraussetzungen
Stellen Sie sicher, dass Sie:

* über Informationen zu den **Quell- und Zielspeicherkonten** verfügen. Für die Quell-VM benötigen Sie den Speicherkonto- und den Containernamen. Der Containername ist üblicherweise **vhds**. Außerdem müssen Sie über ein Zielspeicherkonto verfügen. Wenn Sie nicht bereits über ein Zielspeicherkonto verfügen, können Sie es über das Portal (**Alle Dienste** > „Speicherkonten“ > „Hinzufügen“) oder mithilfe des Cmdlets [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) erstellen. 
* das [AzCopy-Tool](../../storage/common/storage-use-azcopy.md) heruntergeladen und installiert haben. 

### <a name="deallocate-the-vm"></a>Aufheben der Zuordnung der VM
Heben Sie die Zuordnung der VM auf, wodurch die VHD zum Kopieren freigegeben wird. 

* **Portal**: Klicken Sie auf **Virtuelle Computer** > **myVM** > „Beenden“
* **Powershell**: Verwenden Sie [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm), um die Zuordnung des virtuellen Computers **myVM** in der Ressourcengruppe **myResourceGroup** aufzuheben.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

Der **Status** der VM im Azure-Portal ändert sich von **Beendet** in **Beendet (Zuordnung aufgehoben)** .

### <a name="get-the-storage-account-urls"></a>Abrufen der Speicherkonto-URLs
Sie benötigen die URLs der Quell- und Zielspeicherkonten. Die URLs sehen wie folgt aus: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Wenn Sie die Namen des Speicherkontos und des Containers bereits kennen, können Sie einfach die Informationen in Klammern ersetzen, um Ihre URL zu erstellen. 

Sie können das Azure-Portal oder Azure PowerShell verwenden, um die URL abzurufen:

* **Portal**: Klicken Sie auf **>** für **Alle Dienste** > **Speicherkonten** > *Speicherkonto* > **Blobs**. Ihre VHD-Quelldatei befindet sich wahrscheinlich im Container **vhds**. Klicken Sie für den Container auf **Eigenschaften**, und kopieren Sie den Text mit der Bezeichnung **URL**. Sie benötigen die URLs des Quell- und des Zielcontainers. 
* **Powershell**: Verwenden Sie [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm), um die Informationen des virtuellen Computers **myVM** in der Ressourcengruppe **myResourceGroup** abzurufen. Suchen Sie in den Ergebnissen im Abschnitt **Speicherprofil** nach dem **VHD-URI**. Der erste Teil des URI ist die URL zum Container, und der zweite Teil ist der Betriebssystem-VHD-Name für die VM.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Abrufen der Speicherzugriffsschlüssel
Suchen Sie die Zugriffsschlüssel für die Quell- und Zielspeicherkonten. Weitere Informationen zu Zugriffsschlüsseln finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-create-storage-account.md).

* **Portal**: Klicken Sie auf **Alle Dienste** > **Speicherkonten** > *Speicherkonto* > **Zugriffsschlüssel**. Kopieren Sie den Schlüssel mit der Bezeichnung **key1**.
* **Powershell**: Verwenden Sie [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey), um den Speicherschlüssel für das Speicherkonto **mystorageaccount** in der Ressourcengruppe **myResourceGroup** abzurufen. Kopieren Sie den Schlüssel mit der Bezeichnung **key1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopieren der VHD-Datei
Sie können Dateien mithilfe von AzCopy zwischen Speicherkonten kopieren. Für den Zielcontainer wird der angegebene Container für Sie erstellt, wenn er nicht vorhanden ist. 

Um AzCopy zu verwenden, öffnen Sie auf Ihrem lokalen Computer eine Eingabeaufforderung, und navigieren Sie zum Ordner, in dem AzCopy installiert ist. Dies ist ein ähnlicher Ordner wie *C:\Programme (x86)\Microsoft SDKs\Azure\AzCopy*. 

Um alle Dateien innerhalb eines Containers zu kopieren, verwenden Sie den Schalter **/S**. Dies kann verwendet werden, um die Betriebssystem-VHD und alle Datenträger zu kopieren, wenn sie sich im gleichen Container befinden. Dieses Beispiel zeigt, wie alle Dateien aus dem Container **mysourcecontainer** im Speicherkonto **mysourcestorageaccount** in den Container **mydestinationcontainer** im Speicherkonto **mydestinationstorageaccount** kopiert werden. Ersetzen Sie die Namen der Speicherkonten und Container durch Ihre eigenen. Ersetzen Sie `<sourceStorageAccountKey1>` und `<destinationStorageAccountKey1>` durch Ihre eigenen Schlüssel.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Wenn Sie nur eine bestimmte VHD in einem Container mit mehreren Dateien kopieren möchten, können Sie den Dateinamen auch mithilfe des Schalters „/Pattern“ angeben. In diesem Beispiel wird nur die Datei mit dem Namen **myFileName.vhd** kopiert.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Wenn der Vorgang abgeschlossen ist, erhalten Sie eine Meldung ähnlich der folgenden:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Problembehandlung
* Wenn Sie AzCopy verwenden und Ihnen die Fehlermeldung „Fehler beim Authentifizieren der Anforderung durch den Server“ angezeigt wird, stellen Sie sicher, dass der Wert des Autorisierungsheaders, einschließlich der Signatur, richtig ist. Und wenn Sie Schlüssel 2 oder den sekundären Speicherschlüssel verwenden, versuchen Sie es mit dem primären oder dem ersten Speicherschlüssel.

## <a name="create-the-new-vm"></a>Erstellen des neuen virtuellen Computers 

Sie müssen Netzwerkressourcen und andere VM-Ressourcen für den neuen virtuellen Computer erstellen.

### <a name="create-the-subnet-and-vnet"></a>Erstellen des Subnetzes und des virtuellen Netzwerks

Erstellen Sie das vNet und das Subnetz des [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md).

1. Erstellen Sie das Subnetz. Dieses Beispiel erstellt ein Subnetz mit dem Namen **mySubNet** in der Ressourcengruppe **myResourceGroup** und legt das Adresspräfix **10.0.0.0/24** fest.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Erstellen Sie das virtuelle Netzwerk. Dieses Beispiel legt den Namen des virtuellen Netzwerks auf **myVnetName**, den Standort auf **USA, Westen** und das Adresspräfix für das virtuelle Netzwerk auf **10.0.0.0/16** fest. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Erstellen einer Netzwerksicherheitsgruppe und einer RDP-Regel
   Damit Sie sich über RDP bei Ihrem virtuellen Computer anmelden können, benötigen Sie eine Sicherheitsregel, die RDP-Zugriff auf Port 3389 zulässt. Da die VHD für den neuen virtuellen Computer aus einer vorhandenen spezialisierten VM erstellt wurde, können Sie nach dem Erstellen des virtuellen Computers ein vorhandenes Konto des virtuellen Quellcomputers verwenden, der über die Berechtigung zum Anmelden über RDP verfügte.
   Dieser Vorgang muss abgeschlossen werden, bevor die Netzwerkschnittstelle erstellt wird, die dem virtuellen Computer zugewiesen wird.  
   In diesem Beispiel wird der Name der Netzwerksicherheitsgruppe auf **myNsg** und der Name der RDP-Regel auf **myRdpRule** festgelegt.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Weitere Informationen zu Endpunkten und Netzwerksicherheitsgruppen finden Sie unter [Öffnen von Ports für einen virtuellen Computer in Azure mithilfe von PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Erstellen einer öffentlichen IP-Adresse und einer NIC
Sie benötigen eine [öffentliche IP-Adresse](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) und eine Netzwerkschnittstelle, um die Kommunikation mit dem virtuellen Computer im virtuellen Netzwerk zu ermöglichen.

1. Erstellen Sie die öffentliche IP-Adresse. In diesem Beispiel ist der Name der öffentlichen IP-Adresse auf **myIP** festgelegt.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Erstellen Sie die NIC. In diesem Beispiel ist der Name der NIC auf **myNicName** festgelegt. In diesem Schritt wird auch die früher erstellte Netzwerksicherheitsgruppe mit dieser NIC verknüpft.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Festlegen von Name und Größe des virtuellen Computers

In diesem Beispiel wird der Name des virtuellen Computers auf „myVM“ und ihre Größe auf „Standard_A2“ festgelegt.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Hinzufügen der Netzwerkkarte
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurieren des Betriebssystem-Datenträgers

1. Legen Sie den URI für die VHD fest, die Sie hochgeladen oder kopiert haben. In diesem Beispiel wird die VHD-Datei mit dem Namen **myOsDisk.vhd** in einem Speicherkonto namens **myStorageAccount** in einem Container mit dem Namen **myContainer** gespeichert.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Fügen Sie den Betriebssystemdatenträger hinzu. In diesem Beispiel wird beim Erstellen des Betriebssystem-Datenträgers der Name des Betriebssystem-Datenträgers durch Anfügen des Begriffs „osDisk“ an den Namen des virtuellen Computers erstellt. In diesem Beispiel wird auch angegeben, dass diese Windows-basierte VHD dem virtuellen Computer als Betriebssystem-Datenträger angefügt werden soll.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Optional: Wenn Sie über Datenträger für Daten verfügen, die dem virtuellen Computer angefügt werden müssen, fügen Sie die Datenträger für Daten mit den URLs von Daten-VHDs und den entsprechenden logischen Gerätenummern (Logical Unit Number, LUN) hinzu.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Bei Verwendung eines Speicherkontos sehen die URLs der Daten- und Betriebssystem-Datenträger wie folgt aus: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Sie finden dies im Portal, indem Sie zum Zielspeichercontainer wechseln, auf die kopierte Betriebssystem- oder Daten-VHD klicken und dann den Inhalt der URL kopieren.


### <a name="complete-the-vm"></a>Fertigstellen des virtuellen Computers 

Erstellen Sie den virtuellen Computer mithilfe der soeben erstellten Konfigurationen.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

War dieser Befehl erfolgreich, erscheint eine Ausgabe wie die folgende:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Stellen Sie sicher, dass der virtuelle Computer erstellt wurde
Der neu erstellte virtuelle Computer sollte nun im [Azure-Portal](https://portal.azure.com) unter **Alle Dienste** > **Virtuelle Computer** angezeigt werden. Alternativ dazu können Sie ihn mit den folgenden PowerShell-Befehlen anzeigen:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Nächste Schritte
Melden Sie sich bei Ihrem neuen virtuellen Computer an. Anweisungen dazu finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden bei diesem Computer](connect-logon.md).

