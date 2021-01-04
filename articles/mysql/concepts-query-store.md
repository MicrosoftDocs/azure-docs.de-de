---
title: Abfragespeicher – Azure Database for MySQL
description: Erfahren Sie mehr über das Feature „Abfragespeicher“ in Azure Database for MySQL zum Nachverfolgen der Abfrageleistung im Zeitverlauf.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 70e1e5d06ef025801322e15e589d26e31f116fc3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535077"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Überwachen der Azure Database for MySQL-Leistung mit dem Abfragespeicher

**Anwendungsbereich:** Azure Database for MySQL 5.7, 8.0

Das Feature „Abfragespeicher“ in Azure Database for MySQL verfügt über eine Möglichkeit zum Nachverfolgen der Abfrageleistung im Zeitverlauf. Der Abfragespeicher vereinfacht das Beheben von Leistungsproblemen, da er es Ihnen ermöglicht, die am längsten ausgeführten und ressourcenintensivsten Abfragen schnell zu ermitteln. Der Abfragespeicher erfasst automatisch einen Verlauf der Abfragen und Laufzeitstatistiken und bewahrt diese auf, damit Sie sie überprüfen können. Er unterteilt die Daten nach Zeitfenstern, damit Sie Verwendungsmuster für Datenbanken erkennen können. Die Daten für alle Benutzer, Datenbanken und Abfragen werden in der Schemadatenbank **mysql** der Azure Database for MySQL-Instanz gespeichert.

## <a name="common-scenarios-for-using-query-store"></a>Gängige Szenarien für die Verwendung des Abfragespeichers

Der Abfragespeicher kann in unterschiedlichen Szenarien genutzt werden, z. B.:

- Erkennen von zurückgestellten Abfragen
- Bestimmen der Häufigkeit zum Ausführen einer Abfrage in einem angegebenen Zeitfenster
- Vergleichen der durchschnittlichen Ausführungsdauer einer Abfrage für bestimmte Zeitfenster zum Anzeigen großer Abweichungen

## <a name="enabling-query-store"></a>Aktivieren des Abfragespeichers

Der Abfragespeicher ist ein optionales Feature. Daher ist er auf einem Server nicht standardmäßig aktiviert. Der Abfragespeicher wird global für alle Datenbanken auf einem bestimmten Server aktiviert oder deaktiviert. Eine Aktivierung oder Deaktivierung für einzelne Datenbanken ist nicht möglich.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivieren des Abfragespeichers über das Azure-Portal

1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure Database for MySQL-Server aus.
1. Wählen Sie im Bereich **Einstellungen** im Menü die Option **Serverparameter**.
1. Suchen Sie nach dem Parameter „query_store_capture_mode“.
1. Legen Sie den Wert auf „ALL“ fest, und wählen Sie dann **Speichern** aus.

So aktivieren Sie Wartestatistiken in Ihrem Abfragespeicher:

1. Suchen Sie nach dem Parameter „query_store_wait_sampling_capture_mode“.
1. Legen Sie den Wert auf „ALL“ fest, und wählen Sie dann **Speichern** aus.

Es kann bis zu 20 Minuten dauern, bis der erste Batch mit Daten in der mysql-Datenbank gespeichert ist.

## <a name="information-in-query-store"></a>Informationen im Abfragespeicher

Der Abfragespeicher verfügt über zwei Speicher:

- Einen Laufzeitstatistikspeicher zur Aufbewahrung der Statistikinformationen im Zusammenhang mit der Abfrageausführung
- Einen Wartestatistikspeicher zur Aufbewahrung von Wartestatistikinformationen

Um die Speicherverwendung zu minimieren, wird die Laufzeitausführungsstatistik im Laufzeitstatistikspeicher für ein festes, konfigurierbares Zeitfenster aggregiert. Die Informationen in diesem Speicher können durch Abfragen der Abfragespeicheransichten angezeigt werden.

Die folgende Abfrage gibt Informationen über Abfragen im Abfragespeicher zurück:

```sql
SELECT * FROM mysql.query_store;
```

Die folgende Abfrage wird für die Wartestatistik verwendet:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Suchen von Warteanfragen

