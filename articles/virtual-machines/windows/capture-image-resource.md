---
title: Erstellen eines verwalteten Images in Azure | Microsoft-Dokumentation
description: Erstellen Sie ein verwaltetes Image eines generalisierten virtuellen Computers oder einer VHD in Azure. Mit Images können mehrere virtuelle Computer erstellt werden, die verwaltete Datenträger verwenden.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 3a6781387121a691c6599ffaeb5722ecc6e16132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704694"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure

Eine verwaltete Imageressource kann aus einem generalisierten virtuellen Computer erstellt werden, der entweder als verwalteter Datenträger oder als nicht verwalteter Datenträger in einem Speicherkonto gespeichert ist. Mit diesem Image können dann mehrere virtuelle Computer erstellt werden. Weitere Informationen dazu, wie verwaltete Images abgerechnet werden, finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisieren der Windows-VM mithilfe von Sysprep

Sysprep entfernt alle persönlichen Konto- und Sicherheitsinformationen und bereitet den Computer darauf vor, als Image verwendet zu werden. Ausführliche Informationen zu Sysprep finden Sie unter [Sysprep (System Preparation) Overview](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Sysprep (Systemvorbereitung): Übersicht).

Stellen Sie sicher, dass die auf dem Computer ausgeführten Serverrollen von Sysprep unterstützt werden. Weitere Informationen finden Sie unter [Sysprep Support for Server Roles](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) (Sysprep-Unterstützung für Serverrollen).

> [!IMPORTANT]
> Nachdem Sie Sysprep auf einem virtuellen Computer ausgeführt haben, gilt dieser als *generalisiert* und kann nicht neu gestartet werden. Der Generalisierungsprozess eines virtuellen Computers kann nicht rückgängig gemacht werden. Wenn Sie die ursprüngliche Funktionsweise des virtuellen Computers beibehalten müssen, erstellen Sie eine [Kopie des virtuellen Computers](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) und generalisieren diese Kopie. 
>
> Wenn Sie Sysprep vor dem Hochladen der virtuellen Festplatte in Azure zum ersten Mal ausführen möchten, stellen Sie sicher, dass Sie [Ihren virtuellen Computer vorbereitet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) haben.  
> 
> 

Führen Sie zum Generalisieren Ihres virtuellen Windows-Computers die folgenden Schritte aus:

1. Melden Sie sich beim virtuellen Windows-Computer an.
   
2. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator. Wechseln Sie in das Verzeichnis „%windir%\system32\sysprep“, und führen Sie anschließend `sysprep.exe` aus.
   
3. Wählen Sie im Dialogfeld **Systemvorbereitungsprogramm** die Option **Out-of-Box-Experience (OOBE) für System aktivieren**, und aktivieren Sie das Kontrollkästchen **Verallgemeinern**.
   
4. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren** aus.
   
