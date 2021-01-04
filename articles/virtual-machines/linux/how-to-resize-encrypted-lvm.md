---
title: Ändern der Größe von mit Azure Disk Encryption verschlüsselten Datenträgern
description: Dieser Artikel enthält Anweisungen zum Ändern der Größe von Datenträgern, die mit ADE verschlüsselt sind, mit LVM.
author: jofrance
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 732a2ef3c6c33d0b17deaf3b9bdc5d1ac5e5934c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498913"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Vorgehensweise zum Ändern der Größe von LVM-Geräten, die Azure Disk Encryption verwenden

In diesem Artikel erfahren Sie, wie Sie die Größe von Datenträgern ändern, die Azure Disk Encryption verwenden. Zum Ändern der Größe der Datenträger verwenden Sie LVM (Logical Volume Management) unter Linux. Die Schritte gelten für mehrere Szenarien.

Sie können diesen Prozess zum Anpassen der Größe in den folgenden Umgebungen verwenden:

- Linux-Distributionen:
    - Red Hat Enterprise Linux (RHEL) 7 oder höher
    - Ubuntu 16 oder höher
    - SUSE 12 oder höher
- Azure Disk Encryption-Versionen: 
    - Single-pass-Erweiterung
    - Dual-pass-Erweiterung

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

