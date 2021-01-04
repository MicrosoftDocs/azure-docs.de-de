---
title: 'Tutorial: Wiederherstellen eines virtuellen Computers mit der Azure CLI'
description: Erfahren Sie, wie Sie mit Backup und Recovery Services einen Datenträger wiederherstellen und eine Wiederherstellung eines virtuellen Computers in Azure erstellen.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 45e171e064cbd8be5418e20784e6034830d27fe9
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566672"
---
# <a name="restore-a-vm-with-azure-cli"></a>Wiederherstellen eines virtuellen Computers mit der Azure CLI

Azure Backup erstellt Wiederherstellungspunkte, die in geografisch redundanten Recovery-Tresoren gespeichert werden. Wenn Sie eine Wiederherstellung von einem Wiederherstellungspunkt durchführen, können Sie den gesamten virtuellen Computer oder einzelne Dateien wiederherstellen. In diesem Artikel wird das Wiederherstellen eines vollständigen virtuellen Computers mithilfe der Befehlszeilenschnittstelle erläutert. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Auflisten und Auswählen von Wiederherstellungspunkten
> * Wiederherstellen von Datenträgern aus einem Wiederherstellungspunkt
> * Erstellen virtueller Computer von wiederhergestellten Datenträgern

Informationen dazu, wie Sie mithilfe von PowerShell einen Datenträger wiederherstellen und einen wiederhergestellten virtuellen Computer erstellen, finden Sie unter [Verwenden von AzureRM.RecoveryServices.Backup-Cmdlets zum Sichern virtueller Computer](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.18 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

 - Dieses Tutorial erfordert eine Linux-VM, die mit Azure Backup geschützt wurde. Um ein versehentliches Löschen der VM und den Wiederherstellungsprozess zu simulieren, erstellen Sie einen virtuellen Computer von einem Datenträger in einem Wiederherstellungspunkt. Wenn Sie eine Linux-VM benötigen, die mit Azure Backup geschützt wurde, lesen Sie [Sichern eines virtuellen Computers in Azure per CLI](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Übersicht über Azure Backup

Wenn Azure eine Sicherung initiiert, erstellt die Sicherungserweiterung auf dem virtuellen Computer eine Zeitpunkt-Momentaufnahme. Die Sicherungserweiterung wird auf dem virtuellen Computer installiert, wenn die erste Sicherung angefordert wird. Azure Backup kann auch eine Momentaufnahme des zugrunde liegenden Speichers erstellen, falls die VM zum Zeitpunkt der Sicherung nicht ausgeführt wird.

Standardmäßig führt Azure Backup eine konsistente Sicherung des Dateisystems durch. Nachdem Azure Backup die Momentaufnahme erstellt hat, werden die Daten in den Recovery Services-Tresor übertragen. Um die Effizienz zu maximieren, bestimmt und überträgt Azure Backup nur diejenigen Datenblöcke, die seit der vorherigen Sicherung geändert wurden.

Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt.

## <a name="list-available-recovery-points"></a>Auflisten der verfügbaren Wiederherstellungspunkte

Um einen Datenträger wiederherzustellen, wählen Sie einen Wiederherstellungspunkt als Quelle für die Wiederherstellungsdaten aus. Da die Standardrichtlinie jeden Tag einen Wiederherstellungspunkt erstellt und für 30 Tage aufbewahrt, können Sie einen Satz von Wiederherstellungspunkten beibehalten. So haben Sie die Möglichkeit, einen bestimmten Zeitpunkt für die Wiederherstellung auszuwählen.

Um eine Liste der verfügbaren Wiederherstellungspunkte anzuzeigen, verwenden Sie [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). Der **Name** des Wiederherstellungspunkts wird verwendet, um den Datenträger wiederherzustellen. In diesem Tutorial verwenden wir den aktuellsten verfügbaren Wiederherstellungspunkt. Der Parameter `--query [0].name` wählt den letzten Wiederherstellungspunkt wie folgt aus:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Wiederherstellen von VM-Datenträgern

> [!IMPORTANT]
> Es wird dringend empfohlen, mindestens die Version 2.0.74 der Azure-Befehlszeilenschnittstelle zu verwenden, um alle Vorteile einer schnellen Wiederherstellung nutzen zu können – einschließlich der Wiederherstellung verwalteter Datenträger. Es ist am besten, wenn Sie immer die neueste Version verwenden.

### <a name="managed-disk-restore"></a>Wiederherstellung verwalteter Datenträger

Wenn der gesicherte virtuelle Computer über verwaltete Datenträger verfügt und Sie verwaltete Datenträger aus dem Wiederherstellungspunkt wiederherstellen möchten, müssen Sie zunächst ein Azure-Speicherkonto angeben. Dieses Speicherkonto dient zum Speichern der VM-Konfiguration und der Bereitstellungsvorlage für die spätere Bereitstellung des virtuellen Computers auf der Grundlage der wiederhergestellten Datenträger. Anschließend müssen Sie eine Zielressourcengruppe für die Wiederherstellung der verwalteten Datenträger angeben.

1. Verwenden Sie zum Erstellen eines Speicherkontos [az storage account create](/cli/azure/storage/account#az-storage-account-create). Der Name des Speicherkontos darf nur Kleinbuchstaben enthalten und muss global eindeutig sein. Ersetzen Sie *mystorageaccount* durch Ihren eigenen eindeutigen Namen:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Stellen Sie den Datenträger mit [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks) aus dem Wiederherstellungspunkt wieder her. Ersetzen Sie *mystorageaccount* durch den Namen des Speicherkontos, das Sie mit dem vorherigen Befehl erstellt haben. Ersetzen Sie *myRecoveryPointName* durch den Namen des Wiederherstellungspunkts, den Sie über die Ausgabe aus dem vorherigen Befehl [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) erhalten haben. ***Geben Sie auch die Zielressourcengruppe an, in der die verwalteten Datenträger wiederhergestellt werden** _.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Ohne Angabe von _ *target-resource-group** werden die verwalteten Datenträger als nicht verwaltete Datenträger für das angegebene Speicherkonto wiederhergestellt. Dies hat erhebliche Auswirkungen auf die Wiederherstellungszeit, da die Dauer der Datenträgerwiederherstellung ganz vom angegebenen Speicherkonto abhängt. Nur bei Angabe des Parameters „target-resource-group“ können Sie die Vorteile einer sofortigen Wiederherstellung nutzen. Geben Sie den Parameter **target-resource-group** nicht an, wenn verwaltete Datenträger als nicht verwaltete Datenträger wiederhergestellt werden sollen. In diesem Fall geben Sie stattdessen wie unten gezeigt den Parameter **restore-as-unmanaged-disk** an. Dieser Parameter ist ab Az 3.4.0 verfügbar.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Dadurch werden verwaltete Datenträger als nicht verwaltete Datenträger im angegebenen Speicherkonto wiederhergestellt, und die Funktion für die sofortige Wiederherstellung wird nicht genutzt. In zukünftigen Versionen der CLI wird die Angabe des Parameters **target-resource-group** oder **restore-as-unmanaged-disk** obligatorisch sein.

### <a name="unmanaged-disks-restore"></a>Wiederherstellung nicht verwalteter Datenträger

Wenn der gesicherte virtuelle Computer über nicht verwaltete Datenträger verfügt und Sie Datenträger aus dem Wiederherstellungspunkt wiederherstellen möchten, müssen Sie zunächst ein Azure-Speicherkonto angeben. Dieses Speicherkonto dient zum Speichern der VM-Konfiguration und der Bereitstellungsvorlage für die spätere Bereitstellung des virtuellen Computers auf der Grundlage der wiederhergestellten Datenträger. Standardmäßig werden die nicht verwalteten Datenträger in ihren ursprünglichen Speicherkonten wiederhergestellt. Wenn Sie alle nicht verwalteten Datenträger an einem zentralen Ort wiederherstellen möchten, kann das angegebene Speicherkonto auch als Stagingspeicherort für diese Datenträger verwendet werden.

Der wiederhergestellte Datenträger wird in nachfolgenden Schritten zum Erstellen eines virtuellen Computers verwendet.

1. Verwenden Sie zum Erstellen eines Speicherkontos [az storage account create](/cli/azure/storage/account#az-storage-account-create). Der Name des Speicherkontos darf nur Kleinbuchstaben enthalten und muss global eindeutig sein. Ersetzen Sie *mystorageaccount* durch Ihren eigenen eindeutigen Namen:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Stellen Sie den Datenträger mit [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks) aus dem Wiederherstellungspunkt wieder her. Ersetzen Sie *mystorageaccount* durch den Namen des Speicherkontos, das Sie mit dem vorherigen Befehl erstellt haben. Ersetzen Sie *myRecoveryPointName* durch den Namen des Wiederherstellungspunkts, den Sie über die Ausgabe aus dem vorherigen Befehl [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) erhalten haben:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Wie bereits erwähnt, werden die nicht verwalteten Datenträger standardmäßig in ihrem ursprünglichen Speicherkonto wiederhergestellt. Dadurch wird eine optimale Wiederherstellungsleistung sichergestellt. Wenn jedoch alle nicht verwalteten Datenträger in einem bestimmten Speicherkonto wiederhergestellt werden müssen, verwenden Sie das entsprechende Flag, wie weiter unten gezeigt.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Die Ausgabe ähnelt dem folgenden Beispiel, bei dem für den Wiederherstellungsauftrag der Status *InProgress* angezeigt wird:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Wenn der *Status* des Wiederherstellungsauftrags *Abgeschlossen* lautet, wurden die erforderlichen Informationen (VM-Konfiguration und Bereitstellungsvorlage) im Speicherkonto wiederhergestellt.

## <a name="create-a-vm-from-the-restored-disk"></a>Erstellen virtueller Computer von wiederhergestellten Datenträgern

Im letzten Schritt muss auf der Grundlage der verwalteten Datenträger ein virtueller Computer erstellt werden. Zum Erstellen des virtuellen Computers können Sie die Bereitstellungsvorlage verwenden, die in das angegebene Speicherkonto heruntergeladen wurde.

### <a name="fetch-the-job-details"></a>Abrufen der Auftragsdetails

Aus den resultierenden Auftragsdetails ergibt sich der Vorlagen-URI, der abgefragt und bereitgestellt werden kann. Verwenden Sie den Befehl „job show“, um weitere Details für den ausgelösten Wiederherstellungsauftrag anzuzeigen.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Die Ausgabe dieser Abfrage enthält sämtliche Details, für uns ist allerdings nur der Speicherkontoinhalt interessant. Wir können die [Abfragefunktion](/cli/azure/query-azure-cli) der Azure-Befehlszeilenschnittstelle verwenden, um die relevanten Details abzurufen.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Abrufen der Bereitstellungsvorlage

Auf die Vorlage kann nicht direkt zugegriffen werden, da sie sich unter dem Speicherkonto eines Kunden und dem angegebenen Container befindet. Wir benötigen die gesamte URL sowie ein temporäres SAS-Token, um auf diese Vorlage zugreifen zu können.

Extrahieren Sie zunächst den Vorlagen-Blob-URI aus den Auftragsdetails.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Der Vorlagen-Blob-URI hat das folgende Format. Extrahieren Sie den Vorlagennamen.

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Der Vorlagenname aus dem obigen Beispiel ist also ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json```, und der Containername lautet ```myVM-daa1931199fd4a22ae601f46d8812276```.

Rufen Sie als Nächstes das SAS-Token für diesen Container und die Vorlage ab. Eine entsprechende Anleitung finden Sie [hier](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment).

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Bereitstellen der Vorlage zur Erstellung des virtuellen Computers

Stellen Sie nun die Vorlage bereit, um den virtuellen Computer zu erstellen. Eine entsprechende Anleitung finden Sie [hier](../azure-resource-manager/templates/deploy-cli.md).

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Um zu überprüfen, ob Ihr virtueller Computer von dem wiederhergestellten Datenträger erstellt wurde, listen Sie mit [az vm list](/cli/azure/vm#az-vm-list) die VMs in Ihrer Ressourcengruppe auf:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Datenträger von einem Wiederherstellungspunkt wiederhergestellt und dann einen virtuellen Computer von diesem Datenträger erstellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
>
> * Auflisten und Auswählen von Wiederherstellungspunkten
> * Wiederherstellen von Datenträgern aus einem Wiederherstellungspunkt
> * Erstellen virtueller Computer von wiederhergestellten Datenträgern

Fahren Sie mit dem nächsten Tutorial fort, in dem Sie mehr über die Wiederherstellung einzelner Dateien aus einem Wiederherstellungspunkt erfahren.

> [!div class="nextstepaction"]
> [Wiederherstellen von Dateien auf einem virtuellen Computer in Azure](tutorial-restore-files.md)
