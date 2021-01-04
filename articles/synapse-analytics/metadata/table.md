---
title: Tabellen mit gemeinsam genutzten Metadaten
description: Azure Synapse Analytics bietet ein Modell mit gemeinsam genutzten Metadaten, das es ermöglicht, über einen serverlosen SQL-Pool und einen dedizierten SQL-Pool auf eine in einem serverlosen Apache Spark-Pool erstellte Tabelle zuzugreifen, ohne die Daten zu duplizieren.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ee18edd563d94a85dedf48b7a4d6df394c09707
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461381"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics: Gemeinsam genutzte Metadatentabellen


Azure Synapse Analytics ermöglicht den verschiedenen Berechnungsengines von Arbeitsbereichen die gemeinsame Nutzung von Datenbanken und Parquet-basierten Tabellen zwischen Apache Spark-Pools und einem serverlosen SQL-Pool.

Nach der Erstellung einer Datenbank durch einen Spark-Auftrag können darin Tabellen mit Spark erstellt werden, die Parquet als Speicherformat verwenden. Diese Tabellen stehen umgehend für Abfragen von beliebigen Spark-Pools des Azure Synapse-Arbeitsbereichs zur Verfügung. Darüber hinaus können sie von einem beliebigen Spark-Auftrag verwendet werden (entsprechende Berechtigungen vorausgesetzt).

