---
title: Hochladen einer VHD in Azure oder regionsübergreifendes Kopieren eines Datenträgers – Azure PowerShell
description: Hier erfahren Sie, wie Sie per Azure PowerShell und direktem Upload eine VHD in einen verwalteten Azure-Datenträger hochladen und den verwalteten Datenträger regionsübergreifend kopieren.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: c766c78705a1c1e40a9385360d35ac06a3db3a5d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252236"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Hochladen einer VHD in Azure oder Kopieren eines verwalteten Datenträgers in eine andere Region – Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Laden Sie die neueste [Version von AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy) herunter.
- [Installieren Sie das Azure PowerShell-Modul](/powershell/azure/install-Az-ps).
- Wenn Sie beabsichtigen, eine VHD aus der lokalen Umgebung hochzuladen: Eine VHD fester Größe, die [für Azure vorbereitet wurde](prepare-for-upload-vhd-image.md) und lokal gespeichert ist.
- Oder ein verwalteter Datenträger in Azure, wenn Sie eine Kopieraktion ausführen möchten.

## <a name="getting-started"></a>Erste Schritte

Wenn Sie es vorziehen, Datenträger über eine grafische Benutzeroberfläche hochzuladen, können Sie dazu Azure Storage-Explorer verwenden. Ausführliche Informationen finden Sie unter: [Verwenden von Azure Storage-Explorer zum Verwalten von verwalteten Azure-Datenträgern](../disks-use-storage-explorer-managed-disks.md)

Wenn Sie Ihre VHD in Azure hochladen möchten, müssen Sie einen leeren verwalteten Datenträger erstellen, der für diesen Hochladevorgang konfiguriert ist. Bevor Sie einen solchen Datenträger erstellen, sollten Sie einige zusätzliche Informationen über diese Datenträger wissen.

Diese Art des verwalteten Datenträgers verfügt über zwei eindeutige Zustände:

- „ReadyToUpload“: Der Datenträger ist für den Upload bereit, jedoch wurde keine [SAS (Shared Access Signature)](../../storage/common/storage-sas-overview.md) generiert.
- „ActiveUpload“: Der Datenträger ist für den Upload bereit, und die SAS wurde generiert.

> [!NOTE]
> Für den verwalteten Datenträger fallen in beiden Zuständen Kosten an, die sich aus den [Preisen für HDD Standard-Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/) ergeben. Welcher Art von Datenträger verwendet wird, spielt keine Rolle. Das bedeutet, dass beispielsweise der Abrechnungsvorgang für P10 und S10 identisch ist. Dies ist solange der Fall, bis `revoke-access` für den verwalteten Datenträger aufgerufen wird. Dieser Vorgang ist erforderlich, um den Datenträger an eine VM anzufügen.

## <a name="create-an-empty-managed-disk"></a>Erstellen eines leeren verwalteten Datenträgers

Bevor Sie eine leere HDD Standard zum Hochladen erstellen können, benötigen Sie die Dateigröße der VHD in Byte, die Sie hochladen möchten. Der Beispielcode ruft diese Größe für Sie ab. Wenn Sie die Größe manuell abrufen möchten, können Sie Folgendes verwenden: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Dieser Wert wird verwendet, wenn der Parameter **-UploadSizeInBytes** angegeben wird.

Erstellen Sie nun für das Hochladen (Upload) einen leeren HDD Standard-Datenträger. Führen Sie dazu in Ihrer lokalen Shell das [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig)-Cmdlet aus, für das Sie die **Upload**-Einstellung im **-CreateOption**-Parameter und den **-UploadSizeInBytes**-Parameter angeben. Rufen Sie dann [New-AzDisk](/powershell/module/az.compute/new-azdisk) auf, um den Datenträger zu erstellen.

Ersetzen Sie `<yourdiskname>`, `<yourresourcegroupname>` und `<yourregion>`, und führen Sie dann die folgenden Befehle aus:

