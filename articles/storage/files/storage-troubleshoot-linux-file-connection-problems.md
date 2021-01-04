---
title: Beheben von Problemen mit Azure Files unter Linux | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Behandlung von Problemen mit Azure Files unter Linux. Dabei wird auf häufig auftretende Probleme mit Azure Files beim Herstellen einer Verbindung mit Linux-Clients eingegangen, und mögliche Lösungen werden beschrieben.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: e680ba10c507ef83591b56652ee8e95c4d665dda
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492062"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>Behandeln von Azure Files-Problemen unter Linux (SMB)

Dieser Artikel beschreibt allgemeine Probleme im Zusammenhang mit Azure Files, wenn Sie eine Verbindung von Linux-Clients herstellen. Darüber hinaus werden die möglichen Ursachen und Lösungen für diese Probleme bereitgestellt. 

Zusätzlich zu den Schritten zur Problembehandlung in diesem Artikel können Sie [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) verwenden, um sicherzustellen, dass der Linux-Client die erforderlichen Voraussetzungen erfüllt. AzFileDiagnostics automatisiert die Erkennung eines Großteils der in diesem Artikel erwähnten Symptome. Das Tool hilft Ihnen dabei, Ihre Umgebung optimal einzurichten. Diese Informationen stehen auch in der [Problembehandlung für Azure Files-Freigaben](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) zur Verfügung. Die Problembehandlung unterstützt Sie beim Behandeln von Verbindungs-, Zuordnungs- und Einbindungsproblemen im Zusammenhang mit Azure Files-Freigaben.

> [!IMPORTANT]
> Der Inhalt dieses Artikels gilt nur für SMB-Freigaben. Weitere Informationen zu NFS-Freigaben finden Sie unter [Behandeln von Problemen mit Azure NFS-Dateifreigaben](storage-troubleshooting-files-nfs.md).

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>Verbindungsherstellung mit oder Einbindung von Azure-Dateifreigabe nicht möglich

### <a name="cause"></a>Ursache

Häufige Ursachen für dieses Problem:

- Sie verwenden einen Client mit nicht kompatibler Linux-Distribution. Verwenden Sie möglichst die folgenden Linux-Distributionen, um eine Verbindung mit einer Azure-Dateifreigabe herzustellen:

|   | SMB 2.1 <br>(Einbindungen auf virtuellen Computern innerhalb der gleichen Azure-Region) | SMB 3.0 <br>(Einbindungen aus einer lokalen Region und regionsübergreifend) |
| --- | :---: | :---: |
| **Ubuntu Server** | 14.04+ | 16.04 und höher |
| **RHEL** | 7 und höher | 7.5 und höher |
| **CentOS** | 7 und höher |  7.5 und höher |
| **Debian** | 8 und höher |   |
| **openSUSE** | 13.2 und höher | 42.3+ |
| **SUSE Linux Enterprise Server** | 12 | 12 SP3 und höher |

- Auf dem Client sind keine CIFS-Hilfsprogramme (cifs-utils) installiert.
- Die mindestens erforderliche SMB-/CIFS-Version 2.1 ist auf dem Client nicht installiert.
- Der Client unterstützt die SMB 3.0-Verschlüsselung nicht. Die oben aufgeführte Tabelle enthält eine Liste der Linux-Distributionen, die die lokale und regionsübergreifende Bereitstellung unter Verwendung der Verschlüsselung unterstützen. Bei anderen Distributionen wird mindestens die Kernel-Version 4.11 vorausgesetzt.
- Sie versuchen, über den TCP-Port 445 eine Verbindung mit einem Speicherkonto herstellen. Dies wird nicht unterstützt.
- Sie versuchen, auf einem virtuellen Computer eine Verbindung mit der Azure-Dateifreigabe herzustellen, und der virtuelle Computer befindet sich nicht in der gleichen Region wie das Speicherkonto.
- Ist für das Speicherkonto die Einstellung [Sichere Übertragung erforderlich]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) aktiviert, lässt Azure Files nur Verbindungen über SMB 3.0 mit Verschlüsselung zu.

### <a name="solution"></a>Lösung

Verwenden Sie das [Problembehandlungstool für Azure Files-Bereitstellungsfehlern unter Linux](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux), um das Problem zu beheben. Dieses Tool ermöglicht Folgendes:

