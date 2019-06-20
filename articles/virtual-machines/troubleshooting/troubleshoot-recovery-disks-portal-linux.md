---
title: Verwenden einer Linux-Problembehebungs-VM im Azure-Portal | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme auf einer Linux-VM beheben, indem Sie mithilfe des Azure-Portals eine Verbindung zwischen dem Betriebssystemdatenträger und einer Wiederherstellungs-VM herstellen.
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: genli
ms.openlocfilehash: 65187c3ef6debfa27c8c4fea62bcd31b857b4171
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60319797"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Beheben von Problemen einer Linux-VM durch Hinzufügen des Betriebssystemdatenträgers zu einer Wiederherstellungs-VM mit dem Azure-Portal
Wenn für Ihren virtuellen Linux-Computer (VM) ein Start- oder Datenträgerfehler auftritt, müssen Sie möglicherweise Schritte zur Problembehebung auf der virtuellen Festplatte selbst ausführen. Ein gängiges Beispiel wäre ein ungültiger Eintrag in `/etc/fstab`, der den erfolgreichen Start der VM verhindert. In diesem Artikel wird erläutert, wie das Azure-Portal die Verbindung zwischen Ihrer virtuellen Festplatte und einer anderen Linux-VM herstellt, um alle Fehler zu beheben und dann Ihre ursprüngliche VM neu zu erstellen.

## <a name="recovery-process-overview"></a>Übersicht über den Wiederherstellungsprozess
Der Problembehebungsprozess sieht wie folgt aus:

1. Löschen Sie die VM, auf der Probleme auftreten, und behalten Sie die virtuellen Festplatten bei.
2. Fügen Sie einer anderen Linux-Problembehebungs-VM die virtuelle Festplatte hinzu, und stellen Sie sie bereit.
3. Stellen Sie eine Verbindung mit der Problembehebungs-VM her. Bearbeiten Sie Dateien, oder führen Sie ein beliebiges Tool zum Beheben von Problemen auf der ursprünglichen virtuellen Festplatte aus.
4. Heben Sie die Bereitstellung auf, und trennen Sie die virtuelle Festplatte von der Problembehebungs-VM.
5. Erstellen Sie eine VM mithilfe der ursprünglichen virtuellen Festplatte.

