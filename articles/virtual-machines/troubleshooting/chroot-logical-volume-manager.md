---
title: Wiederherstellen von virtuellen Linux-Computern mithilfe von chroot, wenn LVM (Logical Volume Manager) verwendet wird – virtuelle Azure-Computer
description: Wiederherstellung von virtuellen Linux-Computern mit LVMs.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 390443874ea63a8661ef8baea627015fcf679719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002696"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Problembehandlung bei einem virtuellen Linux-Computer ohne Zugriff auf die serielle Azure-Konsole und bei Verwendung von LVM (Logical Volume Manager) im Datenträgerlayout

Dieser Leitfaden zur Problembehandlung ist von Vorteil für Szenarien, in denen ein virtueller Linux-Computer nicht gestartet wird, SSH nicht möglich ist und das Layout des zugrunde liegenden Dateisystems mit LVM (Logical Volume Manager) konfiguriert ist.

## <a name="take-snapshot-of-the-failing-vm"></a>Erstellen einer Momentaufnahme des fehlerhaften virtuellen Computers

Erstellen Sie eine Momentaufnahme des betroffenen virtuellen Computers. 

Die Momentaufnahme wird dann an einen virtuellen **Rettungscomputer** angefügt. Befolgen Sie die Anweisungen [hier](../linux/snapshot-copy-managed-disk.md#use-azure-portal) zum Erstellen einer **Momentaufnahme**.

## <a name="create-a-rescue-vm"></a>Erstellen eines virtuellen Rettungscomputers
Normalerweise empfiehlt sich die Verwendung eines virtuellen Rettungscomputers mit derselben oder einer vergleichbaren Betriebssystemversion. Verwenden Sie dieselbe **Region** und **Ressourcengruppe** wie die des betroffenen virtuellen Computers.

## <a name="connect-to-the-rescue-vm"></a>Herstellen einer Verbindung mit dem virtuellen Rettungscomputer
Stellen Sie die Verbindung mit dem virtuellen **Rettungscomputer** über SSH her. Mit dem folgenden Befehl erhöhen Sie die Rechte und legen sich als Administrator fest:

`sudo su -`

## <a name="attach-the-disk"></a>Anfügen des Datenträgers
Fügen Sie einen Datenträger, der aus der zuvor erstellten Momentaufnahme erstellt wurde, an den virtuellen **Rettungscomputer** an.

Wählen Sie im Azure-Portal den virtuellen **Rettungscomputer** und dann **Datenträger** aus. 

![Erstellen eines Datenträgers](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Füllen Sie die Felder aus. Weisen Sie dem neuen Datenträger einen Namen zu, und wählen Sie dieselbe Ressourcengruppe wie die der Momentaufnahme, den betroffenen virtuellen Computer und den virtuellen Rettungscomputer aus.

Als **Quelltyp** wird **Momentaufnahme** verwendet.
**Quellmomentaufnahme** ist der Name der zuvor erstellten **Momentaufnahme**.

![Erstellen eines Datenträgers 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Erstellen Sie einen Bereitstellungspunkt für den angefügten Datenträger.

`mkdir /rescue`

Führen Sie den Befehl **fdisk -l** aus, um zu überprüfen, ob der Momentaufnahmendatenträger angefügt wurde und alle verfügbaren Geräte und Partitionen aufgeführt sind.

`fdisk -l`

In den meisten Szenarien wird der angefügte Momentaufnahmendatenträger als **/dev/sdc** mit den beiden Partitionen **/dev/sdc1** und **/dev/sdc2** angezeigt.

![fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Das * *\** _ gibt eine Startpartition an. Beide Partitionen sind einzubinden.

Führen Sie den Befehl _ *lsblk** aus, um die LVMs des betroffenen virtuellen Computers anzuzeigen.

`lsblk`

![Screenshot: Ausgabe des Befehls „lsblk“](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Überprüfen Sie, ob LVMs des betroffenen virtuellen Computers angezeigt werden.
Wenn dies nicht der Fall ist, verwenden Sie die folgenden Befehle, um die LVMs zu aktivieren, und führen Sie **lsblk** erneut aus.
Stellen Sie sicher, dass die LVMs des angefügten Datenträgers angezeigt werden, bevor Sie den Vorgang fortsetzen.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Suchen Sie den Pfad zum Einbinden des logischen Volumes, das die Partition „/“ (root) enthält. Diese enthält die Konfigurationsdateien, z. B. „/etc/default/grub“.

In diesem Beispiel ist die Ausgabe **rootvg-rootlv** des vorherigen Befehls **lsblk** das richtige einzubindende logische **root**-Volume und kann im nächsten Befehl verwendet werden.

Mit der Ausgabe des nächsten Befehls wird der einzubindende Pfad für das logische **root**-Volume angezeigt.

`pvdisplay -m | grep -i rootlv`

![rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Binden Sie dieses Gerät im Verzeichnis „/rescue“ ein.

`mount /dev/rootvg/rootlv /rescue`

Binden Sie die Partition ein, bei der das **Boot-Flag** auf „/rescue/boot“ festgelegt ist.

`
mount /dev/sdc1 /rescue/boot
`

Überprüfen Sie mit dem Befehl **lsblk**, ob die Dateisysteme des angefügten Datenträgers ordnungsgemäß eingebunden sind.

![Ausführen von lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

Alternativ können Sie den Befehl **df -Th** ausführen.

![df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Festlegen des chroot-Zugriffs

Legen Sie den **chroot**-Zugriff fest, über den Sie verschiedene Korrekturen ausführen können. Für die einzelnen Linux-Distributionen bestehen geringe Abweichungen.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Wenn beispielsweise folgende Fehlermeldung angezeigt wird:

**chroot: failed to run command ‘/bin/bash’: No such file or directory** (chroot: Fehler beim Ausführen des Befehls „/bin/bash“: Datei oder Verzeichnis nicht vorhanden),

sollten Sie versuchen, das logische **usr**-Volume einzubinden.

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Beachten Sie beim Ausführen von Befehlen in einer **chroot**-Umgebung, dass sie für den angefügten Betriebssystemdatenträger und nicht für den lokalen virtuellen **Rettungscomputer** ausgeführt werden. 

Befehle können zum Installieren, Entfernen und Aktualisieren von Software verwendet werden. Führen Sie eine Problembehandlung für die virtuellen Computer aus, um Fehler zu beheben.


Führen Sie den Befehl „lsblk“ aus. „/rescue“ ist nun „/“, und „/rescue/boot“ ist „/boot“. ![Screenshot: Konsolenfenster mit dem Befehl „lsblk“ und der Ausgabestruktur](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Ausführen von Korrekturen

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Beispiel 1: Konfigurieren des virtuellen Computers für den Start über einen anderen Kernel

In einem häufigen Szenario wird erzwungen, dass ein virtueller Computer über einen früheren Kernel gestartet wird, da der aktuelle installierte Kernel möglicherweise beschädigt wurde oder ein Upgrade nicht ordnungsgemäß ausgeführt wurde.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Exemplarische Vorgehensweise*

Mit dem Befehl **grep** werden die Kernel aufgelistet, die in **grub.cfg** bekannt sind.
![Screenshot: Konsolenfenster mit dem Ergebnis einer grep-Suche nach Kernels](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv list** gibt an, welcher Kernel beim nächsten Start geladen wird. ![Kernel-Standardwert](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** wird verwendet, um den Kernel zu ändern. ![grub2-set](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv list** gibt an, welcher Kernel beim nächsten Start geladen wird. ![Neuer Kernel](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** erstellt „grub.cfg“ mit den erforderlichen Versionen neu. ![grub2-mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Beispiel 2: Aktualisieren von Paketen

Ein fehlerhaftes Kernel-Upgrade kann dazu führen, dass der virtuelle Computer nicht gestartet werden kann.
Binden Sie alle logischen Volumes ein, sodass Pakete entfernt oder neu installiert werden können.

Führen Sie den Befehl **lvs** aus, um zu überprüfen, welche **logischen Volumes**  für die Einbindung verfügbar sind. Jeder virtuelle Computer, der migriert wurde oder von einem anderen Cloudanbieter stammt, weist eine unterschiedliche Konfiguration auf.

Beenden Sie die **chroot**-Umgebung, und binden Sie das erforderliche **logische Volume**  ein.

![Screenshot: Konsolenfenster mit dem Befehl „lvs“, Einbinden des logischen Volumes](./media/chroot-logical-volume-manager/advanced.png)

Greifen Sie dann erneut auf die **chroot**-Umgebung zu, indem Sie folgenden Befehl ausführen:

`chroot /rescue`

Alle logischen Volumes sollten als eingebundene Partitionen sichtbar sein.

![Screenshot: Logische Volumes als eingebundene Partitionen](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Fragen Sie den installierten **Kernel** ab.

![Screenshot: Abfragen des installierten Kernels](./media/chroot-logical-volume-manager/rpm-kernel.png)

Entfernen oder upgraden Sie bei Bedarf den **Kernel**.
![Erweitert](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Beispiel 3: Aktivieren der seriellen Konsole
Wenn der Zugriff auf die serielle Azure-Konsole nicht möglich ist, überprüfen Sie die GRUB-Konfigurationsparameter für den virtuellen Linux-Computer, und korrigieren Sie sie. Ausführliche Informationen finden Sie in [dieser Dokumentation](./serial-console-grub-proactive-configuration.md).

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Beispiel 4: Laden eines Kernels mit problematischem LVM-Auslagerungsvolume

Ein virtueller Computer wird möglicherweise nicht vollständig gestartet und zeigt die Aufforderung **dracut** an.
Weitere Details zu diesem Fehler finden Sie entweder in der seriellen Azure-Konsole oder im Azure-Portal unter „Startdiagnose“ > „Serielles Protokoll“.


Gegebenenfalls ist ein Fehler wie der folgende vorhanden:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Die Konfiguration in „grub.cfg“ sieht vor, dass ein logisches Volume namens **rd.lvm.lv=VG/SwapVol** geladen wird, dieses wird vom virtuellen Computer aber nicht gefunden. Die folgende Zeile zeigt, wie der Kernel mit einem Verweis auf das logische Volume „SwapVol“ geladen wird:

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Entfernen Sie das problematische logische Volume aus der Konfiguration unter „/etc/default/grub“, und erstellen Sie „grub2.cfg“ neu.


## <a name="exit-chroot-and-swap-the-os-disk"></a>Beenden von chroot und Austauschen des Betriebssystemdatenträgers

Nach dem Beheben des Problems können Sie die Einbindung des Datenträgers aufheben und den Datenträger vom virtuellen Rettungscomputer trennen, sodass er mit dem betroffenen Betriebssystemdatenträger des virtuellen Computers ausgetauscht werden kann.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Trennen Sie den Datenträger vom virtuellen Rettungscomputer, und tauschen Sie den Datenträger aus.

Wählen Sie im Portal **Datenträger** den virtuellen Computer und dann **Trennen**
![Trennen des Datenträgers](./media/chroot-logical-volume-manager/detach-disk.png) 

Speichern Sie die Änderungen. ![Speichern der Trennung](./media/chroot-logical-volume-manager/save-detach.png) 

Der Datenträger wird jetzt verfügbar, sodass er mit dem ursprünglichen Betriebssystemdatenträger des betroffenen virtuellen Computers ausgetauscht werden kann.

Navigieren Sie im Azure-Portal zu dem fehlerhaften virtuellen Computer, und wählen Sie aus: **Datenträger** -> **Betriebssystemdatenträger austauschen**
![Austauschen des Datenträgers](./media/chroot-logical-volume-manager/swap-disk.png) 

Füllen Sie die Felder aus. Unter **Datenträger auswählen** wird der soeben im vorherigen Schritt getrennte Momentaufnahmendatenträger verwendet. Geben Sie auch den VM-Namen des betroffenen virtuellen Computers ein, und wählen Sie dann **OK** aus.

![Betriebssystemdatenträger](./media/chroot-logical-volume-manager/new-osdisk.png) 

Wenn der virtuelle Computer ausgeführt wird, wird er beim Austausch des Datenträgers beendet. Starten Sie den virtuellen Computer nach dem Austausch des Datenträgers neu.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen

 [Serielle Azure-Konsole]( ./serial-console-linux.md)

[Verwenden der seriellen Konsole für den Zugriff auf den GRUB- und den Einzelbenutzermodus](./serial-console-grub-single-user-mode.md)