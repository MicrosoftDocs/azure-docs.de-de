---
title: Hauptvorteile
description: Hier erfahren Sie mehr über die grundlegenden Konzepte von Defender für IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/13/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: cb984ca1a74d8b3838b857f2f5679264d1445187
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508607"
---
# <a name="basic-concepts"></a>Grundlegende Konzepte 

In diesem Artikel werden die wichtigsten Vorteile von Azure Defender für IoT beschrieben.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Schnelle nicht-invasive Bereitstellung und passive Überwachung

Defender für IoT-Sensoren stellen eine Verbindung mit Switch-SPAN-Ports (Spiegel) und Netzwerk-TAPs her und beginnen per passiver Überwachung (ohne Agent) sofort mit der Erfassung von ICS-Netzwerkdatenverkehr. Mit Deep Packet Inspection (DPI) wird Datenverkehr sowohl von seriellen als auch von Ethernet-Steuerungsnetzwerkgeräten analysiert. Defender für IoT hat keinerlei Auswirkungen auf OT-Netzwerke, da sich die Lösung nicht im Datenpfad befindet und OT-Geräte damit nicht aktiv überprüft werden. 

Zur Bereitstellung von sofortigen Momentaufnahmen von ausführlichen Windows-Geräteinformationen kann Defender für IoT-Sensoren so konfiguriert werden, dass die passive Überwachung durch eine optionale aktive Komponente ergänzt wird. Diese Komponente verwendet sichere, von Anbietern genehmigte Befehle, um bei Windows-Geräten Details zu Geräten mit einer von Ihnen festgelegten Häufigkeit abzufragen.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Integrierte Kenntnisse von ICS-Protokollen, -Geräten und -Anwendungen

DPI allein reicht nicht aus, um Protokollanomalien zu ermitteln und Geräte präzise zu erkennen. Der Defender für IoT-Sensor eignet sich für einige der größten und komplexesten Umgebungen. Bis heute wurden mehr als 1.300 OT-Netzwerke in allen Branchen analysiert.

## <a name="analytics-and-self-learning-engines"></a>Analyse- und Selbstlern-Engines

Engines erkennen Sicherheitsprobleme durch kontinuierliche Überwachung und mithilfe von fünf Analyse-Engines mit Selbstlernfunktionen, sodass keine Signaturen mehr aktualisiert und keine Regeln mehr definiert werden müssen. Die Engines nutzen ICS-spezifische Verhaltensanalysen und Data Science zur kontinuierlichen Überwachung des OT-Netzwerkdatenverkehrs auf Anomalien. Folgende fünf Engines werden verwendet:

- **Erkennung von Protokollverletzungen:** Erkennt die Verwendung von Paketstrukturen und Feldwerten, die gegen ICS-Protokollspezifikationen verstoßen.

- **Erkennung von Richtlinienverletzungen:** Erkennt Richtlinienverletzungen wie die nicht autorisierte Verwendung von Funktionscodes, den Zugriff auf bestimmte Objekte oder Änderungen an der Gerätekonfiguration.

- **Erkennung von industrieller Schadsoftware:** Erkennt Verhaltensweisen, die darauf hinweisen, dass bekannte Schadsoftware vorhanden ist, z. B. Conficker, Black Energy, Havex, WannaCry und NotPetya.

- **Anomalieerkennung:** Erkennt eine ungewöhnliche Kommunikation zwischen Computern und ungewöhnliche Verhaltensweisen. Die Engine modelliert ICS-Netzwerke als deterministische Sequenzen von Status und Übergängen mithilfe eines patentierten Verfahrens namens „Industrial Finite State Modeling“ (IFSM). Dadurch erfordert die Lösung einen kürzeren Lernzeitraum als generische mathematische Ansätze oder Analysen, die ursprünglich für IT und nicht für OT entwickelt wurden. Sie erkennt auch Anomalien schneller, mit minimalen falsch positiven Ergebnissen.

- **Erkennung von Betriebsvorfällen:** Erkennt Betriebsprobleme, z. B. Verbindungsunterbrechung, die auf frühe Anzeichen von Gerätefehlern hinweisen können.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Analyse des Netzwerkdatenverkehrs zur Risiko- und Sicherheitsrisikobewertung

