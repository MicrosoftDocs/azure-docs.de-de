---
title: 'Azure KI-Leitfaden für Predictive Maintenance-Lösungen: Team Data Science-Prozess'
description: Umfassende Beschreibung der Data Science, auf der Predictive Maintenance-Lösungen in verschiedenen vertikalen Branchen basieren.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 1661f0f6cf024fde48d3706a1f8e47bf65f0d46f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321969"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure KI-Leitfaden für Predictive Maintenance-Lösungen

## <a name="summary"></a>Zusammenfassung

Predictive Maintenance ( **PdM** ) ist eine beliebte Anwendung aus dem Predictive Analytics-Bereich, mit der Unternehmen verschiedener Branchen eine hohe Ressourcennutzung und Einsparungen bei den Betriebskosten erzielen können. In diesem Leitfaden sind die geschäftlichen und analysebezogenen Richtlinien sowie die bewährten Methoden zusammengefasst, die für eine erfolgreiche Entwicklung und Bereitstellung von PdM-Lösungen über die Technologie der [Microsoft Azure KI-Plattform](https://azure.microsoft.com/overview/ai-platform) eingesetzt werden.

Zunächst werden in diesem Leitfaden branchenspezifische Geschäftsszenarien und der Prozess zur Qualifizierung dieser Szenarien für PdM vorgestellt. Außerdem werden die Datenanforderungen und Modellierungsverfahren für die Erstellung von PdM-Lösungen beschrieben. Der Schwerpunkt des Leitfadens liegt auf dem Data Science-Prozess, der Schritte wie die Datenaufbereitung, Featureentwicklung, Modellerstellung und Modelloperationalisierung umfasst. Als Ergänzung zu diesen wichtigen Konzepten ist in diesem Leitfaden ein Satz mit Lösungsvorlagen angegeben, die zur Beschleunigung bei der Entwicklung von PdM-Anwendungen dienen. Zudem wird im Leitfaden auf nützliche Schulungsressourcen für die Praxis verwiesen, die weitere Informationen zu KI im Hintergrund der Data Science-Komponenten enthalten. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science-Leitfaden – Übersicht und Zielgruppe
In der ersten Hälfte dieses Leitfadens werden typische geschäftliche Probleme und die Vorteile der PdM-Implementierung zur Behebung dieser Probleme beschrieben und häufige Anwendungsfälle aufgeführt. Diese Inhalte sind für Personen hilfreich, die in Unternehmen geschäftliche Entscheidungen treffen (Business Decision Makers, BDMs). Im zweiten Teil wird die Data Science-Technologie beschrieben, auf der PdM basiert, und eine Liste mit PdM-Lösungen bereitgestellt, die anhand der in diesem Leitfaden beschriebenen Prinzipien erstellt wurden. Außerdem sind Lernpfade und Verweise auf Schulungsmaterial angegeben. Diese Inhalte sind für Personen hilfreich, die in Unternehmen technische Entscheidungen treffen (Technical Decision Makers, TDMs).

| Empfohlener Abschnitt | Zielgruppe |
|:---------------|:---------------|
| [Business Case für Predictive Maintenance](#business-case-for-predictive-maintenance) |Business Decision Makers (BDMs), die Ausfallzeiten und Betriebskosten senken und die Nutzungsdauer der Ausrüstung verbessern möchten |
| [Data Science für Predictive Maintenance](#data-science-for-predictive-maintenance) |Technical Decision Makers (TDMs), die die PdM-Technologie evaluieren, um die einzigartigen Datenverarbeitungs- und KI-Anforderungen in Bezug auf Predictive Maintenance zu verstehen |
| [Lösungsvorlagen für Predictive Maintenance](#solution-templates-for-predictive-maintenance)|Softwarearchitekten oder KI-Entwickler, die in kurzer Zeit eine Demo oder ein Proof-of-Concept-Dokument erstellen möchten |
| [Schulungsressourcen für Predictive Maintenance](#training-resources-for-predictive-maintenance) | Alle obigen Personen, die sich über die Grundlagen der Data Science-Technologie, Tools und Verfahren informieren möchten

### <a name="prerequisite-knowledge"></a>Vorkenntnisse
In Bezug auf die BDM-Inhalte wird nicht vorausgesetzt, dass der Leser bereits über Data Science-Vorkenntnisse verfügt. In Bezug auf die TDM-Inhalte sind Grundkenntnisse in den Bereichen Statistik und Data Science hilfreich. Kenntnisse in den Bereichen Azure-Daten- und KI-Dienste, Python, R, XML und JSON sind empfehlenswert. KI-Verfahren werden in Form von Python- und R-Paketen implementiert. Zur Implementierung von Lösungsvorlagen werden Azure-Dienste, Entwicklungstools und SDKs verwendet.

## <a name="business-case-for-predictive-maintenance"></a>Business Case für Predictive Maintenance

Für Unternehmen ist es erforderlich, dass die kritische Ausrüstung mit der bestmöglichen Effizienz und Auslastung betrieben wird, damit sich die getätigten Investitionen rentieren. Bei diesen Ressourcen kann es sich um Flugzeugtriebwerke, Turbinen, Aufzüge oder Industriekühlanlagen, für die die Kosten im Millionenbereich liegen, oder auch um Alltagsgeräte wie Fotokopierer, Kaffeemaschinen oder Wasserkühler handeln.
- Standardmäßig nutzen die meisten Unternehmen den Ansatz der _Instandsetzung_ , bei dem Teile ausgetauscht werden, wenn sie kaputtgehen. Mit der Instandsetzung wird sichergestellt, dass die Lebensdauer der Teile vollständig genutzt wird (und keine Restnutzungszeit verschenkt wird), aber für das Unternehmen fallen Stillstandszeiten, Arbeitszeit und ungeplanter Wartungsaufwand an (zu ungewöhnlichen Zeiten oder an schwer erreichbaren Standorten).
- Die nächsthöhere Ebene für Unternehmen ist die _vorbeugende Wartung_ , bei der die Lebensdauer eines Teils bestimmt wird und vor einem Ausfall eine Wartung oder ein Austausch durchgeführt wird. Durch die vorbeugende Wartung werden ungeplante und zu Katastrophen führende Ausfälle vermieden. Aber die hohen Kosten für die geplanten Stillstandszeiten, die zu geringe Ausnutzung der Lebensdauer von Komponenten und der Aufwand an Arbeitszeit sind nach wie vor vorhanden.
- Das Ziel von _Predictive Maintenance_ besteht darin, eine optimale Balance zwischen der Instandsetzung und der vorbeugenden Wartung zu erzielen, indem ein _Just-in-Time_ -Austausch von Komponenten ermöglicht wird. Bei diesem Ansatz werden die Komponenten nur ausgetauscht, wenn ein Ausfall kurz bevorsteht. Indem die Lebensdauer von Komponenten (im Vergleich zur vorbeugenden Wartung) verlängert wird und die Kosten für ungeplante Wartung und Arbeitszeit reduziert werden (im Vergleich zur Instandsetzung), können Unternehmen Kosteneinsparungen und Wettbewerbsvorteile erzielen.

## <a name="business-problems-in-pdm"></a>Geschäftsprobleme in Bezug auf PdM
Für Unternehmen besteht ein hohes Betriebsrisiko aufgrund von unerwarteten Ausfällen, und bei komplexen Systemen können die Grundursachen häufig nur sehr eingeschränkt erkannt werden. Einige wichtige Aufgaben für Unternehmen sind:

- Erkennen von Anomalien bei der Ausrüstung oder der Systemleistung bzw. Funktionalität
- Vorhersagen des Ausfalls einer Ressource in naher Zukunft
- Schätzen der Restlebensdauer einer Ressource
- Identifizieren der Grundursachen für den Ausfall einer Ressource
- Identifizieren, welche Wartungsaktionen zu welchen Zeitpunkten für eine Ressource durchgeführt werden müssen

Typische Zielsetzungen für PdM sind:

- Reduzieren des Betriebsrisikos für unternehmenskritische Ausrüstung
- Steigern der Rendite für Ressourcen, indem Fehler vor ihrem Auftreten erkannt werden
- Kontrollieren der Wartungskosten durch die Ermöglichung von Just-in-Time-Wartungsvorgängen
- Reduzieren von Kundenverlusten, Verbessern des Markenimage und Verringern von Umsatzeinbußen
- Verringern der Lagerkosten durch Reduzierung der Lagerbestände, indem der Zeitpunkt der Nachbestellung vorhergesagt wird
- Erkennen von Grundmustern verschiedener Wartungsprobleme
- Angeben von KPIs (Key Performance Indicators), z.B. Integritätsbewertungen, für den Ressourcenzustand
- Schätzen der Restlebensdauer von Ressourcen
- Empfehlen von rechtzeitigen Wartungsaktivitäten
- Einrichten eines Just-in-Time-Lagerbestands, indem Bestelldaten für den Austausch von Teilen geschätzt werden

Diese Zielsetzungen sind die Ausgangspunkte für folgende Personen:

- _Data Scientists_ , um bestimmte Vorhersageprobleme zu analysieren und zu lösen
- _Cloudarchitekten und -entwickler_ , um eine End-to-End-Lösung zu erstellen

## <a name="qualifying-problems-for-predictive-maintenance"></a>Qualifizierungsprobleme für Predictive Maintenance
Es ist wichtig, darauf hinzuweisen, dass per Predictive Maintenance nicht alle Anwendungsfälle oder Geschäftsprobleme effektiv gelöst werden können. Es gibt drei wichtige Qualifizierungskriterien, die bei der Problemauswahl berücksichtigt werden müssen:

- Das Problem muss vorhersagbar sein. Das heißt, es muss ein Ziel oder Ergebnis vorhanden sein, das vorhergesagt werden kann. Außerdem sollte für das Problem ein eindeutiger Aktionspfad gelten, mit dem Fehler verhindert werden können, wenn sie erkannt werden.
- Das Problem sollte einen Datensatz zum Betriebsverlauf der Ausrüstung aufweisen, der _sowohl positive als auch negative Ergebnisse_ enthält. Die Aktionen, die zur Behandlung negativer Ergebnisse durchgeführt werden, sollten im Rahmen dieser Datensätze ebenfalls angegeben sein. Wichtig sind auch Fehlerberichte, Wartungsprotokolle zum Leistungsabfall, Reparaturprotokolle und Austauschprotokolle. Darüber hinaus sind Angaben zu Reparaturen, die mit dem Ziel der Verbesserung durchgeführt wurden, und Datensätze zu Austauschvorgängen nützlich.
- Der aufgezeichnete Verlauf sollte mit _relevanten_ Daten widergespiegelt werden, deren Qualität _ausreichend hoch_ ist, um als Unterstützung für den Anwendungsfall zu dienen. Weitere Informationen zur Relevanz und Eignung der Daten finden Sie unter [Datenanforderungen für Predictive Maintenance](#data-requirements-for-predictive-maintenance).
- Schließlich sollte das Unternehmen auch über Domänenexperten verfügen, denen das jeweilige Problem vollständig klar ist. Diese Personen sollten mit den internen Prozessen und Methoden vertraut sein, damit der Analyst die Daten verstehen und interpretieren kann. Außerdem sollten sie die erforderlichen Änderungen an vorhandenen Geschäftsprozessen vornehmen können, um bei Bedarf die richtigen Daten für die Probleme sammeln zu können.

## <a name="sample-pdm-use-cases"></a>Beispielanwendungsfälle für PdM
In diesem Abschnitt geht es um Anwendungsfälle für Predictive Maintenance aus mehreren Branchen, z.B. Luft- und Raumfahrt, Versorgungsunternehmen und Transport. Jeder Abschnitt beginnt mit einem Geschäftsproblem, und anschließend werden die Vorteile von PdM, die für das Geschäftsproblem relevanten Daten und zuletzt die Vorteile einer PdM-Lösung beschrieben.

| Geschäftsproblem | Vorteile durch PdM |
|:-----------------|-------------------|
|**Luftfahrt**      |                   |
|_Flugverspätungen und -ausfälle_ aufgrund von technischen Problemen. Fehler, die nicht rechtzeitig behoben werden können, können dazu führen, dass Flüge ausfallen und eine Störung des Zeitplans und Betriebs bewirken. |Mit PdM-Lösungen kann eine Vorhersage getroffen werden, wie hoch die Wahrscheinlichkeit ist, dass der Start eines Flugzeug aufgrund eines technischen Fehlers verzögert wird oder nicht möglich ist.|
|_Fehler im Triebwerk eines Flugzeugs_ : Der Austausch von Teilen eines Flugzeugtriebwerks gehört zu den häufigsten Wartungsaufgaben in der Flugbranche. Bei Wartungslösungen ist eine sorgfältige Verwaltung der Lagerverfügbarkeit, Lieferung und Planung von Bauteilen unerlässlich.|Die Möglichkeit, Daten zur Zuverlässigkeit von Bauteilen zu sammeln, führt zu einer erheblichen Verringerung der Investitionskosten.|
|**Finanzen** |                         |
|_Ausfälle von Geldautomaten_ sind ein häufiges Problem in der Bankbranche. Hierbei besteht das Problem darin, die Wahrscheinlichkeit zu ermitteln, mit der der Abhebevorgang von Geld am Automaten aufgrund eines Papierstaus oder eines Teilefehlers in der Ausgabeeinheit unterbrochen wird. Basierend auf der Vorhersage von Fehlern bei diesen Transaktionen können Geldautomaten proaktiv gewartet werden, um das Auftreten von Fehlern zu verhindern.| Anstatt zuzulassen, dass es während einer Transaktion zu einem Fehler im Automaten kommt, wird dieser so programmiert, dass die Transaktion aufgrund der Vorhersage verweigert wird.|
|**Energieversorgung** |                          |
|_Ausfall von Windturbinen_ : Windturbinen sind in Ländern/Regionen, die auf eine umweltfreundliche Energieerzeugung achten, eine wichtige Energiequelle und mit hohem Investitionsaufwand verbunden. Eine wichtige Komponente in Windkraftanlagen ist der Generatormotor, dessen Ausfall die Anlage unwirksam werden lässt. Außerdem ist die Reparatur sehr teuer.|Durch das Vorhersagen von KPIs, z.B. die mittlere Zeitspanne bis zum Ausfall (Mean Time To Failure, MTTF), können Unternehmen der Windenergiebranche Ausfälle von Turbinen verhindern und die Ausfallzeiten gering halten. Mit den Ausfallwahrscheinlichkeiten erhalten Techniker Informationen zur Überwachung von Turbinen, die voraussichtlich in Kürze ausfallen, sodass die zeitbasierte Wartung geplant werden kann. Vorhersagemodelle liefern Erkenntnisse zu verschiedenen Faktoren, die zum Ausfall beitragen. Mit diesen Informationen können Techniker die Grundursachen von Problemen besser verstehen.|
|_Ausfälle von Leistungsschutzschaltern_ : Zur Versorgung von Wohnhäusern und Unternehmen mit Elektrizität müssen Stromleitungen jederzeit betriebsbereit sein, damit der Strom ankommt. Mithilfe von Leitungsschutzschaltern können Schäden an Stromleitungen bei Überlast oder schwierigen Wetterbedingungen eingeschränkt oder vermieden werden. Das Geschäftsproblem ist hierbei die Vorhersage von Ausfällen der Leistungsschutzschalter.| PdM-Lösungen tragen zur Reduzierung von Reparaturkosten bei und verlängern die Lebensdauer von Ausrüstungsteilen, z.B. Leistungsschutzschaltern. Sie verbessern die Qualität des Stromnetzwerks, indem unerwartete Fehler und Ausfälle reduziert werden.|
|**Transport und Logistik** |    |
|_Ausfälle von Aufzugtüren_ : Große Anbieter von Aufzügen stellen einen umfassenden Service für Millionen von weltweit betriebenen Aufzügen bereit. Die Hauptanliegen der Kunden sind die Sicherheit, die Zuverlässigkeit und der unterbrechungsfreie Betrieb der Aufzüge. Diese Unternehmen verfolgen diese und verschiedene andere Attribute über Sensoren, um Instandsetzungsmaßnahmen und die vorbeugende Wartung zu unterstützen. Bei einem Aufzug ist das häufigste Kundenproblem die Fehlfunktion der Aufzugtüren. Das Geschäftsproblem besteht in diesem Fall darin, eine Knowledge Base-Vorhersageanwendung bereitzustellen, mit der die potenziellen Ursachen von Türausfällen vorhergesagt werden können.| Aufzüge stellen meist Investitionen mit einer Lebensdauer von 20 bis 30 Jahren dar. Jeder Verkaufsvorgang ist in der Regel also stark umkämpft, und die Erwartungen an den Service und Support sind hoch. Durch Predictive Maintenance können diese Unternehmen in Bezug auf die Produkt- und Serviceangebote einen Wettbewerbsvorteil gegenüber der Konkurrenz erzielen.|
|_Radfehler_ : Radfehler sind für die Hälfte aller Zugentgleisungen verantwortlich und bedeuten für die globale Bahnverkehrbranche Kosten in Milliardenhöhe. Außerdem fördern Radfehler die Abnutzung von Schienen, was so weit gehen kann, dass die Schiene vorzeitig bricht. Das Brechen von Schienen führt zu katastrophalen Vorfällen, z.B. Entgleisungen. Zur Vermeidung dieser Vorfälle überwachen Bahnunternehmen die Leistung der Räder und tauschen sie vorbeugend aus. Das Geschäftsproblem ist hierbei die Vorhersage von Radfehlern.| Indem Predictive Maintenance auf Räder angewendet wird, kann dafür der Just-in-Time-Austausch erfolgen. |
|_Ausfälle der Türen von U-Bahn-Zügen_ : Ein Hauptgrund für Verzögerungen im U-Bahn-Betrieb sind Ausfälle der Türen von Zügen. Das Geschäftsproblem besteht hierbei darin, den Ausfall der Zugtüren vorherzusagen.|Wenn ein bevorstehender Türausfall zu einem frühen Zeitpunkt bekannt ist (z.B. die Anzahl von Tagen bis zum Ausfall), kann das Unternehmen den Zeitplan für die Wartung der Zugtüren entsprechend optimieren.|

Im nächsten Abschnitt sind die Details dazu enthalten, wie Sie die oben beschriebenen PdM-Vorteile realisieren können.

## <a name="data-science-for-predictive-maintenance"></a>Data Science für Predictive Maintenance

Dieser Abschnitt enthält allgemeine Richtlinien zu Data Science-Prinzipien und PdM-Methoden. Die Informationen sollen TDMs, Lösungsarchitekten und Entwicklern ein besseres Verständnis der Voraussetzungen und des Prozesses zum Entwickeln von End-to-End-KI-Anwendungen für PdM ermöglichen. Sie können beim Lesen dieses Abschnitts auch die Demos und Proof-of-Concept-Vorlagen unter [Lösungsvorlagen für Predictive Maintenance](#solution-templates-for-predictive-maintenance) nutzen. Anschließend können Sie diese Prinzipien und bewährten Methoden verwenden, um Ihre PdM-Lösung in Azure zu implementieren.

> [!NOTE]
> Ziel dieses Leitfadens ist es NICHT, den Leser in Bezug auf Data Science zu schulen. Im Abschnitt [Schulungsressourcen für Predictive Maintenance](#training-resources-for-predictive-maintenance) finden Sie mehrere hilfreiche Quellen mit weiterem Lesematerial. Mit den in diesem Leitfaden angegebenen [Lösungsvorlagen](#solution-templates-for-predictive-maintenance) werden einige dieser KI-Verfahren für bestimmte PdM-Probleme veranschaulicht.

## <a name="data-requirements-for-predictive-maintenance"></a>Datenanforderungen für Predictive Maintenance

Der Lernerfolg hängt stets von (a) der Qualität des Lerninhalts und (b) von den Fähigkeiten des Lernenden ab. Bei Vorhersagemodellen werden die Muster von Verlaufsdaten erlernt und zukünftige Ergebnisse basierend auf den beobachteten Mustern mit einer bestimmten Wahrscheinlichkeit vorhergesagt. Die Vorhersagegenauigkeit eines Modells richtet sich nach der Relevanz, dem Umfang und der Qualität der Trainings- und Testdaten. Die neuen Daten, die mit diesem Modell „bewertet“ werden, sollten über die gleichen Features und Schemas wie die Trainings- bzw. Testdaten verfügen. Die Featuremerkmale (Typ, Dichte, Verteilung usw.) der neuen Daten sollten mit den Merkmalen der Trainings- und Testdatasets übereinstimmen. Der Schwerpunkt dieses Abschnitts liegt auf diesen Datenanforderungen.

### <a name="relevant-data"></a>Relevante Daten

Erstens müssen die Daten _für das Problem relevant_ sein. Nehmen Sie den oben beschriebenen Anwendungsfall mit dem _Radfehler_ als Beispiel. Die Trainingsdaten sollten Features in Bezug auf den Radbetrieb enthalten. Falls das Problem darin besteht, den Ausfall des _Traktionssystems_ vorherzusagen, müssen die Trainingsdaten alle unterschiedlichen Komponenten des Traktionssystems abdecken. Im ersten Fall geht es um eine bestimmte Komponente, während es im zweiten Fall um den Ausfall eines größeren Subsystems geht. Die allgemeine Empfehlung lautet, Vorhersagesysteme für bestimmte Komponenten zu entwerfen, anstatt größere Subsysteme, da Letztere über stärker verteilte Daten verfügen. Der Domänenexperte (siehe [Qualifizierungsprobleme für Predictive Maintenance](#qualifying-problems-for-predictive-maintenance)) sollte dabei behilflich sein, die relevantesten Teilmengen der Daten für die Analyse auszuwählen. Die relevanten Datenquellen sind unter [Datenaufbereitung für Predictive Maintenance](#data-preparation-for-predictive-maintenance) ausführlicher beschrieben.

### <a name="sufficient-data"></a>Ausreichende Daten
Zwei Fragen werden im Hinblick auf historische Fehlerdaten gestellt: (1) „Wie viele Fehlerereignisse sind zum Trainieren eines Modells erforderlich?“ (2) „Wie viele Datensätze werden als ausreichend angesehen?“ Es gibt hierfür keine definitiven Antworten, sondern nur Faustregeln. Für (1) gilt: Je höher die Anzahl von Fehlerereignissen, desto besser ist das Modell. Für (2) gilt: Die genaue Anzahl von Fehlerereignissen richtet sich nach den Daten und dem Kontext des zu lösenden Problems. Es gilt aber auch: Wenn eine Maschine zu häufig ausfällt, wird sie vom Unternehmen ausgetauscht, und die Fehlerinstanzen werden reduziert. Hierbei ist wiederum die Hilfe durch den Domänenexperten wichtig. Es gibt aber auch Methoden zum Umgang mit _seltenen Ereignissen_. Diese werden im Abschnitt [Behandeln von unausgeglichenen Daten](#handling-imbalanced-data) beschrieben.

### <a name="quality-data"></a>Qualitätsdaten
Die Qualität der Daten ist entscheidend. Jeder Vorhersageattributwert muss in Bezug auf den Wert der Zielvariablen _genau_ sein. Die Datenqualität wird im Bereich der Statistik und Datenverwaltung eingehend untersucht und liegt daher außerhalb des Rahmens dieses Leitfadens.

> [!NOTE]
> Es gibt verschiedene Ressourcen und Unternehmensprodukte zur Bereitstellung von Daten hoher Qualität. Hier sind einige Referenzbeispiele angegeben:
> - Dasu, T, Johnson, T., Exploratory Data Mining and Data Cleaning, Wiley, 2003 (Exploratives Data Mining und Datenbereinigung)
> - [Explorative Datenanalyse (bei Wikipedia)](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Quantitative Data Cleaning for Large Databases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf) (Bereinigung quantitativer Daten für große Datenbanken)
> - [de Jonge, E, van der loo, M, Introduction to Data Cleaning with R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf) (Einführung in die Datenbereinigung mit R)

## <a name="data-preparation-for-predictive-maintenance"></a>Datenaufbereitung für Predictive Maintenance

### <a name="data-sources"></a>Datenquellen

Zu den relevanten Datenquellen für Predictive Maintenance gehören beispielsweise:
- Fehlerverlauf
- Wartungs-/Reparaturverlauf
- Betriebszustände von Maschinen
- Ausrüstungsdaten

#### <a name="failure-history"></a>Fehlerverlauf
Fehlerereignisse sind in PdM-Anwendungen selten. Beim Entwickeln von Vorhersagemodellen muss der Algorithmus aber das normale Betriebsmuster einer Komponente sowie die Fehlermuster erlernen. Daher sollten die Trainingsdaten eine ausreichende Anzahl von Beispielen beider Kategorien enthalten. Wartungsdatensätze und der Verlauf des Austauschs von Teilen sind gute Quellen für die Ermittlung von Fehlerereignissen. Mithilfe von Domänenwissen können Anomalien in den Trainingsdaten auch als Fehler definiert werden.

#### <a name="maintenancerepair-history"></a>Wartungs-/Reparaturverlauf
Der Wartungsverlauf einer Ressource enthält Details zu ausgetauschten Komponenten, durchgeführten Reparaturvorgängen usw. Mit diesen Ereignissen werden Muster der Leistungsminderung aufgezeichnet. Das Fehlen dieser wichtigen Informationen in den Trainingsdaten kann zu irreführenden Modellergebnissen führen. Der Fehlerverlauf kann auch in Form von speziellen Fehlercodes im Wartungsverlauf oder anhand des Datums von Teilebestellungen ermittelt werden. Zusätzliche Datenquellen, die einen Einfluss auf Fehlermuster haben, sollten von Domänenexperten untersucht und bereitgestellt werden.

#### <a name="machine-operating-conditions"></a>Betriebszustände von Maschinen
Sensorbasierte (oder andere) Streamingdaten der Betriebsausrüstung stellen eine wichtige Datenquelle dar. Eine wichtige Annahme in Bezug auf PdM ist, dass sich der Integritätsstatus einer Maschine während des Routinebetriebs im Lauf der Zeit verschlechtert. Die Daten sollten zeitbezogene Features enthalten, mit denen dieses Alterungsmuster und alle Anomalien erfasst werden, die zu einer Verschlechterung führen. Der zeitliche Aspekt der Daten ist erforderlich, damit der Algorithmus die Fehlermuster und anderen Muster im Laufe der Zeit erlernen kann. Basierend auf diesen Datenpunkten erlernt der Algorithmus das Verfahren zum Vorhersagen, wie viele Zeiteinheiten lang eine Maschine noch betrieben werden kann, bevor ein Fehler auftritt.

#### <a name="static-feature-data"></a>Daten zu statischen Merkmalen
Statische Merkmale sind Metadaten zur Ausrüstung. Beispiele hierfür sind Typ, Modell, Herstellungsdatum, Datum des Betriebsstarts, Standort des Systems und andere technische Spezifikationen der Ausrüstung.

Beispiele für relevante Daten zu den [PdM-Beispielanwendungsfällen](#sample-pdm-use-cases) sind in der Tabelle unten angegeben:

| Anwendungsfall | Beispiele für relevante Daten |
|:---------|---------------------------|
|_Verspätungen und Ausfälle von Flügen_ | Flugrouteninformationen in Form von Teilstrecken und Page Logs. Zu den Daten für Teilstrecken gehören Routendetails wie Datum und Uhrzeit des Abflugs und der Ankunft, der Flughafen, Zwischenstopps usw. Page Logs enthalten verschiedene Fehler- und Wartungscodes, die vom Bodenpersonal aufgezeichnet werden.|
|_Fehler im Triebwerk eines Flugzeugs_ | Dies sind Daten, die über Sensoren im Flugzeug ermittelt werden, um Informationen zum Zustand der einzelnen Teile zu liefern. Anhand von Wartungsprotokollen kann ermittelt werden, wann Komponentenfehler aufgetreten sind und wann diese ausgetauscht wurden.|
|_Ausfälle von Geldautomaten_ | Sensormesswerte für jede Transaktion (Bargeld/Scheck) und die Ausgabe von Bargeld. Informationen zur Messung des Abstands zwischen Banknoten, Dicke der Banknoten, Abstand der Banknotenbereitstellung, Scheckattribute usw. Wartungsdatensätze mit Fehlercodes, Reparaturinformationen, Angaben zur letzten Auffüllung des Geldautomaten.|
|_Ausfall von Windturbinen_ | Mit Sensoren werden die Turbinenbedingungen überwacht, z.B. Temperatur, Windrichtung, erzeugter Strom, Generatorgeschwindigkeit usw. Daten werden von einer größeren Zahl von Windturbinen erfasst, die Teil von Windfarmen in verschiedenen Regionen sind. Normalerweise verfügt jede Turbine über mehrere Sensormesswerte, sodass die Messungen nach einem festgelegten Zeitintervall bereitgestellt werden.|
|_Ausfälle von Leistungsschutzschaltern_ | Wartungsprotokolle, die Informationen zu Korrektur-, Vorbeugungs- und Systemmaßnahmen enthalten. Betriebsdaten mit automatischen und manuellen Befehlen, die an Leistungsschutzschalter gesendet werden, z.B. zum Öffnen und Schließen. Gerätemetadaten, z.B. Herstellungsdatum, Standort, Modell usw. Spezifikationen zu Leistungsschutzschaltern, z.B. Spannungsebenen, Geolocation und Umgebungsbedingungen.|
|_Ausfälle von Aufzugtüren_| Aufzugmetadaten, z.B. Typ des Aufzugs, Herstellungsdatum, Wartungshäufigkeit, Gebäudetyp usw. Informationen zum Betrieb, z.B. Anzahl von Türzyklen, durchschnittliche Dauer des Schließvorgangs. Fehlerverlauf mit den dazugehörigen Ursachen.|
|_Radfehler_ | Sensordaten, über die Radbeschleunigung, Bremsaktionen, Laufleistung, Geschwindigkeit usw. gemessen werden. Informationen zu statischen Merkmalen der Räder, z.B. Hersteller, Herstellungsdatum. Aus der Datenbank für die Teilebestellung abgeleitete Fehlerdaten, die die nachverfolgten Bestelldaten und -mengen umfassen.|
|_Ausfälle der Türen von U-Bahn-Zügen_ | Dauer des Vorgangs zum Öffnen und Schließen der Türen sowie andere Betriebsdaten, z.B. der aktuelle Zustand der Zugtüren. Zu den statischen Daten gehören Spalten mit der Ressourcen-ID, der Zeit und dem Zustandswert.|

### <a name="data-types"></a>Datentypen
Basierend auf den obigen Datenquellen sind im PdM-Bereich die folgenden beiden Datentypen zu beobachten:

- _Temporale Daten_ : Betriebstelemetrie, Maschinenzustand, Arbeitsauftragstypen, Prioritätscodes mit Zeitstempeln zum Aufzeichnungszeitpunkt. Für den Fehler-, Wartungs-/Reparatur- und Nutzungsverlauf werden jedem Ereignis ebenfalls Zeitstempel zugeordnet.
- _Statische Daten_ : Bei Merkmalen von Maschinen und Bedienern handelt es sich um statische Daten, da hiermit die technischen Spezifikationen von Maschinen oder die Attribute von Bedienern beschrieben werden. Falls sich diese Merkmale im Laufe der Zeit ändern können, sollten sie auch mit Zeitstempeln versehen werden.

Vorhersage- und Zielvariablen sollten je nach verwendetem Algorithmus vorverarbeitet bzw. in [numerische, kategorische und andere Datentypen](https://www.statsdirect.com/help/basics/measurement_scales.htm) transformiert werden.

### <a name="data-preprocessing"></a>Vorabaufbereitung der Daten
Eine Voraussetzung für die _Featureentwicklung_ ist, dass Sie die Daten aus den verschiedenen Datenströmen aufbereiten müssen, um ein Schema zu erstellen, mit dem die Entwicklung von Features vereinfacht wird. Visualisieren Sie die Daten zuerst in einer Tabelle mit Datensätzen. Jede Zeile der Tabelle stellt eine Trainingsinstanz dar, und die Spalten stehen für die Features der _Vorhersage_ (auch als unabhängige Attribute oder Variablen bezeichnet). Organisieren Sie die Daten so, dass die letzte bzw. letzten Spalten für das _Ziel_ (abhängige Variable) verwendet werden. Weisen Sie für jede Trainingsinstanz eine _Bezeichnung_ als Wert für diese Spalte zu.

Unterteilen Sie für die temporalen Daten die Dauer der Sensordaten in Zeiteinheiten. Jeder Datensatz sollte zu einer Zeiteinheit für eine Ressource gehören _und eindeutige Informationen enthalten_. Zeiteinheiten werden je nach den geschäftlichen Anforderungen in Vielfachen von Sekunden, Minuten, Stunden, Tagen, Monaten usw. definiert. Die Zeiteinheit _muss nicht der Häufigkeit der Datensammlung entsprechen_. Falls die Häufigkeit sehr hoch ist, kann es sein, dass sich die Daten für die Einheiten nicht merklich unterscheiden. Angenommen, die Umgebungstemperatur wurde alle zehn Sekunden erfasst. Wenn für die Trainingsdaten das gleiche Intervall genutzt wird, steigt lediglich die Anzahl von Beispielen, ohne dass zusätzliche Informationen gewonnen werden. Eine bessere Strategie für diesen Fall ist die Verwendung eines Mittelwerts der Daten für einen Zeitraum von zehn Minuten oder, je nach Geschäftsanforderungen, von einer Stunde.

Für statische Daten:
- _Wartungsdatensätze_ : Unformatierte Wartungsdaten verfügen über eine Ressourcen-ID und einen Zeitstempel mit Informationen zu den Wartungsaktivitäten, die zu einem bestimmten Zeitpunkt durchgeführt wurden. Transformieren Sie die Wartungsaktivitäten in _kategoriebezogenen_ Spalten, wobei der Deskriptor jeder Kategorie eindeutig einer bestimmten Wartungsaktion zugeordnet sein sollte. Das Schema für Wartungsdatensätze enthält die Ressourcen-ID, die Zeit und die Wartungsaktion.

- _Fehlerdatensätze_ : Fehler oder Fehlerursachen können als spezifische Fehlercodes oder Fehlerereignisse aufgezeichnet werden, die über bestimmte Geschäftsbedingungen definiert werden. Falls die Ausrüstung über mehrere Fehlercodes verfügt, sollte der Domänenexperte dabei behilflich sein, diejenigen zu identifizieren, die zur Zielvariablen gehören. Verwenden Sie die verbleibenden Fehlercodes oder Bedingungen, um _Vorhersagefeatures_ zu erstellen, die mit diesen Fehlern korrelieren. Das Schema für Fehlerdatensätze enthält die Ressourcen-ID, die Zeit, den Fehler bzw. den Fehlergrund (falls verfügbar).

- _Metadaten für Maschinen und Bediener_ : Führen Sie die Metadaten für Maschinen und Bediener in einem Schema zusammen, um eine Ressource dem Bediener zuzuordnen, einschließlich der jeweiligen Attribute. Das Schema für die Maschinenzustände enthält die Ressourcen-ID, Ressourcenfeatures, Bediener-ID und Bedienerfeatures.

Weitere Schritte zur Vorabaufbereitung der Daten sind der _Umgang mit fehlenden Werten_ und die _Normalisierung_ von Attributwerten. Eine ausführliche Beschreibung würde den Rahmen dieses Leitfadens sprengen. Im nächsten Abschnitt finden Sie einige hilfreiche Verweise.

Wenn die obigen vorab aufbereiteten Datenquellen vorhanden sind, besteht die letzte Transformation vor der Featureentwicklung aus dem Einbinden der obigen Tabellen basierend auf der Ressourcen-ID und dem Zeitstempel. Die sich ergebende Tabelle enthält in der Fehlerspalte NULL-Werte, wenn sich die Maschine im Normalbetrieb befindet. Diese NULL-Werte können mit einem Indikator für den Normalbetrieb zugeordnet werden. Verwenden Sie diese Fehlerspalte, um _Bezeichnungen für das Vorhersagemodell_ zu erstellen. Weitere Informationen finden Sie im Abschnitt zu den [Modellierungsverfahren für Predictive Maintenance](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Featureentwicklung
Die Featureentwicklung ist der erste Schritt vor der Modellierung der Daten. Ihre Rolle im Data Science-Prozess ist [hier beschrieben](./create-features.md). Ein _Feature_ ist ein Vorhersageattribut für das Modell, z.B. Temperatur, Druck, Vibration usw. In Bezug auf PdM umfasst die Featureentwicklung die Abstrahierung der Integrität einer Maschine anhand von Verlaufsdaten, die über einen längeren Zeitraum gesammelt wurden. In dieser Hinsicht unterscheidet sie sich von vergleichbaren Verfahren, z.B. der Remoteüberwachung, Anomalieerkennung und Fehlererkennung. 

### <a name="time-windows"></a>Zeitfenster
Bei der Remoteüberwachung geht es um das Berichten von Ereignissen, die zu bestimmten _Zeitpunkten_ eintreten. Bei Anomalieerkennungsmodellen werden eingehende Datenströme evaluiert (bewertet), um Anomalien für verschiedene Zeitpunkte zu kennzeichnen. Im Rahmen der Fehlererkennung werden Fehler als bestimmte Fehlertypen klassifiziert, wenn diese zu bestimmten Zeitpunkten eintreten. Bei PdM geht es dagegen um das Vorhersagen von Fehlern für einen _Zeitraum in der Zukunft_ basierend auf Features, die das Maschinenverhalten für _Zeiträume in der Vergangenheit_ repräsentieren. Für PdM sind Featuredaten einzelner Zeitpunkte zu ungenau, um für Vorhersagen nützlich zu sein. Die Daten jedes Features müssen also _geglättet_ werden, indem Datenpunkte für Zeitfenster aggregiert werden.

### <a name="lag-features"></a>Verzögerungsfeatures
Anhand der geschäftlichen Anforderungen wird definiert, wie weit mit dem Modell Vorhersagen für die Zukunft getroffen werden sollen. Basierend auf dieser Dauer kann wiederum definiert werden, „wie weit das Modell zurückgehen soll“, um diese Vorhersagen zu erstellen. Dieser Zeitraum wird als _Verzögerung_ bezeichnet, und für diesen Verzögerungszeitraum erstellte Features werden als _Verzögerungsfeatures_ bezeichnet. In diesem Abschnitt werden Verzögerungsfeatures, die aus Datenquellen mit Zeitstempeln erstellt werden können, und die Featureerstellung aus statischen Datenquellen beschrieben. Verzögerungsfeatures sind normalerweise _numerischer_ Art.

> [!IMPORTANT]
> Die Fenstergröße wird per Experimentierung bestimmt und sollte mithilfe eines Domänenexperten endgültig festgelegt werden. Die gleiche Anforderung gilt für die Auswahl und Definition von Verzögerungsfeatures, der dazugehörigen Aggregationen und der Fenstertypen.

#### <a name="rolling-aggregates"></a>Gleitende Aggregate
Für jeden Datensatz einer Ressource wird die Gleitfenstergröße „W“ als Anzahl von Zeiteinheiten zur Berechnung der Aggregate gewählt. Anschließend werden Verzögerungsfeatures berechnet, indem die W-Zeiträume _vor dem Datum_ dieses Datensatzes verwendet werden. In Abbildung 1 veranschaulichen die blauen Linien Sensorwerte einer Ressource für jede Zeiteinheit. Sie geben einen gleitenden Durchschnitt der Featurewerte für ein Fenster mit der Größe W=3 an. Der gleitende Durchschnitt wird für alle Datensätze mit Zeitstempeln im Bereich t<sub>1</sub> (orange) bis t<sub>2</sub> (grün) berechnet. Der Wert für W wird je nach Art der Daten normalerweise in Minuten oder Stunden angegeben. Bei bestimmten Problemen kann durch die Auswahl eines hohen Werts für W (z.B. 12 Monate) der gesamte Verlauf einer Ressource bis zum Zeitpunkt des Datensatzes angegeben werden.

![Abbildung 1. Gleitendes Aggregat – Features](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Abbildung 1. Gleitendes Aggregat – Features

Beispiele für gleitende Aggregate eines Zeitfensters sind Werte vom Typ „Anzahl“, „Mittelwert“, CUMESUM-Measures (Cumulative Sum) und Minimal-/Maximalwerte. Außerdem werden häufig Varianz, Standardabweichung und Anzahl von Ausreißern für N Standardabweichungen verwendet. Beispiele für Aggregate, die auf [Anwendungsfälle](#sample-pdm-use-cases) in diesem Leitfaden angewendet werden können, sind unten aufgeführt. 
- _Flugverspätung_ : Anzahl von Fehlercodes für den letzten Tag bzw. die letzte Woche.
- _Ausfall eines Teils des Flugzeugtriebwerks_ : Gleitender Durchschnitt, Standardabweichung und Summe für den letzten Tag, die letzte Woche usw. Diese Metrik sollte in Zusammenarbeit mit dem Domänenexperten bestimmt werden.
- _Fehler bei Geldautomaten_ : Gleitender Durchschnitt, Median, Bereich, Standardabweichungen, Anzahl von Ausreißern über drei Standardabweichungen hinaus, CUMESUM-Ober- und -Unterwert.
- _Ausfälle der Türen von U-Bahn-Zügen_ : Anzahl von Ereignissen für den letzten Tag, die letzte Woche, die letzten zwei Wochen usw.
- _Ausfälle von Leistungsschutzschaltern_ : Anzahl von Ausfällen für die letzte Woche, das letzte Jahr, die letzten drei Jahre usw.

Ein weiteres nützliches Verfahren bei PdM ist die Erfassung von Trendänderungen, Spitzen und Ebenenänderungen mit Algorithmen, die Anomalien in den Daten erkennen.

#### <a name="tumbling-aggregates"></a>Rollierende Aggregate
Für jeden bezeichneten Datensatz einer Ressource wird ein Fenster der Größe _W- <sub>k</sub>_ definiert, wobei _k_ für die Anzahl von Fenstern der Größe _W_ steht. Aggregate werden dann für _k_ _rollierende Fenster_ _W-k, W- <sub>(k-1)</sub>, …, W- <sub>2</sub>, W- <sub>1</sub>_ für die Zeiträume erstellt, die vor dem Zeitstempel eines Datensatzes liegen. _k_ kann eine niedrige Zahl sein, um kurzfristige Auswirkungen zu erfassen, oder eine hohe Zahl, um langfristige Leistungsabfallmuster zu erfassen. (Siehe Abbildung 2.)

![Abbildung 2. Rollierendes Aggregat – Features](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Abbildung 2. Rollierendes Aggregat – Features

Verzögerungsfeatures für den Anwendungsfall mit den Windturbinen können beispielsweise mit den Werten W=1 und k=3 erstellt werden. Diese Werte implizieren jeweils die Verzögerung für die letzten drei Monate, und es werden Ausreißer nach oben und unten verwendet.

### <a name="static-features"></a>Statische Features

Technische Spezifikationen der Ausrüstung, z.B. Herstellungsdatum, Modellnummer und Standort, sind einige Beispiele für statische Features. Sie werden als _kategoriebezogene_ Variablen für die Modellierung behandelt. Einige Beispiele für den Anwendungsfall mit dem Leistungsschutzschalter sind Spannung, Stromstärke, Stromaufnahme, Transformatortyp und Stromquelle. Bei Radfehlern ist der Typ des Rads (Legierung oder Stahl) ein geeignetes Beispiel.

Die bisher beschriebenen Schritte zur Datenaufbereitung sollten ergeben, dass die Daten wie unten dargestellt organisiert werden. Trainings-, Test- und Validierungsdaten sollten über dieses logische Schema verfügen (in diesem Beispiel wird die Zeiteinheit „Tage“ verwendet).

| Asset-ID | Time | \<Feature Columns> | Bezeichnung |
| ---- | ---- | --- | --- |
| A123 |Tag 1 | erforderlich. erforderlich. erforderlich. | erforderlich. |
| A123 |Tag 2 | erforderlich. erforderlich. erforderlich. | erforderlich. |
| ...  |...   | erforderlich. erforderlich. erforderlich. | erforderlich. |
| B234 |Tag 1 | erforderlich. erforderlich. erforderlich. | erforderlich. |
| B234 |Tag 2 | erforderlich. erforderlich. erforderlich. | erforderlich. |
| ...  |...   | erforderlich. erforderlich. erforderlich. | erforderlich. |

Der letzte Schritt der Featureentwicklung ist die **Bezeichnung** der Zielvariablen. Dieser Prozess richtet sich nach dem Modellierungsverfahren. Das Modellierungsverfahren ist wiederum vom Geschäftsproblem und von der Art der verfügbaren Daten abhängig. Der Prozess der Bezeichnung wird im nächsten Abschnitt beschrieben.

> [!IMPORTANT]
> Die Datenaufbereitung und die Featureentwicklung sind genauso wichtig wie Modellierungsverfahren, um erfolgreiche PdM-Lösungen zu erhalten. Der Domänenexperte und der Praktiker sollten viel Zeit investieren, um die richtigen Features und Daten für das Modell zu ermitteln. Hier sind einige der vielen erhältlichen Bücher zur Featureentwicklung aufgeführt:
> - Pyle, D. Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems), 1999 (Datenaufbereitung für Data Mining)
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists (Feature Engineering für Machine Learning: Prinzipien und Techniken für Datenanalysten), O'Reilly, 2018.
> - Dong, G. Liu, H. (Editors), Feature Engineering for Machine Learning and Data Analytics (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018 (Featureentwicklung für Machine Learning und Datenanalyse)

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modellierungsverfahren für Predictive Maintenance

In diesem Abschnitt werden die wichtigsten Modellierungsverfahren für PdM-Probleme mit den dazugehörigen spezifischen Methoden zur Erstellung von Bezeichnungen beschrieben. Beachten Sie, dass ein Modellierungsverfahren für viele verschiedene Branchen eingesetzt werden kann. Das Modellierungsverfahren ist mit dem Data Science-Problem gekoppelt, anstatt mit dem Kontext der jeweiligen Daten.

> [!IMPORTANT]
> Die Wahl der Bezeichnungen für die Fehlerfälle und der Bezeichnungsstrategie  
> sollte in Abstimmung mit dem Domänenexperten erfolgen.

### <a name="binary-classification"></a>Binäre Klassifizierung
Die binäre Klassifizierung wird verwendet, _um die Wahrscheinlichkeit vorherzusagen, dass ein Ausrüstungsteil in einem in der Zukunft liegenden Zeitraum ausfällt_. Dieser Zeitraum wird als _zukünftiger Zeithorizont „X“_ bezeichnet. X wird anhand des Geschäftsproblems und der jeweiligen Daten in Zusammenarbeit mit dem Domänenexperten festgelegt. Beispiele:
- Erforderliche _Mindestvorlaufzeit_ zum Austauschen von Komponenten, Bereitstellen von Wartungsressourcen und Durchführen der Wartung zur Vermeidung eines Problems, dessen Auftreten für diesen Zeitraum wahrscheinlich ist.
- _Mindestzahl von Ereignissen_ , die eintreten können, bevor es zu einem Problem kommt.

Bei diesem Verfahren werden zwei Arten von Trainingsbeispielen identifiziert. Ein positives Beispiel, _mit dem ein Fehler angegeben wird_ und das die Bezeichnung „1“ (label = 1) hat. Ein negatives Beispiel, mit dem der Normalbetrieb angegeben wird und das die Bezeichnung „0“ (label = 0) hat. Die Zielvariable und somit auch die Bezeichnungswerte sind _kategoriebezogen_. Im Modell sollte für jedes neue Beispiel ermittelt werden, ob für die nächsten X Zeiteinheiten das Auftreten eines Fehlers oder der Normalbetrieb wahrscheinlich ist.

#### <a name="label-construction-for-binary-classification"></a>Erstellung von Bezeichnungen für die binäre Klassifizierung
Hier wird die folgende Frage gestellt: „Wie hoch ist die Wahrscheinlichkeit, dass die Ressource innerhalb der nächsten X Zeiteinheiten ausfällt?“ Zur Beantwortung dieser Frage bezeichnen Sie X Datensätze im Zeitraum vor dem Ausfall einer Ressource als „Ausfall wahrscheinlich“ (label = 1) und alle anderen Datensätze als „normal“ (label =0). (Siehe Abbildung 3.)

![Abbildung 3. Bezeichnungen für binäre Klassifizierung](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Abbildung 3. Bezeichnungen für binäre Klassifizierung

Beispiele für die Bezeichnungsstrategie einiger Anwendungsfälle sind unten aufgeführt.
- _Flugverspätungen_ : Für X kann ein Tag gewählt werden, um Verspätungen für die nächsten 24 Stunden vorherzusagen. Alle Flüge, die innerhalb von 24 Stunden vor einem Ausfall liegen, erhalten dann die Bezeichnung „1“.
- _Fehler bei der Geldausgabe an Geldautomaten_ : Ein Ziel kann hierbei darin bestehen, die Fehlerwahrscheinlichkeit für eine Transaktion innerhalb der nächsten Stunde zu ermitteln. In diesem Fall erhalten alle Transaktionen, die innerhalb der vergangenen Stunde vor dem Fehler liegen, die Bezeichnung „1“. Zum Vorhersagen der Fehlerwahrscheinlichkeit für die nächsten ausgegebenen N Banknoten erhalten alle Banknoten, die innerhalb der letzten N Banknoten eines Fehlers ausgegeben werden, die Bezeichnung „1“.
- _Ausfälle von Leistungsschutzschaltern_ : Das Ziel kann hierbei darin bestehen, den nächsten Ausfall des Leistungsschutzschalter-Befehls vorherzusagen. In diesem Fall wird für X ein zukünftiger Befehl gewählt.
- _Ausfälle der Türen von Zügen_ : X kann als zwei Tage ausgewählt werden.
- _Ausfall von Windturbinen_ : X kann als zwei Monate ausgewählt werden.

### <a name="regression-for-predictive-maintenance"></a>Regression für vorbeugende Wartung
Regressionsmodelle werden verwendet, um _die verbleibende Nutzungsdauer einer Ressource zu berechnen_. Die verbleibende Nutzungsdauer ist als der Zeitraum definiert, in dem eine Ressource betriebsbereit ist, bevor der nächste Fehler auftritt. Jedes Trainingsbeispiel ist ein Datensatz, der zu einer Zeiteinheit _nY_ für eine Ressource gehört, wobei mit _n_ das Vielfache angegeben wird. Das Modell sollte die verbleibende Nutzungsdauer für jedes neue Beispiel als _fortlaufende Zahl_ berechnen. Mit dieser Zahl wird die Zeit angegeben, die bis zum Fehler verbleibt.

#### <a name="label-construction-for-regression"></a>Erstellung von Bezeichnungen für die Regression
Hier wird die folgende Frage gestellt: „Welche Restnutzungsdauer hat das Gerät?“ Berechnen Sie für jeden Datensatz vor dem Fehler die Bezeichnung als Anzahl von Zeiteinheiten, die bis zum nächsten Fehler verbleiben. Bei dieser Methode handelt es sich bei den Bezeichnungen um fortlaufende Variablen. (Siehe Abbildung 4.)

![Abbildung 4. Bezeichnungen für die Regression](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Abbildung 4. Bezeichnungen für die Regression

Für die Regression erfolgt die Bezeichnung per Verweis auf einen Fehlerpunkt. Die Berechnung ist nicht möglich ohne Informationen dazu, wie lange eine Ressource vor einem Fehler fehlerfrei betrieben wurde. Im Gegensatz zur binären Klassifizierung können Ressourcen ohne jegliche Fehler in den Daten nicht für die Modellierung verwendet werden. Dieses Problem lässt sich am besten mit einem anderen statistischen Verfahren lösen, das als [Ereigniszeitanalyse](https://en.wikipedia.org/wiki/Survival_analysis) bezeichnet wird. Es kann aber zu potenziellen Komplikationen kommen, wenn dieses Verfahren auf PdM-Anwendungsfälle angewendet wird, die zeitbezogene Daten mit häufigen Intervallen umfassen. Weitere Informationen zur Ereigniszeitanalyse finden Sie auf [dieser Seite](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klassifizierung mit mehreren Klassen für vorbeugende Wartung
Verfahren mit der Klassifizierung mit mehreren Klassen können in PdM-Lösungen für zwei Szenarien verwendet werden:
- Vorhersage von _zwei zukünftigen Ergebnissen_ : Das erste Ergebnis ist _ein Bereich mit einer Zeitspanne bis zum Ausfall_ für eine Ressource. Die Ressource wird einem von mehreren möglichen Zeiträumen zugewiesen. Das zweite Ergebnis ist die Wahrscheinlichkeit eines Ausfalls in einem zukünftigen Zeitraum aufgrund _einer der verschiedenen Grundursachen_. Anhand dieser Vorhersage können die Wartungsmitarbeiter auf Symptome achten und Wartungszeitpläne erstellen.
- Vorhersage _der wahrscheinlichsten Grundursache_ eines bestimmten Fehlers. Bei diesem Ergebnis werden die richtigen Wartungsaktionen zur Behebung eines Fehlers empfohlen. Mit einer Prioritätenliste, die Grundursachen und empfohlene Reparaturmaßnahmen enthält, können Techniker die Reparaturschritte nach einem Fehler entsprechend ausführen.

#### <a name="label-construction-for-multi-class-classification"></a>Erstellung von Bezeichnungen für die Klassifizierung mit mehreren Klassen
Hier wird die folgende Frage gestellt: „Wie hoch ist die Wahrscheinlichkeit, dass eine Ressource innerhalb der nächsten _nZ_ Zeiteinheiten ausfällt?“ Hierbei steht _n_ für die Anzahl von Zeiträumen. Bezeichnen Sie zur Beantwortung dieser Frage nZ Datensätze vor dem Ausfall einer Ressource, indem Sie sog. Zeittöpfe (3Z, 2Z, Z) verwenden. Bezeichnen Sie alle anderen Datensätze als „normal“ (label = 0). Bei dieser Methode enthält die Zielvariable _kategoriebezogene_ Werte. (Siehe Abbildung 5.)

![Abbildung 5. Bezeichnungen für die Klassifizierung mit mehreren Klassen zur Vorhersage des Fehlerzeitpunkts](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Abbildung 5. Bezeichnungen für die Klassifizierung mit mehreren Klassen zur Vorhersage des Fehlerzeitpunkts

Hier wird die folgende Frage gestellt: „Wie hoch ist die Wahrscheinlichkeit, dass die Ressource innerhalb der nächsten X Zeiteinheiten aufgrund der Grundursache bzw. des Problems _P <sub>i</sub>_ ausfällt?“ Hierbei steht _i_ für die Anzahl von möglichen Grundursachen. Bezeichnen Sie zur Beantwortung dieser Frage X Datensätze vor dem Ausfall einer Ressource als „Ausfall wahrscheinlich aufgrund von Grundursache _P <sub>i</sub>_ “ (label = _P <sub>i</sub>_ ). Bezeichnen Sie alle anderen Datensätze als „normal“ (label = 0). Auch bei dieser Methode sind die Bezeichnungen kategoriebezogen (siehe Abbildung 6).

![Abbildung 6. Bezeichnungen für die Klassifizierung mit mehreren Klassen zur Vorhersage der Grundursache](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Abbildung 6. Bezeichnung für die Klassifizierung mit mehreren Klassen zur Vorhersage des Fehlerzeitpunkts

Bei diesem Modell wird jedem _P <sub>i</sub>_ eine anstehende Fehlerwahrscheinlichkeit sowie die Wahrscheinlichkeit für das Nichtauftreten eines Fehlers zugewiesen. Diese Wahrscheinlichkeiten können nach Größenordnung sortiert werden, um die Vorhersage der Probleme zu ermöglichen, die in Zukunft mit der höchsten Wahrscheinlichkeit auftreten.

Hier wird die folgende Frage gestellt: „Welche Wartungsaktionen werden nach einem Ausfall empfohlen?“ Zur Beantwortung dieser Frage ist für die Bezeichnungen _keine Auswahl eines zukünftigen Zeithorizonts erforderlich_ , da für das Modell kein in der Zukunft liegender Fehler vorhergesagt wird. Es wird lediglich die wahrscheinlichste Grundursache _nach dem Auftreten des Fehlers_ vorhergesagt.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Trainings-, Validierungs- und Testmethoden für Predictive Maintenance
Der [Team Data Science-Prozess](./overview.md) deckt den Trainings-, Test- und Validierungszyklus für Modelle vollständig ab. In diesem Abschnitt werden Aspekte beschrieben, die nur für PdM gelten.

### <a name="cross-validation"></a>Kreuzvalidierung im Vergleich
Das Ziel der [Kreuzvalidierung](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) ist die Definition eines Datasets, um das Modell in der Trainingsphase zu „testen“. Dieses Dataset wird als _Validierungssatz_ bezeichnet. Mit diesem Verfahren werden Probleme wie die _Überanpassung_ eingedämmt, und Sie erhalten Erkenntnisse dazu, wie das Modell für ein unabhängiges Dataset generalisiert wird. Hierbei handelt es sich um ein unbekanntes Dataset, das von einem echten Problem stammen kann. Bei den Trainings- und Testroutinen müssen die zeitabhängigen Aspekte berücksichtigt werden, um eine bessere Generalisierung für noch nicht bekannte zukünftige Daten zu erzielen.

Viele Machine Learning-Algorithmen sind von einer Reihe von Hyperparametern abhängig, mit denen die Modellleistung erheblich verändert werden kann. Die optimalen Werte dieser Hyperparameter werden beim Trainieren des Modells nicht automatisch berechnet. Sie sollten von einem Data Scientist angegeben werden. Es gibt mehrere Möglichkeiten, gute Werte für Hyperparameter zu ermitteln.

Am häufigsten wird die _k-fache Kreuzvalidierung_ verwendet, bei der die Beispiele nach dem Zufallsprinzip in _k_ Durchläufe aufgeteilt werden. Führen Sie für jede Gruppe von Hyperparameterwerten den Lernalgorithmus mit der Häufigkeit _k_ aus. Verwenden Sie bei jedem Durchlauf die Beispielwerte im aktuellen Durchlauf als Validierungssatz und die restlichen Beispielwerte als Trainingssatz. Trainieren Sie den Algorithmus über Trainingsbeispielwerte, und berechnen Sie die Leistungsmetriken anhand von Validierungsbeispielwerten. Berechnen Sie am Ende dieser Schleife den Mittelwert von _k_ Leistungsmetriken. Wählen Sie für jeden Satz mit Hyperparameterwerten diejenigen Werte aus, die über die beste durchschnittliche Leistung verfügen. Bei Auswahl von Hyperparametern muss häufig experimentiert werden.

In Bezug auf PdM-Probleme werden Daten als Zeitreihe mit Ereignissen aufgezeichnet, die aus mehreren Datenquellen stammen. Diese Datensätze können nach der Uhrzeit der Bezeichnung sortiert sein. Wenn das Dataset nach dem _Zufallsprinzip_ in einen Trainingssatz und einen Validierungssatz aufgeteilt wird, _weisen einige Trainingsbeispielwerte daher ggf. einen späteren Zeitpunkt als die Validierungsbeispielwerte auf_. Die zukünftige Leistung von Hyperparameterwerten wird anhand der Daten geschätzt, die _vor_ dem Trainieren des Modells erfasst wurden. Diese Schätzungen können besonders dann zu optimistisch ausfallen, wenn die Zeitreihen nicht unbeweglich sind und im Laufe der Zeit weiterentwickelt werden. Dies kann dazu führen, dass die ausgewählten Hyperparameterwerte suboptimal sind.

Die empfohlene Vorgehensweise ist das Aufteilen der Beispiele in einen Trainings- und einen Validierungssatz auf _zeitabhängige_ Weise, wobei alle Validierungsbeispiele einen späteren Zeitpunkt als alle Trainingsbeispiele aufweisen. Trainieren Sie den Algorithmus für jeden Satz mit Hyperparameterwerten anhand des Trainingsdatasets. Messen Sie die Leistung des Modells anhand des gleichen Validierungssatzes. Wählen Sie die Hyperparameterwerte aus, die die beste Leistung aufweisen. Hyperparameterwerte, die nach der Trainings-/Validierungsaufteilung gewählt werden, führen zu einer besseren zukünftigen Modellleistung als mit den Werten, die zufällig für die Kreuzvalidierung ausgewählt werden.

Das endgültige Modell kann generiert werden, indem ein Lernalgorithmus anhand der gesamten Trainingsdaten mit den besten Hyperparameterwerten trainiert wird.

### <a name="testing-for-model-performance"></a>Testen der Modellleistung
Nach der Erstellung eines Modells ist eine Schätzung der zukünftigen Leistung für neue Daten erforderlich. Eine gute Schätzung ist die Leistungsmetrik von Hyperparameterwerten, die anhand des Validierungssatzes berechnet werden, oder eine Metrik zur durchschnittlichen Leistung, die per Kreuzvalidierung berechnet wird. Diese Schätzungen sind häufig sehr optimistisch. Unternehmen verfügen meist noch über zusätzliche Richtlinien dazu, wie das Modell getestet werden soll.

Die empfohlene Vorgehensweise für PdM ist das Aufteilen der Beispiele in Trainings-, Validierungs- und Testdatasets auf _zeitabhängige_ Weise. Alle Testbeispiele sollten spätere Zeitpunkte als alle Trainings- und Validierungsbeispiele aufweisen. Generieren Sie nach der Aufteilung das Modell, und messen Sie die Leistung wie oben beschrieben.

Wenn Zeitreihen unbeweglich und leicht vorhersagbar sind, werden sowohl beim zufälligen als auch beim zeitabhängigen Ansatz ähnliche Schätzungen der zukünftigen Leistung generiert. Falls die Zeitreihen aber nicht unbeweglich oder schwer vorherzusagen sind, werden mit dem zeitabhängigen Ansatz realistischere Schätzungen der zukünftigen Leistung generiert.

### <a name="time-dependent-split"></a>Zeitabhängige Aufteilung
In diesem Abschnitt werden die bewährten Methoden zum Implementieren der zeitabhängigen Aufteilung beschrieben. Eine zeitabhängige bidirektionale Aufteilung auf Trainings- und Testsätze ist unten beschrieben.

Angenommen, es ist ein Datenstrom von Ereignissen mit Zeitstempel, z.B. Messungen verschiedener Sensoren, vorhanden. Definieren Sie Features und Bezeichnungen von Trainings- und Testbeispielen für Zeitrahmen, die mehrere Ereignisse enthalten. Erstellen Sie beispielsweise für die binäre Klassifizierung Features basierend auf vergangenen Ereignissen, und erstellen Sie Bezeichnungen basierend auf zukünftigen Ereignissen in „X“ Zeiteinheiten in der Zukunft (siehe Abschnitte zur [Featureentwicklung](#feature-engineering) und zu den Modellierungsverfahren). Der Bezeichnungszeitrahmen eines Beispielwerts liegt also nach dem Zeitrahmen seiner Features.

Wählen Sie für die zeitabhängige Aufteilung einen _Trainingstrennzeitpunkt T <sub>c</sub>_ für das Modelltraining, wobei die Hyperparameter Verlaufsdaten bis zum Zeitpunkt T <sub>c</sub> nutzen. Um zu verhindern, dass zukünftige Bezeichnungen nach dem Zeitpunkt T<sub>c</sub> in die Trainingsdaten einfließen, wählen Sie als spätesten Zeitpunkt der Bezeichnung von Trainingsbeispielen einen Zeitpunkt, der X Einheiten vor T<sub>c</sub> liegt. Im Beispiel in Abbildung 7 steht jedes Quadrat für einen Datensatz des Datasets, für das die Features und Bezeichnungen wie oben beschrieben berechnet werden. In der Abbildung sind die Datensätze dargestellt, die in die Trainings- und Testsätze für X=2 und W=3 einfließen sollen:

![Abbildung 7. Zeitabhängige Aufteilung für binäre Klassifizierung](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Abbildung 7. Zeitabhängige Aufteilung für binäre Klassifizierung

Die grünen Quadrate stehen für Datensätze der Zeiteinheiten, die für das Training verwendet werden können. Bei der Generierung jedes Trainingsbeispiels werden die letzten drei Zeiträume für die Featuregenerierung und zwei zukünftige Zeiträume für die Bezeichnung vor dem Zeitpunkt T<sub>c</sub> berücksichtigt. Wenn ein Teil der beiden zukünftigen Zeiträume nach dem Zeitpunkt T<sub>c</sub> liegt, sollten Sie dieses Beispiel aus dem Trainingsdataset ausschließen, da keine Sichtbarkeit über T<sub>c</sub> hinweg vorausgesetzt wird.

Die schwarzen Quadrate stehen für die Datensätze des endgültigen Datasets mit Bezeichnungen, die gemäß der obigen Einschränkung nicht im Trainingsdataset verwendet werden sollten. Diese Datensätze werden auch nicht in den Testdaten verwendet, da sie vor dem Zeitpunkt T<sub>c</sub> liegen. Außerdem hängen die entsprechenden Bezeichnungszeitrahmen teilweise vom Trainingszeitrahmen ab, was nicht ideal ist. Trainings-und Testdaten sollten über separate Bezeichnungszeitrahmen verfügen, um Lecks von Bezeichnungsinformationen zu verhindern.

Das bisher beschriebene Verfahren lässt eine Überlappung zwischen Trainings- und Testbeispielen zu, deren Zeitstempel in der Nähe von T<sub>c</sub> liegen. Eine Lösung zur Erzielung einer stärkeren Trennung ist das Ausschließen von Beispielen, die sich innerhalb von W Zeiteinheiten des Zeitpunkts T<sub>c</sub> vom Testsatz befinden. Für eine solch aggressive Aufteilung ist aber eine hohe Datenverfügbarkeit erforderlich.

Regressionsmodelle für die Vorhersage der verbleibenden Nutzungsdauer sind von möglichen Lecks stärker betroffen. Die Verwendung der zufälligen Aufteilung führt zu einer extremen Überanpassung. Bei Regressionsproblemen sollte so aufgeteilt werden, dass die Datensätze, die zu Ressourcen mit Fehlern vor dem Zeitpunkt T<sub>c</sub> liegen, in den Trainingssatz einfließen. Datensätze von Ressourcen, deren Fehler nach dem Trennzeitpunkt liegen, fließen in den Testsatz ein.

Eine weitere bewährte Methode für die Aufteilung von Daten für das Training und den Test ist die Aufteilung nach der Ressourcen-ID. Die Aufteilung sollte so erfolgen, dass keine Ressourcen aus dem Trainingssatz für das Testen der Modellleistung verwendet werden. Bei diesem Ansatz besteht für ein Modell eine höhere Chance realistischere Ergebnisse mit neuen Ressourcen zu liefern.

### <a name="handling-imbalanced-data"></a>Behandeln von unausgeglichenen Daten
Wenn bei Klassifizierungsproblemen mehr Beispielwerte einer Klasse als für eine andere Klasse vorhanden sind, wird das Dataset als _unausgeglichen_ bezeichnet. Im Idealfall wird eine ausreichende Anzahl von repräsentativen Werten jeder Klasse in den Trainingsdaten bevorzugt, um die Differenzierung zwischen unterschiedlichen Klassen zu ermöglichen. Wenn eine Klasse weniger als 10% der Daten umfasst, werden die Daten als unausgeglichen angesehen. Die unterrepräsentierte Klasse wird als _Minderheitsklasse_ bezeichnet.

Es kommt für viele PdM-Probleme zu unausgeglichenen Datasets, bei denen eine Klasse gegenüber einer oder mehreren anderen Klassen stark unterrepräsentiert ist. In einigen Situationen kann es sein, dass die Minderheitsklasse nur 0,001% der gesamten Datenpunkte ausmacht. Die Unausgeglichenheit von Klassen ist nicht auf PdM beschränkt. Auch in anderen Bereichen, in denen Fehler und Anomalien selten sind, kommt es zu einem ähnlichen Problem, z.B. Betrugserkennung und Netzwerkangriffe. Die Beispiele der Minderheitsklassen bestehen aus diesen Fehlern.

Bei der Unausgeglichenheit von Klassen in Daten wird die Leistung der meisten Standardlernalgorithmen beeinträchtigt, da versucht wird, die Gesamtfehlerrate zu verringern. Für ein Dataset mit 99% an negativen und 1% an positiven Beispielen kann für ein Modell eine Genauigkeit von 99% angegeben werden, indem alle Instanzen als negativ bezeichnet werden. Alle positiven Beispiele sind für das Modell aber falsch klassifiziert. Die Genauigkeit ist zwar hoch, aber der Algorithmus ist nicht nützlich. Daher sind herkömmliche Evaluierungsmetriken, z.B. die _Gesamtgenauigkeit der Fehlerrate_ , für das Lernen mit unausgeglichenen Daten nicht ausreichend. Bei unausgeglichenen Datasets werden andere Metriken für die Modellevaluierung verwendet:
- Precision
- Recall (Trefferquote)
- F1 Scores (F1-Maße)
- Cost Adjusted ROC (Receiver Operating Characteristics) (ROC-Kurven mit Kostenanpassung)

Weitere Informationen zu diesen Metriken finden Sie unter [Modellevaluierung](#model-evaluation).

Es gibt aber einige Methoden, die als Hilfe beim Beheben von Problemen mit der Unausgeglichenheit von Klassen dienen. Die beiden wichtigsten Methoden sind _Stichprobenverfahren_ und _kostenbewusstes Lernen_.

#### <a name="sampling-methods"></a>Stichprobenmethoden
Lernvorgänge mit unausgeglichenen Daten umfassen die Verwendung von Methoden zur Stichprobenentnahme, um das Trainingsdataset in ein ausgeglichenes Dataset zu ändern. Methoden zur Stichprobenentnahme dürfen nicht auf den Testsatz angewendet werden. Es gibt zwar mehrere Stichprobenverfahren, aber die einfachsten sind die _Überquotierung_ und _Unterquotierung_ nach dem Zufallsprinzip.

Die _Überquotierung nach dem Zufallsprinzip_ umfasst das Auswählen einer zufälligen Stichprobe aus der Minderheitsklasse, das Replizieren dieser Beispielwerte und das Hinzufügen zum Trainingsdataset. So wird die Anzahl von Beispielwerten in der Minderheitsklasse erhöht und somit letztendlich die Anzahl von Beispielwerten für unterschiedliche Klassen ausgeglichen. Ein Nachteil der Überquotierung ist, dass mehrere Instanzen von bestimmten Beispielwerten dazu führen können, dass der Klassifizierer zu spezifisch wird und es zu einer Überanpassung kommt. Das Modell weist ggf. eine hohe Trainingsgenauigkeit auf, aber die Leistung in Bezug auf Testdaten im Hintergrund ist unter Umständen nicht optimal.

Bei der _Unterquotierung nach dem Zufallsprinzip_ geht es dagegen um das Auswählen einer zufälligen Stichprobe aus einer Mehrheitsklasse und das Entfernen dieser Beispielwerte aus dem Trainingsdataset. Beim Entfernen von Beispielwerten aus der Mehrheitsklasse kann es passieren, dass dem Klassifizierer wichtige Konzepte der Mehrheitsklasse entgehen. _Hybridstichproben_ , bei denen die Minderheitsklasse überquotiert und die Mehrheitsklasse gleichzeitig unterquotiert wird, sind ebenfalls ein guter Ansatz.

Es gibt viele anspruchsvolle Verfahren für die Stichprobenentnahme. Das gewählte Verfahren hängt von den Dateneigenschaften und Ergebnissen der iterativen Experimente ab, die vom Data Scientist durchgeführt werden.

#### <a name="cost-sensitive-learning"></a>Kostenbewusstes Lernen
Bei PdM sind Fehler, die zur Minderheitsklasse gehören, interessanter als normale Beispielwerte. Das Hauptaugenmerk liegt daher auf der Leistung des Algorithmus in Bezug auf Fehler. Die fehlerhafte Vorhersage einer positiven Klasse als negative Klasse kann teurer als der umgekehrte Fall werden. Diese Situation wird im Allgemeinen als ungleicher Verlust oder asymmetrische Kosten der Fehlklassifizierung von Elementen als unterschiedliche Klassen bezeichnet. Der ideale Klassifizierer sollte eine hohe Vorhersagegenauigkeit für die Minderheitsklasse ermöglichen, ohne die Genauigkeit für die Mehrheitsklasse zu beeinträchtigen.

Es gibt mehrere Arten, diese Ausgeglichenheit zu erzielen. Um das Problem des ungleichen Verlusts zu vermeiden, können Sie der Fehlklassifizierung der Minderheitsklasse hohe Kosten zuweisen und versuchen, die Gesamtkosten möglichst gering zu halten. Bei Algorithmen wie _SVMs (Support Vector Machines)_ ist diese Methode inhärent, indem zugelassen wird, dass die Kosten von positiven und negativen Beispielwerten während des Trainings angegeben werden. Auf ähnliche Weise bieten auch Boostingmethoden wie _Boosted Decision Trees_ normalerweise eine gute Leistung in Bezug auf unausgeglichene Daten.

## <a name="model-evaluation"></a>Modellauswertung
Die Fehlklassifizierung ist ein erhebliches Problem für PdM-Szenarien, bei denen die Kosten für Fehlalarme für das Unternehmen hoch sind. Beispielsweise kann die Entscheidung, ein Flugzeug basierend auf der falschen Vorhersage eines Triebwerksfehlers nicht starten zu lassen, zu einer Störung von Flug- und Reiseplänen führen. Das Herausnehmen einer Maschine aus einer Montagezeile kann zu Umsatzverlusten führen. Daher ist die Modellevaluierung mit den richtigen Leistungsmetriken für neue Testdaten von entscheidender Bedeutung.

Hier sind einige gängige Leistungsmetriken beschrieben, die zum Evaluieren von PdM-Modellen dienen:

- Die [Genauigkeit](https://en.wikipedia.org/wiki/Accuracy_and_precision) ist die am häufigsten verwendete Metrik zum Beschreiben der Leistung eines Klassifizierers. Die Genauigkeit reagiert aber sensibel auf Datenverteilungen und ist ein ineffektiver Wert für Szenarien mit unausgeglichenen Datasets. Stattdessen werden andere Metriken verwendet. Tools wie die [Wahrheitsmatrix](https://en.wikipedia.org/wiki/Confusion_matrix) werden genutzt, um die Genauigkeit des Modells zu berechnen und zu hinterfragen.
- Die Genauigkeit ([Precision](https://en.wikipedia.org/wiki/Precision_and_recall)) von PdM-Modellen bezieht sich auf die Rate der Fehlalarme. Eine geringere Genauigkeit des Modells basiert im Allgemeinen auf einer höheren Zahl von Fehlalarmen.
- Mit der Trefferquote ([Recall](https://en.wikipedia.org/wiki/Precision_and_recall)) wird angegeben, wie viele Fehler im Testsatz vom Modell richtig identifiziert wurden. Höhere Trefferquotenraten bedeuten, dass das Modell beim Identifizieren der tatsächlichen Fehler erfolgreich ist.
- Das F1-Maß ([F1 Score](https://en.wikipedia.org/wiki/F1_score)) ist der harmonische Mittelwert aus der Genauigkeit und der Trefferquote, wobei der Wert zwischen 0 (am schlechtesten) und 1 (am besten) liegt.

Für die binäre Klassifizierung:
- Die ROC-Kurve ([Receiver Operating Characteristic (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)) ist ebenfalls eine gängige Metrik. Bei ROC-Kurven wird die Modellleistung basierend auf einem festen Punkt auf der ROC-Kurve interpretiert.
- Für PdM-Probleme liefern _Deziltabellen_ und _Prognosegütediagramme_ aber mehr Informationen. Der Schwerpunkt liegt dabei nur auf der positiven Klasse (Fehler), und es ergibt sich ein komplexeres Bild der Algorithmusleistung als bei ROC-Kurven.
  - _Deziltabellen_ werden erstellt, indem Testbeispielwerte in absteigender Reihenfolge der Fehlerwahrscheinlichkeiten verwendet werden. Die sortierten Beispielwerte werden dann zu Dezilen gruppiert (10% der Beispiele mit der höchsten Wahrscheinlichkeit, dann 20%, 30% usw.). Anhand des Verhältnisses „(Richtig-Positiv-Rate)/(Zufällige Baseline)“ für jedes Dezil kann die Algorithmusleistung der Dezile einzeln geschätzt werden. Die zufällige Baseline hat den Wert 0,1, 0,2 usw.
  - Bei [Prognosegütediagrammen](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) wird für alle Dezile die Richtig-Positiv-Rate eines Dezils gegenüber der zufälligen Richtig-Positiv-Rate ausgegeben. Normalerweise stehen bei den Ergebnissen die ersten Dezile im Vordergrund, da hiermit die größten Vorteile erzielt werden. Die ersten Dezile können auch als repräsentativ für den „Risikozustand“ angesehen werden, wenn sie für PdM verwendet werden.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modelloperationalisierung für Predictive Maintenance

Der Vorteil des Data Science-Vorgangs wird nur realisiert, wenn für das trainierte Modell die Operationalität hergestellt wird. Dies bedeutet, dass das Modell in den Geschäftssystemen bereitgestellt werden muss, um Vorhersagen basierend auf neuen Daten zu treffen, die zuvor nicht bekannt waren.  Die neuen Daten müssen auf zwei Arten genau mit der _Modellsignatur_ des trainierten Modells konform sein:
- Alle Features müssen in jeder logischen Instanz der neuen Daten (z.B. einer Tabellenzeile) vorhanden sein.
- Die neuen Daten müssen genau wie die Trainingsdaten vorverarbeitet werden, und die Features müssen entsprechend erstellt werden.

Der obige Prozess verfügt in akademischer Literatur und Branchenliteratur über viele verschiedene Bezeichnungen. Aber mit allen folgenden Aussagen ist dasselbe gemeint:
- _Bewerten von neuen Daten_ mit dem Modell
- _Anwenden des Modells_ auf neue Daten
- _Operationalisieren_ des Modells
- _Bereitstellen_ des Modells
- _Ausführen des Modells_ für neue Daten

Wie bereits erwähnt, unterscheidet sich die Modelloperationalisierung für PdM von vergleichbaren Ansätzen. Bei Szenarien mit Anomalie- und Fehlererkennung wird normalerweise die _Onlinebewertung_ (auch als _Echtzeitbewertung_ bezeichnet) durchgeführt. Hierbei wird jeder eingehende Datensatz vom Modell _bewertet_ und eine Vorhersage zurückgegeben. Für die Anomalieerkennung ist die Vorhersage ein Hinweis darauf, dass eine Anomalie aufgetreten ist (Beispiel: einklassige SVM). Für die Fehlererkennung ist dies der Typ oder die Klasse des Fehlers.

Für PdM wird dagegen die _Batchbewertung_ verwendet. Damit sie mit der Modellsignatur konform sind, müssen die Features in den neuen Daten genauso wie die Trainingsdaten erstellt werden. Für die großen Datasets, die für neue Daten typisch sind, werden Features in Zeitfenstern aggregiert und in Batches bewertet. Die Batchbewertung wird normalerweise in verteilten Systemen wie [Spark](https://spark.apache.org/) oder [Azure Batch](../../batch/batch-service-workflow-features.md) durchgeführt. Es gibt eine Reihe von Alternativen, die beide nicht optimal sind:
- Bei Streamingdatenmodulen wird die Aggregation über Fenster im Arbeitsspeicher unterstützt. Es kann also argumentiert werden, dass die Onlinebewertung unterstützt wird. Diese Systeme sind aber für Daten mit hoher Dichte in engen Zeitfenstern oder weniger Elemente in größeren Zeitfenstern geeignet. Unter Umständen können sie für Daten mit hoher Dichte in größeren Zeitfenstern nicht gut skaliert werden, wie dies bei PdM-Szenarien zu beobachten ist.
- Falls die Batchbewertung nicht verfügbar ist, besteht die Lösung darin, die Onlinebewertung so anzupassen, dass neue Daten jeweils in kleineren Batches verarbeitet werden.

## <a name="solution-templates-for-predictive-maintenance"></a>Lösungsvorlagen für Predictive Maintenance

Der letzte Abschnitt dieses Leitfadens enthält eine Liste mit den PdM-Lösungsvorlagen, Tutorials und Experimenten, die in Azure implementiert sind. Diese PdM-Anwendungen können in einigen Fällen innerhalb weniger Minuten in einem Azure-Abonnement bereitgestellt werden. Sie können als Proof-of-Concept-Demos, Sandboxes zum Experimentieren mit Alternativen oder Beschleuniger für tatsächliche Produktionsimplementierungen verwendet werden. Diese Vorlagen befinden sich im [Azure KI-Katalog](https://gallery.azure.ai) oder auf [Azure GitHub](https://github.com/Azure). Die unterschiedlichen Beispiele werden im Laufe der Zeit in diese Lösungsvorlage eingebunden.

| # | Titel | BESCHREIBUNG |
|--:|:------|-------------|
| 2 | [Azure Predictive Maintenance Solution Template](https://github.com/Azure/AI-PredictiveMaintenance) (Azure Predictive Maintenance-Lösungsvorlage) | Eine Open-Source-Lösungsvorlage zur Veranschaulichung von Azure ML-Modellierung in einer vollständigen Azure-Infrastruktur, die Szenarien für Predictive Maintenance im Kontext von IoT-Remoteüberwachung unterstützen kann. |
| 3 | [Deep Learning for Predictive Maintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) (Deep Learning für Predictive Maintenance) | Azure Notebook mit einer Demolösung für die Verwendung von LSTM-Netzwerken (Long Short-Term Memory) – einer Klasse von wiederkehrenden neuronalen Netzwerken – für Predictive Maintenance mit einem [Blogbeitrag für dieses Beispiel](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Azure Predictive Maintenance for Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) (Azure Predictive Maintenance für die Luft- und Raumfahrt) | Eine der ersten PdM-Lösungsvorlagen, die auf Azure ML v1.0 für die Wartung von Flugzeugen basieren. Dieser Leitfaden ist aus diesem Projekt entstanden. |
| 5 | [Azure AI Toolkit for IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) (Azure KI-Toolkit für IoT Edge) | KI für IoT Edge mit TensorFlow. Im Toolkit sind Deep Learning-Modelle in Azure IoT Edge-kompatiblen Docker-Containern verpackt, und die Modelle werden als REST-APIs verfügbar gemacht.
| 6 | [Azure IoT Predictive Maintenance](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS (Preconfigured Solution, vorkonfigurierte Lösung). PdM-Vorlage für die Flugzeugwartung mit IoT Suite. Nutzen Sie auch [ein weiteres Dokument](../../iot-accelerators/quickstart-predictive-maintenance-deploy.md) und die [exemplarische Vorgehensweise](../../iot-accelerators/iot-accelerators-predictive-walkthrough.md) für dasselbe Projekt. |
| 7 | [Predictive Maintenance template using SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) (Predictive Maintenance-Vorlage mit SQL Server R Services) | Demo des Szenarios mit einer verbleibenden Nutzungsdauer basierend auf R Services. |
| 8 | [Predictive Maintenance Modeling Guide (Modellhandbuch für die vorbeugende Wartung)](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Datasetfeature für die Wartung von Flugzeugen, das mit R erstellt wurde und über [Experimente](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) und [Datasets](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) sowie ein Azure-Notebook und [Experimente](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) in AzureML v1.0 verfügt|

## <a name="training-resources-for-predictive-maintenance"></a>Schulungsressourcen für Predictive Maintenance

Microsoft Azure bietet Lernpfade für die grundlegenden Konzepte, die hinter PdM-Techniken stehen, außerdem Inhalte, Schulungen und Übungen zu allgemeinen KI-Konzepten.

| Schulungsressource  | Verfügbarkeit |
|:-------------------|--------------|
| [Lernpfad für PdM mithilfe von Trees und Random Forest](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Öffentlich | 
| [Lernpfad für PdM mithilfe von Deep Learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Öffentlich |
| [AI Developer in Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Öffentlich |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Öffentlich |
| [Azure KI-Lernmaterial auf GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Öffentlich |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Öffentlich |
| [Microsoft KI – YouTube-Webinare](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Öffentlich |
| [Microsoft AI Show](https://channel9.msdn.com/Shows/AI-Show) | Öffentlich |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partner |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Partner |

Zusätzlich werden kostenlose MOOCs (Massive Open Online Courses) zu KI von akademischen Institutionen wie Stanford und MIT sowie anderen Bildungseinrichtungen angeboten.