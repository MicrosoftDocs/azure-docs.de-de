---
title: Zugreifen auf Azure Blob Storage über Azure Databricks und Azure Key Vault
description: In diesem Tutorial erfahren Sie, wie Sie unter Verwendung eines in Azure Key Vault gespeicherten Geheimnisses von Azure Databricks aus auf Azure Blob Storage zugreifen.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: ba80d78cbc7d34b1496daffbd489a1d0dbfed8b4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285657"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Tutorial: Zugreifen auf Azure Blob Storage über Azure Databricks und Azure Key Vault

In diesem Tutorial erfahren Sie, wie Sie unter Verwendung eines in Azure Key Vault gespeicherten Geheimnisses von Azure Databricks aus auf Azure Blob Storage zugreifen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Speicherkontos und eines Blobcontainers mithilfe der Azure-Befehlszeilenschnittstelle
> * Erstellen einer Key Vault-Instanz und Festlegen eines Geheimnisses
> * Erstellen eines Azure Databricks-Arbeitsbereichs und Hinzufügen eines Key Vault-Geheimnisbereichs
> * Zugreifen auf Ihren Blobcontainer über den Azure Databricks-Arbeitsbereich

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest), bevor Sie mit diesem Tutorial beginnen.

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Erstellen eines Speicherkontos und eines Blobcontainers mithilfe der Azure-Befehlszeilenschnittstelle

Erstellen Sie für die Verwendung von Blobs zunächst ein universelles Speicherkonto. Sollten Sie über keine [Ressourcengruppe](/cli/azure/group?view=azure-cli-latest#az-group-create) verfügen, erstellen Sie eine, bevor Sie den Befehl ausführen. Mit dem folgenden Befehl werden die Metadaten des Speichercontainers erstellt und angezeigt. Notieren Sie sich die **ID**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Konsolenausgabe des obigen Befehls. Die ID ist für den Endbenutzer grün hervorgehoben.](../media/databricks-command-output-1.png)

Weisen Sie sich selbst die Rolle [Mitwirkender an Storage-Blobdaten](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) zu, um einen Container zum Hochladen des Blobs erstellen zu können. In diesem Beispiel wird die Rolle dem weiter oben erstellten Speicherkonto zugewiesen.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Nachdem Sie dem Speicherkonto die Rolle zugewiesen haben, können Sie einen Container für Ihr Blob erstellen.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Nach Erstellung des Containers kann ein Blob (Datei Ihrer Wahl) in diesen Container hochgeladen werden. In diesem Beispiel wird eine TXT-Datei mit „helloworld“ hochgeladen.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Listen Sie die Blobs im Container auf, um sich zu vergewissern, dass es im Container enthalten ist.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Konsolenausgabe des obigen Befehls. Sie enthält die Datei, die soeben im Container gespeichert wurde.](../media/databricks-command-output-2.png)

Rufen Sie mithilfe des folgenden Befehls den Wert von **key1** für Ihren Speichercontainer ab. Notieren Sie sich den Wert.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Konsolenausgabe des obigen Befehls. Der Wert von „key1“ ist durch ein grünes Kästchen hervorgehoben.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Erstellen einer Key Vault-Instanz und Festlegen eines Geheimnisses

Der folgende Befehl dient zum Erstellen einer Key Vault-Instanz. Durch diesen Befehl werden auch die Metadaten der Key Vault-Instanz angezeigt. Notieren Sie sich die Werte für **ID** und **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Abbildung](../media/databricks-command-output-4.png)
![Konsolenausgabe des obigen Befehls. ID und Tresor-URI sind für den Benutzer jeweils grün hervorgehoben.](../media/databricks-command-output-5.png)

Verwenden Sie den folgenden Befehl, um das Geheimnis zu erstellen. Legen Sie den Wert des Geheimnisses auf den Wert von **key1** aus Ihrem Speicherkonto fest.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Erstellen eines Azure Databricks-Arbeitsbereichs und Hinzufügen eines Key Vault-Geheimnisbereichs

Die Schritte in diesem Abschnitt können nicht über die Befehlszeile durchgeführt werden. Verwenden Sie [diese Anleitung](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Für Folgendes muss das [Azure-Portal](https://ms.portal.azure.com/#home) verwendet werden:

1. Erstellen Ihrer Azure Databricks-Ressource
1. Starten Ihres Arbeitsbereichs
1. Erstellen eines Key Vault-basierten Geheimnisbereichs

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Zugreifen auf Ihren Blobcontainer über den Azure Databricks-Arbeitsbereich

Die Schritte in diesem Abschnitt können nicht über die Befehlszeile durchgeführt werden. Verwenden Sie [diese Anleitung](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Für Folgendes muss der Azure Databricks-Arbeitsbereich verwendet werden:

1. Erstellen eines **neuen Clusters**
1. Erstellen eines **neuen Notebooks**
1. Ausfüllen der entsprechenden Felder im Python-Skript
1. Ausführen des Python-Skripts

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Nächste Schritte

Stellen Sie sicher, dass Ihre Key Vault-Instanz wiederherstellbar ist:
> [!div class="nextstepaction"]
> [Bereinigen Ihres eigenständigen Clusters](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)