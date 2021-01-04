---
title: SAP HANA-Sicherung mit Azure Backup auf Dateiebene | Microsoft-Dokumentation
description: Für SAP HANA auf virtuellen Azure-Computern stehen grundsätzlich zwei Sicherungsoptionen zur Verfügung. In diesem Artikel wird die SAP HANA-Sicherung mit Azure Backup auf Dateiebene behandelt.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 13bdf4b10ec586bf83a70434f003cc1573aca469
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950260"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA-Sicherung mit Azure Backup auf Dateiebene

## <a name="introduction"></a>Einführung

Dieser Artikel ist ein verwandter Artikel zur [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](./sap-hana-backup-guide.md), der eine Übersicht und Informationen zu den ersten Schritten sowie weitere Details zum Azure Backup-Dienst und zu Speichermomentaufnahmen bietet. 

Verschiedene VM-Typen in Azure lassen eine unterschiedliche Anzahl angefügter VHDs zu. Die genauen Details finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../../sizes.md). Für die in dieser Dokumentation erwähnten Tests haben wir eine Azure-VM des Typs GS5 verwendet, die 64 angefügte Datenträger unterstützt. Bei umfangreichen SAP HANA-Systemen wird ein Großteil der Datenträger u.U. bereits für Daten- und Protokolldateien benötigt – möglicherweise in Kombination mit Software-Striping für einen optimalen Datenträger-E/A-Durchsatz. Weitere Informationen zu empfohlenen Datenträgerkonfigurationen für SAP HANA-Bereitstellungen auf Azure-VMs finden Sie im Artikel [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md). Die gegebenen Empfehlungen berücksichtigen auch Angaben zum Speicherplatz für lokale Sicherungen.

Die Sicherung/Wiederherstellung auf Dateiebene wird standardmäßig mit einer dateibasierten Sicherung über SAP HANA Studio oder über SAP HANA-SQL-Anweisungen verwaltet. Weitere Informationen finden Sie im Artikel [SAP HANA SQL and System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) (Referenz zu SAP HANA-SQL und zu Systemansichten).

![Diese Abbildung zeigt das Dialogfeld des Sicherungsmenüelements in SAP HANA-Studio.](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Diese Abbildung zeigt das Dialogfeld des Sicherungsmenüelements in SAP HANA-Studio. Bei Verwendung des Typs &quot;Datei&quot; muss ein Pfad im Dateisystem angegeben werden, an den SAP HANA die Sicherungsdateien schreibt. Die Wiederherstellung funktioniert auf die gleiche Weise.

Diese Option klingt zwar recht einfach und unkompliziert, es sind jedoch ein paar Punkte zu berücksichtigen. An eine Azure-VM kann nur eine begrenzte Anzahl von Datenträgern angefügt werden. Ggf. reicht die Dateisystemkapazität des virtuellen Computers nicht aus, um SAP HANA-Sicherungsdateien zu speichern. Das hängt von der Größe der Datenbank und von den Anforderungen für den Datenträgerdurchsatz (ggf. mit Software-Striping auf mehreren Datenträgern) ab. Weiter unten in diesem Artikel finden Sie verschiedene Optionen zum Verschieben dieser Sicherungsdateien sowie zum Verwalten der Dateigrößenbeschränkungen und der Leistung beim Verarbeiten großer Datenmengen.

Eine weitere Option mit größerer Freiheit bei der Gesamtkapazität ist Azure Blob Storage. Ein einzelnes Blob ist zwar ebenfalls auf 1 TB beschränkt, die Gesamtkapazität eines einzelnen Blobcontainers liegt momentan jedoch bei 500 TB. Darüber hinaus haben Kunden die Möglichkeit, so genannten &quot;kalten&quot; Blobspeicher auszuwählen und dadurch Kosten zu sparen. Ausführliche Informationen zu kaltem Blobspeicher finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).

Die SAP HANA-Sicherungen können auch in einem georeplizierten Speicherkonto gespeichert werden, um die Sicherheit weiter zu erhöhen. Ausführliche Informationen zur Speicherredundanz und zur Speicherreplikation finden Sie unter [Azure Storage-Redundanz](../../../storage/common/storage-redundancy.md).