- Eine vorhandene LVM-Konfiguration. Weitere Informationen finden Sie in den Anweisungen zum [Konfigurieren von LVM auf einer Linux-VM](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- Datenträger, die bereits mit Azure Disk Encryption verschlüsselt wurden. Weitere Informationen finden Sie in den Anweisungen zum [Konfigurieren von LVM und RAID auf verschlüsselten Geräten](how-to-configure-lvm-raid-on-crypt.md).

- Erfahrung bei der Verwendung von Linux und LVM.

- Erfahrung bei der Verwendung von */dev/disk/scsi1/* -Pfade für Datenträger in Azure. Weitere Informationen finden Sie im Dokument zur [Behandlung von Problemen mit Linux VM-Gerätenamen](../troubleshooting/troubleshoot-device-names-problems.md). 

## <a name="scenarios"></a>Szenarien

Die Verfahren in diesem Artikel gelten für die folgenden Szenarien:

- Herkömmliche LVM- und LVM-on-crypt-Konfigurationen
- Herkömmliche LVM-Verschlüsselung 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Herkömmliche LVM- und LVM-on-crypt-Konfigurationen

Herkömmliche LVM- und LVM-on-crypt-Konfigurationen erweitern ein logisches Volume (LV), wenn in der Volumegruppe (VG) Speicherplatz verfügbar ist.

### <a name="traditional-lvm-encryption"></a>Herkömmliche LVM-Verschlüsselung 

Bei der herkömmlichen LVM-Verschlüsselung werden LVs verschlüsselt. Der gesamte Datenträger ist nicht verschlüsselt.

Die Verwendung der herkömmlichen LVM-Verschlüsselung bietet folgende Möglichkeiten:

- Erweitern des LV, wenn Sie ein neues physisches Volume (PV) hinzufügen.
- Erweitern des LV, wenn Sie die Größe eines vorhandenen PV ändern.

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

Als Methode für die Datenträgerverschlüsselung wird LVM-on-crypt empfohlen. Bei dieser Methode wird der gesamte Datenträger, nicht nur das LV, verschlüsselt.

Die Verwendung von LVM-on-crypt bietet folgende Möglichkeiten: 

- Erweitern des LV, wenn Sie ein neues PV hinzufügen.
- Erweitern des LV, wenn Sie die Größe eines vorhandenen PV ändern.

> [!NOTE]
> Es wird nicht empfohlen, die herkömmliche LVM-Verschlüsselung und LVM-on-crypt auf derselben VM zu kombinieren.

Die folgenden Abschnitte enthalten Beispiele für die Verwendung von LVM und LVM-on-crypt. In den Beispielen werden bereits vorhandene Werte für Datenträger, PVs, VGs, LVs, Dateisysteme, UUIDs (Universally Unique Identifier) und Bereitstellungspunkte verwendet. Ersetzen Sie diese Werte durch Ihre eigenen Werte für die jeweilige Umgebung.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Erweitern eines LV, wenn in der VG Speicherplatz verfügbar ist

Die herkömmliche Methode zum Ändern der Größe von LVs ist die Erweiterung eines LV, wenn in der VG Speicherplatz verfügbar ist. Sie können diese Methode für nicht verschlüsselte Datenträger, herkömmliche LVM-verschlüsselte Volumes und LVM-on-crypt-Konfigurationen verwenden.

1. Überprüfen Sie die aktuelle Größe des Dateisystems, das Sie vergrößern möchten:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe des Dateisystems. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Vergewissern Sie sich, dass die VG über genügend Speicherplatz verfügt, um das LV zu vergrößern:

    ``` bash
    vgs
    ```

    ![Screenshot mit dem Code zum Überprüfen des Speicherplatzes in der VG. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Sie können auch `vgdisplay` verwenden:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mit dem vgdisplay-Code zum Überprüfen des Speicherplatzes in der VG. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Ermitteln Sie das LV, dessen Größe geändert werden soll:

    ``` bash
    lsblk
    ```

    ![Screenshot mit dem Ergebnis des lsblk-Befehls. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Bei LVM-on-crypt besteht der Unterschied darin, dass diese Ausgabe zeigt, dass die Verschlüsselung auf der Datenträgerebene erfolgt.

    ![Screenshot mit dem Ergebnis des lsblk-Befehls. Die Ausgabe ist hervorgehoben. Die verschlüsselte Ebene wird angezeigt.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Überprüfen Sie die LV-Größe:

    ``` bash
    lvdisplay lvname
    ```

    ![Screenshot mit dem Code zum Überprüfen der LV-Größe. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Erhöhen Sie die LV-Größe mit `-r`, um die Größe des Dateisystems online zu ändern:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot mit dem Code zum Erhöhen der LV-Größe. Der Befehl und die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Überprüfen Sie die neuen Größen für das LV und das Dateisystem:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe von LV und Dateisystem. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Die Ausgabe der Größe zeigt, dass die Größe von LV und Dateisystem erfolgreich geändert wurde.

Sie können die LV-Informationen erneut überprüfen, um die Änderungen auf der LV-Ebene zu bestätigen:

``` bash
lvdisplay lvname
```

![Screenshot mit dem Code zum Bestätigen der neuen Größen. Die Größen sind hervorgehoben.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Erweitern eines herkömmlichen LVM-Volumes durch Hinzufügen eines neuen PV

Wenn Sie einen neuen Datenträger hinzufügen müssen, um die VG-Größe zu erhöhen, erweitern Sie das herkömmliche LVM-Volume durch Hinzufügen eines neuen PV.

1. Überprüfen Sie die aktuelle Größe des Dateisystems, das Sie vergrößern möchten:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der aktuellen Größe eines Dateisystems. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Überprüfen Sie die aktuelle PV-Konfiguration:

    ``` bash
    pvs
    ```

    ![Screenshot mit dem Code zum Überprüfen der aktuellen PV-Konfiguration. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Überprüfen Sie die aktuellen VG-Informationen:

    ``` bash
    vgs
    ```

    ![Screenshot mit dem Code zum Überprüfen der aktuellen VG-Informationen. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Überprüfen Sie die aktuelle Liste der Datenträger. Ermitteln Sie die Datenträger, indem Sie die Geräte in */dev/disk/azure/scsi1/* überprüfen.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot mit dem Code zum Überprüfen der aktuellen Datenträgerliste. Der Befehl und die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Überprüfen Sie die Ausgabe von `lsblk`: 

    ``` bash
    lsbk
    ```

    ![Screenshot mit dem Code zum Überprüfen der Ausgabe von lsblk. Der Befehl und die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Fügen Sie den neuen Datenträger an den virtuellen Computer an, indem Sie die Anweisungen zum [Anfügen eines Datenträgers an eine Linux-VM](attach-disk-portal.md) befolgen.

7. Überprüfen Sie die Liste der Datenträger, und beachten Sie den neuen Datenträger.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot mit dem Code zum Überprüfen der Datenträgerliste. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Screenshot mit dem Code zum Überprüfen der Datenträgerliste mit lsblk. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Erstellen Sie ein neues PV auf dem neuen Datenträger:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Screenshot mit dem Code zum Erstellen eines neuen PV. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Diese Methode verwendet den gesamten Datenträger ohne Partition als PV. Alternativ können Sie `fdisk` verwenden, um eine Partition zu erstellen, und diese Partition dann für `pvcreate` verwenden.

9. Vergewissern Sie sich, dass das PV zur PV-Liste hinzugefügt wurde:

    ``` bash
    pvs
    ```

    ![Screenshot mit dem Code zum Anzeigen der PV-Liste. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Erweitern Sie die VG, indem Sie das neue PV hinzufügen:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Screenshot mit dem Code zum Erweitern der VG. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Überprüfen Sie die neue Größe der VG:

    ``` bash
    vgs
    ```

    ![Screenshot mit dem Code zum Überprüfen der VG-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Verwenden Sie `lsblk`, um das LV zu ermitteln, dessen Größe geändert werden soll:

    ``` bash
    lsblk
    ```

    ![Screenshot mit dem Code zum Ermitteln des lokalen Volumes, dessen Größe geändert werden soll. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Erweitern Sie die LV-Größe mit `-r`, um die Größe des Dateisystems online zu erhöhen:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot mit dem Code, um die Größe des Dateisystems online zu erhöhen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Überprüfen Sie die neuen Größen von LV und Dateisystem:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größen von LV und Dateisystem. Der Befehl und das Ergebnis sind hervorgehoben.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Wenn Azure Data Encryption für herkömmliche LVM-Konfigurationen verwendet wird, erfolgt die Verschlüsselung auf der LV-Ebene und nicht auf der Datenträgerebene.
    >
    >An diesem Punkt wird die verschlüsselte Ebene auf den neuen Datenträger erweitert. Der eigentliche Datenträger verfügt über keine Verschlüsselungseinstellungen auf Plattformebene, sodass der Verschlüsselungsstatus nicht aktualisiert wird.
    >
    >Dies ist einer der Gründe, warum LVM-on-crypt als Vorgehensweise empfohlen wird. 

15. Überprüfen Sie die Verschlüsselungsinformationen im Portal:

    ![Screenshot mit Anzeige der Verschlüsselungsinformationen im Portal. Der Datenträgername und die Verschlüsselung sind hervorgehoben.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Um die Verschlüsselungseinstellungen für den Datenträger zu aktualisieren, fügen Sie ein neues LV hinzu, und aktivieren Sie die Erweiterung im virtuellen Computer.
    
16. Fügen Sie ein neues LV, erstellen Sie ein Dateisystem darauf, und fügen Sie es zu `/etc/fstab` hinzu.

17. Richten Sie erneut die Verschlüsselungserweiterung ein. Dieses Mal legen Sie die Verschlüsselungseinstellungen für den neuen Datenträger auf Plattformebene fest. Hier sehen Sie ein CLI-Beispiel:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Überprüfen Sie die Verschlüsselungsinformationen im Portal:

    ![Screenshot mit Anzeige der Verschlüsselungsinformationen im Portal. Der Datenträgername und die Verschlüsselungsinformationen sind hervorgehoben.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Nachdem die Verschlüsselungseinstellungen aktualisiert wurden, können Sie das neue LV löschen. Löschen Sie außerdem den Eintrag aus dem erstellten `/etc/fstab` und `/etc/crypttab`.

![Screenshot mit dem Code zum Löschen des neuen LV. Die gelöschte fstab und crypttab sind hervorgehoben.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Führen Sie die folgenden Schritte aus, um die Bereinigung abzuschließen:

1. Heben Sie die Einbindung des LV auf:

    ``` bash
    umount /mountpoint
    ```

1. Schließen Sie die verschlüsselte Ebene des Volumes:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Löschen Sie das LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Erweitern eines herkömmlichen LVM-Volumes durch Ändern der Größe eines vorhandenen PV

In einigen Szenarien müssen Sie u. U. die Größe eines vorhandenen Datenträgers ändern. Gehen Sie dabei folgendermaßen vor:

1. Ermitteln Sie die verschlüsselten Datenträger:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot mit dem Code zum Ermitteln der verschlüsselten Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Screenshot mit alternativem Code zum Ermitteln der verschlüsselten Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Überprüfen Sie die PV-Informationen:

    ``` bash
    pvs
    ```

    ![Screenshot mit dem Code zum Überprüfen der PV-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Die Ergebnisse in der Abbildung zeigen, dass der gesamte Speicherplatz von allen PVs zurzeit verwendet wird.

3. Überprüfen Sie die VG-Informationen:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Screenshot mit dem Code zum Überprüfen der VG-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Überprüfen Sie die Datenträgergrößen. Sie können `fdisk` oder `lsblk` verwenden, um die Laufwerksgrößen aufzulisten.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Screenshot mit dem Code zum Überprüfen der Datenträgergrößen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Hier haben wir mit `lsblk -fs` ermittelt, welche PVs welchen LVs zugeordnet sind. Sie können die Zuordnungen ermitteln, indem Sie `lvdisplay` ausführen.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Screenshot mit einer alternativen Möglichkeit zum Ermitteln der Zuordnung von physischen Volumes zu logischen Volumes. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    In diesem Fall sind alle vier Datenlaufwerke Teil derselben VG und eines einzigen LV. Ihre Konfiguration kann davon abweichen.

5. Überprüfen Sie die aktuelle Dateisystemnutzung:

    ``` bash
    df -h /datalvm*
    ```

    ![Screenshot mit dem Code zum Überprüfen der Dateisystemnutzung. Der Befehl und die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Ändern Sie die Größe der Datenträger, indem Sie die Anweisungen zum [Erweitern eines verwalteten Azure-Datenträgers](expand-disks.md#expand-an-azure-managed-disk) befolgen. Sie können das Portal, die CLI oder PowerShell verwenden.

    >[!IMPORTANT]
    >Die Größe virtueller Datenträger kann nicht geändert werden, wenn die VM ausgeführt wird. Geben Sie die VM für diesen Schritt frei.

7. Starten Sie die VM, und überprüfen Sie die neuen Größen mit `fdisk`.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Screenshot mit dem Code zum Überprüfen der Datenträgergröße. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    In diesem Fall wurde die Größe `/dev/sdd` von 5 GB in 20 GB geändert.

8. Überprüfen Sie die aktuelle PV-Größe:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Screenshot mit dem Code zum Überprüfen der PV-Größe. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Obwohl die Größe des Datenträgers geändert wurde, weist das PV noch die vorherige Größe auf.

9. Ändern Sie die PV-Größe:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Screenshot mit dem Code zum Ändern der PV-Größe. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Überprüfen Sie die PV-Größe:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Screenshot mit dem Code zum Überprüfen der PV-Größe. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Gehen Sie für alle Datenträger, deren Größe Sie ändern möchten, genauso vor.

11. Überprüfen Sie die VG-Informationen.

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mit dem Code zum Überprüfen der VG-Informationen. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    Die VG verfügt nun über genügend Speicherplatz, damit sie den LVs zugewiesen werden kann.

12. Ändern Sie die LV-Größe:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Screenshot mit dem Code zum Ändern der LV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Überprüfen Sie die Größe des Dateisystems:

    ``` bash
    df -h /datalvm2
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe des Dateisystems. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Erweitern eines LVM-on-crypt-Volumes durch Hinzufügen eines neuen PV

Sie können auch ein LVM-on-crypt-Volume erweitern, indem Sie ein neues PV hinzufügen. Diese Methode stimmt weitgehend mit den Schritten zum [Konfigurieren von LVM und RAID auf verschlüsselten Geräten](how-to-configure-lvm-raid-on-crypt.md#general-steps) überein. Weitere Informationen finden Sie in den Abschnitten, in denen das Hinzufügen eines neuen Datenträgers und dessen Einrichtung in einer LVM-on-crypt-Konfiguration beschrieben werden.

Sie können diese Methode verwenden, um Speicherplatz zu einem vorhandenen LV hinzuzufügen. Sie können auch neue VGs oder LVs erstellen.

1. Überprüfen Sie die aktuelle Größe der VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mit dem Code zum Überprüfen der VG-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Überprüfen Sie die aktuelle Größe des Dateisystems und des LV, das Sie erweitern möchten:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Screenshot mit dem Code zum Überprüfen der LV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe des Dateisystems. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Fügen Sie einen neuen Datenträger zur VM hinzu, und identifizieren Sie ihn.

    Überprüfen Sie die Datenträger, bevor Sie den neuen Datenträger hinzufügen:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe der Datenträger. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Es gibt eine weitere Möglichkeit, um die Datenträger zu überprüfen, bevor Sie den neuen Datenträger hinzufügen:

    ``` bash
    lsblk
    ```

    ![Screenshot mit einem alternativen Code zum Überprüfen der Größe der Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Zum Hinzufügen des neuen Datenträgers können Sie PowerShell, die Azure CLI oder das Azure-Portal verwenden. Weitere Informationen finden Sie in den Anweisungen zum [Anfügen eines Datenträgers an eine Linux-VM](attach-disk-portal.md).

    Auf das neu hinzugefügte Gerät wird das Kernelnamensschema angewendet. Einem neuen Laufwerk wird normalerweise der nächste verfügbare Buchstabe zugewiesen. In diesem Fall wird der Datenträger `sdd` hinzugefügt.

4. Überprüfen Sie die Datenträger, um sich zu vergewissern, dass der neue Datenträger hinzugefügt wurde:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Screenshot mit dem Code zum Auflisten der Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Screenshot mit dem neu hinzugefügten Datenträger in der Ausgabe.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Erstellen Sie auf dem soeben hinzugefügten Datenträger ein Dateisystem. Ordnen Sie den Datenträger den verknüpften Geräten von `/dev/disk/azure/scsi1/` zu.

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Screenshot mit dem Code zum Erstellen eines Dateisystems. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Screenshot mit weiterem Code zum Erstellen eines Dateisystem und Zuordnen des Datenträgers zu den verknüpften Geräten. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Erstellen Sie einen temporären Bereitstellungspunkt für den neuen hinzugefügten Datenträger:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Fügen Sie das soeben erstellte Dateisystem zu `/etc/fstab` hinzu.

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Binden Sie das neu erstellte Dateisystem ein:

    ``` bash
    mount -a
    ```

9. Überprüfen Sie die Einbindung des neuen Dateisystems:

    ``` bash
    df -h
    ```

    ![Screenshot mit dem Code zum Überprüfen der Dateisystemeinbindung. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Screenshot mit weiterem Code zum Überprüfen der Dateisystemeinbindung. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Starten Sie die Verschlüsselung, die Sie zuvor für Datenlaufwerke gestartet haben, erneut.

    >[!TIP]
    >Bei LVM-on-crypt wird die Verwendung von `EncryptFormatAll` empfohlen. Andernfalls erfolgt u. U. eine doppelte Verschlüsselung, wenn Sie weitere Datenträger festlegen.
    >
    >Weitere Informationen finden Sie in den Anweisungen zum [Konfigurieren von LVM und RAID auf verschlüsselten Geräten](how-to-configure-lvm-raid-on-crypt.md).

    Beispiel:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Wenn die Verschlüsselung durchgeführt wurde, wird auf dem neu hinzugefügten Datenträger eine crypt-Ebene angezeigt:

    ``` bash
    lsblk
    ```

    ![Screenshot mit dem Code zum Überprüfen der crypt-Ebene. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Heben Sie die Einbindung der verschlüsselten Ebene des neuen Datenträgers auf:

    ``` bash
    umount ${newmount}
    ```

12. Überprüfen Sie die aktuellen PV-Informationen:

    ``` bash
    pvs
    ```

    ![Screenshot mit dem Code zum Überprüfen der PV-Informationen. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Erstellen Sie auf der verschlüsselten Ebene des Datenträgers ein PV. Verwenden Sie den Gerätenamen aus dem vorherigen `lsblk`-Befehl. Fügen Sie einen `/dev/`-Mapper vor dem Gerätenamen hinzu, um das PV zu erstellen:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Screenshot mit dem Code zum Erstellen eines physischen Volumes auf der verschlüsselten Ebene. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Daraufhin wird eine Warnung mit der Frage angezeigt, ob die aktuelle `ext4 fs`-Signatur zurückgesetzt werden soll. Diese Warnung ist erwartungsgemäß. Beantworten Sie diese Frage mit `y`.

14. Vergewissern Sie sich, dass das neue PV zur LVM-Konfiguration hinzugefügt wurde:

    ``` bash
    pvs
    ```

    ![Screenshot mit dem Code zum Überprüfen, ob das physische Volume zur LVM-Konfiguration hinzugefügt wurde. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Fügen Sie das neue PV zur VG hinzu, die vergrößert werden soll.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Screenshot mit dem Code zum Hinzufügen eines physischen Volumes zu einer Volumegruppe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Überprüfen Sie die neue Größe und den freien Speicherplatz der VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe und des freien Speicherplatzes der Volumegruppe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Beachten Sie die Zunahme der Werte für `Total PE` und `Free PE / Size`.

17. Erhöhen Sie die Größe von LV und Dateisystem. Verwenden Sie `-r`-Option von `lvextend`. In diesem Beispiel wird der gesamte verfügbare Speicherplatz in der VG zum angegebenen LV hinzugefügt.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Screenshot mit dem Code zum Erhöhen der Größe von LV und Dateisystem. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Führen Sie die folgenden Schritte aus, um Ihre Änderungen zu überprüfen.

1. Überprüfen Sie die Größe des LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Screenshot mit dem Code zum Überprüfen der neuen LV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Überprüfen Sie die neue Größe des Dateisystems:

    ``` bash
    df -h mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der neuen Dateisystemgröße. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Überprüfen Sie, ob sich die LVM-Ebene über der verschlüsselten Ebene befindet:

    ``` bash
    lsblk
    ```

    ![Screenshot mit dem Code zum Überprüfen, ob sich die LVM-Ebene über der verschlüsselten Ebene befindet. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Wenn Sie `lsblk` ohne Optionen verwenden, werden die Bereitstellungspunkte mehrmals angezeigt. Der Befehl sortiert nach Gerät und LVs. 

    Sie sollten ggf. `lsblk -fs` verwenden. In diesem Befehl kehrt `-fs` die Sortierreihenfolge um, sodass die Einstellungspunkte lediglich einmal angezeigt werden. Die Datenträger werden mehrmals angezeigt.

    ``` bash
    lsblk -fs
    ```

    ![Screenshot mit alternativem Code zum Überprüfen, ob sich die LVM-Ebene über der verschlüsselten Ebene befindet. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Erweitern einer LVM auf einem verschlüsselten Volume durch Ändern der Größe eines vorhandenen PV

1. Ermitteln Sie die verschlüsselten Datenträger:

    ``` bash
    lsblk
    ```

    ![Screenshot mit dem Code zum Ermitteln der verschlüsselten Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Screenshot mit alternativem Code zum Ermitteln der verschlüsselten Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Überprüfen Sie die PV-Informationen:

    ``` bash
    pvs
    ```

    ![Screenshot mit dem Code zum Überprüfen der PV-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Überprüfen Sie die VG-Informationen:

    ``` bash
    vgs
    ```

    ![Screenshot mit dem Code zum Überprüfen der VG-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Überprüfen Sie die LV-Informationen:

    ``` bash
    lvs
    ```

    ![Screenshot mit dem Code zum Überprüfen der LV-Informationen. Das Ergebnis ist hervorgehoben.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Überprüfen Sie die Dateisystemnutzung:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Screenshot mit dem Code zum Überprüfen der Dateisystemnutzung. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Überprüfen Sie die Größe der Datenträger:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Screenshot mit dem Code zum Überprüfen der Größe der Datenträger. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Ändern Sie die Datenträgergröße. Sie können das Portal, die CLI oder PowerShell verwenden. Weitere Informationen finden Sie im Abschnitt zum Ändern der Datenträgergröße in den Anweisungen zum [Erweitern virtueller Festplatten auf einer Linux-VM](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Die Größe virtueller Datenträger kann nicht geändert werden, wenn die VM ausgeführt wird. Geben Sie die VM für diesen Schritt frei.

8. Überprüfen Sie die Datenträgergrößen:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Screenshot mit dem Code zum Überprüfen der Datenträgergrößen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    In diesem Fall wurde die Größe beider Datenträger von 2 GB in 4 GB geändert. Die Größe von Dateisystem, LV und PV wurde jedoch nicht geändert.

9. Überprüfen Sie die aktuelle PV-Größe. Beachten Sie, dass das PV bei LVM-on-crypt das `/dev/mapper/`-Gerät und nicht das `/dev/sd*`-Gerät ist.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Screenshot mit dem Code zum Überprüfen der aktuellen PV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Ändern Sie die PV-Größe:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Screenshot mit dem Code zum Ändern der PV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Überprüfen Sie die neue PV-Größe:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Screenshot mit dem Code zum Überprüfen der PV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Ändern Sie die Größe der verschlüsselten Ebene des PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Gehen Sie für alle Datenträger, deren Größe Sie ändern möchten, genauso vor.

13. Überprüfen Sie die VG-Informationen:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mit dem Code zum Überprüfen der VG-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    Die VG verfügt nun über genügend Speicherplatz, damit sie den LVs zugewiesen werden kann.

14. Überprüfen Sie die LV-Informationen:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Screenshot mit dem Code zum Überprüfen der LV-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Überprüfen Sie die Dateisystemnutzung:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der Dateisystemnutzung. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Ändern Sie die LV-Größe:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot mit dem Code zum Ändern der LV-Größe. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Hier verwenden wir die Option `-r`, um auch die Größe des Dateisystems zu ändern.

17. Überprüfen Sie die LV-Informationen:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Screenshot mit dem Code zum Abrufen von LV-Informationen. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Überprüfen Sie die Dateisystemnutzung:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mit dem Code zum Überprüfen der Dateisystemnutzung. Die Ergebnisse sind hervorgehoben.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Gehen Sie ggf. wie beschrieben vor, um die Größe anderer LVs zu ändern.

## <a name="next-steps"></a>Nächste Schritte

[Behandeln von Problemen mit Azure Disk Encryption](disk-encryption-troubleshooting.md)