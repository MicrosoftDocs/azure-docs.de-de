---
title: Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher | Microsoft Docs
description: Erfahren Sie, wie Sie die Azure-Befehlszeilenschnittstelle (Azure-CLI) mit Azure Storage verwenden, um Speicherkonten zu erstellen und zu verwalten sowie mit Azure-Blobs und -Dateien zu arbeiten.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: ea7e4757aac0fccf60a44c70e9de6a63c1ec9498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147007"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher

Die plattformübergreifende Azure CLI auf Open-Source-Basis bietet eine Reihe von Befehlen zum Arbeiten mit der Azure-Plattform. Sie bietet im Wesentlichen die gleiche Funktionalität wie das [Azure-Portal](https://portal.azure.com), so z.B. umfangreiche Datenzugriffsfunktionen.

In dieser Anleitung erfahren Sie, wie Sie die [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) nutzen, um für Ressourcen in Ihrem Azure Storage-Konto verschiedene Aufgaben auszuführen. Sie sollten die neueste Version der CLI herunterladen und installieren bzw. ein Upgrade durchführen, bevor Sie dieser Anleitung folgen.

In den Beispielen in der Anleitung wird von der Verwendung der Bash-Shell unter Ubuntu ausgegangen. Doch das Verhalten sollte auf anderen Plattformen ähnlich sein. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Voraussetzungen
Diese Anleitung setzt voraus, dass Sie die grundlegenden Konzepte von Azure Storage verstehen. Außerdem wird vorausgesetzt, dass Sie die Anforderungen an die Kontoerstellung erfüllen können, die unten für Azure und den Storage-Dienst angegeben sind.

### <a name="accounts"></a>Konten
* **Azure-Konto:** Falls Sie noch nicht über ein Azure-Abonnement verfügen, [erstellen Sie ein kostenloses Azure-Konto](https://azure.microsoft.com/free/).
* **Speicherkonto**: Siehe [Erstellen eines Speicherkontos](storage-quickstart-create-account.md) unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Installieren der Azure CLI

Laden Sie die Azure-Befehlszeilenschnittstelle herunter, und installieren Sie sie gemäß den Anweisungen unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-az-cli2).

> [!TIP]
> Wenn Sie Probleme mit der Installation haben, lesen Sie den Abschnitt zur [Behandlung von Installationsproblemen](/cli/azure/install-az-cli2) des Artikels und die Anleitung zur [Behandlung von Installationsproblemen](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) auf GitHub.
>

## <a name="working-with-the-cli"></a>Arbeiten mit der CLI

Sobald die CLI installiert ist, können Sie den Befehl `az` an Ihrer Befehlszeilenschnittstelle (Bash, Terminal, Eingabeaufforderung) aufrufen, um auf die Azure CLI-Befehle zuzugreifen. Geben Sie den Befehl `az` ein, um eine vollständige Liste der Grundbefehle anzuzeigen (die folgende Beispielausgabe wurde beschnitten):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Führen Sie an der Befehlszeilenschnittstelle den Befehl `az storage --help` aus, um die Untergruppen des `storage`-Befehls aufzulisten. Die Beschreibungen der Untergruppen bieten einen Überblick über die Funktionalität, die die Azure-CLI für die Arbeit mit Ihren Speicherressourcen bietet.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Herstellen einer Verbindung zwischen der CLI und Ihrem Azure-Abonnement

Um mit den Ressourcen in Ihrem Azure-Abonnement zu arbeiten, müssen Sie sich zunächst mit `az login` bei Ihrem Azure-Konto anmelden. Dazu gibt es verschiedene Möglichkeiten:

* **Interaktive Anmeldung**: `az login`
* **Anmeldung mit Benutzername und Kennwort**: `az login -u johndoe@contoso.com -p VerySecret`
  * Dies funktioniert nicht mit Microsoft-Konten oder Konten, die die mehrstufige Authentifizierung verwenden.
* **Anmeldung mit einem Dienstprinzipal**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Azure CLI-Beispielskript

Als Nächstes arbeiten wir mit einem kleinen Shellskript, das einige grundlegenden Azure CLI-Befehle für die Interaktion mit Azure Storage-Ressourcen aufruft. Das Skript erstellt zuerst einen neuen Container in Ihrem Speicherkonto und lädt dann eine vorhandene Datei (als Blob) in diesen Container hoch. Anschließend werden alle Blobs im Container aufgelistet. Zum Abschluss wird die Datei in ein von Ihnen angegebenes Zielverzeichnis auf dem lokalen Computer heruntergeladen.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Konfigurieren und Ausführen des Skripts**

1. Öffnen Sie Ihren bevorzugten Texteditor, in den Sie das kopierte vorhergehende Skript einfügen.

2. Aktualisieren Sie als Nächstes die Skriptvariablen entsprechend Ihren Konfigurationseinstellungen. Ersetzen Sie die folgenden Werte wie angegeben:

   * **\<storage_account_name\>** : Der Name Ihres Speicherkontos.
   * **\<storage_account_key\>** Der primäre oder sekundäre Zugriffsschlüssel für Ihr Speicherkonto.
   * **\<container_name\>** Ein Name für den zu erstellenden Container wie z.B. „azure-cli-beispielcontainer“.
   * **\<blob_name\>** Ein Name für das Zielblob im Container.
   * **\<file_to_upload\>** Der Pfad zu der kleinen Datei auf Ihrem lokalen Computer, wie z.B. „~/images/HelloWorld.png“.
   * **\<destination_file\>** Der Zieldateipfad, z.B. „~/downloadedImage.png“.

3. Nachdem Sie die erforderlichen Variablen aktualisiert haben, speichern Sie das Skript, und beenden Sie den Editor. In den nächsten Schritten wird vorausgesetzt, dass Sie Ihr **my_storage_sample.sh** genannt haben.

4. Markieren Sie das Skript bei Bedarf als ausführbar:`chmod +x my_storage_sample.sh`

5. Führen Sie das Skript aus. Beispielsweise in Bash:`./my_storage_sample.sh`

Eine Ausgabe wie die folgende sollte angezeigt werden. Die **\<destination_file\>** , die Sie im Skript angegeben haben, sollte auf Ihrem lokalen Computer angezeigt werden.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> Die vorangehende Ausgabe erfolgt im Format **Tabelle**. Sie können das zu verwendende Ausgabeformat angeben, indem Sie das Argument `--output` in Ihren CLI-Befehlen angeben oder es mithilfe von `az configure` global festlegen.
>

## <a name="manage-storage-accounts"></a>Verwalten von Speicherkonten

### <a name="create-a-new-storage-account"></a>Erstellen eines neuen Speicherkontos
Zum Verwenden von Azure Storage benötigen Sie ein Speicherkonto. Nachdem Sie Ihren Computer für die „Verbindung mit Ihrem Abonnement“ konfiguriert haben, können Sie ein neues Azure-Speicherkonto erstellen.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Erforderlich]: Der Standort. Beispiel: USA, Westen
* `--name` [Erforderlich]: Der Name des Speicherkontos. Der Name muss 3 bis 24 Zeichen lang sein und darf nur aus Kleinbuchstaben und Ziffern bestehen.
* `--resource-group` [Erforderlich]: Name der Ressourcengruppe
* `--sku` [Erforderlich]: Der Name der SKU des Speicherkontos. Zulässige Werte:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Festlegen eines Azure-Standardspeicherkontos in Umgebungsvariablen

