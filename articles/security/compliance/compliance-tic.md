---
title: Leitfaden für eine vertrauenswürdige Internetverbindung mit Azure
description: Leitfaden für eine vertrauenswürdige Internetverbindung mit Azure und SaaS-Diensten
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60608089"
---
# <a name="trusted-internet-connections-guidance"></a>Leitfaden für eine vertrauenswürdige Internetverbindung

Dieser Artikel beschreibt, wie Regierungsbehörden Microsoft Azure Government nutzen können, um die Konformität mit der Initiative für vertrauenswürdige Internetverbindungen (Trusted Internet Connection, TIC) zu erreichen. Der Artikel beschreibt, wie Azure Government mit den Angeboten Azure-IaaS (Infrastructure-as-a-Service) und Azure-PaaS (Platform-as-a-Service) verwendet wird.

## <a name="trusted-internet-connections-overview"></a>Übersicht über vertrauenswürdige Internetverbindungen

Ziel der TIC-Initiative ist die Optimierung und Standardisierung der Sicherheit einzelner externer Netzwerkverbindungen, die von Bundesbehörden genutzt werden. Die Richtlinie wird im [Memorandum M-08-05 der US-Behörde OMB (Office of Management and Budget)](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) beschrieben.

Das Programm für vertrauenswürdige Internetverbindungen (TIC-Programm) wurde im November 2007 vom OMB zur Verbesserung der Sicherheit in der Netzwerkumgebung von Behörden und der Funktionen zur Reaktion auf Incidents eingeführt. TIC ist dafür konzipiert, Netzwerkanalysen des gesamten eingehenden und ausgehenden Datenverkehrs von „.gov“-Websites durchzuführen, um spezifische Signaturen und musterbasierte Daten zu identifizieren. TIC deckt Verhaltensanomalien wie Botnetaktivitäten auf. Behörden müssen ihre Verbindungen mit externen Netzwerken konsolidieren und sämtlichen Datenverkehr über die Angriffserkennungs- und Eindringschutzgeräte (bekannt als EINSTEIN) leiten. Die Geräte werden an einer begrenzten Anzahl von Netzwerkendpunkten gehostet, die als _vertrauenswürdige Internetverbindungen_ bezeichnet werden.

Die Behörden möchten mithilfe des TIC-Programms Folgendes erfahren:
- Wer ist in meinem Netzwerk (autorisiert oder nicht autorisiert)?
- Wann wird auf mein Netzwerk zugegriffen und warum?
- Auf welche Ressourcen wird zugegriffen?

Alle externen Verbindungen von Behörden müssen gegenwärtig über eine vom OMB genehmigte vertrauenswürdige Internetverbindung geleitet werden. Bundesbehörden müssen am TIC-Programm teilnehmen, entweder als TIC Access Provider (TICAP) oder durch Beauftragung eines der größten Tier-1-Internetdienstanbieter. Diese Anbieter werden als MTIPS-Anbieter (Managed Trusted Internet Protocol Service, verwalteter vertrauenswürdiger Internetprotokolldienst) bezeichnet. TIC umfasst obligatorische kritische Funktionen, die von der Behörde und dem MTIPS-Anbieter ausgeführt werden. In der aktuellen Version von TIC werden für jeden TICAP und MTIPS die EINSTEIN-Angriffserkennungsgeräte der Version 2 und die EINSTEIN-Eindringschutzgeräte der Version 3 Accelerated (3A) bereitgestellt. Die Behörde geht eine „Vereinbarung zur Zusammenarbeit“ mit dem US-Heimatschutzministerium (Department of Homeland Security, DHS) ein, um EINSTEIN-Funktionen in US-Bundessystemen einzusetzen.

Damit das Heimatschutzministerium das „.gov“-Netzwerk schützen kann, benötigt es Rohdatenfeeds des Netzwerkdatenflusses, um Incidents im gesamten Bundesunternehmen zu korrelieren und Analysen mit speziellen Tools durchzuführen. Router des Heimatschutzministeriums bieten die Möglichkeit, IP-Netzwerkdatenverkehr beim Passieren einer Schnittstelle in beide Richtungen zu sammeln. Netzwerkadministratoren können den Netzwerkdatenfluss analysieren, um die Quelle und das Ziel des Datenverkehrs, die Klasse des Diensts usw. zu bestimmen. Die Daten des Netzwerkverkehrs werden als „Daten ohne Inhalt“ betrachtet, ähnlich einem Header, einer Quell-IP-Adresse, einer Ziel-IP-Adresse usw. Daten ohne Inhalt ermöglichen es dem Heimatschutzministerium, Informationen rund um den Inhalt zu erhalten: Wer hat was und wie lange gemacht.

