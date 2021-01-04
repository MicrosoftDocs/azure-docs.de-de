---
title: Architektur von Azure Defender für IoT
description: Hier erfahren Sie etwas über die Architektur und den Informationsfluss von Azure Defender für IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: rkarlin
ms.openlocfilehash: a8697094a3366e3b82ca65f1b962101243b22f84
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548918"
---
# <a name="azure-defender-for-iot-architecture"></a>Architektur von Azure Defender für IoT

In diesem Artikel wird die funktionale Systemarchitektur der Defender für IoT-Lösung beschrieben.

## <a name="defender-for-iot-components"></a>Defender für IoT-Komponenten

Defender für IoT verbindet sich sowohl mit der Azure-Cloud als auch mit lokalen Komponenten. Die Lösung ist für Skalierbarkeit in großen und geografisch verteilten Umgebungen mit mehreren Remotestandorten konzipiert. Diese Lösung ermöglicht eine verteilte Architektur mit mehreren Ebenen nach Land, Region, Geschäftseinheit oder Zone. 

Azure Defender für IoT umfasst die folgenden Komponenten: 

**Mit der Cloud verbundene Bereitstellungen**

- Azure Defender für IoT-Sensor-VM oder -Appliance
- Azure-Portal für Cloudverwaltung und Integration in Azure Sentinel
- Lokale Verwaltungskonsole für lokale Standortverwaltung
- Einen eingebetteten Sicherheits-Agent (optional)

**Air-Gap-(Offline)-Bereitstellungen**

- Azure Defender für IoT-Sensor-VM oder -Appliance
- Lokale Verwaltungskonsole für lokale Standortverwaltung


![Architektur von Defender für IoT](./media/architecture/defender-iot-security-architecture.png)

### <a name="azure-defender-for-iot-sensors"></a>Azure Defender für IoT-Sensoren

Defender für IoT-Sensoren erkennen Netzwerkgeräte und überwachen sie kontinuierlich. Sensoren erfassen den ICS-Netzwerkdatenverkehr mithilfe von passiver Überwachung (ohne Agents) auf IoT- und OT-Geräten. 
 
Speziell bei IoT- und OT-Netzwerken bietet die Technologie ohne Agents innerhalb weniger Minuten, nachdem die Verbindung mit dem Netzwerk hergestellt wurde, einen umfassenden Einblick in IoT- und OT-Risiken. Aufgrund ihres Ansatzes einer nicht invasiven Netzwerkdatenverkehrsanalyse (Network Traffic Analysis, NTA) hat sie keinerlei Auswirkungen auf die Leistung des Netzwerks und der Netzwerkgeräte. 
 
Durch die Nutzung von patentierten IOT- und OT-fähigen Verhaltensanalysen und Layer-7 Deep Packet Inspection (DPI) können Sie über herkömmliche signaturbasierte Lösungen hinaus analysieren und so erweiterte IoT- und OT-Bedrohungen (z. B. dateilose Schadsoftware) auf der Grundlage anomaler oder nicht autorisierter Aktivitäten sofort erkennen. 
  
Defender für IoT-Sensoren stellen eine Verbindung mit einem SPAN-Port oder Netzwerk-TAP her und beginnen sofort mit der Ausführung von DPI für IoT- und OT-Netzwerkdatenverkehr. 
 
Datensammlung, Verarbeitung, Analyse und Warnung erfolgt direkt auf dem Sensor. Dies ist für Standorte mit einer Konnektivität von geringer Bandbreite oder hoher Latenz ideal geeignet, weil nur Metadaten an die Verwaltungskonsole übertragen werden.

Der Sensor enthält fünf Analyseerkennungs-Engines. Die Engines lösen Warnungen auf der Grundlage der Analyse von Echtzeit- und vorab aufgezeichnetem Datenverkehr aus. Die folgenden Engines stehen zur Verfügung: 

#### <a name="protocol-violation-detection-engine"></a>Erkennungs-Engine für Protokollverletzungen
Die Erkennungs-Engine für Protokollverletzungen identifiziert die Verwendung von Paketstrukturen und Feldwerten, die gegen ICS-Protokollspezifikationen verstoßen, z. B.: Modbus-Ausnahme und Initiierung einer Warnung zu veraltetem Funktionscode.

#### <a name="policy-violation-detection-engine"></a>Erkennungs-Engine für Richtlinienverletzungen
Mithilfe von maschinellem Lernen warnt die Erkennungs-Engine für Richtlinienverletzungen Benutzer vor jeder Abweichung vom Basisverhalten, z. B. nicht autorisierter Verwendung von bestimmten Funktionscodes, Zugriff auf bestimmte Objekte oder Änderungen an der Gerätekonfiguration. Beispiel: „DeltaV software version changed“ (Version der DeltaV-Software wurde geändert) und Warnungen zu „Unauthorized PLC programming“ (PLC-Programmierung nicht autorisiert). Insbesondere modelliert die Erkennungs-Engine für Richtlinienverletzungen die ICS-Netzwerke als deterministische Sequenzen von Status und Übergängen – mithilfe eines patentierten Verfahrens namens „Industrial Finite State Modeling“ (IFSM). Die Erkennungs-Engine für Richtlinienverletzungen richtet eine Baseline der ICS-Netzwerke ein, sodass die Plattform einen kürzeren Lernzeitraum zum Erstellen einer Baseline des Netzwerks erfordert als generische mathematische Ansätze oder Analysen, die ursprünglich für IT- und nicht für OT-Netzwerke entwickelt wurden.