* Überprüfen der Clientausführungsumgebung
* Erkennen der nicht kompatiblen Clientkonfiguration, die zu einem Zugriffsfehler für Azure Files führen würde
* Bereitstellen einer Anleitung zur Selbsthilfe
* Erfassen der Diagnoseablaufverfolgungen

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>„Bereitstellungsfehler (13): Zugriff verweigert“ beim Bereitstellen einer Azure-Dateifreigabe

### <a name="cause-1-unencrypted-communication-channel"></a>Ursache 1: Unverschlüsselter Kommunikationskanal

Aus Sicherheitsgründen werden Verbindungen mit Azure-Dateifreigaben blockiert, wenn der Kommunikationskanal nicht verschlüsselt ist und der Verbindungsversuch nicht von dem gleichen Datencenter aus erfolgt, in dem sich die Azure-Dateifreigaben befinden. Unverschlüsselte Verbindungen innerhalb desselben Datencenters können auch blockiert werden, wenn die Einstellung [Sichere Übertragung erforderlich](../common/storage-require-secure-transfer.md) für das Speicherkonto aktiviert ist. Ein verschlüsselter Kommunikationskanal wird nur dann bereitgestellt, wenn das Clientbetriebssystem des Benutzers SMB-Verschlüsselung unterstützt.

Weitere Informationen finden Sie unter [Voraussetzungen für das Einbinden einer Azure-Dateifreigabe in Linux und das Paket „cifs-utils“](storage-how-to-use-files-linux.md#prerequisites). 

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

1. Stellen Sie eine Verbindung von einem Client aus her, der SMB-Verschlüsselung unterstützt, oder stellen Sie eine Verbindung von einem virtuellen Computer aus her, der sich im selben Datencenter wie das Azure-Speicherkonto befindet, das für die Azure-Dateifreigabe verwendet wird.
2. Vergewissern Sie sich, dass die Einstellung [Sichere Übertragung erforderlich](../common/storage-require-secure-transfer.md) im Speicherkonto deaktiviert ist, wenn der Client keine SMB-Verschlüsselung unterstützt.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Ursache 2: Virtuelle Netzwerk- oder Firewallregeln sind für das Speicherkonto aktiviert. 

Wenn für das Speicherkonto Regeln für das virtuelle Netzwerk (VNET) und die Firewall konfiguriert sind, wird dem Netzwerkverkehr der Zugriff verweigert, es sei denn, der Client-IP-Adresse oder dem virtuellen Netzwerk wird der Zugriff gestattet.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Stellen Sie sicher, dass virtuelle Netzwerk- und Firewallregeln für das Speicherkonto ordnungsgemäß konfiguriert sind. Um zu testen, ob virtuelle Netzwerk- oder Firewallregeln das Problem verursachen, ändern Sie vorübergehend die Einstellung für das Speicherkonto in **Zugriff aus allen Netzwerken zulassen**. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md).

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>„[Zugriff verweigert] Datenträgerkontingent überschritten“, wenn Sie versuchen, eine Datei zu öffnen

Unter Linux erhalten Sie eine Fehlermeldung, die wie folgt aussieht:

**\<filename>[Zugriff verweigert] Datenträgerkontingent überschritten**

### <a name="cause"></a>Ursache

Sie haben die obere Grenze der gleichzeitig geöffneten Handles erreicht, die für eine Datei oder ein Verzeichnis zulässig sind.

Für eine einzelne Datei oder ein einzelnes Verzeichnis gilt ein Kontingent von 2.000 geöffneten Handles. Wenn Sie über 2.000 geöffnete Handles verfügen, wird eine Fehlermeldung mit dem Hinweis angezeigt, dass das Kontingent erreicht ist.

### <a name="solution"></a>Lösung

Reduzieren Sie die Anzahl der gleichzeitig geöffneten Handles, indem Sie einige Handles schließen und es anschließend erneut versuchen.

Verwenden Sie das PowerShell-Cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle), um geöffnete Handles für eine Dateifreigabe, ein Verzeichnis oder eine Datei anzuzeigen.  

Verwenden Sie das PowerShell-Cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle), um geöffnete Handles für eine Dateifreigabe, ein Verzeichnis oder eine Datei zu schließen.

