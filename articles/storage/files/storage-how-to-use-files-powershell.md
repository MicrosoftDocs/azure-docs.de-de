---
title: 'Schnellstart: Verwalten von Azure-Dateifreigaben mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure-Dateifreigaben mit Azure PowerShell verwalten.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e32aead791fb84415da1b00f1e979a6ac0f28155
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729025"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Schnellstart: Erstellen und Verwalten einer Azure-Dateifreigabe mit Azure PowerShell 
In dieser Anleitung werden Schritt für Schritt die Grundlagen der Verwendung von [Azure-Dateifreigaben](storage-files-introduction.md) mit PowerShell beschrieben. Azure-Dateifreigaben sind genau wie andere Dateifreigaben, werden jedoch in der Cloud gespeichert und von der Azure-Plattform unterstützt. Azure-Dateifreigaben unterstützen das SMB-Protokoll nach Industriestandard und ermöglichen es, Dateien für mehrere Computer, Anwendungen und Instanzen freizugeben. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für diese Anleitung mindestens die Version Az 0.7 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um zu ermitteln, welche Version des Azure PowerShell-Moduls Sie ausführen. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um sich bei Ihrem Azure-Konto anzumelden.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Falls Sie nicht bereits über eine Azure-Ressourcengruppe verfügen, können Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue erstellen. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region „East US“ (USA, Osten) erstellt:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen
Ein Speicherkonto ist ein gemeinsam genutzter Pool mit Speicherplatz, den Sie zum Bereitstellen von Azure-Dateifreigaben oder anderen Speicherressourcen wie Blobs oder Warteschlangen verwenden können. Ein Speicherkonto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze des Speicherkontos erreicht ist.

In diesem Beispiel wird mithilfe des Cmdlets [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein Speicherkonto erstellt. Das Speicherkonto wird mit dem Namen *mystorageaccount\<Zufallszahl>* versehen, und ein Verweis auf das Speicherkonto wird in der Variablen **$storageAcct** gespeichert. Da Speicherkontonamen eindeutig sein müssen, sollten Sie `Get-Random` verwenden, um zu diesem Zweck eine Zahl an den Namen anzufügen. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe
Jetzt können Sie Ihre erste Azure-Dateifreigabe erstellen. Für die Erstellung einer Dateifreigabe können Sie das Cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) verwenden. In diesem Beispiel wird eine Freigabe mit dem Namen `myshare` erstellt.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Freigabenamen dürfen nur Kleinbuchstaben, Zahlen und einzelne Bindestriche enthalten und dürfen nicht mit einem Bindestrich beginnen. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Verwenden Ihrer Azure-Dateifreigabe
Azure Files bietet zwei Methoden für die Arbeit mit Dateien und Ordnern in Ihrer Azure-Dateifreigabe: das [SMB-Protokoll (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) nach Industriestandard und das [REST-Protokoll „File“](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Informationen zum Einbinden einer Dateifreigabe mit SMB finden Sie im folgenden Dokument für Ihr jeweiliges Betriebssystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Verwenden einer Azure-Dateifreigabe mit dem REST-Protokoll „File“ 
Das REST-Protokoll „File“ kann direkt verwendet werden (d.h. Sie können REST-HTTP-Aufrufe selbst erstellen). Gängiger ist jedoch die Verwendung des Azure PowerShell-Moduls, der [Azure-Befehlszeilenschnittstelle](storage-how-to-use-files-cli.md) oder eines Azure Storage SDK. Bei allen diesen Methoden steht ein praktischer Wrapper um das REST-Protokoll „File“ in der Skript-/Programmiersprache Ihrer Wahl zur Verfügung.  

In den meisten Fällen werden Sie ihre Azure-Dateifreigabe über das SMB-Protokoll nutzen, da Sie so die bereits vorhandenen Anwendungen und Tools verwenden können, die sie erwarten. Es gibt jedoch auch Gründe, die für die Verwendung der REST-API „File“ sprechen:

- Sie durchsuchen Ihre Dateifreigabe per Cloud Shell in PowerShell. (In diesem Fall können Dateifreigaben nicht über SMB eingebunden werden.)
- Sie müssen ein Skript oder eine Anwendung über einen Client ausführen, der keine SMB-Freigaben einbinden kann (beispielsweise ein lokaler Client, für den die Blockierung von Port 445 nicht aufgehoben wurde).
- Sie nutzen serverlose Ressourcen (beispielsweise [Azure Functions](../../azure-functions/functions-overview.md)). 

Die folgenden Beispiele zeigen, wie Sie mit dem Azure PowerShell-Modul Ihre Azure-Dateifreigabe mit dem REST-Protokoll „File“ ändern. 

#### <a name="create-directory"></a>Erstellen eines Verzeichnisses
Verwenden Sie das Cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory), um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen *myDirectory* zu erstellen.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Hochladen einer Datei
Um das Hochladen einer Datei mit dem Cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) demonstrieren zu können, müssen wir zuerst auf Ihrem temporären Laufwerk von PowerShell Cloud Shell eine Datei erstellen, die hochgeladen werden kann. 

