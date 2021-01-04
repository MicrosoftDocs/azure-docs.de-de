---
title: Erstellen und Verwalten von Aufträgen für die elastische Datenbank (Vorschau) mit Transact-SQL (T-SQL)
description: Führen Sie Skripts für eine Vielzahl von Datenbanken mit dem Agent für Aufträge für die elastische Datenbank mit Transact-SQL (T-SQL) aus.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
ms.date: 02/07/2020
ms.openlocfilehash: 9c9f5972cdd2690b86610ea585bdd82d736ed163
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792139"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs-preview"></a>Verwenden von Transact-SQL (T-SQL) zum Erstellen und Verwalten von Aufträgen für die elastische Datenbank (Vorschau)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält eine Vielzahl von Beispielszenarien für die ersten Schritte bei der Arbeit mit Aufträgen für die elastische Datenbank mit T-SQL.

In den Beispielen werden die [gespeicherten Prozeduren](#job-stored-procedures) und [Ansichten](#job-views) verwendet, die in der [*Auftragsdatenbank*](job-automation-overview.md#job-database) verfügbar sind.

Transact-SQL (T-SQL) dient zum Erstellen, Konfigurieren, Ausführen und Verwalten von Aufträgen. Die Erstellung des Agents für Aufträge für die elastische Datenbank wird nicht in T-SQL unterstützt. Daher müssen Sie mithilfe des Portals oder mit [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent) zuerst einen *Agent für Aufträge für die elastische Datenbank* erstellen.

## <a name="create-a-credential-for-job-execution"></a>Erstellen von Anmeldeinformationen für die Auftragsausführung

Die Anmeldeinformationen werden verwendet, um eine Verbindung mit Ihren Zieldatenbanken für die Skriptausführung herzustellen. Für die Anmeldeinformationen müssen entsprechende Berechtigungen für die von der Zielgruppe angegebenen Datenbanken vorliegen, damit das Skript erfolgreich ausgeführt werden kann. Wenn Sie einen [logischen SQL-Server](logical-servers.md) bzw. ein Zielgruppenmitglied eines Pools einsetzen, sollten Sie auf jeden Fall Master-Anmeldeinformationen erstellen, die zum Aktualisieren der Anmeldeinformationen verwendet werden, bevor Sie den Server oder den Pool bei der Auftragsausführung erweitern. Die datenbankweit gültigen Anmeldeinformationen werden für die Agent-Datenbank für Aufträge erstellt. Es müssen die gleichen Anmeldeinformationen zum *Erstellen eines Anmeldenamens* und zum *Erstellen eines Benutzers bei der Anmeldung für die Erteilung der Datenbankanmeldeberechtigungen* für die Zieldatenbanken verwendet werden.

```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>';
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>';
GO
```

## <a name="create-a-target-group-servers"></a>Erstellen einer Zielgruppe (Server)

Im folgenden Beispiel wird gezeigt, wie ein Auftrag für alle Datenbanken auf einem Server ausgeführt wird.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in a server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```

## <a name="exclude-an-individual-database"></a>Ausschließen einer einzelnen Datenbank

Im folgenden Beispiel wird veranschaulicht, wie ein Auftrag für alle Datenbanken auf einem Server ausgeführt wird, mit Ausnahme der Datenbank mit dem Namen *MappingDB* .  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in a server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in a server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```

## <a name="create-a-target-group-pools"></a>Erstellen einer Zielgruppe (Pools)

Das folgende Beispiel zeigt, wie Zielgruppen für alle Datenbanken in einem oder in mehreren Pools für elastische Datenbanken erstellt werden.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in a server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```

## <a name="deploy-new-schema-to-many-databases"></a>Bereitstellen eines neuen Schemas für eine Vielzahl von Datenbanken

Das folgende Beispiel zeigt, wie ein neues Schema für alle Datenbanken bereitgestellt wird.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```

## <a name="data-collection-using-built-in-parameters"></a>Datensammlung mithilfe von integrierten Parametern

In vielen Datensammlungsszenarien kann es sinnvoll sein, einige dieser Skriptvariablen einzuschließen, um die Ergebnisse des Auftrags nachzuverarbeiten.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

Um beispielsweise alle Ergebnisse aus derselben Auftragsausführung zusammen zu gruppieren, verwenden Sie *$(job_execution_id)* wie im folgenden Befehl gezeigt wird:

```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```

## <a name="monitor-database-performance"></a>Überwachen der Datenbankleistung

Im folgenden Beispiel wird ein neuer Auftrag zum Sammeln von Leistungsdaten von mehreren Datenbanken erstellt.

Der Auftrags-Agent erstellt die Ausgabetabelle standardmäßig zum Speichern der zurückgegebenen Ergebnisse. Aus diesem Grund muss der Datenbankprinzipal, der mit den Anmeldeinformationen für die Ausgabe verknüpft ist, mindestens über die folgenden Berechtigungen verfügen: `CREATE TABLE` für die Datenbank, `ALTER`, `SELECT`, `INSERT` und `DELETE` für die Ausgabetabelle oder ihr Schema und `SELECT` für die [sys.indexes](/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)-Katalogsicht.

Wenn Sie die Tabelle vorher manuell erstellen möchten, muss sie die folgenden Eigenschaften aufweisen:

1. Spalten mit den richtigen Namen und Datentypen für das Resultset.
2. Zusätzliche Spalte für „internal_execution_id“ mit dem Datentyp „uniqueidentifier“.
3. Ein nicht gruppierter Index mit dem Namen „`IX_<TableName>_Internal_Execution_ID`“ für die Spalte „internal_execution_id“.
4. Alle oben aufgeführten Berechtigungen mit Ausnahme von `CREATE TABLE` für die Datenbank.

Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie die folgenden Befehle aus:

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```

## <a name="view-job-definitions"></a>Anzeigen von Auftragsdefinitionen

Das folgende Beispiel zeigt, wie aktuelle Auftragsdefinitionen angezeigt werden.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```

## <a name="begin-unplanned-execution-of-a-job"></a>Starten der nicht geplanten Ausführung eines Auftrags

Im folgenden Beispiel wird gezeigt, wie sofort ein Auftrag gestartet wird.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```

## <a name="schedule-execution-of-a-job"></a>Planen der Auftragsausführung

Im folgenden Beispiel wird gezeigt, wie Sie einen Auftrag für eine zukünftige Ausführung planen.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>Überwachen des Status einer Auftragsausführung

Das folgende Beispiel veranschaulicht, wie Ausführungsstatusdetails für alle Aufträge angezeigt werden.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions
WHERE job_name = 'ResultsPoolsJob'
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions
WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="cancel-a-job"></a>Abbrechen eines Auftrags

Das folgende Beispiel zeigt den Vorgang zum Abbrechen eines Auftrags.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```

## <a name="delete-old-job-history"></a>Löschen eines alten Auftragsverlaufs

Das folgende Beispiel zeigt, wie der Auftragsverlauf bis zu einem bestimmten Datum gelöscht wird.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>Löschen eines Auftrags sowie des zugehörigen Auftragsverlaufs

Das folgende Beispiel zeigt, wie ein Auftrag und alle zugehörigen Auftragsverläufe gelöscht werden.  
Stellen Sie eine Verbindung mit der [*Auftragsdatenbank*](job-automation-overview.md#job-database) her, und führen Sie den folgenden Befehl aus:

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="job-stored-procedures"></a>In der Auftragsdatenbank gespeicherte Prozeduren

Die folgenden gespeicherten Prozeduren sind in der [Auftragsdatenbank](job-automation-overview.md#job-database) enthalten.

|Gespeicherte Prozedur  |BESCHREIBUNG  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     Fügt einen neuen Auftrag hinzu.    |
|[sp_update_job](#sp_update_job)    |      Aktualisiert einen vorhandenen Auftrag.   |
|[sp_delete_job](#sp_delete_job)     |      Löscht einen vorhandenen Auftrag.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    Fügt einen Schritt zu einem Auftrag hinzu.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     Aktualisiert einen Auftragsschritt.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     Löscht einen Auftragsschritt.    |
|[sp_start_job](#sp_start_job)    |  Startet die Ausführung eines Auftrags.       |
|[sp_stop_job](#sp_stop_job)     |     Hält die Ausführung eines Auftrags an.   |
|[sp_add_target_group](#sp_add_target_group)    |     Fügt eine Zielgruppe hinzu.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    Löscht eine Zielgruppe.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    Fügt einer Zielgruppe eine Datenbank oder eine Gruppe von Datenbanken hinzu.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     Entfernt ein Zielgruppenmitglied aus einer Zielgruppe.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    Entfernt die Verlaufsdatensätze für einen Auftrag.     |

### <a name="sp_add_job"></a><a name="sp_add_job"></a>sp_add_job

Fügt einen neuen Auftrag hinzu.
  
#### <a name="syntax"></a>Syntax  
  
```syntaxsql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
  [ , [ @job_id = ] job_id OUTPUT ]
```

#### <a name="arguments"></a>Argumente  

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags. Der Name muss eindeutig sein und darf nicht das Prozentzeichen (%) enthalten. „job_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@description =** ] 'Beschreibung'  
Die Beschreibung des Auftrags. „description“ ist vom Datentyp „nvarchar(512)“ mit dem NULL-Standardwert. Wenn „description“ ausgelassen wird, wird eine leere Zeichenfolge verwendet.

[ **\@enabled =** ] enabled  
Gibt an, ob der Zeitplan des Auftrags aktiviert ist. „enabled“ ist vom Datentyp „bit“ mit dem Standardwert „0“ (deaktiviert). Wenn der Wert „0“ festgelegt ist, wird der Auftrag nicht aktiviert und nicht gemäß dem Zeitplan ausgeführt, er kann jedoch manuell ausgeführt werden. Wenn der Wert „1“ festgelegt ist, wird der Auftrag wird gemäß dem Zeitplan ausgeführt und kann auch manuell ausgeführt werden.

[ **\@Schedule_interval_type =** ] Schedule_interval_type  
Der Wert gibt an, wie oft der Auftrag ausgeführt werden soll. „schedule_interval_type“ ist vom Datentyp „nvarchar(50)“ mit dem Standardwert „Once“ und kann einen der folgenden Werte aufweisen:

- „Once“
- „Minutes“
- „Hours“
- „Days“
- „Weeks“
- „Months“

[ **\@schedule_interval_count =** ] schedule_interval_count  
Die Anzahl der „schedule_interval_count“-Intervalle, die zwischen den einzelnen Ausführungen des Auftrags stattfinden sollen. „schedule_interval_count“ ist vom Datentyp „int“ und weist den Standardwert „1“ auf. Der Wert muss größer als oder gleich „1“ sein.

[ **\@schedule_start_time =** ] schedule_start_time  
Das Datum, an dem Ausführung des Auftrags beginnen kann. „schedule_start_time“ ist vom Datentyp „DATETIME2“ und weist den Standardwert „00:00:00.0000000 0001-01-01“ auf.

[ **\@schedule_end_time =** ] schedule_end_time  
Das Datum, an dem die Ausführung des Auftrags beendet werden kann. „schedule_end_time“ ist vom Datentyp „DATETIME2“ und weist den Standardwert „9999-12-31 11:59:59.0000000“ auf.

[ **\@job_id =** ] job_id OUTPUT  
Die Auftrags-ID, die dem Auftrag zugewiesen wird, wenn er erfolgreich erstellt wurde. „job_id“ ist eine Ausgabevariable vom Typ „uniqueidentifier“.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

„sp_add_job“ muss über die Agent-Datenbank für Aufträge ausgeführt werden, die beim Erstellen des Agent-Auftrags angegeben wurde.
Nachdem „sp_add_job“ zum Hinzufügen eines Auftrags ausgeführt wurde, können mit „sp_add_jobstep“ Schritte hinzugefügt werden, die die Aktionen für den Auftrag ausführen. Die Nummer der ersten Version des Auftrags ist „0“, die sich schrittweise um 1 erhöht, wenn der erste Schritt hinzugefügt wird.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_update_job"></a><a name="sp_update_job"></a>sp_update_job

Aktualisiert einen vorhandenen Auftrag.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
  [ , [ @new_name = ] 'new_name' ]
  [ , [ @description = ] 'description' ]
  [ , [ @enabled = ] enabled ]
  [ , [ @schedule_interval_type = ] schedule_interval_type ]  
  [ , [ @schedule_interval_count = ] schedule_interval_count ]
  [ , [ @schedule_start_time = ] schedule_start_time ]
  [ , [ @schedule_end_time = ] schedule_end_time ]
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, der aktualisiert werden soll. „job_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@new_name =** ] 'Neuer_Name'  
Der neue Name des Auftrags. „new_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@description =** ] 'Beschreibung'  
Die Beschreibung des Auftrags. „description“ ist vom Datentyp „nvarchar(512)“.

[ **\@enabled =** ] enabled  
Gibt an, ob der Auftragszeitplan aktiviert (1) oder nicht aktiviert (0) ist. „enabled“ ist vom Datentyp „bit“.

[ **\@schedule_interval_type=** ] schedule_interval_type  
Der Wert gibt an, wie oft der Auftrag ausgeführt werden soll. „schedule_interval_type“ ist vom Datentyp „nvarchar(50)“ und kann einen der folgenden Werte aufweisen:

- „Once“
- „Minutes“
- „Hours“
- „Days“
- „Weeks“
- „Months“

[ **\@schedule_interval_count=** ] schedule_interval_count  
Die Anzahl der „schedule_interval_count“-Intervalle, die zwischen den einzelnen Ausführungen des Auftrags stattfinden sollen. „schedule_interval_count“ ist vom Datentyp „int“ und weist den Standardwert „1“ auf. Der Wert muss größer als oder gleich „1“ sein.

[ **\@schedule_start_time=** ] schedule_start_time  
Das Datum, an dem Ausführung des Auftrags beginnen kann. „schedule_start_time“ ist vom Datentyp „DATETIME2“ und weist den Standardwert „00:00:00.0000000 0001-01-01“ auf.

[ **\@schedule_end_time=** ] schedule_end_time  
Das Datum, an dem die Ausführung des Auftrags beendet werden kann. „schedule_end_time“ ist vom Datentyp „DATETIME2“ und weist den Standardwert „9999-12-31 11:59:59.0000000“ auf.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Nachdem „sp_add_job“ zum Hinzufügen eines Auftrags ausgeführt wurde, können mit „sp_add_jobstep“ Schritte hinzugefügt werden, die die Aktionen für den Auftrag ausführen. Die Nummer der ersten Version des Auftrags ist „0“, die sich schrittweise um 1 erhöht, wenn der erste Schritt hinzugefügt wird.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_delete_job"></a><a name="sp_delete_job"></a>sp_delete_job

Löscht einen vorhandenen Auftrag.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
  [ , [ @force = ] force ]
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, der gelöscht werden soll. „job_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@force =** ] force  
Gibt an, ob der Auftrag gelöscht werden soll, wenn für diesen gerade Vorgänge ausgeführt werden, und alle laufenden Ausführungen abgebrochen werden sollen (1) oder ob ein Fehler auftreten soll, wenn gerade Aufträge ausgeführt werden (0). „force“ ist vom Datentyp „bit“.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Der Auftragsverlauf wird automatisch gelöscht, wenn ein Auftrag gelöscht wird.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_add_jobstep"></a><a name="sp_add_jobstep"></a>sp_add_jobstep

Fügt einen Schritt zu einem Auftrag hinzu.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] step_name ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, dem der Schritt hinzugefügt werden soll. „job_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@step_id =** ] step_id  
Die Sequenz-ID des Auftragsschritts. Die Schritt-IDs beginnen bei „1“ und erhöhen sich lückenlos schrittweise. Weist ein vorhandener Schritt bereits diese ID auf, wird die ID dieses Schritts und aller nachfolgenden Schritte inkrementiert, damit dieser neue Schritt in die Sequenz eingefügt werden kann. Wenn dieses Argument nicht angegeben ist, wird „step_id“ automatisch dem letzten Schritt in der Sequenz der Schritte zugewiesen. „step_id“ ist vom Datentyp „int“.

[ **\@step_name =** ] step_name  
Der Name des Schritts. Muss angegeben werden, sofern es sich nicht um den ersten Schritt eines Auftrags handelt, dem (der Einfachheit halber) der Standardname „JobStep“ zugewiesen ist. „step_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@command_type =** ] 'Befehlstyp'  
Der Typ des Befehls, der bei diesem Auftragsschritt ausgeführt wird. „command_type“ ist vom Datentyp „nvarchar(50)“ mit dem Standardwert „TSql“, was bedeutet, dass der Wert des Parameters @command_type ein T-SQL-Skript ist.

Wenn dieses Argument angegeben ist, muss der Wert „TSql“ sein.

[ **\@command_source =** ] 'Befehlsquelle'  
Der Typ des Speicherorts, an dem der Befehl gespeichert ist. „command_source“ ist vom Datentyp „nvarchar(50)“ mit dem Standardwert „Inline“, was bedeutet, dass der Wert des Parameters @command_source der wörtliche Text des Befehls ist.

Wenn dieses Argument angegeben ist, muss der Wert „Inline“ sein.

[ **\@command =** ] 'Befehl'  
Der Befehl muss ein gültiges T-SQL-Skript sein und wird dann bei diesem Auftragsschritt ausgeführt. „command“ ist vom Datentyp „nvarchar(max)“ mit dem NULL-Standardwert.

[ **\@credential_name =** ] 'Name_der_Anmeldeinformationen'  
Der Name der datenbankweit gültigen Anmeldeinformationen, die in dieser Auftragssteuerungsdatenbank gespeichert sind und mit denen eine Verbindung mit den Zieldatenbanken in der Zielgruppe hergestellt wird, wenn dieser Schritt ausgeführt wird. „credential_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@target_group_name =** ] 'Name_der_Zielgruppe'  
Der Name der Zielgruppe, die die Zieldatenbanken enthält, in denen der Auftragsschritt ausgeführt wird. „target_group_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Die Verzögerung vor dem ersten Wiederholungsversuch, wenn der Auftragsschritt bei der ersten Ausführung einen Fehler verursacht. „initial_retry_interval_seconds“ ist vom Datentyp „int“ mit dem Standardwert „1“.

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Die maximale Verzögerung zwischen Wiederholungsversuchen. Wenn die Verzögerung zwischen den Wiederholungsversuchen größer ist als dieser Wert, wird diese stattdessen auf diesen Maximalwert gesetzt. „maximum_retry_interval_seconds“ ist vom Datentyp „int“ mit dem Standardwert „120“.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Der Multiplikator, der auf die Wiederholungsverzögerung anzuwenden ist, wenn bei mehreren Auftragsschrittausführungen ein Fehler aufgetreten ist. Wenn beim ersten Wiederholungsversuch z.B. eine Verzögerung von 5 Sekunden aufgetreten ist und der Wartezeitmultiplikator „2,0“ beträgt, tritt beim zweiten Wiederholungsversuch eine Verzögerung von 10 Sekunden und beim dritten Wiederholungsversuch eine Verzögerung von 20 Sekunden auf. „retry_interval_backoff_multiplier“ ist vom Datentyp „real“ mit dem Standardwert „2,0“.

