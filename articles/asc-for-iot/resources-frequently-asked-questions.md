---
title: Häufig gestellte Fragen zu Azure Security Center für IoT (Vorschauversion) | Microsoft-Dokumentation
description: Hier finden Sie Antworten auf die am häufigsten gestellten Fragen zu Features und Diensten zu Azure Security Center für IoT.
services: asc-for-iot
ms.service: ASCforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: f15cd14ddc3a489db3abbbd2665d242470b5a821
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200703"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>Azure Security Center für IoT – Häufig gestellte Fragen  

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dieser Artikel enthält eine Liste häufig gestellter Fragen (FAQ) zu Azure Security Center (ASC) für IoT sowie die zugehörigen Antworten. 

## <a name="does-azure-provide-support-for-iot-security"></a>Bietet Azure Unterstützung für IoT-Sicherheit?

Azure bietet eine integrierte Ansicht für die Überwachung und Verwaltung Ihrer IoT-Sicherheit als Teil Ihrer gesamten Sicherheitslösung über das Azure Security Center. Wenn Sie Anwendungsentwickler sind, können Sie die IoT Hub-Ansicht verwenden, um Ihre IoT-Anwendungssicherheit zu verwalten.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Was ist einzigartiges Wertangebot für IoT-Sicherheit von Azure?

ASC für IoT ermöglicht es Unternehmen, ihre bestehende Cybersicherheitsansicht auf ihre gesamte IoT-Lösung zu erweitern. Azure bietet eine ganzheitliche Ansicht Ihrer Geschäftslösung und ermöglicht es Ihnen, geschäftsbezogene Maßnahmen und Entscheidungen auf der Grundlage Ihrer Sicherheitslage und der gesammelten Daten zu treffen. Die Kombination verschiedener Sicherheitsfunktionen wie Azure IoT, Azure IoT Edge, Azure Sphere, Azure Central und Azure Security Center ermöglicht es Ihnen, die gewünschte Lösung mit der erforderlichen Sicherheit zu erstellen.

## <a name="who-is-asc-for-iot-made-for"></a>Wofür ist ASC für IoT gemacht? 

ASC für IoT ist in Azure IoT Hub Security integriert und übernimmt die Verwaltung der täglichen Sicherheitsabläufe der Unternehmenslösung. ASC für IoT ist zudem in das Azure Security Center integriert und bietet eine integrierte Ansicht für die Überwachung und Verwaltung Ihrer IoT-Sicherheit als Teil Ihrer gesamten Sicherheitslösung.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>Wie hebt sich ASC für IoT vom Wettbewerb ab?

Andere Lösungen bieten eine Reihe von Funktionen, die es Kunden ermöglichen, ihre eigenen Lösungen zu erstellen. ASC für IoT hingegen bietet eine einzigartige End-to-End-IoT-Sicherheitslösung, die einen umfassenden Überblick über die Sicherheit aller Ihrer zugehörigen Azure-Ressourcen bietet. Azure ermöglicht eine schnelle Bereitstellung und vollständige Integration mit IoT Hub-Modulzwillingen für eine einfache Integration in bestehende Geräteverwaltungstools.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Muss ich ein Kunde von Azure Security Center (ASC) sein?

Nein, aber es wird empfohlen. Ohne ASC erhält ASC für IoT nur begrenzte Daten über verbundene Ressourcen und bietet eine begrenzte Analyse Ihrer potenziellen Angriffsfläche, Bedrohungen und potenziellen Angriffe. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Muss ich Azure-IoT-Kunde sein?

Ja. ASC für IoT basiert auf der Azure IoT-Konnektivität und -Infrastruktur.

## <a name="do-i-have-to-install-an-agent"></a>Muss ich einen Agent installieren?

Die Installation des Agent auf Ihren IoT-Geräten ist nicht zwingend erforderlich, um das Microsoft ASC für IoT zu aktivieren. Sie können zwischen den folgenden drei Optionen wählen und erhalten je nach Ihrer Auswahl verschiedene Stufen der Sicherheitsüberwachung und -verwaltung:

