---
title: Überwachen der Workload Ihres dedizierten SQL-Pools mit DMVs
description: Hier erfahren Sie, wie Sie die Workload Ihres dedizierten SQL-Pools von Azure Synapse Analytics und die Abfrageausführung mit DMVs überwachen können.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 1992c3d525fc1f5a098e1969887a752233d47990
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453808"
---
# <a name="monitor-your-azure-synapse-analytics-dedicated-sql-pool-workload-using-dmvs"></a>Überwachen der Workload Ihres dedizierten SQL-Pools von Azure Synapse Analytics mit DMVs

In diesem Artikel wird beschrieben, wie Sie Ihre Workload mit dynamischen Verwaltungssichten (Dynamic Management Views, DMVs) überwachen können, einschließlich der Untersuchung von Abfrageausführungen im SQL-Pool.

## <a name="permissions"></a>Berechtigungen

Zum Abfragen der DMVs in diesem Artikel benötigen Sie die Berechtigung **VIEW DATABASE STATE** oder **CONTROL**. Normalerweise ist **VIEW DATABASE STATE** die bevorzugte Berechtigung, da sie wesentlich restriktiver ist.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Überwachen von Verbindungen

Alle Anmeldungen bei Ihrem Data Warehouse werden in [sys.dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) protokolliert.  Diese DMV enthält die letzten 10.000 Anmeldungen.  Die Sitzungs-ID ist der Primärschlüssel und wird bei jeder neuen Anmeldung sequenziell zugewiesen.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Überwachen der Abfrageausführung

Alle im SQL-Pool ausgeführten Abfragen werden in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) protokolliert.  Diese DMV enthält die letzten 10.000 ausgeführten Abfragen.  Die Anforderungs-ID identifiziert jede Abfrage eindeutig. Sie ist der Primärschlüssel für diese DMV.  Die Anforderungs-ID wird für jede neue Abfrage sequenziell zugewiesen und erhält das Präfix QID für Abfrage-ID.  Bei der Abfrage dieser DMV für eine bestimmte Sitzungs-ID werden alle Abfragen für eine bestimmte Anmeldung angezeigt.

> [!NOTE]
> Gespeicherte Prozeduren verwenden mehrere Anforderungs-IDs.  Anforderungs-IDs werden in sequenzieller Reihenfolge zugewiesen.

Führen Sie folgende Schritte aus, um Abfrageausführungspläne und -zeiten für eine bestimmte Abfrage zu untersuchen.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>SCHRITT 1: Ermitteln der Abfrage, die Sie untersuchen möchten

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

Notieren Sie sich aus den oben stehenden Abfrageergebnissen **die Anforderungs-ID** der Abfrage, die Sie untersuchen möchten.

