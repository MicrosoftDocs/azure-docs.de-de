---
title: 'Schnellstart: Erstellen eines Blobs mit der Azure-Befehlszeilenschnittstelle'
titleSuffix: Azure Storage
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie die Azure CLI verwenden, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: fa502f5ca95b1726da7f00f987b35be362ae865a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021757"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Schnellstart: Erstellen, Herunterladen und Auflisten von Blobs mit der Azure-Befehlszeilenschnittstelle

Die Azure CLI ist die Befehlszeilenumgebung von Azure und dient zum Verwalten von Azure-Ressourcen. Sie können sie in Ihrem Browser mit Azure Cloud Shell verwenden. Außerdem können Sie sie unter macOS, Linux oder Windows installieren und über die Befehlszeile ausführen. In diesem Schnellstart wird beschrieben, wie Sie die Azure CLI zum Hoch- und Herunterladen von Daten für Azure Blob Storage verwenden.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- Für diesen Artikel ist mindestens Version 2.0.46 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="authorize-access-to-blob-storage"></a>Autorisieren des Zugriffs auf Blobspeicher

Der Zugriff auf Blobspeicher kann über die Azure-Befehlszeilenschnittstelle autorisiert werden. Hierzu können Sie entweder Azure AD-Anmeldeinformationen oder den Speicherkonto-Zugriffsschlüssel verwenden. Es wird empfohlen, Azure AD-Anmeldeinformationen zu verwenden. In diesem Artikel wird die Autorisierung von Blobspeichervorgängen mithilfe von Azure AD gezeigt.

Bei Azure CLI-Befehlen für blobspeicherbezogene Datenvorgänge können Sie mithilfe des Parameters `--auth-mode` die Art der Autorisierung für einen Vorgang angeben. Legen Sie den Parameter `--auth-mode` auf `login` fest, um die Autorisierung mit Azure AD-Anmeldeinformationen zu verwenden. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure-Befehlszeilenschnittstelle](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Der Parameter `--auth-mode` wird nur von blobspeicherbezogenen Datenvorgängen unterstützt. Bei Verwaltungsvorgängen wie etwa dem Erstellen einer Ressourcengruppe oder eines Speicherkontos werden automatisch Azure AD-Anmeldeinformationen für die Autorisierung verwendet.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mit dem Befehl [az storage account create](/cli/azure/storage/account) ein allgemeines Speicherkonto. Das allgemeine Speicherkonto kann für alle vier Dienste verwendet werden: Blobs, Files, Tables und Queues.

Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Erstellen eines Containers

Blobs werden immer in einen Container hochgeladen. Gruppen von Blobs können in Containern strukturiert werden (ähnlich wie Dateien in Ordnern auf Ihrem Computer). Erstellen Sie mit dem Befehl [az storage container create](/cli/azure/storage/container) einen Container zum Speichern von Blobs.

Im folgenden Beispiel wird Ihr Azure AD-Konto genutzt, um den Vorgang zur Erstellung des Containers zu autorisieren. Weisen Sie sich vor der Erstellung des Containers selbst die Rolle [Mitwirkender an Storage-Blobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) zu. Selbst wenn Sie der Kontobesitzer sind, benötigen Sie explizite Berechtigungen, um Datenvorgänge in Ihrem Speicherkonto ausführen zu können. Weitere Informationen zum Zuweisen von Azure-Rollen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer RBAC-Rolle für den Zugriff auf Blob- und Warteschlangendaten](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Die Azure-Rollenzuweisungen können einige Minuten dauern.

Sie können auch den Schlüssel des Speicherkontos nutzen, um den Vorgang zur Erstellung des Containers zu autorisieren. Weitere Informationen zum Autorisieren von Datenvorgängen mithilfe der Azure CLI finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure-Befehlszeilenschnittstelle](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="upload-a-blob"></a>Hochladen eines Blobs

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. Die Beispiele in dieser Schnellstartanleitung zeigen, wie Sie Blockblobs verwenden.

Erstellen Sie zunächst eine Datei für den Upload in ein Blockblob. Verwenden Sie bei Verwendung der Azure Cloud Shell den folgenden Befehl, um eine Datei zu erstellen:

```bash
vi helloworld
```

Wenn die Datei geöffnet wird, drücken Sie **Einfg**. Geben Sie *Hello World* ein und drücken Sie dann **Esc**. Geben Sie danach *:x* ein und drücken Sie dann die **Eingabetaste**.

In diesem Beispiel laden Sie mithilfe des Befehls [az storage blob upload](/cli/azure/storage/blob) ein Blob in den Container hoch, den Sie im letzten Schritt erstellt haben. Es ist nicht erforderlich, einen Dateipfad anzugeben, da die Datei im Stammverzeichnis erstellt wurde. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Dabei wird das Blob erstellt, falls es nicht vorhanden ist, oder überschrieben, falls es bereits vorhanden ist. Laden Sie beliebig viele Dateien hoch, bevor Sie fortfahren.

Wenn Sie mehrere Dateien gleichzeitig hochladen möchten, können Sie den Befehl [az storage blob upload-batch](/cli/azure/storage/blob) verwenden.

## <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs in einem Container

Sie können die Blobs im Container mit dem Befehl [az storage blob list](/cli/azure/storage/blob) auflisten. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Herunterladen eines Blobs

Mit dem Befehl [az storage blob download](/cli/azure/storage/blob) können Sie das zuvor hochgeladene Blob herunterladen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Übertragen von Daten mit AzCopy

Das Befehlszeilenprogramm AzCopy bietet skriptfähige Hochleistungsdatenübertragung für Azure Storage. Mit AzCopy können Daten zwischen Blobspeicher und Azure Files übertragen werden. Weitere Informationen zu AzCopy v10 (der aktuellen Version von AzCopy) finden Sie unter [Erste Schritte mit AzCopy](../common/storage-use-azcopy-v10.md). Informationen zur Verwendung von AzCopy V10 mit Blobspeicher finden Sie unter [Übertragen von Daten mit AzCopy und Blob Storage](../common/storage-use-azcopy-blobs.md).

Im folgenden Beispiel wird AzCopy verwendet, um eine lokale Datei in ein Blob hochzuladen. Denken Sie daran, die Beispielwerte durch Ihre eigenen Werte zu ersetzen:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die im Rahmen dieser Schnellstartanleitung erstellten Ressourcen einschließlich des Speicherkontos löschen möchten, löschen Sie die Ressourcengruppe mithilfe des Befehls [az group delete](/cli/azure/group). Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie Dateien zwischen einem lokalen Dateisystem und einem Container in Azure Blobspeicher übertragen. Weitere Informationen zur Verwendung des Blobspeichers mit der Azure CLI finden Sie im Artikel mit den Azure CLI-Beispielen für Blobspeicher.

> [!div class="nextstepaction"]
> [Azure CLI-Beispiele für Azure Blob Storage](./storage-samples-blobs-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)