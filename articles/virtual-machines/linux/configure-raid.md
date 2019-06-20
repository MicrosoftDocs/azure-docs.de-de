---
title: Konfigurieren von Software-RAID auf einem virtuellen Computer unter Linux | Microsoft Docs
description: Erfahren Sie, wie Sie mdadm zum Konfigurieren von RAID unter Linux in Azure verwenden.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: e773fdcb031f0f8f896ea40d76231fd54a603dc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328798"
---
# <a name="configure-software-raid-on-linux"></a>Konfigurieren von Software-RAID unter Linux
Ein häufiges Szenario ist die Verwendung von Software-RAID auf virtuellen Linux-Computern in Azure, um mehrere angefügte Datenträger als einzelnes RAID-Gerät darzustellen. Dies kann normalerweise angewendet werden, um die Leistung zu verbessern und optimierten Durchsatz im Vergleich zur Verwendung eines einzelnen Datenträgers zu ermöglichen.

## <a name="attaching-data-disks"></a>Anfügen von Datenträgern
Es sind zwei oder mehr leere Datenträger erforderlich, um ein RAID-Gerät zu konfigurieren.  Der Hauptgrund für die Erstellung eines RAID-Geräts ist die Leistungsverbesserung Ihrer Datenträger-E/A.  Basierend auf Ihren E/A-Anforderungen können Sie wählen, in unserem Standardspeicher gespeicherte Datenträger mit bis zu 500 IOPS und Datenträger anzufügen oder in unserem Premiumspeicher gespeicherte Datenträger mit bis zu 5000 IOPS und Datenträger anzufügen. In diesem Artikel wird nicht erläutert, wie Sie einem virtuellen Linux-Computer Datenträger bereitstellen und an diesen anfügen.  Eine ausführliche Anleitung, wie Sie einen leeren Datenträger an einen virtuellen Linux-Computer in Azure anfügen, finden Sie im Microsoft Azure-Artikel [Anfügen eines Datenträgers](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

## <a name="install-the-mdadm-utility"></a>Installieren des mdadm-Dienstprogramms
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS und Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES und openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Erstellen der Datenträgerpartitionen
In diesem Beispiel erstellen wir eine einzelne Datenträgerpartition unter „/dev/sdc“. Die neue Datenträgerpartition wird „/dev/sdc1“ genannt.

1. Starten Sie `fdisk`, um mit dem Erstellen der Partitionen zu beginnen.

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. Drücken Sie an der Eingabeaufforderung 'n', um eine **n**eue Partition zu erstellen:

    ```bash
    Command (m for help): n
    ```

1. Drücken Sie anschließend 'p', um ein **p**rimäre Partition zu erstellen:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Drücken Sie '1', um Partitionsnummer 1 auszuwählen:

    ```bash
    Partition number (1-4): 1
    ```

1. Wählen Sie den Ausgangspunkt der neuen Partition, oder drücken Sie `<enter>`, um die Standardeinstellung zu akzeptieren und die Partition am Anfang des freien Speicherplatzes auf dem Laufwerk zu platzieren:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Legen Sie die Größe der Partition fest, z. B. '+10G', um eine 10-Gigabyte-Partition zu erstellen. Oder drücken Sie einfach `<enter>`, um eine einzelne Partition zu erstellen, die das gesamte Laufwerk umfasst:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ändern Sie anschließend die ID und den Partitions **t**yp von der Standard-ID '83' (Linux) in ID 'fd' (Linux-RAID automatisch):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Zum Abschluss schreiben Sie die Partitionstabelle in das Laufwerk und beenden "fdisk":

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Erstellen des RAID-Arrays
1. Im folgenden Beispiel werden drei Partitionen auf drei separaten Datenträgern (sdc1, sdd1, sde1) zu einem RAID (Level 0) verbunden.  Nach Ausführung dieses Befehls wird ein neues RAID namens **/dev/md127** erstellt. Beachten Sie außerdem Folgendes: Falls diese Datenträger zuvor Teil eines anderen, nicht mehr bestehenden RAID-Arrays waren, muss der Befehl `mdadm` unter Umständen mit dem Parameter `--force` versehen werden:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Erstellen des Dateisystems auf dem neuen RAID-Gerät
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE und Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11**: „boot.md“ aktivieren und „mdadm.conf“ erstellen

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Nach den Änderungen an den SUSE-Systemen kann ein Neustart erforderlich sein. Dieser Schritt ist für SLES 12 *nicht* erforderlich.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Hinzufügen des neuen Laufwerks zu "/etc/fstab"
> [!IMPORTANT]
> Eine falsche Bearbeitung der Datei /etc/fstab könnte zu einem nicht startfähigen System führen. Wenn Sie sich nicht sicher sind, helfen Ihnen die Informationen zur richtigen Bearbeitung dieser Datei in der Dokumentation weiter. Außerdem wird empfohlen, ein Backup der Datei /etc/fstab zu erstellen, bevor Sie sie bearbeiten.

1. Erstellen Sie den gewünschten Bereitstellungspunkt für das neue Dateisystem, zum Beispiel:

    ```bash
    sudo mkdir /data
    ```
1. Beim Bearbeiten von /etc/fstab sollte die **UUID** verwendet werden, um auf das Dateisystem zu verweisen statt auf den Gerätenamen.  Verwenden Sie das Hilfsprogramm `blkid` , um die UUID für das neue Dateisystem zu bestimmen:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. Öffnen Sie "/etc/fstab" in einem Texteditor, und fügen Sie einen Eintrag für das neue Dateisystem hinzu, z. B.:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    Verwenden Sie bei **SLES 11** Folgendes:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Speichern und schließen Sie anschließend /etc/fstab.

1. Testen Sie, ob der Eintrag für /etc/fstab korrekt ist:

    ```bash  
    sudo mount -a
    ```

    Wenn dieser Befehl zu einer Fehlermeldung führt, überprüfen Sie die Syntax in der Datei „/etc/fstab“.
   
    Führen Sie nun den Befehl `mount` aus, um sicherzustellen, dass das Dateisystem eingebunden wurde:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (Optional) Failsafe-Startparameter
   
    **fstab-Konfiguration**
   
    Viele Distributionen beinhalten den Bereitstellungsparameter `nobootwait` oder `nofail`, der der Datei „/etc/fstab“ hinzugefügt werden kann. Diese Parameter lassen Fehler beim Bereitstellen eines bestimmten Dateisystems zu und ermöglichen dem Linux-System das Fortsetzen des Startvorgangs, selbst wenn das RAID-Dateisystem nicht ordnungsgemäß geladen werden kann. Weitere Informationen zu diesen Parametern erhalten Sie in der Dokumentation der Distribution.
   
    Beispiel (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux-Startparameter**
   
    Zusätzlich zu den oben angegebenen Parametern kann der Kernelparameter „`bootdegraded=true`“ das Starten des Systems ermöglichen, selbst wenn das RAID-System beschädigt oder beeinträchtigt ist, weil beispielsweise unabsichtlich ein Datenträger aus dem virtuellen Computer entfernt wurde. Normalerweise kann dies auch den Start des Systems verhindern.
   
    Informationen zum Bearbeiten der Kernel-Parameter erhalten Sie in der Dokumentation der Distribution. Beispielsweise können diese Parameter der Datei „`/boot/grub/menu.lst`“ in vielen Distributionen (CentOS, Oracle Linux, SLES 11) manuell hinzugefügt werden.  In Ubuntu kann dieser Parameter der Variablen `GRUB_CMDLINE_LINUX_DEFAULT` unter „/etc/default/grub“ hinzugefügt werden.


## <a name="trimunmap-support"></a>TRIM/UNMAP-Unterstützung
Einige Linux-Kernels unterstützen TRIM/UNMAP-Vorgänge, um ungenutzte Blöcke auf dem Datenträger zu verwerfen. Diese Vorgänge sind in erster Linie für Standardspeicher nützlich, um Azure darüber zu informieren, dass gelöschte Seiten nicht mehr gültig sind und verworfen werden können. Das Verwerfen von Seiten kann Kosten sparen, wenn Sie große Dateien erstellen und diese dann löschen.

> [!NOTE]
> Mit RAID werden Befehle zum Verwerfen möglicherweise nicht ausgegeben, wenn die Blockgröße für das Array auf einen kleineren Wert als der Standardwert (512 KB) festgelegt ist. Der Grund dafür ist, dass die Granularität für die Aufhebung der Zuordnung auf dem Host ebenfalls auf 512 KB festgelegt ist. Wenn Sie die Blockgröße des Arrays über den mdadm-Parameter `--chunk=` geändert haben, werden TRIM/UNMAP-Anforderungen vom Kernel ignoriert.

Es gibt zwei Methoden, TRIM-Unterstützung auf Ihrem virtuellen Linux-Computer zu aktivieren. Den empfohlenen Ansatz finden Sie wie üblich in Ihrer Distribution:

- Verwenden Sie die Bereitstellungsoption `discard` in `/etc/fstab`, z.B.:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- In einigen Fällen kann sich die Option `discard` auf die Leistung auswirken. Alternativ können Sie den Befehl `fstrim` manuell über die Befehlszeile ausführen oder ihn „crontab“ hinzufügen, um eine regelmäßige Ausführung zu erzielen:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
