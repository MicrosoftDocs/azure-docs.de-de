---
title: Erste Schritte mit Blobspeicher mit Visual Studio (Clouddienste)
description: Erfahren Sie etwas über die ersten Schritte mit Azure Blob Storage in einem Clouddienstprojekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96007030"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Erste Schritte mit Azure Blob Storage und verbundenen Visual Studio-Diensten (Clouddienstprojekte)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt die ersten Schritte mit Azure Blob Storage, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem Visual Studio Clouddienstprojekt ein Azure Storage-Konto erstellt oder auf ein solches Konto verwiesen haben. Wir zeigen Ihnen, wie der Zugriff auf Blob-Container und deren Erstellung funktioniert, und wie Sie allgemeine Aufgaben wie das Hochladen, Auflisten und Herunterladen von Blobs durchführen. Die Beispiele sind in C\# geschrieben und greifen auf die [Microsoft Azure Storage-Clientbibliothek für .NET](/previous-versions/azure/dn261237(v=azure.100)) zurück.

Azure Blob Storage ist ein Dienst zum Speichern großer Mengen unstrukturierter Daten, auf die von überall weltweit über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten.

Dateien werden in Ordnern gespeichert, Speicher-BLOBs analog dazu in Containern. Nachdem Sie einen Speicher erstellt haben, erstellen Sie mindestens einen Container in dem Speicher. Beispielsweise können Sie in einem Speicher namens „Notizbuch“ Container im Speicher namens „Bilder“ erstellen, um Bilder zu speichern, und einen anderen namens „Audio“ erstellen, um Audiodateien zu speichern. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen.

* Weitere Informationen zum programmgesteuerten Bearbeiten von Blobs finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md).
* Allgemeine Informationen zu Azure Storage finden Sie unter [Speicherdokumentation](https://azure.microsoft.com/documentation/services/storage/).
* Allgemeine Informationen zu Azure Cloud Services finden Sie unter [Cloud Services-Dokumentation](https://azure.microsoft.com/documentation/services/cloud-services/).
* Weitere Informationen zum Programmieren von ASP.NET-Anwendungen finden Sie unter [ASP.NET](https://www.asp.net).

## <a name="access-blob-containers-in-code"></a>Zugreifen auf BLOB-Container in Code
Für den programmgesteuerten Zugriff auf Blobs in Clouddienstprojekten müssen Sie die folgenden Elemente hinzuzufügen, sofern sie nicht bereits vorhanden sind.

1. Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Rufen Sie ein **CloudStorageAccount** -Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Rufen Sie ein **CloudBlobClient** -Objekt ab, um auf einen vorhandenen Container in Ihrem Speicherkonto zu verweisen.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. Rufen Sie ein **CloudBlobContainer** -Objekt ab, um auf einen bestimmten BLOB-Container zu verweisen.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Verwenden Sie den gesamten im vorherigen Verfahren angezeigten Code vor dem Code in den folgenden Abschnitten.
> 
> 

## <a name="create-a-container-in-code"></a>Erstellen eines Containers in Code
> [!NOTE]
> Einige APIs, die Aufrufe zum Azure-Speicher in ASP.NET ausführen, sind asynchron. Unter [Asynchrone Programmierung mit Async und Await](/previous-versions/hh191443(v=vs.140)) finden Sie weitere Informationen. Beim Code im folgenden Beispiel wird davon ausgegangen, dass die Programmierung mit Async-Methoden erfolgt.
> 
> 

Um einen Container im Speicherkonto zu erstellen, müssen Sie lediglich wie im folgenden Code einen Aufruf von **CreateIfNotExistsAsync** hinzufügen:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Um die Dateien im Container für alle Benutzer zur Verfügung zu stellen, können Sie den Container mithilfe des folgenden Codes als öffentlich festlegen.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

## <a name="upload-a-blob-into-a-container"></a>Hochladen eines Blobs in einen Container
Azure-Speicher unterstützt Blockblobs und Seitenblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen Blobverweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **UploadFromStream** -Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach können Sie mit der **ListBlobs** -Methode des Containers die Blobs und/oder darin befindlichen Verzeichnisse abrufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, **CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln. Wenn der Typ unbekannt ist, können Sie eine Typenüberprüfung durchführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Im folgenden Code wird gezeigt, wie der URI der einzelnen Elemente im Container **photos** abgerufen und ausgegeben wird:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Wie im vorherigen Codebeispiel gezeigt, wird beim Blob-Dienst ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren. Beachten Sie beispielsweise den folgenden Satz von Blockblobs in einem Container mit dem Namen **photos**:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Wenn Sie **ListBlobs** für den Container aufrufen (wie im obigen Beispiel), dann enthält die zurückgegebene Sammlung **CloudBlobDirectory**- und **CloudBlockBlob**-Objekte, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. Hier sehen Sie die Ausgabe:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


Optional können Sie für den **UseFlatBlobListing**-Parameter der **ListBlobs**-Methode den Wert **true** festlegen. Daraufhin würde jeder Blob unabhängig vom jeweiligen Verzeichnis als **CloudBlockBlob** zurückgegeben werden. Hier sehen Sie den Aufruf von **ListBlobs**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

Und hier sehen Sie die Ergebnisse:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Weitere Informationen finden Sie unter [CloudBlobContainer.ListBlobs](/rest/api/storageservices/List-Blobs).

## <a name="download-blobs"></a>Herunterladen von Blobs
Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die **DownloadToStream** -Methode auf. Im folgenden Beispiel wird die **DownloadToStream** -Methode verwendet, um den Blobinhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

Sie können auch die **DownloadToStream** -Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Löschen von Blobs
Um ein BLOB zu löschen, rufen Sie zuerst einen BLOB-Verweis ab, und rufen Sie dann die **Delete** -Methode auf.

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Asynchrones Auflisten von Blobs auf Seiten
Wenn Sie eine große Anzahl von Blobs auflisten oder die Anzahl der Ergebnisse steuern möchten, die in einem Auflistungsvorgang zurückgegeben werden, können Sie Blobs auf Ergebnisseiten auflisten. In diesem Beispiel wird gezeigt, wie Sie Ergebnisse auf Seiten asynchron zurückgeben, sodass die Ausführung nicht durch einen großen Ergebnissatz blockiert wird.

In diesem Beispiel wird eine einfache Blobauflistung gezeigt, aber Sie können auch eine hierarchische Auflistung verwenden, indem Sie den **useFlatBlobListing**-Parameter der **ListBlobsSegmentedAsync**-Methode auf **FALSE** festlegen.

Da die Beispielmethode eine asynchrone Methode aufruft, muss sie mit dem Schlüsselwort **async** eingeleitet werden und ein **Task**-Objekt zurückgeben. Das Schlüsselwort "await" für die Methode **ListBlobsSegmentedAsync** hält die Ausführung der Beispielmethode an, bis die Auflistung abgeschlossen ist.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]