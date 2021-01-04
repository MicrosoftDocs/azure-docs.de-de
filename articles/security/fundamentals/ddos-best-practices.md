---
title: Entwerfen resilienter Lösungen mit Azure DDoS Protection
description: Erfahren Sie, wie Sie auf der Grundlage von Protokollierungsdaten umfassende Erkenntnisse über Ihre Anwendung gewinnen.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: e298cb0d1a2c510a096f8ead03f8af7e39c206a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498930"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection: Entwerfen robuster Lösungen

Dieser Artikel richtet sich an IT-Entscheidungsträger und Sicherheitspersonal. Sie sollten mit Azure, Netzwerk und Sicherheit vertraut sein.
DDoS ist ein Angriffstyp, der versucht, Anwendungsressourcen zu erschöpfen. Die Verfügbarkeit der Anwendung und ihre Fähigkeit, legitime Anforderungen zu verarbeiten, sollen beeinträchtigt werden. Die Angriffe werden anspruchsvoller, ihr Ausmaß größer und ihre Auswirkungen gravierender. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden. Konzeptionen für Resilienz gegenüber Distributed Denial of Service (DDoS) setzen Planungen und Entwürfe für eine Vielzahl von Fehlerzuständen voraus. Azure bietet dauerhaften Schutz gegen DDoS-Angriffe. Dieser Schutz ist standardmäßig und ohne zusätzliche Kosten in der Azure-Plattform integriert.

Zusätzlich zum grundlegenden Schutz vor DDoS-Angriffen in der Plattform bietet [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) weitere DDoS-Entschärfungsfunktionen zum Schutz gegen Netzwerkangriffe. Es wird automatisch optimiert, um Ihre spezifischen Azure-Ressourcen zu schützen. Der Schutz lässt sich einfach während der Erstellung des neuen virtuellen Netzwerks aktivieren. Dies ist auch nach der Erstellung möglich und erfordert keine Änderungen der Anwendung oder Ressourcen.

