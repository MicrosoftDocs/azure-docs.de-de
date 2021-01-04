---
title: 'Schnellstart: Verwalten von Azure-Dateifreigaben mit der Azure CLI'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Azure Files mit der Azure CLI verwalten. Erstellen Sie eine Ressourcengruppe und ein Speicherkonto, und erstellen und verwenden Sie anschließend eine Azure-Dateifreigabe.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd07c09c4ed5be311bf5a485b9bff938c976a2f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659232"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Schnellstart: Erstellen und Verwalten von Azure-Dateifreigaben mit der Azure CLI
In dieser Anleitung werden die Grundlagen der Verwendung von [Azure-Dateifreigaben](storage-files-introduction.md) mit der Azure CLI Schritt für Schritt beschrieben. Azure-Dateifreigaben sind genau wie andere Dateifreigaben, werden jedoch in der Cloud gespeichert und von der Azure-Plattform unterstützt. Azure-Dateifreigaben unterstützen das SMB-Protokoll (Server Message Block) nach Industriestandard sowie das NFS-Protokoll (Network File System, Vorschau) und ermöglichen es, Dateien für mehrere Computer, Anwendungen und Instanzen freizugeben. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- Standardmäßig wird für Azure CLI-Befehle JSON-Code (JavaScript Object Notation) zurückgegeben. JSON ist die Standardmethode zum Senden und Empfangen der Nachrichten von REST-APIs. Um die Verwendung von JSON-Antworten zu ermöglichen, wird in einigen Beispielen dieses Artikels für Azure CLI-Befehle der Parameter *query* verwendet. Für diesen Parameter wird die [JMESPath-Abfragesprache](http://jmespath.org/) zum Analysieren des JSON-Codes genutzt. Weitere Informationen zur Verwendung der Ergebnisse von Azure CLI-Befehlen per JMESPath-Abfragesprache erhalten Sie im [JMESPath-Tutorial](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Falls Sie nicht bereits über eine Azure-Ressourcengruppe verfügen, können Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe erstellen. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *USA, Westen 2* erstellt:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen
Ein Speicherkonto ist ein gemeinsam genutzter Pool mit Speicherplatz, den Sie zum Bereitstellen von Azure-Dateifreigaben oder anderen Speicherressourcen wie Blobs oder Warteschlangen verwenden können. Ein Speicherkonto kann eine unbegrenzte Anzahl von Dateifreigaben enthalten. Auf einer Freigabe kann eine unbegrenzte Anzahl von Dateien gespeichert werden, bis die Kapazitätsgrenzen des Speicherkontos erreicht sind.

Im folgenden Beispiel wird mit dem Befehl [az storage account create](/cli/azure/storage/account) ein Speicherkonto erstellt. Da Speicherkontonamen eindeutig sein müssen, sollten Sie `$RANDOM` verwenden, um zu diesem Zweck eine Zahl an den Namen anzufügen.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Freigaben mit einer Größe von mehr als 5 TiB (bis maximal 100 TiB pro Freigabe) sind nur in Speicherkonten mit lokal redundantem Speicher (LRS) und zonenredundantem Speicher (ZRS) verfügbar. Wenn Sie ein Konto mit georedundantem Speicher (GRS) oder geozonenredundantem Speicher (GZRS) erstellen möchten, entfernen Sie den Parameter `--enable-large-file-share`.

### <a name="get-the-storage-account-key"></a>Abrufen des Speicherkontoschlüssels
Mit Speicherkontoschlüsseln wird in einem Speicherkonto den Zugriff auf die Ressourcen gesteuert. Die Schlüssel werden automatisch erstellt, wenn Sie ein Speicherkonto erstellen. Sie können die Speicherkontoschlüssel für Ihr Speicherkonto mit dem Befehl [az storage account keys list](/cli/azure/storage/account/keys) abrufen: 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe
Jetzt können Sie Ihre erste Azure-Dateifreigabe erstellen. Erstellen Sie Dateifreigaben mit dem Befehl [az storage share create](/cli/azure/storage/share). In diesem Beispiel wird eine Azure-Dateifreigabe mit dem Namen *myshare* erstellt: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

Freigabenamen dürfen nur Kleinbuchstaben, Zahlen und einzelne Bindestriche enthalten (ein Bindestrich am Anfang ist nicht zulässig). Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Verwenden Ihrer Azure-Dateifreigabe
Azure Files bietet zwei Methoden für die Arbeit mit Dateien und Ordnern in Ihrer Azure-Dateifreigabe: das [SMB-Protokoll (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) nach Industriestandard und das [REST-Protokoll „File“](/rest/api/storageservices/file-service-rest-api). 

Informationen zum Einbinden einer Dateifreigabe mit SMB finden Sie im folgenden Dokument für Ihr jeweiliges Betriebssystem:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Verwenden einer Azure-Dateifreigabe mit dem REST-Protokoll „File“ 
Das REST-Protokoll „File“ kann direkt verwendet werden (Sie können also REST-HTTP-Aufrufe selbst erstellen). Gängiger ist jedoch die Verwendung der Azure CLI, des [Azure PowerShell-Moduls](storage-how-to-use-files-powershell.md) oder eines Azure Storage SDK. Bei allen diesen Methoden steht ein praktischer Wrapper um das REST-Protokoll „File“ in der Skript-/Programmiersprache Ihrer Wahl zur Verfügung.  

Die meisten Benutzer werden ihre Azure-Dateifreigabe in Azure Files wahrscheinlich über das SMB-Protokoll nutzen, da sie so die bereits vorhandenen Anwendungen und Tools verwenden können, die sie erwarten. Es gibt jedoch auch Gründe, die für die Verwendung der REST-API „File“ sprechen:

- Sie durchsuchen Ihre Dateifreigabe über Bash in Azure Cloud Shell. (In diesem Fall können Dateifreigaben nicht über SMB eingebunden werden.)
- Sie nutzen serverlose Ressourcen (beispielsweise [Azure Functions](../../azure-functions/functions-overview.md)). 
- Sie erstellen einen Mehrwertdienst, der mit zahlreichen Azure-Dateifreigaben interagiert, um beispielsweise Sicherungen zu erstellen oder Antivirenüberprüfungen durchzuführen.

Die folgenden Beispiele zeigen, wie Sie über die Azure CLI Ihre Azure-Dateifreigabe mit dem REST-Protokoll „File“ ändern. 

### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses
Verwenden Sie den Befehl [`az storage directory create`](/cli/azure/storage/directory), um im Stammverzeichnis Ihrer Azure-Dateifreigabe ein neues Verzeichnis mit dem Namen *myDirectory* zu erstellen:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Hochladen einer Datei
Zum Demonstrieren eines Dateiuploads mit dem Befehl [`az storage file upload`](/cli/azure/storage/file) erstellen Sie zuerst eine Datei zum Hochladen auf das temporäre Cloud Shell-Laufwerk. Im folgenden Beispiel erstellen Sie die Datei und laden diese anschließend hoch:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Beim lokalen Ausführen der Azure CLI ersetzen Sie `~/clouddrive` durch einen Pfad, der auf Ihrem Computer vorhanden ist.

Nach dem Hochladen der Datei können Sie mit dem Befehl [`az storage file list`](/cli/azure/storage/file) sicherstellen, dass die Datei auf Ihre Azure-Dateifreigabe hochgeladen wurde:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Herunterladen einer Datei
Sie können den Befehl [`az storage file download`](/cli/azure/storage/file) verwenden, um eine Kopie der Datei herunterzuladen, die Sie auf das temporäre Cloud Shell-Laufwerk hochgeladen haben:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Kopieren von Dateien
Eine gängige Aufgabe ist das Kopieren von Dateien zwischen Dateifreigaben. Erstellen Sie eine neue Freigabe, um diese Funktionalität zu demonstrieren. Kopieren Sie die Datei, die Sie auf diese neue Freigabe hochgeladen haben, indem Sie den Befehl [az storage file copy](/cli/azure/storage/file/copy) verwenden: 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Wenn Sie die Dateien auf der neuen Freigabe auflisten, sollte die kopierte Datei angezeigt werden:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Das Cmdlet `az storage file copy start` eignet sich für Dateiverschiebungen zwischen Azure-Dateifreigaben. Für Migrationen und umfangreichere Datenverschiebungen empfehlen wir Ihnen die Verwendung von `rsync` (unter macOS und Linux) bzw. `robocopy` (unter Windows). Von `rsync` und `robocopy` wird für Datenverschiebungen nicht die FileREST-API, sondern SMB verwendet.

## <a name="create-and-manage-share-snapshots"></a>Erstellen und Verwalten von Freigabemomentaufnahmen
Eine weitere nützliche Aufgabe, die Sie mit einer Azure-Dateifreigabe durchführen können, ist die Erstellung von Freigabemomentaufnahmen. Mit einer Momentaufnahme wird eine Kopie einer Azure-Dateifreigabe für einen bestimmten Zeitpunkt erstellt. Freigabemomentaufnahmen ähneln einigen Betriebssystemtechnologien, mit denen Sie unter Umständen bereits vertraut sind:

- Momentaufnahmen vom Typ [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) für Linux-Systeme
- Momentaufnahmen vom Typ [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) für macOS
- [Volumeschattenkopie-Dienst (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) für Windows-Dateisysteme wie NTFS und ReFS
 
Sie können eine Freigabemomentaufnahme erstellen, indem Sie den Befehl [`az storage share snapshot`](/cli/azure/storage/share) verwenden:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Durchsuchen des Inhalts einer Freigabemomentaufnahme
Sie können den Inhalt einer Freigabemomentaufnahme durchsuchen, indem Sie den Zeitstempel der Freigabemomentaufnahme, die Sie mit der Variablen `$snapshot` erfasst haben, an den Befehl `az storage file list` übergeben:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Auflisten von Freigabemomentaufnahmen
Verwenden Sie den folgenden Befehl, um eine Liste mit den Momentaufnahmen anzuzeigen, die Sie für Ihre Freigabe erstellt haben:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Wiederherstellen von einer Freigabemomentaufnahme
Sie können eine Datei mit dem zuvor verwendeten Befehl `az storage file copy start` wiederherstellen. Löschen Sie zuerst die Datei „SampleUpload.txt“, die Sie hochgeladen haben, damit Sie sie aus der Momentaufnahme wiederherstellen können:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Löschen einer Freigabemomentaufnahme
Mit dem Befehl [`az storage share delete`](/cli/azure/storage/share) können Sie eine Freigabemomentaufnahme löschen. Verwenden Sie die Variable, die den `$SNAPSHOT`-Verweis auf den Parameter `--snapshot` enthält:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie den Vorgang abgeschlossen haben, können Sie den Befehl [`az group delete`](/cli/azure/group) verwenden, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Alternativ hierzu können Sie auch Ressourcen einzeln entfernen.
- Entfernen Sie die Azure-Dateifreigaben, die Sie für diesen Artikel erstellt haben, wie folgt:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Entfernen Sie das Speicherkonto selbst wie folgt: (Hiermit werden implizit die von Ihnen erstellten Azure-Dateifreigaben und alle anderen ggf. erstellten Speicherressourcen, z.B. ein Azure-Blobspeichercontainer, entfernt.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Was ist Azure Files?](storage-files-introduction.md)