In diesem Beispiel werden das aktuelle Datum und die Uhrzeit in eine neue Datei auf Ihrem temporären Laufwerk eingefügt, und anschließend wird die Datei auf die Dateifreigabe hochgeladen.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Beim lokalen Ausführen von PowerShell sollten Sie `C:\Users\ContainerAdministrator\CloudDrive\` durch einen Pfad ersetzen, der auf Ihrem Computer vorhanden ist.

Nach dem Hochladen der Datei können Sie das Cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) zum Überprüfen verwenden, um sicherzustellen, dass die Datei auf Ihre Azure-Dateifreigabe hochgeladen wurde. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Herunterladen einer Datei
Sie können das Cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) verwenden, um eine Kopie der Datei herunterzuladen, die Sie gerade auf das temporäre Laufwerk von Cloud Shell hochgeladen haben.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Nach dem Herunterladen der Datei können Sie `Get-ChildItem` verwenden, um zu prüfen, ob die Datei auf das temporäre Laufwerk der Cloud Shell von PowerShell heruntergeladen wurde.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Kopieren von Dateien
Eine häufige Aufgabe besteht darin, Dateien von einer Dateifreigabe auf eine andere oder in bzw. aus einem Azure Blob-Speichercontainer zu kopieren. Zum Demonstrieren dieser Funktionalität können Sie eine neue Freigabe erstellen und die Datei, die Sie gerade hochgeladen haben, mit dem Cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) auf diese neue Freigabe kopieren. 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Wenn Sie die Dateien auf der neuen Freigabe auflisten, sollte die kopierte Datei angezeigt werden.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Das `Start-AzStorageFileCopy`-Cmdlet eignet sich zwar gut für das Verschieben von Ad-hoc-Dateien zwischen Azure-Dateifreigaben und Azure Blob Storage-Containern, aber für umfangreichere Verschiebungen (in Bezug auf die Anzahl oder Größe von Dateien) empfehlen wir die Verwendung von AzCopy. Informieren Sie sich über [AzCopy für Windows](../common/storage-use-azcopy.md) und [AzCopy für Linux](../common/storage-use-azcopy-linux.md). AzCopy muss lokal installiert werden und ist in Cloud Shell nicht verfügbar. 

## <a name="create-and-manage-share-snapshots"></a>Erstellen und Verwalten von Freigabemomentaufnahmen
Eine weitere nützliche Aufgabe, die Sie mit einer Azure-Dateifreigabe durchführen können, ist die Erstellung von Freigabemomentaufnahmen. Mit einer Momentaufnahme wird für eine Azure-Dateifreigabe ein bestimmter Zeitpunkt beibehalten. Freigabemomentaufnahmen ähneln Betriebssystemtechnologien, mit denen Sie unter Umständen bereits vertraut sind:
- [Volumeschattenkopie-Dienst (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) für Windows-Dateisysteme wie NTFS und ReFS
- Momentaufnahmen vom Typ [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) für Linux-Systeme
- Momentaufnahmen vom Typ [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) für macOS 
 Sie können eine Freigabemomentaufnahme für eine Freigabe erstellen, indem Sie die `Snapshot`-Methode im PowerShell-Objekt für eine Dateifreigabe verwenden, die mit dem Cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) abgerufen wird. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Durchsuchen von Freigabemomentaufnahmen
Sie können den Inhalt der Freigabemomentaufnahme durchsuchen, indem Sie den Momentaufnahmenverweis (`$snapshot`) an den Parameter `-Share` des `Get-AzStorageFile`-Cmdlets übergeben.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Auflisten von Freigabemomentaufnahmen
Sie können die Liste mit den Momentaufnahmen, die Sie für Ihre Freigabe erstellt haben, mit dem folgenden Befehl anzeigen.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Wiederherstellen von einer Freigabemomentaufnahme
Sie können eine Datei wiederherstellen, indem Sie den Befehl `Start-AzStorageFileCopy` verwenden, den wir bereits genutzt haben. In dieser Schnellstartanleitung löschen wir zuerst unsere Datei `SampleUpload.txt`, die wir hochgeladen haben, damit wir sie aus der Momentaufnahme wiederherstellen können.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Löschen einer Freigabemomentaufnahme
Sie können eine Freigabemomentaufnahme löschen, indem Sie das Cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) mit der Variablen verwenden, die den Verweis `$snapshot` auf den Parameter `-Share` enthält.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie fertig sind, können Sie die Ressourcengruppe und alle zugehörigen Ressourcen mit dem Cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Alternativ hierzu können Sie Ressourcen einzeln entfernen:

- Zum Entfernen der Azure-Dateifreigaben, die wir für diese Schnellstartanleitung erstellt haben

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Zum Entfernen des eigentlichen Speicherkontos (Es werden implizit die von uns erstellten Azure-Dateifreigaben sowie alle anderen Speicherressourcen entfernt, die Sie ggf. erstellt haben, z.B. ein Azure Blob Storage-Container.)

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist Azure Files?](storage-files-introduction.md)
