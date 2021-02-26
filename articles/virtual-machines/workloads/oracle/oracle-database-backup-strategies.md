---
title: Strategien zur Oracle Database-Sicherung in Azure Virtual Machines
description: Möglichkeiten der Sicherung von Oracle DB-Datenbanken in einer Azure-Linux-VM-Umgebung.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: adb455e7a49ead1e8ba8e9845a4e56542528d0b1
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064354"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Strategien zur Oracle Database-Sicherung auf einer Azure-Linux-VM

Datenbanksicherungen schützen die Datenbank vor Datenverlusten durch Ausfälle von Speicherkomponenten und Rechenzentren. Sie können außerdem zur Wiederherstellung einer Datenbank nach Bedienungsfehlern und für das Klonen einer Datenbank zu Entwicklungs- oder Testzwecken verwendet werden. 

Da in Azure alle Speicher hochredundant sind und der Verlust eines oder mehrerer Datenträger nicht zu einem Datenbankausfall führt, werden Sicherungen am häufigsten zum Schutz vor Bedienungsfehlern, für Klonvorgänge oder zur Datensicherung zu rechtlichen Zwecken verwendet. Außerdem dienen sie zum Schutz vor regionalen Ausfällen, bei denen keine Notfallwiederherstellungstechnologie wie z. B. DataGuard verwendet wird. In diesem Fall müssen die Sicherungen unter Verwendung der georedundanten Replikation in unterschiedlichen Azure-Regionen gespeichert werden, damit sie außerhalb der Region der primären Datenbank verfügbar sind.

## <a name="azure-storage"></a>Azure Storage 

Die [Azure Storage-Dienste](../../../storage/common/storage-introduction.md) sind die Cloudspeicherlösung von Microsoft für moderne Datenspeicherszenarien. Azure Storage umfasst eine Reihe von Diensten, mit denen externer Speicher auf der Azure-Linux-VM eingebunden werden kann, der sich als Sicherungsmedium für Oracle-Datenbanken eignet. Zum Initiieren eines Sicherungs- oder Wiederherstellungsvorgangs und zum Kopieren der Sicherung aus und in den Azure Storage-Dienst ist ein Sicherungstool wie beispielsweise Oracle RMAN erforderlich.
 
Azure Storage-Dienste bieten folgende Vorteile:

-  Robust und hoch verfügbar: Mithilfe von Redundanz wird sichergestellt, dass Ihre Daten sicher sind, falls es zu vorübergehenden Hardwareausfällen kommt. Der gesamte Speicher wird standardmäßig dreifach gespiegelt. Sie können sich auch für das Replizieren von Daten über Rechenzentren oder geografische Regionen hinweg entscheiden, um eine weitere Schutzebene vor lokalen Notfällen oder Naturkatastrophen zu schaffen. Daten, die auf diese Weise repliziert werden, sind bei einem unerwarteten Ausfall weiterhin hoch verfügbar.

-  Sicher. Alle Daten, die in ein Azure-Speicherkonto geschrieben werden, werden vom Dienst verschlüsselt. Bei Azure Storage können Sie genau steuern, wer Zugriff auf Ihre Daten hat.

-  Skalierbar. Azure Storage ist auf hohe Skalierbarkeit ausgelegt, um die Datenspeicherungs- und Leistungsanforderungen heutiger Anwendungen zu erfüllen.

-  Verwaltet. In Azure werden Hardwarewartung, Updates und die Behandlung kritischer Probleme für Sie übernommen.

-  Zugänglich: Auf Daten in Azure Storage kann weltweit per HTTP oder HTTPS zugegriffen werden. Microsoft stellt Clientbibliotheken für Azure Storage in verschiedenen Sprachen (z. B. .NET, Java, Node.js, Python, PHP, Ruby und Go) sowie eine ausgereifte REST-API bereit. Azure Storage unterstützt die Skripterstellung in Azure PowerShell oder der Azure CLI. Darüber hinaus werden im Azure-Portal und über Azure Storage-Explorer einfache visuelle Lösungen für die Arbeit mit Ihren Daten bereitgestellt.

Die Azure Storage-Plattform umfasst die folgenden Datendienste, die für die Verwendung als Sicherungsmedien für eine Oracle-Datenbank geeignet sind:

-  Azure-Blobs: Ein überaus skalierbarer Objektspeicher für Text- und Binärdaten. Er verfügt auch über Unterstützung für Big Data-Analysen mit Data Lake Storage Gen2.

