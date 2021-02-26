---
title: Übersicht über IPv6 für Azure Virtual Network
titlesuffix: Azure Virtual Network
description: IPv6-Beschreibung der IPv6-Endpunkte und -Datenpfade in einem virtuellen Azure-Netzwerk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 86e4bb52f2a97bf36dc4be86289bd513a9f05091
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538254"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Was ist IPv6 für Azure Virtual Network?

IPv6 für Azure Virtual Network (VNET) ermöglicht es Ihnen, Anwendungen in Azure mit IPv6- und IPv4-Konnektivität sowohl innerhalb eines virtuellen Netzwerks als auch für das Internet (in ein- und ausgehender Richtung) bereitzustellen. Aufgrund der Erschöpfung öffentlicher IPv4-Adressen werden neue Netzwerke für Mobilität und Internet der Dinge (IoT) häufig auf IPv6 aufgebaut. Selbst langjährig etablierte ISP- und Mobilnetzwerke werden auf IPv6 umgestellt. Reine IPv4-Dienste können sowohl in vorhandenen als auch in entstehenden Märkten einen echten Nachteil darstellen. Die Dual Stack-IPv4/IPv6-Konnektivität ermöglicht es in Azure gehosteten Diensten, diese Technologielücke mit global verfügbaren Dual Stack-Diensten zu schließen, die problemlos eine Verbindung sowohl mit den vorhandenen IPv4- als auch mit diesen neuen IPv6-Geräten und -Netzwerken herstellen können.

