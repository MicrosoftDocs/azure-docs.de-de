---
title: Sichere Azure-Computing-Architektur
description: Diese Referenzarchitektur für eine DMZ-Architektur auf Unternehmensebene verwendet virtuelle Netzwerkgeräte und weitere Tools. Diese Architektur wurde so entwickelt, dass sie die Funktionsanforderungen des US-Verteidigungsministeriums an eine sichere Cloud Computing-Architektur (Secure Cloud Computing Architecture, SCCA) erfüllt. Sie kann aber auch für jede andere Organisation verwendet werden. Diese Referenz enthält zwei automatisierte Optionen, die Citrix- oder F5-Geräte verwenden.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963242"
---
# <a name="secure-azure-computing-architecture"></a>Sichere Azure-Computing-Architektur

Daten Kunden des US-Verteidigungsministeriums (Department of Defense, DoD), die Workloads in Azure bereitstellen, haben um eine Anleitung gebeten, um sichere virtuelle Netzwerke einzurichten und die Sicherheitstools und -dienste zu konfigurieren, die von den DoD-Standards und -Vorgaben vorgeschrieben werden. 

Die Defense Information System Agency (DISA) hat 2017 das [Secure Cloud Computing Architecture (SCCA) Functional Requirements Document (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) (Dokument mit Funktionsanforderungen für eine sichere Cloud Computing-Architektur) herausgegeben. Eine Secure Cloud Computing Architecture (SCCA) beschreibt die funktionalen Ziele beim Sichern der Verbindungspunkte des Defense Information System Network (DISN) und der kommerziellen Cloudanbieter. Die SCCA beschreibt auch, wie Projektbesitzer Cloudanwendungen an der Verbindungsgrenze sichern. Jede DoD-Entität, die eine Verbindung mit der kommerziellen Cloud herstellt, muss die im SCCA FRD festgelegten Richtlinien einhalten.
 
Die SCCA besteht aus vier Komponenten:
 
- Boundary Cloud Access Point (BCAP, Zugriffspunkt an Cloudgrenze)
- Virtual Datacenter Security Stack (VDSS, Sicherheitsstapel für virtuelles Rechenzentrum)
- Virtual Datacenter Managed Service (VDMS, verwalteter Dienst für virtuelles Rechenzentrum)
- Trusted Cloud Credential Manager (TCCM, Manager für Anmeldeinformationen für eine vertrauenswürdige Cloud) 

Microsoft hat eine Lösung entwickelt, mit der sich die SCCA-Anforderungen sowohl für IL4- als auch für IL5-Workloads erfüllen lassen, die in Azure ausgeführt werden. Diese Azure-spezifische Lösung wird als Secure Azure Computing Architecture (SACA, sichere Azure-Computing-Architektur) bezeichnet. Kunden, die die SACA bereitstellen, erfüllen die Anforderungen des SCCA FRD. Sie können es DoD-Kunden ermöglichen, nach dem Herstellen einer Verbindung Workloads nach Azure zu verlagern.

SCCA-Leitfäden und -Architekturen gelten zwar speziell für DoD-Kunden, aber die neuesten Revisionen der SACA helfen auch zivilen Kunden dabei, Anforderungen an vertrauenswürdige Internetverbindungen (Trusted Internet Connection, TIC) zu erfüllen. Die neuesten Revisionen helfen auch kommerziellen Kunden, die eine sichere DMZ implementieren möchten, um ihre Azure-Umgebungen zu schützen.


## <a name="secure-cloud-computing-architecture-components"></a>Komponenten der SCCA

### <a name="bcap"></a>BCAP

Der Zweck der Komponente BCAP besteht darin, das DISN vor Angriffen zu schützen, die aus der Cloudumgebung stammen. BCAP führt Funktionen zum Erkennen und Verhindern von Angriffen aus. Die Komponente filtert auch nicht autorisierten Datenverkehr heraus. BCAP kann mit anderen Komponenten der SCCA gemeinsam bereitgestellt werden. Es wird empfohlen, diese Komponente unter Verwendung physischer Hardware bereitzustellen. Die BCAP-Sicherheitsanforderungen sind in der folgenden Tabelle aufgelistet.

#### <a name="bcap-security-requirements"></a>BCAP-Sicherheitsanforderungen

![Matrix der BCAP-Anforderungen](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

Der Zweck der Komponente VDSS besteht darin, in Azure gehostete Anwendungen von DoD-Projektbesitzern zu schützen. VDSS führt den größten Teil der Sicherheitsvorgänge in der SCCA aus. Die Komponente überprüft den Datenverkehr, um die in Azure ausgeführten Anwendungen zu sichern. Diese Komponente kann in Ihrer Azure-Umgebung bereitgestellt werden.

#### <a name="vdss-security-requirements"></a>VDSS-Sicherheitsanforderungen

![Matrix der VDSS-Anforderungen](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

Der Zweck von VDMS ist es, Hostsicherheit und gemeinsam genutzte Rechenzentrumsdienste bereitzustellen. Die Funktionen von VDMS können im Hub Ihrer SCCA ausgeführt werden, oder der Projektbesitzer kann Teile davon in seinem eigenen Azure-Abonnement bereitstellen. Diese Komponente kann in Ihrer Azure-Umgebung bereitgestellt werden.

#### <a name="vdms-security-requirements"></a>VDMS-Sicherheitsanforderungen

![Matrix der VDMS-Anforderungen](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM ist eine geschäftliche Rolle. Diese Person ist für die Verwaltung der SCCA verantwortlich. Diese Person muss folgende Pflichten erfüllen: 

- Einrichten von Plänen und Richtlinien für den Kontozugriff auf die Cloudumgebung 
- Sicherstellen, dass die Identitäts- und Zugriffsverwaltung ordnungsgemäß ausgeführt wird 
- Pflegen des Plans für die Verwaltung von Anmeldeinformationen 

Diese Person wird von einem autorisierenden Organ benannt. Die Komponenten BCAP, VDSS und VDMS stellen die Funktionen bereit, die der TCCM für seine Aufgaben benötigt.

#### <a name="tccm-security-requirements"></a>TCCM-Sicherheitsanforderungen

![Matrix der TCCM-Anforderungen](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACA-Komponenten und Überlegungen zur Planung 

Die SACA-Referenzarchitektur dafür konzipiert, die Komponenten VDSS und VDMS in Azure bereitzustellen und der Person mit der TCCM-Rolle die Durchführung ihrer Aufgaben zu ermöglichen. Diese Architektur ist modular aufgebaut. Alle Teile von VDSS und VDMS können sich in einem zentralen Hub befinden. Einige der Kontrollelemente können sich im Bereich des Projektbesitzers oder sogar an einem lokalen Speicherort befinden. Microsoft empfiehlt, die Komponenten VDSS und VDMS gemeinsam in einem zentralen virtuellen Netzwerk bereitzustellen, mit dem alle Projektbesitzer eine Verbindung herstellen können. Das folgende Diagramm veranschaulicht diese Architektur: 


![Diagramm zur SACA-Referenzarchitektur](media/sacav2generic.png)

Wenn Sie Ihre SCCA-Compliancestrategie und die technische Architektur planen, berücksichtigen Sie von Beginn an die folgenden Themen, da diese sich auf jeden Kunden auswirken. Die folgenden Probleme sind bei DoD-Kunden aufgetreten und können die Planung und Ausführung verlangsamen. 

#### <a name="which-bcap-will-your-organization-use"></a>Welche BCAP-Version wird Ihre Organisation verwenden?
   - BCAP der DISA:
        - Die DISA betreibt zwei funktionsfähige BCAPs im Pentagon und in Camp Roberts (Kalifornien). Ein dritter Zugriffspunkt soll in Kürze online geschaltet werden. 
        - Alle BCAPs der DISA verfügen über ExpressRoute-Leitungen zu Azure, über die DoD-Kunden eine Verbindung herstellen können. 
        - Die DISA bietet zwei Microsoft-Peeringsitzungen auf Unternehmensebene für DoD-Kunden, die Microsoft-SaaS-Tools (Software-as-a-Service) wie z.B. Office 365 abonnieren möchten. Durch Nutzung des BCAP von DISA können Sie Konnektivität und Peering für Ihre SACA-Instanz ermöglichen. 
    - Erstellen eines eigenen BCAP:
        - Mit dieser Option können Sie Speicherplatz in einem Rechenzentrum für die gemeinsame Bereitstellung leasen und eine ExpressRoute-Leitung zu Azure einrichten. 
        - Diese Option erfordert zusätzliche Genehmigungen. 
        - Aufgrund der erforderlichen zusätzlichen Genehmigungen und der physischen Einrichtung dauert diese Option am längsten. 
    - Wir empfehlen die Nutzung des BCAP der DISA. Diese Option ist sofort einsatzbereit, bietet integrierte Redundanz und wird bereits heute von Kunden in der Produktion genutzt.
- Routingfähiger IP-Adressraum des DoD:
    - Sie müssen einen routingfähigen IP-Adressraum des DoD am Edge verwenden. Es gibt eine Option, diese Adressräume über NAT mit einem privaten IP-Adressraum in Azure zu verbinden.
    - Wenden Sie sich an das DoD Network Information Center, um einen IP-Adressraum zu beziehen. Sie benötigen diesen im Rahmen Ihrer SNAP-Einreichung (System/Network Approval Process) bei der DISA. 
    - Wenn Sie planen, NAT zum Herstellen einer Verbindung mit einem privaten Adressraum in Azure zu verwenden, benötigen Sie mindestens ein /24-Subnetz eines Adressraums – zugewiesen vom Network Information Center für jede Region, in der Sie die SACA bereitstellen möchten.
- Redundanz:
    - Stellen Sie in mindestens zwei Regionen eine SACA-Instanz bereit. In der DoD-Cloud stellen Sie die Instanz in beiden verfügbaren DoD-Regionen bereit.
    - Verbinden Sie mindestens zwei BCAPs über separate ExpressRoute-Leitungen. Beide ExpressRoute-Verbindungen können dann mit der jeweiligen SACA-Instanz in jeder Region verknüpft werden. 
- Anforderungen speziell für DoD-Komponenten:
    - Hat Ihre Organisation besondere Anforderungen außerhalb der SCCA-Anforderungen? Einige Organisationen haben bestimmte IPS-Anforderungen.
- SACA ist eine modulare Architektur:
    - Verwenden Sie nur die Komponenten, die Sie für Ihre Umgebung benötigen. 
        - Stellen Sie virtuelle Netzwerkgeräte auf einer oder mehreren Ebenen bereit.
        - Verwenden Sie ein integriertes IPS oder ein BYO-IPS.
- DoD Impact Level (IL, Auswirkungsstufe) Ihrer Anwendungen und Daten:
    - Wenn es die Möglichkeit gibt, Anwendungen in Microsoft-IL5-Regionen auszuführen, erstellen Sie Ihre SACA-Instanz in IL5. Die Instanz kann vor IL4-Anwendungen und IL5 verwendet werden. Eine IL4-SACA-Instanz vor einer IL5-Anwendung erhält höchstwahrscheinlich keine Akkreditierung.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Welchen Anbieter für virtuelle Netzwerkgeräte werden Sie für VDSS verwenden?
Wie bereits erwähnt, können Sie die SACA-Referenz mit einer Vielzahl von virtuellen Geräten und Azure-Diensten aufbauen. Microsoft bietet automatisierte Lösungsvorlagen zum Bereitstellen der SACA-Architektur sowohl mit F5 als auch mit Citrix. Diese Lösungen werden im folgenden Abschnitt behandelt.

#### <a name="which-azure-services-will-you-use"></a>Welche Azure-Dienste werden Sie verwenden?
- Es gibt Azure-Dienste, die die Anforderungen für Protokollanalyse, hostbasierten Schutz und IDS-Funktionen erfüllen können. Es ist möglich, dass einige Dienste in Microsoft-IL5-Regionen nicht allgemein verfügbar sind. In diesem Fall müssen Sie möglicherweise Drittanbietertools nutzen, wenn die entsprechenden Azure-Dienste Ihre Anforderungen nicht erfüllen. Nutzen Sie die Tools, mit denen Sie vertraut sind, und ermitteln Sie, ob Sie native Azure-Tools verwenden können.
- Wir empfehlen, so viele native Azure-Tools wie möglich zu verwenden. Diese wurden speziell im Hinblick auf Cloudsicherheit konzipiert und lassen sich nahtlos in den Rest der Azure-Plattform integrieren. Verwenden Sie die nativen Azure-Tools in der folgenden Liste, um verschiedene Anforderungen der SCCA zu erfüllen:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure Front Door](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure-Sicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Festlegen der Größe
    - Die Größe der Umgebung muss festgelegt werden. Sehen Sie sich die Anzahl von gleichzeitigen Verbindungen an, die möglicherweise über die SACA-Instanz eingerichtet werden, sowie die Anforderungen an den Netzwerkdurchsatz. 
    - Dieser Schritt ist von entscheidender Bedeutung. Hierbei wird die Größe der VMs festgelegt, und es werden die Lizenzen identifiziert, die bei den verschiedenen Anbietern erworben werden müssen, die Sie in Ihrer SACA-Instanz verwenden. 
    - Eine vernünftige Kostenanalyse ist ohne diesen Dimensionierungsschritt nicht möglich. Eine korrekte Dimensionierung unterstützt zudem eine optimale Leistung. 


## <a name="most-common-deployment-scenario"></a>Das gängigste Bereitstellungsszenario

 Viele Microsoft-Kunden haben bereits eine vollständige Bereitstellung durchgeführt oder befinden sich zumindest in der Planungsphase für ihre SACA-Umgebungen. Aus den Erfahrungen dieser Kunden konnten wichtige Erkenntnisse in Bezug auf das gängigste Bereitstellungsszenario gewonnen werden. Das folgende Diagramm zeigt die gängigste Architektur: 


![Diagramm zur SACA-Referenzarchitektur](media/sacav2commonscenario.png) 


Wie Sie im Diagramm sehen, abonnieren DoD-Kunden in der Regel zwei der DISA-BCAPs. Einer davon befindet sich an der Westküste, der andere an der Ostküste. An jedem DISA-BCAP-Standort wird ein privater ExpressRoute-Peer zu Azure aktiviert. Diese ExpressRoute-Peers werden mit dem Gateway des virtuellen Netzwerks in den Azure-Regionen „DoD, Osten“ und „DoD, Mitte“ verknüpft. In den Azure-Regionen „DoD, Osten“ und „DoD, Mitte“ wird eine SACA-Instanz bereitgestellt. Der gesamte eingehende und ausgehende Datenverkehr wird durch diese Instanz und von der ExpressRoute-Verbindung zum DISA-BCAP geleitet.

Projektbesitzer wählen dann die Azure-Region aus, in der sie ihre Anwendungen bereitstellen möchten. Sie verwenden das Peering virtueller Netzwerke, um das virtuelle Netzwerk ihrer Anwendung mit dem virtuellen SACA-Netzwerk zu verbinden. Dann erzwingen sie einen Tunnel für den gesamten Datenverkehr, der die VDSS-Instanz passiert.

Wir empfehlen diese Architektur, weil sie die SCCA-Anforderungen erfüllt. Sie ist hoch verfügbar und lässt sich ganz einfach skalieren. Sie vereinfacht auch die Bereitstellung und Verwaltung.

## <a name="automated-saca-deployment-options"></a>Optionen für eine automatisierte SACA-Bereitstellung

 Wie bereits erwähnt, hat Microsoft zusammen mit zwei Anbietern je eine automatisierte Vorlage für die SACA-Infrastruktur erstellt. Beide Vorlagen stellen die folgenden Azure-Komponenten bereit: 

- Virtuelles SACA-Netzwerk
    - Verwaltungssubnetz
        - In diesem Subnetz werden Verwaltungs-VMs und -Dienste bereitgestellt, die auch als „Jumpboxes“ bezeichnet werden.
        - VDMS-Subnetz
            - In diesem Subnetz werden die VMs und Dienste bereitgestellt, die für VDMS verwendet werden.
        - Vertrauenswürdige und nicht vertrauenswürdige Subnetze
            - In diesen Subnetzen werden virtuelle Geräte bereitgestellt.
        - Gatewaysubnetz
            - In diesem Subnetz wird das ExpressRoute-Gateway bereitgestellt.
- Jumpbox-VMs für die Verwaltung
    - Diese werden für die Out-of-Band-Verwaltung der Umgebung verwendet.
- Virtuelle Netzwerkgeräte
    - Sie verwenden entweder Citrix oder F5, je nachdem, welche Vorlage Sie bereitstellen.
- Öffentliche IP-Adressen
    - Diese werden für das Front-End verwendet, bis ExpressRoute online geschaltet wird. Diese IPs werden in den privaten Azure-Adressraum des Back-Ends übersetzt.
- Routentabellen 
    - Diese Routentabellen werden während der Automatisierung angewendet und erzwingen einen Tunnel für den gesamten Datenverkehr, der das virtuelle Gerät passiert.
- Azure Load Balancers – Standard-SKU
    - Diese werden zum Lastenausgleich des Datenverkehrs über alle Geräte hinweg verwendet.
- Netzwerksicherheitsgruppen
    - Hiermit wird gesteuert, welche Arten von Datenverkehr an bestimmte Endpunkte weitergeleitet werden können.


### <a name="citrix-saca-deployment"></a>SACA-Bereitstellung mit Citrix

Eine Citrix-Bereitstellungsvorlage stellt hoch verfügbare Citrix ADC-Geräte auf zwei Ebenen bereit. Diese Architektur erfüllt die Anforderungen von VDSS. 

![SACA-Diagramm mit Citrix](media/citrixsaca.png)


Die Dokumentation und das Bereitstellungsskript für Citrix finden Sie unter [diesem GitHub-Link](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>SACA-Bereitstellung mit F5

Zwei verschiedene F5-Bereitstellungsvorlagen decken zwei verschiedene Architekturen ab. Die erste Vorlage bietet nur eine Ebene von F5-Geräten in einer hoch verfügbaren Aktiv-Aktiv-Konfiguration. Diese Architektur erfüllt die Anforderungen für VDSS. Die zweite Vorlage fügt eine zweite Ebene hoch verfügbarer Aktiv-Aktiv-F5-Geräte hinzu. Mit dieser zweiten Vorlage können Kunden unabhängig von F5 ein eigenes IPS zwischen den F5-Ebenen hinzufügen. Nicht alle DoD-Komponenten schreiben ein bestimmtes IPS vor. Wenn dies der Fall ist, funktioniert die einzelne Ebene mit F5-Geräten für die meisten Kunden, da diese Architektur IPS-Funktionen auf den F5-Geräten einschließt.

![SACA-Diagramm mit F5](media/f5saca.png)

Die Dokumentation und das Bereitstellungsskript für F5 finden Sie unter [diesem GitHub-Link](https://github.com/f5devcentral/f5-azure-saca).












