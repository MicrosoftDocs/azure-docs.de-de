---
title: Ändern der Größe eines Betriebssystem-Datenträgers, der eine GPT-Partition aufweist
description: Dieser Artikel enthält Anweisungen zum Ändern der Größe eines Betriebssystemdatenträgers mit einer GPT-Partition (GUID-Partitionstabelle) unter Linux.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 3565b165c669af3566667d9bdfa401d15fcce101
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544155"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ändern der Größe eines Betriebssystem-Datenträgers, der eine GPT-Partition aufweist

> [!NOTE]
> Dieser Artikel ist nur für Betriebssystemdatenträger mit einer GPT-Partition (GUID-Partitionstabelle) relevant.

In diesem Artikel wird beschrieben, wie Sie die Größe eines Betriebssystem-Datenträgers heraufsetzen, der eine GPT-Partition unter Linux aufweist. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Bestimmen, ob der Betriebssystem-Datenträger eine MBR- oder eine GPT-Partition aufweist

Verwenden Sie den Befehl `parted`, um zu ermitteln, ob die Datenträgerpartition mit einer MBR-Partition (Master Boot Record) oder einer GPT-Partition erstellt wurde.

### <a name="mbr-partition"></a>MBR-Partition

In der folgenden Ausgabe zeigt **Partition Table**  den Wert **msdos**. Dieser Wert kennzeichnet eine MBR-Partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-Partition

In der folgenden Ausgabe zeigt **Partition Table**  den Wert **gpt**. Dieser Wert kennzeichnet eine GPT-Partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Wenn Ihr virtueller Computer (VM) eine GPT-Partition für Ihren Betriebssystem-Datenträger aufweist, setzen Sie die Größe des Betriebssystem-Datenträgers herauf.

## <a name="increase-the-size-of-the-os-disk"></a>Heraufsetzen der Größe des Betriebssystem-Datenträgers

Die folgenden Anweisungen betreffen Linux-gestützte Verteilungen.

> [!NOTE]
> Bevor Sie fortfahren, erstellen Sie eine Sicherungskopie Ihrer VM oder eine Momentaufnahme Ihres Betriebssystem-Datenträgers.

### <a name="ubuntu"></a>Ubuntu

So setzen Sie die Größe des Betriebssystemdatenträgers in Ubuntu 16.*x* und 18.*x* herauf:

1. Beenden Sie den virtuellen Computer.
1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
1. Führen Sie einen Neustart der VM aus, und melden Sie sich dann bei der VM als **Root**-Benutzer an.
1. Vergewissern Sie sich, dass der Betriebssystem-Datenträger jetzt ein größeres Dateisystem anzeigt.

Wie im folgenden Beispiel gezeigt, wurde der Betriebssystemdatenträger im Portal auf 100 GB vergrößert. Das auf **/** eingebundene Dateisystem **/dev/sda1** zeigt jetzt 97 GB an.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

So setzen Sie die Größe des Betriebssystem-Datenträgers in SUSE 12 SP4, SUSE SLES 12 for SAP, SUSE SLES 15 und SUSE SLES 15 for SAP herauf:

1. Beenden Sie den virtuellen Computer.
1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
1. Starten Sie den virtuellen Computer neu.

Wenn der virtuelle Computer neu gestartet wurde, führen Sie diese Schritte aus:

1. Greifen Sie als **Root**-Benutzer mit folgendem Befehl auf Ihre VM zu:

   ```
   # sudo -i
   ```

1. Verwenden Sie den folgenden Befehl, um das Paket **growpart** zu installieren, das zum Ändern der Größe der Partition verwendet wird:

   ```
   # zypper install growpart
   ```

