---
title: Glossar zu Defender für IoT
description: Dieses Glossar enthält eine Kurzbeschreibung wichtiger Begriffe und Konzepte der Defender für IoT-Plattform.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 8007f11cb37063cef46ac7f3ac51e23bdfaae11a
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226453"
---
# <a name="defender-for-iot-glossary"></a>Glossar zu Defender für IoT

Dieses Glossar enthält eine Kurzbeschreibung wichtiger Begriffe und Konzepte der Azure Defender für IoT-Plattform. Wählen Sie die Links unter **Weitere Informationen** aus, um zu verwandten Begriffen im Glossar zu gelangen. So können Sie Tools im Produkt schneller erlernen und einsetzen.

<a name="glossary-a"></a>

## <a name="a"></a>A

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Zugriffsgruppe** | Erfüllen Sie die Zugriffsanforderungen von Benutzern in großen Organisationen, indem Sie Regeln für Zugriffsgruppen erstellen.<br /><br />Mithilfe von Regeln können Sie den Ansichts- und Konfigurationszugriff auf die lokale Verwaltungskonsole von Defender für IoT für bestimmte Benutzerrollen für relevante Geschäftseinheiten, Regionen, Standorte und Zonen steuern.<br /><br />Sie können z. B. Sicherheitsanalysten einer Active Directory-Gruppe den Zugriff auf westeuropäische Fahrzeugdaten erlauben, aber den Zugriff auf Daten in Afrika verhindern. | **[Lokale Verwaltungskonsole](#o)** <br /><br />**[Geschäftseinheit](#b)** |
| **Zugriffstoken** | Generieren Sie Zugriffstoken für den Zugriff auf die Rest-API von Defender für IoT. | **[API](#glossary-a)** |
| **Bestätigen des Warnungsereignisses** | Weisen Sie Defender für IoT an, die Warnung für das erkannte Ereignis einmal auszublenden. Die Warnung wird erneut ausgelöst, wenn das Ereignis wieder erkannt wird. | **[Warnung](#glossary-a)<br /><br />[Erfassen des Warnungsereignisses](#l)<br /><br />[Stummschalten des Warnungsereignisses](#m)** |
| **Warnung** | Eine Benachrichtigung, die eine Defender für IoT-Engine bei Abweichungen vom autorisierten Netzwerkverhalten, Netzwerkanomalien oder verdächtigen Netzwerkaktivitäten und verdächtigem Datenverkehr auslöst. | **[Weiterleitungsregel](#f)<br /><br />[Ausschlussregel](#e)<br /><br />[Systembenachrichtigungen](#s)** |
| **Warnungskommentar** | Kommentare von Sicherheitsanalysten und Administratoren in Warnungsmeldungen. Ein Warnungskommentar kann z. B. Anweisungen für zu ergreifende Entschärfungsmaßnahmen oder die Namen von Personen enthalten, die bei diesem Ereignis informiert werden müssen.<br /><br />Benutzer, die Warnungen überprüfen, können den oder die Kommentare wählen, die den Ereignisstatus oder die unternommenen Schritte zum Untersuchen der Warnung am besten widerspiegeln. | **[Warnung](#glossary-a)** |
| **Anomalie-Engine** | Eine Defender für IoT-Engine, die ungewöhnliche Kommunikation zwischen Computern oder Verhaltensmuster erkennt. Beispielsweise kann die Engine übermäßig viele SMB-Anmeldeversuche erkennen. Anomaliewarnungen werden ausgelöst, wenn diese Ereignisse erkannt werden. | **[Defender für IoT-Engines](#d)** |
| **API** | Ermöglicht externen Systemen den Zugriff auf Daten, die von Defender für IoT erkannt wurden, und die Ausführung von Aktionen unter Verwendung der externen REST-API über SSL-Verbindungen. | **[Zugriffstoken](#glossary-a)** |
| **Bericht zu Angriffsvektoren** | Eine grafische Echtzeitdarstellung von Risikoketten von Geräten mit Sicherheitslücken.<br /><br />Die Berichte ermöglichen Ihnen, die Wirkung von Entschärfungsaktivitäten in der zu bestimmenden Angriffssequenz zu bewerten. Sie können beispielsweise bewerten, ob ein Systemupgrade dem Angreifer durch Unterbrechen der Angriffskette den Weg abschneidet oder ob ein alternativer Angriffsweg bestehen bleibt. Dadurch lassen sich Wiederherstellungs- und Entschärfungsaktivitäten priorisieren. | **[Bericht zur Risikobewertung](#r)** |

## <a name="b"></a>B

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Geschäftseinheit** | Eine logische Organisation Ihres Geschäfts nach bestimmten Branchen.<br /><br />Beispielsweise kann ein globales Unternehmen, das Glas- und Kunststofffabriken unterhält, als zwei verschiedene Geschäftseinheiten geführt werden. Sie können den Zugriff von Defender für IoT-Benutzer auf bestimmte Geschäftseinheiten steuern. | **[Lokale Verwaltungskonsole](#o)<br /><br />[Zugriffsgruppe](#glossary-a)<br /><br />[Standort](#s)<br /><br />[Zone](#z)** |
| **Baseline** | Genehmigte Angaben zu Netzwerkdatenverkehr, Protokollen, Befehlen und Geräten. Defender für IoT erkennt Abweichungen von der Netzwerkbaseline. Sehen Sie sich die genehmigte Baseline für Datenverkehr an, indem Sie Data Mining-Berichte generieren. | **[Data Mining](#d)<br /><br />[Lernmodus](#l)** |

## <a name="c"></a>C

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **CLI-Befehle** | Optionen der Befehlszeilenschnittstelle (CLI) für Administratorbenutzer von Defender für IoT. CLI-Befehle sind für Features verfügbar, auf die über die Defender für IoT-Konsolen nicht zugegriffen werden kann. | - |


## <a name="d"></a>D

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Data Mining** | Generieren Sie umfassende und detaillierte Berichte zu Ihren Netzwerkgeräten:<br /><br />- **SOC-Reaktion auf Vorfälle**: Berichte in Echtzeit, um die sofortige Reaktion auf Vorfälle zu unterstützen. Beispielsweise können in einem Bericht Geräte aufgelistet werden, die möglicherweise gepatcht werden müssen.<br /><br />- **Forensik**: Berichte auf Grundlage von Verlaufsdaten für Untersuchungsberichte.<br /><br />- **IT-Netzwerkintegrität**: Berichte, die helfen, die allgemeine Netzwerksicherheit zu verbessern. Ein Bericht kann beispielsweise Geräte mit unsicheren Anmeldeinformationen für die Authentifizierung enthalten.<br /><br />- **Sichtbarkeit**: Berichte, die alle Abfragepunkte abdecken, um alle Baselineparameter Ihres Netzwerks darzustellen.<br /><br />Speichern Sie Data Mining-Berichte, die Benutzer mit Leseberechtigung anzeigen können. | **[Baseline](#b)<br /><br />[Berichte](#r)** |
| **Defender für IoT-Engines** | Durch selbstlernende Analyse-Engines in Defender für IoT müssen keine Signaturen aktualisiert oder Regeln definiert werden. Die Engines arbeiten mit ICS-spezifischen Verhaltensanalysen und Data Science, um den OT-Netzwerkdatenverkehr fortlaufend auf Anomalien, Schadsoftware, Betriebsprobleme, Protokollverletzungen und Abweichungen von der Baseline für die Netzwerkaktivität zu untersuchen.<br /><br />Wenn eine Engine eine Abweichung erkennt, wird eine Warnung ausgelöst. Warnungen können über den Bildschirm **Warnungen** oder eine SIEM-Lösung angezeigt und verwaltet werden. | **[Warnung](#glossary-a)** |
| **Defender für IoT-Plattform** | Die Defender für IoT-Lösung, die in den Defender für IoT-Sensoren und der lokalen Verwaltungskonsole installiert ist. | **[Sensor](#s)<br /><br />[lokale Verwaltungskonsole](#o)** |
| **Geräteübersicht** | Eine grafische Darstellung der von Defender für IoT erkannten Netzwerkgeräte. Sie zeigt die Verbindungen zwischen Geräten und Informationen zu jedem Gerät. Nutzen Sie die Übersicht für folgende Aufgaben:<br /><br />Abrufen und Steuern von Informationen zu kritischen Geräten<br /><br />Analysieren von Netzwerksegmenten<br /><br />Exportieren von Gerätedetails und -zusammenfassungen | **[Gruppe „Purdue-Ebenen“](#p)** |
| **Gerätebestand: Sensor** | Im Gerätebestand wird eine umfangreiche Palette der von Defender für IoT erfassten Geräteattribute angezeigt. Für die folgenden Aufgaben stehen Optionen zur Verfügung:<br /><br />Filtern der angezeigten Informationen<br /><br />Exportieren dieser Informationen in eine CSV-Datei<br /><br />Importieren von Windows-Registrierungsdetails | **[Gruppe](#g)** <br /><br />**[Gerätebestand: lokale Verwaltungskonsole](#d)** |
| **Gerätebestand: lokale Verwaltungskonsole** | Von angeschlossenen Sensoren stammende Geräteinformationen können über die lokale Verwaltungskonsole im Gerätebestand eingesehen werden. Dadurch erhalten Benutzer der lokalen Verwaltungskonsole eine umfassende Übersicht über alle Netzwerkinformationen. | **[Gerätebestand: Sensor](#d)<br /><br />[Gerätebestand: Datenintegration](#d)** |
| **Gerätebestand: Datenintegration** | Mit den Datenintegrationsfunktionen der lokalen Verwaltungskonsole können Sie die Daten im Gerätebestand mit Informationen aus anderen Unternehmensressourcen verbessern. Beispielressourcen sind Datenbanken für die Konfigurationsverwaltung, DNS, Firewalls und Web-APIs. | **[Gerätebestand: lokale Verwaltungskonsole](#d)** |

## <a name="e"></a>E

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Unternehmenssicht** | Eine globale Übersicht, die Geschäftseinheiten, Websites und Zonen darstellt, in denen Defender für IoT-Sensoren installiert sind. Sehen Sie sich geografische Standorte von Warnungen zu schädlichen Aktivitäten, Betriebswarnungen usw. an. | **[Geschäftseinheit](#b)<br /><br />[Standort](#s)<br /><br />[Zone](#z)** |
| **Zeitskala der Ereignisse** | Eine Zeitskala der im Netzwerk erkannten Aktivitäten, einschließlich:<br /><br />Ausgelöste Warnungen<br /><br />Netzwerkereignisse (zur Information)<br /><br />– Benutzervorgänge wie z. B. Anmeldung, Löschung und Erstellung von Benutzern sowie Warnungsverwaltungsvorgänge wie Stummschalten, Erfassen und Bestätigen. In den Sensorkonsolen verfügbar. | - |
| **Ausschlussregel** | Weisen Sie Defender für IoT an, Auslöser für Warnungen zu ignorieren, die auf Zeiträumen, Geräteadressen und Namen von Warnungen oder auf einem bestimmten Sensor basieren.<br /><br />Wenn Sie z. B. wissen, dass alle von einem bestimmten Sensor überwachten OT-Geräte zwischen 6:30 und 10:15 Uhr morgens eine Wartung durchlaufen, können Sie eine Ausschlussregel festlegen, der zufolge dieser Sensor in dem vordefinierten Zeitraum keine Warnungen senden soll. | **[Warnung](#glossary-a)<br /><br />[Stummschalten des Warnungsereignisses](#m)** |

## <a name="f"></a>F

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Weiterleitungsregel** | Weiterleitungsregeln weisen Defender für IoT an, Warnungsinformationen an Partneranbieter oder -systeme zu senden.<br /><br />Senden Sie beispielsweise Warnungsinformationen an einen Splunk- oder Syslog-Server. | **[Warnung](#glossary-a)** |

## <a name="g"></a>G

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Gruppe** | Vordefinierte oder benutzerdefinierte Gruppen von Geräten mit bestimmten Attributen, z. B. Geräte, die Programmieraktivitäten durchgeführt haben, oder Geräte, die sich in einem bestimmten Subnetz befinden. Mithilfe von Gruppen können Sie Geräte anzeigen und in Defender für IoT analysieren.<br /><br />Gruppen können in der Geräteübersicht und im Gerätebestand angezeigt und erstellt werden. | **[Geräteübersicht](#d)<br /><br />[Gerätebestand](#d)** |

## <a name="h"></a>H

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Open Development Environment von Horizon** | Schützen Sie IoT- und ICS-Geräte, auf denen proprietäre, benutzerdefinierte oder von Standards abweichende Protokolle ausgeführt werden. Nutzen Sie das ODE SDK (Open Development Environment) von Horizon zum Entwickeln von Zergliederungs-Plug-Ins, die Netzwerkdatenverkehr basierend auf definierten Protokollen decodieren. Defender für IoT-Dienste analysieren Datenverkehr und bieten umfangreiche Überwachungs-, Warnungs- und Berichterstellungsfunktionen.<br /><br />Verwenden Sie Horizon für Folgendes:<br /><br />- **Erhöhen** der Transparenz und Kontrolle, ohne Versionen der Defender für IoT-Plattformen upgraden zu müssen<br /><br />- **Schützen** proprietärer Informationen durch lokale Entwicklung als externes Plug-In<br /><br />- **Lokalisieren** des Texts von Warnungen, Ereignissen und Protokollparametern<br /><br />Weitere Einzelheiten erhalten Sie von Ihrem Kundenbetreuer. | **[Protokollunterstützung](#p)<br /><br />[Lokalisierung](#l)** |
| **Benutzerdefinierte Horizon-Warnung** | Verbessern Sie die Warnungsverwaltung in Ihrem Unternehmen durch Auslösen von benutzerdefinierten Warnungen für ein beliebiges Protokoll (basierend auf Zergliederungen von Netzwerkdatenverkehr des Horizon-Frameworks).<br /><br />Diese Warnungen können zur Übermittlung von Informationen verwendet werden:<br /><br />Informationen zur Erkennung von Datenverkehr basierend auf Protokollen und zugrunde liegenden Protokollen in einem proprietären Horizon-Plug-In<br /><br />Informationen zu einer Kombination von Protokollfeldern auf allen Protokollebenen | **[Protokollunterstützung](#p)** |

## <a name="i"></a>I

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Integrationen** | Erweitern Sie Defender für IoT-Funktionen, indem Sie Geräteinformationen mit Partnersystemen teilen. Unternehmen können bisher isolierte Lösungen für Sicherheit, Steuerung des Netzwerkzugriffs, Vorfallreaktion und Geräteverwaltung miteinander verbinden, um systemweite Reaktionen zu beschleunigen und Risiken schneller zu entschärfen. | **[Weiterleitungsregel](#f)** |
| **Internes Subnetz** | Von Defender für IoT definierte Subnetzkonfigurationen. In einigen Fällen, etwa in Umgebungen, in denen öffentliche Bereiche als interne Bereiche fungieren, können Sie Defender für IoT anweisen, alle Subnetze als interne Subnetze aufzulösen. Subnetze werden in der Übersicht und verschiedenen Defender für IoT-Berichten angezeigt. | **[Subnetze](#s)** |

## <a name="l"></a>L

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Erfassen eines Warnungsereignisses** | Weisen Sie Defender für IoT an, den in einem Ereignis der Warnung erkannten Datenverkehr zuzulassen. | **[Warnung](#glossary-a)<br /><br />[Bestätigen des Warnungsereignisses](#glossary-a)<br /><br />[Stummschalten des Warnungsereignisses](#m)** |
| **Lernmodus** | Der Modus, in dem Defender für IoT Ihre Netzwerkaktivitäten erfasst. Diese Aktivität wird zur Baseline Ihres Netzwerks. Defender für IoT verbleibt nach der Installation für einen vordefinierten Zeitraum in diesem Modus. Aktivität, die nach diesem Zeitraum von der erfassten Aktivität abweicht, löst Defender für IoT-Warnungen aus. | **[Intelligente Erfassung von IT-Aktivität](#s)<br /><br />[Baseline](#b)** |
| **Lokalisierung** | Lokalisieren Sie Text von Warnungen, Ereignissen und Protokollparametern für von Horizon entwickelte Zergliederungs-Plug-Ins. | **[Open Development Environment von Horizon](#h)** |

## <a name="m"></a>M

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Stummschalten des Warnungsereignisses** | Weisen Sie Defender für IoT an, Aktivitäten mit identischen Geräten und vergleichbarem Datenverkehr durchgängig zu ignorieren. | **[Warnung](#glossary-a)<br /><br />[Ausschlussregel](#e)<br /><br />[Bestätigen des Wartungsereignisses](#glossary-a)<br /><br />[Erfassen des Warnungsereignisses](#l)** |

## <a name="n"></a>N

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Benachrichtigungen** | Informationen zu Netzwerkänderungen oder nicht aufgelösten Geräteeigenschaften. Es stehen Optionen zur Verfügung, um Geräte- und Netzwerkinformationen mit neu erkannten Daten zu aktualisieren. Durch die Reaktion auf Benachrichtigungen werden der Gerätebestand, die Übersicht und verschiedene Berichte ergänzt. In Sensorkonsolen verfügbar. | **[Warnung](#glossary-a)<br /><br />[Systembenachrichtigungen](#s)** |

## <a name="o"></a>O

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Lokale Verwaltungskonsole** | Die lokale Verwaltungskonsole bietet eine zentrale Ansicht und Verwaltung von Geräten und Bedrohungen, die bei Bereitstellung von Defender für IoT-Sensoren in Ihrer Organisation erkannt werden. | **[Defender für IoT-Plattform](#d)<br /><br />[Sensor](#s)** |
| **Betriebswarnung** | Warnungen, die sich mit Problemen beim Netzwerkbetrieb befassen, z. B. ein Gerät, bei dem der Verdacht besteht, dass es nicht mit dem Netzwerk verbunden ist. | **[Warnung](#glossary-a)<br /><br />[Sicherheitswarnung](#s)** |

## <a name="p"></a>P

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Purdue-Ebene** | Zeigt die gegenseitigen Verbindungen und Abhängigkeiten von Hauptkomponenten bei einer typischen gemeinsame Nutzung der Internetverbindung (Internet Connection Sharing, ICS) in der Übersicht an. |  |
| **Protokollunterstützung** | Zusätzlich zur integrierten Protokollunterstützung können Sie IoT- und ICS-Geräte schützen, auf denen proprietäre, benutzerdefinierte oder von Standards abweichende Protokolle ausgeführt werden, indem Sie das Open Development Environment SDK von Horizon verwenden. | **[Open Development Environment von Horizon](#h)** |

## <a name="r"></a>R

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Region** | Eine logische Aufteilung einer globalen Organisation in geografische Regionen. Beispiele sind Nordamerika, Westeuropa und Osteuropa.<br /><br />In Nordamerika gibt es möglicherweise Fabriken aus verschiedenen Geschäftseinheiten. | **[Zugriffsgruppe](#glossary-a)<br /><br />[Geschäftseinheit](#b)<br /><br />[lokale Verwaltungskonsole](#o)<br /><br />[Standort](#s)<br /><br />[Zone](#z)** |
| **Berichte** | Berichte geben die von Data Mining-Abfrageergebnissen generierten Informationen wieder. Dies schließt Data Mining-Standardergebnisse ein, die in der Ansicht **Berichte** verfügbar sind. Administratoren und Sicherheitsanalysten können außerdem benutzerdefinierte Data Mining-Abfragen generieren und als Berichte speichern. Diese Berichte sind auch für Benutzer mit Leseberechtigung verfügbar. | **[Data Mining](#d)** |
| **Bericht zur Risikobewertung** | Mithilfe von Berichten zur Risikobewertung können Sie eine Sicherheitsbewertung für jedes Netzwerkgerät sowie eine Sicherheitsbewertung für das gesamte Netzwerk erstellen. Das Gesamtergebnis gibt den Prozentsatz bezogen auf 100 %-ige Sicherheit an. Der Bericht enthält Empfehlungen zur Entschärfung, mit deren Hilfe Sie Ihre aktuelle Sicherheitsbewertung verbessern können. | - |

## <a name="s"></a>E

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Sicherheitswarnung** | Warnungen, die sich auf Sicherheitsprobleme beziehen, z. B. übermäßige SMB-Anmeldeversuche oder Erkennungen von Schadsoftware. | **[Warnung](#glossary-a)<br /><br />[Betriebswarnung](#o)** |
| **Selektive Untersuchung** | Defender für IoT untersucht passiv den IT- und OT-Datenverkehr und erkennt u. a. relevante Informationen über Geräte, ihre Attribute und ihr Verhalten. In bestimmten Fällen sind einige Informationen in passiven Netzwerkanalysen möglicherweise nicht sichtbar.<br /><br />Dann können Sie die sicheren, zielgerichteten Untersuchungstools in Defender für IoT verwenden, um wichtige Informationen auf bislang nicht erreichbaren Geräten zu entdecken. | - |
| **Sensor** | Der physische oder virtuelle Computer, auf dem die Defender für IoT-Plattform installiert ist. | **[Lokale Verwaltungskonsole](#o)** |
| **Website** | Der Ort einer Fabrik oder anderen Entität. Der Standort sollte eine oder mehrere Zonen aufweisen, in denen ein Sensor installiert ist. | **[Zone](#z)** |
| **Standortverwaltung** | Die Option für eine lokale Verwaltungskonsole, mit der Sie Sensoren im Unternehmen verwalten können. | - |
| **Intelligente Erfassung von IT-Aktivität** | Nach Abschluss der Lernphase und Deaktivieren des Lernmodus stellt Defender für IoT möglicherweise ein ungewöhnlich hohes Niveau an Abweichungen von der Baseline fest, die das Ergebnis normaler IT-Aktivitäten sind, wie etwa DNS- und HTTP-Anforderungen. Dieser Datenverkehr kann unnötige Warnungen aufgrund von Richtlinienverstößen und Systembenachrichtigungen auslösen. Zum Verringern dieser Warnungen und Benachrichtigungen können Sie die intelligente Erfassung von IT-Aktivität aktivieren. | **[Lernmodus](#l)<br /><br />[Baseline](#b)** |
| **Subnetze** | Um den Schwerpunkt auf die OT-Geräte zu legen, werden IT-Geräte in der Geräteübersicht automatisch nach Subnetz aggregiert. Jedes Subnetz wird in der Übersicht als einzelne Entität dargestellt, einschließlich einer interaktiven Funktion zum Auf-/Zuklappen, die den Fokus auf ein IT-Subnetz und die Rückkehr ermöglicht. | **[Geräteübersicht](#d)** |
| **Systembenachrichtigungen** | Benachrichtigungen der lokalen Verwaltungskonsole zu Folgendem:<br /><br />Verbindungsstatus des Sensors<br /><br />Remotesicherungsfehler | **[Benachrichtigungen](#n)<br /><br />[Warnung](#glossary-a)** |

## <a name="z"></a>Z

| Begriff | BESCHREIBUNG | Weitere Informationen |
|--|--|--|
| **Zone** | Ein Bereich innerhalb eines Standorts, in dem ein oder mehrere Sensoren installiert sind. | **[Standort](#s)<br /><br />[Geschäftseinheit](#b)<br /><br />[Region](#r)** |
