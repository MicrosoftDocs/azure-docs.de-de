---
title: Hochladen von Bilddaten in die Cloud mit Azure Storage | Microsoft Docs
description: Verwenden Sie Azure Blob Storage mit Web-Apps, um App-Daten in einem Speicherkonto zu speichern. In diesem Tutorial wird eine Web-App erstellt, mit der Bilder in Azure-Speicher abgelegt und daraus angezeigt werden.
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: c20c78cb3c946c666b1640ccac6f86c9b52387ea
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843871"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Tutorial: Hochladen von Bilddaten in die Cloud mit Azure Storage

Dieses Tutorial ist der erste Teil einer Serie. In diesem Tutorial wird beschrieben, wie Sie eine Web-App bereitstellen. Die Web-App nutzt die Azure Blob Storage-Clientbibliothek, um Bilder in ein Speicherkonto hochzuladen. Nachdem Sie das Tutorial durchgearbeitet haben, verfügen Sie über eine Web-App, mit der Bilder in Azure-Speicher abgelegt und daraus angezeigt werden.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![Bildgrößenänderungs-App in .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![Bildgrößenänderungs-App in JavaScript](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]

> * Speicherkonto erstellen
> * Erstellen eines Containers und Festlegen von Berechtigungen
> * Abrufen eines Zugriffsschlüssels
> * Bereitstellen einer Web-App in Azure
> * Konfigurieren von App-Einstellungen
> * Interagieren mit der Web-App

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um dieses Tutorial durcharbeiten zu können. Erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, benötigen Sie mindestens Version 2.0.4 der Azure-Befehlszeilenschnittstelle. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.  

Im folgenden Beispiel wird eine Ressourcengruppe namens `myResourceGroup` erstellt.

```bash
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Das Beispiel lädt Bilder in einen Blobcontainer in einem Azure-Speicherkonto hoch. Ein Speicherkonto stellt einen eindeutigen Namespace zum Speichern Ihrer Azure Storage-Datenobjekte sowie für den Zugriff darauf bereit. Erstellen Sie ein Speicherkonto in der Ressourcengruppe, die Sie erstellt haben, indem Sie den Befehl [az storage account create](/cli/azure/storage/account) verwenden.

> [!IMPORTANT]
> In Teil 2 des Tutorials verwenden Sie Azure Event Grid mit Blob Storage. Erstellen Sie Ihr Speicherkonto in einer Azure-Region, die Event Grid unterstützt. Eine Liste mit den unterstützten Regionen finden Sie unter [Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Ersetzen Sie im folgenden Befehl den Platzhalter `<blob_storage_account>` durch Ihren eigenen global eindeutigen Namen für das Blob Storage-Konto.

```bash
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Erstellen von Blob Storage-Containern

Die App verwendet zwei Container im Blob Storage-Konto. Container ähneln Ordnern und Speicherblobs. In den Container *images* lädt die App Bilder mit voller Auflösung hoch. In einem späteren Teil der Reihe lädt eine Azure-Funktionen-App Miniaturansichten der Bilder mit angepasster Größe in den Container *thumbnails* hoch.

Rufen Sie mit dem Befehl [az storage account keys list](/cli/azure/storage/account/keys) den Speicherkontoschlüssel ab. Verwenden Sie anschließend diesen Schlüssel, um mit dem Befehl [az storage container create](/cli/azure/storage/container) zwei Container zu erstellen.

Der öffentliche Zugriff für den Container *images* ist auf `off` festgelegt. Der öffentliche Zugriff für den Container *thumbnails* ist auf `container` festgelegt. Bei der Einstellung `container` für den öffentlichen Zugriff können Benutzer, die die Webseite besuchen, die Miniaturansichten anzeigen.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Notieren Sie sich den Namen und Schlüssel des Blob Storage-Kontos. Die Beispiel-App verwendet diese Einstellungen zum Herstellen der Verbindung mit dem Speicherkonto zum Hochladen der Bilder. 

## <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

Ein [App Service-Plan](../../app-service/overview-hosting-plans.md) gibt den Standort, die Größe und die Funktionen der Webserverfarm an, die Ihre App hostet.

Erstellen Sie mit dem Befehl [az appservice plan create](/cli/azure/appservice/plan) einen App Service-Plan.

Im folgenden Beispiel wird ein App Service-Plan namens `myAppServicePlan` mit dem Tarif **Free** erstellt:

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Die Web-App umfasst einen Hostingort für den Code der Beispiel-App, der aus dem GitHub-Beispielrepository bereitgestellt wird. Erstellen Sie eine [Web-App](../../app-service/overview.md) im App Service-Plan `myAppServicePlan` mit dem Befehl [az webapp create](/cli/azure/webapp).  

Ersetzen Sie im folgenden Befehl `<web_app>` durch einen eindeutigen Namen. Gültige Zeichen sind `a-z`, `0-9` und `-`. Wenn `<web_app>` nicht eindeutig ist, wird die folgende Fehlermeldung angezeigt: *Eine Website mit dem angegebenen Namen `<web_app>` ist bereits vorhanden.* Die Standard-URL der Web-App lautet `https://<web_app>.azurewebsites.net`.  

```bash
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Bereitstellen der Beispiel-App aus dem GitHub-Repository

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

App Service unterstützt verschiedene Möglichkeiten zum Bereitstellen von Inhalt für eine Web-App. In diesem Tutorial stellen Sie die Web-App aus einem [öffentlichen GitHub-Beispielrepository](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) bereit. Konfigurieren Sie die GitHub-Bereitstellung für die Web-App mit dem Befehl [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Das Beispielprojekt enthält eine [ASP.NET MVC](https://www.asp.net/mvc)-App. Die App akzeptiert ein Bild, speichert es unter einem Speicherkonto und zeigt Bilder aus einem Container mit Miniaturansichten an. Die Web-App verwendet die Namespaces [Azure.Storage](/dotnet/api/azure.storage), [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) und [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) für die Interaktion mit dem Azure Storage-Dienst.

```bash
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

App Service unterstützt verschiedene Möglichkeiten zum Bereitstellen von Inhalt für eine Web-App. In diesem Tutorial stellen Sie die Web-App aus einem [öffentlichen GitHub-Beispielrepository](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs) bereit. Konfigurieren Sie die GitHub-Bereitstellung für die Web-App mit dem Befehl [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```bash
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Konfigurieren von Einstellungen für Web-Apps

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Die Beispiel-Web-App nutzt die [Azure Storage-APIs für .NET](/dotnet/api/overview/azure/storage) zum Hochladen von Bildern. Die Anmeldeinformationen des Speicherkontos werden in den App-Einstellungen für die Web-App festgelegt. Fügen Sie der bereitgestellten App mit dem Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) App-Einstellungen hinzu.

```bash
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Für die Beispiel-Web-App wird die [Azure Storage-Clientbibliothek für JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) verwendet, um Bilder hochzuladen. Die Anmeldeinformationen des Speicherkontos werden in den App-Einstellungen für die Web-App festgelegt. Fügen Sie der bereitgestellten App mit dem Befehl [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) App-Einstellungen hinzu.

```bash
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Nachdem Sie die Web-App bereitgestellt und konfiguriert haben, können Sie die Funktionalität zum Hochladen von Bildern in der App testen.

## <a name="upload-an-image"></a>Hochladen von Images

Navigieren Sie zum Testen der Web-App zur URL Ihrer veröffentlichten App. Die Standard-URL der Web-App lautet `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Wählen Sie den Bereich **Fotos hochladen** aus, um eine Datei anzugeben und hochzuladen, oder ziehen Sie eine Datei in den Bereich. Das Bild wird nicht mehr angezeigt, wenn es erfolgreich hochgeladen wurde. Der Abschnitt **Generierte Miniaturansichten** bleibt leer, bis er später in diesem Tutorial getestet wird.

![Hochladen von Fotos in .NET](media/storage-upload-process-images/figure1.png)

Im Beispielcode wird der Task `UploadFileToStorage` in der Datei *Storagehelper.cs* verwendet, um die Bilder mit der [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync)-Methode in den Container *images* des Speicherkontos hochzuladen. Das folgende Codebeispiel enthält einen Task „`UploadFileToStorage`“.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Die folgenden Klassen und Methoden werden in den vorhergehenden Tasks verwendet:

| Klasse | Methode |
|-------|--------|
| [URI](/dotnet/api/system.uri) | [Uri-Konstruktor](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential(String, String)-Konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Wählen Sie **Datei auswählen**, um eine Datei auszuwählen, und klicken Sie dann auf **Bild hochladen**. Der Abschnitt **Generierte Miniaturansichten** bleibt leer, bis er später in diesem Tutorial getestet wird.

![Hochladen von Fotos in Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

Im Beispielcode ist die Route `post` für den Upload des Bilds in einen Blobcontainer verantwortlich Die Route verwendet die Module, die bei der Verarbeitung des Uploads helfen:

- Mit [multer](https://github.com/expressjs/multer) wird die Uploadstrategie für den Routenhandler implementiert.
- [into-stream](https://github.com/sindresorhus/into-stream) konvertiert den Puffer so in einen Stream, wie es für [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-) erforderlich ist.

Wenn die Datei an die Route gesendet wird, wird ihr Inhalt im Arbeitsspeicher beibehalten, bis die Datei in den Blobcontainer hochgeladen wurde.

> [!IMPORTANT]
> Das Laden von großen Dateien in den Arbeitsspeicher kann die Leistung Ihrer Web-App beeinträchtigen. Wenn Sie erwarten, dass Benutzer große Dateien übermitteln, sollten Sie einen Stagingprozess für Dateien im Dateisystem des Webservers in Betracht ziehen und die Uploads im Blobspeicher planen. Sobald sich die Dateien im Blobspeicher befinden, können Sie sie aus dem Dateisystem des Servers entfernen.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Sicherstellen, dass das Bild im Speicherkonto angezeigt wird

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie im linken Menü **Speicherkonten** aus, und wählen Sie dann den Namen Ihres Speicherkontos aus. Wählen Sie **Container** und dann den Container **images** aus.

Stellen Sie sicher, dass das Bild im Container angezeigt wird.

![Liste der Container mit Images im Azure-Portal](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Testen der Miniaturansichtenanzeige

Zum Testen der Miniaturansichtenanzeige laden Sie ein Bild in den Container **thumbnails** hoch. Auf diese Weise soll überprüft werden, ob die App den Container **thumbnails** lesen kann.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie im linken Menü **Speicherkonten** aus, und wählen Sie dann den Namen Ihres Speicherkontos aus. Wählen Sie **Container** und dann den Container **thumbnails** aus. Klicken Sie auf **Hochladen**, um den Bereich **Blob hochladen** zu öffnen.

Wählen Sie mit der Dateiauswahl eine Datei aus, und wählen Sie dann die Option **Hochladen**.

Navigieren Sie wieder zu Ihrer App, um zu überprüfen, ob das in den Container **thumbnails** hochgeladene Bild sichtbar ist.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![Bildgrößenänderungs-App (.NET) mit neuem Bild angezeigt](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![Bildgrößenänderungs-App (Node.js) mit Anzeige eines neuen Bilds](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Im zweiten Teil der Reihe automatisieren Sie die Erstellung der Miniaturansicht, sodass Sie dieses Image nicht benötigen. Wählen Sie im Container **thumbnails** das Bild aus, das Sie hochgeladen haben, und klicken Sie anschließend auf **Löschen**, um das Bild zu entfernen.

Sie können Content Delivery Network (CDN) zum Zwischenspeichern von Inhalten aus Ihrem Azure-Speicherkonto aktivieren. Weitere Informationen finden Sie unter [Integrieren eines Azure-Speicherkontos in CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil der Reihe haben Sie erfahren, wie Sie eine Web-App für die Interaktion mit Speicher konfigurieren.

Fahren Sie mit dem zweiten Teil der Reihe fort, um sich darüber zu informieren, wie Event Grid zum Auslösen einer Azure-Funktion zur Größenänderung eines Bilds verwendet wird.

> [!div class="nextstepaction"]
> [Verwenden von Event Grid zum Auslösen einer Azure-Funktion zum Ändern der Größe eines hochgeladenen Bilds](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
