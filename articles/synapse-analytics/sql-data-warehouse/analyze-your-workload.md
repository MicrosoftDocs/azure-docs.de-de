---
title: Analysieren Ihrer Workload für dedizierte SQL-Pools
description: Lernen Sie Techniken zum Analysieren von Abfrageprioritäten für dedizierte SQL-Pools in Azure Synapse Analytics kennen.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 14c3ad30bac7cec4c11822d825323bb9db2ba440
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454526"
---
# <a name="analyze-your-workload-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Analysieren Ihrer Workload für dedizierte SQL-Pools in Azure Synapse Analytics

Lernen Sie Techniken zum Analysieren Ihrer Workload für dedizierte SQL-Pools in Azure Synapse Analytics kennen. 

## <a name="resource-classes"></a>Ressourcenklassen

Ein dedizierter SQL-Pool stellt Ressourcenklassen zum Zuweisen von Systemressourcen zu Abfragen bereit.  Weitere Informationen zu Ressourcenklassen finden Sie unter [Ressourcenklassen und Workloadverwaltung](resource-classes-for-workload-management.md).  Abfragen warten, wenn die einer Abfrage zugeordnete Ressourcenklasse mehr Ressourcen benötigt, als zurzeit verfügbar sind.

## <a name="queued-query-detection-and-other-dmvs"></a>Erkennung von Abfragen in der Warteschlange und andere DMVs

Sie können die DMV `sys.dm_pdw_exec_requests` verwenden, um Abfragen zu identifizieren, die in eine Parallelitätswarteschlange eingereiht wurden. Abfragen, die auf einen Parallelitätsslot warten, weisen den Status **Angehalten** auf.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Workloadverwaltungsrollen können mit `sys.database_principals`angezeigt werden.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Mit der folgenden Abfrage wird angezeigt, welcher Rolle die einzelnen Benutzer zugewiesen sind.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Ein dedizierter SQL-Pool hat die folgenden Wartetypen:

* **LocalQueriesConcurrencyResourceType:** Bezieht sich auf Abfragen, die sich außerhalb des Parallelitätsslot-Frameworks befinden. DMV-Abfragen und Systemfunktionen wie `SELECT @@VERSION` sind Beispiele für lokale Abfragen.
* **UserConcurrencyResourceType:** Bezieht sich auf Abfragen, die sich innerhalb des Parallelitätsslot-Frameworks befinden. Abfragen für Endbenutzertabellen sind ein Beispiel für die Verwendung dieses Ressourcentyps.
* **DmsConcurrencyResourceType:** Bezieht sich auf Wartezeiten, die sich aufgrund von Datenverschiebungen ergeben.
* **BackupConcurrencyResourceType:** Diese Wartezeit gibt an, dass eine Datenbank gesichert wird. Der maximale Wert für diesen Ressourcentyp ist 1. Wenn mehrere Sicherungen gleichzeitig angefordert wurden, werden die restlichen Sicherungen in die Warteschlange eingereiht. Im Allgemeinen sollten zwischen aufeinanderfolgenden Momentaufnahmen mindestens 10 Minuten verstreichen.

Mit der DMV `sys.dm_pdw_waits` kann angezeigt werden, auf welche Ressourcen eine Abfrage wartet.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

Die `sys.dm_pdw_resource_waits`-DMV zeigt die Warteinformationen für eine bestimmte Abfrage an. Die Ressourcenwartezeit misst die Zeit, die auf die Bereitstellung von Ressourcen gewartet wird. Die Signalwartezeit ist die Zeit, die die zugrunde liegenden SQL-Server benötigen, um die Abfrage auf der CPU zu planen.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

Sie können auch die DMV `sys.dm_pdw_resource_waits` verwenden, um die Anzahl der gewährten Parallelitätsslots zu berechnen.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

Die DMV `sys.dm_pdw_wait_stats` kann für Verlaufsanalysen von Wartezeiten verwendet werden.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten von Datenbankbenutzern und der Sicherheit finden Sie unter [Sichern eines dedizierten SQL-Pools (früher SQL DW)](sql-data-warehouse-overview-manage-security.md). Weitere Informationen dazu, wie größere Ressourcenklassen die Qualität von gruppierten Columnstore-Indizes verbessern können, finden Sie unter [Neuerstellen von Indizes zur Verbesserung der Segmentqualität](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).
