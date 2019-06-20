---
title: Verwenden eines virtuellen Linux-Computers zur Problembehandlung mithilfe der Azure CLI | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme auf einer Linux-VM beheben, indem Sie mithilfe der Azure-CLI eine Verbindung zwischen dem Betriebssystemdatenträger und einer Wiederherstellungs-VM herstellen
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: dfb85b0f9f1dda611c613cb296177cf28391adc0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318849"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Beheben von Problemen einer Linux-VM durch Anfügen des Betriebssystemdatenträgers an eine Wiederherstellungs-VM mithilfe der Azure CLI
Wenn für Ihren virtuellen Linux-Computer (VM) ein Start- oder Datenträgerfehler auftritt, müssen Sie möglicherweise Schritte zur Problembehebung auf der virtuellen Festplatte selbst ausführen. Ein gängiges Beispiel wäre ein ungültiger Eintrag in `/etc/fstab`, der den erfolgreichen Start der VM verhindert. In diesem Artikel wird erläutert, wie die Azure CLI die Verbindung zwischen Ihrer virtuellen Festplatte und einer anderen Linux-VM herstellt, um alle Fehler zu beheben und dann Ihre ursprüngliche VM neu zu erstellen. 


## <a name="recovery-process-overview"></a>Übersicht über den Wiederherstellungsprozess
Der Problembehebungsprozess sieht wie folgt aus:

1. Löschen Sie die VM, auf der Probleme auftreten, und behalten Sie die virtuellen Festplatten bei.
2. Fügen Sie einer anderen Linux-Problembehebungs-VM die virtuelle Festplatte hinzu, und stellen Sie sie bereit.
3. Stellen Sie eine Verbindung mit der Problembehebungs-VM her. Bearbeiten Sie Dateien, oder führen Sie ein beliebiges Tool zum Beheben von Problemen auf der ursprünglichen virtuellen Festplatte aus.
4. Heben Sie die Bereitstellung auf, und trennen Sie die virtuelle Festplatte von der Problembehebungs-VM.
5. Erstellen Sie eine VM mithilfe der ursprünglichen virtuellen Festplatte.

