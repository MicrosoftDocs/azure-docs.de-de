---
title: Abfragen von CSV-Dateien mithilfe eines serverlosen SQL-Pools
description: In diesem Artikel erfahren Sie, wie Sie mithilfe eines serverlosen SQL-Pools einzelne CSV-Dateien mit verschiedenen Dateiformaten abfragen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f2f0cdf307e91fb40c55d4a98139bad1a5eca886
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462589"
---
# <a name="query-csv-files"></a>Abfragen von CSV-Dateien

In diesem Artikel erfahren Sie, wie Sie in Azure Synapse Analytics eine einzelne CSV-Datei mithilfe eines serverlosen SQL-Pools abfragen. CSV-Dateien können unterschiedliche Formate aufweisen: 

- Mit und ohne Kopfzeile
- Werte mit Kommas und Tabstopps als Trennzeichen
- Zeilenenden im Windows- und Unix-Stil
- Werte mit und ohne Anführungszeichen sowie Zeichen mit Escapezeichen

Alle oben aufgeführten Varianten werden im Folgenden behandelt.

## <a name="quickstart-example"></a>Schnellstartbeispiel

Mit der Funktion `OPENROWSET` können Sie den Inhalt einer CSV-Datei lesen, indem Sie die URL zur Datei bereitstellen.

### <a name="read-a-csv-file"></a>Lesen einer CSV-Datei

Am einfachsten können Sie den Inhalt Ihrer `CSV`-Datei anzeigen, indem Sie der Funktion `OPENROWSET` die Datei-URL bereitstellen und CSV als `FORMAT` sowie 2.0 als `PARSER_VERSION` angeben. Wenn die Datei öffentlich verfügbar ist oder Ihre Azure AD-Identität auf diese Datei zugreifen kann, sollten Sie den Inhalt der Datei mithilfe einer Abfrage wie im folgenden Beispiel anzeigen können:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    format = 'csv',
    parser_version = '2.0',
    firstrow = 2 ) as rows
```

Mit der Option `firstrow` wird die erste Zeile in der CSV-Datei übersprungen, bei der es sich in diesem Fall um den Header handelt. Stellen Sie sicher, dass Sie auf diese Datei zugreifen können. Wenn Ihre Datei mit einem SAS-Schlüssel oder einer benutzerdefinierten Identität geschützt ist, müssen Sie [Anmeldeinformationen auf Serverebene für die SQL-Anmeldung](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential) einrichten.

> [!IMPORTANT]
> Wenn die CSV-Datei UTF-8-Zeichen enthält, stellen Sie sicher, dass Sie eine UTF-8-Datenbanksortierung verwenden (z. B. `Latin1_General_100_CI_AS_SC_UTF8`).
> Ein Konflikt zwischen der Textcodierung in der Datei und der Sortierung kann zu unerwarteten Konvertierungsfehlern führen.
> Die Standardsortierung der aktuellen Datenbank kann mithilfe der folgenden T-SQL-Anweisung problemlos geändert werden: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`

### <a name="data-source-usage"></a>Datenquellennutzung

Im vorherigen Beispiel wird der vollständige Pfad zur Datei verwendet. Alternativ können Sie eine externe Datenquelle mit dem Speicherort erstellen, der auf den Stammordner des Speichers verweist:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
```

Nachdem Sie eine Datenquelle erstellt haben, können Sie diese Datenquelle und den relativen Pfad zur Datei in der Funktion `OPENROWSET` verwenden:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) as rows
```

Wenn eine Datenquelle mit einem SAS-Schlüssel oder einer benutzerdefinierten Identität geschützt ist, können Sie die [Datenquelle mit datenbankweit gültigen Anmeldeinformationen](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential) konfigurieren.

### <a name="explicitly-specify-schema"></a>Explizites Angeben des Schemas

Bei `OPENROWSET` können Sie mit der `WITH`-Klausel explizit angeben, welche Spalten aus der Datei gelesen werden sollen:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.csv',
        data_source = 'covid',
        format = 'csv',
        parser_version ='2.0',
        firstrow = 2
    ) with (
        date_rep date 1,
        cases int 5,
        geo_id varchar(6) 8
    ) as rows