#### <a name="industrial-malware-detection-engine"></a>Erkennungs-Engine für industrielle Schadsoftware
Die Erkennungs-Engine für industrielle Schadsoftware identifiziert Verhalten, die auf das Vorhandensein bekannter Schadsoftware hinweisen, z. B. Conficker, Black Energy, Havex, WannaCry, NotPetya und Triton. 

#### <a name="anomaly-detection-engine"></a>Anomalieerkennungs-Engine
Die Anomalieerkennungs-Engine erkennt ungewöhnliche Maschine-zu-Maschine (M2M)-Kommunikation und -Verhaltensweisen. Durch die Modellierung von ICS-Netzwerken als deterministische Sequenzen von Status und Übergängen erfordert die Plattform einen kürzeren Lernzeitraum als generische mathematische Ansätze oder Analysen, die ursprünglich für IT und nicht für OT entwickelt wurden. Sie erkennt auch Anomalien schneller, mit minimalen falsch positiven Ergebnissen. Warnungen der Anomalieerkennungs-Engine enthalten übermäßig viele SMB-Anmeldeversuche und erkannte PLC-Scans.

#### <a name="operational-incident-detection"></a>Erkennung von Betriebsvorfällen
Die Erkennung von Betriebsvorfällen erkennt Betriebsprobleme, z. B. Verbindungsunterbrechung, die auf frühe Anzeichen von Gerätefehlern hinweisen können. Beispielsweise wird vermutet, dass das Gerät getrennt ist (nicht reagiert) und dem Befehl „Siemens S7 stop PLC“ Warnungen gesendet wurden.


### <a name="management-consoles"></a>Verwaltungskonsolen
Die Verwaltung von Azure Defender für IoT in Hybridumgebungen erfolgt über zwei Verwaltungsportale: 
- Sensorkonsole
- Die lokale Verwaltungskonsole
- Das Azure-Portal

#### <a name="sensor-console"></a>Sensorkonsole
Sensorerkennungen werden in der Sensorkonsole angezeigt. Dort können sie in einer Netzwerkübersicht, im Ressourcenbestand und in einer umfangreichen Palette von Berichten (z. B. Risikobewertungsberichten, Data Mining-Abfragen und Angriffsvektoren) angezeigt, untersucht und analysiert werden. Sie können die Konsole auch verwenden, um von Sensormodulen erkannte Bedrohungen anzuzeigen und zu behandeln, Informationen an Drittanbietersysteme weiterzuleiten, Benutzer zu verwalten und vieles mehr.

![Defender für IoT-Sensorkonsole](./media/architecture/sensor-console.png)

#### <a name="on-premises-management-console"></a>Lokale Verwaltungskonsole
Mithilfe der lokalen Verwaltungskonsole können SOC-Operatoren (Security Operations Center) Warnungen verwalten und analysieren, die aus mehreren Sensoren in einem einzigen Dashboard aggregiert wurden. Außerdem bietet die Konsole eine allgemeine Übersicht über die Integrität der OT-Netzwerke.

Diese Architektur bietet eine umfassende einheitliche Ansicht des Netzwerks auf SOC-Ebene, eine optimierte Warnungsverarbeitung sowie die Kontrolle über die Sicherheit des Betriebsnetzwerks. Dadurch wird sichergestellt, dass Entscheidungsfindung und Risikomanagement einwandfrei bleiben.

Neben Mehrinstanzenfähigkeit, Überwachung, Datenanalyse und zentralisierter Sensor-Remotesteuerung stellt die Verwaltungskonsole zusätzliche Systemwartungstools (z. B. Warnungsausschluss) und vollständig angepasste Berichterstellungsfeatures für die einzelnen Remote-Appliances bereit. Diese skalierbare Architektur unterstützt sowohl die lokale Verwaltung auf Standortebene und Zonenebene als auch die globale Verwaltung innerhalb des SOC.

Die Verwaltungskonsole kann für eine Konfiguration mit hoher Verfügbarkeit bereitgestellt werden, die eine Sicherungskonsole bietet, in der Sicherungen aller für die Wiederherstellung erforderlichen Konfigurationsdateien regelmäßig empfangen werden. Wenn die Masterkonsole ausfällt, wird automatisch ein Failover der lokalen Standortverwaltungsappliances durchgeführt, um Verfügbarkeit ohne Unterbrechung aufrechtzuerhalten.

#### <a name="azure-portal"></a>Azure-Portal

