---
title: Sicheres Zugreifen auf Anwendungsdaten
titleSuffix: Azure Storage
description: Verwenden von SAS-Token, Verschlüsselung und HTTPS zum Schutz Ihrer Anwendungsdaten in der Cloud
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f7c5dbaf30965fdd5f438f0351cfa2cd60e05b70
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746565"
---
# <a name="secure-access-to-application-data"></a>Sicheres Zugreifen auf Anwendungsdaten

Dieses Tutorial ist der dritte Teil einer Reihe. Hier erfahren Sie, wie Sie den Zugriff auf das Speicherkonto sichern. 

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von SAS-Token, um auf Miniaturbilder zuzugreifen
> * Aktivieren der serverseitigen Verschlüsselung
> * Aktivieren der Übertragung nur über HTTPS

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) ist ein zuverlässiger Dienst zum Speichern von Dateien für Anwendungen. Dieses Tutorial baut auf das [vorhergehende Thema][previous-tutorial] auf und veranschaulicht, wie Sie den Zugriff auf Ihr Speicherkonto über eine Webanwendung sichern. Nach Abschluss sind die Bilder verschlüsselt, und die Web-App verwendet sichere SAS-Token für den Zugriff auf die Miniaturbilder.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Tutorial abschließen können, müssen Sie das vorherige Storage-Tutorial abgeschlossen haben: [Tutorial: Automatisieren der Größenänderung von hochgeladenen Bildern mit Event Grid][previous-tutorial].

## <a name="set-container-public-access"></a>Festlegen des öffentlichen Zugriffs auf den Container

In diesem Teil der Tutorialreihe werden SAS-Token für den Zugriff auf die Miniaturbilder verwendet. In diesem Schritt legen Sie den öffentlichen Zugriff auf den Container *thumbnails* auf `off` fest.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Konfigurieren von SAS-Token für Miniaturbilder

In Teil 1 dieser Tutorialreihe wurden Bilder aus einem öffentlichen Container in der Webanwendung angezeigt. In diesem Teil der Reihe verwenden Sie SAS-Token (Shared Access Signature), um die Miniaturbilder abzurufen. SAS-Token ermöglichen den eingeschränkten Zugriff auf einen Container oder ein Blob in Abhängigkeit von IP, Protokoll, Zeitintervall und gewährten Rechten. Weitere Informationen zu SAS finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signatures)](../common/storage-sas-overview.md).

In diesem Beispiel wird der Branch `sasTokens` im Quellcoderepository verwendet, der ein aktualisiertes Codebeispiel enthält. Löschen Sie die vorhandene GitHub-Bereitstellung mit [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Anschließend konfigurieren Sie die GitHub-Bereitstellung für die Web-App mit dem Befehl [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Im folgenden Befehl ist `<web-app>` der Name Ihrer Web-App.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

Mit dem Branch `sasTokens` des Repositorys wird die Datei `StorageHelper.cs` aktualisiert. Diese ersetzt den Task `GetThumbNailUrls` durch das Codebeispiel unten. Der aktualisierte Task ruft die Miniaturbild-URLs ab. Dazu wird [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) verwendet, um die Anfangszeit, die Ablaufzeit und die Berechtigungen für das SAS-Token anzugeben. Nach der Bereitstellung der Web-App werden die Miniaturbilder über eine URL mit einem SAS-Token abgerufen. Der aktualisierte Task ist im folgenden Beispiel dargestellt:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

Die folgenden Klassen, Eigenschaften und Methoden werden im vorhergehenden Task verwendet:

| Klasse | Eigenschaften | Methoden |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [URI](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Name](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Abfrage](/dotnet/api/system.uribuilder.query) |  |
|[Liste](/dotnet/api/system.collections.generic.list-1) | | [Add (Hinzufügen)](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Azure Storage-Verschlüsselung

[Azure Storage-Verschlüsselung](../common/storage-service-encryption.md) trägt zum Schutz Ihrer Daten bei, indem ruhende Daten verschlüsselt sowie Verschlüsselung und Entschlüsselung verarbeitet werden. Sämtliche Daten werden mittels 256-Bit- [AES-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)verschlüsselt, einem der sichersten verfügbaren Blockverschlüsselungsverfahren.

Sie können Verschlüsselungsschlüssel durch Microsoft verwalten lassen oder eigene kundenseitig verwaltete Schlüssel verwenden, die in Azure Key Vault oder in Key Vault Managed Hardware Security Model (HSM) (Vorschau) gespeichert sind (Bring Your Own Key, BYOK). Weitere Informationen finden Sie unter [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](../common/customer-managed-keys-overview.md).

Azure Storage-Verschlüsselung verschlüsselt die Daten automatisch in allen Leistungsstufen (Standard oder Premium), allen Bereitstellungsmodellen (Azure Resource Manager und Classic) sowie allen Azure Storage-Diensten (Blob, Queue, Table und File).

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
