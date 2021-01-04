---
title: Erstellen und Hochladen einer Linux-VHD
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Linux-Betriebssystem enthält.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: ef4175d24cfd02bb5cb6470b6334fea190b5bec2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500596"
---
# <a name="information-for-non-endorsed-distributions"></a>Informationen zu nicht unterstützten Distributionen

Das Azure-Plattform-SLA gilt für virtuelle Computer unter dem Betriebssystem Linux nur dann, wenn eine der [bestätigten Distributionen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) verwendet wird. Für diese unterstützten Distributionen stehen im Azure Marketplace vorab konfigurierte Linux-Images zur Verfügung.

* [Linux auf von Azure unterstützten Verteilungen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Unterstützung für Linux-Images in Microsoft Azure](https://support.microsoft.com/kb/2941892)

Für alle Distributionen, die in Azure ausgeführt werden, gelten einige Voraussetzungen. In diesem Artikel können nicht alle Aspekte abgedeckt werden, da jede Distribution anders ist. Auch wenn alle unten genannten Kriterien erfüllt sind, müssen Sie Ihr Linux-System unter Umständen noch erheblich anpassen, damit es ordnungsgemäß ausgeführt wird.

Wir empfehlen Ihnen, mit einer der [unterstützten Distributionen von Linux in Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zu beginnen. In den folgenden Artikeln wird gezeigt, wie Sie die verschiedenen in Azure unterstützten Linux-Distributionen vorbereiten:

- [CentOS-basierte Verteilungen](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES &amp; openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Der restliche Artikel enthält eine allgemeine Anleitung zum Ausführen Ihrer Linux-Distribution in Azure.

## <a name="general-linux-installation-notes"></a>Allgemeine Installationshinweise für Linux
* Das Hyper-V-VHDX-Format (Virtual Hard Disk) wird in Azure nicht unterstützt, sondern nur *VHDs mit fester Größe*.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem Cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) in das VHD-Format konvertieren. Wenn Sie VirtualBox verwenden, wählen Sie beim Erstellen des Datenträgers die Option **Feste Größe** aus, nicht die Standardeinstellung mit dynamischer Zuweisung.
* Azure unterstützt die virtuellen Computer Gen1 (BIOS-Start) und Gen2 (UEFI-Start).
* Die maximal zulässige Größe für die virtuelle Festplatte beträgt 1.023 GB.
* Beim Installieren des Linux-Betriebssystems wird empfohlen, anstelle von Logical Volume Manager (LVM), der bei vielen Installationen voreingestellt ist, Standardpartitionen zu verwenden. Mit Standardpartitionen lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein Betriebssystemdatenträger zu Fehlerbehebungszwecken an einen anderen identischen virtuellen Computer angefügt werden soll. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) können auf Datenträgern verwendet werden.
* Zum Einbinden von UDF-Dateisystemen ist Kernelunterstützung erforderlich. Beim ersten Start in Azure wird die Bereitstellungskonfiguration mithilfe von UDF-formatierten Medien, die an den Gast angefügt sind, an den virtuellen Linux-Computer übergeben. Der Azure Linux-Agent muss das UDF-Dateisystem einbinden, um die Konfiguration zu lesen und die VM bereitzustellen.
* Linux-Kernelversionen vor Version 2.6.37 unterstützen keinen NUMA (Non-Uniform Memory Access, nicht einheitlicher Speicherzugriff) auf Hyper-V mit größeren VM-Größen. Dieses Problem betrifft in erster Linie ältere Distributionen, die den Red Hat 2.6.32-Upstreamkernel verwenden, und wurde in Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504) behoben. Systeme, auf denen benutzerdefinierte Kernel ausgeführt werden, die älter als 2.6.37 sind, bzw. RHEL-basierte Kernel, die älter als 2.6.32-504 sind, müssen in „grub.conf“ in der Kernelbefehlszeile den Startparameter `numa=off` festlegen. Weitere Informationen finden Sie unter [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurieren Sie keine Auslagerungspartition auf dem Betriebssystemdatenträger. Der Linux-Agent kann so konfiguriert werden, dass er eine Auslagerungsdatei auf dem temporären Ressourcendatenträger erstellt, wie in den folgenden Schritten beschrieben.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Stellen Sie beim Konvertieren eines unformatierten Datenträgers in das VHD-Format sicher, dass die Größe des unformatierten Datenträgers vor der Konvertierung ein Vielfaches von 1 MB beträgt – wie in den folgenden Schritten beschrieben.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installieren von Kernelmodulen ohne Hyper-V
Azure wird auf dem Hyper-V-Hypervisor ausgeführt, weshalb Linux anfordert, dass bestimmte Kernelmodule in Azure ausgeführt werden. Wenn Sie über einen außerhalb von Hyper-V erstellten virtuellen Computer verfügen, enthalten Linux-Installationsprogramme möglicherweise keine Treiber für Hyper-V auf dem anfänglichen RAM-Datenträger („initrd“ oder „initramfs“), sofern auf dem virtuellen Computer nicht die Ausführung in einer Hyper-V-Umgebung erkannt wird. Wenn ein anderes Virtualisierungssystem (z.B. VirtualBox, KVM usw.) zur Vorbereitung des Linux-Images verwendet wird, müssen Sie möglicherweise „initrd“ neu erstellen, damit mindestens die Kernelmodule „hv_vmbus“ und „hv_storvsc“ auf dem anfänglichen RAM-Datenträger verfügbar sind.  Dies ist ein bekanntes Problem auf Systemen, die auf der Red Hat-Upstreamdistribution basieren, und tritt möglicherweise auch auf anderen Systemen auf.

Der Mechanismus für die Neuerstellung des Images "initrd" oder "initramfs" variiert je nach der Verteilung. In der Dokumentation zur Distribution oder über den Support erhalten Sie Informationen zur geeigneten Vorgehensweise.  Hier sehen Sie ein Beispiel für die Neuerstellung von „initrd“ mit dem Hilfsprogramm `mkinitrd`:

1. Sichern Sie das bestehende Image „initrd“:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Erstellen Sie „initrd“ mit den Kernelmodulen „hv_vmbus“ und „hv_storvsc“ neu:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Ändern der Größe von virtuellen Festplatten
VHD-Images in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist.  In der Regel sind mit Hyper-V erstellte virtuelle Festplatten bereits ordnungsgemäß ausgerichtet.  Falls die virtuelle Festplatte nicht ordnungsgemäß ausgerichtet ist, wird möglicherweise eine Fehlermeldung wie die folgende angezeigt, wenn Sie versuchen, ein Image von der virtuellen Festplatte zu erstellen.

* Die VHD „http://\//\<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd“ weist eine nicht unterstützte virtuelle Größe von 21.475.270.656 Bytes auf. Die Größe muss eine ganze Zahl (in MB) sein.

Ändern Sie in diesem Fall die Größe des virtuellen Computers über die Hyper-V-Manager-Konsole oder mit dem PowerShell-Cmdlet [Resize-VHD](/powershell/module/hyper-v/resize-vhd?view=win10-ps).  Wenn Sie nicht in einer Windows-Umgebung arbeiten, sollten Sie `qemu-img` verwenden, um (falls erforderlich) die Konvertierung durchzuführen und die Größe der virtuellen Festplatte zu ändern.

> [!NOTE]
> Es gibt einen [bekannten Fehler in den qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611)-Versionen >= 2.2.1, der zu einer nicht ordnungsgemäßen Formatierung der virtuellen Festplatte führt. Dieses Problem wurde in QEMU 2.6 behoben. Es wird empfohlen, entweder `qemu-img` 2.2.0 oder niedriger oder 2.6 oder höher zu verwenden.
> 

1. Das direkte Ändern der Größe der virtuellen Festplatte mithilfe von Tools wie `qemu-img` oder `vbox-manage` kann dazu führen, dass die virtuelle Festplatte nicht startfähig ist.  Es wird empfohlen, die virtuelle Festplatte zuerst in ein unformatiertes Datenträgerimage zu konvertieren.  Wenn das VM-Image bereits als unformatiertes Datenträgerimage erstellt wurde (die Standardeinstellung für einige Hypervisoren wie z.B. KVM), können Sie diesen Schritt überspringen.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Berechnen Sie die erforderliche Größe des Datenträgerimages, sodass die virtuelle Größe auf 1 MB ausgerichtet ist.  Das folgende Bash-Shell-Skript verwendet `qemu-img info`, um die virtuelle Größe des Datenträgerimages zu ermitteln, und berechnet dann die Größe bis zum nächsten MB-Wert.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Ändern Sie die Größe des unformatierten Datenträgers mithilfe von `$rounded_size`, wie oben festgelegt.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Konvertieren Sie nun den unformatierten Datenträger zurück in eine virtuelle Festplatte mit fester Größe.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Bei QEMU Version 2.6+ müssen Sie die Option `force_size` einschließen.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux-Kernelanforderungen

Die Treiber für die Linux-Integrationsdienste (Linux Integration Services, LIS) für Hyper-V und Azure tragen direkt zum Linux-Upstream-Kernel bei. Viele Distributionen, die eine neue Linux-Kernelversion (z.B. 3.x) enthalten, verfügen bereits über diese Treiber oder bieten andernfalls zurückportierte Versionen dieser Treiber mit ihren Kernels.  Diese Treiber werden im Upstreamkernel ständig mit neuen Fehlerbehebungen und Features aktualisiert. Daher empfiehlt sich, nach Möglichkeit eine [unterstützte Distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) auszuführen, die diese Fehlerbehebungen und Updates enthält.

Wenn Sie eine Variante der Red Hat Enterprise Linux-Versionen 6.0 bis 6.3 ausführen, müssen Sie die [neuesten LIS-Treiber für Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409) installieren. Ab RHEL 6.4+ (und Ableitungen) sind die LIS-Treiber bereits im Kernel enthalten, sodass keine zusätzlichen Installationspakete nötig sind.

Wenn ein benutzerdefinierter Kernel erforderlich ist, empfiehlt es sich, eine neue Kernelversion (z.B. 3.8+) zu verwenden. Bei Distributionen oder Anbietern, die ihren Kernel selbst verwalten, müssen Sie die LIS-Treiber regelmäßig vom Upstreamkernel zu Ihrem benutzerdefinierten Kernel zurückportieren.  Selbst wenn Sie bereits eine relativ aktuelle Kernelversion ausführen, wird dringend empfohlen, die Upstreamfehlerbehebungen in den LIS-Treibern nachzuverfolgen und diese bei Bedarf zurückzuportieren. Die Speicherorte der LIS-Treiberquelldateien sind in der Datei [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) in der Linux-Kernelquellstruktur angegeben:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Die folgenden Patches müssen im Kernel enthalten sein. Diese Liste kann nicht alle Distributionen vollständig abdecken.

* [ata_piix: defer disks to the Hyper-V drivers by default (Standardmäßiges Zurückstellen von Datenträgern zu Hyper-V-Treibern)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Account for in-transit packets in the RESET path (Konto für Pakete auf dem Transportweg im RESET-Pfad)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: avoid usage of WRITE_SAME (Vermeiden der Verwendung von WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Disable WRITE SAME for RAID and virtual host adapter drivers (Deaktivieren von WRITE SAME für RAID und virtuelle Hostadaptertreiber)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL pointer dereference fix (Beheben der NULL-Zeigerdereferenzierung)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: ring buffer failures may result in I/O freeze (Einfrieren von E/A durch Ringpufferfehler, in englischer Sprache)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: protect against double execution of __scsi_remove_device (Schutz gegen doppelte Ausführung von „__scsi_remove_device“)](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux Agent
Der [Azure-Linux-Agent](../extensions/agent-linux.md) `waagent` stellt einen virtuellen Linux-Computer in Azure bereit. Im [GitHub-Repository für Linux-Agents](https://github.com/Azure/WALinuxAgent) können Sie die neueste Version abrufen, Probleme einreichen oder Pull Requests senden.

* Der Linux-Agent wird unter der Apache 2.0-Lizenz veröffentlicht. Viele Distributionen stellen bereits RPM- oder DEB-Pakete für den Agent bereit – diese können ganz einfach installiert und aktualisiert werden.
* Für den Azure Linux-Agent ist Python v2.6+ erforderlich.
* Für den Agent ist zudem das Modul python-pyasn1 erforderlich. Die meisten Distributionen stellen dieses Modul als separates Paket für die Installation bereit.
* In einigen Fällen ist der Azure-Linux-Agent möglicherweise nicht mit NetworkManager kompatibel. Viele der von Distributionen bereitgestellten RPM/DEB-Pakete konfigurieren NetworkManager – dies führt zu einem Konflikt mit dem waagent-Paket. In diesen Fällen wird NetworkManager deinstalliert, wenn Sie das Linux-Agent-Paket installieren.
* Der Azure-Linux-Agent muss mindestens die [unterstützte Mindestversion](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) aufweisen.

## <a name="general-linux-system-requirements"></a>Allgemeine Linux-Systemanforderungen

1. Ändern Sie die Zeile für den Kernelstart in GRUB oder GRUB2 so, dass die folgenden Parameter eingeschlossen werden. Auf diese Weise werden alle Konsolennachrichten an den ersten seriellen Port gesendet. Diese Nachrichten helfen dem Azure-Supportteam beim Debuggen von Problemen.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Wir empfehlen zudem, die folgenden Parameter zu *entfernen*, sofern sie vorhanden sind.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Weder grafische Startvorgänge noch Startvorgänge im stillen Modus sind in einer Cloudumgebung nützlich, in der alle Protokolle an den seriellen Port gesendet werden sollen. Die Option `crashkernel` kann bei Bedarf konfiguriert bleiben. Beachten Sie jedoch, dass dieser Parameter die Menge an verfügbarem Arbeitsspeicher im virtuellen Computer um mindestens 128 MB reduziert, was bei kleineren VM-Größen problematisch sein kann.

1. Installieren des Azure Linux Agent:
  
    Der Azure Linux-Agent ist für das Bereitstellen eines Linux-Images auf Azure erforderlich.  Viele Distributionen stellen den Agent als RPM- oder DEB-Paket bereit (das Paket heißt in der Regel „WALinuxAgent“ oder „walinuxagent“).  Der Agent kann zudem manuell installiert werden, indem die folgenden Schritte im [Linux Agent Guide](../extensions/agent-linux.md)eingehalten werden.

1. Stellen Sie sicher, dass der SSH-Server installiert und so konfiguriert ist, dass er beim Booten hochfährt.  Dies ist in der Regel die Standardkonfiguration.

1. Erstellen Sie keinen Auslagerungsbereich auf dem Betriebssystemdatenträger.
  
    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Der lokale Ressourcendatenträger ist ein *temporärer* Datenträger und kann geleert werden, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Ändern Sie nach dem Installieren des Azure-Linux-Agents (siehe Schritt 2) die folgenden Parameter in „/etc/waagent.conf“ nach Bedarf.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Führen Sie die folgenden Befehle aus, um die Bereitstellung des virtuellen Computers aufzuheben.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > In Virtualbox wird nach dem Ausführen von `waagent -force -deprovision` möglicherweise der folgende Fehler angezeigt: `[Errno 5] Input/output error`. Diese Fehlermeldung ist nicht kritisch und kann ignoriert werden.

* Fahren Sie den virtuellen Computer herunter, und laden Sie die virtuelle Festplatte in Azure hoch.