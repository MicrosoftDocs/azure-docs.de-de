---
title: Best Practices für die Bereitstellung
description: Dieser Artikel enthält bewährte Methoden für die Bereitstellung von Azure Purview. Azure Purview ermöglicht Benutzern das Registrieren, Ermitteln, Verstehen und Nutzen von Datenquellen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 1b2841f69ebe91dac748a4b2e24dc0c33756b1da
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400691"
---
# <a name="azure-purview-deployment-best-practices"></a>Bewährte Methoden zur Bereitstellung von Azure Purview

In diesem Artikel werden allgemeine Aufgaben beschrieben, die Ihnen bei der Bereitstellung von Purview in der Produktion helfen. Diese Aufgaben können in Phasen im Verlauf eines Monats oder einem längeren Zeitraum abgeschlossen werden. Selbst Unternehmen, die Purview bereits bereitgestellt haben, können mit diesem Leitfaden sicherstellen, dass sie ihre Investitionen optimal nutzen.

Eine gut geplante Bereitstellung einer Datengovernanceplattform (z. B. Azure Purview) kann folgende Vorteile bieten:

- Bessere Datenermittlung
- Verbesserte analytische Zusammenarbeit
- Maximierte Rendite.

## <a name="prerequisites"></a>Voraussetzungen

* Zugriff auf Microsoft Azure mit einem Entwicklungs- oder Produktionsabonnement
* Möglichkeit zum Erstellen von Azure-Ressourcen einschließlich Purview
* Zugriff auf Datenquellen wie Azure Data Lake Storage oder Azure SQL in Test-, Entwicklungs- oder Produktionsumgebungen
  * Für Data Lake Storage ist die Rolle „Leser“ erforderlich.
  * Für SQL muss die Identität Tabellen abfragen können, um Stichproben von Klassifizierungen zu erhalten.
* Zugriff auf Azure Security Center oder die Möglichkeit der Zusammenarbeit mit dem Security Center-Administrator für die Datenbezeichnung

## <a name="identify-objectives-and-goals"></a>Identifizieren der Ziele

Viele Organisationen haben ihre Datengovernancejourney gestartet, indem sie individuelle Lösungen entwickelt haben, die bestimmte Anforderungen isolierter Gruppen und Datendomänen in der Organisation erfüllen. Erfahrungen können je nach Branche, Produkt und Kultur variieren, aber unabhängig davon finden es die meisten Organisationen schwierig, konsistente Kontrollen und Richtlinien für diese Arten von Lösungen zu verwalten.

Zu einigen der allgemeinen Datengovernanceziele, die Sie wohl in den frühen Phasen identifizieren, zählen folgende:

* Maximieren des Geschäftswerts Ihrer Daten
* Aktivieren einer Datenkultur, in der Datenconsumer Daten leicht finden, interpretieren und vertrauen können
* Verbessern der Zusammenarbeit zwischen verschiedenen Geschäftseinheiten, um eine konsistente Datenumgebung bereitzustellen
* Förderung von Innovationen durch Beschleunigung der Datenanalyse, um die Vorteile der Cloud zu nutzen
* Verkürzen der Zeit zum Ermitteln von Daten durch Self-Service-Optionen für verschiedene Qualifikationsgruppen
* Verkürzen der Markteinführungszeit für die Übermittlung von Analyselösungen, die den Dienst für Ihre Kunden verbessern
* Reduzieren der operativen Risiken aufgrund der Verwendung von domänenspezifischen Tools und nicht unterstützter Technologie

Im allgemeinen Ansatz werden diese übergeordneten Ziele in verschiedene Kategorien und Ziele aufgeschlüsselt. Beispiele:

|Kategorie|Zielsetzung|
|---------|---------|
|Ermittlung|Administratorbenutzer sollten in der Lage sein, Azure- und Nicht-Azure-Datenquellen (einschließlich lokaler Quellen) zu scannen, um automatisch Informationen zu den Datenressourcen zu erfassen.|
|Klassifizierung|Die Plattform sollte Daten auf Basis einer Stichprobe der Daten automatisch klassifizieren und eine manuelle Außerkraftsetzung mithilfe benutzerdefinierter Klassifizierungen ermöglichen.|
|Nutzung|Die geschäftlichen Benutzer sollten in der Lage sein, Informationen zu den einzelnen Ressourcen sowohl für geschäftliche als auch für technische Metadaten zu finden.|
|Herkunft|Jede Ressource muss eine grafische Ansicht der zugrunde liegenden Datasets anzeigen, damit die Benutzer die ursprünglichen Quellen und die vorgenommenen Änderungen verstehen.|
|Zusammenarbeit|Die Plattform muss Benutzern die Zusammenarbeit ermöglichen, indem zusätzliche Informationen zu den einzelnen Datenressourcen bereitgestellt werden.|
|Berichterstellung|Die Benutzer müssen in der Lage sein, die Berichte über die Datenumgebung einschließlich vertraulicher Daten und Daten, die eine zusätzliche Anreicherung benötigen, anzuzeigen.|
|Datengovernance|Die Plattform muss dem Administrator die Möglichkeit geben, Richtlinien für die Zugriffssteuerung zu definieren, und den Datenzugriff auf Benutzerbasis automatisch erzwingen.|
|Workflow|Die Plattform muss die Möglichkeit bieten, den Workflow zu erstellen und zu ändern, sodass Sie problemlos aufskalieren und verschiedene Aufgaben innerhalb der Plattform automatisieren können.|
|Integration|Andere Technologien von Drittanbietern, z. B. für Ticketing oder Orchestrierung, müssen über Skripts oder REST-APIs in die Plattform integrierbar sein.|

