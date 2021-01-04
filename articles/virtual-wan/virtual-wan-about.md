---
title: Azure Virtual WAN – Übersicht | Microsoft-Dokumentation
description: Sie erhalten Informationen darüber, wie Sie die per Virtual WAN automatisierte skalierbare Konnektivität zwischen Branches einrichten, über verfügbare Regionen und Partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 641ed73f507d35d7af548d0164ef8e80979be217
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530475"
---
# <a name="what-is-azure-virtual-wan"></a>Was ist Azure Virtual WAN?

Azure Virtual WAN ist ein Netzwerkdienst, der viele Netzwerk-, Sicherheits- und Routingfunktionen auf einer einzigen Bedienoberfläche vereint. Diese Funktionen umfassen Zweigestellenverbindungen (über die Konnektivitätsautomatisierung von Virtual WAN-Partnereinrichtungen wie SD-WAN oder VPN CPE), Site-to-Site-VPN-Konnektivität, Remotebenutzer-VPN (Point-to-Site), private Konnektivität (ExpressRoute), Konnektivität zwischen Clouds (transitive Konnektivität für virtuelle Netzwerke), VPN ExpressRoute-Interkonnektivität, Routing, Azure Firewall und Verschlüsselung für private Konnektivität. Sie müssen nicht all diese Anwendungsfälle abdecken, um mit der Nutzung von Virtual WAN beginnen zu können. Sie können einfach mit nur einem Anwendungsfall loslegen und das Netzwerk dann abhängig von der weiteren Entwicklung anpassen.

Die Virtual WAN-Architektur ist eine Hub-and-Spoke-Architektur mit integrierter Skalierung und Leistung für Zweigstellen (VPN-/SD-WAN-Geräte), Benutzer (Azure-VPN-, OpenVPN- oder IKEv2-Clients), ExpressRoute-Leitungen und virtuelle Netzwerke. Sie ermöglicht eine [Architektur mit einem globalen Transitnetzwerk](virtual-wan-global-transit-network-architecture.md), bei dem der in der Cloud gehostete Netzwerkhub Übertragungsverbindungen zwischen Endpunkten ermöglicht, die sich in unterschiedlichen Typen von Spokes befinden.

Azure-Regionen dienen als Hubs, die Sie auswählen können, um sich mit ihnen zu verbinden. Alle Hubs sind per Standard-Virtual WAN vollständig miteinander vernetzt, damit Benutzer den Microsoft-Backbone für die Any-to-Any-Konnektivität (alle Spokes) nutzen können. Für Spokekonnektivität mit SD-WAN/VPN-Geräten können Benutzer dies entweder manuell in Azure Virtual WAN einrichten oder die Partnerlösung für Virtual WAN CPE (SD-WAN/VPN) nutzen, um Konnektivität mit Azure einzurichten. Wir führen eine Liste mit Partnern, die die Automatisierung der Konnektivität mit Azure Virtual WAN unterstützen (sie bieten die Möglichkeit, die Geräteinformationen in Azure zu exportieren, die Azure-Konfiguration herunterzuladen und Konnektivität herzustellen). Weitere Informationen finden Sie in dem Artikel [Virtual WAN-Partner und -Standorte](virtual-wan-locations-partners.md).

![Virtual WAN-Diagramm](./media/virtual-wan-about/virtualwan1.png)

Dieser Artikel enthält eine kurze Übersicht über die Netzwerkkonnektivität in Azure Virtual WAN. Virtual WAN hat die folgenden Vorteile:

* **Integrierte Konnektivitätslösungen (Hub & Spoke):** Automatisieren Sie die Site-to-Site-Konfiguration und -Konnektivität zwischen lokalen Standorten und einem Azure-Hub.
* **Automatisierte Spoke-Einrichtung und -Konfiguration:** Verbinden Sie Ihre virtuellen Netzwerke und Workloads nahtlos mit dem Azure-Hub.
* **Intuitive Problembehandlung:** Sie können den gesamten Datenfluss in Azure anzeigen und diese Informationen nutzen, um erforderliche Aktionen durchzuführen.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Virtuelle WANs des Typs „Basic“ und „Standard“

