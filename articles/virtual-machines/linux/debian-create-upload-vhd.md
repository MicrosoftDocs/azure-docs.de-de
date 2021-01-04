---
title: Vorbereiten einer Debian Linux-VHD
description: Hier erfahren Sie, wie Sie Debian-VHD-Images für VM-Bereitstellungen in Azure erstellen.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: 5d99e6ec0beb6eaf83f9c664683c33fc1b71e3f0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500545"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Vorbereiten einer Debian-VHD für Azure
## <a name="prerequisites"></a>Voraussetzungen
In diesem Abschnitt wird davon ausgegangen, dass Sie bereits ein Debian Linux-Betriebssystem aus einer ISO-Datei von der [Debian-Website](https://www.debian.org/distrib/) auf einer virtuelle Festplatte installiert haben. Es gibt mehrere Tools zum Erstellen von VHD-Dateien. Hyper-V ist nur ein Beispiel. Anweisungen zur Verwendung von Hyper-V finden Sie unter [Installieren von Hyper-V und Erstellen eines virtuellen Computers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Installationshinweise
* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes) mit weiteren Tipps zur Vorbereitung von Linux für Azure.
* Das modernere VHDX-Format wird in Azure noch nicht unterstützt. Sie können den Datenträger mit dem Hyper-V-Manager oder dem **Convert-VHD-Cmdlet** in das VHD-Format konvertieren.
* Beim Installieren des Linux-Systems empfiehlt es sich, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) können wahlweise auf Datenträgern verwendet werden.
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Azure-Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen. Weitere Informationen finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Wenn Sie einen unformatierten Datenträger in VHD konvertieren, müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers vor der Konvertierung ein Vielfaches von 1 MB ist. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Verwenden von azure-manage zum Erstellen von Debian-VHDs
Zum Generieren von Debian-VHDs für Azure sind Tools verfügbar (beispielsweise die [azure-manage](https://github.com/credativ/azure-manage)-Skripts von [Credativ](https://www.credativ.com/)). Dies ist der empfohlene Ansatz, mit dem vermieden werden kann, ein Image von Grund auf neu zu erstellen. Wenn Sie beispielsweise eine Debian 8-VHD erstellen möchten, führen Sie die folgenden Befehle aus, um das Hilfsprogramm `azure-manage` (einschließlich Abhängigkeiten) herunterzuladen und das Skript `azure_build_image` auszuführen:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Manuelles Vorbereiten einer Debian-VHD
1. Wählen Sie im Hyper-V-Manager den virtuellen Computer aus.
2. Klicken Sie auf **Verbinden** , um ein Konsolenfenster für den virtuellen Computer zu öffnen.
3. Wenn Sie das Betriebssystem mithilfe einer ISO-Datei installiert haben, kommentieren Sie in `/etc/apt/source.list` alle Zeilen aus, die sich auf „`deb cdrom`“ beziehen.

4. Bearbeiten Sie die Datei `/etc/default/grub`, und ändern Sie den **GRUB_CMDLINE_LINUX**-Parameter wie folgt, um zusätzliche Kernelparameter für Azure einzubinden.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Erstellen Sie Grub neu und führen Sie Folgendes aus:

    ```console
    # sudo update-grub
    ```

6. Fügen Sie „/etc/apt/sources.list“ die Debian-Azure-Repositorys für Debian 8, 9 oder 10 hinzu:

    **Debian 8.x „Jessie“**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9.x „Stretch“**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **Debian 10.x „Buster“**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. Installieren des Azure Linux Agent:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. Ab Debian 9 empfiehlt es sich, für virtuelle Computer in Azure den neuen Debian Cloud-Kernel zu verwenden. Wenn Sie diesen neuen Kernel installieren möchten, erstellen Sie zunächst eine Datei namens „/etc/apt/preferences.d/linux.pref“ mit folgendem Inhalt:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Führen Sie dann „sudo apt-get install linux-image-cloud-amd64“ aus, um den neuen Debian Cloud-Kernel zu installieren.

9. Setzen Sie den virtuellen Computer zurück und bereiten Sie ihn für die Bereitstellung in Azure vor. Führen Sie Folgendes aus:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Klicken Sie im Hyper-V-Manager auf **Aktion** > „Herunterfahren“. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt mit Ihrer Debian-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).