![Die Rolle von Azure DDoS Protection beim Schutz von Kunden und virtuellen Netzwerken vor Angreifern](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Grundlegende bewährte Methoden

Die folgenden Abschnitte enthalten eine ausführliche Anleitung zum Erstellen DDoS-resilienter Dienste in Azure.

### <a name="design-for-security"></a>Sicherheitsorientiertes Design

Stellen Sie sicher, dass die Sicherheit im gesamten Lebenszyklus einer Anwendung Priorität hat – von Entwurf und Implementierung bis hin zu Bereitstellung und Betrieb. Anwendungen können Fehler enthalten, die einer relativ geringen Anzahl Anforderungen die übermäßige Verwendung von Ressourcen erlauben. Dies führt zu einem Dienstausfall. 

Um einen in Microsoft Azure ausgeführten Dienst zu schützen, sollten Sie Ihre Anwendungsarchitektur verstehen und sich auf die [five pillars of software quality (Fünf Säulen der Softwarequalität)](/azure/architecture/guide/pillars) konzentrieren.
Sie sollten Folgendes kennen: das typische Datenverkehrsvolumen, das Modell der Konnektivität zwischen der Anwendung und anderen Anwendungen, sowie die Dienstendpunkte, die dem öffentlichen Internet verfügbar gemacht werden.

Es ist von größter Wichtigkeit sicherzustellen, dass eine Anwendung stabil genug ist, um mit einem Denial-of-Service-Angriff fertig zu werden. Sicherheit und Datenschutz sind direkt in der Azure-Plattform integriert, beginnend mit [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Die SDL spricht Sicherheit in jeder Entwicklungsphase an und sorgt dafür, dass Azure kontinuierlich aktualisiert wird, um noch sicherer zu sein.

### <a name="design-for-scalability"></a>Skalierbarkeitsorientiertes Design

Die Skalierbarkeit zeigt, wie gut ein System eine höhere Last verarbeiten kann. Entwerfen der Anwendungen für eine [horizontale Skalierung](/azure/architecture/guide/design-principles/scale-out), um die Anforderungen einer verstärkten Auslastung zu erfüllen – insbesondere im Falle eines DDoS-Angriffs. Wenn Ihre Anwendung von einer einzelnen Instanz eines Diensts abhängig ist, entsteht dadurch ein Single Point of Failure. Durch Bereitstellen mehrerer Instanzen wird Ihr System stabiler und besser skalierbar.

Wählen Sie für [Azure App Service](../../app-service/overview.md) einen [App Service-Plan](../../app-service/overview-hosting-plans.md) aus, der mehrere Instanzen bietet. Konfigurieren Sie für Azure Cloud Services alle Rollen so, dass sie [mehrere Instanzen](../../cloud-services/cloud-services-choose-me.md) verwenden. Stellen Sie für [Azure Virtual Machines](../../virtual-machines/index.yml) (VMs) sicher, dass die VM-Architektur mehr als eine VM enthält und dass jede VM zu einer [Verfügbarkeitsgruppe](../../virtual-machines/windows/tutorial-availability-sets.md) gehört. Sie sollten [Virtual Machine Scale Sets](../../virtual-machine-scale-sets/overview.md) für Funktionen zur automatischen Skalierung verwenden.

### <a name="defense-in-depth"></a>Tiefgehende Verteidigung

Hinter der tiefgehenden Verteidigung steht die Idee, dem Risiko mit verschiedenen Verteidigungsstrategien zu begegnen. Abwehrmaßnahmen in die Schichten einer Anwendung zu integrieren reduziert die Wahrscheinlichkeit eines erfolgreichen Angriffs. Sie sollten sichere Entwürfe für Ihre Anwendungen mithilfe der integrierten Funktionen der Azure-Plattform implementieren.

Das Risiko von Angriffen steigt z.B. mit der Größe (*Oberflächenbereich*) der Anwendung. Sie können den Oberflächenbereich durch Verwendung einer Genehmigungsliste verringern, um den verfügbar gemachten IP-Adressraum und die Überwachungsports zu schließen, die im Lastenausgleichsmodul ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) und [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)) nicht mehr benötigt werden. Durch [Netzwerksicherheitsgruppen (NSGs)](../../virtual-network/network-security-groups-overview.md) kann die angreifbare Oberfläche ebenfalls reduziert werden.
Sie können mit [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) und [Anwendungssicherheitsgruppen](../../virtual-network/network-security-groups-overview.md#application-security-groups) das Erstellen von Sicherheitsregeln weniger komplex machen und Netzwerksicherheit als natürliche Erweiterung der Struktur einer Anwendung konfigurieren.

Sie sollten Azure-Dienste nach Möglichkeit in einem [virtuellen Netzwerk](../../virtual-network/virtual-networks-overview.md) bereitstellen. Mit dieser Methode können Dienstressourcen über private IP-Adressen kommunizieren. Standardmäßig werden für Datenverkehr von Azure-Diensten aus einem virtuellen Netzwerk öffentliche IP-Adressen als Quell-IP-Adressen verwendet. Bei Verwendung von [Dienstendpunkten](../../virtual-network/virtual-network-service-endpoints-overview.md) wechselt der Dienstdatenverkehr zu privaten Adressen im virtuellen Netzwerk als Quell-IP-Adressen, wenn aus einem virtuellen Netzwerk auf den Azure-Dienst zugegriffen wird.

Häufig werden lokale Ressourcen von Kunden zusammen mit ihren Azure-Ressourcen angegriffen. Wenn Sie die Verbindung einer lokalen Umgebung mit Azure herstellen, sollten Sie dem öffentlichen Internet lokale Ressourcen so wenig wie möglich verfügbar machen. Sie können die Skalierungsfunktionen und erweiterten Funktionen zum DDoS-Schutz von Azure durch Bereitstellung Ihrer gut bekannten öffentlichen Entitäten in Azure verwenden. Da diese öffentlich zugänglichen Entitäten häufig ein Ziel von DDoS-Angriffen sind, reduziert ihre Unterbringung in Azure die Auswirkungen auf Ihre lokalen Ressourcen.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-Angebote für DDoS-Schutz

Azure bietet zwei DDoS-Dienstangebote, die vor Netzwerkangriffen (Schicht 3 und 4) schützen: DDoS Protection Basic und DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Basisschutz ist standardmäßig und ohne zusätzliche Kosten in der Azure-Plattform integriert. Die Skalierung und Kapazität des global bereitgestellten Azure-Netzwerks bietet Schutz vor häufigen Vermittlungsschichtangriffen durch Always On-Datenverkehrsüberwachung und Risikominderung in Echtzeit. DDoS Protection Basic erfordert keine Konfiguration oder Anwendungsänderungen durch den Benutzer. Alle Azure-Dienste einschließlich der PaaS-Dienste wie Azure DNS werden von DDoS Protection Basic geschützt.

![Karte mit Darstellung des Azure-Netzwerks und dem Text „Global DDoS mitigation presence (Globale Präsenz von DDoS-Entschärfung)“ und „Leading DDoS mitigation capacity (Führende DDoS-Entschärfungsfunktionen)“](./media/ddos-best-practices/image3.png)

Basic DDoS Protection in Azure besteht sowohl aus Software- als auch Hardwarekomponenten. Eine Softwaresteuerungsebene entscheidet, wann wo welche Art von Datenverkehr über Hardwareappliances gelenkt werden soll, die Angriffsdatenverkehr analysieren und entfernen. Die Steuerungsebene trifft diese Entscheidung basierend auf der infrastrukturweit gültigen *Richtlinie* für DDoS Protection. Diese Richtlinie wird statisch festgelegt und für alle Azure-Kunden universell angewendet.

Die Richtlinie für DDoS Protection gibt z.B. an, bei welchem Datenverkehrsvolumen der Schutz *ausgelöst* werden soll. (Das bedeutet, dass der Datenverkehr des Mandanten über Bereinigungsanwendungen geleitet werden sollte.) Die Richtlinie gibt dann an, wie die Bereinigungsanwendungen den Angriff *entschärfen* sollen.

Das Ziel des Diensts Azure DDoS Protection Basic ist der Schutz der Infrastruktur und der Azure-Plattform. Er verringert den Datenverkehr, wenn er eine Rate überschreitet, die so signifikant ist, dass sie sich in einer mehrinstanzenfähigen Umgebung auf mehrere Kunden auswirken kann. Er stellt keine Warnungen oder individuell für einen Kunden angepasste Richtlinien bereit.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

Der Dienst Protection Standard bietet verbesserte DDoS-Entschärfungsfeatures. Er wird automatisch optimiert, um Ihre spezifischen Azure-Ressourcen in einem virtuellen Netzwerk zu schützen. Der Schutz kann einfach in jedem neuen oder vorhandenen virtuellen Netzwerk aktiviert werden und erfordert keine Änderung von Anwendung oder Ressource. Er hat mehrere Vorteile gegenüber dem Basisdienst, z.B. Protokollierung, Warnungen und Telemetrie. Die folgenden Abschnitte zeigen die Hauptfeatures des Diensts Azure DDoS Protection Standard.

#### <a name="adaptive-real-time-tuning"></a>Adaptive Echtzeitoptimierung

Der Azure DDoS Protection Basic-Dienst schützt Kunden und verhindert Auswirkungen auf andere Kunden. Wenn z.B. ein Dienst für ein typisches Volumen legitimen eingehenden Datenverkehrs bereitgestellt wird, das kleiner ist als die *Triggerrate* der infrastrukturweiten DDoS Protection-Richtlinie, könnte ein DDoS-Angriff auf die Ressourcen dieses Kunden unbemerkt bleiben. Allgemeiner gesagt: Die komplexe Natur aktueller Angriffe (z.B. Multi-Vektor-DDoS), sowie das anwendungsspezifische Verhalten von Mandanten erfordern kundenspezifische, angepasste Schutzrichtlinien. Der Dienst führt diese Anpassung mithilfe von zwei Erkenntnissen durch:

- Automatische Ermittlung der Datenverkehrsmuster pro Kunde (pro IP) für Schichten 3 und 4

- Minimieren falsch positiver Ergebnisse – davon ausgehend, dass die Skalierung in Azure das Absorbieren signifikanter Datenverkehrsmengen erlaubt

![Diagramm der Arbeitsweise von DDoS Protection Standard mit Markierung der „Policy Generation“ (Generierung der Richtlinie)](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection: Telemetriedaten, Überwachung und Warnungen

DDoS Protection Standard stellt während der Dauer eines DDoS-Angriffs umfangreiche Telemetriedaten über [Azure Monitor](../../azure-monitor/overview.md) zur Verfügung. Sie können Warnungen für alle Azure Monitor-Metriken konfigurieren, die DDoS Protection verwendet. Sie können die Protokollierung mit Splunk (Azure Event Hubs), Azure Monitor-Protokolle und Azure Storage für die erweiterte Analyse über die Schnittstelle für die Azure Monitor-Diagnose integrieren.

##### <a name="ddos-mitigation-policies"></a>DDoS-Entschärfungsrichtlinien

Wählen Sie im Azure-Portal **Monitor** > **Metrik** aus. Wählen Sie im Bereich **Metrik** die Ressourcengruppe, den Ressourcentyp **Öffentliche IP-Adresse** und Ihre öffentliche Azure-IP-Adresse aus. DDoS-Metriken werden im Bereich **Verfügbare Metriken** angezeigt.

DDoS Protection Standard wendet drei automatisch optimierte Entschärfungsrichtlinien (TCP-SYN, TCP und UDP) für jede öffentliche IP-Adresse der geschützten Ressource in dem virtuellen Netzwerk an, für das DDoS aktiviert ist. Sie können die Schwellenwerte der Richtlinien anzeigen, indem Sie die Metrik **Inbound packets to trigger DDoS mitigation** (Eingehende Pakete zum Auslösen einer DDoS-Entschärfung) auswählen.

![Verfügbare Metriken und Metrikdiagramm](./media/ddos-best-practices/image7.png)

Die Schwellenwerte der Richtlinien werden automatisch über unsere Profilerstellung für Netzwerkdatenverkehr konfiguriert, die auf Machine Learning basiert. Die DDoS-Entschärfung wird nur dann für eine IP-Adresse durchgeführt, die einem Angriff ausgesetzt ist, wenn der Richtlinienschwellenwert überschritten wird.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrik für eine IP-Adresse während eines DDoS-Angriffs

Wenn die öffentliche IP-Adresse einem Angriff ausgesetzt wird, ändert sich der Wert der Metrik **Unter DDoS-Angriff oder nicht** automatisch in 1, da DDoS Protection eine Entschärfung des Angriffsdatenverkehrs durchführt.

![Metrik und Diagramm „Unter DDoS-Angriff oder nicht“](./media/ddos-best-practices/image8.png)

Es ist empfehlenswert, eine Warnung für diese Metrik zu konfigurieren. Dadurch werden Sie benachrichtigt, wenn eine aktive DDoS-Entschärfung für Ihre öffentliche IP-Adresse durchgeführt wird.

Weitere Informationen finden Sie unter [Verwalten des Azure DDoS-Standardschutzs mithilfe des Azure-Portals](../../ddos-protection/manage-ddos-protection.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Web Application Firewall für Ressourcenangriffe

Speziell für Ressourcenangriffe auf die Anwendungsschicht sollten Sie Web Application Firewall (WAF) konfigurieren, um Webanwendungen zu schützen. WAF untersucht eingehenden Webdatenverkehr, um SQL-Einfügungen, Cross-Site Scripting, DDoS und andere Schicht 7-Angriffe zu blockieren. Azure stellt [WAF als Feature von Application Gateway](../../web-application-firewall/ag/ag-overview.md) bereit, das zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet. Über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) sind andere WAF-Angebote bei Azure-Partnern erhältlich, die möglicherweise für Ihre Anforderungen besser geeignet sind.

Auch Web Application Firewalls sind anfällig für volumetrische und Zustandserschöpfungsangriffe. Sie sollten zum Schutz vor volumetrischen Angriffen und Protokollangriffen unbedingt DDoS Protection Standard auf dem virtuellen WAF-Netzwerk aktivieren. Weitere Informationen finden Sie im Abschnitt [DDoS Protection – Referenzarchitekturen](#ddos-protection-reference-architectures).

### <a name="protection-planning"></a>Planen des Schutzes

Planung und Vorbereitung sind entscheidend, um zu verstehen, wie ein System sich während eines DDoS-Angriffs verhalten wird. Zu diesen Schritten gehört auch das Erstellen eines Plans für Incident Management.

Sie sollten sicherstellen, dass DDoS Protection Standard für das virtuelle Netzwerk der Endpunkte aktiviert ist, die dem Internet zugewandt sind. Das Konfigurieren von DDoS-Warnungen hilft Ihnen, ständig ein wachsames Auge auf mögliche Angriffe auf Ihre Infrastruktur zu haben. 

Überwachen Sie Ihre Anwendungen separat. Es ist wichtig, das normale Verhalten einer Anwendung zu verstehen. Wenn die Anwendung sich während eines DDoS-Angriffs nicht erwartungsgemäß verhält, müssen Maßnahmen ergriffen werden.

#### <a name="testing-through-simulations"></a>Testen per Simulation

Sie sollten Ihre Annahmen über die Reaktionen Ihrer Dienste auf einen Angriff unbedingt mithilfe regelmäßiger Simulationen testen. Überprüfen Sie während der Tests, ob Ihre Dienste oder Anwendungen weiterhin wie erwartet funktionieren und der Benutzer keine Unterbrechung merkt. Identifizieren Sie Lücken sowohl vom technologischen als auch Prozessstandpunkt, und beziehen Sie sie in die DDoS-Reaktionsstrategie ein. Generell sollten Sie derartige Tests in Stagingumgebungen oder außerhalb der Spitzenzeiten ausführen, um Auswirkungen auf die Produktionsumgebung zu minimieren.

Wir haben gemeinsam mit [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) eine Schnittstelle erstellt. Hier können Azure-Kunden zu Simulationszwecken Datenverkehr für öffentliche Endpunkte generieren, für die DDoS Protection aktiviert ist. Sie können [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) für Folgendes verwenden:

- Zur Überprüfung, wie Azure DDoS Protection Ihre Azure-Ressourcen vor DDoS-Angriffen schützt

- Zur Optimierung Ihres Reaktionsprozesses auf Incidents während DDoS-Angriffen

- Zur Dokumentation der DDoS-Konformität

- Zur Schulung Ihrer Netzwerksicherheitsteams

Cybersecurity erfordert ständige Innovationen bei der Sicherheit. Azure DDoS Standard Protection ist ein Angebot nach neuestem Entwicklungsstand mit einer effektiven Lösung zur Entschärfung zunehmend komplexerer DDoS-Angriffe.

## <a name="components-of-a-ddos-response-strategy"></a>Komponenten einer DDoS-Reaktionsstrategie

DDoS-Angriffe, die Azure-Ressourcen als Ziel haben, erfordern aus Sicht des Benutzers normalerweise nur minimales Eingreifen. Dennoch können Sie durch Integrieren der DDoS-Entschärfung im Rahmen der Reaktionsstrategie auf Incidents minimale Auswirkungen auf die Geschäftskontinuität sicherstellen.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft bietet ein umfassendes Threat Intelligence-Netzwerk (Informationen zu Bedrohungen). Dieses Netzwerk verwendet die gesammelten Kenntnisse einer großen Sicherheitscommunity, die Onlinedienste und Microsoft-Partner sowie Beziehungen in der Internet-Sicherheitscommunity unterstützt. 

Als wichtiger Anbieter von Infrastruktur empfängt Microsoft frühzeitig Warnungen zu Bedrohungen. Microsoft sammelt Threat Intelligence (Informationen zu Bedrohungen) aus Onlinediensten und dem weltweiten Kundenstamm. Die gesamte Threat Intelligence von Microsoft wird wieder in die Azure DDoS Protection-Produkte integriert.

Außerdem führt die Microsoft Digital Crimes Unit (DCU) offensive Strategien gegen Botnets durch. Botnets sind eine häufige Quelle für Befehle und Steuerung von DDoS-Angriffen.

### <a name="risk-evaluation-of-your-azure-resources"></a>Risikobewertung für Ihre Azure-Ressourcen

Es ist zwingend notwendig, dass Sie durchgehend Ihr Risiko für DDoS-Angriffe einschätzen können. Stellen Sie sich in regelmäßigen Abständen die folgenden Fragen:

- Welche neuen, öffentlich verfügbaren Azure-Ressourcen benötigen Schutz?

- Gibt es Single Points of Failure im Dienst? 

- Wie können Dienste isoliert werden, um die Auswirkungen eines Angriffs einzuschränken, aber die Dienste legitimen Kunden weiterhin verfügbar zu machen?

- Gibt es virtuelle Netzwerke, in denen DDoS Protection Standard aktiviert werden sollte, aber noch nicht aktiviert ist? 

- Sind meine Dienste mehrere Regionen übergreifend aktiv / aktiv mit Failover?

### <a name="customer-ddos-response-team"></a>Kunden-DDoS-Reaktionsteam

Das Erstellen eines DDoS-Reaktionsteams ist ein wichtiger Schritt, um eine effiziente und schnelle Reaktion auf einen Angriff zu gewährleisten. Geben Sie verschiedene Kontakte in Ihrer Organisation an, die Planung und Ausführung leiten. Dieses DDoS-Reaktionsteam sollte sehr gut mit dem Azure DDoS Protection Standard-Dienst vertraut sein. Stellen Sie sicher, dass Ihr Team einen Angriff identifizieren und entschärfen kann, indem es in Koordination mit internen und externen Kunden und dem Microsoft-Supportteam arbeitet.

Ihr DDoS-Reaktionsteam sollte Simulationsübungen als normalen Bestandteil Ihrer Planung zur Dienstverfügbarkeit und Kontinuität durchführen. Diese Übungen sollten auch Skalierungstests umfassen.

### <a name="alerts-during-an-attack"></a>Warnungen während eines Angriffs

Azure DDoS Protection Standard identifiziert und entschärft DDoS-Angriffe ohne jegliches Eingreifen des Benutzers. Um benachrichtigt zu werden, wenn eine Entschärfung für eine geschützte öffentliche IP-Adresse aktiv ist, können Sie in der Metrik **einem DDoS Angriff ausgesetzt oder nicht**  eine Warnung konfigurieren. Weitere Informationen finden Sie unter [Manage Azure DDoS Protection Standard using the Azure portal (Verwalten von Azure DDoS Protection Standard über das Azure-Portal)](../../ddos-protection/manage-ddos-protection.md). Sie können außerdem Warnungen für andere DDoS-Metriken erstellen, um das Ausmaß des Angriffs, den gelöschten Datenverkehr usw. zu verstehen.

#### <a name="when-to-contact-microsoft-support"></a>Wann Sie den Microsoft-Support kontaktieren sollten

- Wenn Sie während eines DDoS-Angriffs feststellen, dass die Leistung der geschützten Ressource erheblich beeinträchtig wird oder die Ressource nicht verfügbar ist.

- Wenn Sie vermuten, dass der DDoS Protection-Dienst sich nicht wie erwartet verhält. 

  Der DDoS Protection-Dienst startet die Entschärfung nur, wenn der Wert der Metrik **Policy to trigger DDoS mitigation (TCP/TCP SYN/UDP)** (Richtlinie zum Auslösen der DDoS-Entschärfung (TCP/TCP-SYN/UDP)) niedriger ist als der Datenverkehr, der von der öffentlichen IP-Adresse (hier eine geschützte Ressource) empfangen wird.

- Wenn Sie ein virales Ereignis planen, dass zu einem beträchtlichen Anstieg Ihres Netzwerkdatenverkehrs führen wird.

- Wenn jemand einen DDoS-Angriff auf Ihre Ressourcen angedroht hat.

- Wenn Sie eine IP-Adresse oder einen IP-Adressbereich aus Azure DDoS Protection Standard zulassen müssen. Ein häufiges Szenario besteht darin, die IP zuzulassen, wenn der Datenverkehr von einer externen Cloud-WAF nach Azure geleitet wird. 

Erstellen Sie für Fälle mit gravierenden geschäftlichen Auswirkungen ein Schweregrad-A-[Supportticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>Schritte nach dem Angriff

Nach einem Angriff sollten Sie immer Bilanz ziehen und ggf. die DDoS-Reaktionsstrategie anpassen. Ziehen Sie Folgendes in Betracht:

- Traten aufgrund mangelnder skalierbarer Architektur Unterbrechungen des Dienstes bzw. Unterbrechungen für den Benutzer auf?

- Welche Anwendungen oder Dienste waren am meisten betroffen?

- Wie effektiv war die DDoS-Reaktionsstrategie, und wie kann sie verbessert werden?

Wenn Sie vermuten, dass Sie einem DDoS-Angriff ausgesetzt sind, eskalieren Sie dies über die normalen Supportkanäle von Azure.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection – Referenzarchitekturen

DDoS Protection Standard ist für Dienste vorgesehen, die in einem virtuellen Netzwerk bereitgestellt werden. Weitere Informationen finden Sie unter [Virtual network integration for Azure services (Integration virtueller Netzwerke für Azure-Dienste)](../../virtual-network/virtual-network-for-azure-services.md). Für andere Dienste gilt der Standarddienst DDoS Protection Basic. Die folgenden Referenzarchitekturen sind nach Szenarios angeordnet, wobei Architekturmuster gruppiert sind.

### <a name="virtual-machine-windowslinux-workloads"></a>Die Arbeitsauslastungen virtueller Computer (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Anwendung wird auf VMs mit Lastenausgleichsmodul ausgeführt

Diese Referenzarchitektur zeigt eine Reihe von bewährten Methoden für die Ausführung mehrerer virtueller Windows-Computer (VMs) in einer Skalierungsgruppe hinter einem Lastenausgleichsmodul, um die Verfügbarkeit und Skalierbarkeit zu verbessern. Diese Architektur kann für eine zustandslose Arbeitsauslastung wie einen Webserver verwendet werden.

![Diagramm der Referenzarchitektur für Anwendungen, die auf VMs mit Lastenausgleichsmodul ausgeführt werden](./media/ddos-best-practices/image9.png)

In dieser Architektur wird eine Workload auf mehrere VM-Instanzen verteilt. Es gibt eine einzelne öffentliche IP-Adresse, und der Internetdatenverkehr wird durch ein Lastenausgleichsmodul auf die VMs verteilt. DDoS Protection Standard ist im virtuellen Netzwerk des Azure-Lastenausgleichsmoduls (Internet) aktiviert, dem die öffentliche IP-Adresse zugeordnet wurde.

Beim Lastenausgleich werden die eingehenden Internetanforderungen an die VM-Instanzen verteilt. Virtual Machine Scale Sets ermöglichen das horizontale Hoch- oder Herunterskalieren der Anzahl der VMs – sowohl manuell als auch automatisch basierend auf vordefinierten Regeln. Dies ist wichtig, wenn die Ressource einem DDoS-Angriff ausgesetzt ist. Lesen Sie [diesen Artikel](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), um weitere Informationen zu dieser Referenzarchitektur zu erhalten.

#### <a name="application-running-on-windows-n-tier"></a>Auf Windows-n-Schichten ausgeführte Anwendung

Es gibt viele Möglichkeiten für die Implementierung einer n-schichtigen Architektur. Das folgende Diagramm zeigt eine typische dreischichtige Webanwendung. Diese Architektur baut auf dem Artikel [Ausführen von VMs mit Lastenausgleich zur Steigerung von Skalierbarkeit und Verfügbarkeit](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) auf. In der Internet- und Unternehmensschicht werden VMs mit Lastenausgleich verwendet.

![Diagramm der Referenzarchitektur für Anwendung, die auf Windows-n-Schichten ausgeführt wird](./media/ddos-best-practices/image10.png)

In dieser Architektur ist DDoS Protection Standard im virtuellen Netzwerk aktiviert. Für alle öffentlichen IP-Adressen im virtuellen Netzwerk ist DDoS Protection für Schicht 3 und Schicht 4 aktiviert. Stellen Sie für den Schutz von Layer 7 Application Gateway in der WAF-SKU bereit. Lesen Sie [diesen Artikel](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier), um weitere Informationen zu dieser Referenzarchitektur zu erhalten.

#### <a name="paas-web-application"></a>PaaS-Webanwendung

Diese Referenzarchitektur zeigt die Ausführung einer Azure App Service-Anwendung in einer einzelnen Region. Diese Architektur demonstriert eine Reihe empfohlener Verfahren für eine Webanwendung, die [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) und [Azure SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/) verwendet.
Die Standby-Region ist für Failoverszenarios eingerichtet.

![Diagramm der Referenzarchitektur für eine PaaS-Webanwendung](./media/ddos-best-practices/image11.png)

Azure Traffic Manager leitet eingehende Anforderungen an Application Gateway in einer der Regionen weiter. Während des normalen Betriebs werden Anforderungen an Application Gateway in der aktiven Region weitergeleitet. Wenn diese Region nicht mehr verfügbar ist, führt Traffic Manager ein Failover zu Application Gateway in der Standby-Region aus.

Der gesamte für die Webanwendung bestimmte Datenverkehr aus dem Internet wird über Traffic Manager an die öffentliche Application Gateway-IP-Adresse weitergeleitet. (siehe [Application Gateway support for multi-tenant back ends (Application Gateway-Unterstützung für mehrinstanzenfähige Back-Ends)](../../application-gateway/application-gateway-web-app-overview.md)). In diesem Szenario ist der App-Dienst (Web-Apps) selbst nicht direkt nach außen gerichtet und wird von Application Gateway geschützt. 

Sie sollten die Application Gateway-WAF-SKU (Verhinderungsmodus) zum Schutz vor Angriffen auf Schicht 7 (HTTP/HTTPS/Websocket) konfigurieren. Darüber hinaus werden die Web-Apps so konfiguriert, dass sie Datenverkehr nur über die Application Gateway-IP-Adresse akzeptieren (siehe [IP and Domain Restrictions for Windows Azure Web Sites (IP- und Domäneneinschränkungen für Windows Azure-Websites)](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)).

Lesen Sie [diesen Artikel](/azure/architecture/reference-architectures/app-service-web-app/multi-region), um weitere Informationen zu dieser Referenzarchitektur zu erhalten.

### <a name="mitigation-for-non-web-paas-services"></a>Entschärfung für Non-Web-PaaS-Dienste

#### <a name="hdinsight-on-azure"></a>HDInsight unter Azure

Diese Referenzarchitektur zeigt das Konfigurieren von DDoS Protection Standard für [Azure HDInsight-Cluster](../../hdinsight/index.yml). Stellen Sie sicher, dass der HDInsight-Cluster mit einem virtuellen Netzwerk verknüpft und DDoS Protection in diesem virtuellen Netzwerk aktiviert ist.

![Bereiche „HDInsight“ und „Erweiterte Einstellungen“ mit Einstellungen zum virtuellen Netzwerk](./media/ddos-best-practices/image12.png)

![Auswahl für Aktivierung von DDoS Protection](./media/ddos-best-practices/image13.png)

In dieser Architektur wird der für den HDInsight-Cluster bestimmte Datenverkehr aus dem Internet an die öffentliche IP-Adresse weitergeleitet, die dem HDInsight-Gatewaylastenausgleich zugeordnet ist. Der Gatewaylastenausgleich sendet dann den Datenverkehr direkt an die Hauptknoten oder Workerknoten. Da DDoS Protection Standard im virtuellen HDInsight-Netzwerk aktiviert wird, erhalten alle öffentlichen IP-Adressen im virtuellen Netzwerk DDoS-Schutz für Schicht 3 und Schicht 4. Diese Referenzarchitektur kann mit Referenzarchitekturen für n-Schichten und mehrere Regionen kombiniert werden.

Weitere Informationen zu dieser Referenzarchitektur finden Sie in der Dokumentation [Erweitern von Azure HDInsight per Azure Virtual Network](../../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> Weder die Azure App Service-Umgebung für PowerApps noch die API-Verwaltung in einem virtuellen Netzwerk mit öffentlicher IP-Adresse werden nativ unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* [Gemeinsame Verantwortung in der Cloud](shared-responsibility.md)
* [Azure DDoS Protection-Produktseite](https://azure.microsoft.com/services/ddos-protection/)
* [Azure DDoS Protection Standard overview (Übersicht über Azure DDoS Protection Standard)](../../ddos-protection/ddos-protection-overview.md)