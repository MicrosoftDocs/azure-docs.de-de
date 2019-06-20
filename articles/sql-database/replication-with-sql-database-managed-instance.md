---
title: Konfigurieren der Replikation in einer verwalteten Azure SQL-Datenbank-Instanzdatenbank | Microsoft-Dokumentation
description: Hier finden Sie Informationen zum Konfigurieren der Transaktionsreplikation in einer verwalteten Azure SQL-Datenbank-Instanzdatenbank.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926197"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurieren der Replikation in einer verwalteten Azure SQL-Datenbank-Instanzdatenbank

Die Transaktionsreplikation ermöglicht es Ihnen, Daten aus einer SQL Server-Datenbank oder einer anderen Instanzdatenbank in eine verwaltete Azure SQL-Datenbank-Instanzdatenbank zu replizieren. 

Sie können die Transaktionsreplikation auch verwenden, um Änderungen, die in einer verwalteten Azure SQL-Datenbankinstanz an einer Instanzdatenbank vorgenommen wurden, mithilfe von Push zu übertragen an:

- Eine SQL Server-Datenbank.
- Eine Einzeldatenbank in einer Azure SQL-Datenbank.
- Eine Pooldatenbank in einem Pool für elastische Azure SQL-Datenbanken.
 
Die Transaktionsreplikation steht als Public Preview in der [verwalteten Azure SQL-Datenbankinstanz](sql-database-managed-instance.md) zur Verfügung. Eine verwaltete Instanz kann Verleger-, Verteiler- und Abonnentendatenbanken hosten. Unter [Konfigurationen für die Transaktionsreplikation](sql-database-managed-instance-transactional-replication.md#common-configurations) finden Sie die verfügbaren Konfigurationen.

  > [!NOTE]
  > Dieser Artikel soll einen Benutzer durch die End-to-End-Konfiguration einer Replikation bei einer verwalteten Azure-Datenbankinstanz führen, beginnend mit dem Erstellen der Ressourcengruppe. Wenn Sie verwaltete Instanzen schon bereitgestellt haben, fahren Sie mit [Schritt 4](#4---create-a-publisher-database) fort, um Ihre Verlegerdatenbank zu erstellen, oder mit [Schritt 6](#6---configure-distribution) fort, wenn Sie bereits eine Verleger- oder Abonnentendatenbank haben und mit dem Konfigurieren der Replikation beginnen möchten.  

## <a name="requirements"></a>Requirements (Anforderungen)

Zum Konfigurieren einer verwalteten Instanz, die als Verleger und/oder Verteiler fungieren soll, ist Folgendes erforderlich:

- Die verwaltete Instanz ist derzeit nicht an einer Georeplikationsbeziehung beteiligt.
- Die verwaltete Verlegerinstanz befindet sich in demselben virtuellen Netzwerk wie der Verteiler und der Abonnent, oder [VNET-Peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) wurde zwischen den virtuellen Netzwerken aller drei Entitäten eingerichtet. 
- Für die Verbindung zwischen den Teilnehmern der Replikation wird SQL-Authentifizierung verwendet.
- Ein Azure-Speicherkonto für das Arbeitsverzeichnis für die Replikation.
- Port 445 (TCP ausgehend) ist in den Sicherheitsregeln von NSG (Netzwerksicherheitsgruppe) für die verwalteten Instanzen geöffnet, um auf die Azure-Dateifreigabe zugreifen zu können. 


 > [!NOTE]
 > Einzel- und Pooldatenbanken in Azure SQL-Datenbank können nur Abonnenten sein. 


## <a name="features"></a>Features

Unterstützt:

- Mischung aus Transaktions- und Momentaufnahmereplikation von lokalen und verwalteten SQL Server-Instanzen in Azure SQL-Datenbank.
- Abonnenten können in lokalen SQL Server-Datenbanken, einzelnen Datenbanken/verwalteten Instanzen in Azure SQL-Datenbank oder in einer Datenbank enthalten sein, die sich in einem Pool für elastische Datenbanken in Azure SQL-Datenbank befindet.
- Unidirektionale oder bidirektionale Replikation.

Folgende Features werden in einer verwalteten Instanz in Azure SLQ-Datenbank nicht unterstützt:

- [Aktualisierbare Abonnements](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktive Georeplikation](sql-database-active-geo-replication.md) und [Autofailover-Gruppen](sql-database-auto-failover-group.md) sollten nicht verwendet werden, wenn die Transaktionsreplikation konfiguriert ist.
 
## <a name="1---create-a-resource-group"></a>1 – Erstellen einer Ressourcengruppe

Verwenden Sie das [Azure-Portal](https://portal.azure.com), um eine Ressourcengruppe mit dem Namen `SQLMI-Repl` zu erstellen.  

## <a name="2---create-managed-instances"></a>2 – Erstellen von verwalteten Instanzen

Verwenden Sie das [Azure-Portal](https://portal.azure.com), um zwei [verwaltete Instanzen](sql-database-managed-instance-create-tutorial-portal.md) in demselben virtuellen Netzwerk und Subnetz zu erstellen. Die beiden verwalteten Instanzen sollten benannt werden:

- `sql-mi-pub`
- `sql-mi-sub`

Sie müssen auch [eine Azure-VM konfigurieren zum Herstellen einer Verbindung](sql-database-managed-instance-configure-vm.md) mit Ihren verwalteten Azure SQL-Datenbankinstanzen. 

## <a name="3---create-azure-storage-account"></a>3 – Erstellen eines Azure-Speicherkontos

[Erstellen Sie ein Azure-Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) für das Arbeitsverzeichnis und anschließend im Speicherkonto eine [Dateifreigabe](../storage/files/storage-how-to-create-file-share.md). 

Kopieren Sie den Dateifreigabepfad im Format: `\\storage-account-name.file.core.windows.net\file-share-name`

Kopieren Sie die Speicherzugriffsschlüssel im Format: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Weitere Informationen finden Sie unter [Anzeigen und Kopieren von Speicherzugriffsschlüsseln](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 – Erstellen einer Verlegerdatenbank

Stellen Sie mithilfe von SQL Server Management Studio eine Verbindung mit Ihrer verwalteten `sql-mi-pub`-Instanz her, und führen Sie den folgenden Transact-SQL (T-SQL)-Code zum Erstellen Ihrer Verlegerdatenbank aus:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 – Erstellen einer Abonnentendatenbank

Stellen Sie mithilfe von SQL Server Management Studio eine Verbindung mit Ihrer verwalteten Instanz `sql-mi-sub` her, und führen Sie den folgenden T-SQL-Code zum Erstellen Ihrer leeren Abonnentendatenbank aus:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 – Konfigurieren der Verteilung

Stellen Sie mithilfe von SQL Server Management Studio eine Verbindung mit Ihrer verwalteten Instanz `sql-mi-pub` her, und führen Sie den folgenden T-SQL-Code zum Konfigurieren Ihrer Verteilungsdatenbank aus: 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – Konfigurieren des Verlegers zur Verwendung des Verteilers 

Ändern Sie auf Ihrer verwalteten Verlegerinstanz `sql-mi-pub` die Abfrageausführung in den [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)-Modus, und führen Sie den folgenden Code aus, um den neuen Verteiler bei Ihrem Verleger zu registrieren. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

Dieses Skript konfiguriert einen lokalen Verleger auf der verwalteten Instanz, fügt einen verknüpften Server hinzu und erstellt eine Gruppe von Aufträgen für den SQL Server-Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 – Erstellen von Veröffentlichung und Abonnent

Führen Sie im [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)-Modus das folgende T-SQL-Skript aus, um die Replikation für Ihre Datenbank zu aktivieren und die Replikation zwischen Ihrem Verleger, Verteiler und Abonnenten zu konfigurieren. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – Ändern der Agentparameter

Bei der verwalteten Azure SQL-Datenbankinstanz gibt es zurzeit einige Back-End-Probleme im Hinblick auf die Konnektivität mit den Replikations-Agents. Während dieses Problem behoben wird, ist dies die Problemumgehung zum Verlängern des Anmeldungstimeoutwerts für Replikations-Agents. 

Führen Sie den folgenden T-SQL-Befehl auf dem Verleger aus, um das Anmeldungstimeout zu verlängern: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Falls es erforderlich sein sollte, führen Sie den folgenden T-SQL-Befehl erneut aus, um das Anmeldungstimeout wieder auf den Standardwert zu setzen:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Starten Sie alle drei Agents neu, um diese Änderungen zu übernehmen. 

## <a name="10---test-replication"></a>10 – Testen der Replikation

Nachdem die Replikation konfiguriert wurde, können Sie sie testen, indem Sie neue Elemente auf dem Verleger einfügen und beobachten, wie die Änderungen an den Abonnenten weitergegeben werden. 

Führen Sie den folgenden T-SQL-Codeausschnitt aus, um die Zeilen auf dem Abonnenten anzuzeigen:

```sql
select * from dbo.ReplTest
```

Führen Sie den folgenden T-SQL-Codeausschnitt aus, um zusätzliche Zeilen auf dem Verleger einzufügen. Überprüfen Sie dann die Zeilen erneut auf dem Abonnenten. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Veröffentlichung zu löschen, führen Sie den folgenden T-SQL-Befehl aus:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Um die Replikationsoption aus der Datenbank zu entfernen, führen Sie den folgenden T-SQL-Befehl aus:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Um die Veröffentlichung und Verteilung zu deaktivieren, führen Sie den folgenden T-SQL-Befehl aus:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Sie können Ihre Azure-Ressourcen bereinigen, indem Sie [die verwalteten Instanzressourcen aus der Ressourcengruppe löschen](../azure-resource-manager/manage-resources-portal.md#delete-resources) und anschließend die Ressourcengruppe `SQLMI-Repl` löschen. 

   
## <a name="see-also"></a>Siehe auch

- [Transaktionsreplikation](sql-database-managed-instance-transactional-replication.md)
- [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md)