Sie können mehrere Speicherkonten in Ihrem Azure-Abonnement verwenden. Um eines für alle nachfolgenden Speicherbefehle auszuwählen, können Sie diese Umgebungsvariablen festlegen:

Zeigen Sie zunächst mithilfe des Befehls [az storage account keys list](/cli/azure/storage/account/keys) Ihre Speicherkontoschlüssel an:

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Da Sie nun über den Schlüssel verfügen, können Sie ihn und den Kontonamen als Umgebungsvariablen definieren:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Eine weitere Möglichkeit zum Festlegen eines Standardspeicherkontos ist die Verwendung einer Verbindungszeichenfolge. Rufen Sie zuerst die Verbindungszeichenfolge mit dem Befehl `show-connection-string` ab:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Kopieren Sie die ausgegebene Verbindungszeichenfolge, und legen Sie die Umgebungsvariable `AZURE_STORAGE_CONNECTION_STRING` fest (möglicherweise müssen Sie die Verbindungszeichenfolge in Anführungszeichen setzen):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> In allen Beispiele in den folgenden Abschnitten dieses Artikels wird davon ausgegangen, dass Sie die Umgebungsvariablen `AZURE_STORAGE_ACCOUNT` und `AZURE_STORAGE_KEY` festgelegt haben.