-  Azure Files: Verwaltete Dateifreigaben für Bereitstellungen lokal oder in der Cloud.

-  Azure-Datenträger: Speichervolumes auf Blockebene für virtuelle Azure-Computer.

### <a name="cross-regional-storage-mounting"></a>Regionsübergreifende Speichereinbindung

Die Möglichkeit, regionsübergreifend auf den Sicherungsspeicher zuzugreifen, ist ein wichtiger Aspekt für Business Continuity & Disaster Recovery (BCDR) und nützlich für das Klonen von Datenbanken aus Sicherungen in verschiedenen geografischen Regionen. Der Azure-Cloudspeicher umfasst fünf Ebenen der [Redundanz](../../../storage/common/storage-redundancy.md).
- Bei lokal redundantem Speicher [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) werden die Daten dreimal innerhalb eines einzelnen physischen Standorts in der primären Region repliziert.  
- Bei zonenredundantem Speicher [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) werden die Daten in der primären Region durch LRS geschützt und synchron über drei Azure-Verfügbarkeitszonen hinweg in der primären Region repliziert. Die einzelnen Verfügbarkeitszonen werden außerdem durch LRS geschützt. 
- Bei georedundantem Speicher [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) werden die Daten in der primären Region durch LRS geschützt und asynchron in eine sekundäre Region repliziert, wo sie darüber hinaus durch LRS geschützt werden.
- Bei geozonenredundantem Speicher [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) werden die Daten mit ZRS synchron über drei Azure-Verfügbarkeitszonen hinweg in der primären Region kopiert. Anschließend werden die Daten asynchron an einen einzelnen physischen Standort in der sekundären Region kopiert. An allen Standorten werden die Daten auch durch LRS geschützt. 
- Georedundanter Speicher mit Lesezugriff [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) und geozonenredundanter Speicher mit Lesezugriff [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) ermöglichen jederzeit den Lesezugriff auf die in der sekundären Region replizierten Daten. 

#### <a name="blob-storage-and-file-storage"></a>Blob Storage und File Storage

Bei Verwendung von Azure Files mit den Protokollen SMB oder NFS 4.1 (Vorschau) zur Einbindung als Sicherungsspeicher ist zu beachten, dass in Azure Files kein georedundanter Speicher mit Lesezugriff (RA-GRS) und kein geozonenredundanter Speicher mit Lesezugriff (RA-GZRS) unterstützt wird. 

Wenn außerdem die Anforderung für den Sicherungsspeicher größer als 5 TiB ist, muss in Azure Files die [Funktion für große Dateifreigaben](../../../storage/files/storage-files-planning.md) aktiviert sein, die keine GRS- oder GZRS-Redundanz unterstützt. Nur LRS wird unterstützt. 

Wenn Azure Blob Storage über das NFS 3.0-Protokoll (Vorschau) eingebunden wird, unterstützt der Dienst derzeit nur LRS- und ZRS-Redundanz.  

Wurde Azure Blob Storage mit einer beliebigen Redundanzoption konfiguriert, kann die Einbindung mithilfe von Blobfuse erfolgen.

#### <a name="recovery-services-vault"></a>Recovery Services-Tresor 

Ein Recovery Services-Tresor ist eine Verwaltungsentität, unter der die im Laufe der Zeit erstellten Wiederherstellungspunkte gespeichert werden. Darüber hinaus verfügt er über eine Benutzeroberfläche zum Durchführen von sicherungsbezogenen Vorgängen. Hierzu gehören das Erstellen von bedarfsgesteuerten Sicherungen, Durchführen von Wiederherstellungen und Erstellen von Sicherungsrichtlinien.
Azure Backup übernimmt automatisch die Speicherung für den Tresor. Sie müssen bei der Erstellung angeben, wie dieser Speicher repliziert wird. Dies kann nicht mehr geändert werden, wenn bereits Elemente im Tresor geschützt werden. Wählen Sie für regionale Redundanz die Einstellung „Georedundant“ aus.

