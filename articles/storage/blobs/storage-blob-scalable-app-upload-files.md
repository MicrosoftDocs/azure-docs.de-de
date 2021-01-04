---
title: Paralleles Hochladen großer Mengen von Zufallsdaten in Azure Storage
description: Hier erfahren Sie, wie Sie die Azure Storage-Clientbibliothek verwenden, um große Mengen von Zufallsdaten parallel in ein Azure Storage-Konto hochzuladen.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 5dc1f8b8a7c46a3d6ad6f62d93bc91753e42c3ae
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545039"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Paralleles Hochladen großer Mengen von Zufallsdaten in ein Azure Storage-Konto

Dieses Tutorial ist der zweite Teil einer Reihe. In diesem Tutorial wird gezeigt, wie Sie eine Anwendung bereitstellen, in der große Mengen von Zufallsdaten in ein Azure-Speicherkonto hochgeladen werden.

Im zweiten Teil der Reihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Verbindungszeichenfolge
> * Erstellen der Anwendung
> * Ausführen der Anwendung
> * Überprüfen der Anzahl von Verbindungen

Azure Blob Storage stellt einen skalierbaren Dienst zum Speichern Ihrer Daten bereit. Um sicherzustellen, dass Ihre Anwendung so leistungsfähig wie möglich ist, sollten Sie die Funktionsweise von Blobspeicher verstehen. Es ist unverzichtbar, die Grenzwerte für Azure-Blobs zu kennen. Informationen zu diesen Grenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../blobs/scalability-targets.md).