[ **\@retry_attempts =** ] retry_attempts  
Die Anzahl der Wiederholungen für die Ausführung, wenn beim ersten Versuch ein Fehler auftritt. Wenn der „retry_attempts“-Wert „10“ ist, gibt es einen ersten Versuch und 10 Wiederholungsversuch, d.h. insgesamt 11 Versuche. Wenn beim letzten Wiederholungsversuch ein Fehler auftritt, wird die Auftragsausführung mit dem Lebenszyklus „Fehler“ beendet. „retry_attempts“ ist vom Datentyp „int“ mit dem Standardwert „10“.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
Die maximal zulässige Dauer für den auszuführenden Schritt. Wenn diese Dauer überschritten wird, wird die Auftragsausführung mit dem Lebenszyklus „TimedOut“ beendet. „step_timeout_seconds“ ist vom Datentyp „int“ mit dem Standardwert 43.200 Sekunden (12 Stunden).

[ **\@output_type =** ] 'Ausgabetyp'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Typ des Ziels, in den das erste Resultset des Befehls geschrieben wird. „output_type“ ist vom Datentyp „nvarchar(50)“ mit dem NULL-Standardwert.

Wenn dieses Argument angegeben ist, muss der Wert „SqlDatabase“ sein.

[ **\@output_credential_name =** ] 'Name_der_Anmeldeinformationen_für_die_Ausgabe'  
Wenn dieses Argument nicht den NULL-Wert aufweist, die datenbankweit gültigen Anmeldeinformationen, die zum Herstellen einer Verbindung mit der Ausgabezieldatenbank verwendet wird. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. „output_credential_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert.

