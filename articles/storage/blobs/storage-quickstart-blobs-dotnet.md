---
title: 'Schnellstart: Azure Blob Storage-Bibliothek v12: .NET'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit der Azure Blob Storage-Clientbibliothek Version 12 für .NET einen Container und ein Blob in Blob Storage (Objektspeicher) erstellen. Als Nächstes erfahren Sie, wie Sie den Blob auf Ihren lokalen Computer herunterladen, und wie Sie alle Blobs in einem Container auflisten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f8f27743d8680f5e73e1f7bb7a3f7bd6ff2e0464
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054719"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Schnellstart: Azure Blob Storage-Clientbibliothek v12 für .NET

Erfahren Sie etwas über die ersten Schritte mit der Azure Blob Storage-Clientbibliothek v12 für .NET. Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Führen Sie die Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Blobspeicher ist für die Speicherung großer Mengen unstrukturierter Daten optimiert.

Mit der Azure Blob Storage-Clientbibliothek v12 für .NET ist Folgendes möglich:

* Erstellen eines Containers
* Hochladen eines Blobs in Azure Storage
* Auflisten aller Blobs in einem Container
* Herunterladen des Blobs auf den lokalen Computer
* Löschen eines Containers

Zusätzliche Ressourcen:

* [API-Referenzdokumentation](/dotnet/api/azure.storage.blobs)
* [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Beispiele](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
* Azure Storage-Konto – [Erstellen eines Speicherkontos](../common/storage-account-create.md)
* Aktuelles [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) für Ihr Betriebssystem. Laden Sie unbedingt das SDK und nicht die Runtime herunter.

## <a name="setting-up"></a>Einrichten

In diesem Abschnitt wird beschrieben, wie ein Projekt zur Verwendung mit der Azure Blob Storage-Clientbibliothek v12 für .NET vorbereitet wird.

### <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie eine .NET Core-Anwendung mit dem Namen *BlobQuickstartV12*.

1. Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen *BlobQuickstartV12*. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Wechseln Sie zum neu erstellten Verzeichnis *BlobQuickstartV12*.

   ```console
   cd BlobQuickstartV12
   ```

1. Erstellen Sie im Verzeichnis *BlobQuickstartV12* ein weiteres Verzeichnis namens *data*. In diesem werden die Blobdatendateien erstellt und gespeichert.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installieren des Pakets

Installieren Sie im Anwendungsverzeichnis mit dem Befehl `dotnet add package` das Palet für die Azure Blob Storage-Clientbibliothek für .NET.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Einrichten des App-Frameworks

Über das Projektverzeichnis:

1. Öffnen Sie die Datei *Program.cs* im Editor.
1. Entfernen Sie die Anweisung `Console.WriteLine("Hello World!");`.
1. Fügen Sie Anweisungen vom Typ `using` hinzu.
1. Aktualisieren der `Main`-Methodendeklaration zur Unterstützung von asynchronem Code

Der Code lautet wie folgt:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten). Blob Storage bietet drei Typen von Ressourcen:

* Das Speicherkonto
* Einen Container im Speicherkonto
* Ein Blob im Container

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Blob Storage-Architektur](./media/storage-blobs-introduction/blob1.png)

Verwenden Sie die folgenden .NET-Klassen zur Interaktion mit folgenden Ressourcen:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient) Die `BlobServiceClient`-Klasse ermöglicht Ihnen, Azure Storage-Ressourcen und Blobcontainer zu bearbeiten.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): Die `BlobContainerClient`-Klasse ermöglicht Ihnen, Azure Storage-Container und deren Blobs zu bearbeiten.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): Die `BlobClient`-Klasse ermöglicht Ihnen, Azure Storage-Blobs zu bearbeiten.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo) Die `BlobDownloadInfo`-Klasse stellt die Eigenschaften und den Inhalt dar, die beim Herunterladen eines Blobs zurückgegeben werden.

## <a name="code-examples"></a>Codebeispiele

Mit den Beispielcodeausschnitten wird veranschaulicht, wie folgende Vorgänge mit der Azure Blob Storage-Clientbibliothek für .NET durchgeführt werden:

