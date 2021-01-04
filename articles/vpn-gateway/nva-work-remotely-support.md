---
title: 'Remotearbeit: Aspekte in Bezug auf virtuelle Netzwerkgeräte für die Remotearbeit | Azure VPN Gateway'
description: Dieser Artikel soll Ihnen helfen, die Aspekte zu verstehen, die Sie beim Arbeiten mit virtuellen Netzwerkgeräten (Network Virtual Appliances, NVAs) in Azure während der COVID-19-Pandemie berücksichtigen sollten.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499678"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Remotearbeit: Aspekte in Bezug auf virtuelle Netzwerkgeräte für die Remotearbeit

>[!NOTE]
>In diesem Artikel wird beschrieben, wie Sie virtuelle Netzwerkgeräte, Azure, Microsoft Network und das Azure-Partnerökosystem nutzen können, um remote zu arbeiten und Netzwerkprobleme zu beheben, die Sie aufgrund der COVID-19-Krise bewältigen müssen.
>

Einige Azure-Kunden nutzen virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) von Drittanbietern aus Azure Marketplace, um für Ihre Mitarbeiter, die während der COVID-19-Epidemie von Hause aus arbeiten, kritische Dienste wie Point-to-Site-VPN bereitzustellen. In diesem Artikel werden einige allgemeine Punkte erörtert, die bei der Verwendung von NVAs in Azure zum Bereitstellen von Remotezugriffslösungen zu berücksichtigen sind.

## <a name="nva-performance-considerations"></a>Aspekte zur NVA-Leistung

Alle führenden NVA-Anbieter in Azure Marketplace sollten Empfehlungen zu VM-Größen und zur Anzahl von Instanzen angeben, die bei der Bereitstellung ihrer Lösungen verwendet werden sollen.  Obwohl Ihnen fast alle NVA-Anbieter die Möglichkeit bieten, jede Größe auszuwählen, die Ihnen in einer bestimmten Region zur Verfügung steht, ist es sehr wichtig, dass Sie die Empfehlungen des Anbieters für die Größe der Azure VM-Instanzen befolgen, weil es sich bei diesen Empfehlungen um die VM-Größen handelt, mit denen der Anbieter Leistungstests in Azure durchgeführt hat.  

### <a name="consider-the-following"></a>Beachten Sie Folgendes:

- **Kapazität und Anzahl gleichzeitiger Benutzer**: Dies ist für Point-to-Site-VPN-Benutzer besonders wichtig, weil jeder verbundene Benutzer einen verschlüsselten Tunnel (vom Typ IPSec-VPN oder SSL-VPN) erstellt.  
- **Aggregierter Durchsatz**: Die aggregierte Bandbreite, die Sie für das Bereitstellen von Remotezugriff für die erforderliche Anzahl von Benutzern benötigen.
- **Die erforderliche VM-Größe**: Sie sollten immerdie vom NVA-Anbieter empfohlenen VM-Größen verwenden.  Für Point-to-Site-VPN gilt Folgendes: Bei vielen gleichzeitigen Benutzerverbindungen sollten Sie größere VM-Größen (z. B. VMs der [Dv2- und DSv2-Serie](../virtual-machines/dv2-dsv2-series.md "Dv2- und Dsv2-Serie")) verwenden. Diese VMs verfügen tendenziell über mehr vCPUs und können eine größere Anzahl gleichzeitiger VPN-Sitzungen verarbeiten.  Zusätzlich zur höheren Anzahl virtueller Kerne verfügen größere VM-Größen in Azure auch über eine höhere aggregierte Bandbreitenkapazität als kleinere VM-Größen.
    > **Wichtig:** Jeder Anbieter/Hersteller nutzt Ressourcen anders.  Wenn Sie nicht genau wissen, welche Instanzgrößen Sie für die geschätzte Benutzerauslastung verwenden sollten, wenden Sie sich direkt an den Softwarehersteller, um eine Empfehlung von ihm zu erhalten.
