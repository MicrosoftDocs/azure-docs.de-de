---
title: 'Erstellen oder Löschen eines Blobcontainers mit .NET: Azure Storage'
description: Erfahren Sie, wie Sie einen Blobcontainer in Ihrem Azure Storage-Konto mithilfe der .NET-Clientbibliothek erstellen oder löschen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576446"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Erstellen oder Löschen eines Containers in Azure Storage mit .NET

Blobs in Azure Storage sind in Containern organisiert. Bevor Sie ein Blob hochladen können, müssen Sie zunächst einen Container erstellen. In diesem Artikel wird beschrieben, wie Container mit der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage) erstellt und gelöscht werden.

## <a name="name-a-container"></a>Benennen eines Containers

Ein Containername muss ein gültiger DNS-Name sein, da er Teil des eindeutigen URI ist, der für die Adressierung des Containers oder seiner Blobs verwendet wird. Befolgen Sie diese Regeln, wenn Sie einen Container benennen:

- Containernamen können zwischen 3 und 63 Zeichen lang sein.
- Containernamen müssen mit einem Buchstaben oder einer Zahl beginnen und dürfen nur Kleinbuchstaben, Zahlen und Bindestriche (-) enthalten.
- Zwei oder mehr aufeinanderfolgende Bindestriche sind in Containernamen nicht zulässig.

Der URI für einen Container weist das folgende Format auf:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Erstellen eines Containers

Um einen Container zu erstellen, rufen Sie eine der folgenden Methoden auf:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

Diese Methoden lösen eine Ausnahme aus, wenn ein Container mit demselben Namen bereits vorhanden ist.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Erstellen](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Die Methoden **Create** und **CreateAsync** lösen eine Ausnahme aus, wenn ein Container mit demselben Namen bereits vorhanden ist.

Die Methoden **CreateIfNotExists** und **CreateIfNotExistsAsync** geben einen booleschen Wert zurück, der angibt, ob der Container erstellt wurde. Wenn bereits ein Container mit dem gleichen Namen vorhanden ist, geben diese Methoden **FALSE** zurück, um anzugeben, dass kein neuer Container erstellt wurde.

---

Container werden direkt unterhalb des Speicherkontos erstellt. Es ist nicht möglich, einen Container unter einem anderen Container zu schachteln.

Im folgenden Beispiel wird ein Container asynchron erstellt:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>Erstellen des Stammcontainers

Ein Stammcontainer dient als Standardcontainer für Ihr Speicherkonto. Jedes Speicherkonto kann über einen Stammcontainer verfügen, der den Namen *$root* tragen muss. Der Stammcontainer muss explizit erstellt oder gelöscht werden.

Sie können auf ein Blob verweisen, das im Stammcontainer gespeichert ist, ohne den Namen des Stammcontainers einzubeziehen. Der Stammcontainer ermöglicht das Verweisen auf ein Blob auf der obersten Ebene der Speicherkontohierarchie. Beispielsweise können Sie wie folgt auf ein Blob verweisen, das sich im Stammcontainer befindet:

`https://myaccount.blob.core.windows.net/default.html`

Im folgenden Beispiel wird der Stammcontainer synchron erstellt:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Löschen eines Containers

Um einen Container in .NET zu löschen, verwenden Sie eine der folgenden Methoden:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Löschen](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Löschen](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

Die Methoden **Delete** und **DeleteAsync** lösen eine Ausnahme aus, wenn der Container nicht vorhanden ist.

Die Methoden **DeleteIfExists** und **DeleteIfExistsAsync** geben einen booleschen Wert zurück, der angibt, ob der Container gelöscht wurde. Wenn der angegebene Container nicht vorhanden ist, geben diese Methoden **FALSE** zurück, um anzugeben, dass der Container nicht gelöscht wurde.

Nachdem Sie einen Container gelöscht haben, können Sie *mindestens* 30 Sekunden lang keinen Container mit demselben Namen erstellen. Beim Versuch, einen gleichnamigen Container zu erstellen, tritt der HTTP-Fehlercode 409 (Konflikt) auf. Bei allen anderen Vorgängen für den Container oder den darin enthaltenen Blobs tritt der HTTP-Fehlercode 404 (Nicht gefunden) auf.

Im folgenden Beispiel wird der angegebene Container gelöscht und die Ausnahme behandelt, wenn der Container nicht vorhanden ist:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

Im folgenden Beispiel wird gezeigt, wie alle Container gelöscht werden, die mit einem angegebenen Präfix beginnen.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

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

- [Vorgang zum Erstellen eines Containers](/rest/api/storageservices/create-container)
- [Vorgang zum Löschen eines Containers](/rest/api/storageservices/delete-container)
