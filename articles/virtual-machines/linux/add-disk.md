---
title: Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer mithilfe der Azure CLI
description: Erfahren Sie, wie Sie Ihrem virtuellen Linux-Computer mithilfe von Azure CLI einen persistenten Datenträger hinzufügen.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 9520196c8dce9ea511c2f3b799bd12b34c6f988f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499746"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer

In diesem Artikel wird gezeigt, wie Sie einen persistenten Datenträger an Ihren virtuellen Computer anfügen, um Ihre Daten beizubehalten, auch wenn der virtuelle Computer aufgrund einer Wartung oder Größenänderung neu bereitgestellt wird.


## <a name="attach-a-new-disk-to-a-vm"></a>Anfügen eines neuen Datenträgers an einen virtuellen Computer

Wenn Sie einen neuen, leeren Datenträger für Ihren virtuellen Computer hinzufügen möchten, verwenden Sie den Befehl [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) mit dem `--new`-Parameter. Wenn Ihr virtueller Computer in einer Verfügbarkeitszone ist, wird der Datenträger automatisch in derselben Zone wie der virtuelle Computer erstellt. Weitere Informationen finden Sie in der [Overview of Availability Zones (Übersicht über Verfügbarkeitszonen)](../../availability-zones/az-overview.md). Das folgende Beispiel erstellt einen Datenträger mit dem Namen *myDataDisk* mit einer Größe von 50GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Anfügen eines vorhandenen Datenträgers

Um einen vorhandenen Datenträger anzufügen, suchen Sie die Datenträger-ID, und übergeben Sie die ID an den Befehl [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest). Das folgende Beispiel fragt einen Datenträger mit dem Namen *MyDataDisk* in *MyResourceGroup* ab, und fügt ihn dann dem virtuellen Computer mit dem Namen *myVM* hinzu:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formatieren und Einbinden des Datenträgers

Sie benötigen SSH für Ihren virtuellen Computer, um den neuen Datenträger zu partitionieren, zu formatieren und bereitzustellen, damit er von Ihrem virtuellen Linux-Computer verwendet werden kann. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](mac-create-ssh-keys.md). Im folgenden Beispiel wird eine Verbindung mit einem virtuellen Computer mit der öffentlichen IP-Adresse *10.123.123.25* und dem Benutzernamen *azureuser* hergestellt:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Suchen des Datenträgers

Nachdem eine Verbindung mit dem virtuellen Computer hergestellt wurde, müssen Sie den Datenträger suchen. In diesem Beispiel wird `lsblk` zum Auflisten der Datenträger verwendet. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Hier ist `sdc` der gewünschte Datenträger, da seine Größe 50G beträgt. Wenn Sie aufgrund der Größe allein nicht sicher sind, welcher Datenträger der richtige ist, können Sie im Portal zur Seite für den virtuellen Computer navigieren, **Datenträger** auswählen und die LUN-Nummer für den Datenträger unter **Datenträger** überprüfen. 


### <a name="format-the-disk"></a>Formatieren des Datenträgers

Formatieren Sie den Datenträger mit `parted`. Wenn der Datenträger 2 TiB (Tebibytes) oder größer ist, müssen Sie die GPT-Partitionierung verwenden, wenn er kleiner als 2 TiB ist, können Sie entweder die MBR- oder die GPT-Partitionierung verwenden. 

> [!NOTE]
> Es wird empfohlen, die neueste Version von `parted` zu verwenden, die für Ihre Distribution verfügbar ist.
> Wenn der Datenträger 2 TiB (Tebibytes) oder größer ist, müssen Sie die GPT-Partitionierung verwenden. Wenn der Datenträger kleiner als 2 TiB ist, können Sie entweder die MBR- oder die GPT-Partitionierung verwenden.  


