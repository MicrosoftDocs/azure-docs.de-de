---
title: Azure-Netzwerkarchitektur
description: Dieser Artikel enthält eine allgemeine Beschreibung des Microsoft Azure-Infrastrukturnetzwerks.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: 48a7e52d4284e5c2db1d77d24d91fd4701aad8d7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587144"
---
# <a name="azure-network-architecture"></a>Azure-Netzwerkarchitektur
Die Netzwerkarchitektur von Azure beruht auf einer geänderten Version des als Branchenstandard geltenden Kern-, Verteilungs- und Zugriffsmodells und verfügt über unterschiedliche Hardwareschichten (Layer). Zu diesen Layern zählen:

- Kern (Rechenzentrumrouter)
- Verteilung (Accessrouter und L2-Aggregation). Die Verteilungsschicht trennt das L3-Routing vom L2-Switching.
- Zugriff (L2-Hostswitches)

Die Netzwerkarchitektur besteht aus zwei Schichten von Layer 2-Switches. Eine Schicht aggregiert den Datenverkehr von der anderen Schicht. Die zweite Schicht führt Schleifen aus, um Redundanz zu integrieren. Die Architektur ermöglicht einen flexibleren VLAN-Speicherbedarf und verbessert die Portskalierung. Die Architektur sorgt für eine klare Trennung zwischen L2 und L3 und ermöglicht dadurch die Verwendung von Hardware in jedem einzelnen unterschiedlichen Layer im Netzwerk. Fehler auf einem Layer, die sich auf die anderen Layer auswirken, können so minimiert werden. Mithilfe von Trunks lassen sich Ressourcen freigeben, wie z.B. die Konnektivität zur L3-Infrastruktur.

## <a name="network-configuration"></a>Netzwerkkonfiguration
Die Netzwerkarchitektur eines Azure-Clusters in einem Rechenzentrum besteht aus den folgenden Geräten:

- Router (Rechenzentrum-, Access- und Blattrouter am Netzwerkrand)
- Switches (Aggregation- und Top-of-Rack-Switches)
- Digi-CMs
- Stromverteilereinheiten

Azure verfügt über zwei getrennte Architekturen. Einige Azure-Bestandskunden und gemeinsame Dienste befinden sich in der Standard-LAN-Architektur (Default LAN Architecture, DLA), während sich neue Regionen und virtuelle Kunden in der Quantum 10-Architektur (Q10) befinden. Die DLA ist ein konventioneller Strukturentwurf mit Aktiv/Passiv-Accessroutern und Sicherheits-Zugriffssteuerungslisten (ACLs), die auf die Accessrouter angewendet werden. Bei der Quantum 10-Architektur handelt es sich um einen Routernetzentwurf mit geschlossener bzw. vermaschter Topologie, bei dem ACLs nicht auf die Router angewendet werden. Stattdessen werden ACLs unter dem Routing über Lastenausgleichsfunktionen für Software (Software Load Balancing, SLB) oder softwaredefinierte VLANs angewendet.

Die folgende Abbildung bietet einen allgemeinen Überblick über die Netzwerkarchitektur in einem Azure-Cluster:

![Abbildung zum Azure-Netzwerk][1]

### <a name="quantum-10-devices"></a>Quantum 10-Geräte
Beim Quantum 10-Entwurf wird Layer 3-Switching über mehrere Geräte verteilt in einem Entwurf nach Clos bzw. mit vermaschter Topologie durchgeführt. Zu den Vorteilen des Q10-Designs gehören mehr Funktionen und mehr Möglichkeiten zum Skalieren der vorhandenen Netzwerkinfrastruktur. Der Entwurf verwendet Blattrouter am Netzwerkrand, Spineswitches und Top-of-Rack-Router, um Datenverkehr über mehrere Routen an den Cluster zu übergeben und Fehlertoleranz zu ermöglichen. Sicherheitsdienste wie die Netzwerkadressübersetzung werden über den Softwarelastenausgleich anstatt über Hardwaregeräte ausgeführt.

