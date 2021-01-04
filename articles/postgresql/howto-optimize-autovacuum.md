---
title: Optimieren von Autovacuum – Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel wird beschrieben, wie Sie Autovacuum auf einem Azure Database for PostgreSQL-Einzelserver optimieren können.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012560"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optimieren von Autovacuum auf einem Azure Database for PostgreSQL-Einzelserver

In diesem Artikel wird beschrieben, wie Sie Autovacuum auf einem Azure Database for PostgreSQL-Server effizient optimieren können.

## <a name="overview-of-autovacuum"></a>Übersicht über Autovacuum

PostgreSQL verwendet für eine bessere Datenbankparallelität MVCC (Multiversion Concurrency Control). Jede Aktualisierung führt zu einer Einfügung und einer Löschung, und jede Löschung führt dazu, dass die entsprechenden Zeilen vorläufig zum Löschen markiert werden. Die vorläufige Markierung sorgt dafür, dass inaktive Tupel identifiziert und später endgültig gelöscht werden. Um diese Aufgaben auszuführen, führt PostgreSQL einen Vacuum-Auftrag aus.

Ein Vacuum-Auftrag kann manuell oder automatisch ausgelöst werden. Wenn in der Datenbank viele Aktualisierungs- oder Löschvorgänge stattfinden, sind viele inaktive Tupel vorhanden. Wenn sich die Datenbank im Leerlauf befindet, sind weniger inaktive Tupel vorhanden. Bei einer starken Auslastung der Datenbank müssen häufiger Vacuum-Aufträge ausgeführt werden. Das *manuelle* Ausführen von Vacuum-Aufträgen ist daher unpraktisch.

Autovacuum kann so konfiguriert werden und profitiert von einer Optimierung. Die Standardwerte, mit denen PostgreSQL ausgeliefert wird, sollen sicherstellen, dass das Produkt auf allen Arten von Geräten ausgeführt werden kann. Dies schließt Raspberry Pi-Geräte ein. Die idealen Konfigurationswerte sind von verschiedenen Faktoren abhängig:

- Gesamtanzahl der Ressourcen, z.B. SKU und Speichergröße
- Ressourcennutzung
- Merkmale der einzelnen Objekte

## <a name="autovacuum-benefits"></a>Vorteile von Autovacuum

Wenn Sie Vacuum nicht hin und wieder ausführen, können sich inaktive Tupel ansammeln und zu Problemen führen:

- Datenüberfrachtung – größere Datenbanken und Tabellen
- Größere suboptimale Indizes
- Steigende E/A

## <a name="monitor-bloat-with-autovacuum-queries"></a>Überwachen der Überfrachtung mit Autovacuum-Abfragen

Die folgende Beispielabfrage ist dazu konzipiert, die Anzahl inaktiver und aktiver Tupel in einer Tabelle mit dem Namen „XYZ“ zu identifizieren:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Autovacuum-Konfigurationen

Die Konfigurationsparameter zur Steuerung von Autovacuum werden durch zwei zentrale Fragen beeinflusst:

- Wann sollte Autovacuum starten?
- Wie viel sollte nach dem Start bereinigt werden?

Nachfolgend finden Sie einige Autovacuum-Konfigurationsparameter, die Sie basierend auf den vorgenannten Fragen aktualisieren können, sowie zugehörige Hinweise.

