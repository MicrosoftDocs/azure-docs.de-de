---
title: Vorbereiten virtueller Computer für eine Failoverclusterinstanz
description: Bereiten Sie Ihre virtuellen Azure-Computer für die Verwendung mit einer Failoverclusterinstanz (FCI) und SQL Server unter Azure Virtual Machines vor.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: a9289fad6f7ae1030628bedcf1a62cacc0b1e23a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564470"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Vorbereiten virtueller Computer für eine FCI (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie virtuelle Azure-Computer (VMs) für den Einsatz mit einer SQL Server-Failoverclusterinstanz (FCI) vorbereiten können. Die Konfigurationseinstellungen hängen von der FCI-Speicherlösung ab. Vergewissern Sie sich daher, dass Sie die für Ihre Umgebung und Ihr Unternehmen richtige Konfiguration wählen. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Voraussetzungen 

- Ein Microsoft Azure-Abonnement [Kostenlos](https://azure.microsoft.com/free/) einsteigen 
- Eine Windows-Domäne auf virtuellen Azure-Computern oder ein um Azure erweitertes lokales Rechenzentrum mit Kopplung virtueller Netzwerke.
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- Ein virtuelles Azure-Netzwerk und ein Subnetz mit einem ausreichend großen IP-Adressraum für die folgenden Komponenten:
   - Beide virtuellen Computer
   - Die IP-Adresse des Windows-Failoverclusters
   - Eine IP-Adresse für jede FCI
- DNS-Konfiguration im Azure-Netzwerk mit Verweis auf die Domänencontroller

## <a name="choose-an-fci-storage-option"></a>Wählen einer FCI-Speicheroption

Die Konfigurationseinstellungen für Ihren virtuellen Computer variieren je nach der Speicheroption, die Sie für Ihre SQL Server-Failoverclusterinstanz nutzen möchten. Bevor Sie den virtuellen Computer vorbereiten, prüfen Sie die [verfügbaren FCI-Speicheroptionen](failover-cluster-instance-overview.md#storage). Wählen Sie die Option, die am besten zu Ihrer Umgebung und Ihren Geschäftsanforderungen passt. Wählen Sie dann im Verlauf dieses Artikels auf Grundlage Ihrer Speicherwahl die passenden VM-Konfigurationsoptionen sorgfältig aus. 

## <a name="configure-vm-availability"></a>Konfigurieren der VM-Verfügbarkeit 

Für das Feature „Failovercluster“ müssen virtuelle Computer in einer [Verfügbarkeitsgruppe](../../../virtual-machines/linux/tutorial-availability-sets.md) oder [Verfügbarkeitszone](../../../availability-zones/az-overview.md#availability-zones) platziert werden. Wenn Sie Verfügbarkeitsgruppen wählen, können Sie [Näherungsplatzierungsgruppen](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) verwenden, um die VMs näher zueinander zu positionieren. Tatsächlich sind Näherungsplatzierungsgruppen eine Voraussetzung für den Einsatz freigegebener Azure-Datenträger. 

Wählen Sie die VM-Verfügbarkeitsoption sorgfältig aus, die Ihrer beabsichtigten Clusterkonfiguration entspricht: 

 - **Freigegebene Azure-Datenträger**: [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set), die mit auf 1 festgelegter Fehlerdomäne und Aktualisierungsdomäne konfiguriert ist und sich innerhalb einer [Näherungsplatzierungsgruppe](../../../virtual-machines/windows/proximity-placement-groups-portal.md) befindet.
 - **Premium-Dateifreigaben:** [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) oder [Verfügbarkeitszone](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address). Premium-Dateifreigaben sind die einzige freigegebene Speicheroption, wenn Sie für Ihre VMs Verfügbarkeitszonen als Verfügbarkeitskonfiguration wählen. 
 - **Direkte Speicherplätze**: [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

>[!IMPORTANT]
>Sie können die Verfügbarkeitsgruppe nicht mehr festlegen oder ändern, nachdem Sie einen virtuellen Computer erstellt haben.

## <a name="create-the-virtual-machines"></a>Erstellen der virtuellen Computer

Nachdem Sie die VM-Verfügbarkeit konfiguriert haben, können Sie Ihre virtuellen Computer erstellen. Sie können wählen, ob Sie ein Azure Marketplace-Image einsetzen möchten, bei dem SQL Server bereits installiert ist oder nicht. Wenn Sie jedoch ein Image für SQL Server auf Azure-VMs wählen, müssen Sie SQL Server vom virtuellen Computer deinstallieren, ehe Sie die Failoverclusterinstanz konfigurieren können. 

### <a name="considerations"></a>Überlegungen
In einem Azure IaaS-VM-Gast-Failovercluster werden eine einzelne Netzwerkkarte pro Server (Clusterknoten) und ein einzelnes Subnetz empfohlen. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten und Subnetze in einem Azure IaaS-VM-Gastcluster überflüssig macht. Obwohl im Clustervalidierungsbericht eine Warnung ausgegeben wird, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind, kann diese Warnung für Azure IaaS-VM-Gast-Failovercluster einfach ignoriert werden.

Platzieren Sie beide virtuellen Computer wie folgt:

- In derselben Azure-Ressourcengruppe wie Ihre Verfügbarkeitsgruppe, wenn Sie mit Verfügbarkeitsgruppen arbeiten.
- Im selben virtuellen Netzwerk wie der Domänencontroller.
- In einem Subnetz mit einem genügend großen IP-Adressraum für beide virtuellen Computer und alle FCIs, die in diesem Cluster unter Umständen verwendet werden sollen
- In der Azure-Verfügbarkeitsgruppe oder- Verfügbarkeitszone.

Sie können einen virtuellen Azure-Computer erstellen, indem Sie ein Image [mit](sql-vm-create-portal-quickstart.md) oder [ohne](../../../virtual-machines/windows/quick-create-portal.md) vorinstalliertes SQL Server verwenden. Wenn Sie das SQL Server-Image wählen, müssen Sie die SQL Server-Instanz manuell deinstallieren, bevor Sie die Failoverclusterinstanz installieren. 


## <a name="uninstall-sql-server"></a>Deinstallieren von SQL Server

Im Rahmen des Prozesses der FCI-Erstellung installieren Sie SQL Server als Clusterinstanz im Failovercluster. *Wenn Sie einen virtuellen Computer mit Azure Marketplace-Image ohne SQL Server bereitgestellt haben, können Sie diesen Schritt überspringen.* Wenn Sie ein Image mit vorinstallierter SQL Server-Instanz bereitgestellt haben, müssen Sie die Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM aufheben und SQL Server anschließend deinstallieren. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Aufheben der Registrierung der SQL-IaaS-Agent-Erweiterung für die VM

SQL Server-VM-Images aus Azure Marketplace werden automatisch mit der SQL-IaaS-Agent-Erweiterung registriert. Bevor Sie die vorinstallierte SQL Server-Instanz deinstallieren, müssen Sie zuerst [die Registrierung der SQL-IaaS-Agent-Erweiterung für jede SQL Server-VM aufheben](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Deinstallieren von SQL Server

Nachdem Sie die Registrierung der Erweiterung aufgehoben haben, können Sie SQL Server deinstallieren. Befolgen Sie dazu auf jedem virtuellen Computer diese Schritte: 

1. Verbindung mit diesem virtuellen Computer über RDP herstellen

   Wenn Sie die Verbindung mit einem virtuellen Computer per RDP zum ersten Mal herstellen, wird gefragt, ob Sie zulassen möchten, dass dieser PC über das Netzwerk ermittelt werden kann. Wählen Sie **Ja** aus.

1. Entfernen Sie die SQL Server-Instanz, wenn Sie eines der SQL Server-basierten VM-Images verwenden:

   1. Klicken Sie unter **Programme und Features** mit der rechten Maustaste auf **Microsoft SQL Server 201_ (64-Bit)** , und wählen Sie dann **Deinstallieren/Ändern** aus.
   1. Wählen Sie **Entfernen**.
   1. Wählen Sie die Standardinstanz aus.
   1. Entfernen Sie alle Funktionen unter **Datenbank-Engine-Dienst**. Entfernen Sie unter **Freigegebene Funktionen** nichts. Die Anzeige sollte in etwa wie im folgenden Screenshot aussehen:

      ![Auswählen von Features](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Wählen Sie **Weiter** und anschließend **Entfernen** aus.
   1. Nachdem die Instanz erfolgreich entfernt wurde, starten Sie den virtuellen Computer neu. 

## <a name="open-the-firewall"></a>Öffnen der Firewall 

Öffnen Sie auf jedem virtuellen Computer den von SQL Server verwendeten TCP-Port in der Windows-Firewall. Standardmäßig ist dies Port 1433. Sie können jedoch den SQL Server-Port in einer Azure-VM-Bereitstellung ändern. Öffnen Sie dazu den Port von SQL Server in Ihrer Umgebung. Dieser Port ist bei SQL Server-Images, die über Azure Marketplace bereitgestellt werden, automatisch geöffnet. 

Wenn Sie einen [Lastenausgleich](failover-cluster-instance-vnn-azure-load-balancer-configure.md) verwenden, müssen Sie auch den Port für den Integritätstest öffnen. Standardmäßig ist dies Port 59999. Es kann sich jedoch um einen beliebigen TCP-Port handeln, den Sie beim Einrichten des Lastenausgleichs angeben. 

In dieser Tabelle sind die Ports aufgeführt, die Sie je nach Ihrer FCI-Konfiguration möglicherweise öffnen müssen: 

   | Zweck | Port | Notizen
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Dies ist der normale Port für Standardinstanzen von SQL Server. Falls Sie ein Image aus dem Katalog verwendet haben, ist dieser Port automatisch geöffnet. </br> </br> **Verwendet von**: Allen FCI-Konfigurationen. |
   | Integritätstest | TCP 59999 | Beliebiger geöffneter TCP-Port. Konfigurieren Sie diesen Port für den [Integritätstest](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) des Lastenausgleichs und den Cluster. </br> </br> **Verwendet von**: FCI mit Lastenausgleich. |
   | Dateifreigabe | UDP 445 | Der vom Dateifreigabedienst verwendete Port. </br> </br> **Verwendet von**: FCI mit Premium-Dateifreigabe. |

## <a name="join-the-domain"></a>Beitreten zur Domäne

Außerdem müssen Ihre virtuellen Computer der Domäne beitreten. Nutzen Sie hierfür eine [Schnellstartvorlage](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Überprüfen der Speicherkonfiguration

Über Azure Marketplace erstellte virtuelle Computer werden mit angefügtem Speicher bereitgestellt. Wenn Sie planen, Ihren FCI-Speicher unter Verwendung von Premium-Dateifreigaben oder freigegebenen Azure-Datenträgern zu konfigurieren, können Sie den angefügten Speicher entfernen, um Kosten zu sparen, da lokaler Speicher nicht für die Failoverclusterinstanz genutzt wird. Es ist jedoch möglich, den angefügten Speicher für FCI-Lösungen mit „Direkte Speicherplätze“ zu verwenden, sodass es in diesem Fall nicht unbedingt sinnvoll ist, ihn zu entfernen. Überprüfen Sie Ihre FCI-Speicherlösung, um herauszufinden, ob das Entfernen von angefügtem Speicher optimal ist, um Kosten zu sparen. 


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Umgebung mit virtuellen Computern vorbereitet haben, können Sie nun Ihre Failoverclusterinstanz konfigurieren. 

Befolgen Sie einen der folgenden Leitfäden, um die für Ihr Unternehmen geeignete FCI-Umgebung zu konfigurieren: 
- [Konfigurieren der FCI mit freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Konfigurieren der FCI mit einer Premium-Dateifreigabe](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Konfigurieren der FCI mit „Direkte Speicherplätze“](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und unter [Unterstützte Konfigurationen für Hochverfügbarkeit und Notfallwiederherstellung](hadr-cluster-best-practices.md). 

Weitere Informationen finden Sie hier: 
- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
