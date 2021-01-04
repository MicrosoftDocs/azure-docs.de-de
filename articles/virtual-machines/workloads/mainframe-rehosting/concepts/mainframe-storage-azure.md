---
title: Verschieben von Mainframespeicher zu Azure Storage
description: Hochgradig skalierbare Azure-Speicherressourcen können mainframebasierten Organisationen bei der Migration und Modernisierung von IBM z14-Anwendungen helfen.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 8f57210ff6b65ee3a893fb344a48629466e90004
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095354"
---
# <a name="move-mainframe-storage-to-azure"></a>Verschieben von Mainframespeicher zu Azure

Um Mainframeworkloads unter Microsoft Azure auszuführen, müssen Sie wissen, welche Entsprechungen der Funktionen Ihres Mainframes in Azure verfügbar sind. Die hochgradig skalierbaren Speicherressourcen können Organisationen beim Einstieg in die Modernisierung unterstützen, ohne dass die aktuell verwendeten Anwendungen aufgegeben werden müssen.

Azure bietet mainframeähnliche Funktionen und Speicherkapazität, die mit IBM z14-basierten Systemen vergleichbar ist (bezogen auf das neueste Modell zum Zeitpunkt des Verfassens dieses Artikels). In diesem Artikel erfahren Sie, wie Sie auf Azure vergleichbare Resultate erzielen.

## <a name="mainframe-storage-at-a-glance"></a>Mainframespeicher auf einen Blick

Zwei verschiedene Arten von Speicher sind für IBM-Mainframesysteme charakteristisch. Die erste ist ein DASD-Speichergerät (Direct Access Storage Device). Die zweite ist sequenzieller Speicher. Für die Speicherverwaltung bietet der Mainframe das Data Facility Storage Management Subsystem (DFSMS). Es verwaltet den Datenzugriff auf den verschiedenen Speichergeräten.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) bezeichnet ein separates Gerät für sekundären Speicher (nicht Arbeitsspeicher), der die Verwendung einer eindeutigen Adresse für den direkten Datenzugriff erlaubt. Ursprünglich bezog sich der Begriff DASD auf rotierende Datenträger, Magnettrommeln oder Datenzellen. Heutzutage kann sich der Begriff aber auch auf SSDs (Solid-state Storage Devices), SANs (Storage Area Networks), NAS (Network attached Storage) und optische Laufwerke beziehen. Im Rahmen dieses Dokuments bezieht sich DASD auf rotierende Datenträger, SANs und SSDs.

Im Gegensatz zu DASD-Speicher bezieht sich sequenzieller Speicher auf einem Mainframecomputer auf Geräte wie Bandlaufwerke, bei denen der Zugriff auf Daten ausgehend von einem Ausgangspunkt erfolgt und dann fortlaufend gelesen oder geschrieben wird.

Speichergeräte sind normalerweise mithilfe einer Glasfaserverbindung (Fiber Connection, FICON) angeschlossen, oder der Zugriff erfolgt direkt über den E/A-Bus des Mainframes mithilfe von [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), einer IBM-Technologie für Hochgeschwindigkeitskommunikation zwischen Partitionen auf einem Server mithilfe eines Hypervisors.

Die meisten Mainframesysteme unterteilen Speicher in zwei Typen:

- *Onlinespeicher* (auch als „Heißer“ Speicher bezeichnet) wird für den täglichen Betrieb benötigt. DASD-Speicher wird in der Regel für diesen Zweck verwendet. Allerdings kann auch sequenzieller Speicher, wie etwa tägliche Bandsicherungen (logisch oder physisch), für diesen Zweck verwendet werden.

- Für *Archivspeicher* (auch als „kalter“ Speicher bekannt) besteht keine Gewähr, dass er zu einem bestimmten Zeitpunkt eingebunden ist. Stattdessen erfolgen Einbindung und Zugriff bei Bedarf. Archivspeicher wird oftmals in Form von sequenziellen Bandsicherungen (logisch oder physisch) zur Speicherung implementiert.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe im Hinblick auf E/A-Latenz und IOPS

Mainframes werden häufig für Anwendungen verwendet, die hohe E/A-Leistung und geringe E/A-Latenz erfordern. Sie sind dazu aufgrund ihrer FICON-Verbindungen mit E/A-Geräten und HiperSockets imstande. Wenn HiperSockets verwendet werden, um Anwendungen und Geräte direkt mit dem E/A-Kanal eines Mainframecomputers zu verbinden, können Latenzen im Mikrosekundenbereich erreicht werden.

## <a name="azure-storage-at-a-glance"></a>Azure-Speicher auf einen Blick

