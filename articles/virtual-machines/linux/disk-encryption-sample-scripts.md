---
title: Azure Disk Encryption-Beispielskripts
description: Dieser Artikel enthält den Anhang zu Microsoft Azure Disk Encryption für virtuelle Linux-Computer.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: d178ae39d3af6b39047501f0bc47acbc6e792f48
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911493"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>Azure Disk Encryption-Beispielskripts für virtuelle Linux-Computer

Dieser Artikel bietet Beispielskripts für die Vorbereitung vorab verschlüsselter VHDs und andere Aufgaben.  

> [!NOTE]
> Alle Skripts beziehen sich auf die neueste Nicht-AAD-Version von ADE, sofern nicht anders angegeben.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-Beispielskripts für Azure Disk Encryption 

- **Auflisten aller verschlüsselten VMs Ihres Abonnements**
  
  Sie finden mit [diesem PowerShell-Skript](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1) alle mit ADE verschlüsselten VMs samt Erweiterungsversion in allen Ressourcengruppen, die in einem Abonnement vorhanden sind.

  Alternativ zeigen diese Cmdlets alle mit ADE verschlüsselten VMs an (jedoch nicht die Erweiterungsversion):

   ```azurepowershell-interactive
   $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
   $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
   Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
   ```

- **Auflisten aller verschlüsselten VMSS-Instanzen in Ihrem Abonnement**
    
    Sie finden mit [diesem PowerShell-Skript](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1) alle mit ADE verschlüsselten VMSS-Instanzen samt Erweiterungsversion in allen Ressourcengruppen, die in einem Abonnement vorhanden sind.

