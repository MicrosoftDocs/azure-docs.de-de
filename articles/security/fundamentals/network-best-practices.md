---
title: 'Bewährte Methoden für Netzwerksicherheit: Microsoft Azure'
description: Dieser Artikel enthält eine Reihe von bewährten Methoden für die Netzwerksicherheit unter Verwendung der integrierten Azure-Funktionen.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 68f3b9f9cfa8ea74a8240fab6e47bf737788081f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094323"
---
# <a name="azure-best-practices-for-network-security"></a>Bewährte Methoden für die Netzwerksicherheit in Azure
In diesem Artikel werden bewährte Methoden zur Verbesserung der Netzwerksicherheit in Azure beschrieben. Diese empfohlenen Vorgehensweisen sind aus unseren Erfahrungen mit dem Azure-Netzwerk und den Erfahrungen von Kunden wie Ihnen abgeleitet.

In diesem Artikel wird für jede bewährte Methode Folgendes erläutert:

* Wobei es bei der bewährten Methode geht
* Warum Sie die bewährte Methode nutzen sollten
* Was die Folge sein könnte, wenn Sie die bewährte Methode nicht aktivieren
* Mögliche Alternativen zur bewährten Methode
* Wie Sie erfahren können, wie Sie die empfohlenen Vorgehensweisen aktivieren

Diese bewährten Methoden basieren auf einer Konsensmeinung und den Fähigkeiten und Funktionssätzen der Azure-Plattform zum Erstellungszeitpunkt dieses Artikels. Meinungen und Technologien ändern sich im Laufe der Zeit. Dieser Artikel wird daher regelmäßig aktualisiert, um diese Änderungen widerzuspiegeln.