> [!Note]  
> Die Cmdlets Get-AzStorageFileHandle und Close-AzStorageFileHandle sind im Az PowerShell-Modul, Version 2.4 oder höher, enthalten. Informationen zum Installieren des neuesten Az PowerShell-Moduls finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Langsames Kopieren von Dateien in und aus Azure Files unter Linux

- Wenn Sie keine bestimmte Anforderung für die Mindest-E/A-Größe haben, empfehlen wir Ihnen für eine optimale Leistung die Verwendung von 1 MiB als E/A-Größe.
- Verwenden Sie die richtige Kopiermethode:
    - Verwenden Sie [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) für Übertragungen zwischen zwei Dateifreigaben.
    - Durch die Verwendung von „cp“ oder „dd“ mit „parallel“ lässt sich die Kopiergeschwindigkeit ggf. verbessern. Die Anzahl der Threads richtet sich nach Ihrem Anwendungsfall und der Workload. Die folgenden Beispiele verwenden sechs Befehle: 
    - cp-Beispiel (cp verwendet die Standardblockgröße des Dateisystems als Segmentgröße): `find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`.
    - dd-Beispiel (mit diesem Befehl wird die Blockgröße explizit auf 1 MiB festgelegt): `find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - Open-Source-Tools von Drittanbietern, beispielsweise:
        - [GNU Parallel](https://www.gnu.org/software/parallel/)
        - [Fpart](https://github.com/martymac/fpart) – Sortiert Dateien und packt sie in Partitionen.
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) – Verwendet Fpart und ein Kopiertool, um mehrere Instanzen zu erzeugen und Daten von „src_dir“ zu „dst_url“ zu migrieren.
        - [Multi](https://github.com/pkolano/mutil) – Multithreaded „cp“ und „md5sum“ basierend auf GNU Core Utilities
- Wenn Sie die Dateigröße im Voraus festlegen, anstatt jeden Schreibvorgang als erweiternden Schreibvorgang zu erstellen, können Sie die Kopiergeschwindigkeit in Szenarien verbessern, in denen die Dateigröße bekannt ist. Wenn erweiternde Schreibvorgänge vermieden werden müssen, können Sie eine Zieldateigröße mit dem Befehl `truncate - size <size><file>` festlegen. Danach kopiert der Befehl `dd if=<source> of=<target> bs=1M conv=notrunc` eine Quelldatei, ohne dass die Größe der Zieldatei wiederholt aktualisiert werden muss. Sie können z.B. die Zieldateigröße für jede Datei festlegen, die Sie kopieren möchten (angenommen, eine Freigabe wird unter „/mnt/share“ eingebunden):
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - Anschließend können Sie Dateien kopieren, ohne Schreibvorgänge parallel zu erweitern: `$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-30"></a>„Bereitstellungsfehler (115): Vorgang wird ausgeführt“, beim Bereitstellen von Azure Files mit SMB 3.0

### <a name="cause"></a>Ursache

Einige Linux-Distributionen unterstützen die Verschlüsselungsfeatures in SMB 3.0 noch nicht. Aufgrund eines fehlenden Features erhalten Benutzer möglicherweise eine Fehlermeldung vom Typ 115, wenn sie versuchen, Azure Files mithilfe von SMB 3.0 einzubinden. SMB 3.0 mit vollständiger Verschlüsselung wird erst ab Ubuntu 16.04 unterstützt.

### <a name="solution"></a>Lösung

Das Verschlüsselungsfeature für SMB 3.0 für Linux wurde im Kernel 4.11 eingeführt. Dieses Feature ermöglicht die Einbindung einer Azure-Dateifreigabe aus der lokalen Umgebung oder aus einer anderen Azure-Region. Einige Linux-Distributionen verfügen möglicherweise über zurückportierte Änderungen vom 4.11-Kernel zu älteren Versionen des Linux-Kernels, die von ihnen verwaltet werden. Um festzustellen, ob Ihre Linux-Version SMB 3.0 mit Verschlüsselung unterstützt, sehen Sie unter [Verwenden von Azure Files mit Linux](storage-how-to-use-files-linux.md) nach. 