Parameter|BESCHREIBUNG|Standardwert
---|---|---
autovacuum_vacuum_threshold|Gibt die minimale Anzahl von aktualisierten oder gelöschten Tupeln vor dem Auslösen eines Vacuum-Vorgangs in einer Tabelle an. Der Standardwert ist 50 Tupel. Legen Sie diesen Parameter nur in der Datei „postgresql.conf“ oder über die Serverbefehlszeile fest. Um die Einstellung für einzelne Tabellen außer Kraft zu setzen, ändern Sie die Tabellenspeicherparameter.|50
autovacuum_vacuum_scale_factor|Gibt einen Anteil der Tabellengröße an, der „autovacuum_vacuum_threshold“ zur Entscheidung über die Auslösung eines Vacuum-Vorgangs hinzugefügt wird. Der Standardwert ist 0,2 (20% der Tabellengröße). Legen Sie diesen Parameter nur in der Datei „postgresql.conf“ oder über die Serverbefehlszeile fest. Um die Einstellung für einzelne Tabellen außer Kraft zu setzen, ändern Sie die Tabellenspeicherparameter.|0.2
autovacuum_vacuum_cost_limit|Gibt den Kostengrenzwert an, der bei automatischen Vacuum-Vorgängen verwendet wird. Wenn –1 angegeben ist (der Standard), wird der normale vacuum_cost_limit-Wert verwendet. Falls mehr als ein Worker vorhanden ist, wird der Wert zwischen den ausgeführten Autovacuum-Workern proportional aufgeteilt. Die Summe der Grenzwerte für die einzelnen Worker überschreitet nicht den Wert dieser Variable. Legen Sie diesen Parameter nur in der Datei „postgresql.conf“ oder über die Serverbefehlszeile fest. Um die Einstellung für einzelne Tabellen außer Kraft zu setzen, ändern Sie die Tabellenspeicherparameter.|-1
autovacuum_vacuum_cost_delay|Gibt den Kostenverzögerungswert an, der bei automatischen Vacuum-Vorgängen verwendet wird. Wenn –1 angegeben ist, wird der normale vacuum_cost_delay-Wert verwendet. Der Standardwert sind 20 Millisekunden. Legen Sie diesen Parameter nur in der Datei „postgresql.conf“ oder über die Serverbefehlszeile fest. Um die Einstellung für einzelne Tabellen außer Kraft zu setzen, ändern Sie die Tabellenspeicherparameter.|20 ms
autovacuum_naptime | Gibt die minimale Verzögerung zwischen Autovacuum-Ausführungen in einer bestimmten Datenbank an. In jeder Runde untersucht der Daemon die Datenbank und gibt nach Bedarf VACUUM- und ANALYZE-Befehle für Tabellen in dieser Datenbank aus. Die Verzögerung wird in Sekunden gemessen. Legen Sie diesen Parameter nur in der Datei „postgresql.conf“ oder über die Serverbefehlszeile fest.| 15 s
autovacuum_max_workers | Gibt die maximale Anzahl von Autovacuum-Prozessen (mit Ausnahme des Autovacuum-Startprogramms) an, die parallel ausgeführt werden können. Der Standardwert ist 3. Legen Sie diesen Parameter nur bei einem Serverstart fest.|3

Um die Einstellungen für einzelne Tabellen außer Kraft zu setzen, ändern Sie die Tabellenspeicherparameter.

## <a name="autovacuum-cost"></a>Autovacuum-Kosten

Nachfolgend sind die „Kosten“ für das Ausführen eines Vakuum-Vorgangs angegeben:

- Die Datenseiten, für die Vacuum ausgeführt wird, werden gesperrt.
- Beim Ausführen von Vacuum werden Compute- und Speicherressourcen verwendet.

Deshalb sollte Vacuum weder zu häufig noch zu selten ausgeführt werden. Die Vacuum-Ausführung muss sich an die Workload anpassen. Aufgrund der jeweiligen Vor- und Nachteile wird empfohlen, alle Autovacuum-Parameteränderungen zu überprüfen.

## <a name="autovacuum-start-trigger"></a>Autovacuum-Startauslöser

Autovacuum wird ausgelöst, wenn die Anzahl der inaktiven Tupel „autovacuum_vacuum_threshold“ + „autovacuum_vacuum_scale_factor“ * „reltuples“ überschreitet. Hierbei steht „reltuples“ für eine Konstante.

Die Bereinigung durch Autovacuum muss die Datenbankauslastung berücksichtigen. Andernfalls kann es vorkommen, dass nicht mehr genügend Speicher vorhanden ist und es bei Abfragen zu einer allgemeinen Verlangsamung kommt. Über die Zeit betrachtet sollte die Rate, mit der Tupel durch Vacuum gelöscht werden, mit der Rate übereinstimmen, mit der inaktive Tupel erstellt werden.

Datenbanken mit vielen Aktualisierungs- und Löschvorgängen weisen mehr inaktive Tupel auf und benötigen mehr Speicherplatz. Im Allgemeinen bieten niedrige Werte für „autovacuum_vacuum_scale_factor“ und „autovacuum_vacuum_threshold“ Vorteile für Datenbanken mit vielen Aktualisierungs- und Löschvorgängen. Die niedrigen Werte verhindern eine längere Ansammlung von inaktiven Tupeln. Sie können bei kleineren Datenbanken für beide Parameter höhere Werte verwenden, da das Ausführen von Vacuum weniger dringend ist. Ein häufiges Ausführen von Vacuum belegt Compute- und Speicherressourcen.

