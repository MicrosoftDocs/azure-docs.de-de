---
title: Erstellen einer Konto-SAS mit .NET
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie eine Konto-SAS (Shared Access Signature) mithilfe der .NET-Clientbibliothek erstellen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092209"
---
# <a name="create-an-account-sas-with-net"></a>Erstellen einer Konto-SAS mit .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In diesem Artikel wird beschrieben, wie Sie den Speicherkontoschlüssel zum Erstellen einer Konto-SAS mit der [Azure Storage-Clientbibliothek für .NET](/dotnet/api/overview/azure/storage) verwenden.

## <a name="create-an-account-sas"></a>Erstellen einer Konto-SAS

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Eine Konto-SAS wird mit dem Kontozugriffsschlüssel signiert. Verwenden Sie die [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential)-Klasse, um die Informationen zu erstellen, die zum Signieren der SAS verwendet werden. Erstellen Sie als Nächstes ein neues [AccountSasBuilder](/dotnet/api/azure.storage.sas.accountsasbuilder)-Objekt, und rufen Sie [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) auf, um die SAS-Tokenzeichenfolge abzurufen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Um eine Konto-SAS für einen Container zu erstellen, rufen Sie die Methode [CloudStorageAccount.GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) auf.

Im folgenden Codebeispiel wird eine Konto-SAS erstellt, die für die Blob- und Dateidienste gültig ist und dem Client die Berechtigungen Lesen, Schreiben und Auflisten für den Zugriff auf APIs auf Dienstebene gewährt. Bei der Konto-SAS ist das Protokoll auf HTTPS beschränkt, sodass die Anforderung mit HTTPS durchgeführt werden muss. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>Verwenden einer Konto-SAS von einem Client

Um die Konto-SAS zum Zugreifen auf APIs auf Dienstebene für den Blob-Dienst zu verwenden, erstellen Sie ein Blobdienst-Clientobjekt, indem Sie die SAS und den Blob-Speicherendpunkt für Ihr Speicherkonto verwenden.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Ersetzen Sie in diesem Codeausschnitt den Platzhalter `<storage-account>` durch den Namen Ihres Speicherkontos.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md)
- [Erstellen einer Konto-SAS](/rest/api/storageservices/create-account-sas)
