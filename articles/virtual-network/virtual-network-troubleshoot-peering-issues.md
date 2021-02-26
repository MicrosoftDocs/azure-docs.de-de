---
title: Beheben von Problemen mit dem Peering virtueller Netzwerke
description: Schritte, mit denen sich die meisten Probleme eines Peerings virtueller Netzwerke beheben lassen
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: f3e1ea8861646490875667f6238b3f8813bd6c4b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518163"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Beheben von Problemen mit dem Peering virtueller Netzwerke

In diesem Handbuch zur Problembehandlung finden Sie Schritte zum Beheben der meisten Probleme eines [Peerings virtueller Netzwerke](virtual-network-peering-overview.md).

![Diagramm zu Peering virtueller Netzwerke](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurieren des Peerings zwischen zwei virtuellen Netzwerken.

Gehören die virtuellen Netzwerke zum selben Abonnement oder zu verschiedenen Abonnements?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Die virtuellen Netzwerke gehören zum selben Abonnement.

Verwenden Sie die in den folgenden Artikeln aufgeführten Methoden, um ein Peering virtueller Netzwerke für virtuelle Netzwerke zu konfigurieren, die zum selben Abonnement gehören:

* Wenn sich die virtuellen Netzwerke in *derselben Region* befinden, lesen Sie [Erstellen eines Peerings](./virtual-network-manage-peering.md#create-a-peering).
* Wenn sich die virtuellen Netzwerke in *unterschiedlichen Regionen* befinden, lesen Sie [Peering virtueller Netzwerke](./virtual-network-peering-overview.md). 

> [!Note]
> Für die folgenden Ressourcen funktioniert Konnektivität über globales Peering virtueller Netzwerke nicht: 
>
> * Virtuelle Computer hinter einem internen Lastenausgleich (Internal Load Balancer, ILB) im Tarif (SKU) „Basic“
> * Redis Cache (verwendet Basic ILB SKU)
> * Application Gateway (verwendet Basic ILB SKU)
> * VM-Skalierungsgruppen (verwenden Basic ILB SKU)
> * Azure Service Fabric-Cluster (verwenden Basic ILB SKU)
> * SQL Server Always On (verwendet Basic ILB SKU)
> * Azure App Service-Umgebung für PowerApps (verwendet Basic ILB SKU)
> * Azure API Management (verwendet Basic ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (verwendet Basic ILB SKU)

Weitere Informationen hierzu finden Sie unter [Anforderungen und Einschränkungen](./virtual-network-peering-overview.md#requirements-and-constraints) von globalem Peering.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Die virtuellen Netzwerke gehören zu unterschiedlichen Abonnements oder Active Directory-Mandanten

Um ein Peering virtueller Netzwerke für virtuelle Netzwerke in verschiedenen Abonnements oder Active Directory-Mandanten zu konfigurieren, lesen Sie [Erstellen eines Peerings für unterschiedliche Abonnements über Azure CLI](./create-peering-different-subscriptions.md#cli) aus.

> [!Note]
> Damit Sie ein Netzwerkpeering konfigurieren können, benötigen Sie in beiden Abonnements die Berechtigung **Netzwerkmitwirkender**. Weitere Informationen hierzu finden Sie unter [Peering-Berechtigungen](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurieren eines Peerings virtueller Netzwerke mit Hub-Spoke-Topologie, in der lokale Ressourcen verwendet werden

![Diagramm eines Peerings virtueller Netzwerke mit lokalen Speichen (Spokes)](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Für eine Standort-zu-Standort-Verbindung oder eine ExpressRoute-Verbindung

Führen Sie die Schritte aus, die im folgenden Artikel aufgeführt sind: [Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Für Punkt-zu-Standort-Verbindungen

1. Führen Sie die Schritte aus, die im folgenden Artikel aufgeführt sind: [Konfigurieren des VPN-Gatewaytransits für ein Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Nachdem das Peering virtueller Netzwerke eingerichtet oder geändert wurde, laden Sie das Punkt-zu-Standort-Paket herunter, und installieren Sie dieses erneut, damit die Punkt-zu-Standort-Clients die aktualisierten Routen zum virtuellen Spoke-Netzwerk erhalten.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurieren eines Peerings virtueller Netzwerke mit Hub-Spoke-Topologie für virtuelle Netzwerke

![Diagramm eines Peerings virtueller Netzwerke mit Speichen (Spokes) zu virtuellen Netzwerken](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Die virtuellen Netzwerke gehören zur selben Region.


1. Konfigurieren Sie im virtuellen Hub-Netzwerk ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA).
1. Richten Sie in den virtuellen Speichennetzwerken benutzerdefinierte Routen ein, für die der Typ des nächsten Hops auf „virtuelles Netzwerkgerät“ festgelegt ist.

Weitere Informationen finden Sie unter [Dienstverkettung](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Wenn Sie Hilfe für das Einrichten einer NVA benötigen, [wenden Sie sich an den NVA-Anbieter](https://mskb.pkisolutions.com/kb/2984655).

Informationen zur Problembehandlung beim Einrichten einer NVA und zum Routing finden Sie unter [Probleme mit virtuellen Netzwerkappliances in Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>Die virtuellen Netzwerke gehören zu verschiedenen Regionen.

Datenverkehr über globales Peering virtueller Netzwerke wird jetzt unterstützt. Für die folgenden Ressourcen funktioniert Konnektivität über globales Peering virtueller Netzwerke nicht:

* Virtuelle Computer hinter Basic ILB SKU (Basic-Tarif mit internem Lastenausgleich)
* Redis Cache (verwendet Basic ILB SKU)
* Application Gateway (verwendet Basic ILB SKU)
* Skalierungsgruppen (verwenden Basic ILB SKU)
* Service Fabric-Cluster (verwenden Basic ILB SKU)
* SQL Server Always On (verwendet Basic ILB SKU)
* App Service-Umgebung (verwendet Basic ILB SKU)
* API Management (verwendet Basic ILB SKU)
* Azure AD DS (verwendet Basic ILB SKU)

Informationen zu den Anforderungen und Einschränkungen für globales Peering finden Sie unter [Peering virtueller Netzwerke: Anforderungen und Einschränkungen](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Beheben eines Konnektivitätsproblems zwischen zwei virtuellen Netzwerken, die über Peering verbunden sind

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Konto an, dem die erforderlichen [Rollen und Berechtigungen](virtual-network-manage-peering.md#permissions) zugewiesen sind. Wählen Sie das virtuelle Netzwerk aus, wählen Sie **Peering** aus, und überprüfen Sie dann das Feld **Status**. Welcher Status wird angezeigt?

### <a name="the-peering-status-is-connected"></a>Der Peeringstatus lautet „Verbunden“

So beheben Sie dieses Problem

1. Überprüfen Sie den Netzwerkdatenverkehr:

   Verwenden Sie [Problembehandlung für Verbindung](../network-watcher/network-watcher-connectivity-overview.md) und [Überprüfen des IP-Flusses](../network-watcher/network-watcher-ip-flow-verify-overview.md) vom virtuellen Quellcomputer zum virtuellen Zielcomputer, um zu bestimmen, ob es eine Netzwerksicherheitsgruppe oder benutzerdefinierte Route (User Defined Route, UDR) gibt, die zu Störungen im Datenverkehr führt.

   Wenn Sie eine Firewall oder ein virtuelles Netzwerkgerät (NVA) verwenden: 
   1. Dokumentieren Sie die UDR-Parameter, damit Sie diese nach Abschluss dieses Schritts wiederherstellen können.
   2. Entfernen Sie die UDR im virtuellen Quellcomputer aus dessen Subnetz oder Netzwerkadapter, in dem auf die NVA als nächster Hop verweisen wird. Überprüfen Sie die Verbindung zwischen dem virtuelle Quellcomputer und dem Ziel direkt, d. h., die Verbindung umgeht die NVA. Wenn dieser Schritt nicht funktioniert, lesen Sie [Probleme mit virtuellen Netzwerkappliances in Azure](./virtual-network-troubleshoot-nva.md).

2. Führen Sie eine Netzwerküberwachung aus: 
   1. Starten Sie eine Netzwerküberwachung auf dem virtuellen Zielcomputer. Unter Windows können Sie **Netsh** verwenden. Unter Linux verwenden Sie **TCPDump**.
   2. Führen Sie **TcpPing** oder **PsPing** von der Quell- zur Ziel-IP aus.

      Dies ist ein Beispiel für einen **TcpPing**-Befehl: `tcping64.exe -t <destination VM address> 3389`

   3. Nachdem der **TcpPing**-Befehl abgeschlossen ist, beenden Sie die Netzwerküberwachung auf dem Zielcomputer.
   4. Wenn Pakete von der Quelle eintreffen, gibt es kein Netzwerkproblem. Überprüfen Sie sowohl die Firewall des virtuellen Computers als auch die an diesem Port lauschende Anwendung, um das Konfigurationsproblem zu finden.

   > [!Note]
   > Über globales Peering virtueller Netzwerke (virtuelle Netzwerke in unterschiedlichen Regionen) können Sie keine Verbindungen mit den folgenden Ressourcentypen herstellen:
   >
   > * Virtuelle Computer hinter Basic ILB SKU (Basic-Tarif mit internem Lastenausgleich)
   > * Redis Cache (verwendet Basic ILB SKU)
   > * Application Gateway (verwendet Basic ILB SKU)
   > * Skalierungsgruppen (verwenden Basic ILB SKU)
   > * Service Fabric-Cluster (verwenden Basic ILB SKU)
   > * SQL Server Always On (verwendet Basic ILB SKU)
   > * App Service-Umgebung (verwendet Basic ILB SKU)
   > * API Management (verwendet Basic ILB SKU)
   > * Azure AD DS (verwendet Basic ILB SKU)

Weitere Informationen hierzu finden Sie unter [Anforderungen und Einschränkungen](./virtual-network-peering-overview.md#requirements-and-constraints) von globalem Peering.

### <a name="the-peering-status-is-disconnected"></a>Der Peeringstatus lautet „Getrennt“

Um dieses Problem zu beheben, löschen Sie das Peering aus beiden virtuellen Netzwerken, und erstellen Sie das Peering dann neu.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Beheben eines Konnektivitätsproblems zwischen einem virtuellen Hub-Spoke-Netzwerk und einer lokalen Ressource

Wird in dem Netzwerk eine NVA oder ein VPN-Gateway eines Drittanbieters verwendet?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>In dem Netzwerk wird eine NVA oder ein VPN-Gateway eines Drittanbieters verwendet.

Informationen zum Beheben von Konnektivitätsproblemen, die sich auf eine NVA oder ein VPN-Gateway eines Drittanbieters auswirken, finden Sie in den folgenden Artikeln:

* [Probleme mit virtuellen Netzwerkappliances in Azure](./virtual-network-troubleshoot-nva.md)
* [Dienstverkettung](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>In dem Netzwerk wird weder eine NVA noch ein VPN-Gateway eines Drittanbieters verwendet.

Haben das virtuelle Hub-Netzwerk und das virtuelle Spoke-Netzwerk ein VPN-Gateway?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Sowohl das virtuelle Hub-Netzwerk als auch das virtuelle Spoke-Netzwerk haben ein VPN-Gateway.

Die Verwendung eines Remotegateways wird nicht unterstützt.

Hat das virtuelle Spoke-Netzwerk bereits ein VPN-Gateway, wird die Option **Remotegateways verwenden** in dem virtuellen Spoke-Netzwerk nicht unterstützt. Dies liegt an der Einschränkung des Peerings virtueller Netzwerke.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Sowohl das virtuelle Hub-Netzwerk als auch das virtuelle Spoke-Netzwerk haben kein VPN-Gateway.

Überprüfen Sie für Standort-zu-Standort- oder Azure ExpressRoute-Verbindungen die folgenden Hauptursachen für Konnektivitätsprobleme mit dem virtuellen Remotenetzwerk von einem lokalen Standort aus:

* Vergewissern Sie sich, dass für das virtuelle Netzwerk, das ein Gateway hat, das Kontrollkästchen **Weitergeleiteten Datenverkehr zulassen** aktiviert ist.
* Vergewissern Sie sich, dass für das virtuelle Netzwerk, das kein Gateway hat, das Kontrollkästchen **Remotegateways verwenden** aktiviert ist.
* Veranlassen Sie, dass der Netzwerkadministrator Ihre lokalen Geräte überprüft, um sicherzustellen, dass allen Geräten Adressraum des virtuellen Remotenetzwerks hinzugefügt wurde.

Für Punkt-zu-Standort-Verbindungen:

* Vergewissern Sie sich, dass für das virtuelle Netzwerk, das ein Gateway hat, das Kontrollkästchen **Weitergeleiteten Datenverkehr zulassen** aktiviert ist.
* Vergewissern Sie sich, dass für das virtuelle Netzwerk, das kein Gateway hat, das Kontrollkästchen **Remotegateways verwenden** aktiviert ist.
* Laden Sie das Punkt-zu-Standort-Clientpaket herunter, und installieren Sie es erneut. Für Routen in virtuellen Netzwerken, für die ein erneutes Peering vorgenommen wird, werden nicht automatisch Routen zu Punkt-zu-Standort-Clients hinzugefügt.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Beheben eines Hub-Spoke-Netzwerkverbindungsproblems zwischen virtuellen Spoke-Netzwerken in derselben Region

Ein Hub-Netzwerk muss ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) enthalten. Konfigurieren Sie UDRs in Spokes, für die eine NVA als nächster Hop festgelegt ist, und aktivieren Sie **Weitergeleiteten Datenverkehr zulassen** im virtuellen Hub-Netzwerk.

Weitere Informationen finden Sie unter [Dienstverkettung](./virtual-network-peering-overview.md#service-chaining), und besprechen Sie diese Anforderungen mit dem jeweiligen [NVA-Anbieter](https://mskb.pkisolutions.com/kb/2984655).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Beheben eines Hub-Spoke-Netzwerkverbindungsproblems zwischen virtuellen Spoke-Netzwerken in unterschiedlichen Regionen

Datenverkehr über globales Peering virtueller Netzwerke wird jetzt unterstützt. Für die folgenden Ressourcen funktioniert Konnektivität über globales Peering virtueller Netzwerke nicht:

* Virtuelle Computer hinter Basic ILB SKU (Basic-Tarif mit internem Lastenausgleich)
* Redis Cache (verwendet Basic ILB SKU)
* Application Gateway (verwendet Basic ILB SKU)
* Skalierungsgruppen (verwenden Basic ILB SKU)
* Service Fabric-Cluster (verwenden Basic ILB SKU)
* SQL Server Always On (verwendet Basic ILB SKU)
* App Service-Umgebung (verwendet Basic ILB SKU)
* API Management (verwendet Basic ILB SKU)
* Azure AD DS (verwendet Basic ILB SKU)

Weitere Informationen hierzu finden Sie unter [Anforderungen und Einschränkungen](./virtual-network-peering-overview.md#requirements-and-constraints) von globalem Peering und unter [Hub&Spoke, Daisy-Chain and Full-Mesh VNET topologies in Azure ARM](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Beheben eines Hub-Spoke-Netzwerkverbindungsproblems zwischen einer Web-App und dem virtuellen Spoke-Netzwerk

So beheben Sie dieses Problem

1. Melden Sie sich beim Azure-Portal an. 
1. Wählen Sie in der Web-App die Option **Netzwerk** aus, und wählen Sie dann **VNET-Integration** aus.
1. Überprüfen Sie, ob das virtuelle Remotenetzwerk angezeigt wird. Geben Sie den Adressraum des virtuellen Remotenetzwerks manuell ein (**Netzwerk synchronisieren** und **Route hinzufügen**).

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md)
* [Informationen zu Point-to-Site-VPN-Routing](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Beheben eines Konfigurationsfehlers eines Peerings virtueller Netzwerke 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Der aktuelle Mandant `<TENANT ID>` ist nicht für den Zugriff auf verknüpfte Abonnements autorisiert.

Um dieses Problem zu beheben, lesen Sie [Erstellen eines Peerings: Azure CLI](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Not connected

Um dieses Problem zu beheben, löschen Sie das Peering aus beiden virtuellen Netzwerken, und erstellen Sie das Peering dann neu.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Fehler beim Peering eines virtuellen Databricks-Netzwerks

Um dieses Problem zu beheben, konfigurieren Sie das Peering virtueller Netzwerke unter **Azure Databricks**, und geben Sie dann das virtuelle Zielnetzwerk über die **Ressourcen-ID** an. Weitere Informationen finden Sie unter [Peer a Databricks virtual network to a remote virtual network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Dem virtuellen Remotenetzwerk fehlt ein Gateway

Dieses Problem tritt auf, wenn Sie zwischen virtuellen Netzwerken von verschiedenen Mandanten Peeringverbindungen herstellen und später `Use Remote Gateways` konfigurieren möchten. Eine Einschränkung des Azure-Portals besteht darin, dass es das Vorhandensein eines virtuellen Netzwerkgateways im virtuellen Netzwerk eines anderen Mandanten nicht überprüfen kann.

Es gibt zwei Möglichkeiten, dieses Problem zu beheben:

 * Löschen Sie die Peerings, und aktivieren Sie die Option `Use Remote Gateways`, wenn Sie ein neues Peering erstellen.
 * Verwenden Sie PowerShell oder die Befehlszeilenschnittstelle anstelle des Azure-Portals, um `Use Remote Gateways` zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei Konnektivitätsproblemen zwischen Azure-VMs](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)