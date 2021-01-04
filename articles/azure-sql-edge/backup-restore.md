---
title: 'Sichern und Wiederherstellen von Datenbanken: Azure SQL Edge'
description: Erfahren Sie mehr über Sicherungs- und Wiederherstellungsfunktionen in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395239"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Sichern und Wiederherstellen von Datenbanken in Azure SQL Edge 

Azure SQL Edge basiert auf der neuesten Version der Microsoft SQL Server-Datenbank-Engine. Die Lösung bietet ähnliche Funktionen zum Sichern und Wiederherstellen von Datenbanken, wie Sie in SQL Server unter Linux und für in Containern ausgeführten SQL Server-Instanzen verfügbar sind. Mit der Sicherungs- und Wiederherstellungskomponente wird eine wichtige Vorkehrung zum Schutz von in Azure SQL Edge-Datenbanken gespeicherten Daten bereitgestellt. 

Um die Gefahr eines schwerwiegenden Datenverlusts zu minimieren, sollten Sie Ihre Datenbanken regelmäßig sichern, damit Änderungen an Ihren Daten nicht verloren gehen. Eine gut geplante Sicherungs- und Wiederherstellungsstrategie hilft, Datenbanken vor Datenverlusten zu schützen, die durch eine Vielzahl von Fehlern verursacht werden können. Testen Sie Ihre Strategie, indem Sie einen Sicherungssatz und anschließend Ihre Datenbank wiederherstellen, um sich auf die effektive Reaktion auf einen Notfall vorzubereiten.