Sie können dedizierte VHDs für SAP HANA-Sicherungen in einem dedizierten Sicherungsspeicherkonto mit Georeplizierung platzieren. Alternativ können Sie die VHDs mit den SAP HANA-Sicherungen in ein georepliziertes Speicherkonto oder in ein Speicherkonto in einer anderen Region kopieren.

## <a name="azure-blobxfer-utility-details"></a>Details zum Azure-Hilfsprogramm „blobxfer“

Wenn Sie Verzeichnisse und Dateien in Azure Storage speichern möchten, können Sie dazu entweder die Befehlszeilenschnittstelle oder PowerShell verwenden oder ein Tool mit einem der [Azure SDKs](https://azure.microsoft.com/downloads/) entwickeln. Zum Kopieren von Daten in Azure Storage steht auch das sofort einsatzbereite Hilfsprogramm AzCopy zur Verfügung. (Siehe [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](../../../storage/common/storage-use-azcopy-v10.md).)

Aus diesem Grund wurde „blobxfer“ zum Kopieren von SAP HANA-Sicherungsdateien verwendet. Dieses Open Source-Programm wird von vielen Kunden in Produktionsumgebungen genutzt und ist über [GitHub](https://github.com/Azure/blobxfer) erhältlich. Mit dem Tool können Daten direkt in Azure Blob Storage oder in eine Azure-Dateifreigabe kopiert werden. Darüber hinaus bietet es eine Reihe nützlicher Features wie MD5-Hash oder automatische Parallelität beim Kopieren eines Verzeichnisses mit mehreren Dateien.

## <a name="sap-hana-backup-performance"></a>SAP HANA-Sicherungsleistung
In diesem Kapitel werden Überlegungen zur Leistung behandelt. Die erreichten Zahlen stellen möglicherweise nicht den aktuellen Zustand dar, da es eine stetige Entwicklung für einen besseren Durchsatz in Azure Storage gibt. Daher sollten Sie einzelne Tests für Ihre Konfiguration und Azure-Region durchführen.

![Dieser Screenshot zeigt die SAP HANA-Sicherungskonsole in SAP HANA Studio.](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Dieser Screenshot zeigt die SAP HANA-Sicherungskonsole von SAP HANA Studio. Es hat etwa 42 Minuten gedauert, eine Sicherung von 230 GB auf einem einzelnen Azure HDD Standard-Speicherdatenträger zu sichern, der über das XFS-Dateisystem auf diesem Datenträger an den virtuellen HANA-Computer angefügt ist.

![Dieser Screenshot zeigt YaST auf dem virtuellen SAP HANA-Testcomputer.](media/sap-hana-backup-file-level/one-backup-disk.png)

Dieser Screenshot zeigt YaST auf dem virtuellen SAP HANA-Testcomputer. Sie erkennen den einzelnen 1-TB-Datenträger für die SAP HANA-Sicherung. Das Sichern von 230 GB dauerte etwa 42 Minuten. Darüber hinaus wurden fünf 200-GB-Datenträger angefügt, und Software-RAID „md0“ wurde erstellt – mit Striping für die fünf Azure-Datenträger.

![Wiederholen der gleichen Sicherung mit Software-RAID und übergreifendem Striping für fünf angefügte Azure Standardspeicherdatenträger](media/sap-hana-backup-file-level/five-backup-disks.png)

Bei der Wiederholung der gleichen Sicherung mit Software-RAID und übergreifendem Striping für fünf angefügte Azure Standardspeicherdatenträger konnte die Sicherungsdauer von 42 Minuten auf zehn Minuten verkürzt werden. Die Datenträger wurden ohne Zwischenspeicherung an den virtuellen Computer angefügt. Diese Übung veranschaulicht die Bedeutung des Datenträger-Schreibdurchsatzes für eine gute Sicherungszeit. Sie können zu Azure SDD Standard-Speicher oder Azure Storage Premium wechseln, um den Vorgang weiter zu beschleunigen und eine optimale Leistung zu erzielen. Grundsätzlich wird Azure HDD Standard-Speicher nicht empfohlen, sondern hier nur zu Demonstrationszwecken verwendet. Die Empfehlung lautet, für Produktionssysteme mindestens Azure SSD Standard-Speicher oder Azure Storage Premium zu verwenden.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopieren von SAP HANA-Sicherungsdateien in Azure Blob Storage
Die angegebenen Werte für Leistung, Sicherungsdauer und Kopierdauer stellen möglicherweise nicht den neuesten Status der Azure-Technologie dar. Microsoft verbessert Azure Storage ständig, um einen höheren Durchsatz und eine geringere Latenz zu erzielen. Die Angaben dienen daher lediglich zur Veranschaulichung. Sie müssen Tests entsprechend Ihren individuellen Anforderungen in der Azure-Region Ihrer Wahl ausführen, um einschätzen zu können, welche Methode für Sie am besten geeignet ist.

Azure Blob Storage ist die beste Option zur schnellen Speicherung von SAP HANA-Sicherungsdateien. Ein einzelner Blobcontainer ist auf etwa 500 TB beschränkt. Das sollte für Sicherungen von SAP HANA-Systemen unter Verwendung der Azure-VM-Typen M32ts, M32ls, M64ls und GS5 ausreichend sein. Kunden haben die Wahl zwischen &quot;heißem&quot; und &quot;kaltem&quot; Blobspeicher. (Weitere Informationen finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal).)

Mit dem blobxfer-Tool lassen sich die SAP HANA-Sicherungsdateien komfortabel direkt in Azure Blob Storage kopieren.

![Hier sehen Sie die Dateien einer vollständigen SAP HANA-Dateisicherung.](media/sap-hana-backup-file-level/list-of-backups.png)

Sie sehen die Dateien einer vollständigen SAP HANA-Dateisicherung. Die größte der vier Dateien ist rund 230 GB groß.

![Es hat ungefähr 3.000 Sekunden gedauert, die 230 GB in einen Blobcontainer eines Azure-Standardspeicherkontos zu kopieren.](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Ohne Verwendung des MD5-Hashs hat es beim ersten Test ungefähr 3.000 Sekunden gedauert, die 230 GB in einen Blobcontainer eines Azure-Standardspeicherkontos zu kopieren.

Über die Sicherungskonsole von HANA Studio können Sie die maximal zulässige Größe für HANA-Sicherungsdateien festlegen. In der Beispielumgebung hat sich dadurch die Leistung verbessert, da anstelle einer großen Datei mit 230 GB mehrere kleinere Sicherungsdateien verwendet werden.

Das Festlegen eines HANA-seitigen Grenzwerts für die Sicherungsdateigröße führt nicht zu einer Verkürzung der Sicherungsdauer, da die Dateien sequenziell geschrieben werden. Der Grenzwert für die Dateigröße wurde auf 60 GB festgelegt, weshalb bei der Sicherung anstelle einer einzelnen 230-GB-Datei vier große Datendateien erstellt wurden. Es kann erforderlich werden, mehrere Sicherungsdateien für die Sicherung von HANA-Datenbanken zu verwenden, wenn Ihre Sicherungsziele Einschränkungen hinsichtlich der Größe von Blobdateien aufweisen.

![Anschließend wurde die maximale Dateigröße für HANA-Sicherungen auf 15 GB festgelegt, um die Parallelität des blobxfer-Tools zu testen.](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Anschließend wurde die maximale Dateigröße für HANA-Sicherungen auf 15 GB festgelegt, um die Parallelität des blobxfer-Tools zu testen. Das führte zu 19 Sicherungsdateien. Durch diese Konfiguration konnte die Zeit, die blobxfer benötigt, um die 230 GB in Azure Blob Storage zu kopieren, von 3.000 Sekunden auf 875 Sekunden gesenkt werden.

Wenn Sie sich über das Kopieren von Sicherungen lokaler Datenträger an anderen Speicherorten wie Azure Blob Storage informieren, bedenken Sie, dass die am Ende für einen parallelen Kopiervorgang verwendete Bandbreite auf das Netzwerk- oder Speicherkontingent des jeweiligen VM-Typs angerechnet wird. Demzufolge müssen Sie die Dauer des Kopiervorgangs mit der Netzwerk- und Speicherbandbreite für die normale Workload auf dem virtuellen Computer abgleichen. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopieren von SAP HANA-Sicherungsdateien in eine NFS-Freigabe

Microsoft Azure bietet native NFS-Freigaben über [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Sie können verschiedene Volumes mit einer Kapazität von Dutzenden TB erstellen, um Sicherungen zu speichern und zu verwalten. Sie können auch eine Momentaufnahme dieser Volumes basierend auf der NetApp-Technologie erstellen. Azure NetApp Files (ANF) wird in drei Dienstebenen angeboten, die unterschiedlichen Speicherdurchsatz unterstützen. Im Artikel [Dienstebenen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) finden Sie weitere Informationen. Sie können ein NFS-Volume aus ANF erstellen und einbinden, wie im Artikel [Schnellstart: Einrichten von Azure NetApp Files und Erstellen eines NFS-Volumes](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal) beschrieben.

Neben der Verwendung von nativen NFS-Volumes in Azure mithilfe von ANF können Sie auf verschiedene Weise eigene Bereitstellungen erstellen, die NFS-Freigaben in Azure bereitstellen. Alle haben den Nachteil, dass Sie diese Lösungen selbst bereitstellen und verwalten müssen. Einige dieser Möglichkeiten sind in den folgenden Artikeln dokumentiert:

- [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

Mithilfe der oben beschriebenen NFS-Freigaben können für Sicherungen direkt HANA-Sicherungen erstellt oder Sicherungen lokaler Datenträger auf diese NFS-Freigaben kopiert werden.

> [!NOTE]
> SAP HANA unterstützt NFS v3 und NFS v4.x. Alle anderen Formate wie SMB mit dem CIFS-Dateisystem werden zum Schreiben von HANA-Sicherungen nicht unterstützt. Siehe auch [SAP-Supporthinweis Nr. 1820529](https://launchpad.support.sap.com/#/notes/1820529).

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopieren von SAP HANA-Sicherungsdateien in Azure Files

Sie können eine Azure Files-Freigabe innerhalb einer Azure-Linux-VM einbinden. Ausführliche Informationen zum Durchführen der Konfiguration finden Sie im Artikel [Verwenden des Azure-Dateispeichers unter Linux](../../../storage/files/storage-how-to-use-files-linux.md). Informationen zu Einschränkungen von Azure Files oder Azure Files Premium finden Sie im Artikel [Skalierbarkeits- und Leistungsziele für Azure Files](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> SMB mit CIFS-Dateisystem wird von SAP HANA zum Schreiben von HANA-Sicherungen nicht unterstützt. Siehe auch [SAP-Supporthinweis Nr. 1820529](https://launchpad.support.sap.com/#/notes/1820529). Sie können diese Lösung daher nur als endgültiges Ziel einer HANA-Datenbanksicherung verwenden, die direkt für lokale angefügte Datenträger durchgeführt wurde.
> 

Bei einem Test, der für Azure Files und nicht für Azure Files Premium durchgeführt wurde, dauerte das Kopieren von 19 Sicherungsdateien mit einem Gesamtvolumen von 230 GB ungefähr 929 Sekunden. Wir gehen davon aus, dass mit Azure Files Premium eine wesentlich bessere Zeit erzielt wird. Bedenken Sie jedoch, dass Sie das Interesse an einer schnellen Kopie mit den Anforderungen Ihrer Workload an die Netzwerkbandbreite abgleichen müssen. Da jeder Azure-VM-Typ das Netzwerkbandbreiten-Kontingent erzwingt, müssen Sie mit der Workload zuzüglich der Kopie der Sicherungsdateien im Bereich dieses Kontingents bleiben.

![Diese Abbildung zeigt, dass das Kopieren von 19 SAP HANA-Sicherungsdateien ungefähr 929 Sekunden gedauert hat.](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Das Speichern von SAP HANA-Sicherungsdateien in Azure Files kann eine interessante Option sein. Dies gilt insbesondere für die verbesserte Latenz und den Durchsatz von Azure Files Premium.

## <a name="next-steps"></a>Nächste Schritte
* [Sicherungsanleitung für SAP HANA in Azure Virtual Machines](sap-hana-backup-guide.md) enthält eine Übersicht und Informationen zu den ersten Schritten.
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).