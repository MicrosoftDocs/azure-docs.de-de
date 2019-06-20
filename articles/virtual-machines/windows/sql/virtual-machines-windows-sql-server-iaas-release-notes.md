---
title: SQL Server auf Azure-VMs – Versionshinweise | Microsoft-Dokumentation
description: Erfahren Sie mehr über die neuen Funktionen und Verbesserungen von SQL Server auf Azure-VMs.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: f929369030b6f0a634eb2c84e33eee24af8cb6a2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787509"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>SQL Server auf Azure-VMs – Versionshinweise

Mit Azure können Sie einen virtuellen Computer (VM) mit einem integrierten SQL Server-Image bereitstellen. In diesem Artikel sind die neuen Funktionen und Verbesserungen in den neuesten Versionen von [SQL Server auf virtuellen Azure-Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/) zusammengefasst. Der Artikel enthält zudem wichtige Inhaltsaktualisierungen, die nicht in direktem Zusammenhang mit einer Version stehen, aber im gleichen Zeitraum veröffentlicht werden. Informationen zu Verbesserungen bei anderen Azure-Diensten finden Sie unter [Dienstupdates](https://azure.microsoft.com/updates).

## <a name="april-2019"></a>April 2019

### <a name="service-improvements"></a>Verbesserungen beim Dienst

| Verbesserungen beim Dienst | Details |
| --- | --- |
| **Erweitern der Unterstützung für SQL Server 2008/2008 R2** | [Erweiterung der Unterstützung](virtual-machines-windows-sql-server-2008-eos-extend-support.md) für SQL Server 2008 und SQL Server 2008 R2 durch *unverändertes* Migrieren zu einer Azure-VM. | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Verbesserungen bei der Dokumentation

Keine

## <a name="march-2019"></a>März 2019

| Verbesserungen beim Dienst | Details |
| --- | --- |
| **Unterstützung von benutzerdefinierten Images** | Sie können jetzt die [SQL-IaaS-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md#installation) für benutzerdefinierte Betriebssystem- und SQL-Images installieren, die die eingeschränkte Funktionalität [flexibler Lizenzierung](virtual-machines-windows-sql-ahb.md) bietet. Geben Sie bei der Registrierung Ihres benutzerdefinierten Images beim SQL-Ressourcenanbieter Ihren Lizenztyp als ‚AHUB‘ an, da es andernfalls zu einem Fehler bei der Registrierung kommt.  | 
| **Unterstützung für benannte Instanzen** | Sie können jetzt die [SQL-IaaS-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md#installation) mit einer benannten Instanz verwenden, wenn die Standardinstanz ordnungsgemäß deinstalliert wurde. | 
| **Portalerweiterung** | Die Azure-Portal-Benutzeroberfläche zur Bereitstellung einer SQL Server-VM wurde überarbeitet, um die Benutzerfreundlichkeit zu verbessern. Weitere Informationen finden Sie im kurzen [Schnellstart](quickstart-sql-vm-create-portal.md) und – ausführlicher – in der [Schrittanleitung](virtual-machines-windows-portal-sql-server-provision.md) zum Bereitstellen einer SQL Server-VM.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Februar 2019

| Verbesserungen beim Dienst | Details |
| --- | --- |
| **Verbesserung beim Portal** | Es ist jetzt möglich, mithilfe des [Azure-Portals](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1) das Lizenzierungsmodell für eine SQL Server-VM von nutzungsbasierter Bezahlung in Bring-Your-Own-License zu ändern.|
|**Vereinfachte Bereitstellung von Verfügbarkeitsgruppen mithilfe von Azure SQL VM CLI** | Es ist jetzt einfacher denn je, eine Verfügbarkeitsgruppe auf einer SQL Server-VM in Azure bereitzustellen. [Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) ermöglicht Ihnen die Erstellung des WSFCs, des ILBs und des Verfügbarkeitsgruppenlisteners in Rekordzeit auf der Befehlszeile! Weitere Informationen finden Sie unter [Verwenden der Azure SQL-VM-Befehlszeilenschnittstelle zum Konfigurieren von Always On-Verfügbarkeitsgruppe für SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Dezember 2018

| Verbesserungen beim Dienst | Details |
| --- | --- |
| **Neuer Ressourcenanbieter für SQL-Clustergruppen** | Ein neuer Ressourcenanbieter (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups), der die Metadaten des Windows-Failoverclusters definiert. Ein Einbinden einer SQL Server-VM in die *SqlVirtualMachineGroups* startet den Windows-Failovercluster-Dienst und verknüpft den virtuellen Computer mit dem Cluster.  |
|**Automatisieren des Einrichtens einer Verfügbarkeitsgruppenbereitstellung mit Azure-Schnellstartvorlagen** |Es ist jetzt möglich, mit zwei Azure-Schnellstartvorlagen den Windows-Failovercluster zu erstellen, SQL Server-VMs in diesen einzubinden, den Listener zu erstellen und den internen Load Balancer zu konfigurieren. Weitere Informationen finden Sie unter [Verwenden einer Azure-Schnellstartvorlage zum Konfigurieren von Always On-Verfügbarkeitsgruppen für SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatische Registrierung beim SQL Server-Ressourcenanbieter** | SQL Server-VMs, die nach diesem Monat bereitgestellt werden, werden automatisch beim neuen SQL Server-Ressourcenanbieter registriert. SQL Server-VMs, die vor diesem Monat bereitgestellt wurden, müssen weiterhin manuell registriert werden. Weitere Informationen finden Sie unter [Registrieren vorhandener SQL-VMs mit dem SQL-VM-Ressourcenanbieter](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>November 2018

| Verbesserungen beim Dienst | Details |
| --- | --- |
| **Neuer SQL-VM-Ressourcenanbieter** |  Ein neuer Ressourcenanbieter für SQL Server-VMs (Microsoft.SqlVirtualMachine), der eine bessere Verwaltung Ihrer SQL Server-VM ermöglicht. Weitere Informationen zum Registrieren Ihrer VM finden Sie unter [Registrieren vorhandener SQL-VMs beim neuen Ressourcenanbieter](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). |
|**Wechsel des Lizenzierungsmodells** |Sie können nun mit der Azure CLI oder mit PowerShell für Ihre SQL-VM zwischen dem nutzungsbasierten Modell und dem BYOL-Modell (Bring-Your-Own-License) wechseln. Weitere Informationen finden Sie unter [Ändern des Lizenzierungsmodells für einen virtuellen SQL-Computer](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Zusätzliche Ressourcen

**Virtuelle Windows-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](virtual-machines-windows-sql-server-iaas-overview.md)
* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-windows-migrate-sql.md)
* [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md)
* [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md)
* [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Virtuelle Linux-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Linux-Computer](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Bereitstellen eines virtuellen Linux-Computers mit SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Häufig gestellte Fragen (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server unter Linux – Dokumentation](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