Weitere Informationen dazu, warum Sicherungen wichtig sind, finden Sie unter [Sichern und Wiederherstellen von SQL Server-Datenbanken](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Azure SQL Edge ermöglicht die Sicherung in und Wiederherstellung aus lokalem Speicher und Azure-Blobs. Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung von SQL Server mit Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) und [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Sichern einer Datenbank in Azure SQL Edge

Azure SQL Edge unterstützt die gleichen Sicherungstypen wie SQL Server. Eine vollständige Liste finden Sie in der [Übersicht für die Sicherung](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> In Azure SQL Edge erstellte Datenbanken verwenden standardmäßig das einfache Wiederherstellungsmodell. Daher können für diese Datenbanken keine Protokollsicherungen erfolgen. Wenn dies erforderlich sein sollte, muss ein Administrator das Modell für die Datenbankwiederherstellung in das vollständige Wiederherstellungsmodell ändern. Eine vollständige Liste der von SQL Server unterstützten Wiederherstellungsmodelle finden Sie in der [Übersicht über das Wiederherstellungsmodell](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Sichern auf lokalem Datenträger

Im folgenden Beispiel erstellen Sie mit dem Transact-SQL-Befehl `BACKUP DATABASE` eine Datenbanksicherung im Container. Im Rahmen dieses Beispiels erstellen Sie einen neuer Ordner mit dem Namen *backup* , in dem die Sicherungsdateien gespeichert werden.

1. Erstellen Sie einen Ordner für die Sicherungen. Führen Sie diesen Befehl auf dem Host aus, auf dem sich der Azure SQL Edge-Container befindet. Ersetzen Sie im folgenden Befehl **<AzureSQLEdge_Container_Name>** durch den Namen des Azure SQL Edge-Containers in Ihrer Bereitstellung.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Stellen Sie mithilfe von SQL Server Management Studio (SSMS) oder Azure Data Studio eine Verbindung mit der Azure SQL Edge-Instanz her. Führen Sie den Befehl `BACKUP DATABASE` aus, um die Sicherung der Benutzerdatenbank zu erstellen. Im folgenden Beispiel erstellen Sie eine Sicherung der Datenbank *IronOreSilicaPrediction*.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Nachdem Sie den Befehl ausgeführt haben und die Sicherung der Datenbank erfolgreich war, werden im Abschnitt „Ergebnisse“ von SQL Server Management Studio oder Azure Data Studio Meldungen ähnlich den folgenden angezeigt.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>Sichern über URL

Azure SQL Edge unterstützt Sicherungen in Seiten- und Blockblobs. Weitere Informationen finden Sie unter [Sicherung: Blockblobs vs. Seitenblobs](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob). Im folgenden Beispiel wird die Datenbank *IronOreSilicaPrediction* in einem Blockblob gesichert. 

1. Um Sicherungen in Blockblobs zu konfigurieren, generieren Sie zuerst ein SAS-Token (Shared Access Signature), mit dem SQL Server-Anmeldeinformationen für Azure SQL Edge erstellt werden können. Das Skript erstellt eine SAS, die einer gespeicherten Zugriffsrichtlinie zugeordnet ist. Weitere Informationen finden Sie unter [Shared Access Signatures, Teil 1: Grundlegendes zum SAS-Modell](../storage/common/storage-sas-overview.md). Das Skript schreibt auch den T-SQL-Befehl, der zum Erstellen der Anmeldeinformationen unter SQL Server erforderlich ist. Im folgenden Skript wird davon ausgegangen, dass Sie bereits über ein Azure-Abonnement mit einem Speicherkonto und einen Speichercontainer für die Sicherungen verfügen.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Nachdem das Skript erfolgreich ausgeführt wurde, kopieren Sie den Befehl `CREATE CREDENTIAL` in ein Abfragetool. Stellen Sie dann eine Verbindung mit einer Instanz von SQL Server her, und führen Sie den Befehl zur Erstellung der Anmeldeinformationen mit der SAS aus.

2. Stellen Sie mithilfe von SQL Server Management Studio oder Azure Data Studio eine Verbindung mit der Azure SQL Edge-Instanz her, und erstellen Sie die Anmeldeinformationen dann mithilfe des Befehls im vorherigen Schritt. Stellen Sie sicher, dass Sie den Befehl `CREATE CREDENTIAL` durch die tatsächliche Ausgabe im vorherigen Schritt ersetzen.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Der folgende Befehl erstellt eine Sicherung von *IronOreSilicaPrediction* im Azure-Speichercontainer.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Wiederherstellen einer Datenbank in Azure SQL Edge

In Azure SQL Edge kann eine Wiederherstellung mithilfe eines lokalen Datenträgers, Netzwerkspeicherorts oder Azure Blob Storage-Kontos erfolgen. Weitere Informationen zur Wiederherstellung finden Sie in der [Übersicht über die Wiederherstellung](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server). Eine Übersicht über das einfache Wiederherstellungsmodell in SQL Server finden Sie unter [Vollständige Datenbankwiederherstellungen (einfaches Wiederherstellungsmodell)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> Datenbanken, die in Azure SQL Edge erstellt wurden, können in einer Instanz von Microsoft SQL Server oder Azure SQL wiederhergestellt werden. Außerdem kann eine Datenbank, die in Microsoft SQL Server oder Azure SQL erstellt wurde, in Azure SQL Edge wiederhergestellt werden, sofern die Datenbank keine der Funktionen enthält, die von Azure SQL Edge nicht unterstützt werden. 

### <a name="restore-from-a-local-disk"></a>Wiederherstellen von einem lokalem Datenträger

In diesem Beispiel wird die Sicherung *IronOreSilicaPrediction* verwendet, die im vorherigen Beispiel erstellt wurde. Sie stellen sie nun als neue Datenbank mit einem anderen Namen wieder her.

1. Wenn die Datenbanksicherungsdatei nicht bereits im Container vorhanden ist, können Sie die Datei mit dem folgenden Befehl in den Container kopieren. Im folgenden Beispiel wird davon ausgegangen, dass die Sicherungsdatei auf dem lokalen Host vorhanden ist und in den Ordner „/var/opt/mssqlbBackup“ in einem Azure SQL Edge-Container mit dem Namen *sql1* kopiert wird.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Stellen Sie mithilfe von SQL Server Management Studio oder Azure Data Studio eine Verbindung mit der Azure SQL Edge-Instanz her, um den Wiederherstellungsbefehl auszuführen. Im folgenden Beispiel wird **IronOrePredictDB.bak** so wiederhergestellt, dass dabei die neue Datenbank **IronOreSilicaPrediction_2** erstellt wird.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Nach Ausführung des Wiederherstellungsbefehls wird bei erfolgreichem Wiederherstellungsvorgang im Ausgabefenster eine Meldung ähnlich der folgenden angezeigt. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Wiederherstellen über URL

Azure SQL Edge unterstützt auch das Wiederherstellen einer Datenbank aus einem Azure Storage-Konto. Wiederherstellungen können aus Sicherungen von Blockblobs oder Seitenblobs erfolgen. Im folgenden Beispiel wird die Datenbanksicherungsdatei *IronOreSilicaPrediction_2020_04_16.bak* aus einem Blockblob wiederhergestellt, um die Datenbank *IronOreSilicaPrediction_3* zu erstellen.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```