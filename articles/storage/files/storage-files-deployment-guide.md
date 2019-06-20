---
title: Bereitstellen von Azure Files | Microsoft-Dokumentation
description: Informationen über sämtliche Schritte zum Bereitstellen von Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 416040d91da2f6346027f738de507777f804fd1f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237729"
---
# <a name="how-to-deploy-azure-files"></a>Bereitstellen von Azure Files
[Azure Files](storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll SMB zugegriffen werden kann. In diesem Artikel erfahren Sie, wie Sie Azure Files praktisch in Ihrer Organisation bereitstellen.

Es wird dringend empfohlen, vor dem Ausführen der Schritte in dieser Anleitung [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md) zu lesen.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie die folgenden Schritte bereits ausgeführt haben:

- Sie haben ein Azure Storage-Konto mit den gewünschten Resilienz- und Verschlüsselungsoptionen in der gewünschten Region erstellt. Eine Schritt-für-Schritt-Anleitung zum Erstellen eines Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- Sie haben eine Azure-Dateifreigabe mit Ihrem gewünschten Kontingent in Ihrem Speicherkonto erstellt. Eine Schritt-für-Schritt-Anleitung zum Erstellen einer Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe](storage-how-to-create-file-share.md).

## <a name="transfer-data-into-azure-files"></a>Übertragen von Daten in Azure Files
Sobald Sie eine neue Azure-Dateifreigabe erstellt haben, können Sie vorhandene Dateien, die z.B. lokal gespeichert sind, in diese übertragen. In diesem Abschnitt erfahren Sie, wie Sie Daten mithilfe verschiedener gängiger Methoden, die im [Planungshandbuch](storage-files-planning.md#data-transfer-method) ausgeführt sind, in eine Azure-Dateifreigabe übertragen.

### <a name="azure-file-sync"></a>Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Dies erfolgt durch Umwandeln der Windows-Server in einen Schnellcache der Azure-Dateifreigabe. Sie können alle unter Windows Server verfügbaren Protokolle für den lokalen Zugriff auf Ihre Daten (einschließlich SMB, NFS und FTPS) sowie beliebig viele Caches weltweit verwenden.

Sie können die Azure-Dateisynchronisierung verwenden, um Daten in eine Azure-Dateifreigabe zu übertragen, auch wenn Sie diesen Synchronisierungsmechanismus nicht langfristig einsetzen möchten. Weitere Informationen zur Verwendung der Azure-Dateisynchronisierung für die Datenübertragung in eine Azure-Dateifreigabe finden Sie unter [Planung für eine Azure-Dateisynchronisierungsbereitstellung](storage-sync-files-planning.md) und [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Mit dem Azure Import/Export-Dienst können Sie große Datenmengen auf sichere Weise in eine Azure-Dateifreigabe übertragen, indem Sie Festplattenlaufwerke an ein Azure-Rechenzentrum schicken. Einen ausführlichen Überblick über diesen Dienst finden Sie unter [Verwenden des Microsoft Azure Import/Export-Diensts zum Übertragen von Daten in Azure Storage](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!Note]  
> Der Azure Import/Export-Dienst unterstützt derzeit keinen Export von Dateien aus einer Azure-Dateifreigabe.

Mithilfe der folgenden Schritte importieren Sie Daten von einem lokalen Speicherort in Ihre Azure-Dateifreigabe.

1. Beschaffen Sie die erforderliche Anzahl von Festplatten, die Sie an Azure schicken. Die Festplatten können eine beliebige Größe aufweisen, es müssen jedoch 2,5" oder 3,5" SSDs oder HDDs sein, die den SATA II- oder SATA III-Standard unterstützen. 

2. Schließen Sie die einzelnen Festplatten für die Datenübertragung an den Server/PC an, und stellen Sie sie bereit. Um eine optimale Leistung zu erzielen, sollten Sie den lokalen Exportauftrag lokal auf dem Server ausführen, auf dem sich die Daten befinden. In bestimmten Fällen, z.B. wenn es sich bei dem Dateiserver mit den Daten um ein NAS-Gerät handelt, ist dies möglicherweise nicht möglich. In diesem Fall können Sie die Festplatten auf einem PC bereitstellen.

3. Vergewissern Sie sich, dass jedes Laufwerk online und initialisiert ist und ihm ein Laufwerkbuchstabe zugewiesen ist. Um ein Laufwerk online zu schalten, zu initialisieren und einen Laufwerkbuchstabe zuzuweisen, öffnen Sie das Datenträgerverwaltungs-MMC-Snap-In (diskmgmt.msc).

    - Um einen Datenträger online zu schalten (sofern er nicht bereits online ist), klicken Sie im unteren Bereich der Datenträgerverwaltungs-MMC mit der rechten Maustaste auf den Datenträger und wählen „Online“ aus.
    - Um einen Datenträger zu initialisieren (sobald der Datenträger online ist), klicken Sie im unteren Bereich mit der rechten Maustaste auf den Datenträger und wählen „Initialisieren“ aus. Wenn Sie dazu aufgefordert werden, müssen Sie „GPT“ auswählen.

        ![Screenshot des Menüs „Datenträgerinitialisierung“ in der Datenträgerverwaltungs-MMC](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Um dem Datenträger einen Laufwerkbuchstaben zuzuweisen, klicken Sie mit der rechten Maustaste auf den nicht zugewiesenen Speicher des online geschalteten und initialisierten Datenträgers, und klicken Sie auf „Neues einfaches Volume“. Dadurch können Sie einen Laufwerkbuchstaben zuweisen. Beachten Sie, dass Sie das Volume nicht zu formatieren brauchen, dies werden Sie später nachholen.

        ![Screenshot des Assistenten „Neues einfaches Volume“ in der Datenträgerverwaltungs-MMC](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Erstellen Sie die Dataset-CSV-Datei. Die Dataset-CSV-Datei ist eine Zuordnung des Pfads der lokalen Daten zur gewünschten Azure-Dateifreigabe, in die die Daten kopiert werden sollen. Beispielsweise wird mit der folgenden Dataset-CSV-Datei eine lokale Dateifreigabe (F:\shares\scratch) einer Azure-Dateifreigabe (MyAzureFileShare) zugeordnet:
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Es können mehrere Freigaben für ein Speicherkonto angegeben werden. Weitere Informationen finden Sie unter [Vorbereiten der Dataset-CSV-Datei](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file).

5. Erstellen Sie die Driveset-CSV-Datei. In der Driveset-CSV-Datei sind die Datenträger aufgeführt, die für den lokalen Export-Agent verfügbar sind. In der folgenden Driveset-CSV-Datei sind z.B. die Laufwerke `X:`, `Y:` und `Z:` aufgeführt, die für den lokalen Exportauftrag verwendet werden sollen:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Weitere Informationen finden Sie unter [Vorbereiten der Driveset-CSV-Datei](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file).

6. Kopieren Sie Ihre Daten mithilfe des [WAImportExport-Tools](https://www.microsoft.com/download/details.aspx?id=55280) auf eine oder mehrere Festplatten.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Ändern Sie die Daten auf den Festplatten oder die Journaldatei nicht mehr, nachdem Sie die Festplattenvorbereitung abgeschlossen haben.

7. [Erstellen Sie einen Importauftrag](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy ist ein bekanntes Kopiertool, das in Windows und Windows Server enthalten ist. Robocopy kann zum Übertragen von Daten in Azure Files verwendet werden, indem die Dateifreigabe lokal bereitgestellt wird. Anschließend wird der bereitgestellte Speicherort als Ziel des Robocopy-Befehls verwendet. Die Verwendung von Robocopy ist ganz einfach:

1. [Binden Sie die Azure-Dateifreigabe ein](storage-how-to-use-files-windows.md). Um eine optimale Leistung zu erzielen, sollten Sie die Azure-Dateifreigabe lokal auf dem Server mit den Daten bereitstellen. In bestimmten Fällen, z.B. wenn es sich bei dem Dateiserver mit den Daten um ein NAS-Gerät handelt, ist dies möglicherweise nicht möglich. In diesem Fall können Sie die Azure-Dateifreigabe auf einem PC bereitstellen. In diesem Beispiel wird `net use` auf der Befehlszeile verwendet, um die Dateifreigabe bereitzustellen:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Verwenden Sie `robocopy` in der Befehlszeile, um Daten in die Azure-Dateifreigabe zu verschieben:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy verfügt über eine Vielzahl von Optionen, mit denen Sie das Kopierverhalten nach Bedarf ändern können. Weitere Informationen finden Sie auf der Handbuchseite zu [Robocopy](https://technet.microsoft.com/library/cc733145.aspx).

### <a name="azcopy"></a>AzCopy
AzCopy ist ein Befehlszeilenprogramm, das zum Kopieren von Daten in und aus Azure Files sowie Azure Blob Storage entwickelt wurde, wobei durch einfache Befehle eine optimale Leistung erzielt wird. Die Verwendung von AzCopy ist ganz einfach:

1. Laden Sie die [neueste Version von AzCopy für Windows](https://aka.ms/downloadazcopy) oder [Linux](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy) herunter.
2. Verwenden Sie `azcopy` in der Befehlszeile, um Daten in die Azure-Dateifreigabe zu verschieben. Die Syntax für Windows lautet wie folgt: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Unter Linux ist die Befehlssyntax etwas anders:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy verfügt über eine Vielzahl von Optionen, mit denen Sie das Kopierverhalten nach Bedarf ändern können. Weitere Informationen finden Sie unter [AzCopy unter Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) und [AzCopy unter Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatische Bereitstellung auf den benötigten PCs/Servern
Um eine lokale Dateifreigabe zu ersetzen, sollten Sie die Freigaben auf den Computern vorab bereitzustellen, auf denen sie verwendet werden sollen. Dies kann automatisch für eine Liste von Computern erfolgen.

> [!Note]  
> Um eine Azure-Dateifreigabe einzubinden, müssen Sie den Speicherkontoschlüssel als Kennwort verwenden, daher sollten Sie die Einbindung nur in vertrauenswürdigen Umgebungen durchführen. 

### <a name="windows"></a>Windows
Mit PowerShell können Sie den Bereitstellungsbefehl auf mehreren PCs ausführen. Im folgenden Beispiel wird `$computers` manuell aufgefüllt, Sie können die Liste der Computer für die Bereitstellung aber auch automatisch generieren. Beispielsweise können Sie diese Variable mit Ergebnissen aus Active Directory auffüllen.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Im folgenden Beispiel führt ein einfaches Bash-Skript in Kombination mit SSH zum gleichen Ergebnis. Die Variable `$computer` wird ebenfalls vom Benutzer entsprechend aufgefüllt:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Problembehandlung für Azure Files unter Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Problembehandlung für Azure Files unter Linux](storage-troubleshoot-linux-file-connection-problems.md)
