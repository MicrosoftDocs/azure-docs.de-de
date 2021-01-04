---
title: 'Konfigurieren von LVM und RAID auf verschlüsselten Geräten: Azure Disk Encryption'
description: Dieser Artikel enthält Anweisungen zum Konfigurieren von LVM und RAID auf verschlüsselten Geräten für virtuelle Linux-Computer.
author: jofrance
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 46d2c039806e4e6a72e091458d44e7b21b3dfa70
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843518"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurieren von LVM und RAID auf verschlüsselten Geräten

In diesem Artikel wird schrittweise erläutert, wie die logische Volumeverwaltung (Logical Volume Management, LVM) und RAID auf verschlüsselten Geräten durchführt werden. Der Vorgang gilt für die folgenden Umgebungen:

- Linux-Distributionen
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Azure Disk Encryption – Erweiterung in Einzeldurchlauf
- Azure Disk Encryption – Erweiterung in zwei Durchläufen


## <a name="scenarios"></a>Szenarien

Die Verfahren in diesem Artikel unterstützen die folgenden Szenarien:  

- Konfigurieren von LVM auf verschlüsselten Geräten (LVM-on-Crypt)
- Konfigurieren von RAID auf verschlüsselten Geräten (RAID-on-Crypt)

Nachdem die zugrunde liegenden Geräte verschlüsselt wurden, können Sie die LVM- oder RAID-Strukturen basierend auf dieser verschlüsselten Ebene erstellen. 

Die physischen Volumes werden auf der verschlüsselten Ebene erstellt. Mithilfe der physischen Volumes wird die Volumegruppe erstellt. Sie erstellen die Volumes und fügen die erforderlichen Einträge in „/etc/fstab“ hinzu. 

