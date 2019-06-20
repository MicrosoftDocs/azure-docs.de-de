---
title: Entwerfen von ELT- anstelle von ETL-Prozessen für Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Entwerfen Sie einen Extrahieren, Transformieren und Laden (ETL)- anstelle eines ETL-Prozesses zum Herunterladen von Daten oder für Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load data
ms.date: 05/10/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: de5649498dddcec8c65f2cfca6dcb39fa20a9267
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242257"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-sql-data-warehouse"></a>Entwerfen einer Strategie für Azure SQL Data Warehouse zum Laden von PolyBase-Daten

Traditionell verwenden SMP-Data Warehouses einen ETL-Prozess (Extrahieren, Transformieren und Laden) zum Laden von Daten. Azure SQL Data Warehouse ist eine Architektur mit massiver Parallelverarbeitung (MPP), die die Vorteile der Skalierbarkeit und Flexibilität von Compute-und Speicherressourcen nutzt. Ein ETL-Prozess (Extrahieren, Transformieren und Laden) kann die Vorteile von MPP nutzen und Ressourcen entfernen, die vor dem Laden zum Transformieren der Daten erforderlich sind. Auch wenn SQL Data Warehouse viele Lademethoden unterstützt (u.a. Nicht-PolyBase-Optionen wie BCP und die SQL-BulkCopy-API), stellt PolyBase die schnellste und am besten skalierbare Möglichkeit zum Laden von Daten dar.  PolyBase ist eine Technologie, die über die T-SQL-Sprache auf externe Daten zugreift, die in Azure Blob Storage oder Azure Data Lake Storage gespeichert sind.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Was ist ELT?

ETL (Extrahieren, Transformieren und Laden) ist ein Prozess, mit dem Daten aus einem Quellsystem in ein Ziel-Data Warehouse geladen und dann transformiert werden. 

Die grundlegenden Schritte zum Implementieren eines PolyBase-ETL-Prozesses für SQL Data Warehouse sind:

1. Extrahieren Sie die Quelldaten in Textdateien.
2. Legen Sie die Daten in Azure Blob Storage oder Azure Data Lake Store ab.
3. Bereiten Sie die Daten für das Laden vor.
4. Laden Sie die Daten mit PolyBase in SQL Data Warehouse-Stagingtabellen. 
5. Transformieren Sie die Daten.
6. Fügen Sie die Daten in Produktionstabellen ein.


Ein Tutorial zum Ladevorgang finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage in Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Weitere Informationen finden Sie im Blog [Lademuster](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/). 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahieren der Quelldaten in Textdateien

Das Abrufen von Daten aus dem Quellsystem hängt vom Speicherort ab.  Ziel ist es, die Daten in durch Trennzeichen getrennte Textdateien zu verschieben, die von PolyBase unterstützt werden. 

### <a name="polybase-external-file-formats"></a>Externe PolyBase-Dateiformate

PolyBase lädt Daten aus UTF-8- und UTF-16-codierten, durch Trennzeichen getrennten Textdateien. Zusätzlich zu den durch Trennzeichen getrennten Textdateien lädt es aus den Hadoop-Dateiformaten „RC File“, „ORC“ und „Parquet“. PolyBase kann auch Daten aus Dateien laden, die mit Gzip und Snappy komprimiert wurden. PolyBase unterstützt derzeit kein erweitertes ASCII, keine Formate mit fester Breite und keine geschachtelten Formate wie WinZip, JSON und XML. Wenn Sie aus SQL Server exportieren, können Sie die Daten mit dem [Befehlszeilentool bcp](/sql/tools/bcp-utility) in durch Trennzeichen getrennte Textdateien exportieren. Die Datentypzuordnung „Parquet zu SQL Data Warehouse“ lautet wie folgt:

| **Parquet-Datentyp** |                      **SQL-Datentyp**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        Zeichenfolge         |                            nchar                             |
|        Zeichenfolge         |                           nvarchar                           |
|        Zeichenfolge         |                             char                             |
|        Zeichenfolge         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date        | 1) Laden als „int“ und Umwandeln in „date“ </br> 2) [Verwenden des Azure Databricks SQL Data Warehouse-Connectors](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse) bei </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**Update in Kürze verfügbar**) |
|        decimal        | [Verwenden des Azure Databricks SQL Data Warehouse-Connectors](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse) bei </br> spark.conf.set( "spark.sql.parquet.writeLegacyFormat", "true" ) </br> (**Update in Kürze verfügbar**) |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Laden der Daten in Azure Blob Storage oder Azure Data Lake Storage

