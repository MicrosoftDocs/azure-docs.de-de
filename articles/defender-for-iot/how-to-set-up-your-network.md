---
title: Einrichten des Netzwerks
description: Erfahren Sie mehr über Lösungsarchitektur, Netzwerkvorbereitung, Voraussetzungen und weitere Informationen, die erforderlich sind, um sicherzustellen, dass Sie Ihr Netzwerk erfolgreich für die Arbeit mit Azure Defender für IoT-Appliances einrichten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 4ceedf4d05c6f92e5c32da95f8b54fc6c95de01c
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526509"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Azure Defender für IoT: Netzwerkeinrichtung

Azure Defender für IoT bietet kontinuierliche ICS-Bedrohungsüberwachung und Geräteermittlung. Die Plattform umfasst die folgenden Komponenten:

**Defender für IoT-Sensoren:** Sensoren erfassen den ICS-Netzwerkdatenverkehr mithilfe von passiver Überwachung (ohne Agents). Da sie passiv und nicht intrusiv arbeiten, haben die Sensoren keinerlei Auswirkungen auf die Leistung von OT- und IoT-Netzwerken und -Geräten. Der Sensor wird mit einem SPAN-Port oder Netzwerk-TAP verbunden und beginnt sofort mit der Überwachung Ihres Netzwerks. Ermittlungen werden in der Sensorkonsole angezeigt. Dort können Sie sie in einer Netzwerkdarstellung, einem Geräteinventar und einer umfangreichen Palette von Berichten anzeigen, untersuchen und analysieren. Zu den Beispielen zählen Risikobewertungsberichte, Data Mining-Abfragen und Angriffsvektoren. 

**Defender für die lokale IoT-Verwaltungskonsole**: Die lokale Verwaltungskonsole bietet eine konsolidierte Ansicht aller Netzwerkgeräte. Sie stellt eine Echtzeitansicht der wichtigsten OT- und IoT-Leistungsindikatoren und Warnungen für alle Ihre Einrichtungen bereit. Die enge Integration in Ihre SOC-Workflows und Playbooks ermöglicht eine einfache Priorisierung von Entschärfungsaktivitäten und standortübergreifende Korrelation von Bedrohungen. 

**Defender für IoT für IoT-Portal:** Die Defender for IoT-Anwendung kann Sie beim Kauf von Lösungs-Appliances, bei der Installation und Aktualisierung von Software sowie bei der Aktualisierung von TI-Paketen unterstützen. 

Dieser Artikel bietet Informationen zu Lösungsarchitektur, Netzwerkvorbereitung, Voraussetzungen und weitere Informationen, die Sie bei der erfolgreichen Einrichtung Ihres Netzwerks für die Arbeit mit Azure Defender für IoT-Appliances unterstützen. Leser, die mit den Informationen in diesem Artikel arbeiten, sollten mit Betrieb und Verwaltung von OT- und IoT-Netzwerken vertraut sein. Beispiele hierfür sind Automatisierungstechniker, Werkleiter, Anbieter von OT-Netzwerkinfrastrukturdiensten, Cybersicherheitsteams, CISOs oder CIOs.

Wenden Sie sich an den [Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099), wenn Sie Unterstützung oder Support benötigen.

## <a name="on-site-deployment-tasks"></a>Aufgaben bei der lokalen Bereitstellung

Zu den Aufgaben bei der lokalen Bereitstellung gehören:

- [Sammeln von Standortinformationen](#collect-site-information)

- [Vorbereiten einer Konfigurations-Arbeitsstation](#prepare-a-configuration-workstation)

- [Planen der Rackinstallation](#planning-rack-installation)

### <a name="collect-site-information"></a>Sammeln von Standortinformationen

Erfassen Sie Standortinformationen wie:

- Informationen zum Sensorverwaltungsnetzwerk.

- Netzwerkarchitektur des Standorts.

- Physische Umgebung.

- Systemintegrationen.

- Geplante Anmeldeinformationen von Benutzern.

- Konfigurations-Arbeitsstation.

- SSL-Zertifikate (optional, aber empfohlen).

- SMTP-Authentifizierung (optional). Zum Verwenden des SMTP-Servers mit Authentifizierung bereiten Sie die für Ihren Server erforderlichen Anmeldeinformationen vor.

- DNS-Server (optional). Bereiten Sie die IP-Adresse und den Hostnamen Ihres DNS-Servers vor.

Eine ausführliche Liste und Beschreibung wichtiger Standortinformationen finden Sie unter [Beispiel-Standortbuch](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Richtlinien für erfolgreiche Überwachung

Zum Bestimmen des optimalen Platzes für den Anschluss der Appliance in jedem Ihrer Produktionsnetzwerke empfehlen wir Ihnen folgende Vorgehensweise:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Beispieldiagramm zum Setup eines Produktionsnetzwerks.":::

### <a name="prepare-a-configuration-workstation"></a>Vorbereiten einer Konfigurations-Arbeitsstation

Bereiten Sie eine Windows-Arbeitsstation vor, einschließlich der folgenden Punkte:

- Konnektivität zur Schnittstelle der Sensorverwaltung

- Einem unterstützten Browser

- Terminalsoftware, z. B. PuTTY

Achten Sie darauf, dass die erforderlichen Firewallregeln auf der Arbeitsstation offen sind. Details dazu finden Sie unter [Netzwerk-Zugriffsanforderungen](#network-access-requirements).

#### <a name="supported-browsers"></a>Unterstützte Browser

Die folgenden Browser werden für die Sensoren und Webanwendungen der lokalen Verwaltungskonsole unterstützt:

- Chrome 32 und höher

- Microsoft Edge 86 und höher

- Internet Explorer 10 und höher

### <a name="network-access-requirements"></a>Netzwerk-Zugriffsanforderungen

Überprüfen Sie, ob die Sicherheitsrichtlinie Ihrer Organisation den Zugriff auf Folgendes zulässt:

| Protokoll | Transport | Ein/Aus | Port | Verwendet | Zweck | `Source` | Destination |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | EIN/AUS | 443 | Sensor und lokale Verwaltungskonsole/Webkonsole | Zugriff auf die Webkonsole | Client | Sensor und lokale Verwaltungskonsole |
| SSH | TCP | EIN/AUS | 22 | Befehlszeilenschnittstelle (CLI) | Zugriff auf die CLI | Client | Sensor und lokale Verwaltungskonsole |
| SSL | TCP | EIN/AUS | 443 | Sensor und lokale Verwaltungskonsole | Verbindung zwischen der CyberX-Plattform und der zentralen Verwaltungsplattform | Sensor | Lokale Verwaltungskonsole |
| NTP | UDP | IN | 123 | Uhrzeitsynchronisierung | Lokale Verwaltungskonsole, die als NTP für den Sensor verwendet wird | Sensor | Lokale Verwaltungskonsole |
| NTP | UDP | EIN/AUS | 123 | Uhrzeitsynchronisierung | Mit externem NTP-Server verbundener Sensor, wenn keine lokale Verwaltungskonsole installiert ist | Sensor | NTP |
| SMTP | TCP | OUT | 25 | Email | Die Verbindung zwischen der CyberX-Plattform und der Verwaltungsplattform und dem Mailserver | Sensor und lokale Verwaltungskonsole | E-Mail-Server |
| syslog | UDP | OUT | 514 | LEEF | Protokolle, die von der lokalen Verwaltungskonsole an den Syslog-Server senden | Lokale Verwaltungskonsole und Sensor | Syslog-Server |
| DNS |  | EIN/AUS | 53 | DNS | DNS-Serverport | Lokale Verwaltungskonsole und Sensor | DNS-Server |
| LDAP | TCP | EIN/AUS | 389 | Active Directory | Die Verbindung zwischen der CyberX-Plattform und der Verwaltungsplattform zu Active Directory | Lokale Verwaltungskonsole und Sensor | LDAP-Server |
| LDAPS | TCP | EIN/AUS | 636 | Active Directory | Die Verbindung zwischen der CyberX-Plattform und der Verwaltungsplattform zu Active Directory | Lokale Verwaltungskonsole und Sensor | LDAPS-Server |
| SNMP | UDP | OUT | 161 | Überwachung | Remote-SNMP-Collectors | Lokale Verwaltungskonsole und Sensor | SNMP-Server |
| WMI | UDP | OUT | 135 | Überwachung | Windows-Endpunktüberwachung | Sensor | Relevantes Netzwerkelement |
| Tunneling | TCP | IN | 9000 <br /><br />- zusätzlich zum Port 443 <br /><br />Vom Endbenutzer zur lokalen Verwaltungskonsole <br /><br />- Port 22 vom Sensor zur lokalen Verwaltungskonsole  | Überwachung | Tunneling | Sensor | Lokale Verwaltungskonsole |

### <a name="planning-rack-installation"></a>Planen der Rackinstallation

So planen Sie Ihre Rackinstallation:

1. Bereiten Sie einen Monitor und eine Tastatur die Netzwerkeinstellungen Ihrer Appliance vor.

1. Weisen Sie den Platz im Rack für die Appliance zu.

1. Stellen Sie die Netzstromversorgung für die Appliance bereit.
1. Bereiten Sie das LAN-Kabel zum Verbinden der Verwaltungslösung mit dem Netzwerkswitch vor.
1. Bereiten Sie die LAN-Kabel zum Verbinden der SPAN-Ports (gespiegelten Ports) und/oder der Netzwerkanzapfungen mit der Defender für IoT-Appliance vor. 
1. Konfigurieren, verbinden und überprüfen Sie die SPAN-Ports in den gespiegelten Switches, wie in der Sitzung zur Architekturüberprüfung beschrieben.
1. Verbinden Sie den konfigurierten SPAN-Port mit einem Computer, auf dem Wireshark ausgeführt wird, und überprüfen Sie, ob der Port ordnungsgemäß konfiguriert ist.
1. Öffnen Sie alle relevanten Firewallports.

## <a name="about-passive-network-monitoring"></a>Informationen zur passiven Netzwerküberwachung

Die Appliance empfängt Datenverkehr aus mehreren Quellen, entweder durch Spiegelports am Switch (SPAN-Ports) oder durch Netzwerk-TAPs. Der Verwaltungsport ist mit dem Geschäfts-, Unternehmens- oder Sensorverwaltungs-Netzwerk verbunden und verfügt über Konnektivität zu einer lokalen Verwaltungskonsole oder dem Defender für IoT-Portal.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagramm eines verwalteten Switches mit Portspiegelung.":::

### <a name="purdue-model"></a>Purdue-Modell

In den folgenden Abschnitten sind Purdue-Ebenen beschrieben.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagramm des Purdue-Modells.":::

####  <a name="level-0-cell-and-area"></a>Ebene 0: Zelle und Bereich  

Ebene 0 besteht aus einer Vielzahl von Sensoren, Aktoren und Geräten, die am grundlegenden Fertigungsprozess beteiligt sind. Diese Geräte führen die grundlegenden Funktionen des Industrie-Automatisierungs- und Steuerungssystems aus, wie etwa:

- Steuern eines Motors.

- Messen von Variablen.
- Festlegen einer Ausgabe.
- Ausführen von wichtigen Funktionen wie Lackieren, Schweißen und Biegen.

#### <a name="level-1-process-control"></a>Ebene 1: Prozesssteuerung

Ebene 1 besteht aus eingebetteten Controllern, die den Fertigungsprozess steuern und manipulieren, deren Schlüsselfunktion die Kommunikation mit den Geräten der Ebene 0 ist. Bei der diskreten Fertigung stellen diese Geräte programmierbare Logikcontroller (PLCs) oder Remote-Telemetrieeinheiten (RTUs) dar. In der Prozessfertigung wird der grundlegende Controller als Prozessleitsystem (Distributed Control System, DCS) bezeichnet.

#### <a name="level-2-supervisory"></a>Ebene 2: Aufsicht

Ebene 2 stellt die Systeme und Funktionen dar, die der Laufzeitüberwachung und dem Betrieb eines Bereichs einer Produktionseinrichtung zugeordnet sind. Dazu gehören normalerweise die Folgenden: 

- Operatorschnittstellen oder HMIs

- Alarme oder Warnsysteme

- Prozessverlauf und Batchverwaltungssysteme

- Arbeitsstationen im Kontrollraum

Dieses Systeme kommunizieren mit den PLCs und RTUs in Ebene 1. In machen Fällen kommunizieren sie oder teilen Daten mit den Systemen und Anwendungen des Standorts oder Unternehmens (Ebene 4 und Ebene 5). Diese Systeme basieren hauptsächlich auf Standard-Computerausrüstung und -Betriebssystemen (Unix oder Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Ebenen 3 und 3,5: Umkreisnetzwerk auf Standort- und Fertigungsebene

Die Standortebene stellt die höchste Ebene von industriellen Automatisierungs- und Steuerungssystemen dar. Die Systeme und Anwendungen auf dieser Ebene verwalten standortweit die Automatisierungs- und Steuerungsfunktionen der Fertigung. Die Ebenen 0 bis 3 werden als kritisch für den Betrieb von Standorten angesehen. Zu den auf dieser Ebene vorhandenen Systemen können beispielsweise die folgenden gehören:

- Fertigungsberichte (beispielsweise Zykluszeiten, Qualitätsindex, vorausschauende Wartung)

- Standorthistorie

- Detaillierte Produktionsplanung

- Betriebsverwaltung auf Standortebene

- Geräte- und Materialverwaltung

- Patch-Startserver

- Dateiserver

- Fertigungsdomäne, Active Directory, Terminalserver

Diese Systeme kommunizieren mit der Produktionszone und teilen Daten mit den Unternehmenssystemen und -anwendungen (Ebene 4 und Ebene 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Ebenen 4 und 5: Geschäfts- und Unternehmensnetzwerke

Ebene 4 und Ebene 5 stellen das Standort- oder Unternehmensnetzwerk dar, in dem die zentralen IT-Systeme und -Funktionen betrieben werden. Die IT-Organisation verwaltet auf diesen Ebenen direkt die Dienste, Systeme und Anwendungen.

## <a name="planning-for-network-monitoring"></a>Planen der Netzwerküberwachung

In den folgenden Beispielen werden verschiedene Typen von Topologien für Industriesteuerungs-Netzwerke sowie Überlegungen zur optimalen Überwachung und Platzierung von Sensoren vorgestellt.

### <a name="what-should-be-monitored"></a>Was sollte überwacht werden?

Datenverkehr, der die Schichten 1 und 2 durchläuft, sollte überwacht werden.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Womit sollte die Defender für IoT-Appliance verbunden sein?

Die Defender für IoT-Appliance sollte mit den verwalteten Switches verbunden werden, die den Austausch von Industriedaten in den Schichten 1 und 2 sehen können (in manchen Fällen außerdem Schicht 3).

Das folgende Diagramm ist eine allgemeine Abstraktion eines mehrschichtigen, mehrmandantenfähigen Netzwerks mit einem umfassenden Cybersicherheits-Ökosystem, wie es in der Regel von einem SOC und MSSP betrieben wird.

Normalerweise werden in den Schichten 0 bis 3 des OSI-Modells NTA-Sensoren bereitgestellt.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagramm des OSI-Modells.":::

#### <a name="example-ring-topology"></a>Beispiel: Ringtopologie

Beim Ringnetzwerk handelt es sich um eine Topologie, in der jeder Switch oder Knoten eine Verbindung mit genau zwei anderen Switches herstellt und so einen einzelnen kontinuierlichen Weg für den Datenverkehr bildet.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagramm der Ringtopologie.":::

#### <a name="example-linear-bus-and-star-topology"></a>Beispiel: Lineare Bus- und Sterntopologie

In einem Sternnetzwerk ist jeder Host mit einem zentralen Hub verbunden. In seiner einfachsten Form fungiert ein zentraler Hub als Kanal zum Übertragen von Nachrichten. Im folgenden Beispiel werden niedrigere Switches nicht überwacht, und der Datenverkehr, der für diese Switches lokal bleibt, bleibt ungesehen. Geräte können auf der Grundlage von ARP-Nachrichten identifiziert werden, jedoch fehlen Verbindungsinformationen.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagramm der linearen Bus- und Sterntopologie.":::

#### <a name="multisensor-deployment"></a>Bereitstellung mehrerer Sensoren

Dies sind einige Empfehlungen für das Bereitstellen mehrerer Sensoren:

| **Number** | **Meter** | **Abhängigkeit** | **Anzahl der Sensoren** |
|--|--|--|--|
| Der maximale Abstand zwischen Switches | 80 Meter | Vorbereitetes Ethernet-Kabel | Mehr als 1 |
| Anzahl der OT-Netzwerke | Mehr als 1 | Keine physische Verbindung | Mehr als 1 |
| Anzahl der Switches | Kann RSPAN-Konfiguration verwenden | Bis zu acht Switches mit lokaler Reichweite in Sensornähe nach Verkabelungsentfernung | Mehr als 1 |

#### <a name="traffic-mirroring"></a>Datenverkehrsspiegelung  

Um nur die relevanten Informationen für die Datenverkehrsanalyse anzuzeigen, müssen Sie die Defender für IoT-Plattform mit einem Spiegelungsport an einem Switch oder einem TAP verbinden, der nur Industrie-ICS- und SCADA-Datenverkehr enthält. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Verwenden Sie diesen Switch für Ihr Setup.":::

Sie können den Switchdatenverkehr mithilfe der folgenden Methoden überwachen:

- [Switch-SPAN-Port](#switch-span-port)

- [Remote SPAN (RSPAN)](#remote-span-rspan)

- [Aktiver und passiver Aggregations-TAP](#active-and-passive-aggregation-tap)

SPAN und RSPAN sind Cisco-Terminologie. Switches anderer Marken weisen ähnliche Funktionalität auf, verwenden aber möglicherweise eine abweichende Terminologie.

#### <a name="switch-span-port"></a>Switch-SPAN-Port

Ein Switch-Portanalyzer spiegelt den lokalen Datenverkehr von Schnittstellen auf dem Switch an eine Schnittstelle auf demselben Switch. Hier einige Überlegungen dazu:

- Vergewissern Sie sich, dass der entsprechende Switch die Funktion zur Portspiegelung unterstützt.  

- Die Spiegelungsoption ist standardmäßig deaktiviert.

- Es empfiehlt sich, alle Ports des Switches zu konfigurieren, auch wenn mit ihnen keine Daten verbunden sind. Andernfalls wird möglicherweise ein nicht autorisiertes Gerät mit einem nicht überwachten Port verbunden, und dies würde keine Warnung auf dem Sensor auslösen.

- Konfigurieren Sie in OT-Netzwerken, die Broadcast- oder Multicastmessaging verwenden, den Switch so, dass er nur RX-Übertragungen (Empfangsübertragungen) spiegelt. Andernfalls werden Multicastnachrichten für die Anzahl der aktiven Ports wiederholt, wodurch sich die Bandbreite multipliziert.

Die folgenden Konfigurationsbeispiele dienen nur als Referenz und basieren auf einem Cisco 2960-Switch (24 Ports) unter IOS. Es handelt sich lediglich um typische Beispiele, verwenden Sie sie nicht als Anweisung. Spiegelports unter anderen Cisco-Betriebssystemen und an Switches anderer Marken sind anders konfiguriert.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagramm eines SPAN-Portkonfigurationsterminals.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagramm des SPAN-Portkonfigurationsmodus.":::

##### <a name="monitoring-multiple-vlans"></a>Überwachen mehrerer VLANs

Defender für IoT ermöglicht die Überwachung von VLANs, die in Ihrem Netzwerk konfiguriert sind. Es ist keine Konfiguration des Defender für IoT-Systems erforderlich. Der Benutzer muss sicherstellen, dass der Switch in Ihrem Netzwerk für das Senden von VLAN-Tags an Defender für IoT konfiguriert ist.

Das folgende Beispiel zeigt die erforderlichen Befehle, die auf dem Cisco-Switch konfiguriert werden müssen, um die Überwachung von VLANs in Defender für IoT zu aktivieren:

**Monitor session**: Dieser Befehl ist für den Vorgang des Sendens von VLANs an den SPAN-Port zuständig.

- monitor session 1 source interface Gi1/2

- monitor session 1 filter packet type good Rx

- monitor session 1 destination interface fastEthernet1/1 encapsulation dot1q

**Monitor Trunk Port F.E. Gi1/1**: VLANs sind auf dem Trunk-Port konfiguriert.

- interface GigabitEthernet1/1

- switchport trunk encapsulation dot1q

- switchport mode trunk

#### <a name="remote-span-rspan"></a>Remote SPAN (RSPAN)

Die Remote SPAN-Sitzung spiegelt Datenverkehr von mehreren verteilten Quellports in ein dediziertes Remote-VLAN.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagramm von Remote SPAN.":::

Die Daten im VLAN werden dann durch gebündelte Ports über mehrere Switches an einen bestimmten Switch geleitet, der den physischen Zielport enthält. Dieser Port stellt die Verbindung mit der Defender für IoT-Plattform her.  

##### <a name="more-about-rspan"></a>Weitere Informationen zu RSPAN

- RSPAN ist eine erweiterte Funktion, die ein besonderes VLAN zur Aufnahme des Datenverkehrs erfordert, der von SPAN zwischen den Switches überwacht wird. RSPAN wird nicht auf allen Switches unterstützt. Vergewissern Sie sich, dass der Switch die RSPAN-Funktion unterstützt.

- Die Spiegelungsoption ist standardmäßig deaktiviert.

- Das Remote-VLAN muss auf dem gebündelten Port zwischen den Quell- und Zielswitches zugelassen werden.

- Alle Switches mit einer Verbindung zur gleichen RSPAN-Sitzung müssen vom gleichen Hersteller stammen.

> [!NOTE]
> Vergewissern Sie sich, dass der gebündelte Port, der das Remote- VLAN zwischen den Switches teilt, nicht als Quellport einer Spiegelsitzung definiert ist.
>
> Das Remote VLAN erhöht die Bandbreite des gebündelten Ports um die Bandbreite der gespiegelten Sitzung. Vergewissern Sie sich, dass der gebündelte Port des Switches dies unterstützt.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagramm eines Remote-VLAN.":::

#### <a name="rspan-configuration-examples"></a>RSPAN-Konfigurationsbeispiele

RSPAN: Basierend auf Cisco Catalyst 2960 (24 Ports).

**Beispiel für die Konfiguration des Quellswitches:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Screenshot der RSPAN-Konfiguration.":::

1. Wechseln Sie zum globalen Konfigurationsmodus.

1. Erstellen Sie ein dediziertes VLAN.

1. Identifizieren Sie das VLAN als das RSPAN VLAN.

1. Kehren Sie zum Terminalkonfigurationsmodus zurück.

1. Konfigurieren Sie alle 24 Ports als Sitzungsquellen.

1. Konfigurieren Sie das RSPAN VLAN als Sitzungsziel.

1. Kehren Sie zum privilegierten EXEC-Modus zurück.

1. Überprüfen Sie die Konfiguration der Portspiegelung.

**Konfigurationsbeispiel für einen Zielswitch:**

1. Wechseln Sie zum globalen Konfigurationsmodus.

1. Konfigurieren Sie das RSPAN VLAN als Sitzungsquelle.

1. Konfigurieren Sie den physischen Port 24 als das Sitzungsziel.

1. Kehren Sie zum privilegierten EXEC-Modus zurück.

1. Überprüfen Sie die Konfiguration der Portspiegelung.

1. Speichern Sie die Konfiguration.

#### <a name="active-and-passive-aggregation-tap"></a>Aktiver und passiver Aggregations-TAP

Ein aktiver oder passiver Aggregations-TAP wird inline im Netzwerkkabel installiert. Er verdoppelt sowohl RX als auch TX an den Überwachungssensor.

Der Terminalzugangspunkt (Terminal Access Point, TAP) ist ein Hardwaregerät, das den Fluss von Netzwerkdatenverkehr von Port A ohne Unterbrechung an Port B erlaubt und umgekehrt. Es erstellt fortlaufend eine genaue Kopie beider Seiten des Datenverkehrsflusses, ohne die Netzwerkintegrität zu beschädigen. Einige TAPs aggregieren übertragenen und empfangenen Datenverkehr bei Bedarf mithilfe von Switcheinstellungen. Wenn Aggregation nicht unterstützt wird, verwendet jeder TAP zwei Sensorports zum Überwachen von Sende- und Empfangsverkehr.

TAPs sind aus verschiedenen Gründen vorteilhaft. Sie sind hardwarebasiert und können nicht kompromittiert werden. Sie geben den gesamten Datenverkehr weiter, selbst beschädigte Nachrichten, die von Switches oftmals verworfen werden. Sie sind nicht prozessorabhängig, daher ist das Pakettiming exakt, während Switches die Spiegelfunktion oftmals als Aufgabe geringer Priorität behandeln, was sich auf das Timing der gespiegelten Pakete auswirken kann. Für forensische Zwecke ist ein TAP die beste Vorrichtung.

TAP-Aggregatoren können ebenfalls zur Portüberwachung verwendet werden. Diese Geräte basieren auf Prozessoren und sind nicht so immanent sicher wie Hardware-TAPs. Sie geben das Pakettiming möglicherweise nicht exakt wieder.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagramm aktiver und passiver TAPs.":::

##### <a name="common-tap-models"></a>Verbreitete TAP-Modelle

Diese Modelle wurden auf Kompatibilität getestet. Andere Hersteller und Modelle sind möglicherweise ebenfalls kompatibel.

| Image | Modell |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Screenshot von Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Screenshot von IXIA TPA2-CU3."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Screenshot von US Robotics USR 4503."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Besondere TAP-Konfiguration

| Garland-TAP | US Robotics-TAP |
| ----------- | --------------- |
| Vergewissern Sie sich, dass die Jumper wie folgt eingestellt sind:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Screenshot des US Robotics-Switch.":::| Achten Sie darauf, dass der Aggregationsmodus aktiv ist. |

## <a name="deployment-validation"></a>Überprüfung der Bereitstellung

### <a name="engineering-self-review"></a>Technische Selbstüberprüfung  

Das Überprüfen Ihrer OT- und ICS-Netzwerkdiagramme ist das effizienteste Verfahren, um den besten Ort für die Verbindung zu definieren, an dem Sie den relevantesten Datenverkehr für die Überwachung erfassen können.

Die Standorttechniker wissen, wie ihre Netzwerke aussehen. Bei einer Überprüfungssitzung mit den lokalen Netzwerk- und Betriebsteams lassen sich normalerweise die Erwartungen abklären und der beste Ort zum Anschließen der Appliance definieren.

Relevante Informationen:

- Liste bekannter Geräte (Kalkulationstabelle)  

- Geschätzte Anzahl der Geräte  

- Hersteller und Industrieprotokolle

- Modell der Switches, um zu überprüfen, ob die Option zur Portspiegelung verfügbar ist

- Informationen dazu, wer die Switches verwaltet (beispielsweise IT) und ob es sich um externe Ressourcen handelt

- Liste der OT-Netzwerke am Standort

#### <a name="common-questions"></a>Häufig gestellte Fragen

- Was sind die Gesamtziele der Implementierung? Sind eine vollständige Inventarisierung und eine akkurate Netzwerkkartierung wichtig?

- Gibt es mehrere oder redundante Netzwerke in den ICS? Werden alle Netzwerke überwacht?

- Gibt es Kommunikation zwischen dem ICS und dem Unternehmensnetzwerk (Geschäftsnetzwerk)? Werden diese Kommunikationen überwacht?

- Sind VLANs im Netzwerkentwurf konfiguriert?

- Wie erfolgt die Wartung des ICS, mit permanenten oder vorübergehend eingesetzten Geräten?

- Wo sind Firewalls in den überwachten Netzwerken installiert?

- Gibt es Routing in den überwachten Netzwerken?

- Welche OT-Protokolle sind in den überwachten Netzwerken aktiv?

- Wenn wir eine Verbindung mit diesem speziellen Switch herstellen, sehen wir dann die Kommunikation zwischen dem HMI und den PLCs?

- Wie groß ist die physische Entfernung zwischen den ICS-Switches und der Unternehmensfirewall? 

- Können nicht verwaltete Switches durch verwaltete Switches ersetzt werden, oder ist der Einsatz von Netzwerk-TAPs eine Option?

- Gibt es serielle Kommunikation im Netzwerk? Wenn ja, weisen Sie sie im Netzwerkdiagramm aus.

- Wenn die Defender für IoT-Appliance mit diesem betreffenden Switch verbunden werden soll, gibt es im betreffenden Schrank einen freien physischen Regalplatz?

#### <a name="other-considerations"></a>Andere Aspekte

Der Zweck der Defender für IoT-Appliance ist die Überwachung des Datenverkehrs aus den Schichten 1 und 2.

Bei einigen Architekturen soll die Defender für IoT-Appliance ebenfalls Schicht 3 überwachen, falls in dieser Schicht OT-Datenverkehr vorkommt. Während Sie die Architektur des Standorts überprüfen und entscheiden, ob ein Switch überwacht werden soll, erwägen Sie die folgenden Variablen:

- In welchem Verhältnis stehen Kosten/Vorteil zur Wichtigkeit der Überwachung dieses Switches?

- Wenn ein Switch unverwaltet ist, ist es möglich, den Datenverkehr von einem Switch einer höheren Ebene aus zu überwachen?

  Wenn es sich bei der ICS-Architektur um eine Ringtopologie handelt, muss nur ein Switch in diesem Ring überwacht werden.

- Worin bestehen die Sicherheits- und Betriebsrisiken in diesem Netzwerk?

- Ist es möglich, das VLAN des Switches zu überwachen? Ist dieses VLAN über einen anderen Switch sichtbar, den wir überwachen können?

#### <a name="technical-validation"></a>Technische Validierung

Der Empfang einer Stichprobe von aufgezeichnetem Datenverkehr (PCAP-Datei) vom Switch-SPAN-Port (oder Spiegelungsport) kann in diesen Punkten nützlich sein:

- Überprüfen, ob der Switch ordnungsgemäß konfiguriert ist.

- Prüfen, ob der Datenverkehr, der den Switch durchläuft, für die Überwachung relevant ist (OT-Datenverkehr).

- Bestimmen von Bandbreite und der geschätzten Geräteanzahl an diesem Switch.

Sie können eine PCAP-Beispieldatei (einige Minuten) aufzeichnen, indem Sie einen Laptop über die Wireshark-Anwendung mit einem bereits konfigurierten SPAN-Port verbinden.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Screenshot eines Laptops, der mit einem SPAN-Port verbunden ist.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Screenshot der Aufzeichnung einer PCAP-Beispieldatei.":::

#### <a name="wireshark-validation"></a>Wireshark-Überprüfung

- Überprüfen Sie, ob *Unicast-Pakete* im aufgezeichneten Datenverkehr vorhanden sind. Unicast ist Datenverkehr von einer Adresse zu einer anderen. Wenn der größte Teil des Datenverkehrs aus ARP-Nachrichten besteht, ist die Einrichtung des Switches fehlerhaft.

- Navigieren Sie zu **Statistics** > **Protocol Hierarchy** (Statistik > Protokollhierarchie). Überprüfen Sie, ob industrielle OT-Protokolle vorhanden sind.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Screenshot der Wireshark-Überprüfung.":::

## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie diese Abschnitte zum Beheben von Problemen:

- [Es kann keine Verbindung über eine Webschnittstelle hergestellt werden](#cant-connect-by-using-a-web-interface)

- [Die Appliance reagiert nicht](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Es kann keine Verbindung über eine Webschnittstelle hergestellt werden

1. Vergewissern Sie sich, dass der Computer, mit dem Sie die Verbindung herstellen möchten, sich im gleichen Netzwerk wie die Appliance befindet.

2. Überprüfen Sie, ob das GUI-Netzwerk mit dem Verwaltungsport am Sensor verbunden ist.

3. Führen Sie ein Ping zur IP-Adresse der Appliance aus. Wenn auf Ping keine Antwort erfolgt:

    1. Schließen Sie einen Monitor und eine Tastatur an die Appliance an.

    1. Verwenden Sie den Benutzer **Support** und das betreffende Kennwort für die Anmeldung.

    1. Verwenden Sie den Befehl **network list**, um die aktuelle IP-Adresse anzuzeigen.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Screenshot des Befehls „network list“.":::

4. Wenn die Netzwerkparameter falsch konfiguriert wurden, ändern Sie dies mit dem folgenden Verfahren:

    1. Verwenden Sie den Befehl **network edit-settings**.

    1. Zum Ändern der IP-Adresse des Verwaltungsnetzwerks wählen Sie **Y** aus.

    1. Wählen Sie zum Ändern der Subnetzmaske **Y** aus.

    1. Wählen Sie zum Ändern des DNS **Y** aus.

    1. Zum Ändern der IP-Adresse des Standardgateways wählen Sie **Y** aus.

    1. Zum Ändern der Eingabeschnittstelle (nur für den Sensor) wählen Sie **Y** aus.

    1. Für die Bridge-Schnittstelle wählen Sie **N** aus.

    1. Zum Übernehmen der Einstellungen wählen Sie **Y** aus.

5. Stellen Sie nach dem Neustart eine Verbindung mit dem Benutzer „Support“ her, und verwenden Sie den Befehl **network list**, um zu überprüfen, ob die Parameter geändert wurden.

6. Versuchen Sie von der Benutzeroberfläche aus erneut, Ping auszuführen und eine Verbindung herzustellen.

### <a name="appliance-is-not-responding"></a>Die Appliance reagiert nicht

1. Schließen Sie einen Monitor und eine Tastatur an die Appliance an, oder verwenden Sie PuTTY, um eine Remoteverbindung mit der CLI herzustellen.

2. Verwenden Sie die Support-Anmeldeinformationen, um sich anzumelden.

3. Verwenden Sie den Befehl **system sanity**, um zu überprüfen, ob alle Prozesse ausgeführt werden.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Screenshot des Befehls „system sanity“.":::

Wenden Sie sich bei allen anderen Problemen an den [Microsoft Support](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Standort-Beispielbuch

Verwenden Sie das Standort-Beispielbuch, um wichtige Informationen abzurufen und zu überprüfen, die Sie für das Netzwerksetup benötigen.

### <a name="site-checklist"></a>Standortcheckliste

Gehen Sie diese Liste vor der Bereitstellung durch:

| **#** | **Aufgabe oder Aktivität** | **Status** | **Kommentare** |
|--|--|--|--|
| 1 | Bestellung von Appliances. | ☐ |  |
| 2 | Vorbereiten einer Liste der Subnetze im Netzwerk. | ☐ |  |
| 3 | Bereitstellen einer VLAN-Liste der Produktionsnetzwerke. | ☐ |  |
| 4 | Bereitstellen einer Liste der Switchmodelle im Netzwerk. | ☐ |  |
| 5 | Bereitstellen einer Liste der Hersteller und Protokolle der Industrieanlagen. | ☐ |  |
| 6 | Bereitstellen von Netzwerkdetails für Sensoren (IP-Adresse, Subnetz, D-GW, DNS). | ☐ |  |
| 7 | Erstellen der erforderlichen Firewallregeln und der Zugriffsliste. | ☐ |  |
| 8 | Erstellen von Wartungsports auf Switches für die Portüberwachung oder Konfigurieren von Netzwerk-TAPs nach Wunsch. | ☐ |  |
| 9 | Vorbereiten von Regalplatz für Sensor-Appliances. | ☐ |  |
| 10 | Vorbereiten einer Arbeitsstation für Mitarbeiter. | ☐ |  |
| 11 | Bereitstellen von Tastatur, Monitor und Maus für die Defender für IoT-Rackgeräte. | ☐ |  |
| 12 | Rackeinbau und Verkabelung der Appliances. | ☐ |  |
| 13 | Zuweisen von Standortressourcen zum Unterstützen der Bereitstellung. | ☐ |  |
| 14 | Erstellen von Active Directory-Gruppen oder lokalen Benutzern. | ☐ |  |
| 15 | Einrichten von Aus- und Weiterbildung (Selbstlernmodus). | ☐ |  |
| 16 | Genehmigung oder Ablehnung. | ☐ |  |
| 17 | Planen des Bereitstellungsdatums. | ☐ |  |


| **Datum** | **Hinweis** | **Bereitstellungsdatum** | **Hinweis** |
|--|--|--|--|
| Defender für IoT |  | Standortname* |  |
| Name |  | Name |  |
| Position |  | Position |  |

#### <a name="architecture-review"></a>Architekturüberprüfung

Mithilfe einer Übersicht des Industrienetzwerkdiagramms können Sie den richtigen Standort für die Defender für IoT-Ausrüstung bestimmen.

1.  Sehen Sie sich ein globales Netzwerkdiagramm der industriellen OT-Umgebung an. Beispiel:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagramm der industriellen OT-Umgebung für das globale Netzwerk.":::

    > [!NOTE]
    > Die Defender für IoT-Appliance sollte mit einem Switch auf niedrigerer Ebene verbunden sein, der den Datenverkehr zwischen den Ports auf dem Switch sehen kann.  

2. Geben Sie die ungefähre Anzahl von Netzwerkgeräten an, die überwacht werden. Sie benötigen diese Information, wenn Sie für Ihr Abonnement das Onboarding im Portal für Azure Defender für IoT durchführen. Während des Onboardingprozesses werden Sie aufgefordert, die Geräteanzahl in Inkrementen von 1.000 einzugeben.

3. Geben Sie eine Subnetzliste für die Produktionsnetzwerke und eine Beschreibung an (optional). 

    |  **#**  | **Subnetzname** | **Beschreibung** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Bereitstellen einer VLAN-Liste der Produktionsnetzwerke.

    | **#** | **VLAN-Name** | **Beschreibung** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Zum Überprüfen, ob die Switches über Funktionen zur Portspiegelung verfügen, geben Sie die Modellnummern der Switches an, mit denen die Defender für IoT-Plattform verbunden werden soll:

    | **#** | **Switch** | **Modell** | **Unterstützung für Spiegelung des Datenverkehrs (SPAN, RSPAN oder keine)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Werden die Switches von einem Drittanbieter verwaltet? J oder N 

    Falls ja, von wem? __________________________________ 

    Wie lautet die Richtlinie des Anbieters? __________________________________ 

    Beispiel:

    - Siemens

    - Rockwell-Automatisieurng – Ethernet oder IP

    - Emerson – DeltaV, Ovation
    
6. Gibt es Geräte, die über eine serielle Verbindung im Netzwerk kommunizieren? Ja oder Nein 

    Falls ja, geben Sie an, mit welchen seriellen Kommunikationsprotokollen: ________________ 

    Falls ja, markieren Sie im Netzwerkdiagramm, welche Geräte mit seriellen Protokollen kommunizieren und wo sie sich befinden: 
 
    <Add your network diagram with marked serial connection> 

7. Für QoS ist die Standardeinstellung des Sensors 1,5 MBit/s. Geben Sie an, ob Sie diesen Wert ändern möchten: ________________ 

   Geschäftseinheit (BU): ________________ 

### <a name="specifications-for-site-equipment"></a>Spezifikationen für Standortausrüstung

#### <a name="network"></a>Netzwerk  

Die Sensor-Appliance ist über einen Netzwerkadapter mit einem Switch-SPAN-Port verbunden. Sie ist zur Verwaltung über einen weiteren dedizierten Netzwerkadapter mit dem Unternehmensnetzwerk des Kunden verbunden.

Geben Sie Zugriffsdetails für das Sensor-NIC an, das mit dem Unternehmensnetzwerk verbunden werden soll: 

|  Element               | Appliance 1 | Appliance 2 | Appliance 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP-Adresse der Appliance    |               |               |               |
| Subnet          |               |               |               |
| Standardgateway |               |               |               |
| DNS             |               |               |               |
| Hostname       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/Serververwaltung

|       Element          | Appliance 1 | Appliance 2 | Appliance 3 |
| --------------- | ------------- | ------------- | ------------- |
| IP-Adresse der Appliance     |               |               |               |
| Subnet          |               |               |               |
| Standardgateway |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Lokale Verwaltungskonsole  

|       Element          | Aktiv | Passiv (bei der Verwendung von HA) |
| --------------- | ------ | ----------------------- |
| IP-Adresse             |        |                         |
| Subnet          |        |                         |
| Standardgateway |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Element              | Details |
| --------------- | ------ |
| IP              |        |
| IP-Adresse | |
| Username | |
| Kennwort | |
| Authentifizierungsart | MD5 oder SHA |
| Verschlüsselung | DES oder AES |
| Geheimer Schlüssel | |
| SNMP v2-Community String |

### <a name="on-premises-management-console-ssl-certificate"></a>SSL-Zertifikat für die lokale Verwaltungskonsole

Planen Sie die Verwendung eines SSL-Zertifikats? Ja oder Nein

Wenn ja, welchen Dienst verwenden Sie, um es zu generieren? Welche Attribute nehmen Sie in das Zertifikat auf (z. B. Domäne oder IP-Adresse)?

### <a name="smtp-authentication"></a>SMTP-Authentifizierung

Planen Sie die Verwendung von SMTP zum Weiterleiten von Benachrichtigungen an einen E-Mail-Server? Ja oder Nein

Falls ja, welche Authentifizierungsmethode möchten Sie verwenden?  

### <a name="active-directory-or-local-users"></a>Active Directory oder lokale Benutzer

Wenden Sie sich an einen Active Directory-Administrator, um eine Active Directory-Standortbenutzergruppe zu erstellen, oder erstellen Sie lokale Benutzer. Stellen Sie sicher, dass Ihre Benutzer auf den Bereitstellungstag vorbereitet werden. 

### <a name="iot-device-types-in-the-network"></a>IoT-Gerätetypen im Netzwerk

| Gerätetyp | Anzahl der Geräte im Netzwerk | Durchschnittliche Bandbreite |
| --------------- | ------ | ----------------------- |
| Kamera | |
| Röntgengerät | |

## <a name="see-also"></a>Siehe auch

[Informationen zur Installation von Defender für IoT](how-to-install-software.md)
