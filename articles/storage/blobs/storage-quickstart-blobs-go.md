---
title: 'Azure-Schnellstart: Erstellen eines Blobs im Objektspeicher mithilfe von Go | Microsoft-Dokumentation'
description: In diesem Schnellstart erstellen Sie ein Speicherkonto und einen Container im Objektspeicher (Blob). Anschließend verwenden Sie die Speicherclientbibliothek für Go, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: bf0e868e9ee746da1dfe1b03403d21f7edb3bd5e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544648"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs mit Go

In diesem Schnellstart erfahren Sie, wie Sie mit der Programmiersprache „Go“ Blockblobs in einem Container in Azure Blob Storage hochladen, herunterladen und auflisten. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Stellen Sie sicher, dass die folgenden zusätzlichen Komponenten installiert sind:
 
* [Go 1.8 oder höher](https://golang.org/dl/).
* [Azure Storage Blob SDK für Go](https://github.com/azure/azure-storage-blob-go/). Laden Sie das SDK mit dem folgenden Befehl herunter:

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > Achten Sie darauf, dass Sie `Azure` in der URL großschreiben, um Probleme mit der Groß-/Kleinschreibung bei der Verwendung des SDK zu vermeiden. `Azure` muss in den Importanweisungen ebenfalls großgeschrieben werden.
    
## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung
Die in diesem Schnellstart verwendete [Beispielanwendung](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) ist eine einfache Go-Anwendung.  

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Mit diesem Befehl wird das Repository in Ihren lokalen Git-Ordner geklont. Zum Öffnen des Go-Beispiels für Blob Storage suchen Sie nach der Datei „storage-quickstart.go“.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurieren der Speicherverbindungszeichenfolge
Für diese Lösung müssen der Name und der Schlüssel Ihres Speicherkontos sicher in Umgebungsvariablen gespeichert sein, die sich auf dem Computer befinden, auf dem das Beispiel ausgeführt wird. Befolgen Sie je nach Betriebssystem die Schritte für eines der unten angegebenen Beispiele, um die Umgebungsvariablen zu erstellen.

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Ausführen des Beispiels
Dieses Beispiel erstellt eine Testdatei im aktuellen Ordner, lädt die Testdatei in den Blobspeicher hoch, listet die Blobs im Container auf und lädt die Datei in einen Puffer herunter. 

Geben Sie zum Ausführen des Beispiels den folgenden Befehl aus: 

```go run storage-quickstart.go```

Die folgende Ausgabe ist ein Beispiel der Ausgabe, die zurückgegeben wird, wenn die Anwendung ausgeführt wird.
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Wenn Sie die Taste zum Fortzusetzen drücken, löscht das Beispielprogramm den Speichercontainer und die Dateien. 

> [!TIP]
> Sie können zum Anzeigen der Dateien in Blob Storage auch ein Tool, z.B. den [Azure Storage-Explorer](https://storageexplorer.com), verwenden. Der Azure Storage-Explorer ist ein kostenloses plattformübergreifendes Tool, das Ihnen den Zugriff auf die Speicherkontoinformationen ermöglicht. 
>

## <a name="understand-the-sample-code"></a>Grundlagen des Beispielcodes

Als Nächstes gehen wir schrittweise durch den Beispielcode, damit Sie verstehen können, wie er funktioniert.

### <a name="create-containerurl-and-bloburl-objects"></a>Erstellen von ContainerURL- und BlobURL-Objekten
Zunächst müssen die Verweise auf die Objekte „ContainerURL“ und „ContainerURL“ erstellt werden, die zum Zugreifen auf und Verwalten von Blob Storage verwendet werden. Diese Objekte bieten Low-Level-APIs (beispielsweise für Erstellungs-, Upload- und Downloadvorgänge) zum Ausgeben von REST-APIs.

* Verwenden Sie die Struktur [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential), um Ihre Anmeldeinformationen zu speichern. 

* Erstellen Sie unter Verwendung der Anmeldeinformationen und Optionen eine [**Pipeline**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline). Mit der Pipeline werden u.a. Wiederholungsrichtlinien, Protokollierung und Deserialisierung von HTTP-Antwortnutzlasten festgelegt.  

* Instanziieren Sie eine neue [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) und ein neues Objekt vom Typ [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL), um Vorgänge für Container (Erstellen) und Blobs (Hochladen und Herunterladen) auszuführen.


Sobald das ContainerURL-Objekt vorhanden ist, können Sie das **BlobURL**-Objekt instanziieren, das auf einen Blob verweist, und Vorgänge wie Hochladen, Herunterladen und Kopieren ausführen.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Benennen von Containern, Blobs und Metadaten und Verweisen auf diese](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

In diesem Abschnitt erstellen Sie einen neuen Container. Der Name des Containers lautet **quickstartblobs-[zufällige Zeichenfolge]** . 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. Blockblobs werden am häufigsten und auch in diesem Schnellstart verwendet.  

Zum Hochladen einer Datei in ein Blob öffnen Sie die Datei mit **os.Open**. Anschließend können Sie die Datei mithilfe einer der folgenden REST-APIs an den angegebenen Pfad hochladen: Upload (PutBlob), StageBlock/CommitBlockList (PutBlock/PutBlockList). 

Als Alternative bietet das SDK [High-Level-APIs](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go), die auf den Low-Level-REST-APIs aufbauen. So verwendet beispielsweise die Funktion **_UploadFileToBlockBlob_* _ Vorgänge vom Typ „StageBlock“ (PutBlock), um eine Datei parallel in Blöcken hochzuladen und so den Durchsatz zu optimieren. Ist die Datei kleiner als 256 MB, wird stattdessen „Upload“ (PutBlob) verwendet, um die Übertragung mit einer einzelnen Transaktion durchzuführen.

Im folgenden Beispiel wird die Datei in einen Container mit dem Namen „_*quickstartblobs-[zufällige Zeichenfolge]“ hochgeladen.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Rufen Sie eine Liste mit Dateien im Container ab, indem Sie die **ListBlobs**-Methode auf ein **ContainerURL**-Objekt anwenden. ListBlobs gibt ein einzelnes Segment von Blobs (bis zu 5000) beginnend mit dem angegebenen **Marker** zurück. Verwenden Sie einen leeren Marker, um die Enumeration von vorn zu beginnen. Blobnamen werden in lexikografischer Reihenfolge zurückgegeben. Nach dem Abrufen eines Segments verarbeiten Sie dieses und rufen anschließend erneut ListBlobs auf, um den zuvor zurückgegebenen Marker zu übergeben.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Herunterladen des Blobs

Laden Sie Blobs herunter, indem Sie die Low-Level-Funktion **Download** auf ein BlobURL-Objekt anwenden. Dadurch wird eine Struktur vom Typ **DownloadResponse** zurückgegeben. Führen Sie die Funktion **Body** für die Struktur aus, um zum Lesen der Daten einen Datenstrom vom Typ **RetryReader** zu erhalten. Sollte beim Lesen der Daten ein Verbindungsfehler auftreten, wird durch weitere Anforderungen versucht, die Verbindung wiederherzustellen und den Lesevorgang fortzusetzen. Wenn „MaxRetryRequests“ für „RetryReaderOption“ auf „0“ festgelegt ist (Standardeinstellung), wird der ursprüngliche Antworttext zurückgegeben, und es werden keine Wiederholungen durchgeführt. Alternativ können Sie die High-Level-API **DownloadBlobToBuffer** oder **DownloadBlobToFile** verwenden, um Ihren Code zu vereinfachen.

Der folgende Code lädt das Blob mithilfe der Funktion **Download** herunter. Die Inhalte des Blobs werden in einen Puffer geschrieben und auf der Konsole angezeigt.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie die in diesem Schnellstart hochgeladenen Blobs nicht mehr benötigen, können Sie mit **Delete**-Methode den gesamten Container löschen. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Ressourcen für die Entwicklung von Go-Anwendungen mit Blobs

Sehen Sie sich diese zusätzlichen Ressourcen zur Go-Entwicklung mit Blobspeicher an:

- Zeigen Sie den [Quellcode der Go-Clientbibliothek](https://github.com/Azure/azure-storage-blob-go) für Azure Storage auf GitHub an, und installieren Sie ihn.
- Erkunden Sie die [Beispiele für Blobspeicher](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples), die mit der Go-Clientbibliothek geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte
 
In diesem Schnellstart haben Sie gelernt, wie Sie mit Go Dateien zwischen einem lokalen Datenträger und Azure Blob Storage übertragen. Weitere Informationen zum Azure Storage Blob-SDK finden Sie im [Quellcode](https://github.com/Azure/azure-storage-blob-go/) und in der [API-Referenz](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob).