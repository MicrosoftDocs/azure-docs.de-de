---
title: Bewährte Methoden für die Azure-Netzwerksicherheit | Microsoft-Dokumentation
description: Lernen Sie einige der wesentlichen Features von Azure zum Einrichten sicherer Netzwerkumgebungen kennen.
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: cf015f4857a22b755813d0be1af5a55a8b7b6535
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360471"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Microsoft-Clouddienste und Netzwerksicherheit
Microsoft Cloud Services bieten hyperskalierbare Dienste und Infrastrukturen, Features auf Unternehmensniveau und vielfältige Auswahlmöglichkeiten für Hybridkonnektivität. Kunden können entweder über das Internet oder mit Azure ExpressRoute auf diese Dienste zugreifen. Letzteres stellt private Netzwerkkonnektivität bereit. Microsoft Azure Platform ermöglicht Kunden eine nahtlose Erweiterung ihrer Infrastruktur auf die Cloud und den Aufbau mehrstufiger Architekturen. Zudem können Drittanbieter über Sicherheitsdienste und virtuelle Geräte erweiterte Funktionen anbieten. Dieses Whitepaper bietet einen Überblick über Aspekte zu Sicherheit und Architektur, die Kunden beim Einsatz von Microsoft-Clouddiensten über ExpressRoute berücksichtigen sollten. Außerdem behandelt es die Erstellung sicherer Dienste in virtuellen Azure-Netzwerken.

## <a name="fast-start"></a>Schnelleinstieg
Das folgende logische Diagramm bietet Hilfestellung bei der Auswahl aus den vielen Sicherheitsverfahren, die mit der Azure Platform zur Verfügung stehen. Für eine Kurzübersicht suchen Sie das Beispiel heraus, das Ihrem Fall am ehesten entspricht. Wenn Sie ausführlichere Erklärungen benötigen, lesen Sie das Whitepaper vollständig durch.
[![0]][0]

[Beispiel 1: Erstellen eines Umkreisnetzwerks (auch bekannt als DMZ, demilitarisierte Zone oder überwachtes Subnetz) zum Schutz von Anwendungen durch Netzwerksicherheitsgruppen (NSGs)](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Beispiel 2: Erstellen eines Umkreisnetzwerks zum Schutz von Anwendungen durch eine Firewall und NSGs](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Beispiel 3: Erstellen eines Umkreisnetzwerks zum Schutz von Netzwerken durch eine Firewall, UDR (User-Defined Routing, benutzerdefiniertes Routing) und NSGs](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Beispiel 4: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-VPN über ein virtuelles Gerät](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Beispiel 5: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-, Azure-VPN-Gateway](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Beispiel 6: Hinzufügen einer Hybridverbindung mit ExpressRoute](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Beispiele für das Hinzufügen von Verbindungen zwischen virtuellen Netzwerken, Hochverfügbarkeit und Dienstverkettung werden diesem Dokument in den nächsten Monaten hinzugefügt.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Microsoft-Compliance und Infrastrukturschutz
Um Organisationen bei der Einhaltung nationaler sowie land- und branchenspezifischer Anforderungen hinsichtlich der Erfassung und Nutzung personenbezogener Daten zu helfen, bietet Microsoft mehr als 40 Zertifizierungen und Nachweise. Dies sind mehr als alle anderen Clouddienstanbieter.

Weitere Informationen finden Sie in den Informationen zur Compliance im [Microsoft Trust Center][TrustCenter].

Microsoft verfolgt einen umfassenden Ansatz zum Schutz der Cloudinfrastruktur, die zur Ausführung hyperskalierbarer globaler Dienste erforderlich ist. Die Microsoft-Cloudinfrastruktur umfasst neben den physischen Rechenzentren auch Hardware, Software, Netzwerke sowie administratives und operatives Personal.

![2]

Dieser Ansatz bietet Kunden eine sicherere Grundlage für die Bereitstellung ihrer Dienste in der Microsoft-Cloud. Der nächste Schritt besteht für Kunden darin, eine sichere Architektur zum Schutz dieser Dienste zu entwerfen und einzurichten.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Traditionelle Sicherheitsarchitekturen und Umkreisnetzwerke
Wenngleich Microsoft hohe Investitionen in den Schutz der Cloudinfrastruktur tätigt, müssen Kunden ebenfalls für den Schutz ihrer Clouddienste und Ressourcengruppen sorgen. Ein mehrstufiger Sicherheitsansatz bietet die beste Absicherung. Die Sicherheitszone eines Umkreisnetzwerks schützt die internen Netzwerkressourcen vor einem nicht vertrauenswürdigen Netzwerk. Ein Umkreisnetzwerk bezieht sich auf die Grenzen oder die Teile des Netzwerks, die sich zwischen dem Internet und der geschützten IT-Infrastruktur des Unternehmens befinden.

In typischen Unternehmensnetzwerken wird der Kern der Infrastruktur im Umkreisnetzwerk durch mehrere Stufen von Sicherheitsgeräten stark geschützt. An den Grenzen der einzelnen Stufen befinden sich Sicherheitsgeräte und Stellen zur Durchsetzung von Richtlinien. Jede Stufe kann eine Kombination der folgenden Geräte für Netzwerksicherheit enthalten: Firewalls, DoS-Schutz (Denial of Service), IDS/IPS (Intrusion Detection/Protection Systems) und VPN-Geräte (virtuelle private Netzwerke). Die Durchsetzung von Richtlinien kann über Firewallrichtlinien, Zugriffssteuerungslisten (Access Control Lists, ACLs) oder ein spezifisches Routing erfolgen. Die erste Schutzstufe im Netzwerk, die für den direkten Empfang von Datenverkehr aus dem Internet verantwortlich ist, nutzt eine Kombination dieser Mechanismen, um Angriffe und schädlichen Datenverkehr abzuwehren, aber legitime Anforderungen an das Netzwerk weiterzuleiten. Dieser Datenverkehr wird direkt an Ressourcen im Umkreisnetzwerk geroutet. Die Ressourcen können dann mit anderen Ressourcen kommunizieren, die sich weiter im Inneren des Netzwerks befinden. Zuerst müssen sie jedoch die nächste Sicherheitsgrenze passieren. Die ganz außen liegende Schicht wird als Umkreisnetzwerk bezeichnet, da dieser Teil des Netzwerks dem Internet gegenüber offen ist. Üblicherweise kommen verschiedene Formen des Schutzes auf beiden Seiten zum Einsatz. Die nachfolgende Abbildung zeigt ein Beispiel für ein Umkreisnetzwerk mit einem einzigen Subnetz in einem Unternehmensnetzwerk. Es umfasst zwei Sicherheitsgrenzen.

![3]

Es gibt viele Architekturen, die zum Implementieren eines Umkreisnetzwerks verwendet werden. Diese Architekturen reichen von einem einfachen Lastenausgleichsmodul bis hin zu einem Umkreisnetzwerk mit mehreren Subnetzen und verschiedenen Mechanismen an jeder Grenze, um Datenverkehr zu blockieren und die tieferen Schichten des Unternehmensnetzwerks zu schützen. Der Aufbau des Umkreisnetzwerks richtet sich nach den spezifischen Anforderungen der Organisation und der Risikotoleranz insgesamt.

Wenn Kunden ihre Workloads in öffentliche Clouds verschieben, ist es von wesentlicher Bedeutung, ähnliche Funktionen für Umkreisnetzwerk-Architekturen in Azure zu unterstützen, um Compliance- und Sicherheitsanforderungen zu erfüllen. Dieses Dokument bietet Kunden eine Hilfestellung zum Einrichten einer sicheren Netzwerkumgebung in Azure. Der Schwerpunkt liegt dabei auf dem Umkreisnetzwerk. Es enthält aber auch eine umfassende Erörterung vieler Aspekte der Netzwerksicherheit. Dieser Erörterung liegen die folgenden Fragen zugrunde:

* Wie kann ein Umkreisnetzwerk in Azure erstellt werden?
* Welche Azure-Features stehen zum Erstellen des Umkreisnetzwerks unter anderem zur Verfügung?
* Wie können Back-End-Workloads geschützt werden?
* Wie wird die Internetkommunikation mit Workloads in Azure gesteuert?
* Wie können lokale Netzwerke über Bereitstellungen in Azure geschützt werden?
* Wann sollten native Azure-Sicherheitsfeatures und wann Geräte oder Dienste von Drittanbietern genutzt werden?

Das folgende Diagramm zeigt verschiedene Sicherheitsstufen, die Azure den Kunden bietet. Bei diesen Stufen handelt es sich sowohl um nativ in der Azure Platform enthaltene Funktionen als auch um benutzerdefinierte Features:

![4]

Als erste Grenze gegenüber dem Internet bietet Azure DDoS Schutz vor groß angelegten Angriffen gegen Azure. Die nächste Stufe sind vom Kunden definierten öffentliche IP-Adressen (Endpunkte), anhand derer ermittelt wird, welcher Datenverkehr den Clouddienst zum virtuellen Netzwerk passieren darf. Die native Isolation virtueller Azure-Netzwerke stellt eine vollständige Isolation von allen anderen Netzwerken sicher und gewährleistet, dass der Datenverkehr nur über vom Benutzer konfigurierte Pfade und Methoden fließt. Diese Pfade und Methoden stellen die nächste Stufe dar, in der NSGs, UDR und virtuelle Netzwerkgeräte zum Einrichten von Sicherheitsgrenzen verwendet werden können, um die Anwendungsbereitstellungen im geschützten Netzwerk abzusichern.

Im nächsten Abschnitt erhalten Sie einen Überblick über virtuelle Azure-Netzwerke. Diese virtuellen Netzwerke werden von den Kunden erstellt und sind die Netzwerke, mit denen die bereitgestellten Workloads eine Verbindung herstellen. Virtuelle Netzwerke sind die Grundlage aller Netzwerksicherheitsfeatures, die zum Einrichten eines Umkreisnetzwerks für den Schutz von Kundenbereitstellungen in Azure erforderlich sind.

## <a name="overview-of-azure-virtual-networks"></a>Übersicht über virtuelle Azure-Netzwerke
Bevor der Internetdatenverkehr die virtuellen Azure-Netzwerke erreicht, müssen zwei Sicherheitsstufen der Azure Platform passiert werden:

1.    **DDoS-Schutz**: DDoS-Schutz ist eine Stufe des physischen Azure-Netzwerks, das die Azure Platform selbst vor groß angelegten internetbasierten Angriffen schützt. Bei diesen Angriffe wird mithilfe mehrerer „Bot“-Knoten versucht, einen Internetdienst zu überlasten. Azure verfügt über ein robustes DDoS-Schutzsystem für alle eingehenden und ausgehenden Verbindungen sowie für Verbindungen über Azure-Regionen hinweg. Diese DDoS-Sicherheitsstufe umfasst keine durch den Benutzer konfigurierbaren Attribute und ist für den Kunden nicht zugänglich. Die DDoS-Sicherheitsstufe schützt Azure als Plattform vor groß angelegten Angriffen und überwacht zudem ausgehenden Datenverkehr und Datenverkehr über Azure-Regionen hinweg. Mit virtuellen Netzwerkgeräten im VNET können Kunden zusätzliche Ausfallsicherheitslösungen zum Schutz vor kleineren Angriffen konfigurieren, die vom Plattformschutz nicht abgefangen werden. Beispiel für DDoS in Aktion: Wenn eine IP-Adresse mit Internetzugriff durch einen groß angelegten DDoS-Angriff angegriffen wurde, würde Azure die Quellen der Angriffe erkennen und den problematischen Datenverkehr bereinigen, bevor er das vorgesehene Ziel erreicht. In fast allen Fällen beeinträchtigt der Angriff den angegriffenen Endpunkt nicht. In den seltenen Fällen, in denen ein Endpunkt betroffen ist, wird kein Datenverkehr an andere Endpunkte beeinflusst, nur der angegriffene Endpunkt. Also werden andere Kunden und Dienste in keiner Weise durch den Angriff beeinträchtigt. Es ist wichtig zu beachten, dass Azure DDoS nur nach groß angelegten Angriffen sucht. Es ist möglich, dass Ihr spezifischer Dienst überlastet ist, bevor die Schwellenwerte für den Plattformschutz überschritten werden. Beispielsweise könnte eine Website auf einem einzelnen A0 IIS-Server durch einen DDoS-Angriff offline geschaltet werden, bevor der Azure DDoS-Plattformschutz eine Bedrohung registriert.

2.  **Öffentliche IP-Adressen**: Mit öffentlichen IP-Adressen (aktiviert über Dienstendpunkte, öffentliche IP-Adressen, Application Gateway und andere Azure-Funktionen, die für das Internet eine öffentliche IP-Adresse für die Weiterleitung an Ihre Ressource bieten) können Clouddienste oder Ressourcengruppen öffentliche IP-Internetadressen und Ports verfügbar machen. Der Endpunkt verwendet NAT (Network Address Translation) zum Weiterleiten von Datenverkehr an die interne Adresse und den Port im virtuellen Azure-Netzwerk. Dieser Pfad ist der primäre Weg, auf dem externer Datenverkehr in das virtuelle Netzwerk gelangt. Die öffentlichen IP-Adressen können konfiguriert werden und legen fest, welcher Datenverkehr zugelassen wird bzw. wie und wo eine Übersetzung in das virtuelle Netzwerk stattfindet.

Sobald Datenverkehr das virtuelle Netzwerk erreicht, kommen zahlreiche Funktionen ins Spiel. Virtuelle Azure-Netzwerke sind die Grundlage, auf der die Kunden ihre Workloads einbinden und die grundlegende Sicherheit auf Netzwerkebene anwenden. Es handelt sich um ein privates Netzwerk (ein virtuelles Netzwerkoverlay) in Azure, das den Kunden die folgenden Features und Merkmale bietet:

* **Isolation des Datenverkehrs**: Azure Platform verwendet virtuelle Netzwerke, um Datenverkehr zu isolieren. Virtuelle Computer in einem virtuellen Netzwerk können nicht direkt mit virtuellen Computern in einem anderen virtuellen Netzwerk kommunizieren – selbst dann nicht, wenn beide virtuellen Netzwerke durch denselben Kunden erstellt werden. Isolation ist eine wichtige Eigenschaft, mit der sichergestellt wird, dass VMs und die Kommunikation von Kunden innerhalb eines virtuellen Netzwerks privat bleiben.

>[!NOTE]
>Datenverkehrsisolation bezieht sich nur auf in das virtuelle Netzwerk *eingehenden* Datenverkehr. Standardmäßig ist ausgehender Datenverkehr des VNET mit dem Internet zulässig, kann jedoch bei Bedarf durch NSGs verhindert werden.
>
>

* **Mehrstufige Topologie**: Virtuelle Netzwerke ermöglichen Kunden das Definieren einer mehrstufigen Topologie, indem unterschiedlichen Elementen oder „Stufen“ ihrer Workloads Subnetze und separate Adressräume zugewiesen werden. Diese logischen Gruppierungen und Topologien ermöglichen es den Kunden, unterschiedliche Zugriffsrichtlinien basierend auf dem Workloadtyp zu definieren und außerdem den Datenverkehr zwischen den Stufen zu steuern.
* **Standortübergreifende Konnektivität**: Kunden können standortübergreifende Konnektivität zwischen einem virtuellen Netzwerk und mehreren lokalen Standorten oder anderen virtuellen Netzwerken in Azure einrichten. Um eine Verbindung herzustellen, können Kunden VNET-Peering, Azure-VPN-Gateways, virtuelle Netzwerkgeräte von Drittanbietern oder ExpressRoute verwenden. Azure unterstützt Site-to-Site-VPNs (S2S) unter Verwendung von IPsec/IKE-Standardprotokollen und privater ExpressRoute-Konnektivität.
* **NSG** ermöglicht dem Kunden das Erstellen von Regeln (ACLs) auf der gewünschten Ebene: für Netzwerkschnittstellen, einzelne VMs oder virtuelle Subnetze. Kunden können den Zugriff steuern, indem sie die Kommunikation zwischen den Workloads innerhalb eines virtuellen Netzwerks, zwischen Systemen im Kundennetzwerk über standortübergreifende Verbindungen oder die direkte Internetkommunikation zulassen oder ablehnen.
* **UDR** und **IP-Weiterleitung** ermöglichen Kunden das Definieren der Kommunikationspfade zwischen unterschiedlichen Stufen innerhalb eines virtuellen Netzwerks. Kunden können eine Firewall, IDS/IPS und andere virtuelle Geräte bereitstellen und den Netzwerkdatenverkehr zum Erzwingen von Sicherheitsrichtlinien, zur Überprüfung und zu Überwachungszwecken über diese Sicherheitsgeräte routen.
* **Virtuelle Netzwerkgeräte** im Azure Marketplace: Sicherheitsgeräte wie z.B. Firewalls, Lastenausgleichsmodule und IDS/IPS stehen im Azure Marketplace und im VM-Imagekatalog zur Verfügung. Kunden können diese Geräte in ihren virtuellen Netzwerken und insbesondere an ihren Sicherheitsgrenzen (einschließlich der Subnetze des Umkreisnetzwerks) bereitstellen, um ihre mehrstufige Sicherheitsumgebung zu vervollständigen.

Mithilfe dieser Features und Komponenten kann ein Beispiel für eine Umkreisnetzwerk-Architektur auf der Grundlage von Azure wie in folgendem Diagramm aussehen:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Umkreisnetzwerk – Merkmale und Anforderungen 
Das Umkreisnetzwerk ist das Front-End des Netzwerks und stellt eine direkte Schnittstelle zur Kommunikation mit dem Internet dar. Die eingehenden Paket sollten über die Sicherheitsgeräte wie Firewall, IDS und IPS geleitet werden, bevor sie die Back-End-Server erreichen. Von den Workloads an das Internet gesendete Pakete können zur Richtlinienerzwingung, zur Überprüfung und zu Überwachungszwecken ebenfalls über die Sicherheitsgeräte im Umkreisnetzwerk geleitet werden, bevor sie das Netzwerk verlassen. Zusätzlich kann das Umkreisnetzwerk zum Hosten standortübergreifender VPN Gateways zwischen virtuellen Kundennetzwerken und lokalen Netzwerken eingesetzt werden.

### <a name="perimeter-network-characteristics"></a>Merkmale des Umkreisnetzwerks
In Bezug auf die obige Abbildung weist ein gutes Umkreisnetzwerk folgende Merkmale auf:

* Internetzugriff:
  * Das Subnetz des Umkreisnetzwerks selbst ist eine Schnittstelle zur direkten Kommunikation mit dem Internet.
  * Öffentliche IP-Adressen, VIPs und/oder Dienstendpunkte leiten Internetdatenverkehr an das Front-End-Netzwerk und die zugehörigen Geräte.
  * Eingehender Datenverkehr aus dem Internet passiert die Sicherheitsgeräte, bevor andere Ressourcen im Front-End-Netzwerk erreicht werden.
  * Wenn die ausgehende Sicherheit aktiviert ist, passiert der Datenverkehr im letzten Schritt die Sicherheitsgeräte, bevor er an das Internet gesendet wird.
* Geschütztes Netzwerk:
  * Es gibt keinen direkten Pfad vom Internet zur Kerninfrastruktur.
  * Kanäle zur Kerninfrastruktur müssen die Sicherheitsgeräte passieren, z. B. NSGs, Firewalls oder VPN-Geräte.
  * Andere Geräte dürfen keine Überbrückung zwischen Internet und Kerninfrastruktur herbeiführen.
  * Als Sicherheitsgeräte sowohl an der Grenze zum Internet als auch an der Grenze zum geschützten Netzwerk des Umkreisnetzwerks (z. B. die beiden Firewallsymbole in der obigen Abbildung) kann tatsächlich ein einzelnes virtuelles Gerät mit differenzierten Regeln oder Schnittstellen für jede Grenze dienen (beispielsweise ein physisches Gerät, das logisch getrennt wird und die Last für beide Grenzen des Umkreisnetzwerks verarbeitet).
* Weitere gängige Methoden und Einschränkungen:
  * Workloads dürfen keine kritischen Geschäftsinformationen speichern.
  * Der Zugriff auf und die Aktualisierung von Umkreisnetzwerk-Konfigurationen und -Bereitstellungen ist ausschließlich auf autorisierte Administratoren beschränkt.

### <a name="perimeter-network-requirements"></a>Anforderungen des Umkreisnetzwerks
Um diese Merkmale umzusetzen, befolgen Sie diese Richtlinien zu den Anforderungen virtueller Netzwerke für eine erfolgreiche Implementierung eines Umkreisnetzwerks:

* **Subnetzarchitektur** : Konfigurieren Sie das virtuelle Netzwerk so, dass ein gesamtes Subnetz als Umkreisnetzwerk reserviert ist, getrennt von anderen Subnetzen im selben virtuellen Netzwerk. Durch diese Trennung wird sichergestellt, dass der Datenverkehr zwischen dem Umkreisnetzwerk und anderen internen oder privaten Subnetzstufen über eine Firewall oder ein virtuelles IDS/IPS-Gerät fließt.  Benutzerdefinierte Routen an den Grenzsubnetzen sind erforderlich, um diesen Datenverkehr an das virtuelle Gerät weiterzuleiten.
* **NSG** : Das Subnetz des Umkreisnetzwerks selbst sollte offen sein, um eine Kommunikation mit dem Internet zu ermöglichen, aber dies bedeutet nicht, dass Kunden die NSGs umgehen sollten. Befolgen Sie allgemeine Sicherheitsmaßnahmen, um die aus dem Internet zugänglichen Netzwerkoberflächen auf ein Mindestmaß zu beschränken. Beschränken Sie die Remoteadressbereiche, die auf die Bereitstellungen oder die spezifischen Anwendungsprotokolle und geöffneten Ports zugreifen dürfen. Es gibt allerdings Situationen, in denen eine vollständige Sperrung nicht möglich ist. Wenn Kunden beispielsweise über eine externe Website in Azure verfügen, sollte das Umkreisnetzwerk eingehende Webanforderungen von beliebigen öffentlichen IP-Adressen zulassen, jedoch nur die Webanwendungsports öffnen: TCP an Port 80 und/oder TCP an Port 443.
* **Routingtabelle** : Das Subnetz des Umkreisnetzwerks selbst sollte in der Lage sein, direkt mit dem Internet zu kommunizieren, es sollte jedoch keine direkte Kommunikation von und zum Back-End oder lokalen Netzwerken stattfinden, ohne den Datenverkehr über eine Firewall oder ein Sicherheitsgerät zu leiten.
* **Konfiguration von Sicherheitsgeräten**: Um Pakete zwischen dem Umkreisnetzwerk und allen Komponenten des geschützten Netzwerks weiterzuleiten und zu überprüfen, können Sicherheitsgeräte wie z.B. Firewall-, IDS- und IPS-Geräte mehrfach vernetzt sein. Sie können mit separaten Netzwerkkarten (NICs) für das Umkreisnetzwerk und die Back-End-Subnetze ausgestattet sein. Die NICs im Umkreisnetzwerk kommunizieren direkt mit dem Internet (eingehend und ausgehend), unter Verwendung entsprechender NSGs und der Routingtabelle für das Umkreisnetzwerk. Die NICs zur Verbindung mit den Back-End-Subnetzen verfügen über eingeschränktere NSGs und Routingtabellen für die entsprechenden Back-End-Subnetze.
* **Funktionalität der Sicherheitsgeräte** : Die im Umkreisnetzwerk bereitgestellten Sicherheitsgeräte erfüllen typischerweise die folgenden Funktionen:
  * Firewall: Durchsetzen von Firewallregeln oder Richtlinien für die Zugriffssteuerung für eingehende Anforderungen.
  * Angriffserkennung und -abwehr: Erkennen und Abschwächen böswilliger Angriffe aus dem Internet.
  * Überwachung und Protokollierung: Speichern detaillierter Protokolle für Überwachung und Analyse.
  * Reverseproxy: Umleiten eingehender Anforderungen an die entsprechenden Back-End-Server. Zu dieser Umleitung gehört die Zuordnung und Übersetzung der Zieladressen auf den Front-End-Geräten (in der Regel Firewalls) in die Adressen der Back-End-Server.
  * Weiterleitungsproxy: Bereitstellen der Netzwerkadressübersetzung (NAT) und Überwachen auf Kommunikation aus dem virtuellen Netzwerk mit dem Internet.
  * Router: Weiterleitung von eingehendem und subnetzübergreifendem Datenverkehr innerhalb des virtuellen Netzwerks.
  * VPN-Geräte: Fungieren als standortübergreifende VPN Gateways zur Bereitstellung standortübergreifender VPN-Konnektivität zwischen lokalen Kundennetzwerken und virtuellen Azure-Netzwerken.
  * VPN-Server: Annehmen von VPN-Clientverbindungen mit virtuellen Azure-Netzwerken.

> [!TIP]
> Sorgen Sie für eine vollständige Trennung der beiden folgenden Gruppen: dem autorisierten Personal mit Zugriff auf die Sicherheitsgeräte des Umkreisnetzwerks und den Mitarbeitern, die als Administratoren für Entwicklung, Bereitstellung oder Betrieb von Anwendungen zuständig sind. Durch eine strikte Trennung dieser Gruppen können Sie eine klare Aufgabenverteilung erzielen und verhindern, dass einzelne Personen Kontrollmechanismen zur Anwendungs- und Netzwerksicherheit umgehen.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Fragen in Bezug auf die Einrichtung von Netzwerkgrenzen
Sofern nicht ausdrücklich erwähnt, verweist der Begriff „Netzwerke“ in diesem Abschnitt auf private virtuelle Azure-Netzwerke, die von einem Abonnementadministrator erstellt wurden. Der Begriff bezieht sich nicht auf die zugrunde liegenden physischen Netzwerke in Azure.

Virtuelle Azure-Netzwerke werden auch häufig dazu verwendet, traditionelle lokale Netzwerke zu erweitern. Es ist möglich, entweder Standort-zu-Standort- oder ExpressRoute-Hybridnetzwerklösungen in Umkreisnetzwerk-Architekturen zu integrieren. Dieser Hybridlink ist ein wichtiger Aspekt bei der Erstellung von Netzwerksicherheitsgrenzen.

Die Beantwortung der folgenden drei Fragen ist entscheidend, wenn Sie ein Netzwerk mit einem Umkreisnetzwerk und mehrere Sicherheitsgrenzen erstellen.

#### <a name="1-how-many-boundaries-are-needed"></a>1) Wie viele Grenzen werden benötigt?
Im ersten Schritt muss entschieden werden, wie viele Sicherheitsgrenzen in einem vorgegebenen Szenario benötigt werden:

* Eine einzelne Grenze: Eine Grenze am Front-End-Umkreisnetzwerk zwischen dem virtuellen Netzwerk und dem Internet.
* Zwei Grenzen: Eine Grenze auf der Internetseite des Umkreisnetzwerks und eine zweite Grenze zwischen dem Subnetz des Umkreisnetzwerks und den Back-End-Subnetzen in den virtuellen Azure-Netzwerken.
* Drei Grenzen: Eine Grenze auf der Internetseite des Umkreisnetzwerks, eine zweite Grenze zwischen dem Subnetz des Umkreisnetzwerks und den Back-End-Subnetzen und eine dritte zwischen den Back-End-Subnetzen und dem lokalen Netzwerk.
* N Grenzen: eine variable Anzahl. Je nach geltenden Sicherheitsanforderungen können beliebig viele Sicherheitsgrenzen in einem Netzwerk eingerichtet werden.

Die Anzahl und Art der erforderlichen Grenzen variiert basierend darauf, wie hoch die Risikotoleranz eines Unternehmens ist und welches Szenario implementiert wird. Diese Entscheidung wird häufig gemeinsam von verschiedenen Gruppen innerhalb einer Organisation getroffen – z.B. durch ein Team für Risiko und Compliance, ein Netzwerk- und Plattformteam und ein Team für die Anwendungsentwicklung. Bei dieser Entscheidung sollten Personen mit Einblick in die Sicherheit, relevante Daten und die eingesetzte Technologie einbezogen werden, um ein angemessenes Sicherheitsniveau für jede Implementierung zu gewährleisten.

> [!TIP]
> Verwenden Sie die kleinstmögliche Anzahl von Grenzen, mit denen die Sicherheitsanforderungen für ein vorgegebenes Szenario erfüllt werden können. Je mehr Grenzen implementiert werden, desto schwieriger können sich Betrieb und Problembehandlung gestalten. Gleichzeitig steigt mit der Zeit der Aufwand für das Verwalten der verschiedenen Grenzrichtlinien. Durch eine unzureichende Anzahl von Grenzen steigt jedoch das Risiko. Entscheidend ist, die richtige Balance zu finden.
>
>

![6]

Die obige Abbildung zeigt einen allgemeinen Überblick über ein Netzwerk mit drei Sicherheitsgrenzen. Die Grenzen bestehen zwischen dem Umkreisnetzwerk und dem Internet, dem privaten Azure-Front-End- und -Back-End-Subnetz sowie dem Azure-Back-End-Subnetz und dem lokalen Unternehmensnetzwerk.

#### <a name="2-where-are-the-boundaries-located"></a>2) Wo befinden sich die Grenzen?
Nachdem festgelegt wurde, wie viele Grenzen erforderlich sind, muss entschieden werden, wo diese implementiert werden. Es gibt im Allgemeinen drei Optionen:

* Verwenden einen internetbasierten Zwischendiensts (z. B. einer Cloud-basierten Webanwendungsfirewall, die nicht in diesem Dokument erläutert wird)
* Verwenden nativer Funktionen und/oder virtueller Netzwerkgeräte in Azure
* Verwenden physischer Geräte im lokalen Netzwerk

In reinen Azure-Netzwerken können entweder native Azure-Features (z. B. Azure Load Balancer) oder virtuelle Netzwerkgeräte aus dem umfangreichen Partnerökosystem von Azure (z. B. Check Point-Firewalls) genutzt werden.

Wenn eine Grenze zwischen Azure und einem lokalen Netzwerk benötigt wird, können die Sicherheitsgeräte sich auf einer beliebigen Seite der Verbindung (oder auf beiden Seiten) befinden. Es muss also entschieden werden, wo die Sicherheitsgeräte platziert werden.

In der obigen Abbildung sind die Grenzen zwischen Internet und Umkreisnetzwerk und zwischen Front-End und Back-End vollständig in Azure enthalten, deshalb müssen entweder native Azure-Features oder virtuelle Netzwerkgeräte verwendet werden. Sicherheitsgeräte an der Grenze zwischen Azure (Back-End-Subnetz) und dem Unternehmensnetzwerk können entweder auf Azure-Seite oder auf lokaler Seite platziert werden. Es ist auch eine Kombination von Geräten auf beiden Seiten möglich. Da jede Option maßgebliche Vor- und Nachteile mit sich bringen kann, muss jede Option sorgfältig überdacht werden.

Beispielsweise bietet die Verwendung vorhandener physischer Sicherheitsgeräte auf der Seite des lokalen Netzwerks den Vorteil, dass keine neuen Geräte erforderlich sind. Sie müssen nur neu konfiguriert werden. Der Nachteil besteht jedoch darin, dass der gesamte Datenverkehr von Azure zurück an das lokale Netzwerk geleitet werden muss, damit er die Sicherheitsgeräte passiert. Der Azure-zu-Azure-Datenverkehr könnte sich erheblich auf die Latenz und damit auf die Anwendungsleistung und das Benutzererlebnis auswirken, wenn der Datenverkehr zur Erzwingung von Sicherheitsrichtlinien zurück an das lokale Netzwerk geleitet werden muss.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) Wie werden die Grenzen implementiert?
Jede Sicherheitsgrenze hat andere Funktionsanforderungen (z. B. IDS- und Firewallregeln auf der Internetseite des Umkreisnetzwerks, aber lediglich ACLs zwischen Umkreisnetzwerk und Back-End-Subnetz). Die Entscheidung, welches Gerät (oder wie viele Geräte) zum Einsatz kommt, richtet sich nach dem Szenario und den Sicherheitsanforderungen. Im folgenden Abschnitt werden in den Beispiele 1, 2 und 3 einige Optionen erläutert, die verwendet werden können. Die nativen Netzwerkfeatures von Azure und die über das Partnerökosystem in Azure erhältlichen Geräte bieten unzählige Optionen, um praktisch jedes Szenario abzudecken.