> [!NOTE]
> Die Wartestatistik sollte während Workloadspitzenzeiten nicht aktiviert oder bei leistungsabhängigen Workloads nicht unbegrenzt aktiviert werden. <br>Gehen Sie bei Workloads, die mit hoher CPU-Auslastung oder Servern mit niedrigeren virtuellen Kernen ausgeführt werden, mit Bedacht vor, wenn Sie die Wartestatistik aktivieren. Sie sollte nicht unbegrenzt aktiviert werden. 

Warteereignistypen kombinieren verschiedene Warteereignisse nach Ähnlichkeit in Buckets. Der Abfragespeicher enthält den Warteereignistyp, den spezifischen Warteereignisnamen und die entsprechende Abfrage. Die Möglichkeit zum Korrelieren dieser Warteinformationen mit den Abfragelaufzeitstatistiken bedeutet, dass Sie einen ausführlicheren Überblick darüber erhalten, welche Faktoren sich auf die Abfrageleistung auswirken.

Im Folgenden finden Sie einige Beispiele dafür, wie Sie mithilfe der Wartestatistiken im Abfragespeicher weitere Erkenntnisse zur Ihrer Workload erhalten:

| **Beobachtung** | **Aktion** |
|---|---|
|Lange Sperrwartevorgänge | Überprüfen Sie die Abfragetexte der betroffenen Abfragen, und identifizieren Sie die Zielentitäten. Suchen Sie im Abfragespeicher nach anderen Abfragen, die die gleiche Entität ändern, welche häufig ausgeführt wird bzw. eine lange Dauer aufweist. Nachdem Sie diese Abfragen ermittelt haben, ändern Sie ggf. die Anwendungslogik, um die Parallelität zu verbessern, oder verwenden Sie eine weniger restriktive Isolationsstufe. |
|Lange Puffer-E/A-Wartevorgänge | Suchen Sie die Abfragen mit einer hohen Anzahl an physischen Lesevorgängen im Abfragespeicher. Wenn diese mit den Abfragen mit langen E/A-Wartevorgängen übereinstimmen, sollten Sie ggf. erwägen, einen Index für die zugrunde liegende Entität einzuführen, um Such- anstelle von Scanvorgängen durchzuführen. Dies verringert den E/A-Aufwand der Abfragen. Überprüfen Sie die **Leistungsempfehlungen** für Ihren Server im Portal, um festzustellen, ob Indexempfehlungen für diesen Server vorhanden sind, die die Abfragen optimieren. |
|Lange Arbeitsspeicher-Wartevorgänge | Suchen Sie die im Abfragespeicher die speicherintensivsten Abfragen. Diese Abfragen verzögern wahrscheinlich zusätzlich den Fortschritt der betroffen Abfragen. Überprüfen Sie die **Leistungsempfehlungen** für Ihren Server im Portal, um festzustellen, ob Indexempfehlungen vorhanden sind, die diese Abfragen optimieren.|

## <a name="configuration-options"></a>Konfigurationsoptionen

Wenn der Abfragespeicher aktiviert ist, speichert er Daten in Aggregationsfenstern von 15 Minuten mit bis zu 500 unterschiedlichen Abfragen pro Fenster.

Die folgenden Optionen stehen für die Konfiguration der Abfragespeicherparameter zur Verfügung.

| **Parameter** | **Beschreibung** | **Standard** | **Bereich** |
|---|---|---|---|
| query_store_capture_mode | Schaltet die Abfragespeicherfunktion basierend auf dem Wert ein oder aus. Hinweis: Bei ausgeschaltetem Leistungsschema (performance_schema) werden durch Aktivieren von „query_store_capture_mode“ das Leistungsschema und ein Teil der Leistungsschemainstrumente aktiviert, die für dieses Feature benötigt werden. | ALL | NONE, ALL |
| query_store_capture_interval | Das Erfassungsintervall des Abfragespeichers in Minuten. Ermöglicht das Angeben des Intervalls, nach dem die Abfragemetriken aggregiert werden. | 15 | 5–60 |
| query_store_capture_utility_queries | Kann ein- oder ausgeschaltet werden, um die Erfassung aller Hilfsabfragen, die im System ausgeführt werden, zu aktivieren oder zu deaktivieren. | Nein | YES, NO |
| query_store_retention_period_in_days | Zeitfenster in Tagen für die Aufbewahrung der Daten im Abfragespeicher. | 7 | 1 – 30 |

