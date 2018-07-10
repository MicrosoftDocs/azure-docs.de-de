---
title: Kopieren oder Verschieben von Daten in Azure Storage mit AzCopy unter Linux | Microsoft-Dokumentation
description: Verwenden Sie das Hilfsprogramm AzCopy unter Linux zum Verschieben oder Kopieren von Daten zu oder von Blob- und Dateiinhalten. Kopieren Sie Daten aus lokalen Dateien nach Azure Storage oder innerhalb von bzw. zwischen Speicherkonten. Migrieren Sie Ihre Daten ganz einfach nach Azure Storage.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: seguler
ms.openlocfilehash: 80b112de1fd8417dd64d9d95b7a037ec876d18c7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2018
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Übertragen von Daten mit AzCopy unter Linux

AzCopy ist ein Befehlszeilenprogramm, das entwickelt wurde, um Daten in/aus Microsoft Azure Blob und File Storage zu kopieren. Hierbei werden einfache Befehle verwendet, die für optimale Leistung konzipiert sind. Sie können Daten zwischen einem Dateisystem und einem Speicherkonto oder zwischen Speicherkonten kopieren.  

Es gibt zwei Versionen von AzCopy, die Sie herunterladen können. AzCopy unter Linux ist auf Linux-Plattformen ausgerichtet, die Befehlszeilenoptionen im POSIX-Format bieten. [AzCopy unter Windows](../storage-use-azcopy.md) bietet Befehlszeilenoptionen im Windows-Format. Dieser Artikel behandelt AzCopy unter Linux. 

> [!NOTE]  
> Ab Version 7.2. von AzCopy werden die .NET Core-Abhängigkeiten im Paket mit AzCopy bereitgestellt. Wenn Sie Version 7.2 oder höher verwenden, ist die Installation von .NET Core nicht mehr erforderlich.

## <a name="download-and-install-azcopy"></a>Herunterladen und Installieren von AzCopy

### <a name="installation-on-linux"></a>Installation unter Linux

> [!NOTE]
> Abhängig von Ihrer Distribution müssen Sie unter Umständen .NET Core 2.1-Abhängigkeiten installieren. Diese sind im Artikel [Voraussetzungen für .NET Core unter Linux](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) aufgeführt. Für gängige Distributionen wie Ubuntu 16.04 und RHEL 7 ist dies in der Regel nicht erforderlich.

Die Installation von AzCopy unter Linux (v7.2 oder höher) ist ganz einfach: Extrahieren Sie einfach das TAR-Paket, und führen Sie das Installationsskript aus. 