1. Verwenden Sie den Befehl `lsblk`, um die Partition zu suchen, die im Stammverzeichnis des Dateisystems ( **/** ) eingebunden ist. In diesem Fall sehen Sie, dass Partition 4 des Geräts **sda** unter **/** eingebunden ist:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Ändern Sie die Größe der erforderlichen Partition mit dem Befehl `growpart` und der im vorherigen Schritt bestimmten Partitionsnummer:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Führen Sie erneut den Befehl `lsblk` aus, um zu überprüfen, ob die Partition vergrößert wurde.

   Die folgende Ausgabe zeigt, dass die Größe der Partition **/dev/sda4** in 46,5 GB geändert wurde:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifizieren Sie den Typ des Dateisystems auf dem Betriebssystemdatenträger mithilfe des Befehls `lsblk` mit dem Flag `-f`:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Verwenden Sie die für den Typ des Dateisystems passenden Befehle, um die Größe des Dateisystems zu ändern.
   
   Verwenden Sie für **xfs** diesen Befehl:
   
   ```
   #xfs_growfs /
   ```
   
   Beispielausgabe:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Verwenden Sie für **ext4** diesen Befehl:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Überprüfen Sie die heraufgesetzte Größe des Dateisystems für **df -Th** mit diesem Befehl:
   
   ```
   #df -Thl
   ```
   
   Beispielausgabe:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Im vorherigen Beispiel können wir sehen, dass die Größe des Dateisystems für den Betriebssystem-Datenträger heraufgesetzt wurde.

### <a name="rhel-with-lvm"></a>RHEL mit LVM

1. Greifen Sie als **Root**-Benutzer mit diesem Befehl auf Ihre VM zu:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Verwenden Sie den Befehl `lsblk`, um das logische Volume (LV) zu suchen, das im Stammverzeichnis des Dateisystems ( **/** ) eingebunden ist. In diesem Fall sehen Sie, dass **rootvg-rootlv** unter **/** eingebunden ist. Wenn Sie ein anderes Dateisystem benötigen, ersetzen Sie den LV- und Bereitstellungspunkt in diesem Artikel durchgängig.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Überprüfen Sie, ob in der LVM-Volumegruppe (VG) mit der Stammpartition freier Speicherplatz verfügbar ist. Wenn freier Speicherplatz verfügbar ist, fahren Sie mit Schritt 12 fort.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   In diesem Beispiel gibt die Zeile **Free PE / Size** an, dass in der Volumegruppe 38,02 GB frei sind. Sie müssen die Größe des Datenträgers nicht ändern, bevor Sie der Volumegruppe Speicherplatz hinzufügen.

1. So setzen Sie die Größe des Betriebssystemdatenträgers in RHEL 7.*x* mit LVM herauf:

   1. Beenden Sie den virtuellen Computer.
   1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
   1. Starten Sie den virtuellen Computer.

1. Installieren Sie nach dem Neustart der VM das Paket **cloud-utils-growpart**, um den Befehl `growpart` abzurufen, der zum Vergrößern des Betriebssystemdatenträgers erforderlich ist.

      Dieses Paket ist auf den meisten Azure Marketplace-Images vorinstalliert.

      ```bash
      [root@dd-rhel7vm ~]# yum install cloud-utils-growpart
      ```

1. Ermitteln Sie mit dem Befehl `pvscan`, auf welchem Datenträger und welcher Partition das physische LVM-Volume bzw. die Volumes (PV) in der Volumegruppe mit dem Namen **rootvg** enthalten sind. Notieren Sie sich die Größe und den freien Speicherplatz, die zwischen den Klammern ( **[** und **]** ) angegeben sind.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Überprüfen Sie die Größe der Partition mit `lsblk`. 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Erweitern Sie die Partition mit dieser PV mit `growpart`, dem Gerätenamen und der Partitionsnummer. Dadurch wird die angegebene Partition so erweitert, dass der gesamte freie zusammenhängende Speicherplatz auf dem Gerät verwendet wird.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Überprüfen Sie erneut mit dem Befehl `lsblk`, ob die Größe der Partition wie erwartet geändert wurde. Beachten Sie, dass in diesem Beispiel **sda4** von 63 GB in 95 GB geändert wurde.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Erweitern Sie das PV zur Verwendung der restlichen neu erweiterten Partition:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Überprüfen Sie, ob die neue Größe des PV der erwarteten Größe entspricht, und vergleichen Sie dabei die ursprünglichen Werte für **[Größe/freien Speicherplatz]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Erweitern Sie das gewünschte logische Volume (LV) um die gewünschte Menge. Die Menge muss nicht der gesamte freie Speicherplatz in der Volumegruppe sein. Im folgenden Beispiel wird die Größe von **/dev/mapper/rootvg-rootlv** von 2 GB auf 12 GB heraufgesetzt (um 10 GB). Mit diesem Befehl wird außerdem die Größe des Dateisystems heraufgesetzt.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Beispielausgabe:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. Mit dem Befehl `lvresize` wird automatisch der entsprechende Befehl zum Ändern der Größe für das Dateisystem im LV aufgerufen. Überprüfen Sie mithilfe des folgenden Befehls, ob die Größe des Dateisystems unter **/dev/mapper/rootvg-rootlv** (unter **/** eingebunden) erweitert wurde:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Beispielausgabe:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Um die gleiche Vorgehensweise zum Ändern der Größe jedes beliebigen anderen logischen Volumes zu verwenden, ändern Sie in Schritt 12 den LV-Namen.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>Erstellen Sie immer eine Momentaufnahme des virtuellen Computers, bevor Sie den Betriebssystemdatenträger vergrößern.

So setzen Sie die Größe des Betriebssystemdatenträgers in einer RHEL-RAW-Partition herauf:

1. Beenden Sie den virtuellen Computer.
1. Heraufsetzen der Größe des Betriebssystem-Datenträgers im Portal.
1. Starten Sie den virtuellen Computer.

Wenn der virtuelle Computer neu gestartet wurde, führen Sie diese Schritte aus:

1. Greifen Sie als **Root**-Benutzer mit diesem Befehl auf Ihre VM zu:
 
   ```
   sudo su
   ```

1. Installieren Sie das **gptfdisk**-Paket, das erforderlich ist, um die Größe des Betriebssystemdatenträgers heraufzusetzen:

   ```
   yum install gdisk -y
   ```

1.  Um alle Sektoren anzuzeigen, die auf dem Datenträger verfügbar sind, führen Sie diesen Befehl aus:
    ```
    gdisk -l /dev/sda
    ```

1. Es werden die Informationen zum Partitionstyp angezeigt. Stellen Sie sicher, dass „GPT“ zurückgegeben wird. Identifizieren Sie die Stammpartition. Ändern oder löschen Sie nicht die Startpartition (BIOS-Startpartition) und die Systempartition (EFI-Systempartition).

1. Verwenden Sie diesen Befehl, um die Partitionierung zum ersten Mal zu starten: 
    ```
    gdisk /dev/sda
    ```

1. Sie sehen eine Meldung, in der Sie aufgefordert werden, den nächsten Befehl einzugeben: `Command: ? for help`. Wählen Sie die Taste **w** aus:

   ```
   w
   ```

1. Diese Meldung wird angezeigt: `Warning! Secondary header is placed too early on the disk! Do you want to
correct this problem? (Y/N)`. Wählen Sie die Taste **y** aus: 

   ```
   Y
   ```

1. Es sollte eine Meldung angezeigt werden, die Sie darüber informiert, dass die abschließenden Prüfungen abgeschlossen sind, und Sie zur Bestätigung auffordert. Wählen Sie die Taste **y** aus:

   ```
   Y
   ```

1. Verwenden Sie den Befehl `partprobe`, um zu überprüfen, ob alles ordnungsgemäß passiert:

   ```
   partprobe
   ```

1. Sie haben die vorherigen Schritte ausgeführt, um sicherzustellen, dass der sekundäre GPT-Header am Ende platziert wird. Starten Sie als Nächstes den Prozess der Größenänderung, indem Sie das Tool `gdisk` erneut verwenden. Verwenden Sie den folgenden Befehl:

   ```
   gdisk /dev/sda
   ```
1. Wählen Sie im Befehlsmenü die Taste **p** aus, um eine Liste der Partitionen anzuzeigen. Identifizieren Sie die Stammpartition. (In diesen Schritten wird **sda2** als Stammpartition betrachtet.) Identifizieren Sie die Startpartition. (In diesen Schritten wird **sda3** als Startpartition betrachtet.) 

   ```
   p
   ```
    ![Screenshot mit Stammpartition und Startpartition.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Wählen Sie die Taste **d** aus, um die Partition zu löschen. Wählen Sie dann die Partitionsnummer aus, die der Startpartition zugewiesen ist. (In diesem Beispiel ist es **3**.)
   ```
   d
   3
   ```
1. Wählen Sie die Taste **d** aus, um die Partition zu löschen. Wählen Sie die Partitionsnummer aus, die der Startpartition zugewiesen ist. (In diesem Beispiel ist es **2**.)
   ```
   d
   2
   ```
    ![Screenshot, der die Schritte zum Löschen der Stamm- und Startpartition zeigt.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Wählen Sie zum erneuten Erstellen der Stammpartition mit heraufgesetzter Größe die Taste **n** aus, und geben Sie dann die Partitionsnummer ein, die Sie zuvor für den Stamm gelöscht haben (in diesem Beispiel **2**). Wählen Sie `Default Value` für den ersten Sektor aus. Wählen Sie `Last sector value -  boot size sector` für den letzten Sektor aus (in diesem Fall `4096` entsprechend dem 2-MB-Start). Wählen Sie `8300` für den Hexadezimalcode aus.
   ```
   n
   2
   (Enter default)
   (Calculated value of Last sector value - 4096)
   8300
   ```
1. Wählen Sie zum erneuten Erstellen der Stammpartition die Taste **n** aus, und geben Sie dann die Partitionsnummer ein, die Sie zuvor für den Stamm gelöscht haben (in diesem Beispiel **3**). Wählen Sie `Default Value` für den ersten und den letzten Sektor aus. Wählen Sie `EF02` für den Hexadezimalcode aus.
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Schreiben Sie die Änderungen mit dem Befehl `w`, und wählen Sie dann `Y` aus, um die Änderungen zu bestätigen:
   ```
   w
   Y
   ```
1. Führen Sie den Befehl `partprobe` aus, um die Datenträgerstabilität zu prüfen:
   ```
   partprobe
   ```
1. Starten Sie die VM neu. Die Größe der Stammpartition muss heraufgesetzt werden.
   ```
   reboot
   ```

   ![Screenshot, der die Schritte zum erneuten Erstellen der Startpartition zeigt.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Führen Sie den Befehl `xfs_growfs` für die Partition aus, um ihre Größe zu ändern:
   ```
   xfs_growfs /dev/sda2
   ```

   ![Screenshot, der das Ergebnis der Ausführung von „xfs_growfs“ zeigt.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Nächste Schritte

- [Ändern der Datenträgergröße](expand-disks.md)