Im folgenden Beispiel wird `parted` für `/dev/sdc` verwendet. Dort befindet sich auf den meisten virtuellen Computern normalerweise der erste Datenträger. Ersetzen Sie `sdc` durch die richtige Option für Ihren Datenträger. Die Formatierung wird außerdem mithilfe des [XFS](https://xfs.wiki.kernel.org/)-Dateisystems durchgeführt.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Verwenden Sie das Hilfsprogramm [`partprobe`](https://linux.die.net/man/8/partprobe), um sicherzustellen, dass der Kernel die neue Partition und das neue Dateisystem erkennt. Wenn `partprobe` nicht verwendet wird, können die Befehle „blkid“ oder „lslbk“ nicht sofort die UUID für das neue Dateisystem zurückgeben.


### <a name="mount-the-disk"></a>Einbinden des Datenträgers

Erstellen Sie jetzt mit `mkdir` ein Verzeichnis zum Bereitstellen des neuen Dateisystems. Im folgenden Beispiel wird ein Verzeichnis unter `/datadrive` erstellt:

```bash
sudo mkdir /datadrive
```

Verwenden Sie `mount`, um anschließend das Dateisystem bereitzustellen. Im folgenden Beispiel wird die `/dev/sdc1`-Partition am Einbindungspunkt `/datadrive` eingebunden:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Beibehalten der Bereitstellung

Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es der Datei */etc/fstab* hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique Identifier) in */etc/fstab* zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (z.B. */dev/sdc1*) zu verweisen. Wenn das Betriebssystem während des Starts einen Datenträgerfehler erkennt, wird durch den UUID verhindert, dass an einem bestimmten Ort der falsche Datenträger bereitgestellt wird. Die verbleibenden Datenträger werden dann diesen Geräte-IDs zugewiesen. Verwenden Sie das Hilfsprogramm `blkid`, um den UUID des neuen Laufwerks herauszufinden:

```bash
sudo blkid
```

Die Ausgabe sieht etwa folgendermaßen aus:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Eine falsche Bearbeitung der Datei **/etc/fstab** könnte zu einem nicht startfähigen System führen. Wenn Sie sich nicht sicher sind, helfen Ihnen die Informationen zur richtigen Bearbeitung dieser Datei in der Dokumentation weiter. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

Öffnen Sie anschließend die Datei */etc/fstab* in einem Text-Editor wie folgt:

```bash
sudo nano /etc/fstab
```

In diesem Beispiel verwenden Sie den UUID-Wert für das Gerät `/dev/sdc1`, das in den vorherigen Schritten erstellt wurde, und den Einbindungspunkt `/datadrive`. Fügen Sie am Ende der Datei `/etc/fstab` die folgende Zeile hinzu:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

In diesem Beispiel wird der Nano-Editor verwendet. Daher verwenden Sie nach dem Bearbeiten der Datei die Tastenkombination `Ctrl+O`, um die Datei zu schreiben, und `Ctrl+X`, um den Editor zu beenden.

> [!NOTE]
> Wenn Sie später einen Datenträger entfernen, ohne „fstab“ zu bearbeiten, kann der Start des virtuellen Computers fehlschlagen. Die meisten Verteilungen stellen entweder die „fstab“-Optionen *nofail* und/oder *nobootwait* zur Verfügung. Mit diesen Optionen kann ein System auch dann starten, wenn der Datenträger beim Start nicht bereitgestellt wird. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution.
>
> Die *nofail*-Option stellt sicher, dass der virtuelle Computer gestartet wird, selbst wenn das Dateisystem beschädigt oder der Datenträger zur Startzeit nicht vorhanden ist. Ohne diese Option können Verhalten auftreten, die unter [Cannot SSH to Linux VM due to FSTAB errors](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) (Aufgrund von FSTAB-Fehler keine SSH-Verbindung mit Linux-VM möglich) beschrieben sind.
>
> Die serielle Konsole für Azure-VMs kann für den Konsolenzugriff auf Ihren virtuellen Computer verwendet werden, wenn die Änderung von „fstab“ zu einem Systemstartfehler geführt hat. Weitere Informationen finden Sie in der Dokumentation [Serielle Konsole für virtuelle Computer für Linux](../troubleshooting/serial-console-linux.md).

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP-Unterstützung für Linux in Azure
Einige Linux-Kernels unterstützen TRIM/UNMAP-Vorgänge, um ungenutzte Blöcke auf dem Datenträger zu verwerfen. Dies ist in erster Linie für Standardspeicher nützlich, um Azure darüber zu informieren, dass gelöschte Seiten nicht mehr gültig sind und verworfen werden können. Dies kann auch Kosten sparen, wenn Sie große Dateien erstellen und sie dann löschen.

Es gibt zwei Methoden, TRIM-Unterstützung auf Ihrem virtuellen Linux-Computer zu aktivieren. Den empfohlenen Ansatz finden Sie wie üblich in Ihrer Distribution:

* Verwenden Sie die Bereitstellungsoption `discard` in */etc/fstab*, z.B.:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
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

## <a name="troubleshooting"></a>Problembehandlung

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die Empfehlungen unter [Optimieren virtueller Linux-Computer in Azure](optimization.md) , um sicherzustellen, dass Ihr virtueller Linux-Computer richtig konfiguriert ist.
* Erweitern Sie die Speicherkapazität durch Hinzufügen zusätzlicher Datenträger, und [konfigurieren Sie RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) für zusätzliche Leistung.