Falls Ihr Linux-SMB-Client die Verschlüsselung nicht unterstützt, binden Sie Azure Files mithilfe von SMB 2.1 von einem virtuellen Azure-Linux-Computer aus ein, der sich im gleichen Datencenter befindet wie die Dateifreigabe. Vergewissern Sie sich, dass die Einstellung [Sichere Übertragung erforderlich]( https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) für das Speicherkonto deaktiviert ist. 

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Fehler „Kein Zugriff“ beim Versuch, auf eine Azure-Dateifreigabe zuzugreifen oder sie zu löschen  
Wenn Sie versuchen, auf eine Azure-Dateifreigabe im Portal zuzugreifen oder sie zu löschen, wird möglicherweise folgende Fehlermeldung angezeigt:

Kein Zugriff  
Fehlercode: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Ursache 1: Virtuelle Netzwerk- oder Firewallregeln sind für das Speicherkonto aktiviert.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

Stellen Sie sicher, dass virtuelle Netzwerk- und Firewallregeln für das Speicherkonto ordnungsgemäß konfiguriert sind. Um zu testen, ob virtuelle Netzwerk- oder Firewallregeln das Problem verursachen, ändern Sie vorübergehend die Einstellung für das Speicherkonto in **Zugriff aus allen Netzwerken zulassen**. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Ursache 2: Ihr Benutzerkonto besitzt keinen Zugriff auf das Speicherkonto

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Navigieren Sie zu dem Speicherkonto, in dem sich die Azure-Dateifreigabe befindet, klicken Sie auf **Zugriffssteuerung (IAM)** , und überprüfen Sie, ob Ihr Benutzerkonto Zugriff auf das Speicherkonto besitzt. Weitere Informationen finden Sie unter [Schützen Ihres Speicherkontos mit rollenbasierter Zugriffssteuerung (Azure RBAC)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Eine Datei oder ein Verzeichnis in einer Azure-Dateifreigabe kann nicht gelöscht werden

### <a name="cause"></a>Ursache
Dieses Problem tritt in der Regel auf, wenn für die Datei oder das Verzeichnis ein geöffnetes Handle vorhanden ist. 

### <a name="solution"></a>Lösung

Wenn die SMB-Clients alle geöffneten Handles geschlossen haben und das Problem weiterhin auftritt, führen Sie Folgendes aus:

- Verwenden Sie das PowerShell-Cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle), um geöffnete Handles anzuzeigen.

- Verwenden Sie das PowerShell-Cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle), um geöffnete Handles zu schließen. 

> [!Note]  
> Die Cmdlets Get-AzStorageFileHandle und Close-AzStorageFileHandle sind im Az PowerShell-Modul, Version 2.4 oder höher, enthalten. Informationen zum Installieren des neuesten Az PowerShell-Moduls finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Langsame Leistung in einer Azure-Dateifreigabe, die in einer Linux-VM bereit gestellt ist

### <a name="cause-1-caching"></a>Ursache 1: Caching

Eine mögliche Ursache der langsamen Leistung ist der deaktivierte Cache. Caching kann nützlich sein, wenn Sie wiederholt auf eine Datei zugreifen. Andernfalls kann es einen Mehraufwand verursachen. Überprüfen Sie, ob Sie den Cache verwenden, bevor Sie ihn deaktivieren.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

Um zu überprüfen, ob der Cache deaktiviert ist, suchen Sie nach dem **cache=** -Eintrag.

**Cache=none** gibt an, dass das Caching deaktiviert ist. Stellen Sie die Freigabe erneut bereit, entweder durch die Verwendung des Standardbereitstellungsbefehls oder explizit durch das Hinzufügen der Option **cache=strict** zum Bereitstellungsbefehl, um sicherzustellen, dass das Standardcaching oder der Cachingmodus „strict“ aktiviert ist.

In einigen Szenarios kann die **serverino**-Bereitstellungsoption dazu führen, dass der **ls**-Befehl unverzüglich gegen jeden Verzeichniseintrag läuft. Dieses Verhalten führt zu einer Leistungsminderung, wenn Sie ein großes Verzeichnis auflisten. Sie können die Bereitstellungsoption in Ihrem **/etc/fstab**-Eintrag finden:

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

Sie können auch überprüfen, ob die richtigen Optionen verwendet werden, indem Sie den Befehl **sudo mount | grep cifs** ausführen und dessen Ausgabe überprüfen. Beispielausgabe:

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