Abfragen im Zustand **Angehalten** können aufgrund einer großen Anzahl aktiv ausgeführter Abfragen in eine Warteschlange gestellt werden. Diese Abfragen werden auch in der Abfrage [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) mit dem Typ UserConcurrencyResourceType angezeigt. Informationen zu Parallelitätsgrenzwerten finden Sie unter [Speicher- und Parallelitätsgrenzwerte](memory-concurrency-limits.md) oder [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md). Abfragen können auch aus anderen Gründen warten, beispielsweise wegen Objektsperren.  Wenn Ihre Abfrage auf eine Ressource wartet, finden Sie nähere Informationen unter [Untersuchen von Anfragen, die auf Ressourcen warten](#monitor-waiting-queries) weiter unten in diesem Artikel.

Vereinfachen Sie die Suche nach einer Abfrage in der Tabelle [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) mithilfe von [LABEL](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), um Ihrer Abfrage einen Kommentar hinzuzufügen, der in der Ansicht „sys.dm_pdw_exec_requests“ gesucht werden kann.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>SCHRITT 2: Untersuchen des Abfrageplans

Rufen Sie mit der Anforderungs-ID den DSQL-Plan (Distributed SQL, verteiltes SQL) der Abfrage aus [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ab.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Wenn ein DSQL-Plan mehr Zeit in Anspruch nimmt als erwartet, kann die Ursache ein komplexer Plan mit vielen DSQL-Schritten oder nur ein einziger Schritt sein, der einen langen Zeitraum benötigt.  Wenn der Plan viele Schritte mit mehreren Verschiebungen aufweist, erwägen Sie die Optimierung Ihrer Tabellenverteilungen, um Datenverschiebungen zu reduzieren. Im Artikel [Tabellenverteilung](sql-data-warehouse-tables-distribute.md) wird erläutert, warum Daten verschoben werden müssen, um eine Abfrage zu lösen. Außerdem werden in dem Artikel einige Verteilungsstrategien zum Minimieren der Datenverschiebung erläutert.

Um weitere Informationen zu einem Einzelschritt zu erhalten, beachten Sie die Spalte *operation_type* des Abfrageschritts mit langer Laufzeit, und beachten Sie den **Schrittindex**:

* Fahren Sie für **SQL-Vorgänge** mit Schritt 3 fort: OnOperation, RemoteOperation, ReturnOperation.
* Fahren Sie für **Datenverschiebungsvorgänge** mit Schritt 4 fort: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>SCHRITT 3: Untersuchen von SQL auf den verteilten Datenbanken

Verwenden Sie die Anforderungs-ID und den Schrittindex, um Informationen aus [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) abzurufen. Das Ergebnis enthält Informationen zur Ausführung des Abfrageschritts in allen verteilten Datenbanken.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Wenn der Abfrageschritt ausgeführt wird, können Sie mit [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aus dem Cache des SQL Server-Plans den berechneten SQL Server-Ausführungsplan für den in einer bestimmten Verteilung ausgeführten Schritt abrufen.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>SCHRITT 4: Untersuchen der Datenverschiebung auf den verteilten Datenbanken

Verwenden Sie die Anforderungs-ID und den Schrittindex, um Informationen zu einem Datenverschiebungsschritt, der für jede Verteilung ausgeführt wird, aus [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) abzurufen.

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* Überprüfen Sie die Spalte *total_elapsed_time*, um festzustellen, ob das Verschieben von Daten in einer bestimmten Verteilung erheblich länger dauert als in anderen Verteilungen.
* Überprüfen Sie für die Verteilung mit langer Laufzeit die Spalte *rows_processed*, um festzustellen, ob die Anzahl der Zeilen, die von dieser Verteilung verschoben werden, beträchtlich größer als bei den anderen ist. Falls ja, kann dies auf eine Ungleichmäßigkeit der zugrunde liegenden Daten hinweisen. Eine Ursache für Datenschiefe ist die Verteilung einer Spalte mit vielen NULL-Werten (deren Zeilen alle in dieselbe Verteilung eingefügt werden). Sie vermeiden langsame Abfragen, indem Sie die Verteilung dieser Spaltentypen vermeiden oder indem Sie die Abfrage nach Möglichkeit filtern, um NULL-Werte auszuschließen. 

Wird die Abfrage gerade ausgeführt, können Sie mit [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aus dem Cache des SQL Server-Plans den berechneten SQL Server-Ausführungsplan für den derzeit ausgeführten SQL-Schritt innerhalb einer bestimmten Verteilung abrufen.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>Überwachen von wartenden Abfragen

Wenn Sie feststellen, dass Ihre Abfrage keine Fortschritte erzielt, weil sie auf eine Ressource wartet, können Sie mit folgender Abfrage alle Ressourcen anzeigen, auf die eine Abfrage wartet.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Wenn die Abfrage aktiv auf Ressourcen einer anderen Abfrage wartet, lautet der Status **AcquireResources**.  Wenn die Abfrage über alle erforderlichen Ressourcen verfügt, ist der Status **Granted**.

## <a name="monitor-tempdb"></a>Überwachen von tempdb

tempdb wird zum Speichern von Zwischenergebnissen während der Abfrageausführung verwendet. Eine hohe Auslastung der tempdb-Datenbank kann zu einer schwachen Abfrageleistung führen. Für jede konfigurierte DW100c werden 399 GB Speicherplatz für tempdb zugewiesen (DW1000c bietet insgesamt 3,99 TB Speicherplatz für tempdb).  Nachstehend finden Sie Tipps zur Überwachung der tempdb-Auslastung und zur Verringerung der tempdb-Auslastung in Ihren Abfragen.

### <a name="monitoring-tempdb-with-views"></a>Überwachen von tempdb mit Ansichten

Wenn Sie die tempdb-Auslastung überwachen möchten, installieren Sie zuerst die Ansicht [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) aus dem [Microsoft-Toolkit für SQL-Pool](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring). Anschließend können Sie die folgende Abfrage ausführen, um die tempdb-Auslastung pro Knoten für alle ausgeführten Abfragen anzuzeigen:

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

Wenn Sie eine Abfrage haben, die eine große Menge an Arbeitsspeicher verbraucht, oder wenn eine Fehlermeldung im Zusammenhang mit der Zuordnung von tempdb angezeigt wird, kann dies auf der Ausführung einer sehr umfangreichen [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)- oder [INSERT SELECT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)-Anweisung beruhen, bei der im letzten Datenverschiebungsvorgang ein Fehler aufgetreten ist. Dies kann im Plan für verteilte Abfragen normalerweise als „ShuffleMove“-Vorgang direkt vor der letzten INSERT SELECT-Anweisung identifiziert werden.  Verwenden Sie [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) zum Überwachen von ShuffleMove-Vorgängen.

Die häufigste Minderung besteht darin, Ihre CTAS- oder INSERT SELECT-Anweisung in mehrere Load-Anweisungen aufzuteilen, damit das Datenvolumen das tempdb-Limit von 1TB pro Knoten nicht überschreitet. Sie können auch Ihren Cluster auf eine größere Größe skalieren. Dadurch wird die tempdb-Größe über mehrere Knoten verteilt und so die tempdb auf jedem einzelnen Knoten verkleinert.

Zusätzlich zu den CTAS- und INSERT SELECT-Anweisungen können große, komplexe Abfragen, die mit unzureichendem Speicher ausgeführt werden, in tempdb gelangen, wodurch Abfragen fehlschlagen.  Ziehen Sie die Ausführung mit einer größeren [Ressourcenklasse](resource-classes-for-workload-management.md) in Betracht, um tempdb zu entlasten.

## <a name="monitor-memory"></a>Überwachen des Arbeitsspeichers

Der Arbeitsspeicher kann die Hauptursache für Probleme in Verbindung mit geringer Leistung und unzureichendem Arbeitsspeicher sein. Ziehen Sie die Skalierung Ihres Data Warehouse in Betracht, wenn Sie feststellen, dass die Speicherauslastung von SQL Server beim Ausführen der Abfrage die Grenzwerte erreicht.

Die folgende Abfrage gibt die Speicherauslastung von SQL Server und die Speicherauslastung pro Knoten zurück:

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>Überwachen der Größe von Transaktionsprotokollen

Die folgende Abfrage gibt die Größe von Transaktionsprotokollen für jede Verteilung zurück. Wenn eine der Protokolldateien 160 GB erreicht, sollten Sie Ihre Instanz eventuell zentral hochskalieren oder die Transaktionsgröße beschränken.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>Überwachen des Rollbacks von Transaktionsprotokollen

Wenn bei Ihren Abfragen Fehler auftreten oder deren Verarbeitung sehr lange dauert, können Sie überprüfen und überwachen, ob Sie über Rollbacks von Transaktionen verfügen.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>Überwachen der PolyBase-Last

Die folgende Abfrage stellt eine ungefähre Schätzung des Fortschritts Ihrer Last bereit. Die Abfrage zeigt nur Dateien an, die zurzeit verarbeitet werden.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu DMVs finden Sie unter [Systemsichten](../sql/reference-tsql-system-views.md).