## <a name="top-questions-to-ask"></a>Die wichtigsten Fragen

Wenn Ihre Organisation den allgemeinen Zielen zustimmt, kommen viele Fragen bei den einzelnen Gruppen auf. Es ist wichtig, diese Fragen zu erfassen, um einen Plan zu erstellen, der alle Interessen berücksichtigt. Einige Beispielfragen, die während der ersten Anfangsphase aufkommen können:

1. Was sind die wichtigsten Datenquellen und Datensysteme der Organisation?
2. Welche Optionen gibt es für Datenquellen, die noch nicht von Purview unterstützt werden?
3. Wie viele Purview-Instanzen benötigen wir?
4. Wer sind die Benutzer?
5. Wer kann neue Datenquellen scannen?
6. Wer kann Inhalte innerhalb von Purview ändern?
7. Welchen Prozess kann ich verwenden, um die Datenqualität in Purview zu verbessern?
8. Wie wird der Bootstrap der Plattform mit vorhandenen wichtigen Ressourcen, Glossarbegriffen und Kontakten durchgeführt?
9. Wie erfolgt die Integration in vorhandene Systeme?
10. Wie werden Feedback gesammelt und ein nachhaltiger Prozess erstellt?

Auch wenn Sie die meisten dieser Fragen nicht sofort beantworten können, können sie Ihrer Organisation helfen, dieses Projekt zu gestalten und sicherzustellen, dass alle unverzichtbaren Voraussetzungen erfüllt werden können.

## <a name="include-the-right-stakeholders"></a>Einbeziehen der richtigen Projektbeteiligten

Um den Erfolg der Implementierung von Purview für das gesamte Unternehmen sicherzustellen, ist es wichtig, die richtigen Projektbeteiligten einzubeziehen. In der Anfangsphase sind nur wenige Personen beteiligt. Wenn der Umfang erweitert wird, müssen jedoch weitere Rollen zum Projekt beizutragen und ihr Feedback geben.

Einige wichtige Projektbeteiligte, die Sie möglicherweise einbeziehen möchten:

|Persona|Rollen|
|---------|---------|
|**Chief Data Officer**|Der CDO beaufsichtigt eine Reihe von Funktionen, die Datenverwaltung, Datenqualität, Masterdatenverwaltung, Data Science, Business Intelligence und das Erstellen einer Datenstrategie umfassen können. Er kann der Sponsor des Purview-Implementierungsprojekts sein.|
|**Domänen-/unternehmensbesitzende Person**|Eine Geschäftsperson, die die Verwendung von Tools beeinflusst und die Kontrolle über das Budget hat.|
|**Datenanalyst**|Er kann ein Geschäftsproblem formulieren und Daten analysieren, um Führungskräfte bei Geschäftsentscheidungen zu unterstützen.|
|**Datenarchitekt**|Er entwirft Datenbanken für unternehmenswichtige Branchen-Apps und entwirft und implementiert gleichzeitig die Datensicherheit.|
|**Datentechniker**|Er betreibt und verwaltet den Datenstapel, pullt Daten aus verschiedenen Quellen, integriert Daten und bereitet sie vor und richtet Datenpipelines ein.|
|**Data Scientist**|Er erstellt analytische Modelle und richtet Datenprodukte ein, auf die APIs zugreifen können.|
|**Datenbankadministrator**|Er ist Besitzer von datenbankbezogenen Vorfällen und Anforderungen in Vereinbarungen zum Service Level (Service Level Agreements, SLAs), verfolgt sie nach und löst sie und kann Datenpipelines einrichten.|
|**DevOps**|Entwicklung und Implementierung von Branchenanwendungen; dies kann das Schreiben von Skripts und Orchestrierungsfunktionen umfassen.|
|**Datensicherheitsspezialist**|Er bewertet die allgemeine Netzwerk- und Datensicherheit, in Purview ein- und von dort ausgehende Daten inbegriffen.|

## <a name="identify-key-scenarios"></a>Identifizieren von Schlüsselszenarios

Purview kann verwendet werden, um die Datengovernance zentral in der Datenumgebung einer Organisation zu verwalten, die sich auf Cloud- und lokale Umgebungen erstreckt. Für eine erfolgreiche Implementierung müssen Sie wichtige Szenarios identifizieren, die für das Unternehmen von entscheidender Bedeutung sind. Diese Szenarios können Grenzen von Geschäftseinheiten überschreiten oder sich auf mehrere vor- oder nachgelagerte Benutzerrollen auswirken.