Wenn Sie die Wiederherstellung in einer sekundären [gekoppelten Azure-Region](../../../best-practices-availability-paired-regions.md) durchführen möchten, aktivieren Sie die Funktion [Bereichsübergreifende Wiederherstellung](../../../backup/backup-create-rs-vault.md). Wenn die bereichsübergreifende Wiederherstellung aktiviert ist, wird der Sicherungsspeicher von GRS in georedundanten Speicher mit Lesezugriff (RA-GRS) geändert. 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob Storage ist ein äußerst kostengünstiger, dauerhafter und sicherer cloudbasierter Speicherdienst, der zur Speicherung großer Mengen unstrukturierter Daten verwendet wird und sich ideal für Oracle Database-Sicherungen eignet. Azure Blob Storage kann mit dem NFS 3.0-Protokoll oder mit Blobfuse (Linux FUSE) auf Azure-Linux-VMs eingebunden werden.

#### <a name="azure-blob-blobfuse"></a>Azure Blob Storage mit Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) ist ein Open-Source-Projekt, das zum Bereitstellen eines virtuellen Dateisystems entwickelt wurde, das auf Azure Blob Storage basiert. Blobfuse verwendet die Open-Source-Bibliothek libfuse für die Kommunikation mit dem Linux FUSE-Kernelmodul und implementiert die Dateisystemvorgänge mithilfe der Azure Storage Blob-REST-APIs. Blobfuse ist derzeit für Ubuntu- und CentOS/Red Hat-Distributionen verfügbar. Außerdem ist es für Kubernetes mit dem [CSI-Treiber](https://github.com/kubernetes-sigs/blob-csi-driver) verfügbar. 

Obwohl Blobfuse in allen Azure-Regionen universell vorhanden ist und mit allen Speicherkontotypen funktioniert (einschließlich Universell V1/V2 und Azure Data Lake Store Gen2), hat sich gezeigt, dass die mit Blobfuse erreichte Leistung geringer ist als bei alternativen Protokollen wie SMB oder NFS. Als Medium zur Datensicherung wird daher die Verwendung des SMB- oder [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md)-Protokolls zum Einbinden von Azure Blob Storage empfohlen. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob Storage mit NFS 3.0 (Vorschau)

Die Unterstützung für das NFS 3.0-Protokoll (Network File System) in Azure befindet sich derzeit in der Vorschauphase. Durch die Unterstützung von [NFS](../../../storage/blobs/network-file-system-protocol-support.md) können Windows- und Linux-Clients einen Blob Storage-Container auf einer Azure-VM einbinden. 

NFS 3.0 in der öffentlichen Vorschau unterstützt GPV2-Speicherkonten mit der Leistungsstufe „Standard“ in den folgenden Regionen: 
- Australien (Osten)
- Korea, Mitte
- USA, Süden-Mitte. 

Um die Netzwerksicherheit sicherzustellen, muss sich das für die NFS-Einbindung verwendete Speicherkonto in einem VNet befinden. Azure Active Directory-Sicherheit (AD) und Zugriffssteuerungslisten (Access Control Lists, ACLs) werden in Konten mit aktivierter Unterstützung des NFS 3.0-Protokolls noch nicht unterstützt.

### <a name="azure-files"></a>Azure Files

[Azure Files](../../../storage/files/storage-files-introduction.md) ist ein cloudbasiertes, vollständig verwaltetes, verteiltes Dateisystem, das in lokalen oder cloudbasierten Windows-, Linux- oder macOS-Clients eingebunden werden kann.

Azure Files bietet vollständig verwaltete, plattformübergreifende Dateifreigaben in der Cloud, auf die über das SMB-Protokoll (Server Message Block) und über das NFS-Protokoll (Network File System, Vorschau) zugegriffen werden kann. Azure Files unterstützt derzeit nicht den Zugriff auf mehrere Protokolle, daher kann eine Freigabe entweder eine NFS-Freigabe oder eine SMB-Freigabe sein. Es wird empfohlen, vor dem Erstellen von Azure-Dateifreigaben zu bestimmen, welches Protokoll Ihren Anforderungen am besten entspricht.

Azure-Dateifreigaben können auch über Azure Backup in einem Recovery Services-Tresor geschützt werden. Dadurch wird eine zusätzliche Schutzebene für Oracle RMAN-Sicherungen bereitgestellt.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files mit NFS 4.1 (Vorschau)

Azure-Dateifreigaben können über das NFS 4.1-Protokoll (Network File System) in Linux-Distributionen eingebunden werden. In der Vorschauphase gibt es eine Reihe von Einschränkungen bei den unterstützten Funktionen, die [hier](../../../storage/files/storage-files-how-to-mount-nfs-shares.md) dokumentiert sind. 

