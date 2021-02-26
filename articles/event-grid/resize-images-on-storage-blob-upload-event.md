---
title: 'Tutorial: Verwenden von Azure Event Grid zum Automatisieren der Größenänderung hochgeladener Bilder'
description: 'Tutorial: Azure Event Grid kann bei Blob-Uploads in Azure Storage ausgelöst werden. Sie können diese Funktion verwenden, um in Azure Storage hochgeladene Bilddateien an andere Dienste (z. B. Azure Functions) für die Größenänderung und andere Optimierungen zu senden.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: ca231fc65162fe38f4dcb8b8d5677ef42c7807bb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550512"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>Tutorial: Automatisieren der Größenänderung von hochgeladenen Bildern mit Event Grid

[Azure Event Grid](overview.md) ist ein Ereignisdienst für die Cloud. Event Grid ermöglicht Ihnen das Erstellen von Abonnements für Ereignisse, die von Azure-Diensten oder Drittanbieterressourcen ausgelöst werden.  

Dieses Tutorial stellt Teil zwei einer Reihe von Tutorials zu Storage dar. Es erweitert das [vorherige Tutorial zu Storage][previous-tutorial], um serverlose automatische Miniaturansichtengenerierung mithilfe von Azure Event Grid und Azure Functions hinzuzufügen. Event Grid ermöglicht, dass [Azure Functions](../azure-functions/functions-overview.md) auf Ereignisse von [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) reagieren kann und Miniaturansichten hochgeladener Bilder generiert. Ein Ereignisabonnement wird für das Blob Storage-Erstellungsereignis erstellt. Wenn ein Blob einem bestimmten Blob Storage-Container hinzugefügt wird, wird ein Funktionsendpunkt aufgerufen. Aus Event Grid an die Funktionsbindung übergebene Daten werden für den Zugriff auf das Blob und zum Generieren der Miniaturansicht verwendet.

