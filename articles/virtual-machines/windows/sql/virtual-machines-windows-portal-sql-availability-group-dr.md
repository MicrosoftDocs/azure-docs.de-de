---
title: SQL Server-Verfügbarkeitsgruppen – Azure Virtual Machines – Notfallwiederherstellung | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie eine SQL Server-Verfügbarkeitsgruppe auf virtuellen Azure-Computern mit einem Replikat in einer anderen Region konfigurieren.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: f9e31ac7685d597c741033bc165c6a51280e3d72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571732"
---
# <a name="configure-an-always-on-availability-group-on-azure-virtual-machines-in-different-regions"></a>Konfigurieren einer Always On-Verfügbarkeitsgruppe auf virtuellen Azure-Computern in verschiedenen Regionen

In diesem Artikel erfahren Sie, wie Sie ein SQL Server-Always On-Verfügbarkeitsgruppenreplikat auf virtuellen Azure Computern an einem Azure-Remotestandort konfigurieren. Verwenden Sie diese Konfiguration, um die Notfallwiederherstellung zu unterstützen.

Dieser Artikel gilt für Azure Virtual Machines im Resource Manager-Modus.

Die folgende Abbildung zeigt eine typische Bereitstellung einer Verfügbarkeitsgruppe für virtuelle Azure-Computer:

   ![Verfügbarkeitsgruppe](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic.png)

In dieser Bereitstellung befinden sich alle virtuellen Computer in einer einzelnen Azure-Region. Die Verfügbarkeitsgruppenreplikate können über synchrone Commits mit automatischem Failover an SQL-1 und SQL-2 verfügen. Informationen zum Erstellen dieser Architektur finden Sie unter [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](virtual-machines-windows-portal-sql-availability-group-overview.md).

Bei dieser Architektur kann es zu Ausfallzeiten kommen, wenn nicht mehr auf die Azure-Region zugegriffen werden kann. Fügen Sie daher ein Replikat in einer anderen Azure-Region hinzu, um diesem Problem vorzubeugen. Das folgende Diagramm zeigt die neue Architektur:

   ![Verfügbarkeitsgruppe – Notfallwiederherstellung](./media/virtual-machines-windows-portal-sql-availability-group-dr/00-availability-group-basic-dr.png)

Die obige Abbildung enthält einen neuen virtuellen Computer namens SQL-3. SQL-3 befindet sich in einer anderen Azure-Region. SQL-3 wird dem Windows Server-Failovercluster hinzugefügt. SQL-3 kann ein Verfügbarkeitsgruppenreplikat hosten. Beachten Sie außerdem, dass die Azure-Region für SQL-3 über eine neue Azure Load Balancer-Instanz verfügt.

>[!NOTE]
> Eine Azure-Verfügbarkeitsgruppe wird benötigt, wenn sich mehrere virtuelle Computer in der gleichen Region befinden. Falls die Region nur einen einzelnen virtuellen Computer enthält, ist keine Verfügbarkeitsgruppe erforderlich. Virtuelle Computer können nur zum Zeitpunkt bei der Erstellung in einer Verfügbarkeitsgruppe platziert werden. Falls der virtuelle Computer bereits in einer Verfügbarkeitsgruppe enthalten ist, können Sie später einen virtuellen Computer für ein zusätzliches Replikat hinzufügen.

In dieser Architektur ist das Replikat in der Remoteregion in der Regel mit dem Verfügbarkeitsmodus für asynchrone Commits sowie mit dem manuellen Failovermodus konfiguriert.

Wenn sich Verfügbarkeitsgruppenreplikate auf virtuellen Azure-Computern in verschiedenen Azure-Regionen befinden, wird für jede Region Folgendes benötigt:

* Ein virtuelles Netzwerkgateway
* Eine Verbindung mit dem virtuellen Netzwerkgateway

Das folgende Diagramm zeigt die Netzwerkkommunikation zwischen Rechenzentren:

   ![Verfügbarkeitsgruppe](./media/virtual-machines-windows-portal-sql-availability-group-dr/01-vpngateway-example.png)