### <a name="access-routers"></a>Accessrouter
Die Distribution-/Access-L3-Router (ARs) übernehmen das Hauptrouting für die Distribution- und Accesslayer. Diese Geräte werden zusammen bereitgestellt und sind das Standardgateway für Subnetze. Jedes AR-Paar kann je nach Kapazität mehrere L2-Aggregation-Switchpaare unterstützen. Die maximale Anzahl hängt sowohl von der Kapazität des Geräts als auch den Fehlerdomänen ab. Eine typische Anzahl besteht aus drei L2-Aggregation-Switchpaaren pro AR-Paar.

### <a name="l2-aggregation-switches"></a>L2-Aggregation-Switches  
Diese Geräte dienen als Sammelpunkt für L2-Datenverkehr. Sie stellen die Verteilungsschicht für das L2-Fabric dar und können große Mengen an Datenverkehr verarbeiten. Da diese Geräte Datenverkehr aggregieren, werden 802.1Q-Funktionalität und Technologien mit hoher Bandbreite vorausgesetzt, etwa Portaggregation und 10 GbE.

### <a name="l2-host-switches"></a>L2-Hostswitches
Hosts erstellen eine direkte Verbindung zu diesen Switches. Dabei kann es sich um in ein Rack eingebaute Switches oder Bereitstellungen für Gehäuse handeln. Der 802.1Q-Standard ermöglicht das Definieren eines VLANs als natives VLAN, das als normales (nicht gekennzeichnetes) Ethernet-Frame behandelt wird. Unter normalen Umständen werden Frames auf dem nativen VLAN an einen Trunkport gemäß 802. 1Q-Standard ohne Tags übertragen und empfangen. Dieses Feature wurde für die Migration zu 802. 1Q entwickelt und ist auch mit Geräten kompatibel, die nicht den 802.1Q-Standard erfüllen. In dieser Architektur verwendet nur die Netzwerkinfrastruktur das native VLAN.

Diese Architektur gibt einen Standard für eine native VLAN-Auswahl an. Durch den Standard wird sichergestellt, dass die AR-Geräte möglichst über ein eindeutiges natives VLAN für jeden Trunk und die L2-Aggregation-zu-L2-Aggregation-Trunks verfügen. Die L2-Aggregation-zu-L2-Host-Switch-Trunks verfügen über ein nicht standardmäßiges natives VLAN.

### <a name="link-aggregation-8023ad"></a>Link Aggregation (802.3ad)
Mithilfe von Link Aggregation können mehrere einzelne Verknüpfungen gebündelt und als eine einzige logische Verknüpfung behandelt werden. Um das sofort einsetzbare Debuggen zu vereinfachen, sollte die Anzahl, die zum Festlegen von Portkanalschnittstellen verwendet wird, standardisiert werden. Im restlichen Netzwerk wird die gleiche Anzahl an beiden Enden eines Portkanals verwendet.

Die Zahlen für den L2-Aggregation-zu-L2-Host-Switch sind die Portkanalzahlen, die auf der L2-Aggregation-Seite verwendet werden. Da der Nummernbereich auf der L2-Host-Seite stärker eingeschränkt ist, werden standardmäßig die Zahlen 1 und 2 auf dem L2-Host verwendet. Diese beziehen sich auf den Portkanal und zeigen damit an, dass ein Wechsel zu „a“ bzw. „b“ erfolgt.

### <a name="vlans"></a>VLANs
Die Netzwerkarchitektur verwendet VLANs, um Server zu einer einzigen Broadcastdomäne zu gruppieren. Die VLAN-Nummern entsprechen dem 802.1Q-Standard, der VLANs mit der Nummerierung von 1 bis 4094 unterstützt.

