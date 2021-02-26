---
title: Verwenden von „OPENROWSET“ im serverlosen SQL-Pool
description: In diesem Artikel wird die Syntax von „OPENROWSET“ im serverlosen SQL-Pool beschrieben und die Verwendung von Argumenten erläutert.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9fd10d6a4fb748a61b5e1d9e27777c2fa1134039
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225612"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Verwenden von „OPENROWSET“ mit einem serverlosen SQL-Pool in Azure Synapse Analytics

Die `OPENROWSET(BULK...)`-Funktion ermöglicht den Zugriff auf Dateien in Azure Storage. Die `OPENROWSET`-Funktion liest den Inhalt einer Remotedatenquelle (z. B. einer Datei) und gibt den Inhalt als eine Reihe von Zeilen zurück. Innerhalb des serverlosen SQL-Pools wird die OPENROWSET-Funktion aufgerufen und die BULK-Option angegeben, um auf den OPENROWSET-Massenrowsetanbieter zuzugreifen.  

Auf die `OPENROWSET`-Funktion kann in der `FROM`-Klausel einer Abfrage so verwiesen werden, als handele es sich um einen Tabellennamen vom Typ `OPENROWSET`. Sie unterstützt Massenvorgänge über einen integrierten BULK-Anbieter, mit dem Daten aus einer Datei gelesen und als Rowset zurückgegeben werden können.

## <a name="data-source"></a>Datenquelle

Die OPENROWSET-Funktion in Synapse SQL liest den Inhalt der Datei(en) aus einer Datenquelle. Die Datenquelle ist ein Azure-Speicherkonto, auf das in der `OPENROWSET`-Funktion explizit verwiesen werden oder das dynamisch aus der URL der zu lesenden Dateien abgeleitet werden kann.
Die `OPENROWSET`-Funktion kann optional einen `DATA_SOURCE`-Parameter enthalten, um die Datenquelle anzugeben, die Dateien enthält.
- Mit `OPENROWSET` ohne `DATA_SOURCE` kann der Inhalt der Dateien direkt aus dem URL-Speicherort gelesen werden, der als `BULK`-Option angegeben ist:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

Dies ist eine schnelle und einfache Möglichkeit, den Inhalt der Dateien ohne Vorkonfiguration zu lesen. Mit dieser Option können Sie die Standardauthentifizierungsoption zum Zugreifen auf den Speicher verwenden (Azure AD-Pass-Through für Azure AD-Anmeldungen und SAS-Token für SQL-Anmeldungen). 

- Mit `OPENROWSET` mit `DATA_SOURCE` kann auf Dateien im angegebenen Speicherkonto zugegriffen werden:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    Mit dieser Option können Sie den Speicherort des Speicherkontos in der Datenquelle konfigurieren und die Authentifizierungsmethode angeben, die für den Speicherzugriff verwendet werden soll. 
    
    > [!IMPORTANT]
    > `OPENROWSET` ohne `DATA_SOURCE` bietet eine schnelle und einfache Möglichkeit, auf die Speicherdateien zuzugreifen, weist jedoch eingeschränkte Authentifizierungsoptionen auf. Beispielsweise können Azure AD-Prinzipale nur auf Dateien über deren [Azure AD-Identität](develop-storage-files-storage-access-control.md?tabs=user-identity) oder öffentlich verfügbare Dateien zugreifen. Wenn Sie leistungsfähigere Authentifizierungsoptionen benötigen, verwenden Sie die `DATA_SOURCE`-Option, und definieren Sie die Anmeldeinformation, die Sie für den Speicherzugriff verwenden möchten.


## <a name="security"></a>Sicherheit

Ein Datenbankbenutzer muss über die Berechtigung `ADMINISTER BULK OPERATIONS` verfügen, um die `OPENROWSET`-Funktion zu verwenden.