Diese Szenarios können auf verschiedene Weise notiert werden, Sie sollten jedoch mindestens diese fünf Dimensionen einschließen:

1. Rolle: Wer sind die Benutzer?
2. Quellsystem: Was sind die Datenquellen, z. B. Azure Data Lake Storage Gen2 oder Azure SQL-Datenbank?
3. Auswirkungsbereich: Was ist die Kategorie dieses Szenarios?
4. Detailszenarios: Wie lösen die Benutzer mithilfe von Purview Probleme?
5. Erwartetes Ergebnis: Was sind die Erfolgskriterien?

Die Szenarios müssen spezifisch, realisierbar und mit messbaren Ergebnissen ausführbar sein. Sie können diese Beispielszenarios verwenden:

|Szenario|Detail|Persona|
|---------|---------|---------|
|Katalog unternehmenskritischer Ressourcen|Ich benötige Informationen zu den einzelnen Datasets, um gut zu verstehen, worum es sich dabei handelt. Dieses Szenario umfasst sowohl geschäftliche als auch technische Metadaten über das Dataset im Katalog. Zu den Datenquellen gehören Azure Data Lake Storage Gen2, Azure Synapse DW und/oder Power BI. Dieses Szenario umfasst auch lokale Ressourcen wie SQL Server.|Business Analyst, wissenschaftliche Fachkraft für Daten, technische Fachkraft für Daten|
|Ermitteln unternehmenskritischer Ressourcen|Ich benötige eine Suchmaschine, die alle Metadaten im Katalog durchsuchen kann. Ich sollte mit einer einfachen oder komplexen Suche mithilfe eines Platzhalters einen technischen oder Geschäftsbegriff suchen können.|Business Analyst, wissenschaftliche Fachkraft für Daten, technische Fachkraft für Daten, Datenadministrator|
|Nachverfolgen von Daten, um deren Ursprung zu verstehen und Datenprobleme zu beheben|Ich benötige die Datenherkunft, um Daten in Berichten, Vorhersagen oder Modellen zur ursprünglichen Quelle zurückzuverfolgen und die Änderungen und die Speicherorte der Daten während ihres Lebenszyklus zu verstehen. In diesem Szenario müssen priorisierte Datenpipelines, Azure Data Factory und Databricks unterstützt werden.|Technische Fachkraft für Daten, wissenschaftliche Fachkraft für Daten|
|Anreichern wichtiger Datenressourcen mit Metadaten|Ich muss das Dataset im Katalog mit technischen Metadaten anreichern, die automatisch generiert werden. Klassifizierung und Bezeichnung sind Beispiele hierfür.|Technische Fachkraft für Daten, domänen-/unternehmensbesitzende Person|
|Kontrolle über Datenressourcen mit benutzerfreundlicher Umgebung|Ich benötige ein Unternehmensglossar für unternehmensspezifische Metadaten. Geschäftsbenutzer können Purview für Self-Service-Szenarios verwenden, um ihre Daten mit Anmerkungen zu versehen und zu ermöglichen, dass die Daten bei einer Suche problemlos erkannt werden.|Domänen-/unternehmensbesitzende Person, Business Analyst, wissenschaftliche Fachkraft für Daten, technische Fachkraft für Daten|

## <a name="deployment-models"></a>Bereitstellungsmodelle

Wenn nur eine kleine Gruppe Purview für grundlegende Anwendungsfälle verwendet, könnte der Ansatz so einfach sein, dass eine Purview-Instanz für die gesamte Gruppe eingesetzt wird. Vielleicht fragen Sie sich jedoch auch, ob Ihre Organisation mehrere Purview-Instanzen benötigt. Und wie Mitarbeiter die Ressourcen beim Einsatz mehrerer Purview-Instanzen von einer Stufe zur anderen höherstufen können.

### <a name="determine-the-number-of-purview-instances"></a>Bestimmen der Anzahl von Purview-Instanzen

In den meisten Fällen sollte nur ein Purview-Konto für die gesamte Organisation vorhanden sein. Dabei werden die „Netzwerkeffekte“ maximal genutzt, wobei der Wert der Plattform als Funktion der Daten auf der Plattform exponentiell zunimmt.

Es gibt jedoch Ausnahmen von diesem Muster:

1. **Testen neuer Konfigurationen**: Unternehmen möchten möglicherweise mehrere Instanzen erstellen, um Scankonfigurationen oder Klassifizierungen in isolierten Umgebungen zu testen. Es gibt zwar in einigen Bereichen der Plattform wie dem Glossar eine „Versionsverwaltung“, aber es wäre einfacher, eine „verwerfbare“ Instanz zum kostenlosen Testen zu haben.
2. **Trennen von Test, Vorproduktion und Produktion**: Organisationen möchten unterschiedliche Plattformen für verschiedene Arten von Daten erstellen, die in unterschiedlichen Umgebungen gespeichert werden. Davon wird abgeraten, da es sich bei diesen Arten von Daten um unterschiedliche Inhaltstypen handelt. Sie könnten den Glossarbegriff auf der obersten Hierarchieebene oder Kategorie verwenden, um Inhaltstypen zu trennen.
3. **Mischkonzerne und Verbundmodell**: Mischkonzerne verfügen häufig über viele Geschäftseinheiten (Business Units, BUs), die separat arbeiten, und in einigen Fällen teilen sie sich nicht einmal die Abrechnung untereinander. In diesen Fällen erstellt die Organisation für jede BU eine Purview-Instanz. Dieses Modell ist nicht ideal, kann jedoch notwendig sein, insbesondere, da BUs häufig nicht bereit sind, die Abrechnung untereinander zu teilen.
4. **Compliance**: Es gibt einige strenge Complianceregime, die auch Metadaten als vertraulich behandeln und fordern, dass sie sich in einer bestimmten Geografie befinden. Wenn ein Unternehmen über mehrere geografische Regionen verfügt, sind mehrere Purview-Instanzen, eine für jede Geografie, die einzige Lösung.

### <a name="create-a-process-to-move-to-production"></a>Erstellen eines Prozesses zum Verschieben in die Produktion

Einige Organisationen entscheiden sich möglicherweise, die Dinge einfach zu halten, indem sie mit einer einzigen Produktionsversion von Purview arbeiten. Sie können sich vermutlich auf Ermittlungs-, Such- und Durchsuchszenarios beschränken. Wenn einige Ressourcen falsche Glossarbegriffe aufweisen, ist es recht nachsichtig, die Korrektur den Benutzern selbst zu überlassen. Aber die meisten Organisationen, die Purview verschiedene Geschäftseinheiten übergreifend bereitstellen möchten, benötigen jedoch eine bestimmte Form von Prozess und Kontrolle.

Ein weiterer wichtiger Aspekt, den Sie in Ihren Produktionsprozess einbeziehen müssen, ist die Art, in der Klassifizierungen und Bezeichnungen migriert werden können. Purview hat mehr als 90 Systemklassifizierer. Sie können System- oder benutzerdefinierte Klassifizierungen auf eine Datei, Tabelle oder Spaltenressource anwenden. Klassifizierungen ähneln Betrefftags und werden zum Kennzeichnen und Identifizieren von Inhalten eines bestimmten Typs verwendet, die während der Überprüfung in Ihrer Datenumgebung gefunden werden. Vertraulichkeitsbezeichnungen werden verwendet, um die Klassifizierungstypkategorien in Ihren Organisationsdaten zu ermitteln und dann die Richtlinien zu gruppieren, die Sie auf die einzelnen Kategorien anwenden möchten. Es werden die gleichen Typen von vertraulichen Informationen wie in Microsoft 365 verwendet. Hiermit können Sie Ihre vorhandenen Sicherheitsrichtlinien und den Schutz auf Ihre gesamten Inhalte und Ihre gesamte Datenumgebung ausdehnen. Es können Dokumente automatisch per Scan überprüft und klassifiziert werden. Wenn Sie beispielsweise über eine Datei mit dem Namen „multiple.docx“ verfügen, die eine Personalausweisnummer für ein Land enthält, fügt Purview die Klassifizierung wie z. B. „EU-Personalausweisnummer“ der Seite „Ressourcendetail“ hinzu.

In Purview gibt es mehrere Bereiche, in denen die Katalogadministratoren die bewährten Methoden für Konsistenz und Wartung im Laufe des Lebenszyklus sicherstellen müssen:

* **Datenressourcen**: Datenquellen müssen in den einzelnen Umgebungen jeweils neu gescannt werden. Sie sollten nicht nur in der Entwicklungsumgebung gescannt und dann mithilfe von APIs in der Produktionsumgebung erneut generiert werden. Der Hauptgrund ist, dass die Purview-Scanner die Datenressourcen im Hintergrund viel mehr „verdrahten“, sodass es komplex werden könnte, sie in eine andere Purview-Instanz zu verschieben. Es ist viel einfacher, einfach dieselbe Datenquelle in der Produktionsumgebung hinzuzufügen und die Quellen erneut zu scannen. Es hat sich allgemein bewährt, alle verwendeten Scans, Verbindungen und Authentifizierungsmechanismen zu dokumentieren.
* **Überprüfungsregelsätze**: Dies ist Ihre Sammlung von Regeln, die bestimmten Scans zugewiesen werden, z. B. Dateityp und Klassifizierungen, die erkannt werden sollen. Wenn Sie nicht über zahlreiche Überprüfungsregelsätze verfügen, können Sie diese einfach manuell über die Produktion erneut erstellen. Dies erfordert einen internen Prozess und eine gute Dokumentation. Wenn Ihre Regelsätze jedoch täglich oder wöchentlich geändert werden, kann hierfür die REST-API-Route untersucht werden.
* **Benutzerdefinierte Klassifizierungen**: Ihre Klassifizierungen ändern sich möglicherweise nicht auch regelmäßig. Während der ersten Phase der Bereitstellung kann es einige Zeit dauern, verschiedene Anforderungen zu verstehen, die mit benutzerdefinierten Klassifizierungen aufkommen. Wenn sich dies jedoch eingespielt hat, sind nur geringe Änderungen erforderlich. Hier gilt die Empfehlung, benutzerdefinierte Klassifizierungen manuell zu migrieren oder die REST-API zu verwenden.
* **Glossar**: Es ist möglich, Glossarbegriffe über die Benutzeroberfläche zu exportieren und zu importieren. Für Automatisierungsszenarios können Sie auch die REST-API verwenden.
* **Richtlinien für Ressourcensatzmuster**: Diese Funktion anzuwenden, ist für typische Organisationen eine sehr fortgeschrittene Aktion. In einigen Fällen sind für Ihre Azure Data Lake Storage-Instanz Ordnerbenennungskonventionen und eine bestimmte Struktur festgelegt, die Purview möglicherweise beim Generieren des Ressourcensatzes Probleme bereiten. Ihre Geschäftseinheit möchte möglicherweise auch die Ressourcensatzerstellung mit zusätzlichen Anpassungen ändern, um die geschäftlichen Anforderungen zu erfüllen. In diesem Szenario ist es am besten, alle Änderungen über die Rest-API nachzuverfolgen und die Änderungen über die externe Versionsverwaltungsplattform zu dokumentieren.
* **Rollenzuweisung**: Hier steuern Sie, wer Zugriff auf Purview und welche Berechtigungen hat. Purview verfügt auch über eine REST-API, um den Export und Import von Benutzern und Rollen zu unterstützen, die jedoch nicht mit der Atlas-API kompatibel ist. Stattdessen sollte eine Azure-Sicherheitsgruppe zugewiesen und die Gruppenmitgliedschaft verwaltet werden.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Planen und Implementieren verschiedener Integrationspunkte mit Purview