[ **\@output_subscription_id =** ] 'Abonnement-ID für die Ausgabe'  
Erfordert eine Beschreibung.

[ **\@output_resource_group_name =** ] 'Name_der_Ressourcengruppe_für_die_Ausgabe'  
Erfordert eine Beschreibung.

[ **\@output_server_name =** ] 'Name_des_Ausgabeservers'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der vollqualifizierte DNS-Name des Servers, der die Ausgabezieldatenbank enthält. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. „output_server_name“ ist vom Datentyp „nvarchar(256)“ mit dem NULL-Standardwert.

[ **\@output_database_name =** ] 'Name_der_Ausgabedatenbank'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Name der Datenbank, die die Ausgabezieltabelle enthält. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. „output_database_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert.

[ **\@output_schema_name =** ] 'Name des Ausgabeschemas'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Name des SQL-Schemas, das die Ausgabezieltabelle enthält. Wenn „output_type“ den Wert „SqlDatabase“ enthält, ist der Standardwert „dbo“. „output_schema_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@output_table_name =** ] 'Name_der_Ausgabetabelle'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Name der Tabelle, in die das erste Resultset des Befehls geschrieben wird. Wenn die Tabelle noch nicht vorhanden ist, wird sie basierend auf dem Schema des zurückgebenden Resultsets erstellt. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. „output_table_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert.

