---
title: Erstellen einer verwalteten Azure-VM aus einer generalisierten lokalen VHD | Microsoft-Dokumentation
description: Laden Sie eine generalisierte VHD in Azure hoch, und erstellen Sie damit neue VMs im Resource Manager-Bereitstellungsmodell.
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
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: ee2fe91d915faf7e09dee004891edfc6bef38d6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685412"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Hochladen einer generalisierten VHD und Verwendung dieser zum Erstellen neuer VMs in Azure

In diesem Artikel wird erläutert, wie Sie mithilfe von PowerShell eine VHD eines generalisierten virtuellen Computers in Azure hochladen, ein Image anhand der VHD erstellen und einen neuen virtuellen Computer anhand dieses Images erstellen. Sie können eine VHD hochladen, die aus einem lokalen Virtualisierungstool oder aus einer anderen Cloud exportiert wurde. Durch Verwenden von [Managed Disks](managed-disks-overview.md) für den neuen virtuellen Computer wird die VM-Verwaltung vereinfacht. Außerdem steigt die Verfügbarkeit, wenn der virtuelle Computer in einer Verfügbarkeitsgruppe platziert wird. 

Ein Beispielskript finden Sie unter [Beispielskript zum Hochladen einer generalisierten virtuellen Festplatte nach Azure und Erstellen eines neuen virtuellen Computers](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Voraussetzungen

- Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Lesen Sie [Planen der Migration zu Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) vor dem Starten der Migration zu [Managed Disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalisieren des virtuellen Quellcomputers mithilfe von Sysprep

Sysprep entfernt unter anderem alle persönlichen Kontoinformationen, und bereitet den Computer darauf vor, als Image verwendet zu werden. Ausführliche Informationen zu Sysprep finden Sie unter [Sysprep (Systemvorbereitung) – Übersicht](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Stellen Sie sicher, dass die auf dem Computer ausgeführten Serverrollen von Sysprep unterstützt werden. Weitere Informationen finden Sie unter [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)(Sysprep-Unterstützung für Serverrollen).

> [!IMPORTANT]
> Wenn Sie Sysprep vor dem Hochladen der VHD in Azure zum ersten Mal ausführen möchten, stellen Sie sicher, dass Sie [Ihren virtuellen Computer vorbereitet](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) haben. 
> 
> 

1. Melden Sie sich bei dem virtuellen Windows-Computer an.
2. Öffnen Sie das Eingabeaufforderungsfenster als Administrator. Wechseln Sie in das Verzeichnis „%windir%\system32\sysprep“, und führen Sie anschließend `sysprep.exe` aus.
3. Wählen Sie im Dialogfeld **Systemvorbereitungsprogramm** die Option **Out-of-Box-Experience (OOBE) für System aktivieren** aus, und vergewissern Sie sich, dass das Kontrollkästchen **Verallgemeinern** aktiviert ist.
4. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren** aus.
5. Klicken Sie auf **OK**.
   
    ![Starten von Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Nach Abschluss von Sysprep wird der virtuelle Computer heruntergefahren. Starten Sie den virtuellen Computer nicht neu.


## <a name="get-a-storage-account"></a>Erhalten eines Speicherkontos

Sie benötigen ein Speicherkonto in Azure, in dem das hochgeladene VM-Image gespeichert wird. Sie können ein vorhandenes Speicherkonto auswählen oder ein neues erstellen. 

Wenn Sie die VHD zum Erstellen eines verwalteten Datenträgers für einen virtuellen Computer verwenden, muss der Speicherort des Speicherkontos dem Speicherort entsprechen, an dem Sie den virtuellen Computer erstellen.

Geben Sie zum Anzeigen der verfügbaren Speicherkonten Folgendes ein:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Hochladen der VHD in Ihr Speicherkonto

Verwenden Sie das Cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), um die VHD in einen Container in Ihrem Speicherkonto hochzuladen. In diesem Beispiel wird die Datei *myVHD.vhd* aus *C:\Users\Public\Documents\Virtual hard disks\\* in das Speicherkonto *mystorageaccount* in der Ressourcengruppe *myResourceGroup* hochgeladen. Die Datei wird im Container *mycontainer* abgelegt. Der neue Dateiname lautet *myUploadedVHD.vhd*.

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

### <a name="other-options-for-uploading-a-vhd"></a>Weitere Optionen zum Hochladen einer virtuellen Festplatte
 
Sie können eine VHD zudem mit den folgenden Tools in ein Speicherkonto hochladen:

- [AzCopy](https://aka.ms/downloadazcopy)
- [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Storage-Explorer: Hochladen von Blobs](https://azurestorageexplorer.codeplex.com/)
- [Speicher-Import/Export Service REST-API-Referenz](https://msdn.microsoft.com/library/dn529096.aspx)
-   Wir empfehlen das Verwenden des Import/Export-Diensts, wenn die geschätzte Hochladedauer sieben Tage überschreitet. Sie können mithilfe von [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) die Dauer anhand der Datengröße und Übertragungseinheit schätzen. 
    Der Import/Export-Dienst kann zum Kopieren in oder aus dem Standardspeicherkonto verwendet werden. Sie benötigen ein Tool wie AzCopy zum Kopieren zwischen dem Storage Standard- und dem Storage Premium-Konto.

> [!IMPORTANT]
> Wenn Sie Ihre VHD mithilfe von AzCopy in Azure hochladen, stellen Sie vor dem Ausführen des Uploadskripts sicher, dass [ **/BlobType:page**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) festgelegt ist. Wenn das Ziel ein Blob ist und diese Option nicht angegeben wurde, erstellt AzCopy standardmäßig ein Blockblob.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Erstellen eines verwalteten Images anhand der hochgeladenen VHD 

Erstellen Sie ein verwaltetes Image anhand Ihrer generalisierten Betriebssystem-VHD. Ersetzen Sie die folgenden Werte durch Ihre eigenen Informationen.


Legen Sie zuerst einige Parameter fest:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Erstellen Sie das Image mithilfe Ihrer generalisierten Betriebssystem-VHD.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Nachdem Sie ein Image erstellt haben, können Sie anhand des Images eine oder mehrere neue VMs erstellen. In diesem Beispiel wird ein virtueller Computer namens *myVM* anhand von *myImage* in *myResourceGroup* erstellt.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Nächste Schritte

Melden Sie sich bei Ihrem neuen virtuellen Computer an. Anweisungen dazu finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden bei diesem Computer](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