Um die Daten in Azure Storage zu verschieben, können Sie sie in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) oder [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) verschieben. In beiden Fällen sollten die Daten in Textdateien gespeichert werden. PolyBase kann die Daten von beiden Speicherorten laden.

Tools und Dienste, mit denen Sie Daten in Azure Storage verschieben können:

- Der [Azure ExpressRoute](../expressroute/expressroute-introduction.md)-Dienst verbessert Netzwerkdurchsatz, Leistung und Vorhersagbarkeit. ExpressRoute ist ein Dienst, der Ihre Daten über eine dedizierte private Verbindung zu Azure weiterleitet. Bei ExpressRoute-Verbindungen werden Daten nicht über das öffentliche Internet weitergeleitet. Die Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen über das öffentliche Internet.
- Das Hilfsprogramm [AZCopy](../storage/common/storage-moving-data.md) verschiebt Daten über das öffentliche Internet in Azure Storage. Dies funktioniert, wenn Ihre Datenmengen weniger als 10 TB umfassen. Wenn Sie Ladevorgänge in regelmäßigen Abständen mit AZCopy ausführen möchten, testen Sie die Netzwerkgeschwindigkeit, um festzustellen, ob sie geeignet ist. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) verfügt über ein Gateway, das Sie auf dem lokalen Server installieren können. Anschließend können Sie eine Pipeline erstellen, um Daten vom lokalen Server in Azure Storage zu verschieben. Informationen zur Verwendung der Data Factory mit SQL Data Warehouse finden Sie unter [Laden von Daten in Azure SQL Data Warehouse mit Azure Data Factory](/azure/data-factory/load-azure-sql-data-warehouse).


## <a name="3-prepare-the-data-for-loading"></a>3. Vorbereiten der Daten für das Laden

Möglicherweise müssen Sie die Daten in Ihrem Speicherkonto vorbereiten und bereinigen, bevor Sie sie in SQL Data Warehouse laden. Die Datenvorbereitung kann durchgeführt werden, während sich Ihre Daten in der Quelle befinden, während Sie die Daten in Textdateien exportieren oder nachdem sich die Daten in Azure Storage befinden.  Am einfachsten ist es, so früh wie möglich im Prozess mit den Daten zu arbeiten.  

### <a name="define-external-tables"></a>Definieren externer Tabellen

Bevor Sie Daten laden können, müssen Sie externe Tabellen im Data Warehouse definieren. PolyBase verwendet externe Tabellen, um Daten in Azure Storage zu definieren und auf diese zuzugreifen. Eine externe Tabelle ähnelt einer Datenbanksicht. Die externe Tabelle enthält das Tabellenschema und verweist auf Daten, die außerhalb des Data Warehouse gespeichert sind. 

Die Definition externer Tabellen umfasst die Angabe der Datenquelle, des Formats der Textdateien und der Tabellendefinitionen. Dies sind die Themen der T-SQL-Syntax, die Sie benötigen:
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Ein Beispiel für die Erstellung externer Objekte finden Sie im Schritt [Erstellen externer Tabellen](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) im Tutorial zu Ladevorgängen.

### <a name="format-text-files"></a>Formatieren von Textdateien

Nachdem die externen Objekte definiert sind, müssen Sie die Zeilen der Textdateien mit der Definition der externen Tabelle und des Dateiformats abgleichen. Die Daten in den einzelnen Zeilen der Textdatei müssen mit der Tabellendefinition übereinstimmen.
So formatieren Sie die Textdateien

- Wenn Ihre Daten aus einer nicht relationalen Quelle stammen, müssen Sie sie in Zeilen und Spalten transformieren. Unabhängig davon, ob die Daten aus einer relationalen oder nicht relationalen Quelle stammen, müssen die Daten so transformiert werden, dass sie mit den Spaltendefinitionen der Tabelle übereinstimmen, in die die Daten geladen werden sollen. 
- Formatieren Sie die Daten in der Textdatei so, dass sie mit den Spalten und Datentypen in der SQL Data Warehouse-Zieltabelle übereinstimmen. Eine Nichtübereinstimmung zwischen den Datentypen in den externen Textdateien und der Data Warehouse-Tabelle führt dazu, dass Zeilen beim Laden zurückgewiesen werden.
- Trennen Sie Felder in der Textdatei mit einem Abschlusszeichen.  Stellen Sie sicher, dass Sie ein Zeichen oder eine Zeichenfolge verwenden, die nicht in Ihren Quelldaten enthalten ist. Verwenden Sie das durch [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql) angegebene Abschlusszeichen.


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase"></a>4. Laden der Daten mit PolyBase in SQL Data Warehouse-Stagingtabellen

