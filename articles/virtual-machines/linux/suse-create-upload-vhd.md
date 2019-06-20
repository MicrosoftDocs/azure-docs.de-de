---
title: Erstellen und Hochladen einer SUSE-Linux-VHD in Azure
description: Erfahren Sie, wie Sie eine virtuelle Azure-Festplatte (Virtual Hard Disk, VHD) erstellen und hochladen, die ein SUSE-Linux-Betriebssystem enthält.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 2b0c01ee4b1d1bc5ce83fc0afc309abfcf25f33e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419389"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Vorbereiten eines virtuellen SLES- oder openSUSE-Computers für Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie bereits ein SUSE- oder openSUSE-Linux-Betriebssystem auf einer virtuellen Festplatte installiert haben. Sie können VHD-Dateien mit unterschiedlichen Tools erstellen, beispielsweise mit einer Virtualisierungslösung wie Hyper-V. Anweisungen hierzu finden Sie unter [Installieren der Hyper-V-Rolle und Konfigurieren eines virtuellen Computers](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>Installationshinweise zu SLES/openSUSE
* Beachten Sie auch den Artikelabschnitt [Allgemeine Linux-Systemanforderungen](create-upload-generic.md#general-linux-installation-notes), in dem weitere Tipps zur Vorbereitung von Linux für Azure enthalten sind.
* Das VHDX-Format wird in Azure noch nicht unterstützt, dafür jedoch **virtuelle Festplatten mit fester Größe**.  Sie können den Datenträger mit dem Hyper-V-Manager oder dem convert-vhd-Cmdlet in das VHD-Format konvertieren.
* Beim Installieren des Linux-Systems wird empfohlen, anstelle von LVM (bei vielen Installationen oftmals voreingestellt) die Standardpartitionen zu verwenden. Dadurch lässt sich vermeiden, dass ein LVM-Namenskonflikt mit geklonten virtuellen Computern auftritt, besonders dann, wenn ein BS-Datenträger zu Fehlerbehebungszwecken mit einem anderen virtuellen Computer verbunden wird. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) können wahlweise auf Datenträgern verwendet werden.
* Konfigurieren Sie keine SWAP-Partition auf einem Betriebssystemdatenträger. Der Linux-Agent kann konfiguriert werden, eine Auslagerungsdatei auf dem temporären Ressourcendatenträger zu erstellen.  Weitere Informationen dazu finden Sie in den folgenden Schritten.
* Alle VHDs in Azure benötigen eine virtuelle Größe, die auf 1 MB ausgerichtet ist. Beim Konvertieren von einem unformatierten Datenträger in VHD müssen Sie sicherstellen, dass die Größe des unformatierten Datenträgers ein Vielfaches von 1 MB vor der Konvertierung beträgt. Weitere Informationen finden Sie in den [Linux-Installationshinweisen](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-suse-studio"></a>Verwenden von SUSE Studio
[SUSE Studio](http://www.susestudio.com) können Sie Ihre SLES- und openSUSE-Images für Azure und Hyper-V auf einfache Weise erstellen und verwalten. Diese Vorgehensweise wird zum Anpassen Ihrer eigenen SLES- und openSUSE-Images empfohlen.

Als Alternative zum Erstellen einer eigenen VHD veröffentlicht SUSE auf [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)auch BYOS-Images (Bring Your Own Subscription) für SLES.

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Vorbereiten von SUSE Linux Enterprise Server 11 SP4
1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.
2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.
3. Registrieren Sie Ihr SUSE Linux Enterprise-System, um das Herunterladen von Updates und das Installieren von Paketen zu ermöglichen.
4. Aktualisieren Sie das System mit den neuesten Patches:
   
        # sudo zypper update
5. Installieren Sie den Azure Linux Agent aus dem SLES-Repository:
   
        # sudo zypper install python-azure-agent
6. Prüfen Sie, ob waagent in "chkconfig" aktiviert ist. Ist dies nicht der Fall, legen Sie den Autostart fest:
   
        # sudo chkconfig waagent on
7. Überprüfen Sie, ob der waagent-Dienst ausgeführt wird. Ist dies nicht der Fall, starten Sie ihn: 
   
        # sudo service waagent start
8. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür „/boot/grub/menu.lst“ in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen.
9. Vergewissern Sie sich, dass "/boot/grub/menu.lst" und "/etc/fstab" mit der UUID (by-uuid) auf den Datenträger verweisen und nicht mit der Datenträger-ID (by-id). 
   
    Abrufen der Datenträger-UUID
   
        # ls /dev/disk/by-uuid/
   
    Bei Verwendung von "/dev/disk/by-id/" aktualisieren Sie "/boot/grub/menu.lst" und "/etc/fstab" mit dem ordnungsgemäßen "by-uuid"-Wert.
   
    Vor der Änderung
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Nach der Änderung
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Ändern Sie die udev-Regeln, um eine Generierung statischer Regeln für die Ethernet-Schnittstelle(n) zu vermeiden. Diese Regeln können beim Klonen eines virtuellen Computers unter Microsoft Azure oder Hyper-V zu Problemen führen:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Es wird empfohlen, die Datei "/etc/sysconfig/network/dhcp" zu bearbeiten und den Parameter `DHCLIENT_SET_HOSTNAME` wie folgt zu ändern:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Kommentieren Sie in „/etc/sudoers“ die folgenden Zeilen aus, sofern sie vorhanden sind, oder entfernen Sie sie:
    
     Defaults targetpw   # Kennwort des Zielbenutzers abfragen, also „root ALL“    ALL=(ALL) ALL   # WARNUNG! Verwenden Sie dies nur mit „Defaults targetpw“!
13. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.
14. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.
    
    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## HINWEIS: Legen Sie diesen Parameter auf einen belieben gewünschten Wert fest.
15. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

- - -
## <a name="prepare-opensuse-131"></a>Vorbereiten von openSUSE 13.1+
1. Wählen Sie den virtuellen Computer im mittleren Fensterbereich des Hyper-V-Managers.
2. Klicken Sie auf **Verbinden** , um das Fenster für den virtuellen Computer zu öffnen.
3. Führen Sie in der Shell den Befehl "`zypper lr`" aus. Wenn dieser Befehl den folgenden ähnelnde Ausgaben zurückgibt, sind Repositorys erwartungsgemäß konfiguriert, und es sind keine Anpassungen erforderlich (Versionsnummern können abweichen):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Wenn der Befehl „No repositories defined...“ zurückgibt, verwenden Sie die folgenden Befehle zum Hinzufügen dieser Repositorys:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Sie können dann überprüfen, ob die Repositorys hinzugefügt wurden, indem Sie den Befehl "`zypper lr`" erneut ausführen. Falls eines der relevanten Update-Repositorys nicht aktiviert ist, können Sie es mit dem folgenden Befehl aktivieren:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aktualisieren Sie den Kernel auf die neueste verfügbare Version:
   
        # sudo zypper up kernel-default
   
    Oder aktualisieren Sie das System mit allen neuen Patches:
   
        # sudo zypper update
5. Installieren des Azure Linux Agent:
   
        # sudo zypper install WALinuxAgent
6. Modifizieren Sie die Boot-Zeile des Kernels in Ihrer Grub-Konfiguration, um zusätzliche Kernel-Parameter für Azure einzubinden. Öffnen Sie dafür "/boot/grub/menu.lst" in einem Text-Editor. Stellen Sie sicher, dass der Standardkernel die folgenden Parameter enthält:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Dadurch wird sichergestellt, dass alle Konsolennachrichten zum ersten seriellen Port gesendet werden. Dieser kann Azure bei der Behebung von Fehlern unterstützen. Entfernen Sie außerdem die folgenden Parameter aus der Kernel-Boot-Zeile, sofern diese vorhanden sind:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Es wird empfohlen, die Datei "/etc/sysconfig/network/dhcp" zu bearbeiten und den Parameter `DHCLIENT_SET_HOSTNAME` wie folgt zu ändern:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Wichtig:** Kommentieren Sie in „/etc/sudoers“ die folgenden Zeilen aus, sofern sie vorhanden sind, oder entfernen Sie sie:
   
     Defaults targetpw   # Kennwort des Zielbenutzers abfragen, also „root ALL“    ALL=(ALL) ALL   # WARNUNG! Verwenden Sie dies nur mit „Defaults targetpw“!
9. Stellen Sie sicher, dass der SSH-Server installiert und konfiguriert ist, damit er beim Booten hochfährt.  Dies ist für gewöhnlich die Standardeinstellung.
10. Richten Sie keinen SWAP-Raum auf dem BS-Datenträger ein.
    
    Der Azure Linux Agent kann SWAP-Raum automatisch mit dem lokalen Ressourcendatenträger konfigurieren, der nach der Bereitstellung in Azure mit dem virtuellen Computer verknüpft ist. Beachten Sie, dass der lokale Ressourcendatenträger ein *temporärer* Datenträger ist und geleert werden kann, wenn die Bereitstellung des virtuellen Computers aufgehoben wird. Modifizieren Sie nach dem Installieren des Azure Linux Agent (siehe vorheriger Schritt) die folgenden Parameter in /etc/waagent.conf entsprechend:
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## HINWEIS: Legen Sie diesen Parameter auf einen belieben gewünschten Wert fest.
11. Führen Sie die folgenden Befehle aus, um den virtuellen Computer zurückzusetzen und ihn für die Bereitstellung in Azure vorzubereiten:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Stellen Sie sicher, dass der Azure Linux Agent beim Start ausgeführt wird:
    
        # sudo systemctl enable waagent.service
13. Klicken Sie im Hyper-V-Manager auf **Aktion > Herunterfahren**. Ihre Linux-VHD kann nun in Azure hochgeladen werden.

## <a name="next-steps"></a>Nächste Schritte
Sie können jetzt mit Ihrer SUSE-Linux-VHD-Datei neue virtuelle Azure-Computer in Azure erstellen. Wenn Sie zum ersten Mal die VHD-Datei in Azure hochladen, lesen Sie den Artikel [Erstellen eines virtuellen Linux-Computers aus einem benutzerdefinierten Datenträger mithilfe der Azure CLI 2.0](upload-vhd.md#option-1-upload-a-vhd).
