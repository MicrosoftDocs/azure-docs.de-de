---
title: Konfigurieren einer SQL Server Always On-Verfügbarkeitsgruppe in verschiedenen Regionen
description: In diesem Artikel erfahren Sie, wie Sie eine SQL Server Always On-Verfügbarkeitsgruppe auf virtuellen Azure-Computern mit einem Replikat in einer anderen Region konfigurieren.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 6f63315c3e9b150a54e122d9a1c6948087603d51
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537406"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>Konfigurieren einer SQL Server Always On-Verfügbarkeitsgruppe in verschiedenen Azure-Regionen

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel erfahren Sie, wie Sie ein Replikat einer SQL Server Always On-Verfügbarkeitsgruppe auf virtuellen Azure Computern an einem Azure-Remotestandort konfigurieren. Verwenden Sie diese Konfiguration, um die Notfallwiederherstellung zu unterstützen.

Dieser Artikel gilt für Azure Virtual Machines im Resource Manager-Modus.

Die folgende Abbildung zeigt eine typische Bereitstellung einer Verfügbarkeitsgruppe für virtuelle Azure-Computer:

   ![Diagramm: Azure-Lastenausgleich und Verfügbarkeitsgruppe mit Windows Server-Failovercluster und Always On-Verfügbarkeitsgruppen](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

In dieser Bereitstellung befinden sich alle virtuellen Computer in einer einzelnen Azure-Region. Die Verfügbarkeitsgruppenreplikate können über synchrone Commits mit automatischem Failover an SQL-1 und SQL-2 verfügen. Informationen zum Erstellen dieser Architektur finden Sie unter [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](availability-group-overview.md).

Bei dieser Architektur kann es zu Ausfallzeiten kommen, wenn nicht mehr auf die Azure-Region zugegriffen werden kann. Fügen Sie daher ein Replikat in einer anderen Azure-Region hinzu, um diesem Problem vorzubeugen. Das folgende Diagramm zeigt die neue Architektur:

   ![Verfügbarkeitsgruppe – Notfallwiederherstellung](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

Die obige Abbildung enthält einen neuen virtuellen Computer namens SQL-3. SQL-3 befindet sich in einer anderen Azure-Region. SQL-3 wird dem Windows Server-Failovercluster hinzugefügt. SQL-3 kann ein Verfügbarkeitsgruppenreplikat hosten. Beachten Sie außerdem, dass die Azure-Region für SQL-3 über eine neue Azure Load Balancer-Instanz verfügt.

>[!NOTE]
> Eine Azure-Verfügbarkeitsgruppe wird benötigt, wenn sich mehrere virtuelle Computer in der gleichen Region befinden. Falls die Region nur einen einzelnen virtuellen Computer enthält, ist keine Verfügbarkeitsgruppe erforderlich. Virtuelle Computer können nur zum Zeitpunkt bei der Erstellung in einer Verfügbarkeitsgruppe platziert werden. Falls der virtuelle Computer bereits in einer Verfügbarkeitsgruppe enthalten ist, können Sie später einen virtuellen Computer für ein zusätzliches Replikat hinzufügen.

In dieser Architektur ist das Replikat in der Remoteregion in der Regel mit dem Verfügbarkeitsmodus für asynchrone Commits sowie mit dem manuellen Failovermodus konfiguriert.

Wenn sich Verfügbarkeitsgruppenreplikate auf virtuellen Azure-Computern in verschiedenen Azure-Regionen befinden, wird für jede Region Folgendes benötigt:

* Ein virtuelles Netzwerkgateway
* Eine Verbindung mit dem virtuellen Netzwerkgateway

Das folgende Diagramm zeigt die Netzwerkkommunikation zwischen Rechenzentren:

   ![Diagramm: Zwei virtuelle Netzwerke in verschiedenen Azure-Regionen, die mithilfe von VPN-Gateways kommunizieren](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Bei dieser Architektur fallen Gebühren für ausgehende Daten an, die zwischen Azure-Regionen repliziert werden. Weitere Informationen finden Sie unter [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Erstellen eines Remotereplikats

Gehen Sie wie folgt vor, um ein Replikat in einem Remoterechenzentrum zu erstellen:

1. [Erstellen Sie ein virtuelles Netzwerk in der neuen Region.](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)

1. [Konfigurieren Sie eine VNet-zu-VNet-Verbindung über das Azure-Portal.](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

   >[!NOTE]
   >In bestimmten Fällen muss die VNet-zu-VNet-Verbindung mithilfe von PowerShell erstellt werden. Wenn Sie beispielsweise verschiedene Azure-Konten verwenden, kann die Verbindung nicht über das Portal konfiguriert werden. Lesen Sie in diesem Fall den Artikel [Konfigurieren einer VNet-zu-VNet-Verbindung mithilfe von PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Erstellen Sie einen Domänencontroller in der neuen Region.](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)

   Dieser Domänencontroller ermöglicht die Authentifizierung für den Fall, dass der Domänencontroller am primären Standort nicht verfügbar ist.

1. [Erstellen Sie einen virtuellen SQL Server-Computer in der neuen Region.](create-sql-vm-portal.md)

1. [Erstellen Sie eine Azure Load Balancer-Instanz im Netzwerk für die neue Region.](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)

   Dieser Lastenausgleich muss folgende Anforderungen erfüllen:

   - Er muss sich im gleichen Netzwerk und Subnetz befinden wie der neue virtuelle Computer.
   - Er muss über eine statische IP-Adresse für den Verfügbarkeitsgruppenlistener verfügen.
   - Er muss über einen Back-End-Pool verfügen, der nur die virtuellen Computer aus der Region enthält, in der sich auch der Lastenausgleich befindet.
   - Er muss einen spezifischen TCP-Porttest für die IP-Adresse verwenden.
   - Er muss über eine spezifische Lastenausgleichsregel für die SQL Server-Instanz in der gleichen Region verfügen.  
   - Es muss sich um einen Load Balancer Standard handeln, wenn die virtuellen Computer im Back-End-Pool nicht entweder Teil einer einzelnen Verfügbarkeitsgruppe oder einer VM-Skalierungsgruppe sind. Weitere Informationen finden Sie unter [Übersicht über Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md).
   - Es muss sich um einen Load Balancer Standard handeln, wenn die beiden virtuellen Netzwerke in zwei verschiedenen Regionen mittels Peering über das globale VNET-Peering verbunden sind. Weitere Informationen finden Sie unter [Azure Virtual Network – häufig gestellte Fragen](../../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

1. [Fügen Sie der neuen SQL Server-Instanz das Failoverclustering-Feature hinzu.](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)

1. [Fügen Sie die neue SQL Server-Instanz der Domäne hinzu.](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain)

1. [Konfigurieren Sie das neue SQL Server-Dienstkonto für die Verwendung eines Domänenkontos.](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount)

1. [Fügen Sie die neue SQL Server-Instanz dem Windows Server-Failovercluster hinzu.](availability-group-manually-configure-tutorial.md#addNode)

1. Fügen Sie dem Cluster eine IP-Adressressource hinzu.

   Die IP-Adressressource kann im Failovercluster-Manager erstellt werden. Wählen Sie den Namen des Clusters aus, klicken Sie mit der rechten Maustaste unter **Hauptressourcen des Clusters** auf den Clusternamen, und wählen Sie dann **Eigenschaften** aus: 

   ![Screenshot: Failovercluster-Manager, in dem ein Clustername, „Servername“ und „Eigenschaften“ ausgewählt ist](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   Wählen Sie im Dialogfeld **Eigenschaften** die Option **Hinzufügen** unter **IP-Adresse** aus, und fügen Sie dann die IP-Adresse des Clusternamens aus der Remotenetzwerkregion hinzu. Klicken Sie im Dialogfeld **IP-Adresse** auf **OK**, und wählen Sie dann im Dialogfeld **Clustereigenschaften** erneut **OK** aus, um die neue IP-Adresse zu speichern. 

   ![Hinzufügen der IP-Adresse des Clusters](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Fügen Sie die IP-Adresse als Abhängigkeit für den Namen des Kernclusters hinzu.

   Öffnen Sie die Clustereigenschaften erneut, und wählen Sie die Registerkarte **Abhängigkeiten** aus. Konfigurieren Sie eine OR-Abhängigkeit für die beiden IP-Adressen: 

   ![Clustereigenschaften](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Fügen Sie der Verfügbarkeitsgruppenrolle im Cluster eine IP-Adressressource hinzu. 

   Klicken Sie in Failovercluster-Manager mit der rechten Maustaste auf die Verfügbarkeitsgruppenrolle, und wählen Sie **Ressource hinzufügen**, **Weitere Ressourcen** und dann **IP-Adresse** aus.

   ![Erstellen der IP-Adresse](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Konfigurieren Sie diese IP-Adresse wie folgt:

   - Verwenden Sie das Netzwerk aus dem Remoterechenzentrum.
   - Weisen Sie die IP-Adresse aus der neuen Azure Load Balancer-Instanz zu. 

1. Fügen Sie die IP-Adressressource als Abhängigkeit für den Cluster des Listener-Clientzugriffspunkts (Netzwerkname) hinzu.

   Der folgende Screenshot zeigt eine ordnungsgemäß konfigurierte IP-Adressclusterressource:

   ![Verfügbarkeitsgruppe](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Die Clusterressourcengruppe enthält beide IP-Adressen. Bei beiden IP-Adressen handelt es sich um Abhängigkeiten für den Listener-Clientzugriffspunkt. Verwenden Sie in der Clusterabhängigkeitskonfiguration den Operator **ODER**.

1. [Legen Sie die Clusterparameter in PowerShell fest](availability-group-manually-configure-tutorial.md#setparam).

   Führen Sie das PowerShell-Skript mit dem Clusternetzwerknamen, der IP-Adresse und dem Testport aus, die Sie für den Lastenausgleich in der neuen Region konfiguriert haben.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Aktivieren Sie in der neuen SQL Server-Instanz mithilfe des SQL Server-Konfigurations-Managers die AlwaysOn-Verfügbarkeitsgruppen. (Informationen hierzu finden Sie [hier](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).)

1. [Öffnen Sie Firewallports in der neuen SQL Server-Instanz.](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)

   Welche Portnummern geöffnet werden müssen, hängt von Ihrer Umgebung ab. Öffnen Sie Ports für den Spiegelungsendpunkt und den Integritätstest der Azure Load Balancer-Instanz.


1. [Fügen Sie der Verfügbarkeitsgruppe in der neuen SQL Server-Instanz ein Replikat hinzu.](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio)

   Konfigurieren Sie ein Replikat in einer Azure-Remoteregion für die asynchrone Replikation mit manuellem Failover.  

## <a name="set-connection-for-multiple-subnets"></a>Festlegen der Verbindung für mehrere Subnetze

Das Replikat im Remoterechenzentrum ist Teil der Verfügbarkeitsgruppe, befindet sich aber in einem anderen Subnetz. Wenn dieses Replikat zum primären Replikat wird, treten unter Umständen Verbindungstimeouts für Anwendungen auf. Dieses Verhalten entspricht dem Verhalten einer lokalen Verfügbarkeitsgruppe in einer Bereitstellung mit mehreren Subnetzen. Aktualisieren Sie entweder die Clientverbindung, oder konfigurieren Sie den Namensauflösungs-Cache für die Clusternetzwerknamen-Ressource, um Verbindungen von Clientanwendungen zu ermöglichen.

Aktualisieren Sie vorzugsweise die Clientverbindungszeichenfolgen, um `MultiSubnetFailover=Yes` festzulegen. Weitere Informationen finden Sie unter [Verbinden mit MultiSubnetFailover](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0).

Falls Sie die Verbindungszeichenfolgen nicht ändern können, können Sie den Namensauflösungs-Cache konfigurieren. Weitere Informationen finden Sie unter [Timeoutfehler, und Sie können keine Verbindung mit einem SQL Server 2012 AlwaysOn-Verfügbarkeitsgruppenlistener in einer Umgebung mit mehreren Subnetzen herstellen](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Durchführen eines Failovers auf die Remoteregion

Sie können ein Failover des Replikats auf die Remoteregion durchführen, um die Verbindung zwischen Listener und Remoteregion zu testen. Bei einem asynchronen Replikat können beim Failover Daten verloren gehen. Konfigurieren Sie den Verfügbarkeitsmodus als synchron und den Failovermodus als automatisch, um Datenverluste beim Failover zu vermeiden. Führen Sie die folgenden Schritte durch:

1. Stellen Sie im **Objekt-Explorer** eine Verbindung mit der Instanz von SQL Server her, die als Host für das primäre Replikat fungiert.
1. Klicken Sie unter **AlwaysOn-Verfügbarkeitsgruppen** > **Verfügbarkeitsgruppen** mit der rechten Maustaste auf Ihre Verfügbarkeitsgruppe, und wählen **Eigenschaften** aus.
1. Konfigurieren Sie auf der Seite **Allgemein** unter **Verfügbarkeitsreplikate** das sekundäre Replikat am Notfallwiederherstellungsstandort mit dem Verfügbarkeitsmodus **Synchroner Commit** und dem Failovermodus **Automatisch**.
1. Wenn Sie zur Erzielung von Hochverfügbarkeit über ein sekundäres Replikat verfügen, das sich am gleichen Standort befindet wie Ihr primäres Replikat, legen Sie dieses Replikat auf **Asynchroner Commit** und **Manuell** fest.
1. Wählen Sie „OK“ aus.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und wählen Sie anschließend **Dashboard anzeigen** aus.
1. Vergewissern Sie sich auf dem Dashboard, dass das Replikat am Notfallwiederherstellungsstandort synchronisiert wird.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und wählen Sie anschließend **Failover...** aus. In SQL Server Management Studio wird ein SQL Server-Failover-Assistent geöffnet.  
1. Wählen Sie **Weiter** aus, und wählen Sie die SQL Server-Instanz am Notfallwiederherstellungsstandort aus. Wählen Sie erneut **Weiter** aus.
1. Stellen Sie eine Verbindung mit der SQL Server-Instanz am Notfallwiederherstellungsstandort her, und wählen Sie **Weiter** aus.
1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und wählen Sie anschließend **Fertig stellen** aus.

Verschieben Sie das primäre Replikat nach dem Testen der Verbindung wieder in Ihr primäres Rechenzentrum, und legen Sie den Verfügbarkeitsmodus wieder auf die normalen Betriebseinstellungen fest. Die folgende Tabelle enthält die normalen Betriebseinstellungen für die in diesem Dokument beschriebene Architektur:

| Standort | Serverinstanz | Role | Verfügbarkeitsmodus | Failovermodus
| ----- | ----- | ----- | ----- | -----
| Primäres Rechenzentrum | SQL-1 | Primär | Synchron | Automatic
| Primäres Rechenzentrum | SQL-2 | Secondary | Synchron | Automatic
| Sekundäres Rechenzentrum oder Remoterechenzentrum | SQL-3 | Secondary | Asynchron | Manuell


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Weitere Informationen zu geplanten und erzwungenen manuellen Failovern

Weitere Informationen finden Sie in den folgenden Themen:

- [Ausführen eines geplanten manuellen Failovers einer Verfügbarkeitsgruppe (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [Ausführen eines erzwungenen manuellen Failovers einer Verfügbarkeitsgruppe (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>Nächste Schritte

* [AlwaysOn-Verfügbarkeitsgruppen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Dokumentation zu virtuellen Computern](../../../virtual-machines/windows/index.yml)
* [Azure Load Balancer-Instanzen](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Azure-Verfügbarkeitsgruppen](../../../virtual-machines/manage-availability.md)