Sie verwenden die Azure-CLI und das Azure-Portal, um die Größenänderungsfunktionalität einer vorhandenen Bildupload-App hinzuzufügen.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![Screenshot, der eine veröffentlichte Web-App in einem Browser für das \.NET v12 SDK zeigt.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![Screenshot, der eine veröffentlichte Web-App in einem Browser für das \.NET v10 SDK zeigt.](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure-Speicherkontos
> * Bereitstellen von serverlosem Code mit Azure Functions
> * Erstellen eines Blob Storage-Ereignisabonnements in Event Grid

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Für dieses Tutorial benötigen Sie Folgendes:

Sie müssen das vorherige Blob Storage-Tutorial abgeschlossen haben: [Hochladen von Bilddaten in die Cloud mit Azure Storage][previous-tutorial].

Sie benötigen ein [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). Dieses Tutorial funktioniert nicht mit dem **kostenlosen** Abonnement. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, erfordert dieses Tutorial die Azure CLI-Version 2.0.14 oder höher. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Falls Sie nicht Cloud Shell verwenden, müssen Sie sich erst mithilfe von `az login` anmelden.

Falls Sie den Event Grid-Ressourcenanbieter noch nicht in Ihrem Abonnement registriert haben, stellen Sie sicher, dass er registriert ist.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Für Azure Functions ist ein allgemeines Speicherkonto erforderlich. Erstellen Sie zusätzlich zu dem Blobspeicherkonto, das Sie im vorherigen Tutorial erstellt haben, ein separates allgemeines Speicherkonto in der Ressourcengruppe. Verwenden Sie dazu den Befehl [az storage account create](/cli/azure/storage/account). Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten.

1. Legen Sie eine Variable fest, die den Namen der Ressourcengruppe enthält, die Sie im vorherigen Tutorial erstellt haben.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. Legen Sie eine Variable mit dem Speicherort für zu erstellende Ressourcen fest. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Legen Sie eine Variable für den Namen des neuen Speicherkontos fest, das für Azure Functions erforderlich ist.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Erstellen Sie das Speicherkonto für die Azure-Funktion.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App  

Sie müssen über eine Funktions-App verfügen, die die Ausführung Ihrer Funktion in Azure hostet. Die Funktionen-App bietet eine Umgebung für die serverlose Ausführung des Funktionscodes. Erstellen Sie eine Funktionen-App mithilfe des Befehls [az functionapp create](/cli/azure/functionapp).

Geben Sie im Befehl unten Ihren eigenen eindeutigen Funktions-App-Namen an. Da der Name der Funktions-App als DNS-Standarddomäne für die Funktions-App verwendet wird, muss der Name für alle Apps in Azure eindeutig sein.

1. Geben Sie einen Namen für die Funktions-App an, die erstellt werden soll.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Erstellen Sie die Azure-Funktion.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

Konfigurieren Sie jetzt die Funktions-App für die Verbindung mit dem Blob-Speicherkonto, das Sie im [vorherigen Tutorial][previous-tutorial] erstellt haben.

## <a name="configure-the-function-app"></a>Konfigurieren der Funktions-App

Die Funktion benötigt Anmeldeinformationen für das Blobspeicherkonto, die den Anwendungseigenschaften der Funkions-App mit dem Befehl [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) hinzugefügt werden.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

Die Einstellung `FUNCTIONS_EXTENSION_VERSION=~2` legt fest, dass die Funktions-App unter der Version 2.x der Azure Functions-Runtime ausgeführt wird.

Sie können nun ein Funktionscodeprojekt für diese Funktions-App bereitstellen.

## <a name="deploy-the-function-code"></a>Bereitstellen des Funktionscodes 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Die C#-Beispielfunktion für die Größenänderung ist auf [GitHub](https://github.com/Azure-Samples/function-image-upload-resize) verfügbar. Stellen Sie dieses Codeprojekt für die Funktions-App mithilfe des Befehls [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) bereit.

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Die Node.js-Beispielfunktion für die Größenänderung ist auf [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10) verfügbar. Stellen Sie dieses Funktionscodeprojekt für die Funktions-App mithilfe des Befehls [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) bereit.

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

Die Funktion für die Bildgrößenänderung wird durch HTTP-Anforderungen ausgelöst, die sie vom Event Grid-Dienst empfängt. Erstellen Sie ein Ereignisabonnement, um Event Grid zu informieren, dass Sie diese Benachrichtigungen unter der URL Ihrer Funktion erhalten möchten. Für dieses Tutorial abonnieren Sie durch Blobs erstellte Ereignisse.

Die von der Event Grid-Benachrichtigung an die Funktion übergebenen Daten enthalten die URL des Blobs. Diese URL wird wiederum an die Eingabebindung übergeben, um das hochgeladene Bild aus dem Blob-Speicher abzurufen. Die Funktion generiert eine Miniaturansicht und schreibt den sich ergebenden Datenstrom in einen separaten Container in Blob Storage.

In diesem Projekt wird `EventGridTrigger` als Triggertyp verwendet. Es wird empfohlen, anstelle generischer HTTP-Trigger den Event Grid-Trigger zu verwenden. Event Grid überprüft Event Grid-Funktionstrigger automatisch. Bei generischen HTTP-Triggern müssen Sie die [Überprüfungsantwort](security-authentication.md) implementieren.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Weitere Informationen zu dieser Funktion finden Sie unter in den [Dateien „function.json“ und „run.csx“](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions).

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Weitere Informationen zu dieser Funktion finden Sie in den [Dateien „function.json“ und „index.js“](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail).

---

Der Funktionsprojektcode wird direkt aus dem öffentlichen Beispielrepository bereitgestellt. Weitere Informationen zu den Bereitstellungsoptionen für Azure Functions finden Sie unter [Continuous Deployment für Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Erstellen eines Ereignisabonnements

Ein Ereignisabonnement gibt an, welche vom Anbieter generierten Ereignisse an einen bestimmten Endpunkt gesendet werden sollen. In diesem Fall wird der Endpunkt durch Ihre Funktion zur Verfügung gestellt. Führen Sie die folgenden Schritte durch, um ein Ereignisabonnement zu erstellen, dass Benachrichtigungen an Ihre Funktion im Azure-Portal sendet:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) oben auf der Seite nach `Function App`, wählen Sie dies aus, und wählen Sie die soeben von Ihnen erstellte Funktions-App aus. Wählen Sie **Funktionen** und dann die Funktion **Miniaturansicht** aus.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="Auswählen der Miniaturansichtsfunktion im Portal":::

1.  Wählen Sie **Integration auswählen** aus, wählen Sie dann den **Event Grid-Trigger** aus, und wählen Sie **Event Grid-Abonnement erstellen** aus.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Navigieren zu „Event Grid-Abonnement hinzufügen“ im Azure-Portal" :::

1. Verwenden Sie die in der Tabelle angegebenen Einstellungen für das Ereignisabonnement.
    
    ![Erstellen eines Ereignisabonnements aus der Funktion im Azure-Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **Name** | imageresizersub | Der Name, der Ihr neues Ereignisabonnement angibt. |
    | **Thementyp** | Speicherkonten | Wählen Sie den Speicherkonto-Ereignisanbieter aus. |
    | **Abonnement** | Ihr Azure-Abonnement | Ihr aktuelles Azure-Abonnement ist standardmäßig ausgewählt. |
    | **Ressourcengruppe** | myResourceGroup | Wählen Sie **Vorhandene verwenden** und anschließend die Ressourcengruppe aus, die Sie in diesem Tutorial verwendet haben. |
    | **Ressource** | Ihr Blob Storage-Konto | Wählen Sie das Blob Storage-Konto aus, das Sie erstellt haben. |
    | **Name des Systemthemas** | imagestoragesystopic | Geben Sie einen Namen für das Systemthema ein. Informationen zu Systemthemen finden Sie unter [Übersicht über Systemthemen](system-topics.md). |    
    | **Ereignistypen** | Blob erstellt | Deaktivieren Sie alle Typen außer **Blob erstellt**. Nur Ereignistypen mit `Microsoft.Storage.BlobCreated` werden an die Funktion übergeben. |
    | **Endpunkttyp** | Automatisch generiert | Vordefiniert als **Azure-Funktion** |
    | **Endpunkt** | Automatisch generiert | Name der Funktion. In diesem Fall: **Thumbnail**. |

1. Wechseln Sie zur Registerkarte **Filter**, und führen Sie die folgenden Aktionen durch:
    1. Wählen Sie die Option **Betrefffilter aktivieren** aus.
    1. Geben Sie für **Betreff beginnt mit** den folgenden Wert ein: **/blobServices/default/containers/images/** .

        ![Festlegen eines Filters für das Ereignisabonnement](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. Wählen Sie **Erstellen** aus, um das Ereignisabonnement hinzuzufügen. Dadurch wird ein Ereignisabonnement erstellt, das die Funktion `Thumbnail` auslöst, wenn dem Container `images` ein Blob hinzugefügt wird. Die Funktion ändert die Größe der Bilder und fügt sie dem Container `thumbnails` hinzu.

Nachdem die Back-End-Dienste konfiguriert wurden, testen Sie die Funktion für die Größenänderung von Bildern in der Beispiel-Web-App.

## <a name="test-the-sample-app"></a>Testen der Beispiel-App

Navigieren Sie zum Testen der Größenänderung von Bildern in der Web-App zur URL Ihrer veröffentlichten App. Die Standard-URL der Web-App lautet `https://<web_app>.azurewebsites.net`.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

Klicken Sie auf den Bereich **Fotos hochladen**, um eine Datei auszuwählen und hochzuladen. Sie können auch ein Foto in diese Region ziehen.

Beachten Sie, dass eine Kopie des hochgeladenen Bilds im Karussell **Generierte Miniaturansichten** angezeigt wird, nachdem das hochgeladene Bild nicht mehr angezeigt wird. Die Größe dieses Bilds wurde von der Funktion angepasst, und das Bild wurde dem Container *thumbnails* hinzugefügt und vom Webclient heruntergeladen.

![Screenshot, der eine veröffentlichte Web-App namens „ImageResizer“ in einem Browser für das \.NET v12 SDK zeigt.](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

Klicken Sie auf **Datei auswählen**, um eine Datei auszuwählen, und klicken Sie dann auf **Bild hochladen**. Ist der Upload erfolgreich, wird im Browser eine Erfolgsmeldung angezeigt. Klicken Sie auf den Link, um zur Startseite zurückzukehren. Eine Kopie des hochgeladenen Bilds wird im Bereich **Generierte Miniaturansichten** angezeigt. (Wird das Bild zuerst nicht angezeigt, laden Sie die Seite erneut.) Die Größe dieses Bilds wurde von der Funktion angepasst, und das Bild wurde dem Container *thumbnails* hinzugefügt und vom Webclient heruntergeladen.

![Veröffentlichte Web-App im Browser](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines neuen Azure Storage-Kontos
> * Bereitstellen von serverlosem Code mit Azure Functions
> * Erstellen eines Blob Storage-Ereignisabonnements in Event Grid

Fahren Sie mit Teil 3 der Tutorialreihe zu Storage fort, um zu erfahren, wie der Zugriff auf das Speicherkonto gesichert wird.

> [!div class="nextstepaction"]
> [Sichern des Zugriffs auf die Daten einer Anwendung in der Cloud](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Weitere Informationen zu Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
+ Ein weiteres Tutorial zu Azure Functions finden Sie unter [Erstellen einer Funktion, die in Azure Logic Apps integriert ist](../azure-functions/functions-twitter-email.md).

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
