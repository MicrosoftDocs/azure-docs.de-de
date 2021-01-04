---
title: Abrufen des Typs und des SKU-Namens eines Speicherkontos mit .NET
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie den Typ und den SKU-Namen eines Azure Storage-Kontos mit der .NET-Clientbibliothek abrufen.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fd693573858df095b62a7a7917563141ac19c5b
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579333"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>Abrufen des Typs und des SKU-Namens eines Speicherkontos mit .NET

In diesem Artikel wird gezeigt, wie Sie mithilfe der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage) den Azure Storage-Kontotyp und -SKU-Namen für einen Blob abrufen.

## <a name="about-account-type-and-sku-name"></a>Abrufen von Kontotyp und SKU-Name

**Kontotyp:** Gültige Kontotypen sind `BlobStorage`, `BlockBlobStorage`, `FileStorage`, `Storage` und `StorageV2`. In der [Übersicht über Azure Storage-Konten](storage-account-overview.md) finden Sie weitere Informationen, u. a. Beschreibungen der verschiedenen Speicherkonten.

**SKU-Name:** Zu den gültigen SKU-Namen zählen `Premium_LRS`, `Premium_ZRS`, `Standard_GRS`, `Standard_GZRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_RAGZRS` und `Standard_ZRS`. Bei SKU-Namen muss die Groß-/Kleinschreibung beachtet werden, und es handelt sich dabei um Zeichenfolgenfelder in der [SkuName-Klasse](/dotnet/api/microsoft.azure.management.storage.models.skuname).

## <a name="retrieve-account-information"></a>Abrufen von Kontoinformationen

Im folgenden Codebeispiel werden die schreibgeschützten Kontoeigenschaften abgerufen und angezeigt.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Rufen Sie zum Abrufen des Speicherkontotyps und -SKU-Namens eines Blobs die Methode [GetAccountInfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) oder [GetAccountInfoAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) auf.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Rufen Sie zum Abrufen des Speicherkontotyps und -SKU-Namens eines Blobs die Methode [GetAccountProperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) oder [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) auf.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über andere Vorgänge, die Sie über das [Azure-Portal](https://portal.azure.com) und die Azure-Rest-API für ein Speicherkonto ausführen können.

- [Abrufen von Kontoinformationen (REST)](/rest/api/storageservices/get-account-information)