[ **\@job_version =** ] job_version OUTPUT  
Der Output-Parameter, der die Versionsnummer des neuen Auftrags zugewiesen wird. „job_version“ ist vom Datentyp „int“.

[ **\@max_parallelism =** ] max_parallelism OUTPUT  
Der maximale Grad an Parallelität pro Pool für elastische Datenbanken. Wenn dieser festgelegt ist, kann beim Auftragsschritt ausschließlich der maximale Grenzwert der zahlreichen Datenbanken pro Pool für elastische Datenbanken ausgeführt werden. Dies gilt für sämtliche Pools für elastische Datenbanken, die entweder direkt in die Zielgruppe eingeschlossen sind oder sich auf einem Server befinden, der in der Zielgruppe eingeschlossen ist. „max_parallelism“ ist vom Datentyp „int“.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Wenn „sp_add_jobstep“ erfolgreich aufgeführt wird, erhöht sich schrittweise die aktuelle Versionsnummer des Auftrags. Bei der nächsten Ausführung des Auftrags wird die neue Version verwendet. Wenn der Auftrag gerade ausgeführt wird, enthält die Ausführung nicht den neuen Schritt.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:  

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_update_jobstep"></a><a name="sp_update_jobstep"></a>sp_update_jobstep

Aktualisiert einen Auftragsschritt.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @new_id = ] new_id ]
     [ , [ @new_name = ] 'new_name' ]
     [ , [ @command_type = ] 'command_type' ]
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]
     [ , [ @retry_attempts = ] retry_attempts ]
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]
     [ , [ @output_type = ] 'output_type' ]
     [ , [ @output_credential_name = ] 'output_credential_name' ]
     [ , [ @output_server_name = ] 'output_server_name' ]
     [ , [ @output_database_name = ] 'output_database_name' ]
     [ , [ @output_schema_name = ] 'output_schema_name' ]
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, zu dem der Schritt gehört. „job_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@step_id =** ] step_id  
Die ID des Auftragsschrittes, der geändert werden soll. Es muss entweder „step_id“ oder „step_name“ angegeben werden. „step_id“ ist vom Datentyp „int“.

[ **\@step_name =** ] 'Schrittname'  
Der Name des Schritts, der geändert werden soll. Es muss entweder „step_id“ oder „step_name“ angegeben werden. „step_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@new_id =** ] new_id  
Die neue Sequenz-ID für den Auftragsschritt. Die Schritt-IDs beginnen bei „1“ und erhöhen sich lückenlos schrittweise. Wenn ein Schritt neu angeordnet wird, werden die anderen Schritte automatisch neu nummeriert.

[ **\@new_name =** ] 'Neuer_Name'  
Der neue Name des Schritts. „new_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@command_type =** ] 'Befehlstyp'  
Der Typ des Befehls, der bei diesem Auftragsschritt ausgeführt wird. „command_type“ ist vom Datentyp „nvarchar(50)“ mit dem Standardwert „TSql“, was bedeutet, dass der Wert des Parameters @command_type ein T-SQL-Skript ist.

Wenn dieses Argument angegeben ist, muss der Wert „TSql“ sein.

[ **\@command_source =** ] 'Befehlsquelle'  
Der Typ des Speicherorts, an dem der Befehl gespeichert ist. „command_source“ ist vom Datentyp „nvarchar(50)“ mit dem Standardwert „Inline“, was bedeutet, dass der Wert des Parameters @command_source der wörtliche Text des Befehls ist.

Wenn dieses Argument angegeben ist, muss der Wert „Inline“ sein.

[ **\@command =** ] 'Befehl'  
Die Befehle müssen ein gültiges T-SQL-Skript sein und werden dann bei diesem Auftragsschritt ausgeführt. „command“ ist vom Datentyp „nvarchar(max)“ mit dem NULL-Standardwert.

[ **\@credential_name =** ] 'Name_der_Anmeldeinformationen'  
Der Name der datenbankweit gültigen Anmeldeinformationen, die in dieser Auftragssteuerungsdatenbank gespeichert sind und mit denen eine Verbindung mit den Zieldatenbanken in der Zielgruppe hergestellt wird, wenn dieser Schritt ausgeführt wird. „credential_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@target_group_name =** ] 'Name_der_Zielgruppe'  
Der Name der Zielgruppe, die die Zieldatenbanken enthält, in denen der Auftragsschritt ausgeführt wird. „target_group_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
Die Verzögerung vor dem ersten Wiederholungsversuch, wenn der Auftragsschritt bei der ersten Ausführung einen Fehler verursacht. „initial_retry_interval_seconds“ ist vom Datentyp „int“ mit dem Standardwert „1“.

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
Die maximale Verzögerung zwischen Wiederholungsversuchen. Wenn die Verzögerung zwischen den Wiederholungsversuchen größer ist als dieser Wert, wird diese stattdessen auf diesen Maximalwert gesetzt. „maximum_retry_interval_seconds“ ist vom Datentyp „int“ mit dem Standardwert „120“.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
Der Multiplikator, der auf die Wiederholungsverzögerung anzuwenden ist, wenn bei mehreren Auftragsschrittausführungen ein Fehler aufgetreten ist. Wenn beim ersten Wiederholungsversuch z.B. eine Verzögerung von 5 Sekunden aufgetreten ist und der Wartezeitmultiplikator „2,0“ beträgt, tritt beim zweiten Wiederholungsversuch eine Verzögerung von 10 Sekunden und beim dritten Wiederholungsversuch eine Verzögerung von 20 Sekunden auf. „retry_interval_backoff_multiplier“ ist vom Datentyp „real“ mit dem Standardwert „2,0“.

[ **\@retry_attempts =** ] retry_attempts  
Die Anzahl der Wiederholungen für die Ausführung, wenn beim ersten Versuch ein Fehler auftritt. Wenn der „retry_attempts“-Wert „10“ ist, gibt es einen ersten Versuch und 10 Wiederholungsversuch, d.h. insgesamt 11 Versuche. Wenn beim letzten Wiederholungsversuch ein Fehler auftritt, wird die Auftragsausführung mit dem Lebenszyklus „Fehler“ beendet. „retry_attempts“ ist vom Datentyp „int“ mit dem Standardwert „10“.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
Die maximal zulässige Dauer für den auszuführenden Schritt. Wenn diese Dauer überschritten wird, wird die Auftragsausführung mit dem Lebenszyklus „TimedOut“ beendet. „step_timeout_seconds“ ist vom Datentyp „int“ mit dem Standardwert 43.200 Sekunden (12 Stunden).

[ **\@output_type =** ] 'Ausgabetyp'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Typ des Ziels, in den das erste Resultset des Befehls geschrieben wird. Um den Wert von „output_type“ auf NULL zurückzusetzen, legen Sie den Wert dieses Parameters auf '' (leere Zeichenfolge) fest. „output_type“ ist vom Datentyp „nvarchar(50)“ mit dem NULL-Standardwert.

Wenn dieses Argument angegeben ist, muss der Wert „SqlDatabase“ sein.