>[!IMPORTANT]
>Bei dieser Architektur fallen Gebühren für ausgehende Daten an, die zwischen Azure-Regionen repliziert werden. Weitere Informationen finden Sie unter [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Erstellen eines Remotereplikats

Gehen Sie wie folgt vor, um ein Replikat in einem Remoterechenzentrum zu erstellen:

1. [Erstellen Sie ein virtuelles Netzwerk in der neuen Region.](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)

1. [Konfigurieren Sie eine VNet-zu-VNet-Verbindung über das Azure-Portal.](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

   >[!NOTE]
   >In bestimmten Fällen muss die VNet-zu-VNet-Verbindung mithilfe von PowerShell erstellt werden. Wenn Sie beispielsweise verschiedene Azure-Konten verwenden, kann die Verbindung nicht über das Portal konfiguriert werden. Lesen Sie in diesem Fall den Artikel [Konfigurieren einer VNet-zu-VNet-Verbindung mithilfe von PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Erstellen Sie einen Domänencontroller in der neuen Region.](../../../active-directory/active-directory-new-forest-virtual-machine.md)

   Dieser Domänencontroller ermöglicht die Authentifizierung für den Fall, dass der Domänencontroller am primären Standort nicht verfügbar ist.

1. [Erstellen Sie einen virtuellen SQL Server-Computer in der neuen Region.](virtual-machines-windows-portal-sql-server-provision.md)

1. [Erstellen Sie eine Azure Load Balancer-Instanz im Netzwerk für die neue Region.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)

   Dieser Lastenausgleich muss folgende Anforderungen erfüllen:

   - Er muss sich im gleichen Netzwerk und Subnetz befinden wie der neue virtuelle Computer.
   - Er muss über eine statische IP-Adresse für den Verfügbarkeitsgruppenlistener verfügen.
   - Er muss über einen Back-End-Pool verfügen, der nur die virtuellen Computer aus der Region enthält, in der sich auch der Lastenausgleich befindet.
   - Er muss einen spezifischen TCP-Porttest für die IP-Adresse verwenden.
   - Er muss über eine spezifische Lastenausgleichsregel für die SQL Server-Instanz in der gleichen Region verfügen.  
   - Es muss sich um einen Load Balancer Standard handeln, wenn die virtuellen Computer im Back-End-Pool nicht entweder Teil einer einzelnen Verfügbarkeitsgruppe oder einer VM-Skalierungsgruppe sind. Weitere Informationen finden Sie unter [Übersicht über Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. [Fügen Sie der neuen SQL Server-Instanz das Failoverclustering-Feature hinzu.](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)

1. [Fügen Sie die neue SQL Server-Instanz der Domäne hinzu.](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)

1. [Konfigurieren Sie das neue SQL Server-Dienstkonto für die Verwendung eines Domänenkontos.](virtual-machines-windows-portal-sql-availability-group-prereq.md#setServiceAccount)

1. [Fügen Sie die neue SQL Server-Instanz dem Windows Server-Failovercluster hinzu.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#addNode)

1. Erstellen Sie eine IP-Adressressource für den Cluster.

   Die IP-Adressressource kann im Failovercluster-Manager erstellt werden. Klicken Sie mit der rechten Maustaste auf die Verfügbarkeitsgruppenrolle, und klicken Sie anschließend auf **Ressource hinzufügen** > **Weitere Ressourcen** > **IP-Adresse**.

   ![Erstellen der IP-Adresse](./media/virtual-machines-windows-portal-sql-availability-group-dr/20-add-ip-resource.png)

   Konfigurieren Sie diese IP-Adresse wie folgt:

   - Verwenden Sie das Netzwerk aus dem Remoterechenzentrum.
   - Weisen Sie die IP-Adresse aus der neuen Azure Load Balancer-Instanz zu. 

1. Aktivieren Sie in der neuen SQL Server-Instanz mithilfe des SQL Server-Konfigurations-Managers die AlwaysOn-Verfügbarkeitsgruppen. (Informationen hierzu finden Sie [hier](https://msdn.microsoft.com/library/ff878259.aspx).)

1. [Öffnen Sie Firewallports in der neuen SQL Server-Instanz.](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)

   Welche Portnummern geöffnet werden müssen, hängt von Ihrer Umgebung ab. Öffnen Sie Ports für den Spiegelungsendpunkt und den Integritätstest der Azure Load Balancer-Instanz.

1. [Fügen Sie der Verfügbarkeitsgruppe in der neuen SQL Server-Instanz ein Replikat hinzu.](https://msdn.microsoft.com/library/hh213239.aspx)

   Konfigurieren Sie ein Replikat in einer Azure-Remoteregion für die asynchrone Replikation mit manuellem Failover.  

1. Fügen Sie die IP-Adressressource als Abhängigkeit für den Cluster des Listener-Clientzugriffspunkts (Netzwerkname) hinzu.

   Der folgende Screenshot zeigt eine ordnungsgemäß konfigurierte IP-Adressclusterressource:

   ![Verfügbarkeitsgruppe](./media/virtual-machines-windows-portal-sql-availability-group-dr/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Die Clusterressourcengruppe enthält beide IP-Adressen. Bei beiden IP-Adressen handelt es sich um Abhängigkeiten für den Listener-Clientzugriffspunkt. Verwenden Sie in der Clusterabhängigkeitskonfiguration den Operator **ODER**.

1. [Legen Sie die Clusterparameter in PowerShell fest](virtual-machines-windows-portal-sql-availability-group-tutorial.md#setparam).

Führen Sie das PowerShell-Skript mit dem Clusternetzwerknamen, der IP-Adresse und dem Testport aus, die Sie für den Lastenausgleich in der neuen Region konfiguriert haben.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = “<n.n.n.n>” # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

## <a name="set-connection-for-multiple-subnets"></a>Festlegen der Verbindung für mehrere Subnetze

Das Replikat im Remoterechenzentrum ist Teil der Verfügbarkeitsgruppe, befindet sich aber in einem anderen Subnetz. Wenn dieses Replikat zum primären Replikat wird, treten unter Umständen Verbindungstimeouts für Anwendungen auf. Dieses Verhalten entspricht dem Verhalten einer lokalen Verfügbarkeitsgruppe in einer Bereitstellung mit mehreren Subnetzen. Aktualisieren Sie entweder die Clientverbindung, oder konfigurieren Sie den Namensauflösungs-Cache für die Clusternetzwerknamen-Ressource, um Verbindungen von Clientanwendungen zu ermöglichen.

Aktualisieren Sie vorzugsweise die Clientverbindungszeichenfolgen, um `MultiSubnetFailover=Yes` festzulegen. Weitere Informationen finden Sie unter [Verbinden mit MultiSubnetFailover](https://msdn.microsoft.com/library/gg471494#Anchor_0).

Falls Sie die Verbindungszeichenfolgen nicht ändern können, können Sie den Namensauflösungs-Cache konfigurieren. Weitere Informationen finden Sie unter [Timeoutfehler, und Sie können keine Verbindung mit einem SQL Server 2012 AlwaysOn-Verfügbarkeitsgruppenlistener in einer Umgebung mit mehreren Subnetzen herstellen](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Durchführen eines Failovers auf die Remoteregion

Sie können ein Failover des Replikats auf die Remoteregion durchführen, um die Verbindung zwischen Listener und Remoteregion zu testen. Bei einem asynchronen Replikat können beim Failover Daten verloren gehen. Konfigurieren Sie den Verfügbarkeitsmodus als synchron und den Failovermodus als automatisch, um Datenverluste beim Failover zu vermeiden. Führen Sie die folgenden Schritte aus:

1. Stellen Sie im **Objekt-Explorer** eine Verbindung mit der Instanz von SQL Server her, die als Host für das primäre Replikat fungiert.
1. Klicken Sie unter **AlwaysOn-Verfügbarkeitsgruppen** > **Verfügbarkeitsgruppen** mit der rechten Maustaste auf Ihre Verfügbarkeitsgruppe, und klicken Sie anschließend auf **Eigenschaften**.
1. Konfigurieren Sie auf der Seite **Allgemein** unter **Verfügbarkeitsreplikate** das sekundäre Replikat am Notfallwiederherstellungsstandort mit dem Verfügbarkeitsmodus **Synchroner Commit** und dem Failovermodus **Automatisch**.
1. Wenn Sie zur Erzielung von Hochverfügbarkeit über ein sekundäres Replikat verfügen, das sich am gleichen Standort befindet wie Ihr primäres Replikat, legen Sie dieses Replikat auf **Asynchroner Commit** und **Manuell** fest.
1. Klicken Sie auf OK.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und klicken Sie anschließend auf **Dashboard anzeigen**.
1. Vergewissern Sie sich auf dem Dashboard, dass das Replikat am Notfallwiederherstellungsstandort synchronisiert wird.
1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und klicken Sie anschließend auf **Failover...** . In SQL Server Management Studio wird ein SQL Server-Failover-Assistent geöffnet.  
1. Klicken Sie auf **Weiter**, und wählen Sie die SQL Server-Instanz am Notfallwiederherstellungsstandort aus. Klicken Sie erneut auf **Weiter** .
1. Stellen Sie eine Verbindung mit der SQL Server-Instanz am Notfallwiederherstellungsstandort her, und klicken Sie auf **Weiter**.
1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Fertig stellen**.

Verschieben Sie das primäre Replikat nach dem Testen der Verbindung wieder in Ihr primäres Rechenzentrum, und legen Sie den Verfügbarkeitsmodus wieder auf die normalen Betriebseinstellungen fest. Die folgende Tabelle enthält die normalen Betriebseinstellungen für die in diesem Dokument beschriebene Architektur:

| Location | Serverinstanz | Rolle | Verfügbarkeitsmodus | Failovermodus
| ----- | ----- | ----- | ----- | -----
| Primäres Rechenzentrum | SQL-1 | Primär | Synchron | Automatisch
| Primäres Rechenzentrum | SQL-2 | Sekundär | Synchron | Automatisch
| Sekundäres Rechenzentrum oder Remoterechenzentrum | SQL-3 | Sekundär | Asynchron | Manuell


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Weitere Informationen zu geplanten und erzwungenen manuellen Failovern

Weitere Informationen finden Sie in den folgenden Themen:

- [Ausführen eines geplanten manuellen Failovers einer Verfügbarkeitsgruppe (SQL Server)](https://msdn.microsoft.com/library/hh231018.aspx)
- [Ausführen eines erzwungenen manuellen Failovers einer Verfügbarkeitsgruppe (SQL Server)](https://msdn.microsoft.com/library/ff877957.aspx)

## <a name="additional-links"></a>Weitere Links

* [AlwaysOn-Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/hh510230.aspx)
* [Dokumentation zu virtuellen Computern](https://docs.microsoft.com/azure/virtual-machines/windows/)
* [Azure Load Balancer-Instanzen](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)
* [Azure-Verfügbarkeitsgruppen](../manage-availability.md)