In der Vorschauphase ist Azure Files mit NFS 4.1 außerdem auf die folgenden [Regionen](../../../storage/files/storage-files-how-to-mount-nfs-shares.md) beschränkt:
- USA, Osten (LRS und ZRS)
- USA (Ost) 2
- USA, Westen 2
- Europa, Westen
- Asien, Südosten
- UK, Süden
- Australien (Osten)

#### <a name="azure-files-smb-30"></a>Azure Files mit SMB 3.0

Azure-Dateifreigaben können mithilfe des SMB-Kernelclients in Linux-Distributionen eingebunden werden. Das CIFS-Protokoll (Common Internet File System), das in Linux-Distributionen verfügbar ist, ist eine Variante von SMB. Wenn Azure Files mit SMB auf Linux-VMs eingebunden wird, erfolgt die Einbindung als CIFS-Dateisystem. Außerdem muss das CIFS-Paket installiert werden. 

Azure Files mit SMB ist in allen Azure-Regionen allgemein verfügbar und weist die gleichen Leistungsmerkmale wie das NFS 3.0- und NFS 4.1-Protokoll auf und ist daher derzeit die empfohlene Methode zur Bereitstellung von Sicherungsspeichermedien für Azure-Linux-VMs.  

Zwei unterstützte Versionen von SMB sind verfügbar: SMB 2.1 und SMB 3.0, wobei letztere empfohlen wird, da sie die Verschlüsselung während der Übertragung unterstützt. Die verschiedenen Linux-Kernelversionen weisen jedoch eine unterschiedliche Unterstützung für SMB 2.1 und 3.0 auf. In [dieser Aufstellung](../../../storage/files/storage-how-to-use-files-linux.md) können Sie überprüfen, ob Ihre Anwendung SMB 3.0 unterstützt. 

Da Azure Files als Dateifreigabedienst für mehrere Benutzer konzipiert ist, sollten Sie bestimmte Eigenschaften anpassen, um ihn optimaler als Sicherungsspeichermedium nutzen zu können. Es wird empfohlen, die Zwischenspeicherung zu deaktivieren und die Benutzer- und Gruppen-IDs für erstellte Dateien festzulegen.

## <a name="azure-netapp-files"></a>Azure NetApp Files

Der [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md)-Dienst ist eine umfassende Speicherlösung für Oracle-Datenbanken auf Azure-VMs. Dieser leistungsstarke Dateispeicherdienst auf Unternehmensniveau unterstützt standardmäßig jeden Workloadtyp und ist hochverfügbar. In Verbindung mit dem Oracle Direct NFS-Treiber (dNFS) bietet Azure NetApp Files eine hochgradig optimierte Speicherebene für Oracle Database.