Die Initiative umfasst auch Sicherheitsrichtlinien, Leitfäden und Rahmenbedingungen, die eine lokale Infrastruktur voraussetzen. Wenn Behörden zur Cloud wechseln, um Kosteneinsparungen, betriebliche Effizienz und Innovationen zu erreichen, können die Implementierungsanforderungen von TIC den Netzwerkdatenverkehr verlangsamen. Das Ergebnis sind Beschränkungen von Geschwindigkeit und Flexibilität der Behördenbenutzer beim Zugriff auf ihre cloudbasierten Daten.

## <a name="azure-networking-options"></a>Azure-Netzwerkoptionen

Es gibt drei Hauptoptionen für die Verbindung mit Azure-Diensten:

- Direkte Internetverbindung: Stellen Sie über eine offene Internetverbindung eine Direktverbindung mit Azure-Diensten her. Das Medium und die Verbindung sind öffentlich. Der Datenschutz erfolgt durch eine Verschlüsselung auf Anwendungs- und Transportebene. Die Bandbreite wird von der Internetkonnektivität des Standorts beschränkt. Verwenden Sie mehrere aktive Anbieter, um Resilienz sicherzustellen.
- Virtuelles privates Netzwerk (VPN): Stellen Sie über ein VPN-Gateway eine private Verbindung mit Ihrem virtuellen Azure-Netzwerk her.
Das Medium ist öffentlich, da es die Standardinternetverbindung eines Standorts durchläuft, aber die Verbindung wird in einem Tunnel verschlüsselt, um den Datenschutz zu gewährleisten. Die Bandbreite ist abhängig von den VPN-Geräten und wird durch die gewählte Konfiguration begrenzt. Azure-Point-to-Site-Verbindungen sind in der Regel auf 100 MBit/s beschränkt, und Site-to-Site-Verbindungen sind auf 1,25 GBit/s beschränkt.
- Azure ExpressRoute: Bei ExpressRoute handelt es sich um eine Direktverbindung mit Microsoft-Diensten. Da die Verbindung über einen isolierten Fiber-Channel erfolgt, kann sie je nach Konfiguration öffentlich oder privat sein. Die Bandbreite ist typischerweise auf höchstens 10 GBit/s beschränkt.

Es gibt mehrere Möglichkeiten, die TIC-Anforderungen aus Anhang H (Überlegungen zur Cloud) zu erfüllen, die im Dokument „Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0“ (Dokument zur TIC-Referenzarchitektur, Version 2.0) des US-Heimatschutzministeriums angegeben sind. In diesem Artikel wird der DHS TIC-Leitfaden als **TIC 2.0** bezeichnet.

Um die Verbindung von einem **Ministerium oder einer Behörde (Department/Agency, D/A)** mit Azure oder Office 365 zu ermöglichen, ohne den Datenverkehr über deren vertrauenswürdige Internetverbindung zu leiten, muss der D/A-Benutzer einen verschlüsselten Tunnel oder eine dedizierte Verbindung mit dem Cloud-Dienstanbieter (Cloud Service Provider, CSP) verwenden. Die CSP-Dienste können sicherstellen, dass die Konnektivität mit den D/A-Cloudressourcen nicht dem öffentlichen Internet für den direkten Zugriff durch Regierungsangestellte zur Verfügung gestellt wird.

Office 365 ist mit TIC 2.0, Anhang H, konform und verwendet entweder ExpressRoute mit aktiviertem [Microsoft-Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) oder eine Internetverbindung, die den gesamten Datenverkehr mit TLS 1.2 verschlüsselt. D/A-Endbenutzer im D/A-Netzwerk können sich über ihr Behördennetzwerk und die TIC-Infrastruktur mit dem Internet verbinden. Der gesamte Remoteinternetzugriff auf Office 365 wird blockiert und über die Behörde geleitet. Der D/A-Benutzer kann sich auch über eine ExpressRoute-Verbindung mit Microsoft-Peering, einer Art öffentlichem Peering, mit Office 365 verbinden.  

