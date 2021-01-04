---
title: Failoverclusterinstanzen
description: Erfahren Sie mehr über Failoverclusterinstanzen (FCIs) mit SQL Server in Azure Virtual Machines
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: d5bd2fc150ee1d35127eeb9dbf3dc1eeffdc9659
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685935"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Failoverclusterinstanzen mit SQL Server in Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel werden Funktionsunterschiede beschrieben, wenn Sie mit Failoverclusterinstanzen (FCIs) für SQL Server in Azure Virtual Machines (VMs) arbeiten. 

## <a name="overview"></a>Übersicht

SQL Server in Azure Virtual Machines nutzt die Funktion Windows Server-Failoverclustering (WSFC), um lokale Hochverfügbarkeit durch Redundanz auf Serverinstanzebene zu bieten: eine Failoverclusterinstanz. Eine FCI ist eine einzelne Instanz von SQL Server, die über WSFC-Knoten (oder einfach den Cluster) und möglicherweise über mehrere Subnetze hinweg installiert ist. Im Netzwerk scheint eine FCI eine Instanz von SQL Server zu sein, die auf einem einzelnen Computer ausgeführt wird. Die FCI bietet jedoch Failover von einem WSFC-Knoten auf einen anderen Knoten, wenn der aktuelle Knoten nicht mehr verfügbar ist.

Der restliche Artikel konzentriert sich auf die Unterschiede bei Failoverclusterinstanzen, wenn diese mit SQL Server auf Azure-VMs verwendet werden. Weitere Informationen zur Failoverclusteringtechnologie finden Sie unter: 

- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Failoverclusterinstanzen mit SQL Server in Azure Virtual Machines unterstützen die Verwendung eines Datenträgerzeugen, eines Cloudzeugen oder eines Dateifreigabezeugen für das Clusterquorum.

