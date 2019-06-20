---
title: Anfügen eines Datenträgers an eine Linux-VM | Microsoft Docs
description: Verwenden Sie das Portal, um einen neuen oder vorhandenen Datenträger an einen virtuellen Linux-Computer anzufügen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: d5dd916f7e4434640db6dae6f8c5a73d1ff2d3e0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60187953"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Anfügen eines Datenträgers an einen virtuellen Linux-Computer mithilfe des Portals 
In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal neue und vorhandene Datenträger an einen virtuellen Linux-Computer anfügen können. Sie können auch [einen Datenträger an eine Windows-VM im Azure-Portal anfügen](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Bevor Sie Datenträger an Ihren virtuellen Computer anfügen, lesen Sie diese Tipps:

* Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Bei an virtuelle Computer angefügten Datenträgern handelt es sich um VHD-Dateien, die in Azure gespeichert sind. Details finden Sie in unserer [Einführung in verwaltete Datenträger](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Nach dem Anfügen des Datenträgers müssen Sie [eine Verbindung mit dem virtuellen Linux-Computer zum Bereitstellen des neuen Datenträgers herstellen](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Suchen des virtuellen Computers
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie im linken Menü auf **Virtuelle Computer**.
3. Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.
4. Klicken Sie auf der Seite „Virtuelle Computer“ unter **Zusammenfassung** auf **Datenträger**.
   
    ![Öffnen der Datenträgereinstellungen](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anfügen eines neuen Datenträgers

1. Klicken Sie im Bereich **Datenträger** auf **+ Datenträger anfügen**.
2. Klicken Sie auf das Dropdownmenü **Name**, und wählen Sie **Datenträger erstellen** aus:

    ![Erstellen eines verwalteten Azure-Datenträgers](./media/attach-disk-portal/create-new-md.png)

3. Geben Sie einen Namen für Ihren verwalteten Datenträger ein. Überprüfen und aktualisieren Sie bei Bedarf die Standardeinstellungen, und klicken Sie dann auf **Erstellen**.
   
   ![Überprüfen der Datenträgereinstellungen](./media/attach-disk-portal/create-new-md-settings.png)

4. Klicken Sie auf **Speichern**, um den verwalteten Datenträger zu speichern und die Konfiguration des virtuellen Computers zu aktualisieren:

   ![Speichern des neuen verwalteten Azure-Datenträgers](./media/attach-disk-portal/confirm-create-new-md.png)

5. Nachdem der Datenträger von Azure erstellt und an den virtuellen Computer angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen des virtuellen Computers unter **Datenträger**angezeigt. Da es sich bei verwalteten Datenträgern um Ressourcen der obersten Ebene handelt, werden sie im Stammverzeichnis der Ressourcengruppe angezeigt:

   ![Verwalteter Azure-Datenträger in Ressourcengruppe](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anfügen eines vorhandenen Datenträgers
1. Klicken Sie im Bereich **Datenträger** auf **+ Datenträger anfügen**.
2. Klicken Sie auf das Dropdownmenü **Name**, um eine Liste der vorhandenen verwalteten Datenträger anzuzeigen, auf die von Ihrem Azure-Abonnement aus zugegriffen werden kann. Wählen Sie den verwalteten Datenträger aus, den Sie anfügen möchten:

   ![Anfügen eines vorhandenen verwalteten Azure-Datenträgers](./media/attach-disk-portal/select-existing-md.png)

3. Klicken Sie auf **Speichern**, um den vorhandenen verwalteten Datenträger anzufügen und die Konfiguration des virtuellen Computers zu aktualisieren:
   
   ![Speichern von Aktualisierungen verwalteter Azure-Datenträger](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Nachdem der Datenträger von Azure an den virtuellen Computer angefügt wurde, wird er in den Datenträgereinstellungen des virtuellen Computers unter **Datenträger**angezeigt.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Herstellen einer Verbindung mit dem virtuellen Linux-Computer zum Bereitstellen des neuen Datenträgers
Sie benötigen SSH für Ihren virtuellen Computer, um den neuen Datenträger zu partitionieren, zu formatieren und bereitzustellen, damit er von Ihrem virtuellen Linux-Computer verwendet werden kann. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](mac-create-ssh-keys.md). Im folgenden Beispiel wird eine Verbindung mit einem virtuellen Computer mit dem öffentlichen DNS-Eintrag von *mypublicdns.westus.cloudapp.azure.com* mit dem Benutzernamen *azureuser* erstellt: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Nachdem Sie die Verbindung zu Ihrem virtuellen Computer hergestellt haben, können Sie einen Datenträger anfügen. Suchen Sie zunächst den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird „dmesg“ zum Filtern auf *SCSI*-Datenträgern verwendet:

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

Hier ist *sdc* der Datenträger, den wir möchten. 

### <a name="partition-a-new-disk"></a>Partitionieren eines neuen Datenträgers
Wenn Sie einen vorhandenen Datenträger verwenden, der Daten enthält, überspringen Sie die Schritte bis zum Einbinden des Datenträgers. Wenn Sie einen neuen Datenträger anfügen, müssen Sie den Datenträger partitionieren.

Partitionieren Sie den Datenträger mit `fdisk`. Wenn der Datenträger 2 Tebibyte (TiB) oder größer ist, müssen Sie die GPT-Partitionierung verwenden. Die GPT-Partitionierung können Sie mithilfe von `parted` ausführen. Wenn die Größe des Datenträgers unter 2 TiB liegt, können Sie die MBR- oder die GPT-Partitionierung verwenden. Legen Sie ihn auf Partition 1 als primären Datenträger fest, und übernehmen Sie die anderen Standardwerte. Das folgende Beispiel startet den `fdisk` -Prozess auf */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Verwenden Sie den Befehl `n`, um eine neue Partition hinzuzufügen. In diesem Beispiel haben wir auch `p` als primäre Partition ausgewählt und akzeptieren den Rest der Standardwerte. Die Ausgabe entspricht etwa folgendem Beispiel:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Drucken Sie die Partitionstabelle durch Eingabe von `p`, schreiben Sie dann mit `w` die Tabelle auf den Datenträger, und beenden Sie den Vorgang. Die Ausgabe sollte etwa folgendem Beispiel entsprechen:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Schreiben Sie jetzt mit dem Befehl `mkfs` ein Dateisystem auf die Partition: Geben Sie Ihren Dateisystemtyp und den Gerätenamen an. Das folgende Beispiel erstellt ein *ext4*-Dateisystem auf der */dev/sdc1*-Partition, die in den vorherigen Schritten erstellt wurde:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```
### <a name="mount-the-disk"></a>Einbinden des Datenträgers
Erstellen Sie mit `mkdir` ein Verzeichnis zum Einbinden des Dateisystems. Im folgenden Beispiel wird ein Verzeichnis mit dem Namen */datadrive* erstellt:

```bash
sudo mkdir /datadrive
```

Verwenden Sie `mount`, um anschließend das Dateisystem bereitzustellen. Im folgenden Beispiel wird die */dev/sdc1*-Partition im */datadrive*-Einbindungspunkt eingebunden:

```bash
sudo mount /dev/sdc1 /datadrive
```

Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es der Datei */etc/fstab* hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique IDentifier) in */etc/fstab* zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (z.B. */dev/sdc1*) zu verweisen. Wenn das Betriebssystem während des Starts einen Datenträgerfehler erkennt, wird durch den UUID verhindert, dass an einem bestimmten Ort der falsche Datenträger bereitgestellt wird. Die verbleibenden Datenträger werden dann diesen Geräte-IDs zugewiesen. Verwenden Sie das Hilfsprogramm `blkid`, um den UUID des neuen Laufwerks herauszufinden:

```bash
sudo -i blkid
```

Die Ausgabe sieht in etwa wie im folgenden Beispiel aus:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Eine falsche Bearbeitung der Datei **/etc/fstab** könnte zu einem nicht startfähigen System führen. Wenn Sie sich nicht sicher sind, helfen Ihnen die Informationen zur richtigen Bearbeitung dieser Datei in der Dokumentation weiter. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

Öffnen Sie anschließend die Datei */etc/fstab* in einem Text-Editor wie folgt:

```bash
sudo vi /etc/fstab
```

In diesem Beispiel verwenden Sie den UUID-Wert für das Gerät */dev/sdc1*, das in den vorherigen Schritten erstellt wurde, und den Einbindungspunkt */datadrive*. Fügen Sie am Ende der Datei */etc/fstab* die folgende Zeile hinzu:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Wenn Sie später einen Datenträger entfernen, ohne „fstab“ zu bearbeiten, kann der Start des virtuellen Computers fehlschlagen. Die meisten Verteilungen stellen entweder die „fstab“-Optionen *nofail* und/oder *nobootwait* zur Verfügung. Mit diesen Optionen kann ein System auch dann starten, wenn der Datenträger beim Start nicht bereitgestellt wird. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution.
> 
> Die *nofail*-Option stellt sicher, dass der virtuelle Computer gestartet wird, selbst wenn das Dateisystem beschädigt oder der Datenträger zur Startzeit nicht vorhanden ist. Ohne diese Option können Verhalten auftreten, die unter [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Aufgrund von FSTAB-Fehler keine SSH-Verbindung mit Linux-VM möglich) beschrieben sind.

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP-Unterstützung für Linux in Azure
Einige Linux-Kernels unterstützen TRIM/UNMAP-Vorgänge, um ungenutzte Blöcke auf dem Datenträger zu verwerfen. Dies ist in erster Linie für Standardspeicher nützlich, um Azure darüber zu informieren, dass gelöschte Seiten nicht mehr gültig sind und verworfen werden können. Dies kann auch Kosten sparen, wenn Sie große Dateien erstellen und sie dann löschen.

Es gibt zwei Methoden, TRIM-Unterstützung auf Ihrem virtuellen Linux-Computer zu aktivieren. Den empfohlenen Ansatz finden Sie wie üblich in Ihrer Distribution:

* Verwenden Sie die Bereitstellungsoption `discard` in */etc/fstab*, z.B.:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* In einigen Fällen kann sich die Option `discard` möglicherweise auf die Leistung auswirken. Alternativ können Sie den Befehl `fstrim` manuell über die Befehlszeile ausführen oder ihn „crontab“ hinzufügen, um eine regelmäßige Ausführung zu erzielen:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Nächste Schritte
Sie können auch [einen Datenträger mithilfe der Azure CLI anfügen](add-disk.md).