Azure NetApp Files ermöglicht effiziente speicherbasierte Momentaufnahmekopien des zugrunde liegenden Speichersystems, in dem ein RoW-Mechanismus (Redirect on Write) verwendet wird. Obwohl Momentaufnahmekopien äußerst schnell erstellt und wiederhergestellt werden können, dienen sie nur als erste Schutzmaßnahme, die den größten Teil der erforderlichen Wiederherstellungsvorgänge einer Organisation ausmachen kann. Dies ist häufig die Wiederherstellung nach Bedienungsfehlern. Momentaufnahmekopien stellen jedoch keine vollständige Sicherung dar. Um alle Sicherungs- und Wiederherstellungsanforderungen abzudecken, müssen externe Momentaufnahmereplikate und/oder andere Sicherungskopien in einer entfernten geografischen Region erstellt werden, um Schutz vor regionalen Ausfällen zu bieten. Weitere Informationen zur Verwendung von NetApp Files für Oracle-Datenbanken in Azure finden Sie in diesem [Bericht](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Azure Backup-Dienst

[Azure Backup](../../../backup/backup-overview.md) ist ein vollständig verwalteter PaaS-Dienst und bietet einfache, sichere und kostengünstige Lösungen, um Ihre Daten zu sichern und aus der Microsoft Azure-Cloud wiederherzustellen. Mit Azure Backup können lokale Clients, Azure-VMs und Azure Files-Freigaben sowie SQL Server-, Oracle-, MySQL-, PostgreSQL- und SAP HANA-Datenbanken auf Azure-VMs gesichert und wiederhergestellt werden. 

Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen von Originaldaten. Sicherungen werden in einem [Recovery Services-Tresor](../../../backup/backup-azure-recovery-services-vault-overview.md) mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert. Konfiguration und Skalierung sind unkompliziert. Sicherungen werden außerdem optimiert und können bei Bedarf problemlos wiederhergestellt werden. Dank der Leistung und unbegrenzten Skalierbarkeit der Azure-Cloud bietet Azure Backup Hochverfügbarkeit ohne Wartungs- oder Überwachungsaufwand. Die Menge an übertragenen ein- oder ausgehenden Daten wird in Azure Backup nicht beschränkt, und es fallen keine Kosten für die übertragenen Daten an. Die Daten werden während der Übertragung und im Ruhezustand geschützt. 

Um Dauerhaftigkeit zu gewährleisten, bietet Azure Backup mehrere Replikationstypen, mit denen die Hochverfügbarkeit Ihrer Sicherungsdaten sichergestellt wird.

- Bei lokal redundantem Speicher [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) werden Ihre Daten dreimal in einer Speicherskalierungseinheit in einem Rechenzentrum repliziert. (Es werden also drei Kopien Ihrer Daten erstellt.)
- Georedundanter Speicher [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) ist die standardmäßige und empfohlene Replikationsoption. GRS repliziert Ihre Daten in einer sekundären Region, die mehrere hundert Kilometer vom primären Speicherort der Quelldaten entfernt ist.

Bei einem mit GRS-Redundanz erstellten Tresor kann die Funktion [Bereichsübergreifende Wiederherstellung](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) konfiguriert werden, über die Sie Daten in einer sekundären gepaarten Azure-Region wiederherstellen können.

Der Azure Backup-Dienst bietet ein [Framework](../../../backup/backup-azure-linux-app-consistent.md) zum Erreichen von Anwendungskonsistenz bei Sicherungen von Windows- und Linux-VMs für verschiedene Anwendungen wie Oracle, MySQL, Mongo DB, SAP HANA und PostgreSQL. Dies wird als anwendungskonsistente Momentaufnahmen bezeichnet. Dies umfasst das Aufrufen eines Pre-Skripts (zum Stilllegen der Anwendungen), bevor eine Momentaufnahme der Datenträger erstellt wird, und eines Post-Skripts (zum Reaktivieren der Anwendungen) nach Erstellen der Momentaufnahme, um die Anwendungen wieder in den normalen Modus zu versetzen. Obwohl Beispiele für Pre- und Post-Skripts auf GitHub bereitgestellt sind, liegt das Erstellen und Pflegen dieser Skripts in Ihrer Verantwortung. Im Fall von Oracle muss sich die Datenbank im ARCHIVELOG-Modus befinden, um Onlinesicherungen zu ermöglichen. Die entsprechenden Befehle für Beginn und Ende der Sicherungen der Datenbank werden in den Pre- und Post-Skripts ausgeführt, die Sie erstellen und verwalten müssen. 

Mittlerweile umfasst Azure Backup ein [erweitertes Framework für Pre- und Post-Skripts](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts) (derzeit in der Vorschauphase), über das gepackte Pre- und Post-Skripts für ausgewählte Anwendungen bereitgestellt werden. Benutzer von Azure Backup müssen lediglich die Anwendung angeben, woraufhin bei der Azure-VM-Sicherung automatisch die entsprechenden Pre- und Post-Skripts aufgerufen werden. Für die Pflege der gepackten Pre- und Post-Skripts ist das Azure Backup-Team zuständig, sodass Benutzer sicher sein können, dass diese Skripts unterstützt werden und gültig sind. Derzeit werden für das erweiterte Framework Oracle- und MySQL-Anwendungen unterstützt. Zukünftig sind weitere Anwendungstypen vorgesehen. Bei der Momentaufnahme handelt es sich um eine vollständige Kopie des Speichers und nicht um eine inkrementelle Momentaufnahme oder eine des Typs „Kopie bei Schreibvorgang“, sodass sie ein effektives Medium zur Wiederherstellung Ihrer Datenbank ist.

## <a name="next-steps"></a>Nächste Schritte

- Schnellstart [Erstellen einer Oracle-Datenbank](oracle-database-quick-create.md)
- [Sichern einer Oracle-Datenbank in Azure Files](oracle-database-backup-azure-storage.md)
- [Sichern einer Oracle-Datenbank mithilfe von Azure Backup](oracle-database-backup-azure-backup.md)


