---
title: Code für XEvent-Ereignisdatei
description: Stellt ein PowerShell- und ein Transact-SQL-Skript für ein zweiphasiges Codebeispiel zur Veranschaulichung des Ereignisdateiziels in einem erweiterten Ereignis in Azure SQL-Datenbank bereit. Azure Storage ist ein erforderlicher Bestandteil in diesem Szenario.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: d7a57f98551cf91ed87858caba0907471bcf6b12
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501225"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Code des Ziels „Ereignisdatei“ für erweiterte Ereignisse in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Sie suchen ein vollständiges Codebeispiel als stabile Option zum Erfassen und Melden von Informationen für erweiterte Ereignisse.

In Microsoft SQL Server wird das [Ereignisdateiziel](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) zum Speichern von Ereignisausgaben in einer Datei auf der lokalen Festplatte verwendet. Solche Dateien sind für Azure SQL-Datenbank jedoch nicht verfügbar. Stattdessen wird das Ereignisdateiziel mithilfe des Azure Storage-Diensts unterstützt.

In diesem Thema wird ein Codebeispiel in zwei Phasen vorgestellt:

- PowerShell: Erstellen eines Azure-Speichercontainers in der Cloud.
- Transact-SQL:
  - Zuweisen des Azure-Speichercontainers zu einem Ereignisdateiziel.
  - Erstellen und Starten der Ereignissitzung usw.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

- Ein Azure-Konto und ein Azure-Abonnement. Sie können sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/)registrieren.
- Jede Datenbank, in der eine Tabelle erstellt werden kann.
  
  - Optional können Sie in wenigen Minuten [eine **AdventureWorksLT**-Demodatenbank erstellen](single-database-create-quickstart.md).

