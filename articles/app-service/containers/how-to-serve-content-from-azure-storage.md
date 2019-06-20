---
title: Bereitstellen von Inhalt aus Azure Storage in Linux – App Service
description: Informationen zum Konfigurieren und Bereitstellen von Inhalten aus Azure Storage in Azure App Service unter Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 6b4e145a693aabbf1a00d732e2fd602e7c887a03
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956015"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Bereitstellen von Inhalt aus Azure Storage in App Service unter Linux

Diese Anleitung verdeutlicht die Bereitstellen statischen Inhalts in App Service unter Linux mithilfe von [Azure Storage](/azure/storage/common/storage-introduction). Zu den Vorteilen gehören sicherer Inhalt, die Portabilität des Inhalts, Zugriff auf mehrere Apps und mehrere Übertragungsmethoden. 

## <a name="prerequisites"></a>Voraussetzungen

- Eine vorhandene Web-App (App Service unter Linux oder Web-App für Container).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 oder höher).

## <a name="create-azure-storage"></a>Erstellen eines Azure Storage

> [!NOTE]
> Azure Storage ist ein nicht standardmäßiger Speicher und wird separat berechnet, nicht im Lieferumfang der Web-App enthalten.
>

Erstellen Sie ein [Azure-Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Hochladen von Dateien zu Azure Storage

Um ein lokales Verzeichnis in das Speicherkonto hochzuladen, verwenden Sie den Befehl [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) wie im folgenden Beispiel:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Verknüpfen des Speichers mit Ihrer Web-App (Vorschau)

> [!CAUTION]
> Beim Verknüpfen eines vorhandenen Verzeichnis in einer Web-App mit einem Speicherkonto werden die Inhalte des Verzeichnisses gelöscht. Wenn Sie Dateien für eine bestehende Anwendung migrieren, erstellen Sie vor Beginn eine Sicherung Ihrer Anwendung und ihres Inhalts.
>

Um ein Speicherkonto in einem Verzeichnis in Ihre App Service-App bereitzustellen, verwenden Sie den Befehl [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Als Speichertyp kann „AzureBlob“ oder „AzureFiles“ verwendet werden. Verwenden Sie „AzureBlob“ für diesen Container.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Gehen Sie genauso für alle anderen Verzeichnisse vor, die mit einem Speicherkonto verknüpft werden sollen.

## <a name="verify"></a>Überprüfen

Sobald ein Speichercontainer mit einer Web-App verknüpft ist, können Sie dies durch Ausführen des folgenden Befehls überprüfen:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Verwenden von benutzerdefiniertem Speicher in Docker Compose

Azure Storage kann über die benutzerdefinierte ID mit Apps mit mehreren Containern eingebunden werden. Führen Sie [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list) aus, um den Namen der benutzerdefinierten ID anzuzeigen.

Ordnen Sie in Ihrer Datei *docker-compose.yml* die `volumes`-Option zu `custom-id` zu. Beispiel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Web-Apps in Azure App Service](../configure-common.md)
