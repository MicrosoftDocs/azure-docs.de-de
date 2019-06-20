---
title: SQL Server-Verfügbarkeitsgruppen – Azure Virtual Machines – Übersicht | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Einführung in SQL Server-Verfügbarkeitsgruppen auf virtuellen Azure-Computern.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325810"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern #

Dieser Artikel enthält eine Einführung in SQL Server-Verfügbarkeitsgruppen in Azure Virtual Machines. 

Always On-Verfügbarkeitsgruppen in Azure Virtual Machines sind vergleichbar mit lokalen Always On-Verfügbarkeitsgruppen. Weitere Informationen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppen (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Das Diagramm veranschaulicht die Komponenten einer vollständigen SQL Server-Verfügbarkeitsgruppe in Azure Virtual Machines.

![Verfügbarkeitsgruppe](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Der wesentliche Unterschied bei einer Verfügbarkeitsgruppe in Azure Virtual Machines ist, dass die virtuellen Azure-Computer einen [Lastenausgleich](../../../load-balancer/load-balancer-overview.md) erfordern. Der Lastenausgleich speichert die IP-Adressen für den Verfügbarkeitsgruppenlistener. Falls Sie über mehrere Verfügbarkeitsgruppen verfügen, benötigt jede Gruppe einen Listener. Ein einzelner Lastenausgleich kann mehrere Listener unterstützen.

Außerdem werden in einem Azure IaaS-VM-Gast-Failovercluster eine einzelne Netzwerkkarte pro Server (Clusterknoten) und ein einzelnes Subnetz empfohlen. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten und Subnetze in einem Azure IaaS-VM-Gastcluster überflüssig macht. Obwohl im Clustervalidierungsbericht eine Warnung ausgegeben wird, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind, kann diese Warnung für Azure IaaS-VM-Gast-Failovercluster einfach ignoriert werden. 

|  | Windows Server-Version | SQL Server-Version | SQL Server-Edition | WSFC-Quorumkonfiguration | Notfallwiederherstellung mit mehreren Regionen | Unterstützung mehrerer Subnetze | Unterstützung für ein vorhandenes AD | Notfallwiederherstellung mit mehreren Zonen in derselben Region | Dist-AG-Unterstützung ohne AD-Domäne | Dist-AG-Unterstützung ohne Cluster |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL-VM-Befehlszeilenschnittstelle](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | Cloudzeuge | Nein | Ja | Ja | Ja | Nein | Nein |
| [Schnellstartvorlagen](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | Cloudzeuge | Nein | Ja | Ja | Ja | Nein | Nein |
| [Vorlage im Portal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | Dateifreigabe | Nein | Nein | Nein | Nein | Nein | Nein |
| [Manuell](virtual-machines-windows-portal-sql-availability-group-prereq.md) | Alle | Alle | Alle | Alle | Ja | Ja | Ja | Ja | Ja | Ja |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Wenn Sie zur Erstellung einer SQL Server-Verfügbarkeitsgruppe in Azure Virtual Machines bereit sind, sehen Sie sich die folgenden Tutorials an:

## <a name="manually-with-azure-cli"></a>Manuell über die Azure-Befehlszeilenschnittstelle
Die empfohlene Option zum Konfigurieren und Bereitstellen einer Verfügbarkeitsgruppe ist die Azure-Befehlszeilenschnittstelle, da diese im Hinblick auf Einfachheit und Geschwindigkeit am besten geeignet ist. Mit der Azure-Befehlszeilenschnittstelle können das Erstellen des Windows-Failoverclusters, das Einbinden der SQL Server-VMs in den Cluster und das Erstellen des Listeners und des internen Lastenausgleichs in weniger als insgesamt 30 Minuten erreicht werden. Diese Option erfordert immer noch eine manuelle Erstellung der Verfügbarkeitsgruppe, alle anderen erforderlichen Schritte für die Konfiguration sind jedoch automatisiert. 

Weitere Informationen finden Sie unter [Verwenden der Azure SQL-VM-Befehlszeilenschnittstelle zum Konfigurieren von Always On-Verfügbarkeitsgruppe für SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-sql-availability-group-cli.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatisch mit Azure-Schnellstartvorlagen
In Azure-Schnellstartvorlagen wird der SQL-VM-Ressourcenanbieter genutzt, um den Windows-Failovercluster zu erstellen, SQL Server-VMs in diesen einzubinden, den Listener zu erstellen und den internen Lastenausgleich zu konfigurieren. Diese Option erfordert immer noch eine manuelle Erstellung der Verfügbarkeitsgruppe und des internen Lastenausgleichs, alle anderen erforderlichen Konfigurationsschritte (einschließlich der Konfiguration des internen Lastenausgleichs) sind jedoch automatisiert und vereinfacht. 

Weitere Informationen finden Sie unter [Verwenden einer Azure-Schnellstartvorlage zum Konfigurieren von Always On-Verfügbarkeitsgruppen für SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatisch mit einer Azure-Portal-Vorlage

[Automatisches Konfigurieren der AlwaysOn-Verfügbarkeitsgruppe auf virtuellen Azure-Computern – Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Manuell im Azure-Portal

Sie können die virtuellen Computer auch manuell ohne Vorlage erstellen. Schließen Sie zunächst die erforderlichen Vorbereitungen ab, und erstellen Sie anschließend die Verfügbarkeitsgruppe. Informationen hierzu finden Sie in den folgenden Themen: 

- [Configure prerequisites for SQL Server Always On availability groups on Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md) (Konfigurieren der erforderlichen Komponenten für SQL Server Always On-Verfügbarkeitsgruppen in Azure Virtual Machines)

- [Erstellen einer AlwaysOn-Verfügbarkeitsgruppe zur Verbesserung der Verfügbarkeit und Notfallwiederherstellung](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren einer SQL Server AlwaysOn-Verfügbarkeitsgruppe auf virtuellen Azure-Computern in verschiedenen Regionen](virtual-machines-windows-portal-sql-availability-group-dr.md)
