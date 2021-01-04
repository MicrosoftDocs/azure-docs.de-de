---
title: Erstellen einer Dienst-SAS für einen Container oder ein Blob
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie eine Dienst-SAS (Shared Access Signature) für einen Container oder ein Blob mithilfe der Azure Storage-Bibliothek für Blob Storage erstellen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f55cfcf6d6ec369cdf871e8ba38bd81774dacd8e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092311"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Erstellen einer Dienst-SAS für einen Container oder ein Blob

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In diesem Artikel wird beschrieben, wie Sie den Speicherkontoschlüssel zum Erstellen einer Dienst-SAS für einen Container oder ein Blob mit der Azure Storage-Clientbibliothek für Blob Storage verwenden.

## <a name="create-a-service-sas-for-a-blob-container"></a>Erstellen einer Dienst-SAS für einen Blobcontainer

Mit dem folgenden Codebeispiel wird eine SAS für einen Container erstellt. Wenn der Name einer vorhandenen gespeicherten Zugriffsrichtlinie angegeben wird, wird diese Richtlinie der SAS zugewiesen. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, erstellt der Code eine Ad-hoc-SAS für den Container.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Eine Dienst-SAS wird mit dem Kontozugriffsschlüssel signiert. Verwenden Sie die [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential)-Klasse, um die Informationen zu erstellen, die zum Signieren der SAS verwendet werden. Erstellen Sie als Nächstes ein neues [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder)-Objekt, und rufen Sie [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) auf, um die SAS-Tokenzeichenfolge abzurufen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Um eine Dienst-SAS für einen Container zu erstellen, rufen Sie die Methode [CloudBlobContainer.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) auf.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Eine Dienst-SAS wird mit dem Kontozugriffsschlüssel signiert. Verwenden Sie die [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential)-Klasse, um die Informationen zu erstellen, die zum Signieren der SAS verwendet werden. Rufen Sie als Nächstes die Funktion [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) auf, die die erforderlichen Optionen zum Abrufen der SAS-Tokenzeichenfolge bereitstellt.

```javascript
function getContainerSasUri(containerClient, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        permissions: ContainerSASPermissions.parse("c")
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob container is: ${sasToken}`);

    return `${containerClient.url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-blob"></a>Erstellen einer Dienst-SAS für ein Blob

Mit dem folgenden Codebeispiel wird eine SAS für ein Blob erstellt. Wenn der Name einer vorhandenen gespeicherten Zugriffsrichtlinie angegeben wird, wird diese Richtlinie der SAS zugewiesen. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, erstellt der Code eine Ad-hoc-SAS für das Blob.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Eine Dienst-SAS wird mit dem Kontozugriffsschlüssel signiert. Verwenden Sie die [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential)-Klasse, um die Informationen zu erstellen, die zum Signieren der SAS verwendet werden. Erstellen Sie als Nächstes ein neues [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder)-Objekt, und rufen Sie [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) auf, um die SAS-Tokenzeichenfolge abzurufen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Um eine Dienst-SAS für ein Blob zu erstellen, rufen Sie die Methode [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) auf.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Um eine Dienst-SAS für ein Blob zu erstellen, rufen Sie die Methode [CloudBlob.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) auf.

Zum Erstellen einer Dienst-SAS für ein Blob rufen Sie die Funktion [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) auf, die die erforderlichen Optionen bereitstellt.

```javascript
function getBlobSasUri(containerClient, blobName, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        blobName: blobName
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
        sasOptions.permissions = BlobSASPermissions.parse("r");
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob is: ${sasToken}`);

    return `${containerClient.getBlockBlobClient(blobName).url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-directory"></a>Erstellen einer Dienst-SAS für ein Verzeichnis

In einem Speicherkonto mit einem aktivierten hierarchischen Namespace können Sie eine Dienst-SAS für ein Verzeichnis erstellen. Stellen Sie zum Erstellen der Dienst-SAS sicher, dass Sie Version 12.5.0 oder höher des [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/)-Pakets installiert haben.

Im folgenden Beispiel wird gezeigt, wie Sie eine Dienst-SAS für ein Verzeichnis mit der v12-Clientbibliothek für .NET erstellen:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../common/storage-sas-overview.md)
- [Erstellen einer Dienst-SAS](/rest/api/storageservices/create-service-sas)