Für virtuelle Computer mit verwalteten Datenträgern finden Sie weitere Informationen unter [Problembehandlung bei einem virtuellen Computer mit verwalteten Datenträgern durch Anfügen eines neuen Betriebssystemdatenträgers](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Bestimmen von Problemen beim Start
Überprüfen Sie die Startdiagnose und den Screenshot der VM, um zu bestimmen, warum Ihre VM nicht ordnungsgemäß starten kann. Ein gängiges Beispiel wäre ein ungültiger Eintrag in `/etc/fstab` oder eine zugrunde liegende virtuelle Festplatte, die gelöscht oder verschoben wird.

Wählen Sie Ihre VM im Portal aus, und scrollen Sie dann nach unten zum Abschnitt **Unterstützung + Problembehebung**. Klicken Sie auf **Diagnose starten**, um die Konsolennachrichten anzuzeigen, die von Ihrer VM gestreamt werden. Überprüfen Sie die Konsolenprotokolle, um festzustellen, ob Sie bestimmen können, warum ein Problem für die VM aufgetreten ist. Das folgende Beispiel zeigt eine VM, die im Wartungsmodus hängen geblieben ist und die einen manuellen Eingriff erfordert:

![Anzeigen der Konsolenprotokolle für die VM-Startdiagnose](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Sie können auch am oberen Rand des Startdiagnoseprotokolls auf **Screenshot** klicken, um einen VM-Screenshot herunterzuladen.


## <a name="view-existing-virtual-hard-disk-details"></a>Anzeigen von Details vorhandener virtueller Festplatten
Bevor Sie Ihre virtuelle Festplatte zu einer anderen VM hinzufügen können, müssen Sie den Namen der virtuellen Festplatte (VHD) identifizieren. 

Wählen Sie im Portal Ihre Ressourcengruppe und dann Ihr Speicherkonto aus. Klicken Sie auf **Blobs**, wie im folgenden Beispiel:

![Auswählen von Speicherblobs](./media/troubleshoot-recovery-disks-portal-linux/storage-account-overview.png)

In der Regel verfügen Sie über einen Container namens **VHDs**, der Ihre virtuellen Festplatten speichert. Wählen Sie die Container aus, um eine Liste der virtuellen Festplatten anzuzeigen. Notieren Sie den Namen Ihrer virtuellen Festplatte (das Präfix ist in der Regel der Name Ihrer VM):

![Identifizieren der VHD im Speichercontainer](./media/troubleshoot-recovery-disks-portal-linux/storage-container.png)

Wählen Sie Ihre vorhandenen virtuellen Festplatte aus der Liste aus, und kopieren Sie die URL zur Verwendung in den folgenden Schritten:

![Kopieren vorhandener virtueller Festplatten-URLS](./media/troubleshoot-recovery-disks-portal-linux/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Löschen einer vorhandener VM
Virtuelle Festplatten und VMs sind zwei unterschiedliche Ressourcen in Azure. Eine virtuelle Festplatte ist der Ort, an dem das Betriebssystem selbst, Anwendungen und Konfigurationen gespeichert werden. Die VM selbst besteht nur aus Metadaten, die die Größe oder Position definieren und auf Ressourcen verweisen, z.B. eine virtuelle Festplatte oder virtuelle Netzwerkschnittstellenkarte (NIC). Jede virtuelle Festplatte verfügt über eine zugewiesene Lease, wenn sie mit einer VM verknüpft ist. Obwohl Datenträger auch bei laufendem Betrieb der VM hinzugefügt und getrennt werden können, kann der Betriebssystemdatenträger nicht getrennt werden, es sei denn, die VM-Ressource wird gelöscht. Die Lease ordnet den Betriebssystemdatenträger weiterhin einer VM zu, selbst wenn diese VM beendet und die Zuordnung aufgehoben ist.

Der erste Schritt beim Wiederherstellen Ihrer VM ist das Löschen der VM-Ressource selbst. Das Löschen der VM belässt die virtuellen Festplatten in Ihrem Speicherkonto. Nachdem die VM gelöscht wird, fügen Sie die virtuelle Festplatte zu einer anderen VM hinzu, um die Fehler zu beheben.

Wählen Sie Ihre VM im Portal aus, und klicken Sie auf **Löschen**:

![Der Screenshot der VM-Startdiagnose zeigt einen Startfehler](./media/troubleshoot-recovery-disks-portal-linux/stop-delete-vm.png)

Warten Sie, bis die VM gelöscht wurde, bevor Sie die virtuelle Festplatte einer anderen VM hinzufügen. Die Lease auf der virtuellen Festplatte, die sie zur VM zuordnet, muss freigegeben werden, bevor Sie die virtuelle Festplatte einer anderen VM hinzufügen können.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Hinzufügen einer vorhandenen virtuellen Festplatte zu einer anderen VM
Verwenden Sie eine andere Problembehebungs-VM für die nächsten Schritte. Fügen Sie die vorhandene virtuelle Festplatte zu dieser Problembehebungs-VM hinzu, um den Inhalt des Datenträgers zu durchsuchen und zu bearbeiten. Durch diesen Prozess können Sie z.B. alle Konfigurationsfehler beheben oder zusätzliche Anwendungs- oder Systemprotokolldateien überprüfen. Wählen Sie eine andere Problembehebungs-VM aus, oder erstellen Sie eine.

1. Wählen Sie im Portal Ihre Ressourcengruppe und dann Ihre Problembehebungs-VM aus. Wählen Sie **Datenträger** aus, und klicken Sie dann auf **Vorhandener hinzufügen**:

    ![Hinzufügen des vorhandenen Datenträgers im Portal](./media/troubleshoot-recovery-disks-portal-linux/attach-existing-disk.png)

2. Klicken Sie auf **VHD-Datei**, um Ihre vorhandene virtuelle Festplatte auszuwählen:

    ![Navigieren zu einer vorhandenen VHD](./media/troubleshoot-recovery-disks-portal-linux/select-vhd-location.png)

3. Wählen Sie Ihr Speicherkonto und Ihren Container aus, und klicken Sie auf Ihre vorhandenen VHD. Klicken Sie auf die Schaltfläche **Auswählen**, um Ihre Auswahl zu bestätigen:

    ![Auswählen Ihrer vorhandenen VHD](./media/troubleshoot-recovery-disks-portal-linux/select-vhd.png)

4. Nachdem Sie Ihre VHD ausgewählt haben, klicken Sie auf **OK**, um die vorhandene virtuelle Festplatte hinzuzufügen:

    ![Bestätigen Sie das Hinzufügen der vorhandene virtuellen Festplatten](./media/troubleshoot-recovery-disks-portal-linux/attach-disk-confirm.png)

5. Nach wenigen Sekunden zeigt der Bereich **Datenträger** für Ihre VM Ihre vorhandenen virtuellen Festplatte an, die als Datenträger verbunden ist:

    ![Vorhandene virtuelle Festplatte, die als Datenträger angefügt ist](./media/troubleshoot-recovery-disks-portal-linux/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Bereitstellen des hinzugefügten Datenträgers

> [!NOTE]
> In den folgenden Beispielen sind die Schritte beschrieben, die auf einer Ubuntu-VM erforderlich sind. Bei Verwendung einer anderen Linux-Distribution, z.B. Red Hat Enterprise Linux oder SUSE, sind die Speicherorte für Protokolldateien und `mount`-Befehle ggf. etwas anders. Nähere Informationen zu Ihrer spezifischen Distribution für die entsprechenden Änderungen in Befehlen finden Sie in der Dokumentation.

1. Stellen Sie mithilfe von SSH und den entsprechenden Anmeldeinformationen eine Verbindung zu Ihrer Problembehebungs-VM her. Wenn dieses Laufwerk der erste Datenträger ist, der zu Ihrer Problembehebungs-VM hinzugefügt wurde, ist es wahrscheinlich mit `/dev/sdc` verbunden. Verwenden Sie `dmseg`, um hinzugefügte Datenträger aufzulisten:

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
Sobald Ihre Fehler behoben sind, trennen Sie die vorhandene virtuelle Festplatte von Ihrer Problembehebungs-VM. Sie können Ihre virtuelle Festplatte nicht mit einer anderen VM nutzen, bis die Lease freigegeben wird, die die virtuelle Festplatte zur Problembehebungs-VM hinzufügt.

1. Aufheben der Bereitstellung der vorhandenen virtuellen Festplatte von der SSH-Sitzung bis zu Ihrer Problembehebungs-VM. Wechseln Sie aus dem übergeordneten Verzeichnis zu Ihrem Bereitstellungspunkt:

    ```bash
    cd /
    ```

    Heben Sie die Bereitstellung der vorhandenen virtuellen Festplatte auf. Im folgenden Beispiel wird die Bereitstellung des Geräts in `/dev/sdc1` aufgehoben:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Trennen Sie die virtuelle Festplatte von der VM. Wählen Sie Ihre VM im Portal aus, und klicken Sie auf **Datenträger**. Wählen Sie Ihre vorhandene virtuelle Festplatte aus, und klicken Sie dann auf **Trennen**:

    ![Trennen einer vorhandenen virtuellen Festplatte](./media/troubleshoot-recovery-disks-portal-linux/detach-disk.png)

    Warten Sie, bis die VM den Datenträger erfolgreich getrennt hat, bevor Sie fortfahren.

## <a name="create-vm-from-original-hard-disk"></a>Erstellen einer VM von der ursprünglichen Festplatte
Verwenden Sie zum Erstellen einer VM von Ihrer ursprünglichen virtuellen Festplatte [diese Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). Die Vorlage stellt eine VM in einem vorhandenen virtuellen Netzwerk bereit und nutzt die VHD-URL aus dem früheren Befehl. Klicken Sie wie folgt auf die Schaltfläche **Deploy to Azure** (Bereitstellung in Azure):

![Bereitstellen eines virtuellen Computers aus der GitHub-Vorlage](./media/troubleshoot-recovery-disks-portal-linux/deploy-template-from-github.png)

Die Vorlage wird im Azure-Portal für die Bereitstellung geladen. Geben Sie die Namen für Ihre neue VM und die vorhandenen Azure-Ressourcen ein, und fügen Sie die URL zu Ihrer vorhandenen virtuellen Festplatte hinzu. Um die Bereitstellung zu beginnen, klicken Sie auf **Kaufen**:

![Erstellen einer VM über eine Vorlage](./media/troubleshoot-recovery-disks-portal-linux/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Erneutes Aktivieren der Startdiagnose
Wenn Sie Ihre VM aus der vorhandenen virtuellen Festplatte erstellen, werden Startdiagnoseeinstellungen möglicherweise nicht automatisch aktiviert. Wählen Sie Ihre VM im Portal aus, um den Status der Startdiagnoseeinstellungen zu überprüfen und sie ggf. zu aktivieren. Klicken Sie unter **Überwachen** auf **Diagnoseeinstellungen**. Stellen Sie sicher, dass der Status **An** lautet und das Häkchen neben **Startdiagnose** gesetzt ist. Wenn Sie Änderungen vornehmen, klicken Sie auf **Speichern**:

![Aktualisieren der Startdiagnoseeinstellungen](./media/troubleshoot-recovery-disks-portal-linux/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Problembehandlung bei einem virtuellen Computer mit verwalteten Datenträgern durch Anfügen eines neuen Betriebssystemdatenträgers
1. Beenden Sie die betroffene VM.
2. [Erstellen Sie eine Momentaufnahme](../windows/snapshot-copy-managed-disk.md) des Betriebssystemdatenträgers des virtuellen Computers mit verwalteten Datenträgern.
3. [Erstellen Sie einen verwalteten Datenträger aus der Momentaufnahme](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Fügen Sie den verwalteten Datenträger als Datenträger des virtuellen Computers an](../windows/attach-disk-ps.md).
5. [Ändern Sie den Datenträger aus Schritt 4 in den Betriebssystemdatenträger](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Nächste Schritte
Wenn Probleme beim Herstellen einer Verbindung mit Ihrer VM auftreten, finden Sie unter [Problembehandlung von SSH-Verbindungen mit einer Azure-VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Hilfestellungen. Konsultieren Sie [Beheben von Anwendungskonnektivitätsproblemen auf einer Linux-VM](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) bei Problemen mit dem Zugriff auf Anwendungen, die auf Ihrer VM ausgeführt werden.

Weitere Informationen zu Resource Manager finden Sie unter [Übersicht über den Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
