---
title: Abfragen von geschachtelten Parquet-Typen mit einem serverlosen SQL-Pool
description: In diesem Artikel erfahren Sie, wie Sie geschachtelte Parquet-Typen mit einem serverlosen SQL-Pool abfragen können.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 91f612ba7f19deb739dbb6004e275ea044a5a3d3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462558"
---
# <a name="query-nested-types-in-parquet-and-json-files-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Abfragen von geschachtelten Typen in Parquet und JSON-Dateien mit einem serverlosen SQL-Pool in Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie eine Abfrage mit einem serverlosen SQL-Pool in Azure Synapse Analytics schreiben können. Die Abfrage liest geschachtelte Parquet-Typen.
Bei geschachtelten Typen handelt es sich um komplexe Strukturen zur Darstellung von Objekten oder Arrays. Geschachtelte Typen können in folgenden Formaten gespeichert werden: 
- In [Parquet](query-parquet-files.md). Dabei können mehrere komplexe Spalten mit Arrays und Objekten vorhanden sein.
- In hierarchischen [JSON-Dateien](query-json-files.md). Dabei können komplexe JSON-Dokumente als einzelne Spalte gelesen werden.
- In Azure Cosmos DB-Sammlungen (aktuell in der geschlossenen öffentlichen Vorschau verfügbar). Dabei kann jedes Dokument komplexe geschachtelte Eigenschaften enthalten.

Der serverlose SQL-Pool formatiert alle geschachtelten Typen als JSON-Objekte und -Arrays. Somit können Sie [komplexe Objekte mithilfe von JSON-Funktionen extrahieren oder ändern](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) bzw. [JSON-Daten mithilfe der OPENJSON-Funktion analysieren](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Hier folgt ein Beispiel für eine Abfrage, die Skalar- und Objektwerte aus der JSON-Datei [COVID-19 Open Research Dataset](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) extrahiert, die geschachtelte Objekte enthält: 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

Die Funktion `JSON_VALUE` gibt einen Skalarwert aus dem Feld unter dem angegebenen Pfad zurück. Die Funktion `JSON_QUERY` gibt ein als JSON formatiertes Objekt aus dem Feld unter dem angegebenen Pfad zurück.

> [!IMPORTANT]
> In diesem Beispiel wird eine Datei aus dem COVID-19 Open Research Dataset verwendet. [Die Lizenz und die Struktur der Daten finden Sie hier](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/).

## <a name="prerequisites"></a>Voraussetzungen

Der erste Schritt ist die Erstellung einer Datenbank, in der die Datenquelle erstellt wird. Anschließend initialisieren Sie die Objekte, indem Sie ein [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) für die Datenbank ausführen. Mit dem Setupskript werden die Datenquellen, die für die gesamte Datenbank gültigen Anmeldeinformationen und externe Dateiformate erstellt, die in den Beispielen verwendet werden.

## <a name="project-nested-or-repeated-data"></a>Projizieren von geschachtelten oder wiederholten Daten

Eine Parquet-Datei kann über mehrere Spalten mit komplexen Typen verfügen. Die Werte aus diesen Spalten werden als JSON-Text formatiert und als VARCHAR-Spalten zurückgegeben. Mit der folgenden Abfrage wird die Datei structExample.parquet gelesen und gezeigt, wie die Werte der geschachtelten Spalten gelesen werden sollen: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Diese Abfrage gibt folgendes Ergebnis zurück. Der Inhalt jedes geschachtelten Objekts wird als JSON-Text zurückgegeben.

| DateStruct    | TimeStruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date":"2009-04-25"}| {"Time":"20:51:54.3598000"}|    {"Timestamp":"5501-04-08 12:13:57.4821000"}|    {"Decimal":11143412.25350}| {"Float":0.5}|
|{"Date":"1916-04-29"}| {"Time":"00:16:04.6778000"}|    {"Timestamp":"1990-06-30 20:50:52.6828000"}|    {"Decimal":1963545.62800}|  {"Float":-2.125}|

Die folgende Abfrage liest die Datei justSimpleArray.parquet. Sie projiziert alle Spalten aus der Parquet-Datei einschließlich geschachtelter und wiederholter Daten.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Bei dieser Abfrage wird das folgende Ergebnis zurückgegeben:

|SimpleArray|
| --- |
|[11,12,13]|
|[21,22,23]|

## <a name="read-properties-from-nested-object-columns"></a>Lesen von Eigenschaften aus Spalten mit geschachtelten Objekten

Mit der Funktion `JSON_VALUE` können Sie Werte aus Spalten als JSON-formatierten Text zurückgeben:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

Das Ergebnis ist in der folgenden Tabelle gezeigt:

|title  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Supplementary Information An eco-epidemiolo... | Julien   | - Figure S1 : Phylogeny of... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Im Gegensatz zu JSON-Dateien, die in den meisten Fällen eine einzelne Spalte zurückgeben, die ein komplexes JSON-Objekt enthält, können Parquet-Dateien mehrere komplexe Spalten aufweisen. Sie können die Eigenschaften der geschachtelten Spalten mit der Funktion `JSON_VALUE` für jede Spalte lesen. Mit `OPENROWSET` können Sie die Pfade der geschachtelten Eigenschaften direkt in einer `WITH`-Klausel angeben. Sie können die Pfade als Namen einer Spalte festlegen, oder Sie können einen [JSON-Pfadausdruck](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) hinter dem Spaltentyp hinzufügen.

Die folgende Abfrage liest die Datei structBeispiel.parquet und zeigt, wie Elemente einer geschachtelten Spalte an die Oberfläche gebracht werden: Es gibt zwei Möglichkeiten, auf einen geschachtelten Wert zu verweisen:
- Durch Angeben des Pfadausdrucks des geschachtelten Werts nach der Typspezifikation.
- Durch Formatieren des Spaltennamens als geschachtelten Pfad mithilfe von „.“ zum Verweisen auf die Felder.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Zugreifen auf Elemente aus wiederholten Spalten

Die folgende Abfrage liest die Datei „justSimpleArray.parquet“ und verwendet [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), um ein skalares Element aus einer sich wiederholenden Spalte, z. B. einem Array oder einer Zuordnung, abzurufen:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Das Ergebnis lautet wie folgt:

|SimpleArray    | FirstElement  | SecondElement | ThirdElement |
| --- | --- | --- | --- |
| [11,12,13] | 11   | 12 | 13 |
| [21,22,23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Zugreifen auf Teilobjekte aus komplexen Spalten

Die folgende Abfrage liest die Datei „mapExample.parquet“ und verwendet [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), um ein nicht skalares Element aus einer sich wiederholenden Spalte, z. B. einem Array oder einer Zuordnung, abzurufen:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Sie können auch explizit auf die Spalten verweisen, die in einer `WITH`-Klausel zurückgegeben werden sollen:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

Die Struktur `MapOfPersons` wird als VARCHAR-Spalte zurückgegeben und als JSON-Zeichenfolge formatiert.

## <a name="project-values-from-repeated-columns"></a>Projizieren von Werten aus wiederholten Spalten

Wenn Sie in manchen Spalten über ein Array aus Skalarwerten (z. B. `[1,2,3]`) verfügen, können Sie sie einfach erweitern und mithilfe des folgenden Skripts mit der Hauptzeile verknüpfen:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Nächste Schritte

Der nächste Artikel zeigt Ihnen, wie Sie [JSON-Dateien abfragen](query-json-files.md) können.
