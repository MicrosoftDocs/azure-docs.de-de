---
title: Entwerfen von ELT anstelle von ETL
description: Implementieren von flexiblen Datenladestrategien für dedizierte SQL-Pools in Azure Synapse Analytics
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
ms.openlocfilehash: 64ba24eb0eab581310122908fc05d1d671ac1d40
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531572"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Strategien zum Laden von Daten für einen dedizierten SQL-Pool in Azure Synapse Analytics

In herkömmlichen dedizierten SMP-SQL-Pools wird zum Laden von Daten ein ETL-Prozess (Extrahieren, Transformieren und Laden) verwendet. Synapse SQL verwendet in Azure Synapse Analytics eine Architektur zur Verarbeitung verteilter Abfragen, die die Vorteile der Skalierbarkeit und Flexibilität von Compute- und Speicherressourcen nutzt.

Ein ELT-Prozess (Extrahieren, Laden und Transformieren) nutzt die Verarbeitungsfunktionen der integrierten verteilten Abfragen und verringert die Ressourcen, die vor dem Laden zum Transformieren der Daten erforderlich sind.

Dedizierte SQL-Pools unterstützen zwar viele Lademethoden (unter anderem beliebte SQL Server-Optionen wie [bcp](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) und die [SqlBulkCopy-API](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)), doch die schnellste und am besten skalierbare Möglichkeit zum Laden von Daten stellen externe PolyBase-Tabellen und die [COPY-Anweisung](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dar.

Mit PolyBase und der COPY-Anweisung können Sie über die T-SQL-Sprache auf externe Daten zugreifen, die in Azure Blob Storage oder in Azure Data Lake Storage gespeichert sind. Wir empfehlen die Verwendung der COPY-Anweisung, um beim Laden von Daten so flexibel wie möglich zu sein.


## <a name="what-is-elt"></a>Was ist ELT?

ELT (Extrahieren, Laden und Transformieren) ist ein Prozess, bei dem Daten aus einem Quellsystem extrahiert, in einen dedizierten SQL-Pool geladen und dann transformiert werden.

Dies sind die grundlegenden Schritte für die Implementierung von ELT:

1. Extrahieren Sie die Quelldaten in Textdateien.
2. Legen Sie die Daten in Azure Blob Storage oder Azure Data Lake Store ab.
3. Bereiten Sie die Daten für das Laden vor.
4. Laden Sie die Daten mithilfe von PolyBase oder des COPY-Befehls in Stagingtabellen.
5. Transformieren Sie die Daten.
6. Fügen Sie die Daten in Produktionstabellen ein.

Ein Tutorial zum Ladevorgang finden Sie unter [Laden von Daten aus Azure Blob Storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahieren der Quelldaten in Textdateien

Das Abrufen von Daten aus dem Quellsystem hängt vom Speicherort ab. Ziel ist es, die Daten in unterstützte, durch Trennzeichen getrennte Text- oder CSV-Dateien zu verschieben.

### <a name="supported-file-formats"></a>Unterstützte Dateiformate

Mit PolyBase und der COPY-Anweisung können Sie Daten aus UTF-8- und UTF-16-codierten, durch Trennzeichen getrennten Text- oder CSV-Dateien laden. Neben durch Trennzeichen getrennten Text- oder CSV-Dateien können auch Daten auch aus Hadoop-Dateiformaten wie ORC oder Parquet geladen werden. Mit PolyBase und der COPY-Anweisung können auch Daten aus Dateien geladen werden, die mit Gzip und Snappy komprimiert wurden.

Erweitertes ASCII, Formate mit fester Breite und geschachtelte Formate wie WinZip oder XML werden nicht unterstützt. Beim Exportieren aus SQL Server können Sie die Daten mit dem [Befehlszeilentool bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) in durch Trennzeichen getrennte Textdateien exportieren.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Laden der Daten in Azure Blob Storage oder Azure Data Lake Storage

Wenn Sie Daten in Azure Storage platzieren möchten, können Sie sie in [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oder in [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verschieben. In beiden Fällen sollten die Daten in Textdateien gespeichert werden. PolyBase und die COPY-Anweisung können Daten von beiden Orten laden.

Tools und Dienste, mit denen Sie Daten in Azure Storage verschieben können:

- Der [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)-Dienst verbessert Netzwerkdurchsatz, Leistung und Vorhersagbarkeit. ExpressRoute ist ein Dienst, der Ihre Daten über eine dedizierte private Verbindung zu Azure weiterleitet. Bei ExpressRoute-Verbindungen werden Daten nicht über das öffentliche Internet weitergeleitet. Die Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen über das öffentliche Internet.
- Das Hilfsprogramm [AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verschiebt Daten über das öffentliche Internet in Azure Storage. Dies funktioniert, wenn Ihre Datenmengen weniger als 10 TB umfassen. Wenn Sie Ladevorgänge in regelmäßigen Abständen mit AZCopy ausführen möchten, testen Sie die Netzwerkgeschwindigkeit, um festzustellen, ob sie geeignet ist.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verfügt über ein Gateway, das Sie auf dem lokalen Server installieren können. Anschließend können Sie eine Pipeline erstellen, um Daten vom lokalen Server in Azure Storage zu verschieben. Weitere Informationen zum Verwenden der Data Factory bei dedizierten SQL-Pools finden Sie unter [Laden von Daten für dedizierte SQL-Pools](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Vorbereiten der Daten für das Laden

Möglicherweise müssen Sie die Daten in Ihrem Speicherkonto vorbereiten und bereinigen, bevor Sie den Ladevorgang durchführen. Die Datenvorbereitung kann durchgeführt werden, während sich Ihre Daten in der Quelle befinden, während Sie die Daten in Textdateien exportieren oder nachdem sich die Daten in Azure Storage befinden.  Am einfachsten ist es, so früh wie möglich im Prozess mit den Daten zu arbeiten.  

### <a name="define-the-tables"></a>Definieren der Tabellen

Wenn Sie die COPY-Anweisung verwenden, müssen Sie zuerst die Tabelle(n) definieren, die Sie in Ihren dedizierten SQL-Pool laden.

Bei Verwendung von PolyBase müssen Sie vor dem Laden externe Tabellen in Ihrem dedizierten SQL-Pool definieren. PolyBase verwendet externe Tabellen, um Daten in Azure Storage zu definieren und auf diese zuzugreifen. Eine externe Tabelle ähnelt einer Datenbanksicht. Die externe Tabelle enthält das Tabellenschema und verweist auf Daten, die außerhalb des dedizierten SQL-Pools gespeichert sind.

Die Definition externer Tabellen umfasst die Angabe der Datenquelle, des Formats der Textdateien und der Tabellendefinitionen. Die folgenden Referenzartikel zur T-SQL-Syntax benötigen Sie:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Verwenden Sie beim Laden von Parquet-Dateien die folgende SQL-Datentypzuordnung:

|                         Parquet-Typ                         |   Logischer Parquet-Typ (Anmerkung)   |  SQL-Datentyp   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY/BYTE_ARRAY                      |                                       |    varbinary     |
|                            Double                            |                                       |      float       |
|                            GLEITKOMMAZAHL                             |                                       |       real       |
|                            INT32                             |                                       |       INT        |
|                            INT64                             |                                       |      BIGINT      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      BINARY      |
|                            BINARY                            |                 UTF8                  |     NVARCHAR     |
|                            BINARY                            |                STRING                 |     NVARCHAR     |
|                            BINARY                            |                 ENUM                  |     NVARCHAR     |
|                            BINARY                            |                 UUID                  | UNIQUEIDENTIFIER |
|                            BINARY                            |                DECIMAL                |     Decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(Max)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     Decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     SMALLINT     |
|                            INT32                             |            INT(16,   true)            |     SMALLINT     |
|                            INT32                             |             INT(32, true)             |       INT        |
|                            INT32                             |            INT(8,   false)            |     TINYINT      |
|                            INT32                             |            INT(16, false)             |       INT        |
|                            INT32                             |           INT(32,   false)            |      BIGINT      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     Decimal      |
|                            INT32                             |            TIME (MILLIS)             |       time       |
|                            INT64                             |            INT(64,   true)            |      BIGINT      |
|                            INT64                             |           INT(64, false  )            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     Decimal      |
|                            INT64                             |         TIME (MILLIS)                 |       time       |
|                            INT64                             | TIMESTAMP (MILLIS)                  |    datetime2     |
| [Komplexer Typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 AUFLISTEN                  |   varchar(max)   |
| [Komplexer Typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |

>[!IMPORTANT] 
>- Dedizierte SQL-Pools unterstützen derzeit keine Parquet-Datentypen mit einer Genauigkeit von MICROS oder NANOS. 
>- Wenn die Typen zwischen Parquet und SQL nicht übereinstimmen oder wenn Sie nicht unterstützte Parquet-Datentypen verwenden, tritt eventuell der folgende Fehler auf: **„HdfsBridge::recordReaderFillBuffer – unerwarteter Fehler beim Ausfüllen des Datensatz-Lesepuffers: ClassCastException: ...“**
>- Das Laden eines Werts außerhalb des Bereichs von 0 bis 127 in eine tinyint-Spalte wird für Parquet und das ORC-Dateiformat nicht unterstützt.

Ein Beispiel für die Erstellung externer Objekte finden Sie unter [Erstellen externer Tabellen](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Formatieren von Textdateien

Bei Verwendung von PolyBase müssen die definierten externen Objekte die Zeilen der Textdateien mit der externen Tabelle und der Definition des Dateiformats abgleichen. Die Daten in den einzelnen Zeilen der Textdatei müssen mit der Tabellendefinition übereinstimmen.
So formatieren Sie die Textdateien

- Wenn Ihre Daten aus einer nicht relationalen Quelle stammen, müssen Sie sie in Zeilen und Spalten transformieren. Unabhängig davon, ob die Daten aus einer relationalen oder nicht relationalen Quelle stammen, müssen die Daten so transformiert werden, dass sie mit den Spaltendefinitionen der Tabelle übereinstimmen, in die die Daten geladen werden sollen.
- Formatieren Sie die Daten in der Textdatei so, dass sie mit den Spalten und Datentypen in der Zieltabelle übereinstimmen. Eine Nichtübereinstimmung zwischen Datentypen in den externen Textdateien und der Tabelle des dedizierten SQL-Pools führt dazu, dass Zeilen beim Laden zurückgewiesen werden.
- Trennen Sie Felder in der Textdatei mit einem Abschlusszeichen.  Stellen Sie sicher, dass Sie ein Zeichen oder eine Zeichenfolge verwenden, die nicht in Ihren Quelldaten enthalten ist. Verwenden Sie das durch [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) angegebene Abschlusszeichen.

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Laden der Daten mithilfe von PolyBase oder der COPY-Anweisung

Es hat sich bewährt, die Daten in eine Stagingtabelle zu laden. Stagingtabellen ermöglichen das Behandeln von Fehlern, ohne die Produktionstabellen zu beeinträchtigen. Eine Stagingtabelle bietet Ihnen außerdem die Möglichkeit, die parallele Verarbeitungsarchitektur des dedizierten SQL-Pools für Datentransformationen zu verwenden, bevor die Daten in Produktionstabellen eingefügt werden.

### <a name="options-for-loading"></a>Ladeoptionen

Zum Laden von Daten können Sie eine der folgenden Ladeoptionen nutzen:

- Die [COPY-Anweisung](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) ist das empfohlene Ladehilfsprogramm, da sie das nahtlose und flexible Laden von Daten ermöglicht. Die Anweisung verfügt über viele zusätzliche Ladefunktionen, die PolyBase nicht bereitstellt. 
- [PolyBase mit T-SQL](load-data-from-azure-blob-storage-using-polybase.md) setzt die Definition externer Datenobjekte voraus.
- [PolyBase und COPY-Anweisung mit Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ist ein weiteres Orchestrierungstool.  Es definiert eine Pipeline und plant Aufträge.
- [PolyBase mit SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) funktioniert gut, wenn sich die Quelldaten in SQL Server befinden. SSIS definiert die Zuordnung von Quell- zu Zieltabellen und orchestriert zudem die Workload. Wenn Sie bereits über SSIS-Pakete verfügen, können Sie die Pakete so ändern, dass sie mit dem neuen Data Warehouse-Ziel funktionieren.
- [PolyBase mit Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) überträgt Daten aus einer Tabelle in einen Databricks-Datenrahmen und/oder schreibt Daten aus einem Databricks-Datenrahmen in eine SQL Data Warehouse-Tabelle, die PolyBase verwendet.

### <a name="other-loading-options"></a>Weitere Ladeoptionen

Neben PolyBase und der COPY-Anweisung können Sie auch [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) oder die [SqlBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verwenden. Mit „bcp“ werden Daten direkt in die Datenbank geladen, ohne Azure Blob Storage zu durchlaufen. Daher ist es nur für kleine Workloads konzipiert.

> [!NOTE]
> Die Ladeleistung dieser Optionen ist geringer als bei PolyBase und der COPY-Anweisung.

## <a name="5-transform-the-data"></a>5. Transformieren der Daten

Führen Sie während der Bereitstellung der Daten in der Stagingtabelle entsprechende Transformationen durch, die für Ihre Workload erforderlich sind. Anschließend verschieben Sie die Daten in eine Produktionstabelle.

## <a name="6-insert-the-data-into-production-tables"></a>6. Einfügen der Daten in Produktionstabellen

Die SELECT-Anweisung „INSERT INTO...“ verschiebt die Daten aus der Stagingtabelle in die permanente Tabelle.

Versuchen Sie beim Entwerfen eines ETL-Prozesses, den Prozess für ein kleines Testbeispiel auszuführen. Versuchen Sie, 1000 Zeilen aus der Tabelle in eine Datei zu extrahieren, sie nach Azure zu verschieben und sie dann in eine Stagingtabelle zu laden.

## <a name="partner-loading-solutions"></a>Ladelösungen von Partnern

Viele unserer Partner stellen Ladelösungen bereit. Weitere Informationen finden Sie in der Liste mit unseren [Lösungspartnern](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Anleitung zum Laden finden Sie unter [Guidance for loading data into Azure SQL Data Warehouse](guidance-for-loading-data.md) (Anleitung zum Laden von Daten in Azure SQL Data Warehouse).