Ein weiterer wichtiger Aspekt bei der Implementierung ist, auf welche Weise das lokale Netzwerk mit Azure verbunden wird: über das virtuelle Azure-Gateway oder über ein virtuelles Netzwerkgerät. Diese Optionen werden in den Beispielen 4, 5 und 6 im folgenden Abschnitt ausführlicher erläutert.

Darüber hinaus kann der Datenverkehr zwischen virtuellen Netzwerken in Azure erforderlich sein. Diese Szenarien werden in der Zukunft hinzugefügt.

Sobald Sie sämtliche der oben aufgeführten Fragen beantwortet haben, können Sie mithilfe des Abschnitts [Schnelleinstieg](#fast-start) ermitteln, welche Beispiele für ein bestimmtes Szenario am besten geeignet sind.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Beispiele: Einrichten von Sicherheitsgrenzen mit virtuellen Azure-Netzwerken
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Beispiel 1: Erstellen eines Umkreisnetzwerks zum Schutz von Anwendungen durch NSGs
[Zurück zum Schnelleinstieg](#fast-start) | [Detaillierte Einrichtungsanweisungen zu diesem Beispiel][Example1]

[![7]][7]

#### <a name="environment-description"></a>Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das folgende Ressourcen enthält:

- Eine einzelne Ressourcengruppe
- Ein virtuelles Netzwerk mit zwei Subnetzen: „FrontEnd“ und „BackEnd“
- Eine Netzwerksicherheitsgruppe, die auf beide Subnetze angewendet wird
- Einen Windows-Server, der einen Anwendungswebserver darstellt („IIS01“)
- Zwei Windows-Server, die Back-End-Anwendungsserver darstellen („AppVM01“, „AppVM02“)
- Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")
- Eine öffentliche IP-Adresse, die mit dem Anwendungswebserver verknüpft ist

Skripts und eine Azure Resource Manager-Vorlage finden Sie in den [detaillierten Einrichtungsanweisungen][Example1].

#### <a name="nsg-description"></a>Beschreibung von NSGs
In diesem Beispiel wird eine Netzwerksicherheitsgruppe (NSG) erstellt und dann mit sechs Regeln geladen.

> [!TIP]
> Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität bestimmt, welche Regeln zuerst ausgewertet werden. Sobald eine Regel auf den Datenverkehr zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung (aus Sicht des Subnetzes) angewendet werden.
>
>

Folgende Regeln werden deklarativ für eingehenden Datenverkehr erstellt:

1. Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2. RDP-Datenverkehr (Port 3389) aus dem Internet zu einem virtuellen Computer wird zugelassen.
3. HTTP-Datenverkehr (Port 80) aus dem Internet zum Webserver (IIS01) wird zugelassen.
4. Jeglicher Datenverkehr (alle Ports) von IIS01 zu AppVM1 wird zugelassen.
5. Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte virtuelle Netzwerk (beide Subnetze) wird abgelehnt.
6. Jeglicher Datenverkehr (alle Ports) vom Front-End-Subnetz zum Back-End-Subnetz wird abgelehnt.

Wenn diese Regeln an die einzelnen Subnetze gebunden sind, gelten beim Eingehen einer HTTP-Anforderung aus dem Internet an den Webserver sowohl Regel 3 (Zulassen) als auch Regel 5 (Verweigern). Aber da Regel 3 eine höhere Priorität hat, wird nur sie angewendet, und Regel 5 findet keine Anwendung. Aus diesem Grund wird die HTTP-Anforderung für den Webserver als zulässig eingestuft. Falls derselbe Datenverkehr versuchen würde, den DNS01-Server zu erreichen, würde Regel 5 (Verweigern) zuerst gelten. Für den Datenverkehr wird die Übergabe an den Server also nicht zugelassen. Regel 6 (Verweigern) verhindert die Kommunikation des Front-End-Subnetzes mit dem Back-End-Subnetz (mit Ausnahme von zulässigem Datenverkehr in den Regeln 1 und 4). Mit diesem Regelsatz wird das Back-End-Netzwerk für den Fall geschützt, dass ein Angreifer die Webanwendung auf dem Front-End gefährdet. Der Angreifer könnte nur eingeschränkt auf das „geschützte“ Back-End-Netzwerk zugreifen (nur auf die Ressourcen, die auf dem Server „AppVM01“ zur Verfügung gestellt werden).

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine ausgehenden Regeln geändert. Um den Datenverkehr in beide Richtungen abzusichern, ist benutzerdefiniertes Routing erforderlich (siehe Beispiel 3).

#### <a name="conclusion"></a>Zusammenfassung
Dieses Beispiel ist eine relativ einfache und direkte Möglichkeit, das Back-End-Subnetz von eingehendem Datenverkehr zu isolieren. Weitere Informationen finden Sie in den [detaillierten Einrichtungsanweisungen][Example1]. In diesen Anweisungen wird Folgendes behandelt:

* Erstellen dieses Umkreisnetzwerks mithilfe von klassischen PowerShell-Skripts.
* Erstellen dieses Umkreisnetzwerks mit einer Azure Resource Manager-Vorlage.
* Ausführliche Beschreibungen für jeden NSG-Befehl.
* Ausführliche Szenarien zum Datenverkehrsfluss, die zeigen, wie der Datenverkehr auf jeder Stufe zugelassen oder abgelehnt wird.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Beispiel 2: Erstellen eines Umkreisnetzwerks zum Schutz von Anwendungen durch eine Firewall und NSGs
[Zurück zum Schnelleinstieg](#fast-start) | [Detaillierte Einrichtungsanweisungen zu diesem Beispiel][Example2]

[![8]][8]

#### <a name="environment-description"></a>Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das folgende Ressourcen enthält:

* Eine einzelne Ressourcengruppe
* Ein virtuelles Netzwerk mit zwei Subnetzen: „FrontEnd“ und „BackEnd“
* Eine Netzwerksicherheitsgruppe, die auf beide Subnetze angewendet wird
* Ein virtuelles Netzwerkgerät, in diesem Fall eine Firewall, das mit dem Front-End-Subnetz verbunden ist
* Einen Windows-Server, der einen Anwendungswebserver darstellt („IIS01“)
* Zwei Windows-Server, die Back-End-Anwendungsserver darstellen („AppVM01“, „AppVM02“)
* Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Skripts und eine Azure Resource Manager-Vorlage finden Sie in den [detaillierten Einrichtungsanweisungen][Example2].

#### <a name="nsg-description"></a>Beschreibung von NSGs
In diesem Beispiel wird eine Netzwerksicherheitsgruppe (NSG) erstellt und dann mit sechs Regeln geladen.

> [!TIP]
> Im Allgemeinen sollten Sie zuerst die spezifischeren Regeln zum Zulassen von Aktionen und danach die allgemeineren Regeln zum Ablehnen von Aktionen erstellen. Die zugewiesene Priorität bestimmt, welche Regeln zuerst ausgewertet werden. Sobald eine Regel auf den Datenverkehr zutrifft, werden keine weiteren Regeln ausgewertet. NSG-Regeln können sowohl in eingehender als auch in ausgehender Richtung (aus Sicht des Subnetzes) angewendet werden.
>
>

Folgende Regeln werden deklarativ für eingehenden Datenverkehr erstellt:

1. Interner DNS-Datenverkehr (Port 53) wird zugelassen.
2. RDP-Datenverkehr (Port 3389) aus dem Internet zu einem virtuellen Computer wird zugelassen.
3. Jeglicher Internetdatenverkehr (alle Ports) zum virtuellen Netzwerkgerät (Firewall) wird zugelassen.
4. Jeglicher Datenverkehr (alle Ports) von IIS01 zu AppVM1 wird zugelassen.
5. Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte virtuelle Netzwerk (beide Subnetze) wird abgelehnt.
6. Jeglicher Datenverkehr (alle Ports) vom Front-End-Subnetz zum Back-End-Subnetz wird abgelehnt.

Wenn diese Regeln an die einzelnen Subnetze gebunden sind, gelten beim Eingehen einer HTTP-Anforderung aus dem Internet an die Firewall sowohl Regel 3 (Zulassen) als auch Regel 5 (Verweigern). Aber da Regel 3 eine höhere Priorität hat, wird nur sie angewendet, und Regel 5 findet keine Anwendung. Aus diesem Grund würde die HTTP-Anforderung für die Firewall als zulässig eingestuft werden. Falls versucht würde, denselben Datenverkehr an den IIS01-Server zu senden, würde Regel 5 (Ablehnen) – obwohl der Server sich im Front-End-Subnetz befindet – zuerst angewendet, der Datenverkehr also nicht an den Server übergeben. Regel 6 (Verweigern) verhindert die Kommunikation des Front-End-Subnetzes mit dem Back-End-Subnetz (mit Ausnahme von zulässigem Datenverkehr in den Regeln 1 und 4). Mit diesem Regelsatz wird das Back-End-Netzwerk für den Fall geschützt, dass ein Angreifer die Webanwendung auf dem Front-End gefährdet. Der Angreifer könnte nur eingeschränkt auf das „geschützte“ Back-End-Netzwerk zugreifen (nur auf die Ressourcen, die auf dem Server „AppVM01“ zur Verfügung gestellt werden).

Es gibt eine Standardregel für ausgehenden Datenverkehr, die das Senden von Datenverkehr an das Internet zulässt. In diesem Beispiel wird ausgehender Datenverkehr zugelassen, und es werden keine ausgehenden Regeln geändert. Um den Datenverkehr in beide Richtungen abzusichern, ist benutzerdefiniertes Routing erforderlich (siehe Beispiel 3).

#### <a name="firewall-rule-description"></a>Beschreibung der Firewallregel
In der Firewall sollten Weiterleitungsregeln erstellt werden. Da in diesem Beispiel nur eingehender Internetdatenverkehr an die Firewall und dann an den Webserver geleitet wird, ist nur eine NAT-Weiterleitungsregel erforderlich.

Die Weiterleitungsregel akzeptiert alle eingehenden Quelladressen, die bei dem Versuch, eine HTTP-Adresse (Port 80 oder 443 für HTTPS) zu erreichen, auf die Firewall stoßen. Sie werden von der lokalen Firewallschnittstelle aus gesendet und an den Webserver mit der IP-Adresse 10.0.1.5 umgeleitet.

#### <a name="conclusion"></a>Zusammenfassung
Dieses Beispiel ist eine relativ einfache Möglichkeit zum Schützen Ihrer Anwendung mit einer Firewall und zum Isolieren des Back-End-Subnetzes vor eingehendem Datenverkehr. Weitere Informationen finden Sie in den [detaillierten Einrichtungsanweisungen][Example2]. In diesen Anweisungen wird Folgendes behandelt:

* Erstellen dieses Umkreisnetzwerks mithilfe von klassischen PowerShell-Skripts.
* Erstellen dieses Beispiels mit einer Azure Resource Manager-Vorlage.
* Ausführliche Beschreibungen für jeden NSG-Befehl und jede Firewallregel.
* Ausführliche Szenarien zum Datenverkehrsfluss, die zeigen, wie der Datenverkehr auf jeder Stufe zugelassen oder abgelehnt wird.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Beispiel 3: Erstellen eines Umkreisnetzwerks zum Schutz von Netzwerken durch eine Firewall, UDR und NSGs
[Zurück zum Schnelleinstieg](#fast-start) | [Detaillierte Einrichtungsanweisungen zu diesem Beispiel][Example3]

[![9]][9]

#### <a name="environment-description"></a>Beschreibung der Umgebung
Dieses Beispiel umfasst ein Abonnement, das folgende Ressourcen enthält:

* Eine einzelne Ressourcengruppe
* Ein virtuelles Netzwerk mit drei Subnetzen: „SecNet“, „FrontEnd“ und „BackEnd“
* Ein virtuelles Netzwerkgerät, in diesem Fall eine Firewall, das mit dem Subnetz „SecNet“ verbunden ist
* Einen Windows-Server, der einen Anwendungswebserver darstellt („IIS01“)
* Zwei Windows-Server, die Back-End-Anwendungsserver darstellen („AppVM01“, „AppVM02“)
* Eine Windows Server-Instanz, die einen DNS-Server darstellt ("DNS01")

Skripts und eine Azure Resource Manager-Vorlage finden Sie in den [detaillierten Einrichtungsanweisungen][Example3].

#### <a name="udr-description"></a>Beschreibung von UDR
Standardmäßig sind folgende Systemrouten definiert:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal ist immer mindestens ein definiertes Adresspräfix des virtuellen Netzwerks für dieses spezifische Netzwerk (d.h., diese Angabe ändert sich je nach virtuellem Netzwerk und je nachdem, wie jedes virtuelle Netzwerk definiert ist). Die anderen Systemrouten sind statisch und standardmäßig wie in der Tabelle angegeben.

In diesem Beispiel werden zwei Routingtabellen erstellt, jeweils eine für das Front-End- und das Back-End-Subnetz. Jede Tabelle enthält statische Routen, die sich für das jeweilige Subnetz eignen. In diesem Beispiel enthält jede Tabelle drei Routen, die den gesamten Datenverkehr (0.0.0.0/0) über die Firewall (nächster Hop = IP-Adresse des virtuellen Geräts) leiten:

1. Datenverkehr im lokalen Subnetz ohne definierten nächsten Hop, sodass der Datenverkehr im lokalen Subnetz die Firewall umgehen kann.
2. Virtueller Netzwerkdatenverkehr mit einem als Firewall definierten nächsten Hop. Dieser nächste Hop setzt die Standardregel außer Kraft, die ein direktes Routing des lokalen virtuellen Netzwerkdatenverkehrs ermöglicht.
3. Der gesamte verbleibende Datenverkehr (0/0) mit einem als Firewall definierten nächsten Hop.

> [!TIP]
> Wenn sich der lokale Subnetzeintrag nicht in der benutzerdefinierten Route befindet, wird die Kommunikation im lokalen Subnetz unterbrochen.
>
> * In unserem Beispiel ist es wichtig, dass VNETLocal auf 10.0.1.0/24 verweist! Andernfalls könnte ein Paket, das vom Webserver (10.0.1.4) an einen anderen lokalen Server (z.B. 10.0.1.25) gesendet werden soll, nicht übertragen werden, da es an das virtuelle Netzwerkgerät gesendet wird. Das virtuelle Netzwerkgerät sendet es an das Subnetz, und das Subnetz sendet es in einer Endlosschleife wieder an das virtuelle Netzwerkgerät.
> * Bei Geräten mit mehreren NICs, die mit getrennten Subnetzen verbunden sind, ist die Wahrscheinlichkeit einer Routingschleife höher. Dies sind häufig traditionelle lokale Geräte.
>
>

Sobald die Routingtabellen erstellt wurden, müssen sie an ihre Subnetze gebunden werden. Die Routingtabelle für das Front-End-Subnetz sieht nach Erstellung und Bindung an das Subnetz wie die folgende Ausgabe aus:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR kann jetzt auf das Gateway-Subnetz angewendet werden, auf dem die ExpressRoute-Verbindung verbunden ist.
>
> Beispiele für das Einrichten Ihres Umkreisnetzwerks mit ExpressRoute- oder Standort-zu-Standort-Netzwerkfunktionen finden Sie in den Beispielen 3 und 4.
>
>

#### <a name="ip-forwarding-description"></a>Beschreibung der IP-Weiterleitung
Die IP-Weiterleitung ist eine begleitende Funktion für das benutzerdefinierte Routing (UDR). Bei der IP-Weiterleitung handelt sich um eine Einstellung in einem virtuellen Gerät, das diesem ermöglicht, nicht speziell an dieses Gerät adressierten Datenverkehr zu empfangen und diesen an das endgültige Ziel weiterzuleiten.

Wenn beispielsweise AppVM01 eine Anforderung an den Server DNS01 sendet, leitet UDR diesen Datenverkehr an die Firewall weiter. Wenn die IP-Weiterleitung aktiviert ist, wird Datenverkehr für das Ziel DNS01 (10.0.2.4) vom Gerät (10.0.0.4) akzeptiert und dann an das endgültige Ziel (10.0.2.4) weitergeleitet. Ist die IP-Weiterleitung in der Firewall nicht aktiviert, wird der Datenverkehr vom Gerät nicht akzeptiert, selbst wenn in der Routingtabelle die Firewall als nächster Hop angegeben ist. Um ein virtuelles Gerät verwenden zu können, ist es wichtig, die IP-Weiterleitung zusammen mit UDR zu aktivieren.

#### <a name="nsg-description"></a>Beschreibung von NSGs
In diesem Beispiel wird eine Netzwerksicherheitsgruppe erstellt und dann mit einer einzigen Regel geladen. Diese Gruppe wird dann nur an das Front-End- und das Back-End-Subnetz (nicht an SecNet) gebunden. Die folgende Regel wird deklarativ erstellt:

* Jeglicher Datenverkehr (alle Ports) aus dem Internet in das gesamte virtuelle Netzwerk (alle Subnetze) wird abgelehnt.

Obwohl Netzwerksicherheitsgruppen in diesem Beispiel verwendet werden, dienen sie hauptsächlich als zweite Stufe zum Schutz gegen manuelle Fehlkonfigurationen. Ziel ist es, den gesamten eingehenden Datenverkehr aus dem Internet an das Front-End- oder das Back-End-Subnetz zu blockieren. Datenverkehr sollte nur über das Subnetz „SecNet“ zur Firewall gelangen (und gegebenenfalls an das Front-End- oder Back-End-Subnetz weitergeleitet werden). Darüber hinaus soll – wenn die UDR-Regeln eingerichtet sind – jeglicher Datenverkehr, der an das Front-End- oder Back-End-Subnetz weitergeleitet wurde, nach außen an die Firewall geleitet werden (dank UDR). Die Firewall erkennt diesen Datenverkehr als asymmetrisch und verwirft den ausgehenden Datenverkehr. Es gibt also drei Sicherheitsstufen zum Schutz der Subnetze:

* Keine öffentlichen IP-Adressen auf Front-End- oder Back-End-NICs.
* NSGs verweigern den Datenverkehr aus dem Internet.
* Die Firewall verwirft/ asymmetrischen Datenverkehr.

Ein interessanter Aspekt in Bezug auf die Netzwerksicherheitsgruppe in diesem Beispiel ist, dass sie nur eine Regel enthält, mit der Internetdatenverkehr im gesamten virtuellen Netzwerk abgelehnt wird, einschließlich des Sicherheitssubnetzes. Da jedoch die Netzwerksicherheitsgruppe nur an das Front-End- und Back-End-Subnetz gebunden ist, wird die Regel bei eingehendem Datenverkehr an das Sicherheitssubnetz nicht angewendet. Daher gelangt der Datenverkehr zum Sicherheitssubnetz.

#### <a name="firewall-rules"></a>Firewallregeln
In der Firewall sollten Weiterleitungsregeln erstellt werden. Da die Firewall sämtlichen eingehenden und ausgehenden Datenverkehr sowie den Datenverkehr innerhalb des virtuellen Netzwerks blockiert oder weiterleitet, müssen zahlreiche Firewallregeln festgelegt werden. Darüber hinaus wird jeglicher eingehender Datenverkehr über die öffentliche IP-Adresse des Sicherheitsdiensts geleitet (über unterschiedliche Ports) und muss von der Firewall verarbeitet werden. Eine bewährte Methode besteht darin, die logischen Datenflüsse in Diagrammen abzubilden, bevor Subnetze und Firewallregeln eingerichtet werden, um spätere Überarbeitungen zu vermeiden. Die folgende Abbildung zeigt eine logische Ansicht der Firewallregeln für dieses Beispiel:

![10]

> [!NOTE]
> Je nach verwendetem virtuellen Netzwerkgerät variieren die Verwaltungsports. In diesem Beispiel wird auf eine Barracuda NextGen-Firewall verwiesen, die die Ports 22, 801 und 807 verwendet. Informationen dazu, über welche Ports das verwendete Gerät genau verwaltet wird, finden Sie in der Dokumentation des Geräteanbieters.
>
>

#### <a name="firewall-rules-description"></a>Beschreibung der Firewallregeln
Im vorherigen logischen Diagramm wird das Sicherheitssubnetz nicht dargestellt, da die Firewall die einzige Ressource in diesem Subnetz ist. Das Diagramm zeigt die Firewallregeln und die Art und Weise, wie sie Datenverkehrsflüsse logisch zulassen oder ablehnen. Das Diagramm zeigt nicht den tatsächlichen Weiterleitungspfad. Die für den RDP-Datenverkehr ausgewählten externen Ports liegen in einem höheren Bereich (8014 – 8026) und wurden für eine gewisse Abstimmung auf die letzten beiden Oktette der lokalen IP-Adresse ausgewählt, um die Lesbarkeit zu vereinfachen (die lokale Serveradresse 10.0.1.4 ist beispielsweise Port 8014 zugeordnet). Es können jedoch auch Ports mit einer höheren Nummer ausgewählt werden, wenn sie keine Konflikte verursachen.

Für dieses Beispiel benötigen wir sieben verschiedene Arten von Regeln:

* Externe Regeln (für eingehenden Datenverkehr):
  1. Firewallverwaltungsregel: Diese Regel zur Anwendungsumleitung ermöglicht die Weiterleitung von Datenverkehr an die Verwaltungsports des virtuellen Netzwerkgeräts.
  2. RDP-Regeln (für jeden Windows-Server): Diese vier Regeln (eine für jeden Server) ermöglichen die Verwaltung der einzelnen Server über RDP. Die vier RDP-Regeln können je nach Funktionsumfang des verwendeten virtuellen Netzwerkgeräts in einer Regel zusammengefasst werden.
  3. Regeln für den Anwendungsdatenverkehr: Hierfür gibt es zwei Regeln, eine für den Webdatenverkehr im Front-End, eine für den Datenverkehr im Back-End (z. B. vom Webserver zur Datenebene). Die Konfiguration dieser Regeln richtet sich nach der Netzwerkarchitektur (der Platzierung Ihrer Server) und dem Datenverkehrsfluss (Richtung des Datenverkehrs und verwendete Ports).
     * Mit der ersten Regel gelangt der tatsächliche Anwendungsdatenverkehr zum Anwendungsserver. Während die anderen Regeln für Sicherheit und Verwaltung sorgen, können externe Benutzer oder Dienste mit Regeln für den Anwendungsdatenverkehr auf die Anwendungen zugreifen. In diesem Beispiel befindet sich ein einziger Webserver an Port 80. Daher leitet eine einzige Firewallanwendungsregel den eingehenden Datenverkehr an die externe IP-Adresse, also die interne IP-Adresse der Webserver, um. Die umgeleitete Datenverkehrssitzung wird per NAT an den internen Server übersetzt.
     * Bei der zweiten Regel handelt es sich um die Back-End-Regel, dank derer der Webserver über jeden Port mit dem Server AppVM01 (nicht jedoch mit dem Server „AppVM02“) kommunizieren kann.
* Interne Regeln (für den Datenverkehr innerhalb des virtuellen Netzwerks)
  1. Regel für ausgehenden Datenverkehr zum Internet: Mit dieser Regel kann Datenverkehr aus allen Netzwerken an die ausgewählten Netzwerke weitergeleitet werden. Diese Regel ist üblicherweise standardmäßig in der Firewall vorhanden, jedoch deaktiviert. Diese Regel sollte für dieses Beispiel aktiviert werden.
  2. DNS-Regel: Mit dieser Regel kann nur DNS-Datenverkehr (Port 53) an den DNS-Server weitergeleitet werden. Für diese Umgebung wird der meiste Datenverkehr vom Front-End an das Back-End blockiert. Diese Regel lässt insbesondere DNS aus allen lokalen Subnetzen zu.
  3. Subnetz-zu-Subnetz-Regel: Mit dieser Regel kann jeder Server im Back-End-Subnetz eine Verbindung mit jedem Server im Front-End-Subnetz herstellen (nicht jedoch umgekehrt).
* Ausfallsicherheitsregel (für Datenverkehr, für den keine der obigen Regeln gilt):
  1. Regel zum Ablehnen jeglichen Datenverkehrs: Diese Ablehnungsregel sollte (hinsichtlich der Priorität) die letzte Regel sein. Sollte ein Datenverkehrsfluss keiner der vorherigen Regeln entsprechen, wird er durch diese Regel verworfen. Diese Regel ist eine Standardregel, die üblicherweise implementiert und aktiviert ist. Normalerweise sind keine Änderungen an dieser Regel erforderlich.

> [!TIP]
> In der zweiten Regel für den Anwendungsdatenverkehr ist zur Vereinfachung dieses Beispiels ein beliebiger Port zulässig. In einem realen Szenario sollten jedoch besonders spezifische Port- und Adressbereiche verwendet werden, um die Angriffsfläche dieser Regel zu reduzieren.
>
>

Nachdem alle zuvor genannten Regeln erstellt wurden, ist es wichtig, die Priorität jeder einzelnen Regel zu prüfen, um sicherzustellen, dass der Datenverkehr wie gewünscht zugelassen oder abgelehnt wird. In diesem Beispiel wurden die Regeln nach Priorität geordnet.

#### <a name="conclusion"></a>Zusammenfassung
Dieses Beispiel ist im Vergleich mit den vorherigen Beispielen eine komplexere, aber vollständige Möglichkeit, das Netzwerk zu schützen und zu isolieren. (In Beispiel 2 wird nur die Anwendung geschützt, und in Beispiel 1 werden nur Subnetze isoliert.) Dieser Entwurf ermöglicht eine Überwachung des Datenverkehrs in beide Richtungen, nicht lediglich des eingehenden Anwendungsservers. Es werden Netzwerksicherheitsrichtlinien für alle Server im Netzwerk durchgesetzt. Je nach verwendeten Geräten kann außerdem der gesamte Datenverkehr überwacht werden, um vollständigen Einblick zu erhalten. Weitere Informationen finden Sie in den [detaillierten Einrichtungsanweisungen][Example3]. In diesen Anweisungen wird Folgendes behandelt:

* Erstellen dieses Beispiel-Umkreisnetzwerks mithilfe von klassischen PowerShell-Skripts.
* Erstellen dieses Beispiels mit einer Azure Resource Manager-Vorlage.
* Ausführliche Beschreibungen für jede benutzerdefinierte Route, jeden NSG-Befehl und jede Firewallregel
* Ausführliche Szenarien zum Datenverkehrsfluss, die zeigen, wie der Datenverkehr auf jeder Stufe zugelassen oder abgelehnt wird.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Beispiel 4: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-VPN über ein virtuelles Gerät
[Zurück zum Schnelleinstieg](#fast-start) | Detaillierte Einrichtungsanweisungen zu diesem Beispiel stehen in Kürze zur Verfügung.

[![11]][11]

#### <a name="environment-description"></a>Beschreibung der Umgebung
Ein Hybridnetzwerk unter Verwendung eines virtuellen Netzwerkgeräts kann einem beliebigen der Umkreisnetzwerk-Typen hinzugefügt werden, die in den Beispielen 1, 2 oder 3 beschrieben werden.

Wie in der obigen Abbildung dargestellt, wird eine VPN-Verbindung über das Internet (Standort-zu-Standort) dazu verwendet, ein lokales Netzwerk über ein virtuelles Netzwerkgerät mit einem virtuellen Azure-Netzwerk zu verbinden.

> [!NOTE]
> Wenn Sie ExpressRoute mit aktivierter Azure Public Peering-Option verwenden, sollte eine statische Route erstellt werden. Diese statische Route sollte den Datenverkehr über die Unternehmensgrenze zum Internet an die VPN-IP-Adresse des virtuellen Netzwerkgeräts leiten und nicht über die ExpressRoute-Verbindung. Durch die Netzwerkadressübersetzung in der Azure Public Peering-Option von ExpressRoute kann die VPN-Verbindung unterbrochen werden.
>
>

Nachdem das VPN eingerichtet ist, wird das virtuelle Netzwerkgerät zum zentralen Hub für alle Netzwerke und Subnetze. Die Weiterleitungsregeln der Firewall legen fest, welcher Datenverkehr zulässig ist, wann eine Netzwerkadressübersetzung durchgeführt wird, welche Daten umgeleitet oder verworfen werden (selbst für Datenflüsse zwischen dem lokalen Netzwerk und Azure).

Der Fluss des Datenverkehrs muss sorgfältig geplant werden, da er durch dieses Entwurfsmuster je nach Anwendungsfall optimiert oder beeinträchtigt werden kann.

Eine Umgebung, die auf Beispiel 3 basiert und der anschließend eine Standort-zu-Standort-VPN-Hybridnetzwerkverbindung hinzugefügt wird, führt zu dem folgenden Entwurf:

[![12]][12]

Der lokale Router – oder ein beliebiges anderes Netzwerkgerät, das mit Ihrem virtuellen Netzwerkgerät für das VPN kompatibel ist – fungiert als VPN-Client. Dieses physische Gerät ist für die Initialisierung und die Aufrechterhaltung der VPN-Verbindung mit Ihrem virtuellen Netzwerkgerät verantwortlich.

Aus logischer Sicht des virtuellen Netzwerkgeräts sind die Netzwerke vier separate „Sicherheitszonen“, wobei die Regeln auf dem virtuellen Netzwerkgerät festlegen, wie der Datenverkehr zwischen diesen Zonen abgewickelt wird:

![13]

#### <a name="conclusion"></a>Zusammenfassung
Durch das Hinzufügen einer Standort-zu-Standort-VPN-Hybridnetzwerkverbindung zu einem virtuellen Azure-Netzwerk kann das lokale Netzwerk auf sichere Weise auf Azure erweitert werden. Durch die Verwendung einer VPN-Verbindung wird Ihr Datenverkehr verschlüsselt und über das Internet geroutet. Das virtuelle Netzwerkgerät in diesem Beispiel bietet eine zentrale Möglichkeit zum Durchsetzen und Verwalten der Sicherheitsrichtlinie. Weitere Informationen finden Sie (demnächst) in den detaillierten Einrichtungsanweisungen. In diesen Anweisungen wird Folgendes behandelt:

* Erstellen dieses Beispiel-Umkreisnetzwerks mithilfe von PowerShell-Skripts.
* Erstellen dieses Beispiels mit einer Azure Resource Manager-Vorlage.
* Detaillierte Szenarien zum Datenfluss in diesem Entwurf.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Beispiel 5: Hinzufügen einer Hybridverbindung mit einem Site-to-Site-, Azure-VPN-Gateway
[Zurück zum Schnelleinstieg](#fast-start) | Detaillierte Einrichtungsanweisungen zu diesem Beispiel stehen in Kürze zur Verfügung.

[![14]][14]

#### <a name="environment-description"></a>Beschreibung der Umgebung
Eine Hybridnetzwerkverbindung über ein Azure VPN Gateway kann einem beliebigen der Umkreisnetzwerk-Typen hinzugefügt werden, die in den Beispielen 1 und 2 beschrieben werden.

Wie in der Abbildung oben dargestellt, wird eine VPN-Verbindung über das Internet (Standort-zu-Standort) dazu verwendet, ein lokales Netzwerk über ein Azure VPN Gateway mit einem virtuellen Azure-Netzwerk zu verbinden.

> [!NOTE]
> Wenn Sie ExpressRoute mit aktivierter Azure Public Peering-Option verwenden, sollte eine statische Route erstellt werden. Diese statische Route sollte den Datenverkehr über die Unternehmensgrenze zum Internet an die VPN-IP-Adresse des virtuellen Netzwerkgeräts leiten und nicht über das ExpressRoute-WAN. Durch die Netzwerkadressübersetzung in der Azure Public Peering-Option von ExpressRoute kann die VPN-Verbindung unterbrochen werden.
>
>

Die folgende Abbildung zeigt die beiden Netzwerkrandbereiche in diesem Beispiel. Im ersten Randbereich werden Datenverkehrsflüsse für Netzwerke innerhalb von Azure und zwischen Azure und dem Internet über das virtuelle Netzwerkgerät und NSGs gesteuert. Der zweite Randbereich ist das Azure-VPN-Gateway: ein separater und isolierter Netzwerkrandbereich zwischen dem lokalen und dem Azure-Netzwerk.

Der Fluss des Datenverkehrs muss sorgfältig geplant werden, da er durch dieses Entwurfsmuster je nach Anwendungsfall optimiert oder beeinträchtigt werden kann.

Eine Umgebung, die auf Beispiel 1 basiert und der anschließend eine Standort-zu-Standort-VPN-Hybridnetzwerkverbindung hinzugefügt wird, führt zu dem folgenden Entwurf:

[![15]][15]

#### <a name="conclusion"></a>Zusammenfassung
Durch das Hinzufügen einer Standort-zu-Standort-VPN-Hybridnetzwerkverbindung zu einem virtuellen Azure-Netzwerk kann das lokale Netzwerk auf sichere Weise auf Azure erweitert werden. Durch Verwendung des nativen Azure VPN Gateways wird Ihr Datenverkehr mit IPSec verschlüsselt und über das Internet geroutet. Durch Einsatz des Azure-VPN-Gateways können außerdem die Kosten gesenkt werden (da im Gegensatz zu virtuellen Netzwerkgeräten von Drittanbietern keine zusätzlichen Lizenzierungskosten anfallen). Besonders wirtschaftlich ist diese Option in Beispiel 1, in dem kein virtuelles Netzwerkgerät verwendet wird. Weitere Informationen finden Sie (demnächst) in den detaillierten Einrichtungsanweisungen. In diesen Anweisungen wird Folgendes behandelt:

* Erstellen dieses Beispiel-Umkreisnetzwerks mithilfe von PowerShell-Skripts.
* Erstellen dieses Beispiels mit einer Azure Resource Manager-Vorlage.
* Detaillierte Szenarien zum Datenfluss in diesem Entwurf.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Beispiel 6: Hinzufügen einer Hybridverbindung mit ExpressRoute
[Zurück zum Schnelleinstieg](#fast-start) | Detaillierte Einrichtungsanweisungen zu diesem Beispiel stehen in Kürze zur Verfügung.

[![16]][16]

#### <a name="environment-description"></a>Beschreibung der Umgebung
Ein Hybridnetzwerk mit einer privaten ExpressRoute-Peeringverbindung kann einem beliebigen der Umkreisnetzwerk-Typen hinzugefügt werden, die in Beispiel 1 oder 2 beschrieben werden.

Wie in der Abbildung oben gezeigt wird, bietet das private ExpressRoute-Peering eine direkte Verbindung zwischen Ihrem lokalen Netzwerk und dem virtuellen Azure-Netzwerk. Der Datenverkehr passiert nur das Dienstanbieternetzwerk und das Microsoft Azure-Netzwerk, ohne jeglichen Berührungspunkt mit dem Internet.

> [!TIP]
> Mithilfe von ExpressRoute gelangt der Netzwerkdatenverkehr des Unternehmens nicht in das Internet. Diese Implementierung bietet höhere Sicherheit, eine erheblich gesteigerte Leistung und ermöglicht das Bereitstellen von SLAs durch Ihren ExpressRoute-Anbieter. Das Azure-Gateway kann mit ExpressRoute eine Leistung von bis zu 10 GBit/s erzielen, während das Azure-Gateway mit Site-to-Site-VPNs einen maximalen Durchsatz von 200 MBit/s erreicht.
>
>

Wie im folgenden Diagramm gezeigt, weist die Umgebung mit dieser Option zwei Netzwerkrandbereiche auf. Das virtuelle Netzwerkgerät und NSGs steuern den Fluss des Datenverkehrs für interne Azure-Netzwerke und zwischen Azure und dem Internet. Das Gateway ist hingegen eine getrennte und isolierte Netzwerkgrenze zwischen dem lokalen Netzwerk und Azure.

Der Fluss des Datenverkehrs muss sorgfältig geplant werden, da er durch dieses Entwurfsmuster je nach Anwendungsfall optimiert oder beeinträchtigt werden kann.

Eine Umgebung, die auf Beispiel 1 basiert und der anschließend eine ExpressRoute-Hybridnetzwerkverbindung hinzugefügt wird, führt zu dem folgenden Entwurf:

[![17]][17]

#### <a name="conclusion"></a>Zusammenfassung
Durch das Hinzufügen einer privaten ExpressRoute-Peering-Netzwerkverbindung kann das lokale Netzwerk auf sichere Weise auf Azure erweitert werden und bietet gleichzeitig niedrige Latenz und hohe Leistung. Durch Einsatz des nativen Azure-Gateways, wie in diesem Beispiel, können außerdem die Kosten gesenkt werden (da im Gegensatz zu virtuellen Netzwerkgeräten von Drittanbietern keine zusätzlichen Lizenzierungskosten anfallen). Weitere Informationen finden Sie (demnächst) in den detaillierten Einrichtungsanweisungen. In diesen Anweisungen wird Folgendes behandelt:

* Erstellen dieses Beispiel-Umkreisnetzwerks mithilfe von PowerShell-Skripts.
* Erstellen dieses Beispiels mit einer Azure Resource Manager-Vorlage.
* Detaillierte Szenarien zum Datenfluss in diesem Entwurf.

## <a name="references"></a>Referenzen
### <a name="helpful-websites-and-documentation"></a>Hilfreiche Websites und Dokumentation
* Implementierung mit Azure Resource Manager:
* Zugriff auf Azure mit PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Dokumentation zu virtuellen Netzwerken: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Dokumentation zu Netzwerksicherheitsgruppen: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Dokumentation zum benutzerdefinierten Routing (UDR): [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Virtuelle Azure-Gateways: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Site-to-Site-VPNs: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* ExpressRoute-Dokumentation (lesen Sie unbedingt die Abschnitte „Erste Schritte“ und „Anleitung“): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Flussdiagramm zu den Sicherheitsoptionen"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Sicherheitsfeatures von Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Eine DMZ in einem Unternehmensnetzwerk"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Sicherheitsarchitektur von Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Ein DMZ in einem virtuellen Azure-Netzwerk"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Hybridnetzwerk mit drei Sicherheitsgrenzen"
[7]: ./media/best-practices-network-security/example1design.png "Eingehende DMZ mit NSG"
[8]: ./media/best-practices-network-security/example2design.png "Eingehende DMZ mit NVA und NSG"
[9]: ./media/best-practices-network-security/example3design.png "Bidirektionale DMZ mit virtuellem Netzwerkgerät, Netzwerksicherheitsgruppe und benutzerdefiniertem Routing"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Logische Ansicht der Firewallregeln"
[11]: ./media/best-practices-network-security/example3designoptions.png "DMZ mit einem über ein virtuelles Netzwerkgerät verbundenen Hybridnetzwerk"
[12]: ./media/best-practices-network-security/example4designs2s.png "DMZ mit einem über ein Site-to-Site-VPN verbundenen virtuellen Netzwerkgerät"
[13]: ./media/best-practices-network-security/example4networklogical.png "Logisches Netzwerk aus Sicht des virtuellen Netzwerkgeräts"
[14]: ./media/best-practices-network-security/example5designoptions.png "DMZ mit einem über ein Site-to-Site-Hybridnetzwerk verbundenen Azure-Gateway"
[15]: ./media/best-practices-network-security/example5designs2s.png "DMZ mit Azure-Gateway und Verwendung eines Site-to-Site-VPN"
[16]: ./media/best-practices-network-security/example6designoptions.png "DMZ mit einem über ExpressRoute verbundenen Azure-Gateway"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ mit Azure-Gateway und Verwendung einer ExpressRoute-Verbindung"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
