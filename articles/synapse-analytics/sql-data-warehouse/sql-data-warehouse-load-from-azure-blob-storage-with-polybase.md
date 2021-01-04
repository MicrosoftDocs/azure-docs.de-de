---
title: Laden von Contoso Retail-Daten in dedizierte SQL-Pools
description: Verwenden Sie PolyBase- und T-SQL-Befehle, um zwei Tabellen aus den Contoso Retail-Daten in dedizierte SQL-Pools zu laden.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bbe61444404b16a09a1e0d2bdead72ac53a60744
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452892"
---
# <a name="load-contoso-retail-data-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Laden von Contoso Retail-Daten in dedizierte SQL-Pools in Azure Synapse Analytics

In diesem Tutorial erfahren Sie, wie Sie mithilfe von PolyBase- und T-SQL-Befehlen zwei Tabellen aus den Contoso Retail-Daten in dedizierte SQL-Pools laden.

In diesem Lernprogramm führen Sie folgende Schritte aus:

1. Konfigurieren von PolyBase zum Laden aus Azure Blob Storage
2. Laden von öffentlichen Daten in Ihre Datenbank
3. Durchführen von Optimierungen, nachdem das Laden abgeschlossen ist

## <a name="before-you-begin"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie ein Azure-Konto, das bereits über einen dedizierten SQL-Pool verfügt. Wenn Sie noch kein Data Warehouse bereitgestellt haben, finden Sie entsprechende Anweisungen unter [Schnellstart: Erstellen und Abfragen einer Azure SQL Data Warehouse-Instanz im Azure-Portal](create-data-warehouse-portal.md).

## <a name="configure-the-data-source"></a>Konfigurieren der Datenquelle

PolyBase verwendet externe T-SQL-Objekte, um den Speicherort und die Attribute der externen Daten zu definieren. Die externen Objektdefinitionen werden in dedizierten SQL-Pools gespeichert. Die Daten werden extern gespeichert.

## <a name="create-a-credential"></a>Erstellen von Anmeldeinformationen

**Überspringen Sie diesen Schritt**, wenn Sie die öffentliche Contoso-Daten laden. Sie benötigen keinen sicheren Zugriff auf die öffentlichen Daten, da diese schon für jeden zugänglich sind.

**Überspringen Sie diesen Schritt nicht**, wenn Sie dieses Tutorial als Vorlage für das Laden Ihrer eigenen Daten verwenden. Für den Zugriff auf die Daten durch eine Anmeldeinformation, verwenden Sie das folgende Skript zum Erstellen einer datenbankbezogenen Anmeldeinformation. Verwenden Sie es anschließend zum Definieren des Speicherorts der Datenquelle.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

## <a name="create-the-external-data-source"></a>Erstellen der externen Datenquelle

Verwenden Sie den Befehl [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), um den Speicherort der Daten und den Datentyp zu speichern.

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH
(  
    TYPE = Hadoop
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
);
```

> [!IMPORTANT]
> Wenn Sie Ihre Azure Blob Storage-Container öffentlich machen möchten, sollten Sie bedenken, dass Ihnen als Datenbesitzer Datenausgangsgebühren in Rechnung gestellt werden, wenn Daten das Rechenzentrum verlassen.

## <a name="configure-the-data-format"></a>Konfigurieren des Datenformats

Die Daten werden in Textdateien in Azure Blob Storage gespeichert, und jedes Feld wird durch ein Trennzeichen getrennt. Führen Sie in SSMS den Befehl CREATE EXTERNAL FILE FORMAT aus, um das Format der Daten in den Textdateien anzugeben. Die Contoso-Dateien sind nicht komprimiert und nicht durch senkrechte Striche getrennt.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-schema-for-the-external-tables"></a>Erstellen des Schemas für die externen Tabellen

Da Sie die Datenquelle und das Dateiformat festgelegt haben, können Sie jetzt das Schema für die externen Tabellen erstellen.

Erstellen Sie ein Schema, um einen Speicherort für die Contoso-Daten in der Datenbank zu erstellen.

```sql
CREATE SCHEMA [asb]
GO
```

## <a name="create-the-external-tables"></a>Erstellen von externen Tabellen

Führen Sie das folgende Skript zum Erstellen der externen DimProduct und FactOnlineSales-Tabellen aus. Wir definieren hier nur Spaltennamen und Datentypen und binden diese an den Speicherort und an das Format der Azure Blob Storage-Dateien. Die Definition wird im Data Warehouse gespeichert, und die Daten befinden sich weiterhin im Azure-Speicherblob.

Der Parameter **LOCATION** ist der Ordner im Stammordner im Azure-Speicherblob. Jede Tabelle befindet sich in einem anderen Ordner.

```sql
--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/'
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="load-the-data"></a>Laden der Daten

