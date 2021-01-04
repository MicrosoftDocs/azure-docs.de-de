---
title: Abfragen von Datenspeicher mit einem serverlosen SQL-Pool
description: In diesem Artikel wird beschrieben, wie Sie Azure-Speicher mit der Ressource für einen serverlosen SQL-Pool in Azure Synapse Analytics abfragen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 967250cf29d1f0248f296cb545a764bd8e611773
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462652"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Abfragen von Speicherdateien mit einem serverlosen SQL-Pool in Azure Synapse Analytics

Ein serverloser SQL-Pool ermöglicht Ihnen das Abfragen von Daten in Ihrem Data Lake. Dieses Feature bietet eine T-SQL-Abfrageoberfläche, die semistrukturierte und unstrukturierte Datenabfragen verarbeiten kann. Für Abfragen werden die folgenden T-SQL-Aspekte unterstützt:

- Vollständige [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-Oberfläche, einschließlich der meisten [SQL-Funktionen und -Operatoren](overview-features.md).
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) erstellt eine [externe Tabelle](develop-tables-external-tables.md) und exportiert parallel die Ergebnisse einer Transact-SQL-SELECT-Anweisung in Azure Storage.

Weitere Informationen zu unterstützten und nicht unterstützten Aspekten finden Sie im Artikel [Übersicht über serverlose SQL-Pools](on-demand-workspace-overview.md) oder in den Artikeln zu folgenden Themen:
- [Entwickeln des Speicherzugriffs](develop-storage-files-overview.md) mit Informationen dazu, wie Sie eine [externe Tabelle](develop-tables-external-tables.md) und die Funktion [OPENROWSET](develop-openrowset.md) verwenden, um Daten aus Speicher zu lesen.
- [Steuern des Speicherzugriffs](develop-storage-files-storage-access-control.md) mit Informationen dazu, wie Sie für Synapse SQL den Zugriff auf Speicher per SAS-Authentifizierung oder per verwalteter Identität des Arbeitsbereichs ermöglichen.

## <a name="overview"></a>Übersicht

Um reibungslose Vorgänge beim Abfragen von Daten in Azure Storage-Dateien zu gewährleisten, verwendet ein serverloser SQL-Pool die Funktion [OPENROWSET](develop-openrowset.md) mit zusätzlichen Funktionen:

