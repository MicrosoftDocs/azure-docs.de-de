---
title: Entwerfen einer Strategie zum Laden von PolyBase-Daten für einen dedizierten SQL-Pool
description: Entwerfen Sie einen ELT-Prozess (Extrahieren, Laden und Transformieren) anstelle eines ETL-Prozesses zum Laden von Daten mit dediziertem SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a57abd080bdbbaefbe07258a2b241c093dc8c441
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308735"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Entwerfen einer Strategie zum Laden von PolyBase-Daten für einen dedizierten SQL-Pool in Azure Synapse Analytics

In herkömmlichen SMP-Data Warehouses wird zum Laden von Daten ein ETL-Prozess (Extrahieren, Transformieren und Laden) verwendet. Der Azure SQL-Pool ist eine Architektur mit massiver Parallelverarbeitung (MPP), die die Vorteile der Skalierbarkeit und Flexibilität von Compute- und Speicherressourcen nutzt. Ein ELT-Prozess (Extrahieren, Laden, Transformieren) kann die Vorteile von integrierten verteilten Abfrageverarbeitungsfunktionen nutzen und Ressourcen beseitigen, die vor dem Laden zum Transformieren der Daten erforderlich sind.

Auch wenn der SQL-Pool viele Lademethoden unterstützt (u. a. Nicht-PolyBase-Optionen wie BCP und die SQL-BulkCopy-API), stellt PolyBase die schnellste und am besten skalierbare Möglichkeit zum Laden von Daten dar.  PolyBase ist eine Technologie, die über die T-SQL-Sprache auf externe Daten zugreift, die in Azure Blob Storage oder Azure Data Lake Storage gespeichert sind.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extrahieren, Laden und Transformieren (ELT)

ELT (Extrahieren, Laden und Transformieren) ist ein Prozess, bei dem Daten aus einem Quellsystem extrahiert, in ein Data Warehouse geladen und anschließend transformiert werden.

Grundlegende Schritte zum Implementieren eines PolyBase-ELT-Prozesses für den dedizierten SQL-Pool:

1. Extrahieren Sie die Quelldaten in Textdateien.
2. Legen Sie die Daten in Azure Blob Storage oder Azure Data Lake Store ab.
3. Bereiten Sie die Daten für das Laden vor.
4. Laden Sie die Daten mithilfe von PolyBase in die Stagingtabellen des dedizierten SQL-Pools.
5. Transformieren Sie die Daten.
6. Fügen Sie die Daten in Produktionstabellen ein.

