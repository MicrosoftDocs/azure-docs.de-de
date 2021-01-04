---
title: Übersicht über SQL Server Always On-Verfügbarkeitsgruppen
description: Dieser Artikel bietet eine Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: f39380e253d3fa9e86bfea3a8c436862738ff8e3
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359930"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Always On-Verfügbarkeitsgruppe für SQL Server auf Azure-VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Dieser Artikel bietet eine Einführung in Always On-Verfügbarkeitsgruppen für SQL Server auf Azure Virtual Machines (VMs). 

## <a name="overview"></a>Übersicht

Always On-Verfügbarkeitsgruppen auf Azure Virtual Machines sind vergleichbar mit [lokalen Always On-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Da die virtuellen Computer jedoch in Azure gehostet werden, gibt es auch einige zusätzliche Überlegungen, wie z. B. VM-Redundanz und das Routing von Datenverkehr im Azure-Netzwerk. 

Das folgende Diagramm veranschaulicht eine Verfügbarkeitsgruppe für SQL Server auf Azure-VMs:

![Verfügbarkeitsgruppe](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM-Redundanz 

Zur Verbesserung der Redundanz und Verfügbarkeit sollten sich die virtuellen SQL Server-Computer entweder in der gleichen [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview) oder in unterschiedlichen [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) befinden.

Die Platzierung mehrerer virtueller Computer in der gleichen Verfügbarkeitsgruppe dient zum Schutz vor Ausfällen in einem Rechenzentrum infolge von Hardwarefehlern, da virtuelle Computer in einer Verfügbarkeitsgruppe nicht die gleichen Ressourcen verwenden, sowie vor Ausfällen infolge von Updates, da virtuelle Computer innerhalb einer Verfügbarkeitsgruppe nicht gleichzeitig aktualisiert werden. Verfügbarkeitszonen schützen vor dem Ausfall eines gesamten Rechenzentrums. Die einzelnen Zonen stellen jeweils eine Gruppe von Rechenzentren innerhalb einer Region dar.  Durch die Platzierung von Ressourcen in unterschiedlichen Verfügbarkeitszonen wird sichergestellt, dass durch einen Ausfall auf Rechenzentrumsebene nicht alle Ihre virtuellen Computer offline geschaltet werden.

Bei der VM-Erstellung müssen Sie sich entscheiden, ob Sie Verfügbarkeitsgruppen oder Verfügbarkeitszonen konfigurieren möchten.  Ein virtueller Azure-Computer kann nicht mit beidem konfiguriert werden.


## <a name="connectivity"></a>Konnektivität 

Bei einer herkömmlichen lokalen Bereitstellung stellen Clients über den Namen des virtuellen Netzwerks (VNN) eine Verbindung mit dem Verfügbarkeitsgruppenlistener her, und der Listener leitet den Datenverkehr an das geeignete SQL Server-Replikat in der Verfügbarkeitsgruppe weiter. Beim Routing des Datenverkehrs im Azure-Netzwerk muss eine zusätzliche Voraussetzung erfüllt werden. 

Konfigurieren Sie bei SQL Server auf Azure-VMs einen [Lastenausgleich](availability-group-vnn-azure-load-balancer-configure.md) zum Weiterleiten von Datenverkehr an den Verfügbarkeitsgruppenlistener. Unter SQL Server 2019 CU8 und höher können Sie alternativ einen [DNN-Listener](availability-group-distributed-network-name-dnn-listener-configure.md) (Distributed Network Name, Name des verteilten Netzwerks) konfigurieren, um den herkömmlichen VNN-Verfügbarkeitsgruppenlistener zu ersetzen. 


### <a name="vnn-listener"></a>VNN-Listener 

Verwenden Sie einen [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md), um Datenverkehr vom Client an den herkömmlichen VNN-Listener (Virtual Network Name, Name des virtuellen Netzwerks) der Verfügbarkeitsgruppe im Azure-Netzwerk weiterzuleiten. 

Der Lastenausgleich enthält die IP-Adressen für den VNN-Listener. Falls Sie über mehrere Verfügbarkeitsgruppen verfügen, benötigt jede Gruppe einen VNN-Listener. Ein einzelner Lastenausgleich kann mehrere Listener unterstützen.

Informationen zu den ersten Schritten finden Sie unter [Konfigurieren eines Lastenausgleichs](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>DNN-Listener

Ab SQL Server 2019 CU8 wird der DNN-Listener unterstützt. Der DNN-Listener ersetzt den herkömmlichen Verfügbarkeitsgruppenlistener. So ist es nicht mehr erforderlich, dass der Datenverkehr im Azure-Netzwerk durch einen Azure Load Balancer weitergeleitet wird. 

Der DNN-Listener ist die empfohlene HADR-Konnektivitätslösung in Azure, da sie die Bereitstellung vereinfacht, den Wartungs- und Kostenaufwand reduziert und die Failoverzeit bei einem Ausfall reduziert. 

Verwenden Sie den DNN-Listener, um einen vorhandenen VNN-Listener zu ersetzen, oder verwenden Sie ihn alternativ in Verbindung mit einem vorhandenen VNN-Listener, um Ihre Verfügbarkeitsgruppe mit zwei unterschiedlichen Verbindungspunkten auszustatten: einen, der den VNN-Listenernamen (und Port, falls nicht der Standardport) nutzt, und einen, der den DNN-Listenernamen und entsprechenden Port nutzt. Dies kann für Kunden hilfreich sein, die Latenzzeiten durch Lastenausgleichsfailover vermeiden möchten, aber trotzdem die Vorteile von SQL Server-Funktionen nutzen möchten, die vom VNN-Listener abhängig sind, beispielsweise verteilte Verfügbarkeitsgruppen, Service Broker oder Filestream. Weitere Informationen finden Sie im Thema zur [Interoperabilität zwischen DNN-Listener- und SQL Server-Funktionen](availability-group-dnn-interoperability.md).

Informationen zu den ersten Schritten finden Sie unter [Konfigurieren eines DNN-Listeners](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Bereitstellung 

Es gibt mehrere Optionen für die Bereitstellung einer Verfügbarkeitsgruppe für SQL Server auf Azure-VMS. Dabei verfügen einige über einen höheren Automatisierungsgrad als andere. 

Die folgende Tabelle bietet einen Vergleich der verfügbaren Optionen:

| | Azure-Portal | Azure CLI/PowerShell | Schnellstartvorlagen | Manuell |
|---------|---------|---------|---------|---------|
|**SQL Server-Version** |2016 + |2016 +|2016 +|2012 +|
|**SQL Server-Edition** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Windows Server-Version**| 2016 + | 2016 + | 2016 + | All|
|**Der Cluster wird für Sie erstellt.**|Ja|Ja | Ja |Nein|
|**Die Verfügbarkeitsgruppe wird für Sie erstellt.** |Ja |Nein|Nein|Nein|
|**Der Listener und Lastenausgleich werden unabhängig voneinander erstellt.** |Nein|Nein|Nein|Ja|
|**Kann mit dieser Methode ein DNN-Listener erstellt werden?**|Nein|Nein|Nein|Ja|
|**WSFC-Quorumkonfiguration**|Cloudzeuge|Cloudzeuge|Cloudzeuge|All|
|**DR mit mehreren Regionen** |Nein|Nein|Nein|Ja|
|**Unterstützung mehrerer Subnetze** |Ja|Ja|Ja|Ja|
|**Unterstützung für ein vorhandenes AD**|Ja|Ja|Ja|Ja|
|**DR mit mehreren Zonen in derselben Region**|Ja|Ja|Ja|Ja|
|**Verteilte AG ohne AD**|Nein|Nein|Nein|Ja|
|**Verteilte AG ohne Cluster** |Nein|Nein|Nein|Ja|

Weitere Informationen finden Sie unter [Azure-Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [Schnellstartvorlagen](availability-group-quickstart-template-configure.md) und [Leitfaden](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Überlegungen 

In einem Azure IaaS-VM-Gast-Failovercluster werden eine einzelne Netzwerkkarte pro Server (Clusterknoten) und ein einzelnes Subnetz empfohlen. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten und Subnetze in einem Azure IaaS-VM-Gastcluster überflüssig macht. Obwohl im Clustervalidierungsbericht eine Warnung ausgegeben wird, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind, kann diese Warnung für Azure IaaS-VM-Gast-Failovercluster einfach ignoriert werden. 

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Best Practices für HADR](hadr-cluster-best-practices.md), und beginnen Sie dann mit der Bereitstellung Ihrer Verfügbarkeitsgruppe über das [Azure-Portal](availability-group-azure-portal-configure.md), die [Azure CLI oder PowerShell](./availability-group-az-commandline-configure.md), [Schnellstartvorlagen](availability-group-quickstart-template-configure.md) oder die [manuelle](availability-group-manually-configure-prerequisites-tutorial.md) Bereitstellung.

Alternativ können Sie eine [Verfügbarkeitsgruppe ohne Cluster](availability-group-clusterless-workgroup-configure.md) oder eine Verfügbarkeitsgruppe [in mehreren Regionen](availability-group-manually-configure-multiple-regions.md) bereitstellen.
