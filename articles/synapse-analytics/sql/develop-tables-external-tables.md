---
title: Verwenden externer Tabellen mit Synapse SQL
description: Lesen oder Schreiben von Datendateien mit Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c2daef64b110e59da76d8342508c19c7f1b3cd08
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452786"
---
# <a name="use-external-tables-with-synapse-sql"></a>Verwenden externer Tabellen mit Synapse SQL

Eine externe Tabelle verweist auf Daten in Hadoop, Azure Storage Blob oder Azure Data Lake Store. Externe Tabellen werden verwendet, um Daten aus Dateien zu lesen oder Daten in Dateien in Azure Storage zu schreiben. Mit Synapse SQL können Sie externe Tabellen verwenden, um Daten aus einem dedizierten SQL-Pool oder aus einem serverlosen SQL-Pool zu lesen und dorthin zu schreiben.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Externe Tabellen im dedizierten SQL-Pool und serverlosen SQL-Pool

### <a name="dedicated-sql-pool"></a>[Dedizierter SQL-Pool](#tab/sql-pool) 

In einem dedizierten SQL-Pool können Sie eine externe Tabelle für Folgendes verwenden:

- Abfragen von Azure Blob Storage und Azure Data Lake Gen2 mit Transact-SQL-Anweisungen
- Importieren und Speichern von Daten aus Azure Blob Storage und Azure Data Lake Storage in den dedizierten SQL-Pool

In Verbindung mit der Anweisung [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) werden in einer externen Tabelle ausgewählte Daten in eine Tabelle innerhalb des SQL-Pools importiert. Zusätzlich zur [COPY-Anweisung](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) sind externe Tabellen hilfreich beim Laden von Daten. 