## <a name="use-strong-network-controls"></a>Verwenden von starken Netzwerksteuerungen
Sie können [virtuelle Azure-Computer (VMs)](https://azure.microsoft.com/services/virtual-machines/) und Appliances mit anderen Geräten im Netzwerk verbinden, indem Sie sie in [Azure Virtual Networks](../../virtual-network/index.yml) anordnen. Dies ist ein Konstrukt, mit dem Sie virtuelle Netzwerkschnittstellenkarten mit einem virtuellen Netzwerk verbinden können, um die TCP/IP-basierte Kommunikation zwischen netzwerkfähigen Geräten zu ermöglichen. Virtuelle Computer, die mit einem Azure Virtual Network verbunden sind, können eine Verbindung mit Geräten im selben virtuellen Netzwerk, anderen virtuellen Netzwerken, im Internet oder sogar in eigenen lokalen Netzwerken herstellen.

Wenn Sie Ihr Netzwerk und die Sicherheit Ihres Netzwerks planen, empfehlen wir, Folgendes zu zentralisieren:

- Verwaltung von Kernnetzwerkfunktionen wie ExpressRoute, Bereitstellung von virtuellen Netzwerken und Subnetzen und IP-Adressvergabe.
- Kontrolle über Elemente der Netzwerksicherheit, etwa Funktionen virtueller Netzwerkappliances wie ExpressRoute, Bereitstellung von virtuellen Netzwerken und Subnetzen und IP-Adressvergabe.

Wenn Sie gemeinsame Verwaltungstools zum Überwachen Ihres Netzwerks und der Sicherheit Ihres Netzwerks verwenden, erhalten Sie einen klaren Einblick in beide Aspekte. Eine einfache und einheitliche Sicherheitsstrategie sorgt für weniger Fehler, da sie das menschliche Verständnis und die Zuverlässigkeit der Automatisierung erhöht.

## <a name="logically-segment-subnets"></a>Logische Segmentsubnetze
Virtuelle Azure-Netzwerke ähneln einem LAN in Ihrem lokalen Netzwerk. Virtuelle Azure-Netzwerke basieren auf der Idee, ein Netzwerk mit einem einzigen privaten IP-Adressraum zu erstellen, in dem Sie alle virtuellen Azure-Computer anordnen können. Die verfügbaren privaten IP-Adressräume liegen in den Bereichen der Klasse A (10.0.0.0/8), Klasse B (172.16.0.0/12) und Klasse C (192.168.0.0/16).

Zu den bewährten Methoden für die logische Segmentierung von Subnetzen gehören:

**Bewährte Methode**: Weisen Sie keine Zulassungsregeln mit umfassenden Bereichen zu (z. B. Zulassen von 0.0.0.0 bis 255.255.255.255).  
**Detail**: Stellen Sie sicher, dass Problembehandlungsverfahren vom Einrichten dieser Arten von Regeln abraten oder es untersagen. Diese Zulassungsregeln führen zu einem falschen Gefühl der Sicherheit, und sie werden häufig von Red Teams gefunden und ausgenutzt.

**Bewährte Methode**: Unterteilen Sie den größeren Adressraum in Subnetze.   
**Detail**: Sie können [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-basierte Subnetzprinzipien verwenden, um die Subnetze zu erstellen.

**Bewährte Methode**: Erstellen Sie Netzwerkzugriffssteuerungen zwischen Subnetzen. Das Routing zwischen den Subnetzen wird automatisch durchgeführt, und es ist nicht erforderlich, Routingtabellen manuell zu konfigurieren. Standardmäßig gibt es keine Netzwerkzugriffssteuerung zwischen den Subnetzen, die Sie in virtuellen Azure-Netzwerken erstellen.   
**Detail**: Verwenden Sie eine [Netzwerksicherheitsgruppe](../../virtual-network/virtual-network-vnet-plan-design-arm.md), um vor unerwünschtem Datenverkehr in Azure-Subnetzen zu schützen. Bei Netzwerksicherheitsgruppen handelt es sich um einfache zustandsbehaftete Paketuntersuchungsgeräte, die mit einem 5-Tupel-Ansatz (Quell-IP, Quellport, Ziel-IP, Zielport und Protokoll der Schicht 4) Zulassungs-/Verweigerungsregeln für den Netzwerkdatenverkehr erstellen. Sie können Datenverkehr für eine einzelne IP-Adresse, mehrere IP-Adressen und gesamte Subnetze in beiden Richtungen zulassen oder verweigern.

Bei der Verwendung von Netzwerksicherheitsgruppen für die Netzwerkzugriffssteuerung zwischen Subnetzen können Sie Ressourcen, die derselben Sicherheitszone oder Rolle angehören, in eigenen Subnetzen anordnen.

**Bewährte Methode**: Vermeiden Sie für weniger Komplexität und mehr Flexibilität kleine virtuelle Netzwerke und Subnetze.   
**Detail**: Die meisten Organisationen fügen mehr Ressourcen hinzu, als anfänglich geplant waren, und das erneute Zuordnen von Adressen ist arbeitsaufwendig. Die Verwendung von kleinen Subnetzen hat einen beschränkten Sicherheitswert, und das Zuordnen einer Netzwerksicherheitsgruppe für jedes Subnetz sorgt für Mehraufwand. Definieren Sie Subnetze allgemein, um Flexibilität für Wachstum sicherzustellen.

**Bewährte Methode**: Vereinfachen Sie die Regelverwaltung für Netzwerksicherheitsgruppen, indem Sie [Anwendungssicherheitsgruppen](https://azure.microsoft.com/blog/applicationsecuritygroups/) definieren.  
**Detail**: Definieren Sie eine Anwendungssicherheitsgruppe für Listen von IP-Adressen, die Ihrer Ansicht nach in Zukunft geändert oder für viele Netzwerksicherheitsgruppen verwendet werden könnten. Verwenden Sie aussagekräftige Namen für die Anwendungssicherheitsgruppen, damit andere Personen ihren Inhalt und Zweck verstehen.

## <a name="adopt-a-zero-trust-approach"></a>Einführen eines Zero Trust-Ansatzes
Umkreisnetzwerke basieren auf der Annahme, dass alle Systeme in einem Netzwerk vertrauenswürdig sind. Heutzutage greifen Mitarbeiter jedoch von jedem Ort aus und über eine Vielzahl von Geräten und Apps auf ihre Organisationsressourcen zu. Dadurch sind Sicherheitskontrollen auf Grundlage des Umkreises nicht mehr relevant. Zugriffssteuerungsrichtlinien, die nur darauf basieren, wer auf eine Ressource zugreifen kann, sind nicht ausreichend. Um das Gleichgewicht zwischen Sicherheit und Produktivität zu wahren, müssen Sicherheitsadministratoren auch berücksichtigen, *wie* auf eine Ressource zugegriffen wird.

Netzwerke müssen sich von traditionellen Schutzmaßnahmen weiterentwickeln, da sie möglicherweise anfällig für Sicherheitsverletzungen sind: Ein Angreifer kann einen einzigen Endpunkt innerhalb der vertrauenswürdigen Grenze gefährden und dann schnell im gesamten Netzwerk Fuß fassen. [Zero Trust](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/)-Netzwerke setzen das Konzept von Vertrauen anhand des Netzwerkstandorts in einem Umkreis außer Kraft. Stattdessen verwenden Zero Trust-Architekturen Vertrauensansprüche für Geräte und Benutzer, um den Zugriff auf Unternehmensdaten und -ressourcen zu steuern. Übernehmen Sie für neue Szenarien Zero Trust-Ansätze, die die Vertrauenswürdigkeit zum Zeitpunkt des Zugriffs überprüfen.

Bewährte Methoden:

**Bewährte Methode**: Gewähren Sie bedingten Zugriff auf Ressourcen basierend auf Gerät, Identität, Assurance, Netzwerkadresse und anderen.  
**Detail**: Mit [bedingtem Zugriff mit Azure AD](../../active-directory/conditional-access/overview.md) können Sie die passende Zugriffssteuerung anwenden, indem Sie automatisierte Entscheidungen hinsichtlich der Zugriffssteuerung anhand der erforderlichen Bedingungen implementieren. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Bewährte Methode**: Aktivieren Sie den Portzugriff erst nach Genehmigung des Workflows.  
**Detail**: Sie können [Just-In-Time-VM-Zugriff in Azure Security Center](../../security-center/security-center-just-in-time.md) verwenden, um eingehenden Datenverkehr auf den Azure-VMs zu sperren und dadurch die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einen einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen.

**Bewährte Methode**: Gewähren Sie temporäre Berechtigungen zum Ausführen privilegierter Aufgaben. Dadurch wird verhindert, dass böswillige oder nicht autorisierte Benutzer nach dem Ablauf der Berechtigungen Zugriff erhalten. Der Zugriff wird nur gewährt, wenn Benutzer ihn benötigen.  
**Detail**: Verwenden Sie Just-In-Time-Zugriff in Azure AD Privileged Identity Management oder in einer Drittanbieterlösung, um Berechtigungen zum Ausführen privilegierter Aufgaben zu gewähren.

Zero Trust ist der nächste Entwicklungsschritt bei der Netzwerksicherheit. Die Cyberangriffssituation bringt Organisationen dazu, grundsätzlich von Sicherheitsverletzungen auszugehen, aber dieser Ansatz sollte nicht zu Beschränkungen führen. Zero Trust-Netzwerke schützen Unternehmensdaten und -ressourcen und stellen gleichzeitig sicher, dass Organisationen mithilfe von Technologien, mit denen Mitarbeiter jederzeit, überall und auf jede erdenkliche Weise produktiv arbeiten können, ein modernes Unternehmen schaffen können.

## <a name="control-routing-behavior"></a>Steuern des Routingverhaltens
Wenn Sie einen virtuellen Computer in einem Azure Virtual Network anordnen, kann die VM eine Verbindung mit jeder anderen VM in demselben virtuellen Netzwerk verbinden, auch wenn sich die anderen VMs in unterschiedlichen Subnetzen befinden. Dies ist möglich, weil standardmäßig aktivierte Systemrouten diese Art der Kommunikation ermöglichen. Diese Standardrouten ermöglichen VMs in demselben virtuellen Netzwerk die Initiierung von Verbindungen untereinander und mit dem Internet (gilt nur für ausgehende Kommunikation mit dem Internet).

Die standardmäßigen Systemrouten sind zwar für viele Bereitstellungsszenarien nützlich, aber es kann auch vorkommen, dass Sie die Routingkonfiguration für Ihre Bereitstellungen anpassen möchten. Sie können die nächste Hopadresse konfigurieren, um bestimmte Ziele zu erreichen.

Wir empfehlen Ihnen, beim Bereitstellen einer Sicherheitsappliance für ein virtuelles Netzwerk [benutzerdefinierte Routen](../../virtual-network/virtual-networks-udr-overview.md) zu konfigurieren. Wir erläutern dies in einem späteren Abschnitt mit dem Titel [Beschränken und Schützen Ihrer kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Benutzerdefinierte Routen sind nicht erforderlich, und die Standardsystemrouten funktionieren in der Regel.
>
>

## <a name="use-virtual-network-appliances"></a>Verwenden virtueller Network Appliances
Netzwerksicherheitsgruppen und benutzerdefiniertes Routing können ein gewisses Maß an Netzwerksicherheit in der Netzwerk- und der Transportschicht des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) bieten. Aber in einigen Situationen wollen oder müssen Sie die Sicherheit auf hohen Ebenen des Stapels aktivieren. In diesen Situationen ist es ratsam, von Azure-Partnern angebotene Appliances für die Sicherheit virtueller Netzwerke bereitzustellen.

Sicherheitsappliances für Azure-Netzwerke können eine bessere Sicherheit bieten als die auf Netzwerkebene verfügbaren Steuerungen. Netzwerksicherheitsfunktionen, die von Sicherheitsappliances für virtuelle Netzwerke bereitgestellt werden, sind beispielsweise:


* Firewall
* Angriffserkennung/Eindringschutz
* Verwaltung von Sicherheitsrisiken
* Anwendungssteuerung
* Netzwerkbasierte Erkennung von Anomalien
* Webfilterung
* Virenschutz
* Botnet-Schutz

Die für ein virtuelles Azure-Netzwerk verfügbaren Sicherheitsappliances finden Sie im [Azure Marketplace](https://azure.microsoft.com/marketplace/). Suchen Sie nach den Begriffen „Sicherheit“ und „Netzwerksicherheit“.

## <a name="deploy-perimeter-networks-for-security-zones"></a>Bereitstellen von Umkreisnetzwerken für Sicherheitszonen
Ein [Umkreisnetzwerk](/azure/architecture/vdc/networking-virtual-datacenter) (auch als DMZ bezeichnet) ist ein physisches oder logisches Netzwerksegment, das als zusätzliche Sicherheitsebene zwischen Ihren Ressourcen und dem Internet zu dient. Spezielle Geräte für die Netzwerkzugriffssteuerung am Rande eines Umkreisnetzwerks erlauben nur den gewünschten Datenverkehr in Ihrem virtuellen Netzwerk.

Umkreisnetzwerke sind nützlich, da Sie sich bei der Verwaltung, Überwachung, Protokollierung und Berichterstellung für die Netzwerkzugriffssteuerung auf die Geräte am Rand des Azure Virtual Network konzentrieren können. In einem Umkreisnetzwerk aktivieren Sie normalerweise DDoS-Verhinderung (Distributed Denial of Service), Angriffserkennungs-/Eindringschutzsysteme (IDS/IPS), Firewallregeln und -richtlinien, Webfilterung, Antischadsoftware für das Netzwerk usw. Die Geräte für die Netzwerksicherheit sind zwischen dem Internet und Ihrem Azure Virtual Network angeordnet und verfügen in beiden Netzwerken über eine Schnittstelle.

Dies ist das grundlegende Design eines Umkreisnetzwerks, aber es gibt noch viele andere Designs, z. B. Back-to-Back, Tri-Homed und Multi-Homed.

Basierend auf dem zuvor beschriebenen Zero Trust-Konzept empfehlen wir für alle Bereitstellungen mit hohen Sicherheitsanforderungen die Nutzung eines Umkreisnetzwerks, um die Stufe der Netzwerksicherheit und der Zugriffssteuerung für Ihre Azure-Ressourcen zu erhöhen. Sie können Azure oder eine Drittanbieterlösung verwenden, um eine zusätzliche Sicherheitsebene zwischen Ihren Ressourcen und dem Internet bereitzustellen:

- Native Azure-Kontrollen. [Azure-Firewall](../../firewall/overview.md) und [Web Application Firewall in Application Gateway](../../application-gateway/features.md#web-application-firewall) bieten grundlegende Sicherheit mit einer vollständig zustandsbehafteten Firewall als Dienst, integrierte Hochverfügbarkeit, uneingeschränkte Cloudskalierbarkeit, FQDN-Filterung, Unterstützung für OWASP-Kernregelsätze sowie einfache Einrichtung und Konfiguration.
- Angebote von Drittanbietern. Suchen Sie im [Azure Marketplace](https://azuremarketplace.microsoft.com/) nach Angeboten für Next-Generation-Firewalls (NGFW) und anderen Drittanbieterangeboten, die vertraute Sicherheitstools und erheblich verbesserte Netzwerksicherheit bieten. Die Konfiguration ist möglicherweise komplexer, aber mit einem Angebot eines Drittanbieters können Sie eventuell bereits vorhandene Fähigkeiten und Kenntnisse einsetzen.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Vermeiden der Offenlegung gegenüber dem Internet mit dedizierten WAN-Links
Viele Organisationen haben sich für die Hybrid-IT-Route entschieden. Bei Hybrid-IT befinden sich einige Datenressourcen des Unternehmens in Azure, während andere weiterhin lokal gespeichert sind. In vielen Fällen werden einige Komponenten eines Diensts in Azure ausgeführt, während andere Komponenten weiterhin lokal vorhanden sind.

Bei einem Hybrid-IT-Szenario wird normalerweise eine Art von standortübergreifender Konnektivität verwendet. Standortübergreifende Konnektivität ermöglicht es dem Unternehmen, seine lokalen Netzwerke mit Azure Virtual Networks zu verbinden. Es sind zwei Lösungen für standortübergreifende Konnektivität verfügbar:

* [Site-to-Site-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Dies ist eine vertrauenswürdige, zuverlässige und bewährte Technologie, aber die Verbindung erfolgt über das Internet. Die Bandbreite ist mit einem Maximum von ca. 1,25 GBit/s relativ begrenzt. Site-to-Site-VPN ist in einigen Szenarien eine wünschenswerte Option.
* **Azure ExpressRoute**. Wir empfehlen, dass Sie für Ihre standortübergreifende Konnektivität [ExpressRoute](../../expressroute/expressroute-introduction.md) verwenden. Mit ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf die Microsoft Cloud ausdehnen. Mit ExpressRoute können Sie Verbindungen mit Microsoft Cloud Services wie Azure, Microsoft 365 und Dynamics 365 herstellen. ExpressRoute ist ein dedizierter WAN-Link zwischen Ihrem lokalen Standort und einem Microsoft Exchange-Hostinganbieter. Da dies eine Telekommunikationsverbindung ist, werden Ihre Daten nicht über das Internet übertragen und unterliegen daher auch nicht den potenziellen Risiken der Internetkommunikation.

Der Standort Ihrer ExpressRoute-Verbindung kann sich auf die Firewall-Kapazität, die Skalierbarkeit, die Zuverlässigkeit und die Sichtbarkeit des Netzwerkdatenverkehrs auswirken. Sie müssen ermitteln, wo Sie ExpressRoute in vorhandenen (lokalen) Netzwerken beenden sollten. Ihre Möglichkeiten:

- Führen Sie die Beendigung außerhalb der Firewall aus (Umkreisnetzwerkparadigma), wenn Sie Einblick in den Datenverkehr benötigen, eine vorhandene Methode der Isolation von Rechenzentren fortsetzen müssen oder ausschließlich Extranetressourcen in Azure integrieren.
- Führen Sie die Beendigung innerhalb der Firewall aus (Netzwerkerweiterungsparadigma). Dies ist die Standardempfehlung. In allen anderen Fällen wird empfohlen, Azure als weiteres Rechenzentrum zu behandeln.

## <a name="optimize-uptime-and-performance"></a>Optimieren der Betriebszeit und Leistung
Wenn ein Dienst ausgefallen ist, kann nicht auf Informationen zugegriffen werden. Wenn die Leistung so schlecht ist, dass die Daten nicht genutzt werden können, werden die Daten als nicht verfügbar angesehen. Aus Gründen der Sicherheit müssen Sie alles unternehmen, um sicherzustellen, dass für Ihre Dienste eine optimale Betriebszeit und Leistung gewährleistet ist.

Eine beliebte und effektive Methode zum Verbessern der Verfügbarkeit und Leistung ist der Lastenausgleich. Der Lastenausgleich ist ein Verfahren zum Verteilen von Netzwerkdatenverkehr auf Server, die Teil eines Diensts sind. Wenn Sie im Rahmen Ihres Diensts beispielsweise Front-End-Webserver verwenden, können Sie den Lastenausgleich nutzen, um Datenverkehr auf die verschiedenen Front-End-Webserver zu verteilen.

Die Verteilung von Datenverkehr erhöht die Verfügbarkeit, weil Folgendes passiert, wenn einer der Webserver nicht mehr verfügbar ist: Das Lastenausgleichsmodul beendet das Senden von Datenverkehr an den Server und leitet ihn an die Server um, die noch online sind. Außerdem trägt der Lastenausgleich zur Steigerung der Leistung bei, da der Mehraufwand für den Prozessor, das Netzwerk und den Arbeitsspeicher zum Verarbeiten von Anforderungen auf alle Server mit Lastenausgleich verteilt wird.

Wir empfehlen Ihnen, den Lastenausgleich nach Möglichkeit immer zu nutzen, wenn diese Vorgehensweise für Ihre Dienste geeignet ist. Es folgen Szenarien sowohl auf der Ebene des Azure Virtual Network als auch auf der globalen Ebene, zusammen mit den jeweiligen Lastausgleichsoptionen.

**Szenario:** Sie haben eine Anwendung:

- Bei denen Anforderungen einer Benutzer-/Clientsitzung den gleichen virtuellen Back-End-Computer erreichen müssen. Beispiele hierfür wären Einkaufswagen-Apps und Web-E-Mail-Server.
- Dabei ist nur eine sichere Verbindung zulässig. Unverschlüsselte Kommunikation mit den Servern sind keine akzeptable Option.
- Für die mehrere HTTP-Anforderungen über die gleiche lange bestehende TCP-Verbindung an verschiedene Back-End-Server weitergeleitet werden bzw. für die dort ein Lastenausgleich erfolgen muss.

**Option für den Lastenausgleich**: Verwenden Sie [Azure Application Gateway](../../application-gateway/overview.md), einen Lastenausgleich für HTTP-Web-Datenverkehr. Application Gateway unterstützt die End-to-End-TLS-Verschlüsselung und [TLS-Terminierung](../../application-gateway/overview.md) am Gateway. Webserver können dann von Ver- und Entschlüsselungs-Overheads und unverschlüsseltem Datenverkehr zu den Back-End-Servern entlastet werden.

**Szenario:** Sie benötigen für eingehende Verbindungen aus dem Internet einen Lastenausgleich zwischen Ihren Servern in einem virtuellen Azure-Netzwerk. Szenarien sind, wenn Sie:

- Zustandslose Anwendungen haben, die eingehende Anforderungen aus dem Internet akzeptieren.
- Keine persistenten Sitzungen oder TLS-Auslagerung benötigen. Persistente Sitzungen sind eine Methode, die für den Anwendungslastenausgleich verwendet wird, um die Serveraffinität zu erreichen.

**Option für den Lastenausgleich**: Verwenden Sie das Azure-Portal, um [einen externen Lastenausgleich](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu erstellen, der eingehende Anfragen auf mehrere VMs verteilt, um eine höhere Verfügbarkeit zu gewährleisten.

**Szenario:** Sie benötigen einen Lastenausgleich für Verbindungen von VMs, die sich nicht im Internet befinden. In den meisten Fällen werden die Verbindungen, die für den Lastenausgleich akzeptiert werden, von Geräten in einem Azure Virtual Network initiiert, wie z.B. SQL Server-Instanzen oder interne Webserver.   
**Option für den Lastenausgleich**: Verwenden Sie das Azure-Portal, um [einen internen Lastenausgleich](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu erstellen, der eingehende Anfragen auf mehrere VMs verteilt, um eine höhere Verfügbarkeit zu gewährleisten.

**Szenario:** Sie benötigen globalen Lastenausgleich, da Sie:

- Eine Cloudlösung haben, die weit über mehrere Regionen verteilt ist und ein Höchstmaß an Betriebszeit (Verfügbarkeit) erfordert.
- Ein Höchstmaß an Betriebszeit benötigen, um sicherzustellen, dass Ihr Dienst verfügbar ist, auch wenn ein ganzes Rechenzentrum nicht verfügbar ist.

**Option für den Lastenausgleich**: Verwenden Sie Azure Traffic Manager. Traffic Manager ermöglicht Ihnen den Lastenausgleich für Verbindungen mit Ihren Diensten basierend auf dem Standort des Benutzers.

Wenn ein Benutzer Ihren Dienst beispielsweise von einem EU-Standort aus anfordert, wird die Verbindung an Ihre Dienste umgeleitet, die in einem EU-Rechenzentrum angeordnet sind. Mit diesem Teil des globalen Lastenausgleichs von Traffic Manager können Sie die Leistung verbessern, da die Herstellung der Verbindung mit dem nächsten Rechenzentrum schneller als die Verbindung mit weit entfernten Rechenzentren möglich ist.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Deaktivieren des RDP/SSH-Zugriffs auf virtuelle Computer
Es ist möglich, mit dem [Remotedesktopprotokoll](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) und dem [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell)-Protokoll (SSH) eine Verbindung mit virtuellen Azure-Computern herzustellen. Diese Protokolle ermöglichen die Remoteverwaltung von VMs und sind Standard im Rechenzentrum.

Das potenzielle Sicherheitsproblem bei der Verwendung dieser Protokolle über das Internet besteht darin, dass Angreifer [Brute-Force](https://en.wikipedia.org/wiki/Brute-force_attack)-Verfahren einsetzen können, um Zugriff auf virtuelle Azure-Computer zu erlangen. Nachdem sich die Angreifer Zugang verschafft haben, können sie Ihre VM als Ausgangspunkt für die Kompromittierung anderer Computer im virtuellen Netzwerk nutzen oder sogar Netzwerkgeräte außerhalb von Azure angreifen.

Wir empfehlen Ihnen, den direkten RDP- und SSH-Zugriff auf Ihre virtuellen Azure-Computer über das Internet zu deaktivieren. Nachdem der direkte RDP- und SSH-Zugriff über das Internet deaktiviert wurde, haben Sie andere Möglichkeiten, um für die Durchführung der Remoteverwaltung auf diese VMs zuzugreifen.

**Szenario:** Ein Benutzer kann eine Verbindung mit einem Azure Virtual Network über das Internet herstellen.   
**Option**: [Point-to-Site-VPN-Verbindung](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) ist ein anderer Ausdruck für eine VPN-Client/Server-Verbindung mit Remotezugriff. Nachdem die Point-to-Site-Verbindung hergestellt wurde, kann der Benutzer per RDP oder SSH eine Verbindung mit allen VMs im Azure Virtual Network herstellen, mit denen der Benutzer per Point-to-Site-VPN-Verbindung verbunden ist. Hierbei wird davon ausgegangen, dass der Benutzer dazu berechtigt ist, auf diese VMs zuzugreifen.

Eine Point-to-Site-VPN-Verbindung ist sicherer als direkte RDP- oder SSH-Verbindungen, da sich der Benutzer zweimal authentifizieren muss, bevor er die Verbindung mit einer VM herstellen kann. Zunächst muss sich der Benutzer authentifizieren (und berechtigt sein), um die Point-to-Site-VPN-Verbindung herzustellen. Danach muss sich der Benutzer authentifizieren (und berechtigt sein), um die RDP- oder SSH-Sitzung zu erstellen.

**Szenario:** Ermöglichen Sie es Benutzern in Ihrem lokalen Netzwerk, sich mit VMs in Ihrem Azure Virtual Network zu verbinden.   
**Option**: Über eine [Site-to-Site-VPN-Verbindung](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) wird ein gesamtes Netzwerk über das Internet mit einem anderen Netzwerk verbunden. Sie können eine Site-to-Site-VPN-Verbindung verwenden, um Ihr lokales Netzwerk mit einem Azure Virtual Network zu verbinden. Benutzer in Ihrem lokalen Netzwerk verbinden sich über das RDP- oder SSH-Protokoll über die Site-to-Site-VPN-Verbindung. Sie müssen keinen direkten RDP- oder SSH-Zugriff über das Internet ermöglichen.

**Szenario:** Verwenden Sie eine dedizierte WAN-Verbindung, um Funktionalität bereitzustellen, die der Site-to-Site-VPN-Verbindung ähnelt.   
**Option**: Verwenden Sie [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Es bietet ähnliche Funktionen wie eine Site-to-Site-VPN-Verbindung. Im Folgenden werden die Hauptunterschiede erläutert:

- Die dedizierte WAN-Verbindung verläuft nicht über das Internet.
- Dedizierte WAN-Verbindungen sind in der Regel stabiler und leistungsfähiger.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Beschränken und Schützen Ihrer kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke
Verwenden Sie Azure Private Link für den Zugriff auf Azure-PaaS-Dienste (beispielsweise Azure Storage und SQL-Datenbank) über einen privaten Endpunkt in Ihrem virtuellen Netzwerk. Private Endpunkte ermöglichen es Ihnen, Ihre kritischen Azure-Dienstressourcen auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Der Datenverkehr aus Ihrem virtuellen Netzwerk an den Azure-Dienst verbleibt immer im Backbone-Netzwerk von Microsoft Azure. Ein Verfügbarmachen Ihres virtuellen Netzwerks im öffentlichen Internet ist nicht mehr erforderlich, um Azure-PaaS-Dienste zu nutzen. 

Azure Private Link bietet folgende Vorteile:
- **Verbesserte Sicherheit für Ihre Azure-Dienstressourcen**: Mit Azure Private Link können Azure-Dienstressourcen durch Nutzung eines privaten Endpunkts auf Ihr virtuelles Netzwerk beschränkt und so geschützt werden. Das Beschränken von Dienstressourcen auf einen privaten Endpunkt in einem virtuellen Netzwerk erhöht die Sicherheit, da der Zugriff über das öffentliche Internet auf Ressourcen vollständig verhindert und nur Datenverkehr vom privaten Endpunkt in Ihrem virtuellen Netzwerk zugelassen wird.
- **Privater Zugriff auf Azure-Dienstressourcen auf der Azure-Plattform**: Verbinden Sie Ihr virtuelles Netzwerk über private Endpunkte mit Diensten in Azure. Eine öffentliche IP-Adresse ist nicht erforderlich. Die Private Link-Plattform verarbeitet die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk.
- **Zugriff von lokalen Netzwerken und Peernetzwerken**: Greifen Sie mithilfe privater Endpunkte von einer lokalen Umgebung über privates ExpressRoute-Peering, VPN-Tunnel und virtuelle Netzwerke mit Peering auf Dienste zu, die in Azure ausgeführt werden. Es ist nicht erforderlich, ExpressRoute-Microsoft-Peering einzurichten oder über das Internet auf den Dienst zuzugreifen. Private Link ist eine sichere Möglichkeit, um Workloads zu Azure zu migrieren.
- **Schutz vor Datenlecks**: Ein privater Endpunkt wird nicht dem gesamten Dienst, sondern der Instanz einer PaaS-Ressource zugeordnet. Consumer können nur eine Verbindung mit der entsprechenden Ressource herstellen. Der Zugriff auf alle anderen Ressourcen des Diensts ist blockiert. Dieser Mechanismus ermöglicht den Schutz vor Risiken aufgrund von Datenlecks.
- **Globale Reichweite**: Stellen Sie private Verbindungen zu Diensten her, die in anderen Regionen ausgeführt werden. Das virtuelle Netzwerk des Consumers kann sich beispielsweise in Region A befinden und eine Verbindung mit Diensten in Region B herstellen.
- **Problemlose Einrichtung und Verwaltung**: Sie benötigen in Ihren virtuellen Netzwerken keine reservierten öffentlichen IP-Adressen mehr, um Azure-Ressourcen über eine IP-Firewall zu schützen. Es sind keine NAT- oder Gatewaygeräte erforderlich, um die privaten Endpunkte einzurichten. Private Endpunkte werden über einen einfachen Workflow konfiguriert. Auf Dienstseite können Sie auch die Verbindungsanforderungen für Ihre Azure-Dienstressource problemlos verwalten. Azure Private Link funktioniert auch für Consumer und Dienste, die zu unterschiedlichen Azure Active Directory-Mandanten gehören. 
    
Weitere Informationen zu privaten Endpunkten und den Azure-Diensten und Regionen, für die private Endpunkte verfügbar sind, finden Sie unter [Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview).


## <a name="next-steps"></a>Nächste Schritte
Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](best-practices-and-patterns.md).