![Diagramm zu den Ebenen von LVM-Strukturen](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Auf ähnliche Weise wird das RAID-Gerät basierend auf der verschlüsselten Ebene auf den Datenträgern erstellt. Ein Dateisystem wird auf dem RAID-Gerät erstellt und in „/etc/fstab“ als reguläres Gerät hinzugefügt.

## <a name="considerations"></a>Überlegungen

Wir empfehlen die Verwendung von LVM-on-Crypt. RAID ist eine Option, wenn LVM aufgrund bestimmter Anwendungs- oder Umgebungsbeschränkungen nicht verwendet werden kann.

Sie werden die Option **EncryptFormatAll** verwenden. Weitere Informationen zu dieser Option finden Sie unter [Verwenden des Features EncryptFormatAll für Datenträger auf virtuellen Linux-Computern](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Obwohl Sie diese Methode auch verwenden können, wenn Sie das Betriebssystem verschlüsseln, werden hier lediglich die Datenträger mit den Daten verschlüsselt.

Die Verfahren gehen davon aus, dass Sie die Voraussetzungen bereits in [Azure Disk Encryption-Szenarien auf virtuellen Linux-Computern](./disk-encryption-linux.md) und in [Schnellstart: Erstellen und Verschlüsseln eines virtuellen Linux-Computers mit der Azure CLI](./disk-encryption-cli-quickstart.md) überprüft haben:

Die Version von Azure Disk Encryption mit zwei Durchläufen wird bald eingestellt und sollte bei neuen Verschlüsselungen nicht mehr verwendet werden.

## <a name="general-steps"></a>Allgemeine Schritte

Wenn Sie die „on-Crypt“-Konfigurationen verwenden, verwenden Sie den in den folgenden Verfahren beschriebenen Prozess.

>[!NOTE] 
>Im gesamten Artikel werden Variablen verwendet. Ersetzen Sie die Werte entsprechend.

### <a name="deploy-a-vm"></a>Bereitstellen einer VM 
Die folgenden Befehle sind optional, es wird jedoch empfohlen, sie auf einen neu bereitgestellten virtuellen Computer (VM) anzuwenden.

Mit PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Anfügen von Datenträgern an die VM
Wiederholen Sie die folgenden Befehle für die `$N`-Anzahl neuer Datenträger, die Sie an den virtuellen Computer anfügen möchten.

Mit PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Vergewissern Sie sich, dass die Datenträger an die VM angefügt wurden
Mit PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Liste der angefügten Datenträger in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Liste der angefügten Datenträger in der Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Liste der angefügten Datenträger im Portal](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Betriebssystem:

```bash
lsblk 
```
![Liste der angefügten Datenträger im Betriebssystem](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurieren der zu verschlüsselnden Datenträger
Diese Konfiguration erfolgt auf Betriebssystemebene. Die entsprechenden Datenträger werden über Azure Disk Encryption für eine herkömmliche Verschlüsselung konfiguriert:

- Dateisysteme werden auf den Datenträgern erstellt.
- Temporäre Bereitstellungspunkte zum Einbinden der Dateisysteme werden erstellt.
- Dateisysteme werden in „/etc/fstab“ so konfiguriert, dass sie beim Start eingebunden werden.

Überprüfen Sie den Gerätebuchstaben, der den neuen Datenträgern zugewiesen ist. In diesem Beispiel werden vier Datenträger verwendet.

```bash
lsblk 
```
![An das Betriebssystem angefügte Datenträger](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Erstellen eines Dateisystems auf den einzelnen Datenträgern
Dieser Befehl erstellt iterativ ein ext4-Dateisystem auf jedem Datenträger, der im Abschnitt „in“ der „for“-Schleife definiert ist.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Erstellen eines ext4-Dateisystems](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Finden Sie den universell eindeutigen Bezeichner (UUID) der Dateisysteme, die Sie kürzlich erstellt haben, erstellen Sie einen temporären Ordner, fügen Sie die entsprechenden Einträge in „/etc/fstab“ hinzu, und binden Sie alle Dateisysteme ein.

Dieser Befehl wird ebenfalls iterativ auf jedem Datenträger ausgeführt, der im Abschnitt „in“ der „for“-Schleife definiert ist:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Überprüfen der ordnungsgemäßen Einbindung der Datenträger
```bash
lsblk
```
![Liste der eingebundenen temporären Dateisysteme](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Überprüfen Sie auch, ob die Datenträger konfiguriert sind:

```bash
cat /etc/fstab
```
![Konfigurationsinformationen über „fstab“](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Verschlüsseln der Datenträger
PowerShell mit einem Schlüssel für die Schlüsselverschlüsselung (Key Encryption Key, KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI mit einem KEK:

```bash
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
### <a name="verify-the-encryption-status"></a>Überprüfen des Verschlüsselungsstatus

Fahren Sie mit dem nächsten Schritt erst dann fort, wenn alle Datenträger verschlüsselt sind.

Mit PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Verschlüsselungsstatus in PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Verschlüsselungsstatus in der Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Verschlüsselungsstatus im Portal](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Betriebssystemebene:

```bash
lsblk
```
![Verschlüsselungsstatus im Betriebssystem](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Die Erweiterung fügt die Dateisysteme „/var/lib/azure_disk_encryption_config/azure_crypt_mount“ (alte Verschlüsselung) oder „/etc/crypttab“ (neue Verschlüsselungen) hinzu.

>[!NOTE] 
>Ändern Sie keine dieser Dateien.

Diese Datei übernimmt die Aktivierung dieser Datenträger während des Startvorgangs, damit sie später von LVM oder RAID verwendet werden können. 

Machen Sie sich keine Gedanken über die Bereitstellungspunkte in dieser Datei. Azure Disk Encryption verliert die Fähigkeit, die Datenträger als normales Dateisystem einzubinden, nachdem wir einen physischen Datenträger oder ein RAID-Gerät auf diesen verschlüsselten Geräten erstellt haben. (Dadurch wird das Dateisystemformat, das wir während des Vorbereitungsprozesses verwendet haben, entfernt).

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Entfernen der temporären Ordner und temporären fstab-Einträge
Sie heben die Einbindung der Dateisysteme auf den Datenträgern auf, die als Teil von LVM verwendet werden sollen.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
Entfernen Sie außerdem die Einträge in „/etc/fstab“:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Sicherstellen, dass die Datenträger nicht eingebunden sind und die Einträge aus „/etc/fstab“ entfernt wurden

```bash
lsblk
```
![Überprüfung, ob die Einbindung temporärer Dateisysteme aufgehoben wird](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

Überprüfen Sie auch, ob die Datenträger konfiguriert sind:
```bash
cat /etc/fstab
```
![Überprüfung, ob temporäre fstab-Einträge entfernt werden](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Schritte für LVM-on-Crypt
Nachdem die zugrunde liegenden Datenträger nun verschlüsselt wurden, können Sie die LVM-Strukturen erstellen.

Verwenden Sie anstelle des Gerätenamens die Pfade in „/dev/mapper“ für die einzelnen Datenträger, um ein physisches Volume zu erstellen (auf der verschlüsselten Ebene des Datenträgers, nicht auf dem Datenträger selbst).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurieren von LVM auf den verschlüsselten Ebenen
#### <a name="create-the-physical-volumes"></a>Erstellen der physischen Volumes
Sie erhalten eine Warnung mit der Frage, ob die Dateisystemsignatur entfernt werden kann. Setzen Sie den Vorgang wie dargestellt durch die Eingabe von **y** oder **echo „y“** fort:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Überprüfung, ob ein physisches Volume erstellt wurde](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Die Gerätenamen von „/dev/mapper/“ müssen durch Ihre tatsächlichen Werte entsprechend der Ausgabe von **lsblk** ersetzt werden.

#### <a name="verify-the-information-for-physical-volumes"></a>Überprüfen der Informationen für physische Volumes
```bash
pvs
```

![Informationen für physische Volumes](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Erstellen der Volumegruppe
Erstellen Sie die Volumegruppe mithilfe der bereits initialisierten Geräte:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Überprüfen der Informationen für die Volumegruppe

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informationen für die Volumegruppe](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Erstellen der logischen Volumes

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Überprüfen der erstellten logischen Volumes

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informationen für logische Volumes](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Erstellen von Dateisystemen auf den Strukturen für logische Volumes

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Erstellen der Bereitstellungspunkte für die neuen Dateisysteme

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Hinzufügen der neuen Dateisysteme zu „/etc/fstab“ und Einbinden der Dateisysteme

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Überprüfen der Einbindung der neuen Dateisysteme

```bash
lsblk -fs
df -h
```
![Screenshot mit einem Konsolenfenster mit Dateisystemen, die als data0 und data1 eingebunden sind.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Bei dieser Variation von **lsblk** werden die Geräte aufgelistet, auf denen die Abhängigkeiten in umgekehrter Reihenfolge angezeigt werden. Mit dieser Option können die Geräte, die nach dem logischen Volume gruppiert sind, anstelle der ursprünglichen Gerätenamen in der Form „/dev/sd[Datenträgernummer]“ identifiziert werden.

Es ist wichtig, dass Sie sicherstellen, dass die Option **nofail** zu den Bereitstellungspunktoptionen der LVM-Volumes hinzugefügt wird, die auf einem mit Azure Disk Encryption verschlüsselten Gerät erstellt wurden. Sie verhindert, dass das Betriebssystem beim Startvorgang (oder im Wartungsmodus) hängen bleibt.

Wenn Sie die Option **nofail** nicht verwenden:

- Das Betriebssystem erreicht nie die Phase, in der Azure Disk Encryption gestartet wird und die Datenträger entsperrt und eingebunden werden. 
- Die verschlüsselten Datenträger werden am Ende des Startvorgangs entsperrt. Die LVM-Volumes und Dateisysteme werden automatisch eingebunden, bis sie von Azure Disk Encryption entsperrt werden. 

Sie können das Neustarten des virtuellen Computers testen und überprüfen, ob die Dateisysteme nach dem Start ebenfalls automatisch eingebunden werden. Dieser Vorgang kann je nach Anzahl und Größe der Dateisysteme einige Minuten dauern.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Neustarten der VM und Überprüfen nach dem Neustart

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Schritte für RAID-on-Crypt
Nachdem die zugrunde liegenden Datenträger nun verschlüsselt wurden, können Sie mit dem Erstellen der RAID-Strukturen fortfahren. Der Prozess ist derselbe wie der für LVM, aber anstatt den Gerätenamen zu verwenden, verwenden Sie die Pfade „/dev/mapper“ für die einzelnen Datenträger.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Konfigurieren von RAID basierend auf der verschlüsselten Ebene der Datenträger
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informationen zum konfigurierten RAID über den Befehl „mdadm“](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Die Gerätenamen von „/dev/mapper/“ müssen durch Ihre tatsächlichen Werte entsprechend der Ausgabe von **lsblk** ersetzt werden.

### <a name="checkmonitor-raid-creation"></a>Überprüfen/Überwachen der RAID-Erstellung
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Status von RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Erstellen eines Dateisystems auf dem neuen RAID-Gerät
```bash
mkfs.ext4 /dev/md10
```

Erstellen Sie einen neuen Bereitstellungspunkt für das Dateisystem, fügen Sie das neue Dateisystem in „/etc/fstab“ hinzu, und binden Sie es ein:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Überprüfen Sie die Einbindung des neuen Dateisystems:

```bash
lsblk -fs
df -h
```
![Screenshot mit einem Konsolenfenster mit einem Dateisystem, das als raiddata eingebunden ist.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Es ist wichtig, dass Sie sicherstellen, dass die Option **nofail** zu den Bereitstellungspunktoptionen der RAID-Volumes hinzugefügt wird, die auf einem mit Azure Disk Encryption verschlüsselten Gerät erstellt wurden. Sie verhindert, dass das Betriebssystem beim Startvorgang (oder im Wartungsmodus) hängen bleibt.

Wenn Sie die Option **nofail** nicht verwenden:

- Das Betriebssystem erreicht nie die Phase, in der Azure Disk Encryption gestartet wird und die Datenträger entsperrt und eingebunden werden.
- Die verschlüsselten Datenträger werden am Ende des Startvorgangs entsperrt. Die RAID-Volumes und Dateisysteme werden automatisch eingebunden, bis sie von Azure Disk Encryption entsperrt werden.

Sie können das Neustarten des virtuellen Computers testen und überprüfen, ob die Dateisysteme nach dem Start ebenfalls automatisch eingebunden werden. Dieser Vorgang kann je nach Anzahl und Größe der Dateisysteme einige Minuten dauern.

```bash
shutdown -r now
```

Wenn Sie sich anmelden können:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Nächste Schritte

- [Ändern der Größe von Verwaltungsgeräten für logische Volumes, die mit Azure Disk Encryption verschlüsselt sind](how-to-resize-encrypted-lvm.md)
- [Azure Disk Encryption-Problembehandlung](disk-encryption-troubleshooting.md)
