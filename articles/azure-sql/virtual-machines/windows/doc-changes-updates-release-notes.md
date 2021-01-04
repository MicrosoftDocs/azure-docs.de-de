---
title: Dokumentationsänderungen für SQL Server auf virtuellen Azure-Computern
description: Hier finden Sie Informationen zu den neuen Features und Verbesserungen für verschiedene Releases von SQL Server für Azure Virtual Machines.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: ff4e6e0451b57046fb8f07f5a1051235e1f6d0f5
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325722"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Dokumentationsänderungen für SQL Server auf virtuellen Azure-Computern
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In Azure können Sie einen virtuellen Computer (VM) mit einem integrierten SQL Server-Image bereitstellen. In diesem Artikel werden die Dokumentationsänderungen zusammengefasst, die mit neuen Funktionen und Verbesserungen in den neuesten Releases von [SQL Server in Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) in Zusammenhang stehen. 

## <a name="october-2020"></a>Oktober 2020

| Änderungen | Details |
| --- | --- |
| **DNN für Verfügbarkeitsgruppe** | Sie können jetzt einen [DNN-Listener (Distributed Network Name, Name des verteilten Netzwerks)](availability-group-distributed-network-name-dnn-listener-configure.md) für SQL Server 2019 CU8 und höher als Ersatz für den herkömmlichen [VNN-Listener](availability-group-overview.md#connectivity) konfigurieren, so dass ein Azure Load Balancer nicht mehr erforderlich ist.   | 

## <a name="september-2020"></a>September 2020

| Änderungen | Details |
| --- | --- |
| **Automatische Erweiterungsregistrierung** | Sie können jetzt die Funktion zur [automatischen Registrierung](sql-agent-extension-automatic-registration-all-vms.md) aktivieren, um alle bereits in Ihrem Abonnement bereitgestellten SQL Server-VMs mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) automatisch zu registrieren. Dies gilt für alle vorhandenen VMs, und dadurch werden auch alle SQL Server-VMs automatisch registriert, die zu einem späteren Zeitpunkt hinzugefügt werden.   | 


## <a name="august-2020"></a>August 2020

| Änderungen | Details |
| --- | --- |
| **Konfigurieren von Verfügbarkeitsgruppen im Portal** | Es ist jetzt möglich, [Verfügbarkeitsgruppen über das Azure-Portal zu konfigurieren](availability-group-azure-portal-configure.md). Dieses Feature befindet sich in der Vorschauphase und wird derzeit bereitgestellt. Wenn Ihre gewünschte Region nicht verfügbar ist, überprüfen Sie sie in Kürze erneut. | 


## <a name="july-2020"></a>Juli 2020


| Änderungen | Details |
| --- | --- |
| **Migrieren von Protokollen zu Ultra Disks** | Erfahren Sie, wie Sie [Ihre Protokolldatei zu Ultra Disks migrieren](storage-migrate-to-ultradisk.md), um von hoher Leistung und niedriger Latenz zu profitieren. | 
| **Erstellen einer Verfügbarkeitsgruppe mithilfe von Azure PowerShell** | Die Erstellung einer Verfügbarkeitsgruppe kann jetzt durch die Verwendung von [Azure PowerShell](availability-group-az-commandline-configure.md) und Azure CLI vereinfacht werden. | 


## <a name="june-2020"></a>Juni 2020

| Änderungen | Details |
| --- | --- |
| **Verteilter Netzwerkname (Distributed Network Name, DNN)** | In SQL Server 2019 unter Windows Server 2016 und höheren Versionen wird nun die Weiterleitung von Datenverkehr an Ihre Failoverclusterinstanz (FCI) mithilfe eines [verteilten Netzwerknamens](./failover-cluster-instance-distributed-network-name-dnn-configure.md) (anstelle von Azure Load Balancer) als Vorschau bereitgestellt. Diese Unterstützung vereinfacht und optimiert die Verbindungsherstellung mit Ihrer HA-Lösung (High-Availability-Lösung, Hochverfügbarkeitslösung) in Azure. | 
| **FCI mit freigegebenen Azure-Datenträgern** | Ihre [Failoverclusterinstanz (FCI)](failover-cluster-instance-overview.md) kann nun mithilfe [freigegebener Azure-Datenträger](failover-cluster-instance-azure-shared-disks-manually-configure.md) bereitgestellt werden. |
| **Neu strukturierte FCI-Dokumentation** | Die Dokumentation zu [Failoverclusterinstanzen mit SQL Server auf virtuellen Azure-Computern](failover-cluster-instance-overview.md) wurde umgeschrieben und neu strukturiert, um sie besser verständlich zu machen. Wir haben einige Konfigurationsinhalte wie etwa die [bewährten Methoden für die Clusterkonfiguration](hadr-cluster-best-practices.md), die Vorbereitung eines [virtuellen Computers für eine SQL Server-FCI](failover-cluster-instance-prepare-vm.md) und die Konfiguration von [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md) getrennt. | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Mai 2020 