Es gibt zwei Arten virtueller WANs: Basic und Standard. Die folgende Tabelle zeigt die verfügbaren Konfigurationen für die beiden Typen.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Anweisungen für ein Upgrade eines virtuellen WAN finden Sie unter [Upgrade eines virtuellen WAN von Basic auf Standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektur

Informationen zur Virtual WAN-Architektur sowie zum Migrieren zu Virtual WAN finden Sie in den folgenden Artikeln:

* [Migrieren zu Azure Virtual WAN](migrate-from-hub-spoke-topology.md)
* [Architektur mit einem globalen Transitnetzwerk und Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-Ressourcen

Sie erstellen die folgenden Ressourcen, um ein virtuelles End-to-End-WAN zu konfigurieren:

* **virtualWAN:** Die Ressource „virtualWAN“ stellt eine virtuelle Überlagerung Ihres Azure-Netzwerks dar und ist eine Sammlung aus mehreren Ressourcen. Sie enthält Links zu allen virtuellen Hubs, die Teil des virtuellen WAN sein sollen. Virtual WAN-Ressourcen sind voneinander isoliert und können keinen gemeinsamen Hub enthalten. Virtuelle Hubs in Virtual WAN kommunizieren nicht miteinander.

* **Hub:** Ein virtueller Hub ist ein von Microsoft verwaltetes virtuelles Netzwerk. Der Hub enthält verschiedene Dienstendpunkte zum Ermöglichen von Konnektivität. In Ihrem lokalen Netzwerk (vpnsite) können Sie sich innerhalb des virtuellen Hubs mit einer VPN Gateway-Instanz verbinden, ExpressRoute-Leitungen mit einem virtuellen Hub verbinden oder sogar mobile Benutzer mit einem Point-to-Site-Gateway im virtuellen Hub verbinden. Der Hub ist der Kern Ihres Netzwerks in einer Region. Es kann nur ein Hub pro Azure-Region vorhanden sein.

  Ein Hub-Gateway ist nicht das gleiche wie ein Gateway für virtuelle Netzwerke, das Sie für ExpressRoute und VPN Gateway verwenden. Bei der Verwendung von Virtual WAN erstellen Sie beispielsweise von Ihrer lokalen Site aus keine direkte Site-to-Site-Verbindung mit Ihrem VNET. Stattdessen erstellen Sie eine Site-to-Site-Verbindung mit dem Hub. Der Datenverkehr verläuft immer über das Hub-Gateway. Dies bedeutet, dass Ihre VNETs kein eigenes Gateway für virtuelle Netzwerke benötigen. Mit Virtual WAN können Sie für Ihre VNETs über den virtuellen Hub und das virtuelle Hub-Gateway leicht eine Skalierung durchführen.

* **Virtuelle Netzwerkverbindung für Hub:** Die Ressource für die virtuelle Netzwerkverbindung für den Hub wird verwendet, um den Hub nahtlos mit Ihrem virtuellen Netzwerk zu verbinden.

* **Hub-zu-Hub-Verbindung:** In einem virtuellen WAN sind alle Hubs miteinander verbunden. Dies bedeutet, dass mit einem lokalen Hub verbundene Niederlassungen, Benutzer oder VNETs mit einer anderen Niederlassung oder anderen VNETs mithilfe der vollständig vernetzten Architektur der verbundenen Hubs kommunizieren können. Sie können auch über das vernetzte Hub-zu-Hub-Framework VNETs innerhalb eines Hubs, der durch den virtuellen Hub führt, sowie VNETs zwischen Hubs verbinden.

* **Hubroutingtabelle:**  Sie können eine virtuelle Hubroute erstellen und die Route der Routingtabelle des virtuellen Hubs zuweisen. Sie können der Routingtabelle des virtuellen Hubs mehrere Routen zuweisen.

**Zusätzliche Virtual WAN-Ressourcen**

* **Site:** Diese Ressource wird ausschließlich für Site-to-Site-Verbindungen verwendet. Die Ressource „site“ ist **vpnsite**. Sie stellt Ihr lokales VPN-Gerät und die zugehörigen Einstellungen dar. Durch die Zusammenarbeit mit einem Virtual WAN-Partner verfügen Sie über eine integrierte Lösung zum automatischen Exportieren dieser Informationen nach Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Konnektivitätstypen

Virtual WAN lässt die folgenden Konnektivitätstypen zu: Site-to-Site-VPN, Benutzer-VPN (Point-to-Site) und ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Site-to-Site-VPN-Verbindungen

Sie können sich über eine Site-to-Site-IPsec-/IKE (IKEv2)-Verbindung mit Ihren Ressourcen in Azure verbinden. Weitere Informationen finden Sie unter [Erstellen einer Site-to-Site-Verbindung per Virtual WAN](virtual-wan-site-to-site-portal.md). 

Für diese Art von Verbindung wird ein VPN-Gerät oder ein Gerät von einem Virtual WAN-Partner benötigt. Virtual WAN-Partner ermöglichen die Automatisierung in Bezug auf die Konnektivität. Hierbei handelt es sich um eine Option zum Exportieren der Geräteinformationen nach Azure, Herunterladen der Azure-Konfiguration und Herstellen der Konnektivität mit dem Azure Virtual WAN-Hub. Eine Liste der verfügbaren Partner und Standorte finden Sie in dem Artikel [Virtual WAN-Partner und -Standorte](virtual-wan-locations-partners.md). Wenn Ihr VPN-/SD-WAN-Geräteanbieter im erwähnten Link nicht angegeben ist, können Sie einfach die Schritt-für-Schritt-Anleitung unter [Erstellen einer Site-to-Site-Verbindung per Azure Virtual WAN](virtual-wan-site-to-site-portal.md) verwenden, um die Verbindung einzurichten.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Benutzer-VPN-Verbindungen (Point-to-Site)

Sie können sich über eine IPsec/IKE (IKEv2)- oder OpenVPN-Verbindung mit Ihren Ressourcen in Azure verbinden. Für diese Art von Verbindung muss auf dem Clientcomputer ein VPN-Client konfiguriert sein. Weitere Informationen finden Sie unter [Herstellen einer Point-to-Site-Verbindung](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-Verbindungen
Mit ExpressRoute können Sie Ihr lokales Netzwerk über eine private Verbindung mit Azure verbinden. Informationen zum Erstellen der Verbindung finden Sie unter [Erstellen einer ExpressRoute-Verbindung per Virtual WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Hub-zu-VNET-Verbindungen

Sie können ein virtuelles Azure-Netzwerk mit einem virtuellen Hub verbinden. Weitere Informationen finden Sie unter [Verbinden Ihres VNET mit einem Hub](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Transitkonnektivität

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Transitkonnektivität zwischen VNETs

Virtual WAN ermöglicht Transitkonnektivität zwischen VNETs. VNETs stellen über eine virtuelle Netzwerkverbindung eine Verbindung mit einem virtuellen Hub her. Die Transitkonnektivität zwischen den VNETs unter **Virtual WAN Standard** wird ermöglicht, weil auf jedem virtuellen Hub ein Router vorhanden ist. Dieser Router wird bei der anfänglichen Erstellung des virtuellen Hubs instanziiert.

Der Router kann über vier Routingstatus verfügen: „Bereitgestellt“, „Wird bereitgestellt“, „Fehler“ oder „Keine“. Den **Routingstatus** finden Sie im Azure-Portal, indem Sie zur Seite des virtuellen Hubs navigieren.

* Mit dem Status **Keine** wird angegeben, dass der Router nicht vom virtuellen Hub bereitgestellt wurde. Dies kann passieren, wenn das Virtual WAN den Typ *Basic* aufweist oder wenn der virtuelle Hub vor dem Verfügbarmachen des Diensts bereitgestellt wurde.
* Mit dem Status **Fehler** wird angegeben, dass während der Instanziierung ein Fehler aufgetreten ist. Zum Instanziieren oder Zurücksetzen des Routers können Sie die Option **Router zurücksetzen** verwenden. Navigieren Sie hierzu im Azure-Portal zur Übersichtsseite des virtuellen Hubs.

Für jeden Router eines virtuellen Hubs wird ein aggregierter Durchsatz von bis zu 50 GBit/s unterstützt. Für die Konnektivität zwischen den VNET-Verbindungen wird übergreifend für alle mit einem einzelnen virtuellen Hub verbundenen VNETs eine Gesamtworkload von 2.000 VMs vorausgesetzt.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Transitkonnektivität zwischen VPN und ExpressRoute

Virtual WAN ermöglicht Transitkonnektivität zwischen VPN und ExpressRoute. Dies impliziert, dass per VPN verbundene Sites oder Remotebenutzer mit Sites, die per ExpressRoute verbunden sind, kommunizieren können. Außerdem wird implizit vorausgesetzt, dass das **Branch-to-Branch-Flag** aktiviert ist und BGP bei VPN- und ExpressRoute-Verbindungen unterstützt wird. Dieses Flag finden Sie in den Azure Virtual WAN-Einstellungen im Azure-Portal. Die gesamte Routenverwaltung wird vom Router des virtuellen Hubs bereitgestellt, der auch die Transitkonnektivität zwischen virtuellen Netzwerken ermöglicht.

### <a name="custom-routing"></a><a name="routing"></a>Benutzerdefiniertes Routing

Virtual WAN verfügt über erweiterte Routingoptionen. Beispiele hierfür sind die Einrichtung von benutzerdefinierten Routingtabellen, Optimierung des VNET-Routings mit Routenzuordnung und -verteilung, logischer Gruppierung von Routingtabellen mit Bezeichnungen und Vereinfachung vieler Routingszenarien mit virtuellen Netzwerkgeräten (Network Virtual Appliance, NVA) oder gemeinsam genutzten Diensten.

### <a name="global-vnet-peering"></a><a name="global"></a>Globales VNET-Peering

Beim globalen VNET-Peering kann ein Mechanismus zum Verbinden von zwei VNETs in unterschiedlichen Regionen genutzt werden. Bei Virtual WAN werden VNETs über VNET-Verbindungen mit virtuellen Hubs verbunden. Der Benutzer muss das globale VNET-Peering nicht explizit einrichten. Wenn VNETs mit virtuellen Hubs in derselben Region verbunden werden, fallen Gebühren für das VNET-Peering an. Für VNETs, die mit einem virtuellen Hub in einer anderen Region verbunden werden, fallen Gebühren für das globale VNET-Peering an.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>Verschlüsselung von ExpressRoute-Datenverkehr

Azure Virtual WAN ermöglicht die Verschlüsselung Ihres ExpressRoute-Datenverkehrs. Bei dieser Methode kann eine verschlüsselte Übertragung zwischen den lokalen Netzwerken und virtuellen Azure-Netzwerken über ExpressRoute ermöglicht werden, ohne dass die Übertragung über das öffentliche Internet erfolgt oder öffentliche IP-Adressen verwendet werden. Weitere Informationen finden Sie unter [IPsec über ExpressRoute für Virtual WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Standorte

Informationen zu Standorten finden Sie in dem Artikel [Virtual WAN-Partner und -Standorte](virtual-wan-locations-partners.md).

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Routingtabellen für Virtual WAN-Instanzen vom Typ „Basic“ und „Standard“

Routingtabellen verfügen jetzt über Features für die Zuordnung und Verteilung. Wenn eine Routingtabelle bereits vorhanden ist, verfügt sie nicht über diese Features. Falls bei Ihnen im Rahmen des Hub-Routings bereits vorhandene Routen genutzt werden und Sie diese neuen Funktionen nutzen möchten, sollten Sie Folgendes beachten:

* **Kunden mit Virtual WAN vom Typ „Standard“ mit bereits vorhandenen Routen auf dem virtuellen Hub**: Wenn im Azure-Portal im Abschnitt „Routing“ für den Hub bereits Routen vorhanden sind, müssen Sie diese zuerst löschen und dann versuchen, neue Routingtabellen zu erstellen (verfügbar im Abschnitt „Routingtabellen“ für den Hub im Azure-Portal). Es wird dringend empfohlen, den Löschschritt für alle Hubs in einer Virtual WAN-Instanz durchzuführen.

* **Kunden mit Virtual WAN vom Typ „Basic“ mit bereits vorhandenen Routen auf dem virtuellen Hub**: Wenn im Azure-Portal im Abschnitt „Routing“ für den Hub bereits Routen vorhanden sind, müssen Sie diese zuerst löschen und dann Ihre Virtual WAN-Instanz vom Typ „Basic“ auf „Standard“ **upgraden**. Weitere Informationen finden Sie unter [Upgrade eines Virtual WAN von Basic auf Standard](upgrade-virtual-wan.md). Es wird dringend empfohlen, den Löschschritt für alle Hubs in einer Virtual WAN-Instanz durchzuführen.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Neuerungen

Abonnieren Sie den RSS-Feed, und zeigen Sie die neuesten Virtual WAN-Featureupdates auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN) an.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Site-to-Site-Verbindung per Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
