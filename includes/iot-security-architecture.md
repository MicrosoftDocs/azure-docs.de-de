---
title: include file
description: include file
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28609ad27330ae4ea5ea7c0d02d5a61181fbe0df
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559336"
---
Beim Entwerfen eines Systems ist es wichtig zu verstehen, welche potenziellen Bedrohungen es für das System gibt. Anschließend müssen beim Entwerfen und Zusammenstellen des Systems die richtigen Abwehrmaßnahmen ergriffen werden. Es ist besonders wichtig, schon zu Beginn des Entwurfsprozesses für das Produkt an die Sicherheit zu denken. Indem ein Verständnis entwickelt wird, wie Angreifer ein System unter Umständen kompromittieren können, wird sichergestellt, dass von Anfang an für geeignete Lösungen gesorgt ist.

## <a name="security-starts-with-a-threat-model"></a>Sicherheit beginnt mit einem Bedrohungsmodell

Microsoft nutzt schon seit langer Zeit Bedrohungsmodelle für seine Produkte und hat den Bedrohungsmodellprozess des Unternehmens öffentlich verfügbar gemacht. Die Unternehmenserfahrung zeigt, dass mit der Modellierung unerwartete Vorteile verbunden sind. Diese Vorteile reichen über das reine Verständnis hinaus, welche Bedrohungen am gefährlichsten sind. Beispielsweise wird auch ein Weg für eine offene Diskussion mit anderen Personen außerhalb des Entwicklungsteams bereitet, die zu neuen Ideen und Produktverbesserungen führen kann.

Das Ziel der Bedrohungsmodellierung besteht darin zu verstehen, wie ein Angreifer ein System kompromittieren kann, und anschließend für die richtigen Lösungen zu sorgen. Bei der Bedrohungsmodellierung muss das Designteam Lösungen schon während des Entwurfsprozesses im Blick haben, anstatt erst nach der Bereitstellung des Systems. Diese Tatsache ist von entscheidender Bedeutung, da das Ausstatten von unzähligen Geräten im Feld mit Sicherheitsmaßnahmen übermäßig kompliziert, fehleranfällig und für Kunden risikoreich ist.

Viele Entwicklungsteams leisten hervorragende Arbeit, was die Erfassung der Funktionsanforderungen für das System betrifft, die für Kunden Vorteile bedeuten. Die Identifizierung von nicht offensichtlichen Wegen, auf denen Personen das System missbrauchen können, stellt eine größere Herausforderung dar. Anhand der Bedrohungsmodellierung können Entwicklungsteams besser verstehen, was ein Angreifer unternehmen kann und warum dies möglich ist. Die Bedrohungsmodellierung ist ein strukturierter Prozess, bei dem Diskussionen zu folgenden Punkten angestoßen werden: Sicherheitsentwurfsentscheidungen im System und Änderungen am Entwurf, die während des Prozesses vorgenommen werden und die Sicherheit betreffen. Ein Bedrohungsmodell ist zwar lediglich ein Dokument, aber diese Dokumentation ist auch ein idealer Weg, um die Kontinuität des Wissens sicherzustellen, Erkenntnisse aufzuzeichnen und neue Teams schneller einzuarbeiten. Ein weiteres Ergebnis der Bedrohungsmodellierung ist, dass Sie auch andere Sicherheitsaspekte berücksichtigen können, z.B. welche Sicherheitszusagen Sie gegenüber Ihren Kunden machen möchten. Diese Zusagen liefern zusammen mit der Bedrohungsmodellierung die Informationen und die Grundlage für das Testen Ihrer Lösung für das Internet der Dinge (IoT).

### <a name="when-to-do-threat-modeling"></a>Wann sollte eine Bedrohungsmodellierung durchgeführt werden?

Die [Bedrohungsmodellierung](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) hat den größten Nutzen, wenn sie in die Entwurfsphase eingebunden wird. Beim Entwerfen haben Sie die größtmögliche Flexibilität, um Änderungen vorzunehmen, mit denen Bedrohungen beseitigt werden sollen. Das gewünschte Ergebnis ist, Bedrohungen standardmäßig zu beseitigen. Dies ist viel einfacher als das Hinzufügen von Abhilfemaßnahmen, das Testen dieser Maßnahmen und die Sicherstellung, dass sie stets aktuell sind. Außerdem ist eine Beseitigung auf diese Art nicht immer möglich. Es wird schwieriger, Bedrohungen zu beseitigen, wenn ein Produkt einen höheren Entwicklungsgrad erreicht. Darüber hinaus sind letztendlich mehr Arbeitsschritte und deutlich mehr Kompromisse als bei der Bedrohungsmodellierung zu einem frühen Zeitpunkt der Entwicklung erforderlich.

### <a name="what-to-consider-for-threat-modeling"></a>Was muss bei der Bedrohungsmodellierung berücksichtigt werden?

Es empfiehlt sich, die Lösung als Ganzes zu betrachten und sich auf folgende Bereiche zu konzentrieren:

* Sicherheits- und Datenschutzfeatures
* Features, deren Ausfall sicherheitsrelevant ist
* Features, die eine Vertrauensgrenze betreffen

### <a name="who-performs-threat-modeling"></a>Wer kümmert sich um die Bedrohungsmodellierung?

Die Bedrohungsmodellierung ist ein Vorgang wie jeder andere. Es ist ratsam, das Bedrohungsmodell-Dokument genau wie andere Komponenten der Lösung zu behandeln und zu validieren. Viele Entwicklungsteams leisten hervorragende Arbeit, was die Erfassung der Funktionsanforderungen für das System betrifft, die für Kunden Vorteile bedeuten. Die Identifizierung von nicht offensichtlichen Wegen, auf denen Personen das System missbrauchen können, stellt eine größere Herausforderung dar. Anhand der Bedrohungsmodellierung können Entwicklungsteams besser verstehen, was ein Angreifer unternehmen kann und warum dies möglich ist.

