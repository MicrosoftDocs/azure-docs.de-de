---
title: Erstellen und Hochladen einer Ubuntu-Linux-VHD in Azure
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Ubuntu-Linux-Betriebssystem enthält.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: d3bdc3d0705bd8edc70f55b8372818e60859eedd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500562"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Vorbereiten eines virtuellen Ubuntu-Computers für Azure


Ubuntu veröffentlicht jetzt auf [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) offizielle Azure-VHDs zum Herunterladen. Wenn Sie Ihr eigenes spezialisiertes Ubuntu-Image für Azure erstellen müssen, empfiehlt es sich, mit diesen bekannten, funktionierenden VHDs zu beginnen und sie nach Bedarf anzupassen statt das manuelle Verfahren unten anzuwenden. Die neuesten Versionen des Images können sich immer an folgenden Speicherorten befinden:

* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Ubuntu-Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Installationshinweise zu Ubuntu**

* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes), in dem weitere Tipps zur Vorbereitung von Linux für Azure enthalten sind.
* Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem Cmdlet `Convert-VHD` in das VHD-Format konvertieren.
* Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) können wahlweise auf Datenträgern verwendet werden.
* Konfigurieren Sie keine Auslagerungspartition auf einem Betriebssystemdatenträger. Der Bereitstellungs-Agent für die Cloudinitialisierung kann so konfiguriert werden, dass er eine Auslagerungsdatei oder eine Auslagerungspartition auf dem temporären Ressourcendatenträger erstellt. Weitere Informationen dazu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="manual-steps"></a>Manuelle Schritte
> [!NOTE]
> Ziehen Sie vor dem Erstellen Ihres eigenen benutzerdefinierten Ubuntu-Images für Azure stattdessen die Verwendung eines vorgefertigten und getesteten Images von [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) in Betracht.
> 
> 

1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.

3. Ersetzen Sie die aktuellen Repositorys im Image, um die Azure-Repositorys von Ubuntu zu verwenden.

    Vor der Bearbeitung von `/etc/apt/sources.list` sollten Sie eine Sicherung erstellen:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16.04 und Ubuntu 18.04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Die Ubuntu Azure-Images verwenden jetzt den [für Azure angepassten Kernel](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel). Aktualisieren Sie das Betriebssystem auf den neuesten, für Azure angepassten Kernel, und installieren Sie Azure Linux-Tools (einschließlich Hyper-V-Abhängigkeiten), indem Sie die folgenden Befehle ausführen:

    Ubuntu 16.04 und Ubuntu 18.04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Modifizieren Sie die Boot-Zeile des Kernels für Grub, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür `/etc/default/grub` in einem Texteditor. Suchen Sie nach der Variablen `GRUB_CMDLINE_LINUX_DEFAULT` (oder fügen Sie diese gegebenenfalls hinzu), und bearbeiten Sie sie, um die folgenden Parameter einzubinden:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Speichern und schließen Sie diese Datei. Führen Sie danach `sudo update-grub` aus. Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern in technischer Hinsicht unterstützen.

6. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.

7. Installieren Sie die Cloudinitialisierung (den Bereitstellungs-Agent) und den Azure Linux-Agent (den Handler für Gasterweiterungen). Die Cloudinitialisierung konfiguriert mit netplan die Systemnetzwerk-Konfiguration während der Bereitstellung und jedem nachfolgenden Start.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  Das Paket `walinuxagent` entfernt unter Umständen die Pakete `NetworkManager` und `NetworkManager-gnome`, falls sie installiert sind.

8. Entfernen Sie die Standardkonfigurationen der Cloudinitialisierung und die restlichen netplan-Artefakte, die möglicherweise mit der Bereitstellung der Cloudinitialisierung in Azure in Konflikt stehen:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Konfigurieren Sie die Cloudinitialisierung, um das System mithilfe der Azure-Datenquelle bereitzustellen:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Konfigurieren Sie den Azure Linux-Agent so, dass er die Bereitstellung mit der Cloudinitialisierung durchführt. Um weitere Informationen zu diesen Optionen zu erhalten, werfen Sie einen Blick auf das [WALinuxAgent-Projekt](https://github.com/Azure/WALinuxAgent).

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Bereinigen Sie die Runtimeartefakte und Protokolle von Cloudinitialisierung und Azure Linux-Agent:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:

    > [!NOTE]
    > Der `sudo waagent -force -deprovision+user`-Befehl versucht, das System zu bereinigen und für eine erneute Bereitstellung vorzubereiten. Die `+user`-Option löscht das zuletzt bereitgestellte Benutzerkonto und die zugehörigen Daten.

    > [!WARNING]
    > Die Aufhebung der Bereitstellung mit obigem Befehl garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden und das Image für eine weitere Verteilung geeignet ist.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**.

14. Azure akzeptiert nur VHDs mit fester Größe. Wenn der Betriebssystem-Datenträger des virtuellen Computers keine VHD mit fester Größe ist, verwenden Sie das `Convert-VHD`-PowerShell-Cmdlet, und geben Sie die `-VHDType Fixed`-Option an. Sehen Sie sich hier die Dokumentation zu `Convert-VHD` an: [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=win10-ps).


## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt mit Ihrer Ubuntu-Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).