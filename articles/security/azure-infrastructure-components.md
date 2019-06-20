---
title: Komponenten und Grenzen des Azure-Informationssystems
description: Dieser Artikel enthält eine allgemeine Beschreibung der Microsoft Azure-Architektur und -verwaltung.
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
ms.openlocfilehash: b390dc9bd2b690837a85a5bab361a534b9c9d5a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587203"
---
# <a name="azure-information-system-components-and-boundaries"></a>Komponenten und Grenzen des Azure-Informationssystems
Dieser Artikel enthält eine allgemeine Beschreibung der Azure-Architektur und -Verwaltung. Die Azure-Systemumgebung besteht aus den folgenden Netzwerken:

- Microsoft Azure-Produktionsnetzwerk (Azure-Netzwerk)
- Microsoft-Unternehmensnetzwerk

Separate IT-Teams sind für Betrieb und Wartung dieser Netzwerke verantwortlich.

## <a name="azure-architecture"></a>Azure-Architektur
Azure ist eine Cloud Computing-Plattform und -Infrastruktur für die Erstellung, Bereitstellung und Verwaltung von Anwendungen und Diensten über ein Netzwerk von Rechenzentren. Diese Rechenzentren werden von Microsoft verwaltet. Basierend auf der Anzahl der von Ihnen angegebenen Ressourcen erstellt Azure je nach Ressourcenbedarf virtuelle Computer (VMs). Diese VMs werden auf einem Azure-Hypervisor ausgeführt, der für die Verwendung in der Cloud ausgelegt und nicht öffentlich zugänglich ist.

Auf jedem physischen Serverknoten von Azure befindet sich ein Hypervisor, der direkt über die Hardware ausgeführt wird. Der Hypervisor teilt einen Knoten in eine variable Anzahl von Gast-VMs auf. Darüber hinaus weist jeder Knoten eine Stamm-VM auf, auf der das Hostbetriebssystem ausgeführt wird. Die Windows-Firewall ist auf jedem virtuellen Computer aktiviert. Durch Konfiguration der Dienstdefinitionsdatei können Sie definieren, welche Ports adressierbar sind. Diese Ports sind die einzigen Ports, die intern oder extern offen und adressierbar sind. Der gesamte Datenverkehr und der Zugriff auf Datenträger und Netzwerk werden durch den Hypervisor und das Stammbetriebssystem vermittelt.

Auf der Hostschicht führen virtuelle Azure-Computer eine angepasste Windows Server-Version mit verstärkter Sicherheit aus. Azure verwendet eine Version von Windows Server, die nur die Komponenten einschließt, die zum Hosten von VMs erforderlich sind. Dies verbessert die Leistung und verringert die Angriffsfläche. Computergrenzen werden vom Hypervisor erzwungen, was nicht von der Sicherheit des Betriebssystems abhängig ist.

### <a name="azure-management-by-fabric-controllers"></a>Azure-Verwaltung durch Fabric Controller

In Azure werden VMs, die auf physischen Servern (Blades/Knoten) ausgeführt werden, in Clustern von ca. 1000 gruppiert. Die VMs werden unabhängig von einer horizontal skalierten und redundanten Plattformsoftwarekomponente namens Fabric Controller (FC) verwaltet.

Jeder FC verwaltet den Lebenszyklus von Anwendungen, die in seinem Cluster ausgeführt werden, und führt die Bereitstellung und Überwachung der Integrität der Hardware unter seiner Kontrolle durch. Er führt autonome Vorgänge aus, z.B. erneute Aktivierung von VM-Instanzen auf fehlerfreien Servern, wenn festgestellt wird, dass ein Server fehlgeschlagen ist. Der FC führt auch Anwendungsverwaltungsvorgänge durch, z.B. die Bereitstellung, Aktualisierung und horizontale Skalierung von Anwendungen.

Das Rechenzentrum ist in Cluster unterteilt. Cluster isolieren Fehler auf FC-Ebene und verhindern, dass bestimmte Fehlerklassen über den Cluster hinaus, in dem sie auftreten, Auswirkungen auf Servern haben. FCs, die einen bestimmten Azure-Cluster bedienen, werden in einem FC-Cluster gruppiert.