| Änderungen | Details |
| --- | --- |
| **Azure SQL-Familie** | SQL Server auf virtuellen Azure-Computern ist nun Teil der [Azure SQL-Produktfamilie](../../azure-sql-iaas-vs-paas-what-is-overview.md). Sehen Sie sich die [neue Optik](../index.yml) an! Im Produkt hat sich nichts geändert, aber die Dokumentation soll Azure SQL-Produktentscheidungen einfacher machen. | 


## <a name="january-2020"></a>Januar 2020

| Änderungen | Details |
| --- | --- |
| **Azure Government-Support** | Virtuelle SQL Server-Computer können jetzt mit der SQL-IaaS-Agent-Erweiterung für virtuelle Computer registriert werden, die in der [Azure Government-Cloud](https://azure.microsoft.com/global-infrastructure/government/) gehostet werden. | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Änderungen | Details |
 --- | --- |
| **Kostenloses DR-Replikat in Azure** | Sie können eine [kostenlose passive Instanz](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) für die Notfallwiederherstellung in Azure für Ihre lokale SQL Server-Instanz hosten, wenn Sie über [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)verfügen. | 
| **Registrierung von VMs mit der SQL-IaaS-Erweiterung in einem Massenvorgang** | Sie können jetzt virtuelle SQL Server-Computer mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) per [Massenregistrierung](sql-agent-extension-manually-register-vms-bulk.md) registrieren. | 
|**Leistungsoptimierte Speicherkonfiguration** | Sie ist nun möglich, Ihre [Speicherkonfiguration vollständig anzupassen](storage-configuration.md#new-vms), wenn Sie eine neue SQL Server-VM erstellen. |
|**Premium-Dateifreigabe für FCI** | Sie können nun eine Failoverclusterinstanz mit einer [Premium-Dateifreigabe](failover-cluster-instance-premium-file-share-manually-configure.md) erstellen, anstatt [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) (die ursprüngliche Methode) zu verwenden. 
| **Azure Dedicated Host** | Sie können Ihren virtuellen SQL Server-Computer in [Azure Dedicated Host](dedicated-host.md) ausführen. | 
| **Migration eines virtuellen SQL Server-Computers zu einer anderen Region** | Verwenden Sie Azure Site Recovery, um [Ihre SQL Server-VM aus einer Region in eine andere zu migrieren](move-sql-vm-different-region.md). |
|  **Neue SQL-IaaS-Installationsmodi** | Die SQL Server-IaaS-Erweiterung kann nun im [Lightweight-Modus](sql-server-iaas-agent-extension-automate-management.md) installiert werden, um den SQL Server-Dienst nicht neu starten zu müssen.  |
| **Änderung der SQL Server-Edition** | Sie können nun die [Editionseigenschaft](change-sql-server-edition.md) für Ihren virtuellen SQL Server-Computer ändern. |
| **Änderungen an der SQL-IaaS-Agent-Erweiterung** | Sie können [Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren](sql-agent-extension-manually-register-single-vm.md) und dabei die neuen SQL-IaaS-Modi verwenden. Diese Möglichkeit umfasst auch Images mit [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes).|
| **Bring-Your-Own-License-Images mit dem Azure-Hybridvorteil** | Bei über den Azure Marketplace bereitgestellten Bring-Your-Own-License-Images kann der [Lizenztyp nun in nutzungsbasierte Bezahlung](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks) geändert werden.| 
| **Neue Verwaltung virtueller SQL Server-Computer im Azure-Portal** | Es gibt jetzt eine Methode, mit der Sie Ihre SQL Server-VM im Azure-Portal verwalten können. Weitere Informationen finden Sie unter [Verwalten von SQL Server-VMs über das Azure-Portal](manage-sql-vm-portal.md).  | 
| **Erweiterte Unterstützung von SQL Server 2008 und 2008 R2** | [Erweiterung der Unterstützung](sql-server-2008-extend-end-of-support.md) für SQL Server 2008 und SQL Server 2008 R2 durch *unverändertes* Migrieren zu einer Azure-VM. | 
| **Unterstützung von benutzerdefinierten Images** | Sie können nun die [SQL Server-IaaS-Erweiterung](sql-server-iaas-agent-extension-automate-management.md#installation) für benutzerdefinierte Betriebssystem- und SQL Server-Images installieren, die die eingeschränkte Funktionalität der [flexiblen Lizenzierung](licensing-model-azure-hybrid-benefit-ahb-change.md) bietet. Geben Sie den Lizenztyp „AHUB“ an, wenn Sie Ihr benutzerdefiniertes Image mit der SQL-IaaS-Agent-Erweiterung registrieren. Andernfalls tritt bei der Registrierung ein Fehler auf. | 
| **Unterstützung für benannte Instanzen** | Sie können jetzt die [SQL Server-IaaS-Erweiterung](sql-server-iaas-agent-extension-automate-management.md#installation) mit einer benannten Instanz verwenden, wenn die Standardinstanz ordnungsgemäß deinstalliert wurde. | 
| **Portalerweiterung** | Die Azure-Portal-Benutzeroberfläche zur Bereitstellung einer SQL Server-VM wurde überarbeitet, um die Benutzerfreundlichkeit zu verbessern. Weitere Informationen finden Sie in Kurzform im [Schnellstart](sql-vm-create-portal-quickstart.md) und ausführlicher in der [Schrittanleitung](create-sql-vm-portal.md) zum Bereitstellen einer SQL Server-VM.|
| **Verbesserung beim Portal** | Es ist jetzt möglich, mithilfe des [Azure-Portals](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model) das Lizenzierungsmodell für einen virtuellen SQL Server-Computer von nutzungsbasierter Bezahlung in Bring-Your-Own-License zu ändern.|
| **Vereinfachung der Bereitstellung von Verfügbarkeitsgruppen auf einem virtuellen SQL Server-Computer über die Azure-Befehlszeilenschnittstelle** | Es ist jetzt einfacher denn je, eine Verfügbarkeitsgruppe auf einer SQL Server-VM in Azure bereitzustellen. Sie können die [Azure-Befehlszeilenschnittstelle](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) verwenden, um den Windows-Failovercluster, den internen Lastenausgleich und die Verfügbarkeitsgruppenlistener über die Befehlszeile zu erstellen. Weitere Informationen finden Sie unter [Konfigurieren einer Always On-Verfügbarkeitsgruppe für SQL Server auf einem virtuellen Azure-Computer mithilfe der Azure CLI](./availability-group-az-commandline-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Änderungen | Details |
| --- | --- |
|  **Neuer Ressourcenanbieter für SQL Server-Cluster** | Ein neuer Ressourcenanbieter (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definiert die Metadaten des Windows-Failoverclusters. Ein Einbinden einer SQL Server-VM in die *SqlVirtualMachineGroups* startet den Windows Server-Failovercluster-Dienst (WSFC) und verknüpft den virtuellen Computer mit dem Cluster.  |
| **Automatisiertes Einrichten einer Verfügbarkeitsgruppenbereitstellung mit Azure-Schnellstartvorlagen** |Es ist jetzt möglich, mit zwei Azure-Schnellstartvorlagen den Windows-Failovercluster zu erstellen, virtuelle SQL Server-Computer einzubinden, den Listener zu erstellen und den internen Lastenausgleich zu konfigurieren. Weitere Informationen finden Sie unter [Verwenden von Azure-Schnellstartvorlagen zum Konfigurieren von Always On-Verfügbarkeitsgruppen für SQL Server auf einem virtuellen Azure-Computer](availability-group-quickstart-template-configure.md). | 
| **Automatische Registrierung von VMs mit der SQL-IaaS-Agent-Erweiterung** | Nach diesem Monat bereitgestellte SQL Server-VMs werden automatisch mit der neuen SQL-IaaS-Agent-Erweiterung registriert. SQL Server-VMs, die vor diesem Monat bereitgestellt wurden, müssen weiterhin manuell registriert werden. Weitere Informationen finden Sie unter [Registrieren von virtuellen SQL Server-Computern in Azure mit der SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md).|
|**Neue SQL-IaaS-Agent-Erweiterung** |  Ein neuer Ressourcenanbieter (Microsoft.SqlVirtualMachine) bietet eine bessere Verwaltung Ihrer SQL Server-VMs. Weitere Informationen zum Registrieren Ihrer VMs finden Sie unter [Registrieren von virtuellen SQL Server-Computern in Azure mit der SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md). |
|**Wechsel des Lizenzierungsmodells** | Sie können nun mit der Azure-Befehlszeilenschnittstelle oder PowerShell für Ihre SQL Server-VM zwischen dem nutzungsbasierten Modell und dem Bring-Your-Own-License-Modell wechseln. Weitere Informationen finden Sie unter [Ändern des Lizenzierungsmodells für einen virtuellen SQL Server-Computer in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

**Virtuelle Windows-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](create-sql-vm-portal.md)
* [Migrieren einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md)
* [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Bewährte Methoden für die Leistung von SQL Server auf virtuellen Azure-Computern](performance-guidelines-best-practices.md)
* [Anwendungsmuster und Entwicklungsstrategien für SQL Server auf virtuellen Azure-Computern](application-patterns-development-strategies.md)

**Virtuelle Linux-Computer:**

* [Übersicht über SQL Server auf einem virtuellen Linux-Computer](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Bereitstellen eines virtuellen SQL Server-Computers über das Azure-Portal](../linux/sql-vm-create-portal-quickstart.md)
* [Häufig gestellte Fragen (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server unter Linux – Dokumentation](/sql/linux/sql-server-linux-overview)