Das Defender für IoT-Portal in Azure dient zu Ihrer Unterstützung beim:   Kaufen von Lösungsappliances   Installieren und Aktualisieren von Software   Integrieren von Sensoren in Azure   Aktualisieren von Threat Intelligence-Paketen

## <a name="embedded-security-agent-built-in-mode"></a>Eingebetteter Sicherheits-Agent: Modus „Integriert“

Im Modus **Integriert** wird Defender für IoT aktiviert, wenn Sie in Ihrem IoT Hub die Option **Sicherheit** aktivieren. Durch Echtzeitüberwachung, Empfehlungen und Warnungen bietet der Modus „Integriert“ Gerätesichtbarkeit und beispiellose Sicherheit in einem Schritt. Der Modus „Integriert“ erfordert keine Agentinstallation auf Geräten, und er verwendet erweiterte Analysen für protokollierte Aktivitäten zum Analysieren und Schützen Ihres Feldgeräts und von IoT Hub.

## <a name="embedded-security-agent-enhanced-mode"></a>Eingebetteter Sicherheits-Agent: Modus „Erweitert“

Nach dem Aktivieren der Option **Sicherheit** in Ihrem IoT Hub und der Installation von Defender für IoT-Geräte-Agents auf Ihren Geräten erfassen, aggregieren und analysieren die Agents im Modus **Erweitert** Sicherheitsereignis-Rohdaten von Ihren Geräten. Sicherheitsereignis-Rohdaten können IP-Verbindungen, die Prozesserstellung, Benutzeranmeldungen und andere sicherheitsrelevante Informationen enthalten. Defender für IoT-Geräte-Agents verarbeiten auch die Ereignisaggregation, um hohe Netzwerkdurchsätze zu vermeiden. Die Agents sind in hohem Maße anpassbar, sodass Sie sie für bestimmte Aufgaben wie das Senden ausschließlich wichtiger Informationen mit dem dringlichsten SLA oder für das Aggregieren umfangreicher Sicherheitsinformationen und Kontextdaten zu größeren Segmenten verwenden und auf diese Weise höhere Servicekosten vermeiden können.

Geräte-Agents und andere Anwendungen verwenden das **Azure Security Center SDK zum Senden von Sicherheitsmeldungen**, um Sicherheitsinformationen an Azure IoT Hub zu senden. IoT Hub ruft diese Informationen ab und leitet sie an den Defender für IoT-Dienst weiter.

Sobald der Defender für IoT-Dienst aktiviert ist, versendet IoT Hub zusätzlich zu den weitergeleiteten Daten alle seine internen Daten für die Analyse durch Defender für IoT. Diese Daten umfassen Cloudvorgangsprotokolle von Geräten, Geräte-Identitäten und die Hub-Konfiguration. Alle diese Informationen tragen dazu bei, die Defender für IoT-Analysepipeline zu erstellen.

Die Defender für IoT-Analysepipeline erhält ferner zusätzliche Threat Intelligence-Streams aus verschiedenen Quellen innerhalb von Microsoft sowie von Microsoft-Partnern. Die gesamte Defender für IoT-Analysepipeline arbeitet mit sämtlichen für den Dienst erstellten Kundenkonfigurationen (wie benutzerdefinierte Warnungen und die Verwendung des SDK zum Versenden von Sicherheitsmeldungen).

Bei der Verwendung der Analysepipeline kombiniert Defender für IoT alle Informationsdatenströme, um direkt umsetzbare Empfehlungen und Warnungen zu generieren. Die Pipeline enthält sowohl benutzerdefinierte Regeln, die von Sicherheitsanalytikern und Experten erstellt wurden, als auch nach Abweichungen vom standardmäßigen Geräteverhalten suchende Machine Learning-Modelle und Risikoanalysen.

Defender für IoT-Empfehlungen und -Warnungen (Analysepipeline-Ausgabe) werden in den Log Analytics-Arbeitsbereich des einzelnen Kunden geschrieben. Durch die Einbeziehung der Ereignisrohdaten im Arbeitsbereich sowie der Warnungen und Empfehlungen werden eingehende Untersuchungen und Abfragen möglich, die die genauen Details der festgestellten verdächtigen Aktivitäten verwenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die grundlegende Architektur und den Workflow der Defender für IoT-Lösung kennengelernt. Weitere Informationen zu den Voraussetzungen sowie zum Einstieg in Ihre Sicherheitslösung in IoT Hub und zu deren Aktivierung finden Sie in den folgenden Artikeln:

- [ASC für IoT-Voraussetzungen](service-prerequisites.md)
- [Erste Schritte](getting-started.md)
- [Konfigurieren Ihrer IoT-Lösung](quickstart-configure-your-solution.md)
- [Enable security in IoT Hub (Aktivieren der Sicherheit in IoT Hub)](quickstart-onboard-iot-hub.md)
- [Häufig gestellte Fragen zu Defender für IoT](resources-frequently-asked-questions.md)
- [Defender für IoT-Sicherheitswarnungen](concept-security-alerts.md)