Der Standardskalierungsfaktor von 20 Prozent funktioniert gut für Tabellen mit einem niedrigen Anteil an inaktiven Tupeln. Bei Tabellen mit einem hohen Anteil an inaktiven Tupeln ist dieser Wert weniger gut geeignet. Beispielsweise entspricht dieser Skalierungsfaktor bei einer 20-GB-Tabelle einem Wert von 4 GB für inaktive Tupel. Bei einer 1-TB-Tabelle entspricht dies einem Wert von 200 GB für inaktive Tupel.

Mit PostgreSQL können Sie diese Parameter auf Tabellenebene oder Instanzebene festlegen. In Azure Database for PostgreSQL können diese Parameter derzeit nur auf Tabellenebene festgelegt werden.

## <a name="estimate-the-cost-of-autovacuum"></a>Schätzen der Kosten für Autovacuum

Das Ausführen von Autovacuum ist „kostenaufwendig“, und die Laufzeit von Vacuum-Vorgängen kann mit Parametern gesteuert werden. Die folgenden Parameter helfen dabei, die Kosten für die Ausführung von Vacuum zu schätzen:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Der Vacuum-Prozess liest physische Seiten und sucht nach inaktiven Tupeln. Für jede Seite in „shared_buffers“ wird vom Kostenwert 1 (vacuum_cost_page_hit) ausgegangen. Für alle anderen Seiten wird bei vorhandenen inaktiven Tupeln ein Kostenwert von 20 zugrunde gelegt (vacuum_cost_page_dirty). Wenn keine inaktiven Tupel vorhanden sind, wird der Kostenwert 10 angesetzt (vacuum_cost_page_miss). Der Vacuum-Vorgang wird beendet, wenn der Prozess „autovacuum_vacuum_cost_limit“ überschreitet.

Nachdem der Grenzwert erreicht wird, verbringt der Prozess die durch den Parameter „autovacuum_vacuum_cost_delay“ angegebene Zeit im Ruhezustand, bevor er erneut gestartet wird. Wenn der Grenzwert nicht erreicht wird, wird Autovacuum nach der durch den Parameter autovacuum_nap_time angegebenen Zeit wieder gestartet.

Zusammengefasst ausgedrückt: Die Parameter „autovacuum_vacuum_cost_delay“ und „autovacuum_vacuum_cost_limit“ steuern, wie viel Datenbereinigung pro Zeiteinheit zulässig ist. Beachten Sie, dass die Standardwerte für die meisten Tarife zu niedrig sind. Die optimalen Werte für diese Parameter sind vom Tarif abhängig und sollten entsprechend konfiguriert werden.

Der Parameter „autovacuum_max_workers“ legt die maximale Anzahl von Autovacuum-Prozessen fest, die gleichzeitig ausgeführt werden können.

Mit PostgreSQL können Sie diese Parameter auf Tabellenebene oder Instanzebene festlegen. In Azure Database for PostgreSQL können diese Parameter derzeit nur auf Tabellenebene festgelegt werden.

## <a name="optimize-autovacuum-per-table"></a>Optimieren von Autovacuum pro Tabelle

Alle oben genannten Konfigurationsparameter können pro Tabelle konfiguriert werden. Hier sehen Sie ein Beispiel:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovacuum ist ein synchroner Prozess pro Tabelle. Je größer der Anteil an inaktiven Tupeln in einer Tabelle ist, desto höher sind die „Kosten“ für Autovacuum. Sie können Tabellen mit einer hohen Rate an Aktualisierungs- und Löschvorgängen in mehrere Tabellen aufteilen. Das Aufteilen von Tabellen trägt dazu bei, Autovacuum-Vorgänge parallel auszuführen und die „Kosten“ zum Ausführen von Autovacuum-Vorgängen für eine Tabelle zu verringern. Sie können auch die Anzahl der parallelen Autovacuum-Worker erhöhen, um sicherzustellen, dass Worker großzügig geplant werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung und Optimierung von Autovacuum finden Sie in der folgenden PostgreSQL-Dokumentation:

- [Kapitel 18, Serverkonfiguration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Kapitel 24, Routineaufgaben bei der Datenbankwartung](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