Es ist wahrscheinlich, dass eine ausgereifte Organisation bereits über einen Datenkatalog verfügt. Die Hauptfrage ist, ob die vorhandene Technologie weiterhin verwendet und mit Purview synchronisiert werden soll. Purview ermöglicht das Veröffentlichen von Informationen über die Atlas-APIs, aber sie sind wirklich nicht für die Unterstützung eines derartigen Szenarios vorgesehen. Einige Organisationen entscheiden sich anfänglich, das Bootstrap der Verwendung von Purview durchzuführen, indem sie die vorhandenen Datenressourcen aus anderen Datenkataloglösungen migrieren. Dies kann in einem unidirektionalen Ansatz über die Atlas-APIs erfolgen. Das Synchronisieren zwischen verschiedenen Katalogtechnologien sollte im langfristigen Entwurf nicht berücksichtigt werden. In der Regel verwendet jede Geschäftseinheit möglicherweise weiterhin die vorhandenen Lösungen für ältere Datenressourcen, während Purview verwendet wird, um neuere Datenquellen zu scannen.

Für andere Integrationsszenarios wie Ticketing, benutzerdefinierte Benutzeroberfläche und Orchestrierung können Sie Atlas-APIs und Kafka-Endpunkte verwenden. Im Allgemeinen gibt es vier Integrationspunkte mit Purview:

* **Datenressource**: Dies ermöglicht Purview, die Ressourcen eines Speichers zu scannen, um zu ermitteln, was diese Ressourcen sind, und alle sofort verfügbaren Metadaten zu diesen Ressourcen zu sammeln. Für SQL könnte dies eine Liste von Datenbanken, Tabellen, gespeicherten Prozeduren, Sichten und Konfigurationsdaten darüber sein, die an Orten wie `sys.tables` aufbewahrt werden. Im Fall von Azure Data Factory (ADF) könnte dies dazu führen, dass alle Pipelines aufgezählt und Daten beim Erstellen, letzten Ausführen und im aktuellen Zustand abgerufen werden.
* **Datenherkunft**: Damit kann Purview Informationen zum Verschieben von Daten von einem Analyse-/Datenmutationssystem sammeln. Im Fall von Spark könnte dies das Sammeln von Informationen von der Ausführung eines Notebooks sein, um festzustellen, welche Daten das Notebook erfasst hat, wie sie transformiert wurden und wo es sie ausgegeben hat. Im Fall von SQL könnten Abfrageprotokolle analysiert werden, um zurückzuentwickeln, welche Mutationsvorgänge ausgeführt wurden, und was sie bewirkt haben. Abhängig von den Anforderungen unterstützen wir sowohl push- als auch pullbasierte Datenherkunft.
* **Klassifizierung**: Dies ermöglicht Purview, physische Beispiele aus Datenquellen zu nehmen und diese über das Klassifizierungssystem auszuführen. Das Klassifizierungssystem ermittelt die Semantik für ein Datenelement. Beispielsweise wissen wir möglicherweise, dass es sich bei einer Datei um eine Parquet-Datei handelt, die drei Spalten enthält, und dass die dritte eine Zeichenfolge ist. Aber die Klassifizierer, die wir für die Beispiele ausführen, teilen uns mit, dass die Zeichenfolge ein Name, eine Adresse oder eine Telefonnummer ist. Diesen Integrationspunkt aufzuhellen bedeutet, dass wir definiert haben, wie Purview Objekte wie Notebooks, Pipelines, Parquet-Dateien, Tabellen und Container öffnen kann.
* **Eingebettete Erfahrung**: Produkte mit „Studio“-ähnlicher Umgebung (z. B. ADF, Synapse, SQL Studio, PBI und Dynamics) möchten Benutzern in der Regel ermöglichen, Daten zu ermitteln, mit denen sie interagieren möchten, und auch Orte zur Ausgabe von Daten zu finden. Der Katalog von Purview kann dabei helfen, diese Erfahrungen zu beschleunigen, indem eine Einbettungserfahrung bereitgestellt wird. Diese Erfahrung kann nach Wahl des Partners auf API- oder Benutzeroberflächenebene auftreten. Durch das Einbetten eines Aufrufs von Purview kann die Organisation die Vorteile der Purview-Karte der Datenumgebung nutzen, um Datenressourcen zu finden, Datenherkunft anzuzeigen, Schemas zu überprüfen, Bewertungen und Kontakte zu betrachten usw.

