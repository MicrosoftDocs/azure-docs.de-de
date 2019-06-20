---
title: Sicherheitsfeatures für Azure SQL-Datenbank
description: Dieser Artikel beschreibt allgemein, wie Azure SQL-Datenbank Kundendaten in Azure schützt.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62121655"
---
# <a name="azure-sql-database-security-features"></a>Sicherheitsfeatures für Azure SQL-Datenbank    
Azure SQL-Datenbank stellt einen relationalen Datenbankdienst in Azure bereit. Zum Schutz von Kundendaten und zum Bereitstellen verlässlicher Sicherheitsfeatures, die Kunden von einem relationalen Datenbankdienst erwarten, verfügt SQL-Datenbank über eigene Sicherheitsfunktionen. Diese Funktionen bauen auf den Steuerelementen auf, die von Azure geerbt werden.

## <a name="security-capabilities"></a>Sicherheitsfunktionen

### <a name="usage-of-the-tds-protocol"></a>Verwendung des TDS-Protokolls
Azure SQL-Datenbank unterstützt nur das TDS-Protokoll (Tabular Data Stream), das erfordert, dass ausschließlich über den Standardport TCP/1433 auf die Datenbank zugegriffen werden kann.

### <a name="azure-sql-database-firewall"></a>Firewall von Azure SQL-Datenbank
Um die Kundendaten zu schützen, bietet Azure SQL-Datenbank eine Firewallfunktion, die standardmäßig jeglichen Zugriff auf den SQL-Datenbank-Server verhindert (wie unten gezeigt).

![Firewall von Azure SQL-Datenbank][1]

Die Gatewayfirewall kann Adressen einschränken, wodurch Kunden zulässige IP-Adressbereich differenziert kontrollieren können. Die Firewall gewährt Zugriff basierend auf der Ursprungs-IP-Adresse der jeweiligen Anforderung.

Kunden können die Firewall über ein Verwaltungsportal oder programmgesteuert mithilfe der REST-API für die Azure SQL-Datenbank-Verwaltung konfigurieren. Standardmäßig verweigert die Firewall des Azure SQL-Datenbank-Gateways allen Kunden den TDS-Zugriff auf Azure SQL-Datenbank-Instanzen. Kunden müssen den Zugriff mithilfe von Zugriffssteuerungslisten konfigurieren, um Azure SQL-Datenbank-Verbindungen anhand von Quell- und Zieladressen, Protokollen und Portnummern zuzulassen.

### <a name="dosguard"></a>DoSGuard
DoS-Angriffe (Denial of Service) werden durch einen SQL-Datenbank-Gatewaydienst namens DoSGuard reduziert. DoSGuard überwacht aktiv fehlgeschlagene Anmeldungen von IP-Adressen. Wenn mehrere fehlerhafte Anmeldungen von einer spezifischen IP-Adresse innerhalb eines bestimmten Zeitraums auftreten, wird der IP-Adresse der Zugriff auf alle Ressourcen im Dienst für einen vordefinierten Zeitraum blockiert.

Darüber hinaus führt das Azure SQL-Datenbank-Gateway folgende Vorgänge aus:

- Sichere Kanalfunktionsaushandlungen, um bei der Verbindung mit Datenbankservern überprüfte verschlüsselte TDS FIPS 140-2-Verbindungen zu implementieren.
- Überprüfung zustandsbehafteter TDS-Pakete, während Verbindungen von Clients akzeptiert werden. Das Gateway überprüft die Verbindungsinformationen und übergibt die TDS-Pakete an den entsprechenden physischen Server – basierend auf dem Datenbanknamen, der in der Verbindungszeichenfolge angegeben wurde.

Das übergreifende Prinzip für die Netzwerksicherheit von Azure SQL-Datenbank besteht darin, ausschließlich die für den Betrieb des Diensts erforderliche Verbindung und Kommunikation zu erlauben. Alle anderen Ports, Protokolle und Verbindungen werden standardmäßig blockiert. VLANs und ACLs werden verwendet, um die Netzwerkkommunikation anhand von Quell- und Zielnetzwerken, Protokollen und Portnummern einzuschränken.

Zu den genehmigten Mechanismen zum Implementieren von netzwerkbasierten ACLs gehören ACLs auf Routern und Lastenausgleichsmodule. Diese Mechanismen werden durch Regeln für Azure-Netzwerke, Gast-VM-Firewalls und Azure SQL-Datenbank-Gatewayfirewalls verwaltet, die vom Kunden konfiguriert werden.

## <a name="data-segregation-and-customer-isolation"></a>Trennung von Daten und Isolierung von Kunden
Das Azure-Produktionsnetzwerk ist so strukturiert, dass öffentlich zugängliche Systemkomponenten von internen Ressourcen getrennt sind. Physische und logische Grenzen existieren zwischen Webservern, die Zugriff auf das öffentliche Azure-Portal und die zugrunde liegende virtuelle Azure-Infrastruktur gewähren, in der sich Anwendungsinstanzen von Kunden sowie Kundendaten befinden.

