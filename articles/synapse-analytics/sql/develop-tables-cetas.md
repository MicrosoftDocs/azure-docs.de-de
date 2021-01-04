---
title: CREATE EXTERNAL TABLE AS SELECT (CETAS) in Synapse SQL
description: Hier erfahren Sie mehr über die Verwendung von CREATE EXTERNAL TABLE AS SELECT (CETAS) in Synapse SQL.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8ffb3a0948267ea40a5d0511de63a80ad23584d1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454671"
---
# <a name="cetas-with-synapse-sql"></a>CETAS mit Synapse SQL

Sie können „CREATE EXTERNAL TABLE AS SELECT (CETAS)“ in einem dedizierten SQL-Pool oder in einem serverlosen SQL-Pool verwenden, um die folgenden Aufgaben auszuführen:  

- Erstellen einer externen Tabelle
- paralleles Exportieren der Ergebnisse einer Transact-SQL-SELECT-Anweisung in folgende Dienste:

  - Hadoop
  - Azure Storage-Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-dedicated-sql-pool"></a>CETAS im dedizierten SQL-Pool

Informationen zum dedizierten SQL-Pool sowie zur Verwendung und Syntax von CETAS finden Sie im Artikel [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Zusätzlich finden Sie Anleitungen zur Verwendung von CTAS mit dem dedizierten SQL-Pool im Artikel [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="cetas-in-serverless-sql-pool"></a>CETAS im serverlosen SQL-Pool

Bei Verwendung eines serverlosen SQL-Pools dient CETAS zum Erstellen einer externen Tabelle und Exportieren von Abfrageergebnissen in Azure Storage Blob oder Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Syntax

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumente

*[ [ *Datenbankname* . [ *Schemaname* ] . ] | *Schemaname* . ] *Tabellenname**

Ein- bis dreiteiliger Name der Tabelle, die erstellt werden soll. Bei einer externen Tabelle speichert der serverlose SQL-Pool nur die Tabellenmetadaten. Im serverlosen SQL-Pool werden keine tatsächlichen Daten verschoben oder gespeichert.

LOCATION = *'Pfad_zum_Ordner'*

Gibt an, wohin die Ergebnisse der SELECT-Anweisung auf der externen Datenquelle geschrieben werden sollen. Der Stammordner ist der in der externen Datenquelle angegebene Datenspeicherort. LOCATION muss auf einen Ordner zeigen und einen nachgestellten Schrägstrich aufweisen: /. Beispiel: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Gibt den Namen des externen Datenquellenobjekts an, das den Speicherort enthält, an dem die externen Daten gespeichert werden. Verwenden Sie zum Erstellen einer externen Datenquelle [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Gibt den Namen des externen Dateiformatobjekts an, das das Format für die externe Datendatei enthält. Verwenden Sie zum Erstellen eines externen Dateiformats [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Derzeit werden nur externe Dateiformate mit FORMAT_TYPE=PARQUET und FORMAT_TYPE=DELIMITEDTEXT unterstützt. Die GZip-Komprimierung für das DELIMITEDTEXT-Format wird nicht unterstützt.

WITH *<common_table_expression>*

Gibt ein temporäres benanntes Resultset an, das als allgemeiner Tabellenausdruck (CTE, Common Table Expression) bezeichnet wird. Weitere Informationen finden Sie unter [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

SELECT <select_criteria>

Füllt die neue Tabelle mit den Ergebnissen einer SELECT-Anweisung auf. *Select_criteria* ist der Hauptteil der SELECT-Anweisung, der bestimmt, welche Daten in die neue Tabelle kopiert werden sollen. Informationen zu SELECT-Anweisungen finden Sie unter [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> Die ORDER BY-Klausel in SELECT wird für CETAS nicht unterstützt.

## <a name="permissions"></a>Berechtigungen

Damit CETAS funktioniert, benötigen Sie Berechtigungen zum Auflisten des Ordnerinhalts und zum Schreiben in den mit LOCATION angegebenen Ordner.

## <a name="examples"></a>Beispiele

Diese Beispiele verwenden CETAS, um eine nach Jahr und Staat aggregierte Gesamtbevölkerungszahl in den Ordner „aggregated_data“ zu speichern, der sich in der Datenquelle „population_ds“ befindet.

Dieses Beispiel verwendet die zuvor erstellten Parameter für Anmeldeinformationen, Datenquelle und externes Dateiformat. Informationen dazu finden Sie im Dokument [Externe Tabellen](develop-tables-external-tables.md). Um Abfrageergebnisse in einem anderen Ordner in derselben Datenquelle zu speichern, ändern Sie das LOCATION-Argument. 

Um Ergebnisse in einem anderen Speicherkonto zu speichern, erstellen und verwenden Sie eine andere Datenquelle für das DATA_SOURCE-Argument.

> [!NOTE]
> In den folgenden Beispielen wird ein öffentliches Azure Open Data-Speicherkonto verwendet. Dieses ist schreibgeschützt. Um diese Abfragen auszuführen, müssen Sie die Datenquelle angeben, für die Sie Schreibberechtigungen besitzen.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

Im folgenden Beispiel wird eine externe Tabelle als Quelle für CETAS verwendet. Es nutzt die zuvor erstellten Parameter für Anmeldeinformationen, Datenquelle, externes Dateiformat und externe Tabelle. Informationen dazu finden Sie im Dokument [Externe Tabellen](develop-tables-external-tables.md).

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Unterstützte Datentypen

CETAS kann zum Speichern von Resultsets mit den folgenden SQL-Datentypen verwendet werden:

- BINARY
- varbinary
- char
- varchar
- NCHAR
- NVARCHAR
- smalldate
- date
- datetime
- datetime2
- datetimeoffset
- time
- Decimal
- NUMERIC
- float
- real
- BIGINT
- TINYINT
- SMALLINT
- INT
- BIGINT
- bit
- money
- SMALLMONEY
- UNIQUEIDENTIFIER

> [!NOTE]
> LOBs, die größer als 1 MB sind, können nicht mit CETAS verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, [Apache Spark für externe Azure Synapse-Tabellen](develop-storage-files-spark-tables.md) abzufragen.