```

Die Zahlen nach einem Datentyp in der `WITH`-Klausel stellen den Spaltenindex in der CSV-Datei dar.

> [!IMPORTANT]
> Wenn die CSV-Datei UTF-8-Zeichen enthält, stellen Sie sicher, dass Sie explizit eine UTF-8-Sortierung (z. B. `Latin1_General_100_CI_AS_SC_UTF8`) für alle Spalten in der `WITH`-Klausel angeben, oder legen Sie eine UTF-8-Sortierung auf Datenbankebene fest.
> Ein Konflikt zwischen der Textcodierung in der Datei und der Sortierung kann zu unerwarteten Konvertierungsfehlern führen.
> Die Standardsortierung der aktuellen Datenbank kann mithilfe der folgenden T-SQL-Anweisung problemlos geändert werden: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`
> Sie können die Sortierung der Spaltentypen mit der folgenden Definition problemlos festlegen: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8 8`

In den folgenden Abschnitten erfahren Sie, wie Sie verschiedene Typen von CSV-Dateien abfragen.

## <a name="prerequisites"></a>Voraussetzungen

Im ersten Schritt erstellen Sie eine **Datenbank**, in der die Tabellen erstellt werden sollen. Initialisieren Sie dann die Objekte, indem Sie das [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) für diese Datenbank ausführen. Mit diesem Setupskript werden die Datenquellen, die für die gesamte Datenbank gültigen Anmeldeinformationen und externe Dateiformate erstellt, die in diesen Beispielen verwendet werden.

## <a name="windows-style-new-line"></a>Neue-Zeile-Zeichen im Windows-Stil

Die folgende Abfrage zeigt, wie eine CSV-Datei ohne Kopfzeile gelesen wird, die ein Neue-Zeile-Zeichen im Windows-Stil und Spalten mit Kommas als Trennzeichen enthält.

Dateivorschau:

![Die ersten zehn Zeilen der CSV-Datei (ohne Kopfzeile und mit Neue-Zeile-Zeichen im Windows-Stil).](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Neue-Zeile-Zeichen im Unix-Stil

Die folgende Abfrage zeigt, wie eine Datei ohne Kopfzeile gelesen wird, die ein Neue-Zeile-Zeichen im Unix-Stil und Spalten mit Kommas als Trennzeichen enthält. Beachten Sie den abweichenden Speicherort der Datei im Vergleich zu den anderen Beispielen.

Dateivorschau:

![Die ersten zehn Zeilen der CSV-Datei ohne Kopfzeile und mit Neue-Zeile-Zeichen im Unix-Stil.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Kopfzeile

Die folgende Abfrage zeigt, wie eine Datei mit einer Kopfzeile, mit einem Neue-Zeile-Zeichen im Unix-Stil und Spalten mit Kommas als Trennzeichen gelesen wird. Beachten Sie den abweichenden Speicherort der Datei im Vergleich zu den anderen Beispielen.

Dateivorschau:

![Die ersten zehn Zeilen der CSV-Datei mit Kopfzeile und mit Neue-Zeile-Zeichen im Unix-Stil.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Benutzerdefiniertes Anführungszeichen

Die folgende Abfrage zeigt, wie eine Datei mit Kopfzeile gelesen wird, die ein Neue-Zeile-Zeichen im Unix-Stil, Spalten mit Kommas als Trennzeichen und Werte in Anführungszeichen enthält. Beachten Sie den abweichenden Speicherort der Datei im Vergleich zu den anderen Beispielen.

Dateivorschau:

![Die ersten zehn Zeilen der CSV-Datei mit Kopfzeile und mit Neue-Zeile-Zeichen im Unix-Stil sowie mit Werten in Anführungszeichen.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Diese Abfrage würde dieselben Ergebnisse liefern, wenn Sie den FIELDQUOTE-Parameter weglassen, da der Standardwert für FIELDQUOTE ein doppeltes Anführungszeichen ist.

## <a name="escape-characters"></a>Escape-Zeichen

Die folgende Abfrage zeigt, wie eine Datei mit einer Kopfzeile, mit einem Neue-Zeile-Zeichen im Unix-Stil, Spalten mit Kommas als Trennzeichen und einem Zeichen mit Escapezeichen für das Feldtrennzeichen (Komma) innerhalb von Werten gelesen werden kann. Beachten Sie den abweichenden Speicherort der Datei im Vergleich zu den anderen Beispielen.

Dateivorschau:

![Die ersten zehn Zeilen der CSV-Datei mit Kopfzeile und mit Neue-Zeile-Zeichen im Unix-Stil und Zeichen mit Escapezeichen als Feldtrennzeichen.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Diese Abfrage würde nicht erfolgreich ausgeführt werden, wenn ESCAPECHAR nicht angegeben wird, da das Komma in „Slov,enia“ als Feldtrennzeichen und nicht als Teil des Länder-/Regionsnamens behandelt würde. „Slov,enia“ würde als zwei Spalten behandelt. Daher hätte die bestimmte Zeile eine Spalte mehr als die anderen Zeilen und eine Spalte mehr als Sie in der WITH-Klausel definiert haben.

### <a name="escape-quoting-characters"></a>Versehen von Anführungszeichen mit Escapezeichen

Die folgende Abfrage zeigt, wie eine Datei mit einer Kopfzeile, mit einem Neue-Zeile-Zeichen im Unix-Stil, mit kommagetrennten Spalten und mit einem mit Escapezeichen versehenen doppelten Anführungszeichen innerhalb von Werten gelesen werden kann. Beachten Sie den abweichenden Speicherort der Datei im Vergleich zu den anderen Beispielen.

Dateivorschau:

![Die folgende Abfrage zeigt, wie eine Datei mit einer Kopfzeile, mit einem Neue-Zeile-Zeichen im Unix-Stil, mit kommagetrennten Spalten und mit einem mit Escapezeichen versehenen doppelten Anführungszeichen innerhalb von Werten gelesen werden kann.](./media/query-single-csv-file/population-unix-hdr-escape-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slovenia';
```