- **Auflisten aller Geheimnisse der Datenträgerverschlüsselung, die zum Verschlüsseln von VMs in einem Schlüsseltresor verwendet werden** 

   ```azurepowershell-interactive
   Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
   ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Verwenden des PowerShell-Skripts für die Voraussetzungen für Azure Disk Encryption
Wenn Sie bereits mit den Voraussetzungen für Azure Disk Encryption vertraut sind, können Sie das [PowerShell-Skript zur Überprüfung der Azure Disk Encryption-Voraussetzungen](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ) verwenden. Ein Beispiel für die Verwendung dieses PowerShell-Skripts finden Sie im [Schnellstart: Verschlüsseln einer VM](disk-encryption-powershell-quickstart.md). Sie können die Kommentare aus einem Abschnitt des Skripts, beginnend ab Zeile 211, entfernen, um alle Datenträger für vorhandene virtuelle Computer in einer vorhandenen Ressourcengruppe zu verschlüsseln. 

Die folgende Tabelle zeigt, welche Parameter im PowerShell-Skript verwendet werden können: 


|Parameter|BESCHREIBUNG|Erforderlich?|
|------|------|------|
|$resourceGroupName| Name der Ressourcengruppe, zu der der Schlüsseltresor gehört.  Sofern noch nicht vorhanden, wird eine neue Ressourcengruppe mit diesem Namen erstellt.| True|
|$keyVaultName|Name des Schlüsseltresors, in dem Verschlüsselungsschlüssel platziert werden sollen. Sofern noch nicht vorhanden, wird ein neuer Tresor mit diesem Namen erstellt.| True|
|$location|Standort des Schlüsseltresors. Der Schlüsseltresor und die zu verschlüsselnden virtuellen Computer müssen sich am gleichen Standort befinden. Mit `Get-AzLocation` können Sie eine Standortliste abrufen.|True|
|$subscriptionId|Bezeichner des zu verwendenden Azure-Abonnements.  Die Abonnement-ID kann mit `Get-AzSubscription` abgerufen werden.|True|
|$aadAppName|Name der Azure AD-Anwendung, die zum Schreiben von Geheimnissen in den Schlüsseltresor verwendet wird. Sofern noch nicht vorhanden, wird eine neue Anwendung mit diesem Namen erstellt. Wenn diese App bereits vorhanden ist, übergeben Sie den Parameter aadClientSecret an das Skript.|False|
|$aadClientSecret|Clientgeheimnis der Azure AD-Anwendung, die zuvor erstellt wurde.|False|
|$keyEncryptionKeyName|Name des optionalen Schlüssels für die Schlüsselverschlüsselung in Key Vault. Sofern noch nicht vorhanden, wird ein neuer Schlüssel mit diesem Namen erstellt.|False|

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Ver- oder Entschlüsseln von virtuellen Computer ohne eine Azure AD-App

- [Enable disk encryption on an existing or running Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad) (Aktivieren der Datenträgerverschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Linux-Computer)  
- [Disable encryption on a running Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) (Deaktivieren der Verschlüsselung auf einem ausgeführten virtuellen Linux-Computer) 
    - Die Deaktivierung der Verschlüsselung ist nur auf Datenvolumes für virtuelle Linux-Computer zulässig.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Ver- oder Entschlüsseln von virtuellen Computer mit einer Azure AD-App (früheres Release)
 
- [Enable disk encryption on an existing or running Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) (Aktivieren der Datenträgerverschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Linux-Computer)    


-  [Disable encryption on a running Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) (Deaktivieren der Verschlüsselung auf einem ausgeführten virtuellen Linux-Computer) 
    - Die Deaktivierung der Verschlüsselung ist nur auf Datenvolumes für virtuelle Linux-Computer zulässig. 


- [Create a new encrypted managed disk from a pre-encrypted VHD/storage blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk) (Erstellen eines neuen verschlüsselten verwalteten Datenträgers auf der Grundlage einer vorverschlüsselten VHD/eines Speicherblobs)
    - Erstellt einen neuen verschlüsselten verwalteten Datenträger, wenn eine vorverschlüsselte VHD und die entsprechenden Verschlüsselungseinstellungen vorhanden sind

## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Verschlüsseln eines Betriebssystemdatenträgers auf einer ausgeführten Linux-VM

### <a name="prerequisites-for-os-disk-encryption"></a>Voraussetzungen für die Verschlüsselung des Betriebssystemdatenträgers

* Die VM muss eine Distribution verwenden, die mit der Verschlüsselung von Betriebssystemdatenträgern kompatibel ist. Eine Liste dieser Distributionen finden Sie unter [Von Azure Disk Encryption unterstützte Betriebssysteme](disk-encryption-overview.md#supported-vms). 
* Die VM muss über ein Marketplace-Image im Azure Resource Manager erstellt werden.
* Azure-VM mit mindestens 4 GB RAM (7 GB empfohlen).
* (Für RHEL und CentOS) Deaktivieren Sie SELinux. Informationen zum Deaktivieren von SELinux finden Sie unter „4.4.2. Disabling SELinux“ (Deaktivieren von SELinux) im [SELinux User's and Administrator's Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) (SELinux-Handbuch für Benutzer und Administratoren) in der VM.
* Die VM muss nach dem Deaktivieren von SELinux mindestens einmal neu gestartet werden.

### <a name="steps"></a>Schritte
1. Erstellen Sie eine VM mit einer der oben angegebenen Distributionen.

   Für CentOS 7.2 wird die Verschlüsselung des Betriebssystemdatenträgers über ein spezielles Image unterstützt. Geben Sie zum Verwenden dieses Images beim Erstellen der VM „7.2n“ als SKU an:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Konfigurieren Sie die VM gemäß Ihren Anforderungen. Wenn Sie alle Datenträger (für Betriebssystem und Daten) verschlüsseln möchten, müssen die Datenträger für Daten angegeben und über „/etc/fstab“ bereitgestellt werden.

   > [!NOTE]
   > Verwenden Sie „UUID=...“, um Datenlaufwerke nicht über den Blockgerätnamen, z.B. „/dev/sdb1“, sondern unter „/etc/fstab“ anzugeben. Während der Verschlüsselung ändert sich die Reihenfolge der Laufwerke auf dem virtuellen Computer. Wenn für Ihren virtuellen Computer eine bestimmte Reihenfolge der Blockgeräte erforderlich ist, funktioniert die Bereitstellung nach der Verschlüsselung nicht mehr.

3. Melden Sie sich bei den SSH-Sitzungen ab.

4. Geben Sie zum Verschlüsseln des Betriebssystems für „volumeType“ die Option **All** oder **OS** an, wenn Sie die Verschlüsselung aktivieren.

   > [!NOTE]
   > Alle Prozesse des Benutzerbereichs, die nicht als `systemd`-Dienste ausgeführt werden, müssen per `SIGKILL` beendet werden. Starten Sie die VM neu. Planen Sie für den virtuellen Computer eine Downtime ein, wenn Sie die Verschlüsselung des Betriebssystemdatenträgers auf einem ausgeführten virtuellen Computer aktivieren.

5. Überprüfen Sie den Fortschritt der Verschlüsselung in regelmäßigen Abständen, indem Sie die Anleitung im [nächsten Abschnitt](#monitoring-os-encryption-progress) befolgen.

6. Wenn für „Get-AzVmDiskEncryptionStatus“ der Status „VMRestartPending“ angezeigt wird, können Sie die VM neu starten, indem Sie sich entweder bei der VM anmelden oder die Anmeldung per Portal, PowerShell oder CLI durchführen.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Es wird empfohlen, vor dem Neustart die [Startdiagnose](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) zu speichern.

## <a name="monitoring-os-encryption-progress"></a>Überwachen des Fortschritts der Betriebssystemverschlüsselung
Es gibt drei Möglichkeiten, den Fortschritt der Betriebssystemverschlüsselung zu überwachen:

* Verwenden Sie das `Get-AzVmDiskEncryptionStatus`-Cmdlet, und untersuchen Sie das Feld „ProgressMessage“:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Wenn die VM den Status „OS disk encryption started“ erreicht hat, dauert der Vorgang auf einer VM mit Premium-Speicher ca. 40 bis 50 Minuten.

  Aufgrund von [Issue #388](https://github.com/Azure/WALinuxAgent/issues/388) in WALinuxAgent werden `OsVolumeEncrypted` und `DataVolumesEncrypted` in einigen Distributionen als `Unknown` angezeigt. Bei WALinuxAgent Version 2.1.5 und höher wird dieses Problem automatisch behoben. Falls Sie `Unknown` in der Ausgabe sehen, können Sie den Status der Datenverschlüsselung über den Azure-Ressourcen-Explorer überprüfen.

  Wechseln Sie zum [Azure-Ressourcen-Explorer](https://resources.azure.com/), und erweitern Sie diese Hierarchie im Auswahlbereich auf der linken Seite:

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  Scrollen Sie unter „InstanceView“ nach unten, um den Verschlüsselungsstatus Ihrer Laufwerke anzuzeigen.

  ![VM-Instanzanzeige](./media/disk-encryption/vm-instanceview.png)

* Sehen Sie sich die [Startdiagnose](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) an. Meldungen der ADE-Erweiterung weisen das Präfix `[AzureDiskEncryption]` auf.

* Melden Sie sich per SSH bei der VM an, und rufen Sie das Erweiterungsprotokoll ab:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Es wird empfohlen, sich nicht bei der VM anzumelden, während die Betriebssystemverschlüsselung durchgeführt wird. Kopieren Sie die Protokolle nur dann, wenn die zwei weiteren Methoden nicht zum Erfolg geführt haben.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Vorbereiten einer vorverschlüsselten Linux-VHD
Die Vorbereitung für vorverschlüsselte VHDs kann je nach Distribution variieren. Es sind Beispiele zur Vorbereitung von Ubuntu 16, openSUSE 13.2 und CentOS 7 verfügbar. 

### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurieren Sie die Verschlüsselung während der Installation einer Distribution, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie beim Partitionieren von Datenträgern die Option **Configure encrypted volumes** (Verschlüsselte Volumes konfigurieren).

   ![Ubuntu 16.04-Setup: Konfigurieren verschlüsselter Volumes](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Erstellen Sie ein separates Startlaufwerk, das nicht verschlüsselt sein darf. Verschlüsseln Sie Ihr Stammlaufwerk.

   ![Ubuntu 16.04-Setup: Geräte für Verschlüsselung auswählen](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Geben Sie eine Passphrase an. Dies ist die Passphrase, die Sie in den Schlüsseltresor hochgeladen haben.

   ![Ubuntu 16.04-Setup: Passphrase angeben](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Schließen Sie die Partitionierung ab.

   ![Ubuntu 16.04-Setup: Partitionierung abschließen](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Beim Starten der VM werden Sie nach einer Passphrase gefragt. Verwenden Sie die Passphrase, die Sie in Schritt 3 angegeben haben.

   ![Ubuntu 16.04-Setup: Passphrase beim Starten angeben](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Bereiten Sie die VM für das Hochladen in Azure anhand [dieser Anleitung](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json) vor. Führen Sie den letzten Schritt (das Aufheben der VM-Bereitstellung) noch nicht aus.

Konfigurieren Sie die Verschlüsselung für Azure, indem Sie die folgenden Schritte ausführen:

1. Erstellen Sie unter „/usr/local/sbin/azure_crypt_key.sh“ eine Datei mit dem Inhalt des folgenden Skripts. Achten Sie auf KeyFileName, da dies der von Azure verwendete Dateiname für die Passphrase ist.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
   ```

