---
title: Konfigurieren der Replikation zwischen verwalteten Instanzen
titleSuffix: Azure SQL Managed Instance
description: In diesem Tutorial erfahren Sie, wie Sie die Transaktionsreplikation zwischen einem Verleger/Verteiler einer Azure SQL Managed Instance und einem Abonnenten der SQL Managed Instance konfigurieren.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 67902073b1484835d23566c91cbfae6f1d80d249
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900454"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Tutorial: Konfigurieren der Replikation zwischen zwei verwalteten Instanzen

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Mithilfe der Transaktionsreplikation können Sie Daten von einer Datenbank auf eine andere replizieren, die auf SQL Server oder [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) gehostet wird. SQL Managed Instance kann in der Replikationstopologie ein Verleger, Verteiler oder Abonnent sein. Unter [Konfigurationen für die Transaktionsreplikation](replication-transactional-overview.md#common-configurations) finden Sie die verfügbaren Konfigurationen. 

Die Transaktionsreplikation befindet sich zurzeit in der öffentlichen Vorschau für SQL Managed Instance. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Konfigurieren einer verwalteten Instanz als Replikationsverleger und -verteiler.
> - Konfigurieren einer verwalteten Instanz als Replikationsabonnent.

![Replikation zwischen zwei verwalteten Instanzen](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Dieses Tutorial ist für erfahrene Benutzer bestimmt. Es wird vorausgesetzt, dass sich Benutzer sowohl für verwaltete Instanzen als auch für SQL Server-VMs in Azure mit der Bereitstellung und Verbindungsherstellung auskennen. 


> [!NOTE]
> - In diesem Artikel wird die Verwendung der [Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication) in einer verwalteten SQL-Datenbank-Instanz beschrieben. Sie steht nicht im Zusammenhang mit [Failovergruppen](../database/auto-failover-group-overview.md). Dieses verwaltete Azure SQL Managed Instance-Feature ermöglicht das Erstellen vollständiger Lesereplikate einzelner Instanzen. Beim Konfigurieren der [Transaktionsreplikation mit Failovergruppen](replication-transactional-overview.md#with-failover-groups) sind weitere Aspekte zu berücksichtigen.



## <a name="requirements"></a>Requirements (Anforderungen)

Zum Konfigurieren einer SQL Managed Instance, die als Verleger und/oder Verteiler fungieren soll, ist Folgendes erforderlich:

- Die verwaltete Verlegerinstanz befindet sich im selben virtuellen Netzwerk wie der Verteiler und der Abonnent, oder [VPN-Gateways](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) wurden zwischen den virtuellen Netzwerken aller drei Entitäten konfiguriert. 
- Für die Verbindung zwischen den Teilnehmern der Replikation wird SQL-Authentifizierung verwendet.
- Ein Azure-Speicherkonto für das Arbeitsverzeichnis für die Replikation.
- Port 445 (TCP ausgehend) ist in den Sicherheitsregeln von NSG (Netzwerksicherheitsgruppe) für die verwalteten Instanzen geöffnet, um auf die Azure-Dateifreigabe zugreifen zu können.  Wenn Sie den Fehler `failed to connect to azure storage \<storage account name> with os error 53` erhalten, müssen Sie der NSG des Subnetzes der entsprechenden SQL Managed Instance eine Ausgangsregel hinzufügen.

## <a name="1---create-a-resource-group"></a>1 – Erstellen einer Ressourcengruppe

Verwenden Sie das [Azure-Portal](https://portal.azure.com), um eine Ressourcengruppe mit dem Namen `SQLMI-Repl` zu erstellen.  

## <a name="2---create-managed-instances"></a>2 – Erstellen von verwalteten Instanzen

Verwenden Sie das [Azure-Portal](https://portal.azure.com), um zwei [SQL Managed Instance](instance-create-quickstart.md) in demselben virtuellen Netzwerk und Subnetz zu erstellen. Benennen Sie die beiden verwalteten Instanzen beispielsweise:

- `sql-mi-pub` (zusammen mit einigen Zeichen für die Randomisierung)
- `sql-mi-sub` (zusammen mit einigen Zeichen für die Randomisierung)

Sie müssen auch [eine Azure-VM konfigurieren, um eine Verbindung](connect-vm-instance-configure.md) mit Ihren verwalteten Instanzen herzustellen. 

## <a name="3---create-an-azure-storage-account"></a>3 – Erstellen eines Azure-Speicherkontos

[Erstellen Sie ein Azure-Speicherkonto](../../storage/common/storage-account-create.md#create-a-storage-account) für das Arbeitsverzeichnis und anschließend im Speicherkonto eine [Dateifreigabe](../../storage/files/storage-how-to-create-file-share.md). 

Kopieren Sie den Dateifreigabepfad im Format: `\\storage-account-name.file.core.windows.net\file-share-name`

Beispiel: `\\replstorage.file.core.windows.net\replshare`

Kopieren Sie die Speicherzugriffsschlüssel im Format: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Beispiel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md). 

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

Ändern Sie auf Ihrer SQL Managed Instance des Verlegers `sql-mi-pub` die Abfrageausführung in den [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)-Modus, und führen Sie den folgenden Code aus, um den neuen Verteiler bei Ihrem Verleger zu registrieren.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Achten Sie darauf, dass Sie für den Parameter file_storage nur umgekehrte Schrägstriche (`\`) verwenden. Die Verwendung eines regulären Schrägstrichs (`/`) kann zu einem Fehler führen, wenn die Verbindung mit der Dateifreigabe hergestellt wird.

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


-- Configure your log reader agent
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
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – Ändern der Agentparameter

Bei der Azure SQL Managed Instance gibt es zurzeit einige Back-End-Probleme im Hinblick auf die Konnektivität mit den Replikations-Agents. Während dieses Problem behoben wird, ist dies die Problemumgehung zum Verlängern des Anmeldungstimeoutwerts für Replikations-Agents.

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

Sie können Ihre Azure-Ressourcen bereinigen, indem Sie [die SQL Managed Instance-Ressource aus der Ressourcengruppe löschen](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) und anschließend die Ressourcengruppe `SQLMI-Repl` löschen. 

## <a name="next-steps"></a>Nächste Schritte

Sie erhalten außerdem weitere Informationen zur [Transaktionsreplikation mit Azure SQL Managed Instance](replication-transactional-overview.md) oder zur Konfiguration der Replikation zwischen einem [SQL Managed Instance-Verleger/-Verteiler und einem SQL auf Azure-VM-Abonnenten](replication-two-instances-and-sql-server-configure-tutorial.md).