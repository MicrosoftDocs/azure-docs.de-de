---
title: 'Schnellstart: Azure Blob Storage-Clientbibliothek v2.1 für Python'
description: In diesem Schnellstart erstellen Sie ein Speicherkonto und einen Container im Objektspeicher (Blob). Anschließend verwenden Sie die Speicherclientbibliothek v2.1 für Python, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523302"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Schnellstart: Verwalten von Blobs per Python v2.1 SDK

In dieser Schnellstartanleitung erfahren Sie, wie Sie Blobs mit Python verwalten. Bei Blobs handelt es sich um Objekte, die große Mengen von Text oder Binärdaten enthalten können, z. B. Bilder, Dokumente, Streamingmedien und Archivdaten. Sie führen das Hochladen, Herunterladen und Auflisten von Blobs und das Erstellen und Löschen von Containern durch.

> [!NOTE]
> In dieser Schnellstartanleitung wird eine Legacy-Version der Azure Blob Storage-Clientbibliothek verwendet. Informationen zu den ersten Schritten mit der neuesten Version finden Sie unter [Schnellstart: Verwalten von Blobs per Python v12 SDK](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage-Konto [Informationen zu Azure-Speicherkonten](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/).
- [Azure Storage SDK für Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

In dieser Schnellstartanleitung wird als [Beispielanwendung](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) eine einfache Python-Anwendung verwendet.  

Verwenden Sie den folgenden [git](https://git-scm.com/)-Befehl, um die Anwendung in Ihre Entwicklungsumgebung herunterzuladen. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Öffnen Sie zum Überprüfen des Python-Programms die Datei *example.py* im Stammverzeichnis des Repositorys.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge

Geben Sie in der Anwendung Ihren Speicherkontonamen sowie den Kontoschlüssel an, um ein Objekt vom Typ `BlockBlobService` zu erstellen.

1. Öffnen Sie im Projektmappen-Explorer in Ihrer IDE die Datei *example.py*.

1. Ersetzen Sie die Werte `accountname` und `accountkey` durch Ihren Speicherkontonamen und Schlüssel:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Speichern und schließen Sie die Datei.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Das Beispielprogramm erstellt eine Testdatei im Ordner *Dokumente*, lädt die Datei in den Blobspeicher hoch, listet die Blobs in der Datei auf und lädt die Datei unter einem neuen Namen herunter.

1. Installieren Sie die Abhängigkeiten:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Wechseln Sie zur Beispielanwendung:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Führen Sie das Beispiel aus:

    ```console
    python example.py
    ```

    Daraufhin werden Nachrichten angezeigt, die in etwa wie in der folgenden Ausgabe aussehen:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Überprüfen Sie vor dem Fortfahren die beiden Dateien im Ordner *Dokumente*.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. Wenn Sie diese Dateien öffnen, sehen Sie, dass sie identisch sind.

    Sie können auch ein Tool wie den [Azure Storage-Explorer](https://storageexplorer.com) verwenden. Er ist gut geeignet, um die Dateien im Blobspeicher anzuzeigen. Der Azure Storage-Explorer ist ein kostenloses plattformübergreifendes Tool, das Ihnen den Zugriff auf die Speicherkontoinformationen ermöglicht. 

1. Drücken Sie nach dem Anzeigen der Dateien eine beliebige Taste, um das Beispiel zu beenden und die Testdateien zu löschen.

## <a name="learn-about-the-sample-code"></a>Informationen zum Beispielcode

Nachdem Sie nun wissen, welche Aktionen das Beispiel ausführt, öffnen Sie die Datei *example.py*, und sehen Sie sich den Code an.

### <a name="get-references-to-the-storage-objects"></a>Abrufen von Verweisen auf die Speicherobjekte

In diesem Abschnitt instanziieren Sie die Objekte, erstellen einen neuen Container und legen dann Berechtigungen für den Container fest, damit die Blobs öffentlich sind. Sie geben dem Container den Namen `quickstartblobs`. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Als Erstes erstellen Sie die Verweise auf die Objekte, die zum Zugreifen auf und Verwalten von Blobspeicher verwendet werden. Diese Objekte bauen aufeinander auf und jedes wird vom jeweils nächsten in der Liste verwendet.

* Instanziieren Sie das **BlockBlobService**-Objekt, das auf den Blob-Dienst im Speicherkonto verweist. 

* Instanziieren Sie das **CloudBlobContainer**-Objekt, das den Container darstellt, auf den Sie zugreifen. Vom System werden Container genutzt, um Ihre Blobs zu strukturieren – genau wie Ordner, die Sie auf Ihrem Computer zum Strukturieren Ihrer Dateien verwenden.

Wenn Sie über den Cloudblobcontainer verfügen, instanziieren Sie das **CloudBlockBlob**-Objekt, das auf das spezielle Blob verweist, das für Sie relevant ist. Anschließend können Sie das Blob nach Bedarf hochladen, herunterladen und kopieren.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

### <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. Blockblobs können eine Größe von bis zu 4,7 TB haben und alle Arten von Dateien sein, von Excel-Arbeitsblättern bis zu großen Videodateien. Sie können Anfügeblobs für die Protokollierung verwenden, wenn Sie etwas in eine Datei schreiben und ihr nach und nach weitere Informationen hinzufügen möchten. Seitenblobs werden hauptsächlich für die VHD-Dateien verwendet, die als Grundlage für IaaS-VMs (Infrastructure-as-a-Service) dienen. Blockblobs werden am häufigsten verwendet. In dieser Schnellstartanleitung werden Blockblobs verwendet.

Um eine Datei in ein Blob hochzuladen, ermitteln Sie den vollständigen Dateipfad, indem Sie den Verzeichnisnamen mit dem Dateinamen auf Ihrem lokalen Laufwerk verknüpfen. Anschließend können Sie die Datei mithilfe der Methode `create_blob_from_path` in den angegebenen Pfad hochladen. 

Mit dem Beispielcode wird eine lokale Datei erstellt, die vom System für den Upload und Download verwendet wird. Hierbei wird die hochzuladende Datei als *full_path_to_file* (Vollständiger_Pfad_zur_Datei) und der Name des Blobs als *local_file_name* (Name_der_lokalen_Datei) hochgeladen. In diesem Beispiel wird die Datei in den Container mit dem Namen `quickstartblobs` hochgeladen:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Sie können für Blob Storage mehrere Uploadmethoden verwenden. Wenn Sie also beispielsweise über einen Speicherstream verfügen, können Sie anstelle von `create_blob_from_path` die Methode `create_blob_from_stream` verwenden. 

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Mit dem folgenden Code wird ein `generator`-Element für die `list_blobs`-Methode erstellt. Der Code durchläuft die Liste mit den Blobs im Container und gibt deren Namen in der Konsole aus.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Herunterladen der Blobs


Laden Sie Blobs mithilfe der Methode `get_blob_to_path` auf Ihren lokalen Datenträger herunter.
Mit dem folgenden Code wird das Blob heruntergeladen, das Sie zuvor hochgeladen haben. An den Blobnamen wird *_DOWNLOADED* angefügt, damit auf Ihrem lokalen Datenträger beide Dateien angezeigt werden können.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die in dieser Schnellstartanleitung hochgeladenen Blobs nicht mehr benötigen, können Sie mithilfe der Methode `delete_container` den gesamten Container löschen. Falls Sie stattdessen einzelne Dateien löschen möchten, verwenden Sie die Methode `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Ressourcen für die Entwicklung von Python-Anwendungen mit Blobs

Weitere Informationen zur Python-Entwicklung mit Blobspeicher finden Sie unter den folgenden Ressourcen:

### <a name="binaries-and-source-code"></a>Binärdateien und Quellcode

- Zeigen Sie den [Quellcode der Python-Clientbibliothek](https://github.com/Azure/azure-storage-python) für Azure Storage auf GitHub an, laden Sie ihn herunter, und installieren Sie ihn.

### <a name="client-library-reference-and-samples"></a>Clientbibliothek – Referenz und Beispiele

- Weitere Informationen zur Python-Clientbibliothek finden Sie im Artikel zu [Azure Storage-Bibliotheken für Python](/python/api/overview/azure/storage).
- Sehen Sie sich die [Beispiele für Blobspeicher](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) an, die mit der Python-Clientbibliothek geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte
 
In dieser Schnellstartanleitung haben Sie gelernt, wie Sie mit Python Dateien zwischen einem lokalen Datenträger und Azure Blob Storage übertragen. 

Weitere Informationen zum Storage-Explorer und zu Blobs finden Sie unter [Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer (Vorschau)](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).