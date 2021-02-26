---
title: Paralleles Hochladen großer Mengen von Zufallsdaten in Azure Storage
description: Hier erfahren Sie, wie Sie die Azure Storage-Clientbibliothek verwenden, um große Mengen von Zufallsdaten parallel in ein Azure Storage-Konto hochzuladen.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584464"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Paralleles Hochladen großer Mengen von Zufallsdaten in ein Azure Storage-Konto

Dieses Tutorial ist der zweite Teil einer Reihe. In diesem Tutorial wird gezeigt, wie Sie eine Anwendung bereitstellen, in der große Mengen von Zufallsdaten in ein Azure-Speicherkonto hochgeladen werden.

Im zweiten Teil der Reihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Verbindungszeichenfolge
> * Erstellen der Anwendung
> * Ausführen der Anwendung
> * Überprüfen der Anzahl von Verbindungen

Microsoft Azure Blob Storage stellt einen skalierbaren Dienst zum Speichern Ihrer Daten bereit. Um sicherzustellen, dass Ihre Anwendung so leistungsfähig wie möglich ist, sollten Sie die Funktionsweise von Blobspeicher verstehen. Es ist unverzichtbar, die Grenzwerte für Azure-Blobs zu kennen. Informationen zu diesen Grenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../blobs/scalability-targets.md).