- **Anzahl von Instanzen**: Wenn Sie von einer großen Anzahl von Benutzern und Verbindungen ausgehen, müssen Sie bedenken, dass es Beschränkungen hinsichtlich der Skalierung Ihrer NVA-Instanzgrößen gibt.  Ziehen Sie in Erwägung, mehrere VM-Instanzen bereitzustellen.
- **IPSec-VPN versus SSL-VPN**: IPSec-VPN-Implementierungen erzielen im Allgemeinen eine bessere Leistung als SSL-VPN-Implementierungen.  
- **Lizenzierung**: Stellen Sie sicher, dass die von Ihnen für die NVA-Lösung erworbenen Softwarelizenzen das plötzliche Wachstum abdecken, das während der COVID-19-Pandemie auftreten kann.  Bei vielen NVA-Lizenzierungsprogrammen ist die von der Lösung unterstützte Anzahl von Verbindungen oder die Bandbreite begrenzt.
- **Beschleunigter Netzwerkbetrieb**: Ziehen Sie eine NVA-Lösung mit Unterstützung für beschleunigten Netzwerkbetrieb in Betracht.  Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) in einer VM und somit eine erhebliche Steigerung der Netzwerkleistung. Über diesen Hochleistungspfad wird der Host des Datenpfads umgangen, um Latenzen, Jitter und CPU-Auslastung zu verringern. So können mit unterstützten VM-Typen die anspruchsvollsten Netzwerkworkloads genutzt werden. Der beschleunigte Netzwerkbetrieb wird für die meisten Größen universeller, computeoptimierter Instanzen mit mindestens zwei vCPUs unterstützt.

## <a name="monitoring-resources"></a>Überwachen von Ressourcen

Jede NVA-Lösung verfügt über eigene Tools und Ressourcen zum Überwachen der Leistung des jeweiligen virtuellen Netzwerkgeräts.  Lesen Sie die Dokumentation des jeweiligen Herstellers, um sicherzustellen, dass Sie die Leistungsbeschränkungen verstehen und erkennen können, wann die Kapazität Ihres virtuellen Netzwerkgeräts (fast) erreicht ist.  Darüber hinaus können Sie sich Azure Monitor Network Insights ansehen und grundlegende Leistungsinformationen zu Ihren virtuellen Netzwerkgeräten anzeigen, beispielsweise:

- CPU-Auslastung
- Netzwerk eingehend
- Netzwerk ausgehend
- Eingehende Datenflüsse
- Ausgehende Datenflüsse

## <a name="next-steps"></a>Nächste Schritte

Die meisten wichtigen NVA-Partner haben Orientierungshilfen zur Skalierung bei einem plötzlichen, unerwarteten Wachstum während der COVID-19-Pandemie veröffentlicht. Nachfolgend finden Sie einige nützliche Links zu Partnerressourcen.

[Barracuda: Arbeiten von zu Hause aus bei gleichzeitigem Schutz Ihrer Daten während der COVID-19-Pandemie](https://www.barracuda.com/covid-19/work-from-home "Arbeiten von zu Hause aus bei gleichzeitigem Schutz Ihrer Daten während der COVID-19-Pandemie")

[Check Point – Geschützte Remotemitarbeiter während Corona](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Geschützte Remotemitarbeiter während Corona")

[Cisco: Referenz zur Implementierung, Leistung und Skalierung von AnyConnect für die Vorbereitung auf COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco: Referenz zur Implementierung, Leistung und Skalierung von AnyConnect für die Vorbereitung auf COVID-19")

[Citrix: COVID-19 Response Support Center](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix: COVID-19 Response Support Center")

[F5-Unterstützung zur Bewältigung der dramatischen Zunahme an Remotemitarbeitern](https://www.f5.com/business-continuity "F5-Unterstützung zur Bewältigung der dramatischen Zunahme an Remotemitarbeitern")

[COVID-19: Updates für Kunden und Partner von Fortinet](https://www.fortinet.com/covid-19.html "COVID-19: Updates für unsere Kunden und Partner")

[Palo Alto Networks: COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks: COVID-19 Response Center")

[Kemp: Remote-Arbeit und Always-On-App-Erfahrung für Geschäftskontinuität](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp: Remote-Arbeit und Always-On-App-Erfahrung für Geschäftskontinuität")

