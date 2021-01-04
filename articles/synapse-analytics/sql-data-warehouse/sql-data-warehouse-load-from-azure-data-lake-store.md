---
title: 'Tutorial: Laden von Daten aus Azure Data Lake Storage'
description: Verwenden Sie die COPY-Anweisung, um Daten aus Azure Data Lake Storage für dedizierte SQL-Pools zu laden.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 4886cf6c24e7e96676b9ca5e74b431685cd5a8bb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452862"
---
# <a name="load-data-from-azure-data-lake-storage-into-dedicated-sql-pools-in-azure-synapse-analytics"></a>Laden von Daten aus Azure Data Lake Storage in dedizierte SQL-Pools in Azure Synapse Analytics

In diesem Leitfaden erfahren Sie, wie Sie die [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) verwenden, um Daten aus Azure Data Lake Storage zu laden. Kurze Beispiele zur Verwendung der COPY-Anweisung für alle Authentifizierungsmethoden finden Sie in der folgenden Dokumentation: [Sicheres Laden von Daten mithilfe dedizierter SQL-Pools](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

> [!NOTE]  
> Wenn Sie Feedback zur COPY-Anweisung geben oder Probleme melden möchten, senden Sie eine E-Mail an die folgende Verteilerliste: sqldwcopypreview@service.microsoft.com.
>
> [!div class="checklist"]
>
> * Erstellen Sie die Zieltabelle zum Laden von Daten aus Azure Data Lake Storage.
> * Erstellen Sie die COPY-Anweisung zum Laden von Daten in das Data Warehouse.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, laden Sie die neueste Version von [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) herunter, und installieren Sie sie.

Für dieses Tutorial benötigen Sie Folgendes:

* Einen dedizierten SQL-Pool. Weitere Informationen finden Sie unter [Erstellen eines dedizierten SQL-Pools und Abfragen von Daten](create-data-warehouse-portal.md).
* Ein Data Lake Storage-Konto. Siehe [Erste Schritte mit Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Für dieses Speicherkonto müssen Sie zum Laden eine der folgenden Anmeldeinformationen konfigurieren oder angeben: einen Speicherkontoschlüssel, einen SAS-Schlüssel (Shared Access Signature), einen Azure Directory-Anwendungsbenutzer oder einen AAD-Benutzer, der über die entsprechende Azure-Rolle für das Speicherkonto verfügt.

## <a name="create-the-target-table"></a>Erstellen der Zieltabelle

Stellen Sie eine Verbindung mit Ihrem dedizierten SQL-Pool her, und erstellen Sie die Zieltabelle, in die die Daten geladen werden sollen. In diesem Beispiel wird eine Produktdimensionstabelle erstellt.

```sql
-- A: Create the target table
-- DimProduct
CREATE TABLE [dbo].[DimProduct]
(
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    DISTRIBUTION = HASH([ProductKey]),
    CLUSTERED COLUMNSTORE INDEX
    --HEAP
);
```


## <a name="create-the-copy-statement"></a>Erstellen der COPY-Anweisung

Stellen Sie eine Verbindung mit Ihrem dedizierten SQL-Pool her, und führen Sie die COPY-Anweisung aus. Eine umfassende Liste mit Beispielen finden Sie in der folgenden Dokumentation: [Sicheres Laden von Daten mithilfe dedizierter SQL-Pools](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples).

```sql
-- B: Create and execute the COPY statement

COPY INTO [dbo].[DimProduct] 
--The column list allows you map, omit, or reorder input file columns to target table columns. 
--You can also specify the default value when there is a NULL value in the file.
--When the column list is not specified, columns will be mapped based on source and target ordinality
(
    ProductKey default -1 1,
    ProductLabel default 'myStringDefaultWhenNull' 2,
    ProductName default 'myStringDefaultWhenNull' 3
)
--The storage account location where you data is staged
FROM 'https://storageaccount.blob.core.windows.net/container/directory/'
WITH 
(
   --CREDENTIAL: Specifies the authentication method and credential access your storage account
   CREDENTIAL = (IDENTITY = '', SECRET = ''),
   --FILE_TYPE: Specifies the file type in your storage account location
   FILE_TYPE = 'CSV',
   --FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text (CSV) file
   FIELDTERMINATOR = '|',
   --ROWTERMINATOR: Marks the end of a record in the file
   ROWTERMINATOR = '0x0A',
   --FIELDQUOTE: Specifies the delimiter for data of type string in a delimited text (CSV) file
   FIELDQUOTE = '',
   ENCODING = 'UTF8',
   DATEFORMAT = 'ymd',
   --MAXERRORS: Maximum number of reject rows allowed in the load before the COPY operation is canceled
   MAXERRORS = 10,
   --ERRORFILE: Specifies the directory where the rejected rows and the corresponding error reason should be written
   ERRORFILE = '/errorsfolder',
) OPTION (LABEL = 'COPY: ADLS tutorial');
```

## <a name="optimize-columnstore-compression"></a>Optimieren der Columnstore-Komprimierung

Tabellen werden standardmäßig als gruppierter Columnstore-Index definiert. Nach Abschluss eines Ladevorgangs sind einige der Datenzeilen möglicherweise nicht in den Columnstore-Index komprimiert.  Es gibt zahlreiche Gründe, warum dies geschieht. Weitere Informationen finden Sie unter [Verwalten von Columnstore-Indizes](sql-data-warehouse-tables-index.md).

Um die Abfrageleistung und die Columnstore-Komprimierung nach dem Ladevorgang zu optimieren, stellen Sie die Tabelle wieder her, um den Columstore-Index zu zwingen alle Zeilen zu komprimieren.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimieren von Statistiken

Es empfiehlt sich, Statistiken für einzelne Spalten sofort nach dem Ladevorgang zu erstellen. Es gibt einige Optionen für Statistiken. Beim Erstellen einer Einspaltenstatistik auf jeder Spalten vergeht möglicherweise viel Zeit bis alle Statistiken wiederhergestellt werden. Wenn Sie wissen, dass bestimmte Spalten nicht in den Abfrageprädikaten erhalten sein werden, können Sie die Erstellung von Statistiken für diese Spalten überspringen.

Wenn Sie Einzelspaltenstatistiken für jede Spalte jeder Tabelle erstellen möchten, können Sie das Codebeispiel für die gespeicherte Prozedur `prc_sqldw_create_stats` im Artikel [Statistiken](sql-data-warehouse-tables-statistics.md) verwenden.

Das folgende Beispiel ist ein guter Ausgangspunkt zum Erstellen von Statistiken. Es werden Statistiken für einzelne Spalten für jede Spalte in der Dimensionstabelle erstellt sowie für jede verknüpfte Spalte in der Faktentabelle. Sie können später immer Statistiken für einzelne oder mehrere Spalten auf anderen Faktentabellenspalten hinzufügen.

## <a name="achievement-unlocked"></a>Der Weg ist frei!

Sie haben Daten erfolgreich in Ihr Data Warehouse geladen. Großartig!

## <a name="next-steps"></a>Nächste Schritte
Das Laden von Daten ist der erste Schritt bei der Entwicklung einer Data Warehouse-Lösung mit Azure Synapse Analytics. Sehen Sie sich unsere Entwicklungsressourcen an.

> [!div class="nextstepaction"]
> [Erfahren Sie, wie Tabellen für Data Warehousing entwickelt werden.](sql-data-warehouse-tables-overview.md)

Weitere Ladebeispiele und Verweise finden Sie in der folgenden Dokumentation:
- [COPY-Anweisung – Referenzdokumentation](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [COPY-Beispiele für jede Authentifizierungsmethode](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Schnellstart: COPY-Anweisung für eine einzelne Tabelle](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
