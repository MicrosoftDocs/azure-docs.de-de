---
title: Erstellen und Verwalten einer Blobmomentaufnahme in .NET
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mit der .NET-Clientbibliothek eine schreibgeschützte Blobmomentaufnahme erstellen, um Blobdaten zu einem bestimmten Zeitpunkt zu sichern.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95543373"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Erstellen und Verwalten einer Blobmomentaufnahme in .NET

Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird. In diesem Artikel wird die Erstellung und Verwaltung von Blobmomentaufnahmen unter Verwendung der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage) beschrieben.

Weitere Informationen zu Blobmomentaufnahmen in Azure Storage finden Sie unter [Blobmomentaufnahmen](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Verwenden Sie eine der folgenden Methoden, um eine Momentaufnahme eines Blockblobs mit Version 12.x der Azure Storage-Clientbibliothek für .NETzu erstellen:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

Das folgende Codebeispiel zeigt, wie Sie eine Momentaufnahme mit Version 12.x erstellen. Schließen Sie einen Verweis auf die [Azure.Identity](https://www.nuget.org/packages/azure.identity)-Bibliothek ein, um Ihre Azure AD-Anmeldeinformationen zu verwenden, um Anforderungen an den Dienst zu autorisieren.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Verwenden Sie eine der folgenden Methoden, um eine Momentaufnahme eines Blockblobs mit Version 11.x der Azure Storage-Clientbibliothek für .NET zu erstellen:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

Das folgende Codebeispiel zeigt, wie Sie eine Momentaufnahme mit Version 11.x erstellen. In diesem Beispiel werden zusätzliche Metadaten für die Momentaufnahme angegeben, wenn sie erstellt wird.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Löschen von Momentaufnahmen

Wenn Sie ein Blob löschen möchten, müssen Sie zunächst alle Momentaufnahmen dieses Blobs löschen. Sie können eine Momentaufnahme einzeln löschen oder angeben, dass alle Momentaufnahmen gelöscht werden sollen, wenn das Quellblob gelöscht wird. Wenn Sie versuchen, ein Blob zu löschen, für das noch Momentaufnahmen vorhanden sind, tritt ein Fehler auf.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Zum Löschen eines Blobs und seiner Momentaufnahmen mit Version 12.x der Azure Storage-Clientbibliothek für .NET verwenden Sie eine der folgenden Methoden und binden die [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption)-Enumeration ein:

- [Löschen](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

Im folgenden Codebeispiel wird veranschaulicht, wie Sie ein Blob und die zugehörigen Momentaufnahmen in .NET löschen, wenn `blobClient` ein Objekt vom Typ [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) ist:

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Zum Löschen eines Blobs und seiner Momentaufnahmen mit Version 11.x der Azure Storage-Clientbibliothek für .NET verwenden Sie eine der folgenden Methoden zum Löschen von Blobs und binden die [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption)-Enumeration ein:

- [Löschen](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

Im folgenden Codebeispiel wird veranschaulicht, wie Sie ein Blob und die zugehörigen Momentaufnahmen in .NET löschen, wenn `blockBlob` ein Objekt vom Typ [CloudBlockBlob][dotnet_CloudBlockBlob] ist:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Blobmomentaufnahmen](snapshots-overview.md)
- [Blobversionen](versioning-overview.md)
- [Vorläufiges Löschen für Blobs](./soft-delete-blob-overview.md)