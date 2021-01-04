---
title: Überwachen der Leistung mithilfe von DMVs
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Erfahren Sie, wie Sie allgemeine Leistungsprobleme mithilfe der dynamischen Verwaltungssichten zum Überwachen von Microsoft Azure SQL-Datenbank und Azure SQL Managed Instance ermitteln und diagnostizieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/19/2020
ms.openlocfilehash: 480e9f9031481621ac9d568a7bd97b942f47b947
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493640"
---
# <a name="monitoring-microsoft-azure-sql-database-and-azure-sql-managed-instance-performance-using-dynamic-management-views"></a>Überwachen der Leistung von Microsoft Azure SQL-Datenbank und Azure SQL Managed Instance mithilfe von dynamischen Verwaltungssichten
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Microsoft Azure SQL-Datenbank und Azure SQL Managed Instance unterstützen eine Teilmenge dynamischer Verwaltungssichten für die Diagnose von Leistungsproblemen, die auf blockierte Abfragen oder Abfragen mit langen Laufzeiten, fehlerhafte Abfragepläne usw. zurückzuführen sind. Dieses Thema enthält Informationen zum Erkennen häufiger Leistungsprobleme mithilfe von dynamischen Verwaltungssichten.

Microsoft Azure SQL-Datenbank und Azure SQL Managed Instance bieten eine Teilunterstützung für drei Kategorien von dynamischen Verwaltungssichten:

- Dynamische Verwaltungssichten für die Datenbank
- Dynamische Verwaltungssichten für die Ausführung
- Dynamische Verwaltungssichten für Transaktionen