> [!NOTE]
> Das Anführungszeichen muss mit einem weiteren Anführungszeichen als Escapezeichen versehen werden. Anführungszeichen können nur in Spaltenwerten enthalten sein, wenn der Wert mit Anführungszeichen gekapselt ist.

## <a name="tab-delimited-files"></a>Dateien mit Tabstopps als Trennzeichen

Die folgende Abfrage zeigt, wie eine Datei mit Kopfzeile gelesen wird, die ein Neue-Zeile-Zeichen im Unix-Stil und Spalten mit Tabstopps als Trennzeichen enthält. Beachten Sie den abweichenden Speicherort der Datei im Vergleich zu den anderen Beispielen.

Dateivorschau:

![Die ersten zehn Zeilen der CSV-Datei mit Kopfzeile und mit Neue-Zeile-Zeichen im Unix-Stil sowie Tabstopps als Trennzeichen.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="return-a-subset-of-columns"></a>Zurückgeben einer Teilmenge von Spalten

Bisher haben Sie das CSV-Dateischema mit WITH angegeben und alle Spalten aufgelistet. Sie können nur Spalten angeben, die Sie in Ihrer Abfrage tatsächlich benötigen, indem Sie für jede erforderliche Spalte eine Ordnungszahl verwenden. Sie werden auch Spalten auslassen, die Sie nicht interessieren.

Die folgende Abfrage gibt die Anzahl der verschiedenen Länder-/Regionsnamen in einer Datei zurück, wobei nur die erforderlichen Spalten angegeben werden:

> [!NOTE]
> Werfen Sie einen Blick auf die WITH-Klausel in der Abfrage unten und beachten Sie, dass am Ende der Zeile, in der Sie die Spalte *[country_name]* definieren, „2“ (ohne Anführungszeichen) steht. Dies bedeutet, dass die Spalte *[country_name]* die zweite Spalte in der Datei ist. Die Abfrage ignoriert alle Spalten in der Datei außer der zweiten.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Nächste Schritte

In den nächsten Artikeln erfahren Sie Folgendes:

- [Abfragen von Parquet-Dateien](query-parquet-files.md)
- [Abfragen von Ordnern und mehreren Dateien](query-folders-multiple-csv-files.md)
