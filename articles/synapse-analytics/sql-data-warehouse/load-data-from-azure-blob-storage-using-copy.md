---
title: 'Tutorial: Laden von Daten zu New Yorker Taxis'
description: In diesem Tutorial werden das Azure-Portal und SQL Server Management Studio zum Laden von Daten zu New Yorker Taxis aus einem Azure-Blobspeicher für Synapse SQL verwendet.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd5c56ef74fbe0c60a9d395a7b8a0fbc496e773c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534839"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Tutorial: Laden des Datasets „New York Taxis“

In diesem Tutorial wird die [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) verwendet, um das Dataset „New York Taxicab“ aus einem Azure-Blob-Speicherkonto zu laden. In diesem Tutorial werden das [Azure-Portal](https://portal.azure.com) und [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) für folgende Zwecke verwendet:

> [!div class="checklist"]
>
> * Erstellen eines festgelegten Benutzers zum Laden von Daten
> * Erstellen der Tabellen für das Beispieldataset 
> * Verwenden der T-SQL-Anweisung „COPY“ zum Laden von Daten in Ihr Data Warehouse
> * Anzeigen des Fortschritts beim Laden von Daten

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, laden Sie die neueste Version von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) herunter, und installieren Sie sie.  

In diesem Tutorial wird vorausgesetzt, dass Sie bereits einen dedizierten SQL-Pool aus dem folgenden [Tutorial](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/create-data-warehouse-portal#connect-to-the-server-as-server-admin) erstellt haben.

## <a name="create-a-user-for-loading-data"></a>Erstellen eines Benutzers zum Laden von Daten

Das Serveradministratorkonto dient zum Ausführen von Verwaltungsvorgänge und eignet sich nicht zum Ausführen von Abfragen für Benutzerdaten. Das Laden von Daten ist ein speicherintensiver Vorgang. Arbeitsspeicher-Höchstwerte werden entsprechend den [Data Warehouse-Einheiten](what-is-a-data-warehouse-unit-dwu-cdwu.md) und der [Ressourcenklasse](resource-classes-for-workload-management.md) definiert.

Es wird empfohlen, eine Anmeldung und einen Benutzer speziell zum Laden von Daten zu erstellen. Fügen Sie dann den Benutzer für das Laden einer [Ressourcenklasse](resource-classes-for-workload-management.md) hinzu, die eine geeignete maximale Speicherbelegung ermöglicht.

Verbinden Sie sich als Serveradministrator, damit Sie Anmeldungen und Benutzer erstellen können. Führen Sie die folgenden Schritte aus, um eine Anmeldung und einen Benutzer mit dem Namen **LoaderRC20** zu erstellen. Weisen Sie den Benutzer dann der Ressourcenklasse **staticrc20** zu.

1. Wählen Sie in SSMS mit der rechten Maustaste **Master** zum Anzeigen eines Dropdownmenüs und dann **Neue Abfrage** aus. Ein neues Abfragefenster wird geöffnet.

    ![Neue Abfrage für „Master“](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Geben Sie im Abfragefenster die folgenden T-SQL-Befehle ein, um eine Anmeldung und einen Benutzer mit dem Namen „LoaderRC20“ zu erstellen, und ersetzen Sie dabei Ihr eigenes Kennwort durch „a123STRONGpassword!“.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Wählen Sie **Execute**(Ausführen).

4. Klicken Sie mit der rechten Maustaste auf **mySampleDataWarehouse**, und wählen Sie **Neue Abfrage** aus. Ein neues Abfragefenster wird geöffnet.  

    ![Neue Abfrage für Data Warehouse-Beispiel](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Geben Sie die folgenden T-SQL-Befehle ein, um einen Datenbankbenutzer mit dem Namen „LoaderRC20“ für die Anmeldung „LoaderRC20“ zu erstellen. Die zweite Zeile gewährt dem neuen Benutzer CONTROL-Berechtigungen für das neue Data Warehouse.  Diese Berechtigungen ähneln der Festlegung des Benutzers als Besitzer der Datenbank. Die dritte Zeile fügt den neuen Benutzer als Mitglied der [Ressourcenklasse](resource-classes-for-workload-management.md) „staticrc20“ hinzu.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Wählen Sie **Execute**(Ausführen).

## <a name="connect-to-the-server-as-the-loading-user"></a>Herstellen einer Verbindung mit dem Server als ladender Benutzer

Im ersten Schritt zum Laden von Daten melden Sie sich als „LoaderRC20“ an.  

1. Wählen Sie im Objekt-Explorer das Dropdownmenü **Verbinden** und dann **Datenbank-Engine** aus. Das Dialogfeld **Mit Server verbinden** wird angezeigt.

    ![Herstellen einer Verbindung mit neuer Anmeldung](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Geben Sie den vollqualifizierten Servernamen und dann **LoaderRC20** als Anmeldenamen ein.  Geben Sie Ihr Kennwort für „LoaderRC20“ ein.

3. Wählen Sie **Verbinden**.

4. Wenn die Verbindung bereitsteht, werden zwei Serververbindungen im Objekt-Explorer angezeigt: eine Verbindung als „ServerAdmin“ und eine Verbindung als „MedRCLogin“.

    ![Verbindung ist erfolgreich](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Erstellen der Tabellen für die Beispieldaten

Sie können nun mit dem Laden von Daten in das neue Data Warehouse beginnen. In diesem Teil des Tutorials wird gezeigt, wie Sie die COPY-Anweisung verwenden, um das Dataset „New York City taxi cab“ aus einem Azure Storage-Blobspeicher zu laden. Informationen zum Übertragen Ihrer Daten in Azure-Blobspeicher oder zum direkten Laden der Daten aus Ihrer Quelle finden Sie in der [Ladeübersicht](design-elt-data-loading.md).

Führen Sie die folgenden SQL-Skripts aus, und geben Sie Informationen zu den Daten an, die Sie laden möchten. Diese Informationen umfassen den aktuellen Speicherort der Daten, das Format des Dateninhalts und die Tabellendefinition für die Daten.

1. Im vorherigen Abschnitt haben Sie sich als „LoaderRC20“ beim Data Warehouse angemeldet. Klicken Sie in SSMS mit der rechten Maustaste auf die Verbindung „LoaderRC20“, und wählen Sie **Neue Abfrage** aus.  Ein neues Abfragefenster wird angezeigt.

    ![Fenster für neue Ladeabfrage](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Vergleichen Sie Ihr Abfragefenster mit der Abbildung oben.  Überprüfen Sie, ob das neue Abfragefenster als „LoaderRC20“ ausgeführt wird und Abfragen für die Datenbank „mySampleDataWarehouse“ durchgeführt werden. Verwenden Sie dieses Abfragefenster zum Ausführen aller Ladeschritte.

7. Führen Sie folgende T-SQL-Anweisungen aus, um die Tabellen zu erstellen:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>Laden der Daten in das Data Warehouse

In diesem Abschnitt wird die [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) zum Laden der Beispieldaten aus Azure Storage Blob zu laden.  

> [!NOTE]
> In diesem Tutorial werden die Daten direkt in die endgültige Tabelle geladen. Normalerweise würden Sie Daten in eine Stagingtabelle für Ihre Produktionsworkloads laden. Während sich die Daten in der Stagingtabelle befinden, können Sie alle erforderlichen Transformationen durchführen. 

1. Führen Sie zum Laden der Daten die folgenden Anweisungen aus:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Sehen Sie Ihren Daten beim Laden zu. Sie laden mehrere GB an Daten und komprimieren diese in hoch performante gruppierte Columnstore-Indizes. Führen Sie die folgende Abfrage mit dynamischen Verwaltungssichten (DMVs) aus, um den Status des Ladevorgangs anzuzeigen.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Zeigen Sie alle Systemabfragen an.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Freuen Sie sich darüber, dass Ihre Daten sauber in das Data Warehouse geladen werden.

    ![Anzeigen geladener Tabellen](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es werden Ihnen Computeressourcen und Daten, die Sie in das Data Warehouse geladen haben, in Rechnung gestellt. Diese werden separat berechnet.

* Falls Sie die Daten im Speicher belassen möchten, können Sie Computeressourcen anhalten, wenn Sie das Data Warehouse nicht verwenden. Durch das Anhalten von Computeressourcen wird Ihnen nur die Datenspeicherung berechnet, und Sie können die Computeressourcen fortsetzen, sobald Sie mit den Daten arbeiten möchten.
* Wenn zukünftig keine Gebühren anfallen sollen, können Sie das Data Warehouse löschen.

Führen Sie die folgenden Schritte aus, um Ressourcen nach Wunsch zu bereinigen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihr Data Warehouse aus.

    ![Bereinigen von Ressourcen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Wählen Sie zum Anhalten von Computeressourcen die Schaltfläche **Anhalten**. Wenn das Data Warehouse angehalten ist, wird eine Schaltfläche **Starten** angezeigt.  Wählen Sie zum Fortsetzen der Computeressourcen die Option **Starten**.

3. Wenn Sie das Data Warehouse entfernen möchten, damit keine Gebühren für Compute- oder Speicherressourcen anfallen, wählen Sie **Löschen** aus.

4. Wählen Sie zum Entfernen des von Ihnen erstellten Servers die Option **mynewserver-20180430.database.windows.net** (siehe Abbildung oben) und dann **Löschen** aus.  Seien Sie dabei vorsichtig, denn durch das Löschen des Servers werden auch alle Datenbanken gelöscht, die dem Server zugewiesen sind.

5. Wählen Sie zum Entfernen der Ressourcengruppe die Option **myResourceGroup** und dann **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie ein Data Warehouse und ein Benutzer zum Laden von Daten erstellt werden. Sie haben die einfache [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) zum Laden von Daten in Ihr Data Warehouse verwendet.

Sie haben folgende Schritte ausgeführt:
> [!div class="checklist"]
>
> * Erstellen eines Data Warehouse im Azure-Portal
> * Einrichten einer Firewallregel auf Serverebene im Azure-Portal
> * Herstellen einer Verbindung mit dem Data Warehouse mit SSMS
> * Erstellen eines festgelegten Benutzers zum Laden von Daten
> * Erstellen der Tabellen für die Beispieldaten
> * Verwenden der T-SQL-Anweisung „COPY“ zum Laden von Daten in Ihr Data Warehouse
> * Anzeigen des Fortschritts beim Laden von Daten

Fahren Sie mit der Entwicklungsübersicht fort, um zu erfahren, wie Sie eine vorhandene Datenbank nach Azure Synapse Analytics migrieren:

> [!div class="nextstepaction"]
> [Entwurfsentscheidungen zum Migrieren einer vorhandenen Datenbank nach Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Weitere Ladebeispiele und Verweise finden Sie in der folgenden Dokumentation:

- [COPY-Anweisung – Referenzdokumentation](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [COPY-Beispiele für jede Authentifizierungsmethode](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Schnellstart: COPY-Anweisung für eine einzelne Tabelle](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
