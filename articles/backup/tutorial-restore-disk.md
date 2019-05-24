---
title: Wiederherstellen eines VM-Datenträgers mit Azure Backup
description: Erfahren Sie, wie Sie mit Backup und Recovery Services einen Datenträger wiederherstellen und eine Wiederherstellung eines virtuellen Computers in Azure erstellen.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c3fcf6430f04a3fc10abbd9129e4857e35db84eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127578"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Wiederherstellen eines Datenträgers und Erstellen einer wiederhergestellten VM in Azure
Azure Backup erstellt Wiederherstellungspunkte, die in geografisch redundanten Recovery-Tresoren gespeichert werden. Wenn Sie eine Wiederherstellung von einem Wiederherstellungspunkt durchführen, können Sie den gesamten virtuellen Computer oder einzelne Dateien wiederherstellen. In diesem Artikel wird das Wiederherstellen eines vollständigen virtuellen Computers mithilfe der Befehlszeilenschnittstelle erläutert. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Auflisten und Auswählen von Wiederherstellungspunkten
> * Wiederherstellen von Datenträgern aus einem Wiederherstellungspunkt
> * Erstellen virtueller Computer von wiederhergestellten Datenträgern

Informationen dazu, wie Sie mithilfe von PowerShell einen Datenträger wiederherstellen und einen wiederhergestellten virtuellen Computer erstellen, finden Sie unter [Verwenden von AzureRM.RecoveryServices.Backup-Cmdlets zum Sichern virtueller Computer](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.18 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Voraussetzungen
Dieses Tutorial erfordert eine Linux-VM, die mit Azure Backup geschützt wurde. Um ein versehentliches Löschen der VM und den Wiederherstellungsprozess zu simulieren, erstellen Sie einen virtuellen Computer von einem Datenträger in einem Wiederherstellungspunkt. Wenn Sie eine Linux-VM benötigen, die mit Azure Backup geschützt wurde, lesen Sie [Sichern eines virtuellen Computers in Azure per CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Übersicht über Azure Backup
Wenn Azure eine Sicherung initiiert, erstellt die Sicherungserweiterung auf dem virtuellen Computer eine Zeitpunkt-Momentaufnahme. Die Sicherungserweiterung wird auf dem virtuellen Computer installiert, wenn die erste Sicherung angefordert wird. Azure Backup kann auch eine Momentaufnahme des zugrunde liegenden Speichers erstellen, falls die VM zum Zeitpunkt der Sicherung nicht ausgeführt wird.

Standardmäßig führt Azure Backup eine konsistente Sicherung des Dateisystems durch. Nachdem Azure Backup die Momentaufnahme erstellt hat, werden die Daten in den Recovery Services-Tresor übertragen. Um die Effizienz zu maximieren, bestimmt und überträgt Azure Backup nur diejenigen Datenblöcke, die seit der vorherigen Sicherung geändert wurden.

Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt.


## <a name="list-available-recovery-points"></a>Auflisten der verfügbaren Wiederherstellungspunkte
Um einen Datenträger wiederherzustellen, wählen Sie einen Wiederherstellungspunkt als Quelle für die Wiederherstellungsdaten aus. Da die Standardrichtlinie jeden Tag einen Wiederherstellungspunkt erstellt und für 30 Tage aufbewahrt, können Sie einen Satz von Wiederherstellungspunkten beibehalten. So haben Sie die Möglichkeit, einen bestimmten Zeitpunkt für die Wiederherstellung auszuwählen. 

Um eine Liste der verfügbaren Wiederherstellungspunkte anzuzeigen, verwenden Sie [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). Der **Name** des Wiederherstellungspunkts wird verwendet, um den Datenträger wiederherzustellen. In diesem Tutorial verwenden wir den aktuellsten verfügbaren Wiederherstellungspunkt. Der Parameter `--query [0].name` wählt den letzten Wiederherstellungspunkt wie folgt aus:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Wiederherstellen von VM-Datenträgern
Um den Datenträger aus dem Wiederherstellungspunkt wiederherzustellen, erstellen Sie zunächst ein Azure Storage-Konto. Dieses Speicherkonto wird zum Speichern des wiederhergestellten Datenträgers verwendet. Der wiederhergestellte Datenträger wird in nachfolgenden Schritten zum Erstellen eines virtuellen Computers verwendet.

1. Verwenden Sie zum Erstellen eines Speicherkontos [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Der Name des Speicherkontos darf nur Kleinbuchstaben enthalten und muss global eindeutig sein. Ersetzen Sie *mystorageaccount* durch Ihren eigenen eindeutigen Namen:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Stellen Sie den Datenträger mit [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) aus dem Wiederherstellungspunkt wieder her. Ersetzen Sie *mystorageaccount* durch den Namen des Speicherkontos, das Sie mit dem vorherigen Befehl erstellt haben. Ersetzen Sie *myRecoveryPointName* durch den Namen des Wiederherstellungspunkts, den Sie über die Ausgabe aus dem vorherigen Befehl [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) erhalten haben:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Überwachen des Wiederherstellungsauftrags
Verwenden Sie [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list), um den Status von Wiederherstellungsaufträgen zu überwachen:

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Die Ausgabe ähnelt dem folgenden Beispiel, bei dem für den Wiederherstellungsauftrag der Status *InProgress* angezeigt wird:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Wenn der *Status* des Wiederherstellungsauftrags *Abgeschlossen* lautet, wurde der Datenträger im Speicherkonto wiederhergestellt.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Konvertieren von wiederhergestellten Datenträgern in einem verwalteten Datenträger
Der Wiederherstellungsauftrag erstellt einen nicht verwalteten Datenträger. Um einen virtuellen Computer aus dem Datenträger zu erstellen, muss dieser zunächst in einen verwalteten Datenträger konvertiert werden.

1. Rufen Sie mit [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) die Verbindungsinformationen für Ihr Speicherkonto ab. Ersetzen Sie *mystorageaccount* durch den Namen Ihres Speicherkontos:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Der nicht verwaltete Datenträger wird im Speicherkonto geschützt. Die folgenden Befehle rufen Informationen zum nicht verwalteten Datenträger ab und erstellen die Variable *uri*, die im nächsten Schritt verwendet wird, in dem Sie den verwalteten Datenträger erstellen.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Nun können Sie mit [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create) einen verwalteten Datenträger aus dem wiederhergestellten Datenträger erstellen. Die *uri*-Variable aus dem vorhergehenden Schritt dient als Quelle für den verwalteten Datenträger.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Nachdem Sie den wiederhergestellten Datenträger in einen verwalteten Datenträger konvertiert haben, bereinigen Sie den nicht verwalteten Datenträger und das Speicherkonto mit [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete). Ersetzen Sie *mystorageaccount* durch den Namen Ihres Speicherkontos:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Erstellen virtueller Computer von wiederhergestellten Datenträgern
Im letzten Schritt erstellen Sie einen virtuellen Computer aus dem verwalteten Datenträger.

1. Gehen Sie folgendermaßen vor, um mit [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) einen virtuellen Computer von dem verwalteten Datenträger zu erstellen:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Um zu überprüfen, ob Ihr virtueller Computer von dem wiederhergestellten Datenträger erstellt wurde, listen Sie mit [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) die VMs in Ihrer Ressourcengruppe auf:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie einen Datenträger von einem Wiederherstellungspunkt wiederhergestellt und dann einen virtuellen Computer von diesem Datenträger erstellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Auflisten und Auswählen von Wiederherstellungspunkten
> * Wiederherstellen von Datenträgern aus einem Wiederherstellungspunkt
> * Erstellen virtueller Computer von wiederhergestellten Datenträgern

Fahren Sie mit dem nächsten Tutorial fort, in dem Sie mehr über die Wiederherstellung einzelner Dateien aus einem Wiederherstellungspunkt erfahren.

> [!div class="nextstepaction"]
> [Wiederherstellen von Dateien auf einem virtuellen Computer in Azure](tutorial-restore-files.md)