Es gibt verschiedene Arten, auf externe Daten zuzugreifen.  Sie können Daten direkt aus den externen Tabellen abfragen, sie in neue Tabellen im Data Warehouse laden oder externe Daten zu vorhandenen Data Warehouse-Tabellen hinzufügen.  

### <a name="create-a-new-schema"></a>Erstellen eines neuen Schemas

CTAS erstellt eine neue Tabelle, die Daten enthält.  Erstellen Sie zunächst ein Schema für die Contoso-Daten.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="load-the-data-into-new-tables"></a>Laden der Daten in neue Tabellen

Wenn Daten aus Azure Blob Storage geladen und in einer Tabelle innerhalb des Data Warehouse gespeichert werden sollen, verwenden Sie die Anweisung [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Beim Laden mit [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) werden die stark typisierten externen Tabellen verwendet, die Sie erstellt haben. Wenn die Daten in neue Tabellen geladen werden sollen, verwenden Sie eine CTAS-Anweisung pro Tabelle.

CTAS erstellt eine neue Tabelle und füllt sie mit den Ergebnissen einer SELECT-Anweisung. CTAS definiert die neue Tabelle, sodass sie die gleichen Spalten und Datentypen wie die Ergebnisse der SELECT-Anweisung aufweist. Wenn Sie alle Spalten einer externen Tabelle auswählen, wird die neue Tabelle ein Replikat der Spalten und Datentypen in der externen Tabelle sein.

In diesem Beispiel erstellen wir sowohl die Dimensions- als auch die Faktentabelle als verteilte Hashtabellen.

```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="track-the-load-progress"></a>Verfolgen des Ladefortschritts

Sie können den Ladefortschritt mithilfe der dynamischen Verwaltungsansicht (Dynamic Management View, DMV) verfolgen.

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

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
WHERE
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="optimize-columnstore-compression"></a>Optimieren der Columnstore-Komprimierung

Standardmäßig speichern dedizierte SQL-Pools die Tabelle als gruppierten Columnstore-Index. Nach Abschluss eines Ladevorgangs sind einige der Datenzeilen möglicherweise nicht in den Columnstore-Index komprimiert.  Dieses Problem kann aus verschiedenen Gründen auftreten. Weitere Informationen finden Sie unter [Verwalten von Columnstore-Indizes](sql-data-warehouse-tables-index.md).

Um die Abfrageleistung und die Columnstore-Komprimierung nach dem Ladevorgang zu optimieren, stellen Sie die Tabelle wieder her, um den Columstore-Index zu zwingen alle Zeilen zu komprimieren.

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Weitere Informationen zum Verwalten von Columnstore-Indizes finden Sie im Artikel [Verwalten von Columnstore-Indizes](sql-data-warehouse-tables-index.md) .

## <a name="optimize-statistics"></a>Optimieren von Statistiken

Es empfiehlt sich, Statistiken für einzelne Spalten sofort nach dem Ladevorgang zu erstellen. Wenn Sie wissen, dass bestimmte Spalten nicht in den Abfrageprädikaten enthalten sein werden, können Sie die Erstellung von Statistiken für diese Spalten überspringen. Beim Erstellen einer Einspaltenstatistik auf jeder Spalten vergeht möglicherweise viel Zeit bis alle Statistiken wiederhergestellt werden.

Wenn Sie Einzelspaltenstatistiken für jede Spalte jeder Tabelle erstellen möchten, können Sie das Codebeispiel für die gespeicherte Prozedur `prc_sqldw_create_stats` im Artikel [Statistiken](sql-data-warehouse-tables-statistics.md) verwenden.

Das folgende Beispiel ist ein guter Ausgangspunkt zum Erstellen von Statistiken. Es werden Statistiken für einzelne Spalten für jede Spalte in der Dimensionstabelle erstellt sowie für jede verknüpfte Spalte in der Faktentabelle. Sie können später immer Statistiken für einzelne oder mehrere Spalten auf anderen Faktentabellenspalten hinzufügen.

```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Der Weg ist frei!

Sie haben öffentliche Daten erfolgreich in Ihr Data Warehouse geladen. Großartig!

Sie können jetzt damit beginnen, die Tabellen abzufragen, um Ihre Daten zu untersuchen. Führen Sie die folgende Abfrage aus, um den gesamten Umsatz pro Marke anzuzeigen:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Nächste Schritte

Folgen Sie im Repository für Microsoft SQL Server-Beispiele dem Beispiel zum [Laden des kompletten Contoso Retail-Data Warehouses](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md), um das komplette Dataset zu laden.
Weitere Hinweise zur Entwicklung finden Sie unter [Entwurfsentscheidungen und Programmiertechniken für SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
