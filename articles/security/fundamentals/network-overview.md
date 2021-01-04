---
title: Konzepte und Anforderungen im Zusammenhang mit der Netzwerksicherheit in Azure | Microsoft-Dokumentation
description: Dieser Artikel umfasst grundlegende Erklärungen zu Kernkonzepten und Anforderungen im Bereich Netzwerksicherheit und Informationen über die Vorteile von Azure in jedem dieser Bereiche.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 8a82fd6e6dc1bea0804c429f3af46884f004de69
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489767"
---
# <a name="azure-network-security-overview"></a>Übersicht über die Netzwerksicherheit in Azure

Netzwerksicherheit kann als der Prozess zum Schutz von Ressourcen vor unbefugtem Zugriff oder einem Angriff durch Anwenden von Steuerelementen für den Netzwerkdatenverkehr definiert werden. Ziel ist, dafür zu sorgen, dass nur berechtigter Datenverkehr zugelassen wird. Azure verfügt über eine robuste Netzwerkinfrastruktur zur Unterstützung der Konnektivitätsanforderungen Ihrer Anwendungen und Dienste. Netzwerkkonnektivität ist zwischen Ressourcen in Azure, zwischen lokalen und in Azure gehosteten Ressourcen und zu und aus dem Internet und Azure möglich.

In diesem Artikel werden einige der in Azure angebotenen Optionen im Bereich Netzwerksicherheit erläutert. Sie erhalten Informationen zu folgenden Bereichen:

* Azure-Netzwerke
* Die Netzwerkzugriffssteuerung
* Azure Firewall
* Ein sicherer Remotezugriff und standortübergreifende Konnektivität
* Verfügbarkeit
* Namensauflösung
* Umkreisnetzwerkarchitektur (DMZ)
* Azure-DDoS-Schutz
* Azure Front Door
* Traffic Manager
* Überwachung und Bedrohungserkennung

## <a name="azure-networking"></a>Azure-Netzwerke

Azure erfordert von einem virtuellen Computer eine Verbindung mit einem virtuellen Azure-Netzwerk. Ein virtuelles Netzwerk ist ein logisches Konstrukt, das auf dem physischen Azure-Netzwerkfabric basiert. Jedes virtuelle Netzwerk wird von allen anderen virtuellen Netzwerken isoliert. Dadurch wird sichergestellt, dass der Netzwerkdatenverkehr in Ihren Bereitstellungen nicht für andere Azure-Kunden zugänglich ist.

Weitere Informationen:

* [Virtuelle Netzwerke im Überblick](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Die Netzwerkzugriffssteuerung

Netzwerkzugriffssteuerung bedeutet, die Konnektivität zu oder von bestimmten Geräten oder Subnetzen innerhalb eines virtuellen Netzwerks zu begrenzen. Das Ziel der Netzwerkzugriffssteuerung ist die Beschränkung des Zugriffs auf Ihre virtuellen Computer und Dienste auf genehmigte Benutzer und Geräte. Zugriffssteuerungen basieren auf den Entscheidungen, Verbindungen zu und von virtuellen Computern oder Diensten zuzulassen oder zu verweigern.

Azure unterstützt verschiedene Typen von Netzwerkzugriffssteuerungen. Diese umfassen:

* Die Steuerung der Vermittlungsschicht
* Routensteuerung und Tunnelerzwingung
* Appliances für die Sicherheit des virtuellen Netzwerks

### <a name="network-layer-control"></a>Die Steuerung der Vermittlungsschicht

Jede sichere Bereitstellung erfordert ein gewisses Maß an Netzwerkzugriffssteuerung. Das Ziel der Netzwerkzugriffssteuerung ist die Einschränkung der Kommunikation virtueller Computer auf die erforderlichen Systeme. Andere Verbindungsversuche werden blockiert.

> [!NOTE]
> Storage-Firewalls werden im Artikel [Übersicht über die Sicherheit von Azure Storage](../../storage/blobs/security-recommendations.md) behandelt.

#### <a name="network-security-rules-nsgs"></a>Netzwerksicherheitsgruppen (NSGs)

Wenn Sie eine einfache Zugriffssteuerung auf Netzwerkebene benötigen (basierend auf IP-Adresse und TCP- oder UDP-Protokollen), können Sie Netzwerksicherheitsgruppen verwenden. Eine Netzwerksicherheitsgruppe (NSG) ist eine einfache Firewall, die zustandsbehaftete Pakete filtert und die Zugriffssteuerung auf Grundlage eines [5-Tupels](https://www.techopedia.com/definition/28190/5-tuple) ermöglicht. NSGs umfassen Funktionen zum Vereinfachen der Verwaltung und zum Verringern von Konfigurationsfehlern:

* **Ergänzte Sicherheitsregeln:** vereinfachen die Definition von NSG-Regeln und ermöglichen die Erstellung von komplexen Regeln anstelle der Erstellung mehrerer einfacher Regeln mit dem gleichen Ergebnis.
* **Diensttags:** von Microsoft erstellte Bezeichnungen, die eine Gruppe von IP-Adressen darstellen. Sie werden dynamisch aktualisiert, um IP-Adressbereiche aufzunehmen, die die Bedingungen erfüllen, mit denen die Aufnahme in die Bezeichnung definiert wird. Wenn Sie beispielsweise eine Regel erstellen möchten, die für alle Azure-Speicher in der östlichen Region gilt, können Sie Storage.EastUS verwenden.
* **Anwendungssicherheitsgruppen:** ermöglichen, dass Sie Ressourcen in Anwendungsgruppen bereitstellen und den Zugriff auf diese Ressourcen steuern, indem Sie Regeln erstellen, in denen diese Anwendungsgruppen verwendet werden. Wenn Sie z.B. Webserver in der Anwendungsgruppe „Webserver“ bereitstellen, können Sie eine Regel erstellen, die eine Netzwerksicherheitsgruppe anwendet, die 443-Datenverkehr aus dem Internet in alle Systeme der Anwendungsgruppe „Webserver“ zulässt.

NSGs bieten weder eine Inspektion auf Anwendungsebene noch authentifizierte Zugriffssteuerungen.

Weitere Informationen:

* [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Just-in-Time-VM-Zugriff in ASC

In [Azure Security Center](../../security-center/security-center-introduction.md) können die Netzwerksicherheitsgruppen auf virtuellen Computern verwaltet und der Zugriff auf die virtuellen Computer gesperrt werden, bis ein Benutzer mit den entsprechenden [Azure RBAC](../../role-based-access-control/overview.md)-Berechtigungen (rollenbasierte Zugriffssteuerung in Azure) den Zugriff anfordert. Nachdem der Benutzer erfolgreich autorisiert wurde, nimmt ASC Änderungen an den Netzwerksicherheitsgruppen vor, um den Zugriff auf ausgewählte Ports für den angegebenen Zeitraum zu ermöglichen. Nach Ablauf des Zeitraums werden die NSGs auf ihren vorherigen gesicherten Status zurückgesetzt.

Weitere Informationen:

* [Azure Security Center – Just-in-Time-Zugriff](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Dienstendpunkte

Dienstendpunkte sind eine weitere Möglichkeit der Steuerung des Datenverkehrs. Über eine direkte Verbindung können Sie die Kommunikation mit unterstützten Diensten nur auf Ihre VNETs einschränken. Der Datenverkehr aus Ihrem VNET an den angegebenen Azure-Dienst verbleibt im Backbone-Netzwerk von Microsoft Azure.  

Weitere Informationen:

* [Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Routensteuerung und Tunnelerzwingung

Die Steuerung des Routingverhaltens in Ihren virtuellen Netzwerken ist eine entscheidende Funktion. Wenn das Routing nicht ordnungsgemäß konfiguriert ist, können sich auf dem virtuellen Computer gehostete Anwendungen und Dienste mit nicht autorisierten Geräten verbinden. Dies umfasst auch Systeme, die sich im Besitz potenzieller Angreifer befinden und von diesen betrieben werden.

Azure-Netzwerke unterstützen die Möglichkeit, das Routingverhalten für den Netzwerkdatenverkehr in Ihrem virtuellen Netzwerk benutzerdefiniert anzupassen. Dadurch können Sie die Standardeinträge in der Routingtabelle in Ihrem virtuellen Netzwerk ändern. Mithilfe der Steuerung des Routingverhaltens können Sie sicherstellen, dass der gesamte von einem bestimmten Gerät oder einer Gerätegruppe ausgehende Datenverkehr über einen bestimmten Punkt in Ihr virtuelles Netzwerk eintritt oder es über diesen verlässt.

Beispielsweise verfügen Sie in Ihrem virtuellen Netzwerk möglicherweise über eine Appliance für die Sicherheit des virtuellen Netzwerks. In diesem Fall möchten Sie auch gewährleisten, dass der gesamte ein- und ausgehende Datenverkehr in Ihrem virtuellen Netzwerk Ihre Appliance für die Sicherheit Ihres virtuellen Netzwerks passiert. Sie erreichen dies über die Konfiguration von [benutzerdefinierten Routen](../../virtual-network/virtual-networks-udr-overview.md) (UDRs) in Azure.

Mithilfe der [Tunnelerzwingung](https://www.petri.com/azure-forced-tunneling) können Sie sicherstellen, dass Ihre Dienste nicht über die Erlaubnis verfügen, eine Verbindung mit Geräten im Internet zu initiieren. Beachten Sie, dass sich dieser Mechanismus von der Erlaubnis eingehender Verbindungen und der Antwort auf diese unterscheidet. Front-End-Webserver müssen auf Anforderungen von Internethosts antworten. Aus dem Internet stammender, auf diesen Webservern eingehender Datenverkehr wird daher zugelassen und die Webserver dürfen antworten.

Was Sie nicht erlauben sollten, ist die Initiierung einer ausgehenden Anforderung vonseiten des Front-End-Webservers. Diese Anforderungen können ein Sicherheitsrisiko darstellen, da diese Verbindungen dazu genutzt werden können, Schadsoftware herunterzuladen. Selbst wenn diese Front-End-Server ausgehende Anfragen an das Internet initiieren sollen, möchten Sie möglicherweise die Nutzung Ihrer lokalen Webproxys erzwingen. Dadurch können Sie die URL-Filterung und Protokollierung nutzen.

Um dies zu verhindern, sollten Sie stattdessen die Tunnelerzwingung verwenden. Wenn Sie die Tunnelerzwingung aktivieren, werden alle Verbindungen mit dem Internet zwangsweise über Ihr lokales Gateway hergestellt. Sie können die Tunnelerzwingung konfigurieren, indem Sie benutzerdefinierte Routen nutzen.

Weitere Informationen:

* [Was sind benutzerdefinierte Routen und IP-Weiterleitung?](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Appliances für die Sicherheit des virtuellen Netzwerks

Obwohl Netzwerksicherheitsgruppen, benutzerdefinierte Routen und die Tunnelerzwingung Sicherheit in der Vermittlungs- und Transportschicht des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) bieten, kann es vorkommen, dass Sie Sicherheit auf höheren Ebenen als dem Netzwerk aktivieren möchten.

Ihre Sicherheitsanforderungen können beispielsweise Folgendes beinhalten:

* Authentifizierung und Autorisierung, bevor Zugriff auf Ihre Anwendung gestattet wird
* Angriffserkennung und die Antwort darauf
* Überprüfung der Anwendungsebene für Protokolle auf oberen Ebenen
* URL-Filterung
* Antivirenprogramme und Antischadsoftware auf Netzwerkebene
* Antirobot-Schutz
* Anwendungszugriffssteuerung
* Zusätzlicher DDoS-Schutz (zusätzlich zu dem eigenen DDoS-Schutz von Azure-Fabric)

Sie können mit einer Azure-Partnerlösung auf diese erweiterten Netzwerksicherheitsfunktionen zugreifen. Die aktuellen Azure-Partnerlösungen für die Netzwerksicherheit finden Sie im [Azure Marketplace](https://azure.microsoft.com/marketplace/), indem Sie nach den Stichwörtern „Sicherheit“ und „Netzwerksicherheit“ suchen.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall als ein Dienst mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit. Folgende Features sind enthalten:

* Hochverfügbarkeit
* Cloudskalierbarkeit
* FQDN-Anwendungsfilterregeln
* Filterregeln für den Netzwerkdatenverkehr

Weitere Informationen:

* [Übersicht über Azure Firewall](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Ein sicherer Remotezugriff und standortübergreifende Konnektivität

Die Einrichtung, Konfiguration und Verwaltung Ihrer Azure-Ressourcen muss remote durchgeführt werden. Darüber hinaus möchten Sie vielleicht [Hybrid-IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)-Lösungen bereitstellen, die Komponenten sowohl lokal als auch öffentlich in der Azure-Cloud bereitstellen. Diese Szenarien erfordern einen sicheren Remotezugriff.

Azure-Netzwerke unterstützen die folgenden Szenarien für den sicheren Remotezugriff:

* Verbinden einzelner Arbeitsstationen mit einem virtuellen Netzwerk
* Verbinden lokaler Netzwerke mit einem virtuellen Netzwerk per VPN
* Verbinden lokaler Netzwerke mit einem virtuellen Netzwerk mithilfe eines dedizierten WAN-Links
* Verbinden virtueller Netzwerke untereinander

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Verbinden einzelner Arbeitsstationen mit einem virtuellen Netzwerk

Möglicherweise möchten Sie es einzelnen Entwicklern oder Betriebsmitarbeitern gestatten, virtuelle Computer und Dienste in Azure zu verwalten. Angenommen, Sie benötigen Zugriff auf einen virtuellen Computer in einem virtuellen Netzwerk. Ihre Sicherheitsrichtlinie lässt jedoch keinen RDP- oder SSH-Remotezugriff auf einzelne virtuelle Computer zu. In diesem Fall können Sie eine [Point-to-Site-VPN](../../vpn-gateway/point-to-site-about.md)-Verbindung nutzen.

Mit der Point-to-Site-VPN-Verbindung können Sie eine private und sichere Verbindung zwischen dem Benutzer und dem virtuellen Netzwerk einrichten. Wenn die VPN-Verbindung hergestellt wurde, kann der Benutzer über die VPN-Verknüpfung mit RDP oder SSH auf jeden virtuellen Computer im virtuellen Netzwerk zugreifen. (Dies setzt voraus, dass der Benutzer sich authentifizieren kann und autorisiert ist.) Ein Point-to-Site-VPN unterstützt Folgendes:

* Secure Socket Tunneling-Protokoll (SSTP), ein proprietäres SSL-basiertes VPN-Protokoll. Eine SSL-VPN-Lösung kann Firewalls durchdringen, da die meisten Firewalls den von TLS/SSL verwendeten TCP-Port 443 öffnen. SSTP wird nur auf Windows-Geräten unterstützt. Azure unterstützt alle Versionen von Windows, die über SSTP verfügen (Windows 7 und höher).

* IKEv2-VPN, eine standardbasierte IPsec-VPN-Lösung. IKEv2-VPN kann zum Herstellen einer Verbindung von Mac-Geräten (OSX-Version 10.11 und höher) verwendet werden.

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Weitere Informationen:

* [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung mit einem virtuellen Netzwerk mithilfe von PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Verbinden lokaler Netzwerke mit einem virtuellen Netzwerk per VPN

Möglicherweise möchten Sie Ihr gesamtes Unternehmensnetzwerk oder Teile davon mit einem virtuellen Netzwerk verbinden. Dies ist in hybriden IT-Szenarien üblich, in denen Unternehmen [ihre lokalen Rechenzentren auf Azure ausweiten](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). Oftmals hosten Unternehmen bestimmte Bereiche eines Diensts in Azure und andere Bereiche lokal. Dies kann z.B. der Fall sein, wenn eine Lösung Front-End-Webserver in Azure und lokale Back-End-Datenbanken enthält. Diese Arten von „standortübergreifenden“ Verbindungen erhöhen die Sicherheit der in Azure verwalteten Ressourcen und ermöglichen Szenarien wie das Erweitern des Active Directory-Domänencontrollers auf Azure.

Sie erreichen dies zum Beispiel über ein [Site-to-Site-VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Der Unterschied zwischen einem Site-to-Site-VPN und einem Point-to-Site-VPN ist, dass Letzteres ein einzelnes Gerät mit einem virtuellen Netzwerk verbindet. Ein Site-to-Site-VPN verbindet hingegen ein ganzes Netzwerk (z.B. Ihr lokales Netzwerk) mit einem virtuellen Netzwerk. Site-to-Site-VPN-Verbindungen mit einem virtuellen Netzwerk verwenden das äußerst sichere IPsec-VPN-Protokoll im Tunnelmodus.

Weitere Informationen:

* [Erstellen eines Resource Manager-VNET mit Site-to-Site-VPN-Verbindung über das Azure-Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Informationen zu VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Verbinden lokaler Netzwerke mit einem virtuellen Netzwerk mithilfe eines dedizierten WAN-Links

Punkt-zu-Standort und Standort-zu-Standort-VPN-Verbindungen sind effektive Methoden, um die standortübergreifende Konnektivität zu aktivieren. Allerdings weisen sie, einigen Unternehmen zufolge, die folgenden Nachteile auf:

* VPN-Verbindungen verschieben Daten über das Internet. Bei der Datenverschiebung über ein öffentliches Netzwerk weisen diese Verbindungen möglicherweise Sicherheitsprobleme auf. Darüber hinaus können die Zuverlässigkeit und Verfügbarkeit dieser Internetverbindungen nicht garantiert werden.
* VPN-Verbindungen mit virtuellen Netzwerken können für einige Anwendungen und Zwecke nicht ausreichend Bandbreite bereitstellen, da sie ihr Maximum bei etwa 200 MB/s erreichen.

Organisationen, die die höchsten Sicherheits- und Verfügbarkeitsstufen für ihre standortübergreifenden Verbindungen benötigen, verwenden in der Regel dedizierte WAN-Links für die Verbindung zu Remotestandorten. Azure bietet Ihnen die Möglichkeit, einen dedizierten WAN-Link zu verwenden, um Ihr lokales Netzwerk mit einem virtuellen Netzwerk zu verbinden. Azure ExpressRoute, ExpressRoute Direct und ExpressRoute Global Reach ermöglichen dies.

Weitere Informationen:

* [ExpressRoute - Technische Übersicht](../../expressroute/expressroute-introduction.md)
* [ExpressRoute Direct](../../expressroute/expressroute-erdirect-about.md)
* [ExpressRoute Global Reach](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Verbinden virtueller Netzwerke untereinander

Es ist möglich, viele virtuelle Netzwerke für Ihre Bereitstellungen zu verwenden. Es gibt zahlreiche Gründe, warum dies sinnvoll sein kann. Möglicherweise soll die Verwaltung vereinfacht oder die Sicherheit erhöht werden. Unabhängig von den Beweggründen hinter der Platzierung der Ressourcen in verschiedenen virtuellen Netzwerken möchten Sie in einigen Fällen möglicherweise, dass zwischen den Ressourcen eines jeden Netzwerks eine Verbindung hergestellt wird.

Sie erreichen dies z.B., indem die Dienste in einem virtuellen Netzwerk über ein Internet-Loopback eine Verbindung mit Diensten in einem anderen virtuellen Netzwerk herstellen. Die Verbindung beginnt in einem virtuellen Netzwerk, durchläuft das Internet und endet wieder im virtuellen Zielnetzwerk. Bei Verwendung dieser Option wird die Verbindung den für internetbasierte Kommunikation typischen Sicherheitsproblemen ausgesetzt.

Eine bessere Option ist möglicherweise die Erstellung eines Site-to-Site-VPN, das eine Verbindung zwischen zwei virtuellen Netzwerken herstellt. Bei dieser Methode wird das gleiche [IPsec-Protokoll im Tunnelmodus](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wie bei der zuvor erwähnten standortübergreifenden Site-to-Site-VPN-Verbindung verwendet.

Der Vorteil dieses Ansatzes besteht darin, dass die VPN-Verbindung über das Azure-Netzwerkfabric anstatt über das Internet erstellt wird. Dadurch erhalten Sie im Vergleich zu Site-to-Site-VPNs, die Verbindungen über das Internet herstellen, eine zusätzliche Sicherheitsebene.

Weitere Informationen:

* [Konfigurieren einer VNet-zu-VNet-Verbindung mit Azure Resource Manager und PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Eine andere Möglichkeit zum Verbinden Ihrer virtuellen Netzwerke ist das [VNET-Peering](../../virtual-network/virtual-network-peering-overview.md). Über dieses Feature können Sie zwei Azure-Netzwerke so verbinden, dass die Kommunikation zwischen ihnen über die Microsoft-Backbone-Infrastruktur ohne Übertragung über das Internet erfolgt. Über das VNET-Peering können zwei VNETs innerhalb derselben Region oder in verschiedenen Azure-Regionen verbunden werden. Mithilfe von NSGs kann die Konnektivität zwischen verschiedenen Subnetzen oder Systemen eingeschränkt werden.

## <a name="availability"></a>Verfügbarkeit

Verfügbarkeit ist eine der Schlüsselkomponenten eines jeden Sicherheitsprogramms. Wenn Ihre Benutzer und Systeme über das Netzwerk keinen Zugriff auf die benötigten Ressourcen haben, kann der Dienst als gefährdet betrachtet werden. Azure verfügt über Netzwerktechnologien, die die folgenden Mechanismen für hohe Verfügbarkeit unterstützen:

* HTTP-basierter Lastenausgleich
* Lastenausgleich auf Netzwerkebene
* Globaler Lastenausgleich

Der Lastenausgleich ist ein Verfahren, das entwickelt wurde, um Verbindungen zwischen mehreren Geräten gleichmäßig zu verteilen. Die Ziele des Lastenausgleichs sind:

* Erhöhen der Verfügbarkeit: Wenn Sie einen Lastausgleich für Verbindungen auf mehreren Geräten vornehmen, können ein oder mehrere Geräte nicht mehr verfügbar sein, ohne dass dadurch der Dienst beeinträchtigt wird. Die auf den verbleibenden Onlinegeräten ausgeführten Dienste können weiterhin die Inhalte des Diensts bereitstellen.
* Erhöhen der Leistung: Wenn Sie einen Lastausgleich für Verbindungen auf mehreren Geräten vornehmen, muss die gesamte Verarbeitung nicht auf einem Gerät allein erfolgen. Stattdessen werden die Verarbeitungs- und Speicheranforderungen für die Bereitstellung der Inhalte über mehrere Geräte verteilt.

### <a name="http-based-load-balancing"></a>HTTP-basierter Lastenausgleich

Organisationen, die webbasierte Dienste ausführen, möchten für diese Webdienste häufig einen HTTP-basierten Lastenausgleich verwenden. Dadurch kann ein angemessenes Maß an Leistung und Hochverfügbarkeit sichergestellt werden. Herkömmliche netzwerkbasierte Lastenausgleichsmodule basieren auf Vermittlungs- und Transportschichtprotokollen. Die Entscheidungen des HTTP-basierten Lastenausgleichs basieren dagegen auf Merkmalen des HTTP-Protokolls.

Azure Application Gateway umfasst den HTTP-basierten Lastenausgleich für Ihre webbasierten Dienste. Application Gateway unterstützt Folgendes:

* Cookiebasierte Sitzungsaffinität: Diese Funktion stellt sicher, dass der Client mit dem Server hinter dem Lastenausgleich verbunden bleibt. Dies gewährleistet die Stabilität der Transaktionen.
* TLS-Auslagerung Wenn ein Client eine Verbindung mit dem Lastenausgleich herstellt, wird diese Sitzung mithilfe des HTTPS-Protokolls (TLS) verschlüsselt. Sie können jedoch die Verbindung zwischen dem Lastenausgleich und dem Webserver hinter diesem so einrichten, dass das HTTP-Protokoll (unverschlüsselt) verwendet wird, und somit die Leistung erhöhen. Dies wird als „TLS-Auslagerung“ bezeichnet, da die Webserver hinter dem Lastenausgleich die durch die Verschlüsselung hervorgerufene, zusätzliche Prozessorauslastung nicht tragen müssen. Die Webserver können daher Dienstanforderungen schneller erfüllen.
* URL-basiertes Inhaltsrouting: Diese Funktion ermöglicht es dem Lastenausgleich, ausgehend von der Ziel-URL zu entscheiden, wohin Verbindungen weitergeleitet werden sollen. Dies bietet viel mehr Flexibilität als Lösungen, die Lastenausgleichsentscheidungen auf Grundlage von IP-Adressen treffen.

Weitere Informationen:

* [Übersicht über Application Gateway](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Lastenausgleich auf Netzwerkebene

Im Gegensatz zum HTTP-basierten Lastenausgleich werden beim Lastenausgleich auf Netzwerkebene Entscheidungen auf Grundlage der IP-Adresse und der Portnummern (TCP oder UDP) getroffen.
Mit Azure Load Balancer können Sie die Vorteile des Lastenausgleichs auf Netzwerkebene in Azure nutzen. Einige wichtige Eigenschaften von Load Balancer sind:

* Lastenausgleich auf Netzwerkebene auf Grundlage von IP-Adressen und Portnummern
* Unterstützung für alle Anwendungsschichtprotokolle
* Verteilung der Last auf virtuelle Azure-Computer und Rolleninstanzen von Clouddiensten
* Möglichkeit der Verwendung für Anwendungen und virtuelle Computer mit Internetzugriff (externer Lastenausgleich) und ohne Internetzugriff (interner Lastenausgleich)
* Endpunktüberwachung zur Feststellung, ob einer der Dienste hinter dem Lastenausgleich nicht mehr verfügbar ist

Weitere Informationen:

* [Internetlastenausgleich zwischen mehreren virtuellen Computern oder Diensten](../../load-balancer/load-balancer-overview.md)
* [Interner Lastenausgleich (Übersicht)](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Globaler Lastenausgleich

Einige Organisationen entscheiden sich für die höchstmögliche Verfügbarkeit. Dieses Ziel ist z.B. durch das Hosten von Anwendungen in weltweit verteilten Rechenzentren erreichbar. Wenn eine Anwendung in weltweit verteilten Rechenzentren gehostet wird, kann die Anwendung selbst dann noch ausgeführt werden, wenn eine ganze geopolitische Region nicht mehr verfügbar ist.

Diese Lastenausgleichsstrategie kann zudem zu Leistungsvorteilen führen. Sie können an den Dienst gesendete Anforderungen an das Rechenzentrum weiterleiten, das sich am nächsten bei dem Gerät befindet, das die Anforderung gesendet hat.

In Azure können Sie die Vorteile des globalen Lastenausgleichs mithilfe des Azure Traffic Manager nutzen.

Weitere Informationen:

* [Was ist Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Namensauflösung

Die Namensauflösung ist eine Schlüsselfunktion aller Dienste, die in Azure gehostet werden. Eine Kompromittierung der Namensauflösungsfunktion kann für die Sicherheit bedeuten, dass ein Angreifer Anforderungen von Ihren Websites auf die Website eines Angreifers umleiten kann. Eine sichere Namensauflösung ist eine Voraussetzung für alle Ihre gehosteten Clouddienste.

Es gibt zwei Arten der Namensauflösung, die Sie berücksichtigen müssen:

* Interne Namensauflösung: Diese wird von Diensten in Ihren virtuellen Netzwerken und/oder in lokalen Netzwerken verwendet. Auf Namen, die für die interne Namensauflösung verwendet werden, kann nicht über das Internet zugegriffen werden. Für die optimale Sicherheit ist es wichtig, dass Ihr internes Namensauflösungsschema nicht für externe Benutzer zugänglich ist.
* Externe Namensauflösung: Diese wird von Personen und Geräten außerhalb Ihrer lokalen und virtuellen Netzwerke verwendet. Hierbei handelt es sich um die Namen, die im Internet sichtbar sind und dazu verwendet werden, Verbindungen an Ihre cloudbasierten Dienste zu leiten.

Für die interne Namensauflösung stehen Ihnen zwei Optionen zur Verfügung:

* DNS-Server in einem virtuellen Netzwerk: Beim Erstellen eines neuen virtuellen Netzwerks wird ein DNS-Server erstellt. Dieser DNS-Server kann die Namen der Computer in diesem virtuellen Netzwerk auflösen. Dieser DNS-Server kann nicht konfiguriert werden und wird vom Azure-Fabric-Manager verwaltet. Dadurch ist diese Lösung für die Namensauflösung sicher.
* Bring Your Own DNS-Server (Verwenden eines eigenen DNS-Servers): Sie haben die Möglichkeit, einen DNS-Server Ihrer Wahl in Ihrem virtuellen Netzwerk bereitzustellen. Dieser DNS-Server kann ein in Active Directory integrierter DNS-Server oder eine dedizierte DNS-Serverlösung sein, die von einem Azure-Partner bereitgestellt wird und die Sie im Azure Marketplace erhalten.

Weitere Informationen:

* [Virtuelle Netzwerke im Überblick](../../virtual-network/virtual-networks-overview.md)
* [Verwalten von DNS-Servern, die von einem virtuellen Netzwerk verwendet werden](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Für die externe Namensauflösung stehen Ihnen zwei Optionen zur Verfügung:

* Das lokale Hosten Ihres eigenen externen DNS-Servers
* Das Hosten Ihres eigenen externen DNS-Servers bei einem Dienstanbieter

Viele große Organisationen hosten ihre eigenen DNS-Server lokal. Sie können dies, da sie über die notwendigen Fachkenntnisse im Bereich Netzwerke und die globale Präsenz verfügen.

In den meisten Fällen ist es besser, Ihre DNS-Namensauflösungsdienste bei einem Dienstanbieter zu hosten. Diese Dienstanbieter verfügen über die Fachkenntnisse im Bereich Netzwerke und eine globale Präsenz, sodass eine sehr hohe Verfügbarkeit Ihrer Namensauflösungsdienste gewährleistet werden kann. Verfügbarkeit ist entscheidend für DNS-Dienste, denn falls bei den Namensauflösungsdiensten Fehler auftreten, kann keine Verbindung zu Ihren mit dem Internet verbundenen Diensten hergestellt werden.

Azure bietet Ihnen eine hochverfügbare und hochleistungsfähige externe DNS-Lösung in Form von Azure DNS. Diese externe Namensauflösungslösung nutzt die weltweite Azure DNS-Infrastruktur. Sie können damit Ihre Domänen in Azure mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnung wie für die anderen Azure-Dienste hosten. Als Teil von Azure bietet Azure DNS auch die strengen Sicherheitskontrollen, die in die Plattform integriert sind.

Weitere Informationen:

* [Azure DNS – Übersicht](../../dns/dns-overview.md)
* Mit [Azure DNS für private Domänen](../../dns/private-dns-overview.md) können Sie private DNS-Namen für Azure-Ressourcen konfigurieren, anstatt die automatisch zugewiesenen Namen zu verwenden. Dazu müssen Sie keine benutzerdefinierte DNS-Lösung hinzufügen.

## <a name="perimeter-network-architecture"></a>Umkreisnetzwerkarchitektur

Viele große Organisationen verwenden Umkreisnetzwerke zum Segmentieren ihrer Netzwerke, um eine Pufferzone zwischen dem Internet und ihren Diensten zu erstellen. Der Umkreisbereich des Netzwerks wird als niedrige Sicherheitszone eingestuft. Daher werden keine wertvollen Ressourcen in diesem Netzwerksegment platziert. Normalerweise finden sich dort Netzwerksicherheitsgeräte, die über eine Netzwerkschnittstelle mit dem Umkreisnetzwerksegment verfügen. Eine andere Netzwerkschnittstelle ist mit einem Netzwerk mit virtuellen Computern und Diensten verbunden, die über das Internet eingehende Verbindungen akzeptieren.

Sie können Umkreisnetzwerke auf verschiedene Weise entwerfen. Die Entscheidung, ob und welche Art von Umkreisnetzwerk bereitgestellt werden soll, basiert auf Ihren Anforderungen zur Netzwerksicherheit.

Weitere Informationen:

* [Microsoft Cloud Services und Netzwerksicherheit](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure-DDoS-Schutz

DDoS-Angriffe (Distributed Denial of Service) stellen eines der größten Verfügbarkeits- und Sicherheitsprobleme für Kunden dar, die ihre Anwendungen in die Cloud verschieben. Ein DDoS-Angriff hat das Ziel, die Ressourcen einer Anwendung zu verbrauchen, damit sie für berechtigte Benutzer nicht mehr verfügbar ist. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden.
Microsoft bietet DDoS Protection **Basic** als Bestandteil der Azure-Plattform. Dieser Dienst ist kostenlos und umfasst die Always On-Datenverkehrsüberwachung und Risikominderung von häufig vorkommenden Angriffen auf Netzwerkebene in Echtzeit. Zusätzlich zum Schutz durch DDoS Protection **Basic** können Sie die Option **Standard** aktivieren. Die Funktionen von DDoS Protection Standard umfassen:

* **Native Plattformintegration:** Nativ in Azure integriert. Umfasst die Konfiguration über das Azure-Portal. DDoS Protection Standard erkennt Ihre Ressourcen und die Ressourcenkonfiguration.
* **Sofort einsetzbarer Schutz:** Dank vereinfachter Konfiguration sind alle Ressourcen in einem virtuellen Netzwerk sofort geschützt, sobald DDoS Protection Standard aktiviert wird. Es sind weder Benutzereingriffe noch Benutzerdefinitionen erforderlich. DDoS Protection Standard wehrt einen Angriff sofort automatisch ab, sobald er entdeckt wurde.
* **Stets verfügbare Überwachung des Datenverkehrs:** Die Datenverkehrsmuster Ihrer Anwendungen werden 24 Stunden am Tag und 7 Tage die Woche nach Anzeichen für DDoS-Angriffe überwacht. Abwehrmaßnahmen werden bei Überschreitung der Schutzrichtlinien ausgeführt.
* **Berichte zur Risikominderung nach Angriffen**: Diese Berichte verwenden aggregierte Flowdaten aus dem Netzwerk, um detaillierte Informationen zu Angriffen auf Ihre Ressourcen bereitzustellen.
* **Flowprotokolle zur Risikominderung nach Angriffen**: Mit diesen Protokollen können Sie während eines aktiven DDoS-Angriffs den verworfenen Datenverkehr, den weitergeleiteten Datenverkehr und andere Daten in Zusammenhang mit dem Angriff in Echtzeit prüfen.
* **Adaptive Optimierung:** Dank einer intelligenten Profilerstellung lernt die Funktion den Datenverkehr Ihrer Anwendung kontinuierlich besser kennen. Auf dieser Basis wird das Profil ausgewählt und aktualisiert, das am besten zu Ihrem Dienst passt. Das Profil passt sich den Veränderungen des Datenverkehrs mit der Zeit an. Schutz für Ebene 3 bis Ebene 7: Bietet vollständigen DDoS-Stapelschutz bei Verwendung mit einer Web Application Firewall.
* **Umfangreiche Angriffsabwehr:** Über 60 verschiedene Angriffstypen können mit einer weltweiten Kapazität zum Schutz vor den größten bekannten DDoS-Angriffen abgewehrt werden.
* **Angriffsmetriken:** Mit Azure Monitor kann auf eine Zusammenfassung der Metriken für jeden Angriff zugegriffen werden.
* **Angriffswarnungen:** Mit integrierten Angriffsmetriken können Warnungen am Anfang und Ende eines Angriffs sowie währenddessen konfiguriert werden. Warnungen werden in Ihre Betriebssoftware wie Microsoft Azure Monitor-Protokolle, Splunk, Azure Storage, E-Mail und das Azure-Portal integriert.
* **Kostengarantie:**  Dienstguthaben für Datenübertragung und horizontale Anwendungsskalierung bei dokumentierten DDoS-Angriffen.
* **Schnelle Reaktion bei DDoS-Angriffen**: Kunden von DDoS Protection Standard haben jetzt während eines aktiven Angriffs Zugang zum Rapid Response-Team. Rapid Response hilft bei der Untersuchung von Angriffen, beim Durchführen von kundenspezifischen Maßnahmen während eines Angriffs sowie bei der Analyse nach einem Angriff.


Weitere Informationen:

* [Übersicht über DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Mit dem Azure Front Door-Dienst können Sie das globale Routing Ihres Webdatenverkehrs definieren, verwalten und überwachen. Der Dienst optimiert das Routing des Datenverkehrs im Hinblick auf eine optimale Leistung und hohe Verfügbarkeit. Azure Front Door ermöglicht es Ihnen, WAF-Regeln (Web Application Firewall) für Zugriffssteuerung zu erstellen, um Ihren HTTP/HTTPS-Workload vor Missbrauch auf Basis von Client-IP-Adressen, Landeskennzahl und HTTP-Parametern zu schützen. Darüber hinaus können Sie mit Front Door auch Ratenbegrenzungsregeln erstellen, um schädlichen Botdatenverkehr zu bekämpfen. Der Dienst enthält TLS-Auslagerung und HTTP-/HTTPS-basierte Verarbeitung von Anforderungen auf Anwendungsebene.

Die Front Door-Plattform selbst wird durch Azure DDoS Protection Basic geschützt. Zum weiteren Schutz kann Azure DDoS Protection Standard auf Ihren VNETs aktiviert werden und Ressourcen vor Vermittlungsschichtangriffen (TCP/UDP) über automatische Optimierung und Risikominderung schützen. Front Door ist ein Layer 7-Reverseproxy, der nur Webdatenverkehr über Back-End-Server zulässt und andere Arten von Datenverkehr standardmäßig blockiert.

Weitere Informationen:

* Weitere Informationen zu allen Azure Front Door-Funktionen finden Sie in der [Übersicht zu Azure Front Door](../../frontdoor/front-door-overview.md).

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager ist ein DNS-basierter Lastenausgleich für Datenverkehr, der es Ihnen ermöglicht, den Datenverkehr optimal auf Dienste in den globalen Azure-Regionen zu verteilen, und gleichzeitig Hochverfügbarkeit und Reaktionsfähigkeit sicherzustellen. Traffic Manager verwendet DNS, um Clientanforderungen auf der Grundlage einer Datenverkehrsrouting-Methode und der Integrität der Endpunkte an den optimalen Endpunkt weiterzuleiten. Ein Endpunkt ist ein Dienst mit Internetzugriff, der innerhalb oder außerhalb von Azure gehostet wird. Traffic Manager überwacht die Endpunkt und leitet keinen Datenverkehr an nicht verfügbare Endpunkte weiter.

Weitere Informationen:

* [Azure Traffic Manager – Übersicht](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Überwachung und Bedrohungserkennung

Azure umfasst Funktionen zur Unterstützung in diesem wichtigen Bereich bei der frühen Erkennung, Überwachung sowie Erfassung und Überprüfung des Netzwerkdatenverkehrs.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher kann bei der Problembehandlung verwendet werden und umfasst mehrere komplett neue Tools zur Identifizierung von Sicherheitsproblemen.

Die [Sicherheitsgruppenansicht](../../network-watcher/network-watcher-security-group-view-overview.md) kann für die Überwachung und Sicherheitskonformität von virtuellen Computern verwendet werden. Verwenden Sie dieses Feature, um programmgesteuerte Überprüfungen durchzuführen, bei denen die von Ihrer Organisation definierten Basisrichtlinien mit den effektiven Regeln für die einzelnen virtuellen Computer verglichen werden. Hierdurch können Konfigurationsabweichungen einfacher festgestellt werden.

Die [Paketerfassung](../../network-watcher/network-watcher-packet-capture-overview.md) ermöglicht die Erfassung des Netzwerkdatenverkehrs zur und von der virtuellen Maschine. Sie können Netzwerkstatistiken erfassen und Anwendungsprobleme beheben, was hinsichtlich der Überprüfung von Angriffen auf das Netzwerk sehr nützlich sein kann. Sie können dieses Feature auch zusammen mit Azure Functions verwenden, um als Reaktion auf bestimmte Azure-Warnungen Netzwerkaufnahmen zu starten.

Weitere Informationen zu Network Watcher und zum Testen einiger Funktionen in den Labs finden Sie unter [Übersicht über die Überwachung in Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Aktuelle Hinweise zur Verfügbarkeit und zum Status dieses Diensts finden Sie auf der Seite mit [Azure-Updates](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center unterstützt Sie beim Verhindern, Erkennen und Beheben von Bedrohungen und sorgt für eine größere Transparenz und bessere Kontrolle in Bezug auf die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

Mit Security Center können Sie die Netzwerksicherheit wie folgt optimieren und überwachen:

* Angeben von Empfehlungen zur Netzwerksicherheit
* Überwachen des Zustands der Netzwerksicherheitskonfiguration
* Warnen vor netzwerkbasierten Bedrohungen auf Endpunkt- und Netzwerkebene

Weitere Informationen:

* [Einführung in Azure Security Center](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>TAP eines virtuellen Netzwerks

Mit dem TAP (Terminal Access Point) für virtuelle Azure-Netzwerke können Sie Ihren VM-Netzwerkdatenverkehr kontinuierlich an einen Netzwerkpaketcollector oder ein Analysetool streamen. Der Collector oder das Analysetool wird von einem Partner für virtuelle Netzwerkappliances bereitgestellt. Mit derselben TAP-Ressource für virtuelle Netzwerke können Sie Datenverkehr von mehreren Netzwerkschnittstellen in der gleichen oder in unterschiedlichen Abonnements aggregieren.

Weitere Informationen:

* [TAP eines virtuellen Netzwerks](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Protokollierung

Protokollierung auf Netzwerkebene ist eine entscheidende Funktion für jedes Netzwerksicherheitsszenario. In Azure können Sie Informationen für Netzwerksicherheitsgruppen protokollieren, um Protokollierungsinformationen auf Netzwerkebene zu erhalten. Mit der NSG-Protokollierung erhalten Sie Informationen aus:

* [Aktivitätsprotokolle:](../../azure-monitor/platform/platform-logs-overview.md) In diesen Protokollen können Sie alle Vorgänge anzeigen, die an Ihre Azure-Abonnements übermittelt werden. Diese Protokolle sind standardmäßig aktiviert und können im Azure-Portal verwendet werden. Sie wurden zuvor als „Überwachungsprotokolle“ oder „Betriebsprotokolle“ bezeichnet.
* Ereignisprotokolle: Diese Protokolle enthalten Informationen zu den angewendeten NSG-Regeln.
* Leistungsindikatorenprotokolle: In diesen Protokollen ist aufgezeichnet, wie oft jede NSG-Regel angewendet wurde, um Datenverkehr zuzulassen oder zu verweigern.

Sie können auch das leistungsstarke Datenvisualisierungstool [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/)verwenden, um diese Protokolle anzuzeigen und zu analysieren.
Weitere Informationen:

* [Azure Monitor-Protokolle für Netzwerksicherheitsgruppen (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md)