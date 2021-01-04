---
title: Paralleler Massenimport von Daten in SQL-Partitionstabellen – Team Data Science-Prozess
description: Erstellen Sie partitionierte Tabellen für den schnellen parallelen Massenimport von Daten in eine SQL Server-Datenbank.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322419"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Erstellen und Optimieren von Tabellen für den schnellen parallelen Import von Daten in eine SQL Server-Instanz auf einer Azure-VM

In diesem Artikel wird das Erstellen partitionierter Tabellen für das schnelle parallele Massenimportieren von Daten in eine SQL Server-Datenbank beschrieben. Für das Laden/Übertragen von Big Data in eine SQL-Datenbank können das Importieren von Daten in die SQL-Datenbank sowie die nachfolgenden Abfragen mithilfe von *partitionierten Tabellen und Sichten* verbessert werden. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Erstellen einer neuen Datenbank und eines Satzes von Dateigruppen
* [Erstellen einer neuen Datenbank](/sql/t-sql/statements/create-database-transact-sql), falls noch nicht vorhanden.
* Fügen Sie der Datenbank Datenbankdateigruppen hinzu, die die partitionierten physischen Dateien enthalten. 
* Dies kann bei einer neuen Datenbank mit [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql) und bei einer bereits vorhandenen Datenbank mit [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options) erfolgen.
* Fügen Sie (je nach Anforderungen) den einzelnen Datenbank-Dateigruppen eine oder mehrere Dateien hinzu.
  
  > [!NOTE]
  > Geben Sie die Zieldateigruppe, die die Daten für diese Partition enthält, und die Dateinamen der physischen Datenbank an, in der die Dateigruppendaten gespeichert sind.
  > 
  > 

Mit dem folgenden Beispiel wird eine neue Datenbank mit drei Dateigruppen erstellt, die sich von den primären und Protokollgruppen unterscheiden und jeweils eine physische Datei enthalten. Die Datenbankdateien werden im Standarddatenordner von SQL Server erstellt, der in der SQL Server-Instanz konfiguriert wurde. Weitere Informationen zu den Standarddateispeicherorten finden Sie unter [Dateispeicherorte für Standard- und benannte Instanzen von SQL Server](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server).

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Erstellen einer partitionierten Tabelle
Um die partitionierten Tabellen gemäß dem Datenschema zu erstellen, das den im vorherigen Schritt erstellten Datenbankdateigruppen zugeordnet ist, müssen Sie zunächst eine Partitionsfunktion und ein Partitionsschema erstellen. Wenn die Daten per Massenimport in die partitionierten Tabellen importiert werden, werden die Datensätze gemäß einem Partitionsschema wie unten beschrieben auf Dateigruppen verteilt.

### <a name="1-create-a-partition-function"></a>1. Erstellen einer Partitionsfunktion
[Erstellen Sie eine Partitionsfunktion](/sql/t-sql/statements/create-partition-function-transact-sql), die den Datenbereich/die Grenzen für die einzelnen Partitionstabellen definiert. Im folgenden Beispiel werden die Partitionen nach „month(some\_datetime\_field)“ im Jahr 2013 begrenzt:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. Erstellen eines Partitionsschemas
[Erstellen Sie ein Partitionschema](/sql/t-sql/statements/create-partition-scheme-transact-sql). Dieses Schema ordnet jeden Partitionsbereich in der Partitionsfunktion einer physischen Dateigruppe zu. Beispiel:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
Um die gültigen Bereiche in den einzelnen Partitionen nach Funktion/Schema zu überprüfen, führen Sie die folgende Abfrage durch:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. Erstellen einer Partitionstabelle
[Erstellen Sie die partitionierten Tabellen](/sql/t-sql/statements/create-table-transact-sql) gemäß dem Datenschema, und geben Sie das Partitionsschema und das Einschränkungsfeld zur Partitionierung der Tabelle an. Beispiel:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

Weitere Informationen finden Sie unter [Erstellen partitionierter Tabellen und Indizes](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Massenimport der Daten für die einzelne Partitionstabellen

* Sie können BCP, BULK INSERT oder andere Methoden wie den [SQL-Datenbankmigrations-Assistenten](https://sqlazuremw.codeplex.com/)verwenden. Im Beispiel wird BPC verwendet.
* [Ändern Sie für die Datenbank](/sql/t-sql/statements/alter-database-transact-sql-set-options) das Transaktionsprotokollierungsschema in BULK_LOGGED, um den Aufwand für die Protokollierung zu minimieren. Beispiel:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Starten Sie zum Beschleunigen des Ladevorgangs der Daten die Massenimportvorgänge parallel. Tipps zur schnelleren Verarbeitung von Massenimporten großer Datenmengen in SQL Server-Datenbanken finden Sie unter [Laden von 1 TB in weniger als 1 Stunde](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour).

Das folgende PowerShell-Skript ist ein Beispiel für das parallele Laden von Daten mit BPC.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Erstellen von Indizes zum Optimieren der Leistung beim Zusammenführen und Abfragen
* Wenn Sie Daten für die Modellierung aus mehreren Tabellen extrahieren, erstellen Sie die Indizes für die Verknüpfungsschlüssel zur Verbesserung der Leistung beim Zusammenführen.
* [Erstellen Sie Indizes](/sql/t-sql/statements/create-index-transact-sql) (gruppiert oder nicht gruppiert), deren Ziel dieselbe Dateigruppe für jede Partition ist. Beispiel:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > Sie können auch die Indizes vor dem Massenimport von Daten erstellen. Die Indexerstellung vor dem Massenimport verlangsamt das Laden der Daten.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Advanced Analytics Process and Technology in Aktion – Beispiel
Eine lückenlose exemplarische Vorgehensweise zur Verwendung des Team Data Science-Prozesses mit einem öffentlichen Dataset finden Sie unter [Der Team Data Science-Prozess in Aktion: Verwenden von SQL Server](sql-walkthrough.md).