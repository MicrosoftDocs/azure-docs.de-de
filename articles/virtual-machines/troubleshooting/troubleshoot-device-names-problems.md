---
title: Behandeln von Problemen mit geänderten Gerätenamen von Linux-VMs in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, warum sich Gerätenamen von Linux-VMs ändern und wie das Problem behoben wird.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d636d5f31e78828a518882091af29b25f7219304
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362236"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Behandeln von Problemen mit geänderten Gerätenamen von Linux-VMs

In diesem Artikel wird erläutert, weshalb sich Gerätenamen nach dem Neustart einer Linux-VM oder nach dem erneuten Anfügen der Datenträger ändern. In dem Artikel werden auch Lösungen für dieses Problem vorgestellt.

## <a name="symptoms"></a>Symptome
Wenn Sie in Microsoft Azure Linux-VMs verwenden, können die folgenden Probleme auftreten:

- Der virtuelle Computer startet nach einem Neustart nicht.
- Wenn Datenträger getrennt und wieder angefügt werden, werden deren Gerätenamen geändert.
- Anwendungen oder Skripts, die über den Gerätenamen auf einen Datenträger verweisen, lösen einen Fehler aus, da sich der Gerätename geändert hat.

## <a name="cause"></a>Ursache

In Linux ist nicht garantiert, dass die Gerätepfade nach einem Neustart konsistent bleiben. Die Gerätenamen setzen sich aus Major- (Buchstaben) und Minor-Nummern zusammen. Wenn der Linux-Speichergerätetreiber ein neues Gerät erkennt, weist der Treiber dem Gerät aus dem verfügbaren Bereich Major- und Minor-Nummern zu. Wenn ein Gerät entfernt wird, werden die Gerätenummern zur späteren Verwendung freigegeben.

Das Problem tritt auf, weil die vom SCSI-Subsystem geplante Geräteüberprüfung in Linux asynchron erfolgt. Daher kann der Pfadname eines Geräts nach jedem Neustart variieren.

## <a name="solution"></a>Lösung

Um dieses Problem zu beheben, können Sie die persistente Benennung verwenden. Für die persistente Benennung stehen vier Methoden zur Verfügung: nach der Dateisystembezeichnung, nach der UUID, nach der ID oder nach dem Pfad. Für Azure-Linux-VMs wird die Verwendung der Dateisystembezeichnung oder UUID empfohlen.

Die meisten Verteilungen stellen die Parameter `fstab` **nofail** oder **nobootwait** bereit. Mit diesen Parametern kann ein System neu gestartet werden, wenn der Datenträger beim Start nicht bereitgestellt werden kann. Weitere Informationen zu diesen Parametern finden Sie in der Dokumentation zu Ihrer Distribution. Informationen zum Konfigurieren einer Linux-VM für die Verwendung einer UUID beim Hinzufügen eines Datenträgers finden Sie unter [Herstellen einer Verbindung mit dem virtuellen Linux-Computer zum Bereitstellen des neuen Datenträgers](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Beim Installieren des Azure Linux-Agents auf einer VM werden für die Erstellung einer Reihe symbolischer Verknüpfungen unter dem Pfad „/dev/disk/azure“ udev-Regeln verwendet. Anwendungen und Skripts ermitteln mithilfe von udev-Regeln Datenträger, die an die VM angefügt sind, sowie den Datenträgertyp und die Datenträger-LUNs.

Wenn Sie Ihr fstab bereits so bearbeitet haben, dass Ihr virtueller Computer nicht startet und Sie nicht in der Lage sind, SSH für Ihre VM zu verwenden, können Sie die [serielle VM-Konsole](./serial-console-linux.md) verwenden, um in den [Einzelbenutzermodus](./serial-console-grub-single-user-mode.md) zu gelangen und fstab zu ändern.

### <a name="identify-disk-luns"></a>Identifizieren der logischen Datenträgernummern

Anwendungen verwenden LUNs, um alle angefügten Datenträger zu ermitteln und symbolische Verknüpfungen zu erstellen. Der Azure Linux-Agent enthält udev-Regeln, mit denen symbolische Verknüpfungen zwischen einer LUN und den Geräten erstellt werden:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

LUN-Informationen werden mit `lsscsi` oder einem ähnlichen Tool vom Linux-Gastkonto abgerufen:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Die Informationen zur Gast-LUN dienen in Kombination mit Azure-Abonnementmetadaten dazu, die VHD in Azure Storage mit den Partitionsdaten zu suchen. So können Sie die `az`-CLI z.B. für Folgendes verwenden:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Erkunden des Dateisystem-UUIDs mit „blkid“

Die Ausgabe von `blkid` oder ähnlichen Informationsquellen können mit Anwendungen oder Skripts gelesen oder unter dem Pfad „/dev“ symbolische Verknüpfungen erstellen. Die Ausgabe zeigt die UUIDs aller Datenträger an, die an die VM und die zugehörige Gerätedatei angefügt sind:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Die udev-Regeln des Azure Linux-Agents erstellen unter dem Pfad „/dev/disk/azure“ eine Reihe symbolischer Verknüpfungen:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Anwendungen nutzen die Verknüpfungen dazu, den Startdatenträger und den (kurzlebigen) Ressourcendatenträger zu ermitteln. In Azure sollten die Anwendungen diese Partitionen unter den Pfaden „/dev/disk/azure/root-part1“ oder „/dev/disk/azure-resource-part1“ ermitteln.

Zusätzliche Partitionen aus der `blkid`-Liste befinden sich auf einem Datenträger. Um den Gerätenamen zur Laufzeit zu ermitteln, erhalten Anwendungen die UUID für diese Partitionen und verwenden einen Pfad:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>Abrufen der neuesten Azure Storage-Regeln

Um die neuesten Azure Storage-Regeln abzurufen, führen Sie die folgenden Befehle aus:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Ubuntu: Using UUID (Ubuntu: Verwenden der UUID)](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Persistent Naming (Red Hat: persistente Benennung)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- [Linux: What UUIDs can do for you (Linux: wie UUIDs Ihnen helfen)](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev: Introduction to Device Management In Modern Linux System (udev: Einführung in die Geräteverwaltung in modernen Linux-Systemen)](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

