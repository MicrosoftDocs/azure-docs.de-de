---
title: Auflisten von Blobcontainern mit .NET – Azure Storage
description: Hier erfahren Sie, wie Sie einen Blobcontainer in Ihrem Azure Storage-Konto mithilfe der .NET-Clientbibliothek auflisten.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937252"
---
# <a name="list-blob-containers-with-net"></a>Auflisten von Blobcontainern mit .NET

Wenn Sie die Container in einem Azure Storage Konto über den Code auflisten, können Sie eine Reihe von Optionen angeben, um zu steuern, wie Ergebnisse von Azure Storage zurückgegeben werden. In diesem Artikel wird beschrieben, wie Container mit der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage) aufgelistet werden.  

## <a name="understand-container-listing-options"></a>Grundlegendes zu den Optionen für das Auflisten von Containern

Rufen Sie zum Auflisten von Containern in Ihrem Speicherkonto eine der folgenden Methoden auf:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Die Überladungen dieser Methoden bieten zusätzliche Optionen, um zu steuern, wie Container durch den Auflistungsvorgang zurückgegeben werden. Diese Optionen sind in den folgenden Abschnitten beschrieben.

### <a name="manage-how-many-results-are-returned"></a>Festlegen der Anzahl der zurückgegebenen Ergebnisse

Standardmäßig werden durch einen einzelnen Auflistungsvorgang bis zu 5.000 Ergebnisse zurückgegeben. Wenn Sie einen kleineren Ergebnissatz wünschen, geben Sie einen Wert ungleich NULL für die Größe der zurückzugebenden Ergebnisseite an.

Wenn Ihr Speicherkonto mehr als 5000 Container enthält oder wenn Sie eine Seitengröße angegeben haben, aufgrund deren der Auflistungsvorgang eine Teilmenge der Container im Speicherkonto zurückgibt, gibt Azure Storage ein *Fortsetzungstoken* mit der Liste der Container zurück. Ein Fortsetzungstoken ist ein nicht transparenter Wert, den Sie verwenden können, um den nächsten Satz von Ergebnissen aus Azure Storage abzurufen.

Überprüfen Sie im Code den Wert des Fortsetzungstokens, um zu bestimmen, ob er leer (für .NET v12) oder NULL (für .NET v11 und früher) ist. Wenn das Fortsetzungstoken NULL ist, ist der Satz der Ergebnisse vollständig. Wenn das Fortsetzungstoken nicht NULL ist, rufen Sie die Auflistungsmethode erneut auf, und übergeben Sie das Fortsetzungstoken, um den nächsten Ergebnissatz abzurufen. Dieser Vorgang wird so lange wiederholt, bis das Fortsetzungstoken NULL ist.

### <a name="filter-results-with-a-prefix"></a>Filtern von Ergebnissen mit einem Präfix

Um die Liste der Container zu filtern, geben Sie für den `prefix`-Parameter eine Zeichenfolge an. Die Präfixzeichenfolge kann ein oder mehrere Zeichen enthalten. Azure Storage gibt dann nur die Container zurück, deren Namen mit diesem Präfix beginnen.

### <a name="return-metadata"></a>Zurückgeben von Metadaten

Um zusammen mit den Ergebnissen Containermetadaten zurückzugeben, geben Sie den Wert **Metadata** für die Enumeration [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) (für .NET v12) oder für die Enumeration [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) (für .NET v11 und früher) an. Da Azure Storage für jeden zurückgegebenen Container Metadaten enthält, müssen Sie diese nicht zusätzlich abrufen.

## <a name="example-list-containers"></a>Beispiel: Auflisten von Containern

Im folgenden Beispiel werden die Container in einem Speicherkonto, die mit einem angegebenen Präfix beginnen, asynchron aufgelistet. Das Beispiel listet Container auf, die mit dem angegebenen Präfix beginnen, und gibt die angegebene Anzahl von Ergebnissen pro Aufruf an den Auflistungsvorgang zurück. Danach wird das Fortsetzungstoken verwendet, um das nächste Segment von Ergebnissen abzurufen. Im Beispiel werden mit den Ergebnissen auch Containermetadaten zurückgegeben.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Weitere Informationen

- [Auflisten von Containern](/rest/api/storageservices/list-containers2)
- [Aufzählen von Blobressourcen](/rest/api/storageservices/enumerating-blob-resources)