* [Abrufen der Verbindungszeichenfolge](#get-the-connection-string)
* [Container erstellen](#create-a-container)
* [Hochladen von Blobs in einen Container](#upload-blobs-to-a-container)
* [Auflisten der Blobs in einem Container](#list-the-blobs-in-a-container)
* [Herunterladen von Blobs](#download-blobs)
* [Löschen eines Containers](#delete-a-container)

### <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Der folgende Code ruft die Verbindungszeichenfolge für das Speicherkonto aus der Umgebungsvariable ab, die im Abschnitt [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) erstellt wurde.

Fügen Sie diesen Code in der `Main`-Methode hinzu:

```csharp
Console.WriteLine("Azure Blob Storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Erstellen eines Containers

Legen Sie einen Namen für den neuen Container fest. Der folgende Code hängt einen GUID-Wert an den Containernamen an, damit dieser eindeutig ist.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zum Benennen von Containern und Blobs finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

Erstellen Sie eine Instanz der [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient)-Klasse. Rufen Sie dann die [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)-Methode auf, um den Container in Ihrem Speicherkonto zu erstellen.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Hochladen von Blobs in einen Container

Der folgende Codeausschnitt führt folgende Aktionen durch:

1. Erstellen einer Textdatei im lokalen Verzeichnis *data*
1. Abrufen eines Verweises auf ein [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)-Objekt durch Aufrufen der [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient)-Methode für den Container aus dem Abschnitt [Erstellen eines Containers](#create-a-container)
1. Hochladen der lokalen Textdatei in das Blob durch Aufrufen der [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_)-Methode. Mit dieser Methode wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, sofern es bereits vorhanden ist.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Listen Sie die Blobs im Container auf, indem Sie die [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)-Methode aufrufen. In diesem Fall wurde dem Container nur ein Blob hinzugefügt, sodass beim Auflisten auch nur ein Blob zurückgegeben wird.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Herunterladen von Blobs

Laden Sie das zuvor erstellte Blob herunter, indem Sie die [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync)-Methode aufrufen. Im Beispielcode wird das Suffix „DOWNLOADED“ an den Dateinamen angefügt, damit beide Dateien im lokalen Dateisystem angezeigt werden können.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOADED" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Löschen eines Containers

Im folgenden Code werden die von der App erstellten Ressourcen bereinigt, indem der gesamte Container mithilfe von [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) gelöscht wird. Außerdem werden die von der App erstellten lokalen Dateien gelöscht.

Die App wird unterbrochen, um auf Benutzereingaben zu warten, indem `Console.ReadLine` aufgerufen wird, bevor Blob, Container und lokale Dateien gelöscht werden. Dies ist eine gute Möglichkeit, um zu überprüfen, ob die Ressourcen tatsächlich ordnungsgemäß erstellt wurden, bevor sie gelöscht werden.

Fügen Sie diesen Code am Ende der `Main`-Methode hinzu:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Ausführen des Codes

Mit dieser App wird in Ihrem lokalen Ordner *data* eine Testdatei erstellt und in Blob Storage hochgeladen. Anschließend werden im Beispiel die Blobs im Container aufgelistet, und die Datei wird mit einem neuen Namen heruntergeladen, damit Sie die alte und neue Datei vergleichen können.

Navigieren Sie andernfalls zu Ihrem Anwendungsverzeichnis, erstellen Sie die Anwendung, und führen Sie sie aus.

```console
dotnet build
```

```console
dotnet run
```

Die Ausgabe der App sieht etwa wie das folgende Beispiel aus:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Überprüfen Sie den Ordner *data* vor dem Start der Bereinigung auf die beiden Dateien. Sie können sie öffnen und sich vergewissern, dass sie identisch sind.

Nachdem Sie die Dateien erfolgreich überprüft haben, drücken Sie die **EINGABETASTE**, um die Testdateien zu löschen und die Demo zu beenden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde beschrieben, wie Sie Blobs per .NET hochladen, herunterladen und auflisten.

Weitere Beispiel-Apps für Blob Storage finden Sie unter:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12: .NET-Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Tutorials, Beispiele, Schnellstarts und weitere Dokumentation finden Sie unter [Azure für .NET- und .NET Core-Entwickler](/dotnet/azure/).
* Weitere Informationen zu .NET Core finden Sie unter [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Einstieg in .NET in 10 Minuten).
