---
title: Angeben eines vom Kunden bereitgestellten Schlüssels für eine Anforderung in Blob Storage mit .NET – Azure Storage
description: Erfahren Sie, wie Sie einen vom Kunden bereitgestellten Schlüssel für eine Anforderung in Blob Storage mit .NET angeben.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d592d0020ae1b5a704296ef68f5153f0207714
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427574"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Angeben eines vom Kunden bereitgestellten Schlüssels für eine Anforderung in Blob Storage mit .NET

Clients, die Anforderungen für Azure Blob Storage stellen, haben die Möglichkeit, einen Verschlüsselungsschlüssel für eine einzelne Anforderung anzugeben. Die Einbeziehung des Verschlüsselungsschlüssels in die Anforderung ermöglicht eine präzise Steuerung der Verschlüsselungseinstellungen für Blob-Speichervorgänge. Vom Kunden bereitgestellte Schlüssel können in Azure Key Vault oder einem anderen Schlüsselspeicher gespeichert werden.

In diesem Artikel wird beschrieben, wie Sie einen vom Kunden bereitgestellten Schlüssel für eine Anforderung mit .NET angeben.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Weitere Informationen zum Authentifizieren mit der Azure Identity-Clientbibliothek über Azure Storage finden Sie im Abschnitt **Authentifizieren mit der Azure Identity-Bibliothek** unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>Beispiel: Verwenden eines vom Kunden bereitgestellten Schlüssels zum Hochladen eines Blobs

Im folgenden Beispiel wird ein vom Kunden bereitgestellter Schlüssel erstellt und zum Hochladen eines Blobs verwendet. Der Code lädt einen Block hoch und führt dann einen Commit für die Blockliste aus, um das Blob in Azure Storage zu schreiben.

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Angeben eines Verschlüsselungsschlüssels bei Richten einer Anforderung an Blob Storage](encryption-customer-provided-keys.md)
- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../common/storage-service-encryption.md)