Die folgenden Optionen gelten speziell für Wartestatistiken.

| **Parameter** | **Beschreibung** | **Standard** | **Bereich** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Ermöglicht das Ein-/Ausschalten der Wartestatistik. | Keine | NONE, ALL |
| query_store_wait_sampling_frequency | Ändert die Frequenz der Stichprobenentnahme für Wartezeiten in Sekunden. Möglicher Bereich: zwischen fünf und 300 Sekunden. | 30 | 5–300 |

> [!NOTE]
> Derzeit wird diese Konfiguration durch **query_store_capture_mode** ersetzt. Dies bedeutet, dass sowohl **query_store_capture_mode** als auch **query_store_wait_sampling_capture_mode** für „ALL“ aktiviert sein muss, damit Wartestatistiken funktionieren. Wenn **query_store_capture_mode** deaktiviert ist, ist auch die Wartestatistik deaktiviert, da für die Wartestatistik das aktivierte Leistungsschema (performance_schema) und der vom Abfragespeicher erfasste Abfragetext (query_text) verwendet werden.

Verwenden Sie das [Azure-Portal](howto-server-parameters.md) oder die [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) zum Abrufen oder Festlegen eines anderen Werts für einen Parameter.

## <a name="views-and-functions"></a>Ansichten und Funktionen