[ **\@output_credential_name =** ] 'Name_der_Anmeldeinformationen_für_die_Ausgabe'  
Wenn dieses Argument nicht den NULL-Wert aufweist, die datenbankweit gültigen Anmeldeinformationen, die zum Herstellen einer Verbindung mit der Ausgabezieldatenbank verwendet wird. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. Um den Wert von „output_credential_name“ wieder auf NULL zurückzusetzen, legen Sie den Wert dieses Parameters auf '' (leere Zeichenfolge) fest. „output_credential_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert.

[ **\@output_server_name =** ] 'Name_des_Ausgabeservers'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der vollqualifizierte DNS-Name des Servers, der die Ausgabezieldatenbank enthält. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. Um den Wert von „output_server_name“ wieder auf NULL zurückzusetzen, setzen Sie den Wert dieses Parameters auf '' (leere Zeichenfolge) fest. „output_server_name“ ist vom Datentyp „nvarchar(256)“ mit dem NULL-Standardwert.

[ **\@output_database_name =** ] 'Name_der_Ausgabedatenbank'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Name der Datenbank, die die Ausgabezieltabelle enthält. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. Um den Wert von „output_database_name“ wieder auf NULL zurückzusetzen, setzen Sie den Wert dieses Parameters auf '' (leere Zeichenfolge) fest. „output_database_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert.

[ **\@output_schema_name =** ] 'Name des Ausgabeschemas'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Name des SQL-Schemas, das die Ausgabezieltabelle enthält. Wenn „output_type“ den Wert „SqlDatabase“ enthält, ist der Standardwert „dbo“. Um den Wert von „output_schema_name“ wieder auf NULL zurückzusetzen, setzen Sie den Wert dieses Parameters auf '' (leere Zeichenfolge) fest. „output_schema_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@output_table_name =** ] 'Name_der_Ausgabetabelle'  
Wenn dieses Argument nicht den NULL-Wert aufweist, der Name der Tabelle, in die das erste Resultset des Befehls geschrieben wird. Wenn die Tabelle noch nicht vorhanden ist, wird sie basierend auf dem Schema des zurückgebenden Resultsets erstellt. Muss angegeben werden, wenn „output_type“ den Wert „SqlDatabase“ aufweist. Um den Wert von „output_server_name“ wieder auf NULL zurückzusetzen, setzen Sie den Wert dieses Parameters auf '' (leere Zeichenfolge) fest. „output_table_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert.

[ **\@job_version =** ] job_version OUTPUT  
Der Output-Parameter, der die Versionsnummer des neuen Auftrags zugewiesen wird. „job_version“ ist vom Datentyp „int“.

[ **\@max_parallelism =** ] max_parallelism OUTPUT  
Der maximale Grad an Parallelität pro Pool für elastische Datenbanken. Wenn dieser festgelegt ist, kann beim Auftragsschritt ausschließlich der maximale Grenzwert der zahlreichen Datenbanken pro Pool für elastische Datenbanken ausgeführt werden. Dies gilt für sämtliche Pools für elastische Datenbanken, die entweder direkt in die Zielgruppe eingeschlossen sind oder sich auf einem Server befinden, der in der Zielgruppe eingeschlossen ist. Um den Wert von „max_parallelism“ auf NULL zurückzusetzen, legen Sie den Wert dieses Parameters auf „-1“ fest. „max_parallelism“ ist vom Datentyp „int“.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Keine der laufenden Ausführungen des Auftrags sind davon betroffen. Wenn „sp_update_jobstep“ erfolgreich aufgeführt wird, erhöht sich die aktuelle Versionsnummer des Auftrags schrittweise. Bei der nächsten Ausführung des Auftrags wird die neue Version verwendet.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_delete_jobstep"></a><a name="sp_delete_jobstep"></a>sp_delete_jobstep

Entfernt einen Auftragsschritt aus einem Auftrag.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, aus dem der Schritt entfernt wird. „job_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@step_id =** ] step_id  
Die ID für den zu löschenden Auftragsschritt. Es muss entweder „step_id“ oder „step_name“ angegeben werden. „step_id“ ist vom Datentyp „int“.

[ **\@step_name =** ] 'Schrittname'  
Der Name des zu löschenden Schritts. Es muss entweder „step_id“ oder „step_name“ angegeben werden. „step_name“ ist vom Datentyp „nvarchar(128)“.

[ **\@job_version =** ] job_version OUTPUT  
Der Output-Parameter, der die Versionsnummer des neuen Auftrags zugewiesen wird. „job_version“ ist vom Datentyp „int“.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Keine der laufenden Ausführungen des Auftrags sind davon betroffen. Wenn „sp_update_jobstep“ erfolgreich aufgeführt wird, erhöht sich die aktuelle Versionsnummer des Auftrags schrittweise. Bei der nächsten Ausführung des Auftrags wird die neue Version verwendet.

Die anderen Auftragsschritte werden automatisch neu nummeriert, um die Lücke zu schließen, die der gelöschte Auftragsschritt hinterlassen hat.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_start_job"></a><a name="sp_start_job"></a>sp_start_job

Startet die Ausführung eines Auftrags.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_start_job [ @job_name = ] 'job_name'
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, aus dem der Schritt entfernt wird. „job_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@job_execution_id =** ] job_execution_id OUTPUT  
Parameter „Output“, der der ID der Auftragsausführung zugewiesen wird. „job_version“ ist „uniqueidentifier“.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Keine.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_stop_job"></a><a name="sp_stop_job"></a>sp_stop_job

Hält die Ausführung eines Auftrags an.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```

#### <a name="arguments"></a>Argumente

[ **\@job_execution_id =** ] job_execution_id  
Die ID der Auftragsausführung, die angehalten werden soll. „job_execution_id“ ist vom Datentyp „uniqueidentifier“ mit dem NULL-Standardwert.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Keine.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_add_target_group"></a><a name="sp_add_target_group"></a>sp_add_target_group

Fügt eine Zielgruppe hinzu.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```

#### <a name="arguments"></a>Argumente

[ **\@target_group_name =** ] 'Name der Zielgruppe'  
Der Name der Zielgruppe, die erstellt werden soll. „target_group_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@target_group_id =** ] target_group_id OUTPUT Die Zielgruppen-ID, die dem Auftrag zugewiesen wird, wenn dieser erfolgreich erstellt wurde. „target_group_id“ ist eine Ausgabevariable vom Typ „uniqueidentifier“ mit dem NULL-Standardwert.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Zielgruppen stellen eine einfache Möglichkeit dar, eine Sammlung von Datenbanken als Ziel für einen Auftrag festzulegen.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_delete_target_group"></a><a name="sp_delete_target_group"></a>sp_delete_target_group

