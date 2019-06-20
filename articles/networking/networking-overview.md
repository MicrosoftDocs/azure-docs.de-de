---
title: Azure-Netzwerke | Microsoft-Dokumentation
description: Informationen zu Azure-Netzwerkdiensten und -funktionen.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 02db9f2b8cb2ec71d23ad077b90eeacb905d2a16
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565795"
---
# <a name="azure-networking"></a>Azure-Netzwerke

Azure bietet eine Vielzahl von Netzwerkfunktionen, die einzeln oder zusammen verwendet werden können. Klicken Sie auf eine der folgenden Schlüsselfunktionen, um weitere Informationen zu erhalten:
- [Konnektivität zwischen Azure-Ressourcen](#connectivity): Stellen Sie in einem sicheren, privaten virtuellen Netzwerk in der Cloud eine Verbindung zwischen Azure-Ressourcen her.
- [Internetkonnektivität](#internet-connectivity): Kommunizieren Sie mit und von Azure-Ressourcen über das Internet.
- [Lokale Konnektivität](#on-premises-connectivity): Stellen Sie eine Verbindung zwischen einem lokalen Netzwerk und Azure-Ressourcen her – entweder unter Verwendung eines virtuellen privaten Netzwerks (VPN) über das Internet oder über eine dedizierte Verbindung mit Azure.
- [Lastenausgleich und Datenverkehrsrichtung](#load-balancing): Führen Sie einen Lastenausgleich für Datenverkehr durch, der für Server am gleichen Standort bestimmt ist, und leiten Sie Datenverkehr an Server an anderen Standorten weiter.
- [Sicherheit:](#security) Filtern Sie den Netzwerkdatenverkehr zwischen Netzwerksubnetzen oder einzelnen virtuellen Computern (virtual machines, VMs).
- [Routing](#routing): Verwenden Sie das Standardrouting, oder steuern Sie in vollem Umfang das Routing zwischen Azure- und lokalen Ressourcen.
- [Verwaltbarkeit:](#manageability) Überwachen und verwalten Sie Ihre Azure-Netzwerkressourcen.
- [Bereitstellungs- und Konfigurationstools](#tools): Verwenden Sie ein webbasiertes Portal oder plattformübergreifende Befehlszeilentools zum Bereitstellen und Konfigurieren von Netzwerkressourcen.

## <a name="connectivity"></a>Konnektivität zwischen Azure-Ressourcen

Azure-Ressourcen wie Virtual Machines, Cloud Services, VM-Skalierungsgruppen und Azure App Service-Umgebungen können privat über ein Azure Virtual Network (VNET) miteinander kommunizieren. Bei einem VNET handelt es sich um eine logische Isolation der Azure-Cloud für Ihr [Abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Sie können in jedem Azure-Abonnement und in jeder Azure-[Region](https://azure.microsoft.com/regions) mehrere VNETs implementieren. Jedes VNet ist von anderen VNets isoliert. Für jedes VNet ist Folgendes möglich:

- Geben Sie einen benutzerdefinierten privaten IP-Adressraum ein, indem Sie öffentliche und private Adressen (RFC 1918) verwenden. Azure weist Ressourcen, die mit dem VNet verbunden sind, eine private IP-Adresse aus dem von Ihnen zugewiesenen Adressraum zu.
- Segmentieren Sie das VNet in mindestens ein Subnetz, und ordnen Sie jedem Subnetz einen Teil des VNet-Adressraums zu.
- Verwenden Sie die in Azure enthaltene Namensauflösung, oder geben Sie Ihren eigenen DNS-Server an, der für die mit einem VNet verbundenen Ressourcen genutzt werden kann.

Weitere Informationen zum Azure Virtual Network-Dienst finden Sie im Artikel mit der [Übersicht über Virtual Network](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json). Sie können VNets miteinander verbinden, sodass Ressourcen, für die eine Verbindung mit einem der VNets besteht, darüber miteinander kommunizieren können. Nutzen Sie eine der folgenden Optionen (oder beide), um VNets miteinander zu verbinden:

- **Peering**: Hierdurch wird ermöglicht, dass Ressourcen, die mit unterschiedlichen Azure-VNETs innerhalb derselben Azure-Region verbunden sind, miteinander kommunizieren können. Die Bandbreite und Wartezeit für die VNets entspricht den Werten, die gelten, wenn die Ressourcen mit demselben VNet verbunden sind. Weitere Informationen zum Peering finden Sie im Artikel mit der [Übersicht über das Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **VPN Gateway**: Hierdurch wird ermöglicht, dass Ressourcen, die mit unterschiedlichen Azure-VNETs innerhalb derselben Azure-Region verbunden sind, miteinander kommunizieren können. Der Datenverkehr zwischen VNETs verläuft über Azure VPN Gateway. Die Bandbreite zwischen VNETs ist auf die Bandbreite des Gateways beschränkt. Weitere Informationen zum Verbinden von VNETs mit VPN Gateway finden Sie im Artikel zur [regionsübergreifenden Konfiguration einer VNET-zu-VNET-Verbindung](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="internet-connectivity"></a>Internetkonnektivität

Alle Azure-Ressourcen, die mit einem VNET verbunden sind, verfügen standardmäßig über Internetkonnektivität in ausgehender Richtung. Die private IP-Adresse der Ressource wird von der Azure-Infrastruktur per SNAT-Vorgang (Source Network Address Translated) in eine öffentliche IP-Adresse übersetzt. Weitere Informationen zur Internetkonnektivität in ausgehender Richtung finden Sie im Artikel [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json).

Um über das Internet in eingehender Richtung mit Azure-Ressourcen bzw. in ausgehender Richtung ohne SNAT über das Internet kommunizieren zu können, muss einer Ressource eine öffentliche IP-Adresse zugewiesen sein. Weitere Informationen zu öffentlichen IP-Adressen finden Sie im Artikel [Öffentliche IP-Adressen](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="on-premises-connectivity"></a>Lokale Konnektivität

Sie können über eine VPN-Verbindung oder eine direkte private Verbindung sicher auf Ressourcen in Ihrem VNET zugreifen. Wenn Sie Netzwerkdatenverkehr zwischen Ihrem virtuellen Azure-Netzwerk und Ihrem lokalen Netzwerk senden möchten, müssen Sie ein Gateway für virtuelle Netzwerke erstellen. Sie konfigurieren Einstellungen für das Gateway, um den gewünschten Verbindungstyp zu erstellen: entweder VPN oder ExpressRoute.

Sie können Ihr lokales Netzwerk mit einem VNet verbinden, indem Sie eine Kombination der folgenden Optionen verwenden:

**Point-to-Site (VPN über SSTP)**

Die folgende Abbildung zeigt separate Point-to-Site-Verbindungen zwischen mehreren Computern und einem VNET:

![Punkt-zu-Standort](./media/networking-overview/point-to-site.png)

Diese Verbindung wird zwischen einem einzelnen Computer und einem VNET eingerichtet. Dieser Verbindungstyp ist gut geeignet, wenn Azure noch neu für Sie ist, oder wenn Sie ein Entwickler sind, da keine oder nur sehr geringe Änderungen Ihres vorhandenen Netzwerks erforderlich sind. Er ist auch praktisch, wenn Sie eine Verbindung von einem Remotestandort aus herstellen, beispielsweise von einer Konferenz oder von zu Hause aus. Point-to-Site-Verbindungen sind häufig mit einer Site-to-Site-Verbindung über dasselbe Gateway für virtuelle Netzwerke gekoppelt. Für die Verbindung wird das SSTP-Protokoll verwendet, um zwischen dem Computer und dem VNET eine verschlüsselte Kommunikation über das Internet zu ermöglichen. Die Dauer der Wartezeit für ein Point-to-Site-VPN ist unvorhersehbar, weil der Datenverkehr über das Internet übertragen wird.

**Site-to-Site (IPsec-/IKE-VPN-Tunnel)**

![Site-to-Site](./media/networking-overview/site-to-site.png)

Diese Verbindung wird zwischen Ihrem lokalen VPN-Gerät und Azure VPN Gateway eingerichtet. Bei diesem Verbindungstyp können alle lokalen Ressourcen, die von Ihnen autorisiert werden, auf das VNET zugreifen. Die Verbindung ist ein IPSec/IKE-VPN, mit dem die verschlüsselte Kommunikation über das Internet zwischen Ihrem lokalen Gerät und dem Azure VPN Gateway ermöglicht wird. Sie können mehrere lokale Standorte mit demselben VPN-Gateway verbinden. Das lokale VPN-Gerät an den einzelnen Standorten benötigt eine externe ausgerichtete öffentliche IP-Adresse, die sich nicht hinter einer NAT befindet. Die Dauer der Wartezeit für eine Site-to-Site-Verbindung ist unvorhersehbar, da der Datenverkehr über das Internet übertragen wird.

**ExpressRoute (dedizierte private Verbindung)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Dieser Verbindungstyp wird zwischen Ihrem Netzwerk und Azure über einen ExpressRoute-Partner eingerichtet. Diese Verbindung ist privat. Der Datenverkehr wird nicht über das Internet übertragen. Die Dauer der Wartezeit für eine ExpressRoute-Verbindung ist vorhersehbar, da der Datenverkehr nicht über das Internet verläuft. ExpressRoute kann mit einer Site-to-Site-Verbindung kombiniert werden.

Weitere Informationen zu allen vorherigen Verbindungsoptionen finden Sie im Artikel [Diagramme zur Verbindungstopologie](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="load-balancing"></a>Lastenausgleich und Datenverkehrsrichtung

Microsoft Azure bietet zahlreiche Dienste für das Verwalten der Verteilung von Netzwerkdatenverkehr und dem Lastenausgleich. Sie können keine der folgenden Funktionen separat oder zusammen verwenden:

**DNS-Lastenausgleich**

Der Azure Traffic Manager-Dienst bietet einen globalen DNS-Lastenausgleich. Der Traffic Manager reagiert auf Clients mit der IP-Adresse eines fehlerfreien Endpunkts, basierend auf einer der folgenden Routingmethoden:
- **Geografisch**: Clients werden anhand des geografischen Ursprungsorts der DNS-Abfrage an bestimmte Endpunkte (Azure, extern oder geschachtelt) geleitet. Diese Methode ermöglicht Szenarien, in denen es wichtig ist, die geografische Region eines Clients zu kennen und dementsprechend zu routen. Zu Beispielen dafür gehören die Einhaltung von Datenhoheitsmandaten, die Verortung von Inhalten und Benutzererfahrungen und das Messen von Datenverkehr aus verschiedenen Regionen.
- **Leistung:** Die an den Client zurückgegebene IP-Adresse ist die Adresse, die dem Client am nächsten ist. Der nächstgelegene Endpunkt ist nicht unbedingt derjenige, der geografisch am nächsten liegt. Vielmehr wird mit dieser Methode der nächstgelegene Endpunkt durch die Messung der Netzwerklatenz bestimmt. Der Traffic Manager überwacht anhand einer Internetlatenztabelle die Roundtripzeit zwischen IP-Adressbereichen und den einzelnen Azure-Rechenzentren.
- **Priorität**: Der Datenverkehr wird an den primären Endpunkt (mit der höchsten Priorität) gesendet. Wenn der primäre Endpunkt nicht verfügbar ist, leitet Traffic Manager den Datenverkehr an den sekundären Endpunkt weiter. Wenn weder der primäre noch der sekundäre Endpunkt verfügbar sind, wird der Datenverkehr an den dritten Endpunkt gesendet usw. Ob ein Endpunkt verfügbar ist, hängt vom konfigurierten Status (aktiviert oder deaktiviert) sowie von der fortlaufenden Endpunktüberwachung ab.
- **Gewichteter Roundrobin:** Für jede Anforderung wählt Traffic Manager einen verfügbaren Endpunkt nach dem Zufallsprinzip aus. Die Wahrscheinlichkeit für die Auswahl eines Endpunkts basiert hierbei auf der Gewichtung, die allen verfügbaren Endpunkten zugewiesen wurde. Die Verwendung der gleichen Gewichtung für alle Endpunkte führt zu einer gleichmäßigen Verteilung des Datenverkehrs. Wenn für bestimmte Endpunkte eine höhere oder niedrigere Gewichtung verwendet wird, werden diese Endpunkte häufiger oder seltener in den DNS-Antworten zurückgegeben.

Die folgende Abbildung zeigt eine Anforderung für eine Webanwendung, die an einen Web-App-Endpunkt weitergeleitet wird. Endpunkte können auch andere Azure-Dienste wie virtuelle Computer und Cloud Services sein.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Der Client stellt eine direkte Verbindung zu diesem Endpunkt her. Der Azure Traffic Manager erkennt, wenn ein Endpunkt fehlerhaft ist, und leitet Clients dann an einen anderen, fehlerfreien Endpunkt weiter. Weitere Informationen zum Traffic Manager finden Sie im Artikel mit der [Übersicht über den Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

**Anwendungslastenausgleich**

Der Azure Application Gateway-Dienst stellt den Application Delivery Controller (ADC) als Dienst bereit. Application Gateway bietet für Ihre Anwendungen verschiedene Lastenausgleichsfunktionen der Ebene 7 (HTTP/HTTPS), z.B. eine Web Application Firewall zum Schutz Ihrer Webanwendungen vor Sicherheitsrisiken und Exploits. Application Gateway erlaubt Ihnen außerdem das Optimieren der Produktivität von Webfarmen, indem Sie die CPU-intensive SSL-Beendigung an das Anwendungsgateway auslagern. 

Andere Routingfunktionen für Ebene 7 umfassen Roundrobin-Verteilung des eingehenden Datenverkehrs, cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis, und Möglichkeit zum Hosten von mehreren Websites hinter einem einzelnen Application Gateway. Application Gateway kann als Gateway mit Internetanbindung, rein internes Gateway oder als Kombination dieser beiden Optionen konfiguriert werden. Application Gateway wird vollständig über Azure verwaltet und ist skalierbar und hoch verfügbar. Die Anwendung umfasst viele Diagnose- und Protokollierungsfunktionen zur Verbesserung der Verwaltbarkeit. Weitere Informationen zu Application Gateway finden Sie im Artikel mit der [Übersicht über Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json).

Das folgende Bild zeigt das auf URL-Pfaden basierende Routing mit Application Gateway:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Netzwerklastenausgleich**

Der Azure Load Balancer ist äußerst leistungsfähig und bietet Lastenausgleich der Ebene 4 mit niedriger Latenz für alle UDP- und TCP-Protokolle. Der Dienst verwaltet eingehende und ausgehende Verbindungen. Sie können öffentliche und interne Endpunkte mit Lastenausgleich konfigurieren. Sie können Regeln definieren, um eingehende Verbindungen anhand von TCP- und HTTP-Integritätstestoptionen zu Back-End-Pool-Zielen zuzuordnen, um die Dienstverfügbarkeit zu verwalten. Weitere Informationen zum Load Balancer finden Sie im Artikel mit der [Übersicht über den Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

Die folgende Abbildung zeigt eine Multi-Tier-Anwendung mit Internetzugriff, die sowohl externen als auch internen Lastenausgleich nutzt:

![Load Balancer](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Sicherheit

Sie können Datenverkehr zu und von Azure-Ressourcen mithilfe der folgenden Optionen filtern:

- **Netzwerk**: Sie können Azure-Netzwerksicherheitsgruppen (NSGs) zum Filtern von eingehendem und ausgehendem Datenverkehr von Azure-Ressourcen implementieren. Jede NSG enthält mindestens eine Regel für eingehenden und ausgehenden Datenverkehr. Jede Regel gibt die Quell-IP-Adressen, die Ziel-IP-Adressen, den Port und das Protokoll an, anhand dessen der Datenverkehr gefiltert wird. NSGs können auf einzelne Subnetze und einzelnen virtuelle Computer angewendet werden. Weitere Informationen zu NSGs finden Sie im Artikel mit der [Übersicht über Netzwerksicherheitsgruppen](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Anwendung**: Durch die Verwendung eines Application Gateway mit der Web Application Firewall können Sie Ihre Webanwendungen vor Sicherheitsrisiken und Exploits schützen. Typische Beispiele sind Angriffe durch Einschleusung von SQL-Befehlen, siteübergreifende Skripterstellung und falsch formatierte Header. Application Gateway filtert diesen Datenverkehr heraus und verhindert, dass er Ihre Webserver erreicht. Sie können konfigurieren, welche Regeln aktiviert werden sollen. Die Möglichkeit zum Konfigurieren von SSL-Aushandlungsrichtlinien wird bereitgestellt, damit bestimmte Richtlinien deaktiviert werden können. Weitere Informationen zur Web Application Firewall finden Sie im Artikel zur [Web Application Firewall](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

Wenn Sie eine Netzwerkfunktion benötigen, die von Azure nicht bereitgestellt wird, oder wenn Sie Netzwerkanwendungen verwenden möchten, die Sie lokal einsetzen, können Sie die Produkte in virtuellen Computern implementieren und mit Ihrem VNET verbinden. Der [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) enthält mehrere verschiedene VMs mit vorkonfigurierten Netzwerkanwendungen, die Sie zurzeit verwenden können. Diese vorkonfigurierten VMs werden in der Regel als virtuelle Netzwerkgeräte bezeichnet. Virtuelle Netzwerkgeräte stehen mit Anwendungen wie Firewall- und WAN-Optimierung zur Verfügung.

## <a name="routing"></a>Routing

Azure erstellt Standardroutentabellen, über die Ressourcen, die mit einem beliebigen Subnetz in einem beliebigen VNET verbunden sind, kommunizieren können. Sie können eine oder beide der folgenden Routentypen implementieren, um die von Azure erstellten Standardrouten außer Kraft zu setzen:
- **Benutzerdefiniert**: Sie können benutzerdefinierte Routingtabellen mit Routen erstellen, über die gesteuert wird, wohin der Datenverkehr für die einzelnen Subnetze geleitet wird. Weitere Informationen zu benutzerdefinierten Routen finden Sie im Artikel [Benutzerdefinierte Routen und IP-Weiterleitung](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Border Gateway Protocol (BGP)** : Wenn Sie Ihr VNET über ein Azure VPN Gateway oder eine ExpressRoute-Verbindung mit dem lokalen Netzwerk verbinden, können Sie die BGP-Routen für Ihre VNets übernehmen. BGP ist das standardmäßige Routingprotokoll, mit dem im Internet üblicherweise Routing- und Erreichbarkeitsinformationen zwischen mehren Netzwerken ausgetauscht werden. Im Kontext virtueller Azure-Netzwerke können Azure VPN Gateway-Instanzen und Ihre lokalen VPN-Geräte (so genannte BGP-Peers oder Nachbarn) über BGP Routen austauschen, die beide Gateways über die Verfügbarkeit und Erreichbarkeit der Präfixe informieren, die die beteiligten Gateways oder Router durchlaufen. BGP ermöglicht auch Transitrouting zwischen mehreren Netzwerken. Hierzu werden von einem BGP-Gateway ermittelte Routen eines BGP-Peers an alle anderen BGP-Peers weitergegeben. Weitere Informationen zu BGP finden Sie im Artikel mit der [Übersicht über BGP mit Azure VPN Gateway-Instanzen](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="manageability"></a>Verwaltbarkeit

Azure bietet die folgenden Tools zum Überwachen und Verwalten von Netzwerken:
- **Aktivitätsprotokolle**: Alle Azure-Ressourcen verfügen über Aktivitätsprotokolle, die Aufschluss über durchgeführte Vorgänge, den Status von Vorgängen sowie den Initiator des Vorgangs geben. Weitere Informationen zu Aktivitätsprotokollen finden Sie im Artikel mit der [Übersicht über Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Diagnoseprotokolle:** Regelmäßige und spontane Ereignisse werden von Netzwerkressourcen erstellt, in Azure-Speicherkonten protokolliert und dann an einen Azure Event Hub oder an Azure Monitor-Protokolle gesendet. Diagnoseprotokolle geben Einblick in die Integrität einer Ressource. Diagnoseprotokolle werden für Load Balancer (mit Internetzugriff), Netzwerksicherheitsgruppen, Routen und Application Gateway bereitgestellt. Weitere Informationen zu Diagnoseprotokollen finden Sie im Artikel mit der [Übersicht über Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Metriken**: Metriken sind Leistungsmessungen und -indikatoren, die über einen Zeitraum für Ressourcen gesammelt werden. Metriken können verwendet werden, um basierend auf Schwellenwerten Warnungen auszulösen. Zurzeit sind Metriken für Application Gateway verfügbar. Weitere Informationen zu Metriken finden Sie im Artikel mit der [Übersicht über Metriken](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Problembehandlung:** Informationen zur Problembehandlung sind direkt über das Azure-Portal zugänglich. Diese Informationen unterstützen Sie bei der Diagnose gängiger Probleme mit ExpressRoute, VPN Gateway, Application Gateway, Netzwerksicherheitsprotokolle, Routen, DNS, Load Balancer und Traffic Manager.
- **Rollenbasierte Zugriffssteuerung (RBAC)** : Steuern Sie mit der rollenbasierten Zugriffssteuerung (RBAC), wer Netzwerkressourcen erstellen und verwalten kann. Weitere Informationen zu RBAC finden Sie im Artikel [Erste Schritte mit RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json). 
- **Paketerfassung:** Der Azure Network Watcher-Dienst bietet die Möglichkeit, eine Paketerfassung auf einem virtuellen Computer über eine Erweiterung innerhalb des virtuellen Computers auszuführen. Diese Funktion ist für VMs unter Linux und Windows verfügbar. Weitere Informationen zur Paketerfassung finden Sie im Artikel mit der [Übersicht zur Paketerfassung](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Überprüfen von IP-Datenflüssen:** Network Watcher ermöglicht Ihnen die Überprüfung von IP-Datenflüssen zwischen einem virtuellen Azure-Computer und einer Remoteressource, um zu ermitteln, ob die Pakete zugelassen oder abgelehnt werden. Diese Funktion bietet Administratoren die Möglichkeit, Konnektivitätsprobleme schnell zu diagnostizieren. Weitere Informationen zum Überprüfen von IP-Flüssen finden Sie im Artikel mit der [Übersicht zur IP-Flussüberprüfung](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Behandeln von VPN-Konnektivitätsproblemen**: Die VPN-Problembehandlungsfunktion von Network Watcher bietet die Möglichkeit, eine Verbindung oder ein Gateway abzufragen und die Integrität der Ressourcen zu überprüfen. Weitere Informationen zur Problembehandlung von VPN-Verbindungen finden Sie im Artikel mit der [Übersicht über die Problembehandlung der VPN-Konnektivität](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Anzeigen der Netzwerktopologie**: Zeigen Sie mit Network Watcher eine grafische Darstellung der Netzwerkressourcen in einem VNET an. Weitere Informationen zum Anzeigen der Netzwerktopologie finden Sie im Artikel mit der [Übersicht über die Topologie](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="tools"></a>Bereitstellungs- und Konfigurationstools

Sie können Azure-Netzwerkressourcen mit einem der folgenden Tools bereitstellen und konfigurieren:

- **Azure-Portal:** Hierbei handelt es sich um eine grafische Benutzeroberfläche, die in einem Browser ausgeführt wird. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
- **Azure PowerShell:** Befehlszeilentools zum Verwalten von Azure über Windows-Computer. Weitere Informationen zu Azure PowerShell finden Sie im Artikel mit der [Übersicht über Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fnetworking%2ftoc.json).
- **Azure-Befehlszeilenschnittstelle (CLI)** : Befehlszeilentools zum Verwalten von Azure über Linux-, macOS- oder Windows-Computer. Weitere Informationen zur Azure CLI finden Sie im Artikel mit der [Übersicht über die Azure CLI](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json).
- **Azure Resource Manager-Vorlagen**: Hierbei handelt es sich um Dateien (im JSON-Format), mit der Infrastruktur und Konfiguration der Azure-Lösung definiert werden. Mit einer Vorlage können Sie die Lösung während ihres Lebenszyklus wiederholt bereitstellen und sicher sein, dass Ihre Ressourcen einheitlich bereitgestellt werden. Weitere Informationen zum Erstellen von Vorlagen finden Sie im Artikel [Bewährte Methoden für das Erstellen von Vorlagen](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json). Vorlagen können über das Azure-Portal, die CLI oder PowerShell bereitgestellt werden. Um sofort mit Vorlagen loszulegen, stellen Sie eine der zahlreichen vorkonfigurierten Vorlagen in der Bibliothek mit [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=network) bereit. 

## <a name="pricing"></a>Preise

Einige Azure-Netzwerkdienste sind gebührenpflichtig, andere hingegen kostenlos. Weitere Informationen finden Sie auf den Seiten mit den Preisen von [Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network), [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) und [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie Ihr erstes VNet, und verbinden Sie einige VMs damit, indem Sie die Schritte des Artikels [Erstellen Ihres ersten virtuellen Netzwerks](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) ausführen.
- Verbinden Sie Ihren Computer über eine Point-to-Site-Verbindung mit einem VNET, indem Sie die Schritte im Artikel [Konfigurieren einer Point-to-Site-Verbindung](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) ausführen.
- Führen Sie einen Lastenausgleich des Internetdatenverkehrs zu öffentlichen Servern durch, indem Sie die Schritte im Artikel [Erstellen eines Load Balancers mit Internetzugriff](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) ausführen.
