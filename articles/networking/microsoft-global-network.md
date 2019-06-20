---
title: Globales Microsoft-Netzwerk – Azure
description: Hier wird beschrieben, wie Microsoft sein schnelles und zuverlässiges globales Netzwerk aufbaut
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: networking
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2019
ms.author: ypitsch,kumud
ms.openlocfilehash: 99650577b17874e61d1cede979d7ef2f4bfd491b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245483"
---
# <a name="microsoft-global-network"></a>Globales Microsoft-Netzwerk

Microsoft besitzt und betreibt eines der größten Backbonenetzwerke der Welt. Diese globale und hochentwickelte Architektur, die sich über mehr als 160.000 Kilometer erstreckt, verbindet unsere Rechenzentren und Kunden. 
 
Täglich stellen Kunden rund um die Welt Verbindungen mit Microsoft Azure, Bing, Dynamics 365, Office 365, XBox und vielen anderen Diensten her und übermitteln Billionen von Anforderungen. Unabhängig vom Typ erwarten die Kunden sofortige Zuverlässigkeit und Reaktion von unseren Diensten. 
 
Das [globale Microsoft-Netzwerk](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) ist ein zentraler Bestandteil für die Bereitstellung einer großartigen Cloudumgebung. Durch die Verbindung unserer Microsoft-[Rechenzentren](https://azure.microsoft.com/global-infrastructure/) in 54 Azure-Regionen und durch das große Netz von Edgeknoten, die strategisch auf der ganzen Welt positioniert sind, bietet unser globales Netzwerk sowohl die Verfügbarkeit, Kapazität als auch die Flexibilität, um sämtliche Anforderungen zu erfüllen.

![Globales Microsoft-Netzwerk](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Holen Sie sich das Premium-Cloudnetzwerk
 
Die Entscheidung für die [bestmögliche Umgebung](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) ist mit der Microsoft-Cloud einfach. Ab dem Moment, in dem der Datenverkehr der Kunden über unsere strategisch platzierten Edgeknoten in unserem globalen Netzwerk eintrifft, werden Ihre Daten mit nahezu Lichtgeschwindigkeit über optimierte Routen weitergeleitet. Dadurch wird eine optimale Latenz für bestmögliche Leistungen sichergestellt. Diese Edgeknoten sind über Tausende von Verbindungen an mehr als 145 Standorten mit mehr als 3500 individuellen Internetpartnern (Peers) verbunden und bilden die Grundlage unserer Verbindungsstrategie. 
 
Ganz gleich, ob Sie eine Verbindung von London nach Tokio oder von Washington DC nach Los Angeles herstellen, die Netzwerkleistung wird durch Faktoren wie Latenz, Jitter, Paketverlust und Durchsatz bestimmt und beeinflusst.  Bei Microsoft verwenden wir direkte Verbindungen anstelle von Transitverbindungen. Dies sorgt für einen symmetrischen Antwortdatenverkehr sowie für möglichst kurze und einfache Hops, Peering-Partner und Wege. Dieser Premium-Ansatz, der oft als [Cold-Potato-Routing](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) bezeichnet wird, stellt sicher, dass der Netzwerkdatenverkehr der Kunden so lange wie möglich im Microsoft-Netzwerk verbleibt, bevor er übergeben wird.  
 
Bezieht sich das auf den gesamten Datenverkehr bei der Verwendung von Microsoft-Diensten? Ja, jeder Datenverkehr zwischen Rechenzentren, innerhalb von Microsoft Azure oder zwischen Microsoft-Diensten wie Virtual Machines, Office 365, XBox, SQL DBs, Storage und zwischen virtuellen Netzwerken wird innerhalb unseres globalen Netzwerks und niemals über das öffentliche Internet geleitet, um die optimale Leistung und Integrität sicherzustellen.  
 
Gewaltige Investitionen in Glasfaserkapazitäten und die Vielfalt hinsichtlich unter-, überirdischer und unterseeischer Wege sind entscheidend für uns, um einen konsistenten und hohen Servicelevel aufrechtzuerhalten und gleichzeitig das extreme Wachstum unserer Cloud- und Onlinedienste zu fördern. Die neuesten Erweiterungen unseres globalen Netzwerks sind unser [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea)-Überseekabel, das branchenweit erste unter dem Meeresboden gelegene Open Line System (OLS) zwischen Bilbao, Spanien und Virginia Beach, Virginia, USA sowie das [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) zwischen New York, USA und Dublin, Irland und [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) zwischen Tokio, Japan und Portland, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>Unser Netzwerk ist Ihr Netzwerk

Wir haben zwei Jahrzehnte an Erfahrungen und gewaltige Investitionen in das Netzwerk gesteckt, um jederzeit eine optimale Leistung sicherzustellen. Unternehmen können unsere Netzwerkressourcen voll ausschöpfen und darüber hinaus fortschrittliche Überlagerungsarchitekturen erstellen. 
 
Microsoft Azure bietet das umfassendste Portfolio an Diensten und Funktionen, die es Kunden ermöglichen, Netzwerkanforderungen überall schnell und einfach zu erstellen, zu erweitern und zu erfüllen. Unsere Produktfamilie von Konnektivitätsdiensten umfasst das Peering virtueller Netzwerke zwischen Regionen, Hybrid- und In-Cloud-Architekturen (Point-to-Site und Site-to-Site) sowie globale IP-Übertragungsszenarien.  Für Unternehmen, die ihr eigenes Rechenzentrum oder Netzwerk mit Azure verbinden möchten, oder für Kunden mit hohen Anforderungen an die Datenerfassung oder Datenübertragung, bieten [ExpressRoute](../expressroute/expressroute-introduction.md) und [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) an Peeringstandorten auf der ganzen Welt Optionen mit einer Bandbreite von bis zu 100 GBit/s direkt in das globale Netzwerk von Microsoft.  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) dient zur Ergänzung der WAN-Implementierung des Dienstanbieters und vernetzt Ihre lokalen Standorte auf der ganzen Welt. Wenn Sie ein globales Unternehmen betreiben, können Sie ExpressRoute Global Reach in Verbindung mit Ihren bevorzugten und lokalen Dienstanbietern verwenden, um sämtliche globalen Standorte über das globale Netzwerk von Microsoft zu verbinden. Die Erweiterung Ihres neuen Netzwerks in die Cloud (WAN), um eine große Anzahl von Niederlassungen zu umfassen, kann durch Azure Virtual WAN erfolgen, das Ihre Zweigstellen mit SDWAN- & VPN-Geräten (d. h. Customer Premises Equipment (CPE)) auf benutzerfreundliche Weise und mit automatisierter Konnektivität und Konfigurationsverwaltung nahtlos mit dem globalen Microsoft-Netzwerk verbinden kann. 
 
[Globales VNET-Peering](../virtual-network/virtual-network-peering-overview.md) ermöglicht Kunden das nahtlose, regionsübergreifende Verbinden von zwei oder mehr virtuellen Azure-Netzwerken. Nach dem Peering werden die virtuellen Netzwerke als einzelnes Element angezeigt. Der Datenverkehr zwischen virtuellen Computern in den virtuellen Netzwerken, die mittels Peering miteinander verbunden sind, wird ähnlich wie der Datenverkehr zwischen virtuellen Computern im gleichen Netzwerk nur über private IP-Adressen über die Microsoft-Backbone-Infrastruktur geleitet. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Softwaredefinierte Innovationen für eine optimale Verwaltung

Mit dem Betrieb einer der führenden Clouds der Welt hat Microsoft tiefe Einblicke und Erfahrungen hinsichtlich der [Erstellung und Verwaltung](https://myignite.techcommunity.microsoft.com/sessions/66668) einer hochleistungsfähigen globalen Infrastruktur gewonnen.  
 
Wir halten uns an bewährte Verfahrensprinzipien: 
 
- Einsatz der besten Switchinghardware zwischen den verschiedenen Ebenen des Netzwerks.  
- Bereitstellen neuer Features ohne Auswirkung auf die Endbenutzer.  
- Schnellstmögliches sicheres und zuverlässiges, die ganze Flotte übergreifendes Einführen von Updates. In Stunden statt Wochen.  
- Einsatz umfassender Telemetriedaten auf Cloudebene und vollständig automatisierte Fehlerbehebung.  
- Verwenden einer einheitlichen und softwaredefinierten Netzwerktechnologie zur Steuerung aller Hardwareelemente im Netzwerk.  Vermeiden von Duplizierung und Reduzieren von Fehlern. 
 
Diese Prinzipien gelten auf jeder Ebene des Netzwerks: von Host-Netzwerkschnittstelle, Switchingplattform, Netzwerkfunktionen im Rechenzentrum wie Load Balancer bis hin zum WAN mit unserer Datenverkehr-Engineering-Plattform und unseren optischen Netzwerken.  
 
Das exponentielle Wachstum von Azure und seinem Netzwerk hat einen Punkt erreicht, an dem wir schließlich erkannt haben, dass die menschliche Intuition bei der Verwaltung des globalen Netzwerkbetriebs an ihre Grenzen stößt. Um dem Bedarf der Validierung lang-, mittel- und kurzfristiger Änderungen im Netzwerk gerecht zu werden, haben wir eine Plattform zum synthetischen Spiegeln und Emulieren des Produktionsnetzwerks entwickelt. Die Möglichkeit zur Erstellung gespiegelter Umgebungen und zur Ausführung von Millionen von Simulationen gestattet es uns, Änderungen an Soft- und Hardware sowie deren Auswirkungen zu testen, bevor sie auf unsere Produktionsplattform und in unser Netzwerk übertragen werden. 

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zu den Netzwerkdiensten in Azure](https://azure.microsoft.com/product-categories/networking/)
