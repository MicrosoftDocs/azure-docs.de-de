---
title: Bewährte Methoden für die Clusterkonfiguration
description: Erfahren Sie mehr über die unterstützten Clusterkonfigurationen beim Konfigurieren von Hochverfügbarkeit und Notfallwiederherstellung (HADR) für SQL Server auf Azure Virtual Machines, z. B. unterstützte Quoren oder Verbindungsroutingoptionen.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 5a2540aeb36cfcb2048ec994bbb486badc8a68d1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358808"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Bewährte Methoden für die Clusterkonfiguration (SQL Server auf Azure-VMS)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ein Cluster wird für Hochverfügbarkeit und Notfallwiederherstellung (HADR) mit SQL Server auf Azure Virtual Machines (VMs) verwendet. 

Dieser Artikel enthält bewährte Methoden für die Clusterkonfiguration für [Failoverclusterinstanzen (FCIs)](failover-cluster-instance-overview.md) sowie [Verfügbarkeitsgruppen](availability-group-overview.md), wenn Sie diese mit SQL Server auf Azure-VMs verwenden. 


## <a name="networking"></a>Netzwerk

Verwenden Sie eine einzelne NIC pro Server (Clusterknoten) und ein einzelnes Subnetz. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten und Subnetze in einem Azure-VM-Gastcluster überflüssig macht. Der Clusterüberprüfungsbericht weist Sie darauf hin, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind. Diese Warnung kann für Azure-VM-Gastfailovercluster ignoriert werden.

### <a name="tuning-failover-cluster-network-thresholds"></a>Optimieren von Failovercluster-Netzwerkschwellenwerten

