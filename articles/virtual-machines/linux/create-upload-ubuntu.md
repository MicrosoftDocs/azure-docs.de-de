---
title: Erstellen und Hochladen einer Ubuntu-Linux-VHD in Azure
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein Ubuntu-Linux-Betriebssystem enthält.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 7776e0005facb57d223a1ba1e73d1efa30edec49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60327948"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Vorbereiten eines virtuellen Ubuntu-Computers für Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Offizielle Ubuntu-Cloud-Images
Ubuntu veröffentlicht jetzt auf [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) offizielle Azure-VHDs zum Herunterladen. Wenn Sie Ihr eigenes spezialisiertes Ubuntu-Image für Azure erstellen müssen, empfiehlt es sich, mit diesen bekannten, funktionierenden VHDs zu beginnen und sie nach Bedarf anzupassen statt das manuelle Verfahren unten anzuwenden. Die neuesten Versionen des Images können sich immer an folgenden Speicherorten befinden:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/cosmic/current/cosmic-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Ubuntu-Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](https://technet.microsoft.com/library/hh846766.aspx).

**Installationshinweise zu Ubuntu**

* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes), in dem weitere Tipps zur Vorbereitung von Linux für Azure enthalten sind.
* Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.
* Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) können wahlweise auf Datenträgern verwendet werden.
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen.  Weitere Informationen dazu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="manual-steps"></a>Manuelle Schritte
> [!NOTE]
> Ziehen Sie vor dem Erstellen Ihres eigenen benutzerdefinierten Ubuntu-Images für Azure stattdessen die Verwendung eines vorgefertigten und getesteten Images von [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) in Betracht.
> 
> 

1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.

2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.

3. Ersetzen Sie die aktuellen Repositorys im Image zum Verwenden der Azure-Repositorys von Ubuntu. Die Schritte können je nach Ubuntu-Version geringfügig abweichen.
   
    Vor der Bearbeitung von `/etc/apt/sources.list` sollten Sie eine Sicherung erstellen:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Die Ubuntu-Azure-Images folgen jetzt dem *HardWare Enablement* -Kernel (HWE). Aktualisieren Sie das Betriebssystem auf den neuesten Kernel, indem Sie die folgenden Befehle ausführen:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Weitere Informationen:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Modifizieren Sie die Boot-Zeile des Kernels für Grub, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür `/etc/default/grub` in einem Texteditor. Suchen Sie nach der Variablen `GRUB_CMDLINE_LINUX_DEFAULT` (oder fügen Sie diese gegebenenfalls hinzu), und bearbeiten Sie sie, um die folgenden Parameter einzubinden:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Speichern und schließen Sie diese Datei. Führen Sie danach `sudo update-grub` aus. Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern in technischer Hinsicht unterstützen.

6. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.

7. Installieren des Azure Linux Agent:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Das Paket `walinuxagent` entfernt unter Umständen die Pakete `NetworkManager` und `NetworkManager-gnome`, falls sie installiert sind.

Aktualisieren Sie für Ubuntu 18.04/18.10 die Azure-Datenquelle in folgender Weise: /etc/cloud/cloud.cfg.d/90-azure.cfg, fügen Sie diesen Code am Ende der Datei hinzu:

**Wichtig: der Code muss genau wie dargestellt hinzugefügt werden, einschließlich Leerzeichen.**

```bash
datasource:
   Azure:
     agent_command: [service, walinuxagent, start]
```

1. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="references"></a>Referenzen
[Ubuntu Hardware Enablement-Kernel (HWE)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt mit Ihrer Ubuntu-Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).

