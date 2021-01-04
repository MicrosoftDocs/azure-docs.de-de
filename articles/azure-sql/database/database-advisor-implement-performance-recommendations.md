---
title: 'Database Advisor: Empfehlungen zur Leistung für Azure SQL-Datenbank'
description: Von Azure SQL-Datenbank werden Empfehlungen für Datenbanken bereitgestellt, mit denen die Abfrageleistung für Azure SQL-Datenbank verbessert werden kann.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 79ccf0f8aae7e915601081f875cea294de52d787
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500851"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Database Advisor: Empfehlungen zur Leistung für Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL-Datenbank lernt und passt sich an Ihre Anwendung an. Azure SQL-Datenbank verfügt über einige Database Advisors (Datenbankratgeber), von denen angepasste Empfehlungen zur Steigerung der Leistung bereitgestellt werden. Diese Database Advisors führen eine ständige Bewertung und Analyse des Nutzungsverlaufs durch und stellen Empfehlungen basierend auf den Workloadmustern bereit, mit denen die Leistung verbessert werden kann.

## <a name="performance-overview"></a>Leistungsübersicht

Die Leistungsübersicht enthält eine Zusammenfassung zur Leistung der Datenbank und unterstützt Sie bei der Leistungsoptimierung und Fehlerbehebung.