Der Speicheradministrator muss einem Benutzer außerdem den Zugriff auf die Dateien ermöglichen, indem er ein gültiges SAS-Token bereitstellt oder dem Azure AD-Prinzipal den Zugriff auf Speicherdateien ermöglicht. Weitere Informationen zur Speicherzugriffssteuerung finden Sie in [diesem Artikel](develop-storage-files-storage-access-control.md).

`OPENROWSET` verwendet die folgenden Regeln, um zu bestimmen, wie die Authentifizierung beim Speicher erfolgt:
- Bei `OPENROWSET` ohne `DATA_SOURCE` ist der Authentifizierungsmechanismus abhängig vom Aufrufertyp.
  - Jeder Benutzer kann `OPENROWSET` ohne `DATA_SOURCE` zum Lesen öffentlich verfügbarer Dateien im Azure-Speicher verwenden.
  - Azure AD-Anmeldungen können über die eigene [Azure AD-Identität](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) auf geschützte Dateien zugreifen, wenn der Azure-Speicher dem Azure AD-Benutzer den Zugriff auf zugrunde liegende Dateien erlaubt (z. B. wenn der Aufrufer über die Berechtigung `Storage Reader` für den Speicher verfügt).
  - SQL-Anmeldungen können `OPENROWSET` auch ohne `DATA_SOURCE` verwenden, um auf öffentlich verfügbare Dateien, Dateien, die mit einem SAS-Token geschützt sind, oder eine verwaltete Identität des Synapse-Arbeitsbereichs zuzugreifen. Sie müssen eine [serverbezogene Anmeldeinformation erstellen](develop-storage-files-storage-access-control.md#examples), um den Zugriff auf Speicherdateien zuzulassen. 
- Bei `OPENROWSET` mit `DATA_SOURCE` ist der Authentifizierungsmechanismus in der datenbankbezogenen Anmeldeinformation definiert, die der referenzierten Datenquelle zugewiesen wurde. Mit dieser Option können Sie auf öffentlich verfügbaren Speicher zugreifen oder über ein SAS-Token, die verwaltete Identität des Arbeitsbereichs oder die [Azure AD-Identität des Aufrufers](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (wenn der Aufrufer der Azure AD-Prinzipal ist) auf den Speicher zugreifen. Wenn `DATA_SOURCE` auf nicht öffentlichen Azure-Speicher verweist, müssen Sie eine [datenbankbezogene Anmeldeinformation erstellen](develop-storage-files-storage-access-control.md#examples) und in `DATA SOURCE` darauf verweisen, um den Zugriff auf Speicherdateien zu erlauben.

Der Aufrufer muss über die Berechtigung `REFERENCES` für die Anmeldeinformation verfügen, um sie für die Authentifizierung beim Speicher zu verwenden.

## <a name="syntax"></a>Syntax

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
```

## <a name="arguments"></a>Argumente

Bei den Eingabedateien mit den Zieldaten für die Abfrage stehen zwei Optionen zur Auswahl. Gültige Werte sind:

- 'CSV': Eine beliebige, durch Trennzeichen getrennte Textdatei mit Zeilen-/Spaltentrennzeichen. Als Feldtrennzeichen kann ein beliebiges Zeichen verwendet werden. Für durch Tabstopp getrennte Werte wäre dies beispielsweise: FIELDTERMINATOR = tab.

- 'PARQUET': Binärdatei im Parquet-Format. 

**'unstructured_data_path'**

Bei dem Pfad für unstrukturierte Daten kann es sich um einen absoluten oder relativen Pfad handeln:
- Der absolute Pfad im Format \<prefix>://\<storage_account_path>/\<storage_path> ermöglicht einem Benutzer, die Dateien direkt zu lesen.
- Der relative Pfad im Format '<Speicherpfad>' muss mit dem Parameter `DATA_SOURCE` verwendet werden und beschreibt das Dateimuster innerhalb des Speicherorts <Speicherkontopfad>, der in `EXTERNAL DATA SOURCE` definiert ist. 

Nachfolgend sind die relevanten <storage account path>-Werte für die Verknüpfung mit Ihrer speziellen externen Datenquelle angegeben. 

| Externe Datenquelle       | Präfix | Speicherkontopfad                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net/path/file   |
| Azure Data Lake Store Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

Dient zum Angeben eines Pfads innerhalb Ihres Speichers, der auf den zu lesenden Ordner oder auf die zu lesende Datei verweist. Verweist der Pfad auf einen Container oder Ordner, werden alle Dateien aus diesem Container oder Ordner gelesen. Dateien in Unterordnern werden nicht einbezogen. 

Sie können Platzhalter angeben, um mehrere Dateien oder Ordner zu verwenden. Dabei können mehrere nicht aufeinander folgende Platzhalter verwendet werden.
Im folgenden Beispiel werden alle mit *population* beginnenden *CSV-Dateien* aus allen mit */csv/population* beginnenden Ordnern gelesen:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Wenn Sie den Pfad für unstrukturierte Daten (unstructured_data_path) als Ordner angeben, werden bei einer Abfrage des serverlosen SQL-Pools Dateien aus diesem Ordner abgerufen. 

Durch Angabe von „/**“ am Ende des Pfads können Sie den serverlosen SQL-Pool anweisen, Ordner zu durchlaufen. Beispiel: `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Im Gegensatz zu Hadoop und PolyBase werden vom serverlosen SQL-Pool nur dann Unterordner zurückgegeben, wenn Sie „/**“ am Ende des Pfads angeben. Außerdem werden vom serverlosen SQL-Pool anders als bei Hadoop und PolyBase keine Dateien zurückgegeben, deren Dateiname mit einem Unterstrich (_) oder einem Punkt (.) beginnt.

Das folgende Beispiel zeigt: Bei Verwendung von „unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`“ werden von einer Abfrage des serverlosen SQL-Pools Zeilen aus „mydata.txt“ und „_hidden.txt“ zurückgegeben. „mydata2.txt“ und „mydata3.txt“ werden nicht zurückgegeben, da sie sich in einem Unterordner befinden.

![Rekursive Daten für externe Tabellen](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

Mit der WITH-Klausel können Sie Spalten angeben, die aus Dateien gelesen werden sollen.

- Geben Sie bei CSV-Datendateien Spaltennamen und den jeweiligen Datentyp an, um alle Spalten zu lesen. Wenn Sie sich nur für eine Teilmenge der Spalten interessieren, verwenden Sie Ordinalzahlen, um die Spalten aus den Ursprungsdatendateien anhand der Ordinalzahl auszuwählen. Spalten werden auf der Grundlage der Ordinalangabe gebunden. Bei Verwendung von „HEADER_ROW = TRUE“ erfolgt die Spaltenbindung nach Spaltenname und nicht nach Ordinalposition.
    > [!TIP]
    > Die WITH-Klausel kann für CSV-Dateien auch weggelassen werden. Datentypen werden automatisch aus Dateiinhalten abgeleitet. Mit dem Argument „HEADER_ROW“ können Sie angeben, dass eine Kopfzeile vorhanden ist. In diesem Fall werden Spaltennamen aus der Kopfzeile gelesen. Ausführliche Informationen finden Sie unter [Automatische Schemaerkennung](#automatic-schema-discovery).
    
- Geben Sie bei Parquet-Datendateien Spaltennamen an, die den Spaltennamen in den Ursprungsdatendateien entsprechen. Die Spalten werden nach Name gebunden, und es wird die Groß-/Kleinschreibung beachtet. Ohne Angabe der WITH-Klausel werden alle Spalten aus Parquet-Dateien zurückgegeben.
    > [!IMPORTANT]
    > Bei Spaltennamen in Parquet-Dateien wird die Groß-/Kleinschreibung beachtet. Wenn Sie einen Spaltennamen angeben, dessen Groß-/Kleinschreibung sich von der Schreibweise in der Parquet-Datei unterscheidet, werden für diese Spalte NULL-Werte zurückgegeben.


column_name: Der Name für die Ausgabespalte. Wird der Name angegeben, überschreibt er den Spaltennamen in der Quelldatei und den Spaltennamen im JSON-Pfad, sofern vorhanden. Wird „json_path“ nicht angegeben, wird automatisch „$.column_name“ hinzugefügt. Überprüfen Sie das Argument „json_path“ auf das Verhalten.

column_type: Der Datentyp für die Ausgabespalte. Hier findet die implizite Datentypkonvertierung statt.

column_ordinal: Die Ordinalzahl der Spalte in den Quelldateien. Dieses Argument wird bei Parquet-Dateien ignoriert, da die Bindung auf dem Namen basiert. Im folgenden Beispiel wird nur bei Verwendung einer CSV-Datei eine zweite Spalte zurückgegeben:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path: Der [JSON-Pfadausdruck](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) zur Spalte oder geschachtelten Eigenschaft. Als [PATH-Modus](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE) ist standardmäßig der Lax-Modus ausgewählt.

> [!NOTE]
> Im Strict-Modus tritt bei der Abfrage ein Fehler auf, wenn der angegebene Pfad nicht vorhanden ist. Im Lax-Modus wird die Abfrage erfolgreich ausgeführt, und der JSON-Pfadausdruck wird zu NULL ausgewertet.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Dient zum Angeben des zu verwendenden Feldabschlusszeichens. Das Standard-Feldabschlusszeichen ist ein Komma ( **,** ).

ROWTERMINATOR ='row_terminator'`

Dient zum Angeben des zu verwendenden Zeilenabschlusszeichens. Wenn kein Zeilenabschlusszeichen angegeben wird, wird eines der Standardabschlusszeichen verwendet. Standardabschlusszeichen für „PARSER_VERSION = '1.0'“ sind „\r\n“, „\n“ und „\r“. Standardabschlusszeichen für „PARSER_VERSION = '2.0'“ sind „\r\n“ und „\n“.

ESCAPE_CHAR = 'char'

Dient zum Angeben des Zeichens in der Datei, das als Escapezeichen für sich selbst und für alle Trennzeichenwerte in der Datei fungiert. Wenn auf das Escapezeichen ein Wert folgt, bei dem es sich nicht um das Escapezeichen selbst oder um einen der Trennzeichenwerte handelt, wird das Escapezeichen beim Lesen des Werts gelöscht. 

Der ESCAPE_CHAR-Parameter wird unabhängig davon angewendet, ob FIELDQUOTE aktiviert ist. Er fungiert nicht als Escapezeichen für das Zitierzeichen. Das Anführungszeichen muss mit einem weiteren Anführungszeichen als Escapezeichen versehen werden. Anführungszeichen können nur in Spaltenwerten enthalten sein, wenn der Wert mit Anführungszeichen gekapselt ist.

FIRSTROW = 'first_row' 

Gibt die Nummer der ersten zu ladenden Zeile an. Der Standardwert ist 1 und kennzeichnet die erste Zeile in der angegebenen Datendatei. Die Zeilennummern werden durch Zählen der Zeilenabschlusszeichen bestimmt. FIRSTROW ist einsbasiert.

FIELDQUOTE = 'field_quote' 

Gibt ein Zeichen an, das als Anführungszeichen in der CSV-Datei verwendet wird. Ohne diese Angabe wird das Anführungszeichen (") verwendet. 

DATA_COMPRESSION = 'data_compression_method'

Gibt die Komprimierungsmethode an. Nur in PARSER_VERSION='1.0' unterstützt. Die folgende Komprimierungsmethode wird unterstützt:

- GZIP

PARSER_VERSION = 'parser_version'

Gibt die beim Lesen von Dateien zu verwendende Parserversion an. Zurzeit werden die CSV-Parserversionen 1.0 und 2.0 unterstützt:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

Die CSV-Parserversion 1.0 ist die funktionsreiche Standardversion. Version 2.0 wurde mit dem Fokus auf Leistung erstellt und unterstützt nicht alle Optionen und Codierungen. 

Einzelheiten zu CSV-Parserversion 1.0:

- Die folgenden Optionen werden nicht unterstützt: HEADER_ROW.

Einzelheiten zu CSV-Parserversion 2.0:

- Nicht alle Datentypen werden unterstützt.
- Die maximal zulässige Zeichenlänge für Spalten beträgt 8.000.
- Die maximale Zeilengröße beträgt 8 MB.
- Die folgenden Optionen werden nicht unterstützt: DATA_COMPRESSION.
- Eine leere Zeichenfolge in Anführungszeichen ("") wird als leere Zeichenfolge interpretiert.
- Unterstütztes Format für DATE-Datentyp: JJJJ-MM-TT
- Unterstütztes Format für TIME-Datentyp: HH:MM:SS[.Sekundenbruchteile]
- Unterstütztes Format für DATETIME2-Datentyp: YYYY-MM-DD HH:MM:SS[.Sekundenbruchteile]

HEADER_ROW = { TRUE | FALSE }

Gibt an, ob die CSV-Datei eine Kopfzeile enthält. Standardwert: FALSE. Unterstützt in: PARSER_VERSION='2.0'. Bei „TRUE“ werden Spaltennamen aus der ersten Zeile gelesen (gemäß FIRSTROW-Argument). Bei „TRUE“ und Schemaangabe mit „WITH“ wird für die Bindung von Spaltennamen der Spaltenname und nicht die Ordinalposition herangezogen.

DATAFILETYPE = { 'char' | 'widechar' }

Gibt die Codierung an: „char“ wird für UTF8 verwendet, „widechar“ für UTF16-Dateien.

## <a name="fast-delimited-text-parsing"></a>Schnelle Analyse von Text mit Trennzeichen

Für die Analyse von Text mit Trennzeichen stehen zwei Parserversionen zur Verfügung: Die CSV-Parserversion 1.0 ist die Standardversion und bietet zahlreiche Features. Die Parserversion 2.0 ist dagegen auf hohe Leistung ausgelegt. Die höhere Leistung der Parserversion 2.0 wird durch erweiterte Analysetechniken und Multithreading erreicht. Der Geschwindigkeitsunterschied nimmt zu, je größer die Datei ist.

## <a name="automatic-schema-discovery"></a>Automatische Schemaerkennung

Sie können mühelos CSV- und Parquet-Dateien abfragen, ohne das Schema zu kennen oder anzugeben, indem Sie die WITH-Klausel weglassen. Spaltennamen und Datentypen werden aus Dateien abgeleitet.

Parquet-Dateien enthalten Spaltenmetadaten, die gelesen werden. Typzuordnungen finden Sie unter [Typzuordnung für Parquet](#type-mapping-for-parquet). Beispiele finden Sie unter [Lesen von Parquet-Dateien ohne Angabe eines Schemas](#read-parquet-files-without-specifying-schema).

Bei CSV-Dateien können Spaltennamen aus der Kopfzeile gelesen werden. Mithilfe des Arguments „HEADER_ROW“ können Sie angeben, ob eine Kopfzeile vorhanden ist. Bei „HEADER_ROW = FALSE“ werden generische Spaltennamen verwendet: C1, C2, ... Cn, wobei „n“ die Anzahl von Spalten in der Datei ist. Datentypen werden aus den ersten 100 Datenzeilen abgeleitet. Beispiele finden Sie unter [Lesen von CSV-Dateien ohne Angabe eines Schemas](#read-csv-files-without-specifying-schema).

> [!IMPORTANT]
> Es kann vorkommen, dass der passende Datentyp aufgrund fehlender Informationen nicht abgeleitet werden kann und stattdessen ein größerer Datentyp verwendet wird. Dies führt zu Mehraufwand und ist insbesondere für Zeichenspalten relevant, die als „varchar(8000)“ abgeleitet werden. Um eine optimale Leistung zu erzielen, [überprüfen Sie die abgeleiteten Datentypen](best-practices-sql-on-demand.md#check-inferred-data-types), und [verwenden Sie passende Datentypen](best-practices-sql-on-demand.md#use-appropriate-data-types).

### <a name="type-mapping-for-parquet"></a>Typzuordnung für Parquet

Parquet-Dateien enthalten Typbeschreibungen für die einzelnen Spalten. In der folgenden Tabelle wird beschrieben, wie Parquet-Typen den nativen SQL-Typen zugeordnet werden.

| Parquet-Typ | Logischer Parquet-Typ (Anmerkung) | SQL-Datentyp |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| Double | | float |
| GLEITKOMMAZAHL | | real |
| INT32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |BINARY |
| BINARY |UTF8 |varchar \*(UTF8-Sortierung) |
| BINARY |STRING |varchar \*(UTF8-Sortierung) |
| BINARY |ENUM|varchar \*(UTF8-Sortierung) |
| FIXED_LEN_BYTE_ARRAY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |Decimal |
| BINARY |JSON |varchar(8000) \*(UTF8-Sortierung) |
| BINARY |BSON | Nicht unterstützt |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |Decimal |
| BYTE_ARRAY |INTERVAL | Nicht unterstützt |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |BIGINT |
| INT32 |DATE |date |
| INT32 |DECIMAL |Decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |BIGINT |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |Decimal |
| INT64 |TIME (MICROS) |time – TIME(NANOS) wird nicht unterstützt. |
|INT64 |TIMESTAMP (MILLIS / MICROS) |datetime2 – TIMESTAMP(NANOS) wird nicht unterstützt. |
|[Komplexer Typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |AUFLISTEN |varchar(8000), serialisiert in JSON |
|[Komplexer Typ](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(8000), serialisiert in JSON |

## <a name="examples"></a>Beispiele

### <a name="read-csv-files-without-specifying-schema"></a>Lesen von CSV-Dateien ohne Angabe eines Schemas

Im folgenden Beispiel wird eine CSV-Datei mit einer Kopfzeile gelesen, ohne Spaltennamen und Datentypen anzugeben: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

Im folgenden Beispiel wird eine CSV-Datei ohne Kopfzeile gelesen, ohne Spaltennamen und Datentypen anzugeben: 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>Lesen von Parquet-Dateien ohne Angabe eines Schemas

Im folgenden Beispiel werden alle Spalten der ersten Zeile aus dem Zensus-Dataset im Parquet-Format zurückgegeben, ohne dass die Spaltennamen und Datentypen angegeben werden: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>Lesen bestimmter Spalten aus einer CSV-Datei

Im folgenden Beispiel werden nur zwei Spalten mit den Ordinalzahlen 1 und 4 aus den Dateien „population*.csv“ zurückgegeben. Da in den Dateien keine Kopfzeile vorhanden ist, beginnt der Lesevorgang in der ersten Zeile:

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Lesen bestimmter Spalten aus einer Parquet-Datei

Im folgenden Beispiel werden nur zwei Spalten der ersten Zeile aus dem Zensus-Dataset im Parquet-Format zurückgegeben: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>Angeben von Spalten mithilfe von JSON-Pfaden

Im folgenden Beispiel wird gezeigt, wie Sie [JSON-Pfadausdrücke](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) in der WITH-Klausel verwenden. Darüber hinaus wird der Unterschied zwischen den PATH-Modi „Strict“ und „Lax“ veranschaulicht: 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele finden Sie im [Schnellstart zum Abfragen von Daten im Speicher](query-data-storage.md). Dort erfahren Sie, wie Sie `OPENROWSET` zum Lesen von [CSV](query-single-csv-file.md)-, [PARQUET](query-parquet-files.md)- und [JSON](query-json-files.md)-Dateiformaten verwenden. Machen Sie sich mit den [bewährten Methoden](best-practices-sql-on-demand.md) vertraut, um eine optimale Leistung zu erzielen. Sie erfahren außerdem, wie Sie die Ergebnisse Ihrer Abfrage mithilfe von [CETAS](develop-tables-cetas.md) in Azure Storage speichern.
