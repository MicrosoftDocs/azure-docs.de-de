---
title: Typen von Problemen mit der Abfrageleistung in Azure SQL-Datenbank
description: In diesem Artikel erfahren Sie mehr über die Arten von Problemen mit der Abfrageleistung in Azure SQL-Datenbank und erfahren auch, wie Abfragen mit diesen Problemen ermittelt und korrigiert werden können.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: 6ea17f04538e3444b1baddaa8862add2cfbbaa9c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493422"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Erkennbare Typen von Problemen mit Engpässen bei der Abfrageleistung in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Wenn Sie versuchen, einen Leistungsengpass zu beheben, bestimmen Sie als Erstes, ob der Engpass auftritt, während sich die Abfrage in einem ausgeführten oder wartenden Zustand befindet. Nachdem dies bestimmt wurde, gibt es unterschiedliche Lösungsansätze. Das folgende Diagramm hilft Ihnen, die Faktoren zu verstehen, die entweder ein ausführungs- oder wartebezogenes Problem verursachen können. Probleme und Lösungen im Zusammenhang mit den einzelnen Problemtypen werden in diesem Artikel erläutert.

Sie können [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) von Azure SQL-Datenbank oder [dynamische Verwaltungssichten (Dynamic Management Views, DMVs)](database/monitoring-with-dmvs.md) von SQL Server verwenden, um diese Typen von Leistungsengpässen zu erkennen.

![Workload-Status](./media/identify-query-performance-issues/workload-states.png)

**Ausführungsbezogene Probleme**: Ausführungsbezogene Probleme sind im Allgemeinen mit Kompilierungsproblemen verbunden, die zu einem suboptimalen Abfrageplan oder Ausführungsproblemen aufgrund unzureichender oder überbeanspruchter Ressourcen führen.
**Wartebezogene Probleme**: Wartebezogene Probleme beziehen sich im Allgemeinen auf Folgendes:

- Sperren (Blockieren)
- E/A
- Konflikte im Zusammenhang mit der TempDB-Nutzung
- Wartetyp „Speicherzuweisung“

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Kompilierungsprobleme, die zu einem suboptimalen Abfrageplan führen

Ein vom SQL-Abfrageoptimierer generierter suboptimaler Plan ist möglicherweise die Ursache für eine langsame Abfrageleistung. Der SQL-Abfrageoptimierer generiert möglicherweise aufgrund eines fehlenden Indexes, veralteter Statistiken, einer falschen Schätzung der zu verarbeitenden Zeilenanzahl oder des erforderlichen Arbeitsspeichers einen nicht optimalen Plan. Wenn Sie wissen, dass die Abfrage in der Vergangenheit oder in einer anderen Instanz schneller ausgeführt wurde, untersuchen Sie die tatsächlichen Ausführungspläne auf mögliche Unterschiede.