- [Abfragen mehrerer Dateien oder Ordner](#query-multiple-files-or-folders)
- [Parquet-Dateiformat](#query-parquet-files)
- [Abfragen von CSV-Dateien und Text mit Trennzeichen (Feldabschlusszeichen, Zeilenabschlusszeichen, Escapezeichen)](#query-csv-files)
- [Lesen einer ausgewählten Teilmenge von Spalten](#read-a-chosen-subset-of-columns)
- [Schemarückschluss](#schema-inference)
- [filename-Funktion](#filename-function)
- [filepath-Funktion](#filepath-function)
- [Verwenden von komplexen Typen und geschachtelten oder wiederholten Datenstrukturen](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Abfragen von PARQUET-Dateien

Um Parquet-Quelldaten abzufragen, verwenden Sie FORMAT = 'PARQUET'.

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Beispiele zur Verwendung finden Sie im Artikel [Abfragen von Parquet-Dateien](query-parquet-files.md).

## <a name="query-csv-files"></a>Abfragen von CSV-Dateien

Um CSV-Quelldaten abzufragen, verwenden Sie FORMAT = 'CSV'. Sie können das Schema der CSV-Datei als Teil der Funktion `OPENROWSET` angeben, wenn Sie CSV-Dateien abfragen:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Es gibt noch einige zusätzliche Optionen, die Sie verwenden können, um Analyseregeln an das benutzerdefinierte CSV-Format anzupassen:
- ESCAPE_CHAR = 'char': Dient zum Angeben des Zeichens in der Datei, das als Escapezeichen für sich selbst und für alle Trennzeichenwerte in der Datei fungiert. Wenn auf das Escapezeichen ein Wert folgt, bei dem es sich nicht um das Escapezeichen selbst oder um einen der Trennzeichenwerte handelt, wird das Escapezeichen beim Lesen des Werts gelöscht.
Der Parameter „ESCAPE_CHAR“ wird unabhängig davon angewendet, ob FIELDQUOTE aktiviert ist. Er fungiert nicht als Escapezeichen für das Zitierzeichen. Das Anführungszeichen muss mit einem weiteren Anführungszeichen als Escapezeichen versehen werden. Anführungszeichen können nur in Spaltenwerten enthalten sein, wenn der Wert mit Anführungszeichen gekapselt ist.
- FIELDTERMINATOR ='field_terminator': Dient zum Angeben des zu verwendenden Feldabschlusszeichens. Das Standard-Feldabschlusszeichen ist das Komma ( **,** ).
- ROWTERMINATOR ='row_terminator': Dient zum Angeben des zu verwendenden Zeilenabschlusszeichens. Das Standard-Zeilenabschlusszeichen ist das Zeilenvorschubzeichen:  **\r\n**.

## <a name="file-schema"></a>Dateischema

Mit der Sprache „SQL“ in Synapse SQL können Sie das Schema der Datei als Teil der Funktion `OPENROWSET` definieren und alle oder einige Spalten lesen. Sie können damit auch versuchen, für die Datei per Schemarückschluss automatisch Spaltentypen zu bestimmen.

### <a name="read-a-chosen-subset-of-columns"></a>Lesen einer ausgewählten Teilmenge von Spalten

Um zu lesende Spalten anzugeben, können Sie eine optionale WITH-Klausel in Ihre `OPENROWSET`-Anweisung einfügen.

- Geben Sie bei CSV-Datendateien Spaltennamen und den jeweiligen Datentyp an, um alle Spalten zu lesen. Wenn Sie nur eine Teilmenge von Spalten abrufen möchten, verwenden Sie Ordnungszahlen, um die Spalten anhand ihrer Ordnungszahl aus den Ursprungsdatendateien auszuwählen. Spalten werden anhand der Ordnungszahl gebunden.
- Wenn es sich um Parquet-Datendateien handelt, geben Sie Spaltennamen an, die den Spaltennamen in den Ursprungsdatendateien entsprechen. Spalten werden anhand des Namens gebunden.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

Für jede Spalte müssen Sie den Spaltennamen und -typ in der `WITH`-Klausel angeben.
Beispiele finden Sie unter [Lesen von CSV-Dateien ohne Angabe aller Spalten](query-single-csv-file.md#return-a-subset-of-columns).

## <a name="schema-inference"></a>Schemarückschluss

Indem Sie die WITH-Klausel in der `OPENROWSET`-Anweisung weglassen, können Sie den Dienst anweisen, das Schema anhand der zugrunde liegenden Dateien per Rückschluss automatisch zu erkennen.

> [!NOTE]
> Dies funktioniert derzeit nur für das Parquet-Dateiformat.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Verwenden Sie unbedingt [geeignete abgeleitete Datentypen](best-practices-sql-on-demand.md#check-inferred-data-types), um eine optimale Leistung zu erzielen. 

## <a name="query-multiple-files-or-folders"></a>Abfragen mehrerer Dateien oder Ordner

Um eine T-SQL-Abfrage für eine Gruppe von Dateien innerhalb eines Ordners oder für eine Ordnergruppe auszuführen und diese dabei als einzelne Entität oder einzelnes Rowset zu behandeln, geben Sie einen Pfad oder ein Muster für eine Datei- oder Ordnergruppe an. Verwenden Sie dabei Platzhalterzeichen.

Es gelten die folgenden Regeln:

- Muster können in einem Teil eines Verzeichnispfads oder in einem Dateinamen verwendet werden.
- Im gleichen Verzeichnisschritt oder Dateinamen können mehrere Muster verwendet werden.
- Wenn mehrere Platzhalterzeichen vorhanden sind, werden Dateien in allen übereinstimmenden Pfaden in den resultierenden Dateisatz eingeschlossen.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Beispiele zur Verwendung finden Sie unter [Abfragen von Ordnern und mehreren Dateien](query-folders-multiple-csv-files.md).

## <a name="file-metadata-functions"></a>Datei-Metadatenfunktionen

### <a name="filename-function"></a>filename-Funktion

Diese Funktion gibt den Namen der Datei zurück, aus der die Zeile stammt. 

Informationen zum Abfragen bestimmter Dateien finden Sie in Abschnitt zu Dateinamen im Artikel [Abfragen bestimmter Dateien](query-specific-files.md#filename).

Der Rückgabedatentyp ist „nvarchar(1024)“. Um optimale Leistung zu erzielen, wandeln Sie das Ergebnis der filename-Funktion immer in den entsprechenden Datentyp um. Achten Sie bei Verwendung des Zeichendatentyps unbedingt auf die richtige Länge.

### <a name="filepath-function"></a>filepath-Funktion

Diese Funktion gibt eine vollständigen Pfad oder eine Teil eines Pfads zurück:

- Beim Aufruf ohne Parameter wird der vollständige Pfad einer Datei zurückgegeben, aus der eine Zeile stammt.
- Beim Aufruf mit Parameter wird ein Teil eines Pfads zurückgegeben, der dem Platzhalterzeichen an der im Parameter angegebenen Position entspricht. Der Parameterwert 1 beispielsweise würde den Teil eines Pfads zurückgeben, der dem ersten Platzhalterzeichen entspricht.

Weitere Informationen finden Sie im Abschnitt zu Dateipfaden im Artikel [Abfragen bestimmter Dateien](query-specific-files.md#filepath).

Der Rückgabedatentyp ist „nvarchar(1024)“. Um optimale Leistung zu erzielen, wandeln Sie das Ergebnis der filepath-Funktion immer in den entsprechenden Datentyp um. Achten Sie bei Verwendung des Zeichendatentyps unbedingt auf die richtige Länge.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Verwenden von komplexen Typen und geschachtelten oder wiederholten Datenstrukturen

Um eine problemlose Verwendung von Daten zu ermöglichen, die in geschachtelten oder wiederholten Datentypen (beispielsweise in [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types)-Dateien) gespeichert sind, wurden über den serverlosen SQL-Pool die unten angegebenen Erweiterungen hinzugefügt.

#### <a name="project-nested-or-repeated-data"></a>Projizieren von geschachtelten oder wiederholten Daten

Zum Projizieren von Daten führen Sie eine SELECT-Anweisung in der Parquet-Datei aus, die Spalten mit geschachtelten Datentypen enthält. In der Ausgabe werden die geschalteten Werte in das JSON-Format serialisiert und als varchar(8000)-SQL-Datentyp zurückgegeben.

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Ausführlichere Informationen finden Sie im Abschnitt zum Projizieren von geschachtelten oder wiederholten Daten im Artikel [Abfragen von geschachtelten Parquet-Typen](query-parquet-nested-types.md#project-nested-or-repeated-data).

#### <a name="access-elements-from-nested-columns"></a>Zugreifen auf Elemente aus geschachtelten Spalten

Um auf geschachtelte Elemente aus einer geschachtelten Spalte zuzugreifen – z. B. eine Struktur –, verwenden Sie die Punktnotation, um Feldnamen im Pfad zu verketten. Geben Sie den Pfad als „column_name“ in der WITH-Klausel der Funktion `OPENROWSET` an.

So sieht das Syntaxfragment aus:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Standardmäßig gleicht die Funktion `OPENROWSET` den Namen und Pfad des Quellfelds mit dem Spaltennamen ab, der in der WITH-Klausel angegeben ist. Auf Elemente, die sich auf verschiedenen Schachtelungsebenen innerhalb derselben Parquet-Quelldatei befinden, kann über die WITH-Klausel zugegriffen werden.

**Rückgabewerte**

- Diese Funktion gibt für alle Parquet-Typen, die sich nicht in der Gruppe „Geschachtelter Typ“ befinden, einen Skalarwert wie „int“, „decimal“ oder „varchar“ aus dem angegebenen Element im angegebenen Pfad zurück.
- Wenn der Pfad auf ein Element eines geschachtelten Typs zeigt, gibt die Funktion ein JSON-Fragment zurück, das im obersten Element im angegebenen Pfad beginnt. Das JSON-Fragment weist den Typ „varchar(8000)“ auf.
- Wenn die Eigenschaft im angegebenen column_name-Element nicht gefunden wird, gibt die Funktion einen Fehler zurück.
- Wenn die Eigenschaft im angegebenen column_path-Element nicht gefunden wird, gibt die Funktion je nach [Pfadmodus](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE) im Strict-Modus einen Fehler oder im Lax-Modus NULL zurück.

Beispiele für Abfragen finden Sie im Abschnitt „Zugreifen auf Elemente aus geschachtelten Spalten“ im Artikel [Abfragen von geschachtelten Parquet-Typen](query-parquet-nested-types.md#read-properties-from-nested-object-columns).

#### <a name="access-elements-from-repeated-columns"></a>Zugreifen auf Elemente aus wiederholten Spalten

Um auf Elemente aus einer wiederholten Spalte zuzugreifen – z. B. ein Element eines Arrays oder einer Zuordnung –, verwenden Sie die [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-Funktion für jedes skalare Element, das Sie projizieren und bereitstellen müssen:

- Geschachtelte oder wiederholte Spalte als erster Parameter
- Ein [JSON-Pfad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), der das Element oder die Eigenschaft angibt, auf das bzw. die zugegriffen werden soll, als zweiter Parameter

Um auf nicht skalare Elemente aus einer wiederholten Spalte zuzugreifen, verwenden Sie die [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-Funktion für jedes nicht skalare Element, das Sie projizieren und bereitstellen müssen:

- Geschachtelte oder wiederholte Spalte als erster Parameter
- Ein [JSON-Pfad](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), der das Element oder die Eigenschaft angibt, auf das bzw. die zugegriffen werden soll, als zweiter Parameter

Hier finden Sie das entsprechende Syntaxfragment:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Abfragebeispiele für den Zugriff auf Elemente aus wiederholten Spalten finden Sie im Artikel [Abfragen von geschachtelten Parquet-Typen](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="query-samples"></a>Abfragebeispiele

Sie können sich weiter über das Abfragen verschiedener Datentypen informieren, indem Sie die Beispielabfragen nutzen.

### <a name="tools"></a>Tools

Für Abfragen erforderliche Tools:
    - Azure Synapse Studio 
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Demoeinrichtung

Im ersten Schritt **erstellen Sie eine Datenbank**, in der Sie die Abfragen ausführen. Anschließend initialisieren Sie die Objekte, indem Sie das [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) für diese Datenbank ausführen. 

Mit diesem Setupskript werden die Datenquellen, die für die gesamte Datenbank gültigen Anmeldeinformationen und externe Dateiformate erstellt, die in diesen Beispielen zum Lesen von Daten verwendet werden.

> [!NOTE]
> Datenbanken werden nur zum Anzeigen von Metadaten (nicht für tatsächliche Daten) verwendet.  Notieren Sie sich den von Ihnen verwendeten Datenbanknamen. Sie benötigen ihn später.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Bereitgestellte Demodaten

Die Demodaten enthalten die folgenden Datasets:

- Fahrtenaufzeichnungen für „NYC Taxi – Yellow Taxi“ (Teil des öffentlichen NYC-Datasets im CSV- und Parquet-Format)
- Dataset mit Bevölkerungsdaten im CSV-Format
- Parquet-Beispieldateien mit geschachtelten Spalten
- Bücher im JSON-Format

| Ordnerpfad                                                  | BESCHREIBUNG                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Übergeordneter Ordner für Daten im CSV-Format                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Ordner mit Bevölkerungsdatendateien in verschiedenen CSV-Formaten |
| /csv/taxi/                                                   | Ordner mit öffentlichen NYC-Datendateien im CSV-Format              |
| /parquet/                                                    | Übergeordneter Ordner für Daten im Parquet-Format                     |
| /parquet/taxi                                                | Öffentliche NYC-Datendateien im Parquet-Format, partitioniert nach Jahr und Monat (mithilfe des Hive-/Hadoop-Partitionierungsschemas) |
| /parquet/nested/                                             | Parquet-Beispieldateien mit geschachtelten Spalten                     |
| /json/                                                       | Übergeordneter Ordner für Daten im JSON-Format                        |
| /json/books/                                                 | JSON-Dateien mit Bücherdaten                                   |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Abfragen verschiedener Dateitypen und zum Erstellen und Verwenden von Sichten finden Sie in den folgenden Artikeln:

- [Abfragen von CSV-Dateien](query-single-csv-file.md)
- [Abfragen von Parquet-Dateien](query-parquet-files.md)
- [Abfragen von JSON-Dateien](query-json-files.md)
- [Abfragen von geschachtelten Werten](query-parquet-nested-types.md)
- [Abfragen von Ordnern und mehreren CSV-Dateien](query-folders-multiple-csv-files.md)
- [Verwenden von Dateimetadaten in Abfragen](query-specific-files.md)
- [Erstellen und Verwenden von Sichten](create-use-views.md)