1. Installieren des Sicherheits-Agent ASC für IoT mit oder ohne Änderungen. Diese Option bietet ein Höchstmaß an verbesserten Sicherheitsinformationen über das Geräteverhalten und den Zugriff.

2. Erstellen Ihres eigenen Agents und implementieren des ASC für IoT-Sicherheit-Nachrichtenschemas. Diese Option ermöglicht die Verwendung von Microsoft ASC für IoT-Analysetools auf Ihrem Gerätesicherheits-Agent.

3. Keine Installation eines Sicherheits-Agent auf Ihren IoT-Geräten. Diese Option ermöglicht die Kommunikationsüberwachung des IoT Hubs mit reduzierten Sicherheitsüberwachungs- und -verwaltungsfunktionen. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>Wozu der ASC für IoT-Agent?

Der ASC für IoT-Agent bietet Bedrohungsschutz auf Geräteebene für Gerätekonfiguration, -verhalten und -zugriff (durch Scannen der Konfiguration), Prozess und Konnektivität. Der ASC für IoT-Sicherheits-Agent scannt keine geschäftsrelevanten Daten oder Aktivitäten.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>Wo erhalte ich den ASC für IoT-Sicherheits-Agent?

Der ASC für IoT-Sicherheits-Agent ist Open Source und auf GitHub in 32-Bit- und 64-Bit-Versionen für Windows und Linux verfügbar: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>Wo wird der ASC für IoT-Agent installiert? 

Detaillierte Informationen zur Installation und Bereitstellung des Agent finden Sie in GitHub: https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>Wie lauten die Abhängigkeiten und Voraussetzungen für den Agent?

ASC für IoT unterstützt eine Vielzahl von Plattformen. Informationen zur Unterstützung für Ihre spezifischen Geräte finden Sie unter [Unterstützte Geräteplattformen](how-to-deploy-agent.md). 

## <a name="which-data-is-collected-by-the-agent"></a>Welche Daten sammelt der Agent?

Der Agent sammelt Daten zu Konnektivität, Zugriff, Firewallkonfiguration, Prozessliste und BS-Baseline.

## <a name="how-much-data-will-the-agent-generate"></a>Wie viele Daten generiert der Agent?

Die Generierung von Daten durch den Agent wird durch Gerät, Anwendung, Konnektivitätsart und Konfiguration des Kunde-Agent gesteuert. Aufgrund der hohen Variabilität zwischen Geräten und IoT-Lösungen empfehlen wir, den Agent zunächst in einem Labor oder einer Testumgebung einzusetzen, um die spezifische Konfiguration zu beobachten, zu lernen und einzustellen, die Ihren Bedürfnissen entspricht, und gleichzeitig die Menge der generierten Daten zu messen. Nach dem Start des Dienstes gibt der ASC für IoT-Agent operative Empfehlungen zur Optimierung des Agent-Durchsatzes, um Sie bei der Konfiguration und Anpassung zu unterstützen.

## <a name="how-can-i-control-my-billing"></a>Wie kann ich meine Abrechnung steuern?

ASC für IoT bietet konfigurierbare Agent-Scans, Datenpuffer und die Möglichkeit, benutzerdefinierte Warnmeldungen zu erstellen, die die vom Agent erzeugte Datenmenge erhöhen oder verringern.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>Wird für Nachrichten des Agent Kontingent von IoT Hub verwendet?

Ja. Die vom Agent übertragenen Daten werden in Ihrem IoT Hub-Kontingent gezählt. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>Nächste Schritte Ich habe einen Agent installiert, es werden aber keine Aktivitäten oder Protokolle angezeigt.

1. Überprüfen Sie, ob der [Typ des Agent mit der angegebenen Betriebssystemplattform Ihres Geräts übereinstimmt](how-to-deploy-agent.md).

1. Vergewissern Sie sich, dass der [Agent auf dem Gerät ausgeführt wird](how-to-agent-configuration.md).