Löscht eine Zielgruppe.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```

#### <a name="arguments"></a>Argumente

[ **\@target_group_name =** ] 'Name der Zielgruppe'  
Der Name der Zielgruppe, die gelöscht werden soll. „target_group_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Keine.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

### <a name="sp_add_target_group_member"></a><a name="sp_add_target_group_member"></a>sp_add_target_group_member

Fügt einer Zielgruppe eine Datenbank oder eine Gruppe von Datenbanken hinzu.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]
        [ , [ @target_type = ] 'target_type' ]
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]
        [ , [ @server_name = ] 'server_name' ]
        [ , [ @database_name = ] 'database_name' ]
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]
        [ , [ @shard_map_name = ] 'shard_map_name' ]
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>Argumente

[ **\@target_group_name =** ] 'Name der Zielgruppe'  
Der Name der Zielgruppe, der das Mitglied hinzugefügt wird. „target_group_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@membership_type =** ] 'Mitgliedschaftstyp'  
Gibt an, ob das Zielgruppenmitglied ein- bzw. ausgeschlossen wird. „target_group_name“ ist vom Datentyp „nvarchar(128)“ mit dem Standardwert „Include“. Gültige Werte für „membership_type“ sind „Include“ oder „Exclude“.

[ **\@target_type =** ] 'Zieltyp'  
Der Typ der Zieldatenbank oder der Sammlung von Datenbanken, einschließlich aller Datenbanken auf einem Server, alle Datenbanken in einem Pool für elastische Datenbanken, alle Datenbanken in einer Shardzuordnung oder einer einzelnen Datenbank. „target_type“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert. Gültige Werte für „target_type“ sind „SqlServer“, „SqlElasticPool“, „SqlDatabase“ oder „SqlShardMap“.

[ **\@refresh_credential_name =** ] refresh_'Name_der_Anmeldeinformationen'  
Der Name der datenbankweit gültigen Anmeldeinformationen. „refresh_credential_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@server_name =** ] 'Servername'  
Der Name des Servers, der der angegebenen Zielgruppe hinzugefügt werden soll. „server_name“ muss angegeben werden, wenn „target_type“ den Wert „SqlServer“ aufweist. „server_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@database_name =** ] 'Datenbankname'  
Der Name der Datenbank, die der angegebenen Zielgruppe hinzugefügt werden soll. „database_name“ muss angegeben werden, wenn „target_type“ den Wert „SqlDatabase“ aufweist. „database_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@elastic_pool_name =** ] 'Name_des_Pools_für_elastische_Datenbanken'  
Der Name des Pools für elastische Datenbanken, die der angegebenen Zielgruppe hinzugefügt werden soll. „elastic_pool_name“ muss angegeben werden, wenn „target_type“ den Wert „SqlElasticPool“ aufweist. „elastic_pool_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@shard_map_name =** ] 'Name_der_Shardzuordnung'  
Der Name des Pools der Shardzuordnung, die der angegebenen Zielgruppe hinzugefügt werden soll. „elastic_pool_name“ muss angegeben werden, wenn „target_type“ den Wert „SqlShardMap“ aufweist. „shard_map_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ **\@target_id =** ] target_group_id OUTPUT  
Die Ziel-ID, die dem zu entfernenden Zielgruppenmitglied zugewiesen ist, wenn dieses erstellt und der Zielgruppe hinzugefügt wurde. „target_id“ ist eine Ausgabevariable vom Typ „uniqueidentifier“ mit dem NULL-Standardwert.
Rückgabecodewerte: 0 (erfolgreich) oder 1 (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Ein Auftrag wird zum Zeitpunkt der Ausführung in allen Einzeldatenbanken auf einem Server oder in einem Pool für elastische Datenbanken ausgeführt, wenn ein Server oder Pool für elastische Datenbanken in der Zielgruppe enthalten ist.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

#### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden alle Datenbanken auf den Servern „London“ und „NewYork“ zur Gruppe „Servers Maintaining Customer Information“ hinzugefügt. Bei der Erstellung des Auftrags-Agent müssen Sie eine Verbindung mit der angegebenen Auftragsdatenbank herstellen, d.h. in diesem Fall mit ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a><a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

Entfernt ein Zielgruppenmitglied aus einer Zielgruppe.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
   [ , [ @target_id = ] 'target_id']
```

#### <a name="arguments"></a>Argumente

[ @target_group_name = ] 'target_group_name'  
Der Name der Zielgruppe, aus der das Zielgruppenmitglied entfernt werden soll. „target_group_name“ ist vom Datentyp „nvarchar(128)“ ohne Standardwert.

[ @target_id = ] target_id  
 Die Ziel-ID, dem zu entfernenden Zielgruppenmitglied zugewiesen ist. „target_id“ ist vom Datentyp „uniqueidentifier“ mit dem NULL-Standardwert.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Zielgruppen stellen eine einfache Möglichkeit dar, eine Sammlung von Datenbanken als Ziel für einen Auftrag festzulegen.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

#### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird der Server „London“ aus der Gruppe „Servers Maintaining Customer Information“ entfernt. Bei der Erstellung des Auftrags-Agent müssen Sie eine Verbindung mit der angegebenen Auftragsdatenbank herstellen, d.h. in diesem Fall mit ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ;
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a><a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

Entfernt die Verlaufsdatensätze für einen Auftrag.

#### <a name="syntax"></a>Syntax

```syntaxsql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>Argumente

[ **\@job_name =** ] 'Jobname'  
Der Name des Auftrags, für den die Verlaufsdatensätze gelöscht werden sollen. „job_name“ ist vom Datentyp „nvarchar(128)“ mit dem NULL-Standardwert. Es muss entweder „job_id“ oder „job_name“ angegeben werden, beides ist nicht möglich.

[ **\@job_id =** ] job_id  
 Die ID des Auftrags für die zu löschenden Datensätze. „job_id“ ist vom Datentyp „uniqueidentifier“ mit dem NULL-Standardwert. Es muss entweder „job_id“ oder „job_name“ angegeben werden, beides ist nicht möglich.

[ **\@oldest_date =** ] oldest_date  
 Der älteste Datensatz, der im Verlauf beibehalten werden soll. „oldest_date“ ist vom Datentyp „DATETIME2“ mit dem NULL-Standardwert. Wenn „oldest_date“ angegeben ist, entfernt „sp_purge_jobhistory“ nur Datensätze, die älter sind als der angegebene Wert.

#### <a name="return-code-values"></a>Rückgabecodewerte

„0“ (erfolgreich) oder „1“ (fehlerhaft)

#### <a name="remarks"></a>Bemerkungen

Zielgruppen stellen eine einfache Möglichkeit dar, eine Sammlung von Datenbanken als Ziel für einen Auftrag festzulegen.

#### <a name="permissions"></a>Berechtigungen

Standardmäßig können Mitglieder der festen Serverrolle „sysadmin“ diese gespeicherte Prozedur ausführen. Um einem Benutzer lediglich die Überwachung von Aufträgen zu ermöglichen, können Sie dem Benutzer die folgende Datenbankrolle in der Agent-Datenbank für Aufträge zuweisen, die beim Erstellen des Agent-Auftrags angegeben wurde:

- jobs_reader

Einzelheiten zu den Berechtigungen dieser Rollen finden Sie im Abschnitt „Berechtigungen“ in diesem Dokument. Nur Mitglieder der Rolle „sysadmin“ können mithilfe dieser gespeicherten Prozedur Attribute von Aufträgen bearbeiten, die anderen Benutzern gehören.

#### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden alle Datenbanken auf den Servern „London“ und „NewYork“ zur Gruppe „Servers Maintaining Customer Information“ hinzugefügt. Bei der Erstellung des Auftrags-Agent müssen Sie eine Verbindung mit der angegebenen Auftragsdatenbank herstellen, d.h. in diesem Fall mit ElasticJobs.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```

## <a name="job-views"></a>Auftragsansichten