5. Klicken Sie auf **OK**.
   
    ![Starten von Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Nach Abschluss von Sysprep wird der virtuelle Computer heruntergefahren. Starten Sie den virtuellen Computer nicht neu.


## <a name="create-a-managed-image-in-the-portal"></a>Erstellen eines verwalteten Images im Portal 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie im Menü auf der linken Seite **Virtuelle Computer** aus, und wählen Sie dann den virtuellen Computer aus der Liste aus.

3. Wählen Sie auf der Seite **Virtueller Computer** des virtuellen Computers im oberen Menü die Option **Erfassen** aus.

   Die Seite **Image erstellen** wird angezeigt.

4. Übernehmen Sie unter **Name** entweder den bereits angegebenen Wert, oder geben Sie einen Namen ein, der für das Image verwendet werden soll.

5. Wählen Sie unter **Ressourcengruppe** entweder **Neu erstellen** aus, und geben Sie einen Namen ein, oder wählen Sie die Option **Vorhandene verwenden** und dann aus der Dropdownliste eine Ressourcengruppe aus.

6. Wenn Sie die Quell-VM nach der Erstellung des Image löschen möchten, wählen Sie **Diesen virtuellen Computer nach dem Erstellen des Images automatisch löschen** aus.

7. Wenn es möglich sein soll, das Image in jeder [Verfügbarkeitszone](../../availability-zones/az-overview.md) zu verwenden, wählen Sie für **Zonenresilienz** die Option **Ein**.

8. Wählen Sie **Erstellen** aus, um das Image zu erstellen.

9. Nachdem das Image erstellt wurde, wird es in der Ressourcengruppe in der Liste der Ressourcen als **Image** angezeigt.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Erstellen eines Image einer VM mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Durch Erstellen eines Images direkt von einem virtuellen Computer lässt sich sicherstellen, dass das Image alle Datenträger umfasst, die dem virtuellen Computer zugeordnet sind, einschließlich des Betriebssystemdatenträgers und aller Datenträger für Daten. In diesem Beispiel wird gezeigt, wie ein verwaltetes Image von einer VM mit verwalteten Datenträgern erstellt wird.

Stellen Sie vor Beginn sicher, dass Sie die neueste Version des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` in PowerShell aus, um die entsprechende Version zu ermitteln. Informationen zum Ausführen eines Upgrades finden Sie unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](/powershell/azure/install-az-ps). Wenn Sie PowerShell lokal ausführen, führen Sie `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.


> [!NOTE]
> Wenn Sie das Image in einem zonenredundanten Speicher speichern möchten, müssen Sie es in einer Region erstellen, die [Verfügbarkeitszonen](../../availability-zones/az-overview.md) unterstützt, und den `-ZoneResilient`-Parameter in die Imagekonfiguration einbeziehen (Befehl `New-AzImageConfig`).

Führen Sie die folgenden Schritte aus, um ein VM-Image zu erstellen:

1. Erstellen Sie einige Variablen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Stellen Sie sicher, dass die Zuordnung des virtuellen Computers aufgehoben wurde.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Legen Sie den Status des virtuellen Computers auf **Generalisiert**fest. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Rufen Sie den virtuellen Computer ab. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Erstellen Sie die Imagekonfiguration.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Erstellen Sie das Image.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Erstellen eines Image von einem verwalteten Datenträger mithilfe von PowerShell

Wenn Sie nur ein Image des Betriebssystemdatenträgers erstellen möchten, geben Sie die ID des verwalteten Datenträger als Betriebssystemdatenträger an:

    
1. Erstellen Sie einige Variablen. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Rufen Sie die VM ab.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Rufen Sie die ID des verwalteten Datenträgers ab.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Erstellen Sie die Imagekonfiguration.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Erstellen Sie das Image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Erstellen eines Image von einer Momentaufnahme mithilfe von PowerShell

Sie können ein verwaltetes Image aus einer Momentaufnahme eines generalisierten virtuellen Computers erstellen. Führen Sie dazu die folgenden Schritte aus:

    
1. Erstellen Sie einige Variablen. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Rufen Sie die Momentaufnahme ab.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Erstellen Sie die Imagekonfiguration.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Erstellen Sie das Image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Erstellen eines Images aus einer VHD in einem Speicherkonto

Erstellen Sie ein verwaltetes Image aus einer generalisierten Betriebssystem-VHD in einem Speicherkonto. Sie benötigen den URI der VHD im Speicherkonto mit dem Format „https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*“. In diesem Beispiel befindet sich die VHD unter *mystorageaccount* in einem Container namens *vhdcontainer*, und der VHD-Dateiname lautet *vhdfilename.vhd*.


1.  Erstellen Sie einige Variablen.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Beenden Sie den virtuellen Computer, oder heben Sie seine Zuordnung auf.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Kennzeichnen Sie den virtuellen Computer als generalisiert.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Erstellen Sie das Image mithilfe Ihrer generalisierten Betriebssystem-VHD.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines virtuellen Computers aus einem verwalteten Image](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)    

