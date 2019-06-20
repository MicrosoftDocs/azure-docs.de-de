---
title: Netzwerkaspekte – Azure Dedicated HSM | Microsoft-Dokumentation
description: Übersicht über Netzwerkaspekte für Azure Dedicated HSM-Bereitstellungen
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: 042ecabe38969a6a26c27622b8c3d25193b3e7c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118033"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure-Dienst für dedizierte HSMs – Netzwerke

Der Azure-Dienst für dedizierte HSMs erfordert eine äußerst sichere Netzwerkumgebung. Dies gilt für den Weg von der Azure-Cloud zurück in die IT-Umgebung des Kunden (lokal) mithilfe von verteilten Anwendungen ebenso wie für Szenarien mit Hochverfügbarkeit. Azure-Netzwerke ermöglichen dies, und vier verschiedene Bereiche müssen berücksichtigt werden.

- Erstellen von HSM-Geräten in Ihrem virtuellen Netzwerk (VNET) in Azure
- Herstellen einer Verbindung zwischen lokalen und cloudbasierten Ressourcen für die Konfiguration und Verwaltung von HSM-Geräten
- Erstellen und Verbinden virtueller Netzwerke, um Anwendungsressourcen und HSM-Geräte miteinander zu verbinden
- Regionen übergreifendes Verbinden virtueller Netzwerke zur Kommunikation sowie zum Aktivieren von Hochverfügbarkeitsszenarien

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuelles Netzwerk für Ihre dedizierten HSMs

Dedizierte HSMs sind in einem virtuellen Netzwerk integriert und im eigenen privaten Netzwerk des Kunden in Azure platziert. Dies ermöglicht den Zugriff auf die Geräte von virtuellen Computern oder Computeressourcen im virtuellen Netzwerk aus.  
Weitere Informationen zum Integrieren von Azure-Diensten in das virtuelle Netzwerk und die Funktionen, die es bietet, finden Sie in der Dokumentation [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).

### <a name="virtual-networks"></a>Virtuelle Netzwerke

