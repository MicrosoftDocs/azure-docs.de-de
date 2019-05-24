---
title: Sicherer Zugriff auf Anwendungsdaten in der Cloud mit Azure Storage | Microsoft-Dokumentation
description: Verwenden von SAS-Token, Verschlüsselung und HTTPS zum Schutz Ihrer Anwendungsdaten in der Cloud
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 8e56b02b84c0324f723ead1bbf156c847edbbeb5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787988"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Sichern des Zugriffs auf die Daten einer Anwendung in der Cloud

Dieses Tutorial ist der dritte Teil einer Reihe. Hier erfahren Sie, wie Sie den Zugriff auf das Speicherkonto sichern. 

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von SAS-Token, um auf Miniaturbilder zuzugreifen
> * Aktivieren der serverseitigen Verschlüsselung
> * Aktivieren der Übertragung nur über HTTPS

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) ist ein zuverlässiger Dienst zum Speichern von Dateien für Anwendungen. Dieses Tutorial baut auf das [vorhergehende Thema][previous-tutorial] auf und veranschaulicht, wie Sie den Zugriff auf Ihr Speicherkonto aus einer Webanwendung sichern. Nach Abschluss sind die Bilder verschlüsselt, und die Web-App verwendet sichere SAS-Token für den Zugriff auf die Miniaturbilder.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial abschließen können, müssen Sie das vorherige Storage-Tutorial abgeschlossen haben: [Automatisieren der Größenänderung von hochgeladenen Images mit Event Grid][previous-tutorial]. 

## <a name="set-container-public-access"></a>Festlegen des öffentlichen Zugriffs auf den Container

In diesem Teil der Tutorialreihe werden SAS-Token für den Zugriff auf die Miniaturbilder verwendet. In diesem Schritt legen Sie den öffentlichen Zugriff auf den Container _thumbnails_ auf `off` fest.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurieren von SAS-Token für Miniaturbilder

In Teil 1 dieser Tutorialreihe wurden Bilder aus einem öffentlichen Container in der Webanwendung angezeigt. In diesem Teil der Reihe verwenden Sie [SAS-Token (Shared Access Signature)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature), um die Miniaturbilder abzurufen. SAS-Token ermöglichen den eingeschränkten Zugriff auf einen Container oder ein Blob in Abhängigkeit von IP, Protokoll, Zeitintervall und gewährten Rechten.

In diesem Beispiel wird der Branch `sasTokens` im Quellcoderepository verwendet, der ein aktualisiertes Codebeispiel enthält. Löschen Sie die vorhandene GitHub-Bereitstellung mit [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Anschließend konfigurieren Sie die GitHub-Bereitstellung für die Web-App mit dem Befehl [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

Im folgenden Befehl ist `<web-app>` der Name Ihrer Web-App.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Mit dem Branch `sasTokens` des Repositorys wird die Datei `StorageHelper.cs` aktualisiert. Diese ersetzt den Task `GetThumbNailUrls` durch das Codebeispiel unten. Der aktualisierte Task ruft die Miniaturbild-URLs ab, indem eine [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy) so festgelegt wird, dass sie die Anfangszeit, die Ablaufzeit und die Berechtigungen für das SAS-Token angibt. Nach der Bereitstellung der Web-App werden die Miniaturbilder über eine URL mit einem SAS-Token abgerufen. Der aktualisierte Task ist im folgenden Beispiel dargestellt:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

Die folgenden Klassen, Eigenschaften und Methoden werden im vorhergehenden Task verwendet:

|Klasse  |Eigenschaften| Methoden  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment)    | [Ergebnisse](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime)<br>[Berechtigungen](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions) |        |

## <a name="server-side-encryption"></a>Serverseitige Verschlüsselung

[Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) vereinfacht das Sichern und Schützen Ihrer Daten. SSE verschlüsselt ruhende Daten und übernimmt die Verschlüsselung, die Entschlüsselung und die Schlüsselverwaltung. Sämtliche Daten werden mittels 256-Bit- [AES-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)verschlüsselt, einem der sichersten verfügbaren Blockverschlüsselungsverfahren.

SSE verschlüsselt die Daten automatisch in allen Leistungsebenen (Standard oder Premium), allen Bereitstellungsmodellen (Azure Resource Manager und Classic) sowie allen Azure Storage-Diensten (Blob, Queue, Table und File). 

## <a name="enable-https-only"></a>Aktivieren der Übertragung nur über HTTPS

Damit Anforderungen von Daten in und aus einem Speicherkonto geschützt sind, können Sie die Anforderungen auf HTTPS beschränken. Aktualisieren Sie das für das Speicherkonto erforderliche Protokoll mit dem Befehl [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testen Sie die Verbindung mit `curl` über das `HTTP`-Protokoll.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Da jetzt die sichere Übertragung aktiviert ist, erhalten Sie die folgende Meldung:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Nächste Schritte

In Teil 3 der Reihe haben Sie gelernt, wie Sie den Zugriff auf das Speicherkonto über folgende Vorgänge sichern:

> [!div class="checklist"]
> * Verwenden von SAS-Token, um auf Miniaturbilder zuzugreifen
> * Aktivieren der serverseitigen Verschlüsselung
> * Aktivieren der Übertragung nur über HTTPS

Fahren Sie mit Teil 4 der Reihe fort, um zu erfahren, wie Sie Überwachung und Problembehandlung für eine Cloudspeicheranwendung durchführen.

> [!div class="nextstepaction"]
> [Überwachung und Problembehandlung für eine Cloudspeicheranwendung](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