> [!TIP]
> Wenn Sie einen Betriebssystemdatenträger erstellen, fügen Sie `-HyperVGeneration '<yourGeneration>'` in `New-AzDiskConfig` hinzu.

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Wenn Sie für den Upload einen SSD Premium- oder SSD Standard-Datenträger verwenden möchten, ersetzen Sie **Standard_LRS** durch **Premium_LRS** oder **StandardSSD_LRS**. Ultra Disks werden noch nicht unterstützt.

Nachdem Sie einen leeren verwalteten Datenträger erstellt haben, der für den Hochladevorgang konfiguriert ist, können Sie jetzt eine VHD auf diesen Datenträger hochladen. Um eine VHD auf den Datenträger hochzuladen, benötigen Sie eine beschreibbare SAS, mit der Sie auf den Datenträger als Hochladeziel verweisen können.

Ersetzen Sie `<yourdiskname>` und `<yourresourcegroupname>`, und verwenden Sie dann die folgenden Befehle, um eine beschreibbare SAS für Ihren leeren verwalteten Datenträger zu generieren:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Hochladen einer VHD-Datei

Sie verfügen nun über eine SAS für Ihren leeren verwalteten Datenträger und können diese verwenden, um diesen Datenträger als Ziel für den Uploadbefehl anzugeben.

Verwenden Sie AzCopy v10, um die lokale VHD-Datei auf einen verwalteten Datenträger hochzuladen. Dabei müssen Sie den generierten SAS-URI angeben.

Der Durchsatz für diesen Upload entspricht dem des gleichwertigen [HDD Standard-Datenträgers](../disks-types.md#standard-hdd). Wenn Sie beispielsweise als Größe S4 verwenden, beträgt der Durchsatz bis zu 60 MiB/s. Verwenden Sie hingegen als Größe S70, beträgt der Durchsatz bis zu 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Nachdem der Upload abgeschlossen wurde und Sie keine Daten mehr auf den Datenträger schreiben müssen, widerrufen Sie die SAS. Dadurch ändert sich der Zustand des verwalteten Datenträgers, und Sie können den Datenträger an eine VM anfügen.

Ersetzen Sie `<yourdiskname>` und `<yourresourcegroupname>`, und führen Sie dann die folgenden Befehle aus:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopieren eines verwalteten Datenträgers

Der direkte Upload vereinfacht auch das Kopieren eines verwalteten Datenträgers. Sie können entweder innerhalb derselben Region kopieren oder den verwalteten Datenträger in eine andere Region kopieren.

Das folgende Skript führt dies für Sie aus. Der Vorgang ähnelt mit einigen Unterschieden den zuvor beschriebenen Schritten, da Sie nun mit einem vorhandenen Datenträger arbeiten.

> [!IMPORTANT]
> Sie müssen ein Offset von 512 hinzufügen, wenn Sie die Datenträgergröße eines verwalteten Datenträgers aus Azure in Byte bereitstellen. Dies liegt daran, dass Azure die Fußzeile beim Zurückgeben der Datenträgergröße auslässt. Der Kopiervorgang schlägt fehl, wenn Sie nicht so vorgehen. Das folgende Skript fügt dieses Offset bereits für Sie ein.

Ersetzen Sie `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` und `<yourTargetLocationHere>` (ein Beispiel für einen Standortwert ist „uswest2“) durch Ihre Werte, und führen Sie dann das folgende Skript aus, um einen verwalteten Datenträger zu kopieren.

> [!TIP]
> Wenn Sie einen Betriebssystemdatenträger erstellen, fügen Sie „-HyperVGeneration '<yourGeneration>'“ zu `New-AzDiskConfig` hinzu.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfolgreich eine VHD auf den verwalteten Datenträger hochgeladen und können Datenträger nun an eine VM anfügen, um ihn zu verwenden.

Informationen dazu, wie Sie einen Datenträger für Daten an eine VM anfügen, finden Sie in folgendem Artikel: [Attach a data disk to a Windows VM with PowerShell (Hinzufügen eines Datenträgers zu einer Windows-VM mit PowerShell)](attach-disk-ps.md). Informationen zum Verwenden des Datenträgers als Betriebssystemdatenträger finden Sie unter [Erstellen einer Windows-VM von einem speziellen Datenträger](create-vm-specialized.md#create-the-new-vm).