Azure-IaaS-Optionen ([ Infrastructure-as-a-Service](https://azure.microsoft.com/overview/what-is-iaas/)) für Speicher bieten eine mit Mainframecomputern vergleichbare Kapazität.

Microsoft bietet Petabytes an Speicher für in Azure gehostete Anwendungen, und es besteht eine Reihe von Speicheroptionen. Diese reichen von SSD-Speicher für hohe Leistung bis zu preisgünstigem Blob-Speicher für die Massenspeicherung und Archive. Darüber hinaus bietet Azure eine Datenredundanzoption für die Speicherung – etwas, was sich in einer Mainframeumgebung nur mit größerem Aufwand realisieren lässt.

Azure-Speicher steht in Form von [Azure-Datenträgern](../../../managed-disks-overview.md), [Azure Files](../../../../storage/files/storage-files-introduction.md) und [Azure-Blobs](../../../../storage/blobs/storage-blobs-overview.md) zur Verfügung, wie in der folgenden Tabelle zusammengefasst. Erfahren Sie mehr über die [Einsatzzwecke der einzelnen Optionen](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>type</th><th>BESCHREIBUNG</th><th>Für diese Zwecke geeignet:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Bietet eine SMB-Schnittstelle, Clientbibliotheken und eine <a href="/rest/api/storageservices/file-service-rest-api">REST-Schnittstelle</a>, mit denen Sie von überall auf gespeicherte Dateien zugreifen können.
</td>
<td><ul>
<li>Verschieben einer Anwendung mit der „Lift and Shift“-Methode in die Cloud, wenn die Anwendung bereits die nativen Dateisystem-APIs verwendet, um Daten für andere in Azure ausgeführte Anwendungen freizugeben.</li>
<li>Speichern von Tools zum Entwickeln und Debuggen, auf die von vielen virtuellen Computern zugegriffen werden muss.</li>
</ul>
</td>
</tr>
<tr><td>Azure-Blobs
</td>
<td>Bietet Clientbibliotheken und eine <a href="/rest/api/storageservices/blob-service-rest-api">REST-Schnittstelle</a>, der große Mengen an unstrukturierten Daten in Blockblobs gespeichert und abgerufen werden können. Blob Storage unterstützt darüber hinaus <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> für Big Data-Analyselösungen auf Unternehmensniveau.
</td>
<td><ul>
<li>Unterstützung von Streaming und direktem Zugriff in einer Anwendung.</li>
<li>Zugriff auf die Anwendungsdaten von überall.</li>
<li>Aufbau eines unternehmensweiten Data Lakes und Durchführung von Big Data-Analysen.</li>
</ul></td>
</tr>
<tr><td>Azure Disks
</td>
<td>Bietet Clientbibliotheken und eine <a href="/rest/api/compute/disks">REST-Schnittstelle</a>, mit der Sie Daten beständig von einer angefügten virtuellen Festplatte speichern und abrufen können.
</td>
<td><ul>
<li>Verschieben von Anwendungen mit der „Lift and Shift“-Methode, die native Dateisystem-APIs verwenden, um Daten in beständigen Datenträgern zu lesen und dort hinein zu schreiben.</li>
<li>Speichern von Daten, auf die nicht von außerhalb der VM zugegriffen werden muss, an die der Datenträger angefügt ist.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>„Heißer“ (Online-) und „kalter“ (Archiv-) Speicher

Der Typ des Speichers für ein bestimmtes System hängt von den Anforderungen des Systems ab, einschließlich Speichergröße, Durchsatz und IOPS. Als Ersatz für Speicher vom DASD-Typ auf einem Mainframe verwenden Anwendungen auf Azure normalerweise Azure-Datenträgerspeicher. Für den Archivspeicher von Mainframes wird auf Azure Blob-Speicher verwendet.

SSDs bietet auf Azure die höchste Speicherleistung. Die folgenden Optionen sind verfügbar (zum Zeitpunkt, da dieses Dokument geschrieben wird):

| type         | Size           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD Ultra    | 4 GB bis 64 TB  | 1\.200 bis 160.000 IOPS |
| SSD Premium  | 32 GB bis 32 TB | 12 bis 15.000 IOPS     |
| SSD Standard | 32 GB bis 32 TB | 12 bis 2.000 IOPS      |

Blob-Speicher bietet das größte Speichervolumen auf Azure. Neben der Speichergröße stehen in Azure sowohl verwalteter als auch nicht verwalteter Speicher zu Wahl. Bei verwaltetem Speicher übernimmt Azure die Verwaltung der zugrunde liegenden Speicherkonten. Bei nicht verwaltetem Speicher liegt die Zuständigkeit für die Einrichtung von Azure-Speicherkonten passender Größe, mit denen die Speicheranforderungen erfüllt werden können, beim Benutzer.

## <a name="next-steps"></a>Nächste Schritte

- [Mainframemigration](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframerehosting auf virtuellen Azure-Computern](../overview.md)
- [Move mainframe compute to Azure (Migrieren von Mainframecomputeressourcen zu Azure)](mainframe-compute-Azure.md)
- [Deciding when to use Azure Blobs, Azure Files, or Azure Disks (Kriterien für den Einsatz von Azure-Blobs, Azure-Dateifreigaben und verwalteten Azure-Datenträgern)](../../../../storage/common/storage-introduction.md)
- [Verwaltete SSD Standard-Datenträger für Azure-VM-Workloads](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM-Ressourcen

- [Parallel Sysplex on IBM Z (Parallel Sysplex auf IBM Z)](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS and the Coupling Facility: Beyond the Basics (IBM CICS und die Coupling Facility: Fortgeschrittene Themen)](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Erstellen der erforderlichen Benutzer für eine Installation von Db2 pureScale Feature](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [db2icrt-Instanz erstellen (Befehl)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale Clustered Database Solution (Clusterdatenbank-Lösung mit Db2 pureScale)](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government cloud for mainframe applications (Microsoft Azure Government-Cloud für Mainframeanwendungen)](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft und FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Weitere Migrationsressourcen

- [Virtuelles Azure-Rechenzentrum: Lift and Shift-Leitfaden](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)