### <a name="hardware-inventory"></a>Hardwarebestand

Der FC bereitet während des Bootstrapkonfigurationsprozesses eine Bestandsaufnahme der Azure-Hardware- und -Netzwerkgeräte vor. Neue Hardware und Netzwerkkomponenten, die in die Azure-Produktionsumgebung aufgenommen werden, müssen dem Bootstrapkonfigurationsprozess folgen. Der FC ist verantwortlich für die Verwaltung des gesamten Bestands in der Konfigurationsdatei datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Vom FC verwaltete Betriebssystemimages

Das Betriebssystemteam stellt Images in Form von virtuellen Festplatten zur Verfügung, die auf allen Host- und Gast-VMs in der Azure-Produktionsumgebung bereitgestellt werden. Das Team erstellt diese Basisimages über einen automatisierten Offlinebuildprozess. Das Basisimage ist eine Version des Betriebssystems, in dem der Kernel und andere Kernkomponenten geändert und optimiert wurden, um die Azure-Umgebung zu unterstützen.

Es gibt drei Arten von durch Fabrics verwaltete Betriebssystemimages:

- Host: Ein angepasstes Betriebssystem, das auf Host-VMs ausgeführt wird.
- Nativ: Ein natives Betriebssystem, das auf Mandanten (z.B. Azure Storage) ausgeführt wird. Dieses Betriebssystem benötigt keinen Hypervisor.
- Gast: Ein Gastbetriebssystem, das auf Gast-VMs ausgeführt wird.

Die vom FC verwalteten Host- und nativen Betriebssysteme sind für die Verwendung in der Cloud ausgelegt und nicht öffentlich zugänglich.

#### <a name="host-and-native-operating-systems"></a>Host- und native Betriebssysteme

Das Host- und native Betriebssystem sind Betriebssystemimages mit verstärkter Sicherheit, die die Fabric-Agents hosten und einen Computeknoten (wird als erste VM auf dem Knoten ausgeführt) sowie Speicherknoten ausführen. Der Vorteil bei der Verwendung optimierter Basisimages des Host- und nativen Betriebssystems besteht darin, dass sie die Angriffsfläche verringern, die durch APIs und ungenutzte Komponenten geschaffen wird. Diese können hohe Sicherheitsrisiken darstellen und den Speicherbedarf des Betriebssystems erhöhen. Betriebssysteme mit niedrigerem Speicherbedarf enthalten nur die Komponenten, die in Azure erforderlich sind.

#### <a name="guest-operating-system"></a>Gastbetriebssystem

Interne Azure-Komponenten, die auf VMs mit Gastbetriebssystem ausgeführt werden, haben nicht die Möglichkeit, das Remotedesktopprotokoll auszuführen. Alle Änderungen an den Baselinekonfigurationseinstellungen müssen die Prozesse des Change Managements und der Releaseverwaltung durchlaufen.

## <a name="azure-datacenters"></a>Azure-Rechenzentren
Das Microsoft Cloud Infrastructure and Operations-Team (MCIO) verwaltet die physische Infrastruktur und die Rechenzentrumseinrichtungen für alle Onlinedienste von Microsoft. MCIO ist primär für die Verwaltung der physischen und Umweltsteuerungssysteme in Rechenzentren zuständig sowie für die Verwaltung und Unterstützung der Geräte des äußeren Umkreisnetzwerks (Edgerouter und Rechenzentrumsrouter). MCIO ist auch für die Einrichtung der erforderlichen Mindestserverhardware auf Racks im Rechenzentrum zuständig. Kunden interagieren nicht direkt mit Azure.

## <a name="service-management-and-service-teams"></a>Dienstverwaltung und Dienstteams
Eine Reihe von Technikergruppen, die „Dienstteams“, verwaltet die Unterstützung des Azure-Diensts. Jedes Dienstteam ist für einen Bereich des Azure-Supports zuständig. Jedes Dienstteam muss rund um die Uhr einen Techniker zur Verfügung stellen, der Fehler im Dienst untersucht und behebt. Dienstteams haben nicht standardmäßig physischen Zugang zu der Hardware, die in Azure ausgeführt wird.