## <a name="phase-1-pilot"></a>Phase 1: Pilotphase

In dieser Phase muss Purview für eine sehr kleine Gruppe von Benutzern erstellt und konfiguriert werden. Normalerweise ist es nur eine Gruppe von 2-3 Mitarbeitern, die zusammenarbeiten, um End-to-End-Szenarios auszuführen. Sie werden als die Verfechter von Purview in Ihrer Organisation betrachtet. Das Hauptziel dieser Phase besteht darin, sicherzustellen, dass wichtige Funktionalitäten erreicht werden können und die richtigen Projektbeteiligten das Projekt kennen.

### <a name="tasks-to-complete"></a>Auszuführende Aufgaben

|Aufgabe|Detail|Duration|
|---------|---------|---------|
|Erfassen und Akzeptieren von Anforderungen|Diskussion mit allen Projektbeteiligten, um einen vollständigen Satz von Anforderungen zu erfassen. Verschiedene Rollen müssen teilnehmen, um einer Teilmenge von Anforderungen zuzustimmen, damit die einzelnen Phasen des Projekts abgeschlossen werden können.|1 Woche|
|Einrichten des Starter Kits|Navigieren Sie durch [Purview-Schnellstart](create-catalog-portal.md), und richten Sie das [Purview Starter Kit](tutorial-scan-data.md) ein, um allen Projektbeteiligten eine Purview-Demo zu zeigen.|1 Tag|
|Navigieren in Purview|Erfahren Sie von der Startseite aus, wie Purview verwendet wird.|1 Tag|
|Konfigurieren von ADF für Datenherkunft|Identifizieren Sie wichtige Pipelines und Datenressourcen. Sammeln Sie alle Informationen, die für das Herstellen der Verbindung mit einem internen ADF-Konto erforderlich sind.|1 Tag|
|Überprüfen einer Datenquelle wie Azure Data Lake Storage|Fügen Sie die Datenquelle hinzu, und richten Sie einen Scan ein. Stellen Sie sicher, dass der Scan alle Ressourcen erfolgreich erkennt.|2 Tage|
|Suchen und Durchsuchen|Ermöglichen Sie Endbenutzern den Zugriff auf Purview und das Ausführen von End-to-End-Such- und Durchsuchszenarios.|1 Tag|

### <a name="acceptance-criteria"></a>Akzeptanzkriterien

* Das Purview-Konto wird erfolgreich im Organisationsabonnement unter dem Mandanten der Organisation erstellt.
* Eine kleine Gruppe von Benutzern mit mehreren Rollen kann auf Purview zugreifen.
* Purview ist so konfiguriert, dass mindestens eine Datenquelle gescannt wird.
* Benutzer sollten in der Lage sein, Schlüsselwerte von Purview zu extrahieren, z. B.:
  * Suchen und Durchsuchen
  * Herkunft
* Benutzer sollten in der Lage sein, auf der Ressourcenseite Ressourcenbesitz zuzuweisen.
* Präsentation und Demo, um das Bewusstsein bei wichtigen Projektbeteiligten zu wecken.
* Kooperation der Verwaltung, um zusätzliche Ressourcen für die MVP-Phase zu genehmigen.

## <a name="phase-2-minimum-viable-product"></a>Phase 2: Minimum Viable Product

Wenn Sie zur Durchführung des Purview-Onboardings Anforderungen vereinbart und Geschäftseinheiten beteiligt haben, arbeiten Sie im nächsten Schritt an einem MVP-Release (Minimum Viable Product). In dieser Phase erweitern Sie die Verwendung von Purview auf mehr Benutzer, die zusätzliche horizontale und vertikale Anforderungen haben. Es gibt wichtige Szenarios, die für alle Benutzer horizontal erfüllt werden müssen, z. B. Glossarbegriffe, Suchen und Durchsuchen. Es gibt auch detaillierte vertikale Anforderungen für jede Geschäftseinheit oder Gruppe, um bestimmte End-to-End-Szenarios abzudecken, wie z. B. Datenherkunft von Azure Data Lake Storage bis Azure Synapse DW und Power BI.