Nur für Azure können die zweite Option (VPN) und die dritte Option (ExpressRoute) diese Anforderungen erfüllen, wenn sie in Verbindung mit Diensten verwendet werden, die den Zugang zum Internet einschränken.

![Vertrauenswürdige Internetverbindung (TIC) und Microsoft](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Azure-IaaS-Angebot

Die Einhaltung der TIC-Richtlinie mit Azure-IaaS ist relativ einfach, da Azure-Kunden das Routing in ihrem eigenen virtuellen Netzwerk verwalten.

Die wichtigste Voraussetzung für die Konformität mit der TIC-Referenzarchitektur ist, dass Ihr virtuelles Netzwerk eine private Erweiterung des D/A-Netzwerks wird. Bei einer _privaten_ Erweiterung setzt die Richtlinie voraus, dass kein Datenverkehr das Netzwerk verlässt, außer über die lokale TIC-Netzwerkverbindung. Dieser Prozess wird als _Erzwingen von Tunneln_ bezeichnet. Für TIC-Konformität wird dabei der gesamte Datenverkehr von jedem System in der CSP-Umgebung über ein lokales Gateway in einem Organisationsnetzwerk über die vertrauenswürdige Internetverbindung ins Internet geleitet.

Die TIC-Konformität von Azure-IaaS ist in zwei Hauptschritte unterteilt:

- Schritt 1: Konfiguration
- Schritt 2: Überwachung

### <a name="azure-iaas-tic-compliance-configuration"></a>TIC-Konformität von Azure-IaaS: Konfiguration

Um eine TIC-konforme Architektur mit Azure zu konfigurieren, müssen Sie zunächst den direkten Internetzugriff auf Ihr virtuelles Netzwerk verhindern und dann den Internetdatenverkehr über das lokale Netzwerk erzwingen.

#### <a name="prevent-direct-internet-access"></a>Verhindern eines direkten Internetzugriffs

Der Azure-IaaS-Netzwerkzugriff erfolgt über virtuelle Netzwerke, die aus Subnetzen bestehen, denen die Netzwerkschnittstellencontroller (Network Interface Controller, NIC) virtueller Computer zugeordnet sind.

Das einfachste Szenario zur Unterstützung der TIC-Konformität besteht darin sicherzustellen, dass ein virtueller Computer oder eine Sammlung davon keine Verbindung mit externen Ressourcen herstellen kann. Stellen Sie die Trennung von externen Netzwerken mithilfe von Netzwerksicherheitsgruppen (NSGs) sicher. Verwenden Sie NSGs zum Steuern des Datenverkehrs an NICs oder Subnetze in Ihrem virtuellen Netzwerk. Eine NSG enthält Regeln zur Zugriffssteuerung, die den Datenverkehr auf Grundlage der Richtung des Datenverkehrs, des Protokolls, der Quelladresse und des Quellports, und der Zieladresse und des Zielports zulässt oder verweigert. Sie können die Regeln für eine NSG jederzeit ändern, und die Änderungen werden auf alle zugeordneten Instanzen angewendet. Weitere Informationen zum Erstellen einer NSG finden Sie unter [Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Erzwingen des Internetdatenverkehrs über ein lokales Netzwerk

Azure erstellt automatisch Systemrouten und weist die Routen allen Subnetzen eines virtuellen Netzwerks zu. Sie können zwar keine Systemrouten erstellen oder entfernen, aber einige Systemrouten mit benutzerdefinierten Routen überschreiben. Azure erstellt Standardsystemrouten für jedes Subnetz. Azure fügt bestimmten Subnetzen oder jedem Subnetz optionale Standardrouten hinzu, wenn Sie bestimmte Azure-Funktionen verwenden. Mit dieser Art von Routing wird Folgendes sichergestellt:
- Datenverkehr mit einem Ziel im virtuellen Netzwerk bleibt innerhalb des virtuellen Netzwerks.
- Von IANA festgelegte private Adressräume wie 10.0.0.0/8 werden gelöscht, es sei denn, sie sind im Adressraum des virtuellen Netzwerks enthalten.
- Das „Notfall“-Routing von 0.0.0.0/0 zum Internetendpunkt des virtuellen Netzwerks erfolgt.

![Erzwingen von Tunneln für TIC](media/tic-diagram-c.png)

Der gesamte Datenverkehr, der das virtuelle Netzwerk verlässt, muss über die lokale Verbindung geleitet werden, um sicherzustellen, dass der gesamte Datenverkehr die vertrauenswürdige D/A-Internetverbindung durchläuft. Zum Anpassen von Routen können Sie benutzerdefinierte Routen erstellen oder BGP-Routen (Border Gateway Protocol) zwischen Ihrem lokalen Netzwerkgateway und einem Azure-VPN-Gateway austauschen. Weitere Informationen zu benutzerdefinierten Routen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke: Benutzerdefiniert](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Weitere Informationen zum BGP finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Hinzufügen von benutzerdefinierten Routen

Wenn Sie ein routenbasiertes Gateway des virtuellen Netzwerks verwenden, können Sie Tunneln in Azure erzwingen. Fügen Sie eine benutzerdefinierte Route (User-Defined Route, UDR) hinzu, die für 0.0.0.0/0-Datenverkehr festlegt, dass er an einen **nächsten Hop** Ihres Gateways des virtuellen Netzwerks weitergeleitet wird. Azure priorisiert benutzerdefinierte Routen über systemdefinierte Routen. Sämtlicher nicht virtueller Netzwerkdatenverkehr wird an Ihr Gateway des virtuellen Netzwerks gesendet, das dann den Datenverkehr lokal weiterleiten kann. Nachdem Sie die UDR definiert haben, ordnen Sie die Route vorhandenen oder neuen Subnetzen in allen virtuellen Netzwerken in Ihrer Azure-Umgebung zu.

![Benutzerdefinierte Routen und TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Verwenden von Border Gateway Protocol

Wenn Sie ExpressRoute oder ein BGP-fähiges Gateway des virtuellen Netzwerks verwenden, ist BGP der bevorzugte Mechanismus zum Ankündigen von Routen. Für eine von BGP angekündigte Route von 0.0.0.0.0/0 stellen ExpressRoute und BGP-fähige Gateways virtueller Netzwerke sicher, dass diese Standardroute auf alle Subnetze innerhalb Ihrer virtuellen Netzwerke angewendet wird.

### <a name="azure-iaas-tic-compliance-auditing"></a>TIC-Konformität von Azure-IaaS: Überwachung

Azure bietet mehrere Möglichkeiten, die TIC-Konformität zu überwachen.

#### <a name="view-effective-routes"></a>Anzeigen effektiver Routen

Vergewissern Sie sich, dass die Standardroute weitergegeben wird, indem Sie die _effektiven Routen_ für einen bestimmten virtuellen Computer, einen bestimmten NIC oder eine benutzerdefinierte Routentabelle im [Azure-Portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) oder in [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell) überwachen. **Effektive Routen** zeigt die relevanten benutzerdefinierten Routen, die von BGP angekündigten Routen und die Systemrouten an, die für die jeweilige Entität gelten, wie in der folgenden Abbildung dargestellt:

![Effektive Routen](media/tic-screen-1.png)

> [!NOTE]
> Sie können die effektiven Routen für einen NIC nur anzeigen, wenn der NIC mit einem aktiven virtuellen Computer verknüpft ist.

#### <a name="use-azure-network-watcher"></a>Verwenden von Azure Network Watcher

Azure Network Watcher bietet verschiedene Tools zur Überwachung der TIC-Konformität. Weitere Informationen finden Sie in [dieser Übersicht über Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Erfassen von Datenflussprotokollen für Netzwerksicherheitsgruppen 

Verwenden Sie Network Watcher, um Netzwerk-Datenflussprotokolle zu erfassen, die die Metadaten zum IP-Datenverkehr angeben. Die Netzwerk-Datenflussprotokolle enthalten neben anderen Daten auch die Quell- und Zieladressen der Ziele. Sie können diese Daten mit Protokollen des Gateways des virtuellen Netzwerks, der lokalen Edgegeräte oder der vertrauenswürdigen Internetverbindung verwenden, um zu überwachen, ob der gesamte Datenverkehr lokal weiterleitet wird. 

## <a name="azure-platform-as-a-service-offerings"></a>Azure-PaaS-Angebote

Azure-PaaS-Dienste wie Azure Storage sind über eine im Internet erreichbare URL zugänglich. Jeder, der über genehmigte Anmeldeinformationen verfügt, kann von jedem Ort aus auf die Ressource zugreifen, ohne eine vertrauenswürdige Internetverbindung hierfür zu verwenden. Aus diesem Grund gehen viele Regierungskunden fälschlicherweise davon aus, dass Azure-PaaS-Dienste nicht den TIC-Richtlinien entsprechen. Viele Azure-PaaS-Dienste können mit TIC-Richtlinien konform sein. Ein Dienst ist konform, wenn die Architektur mit der TIC-konformen IaaS-Umgebung identisch ist ([wie zuvor beschrieben](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) und der Dienst einem virtuellen Azure-Netzwerk angefügt ist.

Wenn Azure-PaaS-Dienste in ein virtuelles Netzwerk integriert sind, kann von diesem virtuellen Netzwerk privat auf den Dienst zugegriffen werden. Sie können das benutzerdefinierte Routing für 0.0.0.0/0 über benutzerdefinierte Routen oder BGP anwenden. Durch benutzerdefiniertes Routing wird sichergestellt, dass der gesamte Datenverkehr für das Internet lokal weiterleitet wird, um die vertrauenswürdige Internetverbindung zu durchlaufen. Integrieren Sie Azure-Dienste mit den folgenden Mustern in virtuelle Netzwerke:

- **Bereitstellen einer dedizierten Instanz eines Diensts:** Immer mehr PaaS-Dienste können als dedizierte Instanzen bereitgestellt werden, deren Endpunkte mit virtuellen Netzwerken verbunden sind. Sie können eine App Service-Umgebung für PowerApps im Modus „Isoliert“ bereitstellen, damit der Netzwerkendpunkt auf ein virtuelles Netzwerk beschränkt wird. Die App Service-Umgebung kann dann viele Azure-PaaS-Dienste hosten, z.B. Azure-Web-Apps, Azure API Management und Azure Functions.
- **Verwenden von VNET-Dienstendpunkten:** Immer mehr PaaS-Dienste bieten die Möglichkeit, ihren Endpunkt zu einer privaten IP-Adresse des virtuellen Netzwerks zu verschieben, anstatt eine öffentliche Adresse zu verwenden.

Dienste, die seit Mai 2018 die Bereitstellung dedizierter Instanzen in einem virtuellen Netzwerk oder die Verwendung von Dienstendpunkten unterstützen, sind in den folgenden Tabellen aufgeführt.

> [!Note]
> Der Verfügbarkeitsstatus entspricht den Azure-Diensten, die im Handel erhältlich sind. Der Verfügbarkeitsstatus für Azure-Dienste in Azure Government steht aus.

### <a name="support-for-service-endpoints"></a>Unterstützung für Dienstendpunkte

|Dienst                        |Verfügbarkeit      |
|-------------------------------|------------------|
|Azure Key Vault                | Private Vorschau  |
|Azure Cosmos DB                | Private Vorschau  |
|Identitätsdienste              | Private Vorschau  |
|Azure Data Lake                | Private Vorschau  |
|Azure Database for PostgreSQL  | Private Vorschau  |
|Azure Database for MySQL       | Private Vorschau  |
|Azure SQL Data Warehouse       | Öffentliche Vorschau   |
|Azure SQL-Datenbank             | Allgemeine Verfügbarkeit (General Availability, GA) |
|Azure Storage                  | Allgemein verfügbar               |

### <a name="support-for-virtual-network-injection"></a>Unterstützung für das Einfügen in virtuelle Netzwerke

|Dienst                               |Verfügbarkeit      |
|--------------------------------------|------------------|
|Verwaltete Azure SQL-Datenbank-Instanz   | Öffentliche Vorschau   |
|Azure Kubernetes Service (AKS)        | Öffentliche Vorschau   |
|Azure Service Fabric                  | Allgemein verfügbar               |
|Azure API Management                  | Allgemein verfügbar               |
|Azure Active Directory                | Allgemein verfügbar               |
|Azure Batch                           | Allgemein verfügbar               |
|App Service-Umgebung               | Allgemein verfügbar               |
|Azure Cache for Redis                     | Allgemein verfügbar               |
|Azure HDInsight                       | Allgemein verfügbar               |
|VM-Skalierungsgruppe             | Allgemein verfügbar               |
|Azure Cloud Services                  | Allgemein verfügbar               |


### <a name="virtual-network-integration-details"></a>Details der Integration in ein virtuelles Netzwerk

Das folgende Diagramm zeigt den allgemeinen Netzwerkdatenfluss für den Zugriff auf PaaS-Dienste. Der Zugriff wird für das Einfügen in virtuelle Netzwerke und das Diensttunneling von virtuellen Netzwerken dargestellt. Weitere Informationen zu Netzwerkdienstgateways, virtuellen Netzwerken und Diensttags finden Sie unter [Sicherheitsgruppen: Diensttags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![PaaS-Konnektivitätsoptionen für TIC](media/tic-diagram-e.png)

1. Eine private Verbindung mit Azure wird über ExpressRoute hergestellt. Das private Peering von ExpressRoute mit erzwungenem Tunneln wird verwendet, um ein Routing des gesamten Datenverkehrs des virtuellen Netzwerks des Kunden über ExpressRoute und zurück an den lokalen Standort zu erzwingen. Microsoft-Peering ist nicht erforderlich.
2. Azure VPN Gateway kann in Kombination mit ExpressRoute und Microsoft-Peering die End-to-End-IPSec-Verschlüsselung zwischen dem virtuellen Netzwerk des Kunden und dem lokalen Edge überlagern. 
3. Die Netzwerkverbindung mit dem virtuellen Netzwerk des Kunden wird über NSGs gesteuert, die es dem Kunden ermöglichen, den Zugriff basierend auf IP-Adressen, Ports und Protokollen zu gewähren bzw. zu verweigern.
4. Durch die Einrichtung eines Dienstendpunkts für den Dienst des Kunden wird das virtuelle Netzwerk des Kunden auf den PaaS-Dienst erweitert.
5. Der PaaS-Dienstendpunkt ist standardmäßig so gesichert, dass er den Zugriff innerhalb des virtuellen Netzwerks des Kunden **verweigert** und nur von bestimmten Subnetzen zulässt. Die Standardeinstellung verweigert auch Verbindungen aus dem Internet.
6. Für andere Azure-Dienste, die auf Ressourcen im virtuellen Netzwerk des Kunden zugreifen müssen, sollte einer der folgenden Punkte zutreffen:  
   - Direkt im virtuellen Netzwerk bereitgestellt
   - Selektiv zugelassen, basierend auf der Richtlinie des jeweiligen Azure-Diensts

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Option A: Bereitstellen einer dedizierten Instanz eines Diensts (Einfügen in ein virtuelles Netzwerk)

Durch das Einfügen in ein virtuelles Netzwerk können Kunden dedizierte Instanzen eines bestimmten Azure-Diensts, wie z.B. HDInsight, selektiv in ihrem eigenen virtuellen Netzwerk bereitstellen. Dienstinstanzen werden in einem dedizierten Subnetz im virtuellen Netzwerk des Kunden bereitgestellt. Das Einfügen in ein virtuelles Netzwerk ermöglicht den Zugriff auf die Dienstressourcen über Adressen, die nicht über das Internet geroutet werden können. Lokale Instanzen können über ExpressRoute oder ein Site-to-Site-VPN über den Adressraum des virtuellen Netzwerks direkt auf diese Dienstinstanzen zugreifen, statt Firewalls für den öffentlichen Internetadressraum zu öffnen. Wenn eine dedizierte Instanz einem Endpunkt angefügt ist, können Sie die gleichen Strategien wie für IaaS-TIC-Konformität verwenden. Mit Standardrouting wird sichergestellt, dass der Datenverkehr für das Internet an ein Gateway des virtuellen Netzwerks umgeleitet wird, das lokal gebunden ist. Sie können eingehenden und ausgehenden Zugriff über NSGs für das jeweilige Subnetz weiter steuern.

![Übersicht über das Einfügen in virtuelle Netzwerke](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Option B: Verwenden von VNET-Dienstendpunkten (Diensttunnel)

Eine zunehmende Anzahl von mehrinstanzfähigen Azure-Diensten bietet „Dienstendpunkte“. Dienstendpunkte sind eine alternative Methode für die Integration in virtuelle Azure-Netzwerke. Mit VNET-Dienstendpunkten wird der IP-Adressraum Ihres virtuellen Netzwerks und die Identität Ihres virtuellen Netzwerks über eine direkte Verbindung auf den Dienst erweitert. Der Datenverkehr vom virtuellen Netzwerk zum Azure-Dienst bleibt dabei immer innerhalb des Azure-Backbonenetzwerks. 

Nachdem Sie einen Dienstendpunkt für einen Dienst aktiviert haben, verwenden Sie Richtlinien, die vom Dienst verfügbar gemacht werden, um Verbindungen für den Dienst auf dieses virtuelle Netzwerk zu beschränken. Zugriffsüberprüfungen werden vom Azure-Dienst auf der Plattform erzwungen. Zugriff auf eine gesperrte Ressource wird nur gewährt, wenn die Anforderung aus dem zulässigen virtuellen Netzwerk oder Subnetz oder von den beiden IP-Adressen stammt, die verwendet werden, um den lokalen Datenverkehr zu identifizieren, falls Sie ExpressRoute verwenden. Nutzen Sie diese Methode, um effektiv zu verhindern, dass eingehender/ausgehender Datenverkehr den PaaS-Dienst direkt verlässt.

![Übersicht über Dienstendpunkte](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Native Cloudtools zur Erkennung von Netzwerksituationen

Azure bietet native Cloudtools, die Ihnen dabei helfen, Situationen bezüglich der Datenverkehrsflüsse Ihres Netzwerks richtig einzuschätzen. Die Tools sind für die Konformität mit der TIC-Richtlinie nicht erforderlich. Die Tools können Ihre Sicherheitsfunktionen erheblich verbessern.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) ist ein Azure-Dienst, der Ihrer Organisation eine bessere Möglichkeit bietet, Konformitätsinitiativen zu überwachen und durchzusetzen. Kunden können jetzt ihre Azure Policy-Regeln planen und testen, um eine zukünftige TIC-Konformität zu gewährleisten.

Azure Policy wird auf Abonnementebene angewendet. Der Dienst bietet eine zentralisierte Oberfläche, auf der Sie Konformitätsaufgaben ausführen können. Beispiele:
- Verwalten von Initiativen
- Konfigurieren von Richtliniendefinitionen
- Überwachen der Konformität
- Erzwingen der Konformität
- Verwalten von Ausnahmen

Zusätzlich zu vielen integrierten Definitionen können Administratoren ihre eigenen benutzerdefinierten Definitionen mithilfe von einfachen JSON-Vorlagen festlegen. Microsoft empfiehlt die Priorisierung der Überwachung über die Erzwingung, sofern möglich.

Die folgenden Beispielrichtlinien können in TIC-Konformitätsszenarien verwendet werden:

|Richtlinie  |Beispielszenario  |Vorlage  |
|---------|---------|---------|
|Tabelle mit benutzerdefinierten Routen erzwingen | Stellen Sie sicher, dass die Standardroute in allen virtuellen Netzwerken auf ein zulässiges Gateway eines virtuellen Netzwerks für das Routing auf „Lokal“ verweist.    | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/no-user-defined-route-table.md). |
|Überprüfung, wenn Network Watcher nicht für eine Region aktiviert ist  | Stellen Sie sicher, dass Network Watcher für alle verwendeten Regionen aktiviert ist.  | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/network-watcher-not-enabled.md). |
|NSG x in jedem Subnetz  | Stellen Sie sicher, dass eine NSG (oder ein Satz von zulässigen NSGs) mit blockiertem Internetdatenverkehr auf alle Subnetze in jedem virtuellen Netzwerk angewendet wird. | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/nsg-on-subnet.md). |
|NSG x auf jedem NIC | Stellen Sie sicher, dass eine NSG mit blockiertem Internetdatenverkehr auf alle NICs auf allen virtuellen Computern angewendet wird. | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/nsg-on-nic.md). |
|Ein zulässiges virtuelles Netzwerk für Netzwerkschnittstellen von virtuellen Computern verwenden  | Stellen Sie sicher, dass sich alle NICs in einem zulässigen virtuellen Netzwerk befinden. | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/use-approved-vnet-vm-nics.md). |
|Zulässige Standorte | Stellen Sie sicher, dass alle Ressourcen in Regionen mit konformen virtuellen Netzwerken und Network Watcher-Konfiguration bereitgestellt werden.  | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/allowed-locations.md). |
|Nicht zulässige Ressourcentypen, z.B. **öffentliche IP-Adressen** | Unterbinden Sie die Bereitstellung von Ressourcentypen, die über keinen Konformitätsplan verfügen. Verwenden Sie diese Richtlinie, um die Bereitstellung von Ressourcen mit öffentlichen IP-Adressen zu unterbinden. NSG-Regeln können zwar dazu verwendet werden, den eingehenden Internetdatenverkehr effektiv zu blockieren, aber indem Sie verhindern, dass öffentliche IP-Adressen verwendet werden, bieten Sie noch weniger Angriffsfläche.   | Beginnen Sie mit dieser [Vorlage](../../governance/policy/samples/not-allowed-resource-types.md).  |

### <a name="network-watcher-traffic-analytics"></a>Traffic Analytics von Network Watcher

[Traffic Analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) von Network Watcher nutzt Datenflussprotokolle und andere Protokolle, um einen allgemeinen Überblick über den Netzwerkdatenverkehr zu erhalten. Diese Daten sind nützlich, um die TIC-Konformität zu überwachen und mögliche Problemstellen zu identifizieren. Sie können mit dem Übersichtsdashboard schnell die virtuellen Computer überprüfen, die mit dem Internet kommunizieren, und erhalten eine entsprechende Liste für das TIC-Routing.

![Datenverkehrsanalyse](media/tic-traffic-analytics-1.png)

Verwenden Sie die **geografische Karte**, um wahrscheinliche physische Ziele des Internetdatenverkehrs für Ihre virtuellen Computer schnell zu identifizieren. Sie können verdächtige Standorte oder Musteränderungen identifizieren und selektieren:

![Geografische Karte](media/tic-traffic-analytics-2.png)

Verwenden Sie die **Topologie der virtuellen Netzwerke**, um bestehende virtuelle Netzwerke schnell zu erfassen:

![Karte der Netzwerktopologie](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher – Test des nächsten Hops

Netzwerke in Regionen, die von Network Watcher überwacht werden, können Tests der nächsten Hops durchführen. Im Azure-Portal können Sie eine Quelle und ein Ziel für einen Beispiel-Netzwerkdatenstrom für Network Watcher eingeben, um das Ziel des nächsten Hops aufzulösen. Führen Sie diesen Test für virtuelle Computer und Beispielinternetadressen durch, um sicherzustellen, dass das Ziel des nächsten Hops das erwartete Gateway eines virtuellen Netzwerks ist.

![Tests der nächsten Hops](media/tic-network-watcher.png)

## <a name="conclusions"></a>Zusammenfassung

Sie können den Zugriff für Microsoft Azure, Office 365 und Dynamics 365 problemlos so konfigurieren, dass er die im Mai 2018 formulierten und definierten Richtlinien im TIC 2.0-Anhang H erfüllt. Microsoft weiß, dass der TIC-Leitfaden geändert werden kann. Microsoft versucht, Kunden dabei zu helfen, dem Leitfaden rechtzeitig zu entsprechen, wenn ein neuer Leitfaden veröffentlicht wird.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Anhang: Muster von vertrauenswürdigen Internetverbindungen für gängige Workloads

| Category (Kategorie) | Workload | IaaS | Dedizierte PaaS/Einfügen in virtuelle Netzwerke  | Dienstendpunkte  |
|---------|---------|---------|---------|--------|
| Compute | Virtuelle Azure Linux-Computer | Ja | | |
| Compute | Virtuelle Azure Windows-Computer | Ja | | |
| Compute | VM-Skalierungsgruppen | Ja | | |
| Compute | Azure-Funktionen | | App Service-Umgebung | |
| Web und mobil | Interne Webanwendung | | App Service-Umgebung| |
| Web und mobil | Interne mobile Anwendung | | App Service-Umgebung | |
| Web und mobil | API-Anwendungen | | App Service-Umgebung | |
| Container | Azure Container Service | | | Ja |
| Container | Azure Kubernetes Service (AKS) \* | | | Ja |
| Datenbank | Azure SQL-Datenbank | | Verwaltete Azure SQL-Datenbank-Instanz \* | Azure SQL |
| Datenbank | Azure Database for MySQL | | | Ja |
| Datenbank | Azure Database for PostgreSQL | | | Ja |
| Datenbank | Azure SQL Data Warehouse | | | Ja |
| Datenbank | Azure Cosmos DB | | | Ja |
| Datenbank | Azure Cache for Redis | | Ja | |
| Storage | Azure Blob Storage | Ja | | |
| Storage | Azure Files | Ja | | |
| Storage | Azure Queue Storage | Ja | | |
| Storage | Azure-Tabellenspeicher | Ja | | |
| Storage | Azure Disk Storage | Ja | | |

\* Öffentliche Vorschauversion in Azure Government, Mai 2018