Ein Tutorial zum Laden finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="serverless-sql-pool"></a>[Serverloser SQL-Pool](#tab/sql-on-demand)

Im serverlosen SQL-Pool können Sie eine externe Tabelle für Folgendes verwenden:

- Abfragen von Azure Blob Storage oder Azure Data Lake Storage mit Transact-SQL-Anweisungen
- Speichern der Abfrageergebnisse von einem serverlosen SQL-Pool in Dateien in Azure Blob Storage oder Azure Data Lake Storage mithilfe von [CETAS](develop-tables-cetas.md)

Externe Tabellen können unter Verwendung eines serverlosen SQL-Pools mithilfe der folgenden Schritte erstellt werden:

1. CREATE EXTERNAL DATA SOURCE
2. CREATE EXTERNAL FILE FORMAT
3. CREATE EXTERNAL TABLE

---

### <a name="security"></a>Sicherheit

Der Benutzer muss über die Berechtigung `SELECT` für eine externe Tabelle verfügen, um die Daten lesen zu können.
Externer Tabellenzugriff auf den zugrunde liegenden Azure-Speicher mithilfe der datenbankweit gültigen Anmeldeinformationen, die in der Datenquelle mit den folgenden Regeln definiert wurden:
- Eine Datenquelle ohne Anmeldeinformationen ermöglicht externen Tabellen den Zugriff auf öffentlich verfügbare Dateien im Azure-Speicher.
- Eine Datenquelle kann über Anmeldeinformationen verfügen, die externen Tabellen den Zugriff nur auf die Dateien im Azure-Speicher mithilfe des SAS-Tokens oder der verwalteten Identität für den Arbeitsbereich ermöglichen. Entsprechende Beispiele finden Sie im Artikel [Develop storage files storage access control](develop-storage-files-storage-access-control.md#examples) (Entwickeln der Speicherzugriffssteuerung für Speicherdateien).

> [!IMPORTANT]
> Im dedizierten SQL-Pool ermöglicht eine Datenquelle, die ohne Anmeldeinformationen erstellt wurde, Azure AD-Benutzern den Zugriff auf Speicherdateien mithilfe seiner Azure AD-Identität. Im serverlosen SQL-Pool müssen Sie eine Datenquelle mit datenbankweit gültigen Anmeldeinformationen erstellen, die über die Eigenschaft `IDENTITY='User Identity'` verfügen. Entsprechende Beispiele finden Sie [hier](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

Externe Datenquellen dienen zum Herstellen einer Verbindung mit Speicherkonten. Die vollständige Dokumentation finden Sie [hier](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="syntax-for-create-external-data-source"></a>Syntax für „CREATE EXTERNAL DATA SOURCE“

#### <a name="dedicated-sql-pool"></a>[Dedizierter SQL-Pool](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="serverless-sql-pool"></a>[Serverloser SQL-Pool](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argumente für „CREATE EXTERNAL DATA SOURCE“

data_source_name

Gibt den benutzerdefinierten Namen für die Datenquelle an. Dieser Name muss innerhalb der Datenbank eindeutig sein.

#### <a name="location"></a>Standort
„LOCATION = `'<prefix>://<path>'`“: Dient zum Angeben des Konnektivitätsprotokolls und des Pfads der externen Datenquelle. Die folgenden Muster können am Speicherort verwendet werden:

| Externe Datenquelle        | Speicherort-Präfix | Location path (Pfad zum Speicherort)                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Storage Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Storage Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

Mit dem Präfix `https:` können Sie Unterordner im Pfad verwenden.

#### <a name="credential"></a>Anmeldeinformationen
CREDENTIAL = `<database scoped credential>` sind optionale Anmeldeinformationen, die zur Authentifizierung beim Azure-Speicher verwendet werden. Eine externe Datenquelle ohne Anmeldeinformationen kann auf ein öffentliches Speicherkonto zugreifen. 

Externe Datenquellen ohne Anmeldeinformationen im dedizierten SQL-Pool können auch die Azure AD-Identität des Aufrufers für den Zugriff auf Dateien im Speicher verwenden. Eine externe Datenquelle für den serverlosen SQL-Pool mit Anmeldeinformationen vom Typ `IDENTITY='User Identity'` verwendet die Azure AD-Identität des Aufrufers für den Zugriff auf Dateien.
- Im dedizierten SQL-Pool können datenbankweit gültige Anmeldeinformationen eine benutzerdefinierte Anwendungsidentität, eine verwaltete Identität für den Arbeitsbereich oder einen SAK-Schlüssel angeben. 
- Im serverlosen SQL-Pool können datenbankweit gültige Anmeldeinformationen die Azure AD-Identität des Aufrufers, eine verwaltete Identität für den Arbeitsbereich oder einen SAS-Schlüssel angeben 

#### <a name="type"></a>TYPE
„TYPE = `HADOOP`„ ist eine obligatorische Option im dedizierten SQL-Pool und gibt an, dass die PolyBase-Technologie für den Zugriff auf zugrunde liegende Dateien verwendet wird. Dieser Parameter kann nicht im serverlosen SQL-Pool verwendet werden, der einen integrierten nativen Reader verwendet.

### <a name="example-for-create-external-data-source"></a>Beispiel für „CREATE EXTERNAL DATA SOURCE“

#### <a name="dedicated-sql-pool"></a>[Dedizierter SQL-Pool](#tab/sql-pool)

Im folgenden Beispiel wird eine externe Datenquelle für Azure Data Lake Gen2 erstellt, die auf das Dataset für New York verweist:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[Serverloser SQL-Pool](#tab/sql-on-demand)

Im folgenden Beispiel wird eine externe Datenquelle für Azure Data Lake Gen2 erstellt, auf die mithilfe von SAS-Anmeldeinformationen zugegriffen werden kann:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

Im folgenden Beispiel wird eine externe Datenquelle für Azure Data Lake Gen2 erstellt, die auf das öffentlich verfügbare Dataset für New York verweist:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Erstellt ein Objekt für ein externes Dateiformat, das externe, in Azure Blob Storage oder Azure Data Lake Storage gespeicherte Daten definiert. Das Erstellen eines externen Dateiformats ist eine Voraussetzung für die Erstellung einer externen Tabelle. Die vollständige Dokumentation finden Sie [hier](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Durch Erstellen eines externen Dateiformats wird das tatsächliche Layout der Daten angegeben, auf die von einer externen Tabelle verwiesen wird.

### <a name="syntax-for-create-external-file-format"></a>Syntax für „CREATE EXTERNAL FILE FORMAT“

#### <a name="sql-pool"></a>[SQL-Pool](#tab/sql-pool)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

#### <a name="serverless-sql-pool"></a>[Serverloser SQL-Pool](#tab/sql-on-demand)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

---


### <a name="arguments-for-create-external-file-format"></a>Argumente für „CREATE EXTERNAL FILE FORMAT“

„file_format_name“: Dient zum Angeben eines Namens für das externe Dateiformat.

„FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT]“: Dient zum Angeben des Formats der externen Daten.

- PARQUET: Dient zum Angeben eines Parquet-Formats.
- „DELIMITEDTEXT“: Dient zum Angeben eines Textformats mit Spaltentrennzeichen (auch Feldabschlusszeichen genannt).

„FIELD_TERMINATOR = *field_terminator*“: Nur für durch Trennzeichen getrennte Textdateien relevant. Das Feldabschlusszeichen gibt mindestens ein Zeichen an, welches das Ende der einzelnen Felder (Spalten) in der durch Trennzeichen getrennten Textdatei markiert. Als Standardzeichen wird der senkrechte Strich (ꞌ|ꞌ) verwendet.

Beispiele:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter*: Dient zum Angeben des Feldabschlusszeichens für Zeichenfolgendaten in der durch Trennzeichen getrennten Textdatei. Das Zeichenfolgen-Trennzeichen umfasst mindestens ein Zeichen und ist in einfache Anführungszeichen gesetzt. Der Standardwert ist eine leere Zeichenfolge ("").

Beispiele:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int*: Dient zum Angeben der Zeilennummer, die zuerst gelesen wird, und gilt für alle Dateien. Wenn Sie diesen Wert auf „2“ festlegen, wird beim Laden der Daten in allen Dateien jeweils die erste Zeile (Kopfzeile) übersprungen. Zeilen werden basierend auf dem Vorhandensein von Zeilenabschlusszeichen (/ r/n, r, /n) übersprungen.

USE_TYPE_DEFAULT = { TRUE | **FALSE** }: Gibt an, wie fehlende Werte in durch Trennzeichen getrennten Textdateien behandelt werden sollen, wenn Daten aus der Textdatei abgerufen werden.

TRUE: Beim Abrufen von Daten aus der Textdatei werden fehlende Werte jeweils unter Verwendung des Standardwerts für den Datentyp der entsprechenden Spalte in der externen Tabellendefinition gespeichert. Ersetzen Sie einen fehlenden Wert beispielsweise durch:

- 0, wenn die Spalte als numerische Spalte definiert ist. Dezimalspalten werden nicht unterstützt und führen zu einem Fehler.
- Leere Zeichenfolge (""), wenn die Spalte eine Zeichenfolgenspalte ist.
- 1900-01-01, wenn die Spalte eine Datumsspalte ist.

FALSE: Fehlende Werte werden als NULL-Werte gespeichert. Alle NULL-Werte, die durch Verwendung des Worts NULL in der durch Trennzeichen getrennten Textdatei gespeichert werden, werden als Zeichenfolge „NULL“ importiert.

Encoding = {'UTF8' | 'UTF16'}: Vom serverlosen SQL-Pool können UTF8- und UTF16-codierte, durch Trennzeichen getrennte Textdateien gelesen werden.

DATA_COMPRESSION = *data_compression_method*: Dieses Argument dient zum Angeben der Datenkomprimierungsmethode für die externen Daten. 

Der PARQUET-Dateiformattyp unterstützt folgende Komprimierungsmethoden:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Beim Lesen aus externen PARQUET-Tabellen wird dieses Argument ignoriert, beim Schreiben in externe Tabellen mithilfe von [CETAS](develop-tables-cetas.md) aber verwendet.

Der Dateiformattyp DELIMITEDTEXT unterstützt die folgende Komprimierungsmethode:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = 'parser_version' Gibt die beim Lesen von Dateien zu verwendende Parserversion an. Überprüfen Sie das Argument „PARSER_VERSION“ in [OPENROWSET-Argumente](develop-openrowset.md#arguments), um weitere Details zu erhalten.

### <a name="example-for-create-external-file-format"></a>Beispiel für „CREATE EXTERNAL FILE FORMAT“

Im folgenden Beispiel wird ein externes Dateiformat für Zensusdateien erstellt:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

Der Befehl „CREATE EXTERNAL TABLE“ erstellt eine externe Tabelle für Synapse SQL, um den Zugriff auf Daten zu ermöglichen, die in Azure Blob Storage oder Azure Data Lake Storage gespeichert sind. 

### <a name="syntax-for-create-external-table"></a>Syntax für „CREATE EXTERNAL TABLE“

```sql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

### <a name="arguments-create-external-table"></a>Argumente für „CREATE EXTERNAL TABLE“

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Ein- bis dreiteiliger Name der Tabelle, die erstellt werden soll. Bei einer externen Tabelle speichert der serverlose SQL-Pool nur die Tabellenmetadaten. Im serverlosen SQL-Pool werden keine tatsächlichen Daten verschoben oder gespeichert.

<column_definition>, ...*n* ]

„CREATE EXTERNAL TABLE“ unterstützt das Konfigurieren von Spaltenname, Datentyp, NULL-Zulässigkeit und Sortierung. Sie können DEFAULT CONSTRAINT nicht für externe Tabellen verwenden.

>[!IMPORTANT]
>Die Spaltendefinitionen, einschließlich der Datentypen und der Anzahl der Spalten, müssen mit den Daten in den externen Dateien übereinstimmen. Wenn ein Konflikt besteht, werden die Zeilen der Datei beim Abfragen der tatsächlichen Daten zurückgewiesen.

Beim Lesen aus Parquet-Dateien können Sie die zu lesenden Spalten angeben und die übrigen Spalten überspringen.

LOCATION = '*folder_or_filepath*'

Dient zum Angeben des Ordners oder des Dateipfads und Dateinamens für die tatsächlichen Daten in Azure Blob Storage. Der Speicherort beginnt im Stammordner. Der Stammordner ist der in der externen Datenquelle angegebene Datenspeicherort.

Wenn Sie mithilfe von „LOCATION“ einen Ordnerspeicherort angeben, wird bei einer Abfrage des serverlosen SQL-Pools eine Auswahl aus der Tabelle getroffen, und es werden Dateien aus dem Ordner abgerufen.

> [!NOTE]
> Im Gegensatz zu Hadoop und PolyBase werden vom serverlosen SQL-Pool keine Unterordner zurückgegeben. Es werden Dateien zurückgegeben, deren Dateiname mit einem Unterstrich (_) oder Punkt (.) beginnt.

In diesem Beispiel werden von einer Abfrage des serverlosen SQL-Pools Zeilen aus „mydata.txt“ und „_hidden.txt“ zurückgegeben, wenn „LOCATION='/webdata/'“ angegeben wird. „mydata2.txt“ und „mydata3.txt“ werden nicht zurückgegeben, da sie sich in einem Unterordner befinden.

![Rekursive Daten für externe Tabellen](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name*: Dient zum Angeben des Namens der externen Datenquelle, die den Speicherort der externen Daten enthält. Verwenden Sie zum Erstellen einer externen Datenquelle [CREATE EXTERNAL DATA SOURCE](#create-external-data-source).

FILE_FORMAT = *external_file_format_name*: Dient zum Angeben des Namens des Objekts für das externe Dateiformat, das den Dateityp und die Komprimierungsmethode für die externen Daten enthält. Verwenden Sie zum Erstellen eines externen Dateiformats [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Berechtigungen für „CREATE EXTERNAL TABLE“

Sie benötigen geeignete Anmeldeinformationen mit Berechtigungen zum Auflisten und Lesen, um etwas in einer externen Tabelle auswählen zu können.

### <a name="example-create-external-table"></a>Beispiel für „CREATE EXTERNAL TABLE“

Im folgenden Beispiel wird eine externe Tabelle erstellt und die erste Zeile zurückgegeben:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Erstellen und Abfragen externer Tabellen auf der Grundlage einer Datei in Azure Data Lake

Mithilfe von Data Lake-Erkundungsfunktionen können Sie nun mit einem einfachen Rechtsklick auf die Datei eine externe Tabelle unter Verwendung eines dedizierten oder eines serverlosen SQL-Pools erstellen und abfragen.

### <a name="prerequisites"></a>Voraussetzungen

- Für den Zugriff auf den Arbeitsbereich müssen Sie mindestens über die ARM-Zugriffsrolle „Mitwirkender an Storage-Blobdaten“ für das ADLS Gen2-Konto verfügen.

- Sie müssen mindestens über [Berechtigungen zum Erstellen](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2&preserve-view=true) und Abfragen externer Tabellen im SQL-Pool oder in SQL On-Demand verfügen.

Wählen Sie im Datenbereich die Datei aus, auf deren Grundlage Sie die externe Tabelle erstellen möchten:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Daraufhin wird ein Dialogfenster geöffnet. Wählen Sie die Option für den dedizierten oder den serverlosen SQL-Pool aus, geben Sie einen Namen für die Tabelle ein, und wählen Sie „Skript öffnen“ aus:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

Das SQL-Skript wird automatisch generiert, und das Schema wird aus der Datei abgeleitet:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Führen Sie das Skript aus. Durch das Skript wird automatisch eine Abfrage vom Typ „Top 100 auswählen“ ausgeführt:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Die externe Tabelle ist nun erstellt. Zur späteren Erkundung ihres Inhalts kann der Benutzer sie direkt über den Datenbereich abfragen:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Nächste Schritte

Im [Artikel zu CETAS](develop-tables-cetas.md) erfahren Sie, wie Sie die Abfrageergebnisse in einer externen Tabelle in Azure Storage speichern. Oder Sie können damit beginnen, [Apache Spark für externe Azure Synapse-Tabellen](develop-storage-files-spark-tables.md) abzufragen.