### <a name="tasks-to-complete"></a>Auszuführende Aufgaben

|Aufgabe|Detail|Duration|
|---------|---------|---------|
|[Registrieren und Scannen von Azure Synapse Analytics](register-scan-azure-synapse-analytics.md)|Beginn des Onboardings Ihrer Datenbankquellen und deren Scannen, um Schlüsselressourcen aufzufüllen|2 Tage|
|[Benutzerdefinierte Klassifizierungen in Azure Purview](create-a-custom-classification-and-classification-rule.md)|Nachdem Ihre Objekte gescannt wurden, bemerken die Benutzer möglicherweise, dass zusätzliche Anwendungsfälle für eine weitere Klassifizierung neben den Standardklassifizierungen vorliegt.|2-4 Wochen|
|[Registrieren und Scannen eines Power BI-Mandanten (Vorschau)](register-scan-power-bi-tenant.md)|Wenn Ihre Organisation Power BI verwendet, können Sie Power BI scannen, um alle Datenressourcen zu erfassen, die von wissenschaftlichen oder technischen Fachkräften für Daten verwendet werden, die Anforderungen zum Einschließen von Datenherkunft aus der Speicherebene stellen.|1-2 Wochen|
|[Importieren von Glossarbegriffen](how-to-create-import-export-glossary.md)|In den meisten Fällen wird Ihre Organisation möglicherweise bereits eine Sammlung von Glossarbegriffen und Zuweisungen von Begriffen zu Ressourcen entwickeln. Dies erfordert einen Import in Purview über eine CSV-Datei.|1 Woche|
|Hinzufügen von Kontakten zu Ressourcen|Für die wichtigsten Ressourcen sollten Sie einen Prozess einrichten, um entweder anderen Rollen zu erlauben, Kontakte zuzuweisen, oder Kontakte über REST-APIs zu importieren.|1 Woche|
|Hinzufügen von Vertraulichkeitsbezeichnungen und Scannen|Dies ist möglicherweise für einige Organisationen abhängig von der Verwendung der Bezeichnung aus Microsoft 365 optional.|1-2 Wochen|
|Abrufen von Klassifizierung und Vertraulichkeitserkenntnissen|Für Berichterstellung und Erkenntnisse in Purview können Sie auf diese Funktion zugreifen, um verschiedene Berichte zu erhalten und eine Präsentation für die Verwaltung bereitzustellen.|1 Tag|
|Integrieren des Hinzufügens von Benutzern mit von Purview verwalteten Benutzern|Dieser Schritt erfordert, dass der Purview-Administrator mit dem Azure Active Directory-Administrator zusammenarbeitet, um neue Sicherheitsgruppen einzurichten, um den Zugriff auf Purview zu gewähren.|1 Woche|

### <a name="acceptance-criteria"></a>Akzeptanzkriterien

* Erfolgreiches Integrieren einer größeren Gruppe von Benutzern in Purview (50 +)
* Scannen unternehmenskritischer Datenquellen
* Importieren und Zuweisen aller kritischen Glossarbegriffe
* Erfolgreiches Testen wichtiger Bezeichnungen für Schlüsselressourcen
* Erfolgreiches Erfüllen minimaler Szenarios für die Benutzer der beteiligten Geschäftseinheiten

## <a name="phase-3-pre-production"></a>Phase 3: Vor der Produktion

Nach Verstreichen der MVP-Phase ist es an der Zeit, den Meilenstein für die Vorproduktion zu planen. Ihre Organisation entscheidet sich möglicherweise für eine separate Instanz von Purview für die Vorproduktion und Produktion, oder für das Beibehalten der gleichen Instanz, aber Einschränkung des Zugriffs. In diese Phase können Sie auch die Überprüfung lokaler Datenquellen wie SQL Server einschließen. Wenn es bei den Datenquellen eine von Purview nicht unterstützte Lücke gibt, ist es an der Zeit, die Atlas-API zu untersuchen, um weitere Optionen zu verstehen.

### <a name="tasks-to-complete"></a>Auszuführende Aufgaben

