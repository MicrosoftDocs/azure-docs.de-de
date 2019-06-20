---
title: Übersicht über Azure DDoS Protection Standard| Microsoft-Dokumentation
description: Informationen zum Azure DDoS Protection-Dienst
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209462"
---
# <a name="azure-ddos-protection-standard-overview"></a>Übersicht über Azure DDoS Protection Standard

DDoS-Angriffe (Distributed Denial of Service) stellen eines der größten Verfügbarkeits- und Sicherheitsprobleme für Kunden dar, die ihre Anwendungen in die Cloud verschieben. Ein DDoS-Angriff hat das Ziel, die Ressourcen einer Anwendung zu verbrauchen, damit sie für berechtigte Benutzer nicht mehr verfügbar ist. Jeder Endpunkt, der öffentlich über das Internet erreichbar ist, kann Ziel von DDoS-Angriffen werden.

Azure DDoS Protection in Kombination mit bewährten Anwendungsentwurfsmethoden stellt den bestmöglichen Schutz gegen DDoS-Angriffe dar. Azure DDoS Protection bietet die folgenden Dienstebenen:

- **Basic**: Als Teil der Azure-Plattform automatisch aktiviert. Die stets verfügbare Überwachung des Datenverkehrs und die Abwehr von häufig vorkommenden Angriffen auf Netzwerkebene in Echtzeit bieten die gleichen Schutzmaßnahmen wie die Onlinedienste von Microsoft. Das gesamte weltweite Netzwerk von Azure steht für die Verteilung und Abwehr des regionsübergreifenden Angriffsdatenverkehrs zur Verfügung. Schutz wird für [öffentliche Azure-IP-Adressen](virtual-network-public-ip-address.md) mit IPv4 und IPv6 bereitgestellt.
- **Standard**: Stellt zusätzliche Funktionen zur Angriffsabwehr über die Basic-Dienstebene bereit, die speziell für virtuelle Azure-Netzwerkressourcen optimiert sind. DDoS Protection Standard kann leicht aktiviert werden und erfordert keine Änderung der Anwendung. Schutzrichtlinien werden über dedizierte Datenverkehrsüberwachung und Machine Learning-Algorithmen optimiert. Richtlinien werden auf öffentliche IP-Adressen angewendet, die in virtuellen Netzwerken bereitgestellten Ressourcen wie Azure Load Balancer, Azure Application Gateway und Azure Service Fabric-Instanzen zugeordnet sind. Dieser Schutz wird jedoch nicht auf App Service-Umgebungen angewendet. Über Azure Monitor-Ansichten steht Echtzeittelemetrie während eines Angriffs und für den Verlauf zur Verfügung. In den Diagnoseeinstellungen sind umfassende Analysefunktionen zur Entschärfung von Angriffen verfügbar. Der Schutz auf der Anwendungsschicht kann über die [Azure Application Gateway Web Application Firewall](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder per Installation einer Drittanbieter-Firewall über Azure Marketplace hinzugefügt werden. Schutz wird für [öffentliche Azure-IP-Adressen](virtual-network-public-ip-address.md) mit IPv4 und IPv6 bereitgestellt.

![Vergleich: Azure DDoS Protection Basic und Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS-Angriffstypen, die mit DDoS Protection Standard abgewehrt werden

Die folgenden Arten von Angriffen können mit DDoS Protection Standard abgewehrt werden:

- **Volumetrische Angriffe**: Ziel des Angriffs ist die Überflutung der Netzwerkebene mit einer beträchtlichen Menge scheinbar berechtigten Datenverkehrs. Dazu zählen UDP-Überflutungen, Verstärkungsüberflutungen und andere Überflutungen mit gefälschten Paketen. DDoS Protection Standard wehrt diese bis zu mehreren Gigabytes großen Angriffe ab, indem sie mithilfe des weltweiten Netzwerks von Azure automatisch absorbiert und bereinigt werden.
- **Protokollangriffe**: Diese Angriffe machen den Zugriff auf ein Ziel unmöglich, indem sie eine Schwachstelle in den Schichten 3 und 4 des Protokollstapels ausnutzen. Dazu gehören SYN-Flutangriffe, Reflektionsangriffe und andere Protokollangriffe. DDoS Protection Standard wehrt diese Angriffe ab und unterscheidet dabei zwischen schädlichem und berechtigtem Datenverkehr. Nach Interaktion mit dem Client wird der schädliche Datenverkehr gesperrt. 
- **Angriffe in der Ressourcenschicht (Anwendungsschicht)** : Das Ziel dieser Art von Angriffen sind Webanwendungspakete, um die Datenübertragung zwischen Hosts zu unterbrechen. Zu diesen Angriffen zählen Verletzungen des HTTP-Protokolls, die Einschleusung von SQL-Befehlen, XSS-Angriffe (Cross-Site Scripting) und andere Angriffe auf Schicht 7. Verwenden Sie die [Web Application Firewall von Azure Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Kombination mit DDoS Protection Standard zum Schutz vor diesen Angriffen. Im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall) finden Sie auch Webanwendungsfirewall-Angebote von Drittanbietern.

Mit DDoS Protection Standard werden Ressourcen in einem virtuellen Netzwerk geschützt, einschließlich öffentlicher IP-Adressen, die virtuellen Computern zugeordnet sind, interner Lastenausgleichsmodule und Anwendungsgateways. In Kombination mit der Web Application Firewall von Application Gateway stellt DDoS Protection Standard eine vollständige Abwehrfunktion für Ebene 3 bis Ebene 7 bereit.

## <a name="ddos-protection-standard-features"></a>Funktionen von DDoS Protection Standard

![DDoS-Funktionen](./media/ddos-protection-overview/ddosfeatures.png)

Die Funktionen von DDoS Protection Standard umfassen:

- **Native Plattformintegration:** Nativ in Azure integriert. Umfasst die Konfiguration über das Azure-Portal. DDoS Protection Standard erkennt Ihre Ressourcen und die Ressourcenkonfiguration.
- **Sofort einsetzbarer Schutz:** Dank vereinfachter Konfiguration sind alle Ressourcen in einem virtuellen Netzwerk sofort geschützt, sobald DDoS Protection Standard aktiviert wird. Es sind weder Benutzereingriffe noch Benutzerdefinitionen erforderlich. DDoS Protection Standard wehrt einen Angriff sofort automatisch ab, sobald er entdeckt wurde.
- **Stets verfügbare Überwachung des Datenverkehrs:** Die Datenverkehrsmuster Ihrer Anwendungen werden 24 Stunden am Tag und 7 Tage die Woche nach Anzeichen für DDoS-Angriffe überwacht. Abwehrmaßnahmen werden bei Überschreitung der Schutzrichtlinien ausgeführt.
- **Adaptive Optimierung:** Dank einer intelligenten Profilerstellung lernt die Funktion den Datenverkehr Ihrer Anwendung kontinuierlich besser kennen. Auf dieser Basis wird das Profil ausgewählt und aktualisiert, das am besten zu Ihrem Dienst passt. Das Profil passt sich den Veränderungen des Datenverkehrs mit der Zeit an.
- **Mehrschichtiger Schutz**: Bietet vollständigen DDoS-Stapelschutz bei Verwendung mit einer Web Application Firewall.
- **Umfangreiche Angriffsabwehr:** Über 60 verschiedene Angriffstypen können mit einer weltweiten Kapazität zum Schutz vor den größten bekannten DDoS-Angriffen abgewehrt werden.
- **Angriffsanalysen**: Rufen Sie während eines Angriffs detaillierte Berichte in 5-Minuten-Inkrementen und nach dem Angriff eine vollständige Zusammenfassung ab. Streamen Sie Datenflussprotokolle der Entschärfung an ein Offline-SIEM-System (Security Information and Event Management), um einen Angriff annähernd in Echtzeit zu überwachen.
- **Angriffsmetriken:** Mit Azure Monitor kann auf eine Zusammenfassung der Metriken für jeden Angriff zugegriffen werden.
- **Angriffswarnungen:** Mit integrierten Angriffsmetriken können Warnungen am Anfang und Ende eines Angriffs sowie währenddessen konfiguriert werden. Warnungen werden in Ihre Betriebssoftware wie Microsoft Azure Monitor-Protokolle, Splunk, Azure Storage, E-Mail und das Azure-Portal integriert.
- **Kostengarantie:** Dienstguthaben für Datenübertragung und horizontale Anwendungsskalierung bei dokumentierten DDoS-Angriffen.

## <a name="ddos-protection-standard-mitigation"></a>Abwehrfunktion von DDoS Protection Standard

DDoS Protection Standard überwacht die tatsächliche Auslastung des Datenverkehrs und vergleicht sie ständig mit den Schwellenwerten der DDoS-Richtlinie. Bei Überschreitung des Schwellenwerts für den Datenverkehr wird die DDoS-Abwehr automatisch eingeleitet. Sinkt der Datenverkehr wieder unter den Schwellenwert, wird die Abwehr beendet.

![Lösung](./media/ddos-protection-overview/mitigation.png)

Während des Abwehrvorgangs wird der an die geschützte Ressource gesendete Datenverkehr von DDoS Protection umgeleitet und mehrfach überprüft, wie etwa folgendermaßen:

- Sicherstellen, dass Pakete den Internetspezifikationen entsprechen und nicht falsch formatiert sind.
- Interagieren Sie mit dem Client, um zu bestimmen, ob es sich bei dem Datenverkehr möglicherweise um ein gefälschtes Paket handelt (Beispiel: SYN-Authentifizierung oder SYN-Cookie oder durch Verwerfen eines Pakets, damit die Quelle es erneut übermittelt).
- Übertragungsratenlimits für Pakete, wenn keine andere Erzwingungsmethode ausgeführt werden kann.

DDoS Protection sperrt Angriffsdatenverkehr und leitet den verbleibenden Datenverkehr an das vorgesehene Ziel weiter. Innerhalb weniger Minuten nach Angriffserkennung werden Sie mithilfe der Metriken von Azure Monitor benachrichtigt. Durch Konfigurieren der Protokollierung der DDoS Protection-Telemetrie können Sie für eine zukünftige Analyse die Protokolle in die verfügbaren Optionen schreiben. Metrische Daten werden in Azure Monitor für DDoS Protection Standard 30 Tage lang beibehalten.

Microsoft hat mit [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) eine Partnerschaft zum Erstellen einer Schnittstelle gebildet, durch die Sie für Simulationen Datenverkehr für mit DDoS Protection geschützte öffentliche Endpunkte generieren können. Die BreakPoint Cloud-Simulation erlaubt Ihnen Folgendes:

- Überprüfen, wie Microsoft Azure DDoS Protection Standard Ihre Azure-Ressourcen vor DDoS-Angriffen schützt
- Optimieren Ihres Prozesses der Reaktion auf Incidents während DDoS-Angriffen
- Dokumentieren der DDoS-Kompatibilität
- Schulen Ihrer Netzwerksicherheitsteams

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von DDoS Protection Standard](manage-ddos-protection.md)