Für virtuelle Computer mit verwalteten Datenträgern finden Sie weitere Informationen unter [Problembehandlung bei einem virtuellen Computer mit verwalteten Datenträgern durch Anfügen eines neuen Betriebssystemdatenträgers](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Zum Ausführen dieser Schritte zur Problembehandlung muss die neueste [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein.

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Als Beispielparameternamen werden `myResourceGroup`, `mystorageaccount` und `myVM` verwendet.


## <a name="determine-boot-issues"></a>Bestimmen von Problemen beim Start
Überprüfen Sie die serielle Ausgabe, um zu bestimmen, warum Ihre VM nicht ordnungsgemäß starten kann. Ein gängiges Beispiel ist ein ungültiger Eintrag in `/etc/fstab` oder eine zugrunde liegende virtuelle Festplatte, die gelöscht oder verschoben wird.

Rufen Sie mit [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics) die Startprotokolle ab. Im folgenden Beispiel wird die serielle Ausgabe der VM namens `myVM` in der Ressourcengruppe namens `myResourceGroup` aufgerufen:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Überprüfen Sie die serielle Ausgabe, um zu bestimmen, warum die VM nicht startet. Wenn die serielle Ausgabe keine Auskunft bietet, müssen Sie möglicherweise Protokolldateien in `/var/log` überprüfen, nachdem Sie die virtuelle Festplatte mit der Problembehebungs-VM verbunden haben.


## <a name="view-existing-virtual-hard-disk-details"></a>Anzeigen von Details vorhandener virtueller Festplatten
Damit Sie Ihre virtuelle Festplatte (VHD) an einen anderen virtuellen Computer anfügen können, müssen Sie den URI des Betriebssystemdatenträgers ermitteln. 

Informationen zu Ihrem virtuellen Computer können Sie mit [az vm show](/cli/azure/vm) anzeigen. Verwenden Sie das Flag `--query` zum Extrahieren des URI auf den Betriebssystemdatenträger. Im folgenden Beispiel werden Datenträgerinformationen für den virtuellen Computer `myVM` in der Ressourcengruppe `myResourceGroup` abgerufen:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

Der URI lautet in etwa wie folgt: **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** .

## <a name="delete-existing-vm"></a>Löschen einer vorhandener VM
Virtuelle Festplatten und VMs sind zwei unterschiedliche Ressourcen in Azure. Eine virtuelle Festplatte ist der Ort, an dem das Betriebssystem selbst, Anwendungen und Konfigurationen gespeichert werden. Die VM selbst besteht nur aus Metadaten, die die Größe oder Position definieren und auf Ressourcen verweisen, z.B. eine virtuelle Festplatte oder virtuelle Netzwerkschnittstellenkarte (NIC). Jede virtuelle Festplatte verfügt über eine zugewiesene Lease, wenn sie mit einer VM verknüpft ist. Obwohl Datenträger auch bei laufendem Betrieb der VM hinzugefügt und getrennt werden können, kann der Betriebssystemdatenträger nicht getrennt werden, es sei denn, die VM-Ressource wird gelöscht. Die Lease ordnet den Betriebssystemdatenträger weiterhin einer VM zu, selbst wenn diese VM beendet und die Zuordnung aufgehoben ist.

Der erste Schritt beim Wiederherstellen Ihrer VM ist das Löschen der VM-Ressource selbst. Das Löschen der VM belässt die virtuellen Festplatten in Ihrem Speicherkonto. Nachdem die VM gelöscht wird, fügen Sie die virtuelle Festplatte zu einer anderen VM hinzu, um die Fehler zu beheben.

Löschen Sie mit [az vm delete](/cli/azure/vm) den virtuellen Computer. Im folgenden Beispiel wird die VM namens `myVM` in der Ressourcengruppe namens `myResourceGroup` gelöscht:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Warten Sie, bis die VM gelöscht wurde, bevor Sie die virtuelle Festplatte einer anderen VM hinzufügen. Die Lease auf der virtuellen Festplatte, die sie zur VM zuordnet, muss freigegeben werden, bevor Sie die virtuelle Festplatte einer anderen VM hinzufügen können.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Hinzufügen einer vorhandenen virtuellen Festplatte zu einer anderen VM
Verwenden Sie eine andere Problembehebungs-VM für die nächsten Schritte. Fügen Sie die vorhandene virtuelle Festplatte zu dieser Problembehebungs-VM hinzu, um den Inhalt des Datenträgers zu durchsuchen und zu bearbeiten. Durch diesen Prozess können Sie z.B. alle Konfigurationsfehler beheben oder zusätzliche Anwendungs- oder Systemprotokolldateien überprüfen. Wählen Sie eine andere Problembehebungs-VM aus, oder erstellen Sie eine.

Fügen Sie mit [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk) die vorhandene virtuelle Festplatte an. Wenn Sie die vorhandene virtuelle Festplatte anfügen, geben Sie den URI des Datenträgers an, der im vorherigen Befehl `az vm show` abgerufen wurde. Das folgende Beispiel fügt eine vorhandene virtuelle Festplatte zur Problembehebungs-VM namens `myVMRecovery` in der Ressourcengruppe mit dem Namen `myResourceGroup` hinzu:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Bereitstellen des hinzugefügten Datenträgers

> [!NOTE]
> In den folgenden Beispielen sind die Schritte beschrieben, die auf einer Ubuntu-VM erforderlich sind. Bei Verwendung einer anderen Linux-Distribution, z.B. Red Hat Enterprise Linux oder SUSE, sind die Speicherorte für Protokolldateien und `mount`-Befehle ggf. etwas anders. Nähere Informationen zu Ihrer spezifischen Distribution für die entsprechenden Änderungen in Befehlen finden Sie in der Dokumentation.

1. Stellen Sie mithilfe von SSH und den entsprechenden Anmeldeinformationen eine Verbindung zu Ihrer Problembehebungs-VM her. Wenn dieses Laufwerk der erste Datenträger ist, der zu Ihrer Problembehebungs-VM hinzugefügt wurde, ist es wahrscheinlich mit `/dev/sdc` verbunden. Verwenden Sie `dmseg`, um hinzugefügte Datenträger anzuzeigen:

    ```bash
    dmesg | grep SCSI
    ```

    Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    Im vorherigen Beispiel befindet sich der Betriebssystemdatenträger in `/dev/sda`, und der temporäre Datenträger, der für jede VM bereitgestellt ist, befindet sich in `/dev/sdb`. Wenn Sie mehrere Datenträger haben, müssen Sie in `/dev/sdd`, `/dev/sde` usw. vorhanden sein.

2. Erstellen Sie ein Verzeichnis, um Ihre vorhandene virtuelle Festplatte bereitzustellen. Im folgenden Beispiel wird ein Verzeichnis namens `troubleshootingdisk` erstellt:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Wenn Sie über mehrere Partitionen auf Ihrer vorhandenen virtuellen Festplatte verfügen, stellen Sie die erforderliche Partition bereit. Das folgende Beispiel stellt die erste primäre Partition in `/dev/sdc1` bereit:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Die bewährte Methode ist das Bereitstellen von Datenträgern auf VMs in Azure mithilfe des Universally Unique Identifiers (UUID) der virtuellen Festplatte. In diesem kurzen Szenario zur Problembehebung ist das Bereitstellen der virtuellen Festplatte mithilfe von UUID nicht erforderlich. Wenn allerdings unter normalen Bedingungen `/etc/fstab` bearbeitet wird, um virtuelle Festplatten mithilfe des Gerätenamens anstatt des UUID bereitzustellen, dann wird die VM möglicherweise nicht erfolgreich starten können.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Beheben von Problemen auf der ursprünglichen virtuellen Festplatte
Nun, da die vorhandene virtuelle Festplatte bereitgestellt ist, können Sie jetzt alle Schritte zur Wartung und Problembehebung ausführen. Fahren Sie mit den folgenden Schritte fort, nachdem Sie die Probleme behoben haben.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Aufheben der Bereitstellung und Trennen der ursprünglichen virtuellen Festplatte
Sobald Ihre Fehler behoben sind, heben Sie die Bereitstellung auf, und trennen Sie die vorhandene virtuelle Festplatte von Ihrer Problembehebungs-VM. Sie können Ihre virtuelle Festplatte nicht mit einer anderen VM nutzen, bis die Lease freigegeben wird, die die virtuelle Festplatte zur Problembehebungs-VM hinzufügt.

1. Aufheben der Bereitstellung der vorhandenen virtuellen Festplatte von der SSH-Sitzung bis zu Ihrer Problembehebungs-VM. Wechseln Sie aus dem übergeordneten Verzeichnis zu Ihrem Bereitstellungspunkt:

    ```bash
    cd /
    ```

    Heben Sie die Bereitstellung der vorhandenen virtuellen Festplatte auf. Im folgenden Beispiel wird die Bereitstellung des Geräts in `/dev/sdc1` aufgehoben:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Trennen Sie die virtuelle Festplatte von der VM. Beenden Sie die SSH-Sitzung zu Ihrer Problembehebungs-VM. Listen Sie mit [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk) die an die Problembehandlungs-VM angefügten Datenträger auf. Im folgenden Beispiel werden die Datenträger aufgelistet, die zur VM namens `myVMRecovery` in der Ressourcengruppe namens `myResourceGroup` hinzugefügt wurden:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Notieren Sie den Namen Ihrer vorhandenen virtuellen Festplatte. Der Name einer Festplatte mit dem URI **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** lautet beispielsweise **myVHD**. 

    Trennen Sie mit [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk) den Datenträger von Ihrem virtuellen Computer. Im folgenden Beispiel wird der Datenträger mit dem Namen `myVHD` vom virtuellen Computer `myVMRecovery` in der Ressourcengruppe `myResourceGroup` getrennt:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Erstellen einer VM von der ursprünglichen Festplatte
Verwenden Sie zum Erstellen einer VM von Ihrer ursprünglichen virtuellen Festplatte [diese Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Die tatsächliche JSON-Vorlage ist unter dem folgenden Link verfügbar:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Mit der Vorlage wird anhand des VHD-URI aus dem Befehl weiter oben ein virtueller Computer bereitgestellt. Stellen Sie mit [az group deployment create](/cli/azure/group/deployment) die Vorlage bereit. Geben Sie wie folgt den URI für Ihre ursprüngliche virtuelle Festplatte und dann den Betriebssystemtyp sowie die Größe und den Namen des virtuellen Computers an:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Erneutes Aktivieren der Startdiagnose
Wenn Sie Ihre VM aus der vorhandenen virtuellen Festplatte erstellen, werden Startdiagnoseeinstellungen möglicherweise nicht automatisch aktiviert. Aktivieren Sie mit [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics) die Startdiagnose. Im folgenden Beispiel wird die Diagnoseerweiterung in der VM namens `myDeployedVM` in der Ressourcengruppe namens `myResourceGroup` aktiviert:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Problembehandlung bei einem virtuellen Computer mit verwalteten Datenträgern durch Anfügen eines neuen Betriebssystemdatenträgers
1. Beenden Sie die betroffene VM.
2. [Erstellen Sie eine Momentaufnahme](../linux/snapshot-copy-managed-disk.md) des Betriebssystemdatenträgers des virtuellen Computers mit verwalteten Datenträgern.
3. [Erstellen Sie einen verwalteten Datenträger aus der Momentaufnahme](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Fügen Sie den verwalteten Datenträger als Datenträger des virtuellen Computers an](../windows/attach-disk-ps.md).
5. [Ändern Sie den Datenträger aus Schritt 4 in den Betriebssystemdatenträger](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Nächste Schritte
Wenn Probleme beim Herstellen einer Verbindung mit Ihrer VM auftreten, finden Sie unter [Problembehandlung von SSH-Verbindungen mit einer Azure-VM](troubleshoot-ssh-connection.md) Hilfestellungen. Konsultieren Sie [Beheben von Anwendungskonnektivitätsproblemen auf einer Linux-VM](troubleshoot-app-connection.md) bei Problemen mit dem Zugriff auf Anwendungen, die auf Ihrer VM ausgeführt werden.