[Partitionsbenennung](../blobs/storage-performance-checklist.md#partitioning) ist ein weiterer potenziell wichtiger Faktor beim Entwerfen einer Hochleistungsanwendung, die Blobs verwendet. Bei Blockgrößen größer oder gleich 4 MiB werden [Blockblobs mit hohem Durchsatz](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) verwendet, bei denen die Partitionsbenennung die Leistung nicht beeinträchtigt. Bei Blockgrößen kleiner als 4 MiB wird von Azure-Speicher ein bereichsbasiertes Partitionierungsschema verwendet, um Skalierung und Lastausgleich vorzunehmen. Diese Konfiguration bedeutet, dass Dateien mit ähnlichen Benennungskonventionen oder Präfixen in derselben Partition gespeichert werden. Diese Logik beinhaltet den Namen des Containers, in den die Dateien hochgeladen werden. In diesem Tutorial verwenden Sie Dateien, die sowohl GUIDs für Namen als auch zufällig generierten Inhalt haben. Die Dateien werden dann in fünf verschiedene Container mit zufälligen Namen hochgeladen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial abschließen können, müssen Sie das vorherige Storage-Tutorial abgeschlossen haben: [Erstellen eines virtuellen Computers und eines Speicherkontos für eine skalierbare Anwendung][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Herstellen einer Remoteverbindung mit dem virtuellen Computer

Führen Sie auf Ihrem lokalen Computer den folgenden Befehl aus, um eine Remotedesktopsitzung mit dem virtuellen Computer zu erstellen. Ersetzen Sie die IP-Adresse mit dem publicIPAddress-Wert des virtuellen Computers. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen ein, die Sie beim Erstellen des virtuellen Computers verwendet haben.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Konfigurieren der Verbindungszeichenfolge

Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto. Wählen Sie in Ihrem Speicherkonto unter **Einstellungen** die Option **Zugriffsschlüssel** aus. Kopieren Sie die **Verbindungszeichenfolge** aus dem primären oder sekundären Schlüssel. Melden Sie sich bei dem virtuellen Computer an, den Sie im vorherigen Tutorial erstellt haben. Öffnen Sie eine **Eingabeaufforderung** als Administrator, und führen Sie den `setx`-Befehl mit dem `/m`-Argument aus. Dieser Befehl speichert eine Umgebungsvariable mit der Computereinstellung. Die Umgebungsvariable ist erst verfügbar, nachdem Sie die **Eingabeaufforderung** erneut geladen haben. Ersetzen Sie **\<storageConnectionString\>** im folgenden Beispiel:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Öffnen Sie nun eine andere **Eingabeaufforderung**, navigieren Sie zu `D:\git\storage-dotnet-perf-scale-app`, und geben Sie `dotnet build` ein, um die Anwendung zu erstellen.

## <a name="run-the-application"></a>Ausführen der Anwendung

Navigieren Sie zu `D:\git\storage-dotnet-perf-scale-app`.

Drücken Sie `dotnet run`, um die Anwendung auszuführen. Wenn Sie `dotnet` erstmals ausführen, wird der lokale Paketcache gefüllt, um die Wiederherstellungsgeschwindigkeit zu verbessern und Offlinezugriff zu ermöglichen. Das Ausführen dieses Befehl dauert bis zu einer Minute und nur erfolgt nur ein Mal.

```
dotnet run
```

Die Anwendung erstellt fünf zufällig benannte Container und beginnt damit, die Dateien, die sich im Stagingverzeichnis befinden, in das Speicherkonto hochzuladen. Die Anwendung legt die Mindestanzahl von Threads auf 100 und das [DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) auf 100 fest, um sicherzustellen, dass eine große Anzahl von gleichzeitigen Verbindungen zulässig ist, wenn die Anwendung ausgeführt wird.

Zusätzlich zum Festlegen der Thread- und der Verbindungsgrenzwerte werden die [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) für die [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync)-Methode so konfiguriert, dass Parallelismus verwendet und MD5-Hashvalidierung deaktiviert wird. Die Dateien werden in 100-MB-Blöcken hochgeladen. Diese Konfiguration bietet eine bessere Leistung, kann aber teuer sein, wenn ein leistungsschwaches Netzwerk verwendet wird, denn wenn ein Fehler auftritt, wird der gesamte 100-MB-Block wiederholt.

|Eigenschaft|Wert|BESCHREIBUNG|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Die Einstellung bewirkt, dass der Blob beim Hochladen in Blöcke aufgeteilt wird. Wenn die höchste Leistung erzielt werden soll, muss dieser Wert dem Achtfachen der Anzahl von Kernen entsprechen. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Diese Eigenschaft deaktiviert die Überprüfung des MD5-Hashs des hochgeladenen Inhalts. Die Deaktivierung der MD5-Überprüfung führt zu einer schnelleren Übertragung. Die Gültigkeit oder Integrität der übertragenen Dateien wird jedoch nicht bestätigt.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Diese Eigenschaft bestimmt, ob ein MD5-Hash berechnet und mit der Datei gespeichert wird.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 2-Sekunden-Backoff mit max. 10 Wiederhol. |Bestimmt die Wiederholungsrichtlinie von Anforderungen. Nach einem Verbindungsfehler erfolgt eine Wiederholung. In diesem Beispiel wird eine [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry)-Richtlinie mit einem Backoff von 2 Sekunden und maximal 10 Wiederholungen konfiguriert. Diese Einstellung ist wichtig, wenn Ihre Anwendung den Skalierbarkeitszielen für Blobspeicher nahekommt. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../blobs/scalability-targets.md).  |

Der `UploadFilesAsync`-Task ist im folgenden Beispiel dargestellt:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
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

Das folgende Beispiel ist eine abgeschnittene Anwendungsausgabe für eine Ausführung auf einem Windows-System.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Überprüfen der Verbindungen

Während die Dateien hochgeladen werden, können Sie die Anzahl der gleichzeitigen Verbindungen mit Ihrem Speicherkonto überprüfen. Öffnen Sie eine **Eingabeaufforderung**, und geben Sie `netstat -a | find /c "blob:https"` ein. Mit diesem Befehl wird die Anzahl der aktuell mit `netstat` geöffneten Verbindungen angezeigt. Im folgenden Beispiel ist eine ähnliche Ausgabe dargestellt wie die Ausgabe, die angezeigt wird, wenn Sie das Tutorial selbst ausführen. Wie aus dem Beispiel hervorgeht, waren über 800 Verbindungen geöffnet, als die zufälligen Dateien in das Speicherkonto hochgeladen wurden. Dieser Wert ändert sich durchgehend beim Ausführen des Uploads. Durch Hochladen in parallelen Blöcken verringert sich die Zeitspanne, die zum Übertragen der Inhalte erforderlich ist, erheblich.

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