![Leistungsübersicht für Azure SQL-Datenbank](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- Die Kachel **Empfehlungen** bietet eine Aufschlüsselung der Optimierungsempfehlungen für Ihre Datenbank (bei mehreren Empfehlungen werden die wichtigsten drei angezeigt). Durch Klicken auf diese Kachel gelangen Sie zu den **[Optionen für Leistungsempfehlungen](database-advisor-find-recommendations-portal.md#viewing-recommendations)** .
- Die Kachel **Optimierungsaktivität** bietet eine Zusammenfassung der laufenden und abgeschlossenen Optimierungsaktionen für die Datenbank, sodass Sie einen schnellen Überblick über den Verlauf der Optimierungsaktivitäten erhalten. Wenn Sie auf diese Kachel klicken, gelangen Sie zur Ansicht des vollständigen Optimierungsverlaufs für Ihre Datenbank.
- Die Kachel **Automatische Optimierung** zeigt die **[Konfiguration der automatischen Optimierung](automatic-tuning-enable.md)** für Ihre Datenbank an (die Optimierungsaktionen, die automatisch auf die Datenbank angewendet werden). Durch Klicken auf diese Kachel öffnen Sie das Dialogfeld zur Automatisierungskonfiguration.
- Die Kachel **Datenbankabfragen** zeigt die Zusammenfassung der Abfrageleistung für Ihre Datenbank (DTU-Nutzung insgesamt und die Abfragen mit dem größten Ressourcenverbrauch). Durch Klicken auf diese Kachel gelangen Sie zur **[Statistik zur Abfrageleistung](query-performance-insight-use.md)** .

## <a name="performance-recommendation-options"></a>Optionen für Leistungsempfehlungen

Folgende Optionen für Leistungsempfehlungen sind in Azure SQL-Datenbank verfügbar:

| Leistungsempfehlung | Unterstützung für Einzel- und Pooldatenbanken | Unterstützung der Instanzdatenbank |
| :----------------------------- | ----- | ----- |
| **Empfehlungen zum Erstellen eines Index**: Empfiehlt das Erstellen von Indizes, durch die sich die Leistung Ihrer Workload verbessern kann. | Ja | Nein |
| **Empfehlungen zum Löschen eines Index**: Empfiehlt das tägliche Entfernen redundanter und doppelter Indizes (mit Ausnahme von eindeutigen Indizes) sowie Indizes, die über einen langen Zeitraum hinweg (über 90 Tage) nicht verwendet wurden. Beachten Sie, dass diese Option nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. Das Löschen nicht verwendeter Indizes wird bei den Dienstebenen „Premium“ und „Unternehmenskritisch“ nicht unterstützt. | Ja | Nein |
| **Empfehlungen zum Parametrisieren von Abfragen (Vorschau)** : Empfiehlt die erzwungene Parametrisierung in Fällen, in denen eine oder mehrere Abfragen ständig neu kompiliert werden, aber am Ende immer der gleiche Abfrageausführungsplan daraus hervorgeht. | Ja | Nein |
| **Empfehlungen zum Beheben von Schemaproblemen (Vorschau)** : Empfehlungen für die Schemakorrektur werden angezeigt, wenn Azure SQL-Datenbank eine Anomalie in der Anzahl von schemabezogenen SQL-Fehlern erkennt, die in Ihrer Datenbank auftreten. Empfehlungen zum Beheben von Schemaproblemen werden in Kürze von Microsoft eingestellt. | Ja | Nein |

![Leistungsempfehlungen für Azure SQL-Datenbank](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

Informationen zum Anwenden der Leistungsempfehlungen finden Sie unter [Anwenden von Empfehlungen](database-advisor-find-recommendations-portal.md#applying-recommendations). Informationen zum Anzeigen des Status von Empfehlungen finden Sie unter [Überwachen von Vorgängen](database-advisor-find-recommendations-portal.md#monitoring-operations).

Sie können auch den vollständigen Verlauf der Aktionen zur Leistungsoptimierung suchen, die in der Vergangenheit angewendet wurden.

## <a name="create-index-recommendations"></a>Empfehlungen zum Erstellen eines Index

Azure SQL-Datenbank überwacht kontinuierlich die ausgeführten Abfragen und erkennt die Indizes, die die Leistung verbessern könnten. Sobald genügend Vertrauen besteht, dass ein bestimmter Index fehlt, wird eine neue Empfehlung **Index erstellen** erstellt.

Azure SQL-Datenbank baut Vertrauen auf, indem berechnet wird, zu welchem Leistungsgewinn der Index mit der Zeit führen würde. Je nach geschätztem Leistungsgewinn werden Empfehlungen als „Hoch“, „Mittel“ oder „Niedrig“ eingestuft.

Indizes, die mit Empfehlungen erstellt wurden, sind immer als automatisch erstellte Indizes gekennzeichnet. Sie können in der [Ansicht „sys.indexes“](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql) anzeigen, welche Indizes automatisch erstellt wurden. Automatisch erstellte Indizes blockieren keine ALTER/RENAME-Befehle.

Wenn Sie versuchen, die Spalte mit einem automatisch erstellten Index zu löschen, wird der Befehl übergeben. Der automatisch erstellte Index wird ebenfalls mit dem Befehl gelöscht. Reguläre Indizes blockieren den Befehl ALTER/RENAME für Spalten, die indiziert sind.

Nachdem die Empfehlung zum Erstellen eines Indexes angewendet wurde, vergleicht Azure SQL-Datenbank die Leistung der Abfragen mit der Baselineleistung. Wenn der neue Index die Leistung verbessert, wird die Empfehlung als erfolgreich gekennzeichnet, und der Auswirkungsbericht steht zur Verfügung. Wenn der Index nicht zu einer Leistungsverbesserung geführt hat, wird er automatisch rückgängig gemacht. Azure SQL-Datenbank verwendet diesen Prozess, um sicherzustellen, dass Empfehlungen die Leistung der Datenbank verbessern.

Jede Empfehlung **Index erstellen** verfügt über eine Backoffrichtlinie, die verhindert, dass die Empfehlung bei einem hohen Ressourcenverbrauch einer Datenbank oder eines Pools angewendet wird. In der Backoffrichtlinie werden CPU, Daten-E/A, Protokoll-E/A und verfügbarer Speicher berücksichtigt.

Wenn CPU, Daten-E/A oder Protokoll-E/A während der letzten 30 Minuten über 80 % gelegen haben, wird die Empfehlung zum Erstellen eines Indexes aufgeschoben. Wenn der verfügbare Speicher nach der Indexerstellung unter 10 % liegt, geht die Empfehlung in den Status „Fehler“ über. Falls nach einigen Tagen die automatische Optimierung weiterhin davon ausgeht, dass dieser Index von Vorteil wäre, wird der Prozess erneut gestartet.

Dieser Prozess wird wiederholt, bis genügend freier Speicherplatz zum Erstellen eines Indexes verfügbar ist oder der Index nicht mehr als vorteilhaft betrachtet wird.

## <a name="drop-index-recommendations"></a>Empfehlungen zum Löschen eines Indexes

Zusätzlich zum Erkennen fehlender Indizes analysiert Azure SQL-Datenbank kontinuierlich die Leistung der vorhandenen Indizes. Wenn der Index nicht verwendet wird, empfiehlt die Azure SQL-Datenbank seine Löschung. Das Löschen eines Indexes wird in zwei Fällen empfohlen:

- Der Index ist ein Duplikat eines anderes Indexes (gleiche indizierte und enthaltene Spalte, gleiches Partitionsschema und gleiche Filter).
- Der Index wurde für einen längeren Zeitraum (93 Tage) nicht verwendet.

„Index löschen“-Empfehlungen durchlaufen auch die Überprüfung nach der Implementierung. Wenn die Leistung verbessert wird, ist ein Auswirkungsbericht verfügbar. Wenn die Leistung beeinträchtigt wird, wird die Empfehlung zurückgesetzt.

## <a name="parameterize-queries-recommendations-preview"></a>Empfehlungen zum Parametrisieren von Abfragen (Vorschau)

Empfehlungen zum *Parametrisieren von Abfragen* werden angezeigt, wenn eine oder mehrere Abfragen ständig neu kompiliert werden, aber am Ende immer derselbe Abfrageausführungsplan daraus hervorgeht. Diese Bedingung eröffnet die Möglichkeit, erzwungene Parametrisierung anzuwenden. Durch die erzwungene Parametrisierung können Abfragepläne in Zukunft zwischengespeichert und wiederverwendet werden. Dies verbessert die Leistung und verringert den Ressourcenverbrauch.

Jede Abfrage muss zunächst kompiliert werden, um einen Ausführungsplan zu erstellen. Jeder generierte Plan wird dem Plancache hinzugefügt. Nachfolgende Ausführungen derselben Abfrage können dieser Plan aus dem Cache wiederverwenden. Auf diese Weise wird eine erneute Kompilierung überflüssig.

Abfragen mit nicht parametrisierten Werten können zu Mehrkosten bei der Leistung führen, da der Ausführungsplan jedes Mal neu kompiliert wird, wenn die nicht parametrisierten Werte unterschiedlich sind. In vielen Fällen werden für die gleichen Abfragen mit unterschiedlichen Parameterwerten die gleichen Ausführungspläne generiert. Diese Pläne werden dem Plancache jedoch separat hinzugefügt.

Der Vorgang der Neukompilierung von Ausführungsplänen verbraucht Datenbankressourcen, erhöht die Abfragedauer und führt zu einem Überlauf des Plancaches. Diese Ereignisse bewirken ihrerseits, dass Pläne aus dem Cache entfernt werden. Sie können das entsprechende Verhalten ändern, indem Sie für die Datenbank die Option für erzwungene Parametrisierung festlegen.

Damit Sie die Auswirkungen dieser Empfehlung besser abschätzen können, wird ein Vergleich zwischen der tatsächlichen CPU-Auslastung und der prognostizierten CPU-Auslastung nach Anwendung der Empfehlung bereitgestellt. Diese Empfehlung kann Sie dabei unterstützen, CPU-Einsparungen zu erzielen. Sie kann Ihnen auch helfen, die Abfragedauer und den Mehraufwand für den Plancache zu verringern. Dies bedeutet, dass eine größere Anzahl der Pläne im Cache verbleiben und wiederverwendet werden kann. Sie können diese Empfehlung schnell umsetzen, indem Sie den Befehl **Anwenden** auswählen.

Nachdem Sie diese Empfehlung angewendet haben, wird innerhalb von Minuten erzwungenen Parametrisierung für Ihre Datenbank aktiviert. Sie startet den Überwachungsvorgang, der ungefähr 24 Stunden andauert. Nach Ablauf dieses Zeitraums können Sie den Überprüfungsbericht anzeigen. Dieser Bericht zeigt die CPU-Auslastung Ihrer Datenbank 24 Stunden bevor und nachdem die Empfehlung angewendet wurde. Der Azure SQL Database Advisor verfügt über einen Sicherheitsmechanismus, der die angewendete Empfehlung automatisch wieder zurücknimmt, falls ein Absinken der Leistung erkannt wird.

## <a name="fix-schema-issues-recommendations-preview"></a>Empfehlungen zum Beheben von Schemaproblemen (Vorschau)

> [!IMPORTANT]
> Empfehlungen zum Beheben von Schemaproblemen werden in Kürze von Microsoft eingestellt. Wir empfehlen die Verwendung von [Intelligent Insights](intelligent-insights-overview.md) zum Überwachen Ihrer Datenbankleistungsprobleme, einschließlich der Schemaprobleme, die zuvor durch die Empfehlungen zum Beheben von Schemaproblemen abgedeckt wurden.

Empfehlungen zum **Beheben von Schemaproblemen** werden angezeigt, wenn Azure SQL-Datenbank eine Anomalie bei der Anzahl von schemabezogenen SQL-Fehlern erkennt, die in Ihrer Datenbank auftreten. Diese Empfehlung wird in der Regel angezeigt, wenn in Ihrer Datenbank innerhalb einer Stunde mehrere schemabezogene Fehler auftreten (ungültiger Spaltenname, ungültiger Objektname usw.).

„Schemaprobleme“ sind eine Klasse von Syntaxfehlern. Sie treten auf, wenn die Definition der SQL-Abfrage und die Definition des Datenbankschemas nicht übereinstimmen. Dies kann z.B. der Fall sein, wenn eine der von der Abfrage erwarteten Spalten in der Zieltabelle fehlt oder umgekehrt.

Die Empfehlung zum „Beheben von Schemaproblemen“ wird angezeigt, wenn Azure SQL-Datenbank eine Anomalie bei der Anzahl von schemabezogenen SQL-Fehlern erkennt, die in Ihrer Datenbank auftreten. Die folgende Tabelle zeigt die möglichen Fehler in Bezug auf Schemaprobleme:

| SQL-Fehlercode | `Message` |
| --- | --- |
| 201 |Die' *'-Prozedur oder -Funktion erwartet den '* '-Parameter, der nicht bereitgestellt wurde. |
| 207 |Ungültiger Spaltenname '*'. |
| 208 |Ungültiger Objektname '*'. |
| 213 |Der Spaltenname oder die Anzahl der bereitgestellten Werte entspricht nicht der Tabellendefinition. |
| 2812 |Die gespeicherte Prozedur '*' wurde nicht gefunden. |
| 8144 |Für die '*'-Prozedur oder -Funktion wurden zu viele Argumente angegeben. |

## <a name="custom-applications"></a>Benutzerdefinierte Anwendungen

Entwickler sollten in Betracht ziehen, benutzerdefinierte Anwendungen unter Verwendung von Leistungsempfehlungen für Azure SQL-Datenbank zu entwickeln. Auf alle im Portal aufgeführten Empfehlungen für eine Datenbank kann über die [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)-API zugegriffen werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur automatischen Optimierung von Datenbankindizes und Abfrageausführungsplänen finden Sie unter [Automatische Optimierung der Azure SQL-Datenbank](automatic-tuning-overview.md).
- Weitere Informationen zum automatischen Überwachen der Datenbankleistung mit der automatisierten Diagnose und der Ursachenanalyse von Leistungsproblemen finden Sie unter [Azure SQL Intelligent Insights](intelligent-insights-overview.md).
- Unter [Query Performance Insight](query-performance-insight-use.md) erfahren Sie, wie Sie die Auswirkungen Ihrer wichtigsten Abfragen auf die Leistung anzeigen und untersuchen können.