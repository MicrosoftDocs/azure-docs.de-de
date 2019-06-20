---
title: 'Azure Security and Compliance Blueprint: Dreischichtige IaaS-Webanwendungen für UK OFFICIAL'
description: 'Azure Security and Compliance Blueprint: Dreischichtige IaaS-Webanwendungen für UK OFFICIAL'
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 13ea2b68027c81bca7b43cef62cf7039aa0ea8dd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609499"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure Security and Compliance Blueprint: Dreischichtige IaaS-Webanwendungen für UK OFFICIAL

## <a name="overview"></a>Übersicht

 Dieser Artikel enthält Anleitungen und Automatisierungsskripts zum Bereitstellen einer webbasierten, dreischichtigen Microsoft Azure-Architektur, die sich für das Verarbeiten vieler Workloads eignet, die im Vereinigten Königreich als OFFICIAL klassifiziert werden.

 Bei Verwendung eines Infrastruktur-als-Code-Ansatzes stellt die Sammlung von Vorlagen für [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) eine Umgebung bereit, die an den 14 [Cloudsicherheitsprinzipien](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) des UK National Cyber Security Centre (NCSC) und den [Critical Security Controls](https://www.cisecurity.org/critical-controls.cfm) (Kritische Sicherheitsregulierungen) des Center for Internet Security (CIS) ausgerichtet ist.

 Das NCSC empfiehlt, dass seine Cloudsicherheitsprinzipien von Kunden für die Beurteilung der Sicherheitseigenschaften des Diensts und zum Verstehen der Aufteilung der Zuständigkeit zwischen Kunde und Lieferant verwendet werden. Wir haben zu jedem dieser Prinzipien Informationen zur Verfügung gestellt, die Ihnen das Verständnis der Aufteilung der Zuständigkeiten erleichtern sollen.

 Diese Architektur und die entsprechende Azure Resource Manager-Vorlagen werden vom Microsoft-Whitepaper [14 Cloud Security Controls for UK cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1) (14 Sicherheitskontrollen für die UK-Cloud mithilfe von Microsoft Azure) unterstützt. In diesem Dokument wird katalogisiert, wie Azure-Dienste sich an den 14 Prinzipien der Cloudsicherheit gemäß UK NCSC ausrichten, und was es Organisationen ermöglicht, ihre Fähigkeit zur Einhaltung ihrer Complianceverpflichtungen zu beschleunigen, indem sie cloudbasierte Dienste global und im Vereinigten Königreich in der Microsoft Azure Cloud nutzen.

 Mit dieser Vorlage wird die Infrastruktur für den Workload bereitgestellt. Der Anwendungscode und unterstützende Software der Geschäftsschicht und der Datenschicht muss installiert und konfiguriert sein. Ausführlichere Bereitstellungsanweisungen finden Sie [hier](https://aka.ms/ukwebappblueprintrepo).

 Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich schnell und einfach registrieren: [Erste Schritte mit Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

 Die Azure-Vorlagen stellen eine dreischichtige Webanwendungsarchitektur in einer Azure-Cloudumgebung bereit, die UK OFFICIAL-Workloads unterstützt. Die Architektur bietet eine sichere Hybridumgebung, die ein vorhandenes lokales Netzwerk nach Azure erweitert und den sicheren Zugriff auf webbasierte Workloads durch Unternehmensbenutzer oder aus dem Internet ermöglicht.

![Dreischichtige IaaS-Webanwendung für UK OFFICIAL – Diagramm zur Referenzarchitektur](images/ukofficial-iaaswa-architecture.png?raw=true "Dreischichtige IaaS-Webanwendung für UK OFFICIAL – Diagramm zur Referenzarchitektur")

 Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

(1) /16 virtuelles Netzwerk: betriebsbereites VNet
- (3) /24 Subnetze: 3-schichtig (Web, Geschäft, Daten)
- (1) /27 Subnetz: ADDS
- (1) /27 Subnet7: Gatewaysubnetz
- (1) /29 Subnet: Application Gateway-Subnetz
- Verwendet Standard-DNS (von Azure bereitgestellt)
- Peering ist im Verwaltungs-VNet aktiviert
- Netzwerksicherheitsgruppe (NSG) zum Verwalten des Datenverkehrsflusses

(1) /24 virtuelles Netzwerk: Verwaltungs-VNet
- (1) /27 Subnetz
- Verwendet (2) ADDS-DNS-Einträge und einen (1) Azure DNS-Eintrag
- Im betriebsbereiten VNEt ist Peering aktiviert
- Netzwerksicherheitsgruppe (NSG) zum Verwalten des Datenverkehrsflusses

(1) Application Gateway
- WAF: aktiviert
- WAF-Modus: Prävention
- Regelsatz: OWASP 3.0
- HTTP-Listener an Port 80
- Konnektivität/Datenverkehr durch NSG reguliert
- Endpunkt mit öffentlicher IP-Adresse definiert (Azure)

(1) VPN: routenbasierter Site-to-Site-IPSec-VPN-Tunnel
- Endpunkt mit öffentlicher IP-Adresse definiert (Azure)
- Konnektivität/Datenverkehr durch NSG reguliert
- (1) lokales Netzwerkgateway (lokaler Endpunkt)
- (1) Azure-Netzwerkgateway (Azure-Endpunkt)

(9) virtuelle Computer: Alle virtuellen Computer werden mit DSC-Einstellungen für die Azure-IaaS-Antischadsoftware bereitgestellt.

- (2) Active Directory Domain Services-Domänencontroller (Windows Server 2012 R2)
  - (2) DNS-Serverrollen: 1 pro VM
  - (2) Netzwerk-Schnittstellenkarten, die mit dem betriebsbereiten VNet verbunden sind: 1 pro VM
  - Beide sind Mitglied der in der Vorlage definierten Domäne
    - Als Teil der Bereitstellung erstellte Domäne


- (1) Jumpbox-Verwaltungs-VM (Bollwerkhost)
  - 1 Netzwerk-Schnittstellenkarte im Verwaltungs-VNet mit öffentlicher IP-Adresse
    - NSG wird zum Einschränken des Datenverkehrs (eingehend/ausgehend) mit bestimmten Quellen verwendet
  - Kein Domänenmitglied


- (2) VMs in der Webschicht
  - (2) IIS-Serverrollen: 1 pro VM
  - (2) Netzwerk-Schnittstellenkarten, die mit dem betriebsbereiten VNet verbunden sind: 1 pro VM
  - Kein Domänenmitglied


- (2) VMs in der Geschäftsschicht
  - (2) Netzwerk-Schnittstellenkarten, die mit dem betriebsbereiten VNet verbunden sind: 1 pro VM
  - Kein Domänenmitglied


- (2) VMs in der Datenschicht
  - (2) Netzwerk-Schnittstellenkarten, die mit dem betriebsbereiten VNet verbunden sind: 1 pro VM
  - Kein Domänenmitglied

Verfügbarkeitsgruppen
- (1) Gruppe Active Directory-Domänencontroller-VMs: 2 VMs
- (1) Gruppe Webschicht-VMs: 2 VMs
- (1) Gruppe Geschäftsschicht-VMs: 2 VMs
- (1) Gruppe Datenschicht-VMs: 2 VMs

Load Balancer
- (1) Lastenausgleichsmodul für die Webschicht
- (1) Lastenausgleichsmodul für die Geschäftsschicht
- (1) Lastenausgleichsmodul für die Datenschicht

Storage
- (14) Speicherkonten insgesamt
  - Verfügbarkeitsgruppe für den Active Directory-Domänencontroller
    - (2) Primäre, lokal redundante Speicherkonten (LRS): 1 für jede VM  
    - (1) Lokal redundantes Diagnosespeicherkonto (LRS) für die ADDS-Verfügbarkeitsgruppe
  - Jumpbox-Verwaltungs-VM
    - (1) Primäres, lokal redundantes Speicherkonto (LRS) für die Jumpbox-VM
    - (1) Lokal redundantes Diagnosespeicherkonto (LRS) für die Jumpbox-VM
  - VMs in der Webschicht
    - (2) Primäre, lokal redundante Speicherkonten (LRS): 1 für jede VM  
    - (1) Lokal redundantes Diagnosespeicherkonto (LRS) für die Webschicht-Verfügbarkeitsgruppe
  - VMs in der Geschäftsschicht
    - (2) Primäre, lokal redundante Speicherkonten (LRS): 1 für jede VM  
    - (1) Lokal redundantes Diagnosespeicherkonto (LRS) für die Geschäftsschicht-Verfügbarkeitsgruppe
  - VMs in der Datenschicht
    - (2) Primäre, lokal redundante Speicherkonten (LRS): 1 für jede VM  
    - (1) Lokal redundantes Diagnosespeicherkonto (LRS) für die Datenschicht-Verfügbarkeitsgruppe

### <a name="deployment-architecture"></a>Bereitstellungsarchitektur:

**Lokales Netzwerk:** Ein privates lokales Netzwerk innerhalb einer Organisation.

**Produktions-VNET:** Im Produktions-[VNET](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (virtuelles Netzwerk) werden die Anwendung und sonstige in Azure ausgeführte Betriebsressourcen gehostet. Jedes VNet kann mehrere Subnetze enthalten, die zum Isolieren und Verwalten des Netzwerkverkehrs verwendet werden.

**Webebene:** Verarbeitet eingehende HTTP-Anforderungen. Antworten werden durch diese Schicht zurückgegeben.

**Geschäftsebene:** Implementiert Geschäftsprozesse und sonstige Funktionslogik für das System.

**Datenbankebene:** Stellt dauerhaften Datenspeicher bereit und verwendet dazu [SQL Server-Always On-Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/hh510230.aspx) zum Erreichen von Hochverfügbarkeit. Kunden können [Azure SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) als PaaS-Alternative verwenden.

**Gateway**: [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) stellt Verbindungen zwischen den Routern im lokalen Netzwerk und dem Produktions-VNET bereit.

**Internetgateway und öffentliche IP-Adresse:** Das Internetgateway macht Anwendungsdienste über das Internet für Benutzer verfügbar. Datenverkehr, der auf diese Dienste zugreift, ist mithilfe eines [Application Gateways](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) geschützt, das Funktionen zum Routing in Schicht 7 und Lastenausgleich mit Schutz durch die Web Application Firewall (WAF) bietet.

**Verwaltungs-VNET:** Dieses [VNET](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) enthält Ressourcen, die Verwaltungs- und Überwachungsfunktionen für die im Produktions-VNET ausgeführten Workloads implementieren.

**Jumpbox:** Diese sichere VM im Netzwerk (auch als [Bastionhost](https://en.wikipedia.org/wiki/Bastion_host) bezeichnet) wird von Administratoren für das Herstellen von Verbindungen mit VMs im Produktions-VNET verwendet. Die Jumpbox verfügt über eine NSG, bei der Remotedatenverkehr nur von öffentlichen IP-Adressen zugelassen wird, die in einer Liste mit sicheren Adressen enthalten sind. Damit Remotedesktopverkehr (RDP) zugelassen wird, muss die Quelle des Datenverkehrs in der NSG definiert sein. Die Verwaltung der Produktionsressourcen erfolgt über RDP unter Verwendung einer geschützten Jumpbox-VM.

**Benutzerdefinierte Routen:** Mit [benutzerdefinierten Routen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) wird der Fluss von IP-Datenverkehr in Azure-VNETs definiert.

**VNETs mit Netzwerkpeering:** Die VNETs für Produktion und Verwaltung sind mittels [VNET-Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) verbunden.
Diese VNets werden nach wie vor als separate Ressourcen verwaltet, erscheinen aber für alle Verbindungszwecke für diese virtuellen Computer als eine Ressource. Diese Netzwerke kommunizieren miteinander direkt mithilfe privater IP-Adressen. VNet-Peering hat zur Voraussetzung, dass sich die VNets in der gleichen Azure-Region befinden.

**Netzwerksicherheitsgruppen:** [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) enthalten Zugriffssteuerungslisten, die Datenverkehr in einem VNET zulassen oder ablehnen. NSGs können dafür verwendet werden, den Datenverkehr auf der Ebene eines Subnetzes oder einzelner VMs zu schützen.

**Active Directory Domain Services (AD DS):** Diese Architektur weist eine dedizierte Bereitstellung von [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) auf.

**Protokollierung und Überwachung:** Im [Azure-Aktivitätsprotokoll](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) werden Vorgänge erfasst, die mit den Ressourcen in Ihrem Abonnement ausgeführt werden, etwa wer einen Vorgang eingeleitet hat, wann der Vorgang ausgeführt wurde, den Status des Vorgangs und die Werte anderer Eigenschaften, die Sie zur Untersuchung des Vorgangs heranziehen können. Das Azure-Aktivitätsprotokoll ist ein Azure-Plattformdienst, der alle Aktionen in einem Abonnement erfasst. Die Protokolle können bei Bedarf archiviert oder exportiert werden.

**Netzwerküberwachung und -warnung:** [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) ist ein Plattformdienst, der die Erfassung von Netzwerkpaketen, Datenflussprotokollierung, Topologietools und Diagnosen für Netzwerkdatenverkehr in Ihren VNETs bereitstellt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="business-continuity"></a>Geschäftskontinuität

**Hochverfügbarkeit**: Serverworkloads werden in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zusammengefasst, um Hochverfügbarkeit für virtuelle Computer in Azure besser sicherzustellen. Diese Konfiguration hilft dabei, sicherzustellen, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die von der Azure-SLA zugesicherte Verfügbarkeit von 99,95 % eingehalten wird.

### <a name="logging-and-audit"></a>Protokollierung und Überwachung

**Überwachung:** [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) ist der Plattformdienst, der eine einzelne Quelle zum Überwachen des Aktivitätsprotokolls, der Metriken und der Diagnoseprotokolle aller Ihrer Azure-Ressourcen bereitstellt. Azure Monitor kann zum Visualisieren, Abfragen, Weiterleiten und Archivieren der Metriken und Protokolle konfiguriert werden, die aus Ressourcen in Azure stammen, und ggf. auf dieser Grundlage handeln. Es wird empfohlen, die ressourcenbasierte Zugriffssteuerung zum Schützen des Überwachungspfads zu verwenden, um Benutzern nicht die Möglichkeit zum Ändern der Protokolle zu geben.

**Aktivitätsprotokolle:** Konfigurieren Sie [Azure-Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), um Einblicke in die Vorgänge zu geben, die von Ressourcen in Ihrem Abonnement ausgeführt wurden.

**Diagnoseprotokolle:** [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sind alle Protokolle, die von einer Ressource ausgegeben werden. Diese Protokolle können Windows-Ereignissystemprotokolle, Blob-, -Tabellen- und -Warteschlangenprotokolle umfassen.

**Firewallprotokolle:** Application Gateway stellt umfassende Diagnose- und Zugriffsprotokolle bereit. Firewallprotokolle stehen für Application Gateway-Ressourcen zur Verfügung, für die WAF aktiviert ist.

**Protokollarchivierung:** Die Speicherung von Protokolldaten kann so konfiguriert werden, dass für die Archivierung und eine festgelegte Aufbewahrungsdauer in ein zentrales Azure Storage-Konto geschrieben wird. Protokolle können mithilfe von Azure Monitor-Protokolle oder SIEM-Systemen (Security Information & Event Management) von Drittanbietern verarbeitet werden.

### <a name="identity"></a>Identity

**Active Directory Domain Services:** Diese Architektur bietet eine Bereitstellung von Active Directory Domain Services in Azure. Spezifische Empfehlungen zur Implementierung von Active Directory in Azure finden Sie in den folgenden Artikeln:

[Erweitern von Active Directory Domain Services (AD DS) auf Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain)

[Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Azure-Computern](https://msdn.microsoft.com/library/azure/jj156090.aspx)

**Active Directory-Integration:** Als Alternative zu einer dedizierten AD DS-Architektur können Kunden [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity)-Integration oder [Active Directory in Azure als Mitglied einer lokalen Gesamtstruktur](https://docs.microsoft.com/azure/guidance/guidance-ra-identity) verwenden.

### <a name="security"></a>Sicherheit

**Verwaltungssicherheit:** Dieser Blueprint ermöglicht Administratoren das Herstellen von Verbindungen mit dem Verwaltungs-VNET und der Jumpbox mithilfe von RDP von einer vertrauenswürdigen Quelle. Der Netzwerkverkehr für das Verwaltungs-VNet wird mithilfe von NSGs gesteuert. Der Zugriff auf Port 3389 ist auf Datenverkehr von einem vertrauenswürdigen IP-Bereich eingeschränkt, der auf das Subnetz zugreifen kann, das die Jumpbox enthält.

Kunden können sich außerdem für die Verwendung eines [Verwaltungsmodells mit erhöhter Sicherheit](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) entscheiden, um die Umgebung beim Herstellen von Verbindungen mit dem Verwaltungs-VNet und der Jumpbox zu schützen. Es wird empfohlen, dass Kunden mit erhöhtem Sicherheitsbedarf eine [Arbeitsstation mit privilegiertem Zugriff](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) und eine RDGateway-Konfiguration verwendet werden. Die Verwendung von virtuellen Netzwerkappliances und öffentlichen/privaten DMZs bietet weitere Steigerungen der Sicherheit.

**Schützen des Netzwerks:** [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) werden für jedes Subnetz empfohlen, um eine zweite Schutzebene vor eingehendem Verkehr bereitzustellen, der ein fehlerhaft konfiguriertes oder deaktiviertes Gateway umgeht. Beispiel: [Resource Manager-Vorlage für das Bereitstellen einer NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups)

**Schützen öffentlicher Endpunkte:** Das Internetgateway macht Anwendungsdienste über das Internet für Benutzer verfügbar. Datenverkehr, der auf diese Dienste zugreift, wird mithilfe eines [Application Gateways](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) geschützt, das eine Web Application Firewall und HTTPS-Protokollverwaltung bereitstellt.

**IP-Bereiche:** Bei den in der Architektur verwendeten IP-Bereichen handelt es sich um Vorschläge. Kunden sollten ihre eigene Umgebung berücksichtigen und geeignete Bereiche verwenden.

**Hybridkonnektivität:** Die cloudbasierten Workloads sind mit dem lokalen Rechenzentrum durch IPsec-VPN mithilfe von Azure VPN Gateway verbunden. Kunden sollten sicherstellen, dass sie ein geeignetes VPN-Gateway für Verbindungen mit Azure verwenden. Beispiel: [Resource Manager-Vorlage für VPN Gateway](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection) Kunden, die umfangreiche, unternehmenswichtige Workloads mit Big Data-Anforderungen ausführen, sollten eine Hybridnetzwerkarchitektur unter Verwendung von [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) für private Netzwerkkonnektivität mit den Microsoft-Clouddiensten in Erwägung ziehen.

**Trennung von Zuständigkeiten:** Diese Referenzarchitektur trennt die VNETs für Verwaltungsvorgänge und Geschäftsvorgänge. Separate VNets und Subnetze ermöglichen die Verwaltung des Datenverkehrs zwischen Netzwerksegmenten, einschließlich Einschränkungen für eingehenden und ausgehenden Datenverkehr, mithilfe von NSGs und berücksichtigen die bewährten Methoden für [Microsoft-Clouddienste und Netzwerksicherheit](https://docs.microsoft.com/azure/best-practices-network-security).

**Ressourcenverwaltung:** Azure-Ressourcen, wie etwa VMs, VNETs und Lastenausgleichsmodule, werden verwaltet, indem sie in [Azure-Ressourcengruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) zusammengefasst werden. Rollen für die ressourcenbasierte Zugriffssteuerung können anschließend den einzelnen Ressourcengruppen zugewiesen werden, um den Zugriff exklusiv auf autorisierte Benutzer zu beschränken.

**Einschränkungen der Zugriffssteuerung:** Verwenden Sie die [rollenbasierte Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) (Role-Based Access Control, RBAC), um die Ressourcen in Ihrer Anwendung mithilfe von [benutzerdefinierten Rollen](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) zu verwalten. RBAC kann dazu verwendet werden, die Vorgänge einzuschränken, die von DevOps in den einzelnen Schichten ausgeführt werden können. Verwenden Sie beim Erteilen von Berechtigungen den [Ansatz der geringsten Rechte](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Protokollieren Sie alle Verwaltungsvorgänge, und führen Sie regelmäßig Überwachungen durch, um sicherzustellen, dass alle Konfigurationsänderungen auf Planung beruhen.

**Internetzugriff:** Diese Referenzarchitektur verwendet [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) als das dem Internet zugewandte Gateway und als Lastenausgleichsmodul. Für einige Kunden können ferner virtuelle Netzwerkappliances von Drittanbietern zum Schaffen zusätzlichen Schichten an Netzwerksicherheit als Alternative zum [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) sinnvoll sein.

**Azure Security Center**: Das [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) bietet eine zentrale Übersicht über den Sicherheitsstatus von Ressourcen im Abonnement und gibt Empfehlungen, mit deren Hilfe die Gefährdung von Ressourcen verhindert wird. Es kann außerdem verwendet werden, um detailliertere Richtlinien zu aktivieren. Beispielsweise können Richtlinien auf bestimmte Ressourcengruppen angewendet werden, die es dem Unternehmen gestatten, seine Aufstellung maßgerecht an die Gefährdungslage anzupassen. Kunden wird empfohlen, das Azure Security Center in ihrem Azure-Abonnement zu aktivieren.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC-Cloudsicherheitsprinzipien – Dokumentation der Compliance

Der Crown Commercial Service (eine Agentur, die an der Verbesserung der Handels- und Beschaffungsaktivitäten der Regierung arbeitet) hat die Klassifizierung der in den Bereich fallenden Unternehmensclouddienste von Microsoft neu als G-Cloud v6 festgelegt und dabei alle Angebote von Microsoft auf der Stufe OFFICIAL berücksichtigt. Details zu Azure und G-Cloud finden Sie im [Azure UK G-Cloud security assessment summary](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud) (Zusammenfassung der Sicherheitsbewertung von Azure UK G-Cloud).

Dieser Blueprint ist an den 14 Prinzipien zur Cloudsicherheit ausgerichtet, die in den [Cloudsicherheitsprinzipien](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) der NCSC dokumentiert sind, um eine Umgebung sicherzustellen, die als UK-OFFICIAL klassifizierte Workloads unterstützt.

Die [Kundenzuständigkeitsmatrix](https://aka.ms/ukofficial-crm) (Excel-Arbeitsmappe) listet alle 14 Prinzipien der Cloudsicherheit auf, und die Matrix gibt für jedes Prinzip (oder jeden Teilbereich eines Prinzips) an, ob die Implementierung des Prinzips in die Zuständigkeit von Microsoft oder in die des Kunden fällt, oder ob sie von beiden geteilt wird.

Die [Prinzipimplementierungsmatrix](https://aka.ms/ukofficial-iaaswa-pim) (Excel-Arbeitsmappe) listet alle 14 Prinzipien der Cloudsicherheit auf, und die Matrix gibt für jedes Prinzip (oder jeden Teilbereich eines Prinzips), das in der Kundenzuständigkeitsmatrix als in der Kundenzuständigkeit liegend ausgewiesen ist, an, 1) ob das Prinzip durch die Blueprint-Automatisierung implementiert wird und gibt 2) eine Beschreibung, wie die Implementierung die Anforderung(en) des Prinzips abdeckt.

Darüber hinaus hat die CSA (Cloud Security Alliance) die Cloud Control Matrix veröffentlicht, um Kunden bei der Bewertung von Cloudanbietern zu unterstützen und ihnen das Benennen der Fragen zu ermöglichen, die vor der Umstellung auf Clouddienste beantwortet werden sollten. Als Antwort darauf hat Microsoft Azure das CSA Consensus Assessment Initiative Questionnaire ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)) beantwortet, das beschreibt, wie Microsoft den vorgeschlagenen Prinzipien Rechnung trägt.

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Es gibt zwei Methoden, die von bereitstellenden Benutzern zum Bereitstellen dieser Blueprint-Automatisierung verwendet werden können. Die erste Methode verwendet ein PowerShell-Skript, während die zweite Methode das Azure-Portal zum Bereitstellen der Referenzarchitektur nutzt. Ausführlichere Bereitstellungsanweisungen finden Sie [hier](https://aka.ms/ukofficial-iaaswa-repo).

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