Alle öffentlichen zugänglichen Informationen werden innerhalb des Azure-Produktionsnetzwerks verwaltet. Das Produktionsnetzwerk unterliegt der zweistufigen Authentifizierung und Schutzmechanismen für Grenzen, verwendet die im vorherigen Abschnitt beschriebenen Firewall- und Sicherheitsfeatures und nutzt die in den folgenden Abschnitten beschriebenen Funktionen zum Isolieren von Daten.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Nicht autorisierte Systeme und Isolation des FC
Da der Fabric Controller (FC) der zentrale Orchestrator von Azure-Fabric ist, sind zentrale Steuerelemente zur Minderung von Bedrohungen vorhanden, insbesondere durch potenziell gefährdete FAs in Kundenanwendungen. Der FC erkennt keine Hardware, deren Geräteinformationen (z.B. die MAC-Adresse) nicht vorab im FC geladen wurden. Die DHCP-Server auf dem FC über konfigurierte Listen von MAC-Adressen der Knoten verfügen, die sie starten möchten. Selbst wenn nicht autorisierte Systeme eine Verbindung herstellten, werden diese nicht in den Fabricbestand aufgenommen und sind daher nicht dazu autorisiert, mit einem anderen System im Fabricbestand zu kommunizieren. Damit wird das Risiko reduziert, dass nicht autorisierte Systeme mit dem FC kommunizieren und Zugriff auf das VLAN und auf Azure erhalten.

### <a name="vlan-isolation"></a>VLAN-Isolation
Das Azure-Produktionsnetzwerk ist in drei primäre VLAN-Netzwerke logisch getrennt:

- Haupt-VLAN: Dient zum Verbinden nicht vertrauenswürdiger Kundenknoten.
- FC-VLAN: Enthält vertrauenswürdige FCs und unterstützende Systeme.
- Geräte-VLAN: Enthält vertrauenswürdige Netzwerk- und andere Infrastrukturgeräte.

### <a name="packet-filtering"></a>Paketfilterung
Die IP-Filter und Softwarefirewalls, die im Stammbetriebssystem und im Gastbetriebssystem der Knoten implementiert sind, erzwingen Einschränkungen der Konnektivität und verhindern nicht autorisierten Datenverkehr zwischen virtuellen Computern.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, Stammbetriebssystem und Gast-VMs
Die Isolation des Stammbetriebssystem von den Gast-VMs und der Gast-VMs voneinander wird durch den Hypervisor und das Stammbetriebssystem verwaltet.

### <a name="types-of-rules-on-firewalls"></a>Arten von Regeln in Firewalls
Eine Regel wird wie folgt definiert:

{Security Response Center-IP (SRC), SRC-Port, Ziel-IP, Zielport, Zielprotokoll, Ein/Aus, Zustandsbehaftet/Zustandslos, zustandsbehaftetes Flow Timeout}.

SYN-Pakete (Synchronous Idle Character) können nur empfangen oder gesendet werden, wenn eine der Regeln dies zulässt. Azure verwendet zustandslose Regeln für TCP, wobei das Prinzip darin besteht, dass nur alle Nicht-SYN-Pakete in einen oder aus einem virtuellen Computer zugelassen werden. Die Voraussetzung für dieses Sicherheitsmodell ist, dass ein beliebiger Host-Stack tolerant gegenüber der Missachtung eines SYN-Pakets kann, wenn zuvor kein SYN-Paket erkannt wurde. Das TCP-Protokoll selbst ist zustandsbehaftet und erreicht in Kombination mit der zustandslosen SYN-basierten Regel das allgemeine Verhalten einer zustandsbehafteten Implementierung.

Azure verwendet eine zustandsbehaftete Regel für das User Datagramm-Protokoll (UDP). Jedes Mal, wenn ein UDP-Paket mit einer Regel übereinstimmt, wird ein umgekehrter Flow in die andere Richtung erstellt. Dieser Flow enthält ein integriertes Zeitlimit.

Kunden sind dafür verantwortlich, ihre Firewalls zusätzlich zu den von Azure bereitgestellten Einstellungen selbst einzurichten. Hier können Kunden die Regeln für eingehenden und ausgehenden Datenverkehr definieren.

### <a name="production-configuration-management"></a>Verwaltung der Produktionskonfiguration
Sichere Standardkonfigurationen werden von den jeweiligen Betriebsteams in Azure und Azure SQL-Datenbank verwaltet. Alle Änderungen an der Konfiguration der Produktionssysteme werden mithilfe eines zentralen Nachverfolgungssystems dokumentiert und überwacht. Änderungen an Software und Hardware werden über das zentrale Nachverfolgungssystem überwacht. Netzwerkänderungen im Zusammenhang mit der Zugriffssteuerungsliste werden mithilfe eines ACL-Verwaltungsdiensts nachverfolgt.

Alle Konfigurationsänderungen an Azure werden in der Stagingumgebung entwickelt und getestet. Danach werden sie in der Produktionsumgebung bereitgestellt. Softwarebuilds werden im Rahmen der Tests überprüft. Überprüfungen der Sicherheit und des Datenschutzes werden als Teil einer Checkliste von Zugangskriterien überprüft. Änderungen werden vom jeweiligen Bereitstellungsteam in geplanten Abständen bereitgestellt. Releases werden vom Personal der Bereitstellungsteams überprüft und genehmigt, bevor sie für die Produktion bereitgestellt werden.

Änderungen werden für die erfolgreiche Durchführung überwacht. In einem Fehlerszenario wird ein Rollback der Änderung auf den vorherigen Zustand ausgeführt, oder es wird mit Genehmigung des zuständigen Personals ein Hotfix für den Fehler bereitgestellt. Die Konfigurationseinstellungen in der virtuellen Azure-Umgebung werden über Quelldepot, Git, TFS, MDS (Master Data Services), Runner, Azure-Sicherheitsüberwachung, den FC und die WinFabric-Plattform zentral verwaltet, angewendet und überprüft.

Auf ähnliche Weise verfügen Hardware- und Netzwerkänderungen über bewährte Validierungsschritte, um die Einhaltung der Buildanforderungen auszuwerten. Die Releases werden durch ein koordiniertes CAB (Change Advisory Board) aus den entsprechenden Gruppen überprüft und autorisiert.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über die Sicherung der Azure-Infrastruktur seitens Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure production network (Azure-Produktionsnetzwerk)](azure-production-network.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