Die ursprüngliche IPv6-Konnektivität von Azure macht es einfach, eine Dual Stack-Internetverbindung (IPv4/IPv6) für Anwendungen bereitzustellen, die in Azure gehostet werden. Sie ermöglicht eine einfache Bereitstellung von VMs mit IPv6-Konnektivität mit Lastenausgleich für eingehende und ausgehende initiierte Verbindungen. Dieses Feature ist noch verfügbar. Weitere Informationen finden Sie [hier](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 für das virtuelle Azure-Netzwerk bietet viel umfangreichere Funktionen, sodass vollständige IPv6-Lösungsarchitekturen in Azure bereitgestellt werden können.


Die folgende Abbildung zeigt eine einfache Dual-Stack-Bereitstellung (IPv4/IPv6) in Azure:

![Abbildung: IPv6-Netzwerkbereitstellung](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vorteile

Vorteile von IPv6 für Azure VNET:

- Hilft dabei, die Reichweite Ihrer in Azure gehosteten Anwendungen auf die wachsenden Märkte für Mobilgeräte und das Internet der Dinge auszuweiten.
- Dual Stack-IPv4/IPv6-VMs stellen maximale Flexibilität bei der Dienstbereitstellung zur Verfügung. Eine einzige Dienstinstanz kann sowohl mit IPv4- als auch IPv6-fähigen Internetclients eine Verbindung herstellen.
- Baut auf der bereits lange etablierten IPv6-Konnektivität zwischen Azure-VMs und dem Internet auf.
- Standardmäßig sicher, da die IPv6-Verbindung mit dem Internet nur dann hergestellt wird, wenn Sie sie in Ihrer Bereitstellung explizit anfordern.

## <a name="capabilities"></a>Funktionen

IPv6 für Azure VNET beinhaltet die folgenden Funktionen:

- Azure-Kunden können ihren eigenen virtuellen IPv6-Netzwerkadressraum definieren, um den Anforderungen ihrer Anwendungen und Kunden gerecht zu werden, oder eine nahtlose Integration in ihren lokalen IP-Bereich vornehmen.
- Virtuelle Dual Stack-Netzwerke (IPv4 und IPv6) mit Dual Stack-Subnetzen ermöglichen es Anwendungen, eine Verbindung mit IPv4- und IPv6-Ressourcen in ihrem virtuellen Netzwerk oder im Internet herzustellen.
    > [!IMPORTANT]
    > Die Subnetze für IPv6 müssen exakt /64 groß sein.  Dadurch ist zukünftige Kompatibilität sichergestellt, wenn Sie Routing des Subnetzes an ein lokales Netzwerk aktivieren möchten, denn einige Router können nur /64-IPv6-Routen akzeptieren.  
- Schützen Sie Ihre Ressourcen mit IPv6-Regeln für Netzwerksicherheitsgruppen.
    - Der DDoS-Schutz (Distributed Denial of Service) der Azure-Plattform wird auf öffentliche IP-Adressen mit Internetzugriff erweitert.
- Passen Sie das Routing von IPv6-Datenverkehr in Ihrem virtuellen Netzwerk mit benutzerdefinierten Routen an, insbesondere wenn Sie Network Virtual Appliances zur Erweiterung Ihrer Anwendung nutzen.
- Auf allen virtuellen Linux- und Windows-Computern kann IPv6 für Azure VNET verwendet werden.
- Unterstützung einer [öffentlichen IPv6-Load Balancer Standard-Instanz](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) für die Erstellung resilienter, skalierbarer Anwendungen, einschließlich:
    - Optionale IPv6-Integritätstests, mit denen bestimmt werden kann, ob Instanzen von Back-End-Pools fehlerfrei sind und somit neue Flows empfangen können.
    - Optionale Ausgangsregeln für uneingeschränkte deklarative Kontrolle über ausgehende Verbindungen, um diese Funktion nach Belieben skalieren und optimieren zu können.
    - Mehrere optionale Front-End-Konfigurationen, sodass ein einzelner Lastenausgleich mehrere öffentliche IPv6-Adressen nutzen kann. (Für verschiedene Front-End-Adressen können das gleiche Front-End-Protokoll und der gleiche Port verwendet werden.)
    - Optionale IPv6-Ports können mithilfe der Funktion *Floating IP* der Lastenausgleichsregeln in Back-End-Instanzen wiederverwendet werden. 
    - Hinweis: Der Lastenausgleich führt keine Protokollübersetzung durch (kein NAT64). 
    - Hinweis: Für IPv6 kann nur ein Lastenausgleich auf die primäre Netzwerkschnittstelle (NIC) auf Azure-VMs ausgeführt werden. 
- Unterstützung einer [internen IPv6-Load Balancer Standard-Instanz](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) zur Erstellung resilienter Anwendungen mit mehreren Ebenen innerhalb von Azure VNETs.   
- Unterstützung einer öffentlichen IPv6-Load Balancer Basic-Instanz für die Kompatibilität mit Legacy-Bereitstellungen
- [Reservierte öffentliche IPv6-IP-Adressen und IPv6-Adressbereiche](ipv6-public-ip-address-prefix.md) bieten stabile vorhersagbare IPv6-Adressen, die die Filterung der in Azure gehosteten Anwendungen für Ihr Unternehmen und Ihre Kunden vereinfachen.
- Eine öffentliche IP-Adresse auf Instanzebene ermöglicht direkte IPv6-Internetkonnektivität für einzelne virtuelle Computer.
- [Hinzufügen von IPv6 zu vorhandenen reinen IPv4-Bereitstellungen:](ipv6-add-to-existing-vnet-powershell.md) Über diese Funktion können Sie IPv6-Konnektivität einfach vorhandenen reinen IPv4-Bereitstellungen hinzufügen, ohne die Bereitstellungen neu erstellen zu müssen.  Der IPv4-Netzwerkdatenverkehr wird während dieses Vorgangs nicht beeinträchtigt. Abhängig von der Anwendung und dem Betriebssystem können Sie IPv6 möglicherweise sogar Livediensten hinzufügen.    
- Internetclients können dank der Azure DNS-Unterstützung von IPv6-Einträgen (AAAA) nahtlos unter Verwendung ihres bevorzugten Protokolls auf Ihre Anwendung mit dualem Stapel zugreifen. 
- Erstellen Sie Dual Stack-Anwendungen, die automatisch mit VM-Skalierungsgruppen für die Last mit IPv6 skaliert werden.
- [Peering virtueller Netzwerke (VNETs)](virtual-network-peering-overview.md) – sowohl mit regionalem als auch mit globalem Peering – ermöglicht die nahtlose Verbindung von Dual Stack-VNETs: Die IPv4- und IPv6-Endpunkte auf virtuellen Computern in den Peernetzwerken können miteinander kommunizieren. Sogar das Peering von Dual Stack-VNETs mit reinen IPv4-VNETs ist möglich, da die Bereitstellungen zu Dual Stack übertragen werden. 
- IPv6-Problembehandlung und -Diagnose sind mit Lastenausgleichsmetriken und -Warnungen sowie mit Network Watcher-Funktionen verfügbar, z. B. Paketerfassung, NSG-Datenflussprotokolle, Problembehandlung von Verbindungsproblemen und Verbindungsüberwachung.   

## <a name="scope"></a>`Scope`
IPv6 für Azure VNET ist eine grundlegende Gruppe von Funktionen, über die Kunden Dual Stack-Anwendungen (IPv4 + IPv6) in Azure hosten können.  Wir beabsichtigen, die Unterstützung für IPv6 im Lauf der Zeit zu weiteren Azure-Netzwerkfunktionen hinzuzufügen und schließlich Dual Stack-Versionen von Azure-PaaS-Diensten anzubieten. In der Zwischenzeit kann über die IPv4-Endpunkte auf virtuellen Dual Stack-Computern auf alle Azure-PaaS-Dienste zugegriffen werden.   

## <a name="limitations"></a>Einschränkungen
Das aktuelle Release von IPv6 für Azure Virtual Network weist die folgenden Einschränkungen auf:
- IPv6 für Azure Virtual Network ist in allen globalen Azure Commercial- und US Government-Regionen unter Verwendung aller Bereitstellungsmethoden verfügbar.  
- ExpressRoute-Gateways können für reinen IPv4-Datenverkehr in einem VNET mit aktiviertem IPv6 verwendet werden.  Die Unterstützung des IPv6-Datenverkehrs befindet sich in Planung.   
- VPN-Gateways können in einem VNET mit aktiviertem IPv6 WEDER direkt noch durch Peering mit „UseRemoteGateway“ verwendet werden.
- Die Azure-Plattform (AKS usw.) unterstützt die IPv6-Kommunikation für Container nicht.  
- Für IPv6 kann nur ein Lastenausgleich auf die primäre Netzwerkschnittstelle (NIC) auf Azure-VMs ausgeführt werden. Der Lastenausgleich für IPv6-Datenverkehr auf sekundäre NICs wird nicht unterstützt.    
- Reine virtuelle IPv6-Computer oder VM-Skalierungsgruppen werden nicht unterstützt. Jede NIC muss mindestens eine IPv4-IP-Konfiguration enthalten. 
- Beim Hinzufügen von IPv6 zu vorhandenen IPv4-Bereitstellungen können IPv6-Bereiche nicht zu einem VNET hinzugefügt werden, das über vorhandene Ressourcennavigationslinks verfügt.  
- Das Weiterleiten von DNS für IPv6 wird derzeit für öffentliches Azure-DNS unterstützt, aber Reverse-DNS wird noch nicht unterstützt.   

## <a name="pricing"></a>Preise

Azure-IPv6-Ressourcen und -Bandbreite werden zu den gleichen Tarifen wie IPv4 berechnet. Es gibt keine zusätzlichen oder abweichenden Gebühren für IPv6. Sie können Details zu den Preisen für [öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/), [Netzwerkbandbreite](https://azure.microsoft.com/pricing/details/bandwidth/) oder [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) anzeigen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [eine IPv6-Dual Stack-Anwendung mithilfe von Azure PowerShell bereitstellen](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Erfahren Sie, wie Sie [eine IPv6-Dual Stack-Anwendung mithilfe der Azure CLI bereitstellen](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Erfahren Sie, wie Sie [eine IPv6-Dual Stack-Anwendung mithilfe von Resource Manager-Vorlagen (JSON) bereitstellen](ipv6-configure-standard-load-balancer-template-json.md).
