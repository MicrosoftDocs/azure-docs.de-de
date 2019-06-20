---
title: Einbinden eines Azure Files-Volumes in Azure Container Instances
description: Erfahren Sie, wie Sie ein Azure Files-Volume einbinden, sodass der Zustand bei Azure Container Instances beibehalten wird.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/05/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 365264d40554f45533e2ddf0aeb9d85f3e8f8d2d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60564020"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Einbinden einer Azure-Dateifreigabe in Azure Container Instances

Standardmäßig ist Azure Container Instances zustandslos. Wenn der Container abstürzt oder beendet wird, gehen alle Zustände verloren. Um den Zustand nach Ablauf der Lebensdauer des Containers beizubehalten, müssen Sie ein Volume aus einem externen Speicher einbinden. In diesem Artikel wird das Einbinden einer mit [Azure Files](../storage/files/storage-files-introduction.md) erstellten Azure-Dateifreigabe für die Verwendung mit Azure Container Instances veranschaulicht. Azure Files bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll Server Message Block (SMB) zugegriffen werden kann. Durch das Verwenden einer Azure-Dateifreigabe mit Azure Container Instances werden Dateifreigabefeatures bereitgestellt, die Azure-Dateifreigaben mit virtuellen Azure-Computern ähneln.

> [!NOTE]
> Zurzeit ist das Einbinden einer Azure-Dateifreigabe in Linux-Container eingeschränkt. Bis alle Features auch für Windows-Container verfügbar sind, finden Sie die aktuellen Plattformunterschiede unter [Kontingente und Regionsverfügbarkeit für Azure Container Instances](container-instances-quotas.md).

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe

Bevor Sie eine Azure-Dateifreigabe in Azure Container Instances einbinden, müssen Sie sie erstellen. Führen Sie das folgende Skript aus, um ein Speicherkonto zum Hosten der Dateifreigabe und die Freigabe selbst zu erstellen. Der Name des Speicherkontos muss global eindeutig sein, daher fügt das Skript der Basis-Zeichenfolge einen Zufallswert hinzu.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Erhalten der Zugriffsinformationen für das Azure-Speicherkonto

Um eine Azure-Dateifreigabe als Volume in Azure Container Instances einzubinden, benötigen Sie drei Werte: den Namen des Speicherkontos, den Freigabenamen und den Speicherzugriffsschlüssel.

Wenn Sie das obige Skript verwendet haben, wurde der Name des Speicherkontos in der Variable $ACI_PERS_STORAGE_ACCOUNT_NAME gespeichert. Um den Kontonamen einzusehen, geben Sie Folgendes ein:

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Der Freigabename ist bereits bekannt (im Skript oben festgelegt als *acishare*), daher verbleibt nur der Speicherkontoschlüssel, der mit dem folgenden Befehl gesucht werden kann:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>Bereitstellen des Containers und Einbinden des Volumes

Um eine Azure-Dateifreigabe als Volume in einen Container einzubinden, geben Sie die Freigabe und den Einbindungspunkt des Volumes bei der Erstellung des Containers mit [az container create][az-container-create] an. Wenn Sie die vorherigen Schritte ausgeführt haben, können Sie die Freigabe, die Sie zuvor erstellt haben, einbinden, indem Sie mit dem folgenden Befehl einen Container erstellen:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

Der Wert `--dns-name-label` muss innerhalb der Azure-Region, in der Sie die Containerinstanz erstellen, eindeutig sein. Aktualisieren Sie den Wert im vorherigen Befehl, wenn Sie beim Ausführen des Befehls eine Fehlermeldung bezüglich der **DNS-Namensbezeichnung** erhalten.

## <a name="manage-files-in-mounted-volume"></a>Verwalten von Dateien in eingebundenen Datenträgern

Nachdem der Container gestartet wurde, können Sie mithilfe der einfachen Web-App, die über das Microsoft-Image [aci-hellofiles][aci-hellofiles] bereitgestellt wird, kleine Textdateien in der Azure-Dateifreigabe unter dem angegebenen Einbindungspfad erstellen. Beziehen Sie den vollqualifizierten Domänennamen (FQDN) für die Web-App mit dem Befehl [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

Mithilfe des [Azure-Portals][portal] oder eines Tools wie [Microsoft Azure Storage-Explorer][storage-explorer] können Sie die in die Dateifreigabe geschriebene Datei abrufen und überprüfen.

## <a name="mount-multiple-volumes"></a>Einbinden mehrerer Volumes

Sie können mehrere Volumes in eine Containerinstanz einbinden, indem Sie zum Bereitstellen eine [Azure Resource Manager-Vorlage](/azure/templates/microsoft.containerinstance/containergroups) oder eine YAML-Datei verwenden.

Stellen Sie zum Verwenden einer Vorlage die Freigabedetails bereit, und definieren Sie die Volumes, indem Sie das Array `volumes` im Abschnitt `properties` der Vorlage ausfüllen. Wenn Sie z.B. zwei Azure Files-Freigaben mit den Namen *share1* und *share2* im Speicherkonto *MyStorageAccount* erstellt haben, wird das Array `volumes` ähnlich wie im folgenden Beispiel aussehen:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Füllen Sie als Nächstes für jeden Container in der Containergruppe, in den Sie die Volumes einbinden möchten, das Array `volumeMounts` im Abschnitt `properties` der Containerdefinition auf. Dadurch werden beispielsweise die beiden zuvor definierten Volumes *myvolume1* und *myvolume2* eingebunden:

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

Ein Beispiel für die Bereitstellung der Containerinstanzen mit einer Azure Resource Manager-Vorlage finden Sie unter [Bereitstellen einer Containergruppe](container-instances-multi-container-group.md). Ein Beispiel mit einer YAML-Datei finden Sie unter [Bereitstellen einer Gruppe mit mehreren Containern mit YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie andere Volumetypen in Azure Container Instances bereitgestellt werden:

* [Einbinden eines emptyDir-Volumes in Azure Container Instances](container-instances-volume-emptydir.md)
* [Einbinden eines gitRepo-Volumes in Azure Container Instances](container-instances-volume-gitrepo.md)
* [Einbinden eines geheimen Volumes in Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show