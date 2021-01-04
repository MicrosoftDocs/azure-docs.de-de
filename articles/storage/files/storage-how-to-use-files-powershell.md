---
title: 'Schnellstart: Verwalten von Azure-Dateifreigaben mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure-Dateifreigaben mit Azure PowerShell verwalten.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626910"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Schnellstart: Erstellen und Verwalten einer Azure-Dateifreigabe mit Azure PowerShell 
In dieser Anleitung werden Schritt für Schritt die Grundlagen der Verwendung von [Azure-Dateifreigaben](storage-files-introduction.md) mit PowerShell beschrieben. Azure-Dateifreigaben sind genau wie andere Dateifreigaben, werden jedoch in der Cloud gespeichert und von der Azure-Plattform unterstützt. Azure-Dateifreigaben unterstützen das SMB-Protokoll (Server Message Block) nach Industriestandard sowie das NFS-Protokoll (Network File System, Vorschau) und ermöglichen es, Dateien für mehrere Computer, Anwendungen und Instanzen freizugeben. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für diese Anleitung mindestens die Version Az 0.7 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um zu ermitteln, welche Version des Azure PowerShell-Moduls Sie ausführen. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um sich bei Ihrem Azure-Konto anzumelden.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Falls Sie nicht bereits über eine Azure-Ressourcengruppe verfügen, können Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue erstellen. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region „USA, Westen 2“ erstellt:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen
Bei einem Speicherkonto handelt es sich um einen gemeinsam genutzten Pool mit Speicherplatz, den Sie zum Bereitstellen von Azure-Dateifreigaben verwenden können. Ein Speicherkonto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze des Speicherkontos erreicht ist. In diesem Beispiel wird ein Speicherkonto vom Typ „Allgemein v2“ (General Purpose version 2, GPv2) erstellt, in dem Standard-Azure-Dateifreigaben sowie andere Speicherressourcen wie Blobs oder Warteschlangen auf einem Festplattenlaufwerk (Hard-Disk Drive, HDD) gespeichert werden können. Azure Files unterstützt auch SSDs (Solid State Drives). Premium-Azure-Dateifreigaben können in FileStorage-Speicherkonten erstellt werden.

In diesem Beispiel wird mithilfe des Cmdlets [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein Speicherkonto erstellt. Das Speicherkonto wird mit dem Namen *mystorageaccount\<random number>* versehen, und ein Verweis auf das Speicherkonto wird in der Variablen **$storageAcct** gespeichert. Da Speicherkontonamen eindeutig sein müssen, sollten Sie `Get-Random` verwenden, um zu diesem Zweck eine Zahl an den Namen anzufügen. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Freigaben mit einer Größe von mehr als 5 TiB (bis maximal 100 TiB pro Freigabe) sind nur in Speicherkonten mit lokal redundantem Speicher (LRS) und zonenredundantem Speicher (ZRS) verfügbar. Wenn Sie ein Konto mit georedundantem Speicher (GRS) oder geozonenredundantem Speicher (GZRS) erstellen möchten, entfernen Sie den Parameter `-EnableLargeFileShare`.

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe
Jetzt können Sie Ihre erste Azure-Dateifreigabe erstellen. Für die Erstellung einer Dateifreigabe können Sie das Cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) verwenden. In diesem Beispiel wird eine Freigabe mit dem Namen `myshare` erstellt.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

Freigabenamen dürfen nur Kleinbuchstaben, Zahlen und einzelne Bindestriche enthalten und dürfen nicht mit einem Bindestrich beginnen. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Verwenden Ihrer Azure-Dateifreigabe
Azure Files bietet zwei Methoden für die Arbeit mit Dateien und Ordnern in Ihrer Azure-Dateifreigabe: das [SMB-Protokoll (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) nach Industriestandard und das [REST-Protokoll „File“](/rest/api/storageservices/file-service-rest-api). 

Informationen zum Einbinden einer Dateifreigabe mit SMB finden Sie im folgenden Dokument für Ihr jeweiliges Betriebssystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Verwenden einer Azure-Dateifreigabe mit dem REST-Protokoll „File“ 
Das REST-Protokoll „File“ kann direkt verwendet werden (d. h. Sie können REST-HTTP-Aufrufe selbst erstellen). Gängiger ist jedoch die Verwendung des Azure PowerShell-Moduls, der [Azure-Befehlszeilenschnittstelle](storage-how-to-use-files-cli.md) oder eines Azure Storage SDK. Bei allen diesen Methoden steht ein praktischer Wrapper um das REST-Protokoll „File“ in der Skript-/Programmiersprache Ihrer Wahl zur Verfügung.  

In den meisten Fällen werden Sie ihre Azure-Dateifreigabe über das SMB-Protokoll nutzen, da Sie so die bereits vorhandenen Anwendungen und Tools verwenden können, die sie erwarten. Es gibt jedoch auch Gründe, die für die Verwendung der REST-API „File“ sprechen:

- Sie durchsuchen Ihre Dateifreigabe per Cloud Shell in PowerShell. (In diesem Fall können Dateifreigaben nicht über SMB eingebunden werden.)
- Sie nutzen serverlose Ressourcen (beispielsweise [Azure Functions](../../azure-functions/functions-overview.md)). 
- Sie erstellen einen Mehrwertdienst, der mit zahlreichen Azure-Dateifreigaben interagiert, um beispielsweise Sicherungen zu erstellen oder Antivirenüberprüfungen durchzuführen.

Die folgenden Beispiele zeigen, wie Sie mit dem Azure PowerShell-Modul Ihre Azure-Dateifreigabe mit dem REST-Protokoll „File“ ändern. Der Parameter `-Context` dient zum Abrufen des Speicherkontoschlüssels, um die angegebenen Aktionen für die Dateifreigabe auszuführen. Um den Speicherkontoschlüssel abrufen zu können, müssen Sie für das Speicherkonto über die Azure-Rolle `Owner` verfügen.

#### <a name="create-directory"></a>Erstellen eines Verzeichnisses
Verwenden Sie das Cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory), um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen *myDirectory* zu erstellen.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Hochladen einer Datei
Um das Hochladen einer Datei mit dem Cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) demonstrieren zu können, müssen wir zuerst auf Ihrem temporären Laufwerk von PowerShell Cloud Shell eine Datei erstellen, die hochgeladen werden kann. 