### <a name="how-to-perform-threat-modeling"></a>Wie wird die Bedrohungsmodellierung durchgeführt?

Der Vorgang zum Erstellen von Bedrohungsmodellen besteht aus den folgenden vier Schritten:

* Modellieren der Anwendung
* Auflisten von Bedrohungen
* Minimieren von Risiken
* Überprüfen der Abwehrmaßnahmen

#### <a name="the-process-steps"></a>Prozessschritte

Befolgen Sie beim Erstellen eines Bedrohungsmodells diese drei Faustregeln:

1. Erstellen Sie ein Diagramm der Referenzarchitektur.

2. Beginnen Sie mit einem „breiten“ Ansatz. Verschaffen Sie sich einen Überblick, und entwickeln Sie ein Verständnis des gesamten Systems, bevor Sie in die Tiefe gehen. Durch diese Herangehensweise ist sichergestellt, dass Sie an den richtigen Stellen in die Tiefe gehen.

3. Treiben Sie den Prozess voran, und lassen Sie sich nicht vom Prozess vorantreiben. Falls in der Modellierungsphase ein Problem auftritt und Sie dieses untersuchen möchten, dann tun Sie es! Sie müssen die angegebenen Schritte nicht sklavisch ausführen.

#### <a name="threats"></a>Bedrohungen

Die vier Kernelemente eines Bedrohungsmodells sind:

* Prozesse wie Webdienste, Win32-Dienste oder *nix-Daemons. Einige komplexe Entitäten (z.B. Bereichsgateways und Sensoren) können als Prozess abstrahiert werden, wenn in diesen Bereichen kein technischer Drilldown möglich ist.

* Datenspeicher (alle Speicherorte von Daten, z.B. eine Konfigurationsdatei oder eine Datenbank)

* Datenfluss (Daten werden zwischen anderen Elementen in der Anwendung verschoben)

* Externe Entitäten (alle Elemente, die mit dem System interagieren, aber nicht von der Anwendung kontrolliert werden, z.B. Benutzer und Satelliten-Feeds)

Alle Elemente im Architekturdiagramm unterliegen verschiedenen Bedrohungen. In diesem Artikel wird der mnemonische STRIDE-Code verwendet. Weitere Informationen zu den STRIDE-Elementen finden Sie unter [Threat Modeling Again, STRIDE](/archive/blogs/larryosterman/threat-modeling-again-stride) (Noch einmal Bedrohungsmodellierung: STRIDE).

Verschiedene Elemente des Anwendungsdiagramms unterliegen bestimmten STRIDE-Bedrohungen:

* Prozesse unterliegen STRIDE
* Datenflüsse unterliegen TID
* Datenspeicher unterliegen TID und manchmal R, wenn die Datenspeicher Protokolldateien sind.
* Externe Entitäten unterliegen SRD

## <a name="security-in-iot"></a>Sicherheit im Internet der Dinge (IoT)

Verbundene Geräte für spezielle Zwecke verfügen über eine erhebliche Anzahl von potenziellen Interaktionsoberflächen-Bereichen und Interaktionsmustern. Diese müssen alle berücksichtigt werden, um ein geeignetes Framework zum Schützen des digitalen Zugriffs auf diese Geräte bereitzustellen. Der Begriff „digitaler Zugriff“ wird hier zur Unterscheidung von allen Vorgängen verwendet, die per direkter Geräteinteraktion durchgeführt werden, bei der die Zugriffssicherheit durch eine physische Zugangskontrolle erreicht wird. Ein Beispiel hierfür ist die Anordnung des Geräts in einem Raum, dessen Tür mit einem Schloss verriegelt werden kann. Der physische Zugang kann zwar nicht mit Software und Hardware verhindert werden, aber es können Maßnahmen ergriffen werden, um zu verhindern, dass der physische Zugang zu einem Eingriff in das System führt.

Wenn Sie die Interaktionsmuster untersuchen, wenden Sie die gleiche Aufmerksamkeit auf „Gerätesteuerung“ und „Gerätedaten“ an. „Gerätesteuerung“ kann als alle Informationen klassifiziert werden, die für ein Gerät von einer Partei mit dem Ziel bereitgestellt werden, sein Verhalten gegenüber seinem Zustand oder dem Zustand der Umgebung zu beeinflussen. „Gerätedaten“ können als alle Informationen deklariert werden, die von einem Gerät in Bezug auf seinen Zustand und den beobachteten Zustand seiner Umgebung an andere Parteien weitergegeben werden.

Um die für die Sicherheit relevanten bewährten Methoden zu optimieren, wird empfohlen, eine typische IoT-Architektur im Rahmen der Bedrohungsmodellierung in mehrere Komponenten/Zonen zu unterteilen. Diese Zonen werden in diesem Abschnitt umfassend beschrieben und lauten:

* Gerät,
* Bereichsgateway,
* Cloudgateways und
* Dienste.

Zonen sind ein allgemeiner Ansatz zum Segmentieren einer Lösung. Jede Zone verfügt darüber hinaus häufig über eigene Anforderungen an Daten, Authentifizierung und Autorisierung. Zonen können auch verwendet werden, um Schäden zu isolieren und die Auswirkungen von niedrigeren Vertrauenszonen auf höher angeordnete Vertrauenszonen zu begrenzen.

Alle Zonen sind durch eine Vertrauensgrenze getrennt. Diese ist im nachstehenden Diagramm als rote Linie dargestellt. Sie gibt den Übergang der Daten/Informationen von einer Quelle zur anderen an. Bei diesem Übergang können die Daten/Informationen Angriffen vom Typ Spoofing, Manipulation, Nichtanerkennung, Offenlegung von Daten, Denial of Service und Rechteerweiterungen (STRIDE) ausgesetzt sein.