Wenn Sie Windows-Failoverclusterknoten auf Azure-VMs mit SQL Server Always On ausführen, wird empfohlen, die Clustereinstellung in einen weniger strengen Überwachungsstatus zu ändern.  Dadurch wird der Cluster deutlich stabiler und zuverlässiger.  Weitere Informationen hierzu finden Sie unter [IaaS mit SQL Always On: Optimieren der Failovercluster-Netzwerkschwellenwerte](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Quorum

Ein Cluster mit zwei Knoten funktioniert zwar ohne eine [Quorumressource](/windows-server/storage/storage-spaces/understand-quorum), Kunden müssen jedoch unbedingt eine Quorumressource verwenden, um Unterstützung für die Produktion zu erhalten. Ein Cluster ohne Quorumressource besteht die Clustervalidierung nicht. 

Technisch gesehen kann ein Cluster mit drei Knoten den Verlust eines einzelnen Knotens (bei zwei verbleibenden Knoten) ohne Quorumressource überstehen. Nachdem der Cluster jedoch auf zwei Knoten ausgefallen ist, besteht das Risiko, dass die Clusterressourcen im Fall eines Knotenausfalls oder eines Kommunikationsfehlers offline geschaltet werden, um ein Split-Brain-Szenario zu verhindern.

Durch das Konfigurieren einer Quorumressource kann der Cluster mit nur einem Knoten online bleiben.

In der folgenden Tabelle sind die verfügbaren Quorumoptionen in der Reihenfolge aufgeführt, in der sie für einen virtuellen Azure-Computer verwendet werden sollten. Dabei ist der Datenträgerzeuge die bevorzugte Wahl: 


||[Datenträgerzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Cloudzeuge](/windows-server/failover-clustering/deploy-cloud-witness)  |[Dateifreigabenzeuge](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Unterstütztes Betriebssystem**| All |Windows Server 2016+| All|


### <a name="disk-witness"></a>Datenträgerzeuge

Ein Datenträgerzeuge ist ein kleiner Clusterdatenträger in der Gruppe „Verfügbarer Clusterspeicher“. Dieser Datenträger ist hochverfügbar und unterstützt ein Failover zwischen Knoten. Er enthält eine Kopie der Clusterdatenbank mit einer Standardgröße, die in der Regel weniger als 1 GB beträgt. Der Datenträgerzeuge ist die bevorzugte Quorumoption für Cluster, die freigegebene Azure-Datenträger verwenden (oder eine Lösung für freigegebene Datenträger wie freigegebenes SCSI, iSCSI oder Fibre Channel-SAN).  Ein freigegebenes Clustervolume kann nicht als Datenträgerzeuge verwendet werden.

Konfigurieren Sie einen freigegebenen Azure-Datenträger als Datenträgerzeuge. 

Informationen zu den ersten Schritten finden Sie unter [Konfigurieren eines Datenträgerzeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Unterstütztes Betriebssystem**: All   


### <a name="cloud-witness"></a>Cloudzeuge

Ein Cloudzeuge ist ein Quorumzeugentyp für Failovercluster, der Microsoft Azure zum Bereitstellen einer Stimme für das Clusterquorum verwendet. Die Standardgröße beträgt ungefähr 1 MB, und nur der Zeitstempel ist enthalten. Ein Cloudzeuge eignet sich ideal für Bereitstellungen an mehreren Standorten, in mehreren Zonen und in mehreren Regionen.

Informationen zu den ersten Schritten finden Sie unter [Konfigurieren eines Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Unterstütztes Betriebssystem**: Windows Server 2016 oder höher   


### <a name="file-share-witness"></a>Dateifreigabenzeuge

Ein Dateifreigabezeuge ist eine SMB-Dateifreigabe, die in der Regel auf einem Dateiserver mit Windows Server konfiguriert ist. Dieser Zeuge verwaltet Clusteringinformationen in einer Datei „witness.log“, speichert aber keine Kopie der Clusterdatenbank. In Azure können Sie eine [Azure-Dateifreigabe](../../../storage/files/storage-how-to-create-file-share.md) zur Verwendung als Dateifreigabezeuge konfigurieren, oder Sie können eine Dateifreigabe auf einem separaten virtuellen Computer verwenden.

Wenn Sie eine Azure-Dateifreigabe verwenden möchten, können Sie diese mit dem gleichen Prozess einbinden wie die [Premium-Dateifreigabe](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Informationen zu den ersten Schritten finden Sie unter [Konfigurieren eines Dateifreigabenzeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Unterstütztes Betriebssystem**: Windows Server 2012 und höher   

## <a name="connectivity"></a>Konnektivität

In einer herkömmlichen lokalen Netzwerkumgebung scheint eine SQL Server-Failoverclusterinstanz eine einzelne Instanz von SQL Server zu sein, die auf einem einzelnen Computer ausgeführt wird. Da die Failoverclusterinstanz ein Failover von Knoten zu Knoten durchführt, stellt der Name des virtuellen Netzwerks (VNN) für die Instanz einen einheitlichen Verbindungspunkt bereit und ermöglicht es Anwendungen, eine Verbindung mit der SQL Server-Instanz herzustellen, ohne zu wissen, welcher Knoten derzeit aktiv ist. Wenn ein Failover auftritt, wird der Name des virtuellen Netzwerks für den neuen aktiven Knoten registriert, nachdem dieser gestartet wurde. Dieser Prozess ist für den Client oder die Anwendung transparent, der bzw. die eine Verbindung mit SQL Server herstellt. Dadurch werden die Ausfallzeiten des Clients oder der Anwendung bei einem Fehler minimiert. Analog dazu verwendet der Verfügbarkeitsgruppenlistener einen VNN, um Datenverkehr an das entsprechende Replikat weiterzuleiten. 

Verwenden Sie einen VNN mit Azure Load Balancer oder einen Namen eines verteilten Netzwerks (Distributed Network Name, DNN), um Datenverkehr an den VNN der Failoverclusterinstanz mit SQL Server auf virtuellen Azure-Computern weiterzuleiten oder um den vorhandenen VNN-Listener in einer Verfügbarkeitsgruppe zu ersetzen. 


In der folgenden Tabelle wird die Unterstützbarkeit der HADR-Verbindung verglichen: 

| |**Name des virtuellen Netzwerks (Virtual Network Name, VNN)**  |**Name des verteilten Netzwerks (Distributed Network Name, DNN)**  |
|---------|---------|---------|
|**Betriebssystemversion (Min.)**| All | Windows Server 2016 |
|**Mindestversion von SQL Server** |All |SQL Server 2019 CU2 (für FCI)<br/> SQL Server 2019 CU8 (für Verfügbarkeitsgruppe)|
|**Unterstützte HADR-Lösung** | Failoverclusterinstanz <br/> Verfügbarkeitsgruppe | Failoverclusterinstanz <br/> Verfügbarkeitsgruppe|


### <a name="virtual-network-name-vnn"></a>Name des virtuellen Netzwerks (Virtual Network Name, VNN)

Da der virtuelle IP-Zugriffspunkt in Azure anders funktioniert, müssen Sie [Azure Load Balancer](../../../load-balancer/index.yml) konfigurieren, um Datenverkehr an die IP-Adresse der FCI-Knoten oder an den Verfügbarkeitsgruppenlistener weiterzuleiten. Auf virtuellen Azure-Computern enthält ein Lastenausgleich die IP-Adresse für den VNN, auf dem die geclusterten SQL Server-Ressourcen basieren. Der Lastenausgleich verteilt eingehende Flows, die am Front-End eintreffen, und leitet diesen Datenverkehr dann an die durch den Back-End-Pool definierten Instanzen weiter. Sie konfigurieren den Datenverkehrsfluss mithilfe von Lastenausgleichsregeln und Integritätstests. Bei SQL Server-FCIs sind die Back-End-Pool-Instanzen die virtuellen Azure-Computer, auf denen SQL Server ausgeführt wird. 

Bei Verwendung des Lastenausgleichs kommt es zu einer geringfügigen Failoververzögerung, da der Integritätstest standardmäßig alle 10 Sekunden Aktivitätsprüfungen durchführt. 

Informieren Sie sich zunächst darüber, wie Sie Azure Load Balancer für eine [Failoverclusterinstanz](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oder für eine [Verfügbarkeitsgruppe](availability-group-vnn-azure-load-balancer-configure.md) konfigurieren.

**Unterstütztes Betriebssystem**: All   
**Unterstützte SQL-Version**: All   
**Unterstützte HADR-Lösung**: Failoverclusterinstanz und Verfügbarkeitsgruppe   


### <a name="distributed-network-name-dnn"></a>Name des verteilten Netzwerks (Distributed Network Name, DNN)

Der Name des verteilten Netzwerks ist ein neues Azure-Feature für SQL Server 2019. Der DNN ist eine Alternative, die es SQL Server-Clients ermöglicht, eine Verbindung mit der SQL Server-Failoverclusterinstanz oder -Verfügbarkeitsgruppe herzustellen, ohne einen Lastenausgleich zu verwenden. 

Wenn eine DNN-Ressource erstellt wird, bindet der Cluster den DNS-Namen an die IP-Adressen aller Knoten im Cluster. Vom SQL-Client wird versucht, eine Verbindung mit den einzelnen IP-Adressen in dieser Liste herzustellen, um zu ermitteln, mit welcher Ressource eine Verbindung hergestellt werden soll.  Sie können diesen Prozess beschleunigen, indem Sie `MultiSubnetFailover=True` in der Verbindungszeichenfolge angeben. Diese Einstellung weist den Anbieter an, alle IP-Adressen parallel zu testen, sodass der Client sofort eine Verbindung mit der FCI oder dem Listener herstellen kann. 

Wenn möglich, wird der Name eines verteiltes Netzwerks über einen Lastenausgleich empfohlen: 
- Die End-to-End-Lösung ist stabiler, da Sie die Lastenausgleichsressource nicht mehr pflegen müssen. 
- Wenn Sie die Lastenausgleichstests weglassen, wird die Failoverdauer minimiert. 
- Der DNN vereinfacht die Bereitstellung und Verwaltung der Failoverclusterinstanz oder des Verfügbarkeitsgruppenlisteners mit SQL Server auf virtuellen Azure-Computern. 

Bei Verwendung des DNN funktionieren die meisten SQL Server-Features transparent mit FCI und Verfügbarkeitsgruppen. Es gibt jedoch bestimmte Features, die ggf. besondere Aufmerksamkeit erfordern. Weitere Informationen finden Sie unter [Featureinteroperabilität mit SQL Server-FCI und DNN](failover-cluster-instance-dnn-interoperability.md) sowie unter [Featureinteroperabilität mit Verfügbarkeitsgruppe und DNN-Listener](availability-group-dnn-interoperability.md). 

Informieren Sie sich zunächst darüber, wie Sie eine DNN-Ressource für eine [Failoverclusterinstanz](failover-cluster-instance-distributed-network-name-dnn-configure.md) oder für eine [Verfügbarkeitsgruppe](availability-group-distributed-network-name-dnn-listener-configure.md) konfigurieren.

**Unterstütztes Betriebssystem**: Windows Server 2016 oder höher   
**Unterstützte SQL-Version**: SQL Server 2019 CU2 (FCI) und SQL Server 2019 CU8 (Verfügbarkeitsgruppe)   
**Unterstützte HADR-Lösung**: Failoverclusterinstanz und Verfügbarkeitsgruppe   


## <a name="limitations"></a>Einschränkungen

Beachten Sie die folgenden Einschränkungen, wenn Sie mit FCIs oder Verfügbarkeitsgruppen und SQL Server auf Azure Virtual Machines arbeiten. 

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines unterstützt Microsoft Distributed Transaction Coordinator (MSDTC) unter Windows Server 2019 mit Speicher auf freigegebenen Clustervolumes (CSV) und mit [Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md) oder auf SQL Server-VMs, die freigegebene Azure-Datenträger verwenden. 

In Azure Virtual Machines wird MSDTC unter Windows Server 2016 und früheren Versionen mit freigegebenen Clustervolumes aus den folgenden Gründen nicht unterstützt:

- Die MSDTC-Clusterressource kann nicht für die Verwendung von freigegebenem Speicher konfiguriert werden. Wenn Sie unter Windows Server 2016 eine MSDTC-Ressource erstellen, wird kein freigegebener Speicher für die Verwendung angezeigt, selbst wenn der Speicher verfügbar ist. Dieses Problem wurde in Windows Server 2019 behoben.
- Der einfache Lastenausgleich verarbeitet keine RPC-Ports.


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die geeigneten bewährten Methoden für Ihre Lösung ermittelt haben, können Sie [Ihren virtuellen SQL Server-Computer für eine FCI vorbereiten](failover-cluster-instance-prepare-vm.md) oder Ihre Verfügbarkeitsgruppe über das [Azure-Portal](availability-group-azure-portal-configure.md), per [Azure-Befehlszeilenschnittstelle/PowerShell](./availability-group-az-commandline-configure.md) oder mithilfe von [Azure-Schnellstartvorlagen](availability-group-quickstart-template-configure.md) erstellen.