## <a name="create-and-manage-blobs"></a>Erstellen und Verwalten von Blobs
Azure Blob Storage ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Dieser Abschnitt setzt voraus, dass Sie mit den Azure Blob Storage-Konzepten bereits vertraut sind. Ausführliche Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../blobs/storage-dotnet-how-to-use-blobs.md) und [Konzepte des Blob-Diensts](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Erstellen eines Containers
Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Mit dem Befehl `az storage container create` können Sie einen Container erstellen:

```azurecli
az storage container create --name <container_name>
```

Sie können eine der drei Ebenen von Lesezugriff für einen neuen Container festlegen, indem Sie das optionale Argument `--public-access` angeben:

* `off` (Standard): Containerdaten sind für den Kontobesitzer privat.
* `blob`: Öffentlicher Lesezugriff für Blobs.
* `container`: Öffentlicher Lese- und Auflistungszugriff auf den gesamten Container.

Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Hochladen eines Blobs in einen Container
Azure Blob Storage unterstützt Block-, Anfüge- und Seitenblobs. Laden Sie Blobs mithilfe des Befehls `blob upload` in einen Container hoch:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Wenn Sie direkt in einen Ordner innerhalb des Containers in Ihrem Speicherkonto hochladen möchten, ersetzen Sie `--name <blob_name>` durch `--name <folder/blob_name>`.

 Standardmäßig lädt der Befehl `blob upload` *.VHD -Dateien in Seitenblobs oder andernfalls Blockblobs hoch. Um einen anderen Typ anzugeben, wenn Sie ein Blob hochladen, können Sie das Argument `--type` angeben. Uulässige Werte sind `append`, `block` und `page`.

 Weitere Informationen zu den verschiedenen Blobtypen finden Sie unter [Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) (Grundlegendes zu Block-, Anfüge- und Seitenblobs).


### <a name="download-a-blob-from-a-container"></a>Herunterladen eines Blobs aus einem Container
In diesem Beispiel wird veranschaulicht, wie Blobs aus einem Container heruntergeladen werden:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Auflisten der Blobs in einem Container mit dem Befehl [az storage blob list](/cli/azure/storage/blob).

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Kopieren von Blobs
Sie können Blobs innerhalb oder zwischen Speicherkonten und Regionen asynchron kopieren.

Im folgenden Beispiel wird veranschaulicht, wie Sie Blobs von einem Speicherkonto in ein anderes kopieren. Zunächst erstellen wir einen Container im Quellspeicherkonto, der den öffentlichen Lesezugriff für seine Blobs angibt. Als Nächstes laden wir eine Datei in den Container hoch. Schließlich kopieren wird das Blob aus dem Container in das Zielspeicherkonto.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

Im oben stehenden Beispiel muss der Zielcontainer bereits im Zielspeicherkonto vorhanden sein, damit der Kopiervorgang erfolgreich durchgeführt werden kann. Darüber hinaus muss das im `--source-uri`-Argument angegebene Quellblob entweder ein Token einer Shared Access Signature (SAS) enthalten oder öffentlich zugänglich sein.

### <a name="delete-a-blob"></a>Löschen eines BLOBs
Verwenden Sie zum Löschen eines Blobs den Befehl `blob delete`:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Erstellen und Verwalten von Dateifreigaben
Azure Files bietet einen gemeinsam genutzten Speicher für Anwendungen und verwendet dabei das SMB-Protokoll (Server Message Block). Microsoft Azure Virtual Machines und Clouddienste können wie lokale Anwendungen Dateidaten mithilfe bereitgestellter Freigaben teilen. Dateifreigaben und Dateidaten können über die Azure-Befehlszeilenschnittstelle verwaltet werden. Weitere Informationen zu Azure Files finden Sie unter [Einführung in Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe
Eine Azure-Dateifreigabe ist eine SMB-Dateifreigabe in Azure. Alle Verzeichnisse und Dateien müssen in einer Dateifreigabe erstellt werden. Ein Konto kann eine unbegrenzte Anzahl von Freigaben enthalten, und eine Freigabe kann eine unbegrenzte Anzahl von Dateien speichern, bis die Kapazitätsgrenze des Speicherkontos erreicht ist. Im folgenden Beispiel wird eine Dateifreigabe namens **myshare**erstellt.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses
Ein Verzeichnis stellt eine hierarchische Struktur für eine Azure-Dateifreigabe bereit. Im folgenden Beispiel wird ein Verzeichnis namens **myDir** in der Dateifreigabe erstellt.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Ein Verzeichnispfad kann mehrere Ebenen beinhalten, z. B. **dir1/dir2**. Sie müssen allerdings sicherstellen, dass alle übergeordneten Verzeichnisse vorhanden sind, bevor Sie ein Unterverzeichnis erstellen. Beispielsweise müssen Sie für den Pfad **dir1/dir2** zuerst das Verzeichnis **dir1** und anschließend das Verzeichnis **dir2** erstellen.

### <a name="upload-a-local-file-to-a-share"></a>Hochladen einer lokalen Datei in eine Freigabe
Im folgenden Beispiel wird eine Datei aus **~/temp/samplefile.txt** in die Dateifreigabe **myshare** hochgeladen. Das Argument `--source` gibt die vorhandene hochzuladende lokale Datei an.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Wie bei der Verzeichniserstellung können Sie einen Verzeichnispfad in der Freigabe angeben, um die Datei in ein in der Freigabe vorhandenes Verzeichnis hochzuladen:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Eine Datei in der Freigabe kann bis zu 1 TB groß sein.

### <a name="list-the-files-in-a-share"></a>Auflisten der Dateien in einer Freigabe
Sie können Dateien und Verzeichnissen in einer Freigabe mit dem Befehl `az storage file list` auflisten:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopieren von Dateien      
Sie können eine Datei in eine andere Datei, eine Datei in ein Blob oder ein Blob in eine Datei kopieren. Geben Sie beispielsweise Folgendes ein, um eine Datei in ein Verzeichnis in einer anderen Freigabe zu kopieren:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Erstellen einer Freigabemomentaufnahme
Mit dem Befehl `az storage share snapshot` können Sie eine Freigabemomentaufnahme erstellen:

```cli
az storage share snapshot -n <share name>
```

Beispielausgabe
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Auflisten von Freigabemomentaufnahmen

Mit `az storage share list --include-snapshots` können Sie Freigabemomentaufnahmen einer bestimmten Freigabe auflisten.

```cli
az storage share list --include-snapshots
```

**Beispielausgabe**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Durchsuchen von Freigabemomentaufnahmen
Sie können mit `az storage file list` auch eine bestimmte Freigabemomentaufnahme durchsuchen, um den jeweiligen Inhalt anzuzeigen. Hierfür müssen der Freigabename `--share-name <snare name>` und der Zeitstempel `--snapshot '2017-10-04T19:45:18.0000000Z'` angegeben werden.

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Beispielausgabe**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Wiederherstellen von Freigabemomentaufnahmen

Sie können eine Datei wiederherstellen, indem Sie mit dem Befehl `az storage file download` eine Datei aus einer Freigabemomentaufnahme kopieren oder herunterladen.

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Beispielausgabe**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Löschen von Freigabemomentaufnahmen
Mit dem Befehl `az storage share delete` können Sie eine Freigabemomentaufnahme löschen, indem Sie den Parameter `--snapshot` mit dem Zeitstempel der Freigabemomentaufnahme angeben:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Beispielausgabe
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Nächste Schritte
Es folgen einige zusätzlichen Ressourcen mit weiteren Informationen zum Arbeiten mit der Azure CLI. 

* [Erste Schritte mit der Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLI: Befehlsreferenz](/cli/azure)
* [Azure CLI auf GitHub](https://github.com/Azure/azure-cli)
