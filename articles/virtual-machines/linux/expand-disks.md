---
title: Erweitern von virtuellen Festplatten auf Linux-VMs
description: Erfahren Sie, wie Sie virtuelle Festplatten auf einer Linux-VM mit der Azure CLI erweitern.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: bbb959b6b1d71c81f7b920b3962f693716041e16
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016232"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Erweitern von virtuellen Festplatten auf virtuellen Linux-Computern mit der Azure-CLI

Dieser Artikel erläutert, wie verwaltete Datenträger für einen virtuellen Linux-Computer mit der Azure CLI erweitert werden können. Sie können [Datenträger hinzufügen](add-disk.md), um zusätzlichen Speicherplatz zur Verfügung zu stellen, und Sie können auch einen vorhandenen Datenträger für Daten erweitern. Die Standardgröße der virtuellen Festplatte für das Betriebssystem beträgt normalerweise 30 GB auf einem virtuellen Linux-Computer in Azure. 

> [!WARNING]
> Achten Sie immer darauf, dass Ihr Dateisystem in einem fehlerfreien Zustand ist und dass Ihre Datenträgerpartitionstabelle die neue Größe unterstützt. Vergewissern Sie sich außerdem, dass Ihre Daten gesichert sind, bevor Sie Vorgänge zur Größenänderung von Datenträgern ausführen. Weitere Informationen finden Sie unter [Sichern virtueller Linux-Computer in Azure](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Erweitern eines verwalteten Azure-Datenträgers
Überprüfen Sie, ob Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index#az-login) bei einem Azure-Konto angemeldet sind.

Für diesen Artikel ist ein vorhandener virtueller Computer in Azure mit mindestens einem angefügten und vorbereiteten Datenträger erforderlich. Wenn Sie noch nicht über einen virtuellen Computer verfügen, den Sie verwenden können, finden Sie entsprechende Informationen unter [Erstellen und Vorbereiten eines virtuellen Computers mit Datenträgern](tutorial-manage-disks.md#create-and-attach-disks).

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen wie *myResourceGroup* und *myVM* durch Ihre eigenen Werte.

1. Vorgänge auf virtuellen Festplatten können nicht durchgeführt werden, wenn die VM ausgeführt wird. Heben Sie die Zuordnung der VM mit [az vm deallocate](/cli/azure/vm#az-vm-deallocate) auf. Im folgenden Beispiel wird die Zuordnung für die VM *myVM* in der Ressourcengruppe *myResourceGroup* aufgehoben:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Die VM muss aufgehoben werden, um die virtuelle Festplatte zu erweitern. Durch Beenden der VM mit `az vm stop` werden die Computeressourcen nicht freigegeben. Verwenden Sie `az vm deallocate`, um Computerressourcen freizugeben.

1. Sie überprüfen die Liste der verwalteten Datenträger in einer Ressourcengruppe mit [az disk list](/cli/azure/disk#az-disk-list). Im folgenden Beispiel wird eine Liste mit verwalteten Datenträgern in der Ressourcengruppe *myResourceGroup* aufgelistet:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Sie erweitern den erforderlichen Datenträger mit [az disk update](/cli/azure/disk#az-disk-update). Im folgenden Beispiel wird der verwaltete Datenträger *myDataDisk* auf *200* GB erweitert:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Wenn Sie einen verwalteten Datenträger erweitern, wird die aktualisierte Größe auf die nächste Größe für verwaltete Datenträger aufgerundet. Eine Tabelle der verfügbaren verwalteten Datenträgergrößen und -ebenen finden Sie unter [Übersicht über Azure Managed Disks – Preise und Abrechnung](../managed-disks-overview.md).

1. Starten Sie den virtuellen Computer mit [az vm start](/cli/azure/vm#az-vm-start). Im folgenden Beispiel wird die VM *myVM* in der Ressourcengruppe *myResourceGroup* gestartet:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Erweitern einer Datenträgerpartition und des Dateisystems
Um einen erweiterten Datenträger zu verwenden, erweitern Sie die zugrunde liegende Partition und das Dateisystem.

1. SSH mit Ihrer VM mit den entsprechenden Anmeldeinformationen. Sie können die öffentliche IP-Adresse Ihres virtuellen Computers mit dem Befehl [az vm show](/cli/azure/vm#az-vm-show) anzeigen:

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Erweitern Sie die zugrunde liegende Partition und das Dateisystem.

    a. Wenn der Datenträger bereits eingebunden ist, heben Sie die Einbindung auf:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Verwenden Sie `parted`, um Datenträgerinformationen anzuzeigen und die Größe der Partition zu ändern:

    ```bash
    sudo parted /dev/sdc
    ```

    Informationen zum vorhandenen Partitionslayout können Sie mit `print` anzeigen. Die Ausgabe ähnelt dem folgenden Beispiel, das zeigt, dass die Größe des zugrunde liegenden Datenträgers 215 GB beträgt:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Erweitern Sie die Partition mit `resizepart`. Geben Sie die Partitionsnummer *1* und die Größe für die neue Partition ein:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Geben Sie zum Beenden den Befehl `quit` ein.

1. Überprüfen Sie nach der Änderung der Partitionsgröße die Partitionskonsistenz mit `e2fsck`:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Ändern Sie die Größe des Dateisystems mit `resize2fs`:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Binden Sie die Partition am gewünschten Speicherort ein, z.B. `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Verwenden Sie `df -h`, um zu überprüfen, ob die Größe des Datenträgers geändert wurde. In der folgenden Beispielausgabe ist zu sehen, dass das Datenträgerlaufwerk für Daten, */dev/sdc1*, jetzt eine Größe von 200 GB aufweist:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Nächste Schritte
* Wenn Sie zusätzlichen Speicher benötigen, können Sie auch [Datenträger zu einer Linux-VM hinzufügen](add-disk.md). 
* Weitere Informationen zur Datenträgerverschlüsselung finden Sie unter [Azure Disk Encryption für Linux-VMs](disk-encryption-overview.md).
