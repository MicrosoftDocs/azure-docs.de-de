---
title: 'Azure-Schnellstart: Erstellen eines Blobs im Objektspeicher mithilfe von PHP | Microsoft-Dokumentation'
description: Hier lernen Sie schnell, wie Sie mit PHP Objekte nach/aus Azure Blob Storage übertragen. Sie erfahren, wie Sie Blockblobs in einem Container in Azure Blob Storage hochladen, herunterladen und auflisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: af35927abfa491891dffe2c2397d6daf8c1ccde2
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543118"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Übertragen von Objekten nach/aus Azure Blob Storage mit PHP
In diesem Schnellstart erfahren Sie, wie Sie mit PHP Blockblobs in einem Container in Azure Blob Storage hochladen, herunterladen und auflisten. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Stellen Sie sicher, dass die folgenden zusätzlichen Komponenten installiert sind:

* [PHP](https://php.net/downloads.php)
* [Azure Storage SDK für PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung
Die in diesem Schnellstart verwendete [Beispielanwendung](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) ist eine einfache PHP-Anwendung.  

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Mit diesem Befehl wird das Repository in Ihren lokalen Git-Ordner geklont. Suchen Sie zum Öffnen der PHP-Beispielanwendung nach dem Ordner „storage-blobs-php-quickstart“, und öffnen Sie die Datei „phpqs.php“.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge
In der Anwendung müssen Sie Ihren Speicherkontonamen sowie den Kontoschlüssel angeben, um die **BlobRestProxy**-Instanz für Ihre Anwendung zu erstellen. Es wird empfohlen, diese Bezeichner in einer Umgebungsvariablen auf dem lokalen Computer zu speichern, auf dem die Anwendung ausgeführt wird. Verwenden Sie je nach Betriebssystem die Schritte für eines der folgenden Beispiele, um die Umgebungsvariable zu erstellen. Ersetzen Sie die Werte **youraccountname** und **youraccountkey** durch Ihren Kontonamen und den Kontoschlüssel.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung
Fügen Sie den Ordner aus Ihrem lokalen Git-Ordner in ein Verzeichnis ein, das von Ihrem PHP-Server bereitgestellt wird. Öffnen Sie anschließend eine Eingabeaufforderung für dieses Verzeichnis, und geben Sie `php composer.phar install` ein.

## <a name="run-the-sample"></a>Ausführen des Beispiels
In diesem Beispiel wird im Ordner „.“ eine Testdatei erstellt. Das Beispielprogramm lädt die Testdatei in den Blobspeicher hoch, listet die Blobs im Container auf und lädt die Datei mit einem neuen Namen herunter. 

Führen Sie das Beispiel aus. Die folgende Ausgabe ist ein Beispiel der Ausgabe, die zurückgegeben wird, wenn die Anwendung ausgeführt wird.
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Wenn Sie auf die angezeigte Schaltfläche klicken, löscht das Beispielprogramm den Speichercontainer und die Dateien. Überprüfen Sie den Ordner Ihres Servers auf die beiden Dateien, bevor Sie fortfahren. Sie können diese öffnen und prüfen, ob sie identisch sind.

Sie können zum Anzeigen der Dateien in Blob Storage auch ein Tool, z.B. den [Azure Storage-Explorer](https://storageexplorer.com), verwenden. Der Azure Storage-Explorer ist ein kostenloses plattformübergreifendes Tool, das Ihnen den Zugriff auf die Speicherkontoinformationen ermöglicht. 

Nachdem Sie die Dateien erfolgreich überprüft haben, drücken Sie eine beliebige Taste, um die Demo zu beenden und die Testdateien zu löschen. Da Sie jetzt wissen, welche Aktionen im Beispiel durchgeführt werden, können Sie die Datei „example.rb“ öffnen, um sich den Code anzusehen. 

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Als Nächstes gehen wir schrittweise durch den Beispielcode, damit Sie verstehen können, wie er funktioniert.

### <a name="get-references-to-the-storage-objects"></a>Abrufen von Verweisen auf die Speicherobjekte
Zunächst müssen die Verweise auf die Objekte erstellt werden, die zum Zugreifen auf und Verwalten von Blob Storage verwendet werden. Diese Objekte bauen aufeinander auf und jedes wird vom jeweils nächsten in der Liste verwendet.

* Erstellen Sie eine Instanz des **BlobRestProxy**-Objekts von Azure Storage, um die Anmeldeinformationen für die Verbindung einzurichten. 
* Erstellen Sie das **BlobService**-Objekt, das auf den Blob-Dienst im Speicherkonto verweist. 
* Erstellen Sie das **Container**-Objekt, das den Container darstellt, auf den Sie zugreifen. Container werden zum Organisieren der Blobs verwendet, so wie Sie auf Ihrem Computer Ordner zum Organisieren von Dateien verwenden.

Wenn Sie über das **blobClient**-Containerobjekt verfügen, können Sie das **Block**-Blobobjekt erstellen, mit dem auf das gewünschte jeweilige Blob verwiesen wird. Anschließend können Sie Vorgänge wie das Hochladen, Herunterladen und Kopieren durchführen.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

In diesem Abschnitt richten Sie eine Instanz des Azure Storage-Clients ein, instanziieren das Blob-Dienstobjekt, erstellen einen neuen Container und legen Berechtigungen für den Container fest, damit die Blobs öffentlich gemacht werden. Der Name des Containers lautet **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. Blockblobs werden am häufigsten und auch in diesem Schnellstart verwendet.  

Um eine Datei in einen Blob hochzuladen, rufen Sie den vollständigen Pfad der Datei ab, indem Sie den Verzeichnisnamen und den Dateinamen auf Ihrem lokalen Laufwerk verknüpfen. Sie können anschließend die Datei an den Speicherort unter dem angegebenen Pfad hochladen, indem Sie die **createBlockBlob()**-Methode verwenden. 

Im Beispielcode wird eine lokale Datei nach Azure hochgeladen. Im Code wird die Datei als **myfile** und der Name des Blobs als **fileToUpload** gespeichert. Im folgenden Beispiel wird die Datei in einen Container mit dem Namen **quickstartblobs** hochgeladen.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Verwenden Sie die **createblocklist()**-Methode, um eine Teilaktualisierung für den Inhalt eines Blockblobs durchzuführen. Blockblobs können eine Größe von bis zu 4,7 TB haben und alle Arten von Dateien sein, von Excel-Arbeitsblättern bis zu großen Videodateien. Seitenblobs werden in erster Linie für die VHD-Dateien verwendet, die IaaS-VMs zugrunde liegen. Anfügeblobs dienen der Protokollierung und können z.B. verwendet werden, um beim Schreiben in eine Datei zusätzliche Daten hinzuzufügen. Das Anfügeblob sollte nur in einem Writer-Modell verwendet werden. Die meisten Objekte, die in Blob Storage gespeichert werden, sind allerdings Blockblobs.

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Sie können eine Liste mit Dateien des Containers abrufen, indem Sie die **listBlobs()**-Methode verwenden. Der folgende Code ruft die Liste der Blobs ab und durchläuft sie, um die Namen der in einem Container gefundenen Blobs anzuzeigen.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Abrufen des Inhalts Ihres Blobs

Rufen Sie den Inhalt Ihrer Blobs mit der **getBlob()**-Methode ab. Im folgenden Code wird der Inhalt des Blobs angezeigt, der in einem vorherigen Abschnitt hochgeladen wurde.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die in diesem Schnellstart hochgeladenen Blobs nicht mehr benötigen, können Sie mit der **deleteContainer()**-Methode den gesamten Container löschen. Wenn die erstellten Dateien nicht mehr benötigt werden, können Sie die **deleteBlob()** -Methode verwenden, um die Dateien zu löschen.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Ressourcen für die Entwicklung von PHP-Anwendungen mit Blobs

Sehen Sie sich diese zusätzlichen Ressourcen zur PHP-Entwicklung mit Blobspeicher an:

- Zeigen Sie den [Quellcode der PHP-Clientbibliothek](https://github.com/Azure/azure-storage-php) für Azure Storage auf GitHub an, laden Sie ihn herunter, und installieren Sie ihn.
- Erkunden Sie die [Beispiele für Blobspeicher](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob), die mit der PHP-Clientbibliothek geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte
 
In diesem Schnellstart haben Sie gelernt, wie Sie mit PHP Dateien zwischen einem lokalen Datenträger und Azure Blob Storage übertragen. Weitere Informationen zum Arbeiten mit PHP erhalten Sie, wenn Sie mit unserem PHP Developer Center fortfahren.

> [!div class="nextstepaction"]
> [PHP Developer Center](https://azure.microsoft.com/develop/php/)


Weitere Informationen zum Storage-Explorer und Blobs finden Sie unter [Verwalten von Azure Blob Storage-Ressourcen mit dem Speicher-Explorer (Vorschau)](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).