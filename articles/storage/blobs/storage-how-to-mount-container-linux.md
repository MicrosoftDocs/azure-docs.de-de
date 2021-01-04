---
title: Einbinden von Azure Blob Storage als Dateisystem unter Linux | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure Blob Storage-Container mit blobfuse, einem virtuellen Dateisystemtreiber unter Linux, einbinden.
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: 8de395e34b43a4edad2affa591adb8ab34ff9e66
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96921698"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Einbinden von Blob Storage als Dateisystem mit blobfuse

## <a name="overview"></a>Übersicht
[Blobfuse](https://github.com/Azure/azure-storage-fuse) ist ein virtueller Dateisystemtreiber für Azure Blob Storage. Sie können Blobfuse verwenden, um über das Linux-Dateisystem auf die vorhandenen Blockblobdaten in Ihrem Speicherkonto zuzugreifen. Blobfuse verwendet das Schema für virtuelle Verzeichnisse mit einem Schrägstrich (/) als Trennzeichen.  

In diesem Handbuch wird gezeigt, wie Sie blobfuse verwenden, einen Blobspeichercontainer unter Linux einbinden und auf Daten zugreifen. Weitere Informationen zu blobfuse finden Sie im [blobfuse-Repository](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse garantiert keine 100-prozentige Konformität mit POSIX, da Anforderungen einfach in [Blob-REST-APIs](/rest/api/storageservices/blob-service-rest-api) übersetzt werden. Umbenennungsvorgänge sind in POSIX beispielsweise atomisch, in blobfuse jedoch nicht.
> Eine vollständige Liste der Unterschiede zwischen einem nativen Dateisystem und blobfuse finden Sie im [blobfuse-Quellcoderepository](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installieren von blobfuse unter Linux
blobfuse-Binärdateien befinden sich für Ubuntu- und RHEL-Distributionen in den [Microsoft-Softwarerepositorys für Linux](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software). Um Blobfuse in diesen Distributionen zu installieren, konfigurieren Sie eines der Repositorys aus der Liste. Sie können die Binärdateien auch gemäß den [Azure Storage-Installationsschritten](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) aus dem Quellcode erstellen, wenn für Ihre Distribution keine Binärdateien verfügbar sind.

Blobfuse unterstützt die Installation unter Ubuntu 14.04, 16.04, 18.04 und 20.04. Führen Sie diesen Befehl aus, um sicherzustellen, dass Sie eine dieser Versionen bereitgestellt haben:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Konfigurieren des Microsoft-Paketrepositorys
Konfigurieren Sie das [Linux-Paketrepository für Microsoft-Produkte](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Beispiel einer Konfiguration für eine Enterprise Linux 6-Distribution:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Analog dazu ändern Sie die URL in `.../rhel/7/...`, um auf eine Enterprise Linux 7-Distribution zu verweisen.

Ein weiteres Beispiel für eine Konfiguration für eine Ubuntu 14.04-Distribution:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Ändern Sie auf ähnliche Weise die URL in `.../ubuntu/16.04/...` oder `.../ubuntu/18.04/...`, um auf eine andere Ubuntu-Version zu verweisen.

### <a name="install-blobfuse"></a>Installieren von blobfuse

In einer Ubuntu/Debian-Distribution:
```bash
sudo apt-get install blobfuse
```

In einer Enterprise Linux-Distribution:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Vorbereiten der Einbindung
Blobfuse erfordert einen temporären Pfad im Dateisystem zum Puffern und Zwischenspeichern geöffneter Dateien, wodurch nahezu die gleiche Leistung wie bei einer nativen Lösung erreicht wird. Wählen Sie für diesen temporären Pfad den leistungsstärksten Datenträger aus, oder verwenden Sie eine RAM-Disk, um eine optimale Leistung zu erzielen. 

> [!NOTE]
> Blobfuse speichert die Inhalte aller geöffneten Dateien am temporären Pfad. Stellen Sie sicher, dass ausreichend Speicher für alle geöffneten Dateien verfügbar ist. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Optional) Verwenden einer RAM-Disk für den temporären Pfad
Das folgende Beispiel erstellt eine RAM-Disk mit 16 GB sowie ein Verzeichnis für Blobfuse. Wählen Sie basierend auf Ihren Anforderungen die Größe aus. Diese RAM-Disk ermöglicht blobfuse das Öffnen von Dateien mit einer Größe von bis zu 16 GB. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Verwenden eines SSD-Datenträgers als temporärer Pfad
In Azure können Sie die auf Ihren virtuellen Computern verfügbaren kurzlebigen Datenträger (SSD) nutzen, um für blobfuse einen Puffer mit geringer Latenz bereitzustellen. In Ubuntu-Distributionen wird dieser kurzlebige Datenträger mit „/mnt“ bereitgestellt. In Red Hat- und CentOS-Distributionen wird der Datenträger mit „mnt/resource/“ bereitgestellt.

Stellen Sie sicher, dass Benutzer auf den temporären Pfad zugreifen können:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Konfigurieren der Anmeldeinformationen für Speicherkonten
blobfuse erfordert, dass Ihre Anmeldeinformationen in einer Textdatei in folgendem Format gespeichert werden: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName` ist das Präfix für Ihr Speicherkonto – nicht die vollständige URL.

Erstellen Sie diese Datei mit:

```
touch ~/fuse_connection.cfg
```

Nachdem Sie diese Datei erstellt und bearbeitet haben, schränken Sie unbedingt den Zugriff darauf ein, damit andere Benutzer sie nicht lesen können.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Wenn Sie die Konfigurationsdatei unter Windows erstellt haben, stellen Sie sicher, dass Sie `dos2unix` ausführen, um die Datei zu bereinigen und in das Unix-Format zu konvertieren. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Erstellen eines leeren Verzeichnisses für die Einbindung
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Einbinden

> [!NOTE]
> Eine vollständige Liste der Einbindungsoptionen finden Sie im [blobfuse-Repository](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Führen Sie zum Einbinden von Blobfuse den folgenden Befehl für den Benutzer aus. Dieser Befehl bindet den unter „/path/to/fuse_connection.cfg“ angegebenen Container am Speicherort „/mycontainer“ ein.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Sie sollten jetzt über die regulären Dateisystem-APIs auf Ihre Blockblobs zugreifen können. Der Benutzer, der das Verzeichnis bereitstellt, ist die einzige Person, die standardmäßig darauf zugreifen kann, sodass der Zugriff gesichert ist. Um den Zugriff für alle Benutzer zuzulassen, können Sie für die Einbindung die Option ```-o allow_other``` verwenden. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Nächste Schritte

* [blobfuse-Homepage](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Melden von Problemen mit blobfuse](https://github.com/Azure/azure-storage-fuse/issues)