### <a name="customer-vlans"></a>Kunden-VLANs
Sie haben verschiedene Optionen zur VLAN-Implementierung, die Sie über das Azure-Portal bereitstellen können, um die Anforderungen ihrer Lösung in Bezug auf Trennung und Architektur zu erfüllen. Diese Lösungen werden durch virtuelle Computer bereitgestellt. Beispiele für Kundenreferenzarchitektur finden Sie auf der Seite [Azure-Referenzarchitekturen](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Edgearchitektur
Azure-Rechenzentren basieren auf stark redundanten und gut bereitgestellten Netzwerkinfrastrukturen. Microsoft implementiert Netzwerke innerhalb der Azure-Rechenzentren mit n+1-Redundanzarchitekturen, die immer über den Anforderungen liegen. Vollständige Failoverfeatures innerhalb und zwischen Rechenzentren tragen dazu bei, die Netzwerk- und Dienstverfügbarkeit sicherzustellen. Extern werden Rechenzentren von dedizierten Netzwerkverbindungen mit hoher Bandbreite verarbeitet. Diese Verbindungen verbinden redundant Eigenschaften mit mehr als 1.200 Internetdienstanbietern auf der ganzen Welt an mehreren Peeringpunkten. Dies bietet potenzielle Edgekapazität von mehr als 2.000 Gbit/s über das Netzwerk.

Filterungsrouter auf dem Edge- und Accesslayer des Azure-Netzwerks bieten anerkannte Sicherheit auf Paketebene und tragen dazu bei, nicht autorisierte Versuche zum Herstellen einer Verbindung mit Azure zu verhindern. Die Router sorgen dafür, dass die Pakete auch tatsächlich Daten im erwarteten Format enthalten und das Client-/Server-Kommunikationsschema erfüllen. Azure implementiert eine mehrstufige Architektur, die aus den folgenden Netzwerkisolierungs- und Zugriffssteuerungskomponenten besteht:

- **Edgerouter**: Diese isolieren die Anwendungsumgebung vom Internet. Edgerouter bieten Spoofingschutz und beschränken den Zugriff mithilfe von ACLs.
- **Verteilungsrouter (Access)** : Diese lassen nur durch Microsoft genehmigte IP-Adressen zu, bieten Spoofingschutz und anerkannte Verbindungen mithilfe von ACLs.

### <a name="ddos-mitigation"></a>DDoS-Entschärfung
Verteilte Denial-of-Service-Angriffe (Distributed Denial Of Service, DDoS) stellen weiterhin ein echtes Risiko für die Zuverlässigkeit der Onlinedienste dar. Angesichts immer gezielterer und ausgefeilterer Angriffe sowie der zunehmenden geografischen Diversifizierung der von Microsoft bereitgestellten Dienste hat die Identifizierung von Angriffen und die Reduzierung ihrer Auswirkungen oberste Priorität.

[Azure DDoS Protection Standard](../virtual-network/ddos-protection-overview.md) bietet Schutz vor DDoS-Angriffen. Weitere Informationen finden Sie unter [Azure DDoS Protection: Bewährte Methoden und Referenzarchitekturen](azure-ddos-best-practices.md).

> [!NOTE]
> Microsoft bietet standardmäßig für alle Azure-Kunden DDoS-Schutz.
>
>

## <a name="network-connection-rules"></a>Netzwerkverbindungsregeln
Im Netzwerk stellt Azure Edgerouter bereit, die für Sicherheit auf Paketebene sorgen, um nicht autorisierte Versuche zu verhindern, eine Verbindung mit Azure herzustellen. Edgerouter sorgen dafür, dass die Pakete auch tatsächlich Daten im erwarteten Format enthalten und das Client-/Server-Kommunikationsschema erfüllen.

Edgerouter isolieren die Anwendungsumgebung vom Internet. Diese Edgerouter bieten Spoofingschutz und beschränken den Zugriff mithilfe von ACLs. Microsoft konfiguriert Edgerouter mithilfe eines ACL-Ansatzes und beschränken Netzwerkprotokolle, die die Edge- und Accessrouter durchlaufen.

Microsoft positioniert Netzwerkgeräte an Zugriffs- und Edgestandorten, die als Grenzpunkte fungieren, auf die Eingangs- oder Ausgangsfilter angewendet werden.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über den Schutz der Azure-Infrastruktur durch Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