|Aufgabe|Detail|Duration|
|---------|---------|---------|
|Optimieren Ihrer Überprüfung mithilfe des Überprüfungsregelsatzes|Ihre Organisation verfügt über viele Datenquellen für die Vorproduktion. Es ist wichtig, die Schlüsselkriterien für die Überprüfung vorab zu definieren, damit Klassifizierungen und Dateierweiterungen konsistent auf das Board angewendet werden können.|1-2 Tage|
|Bewerten der Verfügbarkeit der Region für die Überprüfung|Abhängig von der Region der Datenquellen und den organisatorischen Anforderungen hinsichtlich Compliance und Sicherheit sollten Sie überlegen, welche Regionen für die Überprüfung verfügbar sein müssen.|1 Tag|
|Grundlegendes zum Firewallkonzept beim Scannen|In diesem Schritt müssen Sie untersuchen, wie die Organisation die Firewall konfiguriert, und wie sich Purview authentifizieren kann, um zum Scannen auf die Datenquellen zuzugreifen.|1 Tag|
|Grundlegendes zum Private Link-Konzept beim Scannen|Wenn Ihre Organisation Private Link verwendet, müssen Sie das Fundament der Netzwerksicherheit planen, um Private Link in die Anforderungen einzubeziehen.|1 Tag|
|[Registrieren und Scannen einer lokalen SQL Server-Instanz](register-scan-on-premises-sql-server.md)|Dies ist optional, wenn Sie über eine lokale SQL Server-Instanz verfügen. Für die Überprüfung muss eine [selbstgehostete Integration Runtime](manage-integration-runtimes.md) eingerichtet und SQL Server als Datenquelle hinzugefügt werden.|1-2 Wochen|
|Verwenden der Purview-REST-API für Integrationsszenarios|Bei Anforderungen, Purview in andere Technologien von Drittanbietern wie Orchestrierungs- oder Ticketingsysteme zu integrieren, können Sie den Bereich der Rest-API erkunden.|1-4 Wochen|
|Grundlegendes zu Purview-Preisen|In diesem Schritt erhält die Organisation wichtige Finanzinformationen, um Entscheidungen treffen zu können.|1-5 Tage|

### <a name="acceptance-criteria"></a>Akzeptanzkriterien

* Erfolgreiches Integrieren von mindestens einer Geschäftseinheit mit allen Benutzern
* Überprüfen einer lokalen Datenquelle wie SQL Server
* Proof of Concept mindestens eines Integrationsszenarios mit REST-API
* Absolvieren eines Plans, in die Produktion zu gehen, der wichtige Infrastruktur- und Sicherheitsbereiche beinhalten sollte

## <a name="phase-4-production"></a>Phase 4: Bereitstellung

Die obigen Phasen sollten befolgt werden, um eine effektive Governance von Informationen zu erstellen. Dies ist die Grundlage für bessere Governanceprogramme. Datengovernance unterstützt Ihre Organisation beim Vorbereiten der wachsenden Trends wie KI, Hadoop, IoT und Blockchain. Es ist nur der Anfang vieler Daten- und Analyseaspekte, und es gibt noch viele weitere Möglichkeiten. Das Ergebnis dieser Lösung wäre Folgendes:

* **Geschäftlich fokussiert**: Eine Lösung, die an Geschäftsanforderungen und Szenarios statt technischer Anforderungen ausgerichtet ist.
* **Bereit für die Zukunft**: Eine Lösung maximiert Standardfeatures der Plattform und verwendet standardisierte Branchenpraktiken für Konfigurations- oder Skriptaktivitäten, um die Fortschritte/Weiterentwicklung der Plattform zu unterstützen.

### <a name="tasks-to-complete"></a>Auszuführende Aufgaben

|Aufgabe|Detail|Duration|
|---------|---------|---------|
|Überprüfen von Produktionsdatenquellen mit aktivierter Firewall|Wenn dies optional ist, wenn eine Firewall vorhanden ist, aber Sie sollten unbedingt Optionen zum Härten Ihrer Infrastruktur untersuchen.|1-5 Tage|
|Aktivieren von Private Link|Wenn dies optional ist, wenn Private Link verwendet wird. Andernfalls können Sie dies überspringen, da es nur unverzichtbar ist, wenn Private Link aktiviert ist.|1-5 Tage|
|Erstellen eines automatisierten Workflows|Der Workflow ist wichtig, um Prozesse wie Genehmigung, Eskalation, Review und Problemverwaltung zu automatisieren.|2-3 Wochen|
|Dokumentation des Vorgangs|Datengovernance ist kein einmalige Projekt. Sie ist ein kontinuierliches Programm, das die datengesteuerte Entscheidungsfindung und das Schaffen von Verkaufschancen für Unternehmen nährt. Es ist wichtig, Schlüsselprozeduren und Geschäftsstandards zu dokumentieren.|1 Woche|

### <a name="acceptance-criteria"></a>Akzeptanzkriterien

* Erfolgreiche Integration aller Geschäftseinheiten und ihrer Benutzer
* Erfolgreiche Erfüllung der Infrastruktur- und Sicherheitsanforderungen für die Produktion
* Erfolgreiche Erfüllung aller von den Benutzern benötigter Anwendungsfälle

## <a name="platform-hardening"></a>Plattformhärtung

Weitere Maßnahmen zur Härtung können ausgeführt werden:

* Erhöhen des Sicherheitsstatus durch Aktivieren der Überprüfung von Firewallressourcen oder Verwenden von Private Link
* Optimieren der Bereichsüberprüfung, um die Scanleistung zu verbessern
* Verwenden von REST-APIs zum Exportieren kritischer Metadaten und Eigenschaften für die Sicherung und Wiederherstellung
* Verwenden von Workflows zum Automatisieren von Ticketing und Ereignissen zur Vermeidung menschlicher Fehler

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Scannen von Daten mit Azure Purview (Vorschau)](tutorial-scan-data.md)
- [Tutorial: Navigieren zur Azure Purview-Startseite (Vorschau) und Suchen nach einer Ressource](tutorial-asset-search.md)