Dienstteams sind für folgende Dienste verfügbar:

- Anwendungsplattform
- Azure Active Directory
- Azure Compute
- Azure Net
- Cloud-Engineeringdienste
- ISSD: Sicherheit
- Mehrstufige Authentifizierung
- SQL-Datenbank
- Storage

## <a name="types-of-users"></a>Benutzertypen
Mitarbeiter (oder Auftragnehmer) von Microsoft gelten als interne Benutzer. Alle anderen Benutzer werden als externe Benutzer betrachtet. Alle internen Azure-Benutzer müssen ihren Mitarbeiterstatus mit einer Vertraulichkeitsstufe kategorisieren lassen, die ihren Zugriff auf Kundendaten (Zugriff oder kein Zugriff) definiert. Benutzerberechtigungen in Azure (Autorisierungsberechtigung nach der Authentifizierung) werden in der folgenden Tabelle beschrieben:

| Rolle | Intern oder extern | Vertraulichkeitsstufe | Autorisierte Berechtigungen und ausgeführte Funktionen | Zugriffstyp
| --- | --- | --- | --- | --- |
| Azure-Rechenzentrumstechniker | Intern | Kein Zugriff auf Kundendaten | Verwalten der physischen Sicherheit der lokalen Umgebung. Durchführen von Kontrollgängen innerhalb und außerhalb des Rechenzentrums und Überwachen aller Eingänge. Begleiten von nicht autorisierten Mitarbeitern in und aus bestimmte(n) Rechenzentren, die allgemeine Dienstleistungen (z.B. Bewirtung oder Reinigungsarbeiten) oder IT-Arbeiten innerhalb des Rechenzentrums vollbringen. Durchführen von Routineüberwachungen und -wartungen von Netzwerkhardware. Durchführen von Aufgaben im Rahmen des Incident Managements und der Problemlösung mithilfe einer Vielzahl von Tools. Durchführen von Routineüberwachungen und -wartungen der physischen Hardware in den Rechenzentren. Auf Anfrage Zugang zur Umgebung von Grundstückeigentümern. Möglichkeit zur Durchführung forensischer Untersuchungen, Protokollierung von Incidentberichten und Durchsetzung von obligatorischen Sicherheitsschulungen und Richtlinienanforderungen. Verantwortung für den Betrieb und die Wartung kritischer Sicherheitstools, z.B. Scanner und Protokollsammlung. | Beständiger Zugriff auf die Umgebung. |
| Azure-Incidentselektierung (Rapid Response-Techniker) | Intern | Zugriff auf Kundendaten | Verwalten der Kommunikation zwischen MCIO-, Support- und Entwicklerteams. Bearbeitung von Incidents der Selektierungsplattform, Problemen bei der Bereitstellung und Dienstanforderungen. | Just-in-Time-Zugriff auf die Umgebung – mit eingeschränktem beständigem Zugriff auf nicht kundenbezogene Systeme. |
| Azure-Bereitstellungstechniker | Intern | Zugriff auf Kundendaten | Durchführen der Bereitstellung und des Upgrades von Plattformkomponenten, Software und geplanten Konfigurationsänderungen zur Unterstützung von Azure. | Just-in-Time-Zugriff auf die Umgebung – mit eingeschränktem beständigem Zugriff auf nicht kundenbezogene Systeme. |
| Azure-Kundensupport bei Ausfällen (Mandant) | Intern | Zugriff auf Kundendaten | Debuggen und Diagnostizieren von Plattformausfällen und Fehlern bei einzelnen Computemandanten und Azure-Konten. Analysieren von Fehlern. Steuern von wichtigen Updates für die Plattform oder die Kunden und Erzielen von technischen Verbesserungen über den Support. | Just-in-Time-Zugriff auf die Umgebung – mit eingeschränktem beständigem Zugriff auf nicht kundenbezogene Systeme. |
| Azure Live Site Engineers (Überwachungsengineers) und Incident | Intern | Zugriff auf Kundendaten | Diagnostizieren und Beheben von Problemen mit der Plattformintegrität durch Diagnosetools. Steuern von Fehlerbehebungen für Volumetreiber, Reparatur von durch Ausfälle verursachten Posten und Unterstützung bei Maßnahmen zur Behebung von Ausfällen. | Just-in-Time-Zugriff auf die Umgebung – mit eingeschränktem beständigem Zugriff auf nicht kundenbezogene Systeme. |
|Azure-Kunden | Extern | – | – | – |