2. Ändern Sie die Verschlüsselungskonfiguration in */etc/crypttab*. Diese sollte wie folgt aussehen:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Fügen Sie dem Skript Berechtigungen für die Ausführung hinzu:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Bearbeiten Sie */etc/initramfs-tools/modules* , indem Sie Zeilen anfügen:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Führen Sie `update-initramfs -u -k all` zum Aktualisieren von „initramfs“ aus, damit `keyscript` wirksam wird.

7. Nun können Sie die Bereitstellung des virtuellen Computers aufheben.

   ![Ubuntu 16.04-Setup: initramfs aktualisieren](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Fahren Sie mit dem nächsten Schritt fort, und laden Sie Ihre VHD in Azure hoch.

### <a name="opensuse-132"></a>openSUSE 13.2
Konfigurieren Sie die Verschlüsselung während der Installation einer Distribution, indem Sie diese Schritte ausführen:
1. Wählen Sie beim Partitionieren der Datenträger die Option **Encrypt Volume Group** (Volumegruppe verschlüsseln) aus, und geben Sie dann ein Kennwort ein. Dies ist das Kennwort, die Sie in Ihren Schlüsseltresor hochladen.

   ![openSUSE 13.2-Setup: Volumegruppe verschlüsseln](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Starten Sie die VM, indem Sie Ihr Kennwort verwenden.

   ![openSUSE 13.2-Setup: Passphrase beim Starten angeben](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Bereiten Sie die VM für das Hochladen in Azure mithilfe der Anweisungen im Abschnitt zum [Vorbereiten einer SLES- oder openSUSE-VM für Azure](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131) vor. Führen Sie den letzten Schritt (das Aufheben der VM-Bereitstellung) noch nicht aus.

Konfigurieren Sie die Verschlüsselung für Azure, indem Sie die folgenden Schritte ausführen:
1. Bearbeiten Sie die Datei „/etc/dracut.conf“, und fügen Sie die folgende Zeile hinzu:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Kommentieren Sie diese Zeilen am Ende der Datei „/usr/lib/dracut/modules.d/90crypt/module-setup.sh“ aus:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fügen Sie die folgende Zeile am Anfang der Datei „/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh“ an:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Und ändern Sie alle Vorkommen von:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   in:
   ```bash
    if [ 1 ]; then
   ```
4. Bearbeiten Sie die Datei „/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh“, und fügen Sie sie an „# Open LUKS device“ an:

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Führen Sie `/usr/sbin/dracut -f -v` aus, um initrd zu aktualisieren.

6. Nun können Sie die Bereitstellung der VM aufheben und Ihre VHD in Azure hochladen.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 und RHEL 7

Konfigurieren Sie die Verschlüsselung während der Installation einer Distribution, indem Sie diese Schritte ausführen:
1. Wählen Sie beim Partitionieren von Datenträgern die Option **Encrypt my data** (Daten verschlüsseln).

   ![CentOS 7-Setup: Installationsziel](./media/disk-encryption/centos-encrypt-fig1.png)

2. Stellen Sie sicher, dass für die Stammpartition die Option **Encrypt** (Verschlüsseln) ausgewählt ist.

   ![CentOS 7-Setup: Verschlüsselung für Stammpartition auswählen](./media/disk-encryption/centos-encrypt-fig2.png)

3. Geben Sie eine Passphrase an. Dies ist die Passphrase, die Sie in Ihren Schlüsseltresor hochladen.

   ![CentOS 7-Setup: Passphrase angeben](./media/disk-encryption/centos-encrypt-fig3.png)

4. Beim Starten der VM werden Sie nach einer Passphrase gefragt. Verwenden Sie die Passphrase, die Sie in Schritt 3 angegeben haben.

   ![CentOS 7-Setup: Passphrase beim Start eingeben](./media/disk-encryption/centos-encrypt-fig4.png)

5. Bereiten Sie die VM für das Hochladen in Azure mithilfe der Anweisungen zu CentOS 7.0+ im Abschnitt zum [Vorbereiten einer CentOS-basierten VM für Azure](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70) vor. Führen Sie den letzten Schritt (das Aufheben der VM-Bereitstellung) noch nicht aus.

6. Nun können Sie die Bereitstellung der VM aufheben und Ihre VHD in Azure hochladen.

Konfigurieren Sie die Verschlüsselung für Azure, indem Sie die folgenden Schritte ausführen:

1. Bearbeiten Sie die Datei „/etc/dracut.conf“, und fügen Sie die folgende Zeile hinzu:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Kommentieren Sie diese Zeilen am Ende der Datei „/usr/lib/dracut/modules.d/90crypt/module-setup.sh“ aus:
   ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
   ```

3. Fügen Sie die folgende Zeile am Anfang der Datei „/usr/lib/dracut/modules.d/90crypt/parse-crypt.sh“ an:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   Und ändern Sie alle Vorkommen von:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   zu
   ```bash
    if [ 1 ]; then
   ```
4. Bearbeiten Sie die Datei „/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh“, und fügen Sie sie nach „# Open LUKS device“ Folgendes an:
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Führen Sie „/usr/sbin/dracut -f -v“ aus, um initrd zu aktualisieren.

    ![CentOS 7-Setup: „/usr/sbin/dracut -f -v“ ausführen](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Hochladen einer verschlüsselten VHD in ein Azure-Speicherkonto
Nachdem die DM-Crypt-Verschlüsselung aktiviert wurde, muss die lokale verschlüsselte VHD in Ihr Speicherkonto hochgeladen werden.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Hochladen des Geheimnisses für den vorab verschlüsselten virtuellen Computer in Ihren Schlüsseltresor
Beim Verschlüsseln mit einer Azure AD-App (früheres Release) muss das zuvor abgerufene Geheimnis zur Datenträgerverschlüsselung als Geheimnis in den Schlüsseltresor hochgeladen werden. Der Schlüsseltresor muss für die Datenverschlüsselung aktiviert sein und über Berechtigungen für Ihren Azure AD-Client verfügen.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Geheimnis der Datenträgerverschlüsselung ohne Verschlüsselung per KEK
Verwenden Sie [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret), um das Geheimnis im Schlüsseltresor einzurichten. Die Passphrase wird als Base64-Zeichenfolge codiert und dann in den Schlüsseltresor hochgeladen. Stellen Sie außerdem sicher, dass die folgenden Tags festgelegt sind, während das Geheimnis im Schlüsseltresor erstellt wird.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Verwenden Sie im nächsten Schritt `$secretUrl`, um [den Betriebssystemdatenträger ohne KEK anzufügen](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Geheimnis der Datenträgerverschlüsselung mit Verschlüsselung per KEK
Das Geheimnis kann optional mit einem KEK verschlüsselt werden, bevor er in den Schlüsseltresor hochgeladen wird. Verwenden Sie die Wrapper-[API](/rest/api/keyvault/wrapkey), um das Geheimnis zuerst mit dem KEK zu verschlüsseln. Die Ausgabe dieses Wrapper-Vorgangs ist eine Base64-codierte URL-Zeichenfolge, die dann als Geheimnis mit dem [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret)-Cmdlet hochgeladen wird.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Verwenden Sie im nächsten Schritt `$KeyEncryptionKey` und `$secretUrl`, um den [ Betriebssystemdatenträger mit KEK](#using-a-kek) anzufügen.

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Angeben einer Geheimnis-URL beim Anfügen eines Betriebssystemdatenträgers

###  <a name="without-using-a-kek"></a>Ohne KEK
Beim Anfügen des Betriebssystemdatenträgers muss `$secretUrl` übergeben werden. Die URL wurde im Abschnitt „Geheimnis der Datenträgerverschlüsselung ohne Verschlüsselung per KEK“ generiert.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Mit KEK
Beim Anfügen des Betriebssystemdatenträgers müssen `$KeyEncryptionKey` und `$secretUrl` übergeben werden. Die URL wurde im Abschnitt „Geheimnis der Datenträgerverschlüsselung mit Verschlüsselung per KEK“ generiert.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
