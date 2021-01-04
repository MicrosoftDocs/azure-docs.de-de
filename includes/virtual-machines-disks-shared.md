---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 74c77356df4f35461a8b9f1459712cdcf7f77cbf
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559071"
---
Freigegebene Azure-Datenträger sind ein neues Feature für verwaltete Azure-Datenträger, mit dem Sie einen verwalteten Datenträger gleichzeitig an mehrere virtuelle Computer anfügen können. Durch das Anfügen eines verwalteten Datenträgers an mehrere virtuelle Computer können Sie entweder neue gruppierte Anwendungen in Azure bereitstellen oder bereits vorhandene gruppierte Anwendungen zu Azure migrieren.

## <a name="how-it-works"></a>Funktionsweise

Virtuelle Computer im Cluster können von ihrem angefügten Datenträger lesen oder darauf schreiben. Dies ist abhängig von der Reservierung, die von der gruppierten Anwendung unter Verwendung von [SCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI PR) ausgewählt wurde. Bei SCSI PR handelt es sich um einen Branchenstandard, der von Anwendungen im lokalen Storage Area Network (SAN) genutzt wird. Wenn Sie SCSI PR für einen verwalteten Datenträger aktivieren, können Sie diese Anwendungen unverändert zu Azure migrieren.

Freigegebene verwaltete Datenträger bieten gemeinsam genutzten Blockspeicher, auf den von mehreren virtuellen Computern aus zugegriffen werden kann. Die Datenträger werden als logische Gerätenummern (Logical Unit Numbers, LUNs) verfügbar gemacht. LUNs werden einem Initiator (virtueller Computer) von einem Ziel (Datenträger) präsentiert. Diese LUNs sehen für den virtuellen Computer wie ein direkt angefügter Speicher (Direct-Attached-Storage, DAS) oder wie ein lokales Laufwerk aus.

Freigegebene verwaltete Datenträger bieten nativ kein vollständig verwaltetes Dateisystem, auf das über SMB/NFS zugegriffen werden kann. Sie müssen einen Cluster-Manager wie Windows Server-Failovercluster (WSFC) oder Pacemaker verwenden, der Clusterknotenkommunikation und Schreibsperren verarbeitet.

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Betriebssystemanforderungen