- SQL Server Management Studio („ssms.exe“), im Idealfall die aktuelle monatliche Updateversion.
  Sie können "ssms.exe" in der neuesten Version wie folgt herunterladen:
  
  - Im Thema [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
  - [Über diesen direkten Link zum Herunterladen.](https://go.microsoft.com/fwlink/?linkid=616025)

- Die [Azure PowerShell-Module](https://go.microsoft.com/?linkid=9811175) müssen installiert sein.

  - Die Module umfassen verschiedene Befehle, z.B. **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Phase 1: PowerShell-Code für den Azure-Speichercontainer

Dieser PowerShell-Code wird in Phase 1 des zweiphasigen Codebeispiels erstellt.

Das Skript beginnt mit Befehlen zum Bereinigen nach einer möglichen vorherigen Ausführung und kann darum erneut ausgeführt werden.

1. Fügen Sie das PowerShell-Skript in einem einfachen Texteditor (z.B. Editor) ein, und speichern Sie es als Datei mit der Erweiterung **.ps1**.
2. Starten Sie PowerShell ISE als Administrator.
3. Geben Sie an der Eingabeaufforderung<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>ein, und drücken Sie dann die EINGABETASTE.
4. Öffnen Sie in PowerShell ISE Ihre Datei mit der Erweiterung **.ps1** . Führen Sie das Skript aus.
5. Mit dem Skript wird zunächst ein Fenster geöffnet, in dem Sie sich bei Azure anmelden.

   - Wenn Sie das Skript ohne Unterbrechung der Sitzung erneut ausführen, können Sie den **Add-AzureAccount** -Befehl auf einfache Weise auskommentieren.

![PowerShell ISE mit installiertem Azure-Modul, bereit für die Ausführung des Skripts.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>PowerShell-Code

In diesem PowerShell-Skript wird davon ausgegangen, dass Sie das Azure-Modul bereits installiert haben. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Beachten Sie die benannten Werte, die beim Beenden des PowerShell-Skripts ausgegeben werden. Diese Werte müssen Sie im Transact-SQL-Skript bearbeiten, das als Phase 2 folgt.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> Im vorherigen PowerShell-Codebeispiel sind erweiterte SQL-Ereignisse nicht mit den ADLS Gen2-Speicherkonten kompatibel.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Phase 2: Transact-SQL-Code zum Verwenden des Azure-Speichercontainers

- In Phase 1 dieses Codebeispiels haben Sie ein PowerShell-Skript zum Erstellen eines Azure-Speichercontainers ausgeführt.
- In Phase 2 muss das folgende Transact-SQL-Skript nun diesen Container verwenden.

Das Skript beginnt mit Befehlen zum Bereinigen nach einer möglichen vorherigen Ausführung und kann darum erneut ausgeführt werden.

Beim Beenden des PowerShell-Skripts wurden einige benannte Werte ausgegeben. Sie müssen das Transact-SQL-Skript so bearbeiten, dass diese Werte verwendet werden. Die Bearbeitungspunkte finden Sie, wenn Sie im Transact-SQL-Skript nach **TODO** suchen.

1. Öffnen Sie SQL Server Management Studio ("ssms.exe").
2. Stellen Sie eine Verbindung mit Ihrer Datenbank in Azure SQL-Datenbank her.
3. Klicken Sie, um einen neuen Abfragebereich zu öffnen.
4. Fügen Sie das folgende Transact-SQL-Skript in den Abfragebereich ein.
5. Suchen Sie alle Vorkommen von **TODO** im Skript, und nehmen Sie jeweils die entsprechende Bearbeitung vor.
6. Speichern Sie die Änderungen, und führen Sie dann das Skript aus.

> [!WARNING]
> Der durch das vorangehende PowerShell-Skript generierte SAS-Schlüsselwert beginnt unter Umständen mit einem Fragezeichen. Wenn Sie den SAS-Schlüssel im folgenden T-SQL-Skript verwenden, müssen Sie *das vorangestellte Fragezeichen entfernen*. Andernfalls kann die Ausführung des Skripts aus Sicherheitsgründen blockiert werden.

### <a name="transact-sql-code"></a>Transact-SQL-Code

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Wenn das Ziel bei der Ausführung nicht angefügt wird, müssen Sie die Ereignissitzung beenden und dann erneut starten:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Output

Klicken Sie auf eine Zelle unter der Spaltenüberschrift **event_data_XML**, nachdem das Transact-SQL-Skript abgeschlossen ist. Es wird ein **\<event>** -Element mit einer UPDATE-Anweisung angezeigt.

Hier sehen Sie ein **\<event>** -Element, das beim Testen generiert wurde:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Im vorherigen Transact-SQL-Skript wurde die folgende Systemfunktion verwendet, um die Ereignisdatei zu lesen:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

Eine Erläuterung der erweiterten Optionen zum Anzeigen von Daten aus erweiterten Ereignissen finden Sie unter:

- [Advanced Viewing of Target Data from Extended Events (Erweiterte Anzeige von Zieldaten aus erweiterten Ereignissen)](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konvertieren des Codebeispiels für die Ausführung in SQL Server

Angenommen, Sie möchten das vorhergehende Transact-SQL-Codebeispiel in Microsoft SQL Server ausführen.

- Der Einfachheit halber möchten Sie die Verwendung des Azure-Speichercontainers vollständig durch eine einfache Datei ersetzen, z.B. durch *C:\myeventdata.xel*. Die Datei wird auf die lokale Festplatte des Computers geschrieben, der SQL Server hostet.
- Sie benötigen keine Transact-SQL-Anweisungen für **CREATE MASTER KEY** und **CREATE CREDENTIAL**.
- In der **ADD TARGET**-Klausel der **CREATE EVENT SESSION**-Anweisung ersetzen Sie den für **filename=** zugewiesenen HTTP-Wert durch eine vollständige Pfadzeichenfolge wie z.B. *C:\myfile.xel*.
  
  - Es wird kein Azure-Speicherkonto benötigt.

## <a name="more-information"></a>Weitere Informationen

Weitere Informationen zu Konten und Containern im Azure Storage-Dienst finden Sie in den folgenden Artikeln:

- [Verwenden des Blob-Speichers mit .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Benennen von Containern, BLOBs und Metadaten und Verweisen auf diese](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [Arbeiten mit dem Stammcontainer](/rest/api/storageservices/Working-with-the-Root-Container)
- [Lektion 1: Erstellen einer gespeicherten Zugriffsrichtlinie und einer Shared Access Signature für einen Azure-Container](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [Lektion 2: Erstellen von SQL Server-Anmeldeinformationen mit einer Shared Access Signature (SAS)](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Erweiterte Ereignisse für Microsoft SQL Server](/sql/relational-databases/extended-events/extended-events)