Wenn die Optionen **cache=strict** oder **serverino** nicht vorhanden sind, heben Sie die Bereitstellung von Azure Files auf, und stellen Sie sie wieder her, indem Sie den „mount“-Befehl aus der [Dokumentation](./storage-how-to-use-files-linux.md) ausführen. Überprüfen Sie dann erneut, ob der **/etc/fstab**-Eintrag die richtigen Optionen hat.

### <a name="cause-2-throttling"></a>Ursache 2: Drosselung

Es ist möglich, dass eine Drosselung auftritt und Ihre Anforderungen an eine Warteschlange gesendet werden. Sie können dies anhand der [Azure Storage-Metriken in Azure Monitor](../blobs/monitor-blob-storage.md) überprüfen.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Stellen Sie sicher, dass sich Ihre App innerhalb der [Skalierbarkeitsziele für Azure Files](storage-files-scale-targets.md#azure-files-scale-targets) befindet.

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>Zeitstempel gingen beim Kopieren von Dateien von Windows auf Linux verloren

Auf Linux/Unix-Plattformen ist der Befehl **cp -p** nicht erfolgreich, wenn die Dateien 1 und 2 unterschiedlichen Benutzern gehören.

### <a name="cause"></a>Ursache

Das Force-Flag **f** in COPYFILE führt zu der Ausführung von **cp -p -f** unter Unix. Dieser Befehl schlägt auch fehl, um den Zeitstempel der Datei beizubehalten, die Ihnen nicht gehört.

### <a name="workaround"></a>Problemumgehung

Verwenden Sie das Speicherbenutzerkonto, um die Dateien zu kopieren:

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: cannot access '&lt;Pfad&gt;': Input/output error

Wenn Sie versuchen, mit dem Befehl „Is“ Dateien einer Azure-Dateifreigabe aufzulisten, bleibt der Befehl beim Auflisten der Dateien hängen. Sie erhalten den folgenden Fehler:

**ls: cannot access '&lt;Pfad&gt;': Input/output error**


### <a name="solution"></a>Lösung
Upgraden Sie den Linux-Kernel auf die folgenden Versionen, in denen dieses Problem behoben wurde:

- 4.4.87+
- 4.9.48+
- 4.12.11+
- Alle Versionen ab 4.13

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>Symbolische Verknüpfungen kann nicht erstellt werden – ln: Fehler beim Erstellen der symbolischen Verknüpfung „t“: Dieser Vorgang wird nicht unterstützt.

### <a name="cause"></a>Ursache
Standardmäßig ermöglicht das Bereitstellen von Azure-Dateifreigaben unter Linux mit CIFS keine Unterstützung symbolischer Verknüpfungen (Symlinks). Die Fehlermeldung sieht in etwa wie folgt aus:
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>Lösung
Der Linux-CIFS-Client unterstützt die Erstellung symbolischer Verknüpfungen im Windows-Stil über das SMB 2- oder SMB 3-Protokoll nicht. Derzeit unterstützt der Linux-Client eine andere Art von symbolischen Verknüpfungen namens [Minshall+Französische Symlinks](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks) (sowohl für Erstellungs- als auch für Folgevorgänge). Kunden, die symbolische Verknüpfungen benötigen, können die Bereitstellungsoption „mfsymlinks“ verwenden. Die Verwendung von „mfsymlinks“ empfiehlt sich, da dieses Format auch von Macs verwendet wird.

Wenn Sie symbolische Verknüpfungen verwenden möchten, fügen Sie am Ende des CIFS-Bereitstellungsbefehls Folgendes hinzu:

```
,mfsymlinks
```

Der Befehl sollte also in etwa wie folgt aussehen:

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

Anschließend können Sie symbolische Verknüpfungen gemäß den Informationen im [Wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers) erstellen.

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>„Bereitstellungsfehler (112): Host ist ausgefallen“ aufgrund eines Timeouts bei der Herstellung einer erneuten Verbindung

Ein „112“-Bereitstellungsfehler tritt auf dem Linux-Client auf, wenn sich der Client für eine längere Zeit im Leerlauf befand. Nach längerer Zeit im Leerlauf trennt der Client die Verbindung, und es tritt ein Verbindungstimeout auf.  

### <a name="cause"></a>Ursache

Die Verbindung kann aus den folgenden Gründen im Leerlauf sein:

-   Ein Grund dafür sind Netzwerkkommunikationsfehler, die das erneute Wiederherstellen einer TCP-Verbindung mit dem Server verhindern, wenn die Standardoption zur zeitweiligen Einbindung verwendet wird
-   Aktuelle Verbindungswiederherstellungs-Updates, die in älteren Kernel nicht vorhanden sind

### <a name="solution"></a>Lösung

Dieses Verbindungswiederherstellungs-Problem im Linux-Kernel wurde jetzt im Rahmen der folgenden Änderungen behoben:

- [Fix reconnect to not defer smb3 session reconnect long after socket reconnect (Stellen Sie die Verbindung wieder her, um die Verbindungswiederherstellung der smb3-Sitzung lange nach der Socket-Verbindungswiederherstellung rückzustellen)](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [Call echo service immediately after socket reconnect (Sofortiges Aufrufen des Echo-Diensts nach der Socket-Verbindungswiederherstellung)](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS: Fix a possible memory corruption during reconnect (Beheben einer Arbeitsspeicherbeschädigung während der Verbindungswiederherstellung)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS: Fix a possible double locking of mutex during reconnect (Beheben einer möglichen doppelten Sperre des Mutex während der Verbindungswiederherstellung, für Kernel v4.9 und spätere Versionen)](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

Allerdings können diese Änderungen noch nicht zu allen Linux-Distributionen portiert werden. Wenn Sie eine gängige Linux-Distribution verwenden, können Sie unter [Verwenden von Azure Files mit Linux](storage-how-to-use-files-linux.md) feststellen, welche Version Ihrer Distribution die erforderlichen Kerneländerungen aufweist.

### <a name="workaround"></a>Problemumgehung

Sie können dieses Problem umgehen, indem Sie eine ständige Bereitstellung festlegen. Bei einer ständigen Bereitstellung muss der Client auf eine Verbindungsherstellung oder auf eine explizite Unterbrechung warten. Dadurch lassen sich durch Netzwerktimeouts bedingte Fehler verhindern. Diese Problemumgehung kann jedoch endlose Wartevorgänge verursachen. Seien Sie darauf vorbereitet, Verbindungen nach Bedarf zu beenden.

Wenn Sie nicht auf die neuesten Kernelversionen upgraden können, können Sie dieses Problem umgehen, indem Sie eine Datei in der Azure-Dateifreigabe speichern und mindestens alle 30 Sekunden in diese Datei schreiben. Dabei muss es sich um einen Schreibvorgang handeln, wie z.B. die Umschreibung des Erstellungs- oder Änderungsdatums in der Datei. Andernfalls erhalten Sie möglicherweise zwischengespeicherte Ergebnisse, und Ihr Vorgang kann die Verbindungswiederherstellung möglicherweise nicht auslösen.

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-30"></a>„CIFS VFS: error -22 on ioctl to get interface list“ (Fehler -22 bei IOCTL zum Abrufen der Schnittstellenliste) beim Einbinden einer Azure-Dateifreigabe mithilfe von SMB 3.0

### <a name="cause"></a>Ursache
Dieser Fehler wird protokolliert, weil Azure Files [SMB Multichannel derzeit nicht unterstützt](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

### <a name="solution"></a>Lösung
Dieser Fehler kann ignoriert werden.


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>Kein Zugriff auf Ordner oder Dateien möglich, deren Name ein Leerzeichen oder einen Punkt am Ende hat

Sie können während der Bereitstellung unter Linux nicht auf Ordner oder Dateien der Azure-Dateifreigabe zugreifen. Befehle wie „du“ und „ls“ und/oder Anwendungen von Drittanbietern können während des Zugriffs auf die Freigabe mit dem Fehler „Datei oder Verzeichnis nicht vorhanden“ fehlschlagen. Das Hochladen von Dateien in die genannten Ordner über das Portal ist jedoch möglich.

### <a name="cause"></a>Ursache

Die Ordner oder Dateien wurden von einem System hochgeladen, das die Zeichen am Ende des Namens in ein anderes Zeichen codiert. Dateien, die von einem Macintosh-Computer hochgeladen wurden, haben möglicherweise das Zeichen „0xf028“ oder „0xf029“ anstelle von „0x20“ (Leerzeichen) oder „0x2E“ (Punkt).

### <a name="solution"></a>Lösung

Verwenden Sie die mapchars-Option für die Freigabe, während Sie diese unter Linux bereitstellen: 

nicht:

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

sondern:

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.