Als einzige Lösung der Branche nutzt Defender für IoT proprietäre NTA-Algorithmen (Network Traffic Analysis, Netzwerkdatenverkehrsanalyse) zur passiven Erkennung von Sicherheitsrisiken im Netzwerk und bei Endpunkten, z. B.:

- Nicht autorisierte Remotezugriffsverbindungen
- Nicht autorisierte oder nicht dokumentierte Geräte
- Schwache Authentifizierung
- Anfällige Geräte (basierend auf ungepatchten CVEs)
- Nicht autorisierte Bridges zwischen Subnetzen
- Schwache Firewallregeln

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Data Mining für Untersuchungen, Forensik und Bedrohungssuche

Die Plattform bietet eine intuitive Data-Mining-Schnittstelle für die präzise Suche nach Datenverkehrsverlaufsdaten in allen relevanten Dimensionen. Beispiele hierfür sind Zeitraum, IP-Adresse, Mac-Adresse und Ports. Sie können auch protokollspezifische Abfragen basierend auf Funktionscodes, Protokolldiensten und Modulen durchführen. Für die weitere Drilldownanalyse sind zahlreiche PCAP-Dateien verfügbar.

## <a name="sensor-cloud-management-mode"></a>Sensor Cloud Management (Modus)

Mit dem Modus „Sensor Cloud Management“ wird festgelegt,wo vom Sensor erkannte Informationen zum Gerät, Warnungen und andere Informationen angezeigt werden.

Bei **mit der Cloud verbundenen Sensoren** werden die vom Sensor erkannten Informationen in der Sensorkonsole angezeigt. Warnungsinformationen werden über einen IoT-Hub übermittelt und können für andere Azure-Dienste wie Azure Sentinel freigegeben werden.

Bei **lokal verbundenen Sensoren** werden die vom Sensor erkannten Informationen in der Sensorkonsole angezeigt. Erkennungsinformationen werden auch für die lokale Verwaltungskonsole freigegeben, wenn der Sensor damit verbunden ist.

## <a name="air-gapped-networks"></a>Air-Gap-Netzwerke

Wenn Sie in einer Air-Gap-Umgebung arbeiten, bietet die lokale Verwaltungskonsole in Defender für IoT eine Echtzeitansicht der wichtigsten IoT- und OT-Risikoindikatoren sowie Warnungen für alle Ihre Einrichtungen. Die enge Integration mit Ihren SOC-Workflows und Runbooks ermöglicht eine einfache Priorisierung von Entschärfungsaktivitäten und standortübergreifende Korrelation von Bedrohungen.  

Defender für IoT bietet eine konsolidierte Ansicht aller Geräte. Zudem stellt die Lösung wichtige Geräteinformationen wie Typ (PLC, RTU, DCS usw.), Hersteller, Modell und Revisionsebene der Firmware sowie Warnungsinformationen bereit.  

Defender für IoT ermöglicht die effektive Verwaltung mehrerer Bereitstellungen und eine umfassende einheitliche Ansicht des Netzwerks. Zudem bietet Defender für IoT eine optimierte Warnungsverarbeitung und die Kontrolle über die Sicherheit des Betriebsnetzwerks.

Bei der lokalen Verwaltungskonsole handelt es sich um eine webbasierte Verwaltungsplattform, mit der Sie Aktivitäten von globalen Sensorinstallationen überwachen und steuern können. Mit der lokalen Verwaltungskonsole lassen sich nicht nur die von bereitgestellten Sensoren empfangene Daten verwalten, sondern auch Daten von einer Reihe verschiedener Geschäftsressourcen nahtlos integrieren: Dazu gehören Konfigurationsverwaltungsdatenbanken (CMDB), DNS, Firewalls, Web-APIs usw.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Anzeige der lokalen Verwaltungskonsole.":::

Vor der Nutzung der lokalen Verwaltungskonsole sollten Sie sich mit den Konzepten, Funktionen und Features für Sensoren vertraut machen.

## <a name="integrations"></a>Integrationen

Sie können die Funktionen von Defender für IoT erweitern, indem Sie sowohl Geräte- als auch Warnungsinformationen für Partnersysteme freigeben. Mithilfe von Integrationen können Unternehmen bisher isolierte Sicherheitslösungen einbinden, um Gerätesichtbarkeit und Threat Intelligence erheblich zu verbessern. Zudem können Unternehmen mithilfe von Integrationen systemweite Reaktionen beschleunigen und Risiken schneller auffangen. 