Die von Spark erstellten, verwalteten und externen Tabellen werden auch als externe Tabellen mit demselben Namen in der entsprechenden synchronisierten Datenbank im serverlosen SQL-Pool verfügbar gemacht. Unter [Verfügbarmachen einer Spark-Tabelle in SQL](#expose-a-spark-table-in-sql) finden Sie weitere Details zur Tabellensynchronisierung.

Da die Tabellen asynchron mit dem serverlosen SQL-Pool synchronisiert werden, kommt es zu einer Verzögerung bei der Anzeige.

## <a name="manage-a-spark-created-table"></a>Verwalten einer von Spark erstellten Tabelle

Verwalten Sie von Spark erstellte Datenbanken mithilfe von Spark. Löschen Sie sie beispielsweise über einen Auftrag für serverlose Apache Spark-Pools, und erstellen Sie Tabellen in der Datenbank über Spark.

Wenn Sie Objekte in einer solchen Datenbank über einen serverlosen SQL-Pool erstellen oder versuchen, die Datenbank zu löschen, ist der Vorgang zwar erfolgreich, die ursprüngliche Spark-Datenbank wird jedoch nicht geändert.

## <a name="expose-a-spark-table-in-sql"></a>Verfügbarmachen einer Spark-Tabelle in SQL

### <a name="shared-spark-tables"></a>Gemeinsam genutzte Spark-Tabellen

In Spark stehen zwei Arten von Tabellen zur Verfügung, die von Azure Synapse automatisch in SQL verfügbar gemacht werden:

- Verwaltete Tabellen

  Spark bietet zahlreiche Optionen für die Speicherung von Daten in verwalteten Tabellen. Hierzu zählen beispielsweise TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA und LIBSVM. Diese Dateien werden für gewöhnlich im Verzeichnis `warehouse` gespeichert, in dem die Daten der verwalteten Tabelle gespeichert werden.

- Externe Tabellen

  Spark ermöglicht auch die Erstellung externer Tabellen auf der Grundlage vorhandener Daten – entweder durch Angabe der Option `LOCATION`oder durch Verwendung des Hive-Formats. Solche externen Tabellen können auf einer Vielzahl von Datenformaten basieren (einschließlich Parquet).

Azure Synapse gibt aktuell nur verwaltete und externe Spark-Tabellen mit im Parquet-Format gespeicherten Daten für die SQL-Engines frei. Auf anderen Formaten basierende Tabellen werden nicht automatisch synchronisiert. Solche Tabellen können Sie ggf. explizit selbst als externe Tabelle in Ihrer eigenen SQL-Datenbank synchronisieren, sofern die SQL-Engine das zugrunde liegende Format der Tabelle unterstützt.

### <a name="share-spark-tables"></a>Gemeinsames Nutzen von Spark-Tabellen

Die gemeinsam nutzbaren verwalteten und externen Spark-Tabellen werden in der SQL-Engine als externe Tabellen mit folgenden Eigenschaften verfügbar gemacht:

- Die Datenquelle der externen SQL-Tabelle ist die Datenquelle, die den Ordner des Spark-Tabellenspeicherorts darstellt.
- Die externe SQL-Tabelle hat das Parquet-Dateiformat.
- Für die Zugriffsanmeldeinformationen der externen SQL-Tabelle wird das Pass-Through-Verfahren verwendet.

Da alle Spark-Tabellennamen gültige SQL-Tabellennamen und alle Spark-Spaltennamen gültige SQL-Spaltennamen sind, werden für die externe SQL-Tabelle die Spark-Tabellennamen und -Spaltennamen verwendet.

Spark-Tabellen bieten andere Datentypen als die SQL-Engines von Synapse. In der folgenden Tabelle werden die Spark-Tabellendatentypen den SQL-Typen zugeordnet:

| Spark-Datentyp | SQL-Datentyp | Kommentare |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Mit Sortierung (`Latin1_General_100_BIN2_UTF8`) |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Wird in JSON mit Sortierung (`Latin1_General_100_BIN2_UTF8`) serialisiert |
| `map`       |    `varchar(max)`   | Wird in JSON mit Sortierung (`Latin1_General_100_BIN2_UTF8`) serialisiert |
| `struct`    |    `varchar(max)`   | Wird in JSON mit Sortierung (`Latin1_General_100_BIN2_UTF8`) serialisiert |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Sicherheitsmodell

Die Spark-Datenbanken und -Tabellen sowie deren synchronisierte Darstellungen in der SQL-Engine werden auf der zugrunde liegenden Speicherebene geschützt. Da sie derzeit nicht über Berechtigungen für die eigentlichen Objekte verfügen, können die Objekte im Objekt-Explorer angezeigt werden.

Der Sicherheitsprinzipal, von dem eine verwaltete Tabelle erstellt wird, gilt als Besitzer der Tabelle und verfügt über alle Rechte für die Tabelle sowie für die zugrunde liegenden Ordner und Dateien. Der Besitzer der Datenbank wird zudem automatisch zum Mitbesitzer der Tabelle.

Wenn Sie eine externe Spark- oder SQL-Tabelle mit Passthrough-Authentifizierung erstellen, werden die Daten nur auf der Ordner- und Dateiebene geschützt. Bei einer Abfrage für diese externe Tabelle wird die Sicherheitsidentität des Abfrageübermittlers an das Dateisystem übergeben und auf Zugriffsrechte überprüft.

Weitere Informationen zum Festlegen von Berechtigungen für die Ordner und Dateien finden Sie unter [Azure Synapse Analytics: Gemeinsam genutzte Datenbank](database.md).

## <a name="examples"></a>Beispiele

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Erstellen einer verwalteten Parquet-basierten Tabelle in Spark und Ausführen von Abfragen über einen serverlosen SQL-Pool

In diesem Szenario verfügen Sie über eine Spark-Datenbank namens `mytestdb`. Informationen finden Sie unter [Erstellen einer Spark-Datenbank mit einem serverlosen SQL-Pool und Herstellen einer Verbindung mit dieser Datenbank](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Führen Sie den folgenden Befehl aus, um mit SparkSQL eine verwaltete Spark-Tabelle zu erstellen:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Mit diesem Befehl wird die Tabelle `myParquetTable` in der Datenbank `mytestdb` erstellt. Nach einer kurzen Verzögerung wird die Tabelle aus dem serverlosen SQL-Pool angezeigt. Führen Sie beispielsweise die folgende Anweisung über den serverlosen SQL-Pool aus:

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Vergewissern Sie sich, dass `myParquetTable` in den Ergebnissen enthalten ist.

>[!NOTE]
>Tabellen mit einem anderen Speicherformat als Parquet werden nicht synchronisiert.

Fügen Sie als Nächstes über Spark einige Werte in die Tabelle ein. Verwenden Sie dazu beispielsweise die folgenden C#-Spark-Anweisungen in einem C#-Notebook:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Nun können Sie die Daten wie folgt aus dem serverlosen SQL-Pool lesen:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Als Ergebnis sollte die folgende Zeile zurückgegeben werden:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Erstellen einer Parquet-basierten externen Tabelle in Spark und Ausführen von Abfragen über den serverlosen SQL-Pool

In diesem Beispiel wird eine externe Spark-Tabelle auf der Grundlage der Parquet-Datendateien erstellt, die im vorherigen Beispiel für die verwaltete Tabelle erstellt wurden.

Führen Sie mit SparkSQL beispielsweise Folgendes aus:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Ersetzen Sie den Platzhalter `<fs>` durch den Namen des Standarddateisystems für den Arbeitsbereich und den Platzhalter `<synapse_ws>` durch den Namen des für dieses Beispiel verwendeten Synapse-Arbeitsbereichs.

Im vorherigen Beispiel wurde die Tabelle `myExtneralParquetTable` in der Datenbank `mytestdb` erstellt. Nach einer kurzen Verzögerung wird die Tabelle aus dem serverlosen SQL-Pool angezeigt. Führen Sie beispielsweise die folgende Anweisung über den serverlosen SQL-Pool aus:

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Vergewissern Sie sich, dass `myExternalParquetTable` in den Ergebnissen enthalten ist.

Nun können Sie die Daten wie folgt aus dem serverlosen SQL-Pool lesen:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Als Ergebnis sollte die folgende Zeile zurückgegeben werden:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu gemeinsam genutzten Metadaten von Azure Synapse Analytics finden Sie [hier](overview.md).
- Weitere Informationen zu gemeinsam genutzten Azure Synapse Analytics-Datenbanken für Metadaten finden Sie [hier](database.md).