Weitere Informationen finden Sie unter [Bewährten Quorummethoden mit SQL Server-VMs in Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Storage

In herkömmlichen lokalen Clusterumgebungen verwendet ein Windows-Failovercluster ein SAN (Storage Area Network), das für beide Knoten als freigegebener Speicher verfügbar ist. SQL Server-Dateien werden im freigegebenen Speicher gehostet, und nur der aktive Knoten kann jeweils auf die Dateien zugreifen. 

SQL Server auf Azure-VMs bietet verschiedene Optionen als freigegebene Speicherlösung für eine Bereitstellung von SQL Server-Failoverclusterinstanzen: 

||[Freigegebene Azure-Datenträger](../../../virtual-machines/disks-shared.md)|[Premium-Dateifreigaben](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Direkte Speicherplätze (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Betriebssystemversion (Min.)**| All |Windows Server 2012|Windows Server 2016|
|**Mindestversion von SQL Server**|All|SQL Server 2012|SQL Server 2016|
|**Unterstützte VM-Verfügbarkeit** |Verfügbarkeitsgruppen mit Näherungsplatzierungsgruppen |Verfügbarkeitsgruppen und Verfügbarkeitszonen|Verfügbarkeitsgruppen |
|**Unterstützt FileStream**|Ja|Nein|Ja |
|**Azure-Blobcache**|Nein|Nein|Ja|

Im restlichen Teil dieses Abschnitts werden die Vorteile und Einschränkungen der einzelnen Speicheroptionen aufgeführt, die für SQL Server auf Azure-VMs verfügbar sind. 

### <a name="azure-shared-disks"></a>Freigegebene Azure-Datenträger

[Freigegebene Azure-Datenträger](../../../virtual-machines/disks-shared.md) sind eine Funktion von [verwalteten Azure-Datenträgern](../../../virtual-machines/managed-disks-overview.md). Windows Server-Failoverclustering unterstützt die Verwendung von freigegebenen Azure-Datenträgern mit einer Failoverclusterinstanz. 

**Unterstütztes Betriebssystem**: All   
**Unterstützte SQL-Version**: All     

**Vorteile:** 
- Nützlich für Anwendungen, die zu Azure unter Beibehaltung der HADR-Architektur (High-Availability and Disaster Recovery, Hochverfügbarkeit und Notfallwiederherstellung) migriert werden sollen. 
- Clusteranwendungen können aufgrund von SCSI PR-Unterstützung (SCSI Persistent Reservations) unverändert zu Azure migriert werden. 
- Unterstützt freigegebenen Azure SSD Premium- und Azure Ultra-Datenträgerspeicher.
- Kann einen einzelnen freigegebenen Datenträger oder Striping für mehrere freigegebene Datenträger verwenden, um einen freigegebenen Speicherpool zu erstellen. 
- Unterstützt Filestream.


**Einschränkungen:** 
- Virtuelle Computer müssen sich in derselben Verfügbarkeitsgruppe und in derselben Näherungsplatzierungsgruppe befinden.
- Verfügbarkeitszonen werden nicht unterstützt.
- Zwischenspeichern von SSD Premium-Datenträgern wird nicht unterstützt.
 
Informationen zu den ersten Schritten finden Sie unter [SQL Server-Failoverclusterinstanz mit freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Speicherplätze direkt

[Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) ist eine Windows Server-Funktion, die mit Failoverclustering in Azure Virtual Machines unterstützt wird. Sie bietet ein softwarebasiertes virtuelles SAN.

**Unterstütztes Betriebssystem**: Windows Server 2016 oder höher   
**Unterstützte SQL-Version**: SQL Server 2016 und höher   


**Vorteile:** 
- Ausreichende Netzwerkbandbreite ermöglicht eine robuste und leistungsstarke freigegebene Speicherlösung. 
- Unterstützt Azure-Blobcache, damit Lesevorgänge lokal aus dem Cache bedient werden können. (Aktualisierungen werden gleichzeitig auf beide Knoten repliziert.) 
- Unterstützt FileStream. 

**Einschränkungen:**
- Nur für Windows Server 2016 oder höher verfügbar. 
- Verfügbarkeitszonen werden nicht unterstützt.
- Erfordert, dass die gleiche Datenträgerkapazität an beide virtuelle Computer angefügt wird. 
- Hohe Netzwerkbandbreite ist erforderlich, um Hochleistung trotz der aktuell ausgeführten Datenträgerreplikation zu erzielen. 
- Erfordert eine größere VM-Größe und doppelte Speicherkapazität, da der Speicher an jede VM angefügt wird. 

Informationen zu den ersten Schritten finden Sie unter [SQL Server-Failoverclusterinstanz mit direkten Speicherplätzen](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Premium-Dateifreigabe

[Premium-Dateifreigaben](../../../storage/files/storage-how-to-create-premium-fileshare.md) sind eine Funktion von [Azure Files](../../../storage/files/index.yml). Premium-Dateifreigaben sind SSD-gestützt und weisen konsistent niedrige Latenz auf. Sie werden für die Verwendung mit Failoverclusterinstanzen für SQL Server 2012 oder höher unter Windows Server 2012 oder höher vollständig unterstützt. Premium-Dateifreigaben bieten Ihnen mehr Flexibilität, weil Sie ohne Ausfallzeiten die Größe der Dateifreigabe ändern und diese skalieren können.

**Unterstütztes Betriebssystem**: Windows Server 2012 und höher   
**Unterstützte SQL-Version**: SQL Server 2012 und höher   

**Vorteile:** 
- Einzige freigegebene Speicherlösung für virtuelle Computer, die auf mehrere Verfügbarkeitszonen verteilt sind. 
- Vollständig verwaltetes Dateisystem mit einstelligen Latenzzeiten und burstfähiger E/A-Leistung. 

**Einschränkungen:**
- Nur für Windows Server 2012 oder höher verfügbar. 
- FileStream wird nicht unterstützt 


Informationen zu den ersten Schritten finden Sie unter [SQL Server-Failoverclusterinstanz mit Premium-Dateifreigabe](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Partner

Es gibt Partnerclusteringlösungen mit unterstütztem Speicher. 

**Unterstütztes Betriebssystem**: All   
**Unterstützte SQL-Version**: All   

In einem Beispiel wird SIOS DataKeeper als Speicher verwendet. Weitere Informationen finden Sie im Blogbeitrag [Failoverclustering and SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI und ExpressRoute

Sie können auch einen freigegebenen iSCSI-Zielblockspeicher über Azure ExpressRoute bereitstellen. 

**Unterstütztes Betriebssystem**: All   
**Unterstützte SQL-Version**: All   

Beispielsweise macht NetApp Private Storage (NPS) ein iSCSI-Ziel über ExpressRoute mit Equinix für virtuelle Azure-Computer verfügbar.

Für gemeinsame Speichernutzungs- und Datenreplikationslösungen von Microsoft-Partnern sollten Sie sich an den Hersteller wenden, falls beim Failover Probleme in Bezug auf den Datenzugriff auftreten.

## <a name="connectivity"></a>Konnektivität

Failoverclusterinstanzen mit SQL Server in Azure Virtual Machines verwenden einen [verteilten Netzwerknamen (Distributed Network Name, DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) oder einen [virtuellen Netzwerknamen (VNN) mit Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md), um Datenverkehr an die SQL Server-Instanz unabhängig davon weiterzuleiten, welcher Knoten derzeit Besitzer der Clusterressourcen ist. Es gibt weitere Überlegungen, wenn bestimmte Features und der DNN mit einer SQL Server-FCI verwendet werden. Weitere Informationen finden Sie unter [DNN-Interoperabilität mit der SQL Server-FCI](failover-cluster-instance-dnn-interoperability.md). 

Weitere Informationen zu den Optionen für Clusterkonnektivität finden Sie unter [Weiterleiten von HADR-Verbindungen an SQL Server auf Azure-VMs](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Einschränkungen

BeachtenSie die folgenden Einschränkungen für Failoverclusterinstanzen mit SQL Server in Azure Virtual Machines. 

### <a name="lightweight-extension-support"></a>Unterstützung der Lightweight-Erweiterung   

Zurzeit werden SQL Server-Failoverclusterinstanzen auf Azure-VMs nur mit dem [Lightweight-Verwaltungsmodus](sql-server-iaas-agent-extension-automate-management.md#management-modes) der IaaS-Agent-Erweiterung von SQL Server unterstützt. Um aus dem vollständigen Erweiterungsmodus in den Lightweight-Modus zu wechseln, löschen Sie die Ressource **Virtueller SQL-Computer** für die entsprechenden VMs, und registrieren Sie diese dann bei der Erweiterung für den SQL-IaaS-Agent im Lightweight-Modus. Wenn Sie die Ressource **Virtueller SQL-Computer** mithilfe des Azure-Portals löschen, deaktivieren Sie das Kontrollkästchen neben dem richtigen virtuellen Computer, um das Löschen dieses Computers zu verhindern. 

Die vollständige Erweiterung unterstützt Funktionen wie die automatische Sicherung, Patchen und erweiterte Portalverwaltung. Diese Funktionen stehen für SQL Server-VMs, die im Lightweight-Verwaltungsmodus registriert sind, nicht zur Verfügung.

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines unterstützt Microsoft Distributed Transaction Coordinator (MSDTC) unter Windows Server 2019 mit Speicher auf freigegebenen Clustervolumes (CSV) und mit [Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) oder auf SQL Server-VMs, die freigegebene Azure-Datenträger verwenden. 

In Azure Virtual Machines wird MSDTC unter Windows Server 2016 und früheren Versionen mit freigegebenen Clustervolumes aus den folgenden Gründen nicht unterstützt:

- Die MSDTC-Clusterressource kann nicht für die Verwendung von freigegebenem Speicher konfiguriert werden. Wenn Sie unter Windows Server 2016 eine MSDTC-Ressource erstellen, wird kein freigegebener Speicher für die Verwendung angezeigt, selbst wenn der Speicher verfügbar ist. Dieses Problem wurde in Windows Server 2019 behoben.
- Der einfache Lastenausgleich verarbeitet keine RPC-Ports.


## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [bewährte Methoden für Clusterkonfigurationen](hadr-cluster-best-practices.md). Anschließend können Sie [die SQL Server-VM für FCI](failover-cluster-instance-prepare-vm.md) vorbereiten. 

Weitere Informationen finden Sie unter 

- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)