![IoT-Sicherheitszonen](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Die Komponenten, die innerhalb der einzelnen Grenzen dargestellt sind, werden auch einem STRIDE-Vorgang unterzogen, sodass eine vollständige 360-Grad-Ansicht der Bedrohungsmodellierung möglich ist. In den nachstehenden Abschnitten werden die einzelnen Komponenten und jeweiligen Sicherheitsbedenken und sowie die Lösungen, für die gesorgt werden sollte, näher erläutert.

In den nachstehenden Abschnitten werden Standardkomponenten beschrieben, die in diesen Zonen normalerweise angeordnet sind.

### <a name="the-device-zone"></a>Gerätezone

Die Geräteumgebung ist der direkte physische Bereich um das Gerät herum, in dem der physische Zugang bzw. der digitale Peer-to-Peer-Zugriff auf das Gerät über das „lokale Netzwerk“ möglich ist. Ein „lokales Netzwerk“ wird als Netzwerk angesehen, das vom öffentlichen Internet getrennt und isoliert ist – aber ggf. über ein Brücke zum Internet verfügt – und über eine drahtlose Funktechnik für den Nahbereich verfügt, um die Peer-to-Peer-Kommunikation von Geräten zu ermöglichen. Es enthält *keine* Technologie für die Netzwerkvirtualisierung, mit der die Illusion eines lokalen Netzwerks erzeugt wird. Außerdem umfasst es keine öffentlichen Betreibernetzwerke, bei denen zwei Geräte über das öffentliche Netzwerk kommunizieren müssen, wenn sie eine Peer-to-Peer-Kommunikationsbeziehung eingehen möchten.

### <a name="the-field-gateway-zone"></a>Bereichsgateway-Zone

Ein Bereichsgateway ist ein Gerät bzw. eine Appliance oder eine Servercomputersoftware für allgemeine Zwecke, das bzw. die zum Ermöglichen der Kommunikation und unter Umständen auch als Gerätekontrollsystem und Hub für die Verarbeitung von Gerätedaten dient. Die Bereichsgateway-Zone enthält das Bereichsgateway selbst und alle Geräte, die daran angeschlossen sind. Wie der Name schon sagt, arbeiten Gateways außerhalb von dedizierten Datenverarbeitungseinrichtungen. Sie sind normalerweise standortgebunden, können physischen Angriffen ausgesetzt sein und verfügen über eine eingeschränkte Betriebsredundanz. Dies bedeutet also, dass ein Bereichsgateway meist ein Element ist, das berührt und sabotiert werden kann, sofern seine Funktion bekannt ist.

Ein Bereichsgateway unterscheidet sich von einem reinen Datenverkehrsrouter darin, dass es beim Verwalten des Zugriffs- und Informationsflusses eine aktive Rolle spielt. Dies bedeutet, dass es sich um eine Entität mit Anwendungsadressierung sowie eine Netzwerkverbindung oder ein Sitzungsterminal handelt. Ein NAT-Gerät oder eine Firewall können dagegen nicht als Bereichsgateways dienen, da es keine expliziten Verbindungs- oder Sitzungsterminals sind, sondern sie leiten eher Verbindungen oder Sitzungen weiter (oder blockieren den Vorgang). Das Bereichsgateway hat zwei getrennte Oberflächenbereiche. Einer ist auf die Geräte ausgerichtet, die damit verbunden sind, und repräsentiert den Innenbereich der Zone. Der andere ist auf alle externen Parteien ausgerichtet und dient als Edgebereich der Zone.

### <a name="the-cloud-gateway-zone"></a>Cloudgateway-Zone

Ein Cloudgateway ist ein System, das die Remotekommunikation von und zu Geräten oder Bereichsgateways von mehreren unterschiedlichen Standorten über das öffentliche Netzwerk ermöglicht. Dies erfolgt normalerweise auf Grundlage eines cloudbasierten Systems für die Steuerung und Datenanalyse bzw. eines Partnerverbunds dieser Systeme. In einigen Fällen ist mit einem Cloudgateway der unmittelbare Zugriff von Terminals wie Tablets oder Smartphones auf Geräte für spezielle Zwecke möglich. Im hier beschriebenen Kontext ist mit „Cloud“ ein dediziertes Datenverarbeitungssystem gemeint, das nicht an denselben Standort wie die verbundenen Geräte oder Bereichsgateways gebunden ist. In einer Cloudzone verhindern betriebliche Maßnahmen außerdem den zielgerichteten physischen Zugriff, und sie ist nicht unbedingt für eine Public Cloud-Infrastruktur verfügbar.  

Ein Cloudgateway kann auch einem Overlay für die Netzwerkvirtualisierung zugeordnet werden, um das Cloudgateway und alle verbundenen Geräte oder Bereichsgateways gegenüber anderem Netzwerk-Datenverkehr zu isolieren. Das Cloudgateway selbst ist weder ein Gerätesteuerungssystem noch eine Verarbeitungs- oder Speichereinrichtung für Gerätedaten. Diese Einrichtungen sind über Schnittstellen mit dem Cloudgateway verbunden. Die Cloudgateway-Zone enthält das Cloudgateway selbst sowie alle Bereichsgateways und Geräte, die direkt oder indirekt damit verbunden sind. Der Edgebereich der Zone ist ein separater Oberflächenbereich, über den alle externen Parteien kommunizieren.

### <a name="the-services-zone"></a>Dienstzone

Ein „Dienst“ ist in diesem Kontext als eine beliebige Softwarekomponente oder ein Modul definiert, die bzw. das mit Geräten über ein Bereichs- oder Cloudgateway für die Datenerfassung und -analyse sowie für Befehls- und Steuerungszwecke zusammenarbeitet. Dienste fungieren als Vermittler. Sie arbeiten unter ihrer Identität gegenüber Gateways und anderen Subsystemen, speichern und analysieren Daten, geben autonom Befehle an Geräte aus, die auf Datenerkenntnissen oder Zeitplänen basieren, und machen Informationen und Steuerfunktionen für autorisierte Endbenutzer verfügbar.

### <a name="information-devices-versus-special-purpose-devices"></a>Vergleich von Informationsgeräten und Geräten für spezielle Einsatzgebiete

PCs, Smartphones und Tablets sind in erster Linie interaktive Informationsgeräte. Smartphones und Tablets sind explizit auf die Maximierung der Akkulebensdauer ausgelegt. Vorzugsweise werden sie teilweise ausgeschaltet, wenn keine direkte Interaktion mit einer Person erfolgt oder keine Dienste wie das Wiedergeben von Musik oder das Leiten des Besitzers an einen bestimmten Ort genutzt werden. Aus Systemsicht dienen diese IT-Geräte hauptsächlich als Proxys für Menschen. Sie sind quasi das „Stellglied für den Menschen“, das Aktionen vorschlägt, und der „Sensor für den Menschen“, über den Eingaben erfasst werden.

Geräte für spezielle Zwecke, z.B. einfache Temperatursensoren bis hin zu komplexen Produktionslinien in Fabriken mit Tausenden von Komponenten, unterscheiden sich hiervon. Diese Geräte sind viel stärker auf einen bestimmten Zweck zugeschnitten, und auch wenn sie über eine Benutzeroberfläche verfügen, sind sie eher dafür ausgelegt, mit Ressourcen der physischen Welt zu interagieren bzw. in diese integriert zu werden. Sie dienen zum Messen und Melden von Umgebungszuständen, Betreiben von Ventilen, Steuern von Servomotoren, Auslösen von Alarmen, Schalten von Beleuchtungseinrichtungen und vielem mehr. Sie werden für Arbeitsschritte verwendet, für die ein IT-Gerät zu allgemein, zu teuer, zu groß oder zu fehleranfällig ist. Der konkrete Zweck gibt eindeutig das technische Design sowie das verfügbare Budget für die Produktion und die geplante Lebensdauer vor. Aus der Kombination dieser beiden Schlüsselfaktoren ergibt sich eine Einschränkung des verfügbaren Budgets für die Betriebsenergie und des physischen Platzbedarfs und somit auch der verfügbaren Speicher-, Compute- und Sicherheitsfunktionen.

Wenn bei automatisierten oder remote gesteuerten Geräten ein Fehler auftritt, kann dies beispielsweise zu physischen Schäden oder einer Beschädigung der Steuerlogik und sogar zu unbefugten Eindringversuchen und Manipulationen führen. Fertigungslose können zerstört werden, Gebäude können geplündert oder in Brand gesetzt werden, und Menschen können verletzt werden oder sogar zu Tode kommen. Dies ist eine ganz und gar andere Schadensdimension als das Leerräumen einer gestohlenen Kreditkarte. Die Sicherheitsebene für Geräte, mit denen physische Abläufe ausgeführt werden, und für Sensordaten, die zu Befehlen für Abläufe dieser Art führen, muss höher als für alle E-Commerce- oder Bankszenarien sein.

### <a name="device-control-and-device-data-interactions"></a>Interaktionen der Gerätesteuerung und der Gerätedaten

Verbundene Geräte für spezielle Zwecke verfügen über eine erhebliche Anzahl von potenziellen Interaktionsoberflächen-Bereichen und Interaktionsmustern. Diese müssen alle berücksichtigt werden, um ein geeignetes Framework zum Schützen des digitalen Zugriffs auf diese Geräte bereitzustellen. Der Begriff „digitaler Zugriff“ wird hier zur Unterscheidung von allen Vorgängen verwendet, die per direkter Geräteinteraktion durchgeführt werden, bei der die Zugriffssicherheit durch eine physische Zugangskontrolle erreicht wird. Ein Beispiel hierfür ist die Anordnung des Geräts in einem Raum, dessen Tür mit einem Schloss verriegelt werden kann. Der physische Zugang kann zwar nicht mit Software und Hardware verhindert werden, aber es können Maßnahmen ergriffen werden, um zu verhindern, dass der physische Zugang zu einem Eingriff in das System führt.

Wenn Sie bei der Bedrohungsmodellierung die Interaktionsmuster untersuchen, wenden Sie die gleiche Aufmerksamkeit auf „Gerätesteuerung“ und „Gerätedaten“ an. „Gerätesteuerung“ kann als alle Informationen klassifiziert werden, die für ein Gerät von einer Partei mit dem Ziel bereitgestellt werden, sein Verhalten gegenüber seinem Zustand oder dem Zustand der Umgebung zu beeinflussen. „Gerätedaten“ können als alle Informationen deklariert werden, die von einem Gerät in Bezug auf seinen Zustand und den beobachteten Zustand seiner Umgebung an andere Parteien weitergegeben werden.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Bedrohungsmodellierung für die Azure IoT-Referenzarchitektur

Microsoft nutzt das zuvor beschriebene Framework, um die Bedrohungsmodellierung für Azure IoT durchzuführen. Im nachstehenden Abschnitt wird daher das konkrete Beispiel der Azure IoT-Referenzarchitektur verwendet, um zu veranschaulichen, wie die Bedrohungsmodellierung für IoT angegangen und den identifizierten Bedrohungen begegnet werden sollte. In diesem Beispiel werden vier Hauptbereiche identifiziert:

* Geräte und Datenquellen,
* Datentransport,
* Geräte und Ereignisverarbeitung und
* Präsentation

![Bedrohungsmodellierung für Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

Das nachstehende Diagramm enthält eine vereinfachte Ansicht der IoT-Architektur von Microsoft. Es wird ein Modell für ein Datenflussdiagramm verwendet, das vom Microsoft Threat Modeling Tool genutzt wird:

![Bedrohungsmodellierung für Azure IoT mit dem Threat Modeling Tool von Microsoft](media/iot-security-architecture/iot-security-architecture-fig3.png)

Es ist wichtig zu beachten, dass in der Architektur die Geräte- und Gatewayfunktionen getrennt sind. Diese Herangehensweise ermöglicht Benutzern, Gatewaygeräte mit höherer Sicherheit zu nutzen, die mit dem Cloudgateway über sichere Protokolle kommunizieren können. Dies erfordert normalerweise einen höheren Verarbeitungsaufwand, der von einem nativen Gerät – z.B. einem Thermostat – allein nicht bewältigt werden kann. In den Azure-Dienstzonen wird davon ausgegangen, dass das Cloudgateway durch den Azure IoT Hub-Dienst repräsentiert wird.

### <a name="device-and-data-sourcesdata-transport"></a>Geräte und Datenquellen bzw. Datentransport

In diesem Abschnitt wird die zuvor beschriebene Architektur aus Sicht der Bedrohungsmodellierung betrachtet, und Sie erhalten eine Übersicht darüber, wie einige damit verbundene Probleme gelöst werden. In diesem Beispiel werden hauptsächlich die Kernelemente eines Bedrohungsmodells betrachtet:

* Prozesse (von Ihnen kontrollierte und externe Elemente)
* Kommunikation (auch als Datenflüsse bezeichnet)
* Speicher (auch als Datenspeicher bezeichnet)

#### <a name="processes"></a>Prozesse

In jeder Kategorie der Azure IoT-Architektur wird in diesem Beispiel versucht, den unterschiedlichen Bedrohungen auf den einzelnen Daten- und Informationsebenen zu begegnen: Prozess, Kommunikation und Speicherung. Im Folgenden finden Sie eine Übersicht über die häufigsten Bedrohungen für die Kategorie „Prozess“, gefolgt von einer Übersicht darüber, welche Lösungen für diese Bedrohungen am besten geeignet sind:

**Spoofing (S)** : Ein Angreifer kann kryptografische Schlüsseldaten von einem Gerät entwenden – entweder auf Software- oder Hardwareebene – und dann mit einem anderen physischen oder virtuellen Gerät unter der Identität des Geräts zugreifen, von dem die Schlüsseldaten gestohlen wurden. Ein gutes Beispiel hierfür sind Fernbedienungen, mit denen alle Fernseher bedient werden können und die daher gern für Scherze verwendet werden.

**Denial of Service (D)** : Ein Gerät kann so manipuliert werden, dass es nicht mehr funktionsfähig ist oder kommunizieren kann, indem Funkfrequenzen gestört oder Kabel durchschnitten werden. Beispielsweise liefert eine Überwachungskamera, deren Stromversorgung oder Netzwerkverbindung absichtlich unterbrochen wurde, überhaupt keine Daten mehr.

**Manipulation (M)** : Ein Angreifer kann die Software, die auf dem Gerät ausgeführt wird, teilweise oder vollständig austauschen. Unter Umständen kann mit der neuen Software dann die echte Identität des Geräts genutzt werden, falls die Schlüsseldaten oder die Kryptografieeinrichtungen mit den Schlüsseldaten für das unzulässige Programm verfügbar sind. Beispielsweise kann ein Angreifer entwendete Schlüsseldaten ggf. nutzen, um Daten vom Gerät abzufangen und im Kommunikationspfad zu unterdrücken und durch falsche Daten zu ersetzen, die mit den gestohlenen Schlüsseldaten authentifiziert werden.

**Offenlegung von Daten (O)** : Wenn auf dem Gerät manipulierte Software ausgeführt wird, können mit dieser Software unter Umständen Daten an unbefugte Personen weitergegeben werden. Ein Angreifer kann beispielsweise entwendete Schlüsseldaten nutzen, um sich in den Kommunikationspfad zwischen dem Gerät und einem Controller oder Bereichsgateway oder Cloudgateway einzuschalten und Informationen abzuschöpfen.

**Rechteerweiterungen (R)** : Ein Gerät, das eine bestimmte Funktion erfüllt, kann dazu gebracht werden, eine andere Funktion auszuführen. Für ein Ventil, das für das halbe Öffnen programmiert ist, kann beispielsweise erreicht werden, dass es ganz geöffnet wird.

| **Komponente** | **Bedrohung** | **Abhilfe** | **Risiko** | **Implementierung** |
| --- | --- | --- | --- | --- |
| Sicherungsmedium |E |Zuweisen der Identität zum Gerät und Authentifizieren des Geräts |Ersetzen des Geräts oder eines Teils der Geräts durch ein anderes Gerät. Woher wissen Sie, dass Sie mit dem richtigen Gerät kommunizieren? |Authentifizieren des Geräts per Transport Layer Security (TLS) oder IPSec. Die Infrastruktur sollte die Verwendung eines vorinstallierten Schlüssels (Pre-Shared Key, PSK) auf den Geräten unterstützen, bei denen keine vollständige asymmetrische Verschlüsselung möglich ist. Nutzung von Azure AD, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TRID |Wenden Sie manipulationssichere Mechanismen auf das Gerät an, z.B. indem es erschwert oder unmöglich gemacht wird, Schlüssel und andere Verschlüsselungsdaten vom Gerät zu entwenden. |Das Risiko besteht darin, dass das Gerät manipuliert wird (physischer Eingriff). Woher wissen Sie, dass das Gerät nicht manipuliert wurde? |Die effektivste Lösung ist ein Trusted Platform Module (TPM) mit der Möglichkeit, Schlüssel in einem speziellen On-Chip-Verfahren zu speichern. Hierbei können die Schlüssel nicht gelesen, sondern nur für kryptografische Vorgänge verwendet werden, für die der Schlüssel erforderlich ist. Der Schlüssel wird aber niemals offengelegt. Speicherverschlüsselung für das Gerät. Schlüsselverwaltung für das Gerät. Signieren des Codes. |
|| E |Verwenden der Zugriffssteuerung für das Gerät, Autorisierungsschema |Wenn es für das Gerät zulässig ist, dass einzelne Aktionen basierend auf den Befehlen einer externen Quelle oder sogar über kompromittierte Sensoren durchgeführt werden, sind bei einem Angriff Vorgänge möglich, die sonst nicht zugänglich sind. |Verwenden Sie ein Autorisierungsschema für das Gerät. |
| Bereichsgateway |E |Authentifizieren des Bereichsgateways gegenüber dem Cloudgateway (z.B. zertifikat-, PSK- oder anspruchsbasiert) |Wenn das Bereichsgateway per Spoofing übernommen wird, kann sich der Angreifer als jedes Gerät ausgeben. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Alle üblichen Schlüsselspeicherungs- und Nachweisaspekte von Geräten. Am besten ist TPM geeignet. 6LowPAN-Erweiterung für IPSec zur Unterstützung von Wireless Sensor Networks (WSN). |
|| TRID |Schützen des Bereichsgateways vor Manipulationen (TPM?) |Spoofing-Angriffe, bei denen für das Cloudgateway der Eindruck erweckt wird, dass es mit dem Bereichsgateway kommuniziert, können zur Offenlegung und Manipulation von Daten führen. |Speicherverschlüsselung, TPMs, Authentifizierung. |
|| E |Zugriffssteuerungsmechanismus für Bereichsgateway | | |

Hier sind einige Beispiele für Bedrohungen in dieser Kategorie aufgeführt:

**Spoofing**: Ein Angreifer kann kryptografische Schlüsseldaten von einem Gerät entwenden – entweder auf Software- oder Hardwareebene – und dann mit einem anderen physischen oder virtuellen Gerät unter der Identität des Geräts zugreifen, von dem die Schlüsseldaten gestohlen wurden.

**Denial of Service**: Ein Gerät kann so manipuliert werden, dass es nicht mehr funktionsfähig ist oder kommunizieren kann, indem Funkfrequenzen gestört oder Kabel durchschnitten werden. Beispielsweise liefert eine Überwachungskamera, deren Stromversorgung oder Netzwerkverbindung absichtlich unterbrochen wurde, überhaupt keine Daten mehr.

**Manipulation**: Ein Angreifer kann die Software, die auf dem Gerät ausgeführt wird, teilweise oder vollständig austauschen. Unter Umständen kann mit der neuen Software dann die echte Identität des Geräts genutzt werden, falls die Schlüsseldaten oder die Kryptografieeinrichtungen mit den Schlüsseldaten für das unzulässige Programm verfügbar sind.

**Manipulation**: Eine Überwachungskamera, die den sichtbaren Bereich eines leeren Flurs zeigt, kann auf ein Foto des Flurs gerichtet werden. Ein Rauch- oder Feuermelder kann ausgelöst werden, indem eine Person ein Feuerzeug darunter hält. In beiden Fällen ist das Gerät gegenüber dem System in technischer Hinsicht vollkommen vertrauenswürdig, meldet jedoch manipulierte Informationen.

**Manipulation**: Ein Angreifer kann entwendete Schlüsseldaten ggf. nutzen, um Daten vom Gerät abzufangen, im Kommunikationspfad zu unterdrücken und durch falsche Daten zu ersetzen, die mit den gestohlenen Schlüsseldaten authentifiziert werden.

**Manipulation**: Ein Angreifer kann die auf dem Gerät ausgeführte Software teilweise oder vollständig austauschen. Unter Umständen kann mit der neuen Software dann die echte Identität des Geräts genutzt werden, falls die Schlüsseldaten oder die Kryptografieeinrichtungen mit den Schlüsseldaten für das unzulässige Programm verfügbar sind.

**Veröffentlichung von Informationen**: Wenn auf dem Gerät manipulierte Software ausgeführt wird, können mit dieser Software unter Umständen Daten an unbefugte Personen weitergegeben werden.

**Veröffentlichung von Informationen**: Ein Angreifer kann entwendete Schlüsseldaten nutzen, um sich in den Kommunikationspfad zwischen dem Gerät und einem Controller oder Bereichsgateway oder Cloudgateway einzuschalten und Informationen abzuschöpfen.

**Denial of Service**: Das Gerät kann ausgeschaltet oder in einen Modus geschaltet werden, in dem die Kommunikation nicht möglich ist (dies ist bei vielen Maschinen in der Industrie beabsichtigt).

**Manipulation**: Das Gerät kann umkonfiguriert werden, damit es in einem für das Steuersystem unbekannten Zustand betrieben wird (außerhalb der bekannten Kalibrierungsparameter), und so Daten liefern, die fehlinterpretiert werden können.

**Rechteerweiterungen**: Ein Gerät, das eine bestimmte Funktion erfüllt, kann dazu gebracht werden, eine andere Funktion auszuführen. Für ein Ventil, das für das halbe Öffnen programmiert ist, kann beispielsweise erreicht werden, dass es ganz geöffnet wird.

**Denial of Service**: Das Gerät kann in einen Zustand geschaltet werden, in dem keine Kommunikation möglich ist.

**Manipulation**: Das Gerät kann umkonfiguriert werden, damit es in einem für das Steuersystem unbekannten Zustand betrieben wird (außerhalb der bekannten Kalibrierungsparameter), und so Daten liefern, die fehlinterpretiert werden können.

**Spoofing/Manipulation/Nichtanerkennung**: Wenn ein Gerät nicht geschützt ist (bei Fernbedienungen für Verbraucher häufig der Fall), kann ein Angreifer den Zustand des Geräts anonym manipulieren. Ein gutes Beispiel hierfür sind Fernbedienungen, mit denen alle Fernseher bedient werden können und die daher gern für Scherze verwendet werden.

#### <a name="communication"></a>Kommunikation

Hier geht es um Bedrohungen in Bezug auf den Kommunikationspfad zwischen Geräten und Bereichsgateways sowie Geräten und Cloudgateways. Die nachstehende Tabelle enthält einige Hinweise zu geöffneten Sockets des Geräts bzw. zur VPN-Verbindung:

| **Komponente** | **Bedrohung** | **Abhilfe** | **Risiko** | **Implementierung** |
| --- | --- | --- | --- | --- |
| Vom Gerät zum IoT Hub |TID |(D)TLS (PSK/RSA) zum Verschlüsseln des Datenverkehrs |Abhören oder Beeinträchtigen der Kommunikation zwischen dem Gerät und dem Gateway |Sicherheit auf der Protokollebene. Bei benutzerdefinierten Protokollen müssen Sie ermitteln, wie der Schutz ermöglicht werden kann. In den meisten Fällen erfolgt die Kommunikation vom Gerät zum IoT Hub (Gerät initiiert die Verbindung). |
| Gerät zu Gerät |TID |(D)TLS (PSK/RSA) zum Verschlüsseln des Datenverkehrs |Lesen von Daten bei der Übermittlung zwischen Geräten. Manipulation der Daten. Überladen des Geräts mit neuen Verbindungen. |Sicherheit auf der Protokollebene (MQTT/AMQP/HTTP/CoAP). Bei benutzerdefinierten Protokollen müssen Sie ermitteln, wie der Schutz ermöglicht werden kann. Die Lösung für die DoS-Bedrohung ist das Peering von Geräten über ein Cloud- oder Bereichsgateway und die ausschließliche Nutzung als Clients gegenüber dem Netzwerk. Das Peering kann zu einer direkten Verbindung zwischen den Peers führen, nachdem die Vermittlung über das Gateway erfolgt ist. |
| Von externer Entität zum Gerät |TID |Starke Kopplung der externen Entität mit dem Gerät |Abhören der Verbindung mit dem Gerät. Beeinträchtigen der Kommunikation mit dem Gerät. |Sichere Kopplung der externen Entität mit dem Gerät (NFC/Bluetooth LE). Kontrollieren des Bedienbereichs des Geräts (physisch). |
| Vom Bereichsgateway zum Cloudgateway |TID |TLS (PSK/RSA) zum Verschlüsseln des Datenverkehrs |Abhören oder Beeinträchtigen der Kommunikation zwischen dem Gerät und dem Gateway |Sicherheit auf der Protokollebene (MQTT/AMQP/HTTP/CoAP). Bei benutzerdefinierten Protokollen müssen Sie ermitteln, wie der Schutz ermöglicht werden kann. |
| Vom Gerät zum Cloudgateway |TID |TLS (PSK/RSA) zum Verschlüsseln des Datenverkehrs |Abhören oder Beeinträchtigen der Kommunikation zwischen dem Gerät und dem Gateway |Sicherheit auf der Protokollebene (MQTT/AMQP/HTTP/CoAP). Bei benutzerdefinierten Protokollen müssen Sie ermitteln, wie der Schutz ermöglicht werden kann. |

Hier sind einige Beispiele für Bedrohungen in dieser Kategorie aufgeführt:

**Denial of Service**: Eingeschränkte Geräte sind im Allgemeinen für DoS-Bedrohungen anfällig, wenn sie im Netzwerk aktiv auf eingehende Verbindungen oder nicht angeforderte Datagramme lauschen. Ein Angreifer kann dann viele Verbindungen parallel öffnen und nicht oder nur langsam verarbeiten, oder das Gerät kann mit unerwünschtem Datenverkehr geflutet werden. In beiden Fällen kann dies dazu führen, dass das Gerät im Netzwerk nicht mehr betriebsbereit ist.

**Spoofing, Offenlegung von Daten**: Eingeschränkte Geräte und Geräte für spezielle Zwecke verfügen häufig nur über einen zentralen Sicherheitsmechanismus, z.B. Kennwort- oder PIN-Schutz, oder sie verlassen sich ganz auf die Vertrauenswürdigkeit des Netzwerks. Das bedeutet, dass der Zugriff auf Informationen gewährt wird, wenn sich ein Gerät im selben Netzwerk befindet. Häufig ist das Netzwerk dabei nur mit einem gemeinsam verwendeten Schlüssel geschützt. Wenn der gemeinsame geheime Schlüssel für das Gerät oder das Netzwerk also offengelegt wird, kann das Gerät gesteuert werden, oder es können vom Gerät übermittelte Daten mitverfolgt werden.  

**Spoofing:** Ein Angreifer kann die Übermittlung abfangen oder teilweise außer Kraft setzen und den Absender nachahmen („Man in the middle“).

**Manipulation:** Ein Angreifer kann die Übermittlung abfangen oder teilweise außer Kraft setzen und falsche Informationen senden. 

**Offenlegung von Daten:** Ein Angreifer kann bei einer Übermittlung mithören und unbefugt Informationen erlangen. **Denial of Service:** Ein Angreifer kann das Übermittlungssignal blockieren und die Bereitstellung von Daten verhindern.

#### <a name="storage"></a>Storage

Jedes Gerät und Bereichsgateway verfügt über eine Art von Speicher (temporär für das Einreihen von Daten in die Warteschlange, Betriebssystemimage-Speicher).

| **Komponente** | **Bedrohung** | **Abhilfe** | **Risiko** | **Implementierung** |
| --- | --- | --- | --- | --- |
| Speicher des Geräts |TRID |Speicherverschlüsselung, Signieren der Protokolle |Lesen von Daten aus dem Speicher (personenbezogene Daten), Manipulieren von Telemetriedaten. Manipulieren von Befehlssteuerungsdaten in der Warteschlange oder im Cache. Die Manipulation von Konfigurations- oder Firmwareupdate-Paketen im lokalen Cache bzw. in der Warteschlange kann dazu führen, dass Komponenten des Betriebssystems oder des Systems kompromittiert werden. |Verschlüsselung, Nachrichtenauthentifizierungscode (Message Authentication Code, MAC) oder digitale Signatur. Falls möglich, strenge Zugriffssteuerung mit Zugriffssteuerungslisten oder Berechtigungen für Ressourcen. |
| Betriebssystemimage des Geräts |TRID | |Manipulation des Betriebssystems/Austausch von Betriebssystemkomponenten |Schreibgeschützte Betriebssystempartition, signiertes Betriebssystemimage, Verschlüsselung |
| Bereichsgatewayspeicher (Einreihen von Daten in die Warteschlange) |TRID |Speicherverschlüsselung, Signieren der Protokolle |Lesen von Daten aus dem Speicher (personenbezogene Daten), Manipulieren von Telemetriedaten, Manipulieren von Befehlssteuerungsdaten in der Warteschlange oder im Cache. Die Manipulation von Konfigurations- oder Firmwareupdate-Paketen (für Geräte oder das Bereichsgateway bestimmt) im lokalen Cache bzw. in der Warteschlange kann dazu führen, dass Komponenten des Betriebssystems oder des Systems kompromittiert werden. |BitLocker |
| Betriebssystemimage des Bereichsgateways |TRID | |Manipulation des Betriebssystems/Austausch von Betriebssystemkomponenten |Schreibgeschützte Betriebssystempartition, signiertes Betriebssystemimage, Verschlüsselung |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Geräte und Ereignisverarbeitung und Cloudgatewayzone

Ein Cloudgateway ist ein System, das die Remotekommunikation von und zu Geräten oder Bereichsgateways von mehreren unterschiedlichen Standorten über das öffentliche Netzwerk ermöglicht. Dies erfolgt normalerweise auf Grundlage eines cloudbasierten Systems für die Steuerung und Datenanalyse bzw. eines Partnerverbunds dieser Systeme. In einigen Fällen ist mit einem Cloudgateway der unmittelbare Zugriff von Terminals wie Tablets oder Smartphones auf Geräte für spezielle Zwecke möglich. Im hier beschriebenen Kontext ist mit „Cloud“ ein dediziertes Datenverarbeitungssystem gemeint, das nicht wie die verbundenen Geräte oder Bereichsgateways an einen Standort gebunden ist und bei dem der physische Zugang durch betriebliche Maßnahmen verhindert wird. Es geht nicht unbedingt um eine „Public Cloud“-Infrastruktur. Ein Cloudgateway kann auch einem Overlay für die Netzwerkvirtualisierung zugeordnet werden, um das Cloudgateway und alle verbundenen Geräte oder Bereichsgateways gegenüber anderem Netzwerk-Datenverkehr zu isolieren. Das Cloudgateway selbst ist weder ein Gerätesteuerungssystem noch eine Verarbeitungs- oder Speichereinrichtung für Gerätedaten. Diese Einrichtungen sind über Schnittstellen mit dem Cloudgateway verbunden. Die Cloudgateway-Zone enthält das Cloudgateway selbst sowie alle Bereichsgateways und Geräte, die direkt oder indirekt damit verbunden sind.

Beim Cloudgateway handelt es sich meist um eine speziell erstellte Software, die als Dienst ausgeführt wird und verfügbar gemachte Endpunkte aufweist, mit denen das Bereichsgateway und die Geräte Verbindungen herstellen können. Aus diesem Grund muss beim Entwerfen schon von Anfang an die Sicherheit im Blick behalten werden. Halten Sie sich an den [SDL](https://www.microsoft.com/sdl)-Prozess zum Entwerfen und Erstellen dieses Diensts.

#### <a name="services-zone"></a>Dienstzone

Ein Steuersystem (oder Controller) ist eine Softwarelösung, die mit einem Gerät, Bereichsgateway oder Cloudgateway zusammenarbeitet, um ein oder mehrere Geräte zu steuern oder Gerätedaten zu Darstellungszwecken oder für die nachgelagerte Steuerung zu erfassen, zu speichern und zu analysieren. Steuersysteme sind die einzigen Entitäten im Rahmen dieser Beschreibung, die eine unmittelbare Interaktion mit Personen ermöglichen. Eine Ausnahme sind zwischengeschaltete Geräteoberflächen für die physische Kontrolle. Dies kann beispielsweise ein Schalter sein, mit dem eine Person das Gerät ausschalten oder andere Eigenschaften ändern kann und für den es keine digital zugängliche Funktionsentsprechung gibt.

Bei zwischengeschalteten Geräteoberflächen für die physische Kontrolle wird die Funktion der physischen Steueroberfläche durch eine Steuerlogik eingeschränkt, sodass eine gleichwertige Funktion remote initiiert werden kann oder Konflikte in Bezug auf die Remoteeingabe verhindert werden können. Zwischengeschaltete Steueroberflächen dieser Art sind konzeptionell einem lokalen Steuersystem zugeordnet, für das die gleiche zugrunde liegende Funktionalität wie bei jedem anderen Remotesteuersystem genutzt wird, dem das Gerät unter Umständen parallel zugeordnet ist. Informationen zu den wichtigsten Cloud Computing-Bedrohungen finden Sie auf der Seite der [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen finden Sie in den folgenden Artikeln:

* [SDL Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT reference architecture available](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/) (Microsoft Azure IoT-Referenzarchitektur verfügbar)