[Partitionsbenennung](../blobs/storage-performance-checklist.md#partitioning) ist ein weiterer potenziell wichtiger Faktor beim Entwerfen einer Hochleistungsanwendung, die Blobs verwendet. Bei Blockgrößen größer oder gleich 4 MiB werden [Blockblobs mit hohem Durchsatz](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) verwendet, bei denen die Partitionsbenennung die Leistung nicht beeinträchtigt. Bei Blockgrößen kleiner als 4 MiB wird von Azure-Speicher ein bereichsbasiertes Partitionierungsschema verwendet, um Skalierung und Lastausgleich vorzunehmen. Diese Konfiguration bedeutet, dass Dateien mit ähnlichen Benennungskonventionen oder Präfixen in derselben Partition gespeichert werden. Diese Logik beinhaltet den Namen des Containers, in den die Dateien hochgeladen werden. In diesem Tutorial verwenden Sie Dateien, die sowohl GUIDs für Namen als auch zufällig generierten Inhalt haben. Die Dateien werden dann in fünf verschiedene Container mit zufälligen Namen hochgeladen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial abschließen können, müssen Sie das vorherige Storage-Tutorial abgeschlossen haben: [Erstellen eines virtuellen Computers und eines Speicherkontos für eine skalierbare Anwendung][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Herstellen einer Remoteverbindung mit dem virtuellen Computer

Führen Sie auf Ihrem lokalen Computer den folgenden Befehl aus, um eine Remotedesktopsitzung mit dem virtuellen Computer zu erstellen. Ersetzen Sie die IP-Adresse mit dem publicIPAddress-Wert des virtuellen Computers. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen des virtuellen Computers verwendet haben.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurieren der Verbindungszeichenfolge

Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto. Wählen Sie in Ihrem Speicherkonto unter **Einstellungen** die Option **Zugriffsschlüssel** aus. Kopieren Sie die **Verbindungszeichenfolge** aus dem primären oder sekundären Schlüssel. Melden Sie sich bei dem virtuellen Computer an, den Sie im vorherigen Tutorial erstellt haben. Öffnen Sie eine **Eingabeaufforderung** als Administrator, und führen Sie den `setx`-Befehl mit dem `/m`-Argument aus. Dieser Befehl speichert eine Umgebungsvariable mit der Computereinstellung. Die Umgebungsvariable ist erst verfügbar, nachdem Sie die **Eingabeaufforderung** erneut geladen haben. Ersetzen Sie **\<storageConnectionString\>** im folgenden Beispiel:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Öffnen Sie nun eine andere **Eingabeaufforderung**, navigieren Sie zu `D:\git\storage-dotnet-perf-scale-app`, und geben Sie `dotnet build` ein, um die Anwendung zu erstellen.

## <a name="run-the-application"></a>Ausführen der Anwendung

Navigieren Sie zu `D:\git\storage-dotnet-perf-scale-app`.

Drücken Sie `dotnet run`, um die Anwendung auszuführen. Wenn Sie `dotnet` erstmals ausführen, wird der lokale Paketcache gefüllt, um die Wiederherstellungsgeschwindigkeit zu verbessern und Offlinezugriff zu ermöglichen. Das Ausführen dieses Befehl dauert bis zu einer Minute und nur erfolgt nur ein Mal.

```console
dotnet run
```

Die Anwendung erstellt fünf zufällig benannte Container und beginnt damit, die Dateien, die sich im Stagingverzeichnis befinden, in das Speicherkonto hochzuladen.

Die Methode `UploadFilesAsync` wird im folgenden Beispiel dargestellt:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Die minimale Anzahl und die maximale Anzahl von Threads werden auf 100 festgelegt, um sicherzustellen, dass eine große Anzahl gleichzeitiger Verbindungen zulässig ist.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```
Zusätzlich zum Festlegen der Thread- und der Verbindungsgrenzwerte werden die [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) für die [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync)-Methode so konfiguriert, dass Parallelismus verwendet und MD5-Hashvalidierung deaktiviert wird. Die Dateien werden in 100-MB-Blöcken hochgeladen. Diese Konfiguration bietet eine bessere Leistung, kann aber teuer sein, wenn ein leistungsschwaches Netzwerk verwendet wird, denn wenn ein Fehler auftritt, wird der gesamte 100-MB-Block wiederholt.

|Eigenschaft|Wert|BESCHREIBUNG|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Die Einstellung bewirkt, dass der Blob beim Hochladen in Blöcke aufgeteilt wird. Wenn die höchste Leistung erzielt werden soll, muss dieser Wert dem Achtfachen der Anzahl von Kernen entsprechen. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Diese Eigenschaft deaktiviert die Überprüfung des MD5-Hashs des hochgeladenen Inhalts. Die Deaktivierung der MD5-Überprüfung führt zu einer schnelleren Übertragung. Die Gültigkeit oder Integrität der übertragenen Dateien wird jedoch nicht bestätigt.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Diese Eigenschaft bestimmt, ob ein MD5-Hash berechnet und mit der Datei gespeichert wird.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 2-Sekunden-Backoff mit max. 10 Wiederhol. |Bestimmt die Wiederholungsrichtlinie von Anforderungen. Nach einem Verbindungsfehler erfolgt eine Wiederholung. In diesem Beispiel wird eine [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry)-Richtlinie mit einem Backoff von 2 Sekunden und maximal 10 Wiederholungen konfiguriert. Diese Einstellung ist wichtig, wenn Ihre Anwendung den Skalierbarkeitszielen für Blobspeicher nahekommt. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../blobs/scalability-targets.md).  |

---

Das folgende Beispiel ist eine abgeschnittene Anwendungsausgabe für eine Ausführung auf einem Windows-System.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>Überprüfen der Verbindungen

Während die Dateien hochgeladen werden, können Sie die Anzahl der gleichzeitigen Verbindungen mit Ihrem Speicherkonto überprüfen. Öffnen Sie ein Konsolenfenster, und geben Sie `netstat -a | find /c "blob:https"` ein. Mit diesem Befehl wird die Anzahl der aktuell geöffneten Verbindungen angezeigt. Wie aus dem folgenden Beispiel hervorgeht, waren über 800 Verbindungen geöffnet, als die zufälligen Dateien in das Speicherkonto hochgeladen wurden. Dieser Wert ändert sich durchgehend beim Ausführen des Uploads. Durch Hochladen in parallelen Blöcken verringert sich die Zeitspanne, die zum Übertragen der Inhalte erforderlich ist, erheblich.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Nächste Schritte

Im zweiten Teil der Reihe haben Sie gelernt, wie Sie große Mengen von Zufallsdaten parallel in ein Speicherkonto hochladen können, wozu Folgendes gehört:

> [!div class="checklist"]
> * Konfigurieren der Verbindungszeichenfolge
> * Erstellen der Anwendung
> * Ausführen der Anwendung
> * Überprüfen der Anzahl von Verbindungen

Wechseln Sie zum dritten Teil der Reihe, um große Datenmengen aus einem Speicherkonto herunterzuladen.

> [!div class="nextstepaction"]
> [Herunterladen großer Mengen von Zufallsdaten von Azure Storage](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