Vor der Bereitstellung eines dedizierten HSM-Geräts müssen Kunden zunächst ein virtuelles Netzwerk in Azure erstellen oder eines verwenden, das bereits im Abonnement des Kunden vorhanden ist. Das virtuelle Netzwerk definiert den Sicherheitsbereich für das dedizierte HSM-Gerät. Weitere Informationen zum Erstellen virtueller Netzwerke finden Sie in der [Dokumentation zu virtuellen Netzwerken](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Subnetze

Subnetze unterteilen das virtuelle Netzwerk in getrennte Adressräume, die von den Azure-Ressourcen, die Sie darin platzieren, verwendet werden können. Dedizierte HSMs werden in einem Subnetz des virtuellen Netzwerks bereitgestellt. Jedes dedizierte HSM-Gerät, das im Subnetz des Kunden bereitgestellt wird, erhält eine private IP-Adresse aus diesem Subnetz. Das Subnetz, in dem das HSM-Gerät bereitgestellt wird, muss explizit an den Dienst delegiert werden: Microsoft.HardwareSecurityModules/dedicatedHSMs. Dadurch werden bestimmte Berechtigungen für den HSM-Dienst für die Bereitstellung im Subnetz gewährt. Die Delegierung an dedizierte HSMs erzwingt bestimmte Richtlinieneinschränkungen im Subnetz. Netzwerksicherheitsgruppen (NSGs) und benutzerdefinierte Routen (User-Defined Routes, UDRs) werden in delegierten Subnetzen derzeit nicht unterstützt. Daher kann ein Subnetz, sobald es an dedizierte HSMs delegiert wird, nur zum Bereitstellen von HSM-Ressourcen verwendet werden. Bei der Bereitstellung anderer Kundenressourcen im Subnetz tritt ein Fehler auf.


### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

Eine Voraussetzung der aktuellen Architektur ist die Konfiguration eines ExpressRoute-Gateways im Kundensubnetz, wo ein HSM-Gerät platziert werden muss, um die Integration des HSM-Geräts in Azure zu ermöglichen. Dieses ExpressRoute-Gateway kann nicht für die Verbindung zwischen lokalen Standorten und Kunden-HSM-Geräten in Azure genutzt werden.

## <a name="connecting-your-on-premises-it-to-azure"></a>Herstellen der Verbindung Ihrer lokalen IT mit Azure

Wenn Sie cloudbasierte Ressourcen erstellen, ist eine private Verbindung zurück zu lokalen IT-Ressourcen eine typische Anforderung. Im Fall von Dedicated HSM geht es dabei hauptsächlich um die HSM-Clientsoftware zur Konfiguration der HSM-Geräte sowie um Aktivitäten wie z. B. Sicherungen und das Entnehmen von Protokollen aus HSMs für die Analyse. Eine wichtige Entscheidung ist hier die Art der Verbindung, da Optionen zur Verfügung stehen.  Die flexibelste Option ist Site-to-Site-VPN, da wahrscheinlich mehrere lokale Ressourcen die sichere Kommunikation mit Ressourcen (einschließlich HSMs) in der Azure-Cloud benötigen. Dies erfordert, dass eine Kundenorganisation über ein VPN-Gerät verfügt, um die Verbindung zu erleichtern. Eine Point-to-Site-VPN-Verbindung kann verwendet werden, wenn es nur einen einzigen lokalen Endpunkt wie z.B. eine einzige Arbeitsstation für die Verwaltung gibt.
Weitere Informationen zu Konnektivitätsoptionen finden Sie unter [Planungsoptionen für VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Zu diesem Zeitpunkt ist ExpressRoute keine Option für die Verbindung mit lokalen Ressourcen. Es ist auch zu beachten, dass das ExpressRoute-Gateway bei Verwendung wie oben beschrieben nicht für Verbindungen mit einer lokalen Infrastruktur geeignet ist.

### <a name="point-to-site-vpn"></a>Punkt-zu-Standort-VPN

Ein Point-to-Site-VPN ist die einfachste Form einer sicheren Verbindung mit einem einzelnen lokalen Endpunkt. Dies ist möglicherweise relevant, wenn Sie beabsichtigen, nur eine einzige Arbeitsstation für die Verwaltung dedizierter HSMs auf Azure-Basis einzusetzen.

### <a name="site-to-site-vpn"></a>Site-to-Site-VPN-Verbindung

Ein virtuelles Site-to-Site-VPN ermöglicht die sichere Kommunikation zwischen dedizierten HSMs auf Azure-Basis und Ihrer lokalen IT. Ein Grund dafür ist eine Sicherungsfunktion für die lokalen HSMs und die Notwendigkeit einer Verbindung zwischen den beiden für die Ausführung der Sicherung.

## <a name="connecting-virtual-networks"></a>Herstellen einer Verbindung zwischen virtuellen Netzwerken

Eine typische Bereitstellungsarchitektur für das dedizierte HSM beginnt mit einem einzelnen virtuellen Netzwerk und einem entsprechenden Subnetz, wo die HSM-Geräte erstellt und bereitgestellt werden. In der gleichen Region könnten sich zusätzliche virtuelle Netzwerke und Subnetze für Anwendungskomponenten befinden, die das dedizierte HSM nutzen. Um die Kommunikation zwischen diesen Netzwerken zu ermöglichen, verwenden wir das Peering in virtuellen Netzwerken.

### <a name="virtual-network-peering"></a>Peering in virtuellen Netzwerken

Wenn mehrere virtuelle Netzwerke innerhalb einer Region gegenseitig auf die Ressourcen der anderen zugreifen müssen, kann Peering in virtuellen Netzwerken zum Erstellen sicherer Kommunikationskanäle zwischen ihnen verwendet werden.  Das Peering virtueller Netzwerke ermöglicht nicht nur sichere Kommunikation, sondern stellt auch Verbindungen mit geringer Latenz und hoher Bandbreite zwischen den Ressourcen in Azure sicher.

![Netzwerkpeering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Herstellen Azure-Regionen übergreifender Verbindungen

Die HSM-Geräte haben die Möglichkeit, über Softwarebibliotheken Datenverkehr an ein alternatives HSM umzuleiten. Die Umleitung des Datenverkehrs empfiehlt sich, wenn Geräte ausfallen oder der Zugriff auf ein Gerät ausfällt. Ausfallszenarien auf regionaler Ebene können verringert werden, indem HSMs in anderen Regionen bereitgestellt und die Regionen übergreifende Kommunikation zwischen virtuellen Netzwerken aktiviert wird.

### <a name="cross-region-ha-using-vpn-gateway"></a>Regionen übergreifende HOCHVERFÜGBARKEIT mit VPN Gateway

Für global verteilte Anwendungen oder regionale Failoverszenarien mit hoher Verfügbarkeit müssen virtuelle Netzwerke Regionen übergreifend verbunden werden. Mit dem Azure-Dienst für dedizierte HSMs kann Hochverfügbarkeit mit VPN Gateway erreicht werden, das einen sicheren Tunnel zwischen den beiden virtuellen Netzwerken bereitstellt. Weitere Informationen zu VNET-to-VNET-Verbindungen mit VPN Gateway finden Sie im Artikel [Was ist VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Globales VNET-Peering ist in regionsübergreifenden Konnektivitätsszenarien mit dedizierten HSMs zu diesem Zeitpunkt nicht verfügbar, und VPN Gateway sollte stattdessen verwendet werden. 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen](faq.md)
- [Unterstützungsmöglichkeiten](supportability.md)
- [Hochverfügbarkeit](high-availability.md)
- [Physische Sicherheit](physical-security.md)
- [Überwachung](monitoring.md)
- [Bereitstellungsarchitektur](deployment-architecture.md)