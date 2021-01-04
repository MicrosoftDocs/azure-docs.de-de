---
title: Anfügen eines Datenträgers an einen virtuellen Linux-Computer
description: Verwenden Sie das Portal, um einen neuen oder vorhandenen Datenträger an einen virtuellen Linux-Computer anzufügen.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 8f60c83417e9c614ca30f140e6acbbf08e5643cf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500647"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Anfügen eines Datenträgers an einen virtuellen Linux-Computer mithilfe des Portals 
In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal neue und vorhandene Datenträger an einen virtuellen Linux-Computer anfügen können. Sie können auch [einen Datenträger an eine Windows-VM im Azure-Portal anfügen](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Bevor Sie Datenträger an Ihren virtuellen Computer anfügen, lesen Sie diese Tipps:

* Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Bei an virtuelle Computer angefügten Datenträgern handelt es sich um VHD-Dateien, die in Azure gespeichert sind. Details finden Sie in unserer [Einführung in verwaltete Datenträger](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Nach dem Anfügen des Datenträgers müssen Sie [eine Verbindung mit dem virtuellen Linux-Computer zum Bereitstellen des neuen Datenträgers herstellen](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Suchen des virtuellen Computers
1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach dem virtuellen Computer. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.
2. Wählen Sie die VM aus der Liste aus.
3. Wählen Sie auf der Seite **Virtuelle Computer** unter **Einstellungen** die Option **Datenträger** aus.


## <a name="attach-a-new-disk"></a>Anfügen eines neuen Datenträgers

1. Wählen Sie im Bereich **Datenträger** unter **Datenträger** die Option **Neuen Datenträger erstellen und anfügen** aus.

1. Geben Sie einen Namen für Ihren verwalteten Datenträger ein. Überprüfen Sie die Standardeinstellungen, und aktualisieren Sie bei Bedarf die Werte für **Speichertyp**, **Größe (GiB)** , **Verschlüsselung** und **Hostzwischenspeicherung**.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Überprüfen der Datenträgereinstellungen":::


1. Wählen Sie anschließend am oberen Rand der Seite **Speichern** aus, um den verwalteten Datenträger zu erstellen und die VM-Konfiguration zu aktualisieren.


## <a name="attach-an-existing-disk"></a>Anfügen eines vorhandenen Datenträgers
1. Wählen Sie im Bereich **Datenträger** unter **Datenträger** die Option **Attach existing disks** (Vorhandene Datenträger anfügen) aus.
1. Klicken Sie auf das Dropdownmenü für **Datenträgername**, und wählen Sie in der Liste der verfügbaren verwalteten Datenträger einen Datenträger aus. 

1. Klicken Sie auf **Speichern**, um den vorhandenen verwalteten Datenträger anzufügen und die Konfiguration des virtuellen Computers zu aktualisieren:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Herstellen einer Verbindung mit dem virtuellen Linux-Computer zum Bereitstellen des neuen Datenträgers
Sie benötigen SSH für Ihren virtuellen Computer, um den neuen Datenträger zu partitionieren, zu formatieren und bereitzustellen, damit er von Ihrem virtuellen Linux-Computer verwendet werden kann. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](mac-create-ssh-keys.md). Im folgenden Beispiel wird eine Verbindung mit einem virtuellen Computer mit der öffentlichen IP-Adresse *10.123.123.25* und dem Benutzernamen *azureuser* hergestellt: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Suchen des Datenträgers

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
sdc     3:0:0:0       4G
```

In diesem Beispiel wurde der Datenträger `sdc` hinzugefügt. Hierbei handelt es sich um LUN 0 mit 4 GB.

Das folgende komplexere Beispiel zeigt, wie mehrere Datenträger im Portal aussehen:

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Überprüfen der Datenträgereinstellungen":::

In der Abbildung sehen Sie, dass drei Datenträger vorhanden sind: 4 GB in LUN 0, 16 GB in LUN 1 und 32 GB in LUN 2.

Dies kann bei Verwendung von `lsblk` wie folgt aussehen:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

In der Ausgabe von `lsblk` sehen Sie, dass `sdc` der Datenträger mit 4 GB in LUN 0, `sdd` der Datenträger mit 16 GB in LUN 1 und `sde` der Datenträger mit 32 GB in LUN 2 ist.

### <a name="partition-a-new-disk"></a>Partitionieren eines neuen Datenträgers

Wenn Sie einen vorhandenen Datenträger verwenden, der Daten enthält, überspringen Sie die Schritte bis zum Einbinden des Datenträgers. Wenn Sie einen neuen Datenträger anfügen, müssen Sie den Datenträger partitionieren.

Das Hilfsprogramm `parted` kann zum Partitionieren und Formatieren eines Datenträgers verwendet werden.

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

Erstellen Sie mit `mkdir` ein Verzeichnis zum Einbinden des Dateisystems. Im folgenden Beispiel wird ein Verzeichnis unter `/datadrive` erstellt:

```bash
sudo mkdir /datadrive
```

Verwenden Sie `mount`, um anschließend das Dateisystem bereitzustellen. Im folgenden Beispiel wird die Partition */dev/sdc1* am Einbindungspunkt `/datadrive` eingebunden:

```bash
sudo mount /dev/sdc1 /datadrive
```

Um sicherzustellen, dass das Laufwerk nach einem Neustart automatisch wieder eingebunden wird, muss es der Datei */etc/fstab* hinzugefügt werden. Außerdem wird dringend empfohlen, den UUID (Universally Unique Identifier, universell eindeutiger Bezeichner) in */etc/fstab* zu verwenden, um auf das Laufwerk und nicht auf den Gerätenamen (z. B. */dev/sdc1*) zu verweisen. Wenn das Betriebssystem während des Starts einen Datenträgerfehler erkennt, wird durch den UUID verhindert, dass an einem bestimmten Ort der falsche Datenträger bereitgestellt wird. Die verbleibenden Datenträger werden dann diesen Geräte-IDs zugewiesen. Verwenden Sie das Hilfsprogramm `blkid`, um den UUID des neuen Laufwerks herauszufinden:

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

Hier wurde der Nano-Editor verwendet. Daher verwenden Sie nach dem Bearbeiten der Datei die Tastenkombination `Ctrl+O`, um die Datei zu schreiben, und `Ctrl+X`, um den Editor zu beenden.

> [!NOTE]
> Wenn Sie später einen Datenträger entfernen, ohne „fstab“ zu bearbeiten, kann der Start des virtuellen Computers fehlschlagen. Die meisten Verteilungen stellen entweder die „fstab“-Optionen *nofail* und/oder *nobootwait* zur Verfügung. Mit diesen Optionen kann ein System auch dann starten, wenn der Datenträger beim Start nicht bereitgestellt wird. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution.
> 
> Die *nofail*-Option stellt sicher, dass der virtuelle Computer gestartet wird, selbst wenn das Dateisystem beschädigt oder der Datenträger zur Startzeit nicht vorhanden ist. Ohne diese Option können Verhalten auftreten, die unter [Cannot SSH to Linux VM due to FSTAB errors](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) (Aufgrund von FSTAB-Fehler keine SSH-Verbindung mit Linux-VM möglich) beschrieben sind.


## <a name="verify-the-disk"></a>Überprüfen des Datenträgers

Sie können jetzt `lsblk` erneut verwenden, um den Datenträger und den Einbindungspunkt anzuzeigen.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Die Ausgabe sieht in etwa wie folgt aus:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Sie sehen, dass `sdc` jetzt in `/datadrive` eingebunden ist.

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

## <a name="next-steps"></a>Nächste Schritte
Sie können auch [einen Datenträger mithilfe der Azure CLI anfügen](add-disk.md).