- Ermitteln Sie fehlende Indizes mithilfe einer der folgenden Methoden:

  - [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index).
  - [Database Advisor](database/database-advisor-implement-performance-recommendations.md) für Einzel- und Pooldatenbanken.
  - DMVs. Dieses Beispiel zeigt Ihnen, welche Auswirkungen ein fehlender Index hat, wie ein [fehlender Index](database/performance-guidance.md#identifying-and-adding-missing-indexes) mithilfe von DMVs erkannt werden kann und welche Auswirkungen das Befolgen der Empfehlung zum fehlenden Index hat.
- Versuchen Sie, [Abfragehinweise anzuwenden](/sql/t-sql/queries/hints-transact-sql-query), [Statistiken zu aktualisieren](/sql/t-sql/statements/update-statistics-transact-sql) oder [Indizes neu zu erstellen](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes), um einen besseren Plan zu erhalten. Aktivieren Sie die [automatische Plankorrektur](../azure-sql/database/automatic-tuning-overview.md) in Azure SQL-Datenbank, um solche Probleme automatisch zu beheben.

  Dieses [Beispiel](database/performance-guidance.md#query-tuning-and-hinting) zeigt, wie sich ein suboptimaler Abfrageplan aufgrund einer parametrisierten Abfrage auswirkt, wie diese Bedingung erkannt wird und wie ein Abfragehinweis zur Behebung verwendet werden kann.

- Probieren Sie das Ändern des Datenbank-Kompatibilitätsgrads und Implementieren der intelligenten Abfrageverarbeitung. Der SQL-Abfrageoptimierer kann je nach Kompatibilitätsgrad Ihrer Datenbank einen anderen Abfrageplan generieren. Höhere Kompatibilitätsgrade bieten mehr [Möglichkeiten zur intelligenten Abfrageverarbeitung](/sql/relational-databases/performance/intelligent-query-processing).

  - Weitere Informationen zur Abfrageverarbeitung finden Sie im [Handbuch zur Architektur der Abfrageverarbeitung](/sql/relational-databases/query-processing-architecture-guide).
  - Weitere Informationen zum Ändern des Datenbank-Kompatibilitätsgrads und zu den Unterschieden bei Datenbank-Kompatibilitätsgraden finden Sie unter [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - Weitere Informationen zur Kardinalitätsschätzung finden Sie unter [Kardinalitätsschätzung](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Korrigieren von Abfragen mit suboptimalen Ausführungsplänen

In den folgenden Abschnitten wird erläutert, wie Abfragen mit einem suboptimalen Aufsführungsplan korrigiert werden.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> Abfragen mit PSP-Problemen (Parameter Sensitive Plan, parameterempfindlicher Plan)

Ein PSP-Problem (Parameter Sensitive Plan; parameterempfindlicher Plan) tritt auf, wenn der Abfrageoptimierer einen Abfrageausführungsplan generiert, der nur für einen bestimmten Parameterwert (oder eine Gruppe von Werten) optimal ist, und der zwischengespeicherte Plan somit für Parameterwerte späterer Ausführungen nicht optimal ist. Nicht optimale Pläne können zu Problemen mit der Abfrageleistung führen und den allgemeinen Workloaddurchsatz beeinträchtigen.

Weitere Informationen zur Parameterermittlung und Abfrageverarbeitung finden Sie im [Handbuch zur Architektur der Abfrageverarbeitung](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

PSP-Probleme können auf unterschiedliche Weise umgangen werden. Jede Problemumgehungen geht mit bestimmten Kompromissen und Nachteilen einher:

- Verwenden des Abfragehinweises [RECOMPILE](/sql/t-sql/queries/hints-transact-sql-query) bei jeder Abfrageausführung. Bei dieser Problemumgehung werden Kompilierungszeit und erhöhte CPU-Leistung für eine bessere Qualität des Abfrageplans eingebüßt. Die Option `RECOMPILE` kann bei Workloads, die einen hohen Durchsatz erfordern, häufig nicht verwendet werden.
- Verwenden Sie den Abfragehinweis [OPTION (OPTIMIZE FOR...)](/sql/t-sql/queries/hints-transact-sql-query), um den tatsächlichen Parameterwert mit einem typischen Parameterwert zu überschreiben, der einen Plan erzeugt, der für die meisten möglichen Parameterwerte geeignet ist. Diese Option erfordert ein gutes Verständnis der optimalen Parameterwerte und zugehörigen Planmerkmale.
- Verwenden Sie den Abfragehinweis [OPTION (OPTIMIZE FOR UNKNOWN)](/sql/t-sql/queries/hints-transact-sql-query), um den tatsächlichen Parameterwert zu überschreiben und stattdessen den Durchschnittswert des Dichtevektors zu verwenden. Hierzu können Sie auch die eingehenden Parameterwerte in lokalen Variablen erfassen und dann anstatt der Parameter die lokalen Variablen innerhalb der Prädikate verwenden. Für diese Korrektur muss die durchschnittliche Dichte *gut genug* sein.
- Verwenden Sie den Abfragehinweis [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query), um die Parameterermittlung vollständig zu deaktivieren.
- Verwenden Sie den Abfragehinweis [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query), um erneute Kompilierungen im Cache zu verhindern. Bei dieser Lösung wird davon ausgegangen, dass sich der ausreichende allgemeine Plan bereits im Cache befindet. Sie können auch automatische Statistikaktualisierungen deaktivieren, um die Wahrscheinlichkeit zu verringern, dass der gute Plan entfernt und ein neuer schlechter Plan kompiliert wird.
- Erzwingen Sie den Plan durch die explizite Verwendung des Abfragehinweises [USE PLAN](/sql/t-sql/queries/hints-transact-sql-query), indem Sie die Abfrage neu schreiben und den Hinweis dem Abfragetext hinzufügen. Alternativ können Sie einen bestimmten Plan mithilfe des Abfragespeichers oder durch Aktivieren der [automatischen Optimierung](../azure-sql/database/automatic-tuning-overview.md) festlegen.
- Ersetzen der Einzelprozedur durch eine geschachtelte Gruppe von Prozeduren, die jeweils basierend auf bedingter Logik und zugehörigen Parameterwerten verwendet werden können.
- Erstellen von Alternativen für die dynamische Zeichenfolgenausführung zur Definition einer statischen Prozedur.

Weitere Informationen zur Behebung von PSP-Problemen finden Sie in den folgenden Blogbeiträgen:

- [I Smell a Parameter!](/archive/blogs/queryoptteam/i-smell-a-parameter) (Ich rieche einen Parameter)
- [Conor, dynamischer SQL-Code, Prozeduren und Planqualität für parametrisierte Abfragen](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [Techniken zur SQL-Abfrageoptimierung in SQL Server: Parameterermittlung](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Durch nicht ordnungsgemäße Parametrisierung verursachte Kompilierungsaktivität

Wenn eine Abfrage Literale enthält, wird die Anweisung entweder automatisch durch die Datenbank-Engine parametrisiert, oder ein Benutzer parametrisiert die Anweisung explizit, um die Anzahl von Kompilierungen zu verringern. Eine hohe Anzahl von Kompilierungen für eine Abfrage mit dem gleichen Muster und unterschiedlichen Literalwerten kann zu einer hohen CPU-Auslastung führen. Wenn Sie eine Abfrage nur teilweise parametrisieren, sodass sie weiterhin Literale enthält, wird sie von der Datenbank-Engine nicht weiter parametrisiert.

Hier sehen Sie ein Beispiel für eine teilweise parametrisierte Abfrage:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

In diesem Beispiel nimmt `t1.c1` den Wert `@p1` an, aber `t2.c2` verwendet weiterhin die GUID als Literal. Wenn Sie in diesem Fall den Wert für `c2` ändern, wird die Abfrage als eine andere Abfrage behandelt und eine neue Kompilierung durchgeführt. In diesem Beispiel muss auch die GUID parametrisiert werden, um die Anzahl von Kompilierungen zu verringern.

Die folgende Abfrage zeigt die Anzahl von Abfragen nach Abfragehash, um zu ermitteln, ob eine Abfrage ordnungsgemäß parametrisiert ist:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>Faktoren, die sich auf Abfrageplanänderungen auswirken

Die Neukompilierung eines Abfrageausführungsplans führt möglicherweise zu einem generierten Abfrageplan, der sich von dem ursprünglich zwischengespeicherten Plan unterscheidet. Ein vorhandener ursprünglicher Plan kann aus unterschiedlichen Gründen automatisch neu kompiliert werden:

- Von der Abfrage wird auf Änderungen im Schema verwiesen.
- Von der Abfrage wird auf Datenänderungen in den Tabellen verwiesen.
- Abfragekontextoptionen wurden geändert.

Ein kompilierter Plan kann aus verschiedenen Gründen aus dem Cache entfernt werden:

- Neustart der Instanz
- Auf die Datenbank begrenzte Konfigurationsänderungen
- Hohe Arbeitsspeicherauslastung
- Explizite Anforderungen zum Löschen des Caches

Bei Verwendung eines RECOMPILE-Hinweises wird der Plan nicht zwischengespeichert.

Eine Neukompilierung (oder eine erneute Kompilierung nach dem Entfernen aus dem Cache) kann trotzdem zur Generierung eines Abfrageausführungsplans führen, der mit dem ursprünglichen Plan identisch ist. Wenn sich der Plan vom vorherigen oder ursprünglichen Plan unterscheidet, lässt sich dies wahrscheinlich wie folgt erklären:

- **Geändertes physisches Design:** Neu erstellte Indizes können beispielsweise die Anforderungen einer Abfrage effektiver abdecken. Die neuen Indizes können in einer neuen Kompilierung verwendet werden, wenn der Abfrageoptimierer entscheidet, dass der neue Index besser geeignet ist als die Datenstruktur, die ursprünglich für die erste Version der Abfrageausführung gewählt wurde. Jegliche physische Änderung an den referenzierten Objekten führt zur Kompilierzeit möglicherweise zu einer neuen Planauswahl.

- **Unterschiede zwischen Serverressourcen:** Wenn sich ein Plan in einem System vom Plan in einem anderen System unterscheidet, kann die Ressourcenverfügbarkeit (etwa die Anzahl verfügbarer Prozessoren) darüber entscheiden, welcher Plan generiert wird. Wenn ein System also beispielsweise über mehr Prozessoren verfügt, wird möglicherweise ein paralleler Plan ausgewählt.

- **Unterschiedliche Statistiken:** Möglicherweise haben sich die den referenzierten Objekten zugeordneten Statistiken geändert, oder sie unterscheiden sich wesentlich von den Statistiken des ursprünglichen Systems. Wenn sich die Statistik ändert und eine Neukompilierung durchgeführt wird, verwendet der Abfrageoptimierer die Statistik ab der Änderung. Die überarbeiteten Datenverteilungen und Häufigkeiten der Statistik unterscheiden sich unter Umständen von denen der ursprünglichen Kompilierung. Diese Änderungen werden zur Erstellung von Kardinalitätsschätzungen verwendet. (Bei *Kardinalitätsschätzungen* handelt es sich um die Anzahl von Zeilen, die voraussichtlich die logische Abfragestruktur durchlaufen). Änderungen an Kardinalitätsschätzungen können Sie ggf. dazu veranlassen, andere physische Operatoren und eine andere Vorgangsreihenfolge auszuwählen. Selbst geringfügige Änderungen an Statistiken können zu einem anderen Abfrageausführungsplan führen.

- **Geänderter Datenbank-Kompatibilitätsgrad oder geänderte Version der Kardinalitätsschätzung:** Bei Änderungen am Datenbank-Kompatibilitätsgrad können neue Strategien und Funktionen aktiviert werden, die möglicherweise zu einem anderen Abfrageausführungsplan führen. Neben dem Datenbank-Kompatibilitätsgrad kann die Wahl des Abfrageausführungsplans zur Kompilierungszeit auch durch Deaktivieren oder Aktivieren des Ablaufverfolgungsflags 4199 oder durch Ändern des Zustands der datenbankbezogenen Konfiguration „QUERY_OPTIMIZER_HOTFIXES“ beeinflusst werden. Auch die Ablaufverfolgungsflags 9481 (Legacy-CE erzwingen) und 2312 (Standard-CE erzwingen) wirken sich auf den Plan aus.

## <a name="resource-limits-issues"></a>Probleme aufgrund von Ressourceneinschränkungen

Eine langsame Abfrageleistung, die nicht auf suboptimale Abfragepläne und fehlende Indizes zurückzuführen ist, hängt im Allgemeinen mit unzureichenden oder überlasteten Ressourcen zusammen. Wenn der Abfrageplan optimal ist, könnte die Abfrage (und die Datenbank) an die Ressourcengrenzen für die Datenbank, den Pool für elastische Datenbanken oder die verwaltete Instanz stoßen. Ein Beispiel dafür ist möglicherweise der übermäßige Durchsatz beim Schreiben von Protokollen für die Dienstebene.

- Erkennen von Ressourcenproblemen mithilfe des Azure-Portals: Informationen zum Feststellen, ob Ressourcenlimits das Problem sind, finden Sie unter [SQL-Datenbank-Ressourcenüberwachung](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring). Informationen zu Einzeldatenbanken und Pools für elastische Datenbanken finden Sie unter [Database Advisor: Empfehlungen zur Leistung](database/database-advisor-implement-performance-recommendations.md) und [Query Performance Insight](database/query-performance-insight-use.md).
- Erkennen von Ressourcenlimits mithilfe von [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)
- Erkennen von Ressourcenproblemen mithilfe von [DMVs](database/monitoring-with-dmvs.md):

  - Die DMV [sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) gibt die CPU-, E/A- und Arbeitsspeichernutzung für die Datenbank zurück. Für alle 15 Sekunden ist eine Zeile enthalten, selbst wenn keine Aktivität in der Datenbank erfolgt ist. Verlaufsdaten werden eine Stunde lang aufbewahrt.
  - Die DMV [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) gibt CPU-Auslastungs- und Speicherdaten für Azure SQL-Datenbank zurück. Die Daten werden in Intervallen von fünf Minuten gesammelt und aggregiert.
  - [Viele einzelne Abfragen, die zusammen zu einer hohen CPU-Auslastung führen](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Wenn Sie herausfinden, dass das Problem durch unzureichende Ressourcen entstanden ist, können Sie die Ressourcen ausbauen, um die Kapazität Ihrer Datenbank zu erhöhen und die CPU-Anforderungen zu erfüllen. Weitere Informationen finden Sie unter [Skalieren von Einzeldatenbankressourcen in Azure SQL-Datenbank](database/single-database-scale.md) sowie unter [Skalieren von Ressourcen für Pools für elastische Datenbanken in Azure SQL-Datenbank](database/elastic-pool-scale.md). Informationen zum Skalieren einer verwalteten Instanz finden Sie unter [Ressourcenlimits der Dienstebene](managed-instance/resource-limits.md#service-tier-characteristics).

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Leistungsprobleme aufgrund eines erhöhten Workloadaufkommens

Eine erhöhte CPU-Auslastung kann auf eine Zunahme des Anwendungsdatenverkehrs und des Workloadaufkommens zurückzuführen sein. Dieses Problem muss allerdings sorgfältig diagnostiziert werden. Beantworten Sie im Falle eines Problems mit hoher CPU-Auslastung die folgenden Fragen, um zu ermitteln, ob die höhere Auslastung auf Veränderungen beim Workloadaufkommen zurückzuführen ist:

- Sind die Abfragen von der Anwendung die Ursache für das Problem mit hoher CPU-Auslastung?
- Für die [Abfragen mit der höchsten CPU-Auslastung, die Sie identifizieren können](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past):

  - Wurden der gleichen Abfrage mehrere Ausführungspläne zugeordnet? Falls ja: Warum?
  - Waren die Ausführungszeiten bei Abfragen mit dem gleichen Ausführungsplan konsistent? Hat sich die Ausführungsanzahl erhöht? Falls ja, führt die Workloadzunahme wahrscheinlich zu Leistungsproblemen.

Zusammenfassend lässt sich sagen: Wenn der Abfrageausführungsplan nicht anders ausgeführt wurde, die CPU-Auslastung aber zusammen mit der Anzahl von Ausführungen zugenommen hat, hängt das Leistungsproblem wahrscheinlich mit einer Workloadzunahme zusammen.

Es ist nicht immer einfach, eine Änderung des Workloadaufkommens zu ermitteln, die einem CPU-Problem zugrunde liegt. Beachten Sie folgende Faktoren:

- **Geänderte Ressourcennutzung:** Nehmen wir beispielsweise an, in einem Szenario hat sich die CPU-Auslastung für einen längeren Zeitraum auf 80 Prozent erhöht. Die CPU-Auslastung allein bedeutet jedoch nicht, dass sich das Workloadaufkommen geändert hat. Auch Regressionen im Abfrageausführungsplans und eine geänderte Datenverteilung können zu einer höheren Ressourcennutzung beitragen, selbst wenn die Anwendung die gleiche Workload ausführt.

- **Auftauchen einer neuen Abfrage:** Eine Anwendung kann zu unterschiedlichen Zeiten eine neue Gruppe von Abfragen auslösen.

- **Erhöhung oder Verringerung der Anforderungsanzahl:** Dieses Szenario ist der offensichtlichste Maßstab einer Workload. Die Anzahl der Abfragen entspricht nicht immer einer gesteigerten Ressourcennutzung. Diese Metrik ist dennoch ein wichtiges Signal, falls sich andere Faktoren nicht geändert haben.

Verwenden Sie Intelligent Insights, um [Zunahmen von Workloads](database/intelligent-insights-troubleshoot-performance.md#workload-increase) zu erkennen und [Regressionen zu planen](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Wartebezogene Probleme

Sobald Sie einen suboptimalen Plan und *wartebezogene Probleme* im Zusammenhang mit Ausführungsproblemen beseitigt haben, besteht das Leistungsproblem meist darin, dass die Abfragen wahrscheinlich auf eine Ressource warten. Wartebezogene Probleme können folgende Ursachen haben:

- **Blockierung:**

  Möglicherweise wurden einige Objekte in der Datenbank für eine Abfrage gesperrt, während andere Abfragen versuchen, auf die gleichen Objekte zuzugreifen. Blockierende Abfragen können mithilfe von [DMVs](database/monitoring-with-dmvs.md#monitoring-blocked-queries) oder [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking) ermittelt werden.
- **E/A-Probleme**

  Abfragen warten möglicherweise darauf, dass Seiten in die Daten- oder Protokolldateien geschrieben werden. Überprüfen Sie in diesem Fall die Wartestatistik `INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG` oder `PAGEIOLATCH_*` in der DMV. Erfahren Sie, wie DMVs zum [Identifizieren von Problemen mit der E/A-Leistung](database/monitoring-with-dmvs.md#identify-io-performance-issues) verwendet werden können.
- **TempDB-Probleme**

  Wenn die Workload temporäre Tabellen verwendet oder die Pläne TempDB-Überläufe enthalten, liegt bei den Anfragen unter Umständen ein Problem mit dem TempDB-Durchsatz vor. Erfahren Sie, wie DMVs zum [Identifizieren von TempDB-Problemen ](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues) verwendet werden können.
- **Arbeitsspeicherbezogene Probleme**

  Wenn für die Workload nicht genügend Arbeitsspeicher zur Verfügung steht, sinkt unter Umständen die Seitenlebenserwartung, oder die Abfragen erhalten weniger Arbeitsspeicher als sie benötigen. In bestimmten Fällen können arbeitsspeicherbezogene Probleme durch die integrierte Intelligenz des Abfrageoptimierers behoben werden. Erfahren Sie, wie DMVs zum [Identifizieren von Problemen mit der Speicherzuweisung](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues) verwendet werden können.

### <a name="methods-to-show-top-wait-categories"></a>Methoden zum Anzeigen der wichtigsten Wartekategorien

Im Anschluss finden Sie gängige Methoden zum Anzeigen der wichtigsten Kategorien von Wartetypen:

- Verwenden Sie Intelligent Insights, um Abfragen mit Leistungsbeeinträchtigung aufgrund [erhöhter Wartezeiten](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) zu identifizieren.
- Verwenden Sie den [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store), um Wartestatistiken für die einzelnen Abfragen im Zeitverlauf zu suchen. Im Abfragedatenspeicher sind Wartetypen mit Wartekategorien kombiniert. Die Zuordnung von Wartekategorien zu Wartetypen finden Sie unter [sys.query_store_wait_stats (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table).
- Verwenden Sie [sys.dm_db_wait_stats (Azure SQL-Datenbank)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database), um Informationen zu allen Wartezeiten für Threads zurückzugeben, die während des Abfragevorgangs ausgeführt wurden. Sie können diese aggregierte Ansicht verwenden, um Leistungsprobleme mit Azure SQL-Datenbank sowie mit bestimmten Abfragen und Batches zu diagnostizieren. Abfragen können auf Ressourcen, Warteschlangen oder externe Vorgänge warten.
- Verwenden Sie [sys.dm_os_waiting_tasks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql), um Informationen zur Warteschlange von Aufgaben zurückzugeben, die auf eine Ressource warten.

In Szenarien mit hoher CPU-Auslastung wird die CPU-Auslastung durch den Abfragespeicher und die Wartestatistik in folgenden Fällen möglicherweise nicht korrekt wiedergegeben:

- Abfragen mit hoher CPU-Auslastung werden noch ausgeführt.
- Die Abfragen mit hoher CPU-Auslastung wurden parallel zu einem Failover ausgeführt.

DMVs, die den Abfragespeicher und die Wartestatistik nachverfolgen, zeigen nur Ergebnisse für erfolgreich abgeschlossene Abfragen sowie für Abfragen an, bei denen ein Timeout aufgetreten ist. Sie zeigen keine Daten für aktuell ausgeführte Anweisungen an, bis die Anweisungen abgeschlossen sind. Verwenden Sie die dynamische Verwaltungssicht [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), um aktuell ausgeführte Abfragen und die Zeit des zugeordneten Workers anzuzeigen.

> [!TIP]
> Weitere Tools:
>
> - [TigerToolbox: Wartevorgänge und Latches](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox: usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über die Überwachung und Optimierung von SQL-Datenbank](database/monitor-tune-overview.md)