Es hat sich bewährt, die Daten in eine Stagingtabelle zu laden. Stagingtabellen ermöglichen das Behandeln von Fehlern, ohne die Produktionstabellen zu beeinträchtigen. Eine Stagingtabelle bietet Ihnen außerdem die Möglichkeit, Datentransformationen mit SQL Data Warehouse-MPP auszuführen, bevor Daten in Produktionstabellen eingefügt werden.

### <a name="options-for-loading-with-polybase"></a>Optionen beim Laden mit PolyBase

Um Daten mit PolyBase zu laden, können Sie eine der folgenden Ladeoptionen nutzen:

- [PolyBase mit T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funktioniert gut, wenn sich Ihre Daten in Azure Blob Storage oder Azure Data Lake Store befinden. Es bietet Ihnen die größtmögliche Kontrolle über den Ladevorgang, erfordert aber auch die Definition externer Datenobjekte. Die anderen Methoden definieren diese Objekte im Hintergrund, wenn Sie Quelltabellen zu Zieltabellen zuordnen.  Zum Orchestrieren von T-SQL-Ladevorgängen können Sie Azure Data Factory-, SSIS- oder Azure-Funktionen verwenden. 
- [PolyBase mit SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) funktioniert gut, wenn sich die Quelldaten in SQL Server befinden (entweder lokal in SQL Server oder in der Cloud). SSIS definiert die Zuordnung von Quell- zu Zieltabellen und orchestriert zudem die Workload. Wenn Sie bereits über SSIS-Pakete verfügen, können Sie die Pakete so ändern, dass sie mit dem neuen Data Warehouse-Ziel funktionieren. 
- [PolyBase mit Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) ist ein weiteres Orchestrierungstool.  Es definiert eine Pipeline und plant Aufträge. 
- [PolyBase mit Azure DataBricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) überträgt Daten aus einer SQL Data Warehouse-Tabelle in einen Databricks-Datenrahmen und/oder schreibt Daten aus einem Databricks-Datenrahmen in eine SQL Data Warehouse-Tabelle, die PolyBase verwendet.

### <a name="non-polybase-loading-options"></a>Nicht von PolyBase stammende Ladeoptionen

Wenn Ihre Daten nicht mit PolyBase kompatibel sind, können Sie [bcp](/sql/tools/bcp-utility) oder die [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx) verwenden. Mit „bcp“ werden Daten direkt in SQL Data Warehouse geladen, ohne Azure Blob Storage zu durchlaufen. Daher ist es nur für kleine Workloads konzipiert. Beachten Sie, dass die Leistung dieser Optionen beim Laden wesentlich langsamer ist als bei PolyBase. 


## <a name="5-transform-the-data"></a>5. Transformieren der Daten

Führen Sie während der Bereitstellung der Daten in der Stagingtabelle entsprechende Transformationen durch, die für Ihre Workload erforderlich sind. Anschließend verschieben Sie die Daten in eine Produktionstabelle.


## <a name="6-insert-the-data-into-production-tables"></a>6. Einfügen der Daten in Produktionstabellen

Die SELECT-Anweisung „INSERT INTO...“ verschiebt die Daten aus der Stagingtabelle in die permanente Tabelle. 

Versuchen Sie beim Entwerfen eines ETL-Prozesses, den Prozess für ein kleines Testbeispiel auszuführen. Versuchen Sie, 1000 Zeilen aus der Tabelle in eine Datei zu extrahieren, sie nach Azure zu verschieben und sie dann in eine Stagingtabelle zu laden. 


## <a name="partner-loading-solutions"></a>Ladelösungen von Partnern

Viele unserer Partner stellen Ladelösungen bereit. Weitere Informationen finden Sie in der Liste mit unseren [Lösungspartnern](sql-data-warehouse-partner-business-intelligence.md). 


## <a name="next-steps"></a>Nächste Schritte

Eine Anleitung zum Laden finden Sie unter [Anleitung zum Laden von Daten](guidance-for-loading-data.md).


