---
title: Chroot-Umgebung auf einem virtuellen Linux-Rettungscomputer
description: In diesem Artikel wird beschrieben, wie Sie Probleme bei der chroot-Umgebung auf dem virtuellen Rettungscomputer (VM) unter Linux beheben.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377937"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Chroot-Umgebung auf einem virtuellen Linux-Rettungscomputer

In diesem Artikel wird beschrieben, wie Sie Probleme bei der chroot-Umgebung auf dem virtuellen Rettungscomputer (VM) unter Linux beheben.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16.x und Ubuntu 18.x

1. Entfernen Sie den betroffenen virtuellen Computer, oder heben Sie dessen Zuordnung auf.
1. Erstellen Sie mithilfe eines verwalteten Datenträgers ein VM-Rettungsimage derselben Betriebssystemversion in derselben Ressourcengruppe (RSG) und an demselben Standort.
1. Verwenden Sie das Azure-Portal, um eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers zu erstellen.
1. Erstellen Sie einen Datenträger aus der Momentaufnahme des Betriebssystemdatenträgers, und fügen Sie ihn an den virtuellen Rettungscomputer an.
1. Nachdem der Datenträger erstellt wurde, beheben Sie die Probleme in der chroot-Umgebung auf dem virtuellen Rettungscomputer.

   1. Greifen Sie mit dem folgenden Befehl auf Ihre VM als Stammbenutzer zu:

      `sudo su -`

   1. Suchen Sie den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird **dmesg** zum Filtern auf **SCSI** -Datenträgern verwendet:

      `dmesg | grep SCSI`

      Ihre Ausgabe entspricht etwa folgendem Beispiel: In diesem Beispiel soll der **SDC** -Datenträger Folgendes durchführen:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Die folgenden Befehle für den Zugriff auf die chroot-Umgebung verwenden:

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Behandeln Sie die Probleme der chroot-Umgebung.

   1. Die folgenden Befehle zum Beenden der chroot-Umgebung verwenden:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > Wenn Sie die Fehlermeldung `unable to unmount /rescue` erhalten, fügen Sie dem Befehl „umount“ die Option „-l“ hinzu.
      >
      > Beispiel: `umount -l /rescue`

1. Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger gegen den ursprünglichen virtuellen Computer aus.
1. Starten Sie den ursprünglichen virtuellen Computer, und überprüfen Sie die Konnektivität.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/Centos/Oracle 6.x und Oracle 8.x und RHEL/Centos 7.x mit RAW-Partitionen

1. Entfernen Sie den betroffenen virtuellen Computer, oder heben Sie dessen Zuordnung auf.
1. Erstellen Sie mithilfe eines verwalteten Datenträgers ein VM-Rettungsimage derselben Betriebssystemversion in derselben Ressourcengruppe (RSG) und an demselben Standort.
1. Verwenden Sie das Azure-Portal, um eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers zu erstellen.
1. Erstellen Sie einen Datenträger aus der Momentaufnahme des Betriebssystemdatenträgers, und fügen Sie ihn an den virtuellen Rettungscomputer an.
1. Nachdem der Datenträger erstellt wurde, beheben Sie die Probleme in der chroot-Umgebung auf dem virtuellen Rettungscomputer.

   1. Greifen Sie mit dem folgenden Befehl auf Ihre VM als Stammbenutzer zu:

      `sudo su -`

   1. Suchen Sie den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird **dmesg** zum Filtern auf **SCSI** -Datenträgern verwendet:

      `dmesg | grep SCSI`

      Ihre Ausgabe entspricht etwa folgendem Beispiel: In diesem Beispiel soll der **SDC** -Datenträger Folgendes durchführen:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Die folgenden Befehle für den Zugriff auf die chroot-Umgebung verwenden:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Behandeln Sie die Probleme der chroot-Umgebung.

   1. Die folgenden Befehle zum Beenden der chroot-Umgebung verwenden:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > Wenn Sie die Fehlermeldung `unable to unmount /rescue` erhalten, fügen Sie dem Befehl „umount“ die Option „-l“ hinzu.
      >
      > Beispiel: `umount -l /rescue`

1. Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger gegen den ursprünglichen virtuellen Computer aus.
1. Starten Sie den ursprünglichen virtuellen Computer, und überprüfen Sie die Konnektivität.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/Centos 7.x mit LVM

   > [!NOTE]
   > Wenn Ihr ursprünglicher virtueller Computer den Logical Volume Manager (LVM) auf dem Betriebssystemdatenträger enthält, erstellen Sie den virtuellen Rettungscomputer unter Verwendung des Images mit RAW-Partitionen auf dem Betriebssystemdatenträger.

1. Entfernen Sie den betroffenen virtuellen Computer, oder heben Sie dessen Zuordnung auf.
1. Erstellen Sie mithilfe eines verwalteten Datenträgers ein VM-Rettungsimage derselben Betriebssystemversion in derselben Ressourcengruppe (RSG) und an demselben Standort.
1. Verwenden Sie das Azure-Portal, um eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers zu erstellen.
1. Erstellen Sie einen Datenträger aus der Momentaufnahme des Betriebssystemdatenträgers, und fügen Sie ihn an den virtuellen Rettungscomputer an.
1. Nachdem der Datenträger erstellt wurde, beheben Sie die Probleme in der chroot-Umgebung auf dem virtuellen Rettungscomputer.

   1. Greifen Sie mit dem folgenden Befehl auf Ihre VM als Stammbenutzer zu:

      `sudo su -`

   1. Suchen Sie den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird **dmesg** zum Filtern auf **SCSI** -Datenträgern verwendet:

      `dmesg | grep SCSI`

      Ihre Ausgabe entspricht etwa folgendem Beispiel: In diesem Beispiel soll der **SDC** -Datenträger Folgendes durchführen:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Verwenden Sie den folgenden Befehl, um die logische Volumegruppe zu aktivieren:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Verwenden Sie den Befehl `lsblk`, um die LVM-Namen abzurufen:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Die folgenden Befehle für den Zugriff auf die chroot-Umgebung verwenden:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Behandeln Sie die Probleme der chroot-Umgebung.

   1. Die folgenden Befehle zum Beenden der chroot-Umgebung verwenden:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > Wenn Sie die Fehlermeldung `unable to unmount /rescue` erhalten, fügen Sie dem Befehl „umount“ die Option „-l“ hinzu.
      >
      > Beispiel: `umount -l /rescue`

1. Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger gegen den ursprünglichen virtuellen Computer aus.
1. Starten Sie den ursprünglichen virtuellen Computer, und überprüfen Sie die Konnektivität.

## <a name="rhel-8x-with-lvm"></a>RHEL 8.x mit LVM

   > [!NOTE]
   > Wenn Ihr ursprünglicher virtueller Computer den Logical Volume Manager (LVM) auf dem Betriebssystemdatenträger enthält, erstellen Sie den virtuellen Rettungscomputer unter Verwendung des Images mit RAW-Partitionen auf dem Betriebssystemdatenträger.

1. Entfernen Sie den betroffenen virtuellen Computer, oder heben Sie dessen Zuordnung auf.
1. Erstellen Sie mithilfe eines verwalteten Datenträgers ein VM-Rettungsimage derselben Betriebssystemversion in derselben Ressourcengruppe (RSG) und an demselben Standort.
1. Verwenden Sie das Azure-Portal, um eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers zu erstellen.
1. Erstellen Sie einen Datenträger aus der Momentaufnahme des Betriebssystemdatenträgers, und fügen Sie ihn an den virtuellen Rettungscomputer an.
1. Nachdem der Datenträger erstellt wurde, beheben Sie die Probleme in der chroot-Umgebung auf dem virtuellen Rettungscomputer.

   1. Greifen Sie mit dem folgenden Befehl auf Ihre VM als Stammbenutzer zu:

      `sudo su -`

   1. Suchen Sie den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird **dmesg** zum Filtern auf **SCSI** -Datenträgern verwendet:

      `dmesg | grep SCSI`

      Ihre Ausgabe entspricht etwa folgendem Beispiel: In diesem Beispiel soll der **SDC** -Datenträger Folgendes durchführen:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Verwenden Sie den folgenden Befehl, um die logische Volumegruppe zu aktivieren:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. Verwenden Sie den Befehl `lsblk`, um die LVM-Namen abzurufen:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Die folgenden Befehle für den Zugriff auf die chroot-Umgebung verwenden:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Behandeln Sie die Probleme der chroot-Umgebung.

   1. Die folgenden Befehle zum Beenden der chroot-Umgebung verwenden:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > Wenn Sie die Fehlermeldung `unable to unmount /rescue` erhalten, fügen Sie dem Befehl „umount“ die Option „-l“ hinzu.
      >
      > Beispiel: `umount -l /rescue`

1. Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger gegen den ursprünglichen virtuellen Computer aus.
1. Starten Sie den ursprünglichen virtuellen Computer, und überprüfen Sie die Konnektivität.

## <a name="oracle-7x"></a>Oracle 7.x

1. Entfernen Sie den betroffenen virtuellen Computer, oder heben Sie dessen Zuordnung auf.
1. Erstellen Sie mithilfe eines verwalteten Datenträgers ein VM-Rettungsimage derselben Betriebssystemversion in derselben Ressourcengruppe (RSG) und an demselben Standort.
1. Verwenden Sie das Azure-Portal, um eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers zu erstellen.
1. Erstellen Sie einen Datenträger aus der Momentaufnahme des Betriebssystemdatenträgers, und fügen Sie ihn an den virtuellen Rettungscomputer an.
1. Nachdem der Datenträger erstellt wurde, beheben Sie die Probleme in der chroot-Umgebung auf dem virtuellen Rettungscomputer.

   1. Greifen Sie mit dem folgenden Befehl auf Ihre VM als Stammbenutzer zu:

      `sudo su -`

   1. Suchen Sie den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird **dmesg** zum Filtern auf **SCSI** -Datenträgern verwendet:

      `dmesg | grep SCSI`

      Ihre Ausgabe entspricht etwa folgendem Beispiel: In diesem Beispiel soll der **SDC** -Datenträger Folgendes durchführen:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Die folgenden Befehle für den Zugriff auf die chroot-Umgebung verwenden:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Behandeln Sie die Probleme der chroot-Umgebung.

   1. Die folgenden Befehle zum Beenden der chroot-Umgebung verwenden:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Wenn Sie die Fehlermeldung `unable to unmount /rescue` erhalten, fügen Sie dem Befehl „umount“ die Option „-l“ hinzu.
      >
      > Beispiel: `umount -l /rescue`

1. Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger gegen den ursprünglichen virtuellen Computer aus.
1. Starten Sie den ursprünglichen virtuellen Computer, und überprüfen Sie die Konnektivität.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 für SAP && ## SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 für SAP

1. Entfernen Sie den betroffenen virtuellen Computer, oder heben Sie dessen Zuordnung auf.
1. Erstellen Sie mithilfe eines verwalteten Datenträgers ein VM-Rettungsimage derselben Betriebssystemversion in derselben Ressourcengruppe (RSG) und an demselben Standort.
1. Verwenden Sie das Azure-Portal, um eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers zu erstellen.
1. Erstellen Sie einen Datenträger aus der Momentaufnahme des Betriebssystemdatenträgers, und fügen Sie ihn an den virtuellen Rettungscomputer an.
1. Nachdem der Datenträger erstellt wurde, beheben Sie die Probleme in der chroot-Umgebung auf dem virtuellen Rettungscomputer.

   1. Greifen Sie mit dem folgenden Befehl auf Ihre VM als Stammbenutzer zu:

      `sudo su -`

   1. Suchen Sie den Datenträger mithilfe von `dmesg` (die Methode, die Sie zum Ermitteln Ihres neuen Datenträgers verwenden, kann möglicherweise anders sein). Im folgenden Beispiel wird **dmesg** zum Filtern auf **SCSI** -Datenträgern verwendet:

      `dmesg | grep SCSI`

      Ihre Ausgabe entspricht etwa folgendem Beispiel: In diesem Beispiel soll der **SDC** -Datenträger Folgendes durchführen:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Die folgenden Befehle für den Zugriff auf die chroot-Umgebung verwenden:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Behandeln Sie die Probleme der chroot-Umgebung.

   1. Die folgenden Befehle zum Beenden der chroot-Umgebung verwenden:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Wenn Sie die Fehlermeldung `unable to unmount /rescue` erhalten, fügen Sie dem Befehl „umount“ die Option „-l“ hinzu.
      >
      > Beispiel: `umount -l /rescue`

1. Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger gegen den ursprünglichen virtuellen Computer aus.
1. Starten Sie den ursprünglichen virtuellen Computer, und überprüfen Sie die Konnektivität.

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von SSH-Verbindungsproblemen](troubleshoot-ssh-connection.md)
