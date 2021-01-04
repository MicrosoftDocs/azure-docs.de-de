---
title: 'Verschieben von Daten auf einen virtuellen SQL Server-Computer: Team Data Science-Prozess'
description: Verschieben von Daten aus Flatfiles oder von einer lokalen SQL Server-Instanz zu SQL Server auf einer Azure-VM
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
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320333"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Verschieben von Daten zu SQL Server auf einem virtuellen Azure-Computer

In diesem Artikel werden die Optionen zum Verschieben von Daten aus Flatfiles (CSV- oder TSV-Format) oder von einer lokalen SQL Server-Instanz in SQL Server auf einem virtuellen Azure-Computer beschrieben. Diese Tasks zum Verschieben von Daten in die Cloud sind Teil des Team Data Science-Prozesses.

Ein Thema, in dem die Optionen für das Verschieben von Daten in eine Azure SQL-Datenbank für Machine Learning beschrieben werden, finden Sie unter [Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning](move-sql-azure.md).

In der folgenden Tabelle sind die Optionen zum Verschieben von Daten zu SQL Server auf einem virtuellen Azure-Computer zusammengefasst.

| <b>QUELLE</b> | <b>ZIEL: SQL Server auf virtuellen Azure-Computern</b> |
| --- | --- |
| <b>Flatfile</b> |1. <a href="#insert-tables-bcp">Befehlszeilenprogramm zum Massenkopieren (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">SQL-Abfrage zum Masseneinfügen </a><br> 3. <a href="#sql-builtin-utilities">Integrierte grafische Hilfsprogramme in SQL Server</a> |
| <b>Lokaler SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer</a><br> 2. <a href="#export-flat-file">Exportieren in eine Flatfile </a><br> 3. <a href="#sql-migration">SQL-Datenbankmigrations-Assistent</a> <br> 4. <a href="#sql-backup">Datenbanksicherung und -wiederherstellung</a><br> |

In diesem Dokument wird davon ausgegangen, dass die SQL-Befehle in SQL Server Management Studio oder im Datenbank-Explorer von Visual Studio ausgeführt werden.

> [!TIP]
> Alternativ dazu können Sie [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) verwenden, um eine Pipeline zu erstellen und zu planen, die Daten in eine SQL Server-VM auf Azure verschiebt. Weitere Informationen hierzu finden Sie unter [Kopieren von Daten mit Azure Data Factory (Kopieraktivität)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Voraussetzungen
In diesem Tutorial wird Folgendes vorausgesetzt:

* Ein **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/)registrieren.
* Ein **Azure-Speicherkonto**. Sie benötigen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) . Nachdem Sie das Speicherkonto erstellt haben, müssen Sie den Kontoschlüssel für den Zugriff auf den Speicher abrufen. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).
* Bereitgestellter **SQL Server auf einem virtuellen Azure-Computer**. Anleitungen finden Sie unter [Einrichten eines virtuellen Azure SQL Server-Computers als IPython Notebook-Server für die erweiterte Analyse](../data-science-virtual-machine/overview.md).
* Lokal installierte und konfigurierte **Azure PowerShell** . Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Verschieben von Daten aus einer Flatfilequelle zu SQL Server auf einem virtuellen Azure-Computer
Wenn sich Ihre Daten in einer Flatfile (in Zeilen/Spalten angeordnet) befinden, können sie über die folgenden Methoden auf eine SQL Server-VM in Azure verschoben werden:

1. [Befehlszeilenprogramm zum Massenkopieren (BCP)](#insert-tables-bcp)
2. [SQL-Abfrage zum Masseneinfügen](#insert-tables-bulkquery)
3. [Integrierte grafische Hilfsprogramme in SQL Server (Importieren/Exportieren, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Befehlszeilenprogramm zum Massenkopieren (BCP)
BPC ist ein Befehlszeilenprogramm, das mit SQL Server installiert wird und eine der schnellsten Möglichkeiten zum Verschieben von Daten darstellt. Es funktioniert für alle drei SQL Server-Varianten (lokaler SQL Server, SQL Azure und SQL Server-VM in Azure).

> [!NOTE]
> **Wo sollten sich die Daten für die Verwendung mit BCP befinden?**  
> Auch wenn dies nicht erforderlich ist, wird die Übertragung beschleunigt, wenn sich die Ausgangsdaten auf demselben Computer wie der Ziel-SQL Server befinden (Netzwerkgeschwindigkeit im Vergleich zur E/A-Geschwindigkeit des lokalen Datenträgers). Sie können die Flatfiles mit den Daten auf den Computer verschieben, auf dem SQL Server installiert ist. Dazu stehen verschiedene Dateikopiertools wie [AZCopy](../../storage/common/storage-use-azcopy-v10.md), [Azure Storage-Explorer](https://storageexplorer.com/) oder das Kopieren und Einfügen unter Windows über RDP (Remotedesktopprotokoll) zur Verfügung.
>
>

1. Stellen Sie sicher, dass die Datenbank und die Tabellen in der SQL Server-Zieldatenbank erstellt werden. Es folgt ein Beispiel für die Durchführung unter Verwendung der Befehle `Create Database` und `Create Table`:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Generieren Sie die Formatdatei, die das Schema für die Tabelle beschreibt, durch Eingabe des folgenden Befehls an der Befehlszeile des Computers, auf dem BPC installiert ist.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Fügen Sie die Daten in die Datenbank mit dem bcp-Befehl ein, der über die Befehlszeile funktionieren sollte, wenn SQL Server auf demselben Computer installiert ist:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimieren von BCP-Einfügevorgängen** : Im Artikel [Richtlinien zur Optimierung von Massenimport](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) finden Sie Informationen zum Optimieren von Massenimportvorgängen.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Parallelisieren von Einfügevorgängen für schnellere Datenverschiebungen
Wenn die Daten, die Sie verschieben möchten, sehr umfangreich sind, können Sie den Vorgang beschleunigen, indem Sie mehrere BPC-Befehle parallel in einem PowerShell-Skript ausführen.

> [!NOTE]
> **Erfassen großer Datenmengen** Partitionieren Sie zur Optimierung der Ladevorgänge bei großen bis sehr großen Datasets Ihre logischen und physischen Datenbanktabellen mithilfe mehrerer Dateigruppen und Partitionstabellen. Weitere Informationen zum Erstellen und Laden von Daten in Partitionstabellen finden Sie unter [Paralleles Laden von SQL-Partitionstabellen](parallel-load-sql-partitioned-tables.md).
>
>

Das folgende PowerShell-Beispielskript veranschaulicht das parallele Einfügen mithilfe von BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL-Abfrage zum Masseneinfügen
Mit der [SQL-Abfrage zum Masseneinfügen](/sql/t-sql/statements/bulk-insert-transact-sql) können Sie Daten aus zeilen-/spaltenbasierten Dateien in die Datenbank importieren (Informationen zu den unterstützten Typen finden Sie unter [Vorbereiten von Daten für den Massenexport oder -import [SQL Server])](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server).

Im Folgenden sehen Sie einige Beispielbefehle für Masseneinfügungen:  

1. Analysieren Sie vor dem Importieren Ihre Daten, und legen Sie benutzerdefinierte Optionen fest, um sicherzustellen, dass die SQL Server-Datenbank dasselbe Format für alle Sonderfelder wie z. B. Datumsangaben annimmt. Hier ist ein Beispiel für das Festlegen des Datumsformats Jahr-Monat-Tag (wenn die Daten das Datum im Jahr-Monat-Tag-Format enthalten):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importieren von Daten mit Anweisungen zum Massenimport:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Integrierte Hilfsprogramme in SQL Server
Sie können die SQL Server Integration Services (SSIS) zum Importieren von Daten aus einer Flatfile in die SQL Server-VM in Azure verwenden.
SSIS ist in zwei Studio-Umgebungen verfügbar. Weitere Informationen finden Sie unter [Integration Services (SSIS) und Studio-Umgebungen](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* Einzelheiten zu den SQL Server Data Tools finden Sie unter [Microsoft SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)  
* Ausführliche Informationen zum Import/Export-Assistenten finden Sie unter [SQL Server-Import/Export-Assistent](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Verschieben von Daten von einer lokalen SQL Server-Instanz nach SQL Server auf einem virtuellen Azure-Computer
Sie können auch die folgenden Migrationsstrategien verwenden:

1. [Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportieren in eine Flatfile](#export-flat-file)
3. [SQL-Datenbankmigrations-Assistent](#sql-migration)
4. [Datenbanksicherung und -wiederherstellung](#sql-backup)

Diese Optionen werden im Folgenden beschrieben:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer
Der **Assistent zum Bereitstellen einer SQL Server-Datenbank auf einem virtuellen Microsoft Azure-Computer** ist eine einfache und empfohlene Möglichkeit zum Verschieben von Daten von einer lokalen SQL Server-Instanz nach SQL Server auf einem virtuellen Azure-Computer. Ausführliche Schritte sowie eine Erläuterung weitere Alternativen finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportieren in eine Flatfile
Für das Massenexportieren von Daten von einer lokalen SQL Server-Instanz stehen verschiedene Vorgehensweisen zur Verfügung, die unter [Massenimport und -export von Daten [SQL Server]](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) beschrieben werden. In diesem Dokument wird als Beispiel BPC (Bulk Copy Program) beschrieben. Nachdem die Daten in eine Flatfile exportiert wurden, können sie mit einem Massenimport auf einen anderen SQL Server importiert werden.

1. Exportieren Sie die Daten von der lokalen SQL Server-Instanz folgendermaßen mit dem Hilfsprogramm bcp in eine Datei:

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Erstellen Sie die Datenbank und die Tabelle auf der SQL Server-VM in Azure mithilfe der Befehle `create database` und `create table` für das in Schritt 1 exportierte Tabellenschema.
3. Erstellen Sie eine Formatdatei zur Beschreibung des Tabellenschemas der zu exportierenden/importierenden Daten. Einzelheiten zur Formatdatei finden Sie unter [Erstellen einer Formatdatei (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Generieren der Formatdatei bei Ausführung von bcp auf dem SQL Server-Computer

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Generieren der Formatdatei bei Remoteausführung von BPC auf dem SQL Server:

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Verwenden Sie eine der im Abschnitt [Dateiquelle](#filesource_to_sqlonazurevm) beschriebenen Methoden, um die Daten in Flatfiles auf einen SQL Server zu verschieben.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>SQL-Datenbankmigrations-Assistent
[SQL-Datenbankmigrations-Assistent](https://sqlazuremw.codeplex.com/) stellt eine benutzerfreundliche Möglichkeit zum Verschieben von Daten zwischen zwei SQL Server-Instanzen dar. Er bietet Ihnen die Möglichkeit, das Datenschema zwischen Quell- und Zieltabellen zuzuordnen und die Spaltentypen auszuwählen, sowie verschiedene andere Funktionen. Im Hintergrund wird BPC zum Massenkopieren verwendet. Nachfolgend finden Sie einen Screenshot des Begrüßungsbildschirms für den SQL-Datenbankmigrations-Assistenten.  

![SQL Server-Migrations-Assistent][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Datenbanksicherung und -wiederherstellung
SQL Server unterstützt:

1. [Funktionen zur Datenbanksicherung und -wiederherstellung](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (sowohl in einer lokalen Datei als auch per bacpac-Export in ein Blob) und [Datenebenenanwendungen](/sql/relational-databases/data-tier-applications/data-tier-applications) (mit bacpac).
2. Die Möglichkeit, SQL Server-VMs in Azure direkt mit einer kopierten Datenbank zu erstellen oder eine vorhandenen Datenbank in SQL-Datenbank zu kopieren. Weitere Informationen finden Sie unter [Use the Copy Database Wizard](/sql/relational-databases/databases/use-the-copy-database-wizard).

Einen Screenshot der Optionen für das Sichern/Wiederherstellen von Datenbanken in SQL Server Management Studio finden Sie unten.

![SQL Server-Importtool][1]

## <a name="resources"></a>Ressourcen
[Migrieren einer Datenbank zu SQL Server auf einem virtuellen Azure-Computer](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Übersicht zu SQL Server auf virtuellen Azure-Computern](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png