Azure verwendet eindeutige Bezeichner zur Authentifizierung von Organisationsbenutzern und Kunden (oder Prozesse, die im Namen der Organisationsbenutzer agieren). Dies gilt für alle Assets und Geräte, die Teil der Azure-Umgebung sind.

### <a name="azure-internal-authentication"></a>Interne Azure-Authentifizierung

Die Kommunikation zwischen internen Azure-Komponenten ist mit TLS-Verschlüsselung geschützt. In den meisten Fällen sind die X.509-Zertifikate selbstsigniert. Zertifikate mit Verbindungen, auf die von außerhalb des Azure-Netzwerks zugegriffen werden kann, stellen ebenso wie Zertifikate für die FCs eine Ausnahme dar. FCs haben Zertifikate, die von der Microsoft-Zertifizierungsstelle (CA) ausgestellt werden, hinter der eine vertrauenswürdige Stammzertifizierungsstelle steht. Dadurch kann problemlos ein Rollover für öffentliche FC-Schlüssel ausgeführt werden. Darüber hinaus werden öffentliche FC-Schlüssel von Microsoft-Entwicklungstools verwendet. Wenn neue Anwendungsimages von Entwicklern gesendet werden, werden die Images mit einem öffentlichen FC-Schlüssel verschlüsselt, um eingebettete Geheimnisse zu schützen.

### <a name="azure-hardware-device-authentication"></a>Azure-Hardwaregeräteauthentifizierung

Der FC verwaltet einen Satz von Anmeldeinformationen (Schlüssel und/oder Kennwörter), mit denen er sich bei verschiedenen unter seiner Kontrolle stehenden Hardwaregeräten authentifiziert. Microsoft verwendet ein System, um den Zugriff auf diese Anmeldeinformationen zu verhindern. Dabei soll insbesondere durch das Transportieren, Speichern und Verwenden der neuen Anmeldeinformationen verhindert werden, dass Azure-Entwickler, Administratoren und Sicherungsdienste/-mitarbeiter auf sensible, vertrauliche oder private Informationen zugreifen.

Microsoft verwendet die Verschlüsselung basierend auf dem öffentlichen Schlüssel der Masteridentität des FC. Diese wird beim FC-Setup und bei FC-Neukonfigurationszeiten verwendet, um die für den Zugriff auf die Netzwerkhardwaregeräte verwendeten Anmeldeinformationen zu übertragen. Wenn der FC die Anmeldeinformationen erfordert, ruft der FC diese ab und entschlüsselt sie.

### <a name="network-devices"></a>Netzwerkgeräte

Das Azure-Netzwerkteam konfiguriert Netzwerkdienstkonten, um einem Azure-Client die Authentifizierung bei Netzwerkgeräten (Routern, Switches und Lastenausgleichsmodulen) zu ermöglichen.

## <a name="secure-service-administration"></a>Sichere Dienstverwaltung
Das Azure-Betriebspersonal muss sichere Arbeitsstationen für Administratoren (SAWs) verwenden. Kunden können ähnliche Steuerelemente implementieren, indem sie Arbeitsstationen mit privilegiertem Zugriff verwenden. Bei SAWs verwendet das Verwaltungspersonal ein persönlich zugewiesenes Administratorkonto, das von dem Standardbenutzerkonto des Benutzers getrennt ist. Die SAW baut auf dieser Kontotrennungsmethode auf, indem für solche sensiblen Konten eine vertrauenswürdige Arbeitsstation bereitgestellt wird.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über den Schutz der Azure-Infrastruktur durch Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)