Die folgenden Ansichten sind in der [Auftragsdatenbank](job-automation-overview.md#job-database) verfügbar.

|Sicht  |BESCHREIBUNG  |
|---------|---------|
|[job_executions](#job_executions-view)     |  Zeigt den Auftragsausführungsverlauf an.      |
|[jobs](#jobs-view)     |   Zeigt alle Aufträge an.      |
|[job_versions](#job_versions-view)     |   Zeigt alle Auftragsversionen an.      |
|[jobsteps](#jobsteps-view)     |     Zeigt alle Schritte in der aktuellen Version des jeweiligen Auftrags an.    |
|[jobstep_versions](#jobstep_versions-view)     |     Zeigt alle Schritte in allen Versionen des jeweiligen Auftrags an.    |
|[target_groups](#target_groups-view)     |      Zeigt alle Zielgruppen an.   |
|[target_group_members](#target_group_members-view)     |   Zeigt alle Mitglieder sämtlicher Zielgruppen an.      |

### <a name="job_executions-view"></a><a name="job_executions-view"></a>Ansicht „job_executions“

[jobs].[job_executions]

Zeigt den Auftragsausführungsverlauf an.

|Spaltenname | Datentyp | BESCHREIBUNG |
|---------|---------|---------|
|**job_execution_id** | UNIQUEIDENTIFIER | Eindeutige ID einer Instanz einer Auftragsausführung.
|**job_name** | nvarchar(128) | Der Name des Auftrags.
|**job_id** | UNIQUEIDENTIFIER | Eindeutige ID des Auftrags.
|**job_version** | INT | Version des Auftrags (wird automatisch jedes Mal aktualisiert, wenn der Auftrag geändert wird)
|**step_id** |INT | Eindeutiger Bezeichner (für diesen Auftrag) für den Schritt NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.
|**is_active** | bit | Gibt an, ob Informationen aktiv oder inaktiv sind. „1“ weist auf aktive Aufträge hin, während „0“ auf inaktive Aufträge hinweist.
|**lifecycle** | nvarchar(50) | Wert, der den Status des Auftrags angibt: „Created“, „In Progress“, „Failed“, „Succeeded“, „Skipped“, „SucceededWithSkipped“|
|**create_time**| datetime2(7) | Datum und Uhrzeit, an dem der Auftrag erstellt wurde.
|**start_time** | datetime2(7) | Datum und Uhrzeit, an dem die Ausführung des Auftrags gestartet wurde. NULL, wenn der Auftrag noch nicht ausgeführt wurde.
|**end_time** | datetime2(7) | Datum und Uhrzeit, an dem die Ausführung des Auftrags abgeschlossen wurde. NULL, wenn der Auftrag noch nicht ausgeführt oder die Ausführung noch nicht abgeschlossen wurde.
|**current_attempts** | INT | Häufigkeit, mit der der Schritt wiederholt wurde. Für den übergeordneten Auftrag wird „0“ und für untergeordnete Auftragsausführungen abhängig von der Ausführungsrichtlinie „1“ oder häufiger festgelegt.
|**current_attempt_start_time** | datetime2(7) | Datum und Uhrzeit, an dem die Ausführung des Auftrags gestartet wurde. NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.
|**last_message** | nvarchar(max) | Meldung zu Auftrags- oder Schrittverlauf.
|**target_type** | nvarchar(128) | Der Typ der Zieldatenbank oder der Sammlung von Datenbanken, einschließlich aller Datenbanken auf einem Server, alle Datenbanken in einem Pool für elastische Datenbanken oder einer Datenbank. Gültige Werte für „target_type“ sind „SqlServer“, „SqlElasticPool“ oder „SqlDatabase“. NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.
|**target_id** | UNIQUEIDENTIFIER | Eindeutige ID des Zielgruppenmitglieds.  NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.
|**target_group_name** | nvarchar(128) | Der Name der Zielgruppe. NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.
|**target_server_name** | nvarchar(256)  | Der Name des in der Zielgruppe enthaltenen Servers. Nur angegeben, wenn „target_type“ den Wert „SqlServer“ aufweist. NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.
|**target_database_name** | nvarchar(128) | Der Name der in der Zielgruppe enthaltenen Datenbank. Wird nur angegeben, wenn „target_type“ den Wert „SqlDatabase“ aufweist. NULL weist darauf hin, dass es sich hierbei um eine übergeordnete Auftragsausführung handelt.

### <a name="jobs-view"></a>Ansicht „jobs“

[jobs].[jobs]

Zeigt alle Aufträge an.

|Spaltenname | Datentyp |BESCHREIBUNG|
|------|------|-------|
|**job_name** | nvarchar(128) | Der Name des Auftrags.|
|**job_id**| UNIQUEIDENTIFIER |Eindeutige ID des Auftrags.|
|**job_version** |INT |Version des Auftrags (wird automatisch jedes Mal aktualisiert, wenn der Auftrag geändert wird)|
|**description** |nvarchar(512)| Die Beschreibung des Auftrags. Bit für Aktivierung (Enabled): Gibt an, ob der Auftrag aktiviert oder deaktiviert ist. „1“ gibt an, dass die Aufträge aktiviert sind, und „0“, dass die Aufträge deaktiviert sind.|
|**schedule_interval_type**|nvarchar(50) |Wert, der angibt, wie oft der Auftrag ausgeführt werden soll: „Once“, „Minutes“, „Hours“, „Days“, „Weeks“, „Months“.
|**schedule_interval_count**|INT|Die Anzahl der „schedule_interval_type“-Intervalle, die zwischen den einzelnen Ausführungen des Auftrags stattfinden sollen.|
|**schedule_start_time**|datetime2(7)|Datum und Uhrzeit, an dem die Ausführung des Auftrags zuletzt gestartet wurde.|
|**schedule_end_time**|datetime2(7)|Datum und Uhrzeit, an dem die Ausführung des Auftrags zuletzt abgeschlossen wurde.|

### <a name="job_versions-view"></a><a name="job_versions-view"></a>Ansicht „job_versions“

[jobs].[job_versions]

Zeigt alle Auftragsversionen an.

|Spaltenname|Datentyp|BESCHREIBUNG|
|------|------|-------|
|**job_name**|nvarchar(128)|Der Name des Auftrags.|
|**job_id**|UNIQUEIDENTIFIER|Eindeutige ID des Auftrags.|
|**job_version**|INT|Version des Auftrags (wird automatisch jedes Mal aktualisiert, wenn der Auftrag geändert wird)|

### <a name="jobsteps-view"></a>Ansicht „jobsteps“

[jobs].[jobsteps]

Zeigt alle Schritte in der aktuellen Version des jeweiligen Auftrags an.

|Spaltenname|Datentyp|BESCHREIBUNG|
|------|------|-------|
|**job_name**|nvarchar(128)|Der Name des Auftrags.|
|**job_id**|UNIQUEIDENTIFIER|Eindeutige ID des Auftrags.|
|**job_version**|INT|Version des Auftrags (wird automatisch jedes Mal aktualisiert, wenn der Auftrag geändert wird)|
|**step_id**|INT|Eindeutiger Bezeichner (für diesen Auftrag) für den Schritt|
|**step_name**|nvarchar(128)|Der (bei diesem Auftrag) eindeutige Name für den Schritt.|
|**command_type**|nvarchar(50)|Der Typ des Befehls, der beim Auftragsschritt ausgeführt werden soll. Bei v1 muss der Wert „TSql“ sein und wird standardmäßig auf diesen festgelegt.|
|**command_source**|nvarchar(50)|Adresse des Befehls. Bei v1 ist „Inline“ der Standardwert, und nur dieser Wert wird akzeptiert.|
|**command**|nvarchar(max)|Die Befehle, die in elastischen Aufträgen über „command_type“ ausgeführt werden sollen.|
|**credential_name**|nvarchar(128)|Name der datenbankweit gültigen Anmeldeinformationen, die für die Ausführung des Auftrags verwendet werden.|
|**target_group_name**|nvarchar(128)|Der Name der Zielgruppe.|
|**target_group_id**|UNIQUEIDENTIFIER|Eindeutige ID der Zielgruppe.|
|**initial_retry_interval_seconds**|INT|Die Verzögerung vor dem ersten Wiederholungsversuch. Der Standardwert ist 1.|
|**maximum_retry_interval_seconds**|INT|Die maximale Verzögerung zwischen Wiederholungsversuchen. Wenn die Verzögerung zwischen den Wiederholungsversuchen größer ist als dieser Wert, wird diese stattdessen auf diesen Maximalwert gesetzt. Der Standardwert ist „120“.|
|**retry_interval_backoff_multiplier**|real|Der Multiplikator, der auf die Wiederholungsverzögerung anzuwenden ist, wenn bei mehreren Auftragsschrittausführungen ein Fehler aufgetreten ist. Der Standardwert ist „2,0“.|
|**retry_attempts**|INT|Die Anzahl der Wiederholungsversuche für den Fall, dass dieser Schritt fehlschlägt. Der Standardwert ist „10“, d.h., es werden keine Wiederholungsversuche unternommen.|
|**step_timeout_seconds**|INT|Der Zeitraum in Minuten zwischen zwei Wiederholungsversuchen. Der Standardwert ist „0“, was auf ein 0-Minuten-Intervall hinweist.|
|**output_type**|nvarchar(11)|Adresse des Befehls. In der aktuellen Vorschauversion ist „Inline“ der Standardwert, und nur dieser Wert wird akzeptiert.|
|**output_credential_name**|nvarchar(128)|Der Name der Anmeldeinformationen, mit denen eine Verbindung mit dem Zielserver hergestellt werden soll, um das Resultset zu speichern.|
|**output_subscription_id**|UNIQUEIDENTIFIER|Eindeutige ID des Abonnements des Zielservers bzw. der Zieldatenbank für das Resultset im Zuge der Abfrageausführung.|
|**output_resource_group_name**|nvarchar(128)|Der Name der Ressourcengruppe, in der sich der Zielserver befindet.|
|**output_server_name**|nvarchar(256)|Der Name des Zielservers für das Resultset.|
|**output_database_name**|nvarchar(128)|Der Name der Zieldatenbank für das Resultset.|
|**output_schema_name**|nvarchar(max)|Der Name des Zielschemas. Wird auf den Standardwert „dbo“ festgelegt, wenn kein Name angegeben wird.|
|**output_table_name**|nvarchar(max)|Der Name der Tabelle, in dem das Resultset von den Abfrageergebnissen gespeichert werden soll. Die Tabelle wird automatisch basierend auf dem Schema des Resultsets erstellt, sofern diese noch nicht vorhanden ist. Das Schema muss mit dem Schema des Resultsets übereinstimmen.|
|**max_parallelism**|INT|Die maximale Anzahl von Datenbanken pro Pool für elastische Datenbanken, in der der Auftragsschritt jeweils ausgeführt wird. Der Standardwert ist NULL, d.h., es liegt keine Begrenzung vor. |

### <a name="jobstep_versions-view"></a><a name="jobstep_versions-view"></a>Ansicht „jobstep_versions“

[jobs].[jobstep_versions]

Zeigt alle Schritte in allen Versionen des jeweiligen Auftrags an. Das Schema ist mit [jobsteps](#jobsteps-view) identisch.

### <a name="target_groups-view"></a><a name="target_groups-view"></a>Ansicht „target_groups“

[jobs].[target_groups]

Listet alle Zielgruppen auf.

|Spaltenname|Datentyp|BESCHREIBUNG|
|-----|-----|-----|
|**target_group_name**|nvarchar(128)|Der Name der Zielgruppe, eine Sammlung von Datenbanken.
|**target_group_id**|UNIQUEIDENTIFIER|Eindeutige ID der Zielgruppe.

### <a name="target_group_members-view"></a><a name="target_group_members-view"></a>target_group_members-Ansicht

[jobs].[target_group_members]

Zeigt alle Mitglieder sämtlicher Zielgruppen an.

|Spaltenname|Datentyp|BESCHREIBUNG|
|-----|-----|-----|
|**target_group_name**|nvarchar(128|Der Name der Zielgruppe, eine Sammlung von Datenbanken. |
|**target_group_id**|UNIQUEIDENTIFIER|Eindeutige ID der Zielgruppe.|
|**membership_type**|INT|Gibt an, ob das Zielgruppenelement in der Zielgruppe ein- bzw. ausgeschlossen ist. Gültige Werte für „target_group_name“ sind „Include“ oder „Exclude“.|
|**target_type**|nvarchar(128)|Der Typ der Zieldatenbank oder der Sammlung von Datenbanken, einschließlich aller Datenbanken auf einem Server, alle Datenbanken in einem Pool für elastische Datenbanken oder einer Datenbank. Gültige Werte für „target_type“ sind „SqlServer“, „SqlElasticPool“, „SqlDatabase“ oder „SqlShardMap“.|
|**target_id**|UNIQUEIDENTIFIER|Eindeutige ID des Zielgruppenmitglieds.|
|**refresh_credential_name**|nvarchar(128)|Name der datenbankweit gültigen Anmeldeinformationen, mit denen eine Verbindung mit dem Zielgruppenmitglied hergestellt werden soll.|
|**subscription_id**|UNIQUEIDENTIFIER|Eindeutige ID des Abonnements.|
|**resource_group_name**|nvarchar(128)|Der Name der Ressourcengruppe, in der sich das Zielgruppenelement befindet.|
|**server_name**|nvarchar(128)|Der Name des in der Zielgruppe enthaltenen Servers. Nur angegeben, wenn „target_type“ den Wert „SqlServer“ aufweist. |
|**database_name**|nvarchar(128)|Der Name der in der Zielgruppe enthaltenen Datenbank. Wird nur angegeben, wenn „target_type“ den Wert „SqlDatabase“ aufweist.|
|**elastic_pool_name**|nvarchar(128)|Der Name des in der Zielgruppe enthaltenen Pools für elastische Datenbanken. Wird nur angegeben, wenn „target_type“ den Wert „SqlElasticPool“ enthält.|
|**shard_map_name**|nvarchar(128)|Der Name der in der Zielgruppe enthaltenen Shardzuordnungen. Wird nur angegeben, wenn „target_type“ den Wert „SqlShardMap“ aufweist.|

## <a name="resources"></a>Ressourcen

- ![Symbol für Themenlink](/sql/database-engine/configure-windows/media/topic-link.gif "Symbol für Themenlink") [Transact-SQL-Syntaxkonventionen](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Verwalten von elastischen Aufträgen mithilfe von PowerShell](elastic-jobs-powershell-create.md)
- [Autorisierung und Berechtigungen](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)