**RHEL 6-basierte Distributionen**: [Downloadlink](https://aka.ms/downloadazcopylinuxrhel6)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinuxrhel6
tar -xf azcopy.tar.gz
sudo ./install.sh
```

**Alle anderen Linux-Distributionen**: [Downloadlink](https://aka.ms/downloadazcopylinux64)
```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Sie können die extrahierten Dateien entfernt, nachdem AzCopy unter Linux installiert ist. Wenn Sie nicht über Administratorrechte verfügen, können Sie `azcopy` auch mit dem Shellskript „azcopy“ im extrahierten Ordner ausführen.

### <a name="alternative-installation-on-ubuntu"></a>Alternative Installation unter Ubuntu

**Ubuntu 14.04**

Apt-Quelle für das Microsoft-Produktrepository für Linux hinzufügen und AzCopy installieren:

```bash
sudo echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod/ trusty main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

**Ubuntu 16.04**

Apt-Quelle für das Microsoft-Produktrepository für Linux hinzufügen und AzCopy installieren:

```bash
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod/ xenial main" > azure.list
sudo cp ./azure.list /etc/apt/sources.list.d/
apt-key adv --keyserver packages.microsoft.com --recv-keys EB3E94ADBE1229CF
```

```bash
sudo apt-get update
sudo apt-get install azcopy
```

## <a name="writing-your-first-azcopy-command"></a>Schreiben Ihres ersten AzCopy-Befehls
Die grundlegende Syntax für AzCopy-Befehle ist:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Die folgenden Beispiele zeigen eine Vielzahl von Szenarios zum Kopieren von Daten zu und aus Microsoft Azure Blobs und Dateien. Für eine detailliertere Erklärung der in jedem Beispiel verwendeten Parameter, wechseln Sie zum `azcopy --help`-Menü.

## <a name="blob-download"></a>Blob: Herunterladen
### <a name="download-single-blob"></a>Herunterladen eines einzelnen Blobs

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Wenn der Ordner `/mnt/myfiles` nicht vorhanden ist, erstellt AzCopy ihn und lädt `abc.txt ` in den neuen Ordner herunter. 

### <a name="download-single-blob-from-secondary-region"></a>Ein einzelnes Blob aus der sekundären Region herunterladen

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Beachten Sie, dass der Lesezugriff im georedundanten Speicher aktiviert sein muss.

### <a name="download-all-blobs"></a>Herunterladen aller Blobs

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Nehmen Sie an, die folgenden BLOBs befinden sich im angegebenen Container:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Nach dem Herunterladen enthält das Verzeichnis `/mnt/myfiles` die folgenden Dateien:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Wenn Sie die Option `--recursive` nicht angeben, werden keine Blobs heruntergeladen.

### <a name="download-blobs-with-specified-prefix"></a>Herunterladen von Blobs mit angegebenem Präfix

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Nehmen Sie an, die folgenden BLOBs befinden sich im angegebenen Container. Alle Blobs, die mit dem Präfix `a` beginnen, werden heruntergeladen.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Nach dem Herunterladen enthält der Ordner `/mnt/myfiles` die folgenden Dateien:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

Das Präfix gilt für das virtuelle Verzeichnis, das den ersten Teil des Blobnamens bildet. Im obigen Beispiel stimmt das virtuelle Verzeichnis nicht mit dem angegebenen Präfix überein. Daher wird kein Blob heruntergeladen. Darüber hinaus lädt AzCopy keine Blobs herunter, wenn die Option `--recursive` nicht angegeben ist.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Für exportierte Dateien und Quell-Blobs dieselbe Uhrzeit der letzten Änderung festlegen

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

Sie haben auch die Möglichkeit, Blobs auf Basis der Uhrzeit ihrer letzten Änderung vom Herunterladen auszuschließen. Fügen Sie die Option `--exclude-newer` hinzu, falls Sie beispielsweise Blobs, deren Uhrzeit der letzten Änderung die gleiche oder eine spätere ist als die der Zieldatei, ausschließen wollen:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

Fügen Sie die Option `--exclude-older` hinzu, falls Sie Blobs, deren Uhrzeit der letzten Änderung die gleiche oder eine frühere ist als die der Zieldatei, ausschließen wollen:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: Hochladen
### <a name="upload-single-file"></a>Hochladen einer einzelnen Datei

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Wenn der angegebene Zielcontainer nicht vorhanden ist, wird er von AzCopy erstellt und die Datei anschließend in den Container hochgeladen.

### <a name="upload-single-file-to-virtual-directory"></a>Hochladen einer einzelnen Datei in ein virtuelles Verzeichnis

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Wenn das angegebene virtuelle Verzeichnis nicht vorhanden ist, lädt AzCopy die Datei so hoch, dass das virtuelle Verzeichnis Teil des Blobnamens wird (*z.B.*, `vd/abc.txt` im obigen Beispiel).

### <a name="redirect-from-stdin"></a>Umleitung von STDIN

```azcopy
gzip myarchive.tar -c | azcopy \
    --destination https://myaccount.blob.core.windows.net/mycontainer/mydir/myarchive.tar.gz \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Hochladen aller Dateien

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Wenn Sie die Option `--recursive` angeben, werden die Inhalte des angegebenen Verzeichnisses rekursiv in den Blobspeicher hochgeladen. Das bedeutet, dass alle Unterordner und die darin enthaltenen Dateien ebenfalls hochgeladen werden. Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Nach dem Hochladen enthält der Container die folgenden Dateien:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Wenn die Option `--recursive` nicht angegeben ist, werden nur die folgenden drei Dateien hochgeladen:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Hochladen von Dateien, die einem angegebenen Muster entsprechen

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Nehmen Sie z. B. an, die folgenden Dateien befinden sich im Ordner `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Nach dem Hochladen enthält der Container die folgenden Dateien:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Wenn die Option `--recursive` nicht angegeben ist, überspringt AzCopy die Dateien, die in den Unterverzeichnissen sind:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Angeben des MIME-Inhaltstyps für Ziel-Blobs
AzCopy legt den Inhaltstyp eines Zielblobs standardmäßig als `application/octet-stream`fest. Sie können den Inhaltstyp jedoch mit der Option `--set-content-type [content-type]` explizit angeben. Mit dieser Syntax wird der Inhaltstyp für alle Blobs in einem Hochladevorgang festgelegt.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Wenn Sie für die Option `--set-content-type` keinen Wert angeben, legt AzCopy den Inhaltstyp jedes Blobs und jeder Datei gemäß der jeweiligen Dateierweiterung fest.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Blob: Kopieren
### <a name="copy-single-blob-within-storage-account"></a>Kopieren eines einzelnen Blobs innerhalb eines Azure Storage-Kontos

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Wenn Sie einen Blob ohne die Option „--sync-copy“ kopieren, wird ein [serverseitiger Kopiervorgang](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) ausgeführt.

### <a name="copy-single-blob-across-storage-accounts"></a>Kopieren von Blobs zwischen Azure Storage-Konten

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Wenn Sie einen Blob ohne die Option „--sync-copy“ kopieren, wird ein [serverseitiger Kopiervorgang](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) ausgeführt.

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Kopieren eines einzelnen Blobs aus der sekundären Region in die primäre Region

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Beachten Sie, dass der Lesezugriff im georedundanten Speicher aktiviert sein muss.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Kopieren eines einzelnen Blobs und seiner Momentaufnahmen zwischen Storage-Konten

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Nach dem Kopiervorgang enthält der Zielcontainer den Blob und seine Momentaufnahmen. Der Container enthält den folgenden Blob und seine Momentaufnahmen:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Synchrones Kopieren von Blobs zwischen Storage-Konten
Standardmäßig kopiert AzCopy Daten zwischen zwei Speicherendpunkten auf asynchrone Weise. Aus diesem Grund wird der Kopiervorgang im Hintergrund ausgeführt, indem wenig Bandbreitenkapazität verwendet wird, die keine SLA im Hinblick darauf hat, wie schnell ein Blob kopiert wird. 

Die Option `--sync-copy` gewährleistet, dass der jeweilige Kopiervorgang mit gleichmäßiger Geschwindigkeit erfolgt. Beim synchronen Kopieren lädt AzCopy die zu kopierenden Blobs aus der angegebenen Quelle in den lokalen Arbeitsspeicher herunter und dann in das Speicherziel des jeweiligen Blobs hoch.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` können zu zusätzlichen Ausgangskosten im Vergleich zu asynchronen Kopien führen. Zum Vermeiden von Ausgangskosten empfiehlt sich die Verwendung dieser Option auf einem virtuellen Azure-Computer, der sich in derselben Region wie das Quellspeicherkonto befindet.

## <a name="file-download"></a>Datei: Herunterladen
### <a name="download-single-file"></a>Herunterladen einer einzelnen Datei

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Wenn es sich bei der angegebenen Quelle um eine Azure-Dateifreigabe handelt, müssen Sie entweder den genauen Dateinamen angeben (*z.B.* `abc.txt`), um eine einzelne Datei herunterzuladen, oder die Option `--recursive`, um alle Dateien in der Freigabe rekursiv herunterzuladen. Wenn Sie sowohl ein Dateimuster als auch die Option `--recursive` angeben, führt dies zu einem Fehler.

### <a name="download-all-files"></a>Herunterladen aller Dateien

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Beachten Sie, dass alle leeren Ordner nicht heruntergeladen werden.

## <a name="file-upload"></a>Datei: Hochladen
### <a name="upload-single-file"></a>Hochladen einer einzelnen Datei

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Hochladen aller Dateien

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Beachten Sie, dass alle leeren Ordner nicht hochgeladen werden.

### <a name="upload-files-matching-specified-pattern"></a>Hochladen von Dateien, die einem angegebenen Muster entsprechen

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Datei: Kopieren
### <a name="copy-across-file-shares"></a>Kopieren zwischen Dateifreigaben

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Wenn Sie eine Datei zwischen Dateifreigaben kopieren, wird ein [serverseitiger Kopiervorgang](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) ausgeführt.

### <a name="copy-from-file-share-to-blob"></a>Kopieren aus einer Dateifreigabe zum Blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Wenn Sie eine Datei von einer Dateifreigabe in ein Blob kopieren, wird ein [serverseitiger Kopiervorgang](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) ausgeführt.

### <a name="copy-from-blob-to-file-share"></a>Kopieren vom Blob zur Dateifreigabe

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Wenn Sie eine Datei aus einem Blob in eine Dateifreigabe kopieren, wird ein [serverseitiger Kopiervorgang](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) ausgeführt.

### <a name="synchronously-copy-files"></a>Synchrones Kopieren von Dateien
Sie können die Option `--sync-copy` angeben, um Daten synchron aus File Storage zu File Storage, aus File Storage zu Blob Storage oder aus Blob Storage zu File Storage zu kopieren. AzCopy führt diesen Vorgang aus, indem die Quelldaten in den lokalen Speicher heruntergeladen und dann in das Ziel hochgeladen werden. In diesem Fall finden Standardausgangskosten Anwendung.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Der Standardblobtyp beim Kopieren aus File Storage in einen Blob Storage ist Blockblob. Benutzer können die Option `--blob-type page` angeben, um den Zielblobtyp zu ändern. Die Typen `page | block | append` sind verfügbar.

Beachten Sie, dass `--sync-copy` zu zusätzlichen Ausgangskosten im Vergleich zu asynchronen Kopien führen kann. Zum Vermeiden von Ausgangskosten empfiehlt sich die Verwendung dieser Option auf einem Azure-VM, der sich in derselben Region wie das Quellspeicherkonto befindet.

## <a name="other-azcopy-features"></a>Andere AzCopy-Funktionen
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Nur Daten kopieren, die im Ziel nicht vorhanden sind.
Die Parameter `--exclude-older` und `--exclude-newer` erlauben Ihnen, jeweils ältere oder neuere Quellressourcen vom Kopieren auszuschließen. Falls Sie nur Quellressourcen kopieren wollen, die im Ziel nicht existieren, können Sie beide Parameter im AzCopy-Befehl angeben.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Verwenden Sie eine Konfigurationsdatei, um das Befehlszeilenparameter anzugeben

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Sie können beliebige AzCopy-Befehlszeilenparameter in eine Konfigurationsdatei einfügen. AzCopy verarbeitet die Parameter in der Datei ebenso wie bei der Angabe über die Befehlszeile, wobei mit den Inhalten der Datei ein direkter Austausch erfolgt.

Nehmen wir an, eine Konfigurationsdatei namens `copyoperation` enthält die folgenden Zeilen. Jeder AzCopy-Parameter kann in einer einzelnen Zeile angegeben werden.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

oder in verschiedenen Zeilen:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

Wenn Sie den Parameter auf zwei Zeilen verteilen, tritt bei der Ausführung von AzCopy ein Fehler auf, wie hier für den Parameter `--source-key` dargestellt:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Angeben einer Shared Access Signature (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

Sie können auch eine SAS in der Container-URI angeben:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Journaldateiordner
Sobald ein Befehl für AzCopy ausgeführt wird, prüft es, ob eine Journaldatei im Standardordner oder in einem Ordner vorhanden ist, den Sie über diese Option angegeben haben. Wenn die Journaldatei in keinem dieser Ordner vorhanden ist, behandelt AzCopy diesen Vorgang als neuen Vorgang und generiert somit eine neue Journaldatei.

Wenn die Journaldatei vorhanden ist, prüft AzCopy, ob die von Ihnen eingegebene Befehlszeile mit der Befehlszeile in der Journaldatei übereinstimmt. Wenn die beiden Befehlszeilen übereinstimmen, setzt AzCopy den unvollständigen Vorgang fort. Wenn sie nicht übereinstimmen, wird der Benutzer aufgefordert, entweder die Journaldatei zu überschreiben, um einen neuen Vorgang zu starten, oder den aktuellen Vorgang abzubrechen.

Falls Sie den Standardspeicherort für die Journaldatei verwenden wollen:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Wenn Sie die Option `--resume` auslassen oder die Option `--resume` ohne den Ordnerpfad angeben (wie oben gezeigt), dann erstellt AzCopy die Journaldatei am Standardspeicherort `~\Microsoft\Azure\AzCopy`. Wenn die Journaldatei bereits vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

Falls Sie einen benutzerdefinierten Speicherort für die Journaldatei angeben wollen:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

In diesem Beispiel wird die Journaldatei erstellt, sofern diese nicht bereits vorhanden ist. Wenn sie vorhanden ist, setzt AzCopy den Vorgang auf Basis der Journaldatei fort.

Falls Sie einen AzCopy-Vorgang fortsetzen möchten, wiederholen Sie den gleichen Befehl. AzCopy unter Linux wird Sie dann zur Bestätigung auffordern:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Ausführliche Ausgabeprotokolle

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Angeben der Anzahl der zu startenden gleichzeitigen Vorgängen
Die Option `--parallel-level` gibt die Anzahl der gleichzeitigen Kopiervorgänge an. AzCopy startet standardmäßig eine bestimmte Anzahl gleichzeitiger Vorgänge zum Erhöhen des Datenübertragungsdurchsatzes. Die Anzahl gleichzeitiger Vorgänge entspricht der Anzahl der verfügbaren Prozessoren achtmal. Wenn Sie AzCopy in einem Netzwerk mit geringer Bandbreite ausführen, können Sie eine niedrigere Anzahl für --parallel-level angeben, um Fehler durch gleichzeitig verwendete Ressourcen zu vermeiden.

>[!TIP]
>Um die vollständige Liste der AzCopy-Parameter angezeigt zu bekommen, sehen Sie sich das Menü „azcopy --help“ an.

## <a name="installation-steps-for-azcopy-71-and-earlier-versions"></a>Installationsschritte für AzCopy 7.1 und ältere Versionen

AzCopy unter Linux (nur v7.1 und ältere Versionen) erfordert das .NET Core-Framework. Installationsanweisungen finden Sie auf der Seite zur [.NET Core-Installation](https://www.microsoft.com/net/core#linuxubuntu).

Beginnen Sie beispielsweise mit der Installation von .NET Core unter Ubuntu 16.10. Das neueste Installationshandbuch finden Sie auf der [.NET Core unter Linux](https://www.microsoft.com/net/core#linuxubuntu)-Installationsseite.


```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ yakkety main" > /etc/apt/sources.list.d/dotnetdev.list' 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.0
```

Nach der Installation von .NET Core laden Sie AzCopy herunter und installieren es.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Sie können die extrahierten Dateien entfernt, nachdem AzCopy unter Linux installiert ist. Wenn Sie nicht über Administratorrechte verfügen, können Sie `azcopy` auch mit dem Shellskript „azcopy“ im extrahierten Ordner ausführen.

## <a name="known-issues-and-best-practices"></a>Bekannte Probleme und bewährte Methoden
### <a name="error-installing-azcopy"></a>Fehler bei der Installation von AzCopy
Treten Probleme bei der Installation von AzCopy auf, können Sie versuchen AzCopy mithilfe des Bash-Skripts im extrahierten `azcopy`-Ordner auszuführen.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Einschränken gleichzeitiger Schreibvorgänge beim Kopieren von Daten
Wenn Sie BLOBs oder Dateien mit AzCopy kopieren, denken Sie daran, dass eine andere Anwendung die Daten möglicherweise modifiziert, während Sie diese kopieren. Stellen Sie nach Möglichkeit sicher, dass die von Ihnen kopierten Daten während des Kopiervorgangs nicht verändert werden. Wenn Sie z. B. eine virtuelle Festplatte (Virtual Hard Disk, VHD) mit einem virtuellen Azure-Computer kopieren, stellen Sie sicher, dass derzeit keine anderen Anwendungen auf diese virtuelle Festplatte schreiben. Eine gute Möglichkeit hierfür bietet das Leasen der Ressource, die kopiert werden soll. Alternativ können Sie zunächst eine Momentaufnahme der virtuellen Festplatte (VHD) erstellen und anschließend die Momentaufnahme kopieren.

Wenn Sie andere Anwendungen nicht daran hindern können, während des Kopiervorgangs in BLOBs oder Dateien zu schreiben, beachten Sie, dass die kopierten Ressourcen bei der Beendigung des Auftrags möglicherweise nicht mehr vollständig mit den Quellressourcen übereinstimmen.

### <a name="running-multiple-azcopy-processes"></a>Ausführen mehrerer AzCopy-Prozesse
Sie können mehrere AzCopy-Prozesse auf einem einzelnen Client ausführen. Dazu müssen jedoch verschiedene Journalordner verwendet werden. Die Verwendung eines einzelnen Journalordners für mehrere AzCopy-Prozesse wird nicht unterstützt.

1. Prozess:
```azcopy
azcopy \
    --source /mnt/myfiles1 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles1 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal1"
```

2. Prozess:
```azcopy
azcopy \
    --source /mnt/myfiles2 \
    --destination https://myaccount.blob.core.windows.net/mycontainer/myfiles2 \
    --dest-key <key> \
    --resume "/mnt/myazcopyjournal2"
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Storage und zu AzCopy finden Sie in den folgenden Ressourcen:

### <a name="azure-storage-documentation"></a>Azure Storage-Dokumentation:
* [Einführung in Azure Storage](../storage-introduction.md)
* [Erstellen Sie ein Speicherkonto](../storage-create-storage-account.md)
* [Verwalten von Blobs mit dem Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Verwenden der Azure CLI 2.0 mit Azure Storage](../storage-azure-cli.md)
* [Verwenden des BLOB-Speichers mit C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Gewusst wie: Verwenden von Blob Storage mit Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Gewusst wie: Verwenden von Blob Storage mit Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Gewusst wie: Verwenden von Blob Storage mit Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Azure Storage-Blogbeiträge:
* [Announcing AzCopy on Linux Preview (Ankündigung von AzCopy unter der Linux-Vorschau)](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Einführung in die Vorschau der Microsoft Azure Storage Data Movement-Bibliothek](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Einführung in das synchrone Kopieren und benutzerdefinierte Inhaltstypen](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Ab sofort allgemein verfügbar: AzCopy 3.0 sowie die Vorschau auf AzCopy 4.0 mit Tabellen- und Dateiunterstützung](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimiert für große Kopierszenarien](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Unterstützung des Lesezugriffs auf georedundanten Speicher](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transfer data with restartable mode and SAS token (AzCopy: Übertragen von Daten mit neu startbarem Modus und SAS-Token)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Verwenden des kontoübergreifenden Kopierblobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Hochladen/Herunterladen von Dateien für Microsoft Azure-Blobs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