2. Überprüfen Sie, ob der [Dienst erfolgreich](quickstart-onboard-iot-hub.md) für **Security** in Ihrem IoT Hub aktiviert wurde. 

3. Überprüfen Sie, ob das Gerät im [IoT Hub mit dem ASC für IoT-Modul konfiguriert](quickstart-create-security-twin.md) ist.  

Wenn die Aktivitäten oder Protokolle immer noch nicht verfügbar sind, wenden Sie sich an Ihren ASC für IoT-Partner, um zusätzliche Hilfe zu erhalten.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Was geschieht, wenn die Internetverbindung nicht mehr funktioniert?

Solange das Gerät läuft, wird der Agent weiterhin ausgeführt und Daten werden gespeichert. Die Daten werden entsprechend der Größenkonfiguration im Cache für Sicherheitsmeldungen gespeichert. Wenn das Gerät wieder eine Verbindung herstellt, werden die Sicherheitsmeldungen wieder gesendet. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>Stellt sich der Sicherheits-Agent selbst wieder hier, wenn das Gerät neu gestartet wird?

Der Sicherheits-Agent ist so konzipiert, dass er automatisch wieder ausgeführt wird, wenn das Gerät neu gestartet wird.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>Kann der Agent die Leistung des Geräts oder einer anderen installierten Software beeinträchtigen?

Der Agent verbraucht wie jede andere Anwendung/jeder andere Prozess Computerressourcen und sollte die normale Geräteaktivität nicht stören. Der Ressourcenverbrauch auf dem Gerät, das der Agent ausführt, ist mit der Einrichtung und Konfiguration gekoppelt. Wir empfehlen, Ihre Agentkonfiguration in einer geschlossenen Umgebung zusammen mit der Interoperabilität mit Ihren anderen IoT-Anwendungen und -Funktionen zu testen, bevor Sie versuchen, sie in einer Produktionsumgebung einzusetzen.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>Ich führe einige Wartungsmaßnahmen auf dem Gerät aus. Kann ich den Agent deaktivieren?

Der Agent kann nicht deaktiviert werden.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>Gibt es eine Möglichkeit zum Testen, ob der Agent ordnungsgemäß funktioniert? 

Wenn der Agent die Kommunikation einstellt oder keine Sicherheitsmeldungen sendet, wird eine Warnung **Gerät ist still** generiert.

## <a name="can-i-create-my-own-alerts"></a>Kann ich meine eigenen Warnungen erstellen?

Ja. Sie können eine benutzerdefinierte Warnung für vordefinierte Verhaltensweisen wie IP-Adresse und offene Ports festlegen. Weitere Informationen zu benutzerdefinierten Warnungen und deren Ausführung finden Sie unter [Erstellen von benutzerdefinierten Warnungen](quickstart-create-custom-alerts.md). 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>Wo kann ich Protokolle sehen? Kann ich Protokolle anpassen?

- Warnungen und Empfehlungen können Sie über Ihren angeschlossenen Log Analytics-Arbeitsbereich anzeigen. Im Arbeitsbereich können Sie die Speichergröße und die Dauern konfigurieren.

- Rohdaten von Ihrem Sicherheits-Agent können auch in Ihrem Log Analytics-Konto gespeichert werden. Berücksichtigen Sie Größe, Dauer, Speicherbedarf und damit verbundene Kosten, bevor Sie die Konfiguration dieser Option ändern. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>Warum sollte ich ASC für IoT zur Modulidentität hinzufügen? Verwendungszweck

Das ASC für IoT-Modul dient zur Konfiguration und Verwaltung des Agent.


## <a name="next-steps"></a>Nächste Schritte

Um mehr darüber zu erfahren, wie Sie mit ASC für IoT beginnen können, lesen Sie die folgenden Artikel:


- Lesen Sie die [Übersicht](overview.md) über ASC für IoT
- Überprüfen der [Dienstvoraussetzungen](service-prerequisites.md)
- Erfahren Sie mehr über die [Ersten Schritte](getting-started.md)
- Verstehen der [Sicherheitswarnungen von ASC für IoT](concept-security-alerts.md)