Ausführliche Informationen zu dynamischen Verwaltungssichten finden Sie unter [Dynamische Verwaltungssichten und Funktionen (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

## <a name="permissions"></a>Berechtigungen

In Azure SQL-Datenbank wird zum Abfragen einer dynamischen Verwaltungssicht die Berechtigung **VIEW DATABASE STATE** benötigt. Die Berechtigung **VIEW DATABASE STATE** gibt Informationen zu allen Objekten innerhalb der aktuellen Datenbank zurück.
Zum Erteilen der Berechtigung **VIEW DATABASE STATE** für einen bestimmten Benutzer führen Sie die folgende Abfrage aus:

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

In Azure SQL Managed Instance ist zum Abfragen einer dynamischen Verwaltungssicht die Berechtigung **VIEW SERVER STATE** erforderlich. Weitere Informationen finden Sie unter [Dynamische Systemverwaltungssichten](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views#required-permissions).

Bei einer lokalen Instanz von SQL Server und in Azure SQL Managed Instance geben dynamische Verwaltungssichten Informationen zum Serverzustand zurück. In Azure SQL-Datenbank werden lediglich Informationen zur aktuellen logischen Datenbank zurückgegeben.

Dieser Artikel enthält eine Sammlung von DMV-Abfragen, die Sie mit SQL Server Management Studio oder Azure Data Studio ausführen können, um die folgenden Typen von Abfrageleistungsproblemen zu ermitteln:

- [Ermitteln von Abfragen im Zusammenhang mit übermäßiger CPU-Auslastung](#identify-cpu-performance-issues)
- [Wartevorgänge vom Typ PAGELATCH_* und WRITE_LOG im Zusammenhang mit E/A-Engpässen](#identify-io-performance-issues)
- [Durch TempDB-Konflikt verursachte Wartevorgänge vom Typ PAGELATCH_*](#identify-tempdb-performance-issues)
- [Probleme bei Wartevorgängen vom Typ RESOURCE_SEMAHPORE für Speicherzuweisung](#identify-memory-grant-wait-performance-issues)
- [Ermitteln von Datenbank- und Objektgrößen](#calculating-database-and-objects-sizes)
- [Abrufen von Informationen zu aktiven Sitzungen](#monitoring-connections)
- [Abrufen systemweiter und datenbankspezifischer Informationen zur Ressourcennutzung](#monitor-resource-use)
- [Abrufen von Informationen zur Abfrageleistung](#monitoring-query-performance)

## <a name="identify-cpu-performance-issues"></a>Identifizieren von CPU-Leistungsproblemen

Wenn die CPU-Auslastung über längere Zeit über 80 % liegt, sollten Sie die folgenden Problembehandlungsschritte in Betracht ziehen:

### <a name="the-cpu-issue-is-occurring-now"></a>Das CPU-Problem tritt jetzt auf

Wenn das Problem aktuell auftritt, sind zwei Szenarien möglich:

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>Viele einzelne Abfragen, die zusammen zu einer hohen CPU-Auslastung führen

Verwenden Sie die folgende Abfrage, um die häufigsten Abfragehashes zu identifizieren:

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM (SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
    FROM (SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
                CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
    GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>Abfragen mit langer Ausführungszeit, die CPU-Ressourcen verbrauchen, werden noch ausgeführt

Verwenden Sie die folgende Abfrage, um diese Abfragen zu identifizieren:

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
    CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>Das CPU-Problem ist in der Vergangenheit aufgetreten

Wenn das Problem in der Vergangenheit aufgetreten ist und Sie eine Ursachenanalyse durchführen möchten, verwenden Sie den [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store). Benutzer mit Datenbankzugriff können T-SQL verwenden, um die Daten aus dem Abfragespeicher abzufragen. Die Standardkonfigurationen des Abfragespeichers verwenden eine Granularität von 1 Stunde. Verwenden Sie die folgende Abfrage, um die Aktivität für Abfragen mit hohem CPU-Verbrauch zu untersuchen. Diese Abfrage gibt die häufigsten 15 Abfragen mit CPU-Ressourcenverbrauch zurück. Denken Sie daran, `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()` zu ändern:

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

Sobald Sie die problematischen Abfragen identifiziert haben, sollten Sie diese optimieren, um die CPU-Auslastung zu reduzieren.  Wenn Sie keine Zeit haben, die Abfragen zu optimieren, können Sie auch das Servicelevelziel der Datenbank erweitern, um das Problem zu umgehen.

## <a name="identify-io-performance-issues"></a>Identifizieren von Problemen mit der E/A-Leistung

Wenn Sie Probleme mit der E/A-Leistung feststellen, sind dies die häufigsten Wartetypen in Zusammenhang mit E/A-Problemen:

- `PAGEIOLATCH_*`

  Für E/A-Probleme bei Datendateien (einschließlich `PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP`).  Wenn der Name des Wartetyps die Buchstaben **IO** enthält, verweist er auf ein E/A-Problem. Wenn der Name des Pagelatch-Wartevorgangs die Buchstaben **IO** nicht enthält, verweist er auf eine andere Art Problem (z.B. einen tempdb-Konflikt).

- `WRITE_LOG`

  Für E/A-Probleme bei Transaktionsprotokollen.

### <a name="if-the-io-issue-is-occurring-right-now"></a>Das E/A-Problem tritt jetzt auf

Verwenden Sie [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) oder [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql), um `wait_type` und `wait_time` anzuzeigen.

#### <a name="identify-data-and-log-io-usage"></a>Identifizieren der Daten- und Protokoll-E/A-Nutzung

Verwenden Sie die folgende Abfrage, um die Daten- und Protokoll-E/A-Nutzung zu identifizieren. Wenn die Daten- oder Protokoll-E/A über 80 % liegt, bedeutet dies, dass die verfügbaren E/A-Ressourcen für die SQL-Datenbank-Dienstebene belegt sind.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

Wenn das E/A-Limit erreicht ist, haben Sie zwei Möglichkeiten:

- Option 1: Upgrade der Computegröße oder Dienstebene
- Option 2: Identifizieren und Optimieren der Abfragen, die die meisten E/A-Ressourcen verbrauchen

#### <a name="view-buffer-related-io-using-the-query-store"></a>Anzeigen von pufferbezogenen E/A-Vorgängen, die den Abfragespeicher verwenden

Für Option 2 können Sie die folgende Abfrage für pufferbezogene Vorgänge im Abfragespeicher verwenden, um die letzten zwei Stunden der überwachten Aktivitäten anzuzeigen:

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>Anzeigen sämtlicher E/A-Vorgänge für WRITELOG-Wartevorgänge

Wenn der Wartetyp `WRITELOG` ist, verwenden Sie die folgende Abfrage, um sämtliche Protokoll-E/A-Vorgänge nach Anweisung anzuzeigen:

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>Identifizieren von Problemen mit der `tempdb`-Leistung

Wenn Sie Probleme mit der E/A-Leistung feststellen, ist `PAGELATCH_*` (nicht `PAGEIOLATCH_*`) der häufigste Wartetyp in Zusammenhang mit `tempdb`-Problemen. `PAGELATCH_*`-Wartevorgänge bedeuten jedoch nicht immer, dass ein `tempdb`-Konflikt vorliegt.  Dieser Wartevorgang kann auch auf einen Konflikt mit Benutzerobjekt-Datenseiten aufgrund von gleichzeitigen Anforderungen an dieselbe Datenseite hinweisen. Zur genaueren Ermittlung, ob es sich um einen `tempdb`-Konflikt handelt, verwenden Sie [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql), um zu bestätigen, dass der Wert von „wait_resource“ mit `2:x:y` beginnt, wobei „2“ die `tempdb`-Datenbank-ID, `x` die Datei-ID und `y` die Seiten-ID ist.  

Bei tempdb-Konflikten besteht eine gängige Methode darin, den Anwendungscode, der `tempdb` benötigt, zu reduzieren oder neu zu schreiben.  In folgenden Bereichen wird `tempdb` meist genutzt:

- Temporäre Tabellen
- Tabellenvariablen
- Tabellenwertparameter
- Versionsspeichernutzung (insbesondere in Zusammenhang mit Transaktionen mit langer Ausführungszeit)
- Abfragen mit Abfrageplänen, die Sortiervorgänge, Hashjoins und Spoolvorgänge verwenden

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>Häufige Abfragen, die Tabellenvariablen und temporäre Tabellen verwenden

Verwenden Sie die folgende Abfrage, um die häufigsten Abfragen zu identifizieren, die Tabellenvariablen und temporäre Tabellen verwenden:

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>Identifizieren von Transaktionen mit langer Ausführungszeit

Verwenden Sie die folgende Abfrage, um Transaktionen mit langer Ausführungszeit zu identifizieren. Transaktionen mit langer Ausführungszeit verhindern das Bereinigen von Versionsspeichern.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>Identifizieren von Leistungsproblemen bei Wartevorgängen für Speicherzuweisung

Wenn der häufigste Wartetyp `RESOURCE_SEMAHPORE` ist und kein Problem mit einer hohen CPU-Auslastung vorliegt, besteht möglicherweise ein Problem bei Wartevorgängen für die Speicherzuweisung.

### <a name="determine-if-a-resource_semahpore-wait-is-a-top-wait"></a>Ermitteln, ob ein `RESOURCE_SEMAHPORE`-Wartevorgang ein häufiger Wartevorgang ist

Verwenden Sie die folgende Abfrage, um zu ermitteln, ob ein `RESOURCE_SEMAHPORE`-Wartevorgang ein häufiger Wartevorgang ist.

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identify-high-memory-consuming-statements"></a>Ermitteln von Anweisungen mit hohem Arbeitsspeicherverbrauch

Verwenden Sie die folgende Abfrage, um Anweisungen mit hohem Arbeitsspeicherverbrauch zu identifizieren:

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>Identifizieren der 10 häufigsten aktiven Speicherzuweisungen

Verwenden Sie die folgende Abfrage, um die 10 häufigsten aktiven Speicherzuweisungen zu identifizieren:

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>Berechnen von Datenbank- und Objektgrößen

Die folgende Abfrage gibt die Größe Ihrer Datenbank (in Megabyte) zurück:

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

Die folgende Abfrage gibt die Größe der einzelnen Objekte in Ihrer Datenbank in Megabyte zurück:

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Überwachen von Verbindungen

Sie können mithilfe der Sicht [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql) Informationen über Verbindungen mit einem bestimmten Server und einer verwalteten Instanz abrufen und Details zu jeder Verbindungen anzeigen. Darüber hinaus ist die Sicht [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) für das Abrufen von Informationen zu allen aktiven Benutzerverbindungen und internen Aufgaben nützlich.

Verwenden Sie die folgende Abfrage zum Abrufen der Informationen zur aktuellen Verbindung:

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> Wenn Sie **sys.dm_exec_requests** und **sys.dm_exec_sessions views** ausführen und die Berechtigung **VIEW DATABASE STATE** für die Datenbank besitzen, werden Ihnen alle zurzeit ausgeführten Sitzungen in der Datenbank angezeigt. Andernfalls wird Ihnen nur die aktuelle Sitzung angezeigt.

## <a name="monitor-resource-use"></a>Überwachen der Ressourcennutzung

Sie können die Azure SQL-Datenbank-Ressourcennutzung mithilfe von [Query Performance Insight für SQL-Datenbank](query-performance-insight-use.md) überwachen. Für Azure SQL-Datenbank und Azure SQL Managed Instance können Sie die Überwachung mit dem [Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) durchführen.

Außerdem lässt sich die Nutzung über diese Sichten überwachen:

- Azure SQL-Datenbank: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- Azure SQL Managed Instance: [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database)
- Azure SQL-Datenbank und Azure SQL Managed Instance: [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)

### <a name="sysdm_db_resource_stats"></a>sys.dm_db_resource_stats

Sie können die Sicht [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) in jeder Datenbank verwenden. Die Sicht **sys.dm_db_resource_stats** enthält Daten zur Ressourcennutzung in der letzten Zeit relativ zur Dienstebene. Durchschnittliche Prozentsätze für CPU, Dateneingang/-ausgang, Protokollschreibvorgänge und Arbeitsspeicher werden alle 15 Sekunden aufgezeichnet und eine Stunde lang aufbewahrt.

Da diese Ansicht eine detailliertere Darstellung der Ressourcennutzung ist, sollten Sie für alle Analysen des aktuellen Zustands oder für die Problembehandlung zuerst **sys.dm_db_resource_stats** verwenden. Mit dieser Abfrage wird beispielsweise die durchschnittliche und maximale Ressourcennutzung für die aktuelle Datenbank in der letzten Stunde angezeigt:

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

Beispiele für andere Abfragen finden Sie unter [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).

### <a name="sysserver_resource_stats"></a>sys.server_resource_stats

Sie können [sys.server_resource_stats](/sql/relational-databases/system-catalog-views/sys-server-resource-stats-azure-sql-database) verwenden, um CPU-Nutzung, E/A und Speicherdaten für eine Azure SQL Managed Instance-Instanz zurückzugeben. Die Daten werden in Intervallen von fünf Minuten gesammelt und aggregiert. Für jede 15-Sekunden-Berichterstattung ist eine Zeile vorhanden. Die zurückgegebenen Daten enthalten Informationen zu CPU-Nutzung, Speichergröße, E/A-Auslastung und SKU der verwalteten Instanz. Verlaufsdaten werden ungefähr 14 Tage lang beibehalten.

```sql
DECLARE @s datetime;  
DECLARE @e datetime;  
SET @s= DateAdd(d,-7,GetUTCDate());  
SET @e= GETUTCDATE();  
SELECT resource_name, AVG(avg_cpu_percent) AS Average_Compute_Utilization
FROM sys.server_resource_stats
WHERE start_time BETWEEN @s AND @e  
GROUP BY resource_name  
HAVING AVG(avg_cpu_percent) >= 80
```

### <a name="sysresource_stats"></a>sys.resource_stats

Die Sicht [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) in der **master**-Datenbank umfasst zusätzliche Informationen, die beim Überwachen der Leistung Ihrer Datenbank innerhalb der jeweiligen Dienstebene und Computegröße nützlich sind. Die Daten werden alle fünf Minuten gesammelt und c.a. 14 Tage lang aufbewahrt. Diese Sicht ist für eine längere Verlaufsanalyse der Ressourcennutzung Ihrer Datenbank hilfreich.

Der folgende Graph zeigt die CPU-Ressourcennutzung für eine Premium-Datenbank mit der Computegröße P2 für jede Stunde einer Woche. Dieser Graph beginnt mit einem Montag und zeigt fünf Arbeitstage und dann das Wochenende, an dem die Anwendung deutlich weniger gefragt ist.

![Nutzung der Datenbankressource](./media/monitoring-with-dmvs/sql_db_resource_utilization.png)

Die Daten verdeutlichen, dass diese Datenbank derzeit über eine CPU-Spitzenlast von etwas mehr als 50 Prozent CPU-Auslastung in Bezug zur Computegröße P2 verfügt (Dienstagmittag). Falls die CPU der entscheidende Faktor im Ressourcenprofil der Anwendung ist, entscheiden Sie sich ggf. für die Computegröße P2, um die Bewältigung der Workload stets sicherstellen zu können. Wenn eine Anwendung im Laufe der Zeit voraussichtlich größer wird, ist die Verwendung eines zusätzlichen Ressourcenpuffers ratsam, damit für die Anwendung nie der Grenzwert für die Leistungsebene erreicht wird. Wenn Sie die Computegröße erhöhen, können Sie für den Kunden sichtbare Fehler vermeiden. Diese können ggf. auftreten, wenn eine Datenbank nicht über genügend Leistung zum effektiven Verarbeiten von Anforderungen verfügt, vor allem in latenzsensiblen Umgebungen. Ein Beispiel ist eine Datenbank für eine Anwendung, mit der Webseiten basierend auf den Ergebnissen von Datenbankaufrufen farbig gestaltet werden.

Andere Anwendungstypen können denselben Graphen unter Umständen anders interpretieren. Wenn eine Anwendung beispielsweise jeden Tag versucht, Gehaltsabrechnungsdaten zu verarbeiten, und dasselbe Diagramm gilt, wird diese Art von „Batchauftrag“-Modell bei der Computegröße P1 wahrscheinlich zufriedenstellend ausgeführt. Computegröße P1 verfügt über 100 DTUs im Vergleich zu 200 DTUs bei Computegröße P2. Computegröße P1 stellt gegenüber Computegröße P2 die halbe Leistungsfähigkeit bereit. Eine Nutzung von 50 Prozent CPU-Auslastung bei P2 entspricht also 100 Prozent CPU-Auslastung bei P1. Wenn die Anwendung nicht über Timeouts verfügt, spielt es unter Umständen keine Rolle, ob ein Auftrag 2 oder 2,5 Stunden dauert, solange er noch am selben Tag abgeschlossen wird. Für eine Anwendung in dieser Kategorie reicht wahrscheinlich die Computegröße P1 aus. Sie können die Tatsache nutzen, dass es am Tag Zeiten gibt, in denen die Ressourcennutzung niedriger ist. Dies bedeutet, dass „Spitzen“ ggf. in einen der Zeiträume später am Tag verlagert werden können. Die Computegröße P1 ist für diese Art von Anwendung ggf. gut geeignet (und spart Kosten), solange die Aufträge jeden Tag pünktlich abgeschlossen werden können.

Die Datenbank-Engine macht die Informationen zum Ressourcenverbrauch für jede aktive Datenbank in der Sicht **sys.resource_stats** der **master**-Datenbank jedes Servers verfügbar. Die Daten in der Tabelle werden zu Intervallen von fünf Minuten zusammengefasst. Bei den Dienstebenen „Basic“, „Standard“ und „Premium“ kann es länger als fünf Minuten dauern, bis sie in der Tabelle angezeigt werden. Diese Daten sind also besser für Verlaufsanalysen als für Analysen nahezu in Echtzeit geeignet. Fragen Sie die Sicht **sys.resource_stats** ab, um den kürzlichen Verlauf einer Datenbank anzuzeigen und zu überprüfen, ob die gewählte Reservierung zur gewünschten Leistung zur richtigen Zeit geführt hat.

> [!NOTE]
> In Azure SQL-Datenbank ist eine Verbindung mit der **master**-Datenbank erforderlich, um **sys.resource_stats** in den folgenden Beispielen abzufragen.

Dieses Beispiel veranschaulicht, wie die Daten in dieser Sicht verfügbar gemacht werden:

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Katalogsicht „sys.resource_stats“](./media/monitoring-with-dmvs/sys_resource_stats.png)

Das nächste Beispiel zeigt verschiedene Möglichkeiten zur Verwendung der **sys.resource_stats**-Katalogsicht, um Informationen zur Ressourcennutzung durch Ihre Datenbank abzurufen:

1. Sie können diese Abfrage ausführen, um die Ressourcennutzung der letzten Woche für die Datenbank „userdb1“ anzuzeigen:

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. Um auszuwerten, wie gut Ihre Workload zur Computegröße passt, müssen Sie die einzelnen Aspekte der Ressourcenmetriken untersuchen: CPU, Lesevorgänge, Schreibvorgänge, Anzahl von Workern und Anzahl von Sitzungen. Hier wird von einer überarbeiteten Abfrage **sys.resource_stats** verwendet, um den Durchschnitt und die Höchstwerte dieser Ressourcenmetriken zu melden:

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. Mit diesen Informationen zu den Durchschnitts- und Höchstwerten der einzelnen Ressourcenmetriken können Sie bewerten, wie gut Ihre Workload zur ausgewählten Computegröße passt. Normalerweise erhalten Sie mit den Durchschnittswerten aus **sys.resource_stats** eine gute Grundlage gegenüber der Zielgröße. Dies sollte Ihre primäre Messlatte sein. Beispielsweise können Sie die Standard-Dienstebene mit der Computegröße S2 verwenden. Die durchschnittlichen Nutzungsprozentsätze für Lese- und -Schreibvorgänge für CPU und E/A liegen unter 40 Prozent, die durchschnittliche Anzahl von Workern unter 50 und die durchschnittliche Sitzungsanzahl unter 200. Für diese Workload ist unter Umständen die Computegröße S1 geeignet. Es ist leicht zu erkennen, ob Ihre Datenbank die Grenzen für Worker und Sitzungen einhält. Um zu ermitteln, ob sich für eine Datenbank in Bezug auf CPU, Lesevorgänge und Schreibvorgänge eine niedrigere Computegröße eignet, dividieren Sie die DTU-Anzahl der niedrigeren Computegröße durch die DTU-Anzahl der aktuellen Computegröße, und multiplizieren Sie dann das Ergebnis mit 100:

    `S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40`

    Als Ergebnis erhalten Sie den relativen Leistungsunterschied zwischen den beiden Computegrößen in Prozent. Wenn die Ressourcennutzung diesen Betrag nicht überschreitet, kann für Ihre Workload ggf. die niedrigere Computegröße geeignet sein. Sie sollten sich aber alle Bereiche der Ressourcennutzungswerte ansehen und anhand des Prozentsatzes ermitteln, wie oft für Ihre Datenbankworkload die niedrigere Computegröße geeignet wäre. Mit der folgenden Abfrage wird der Prozentsatz für die Eignung pro Ressourcendimension basierend auf dem in diesem Beispiel berechneten Schwellenwert von 40% ausgegeben:

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Auf Basis Ihrer Dienstebene für die Datenbank können Sie entscheiden, ob für Ihre Workload die niedrigere Computegröße geeignet ist. Wenn das Ziel für die Datenbankworkload 99,9 Prozent beträgt und die obige Abfrage höhere Werte als 99,9 Prozent für alle drei Ressourcendimensionen zurückgibt, ist die Wahrscheinlichkeit hoch, dass sich die niedrigere Computegröße für Ihre Workload eignet.

    Wenn Sie sich den Prozentsatz für die Eignung ansehen, erhalten Sie auch Informationen dazu, ob Sie zur nächsthöheren Computegröße wechseln müssen, um das Ziel zu erreichen. „userdb1“ weist beispielsweise die folgende CPU-Auslastung für die letzte Woche auf:

   | Durchschnittlicher CPU-Prozentwert | Maximaler CPU-Prozentwert |
   | --- | --- |
   | 24,5 |100,00 |

    Der durchschnittliche CPU-Wert beträgt ca. ein Viertel der Obergrenze der Computegröße. Dies würde also gut zur Computegröße der Datenbank passen. Der Höchstwert zeigt aber, dass die Datenbank die Obergrenze der Computegröße erreicht. Müssen Sie also zur nächsthöheren Computegröße wechseln? Prüfen Sie, wie häufig Ihre Workload 100 Prozent erreicht, und vergleichen Sie dies dann mit Ihrem Ziel für die Datenbankworkload.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    Wenn diese Abfrage für eine der drei Ressourcendimensionen einen Wert unterhalb von 99,9 Prozent zurückgibt, können Sie entweder die Umstellung auf die nächsthöhere Computegröße erwägen oder Verfahren zur Anwendungsoptimierung nutzen, um die Last für die Datenbank zu reduzieren.

4. Bei dieser Übung wird auch die projizierte zukünftige Zunahme der Workload berücksichtigt.

In Pools für elastische Datenbanken können Sie einzelne Datenbanken mit den in diesem Abschnitt beschriebenen Methoden überwachen. Sie können den Pool jedoch auch als Ganzes überwachen. Informationen dazu finden Sie unter [Überwachen und Verwalten von Pools für elastische Datenbanken](elastic-pool-overview.md).

### <a name="maximum-concurrent-requests"></a>Maximale Anzahl von gleichzeitigen Anforderungen

Um die Anzahl gleichzeitiger Anforderungen anzuzeigen, führen diese Transact-SQL-Abfrage in Ihrer Datenbank aus:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R;
```

Um die Workload einer SQL Server-Datenbank zu analysieren, ändern Sie diese Abfrage so ab, dass nach der zu analysierenden Datenbank gefiltert wird. Wenn Sie beispielsweise über eine lokale Datenbank mit dem Namen „MyDatabase“ verfügen, wird mit dieser Transact-SQL-Abfrage die Anzahl von gleichzeitigen Abfragen in dieser Datenbank zurückgegeben:

```sql
SELECT COUNT(*) AS [Concurrent_Requests]
FROM sys.dm_exec_requests R
INNER JOIN sys.databases D ON D.database_id = R.database_id
AND D.name = 'MyDatabase';
```

Dies ist nur eine Momentaufnahme zu einem bestimmten Zeitpunkt. Um ein besseres Verständnis Ihrer Workload und der Anforderungen an gleichzeitige Anforderungen zu entwickeln, müssten Sie im Laufe der Zeit viele Beispiele sammeln.

### <a name="maximum-concurrent-logins"></a>Maximale Anzahl von gleichzeitigen Anmeldungen

Sie können Ihre Benutzer- und Anmeldungsmuster analysieren, um Informationen zur Häufigkeit der Anmeldungen zu erhalten. Sie können auch reale Auslastungen in einer Testumgebung ausführen, um sicherzustellen, dass diese oder andere in diesem Artikel beschriebene Grenzwerte nicht erreicht oder überschritten werden. Es gibt keine einzelne Abfrage oder dynamische Verwaltungssicht (DMV), mit der Sie die Anzahl von gleichzeitigen Anmeldungen oder den Verlauf anzeigen können.

Der Dienst authentifiziert jede Anmeldung, wenn mehrere Clients die gleiche Verbindungszeichenfolge verwenden. Wenn zehn Benutzer gleichzeitig mit dem gleichen Benutzernamen und Kennwort mit einer Datenbank verbunden sind, wären dies also zehn gleichzeitige Anmeldungen. Diese Obergrenze gilt nur für die Dauer der Anmeldung und Authentifizierung. Wenn sich die gleichen zehn Benutzer nacheinander an der Datenbank anmelden, wäre die Anzahl von gleichzeitigen Anmeldungen niemals höher als 1.

> [!NOTE]
> Diese Beschränkung gilt derzeit nicht für Datenbanken in Pools für elastische Datenbanken.

### <a name="maximum-sessions"></a>Maximale Anzahl von Sitzungen

Um die Anzahl aktueller aktiver Sitzungen anzuzeigen, führen Sie diese Transact-SQL-Abfrage in Ihrer Datenbank aus:

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections
```

Wenn Sie die SQL Server-Workload analysieren, ändern Sie die Abfrage für eine spezifische Datenbank ab. Diese Abfrage ist zum Ermitteln der möglichen Sitzungsanforderungen für die Datenbank hilfreich, wenn Sie eine Umstellung auf Azure erwägen.

```sql
SELECT COUNT(*) AS [Sessions]
FROM sys.dm_exec_connections C
INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
INNER JOIN sys.databases D ON (D.database_id = S.database_id)
WHERE D.name = 'MyDatabase'
```

Diese Abfragen geben wieder eine Anzahl zu einem bestimmten Zeitpunkt zurück. Wenn Sie im Laufe der Zeit mehrere Beispielwerte sammeln, können Sie sich am besten über Ihre Sitzungsnutzung informieren.

Sie können Verlaufsstatistiken für Sitzungen abrufen, indem Sie [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) abfragen und die Spalte **active_session_count** überprüfen.

## <a name="monitoring-query-performance"></a>Überwachen der Abfrageleistung

Langsame Abfragen oder Abfragen mit langen Ausführungszeiten können beträchtliche Systemressourcen beanspruchen. In diesem Abschnitt wird veranschaulicht, wie mit dynamischen Verwaltungssichten häufig auftretende Leistungsprobleme bei Abfragen ermittelt werden können.

### <a name="finding-top-n-queries"></a>Suchen der ersten N Abfragen

Das folgende Beispiel gibt Informationen über die „Top-Fünf“-Abfragen gemessen an durchschnittlicher CPU-Zeit zurück. In diesem Beispiel werden die Abfragen entsprechend ihrem Abfragenhash zusammengefasst, sodass logisch äquivalente Abfragen nach ihrem kumulativen Ressourcenverbrauch gruppiert werden.

```sql
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
     MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
FROM sys.dm_exec_query_stats AS QS
CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### <a name="monitoring-blocked-queries"></a>Überwachen blockierter Abfragen

Langsame Abfragen oder Abfragen mit langer Laufzeit können zu einer übermäßigen Ressourcennutzung beitragen und auf blockierte Abfragen zurückzuführen sein. Ursache für das Blockieren kann ein mangelhafter Anwendungsentwurf, fehlerhafte Abfragepläne oder ein Mangel an nützlichen Indizes usw. sein. Verwenden Sie die Sicht „sys.dm_tran_locks“, um Informationen über die aktuellen Sperraktivitäten in der Datenbank abzurufen. Beispielcode finden Sie unter [sys.dm_tran_locks (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

### <a name="monitoring-query-plans"></a>Überwachen von Abfrageplänen

Auch ein ineffizienter Abfrageplan kann die CPU-Auslastung erhöhen. Im folgenden Beispiel wird die Sicht [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql) verwendet, um festzustellen, welche Abfrage die CPU kumulativ am meisten auslastet.

```sql
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Weitere Informationen

[Einführung in Azure SQL-Datenbank und Azure SQL Managed Instance](sql-database-paas-overview.md)