Freigegebene Datenträger unterstützen verschiedene Betriebssysteme. Informationen zu den unterstützten Betriebssystemen finden Sie in den Abschnitten [Windows](#windows) und [Linux](#linux).

## <a name="disk-sizes"></a>Datenträgergrößen

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Beispielworkloads

### <a name="windows"></a>Windows

Freigegebene Azure-Datenträgen werden unter Windows Server 2008 und höher unterstützt. Die meisten Windows-basierten Clustervorgänge basieren auf WSFC. Dieser Cluster verarbeitet die gesamte Kerninfrastruktur für die Clusterknotenkommunikation und ermöglicht den Anwendungen die Nutzung paralleler Zugriffsmuster. Mit WSFC können abhängig von Ihrer Windows Server-Version sowohl CSV-basierte als auch CSV-fremde Optionen verwendet werden. Ausführliche Informationen finden Sie unter [Erstellen eines Failoverclusters](/windows-server/failover-clustering/create-failover-cluster).

Die folgende Liste enthält einige beliebte Anwendungen, die unter WSFC ausgeführt werden:

- [Erstellen einer FCI mit freigegebenen Azure-Datenträgern (SQL Server auf Azure-VMs)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Dateiserver mit horizontaler Skalierung (Scale-Out File Server, SOFS) [Vorlage] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [Vorlage] (https://aka.ms/azure-shared-disk-sapacs-template)
- Dateiserver zur allgemeinen Verwendung (IW-Workload)
- Remotedesktopserver-Benutzerprofildatenträger (Remote Desktop Server User Profile Disk, RDS UPD)

### <a name="linux"></a>Linux

Freigegebene Azure-Datenträger werden unter Folgendem unterstützt:
- [SUSE SLE für SAP und SUSE SLE HA 15 SP1 und höher](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18.04 und höher](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [RHEL-Entwicklervorschau in jeder RHEL 8-Version](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Von Linux-Clustern können Cluster-Manager wie [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker) verwendet werden. Pacemaker basiert auf [Corosync](http://corosync.github.io/corosync/) und ermöglicht die Clusterkommunikation für Anwendungen in hochverfügbaren Umgebungen. Beispiele für gängige Clusterdateisysteme wären etwa [ocfs2](https://oss.oracle.com/projects/ocfs2/) und [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Sie können auf SCSI Persistent Reservation (SCSI PR) und/oder STONITH Block Device (SBD) basierende Clusteringmodelle für die Vermittlung des Zugriffs auf den Datenträger verwenden. Bei Verwendung von SCSI PR können Sie Reservierungen und Registrierungen mithilfe von Hilfsprogrammen wie [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) und [sg_persist](https://linux.die.net/man/8/sg_persist) bearbeiten.

## <a name="persistent-reservation-flow"></a>Ablauf mit permanenter Reservierung

Das folgende Diagramm zeigt eine exemplarische Clusterdatenbankanwendung mit zwei Knoten, die SCSI PR nutzt, um Failover von einem Knoten auf den anderen zu ermöglichen.

![Cluster mit zwei Knoten. Der Zugriff auf den Datenträger wird von einer im Cluster ausgeführten Anwendung gesteuert.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Der Ablauf ist wie folgt:

1. Die gruppierte Anwendung, die sowohl auf „Azure VM 1“ als auch auf „Azure VM 2“ ausgeführt wird, registriert ihre Lese- oder Schreibabsicht für den Datenträger.
1. Die Anwendungsinstanz auf VM 1 sichert sich daraufhin die exklusive Reservierung, um auf den Datenträger zu schreiben.
1. Diese Reservierung wird für Ihren Azure-Datenträger erzwungen, und die Datenbank kann nun exklusiv auf den Datenträger schreiben. Schreibvorgänge von der Anwendungsinstanz auf VM 2 sind nicht erfolgreich.
1. Wenn die Anwendungsinstanz auf VM 1 ausfällt, kann die Instanz auf VM 2 ein Datenbankfailover initiieren und den Datenträger übernehmen.
1. Für den Azure-Datenträger wird nun diese Reservierung erzwungen, und der Datenträger akzeptiert keine Schreibvorgänge von VM 1 mehr. Er akzeptiert nur noch Schreibvorgänge von VM 2.
1. Die gruppierte Anwendung kann das Datenbankfailover abschließen und Anforderungen von VM 2 verarbeiten.

Das folgende Diagramm zeigt eine weitere gängige gruppierte Workload mit mehreren Knoten, die Daten vom Datenträger lesen, um parallele Prozesse auszuführen (etwa zum Trainieren von Machine Learning-Modellen).

![VM-Cluster mit vier Knoten, wobei jeder Knoten eine Schreibabsicht registriert und die Anwendung exklusive Reservierungen verwendet, um die Schreibergebnisse ordnungsgemäß zu behandeln.](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Der Ablauf ist wie folgt:

1. Die auf allen virtuellen Computern ausgeführte gruppierte Anwendung registriert eine Lese- oder Schreibabsicht für den Datenträger.
1. Die Anwendungsinstanz auf VM 1 sichert sich eine exklusive Reservierung, um auf den Datenträger zu schreiben, wodurch der Datenträger für Lesevorgänge von anderen virtuellen Computern verfügbar wird.
1. Diese Reservierung wird für Ihren Azure-Datenträger erzwungen.
1. Alle Knoten im Cluster können nun von dem Datenträger lesen. Ergebnisse werden nur von einem einzelnen Knoten auf den Datenträger geschrieben (im Auftrag aller Knoten im Cluster).

### <a name="ultra-disks-reservation-flow"></a>Ablauf mit Reservierung von Disk Ultra-Datenträgern

Disk Ultra-Datenträger bieten eine zusätzliche Drosselung, sodass insgesamt zwei Drosselungen erfolgen. Aus diesem Grund kann der Ablauf mit Reservierung von Disk Ultra-Datenträgern wie im vorherigen Abschnitt beschrieben funktionieren, oder er kann die Leistung differenzierter drosseln und verteilen.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Abbildung einer Tabelle, die den schreibgeschützten Zugriff oder Lese-/Schreibzugriff für die Kategorien „Reservierungsinhaber“, „Registriert“ und „Andere“ darstellt":::

## <a name="performance-throttles"></a>Leistungsdrosselungen

### <a name="premium-ssd-performance-throttles"></a>Leistungsdrosselungen für SSD Premium

Bei SSD Premium sind Datenträger-IOPS und -Durchsatz festgelegt. Ein P30-Datenträger bietet beispielsweise 5000 IOPS. Dieser Wert bleibt unabhängig davon gleich, ob der Datenträger für zwei oder für fünf VMs freigegeben wird. Die Datenträgerlimits können von einer einzelnen VM erreicht oder auf zwei oder mehr VMs aufgeteilt werden. 

### <a name="ultra-disk-performance-throttles"></a>Leistungsdrosselungen für Disk Ultra-Datenträger

Disk Ultra-Datenträger bieten Ihnen die einzigartige Möglichkeit, Ihre Leistung festzulegen, indem sie modifizierbare Attribute verfügbar machen und es Ihnen ermöglichen, diese zu ändern. Standardmäßig gibt es nur zwei änderbare Attribute, aber freigegebene Disk Ultra-Datenträger verfügen über zwei zusätzliche Attribute.


|attribute  |BESCHREIBUNG  |
|---------|---------|
|DiskIOPSReadWrite     |Die Gesamtzahl der zulässigen IOPS für alle virtuellen Computer, die den Freigabedatenträger mit Schreibzugriff einbinden.         |
|DiskMBpsReadWrite     |Der zulässige Gesamtdurchsatz (MB/s) für alle virtuellen Computer, die den freigegebenen Datenträger mit Schreibzugriff einbinden.         |
|DiskIOPSReadOnly*     |Die Gesamtzahl der zulässigen IOPS für alle virtuellen Computer, die den freigegebenen Datenträger als `ReadOnly` einbinden.         |
|DiskMBpsReadOnly*     |Der zulässige Gesamtdurchsatz (MB/s) für alle virtuellen Computer, die den freigegebenen Datenträger als `ReadOnly` einbinden.         |

\* Gilt nur für freigegebene Disk Ultra-Datenträger

Die folgenden Formeln erläutern, wie die Leistungsattribute festgelegt werden können, da sie vom Benutzer geändert werden können:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - IOPS-Grenzwerte von 300 IOPS/GiB, maximal 160.000 IOPS pro Datenträger
    - Mindestens 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly beträgt mindestens 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - Der Grenzwert für den Durchsatz eines einzelnen Datenträgers beträgt 256 KiB/s für jeden bereitgestellten IOPS-Wert, bis zu maximal 2.000 MBit/s pro Datenträger.
    - Der garantierte Mindestdurchsatz pro Datenträger beträgt 4 KiB/s für jeden bereitgestellten IOPS-Wert mit einem Gesamtmindestwert von 1 MB/s.

#### <a name="examples"></a>Beispiele

In den folgenden Beispielen werden einige Szenarios veranschaulicht, die zeigen, wie die Drosselung insbesondere mit freigegebenen Disk Ultra-Datenträgern funktionieren kann.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster mit zwei Knoten unter Verwendung von freigegebenen Clustervolumes

Es folgt ein Beispiel für einen WSFC mit zwei Knoten, der freigegebene Clustervolumes verwendet. Bei dieser Konfiguration haben beide virtuellen Computer gleichzeitigen Schreibzugriff auf den Datenträger, was dazu führt, dass die `ReadWrite`-Drosselung auf die beiden virtuellen Computer aufgeteilt und die `ReadOnly`-Drosselung nicht verwendet wird.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV-Beispiel für zwei Knoten, Ultra-Datenträger":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster mit zwei Knoten ohne freigegebene Clustervolumes

Es folgt ein Beispiel für einen WSFC mit zwei Knoten, der keine freigegebenen Clustervolumes verwendet. Bei dieser Konfiguration hat nur ein virtueller Computer Schreibzugriff auf den Datenträger. Dies führt dazu, dass die `ReadWrite`-Drosselung ausschließlich für den primären virtuellen Computer und die `ReadOnly`-Drosselung nur für den sekundären virtuellen Computer verwendet wird.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV-Beispiel für zwei Knoten, Ultra-Datenträger ohne CSV":::

##### <a name="four-node-linux-cluster"></a>Linux-Cluster mit vier Knoten

Es folgt ein Beispiel für einen Linux-Cluster mit vier Knoten, einem einzelnen Writer und drei Lesern mit horizontaler Skalierung. Bei dieser Konfiguration hat nur ein virtueller Computer Schreibzugriff auf den Datenträger. Dies führt dazu, dass die `ReadWrite`-Drosselung ausschließlich für den primären virtuellen Computer verwendet und die `ReadOnly`-Drosselung auf die sekundären virtuellen Computer aufgeteilt wird.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Beispiel für vier Knoten und Ultradrosselung":::

#### <a name="ultra-pricing"></a>Preise für Ultra-Datenträger

Die Preise für freigegebene Ultra-Datenträger basieren auf der bereitgestellten Kapazität, der Gesamtzahl zulässiger IOPS (diskIOPSReadWrite + diskIOPSReadOnly) und dem insgesamt zulässigen Durchsatz in MBit/s (diskMBpsReadWrite + diskMBpsReadOnly). Für zusätzliche VM-Einbindungen fallen keine weitere Gebühren an. Eine Beispiel: Ein freigegebener Ultra-Datenträger mit der folgenden Konfiguration (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) wird mit 1024 GiB, 10100 IOPS und 601 MBit/s abgerechnet, unabhängig davon, ob er in zwei oder fünf VMs eingebunden ist.