Ein Tutorial zum Ladevorgang finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage in Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Weitere Informationen finden Sie im Blog [Lademuster](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahieren der Quelldaten in Textdateien

Das Abrufen von Daten aus dem Quellsystem hängt vom Speicherort ab.  Ziel ist es, die Daten in durch Trennzeichen getrennte Textdateien zu verschieben, die von PolyBase unterstützt werden.

### <a name="polybase-external-file-formats"></a>Externe PolyBase-Dateiformate

PolyBase lädt Daten aus UTF-8- und UTF-16-codierten, durch Trennzeichen getrennten Textdateien. Zusätzlich zu den durch Trennzeichen getrennten Textdateien lädt es aus den Hadoop-Dateiformaten „RC File“, „ORC“ und „Parquet“. PolyBase kann auch Daten aus Dateien laden, die mit Gzip und Snappy komprimiert wurden. PolyBase unterstützt derzeit kein erweitertes ASCII, keine Formate mit fester Breite und keine geschachtelten Formate wie WinZip, JSON und XML.

Wenn Sie aus SQL Server exportieren, können Sie die Daten mit dem [Befehlszeilentool bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in durch Trennzeichen getrennte Textdateien exportieren. Die Datentypzuordnung „Parquet zu Azure Synapse Analytics“ lautet wie folgt:

| **Parquet-Datentyp** |                      **SQL-Datentyp**                       |
| :-------------------: | :----------------------------------------------------------: |
|        TINYINT        |                           TINYINT                            |
|       SMALLINT        |                           SMALLINT                           |
|          INT          |                             INT                              |
|        BIGINT         |                            BIGINT                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          SMALLMONEY                          |
|        Zeichenfolge         |                            NCHAR                             |
|        Zeichenfolge         |                           NVARCHAR                           |
|        Zeichenfolge         |                             char                             |
|        Zeichenfolge         |                           varchar                            |
|        BINARY         |                            BINARY                            |
|        BINARY         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        Decimal        |                            Decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Laden der Daten in Azure Blob Storage oder Azure Data Lake Storage

Um die Daten in Azure Storage zu verschieben, können Sie sie in [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) oder [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) verschieben. In beiden Fällen sollten die Daten in Textdateien gespeichert werden. PolyBase kann die Daten von beiden Speicherorten laden.

Tools und Dienste, mit denen Sie Daten in Azure Storage verschieben können:

- Der [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)-Dienst verbessert Netzwerkdurchsatz, Leistung und Vorhersagbarkeit. ExpressRoute ist ein Dienst, der Ihre Daten über eine dedizierte private Verbindung zu Azure weiterleitet. Bei ExpressRoute-Verbindungen werden Daten nicht über das öffentliche Internet weitergeleitet. Die Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen über das öffentliche Internet.
- Das Hilfsprogramm [AZCopy](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) verschiebt Daten über das öffentliche Internet in Azure Storage. Dies funktioniert, wenn Ihre Datenmengen weniger als 10 TB umfassen. Wenn Sie Ladevorgänge in regelmäßigen Abständen mit AZCopy ausführen möchten, testen Sie die Netzwerkgeschwindigkeit, um festzustellen, ob sie geeignet ist.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) verfügt über ein Gateway, das Sie auf dem lokalen Server installieren können. Anschließend können Sie eine Pipeline erstellen, um Daten vom lokalen Server in Azure Storage zu verschieben. Weitere Informationen zum Verwenden der Data Factory mit dem dedizierten SQL-Pool finden Sie unter [Laden von Daten in Azure Synapse Analytics mithilfe von Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Vorbereiten der Daten für das Laden

Möglicherweise müssen Sie die Daten in Ihrem Speicherkonto vorbereiten und bereinigen, bevor Sie sie in den dedizierten SQL-Pool laden. Die Datenvorbereitung kann durchgeführt werden, während sich Ihre Daten in der Quelle befinden, während Sie die Daten in Textdateien exportieren oder nachdem sich die Daten in Azure Storage befinden.  Am einfachsten ist es, so früh wie möglich im Prozess mit den Daten zu arbeiten.  

### <a name="define-external-tables"></a>Definieren externer Tabellen

Bevor Sie Daten laden können, müssen Sie externe Tabellen im Data Warehouse definieren. PolyBase verwendet externe Tabellen, um Daten in Azure Storage zu definieren und auf diese zuzugreifen. Eine externe Tabelle ähnelt einer Datenbanksicht. Die externe Tabelle enthält das Tabellenschema und verweist auf Daten, die außerhalb des Data Warehouse gespeichert sind.

Die Definition externer Tabellen umfasst die Angabe der Datenquelle, des Formats der Textdateien und der Tabellendefinitionen. Im Folgenden finden Sie die Themen zur T-SQL-Syntax, die Sie benötigen:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="format-text-files"></a>Formatieren von Textdateien

Nachdem die externen Objekte definiert sind, müssen Sie die Zeilen der Textdateien mit der Definition der externen Tabelle und des Dateiformats abgleichen. Die Daten in den einzelnen Zeilen der Textdatei müssen mit der Tabellendefinition übereinstimmen.
So formatieren Sie die Textdateien

- Wenn Ihre Daten aus einer nicht relationalen Quelle stammen, müssen Sie sie in Zeilen und Spalten transformieren. Unabhängig davon, ob die Daten aus einer relationalen oder nicht relationalen Quelle stammen, müssen die Daten so transformiert werden, dass sie mit den Spaltendefinitionen der Tabelle übereinstimmen, in die die Daten geladen werden sollen.
- Formatieren Sie die Daten in der Textdatei so, dass sie mit den Spalten und Datentypen in der Zieltabelle des SQL-Pools übereinstimmen. Eine Nichtübereinstimmung zwischen den Datentypen in den externen Textdateien und der Data Warehouse-Tabelle führt dazu, dass Zeilen beim Laden zurückgewiesen werden.
- Trennen Sie Felder in der Textdatei mit einem Abschlusszeichen.  Stellen Sie sicher, dass Sie ein Zeichen oder eine Zeichenfolge verwenden, die nicht in Ihren Quelldaten enthalten ist. Verwenden Sie das durch [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) angegebene Abschlusszeichen.

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Laden der Daten in die Stagingtabellen des dedizierten SQL-Pools mithilfe von PolyBase

Es hat sich bewährt, die Daten in eine Stagingtabelle zu laden. Stagingtabellen ermöglichen das Behandeln von Fehlern, ohne die Produktionstabellen zu beeinträchtigen. Eine Stagingtabelle bietet Ihnen außerdem die Möglichkeit, in SQL-Pool integrierte verteilte Abfrageverarbeitungsfunktionen für Datentransformationen zu verwenden, bevor die Daten in Produktionstabellen eingefügt werden.

### <a name="options-for-loading-with-polybase"></a>Optionen beim Laden mit PolyBase

Um Daten mit PolyBase zu laden, können Sie eine der folgenden Ladeoptionen nutzen:

- [PolyBase mit T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) funktioniert gut, wenn sich Ihre Daten in Azure Blob Storage oder Azure Data Lake Store befinden. Es bietet Ihnen die größtmögliche Kontrolle über den Ladevorgang, erfordert aber auch die Definition externer Datenobjekte. Die anderen Methoden definieren diese Objekte im Hintergrund, wenn Sie Quelltabellen zu Zieltabellen zuordnen.  Zum Orchestrieren von T-SQL-Ladevorgängen können Sie Azure Data Factory-, SSIS- oder Azure-Funktionen verwenden.
- [PolyBase mit SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) funktioniert gut, wenn sich die Quelldaten in SQL Server befinden. SSIS definiert die Zuordnung von Quell- zu Zieltabellen und orchestriert zudem die Workload. Wenn Sie bereits über SSIS-Pakete verfügen, können Sie die Pakete so ändern, dass sie mit dem neuen Data Warehouse-Ziel funktionieren.
- [PolyBase mit Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ist ein weiteres Orchestrierungstool.  Es definiert eine Pipeline und plant Aufträge.
- [PolyBase mit Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) überträgt Daten aus einer Azure Synapse Analytics-Tabelle in einen Databricks-Datenrahmen und/oder schreibt Daten aus einem Databricks-Datenrahmen in eine Azure Synapse Analytics-Tabelle, die PolyBase verwendet.

### <a name="non-polybase-loading-options"></a>Nicht von PolyBase stammende Ladeoptionen

Wenn Ihre Daten nicht mit PolyBase kompatibel sind, können Sie [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) oder die [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) verwenden. Mit „bcp“ werden Daten direkt in den dedizierten SQL-Pool geladen, ohne Azure Blob Storage zu durchlaufen. Daher ist es nur für kleine Lasten konzipiert. Beachten Sie, dass die Leistung dieser Optionen beim Laden wesentlich langsamer ist als bei PolyBase.

## <a name="5-transform-the-data"></a>5. Transformieren der Daten

Führen Sie während der Bereitstellung der Daten in der Stagingtabelle entsprechende Transformationen durch, die für Ihre Workload erforderlich sind. Anschließend verschieben Sie die Daten in eine Produktionstabelle.

## <a name="6-insert-the-data-into-production-tables"></a>6. Einfügen der Daten in Produktionstabellen

Die SELECT-Anweisung „INSERT INTO...“ verschiebt die Daten aus der Stagingtabelle in die permanente Tabelle.

Versuchen Sie beim Entwerfen eines ETL-Prozesses, den Prozess für ein kleines Testbeispiel auszuführen. Versuchen Sie, 1000 Zeilen aus der Tabelle in eine Datei zu extrahieren, sie nach Azure zu verschieben und sie dann in eine Stagingtabelle zu laden.

## <a name="partner-loading-solutions"></a>Ladelösungen von Partnern

Viele unserer Partner stellen Ladelösungen bereit. Weitere Informationen finden Sie in der Liste mit unseren [Lösungspartnern](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Nächste Schritte

Eine Anleitung zum Laden finden Sie unter [Anleitung zum Laden von Daten](data-loading-best-practices.md).
