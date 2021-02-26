---
title: Herunterladen großer Mengen von Zufallsdaten von Azure Storage | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie das Azure-SDK verwenden, um große Mengen von Zufallsdaten aus einem Azure-Speicherkonto herunterzuladen.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584973"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Herunterladen großer Mengen von Zufallsdaten von Azure Storage

Dieses Tutorial ist der dritte Teil einer Reihe. In diesem Tutorial wird gezeigt, wie Sie große Datenmengen von Azure Storage herunterladen.

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren der Anwendung
> * Ausführen der Anwendung
> * Überprüfen der Anzahl von Verbindungen

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial abschließen können, müssen Sie das vorherige Storage-Tutorial abgeschlossen haben: [Paralleles Hochladen großer Mengen von Zufallsdaten in ein Azure Storage-Konto][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Herstellen einer Remoteverbindung mit dem virtuellen Computer

 Führen Sie auf Ihrem lokalen Computer den folgenden Befehl aus, um eine Remotedesktopsitzung mit dem virtuellen Computer zu erstellen. Ersetzen Sie die IP-Adresse mit dem publicIPAddress-Wert des virtuellen Computers. Geben Sie nach Aufforderung die beim Erstellen des virtuellen Computers verwendeten Anmeldeinformationen ein.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Aktualisieren der Anwendung

Im vorherigen Tutorial haben Sie nur Dateien in das Speicherkonto hochgeladen. Öffnen Sie `D:\git\storage-dotnet-perf-scale-app\Program.cs` in einem Text-Editor. Ersetzen Sie die `Main`-Methode durch das folgende Beispiel. In diesem Beispiel wird der Uploadtask auskommentiert, und die Auskommentierung des Downloadtasks und des Tasks zum Löschen des Inhalts im Speicherkonto nach Abschluss des Vorgangs wird aufgehoben.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Nachdem die Anwendung aktualisiert wurde, müssen Sie die Anwendung neu erstellen. Öffnen Sie eine `Command Prompt`, und navigieren Sie zu `D:\git\storage-dotnet-perf-scale-app`. Erstellen Sie die Anwendung neu, indem Sie `dotnet build` ausführen wie im folgenden Beispiel dargestellt:

```console
dotnet build
```

## <a name="run-the-application"></a>Ausführen der Anwendung

Nachdem die Anwendung neu erstellt wurde, können Sie die Anwendung mit dem aktualisierten Code ausführen. Öffnen Sie eine `Command Prompt` (falls nicht bereits geöffnet), und navigieren Sie zu `D:\git\storage-dotnet-perf-scale-app`.

Drücken Sie `dotnet run`, um die Anwendung auszuführen.

```console
dotnet run
```

Der `DownloadFilesAsync`-Task ist im folgenden Beispiel dargestellt:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Die Anwendung liest die Container, die sich im Speicherkonto befinden, das in der **storageconnectionstring** angegeben ist. Sie durchläuft die Blobs mithilfe der Methode [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) und lädt sie mithilfe der Methode [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) auf den lokalen Computer herunter.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Die Anwendung liest die Container, die sich im Speicherkonto befinden, das in der **storageconnectionstring** angegeben ist. Sie durchläuft die Blobs (jeweils zehn auf einmal) mit der [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)-Methode in den Containern und lädt sie mit der [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync)-Methode auf den lokalen Computer herunter.

In der folgenden Tabelle sind die [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions)-Elemente aufgeführt, die für jedes Blob definiert sind, wenn es heruntergeladen wird:

|Eigenschaft|Wert|BESCHREIBUNG|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Diese Eigenschaft deaktiviert die Überprüfung des MD5-Hashs des hochgeladenen Inhalts. Die Deaktivierung der MD5-Überprüfung führt zu einer schnelleren Übertragung. Die Gültigkeit oder Integrität der übertragenen Dateien wird jedoch nicht bestätigt. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Diese Eigenschaft bestimmt, ob ein MD5-Hash berechnet und gespeichert wird.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Überprüfen der Verbindungen

Während die Dateien heruntergeladen werden, können Sie die Anzahl der gleichzeitigen Verbindungen mit Ihrem Speicherkonto überprüfen. Öffnen Sie ein Konsolenfenster, und geben Sie `netstat -a | find /c "blob:https"` ein. Mit diesem Befehl wird die Anzahl der aktuell geöffneten Verbindungen angezeigt. Wie aus dem folgenden Beispiel hervorgeht, waren über 280 Verbindungen geöffnet, als die Dateien aus dem Speicherkonto heruntergeladen wurden:

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Nächste Schritte

Im dritten Teil der Serie haben Sie erfahren, wie Sie große Mengen von Daten aus einem Speicherkonto herunterladen können, und haben u. a. Folgendes gelernt:

> [!div class="checklist"]
> * Ausführen der Anwendung
> * Überprüfen der Anzahl von Verbindungen

Fahren Sie mit dem vierten Teil der Serie fort, um Durchsatz- und Latenzmetriken im Portal zu überprüfen.

> [!div class="nextstepaction"]
> [Überprüfen von Durchsatz- und Latenzmetriken im Portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