Durch Integrationen lässt sich die Komplexität verringern und IT- und OT-Silos werden überflüssig, da sie in die bestehenden SOC-Workflows und den Sicherheitsstapel integriert werden. Beispiel:

- SIEM-Lösungen wie IBM QRadar, Splunk, ArcSight, LogRhythm und RSA NetWitness

- Sicherheitsorchestrierung und Ticketausstellungssysteme wie ServiceNow und IBM Resilient

- Sichere Remotezugriffslösungen wie CyberArk Privileged Session Manager (PSM) und BeyondTrust

- Sichere NAC-Systeme (Network Access Control, Netzwerkzugriffssteuerung) wie Aruba ClearPass und Forescout CounterACT

- Firewalls wie Fortinet und Check Point

## <a name="complete-protocol-support"></a>Umfassende Protokollunterstützung

Zusätzlich zur integrierten Protokollunterstützung können Sie IoT- und ICS-Geräte schützen, auf denen proprietäre, benutzerdefinierte oder von allen Standards abweichende Protokolle ausgeführt werden. Mit dem Horizon ODE SDK (Open Development Environment) können Entwickler Zergliederungs-Plug-Ins erstellen, die Netzwerkdatenverkehr basierend auf definierten Protokollen decodieren. Der Datenverkehr wird durch Dienste analysiert, die umfangreiche Überwachungs-, Warnungs- und Berichterstellungsfunktionen bieten. Verwenden Sie Horizon für Folgendes:

- Erweitern der Transparenz und Kontrolle, ohne ein Upgrade auf neue Versionen durchführen zu müssen

- Sichern proprietärer Informationen durch Vor-Ort-Entwicklung als externes Plug-In

- Lokalisieren von Text für Warnungen, Ereignisse und Protokollparameter

Darüber hinaus können Sie auch proprietäre Protokollwarnungen verwenden, um Informationen zu übermitteln:

- Informationen zur Erkennung von Datenverkehr basierend auf Protokollen und zugrunde liegenden Protokollen in einem proprietären Horizon-Plug-In.

- Informationen zu einer Kombination von Protokollfeldern aus allen Protokollebenen. Beispielsweise können Sie in einer Umgebung, in der MODBUS ausgeführt wird, eine Warnung generieren, wenn der Sensor einen Schreibbefehl in einem Speicherregister für eine bestimmte IP-Adresse und ein Ethernet-Ziel erkennt. Oder Sie können eine Warnung generieren,wenn auf eine bestimmte IP-Adresse zugegriffen wird.

Warnungen werden ausgelöst, wenn die Bedingungen für Horizon-Warnungsregeln erfüllt sind.

Wenn Sie mit benutzerdefinierten Horizon-Warnungen arbeiten, können Sie außerdem Ihre eigenen Warnungstitel und Warnmeldungen schreiben. Aufgelöste Protokollfelder und Werte können ebenfalls in den Text der Warnmeldung eingebettet werden.

Mithilfe von benutzerdefinierter, bedingungsbasierter Warnungsauslösung und Messaging können Sie bestimmte Netzwerkaktivitäten ermitteln sowie Ihre Sicherheits-, IT- und Betriebsteams effektiv aktualisieren.


## <a name="high-availability"></a>Hochverfügbarkeit

Erhöhen Sie die Resilienz ihrer Defender für IoT-Bereitstellung, indem Sie eine lokale hochverfügbare Verwaltungskonsolen-Appliance installieren. Mit Hochverfügbarkeitsbereitstellungen wird sichergestellt, dass Ihre verwalteten Sensoren laufend an eine aktive lokale Verwaltungskonsole berichten.

Diese Bereitstellung wird mit einem Paar lokaler Verwaltungskonsolen implementiert, das aus einer primären und sekundären Appliance besteht.

## <a name="localization"></a>Lokalisierung

Viele Konsolenfunktionen unterstützen eine große Vielzahl von Sprachen.

## <a name="next-step"></a>Nächster Schritt

[Erste Schritte mit Azure Defender für IoT](getting-started.md)