Mithilfe der folgenden Ansichten und Funktionen können Sie den Abfragespeicher anzeigen und verwalten. Jeder Benutzer mit der [öffentlichen Rolle für die Auswahl von Berechtigungen](howto-create-users.md#to-create-additional-admin-users-in-azure-database-for-mysql) kann diese Ansichten verwenden, um die Daten im Abfragespeicher anzuzeigen. Diese Sichten sind nur in der **mysql**-Datenbank verfügbar.

Abfragen werden normalisiert, indem ihre Struktur nach dem Entfernen von Literalen und Konstanten untersucht wird. Wenn zwei Abfragen mit Ausnahme von Literalwerten identisch sind, haben sie denselben Hash.

### <a name="mysqlquery_store"></a>mysql.query_store

In dieser Ansicht werden alle Daten im Abfragespeicher zurückgegeben. Es gibt eine Zeile für jede einzelne Datenbank-ID, Benutzer-ID und Abfrage-ID.

| **Name** | **Datentyp** | **IS_NULLABLE** | **Beschreibung** |
|---|---|---|---|
| `schema_name`| varchar(64) | Nein | Name des Schemas |
| `query_id`| bigint(20) | Nein| Eindeutige ID, die für die spezifische Abfrage generiert wird. Wenn die gleiche Abfrage in einem anderen Schema ausgeführt wird, wird eine neue ID generiert. |
| `timestamp_id` | timestamp| Nein| Zeitstempel für die Ausführung der Abfrage. Dieser Wert basiert auf der Konfiguration von „query_store_interval“.|
| `query_digest_text`| longtext| Nein| Der normalisierte Abfragetext nach dem Entfernen aller Literale.|
| `query_sample_text` | longtext| Nein| Erste Darstellung der eigentlichen Abfrage mit Literalen.|
| `query_digest_truncated` | bit| YES| Gibt an, ob der Abfragetext gekürzt wurde. Der Wert lautet „YES“, wenn die Abfrage länger als 1 KB ist.|
| `execution_count` | bigint(20)| Nein| Gibt an, wie oft die Abfrage für diese Zeitstempel-ID bzw. während des konfigurierten Intervallzeitraums ausgeführt wurde.|
| `warning_count` | bigint(20)| Nein| Anzahl von Warnungen, die von dieser Abfrage während des Intervalls generiert wurden.|
| `error_count` | bigint(20)| Nein| Anzahl von Fehlern, die von dieser Abfrage während des Intervalls generiert wurden.|
| `sum_timer_wait` | double| YES| Gesamte Ausführungsdauer dieser Abfrage während des Intervalls.|
| `avg_timer_wait` | double| YES| Durchschnittliche Ausführungsdauer dieser Abfrage während des Intervalls.|
| `min_timer_wait` | double| YES| Minimale Ausführungszeit für diese Abfrage.|
| `max_timer_wait` | double| YES| Maximale Ausführungszeit.|
| `sum_lock_time` | bigint(20)| Nein| Gesamte Zeit, die für alle Sperren dieser Abfrageausführung während dieses Zeitfensters aufgewendet wurde.|
| `sum_rows_affected` | bigint(20)| Nein| Anzahl betroffener Zeilen.|
| `sum_rows_sent` | bigint(20)| Nein| Anzahl von Zeilen, die an den Client gesendet wurden.|
| `sum_rows_examined` | bigint(20)| Nein| Anzahl untersuchter Zeilen|
| `sum_select_full_join` | bigint(20)| Nein| Anzahl von vollständigen Verknüpfungen.|
| `sum_select_scan` | bigint(20)| Nein| Anzahl für Scanauswahl. |
| `sum_sort_rows` | bigint(20)| Nein| Anzahl sortierter Zeilen.|
| `sum_no_index_used` | bigint(20)| Nein| Anzahl der Fälle, in denen von der Abfrage keine Indizes verwendet wurden.|
| `sum_no_good_index_used` | bigint(20)| Nein| Anzahl der Fälle, in denen die Abfrageausführungs-Engine keine guten Indizes verwendet hat.|
| `sum_created_tmp_tables` | bigint(20)| Nein| Gesamtzahl erstellter temporärer Tabellen.|
| `sum_created_tmp_disk_tables` | bigint(20)| Nein| Gesamtzahl temporärer Tabellen, die auf dem Datenträger erstellt wurden (E/A-Generierung).|
| `first_seen` | timestamp| Nein| Erstes Auftreten der Abfrage (UTC) während des Aggregationsfensters.|
| `last_seen` | timestamp| Nein| Letztes Auftreten der Abfrage (UTC) während dieses Aggregationsfensters.|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Diese Ansicht gibt Warteereignisdaten im Abfragespeicher zurück. Es gibt eine Zeile für jede einzelne Datenbank-ID, Benutzer-ID und jedes Ereignis.

| **Name**| **Datentyp** | **IS_NULLABLE** | **Beschreibung** |
|---|---|---|---|
| `interval_start` | timestamp | Nein| Start des Intervalls (15-Minuten-Inkrement).|
| `interval_end` | timestamp | Nein| Ende des Intervalls (15-Minuten-Inkrement).|
| `query_id` | bigint(20) | Nein| Generierte eindeutige ID in der normalisierten Abfrage (aus Abfragespeicher).|
| `query_digest_id` | varchar(32) | Nein| Der normalisierte Abfragetext nach dem Entfernen aller Literale (aus dem Abfragespeicher). |
| `query_digest_text` | longtext | Nein| Erste Darstellung der eigentlichen Abfrage mit Literalen (aus dem Abfragespeicher). |
| `event_type` | varchar(32) | Nein| Kategorie des Warteereignisses. |
| `event_name` | varchar(128) | Nein| Name des Warteereignisses. |
| `count_star` | bigint(20) | Nein| Anzahl von Warteereignissen, für die während des Intervalls für die Abfrage Stichproben entnommen wurden. |
| `sum_timer_wait_ms` | double | Nein| Gesamte Wartezeit (in Millisekunden) dieser Abfrage während des Intervalls. |

### <a name="functions"></a>Functions

| **Name**| **Beschreibung** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Löscht alle Abfragespeicherdaten vor dem angegebenen Zeitstempel. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Löscht alle Warteereignisdaten vor dem angegebenen Zeitstempel. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Löscht Empfehlungen, die vor dem angegebenen Zeitstempel ablaufen. |

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

- Wenn für einen MySQL-Server der Parameter `default_transaction_read_only` aktiviert ist, kann der Abfragespeicher keine Daten erfassen.
- Die Funktion des Abfragespeichers kann durch lange Unicodeabfragen (\>= 6.000 Bytes) unterbrochen werden.
- Der Aufbewahrungszeitraum für Wartestatistiken beträgt 24 Stunden.
- Für Wartestatistiken werden Stichproben verwendet, um einen Teil der Ereignisse zu erfassen. Die Häufigkeit kann mit dem Parameter `query_store_wait_sampling_frequency` geändert werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Query Performance Insight in Azure Database for MariaDB](concepts-query-performance-insight.md).