In diesem Beispiel werden das aktuelle Datum und die Uhrzeit in eine neue Datei auf Ihrem temporären Laufwerk eingefügt, und anschließend wird die Datei auf die Dateifreigabe hochgeladen.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Beim lokalen Ausführen von PowerShell sollten Sie `~/CloudDrive/` durch einen Pfad ersetzen, der auf Ihrem Computer vorhanden ist.

Nach dem Hochladen der Datei können Sie das Cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) zum Überprüfen verwenden, um sicherzustellen, dass die Datei auf Ihre Azure-Dateifreigabe hochgeladen wurde. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Herunterladen einer Datei
Sie können das Cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) verwenden, um eine Kopie der Datei herunterzuladen, die Sie gerade auf das temporäre Laufwerk von Cloud Shell hochgeladen haben.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Nach dem Herunterladen der Datei können Sie `Get-ChildItem` verwenden, um zu prüfen, ob die Datei auf das temporäre Laufwerk der Cloud Shell von PowerShell heruntergeladen wurde.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Kopieren von Dateien
Eine gängige Aufgabe ist das Kopieren von Dateien zwischen Dateifreigaben. Zum Demonstrieren dieser Funktionalität können Sie eine neue Freigabe erstellen und die Datei, die Sie gerade hochgeladen haben, mit dem Cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) auf diese neue Freigabe kopieren. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Wenn Sie die Dateien auf der neuen Freigabe auflisten, sollte die kopierte Datei angezeigt werden.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Das Cmdlet `Start-AzStorageFileCopy` eignet sich für Ad-hoc-Dateiverschiebungen zwischen Azure-Dateifreigaben. Für Migrationen und umfangreichere Datenverschiebungen wird die Verwendung von `robocopy` (unter Windows) bzw. `rsync` (unter macOS und Linux) empfohlen. Von `robocopy` und `rsync` wird für Datenverschiebungen nicht die FileREST-API, sondern SMB verwendet.

## <a name="create-and-manage-share-snapshots"></a>Erstellen und Verwalten von Freigabemomentaufnahmen
Eine weitere nützliche Aufgabe, die Sie mit einer Azure-Dateifreigabe durchführen können, ist die Erstellung von Freigabemomentaufnahmen. Mit einer Momentaufnahme wird für eine Azure-Dateifreigabe ein bestimmter Zeitpunkt beibehalten. Freigabemomentaufnahmen ähneln Betriebssystemtechnologien, mit denen Sie unter Umständen bereits vertraut sind:

- [Volumeschattenkopie-Dienst (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) für Windows-Dateisysteme wie NTFS und ReFS.
- Momentaufnahmen vom Typ [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) für Linux-Systeme
- Momentaufnahmen vom Typ [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) für macOS 

Sie können eine Freigabemomentaufnahme für eine Freigabe erstellen, indem Sie die `Snapshot`-Methode im PowerShell-Objekt für eine Dateifreigabe verwenden, die mit dem Cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) abgerufen wird. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Durchsuchen von Freigabemomentaufnahmen
Sie können den Inhalt der Freigabemomentaufnahme durchsuchen, indem Sie den Momentaufnahmenverweis (`$snapshot`) an den Parameter `-Share` des `Get-AzStorageFile`-Cmdlets übergeben.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Auflisten von Freigabemomentaufnahmen
Sie können die Liste mit den Momentaufnahmen, die Sie für Ihre Freigabe erstellt haben, mit dem folgenden Befehl anzeigen.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Wiederherstellen von einer Freigabemomentaufnahme
Sie können eine Datei wiederherstellen, indem Sie den Befehl `Start-AzStorageFileCopy` verwenden, den wir bereits genutzt haben. In dieser Schnellstartanleitung löschen wir zuerst unsere Datei `SampleUpload.txt`, die wir hochgeladen haben, damit wir sie aus der Momentaufnahme wiederherstellen können.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Löschen einer Freigabemomentaufnahme
Sie können eine Freigabemomentaufnahme löschen, indem Sie das Cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) mit der Variablen verwenden, die den Verweis `$snapshot` auf den Parameter `-Share` enthält.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie fertig sind, können Sie die Ressourcengruppe und alle zugehörigen Ressourcen mit dem Cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternativ hierzu können Sie Ressourcen einzeln entfernen:

- Zum Entfernen der Azure-Dateifreigaben, die wir für diese Schnellstartanleitung erstellt haben

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Vor dem Löschen der Azure-Dateifreigabe müssen alle Freigabemomentaufnahmen für die erstellten Azure-Dateifreigaben gelöscht werden.

- Zum Entfernen des eigentlichen Speicherkontos (Es werden implizit die von uns erstellten Azure-Dateifreigaben sowie alle anderen Speicherressourcen entfernt, die Sie ggf. erstellt haben, z.B. ein Azure Blob Storage-Container.)

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Was ist Azure Files?](storage-files-introduction.md)