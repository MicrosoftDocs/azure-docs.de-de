---
title: Abfragen von Parquet-Dateien mithilfe eines serverlosen SQL-Pools
description: In diesem Artikel erfahren Sie, wie Sie Parquet-Dateien mithilfe eines serverlosen SQL-Pools abfragen.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 20bfbaeea48711a680877e4d5d8f618e84eb12d7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462577"
---
# <a name="query-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Abfragen von Parquet-Dateien mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie mithilfe eines serverlosen SQL-Pools eine Abfrage schreiben, die Parquet-Dateien liest.

## <a name="quickstart-example"></a>Schnellstartbeispiel

Mit der Funktion `OPENROWSET` können Sie den Inhalt einer Parquet-Datei lesen, indem Sie die URL zur Datei bereitstellen.

### <a name="read-parquet-file"></a>Lesen einer Parquet-Datei

Am einfachsten können Sie den Inhalt Ihrer `PARQUET`-Datei anzeigen, indem Sie der Funktion `OPENROWSET` die Datei-URL bereitstellen und Parquet als `FORMAT` angeben. Wenn die Datei öffentlich verfügbar ist oder Ihre Azure AD-Identität auf diese Datei zugreifen kann, sollten Sie den Inhalt der Datei mithilfe einer Abfrage wie im folgenden Beispiel anzeigen können:

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

Stellen Sie sicher, dass Sie auf diese Datei zugreifen können. Wenn Ihre Datei mit einem SAS-Schlüssel oder einer benutzerdefinierten Azure-Identität geschützt ist, müssen Sie [Anmeldeinformationen auf Serverebene für die SQL-Anmeldung](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential) einrichten.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie eine UTF-8-Datenbanksortierung (z. B. `Latin1_General_100_CI_AS_SC_UTF8`) verwenden, da Zeichenfolgenwerte in Parquet-Dateien mit UTF-8 codiert sind.
> Ein Konflikt zwischen der Textcodierung in der Parquet-Datei und der Sortierung kann zu unerwarteten Konvertierungsfehlern führen.
> Die Standardsortierung der aktuellen Datenbank kann mit der folgenden T-SQL-Anweisung problemlos geändert werden: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`.

### <a name="data-source-usage"></a>Datenquellennutzung

Im vorherigen Beispiel wird der vollständige Pfad zur Datei verwendet. Alternativ können Sie eine externe Datenquelle mit dem Speicherort erstellen, der auf den Stammordner des Speichers verweist, und diese Datenquelle sowie den relativen Pfad zur Datei in der Funktion `OPENROWSET` verwenden:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

Wenn eine Datenquelle mit einem SAS-Schlüssel oder einer benutzerdefinierten Identität geschützt ist, können Sie die [Datenquelle mit datenbankweit gültigen Anmeldeinformationen](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential) konfigurieren.

### <a name="explicitly-specify-schema"></a>Explizites Angeben des Schemas

Bei `OPENROWSET` können Sie mit der `WITH`-Klausel explizit angeben, welche Spalten aus der Datei gelesen werden sollen:

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie explizit eine UTF-8-Sortierung (z. B. `Latin1_General_100_CI_AS_SC_UTF8`) für alle Zeichenfolgenspalten in der `WITH`-Klausel angeben, oder legen Sie eine UTF-8-Sortierung auf Datenbankebene fest.
> Ein Konflikt zwischen der Textcodierung in der Datei und der Sortierung der Zeichenfolgenspalte kann zu unerwarteten Konvertierungsfehlern führen.
> Die Standardsortierung der aktuellen Datenbank kann mithilfe der folgenden T-SQL-Anweisung problemlos geändert werden: `alter database current collate Latin1_General_100_CI_AI_SC_UTF8`.
> Sie können die Sortierung der Spaltentypen problemlos mit der folgenden Definition festlegen: `geo_id varchar(6) collate Latin1_General_100_CI_AI_SC_UTF8`.

In den folgenden Abschnitten erfahren Sie, wie Sie verschiedene Typen von Parquet-Dateien abfragen.

## <a name="prerequisites"></a>Voraussetzungen

Der erste Schritt besteht in der **Erstellung einer Datenbank** mit einer Datenquelle, die auf das Speicherkonto [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) verweist. Initialisieren Sie dann die Objekte, indem Sie das [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) für diese Datenbank ausführen. Mit diesem Setupskript werden die Datenquellen, die für die gesamte Datenbank gültigen Anmeldeinformationen und externe Dateiformate erstellt, die in diesen Beispielen verwendet werden.

## <a name="dataset"></a>Dataset

In diesem Beispiel wird das Dataset [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) verwendet. Sie können Parquet-Dateien auf dieselbe Weise abfragen wie Sie [CSV-Dateien lesen](query-parquet-files.md). Der einzige Unterschied besteht darin, dass der Parameter `FILEFORMAT` auf `PARQUET` festgelegt werden muss. In den Beispielen in diesem Artikel werden die Besonderheiten beim Lesen von Parquet-Dateien erläutert.

## <a name="query-set-of-parquet-files"></a>Abfragesatz von Parquet-Dateien

Bei der Abfrage von Parquet-Dateien können Sie nur die relevanten Spalten angeben.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Automatischer Schemarückschluss

Sie brauchen die OPENROWSET WITH-Klausel nicht zu verwenden, wenn Sie Parquet-Dateien lesen. Spaltennamen und Datentypen werden automatisch aus Parquet-Dateien gelesen.

Das folgende Beispiel zeigt die automatischen Schemarückschlussfunktionen für Parquet-Dateien. Hierzu wird die Anzahl von Zeilen im September 2017 ohne Angabe eines Schemas zurückgegeben.

> [!NOTE]
> Beim Lesen von Parquet-Dateien müssen Sie in der OPENROWSET WITH-Klausel keine Spalten angeben. In diesem Fall verwendet der Abfragedienst des serverlosen SQL-Pools Metadaten in der Parquet-Datei und bindet Spalten nach Namen.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Abfragen von partitionierten Daten

Das in diesem Beispiel bereitgestellte Dataset ist in separate Unterordner aufgeteilt (partitioniert). Mithilfe der filepath-Funktion können Sie bestimmte Partitionen als Ziel verwenden. Dieses Beispiel zeigt die Fahrpreisbeträge nach Jahr, Monat und Zahlungsart für die ersten drei Monate des Jahres 2017.

> [!NOTE]
> Die Abfrage des serverlosen SQL-Pools ist kompatibel mit dem Hive/Hadoop-Partitionierungsschema.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Typzuordnung

Informationen zur Parquet-Typzuordnung zum nativen SQL-Typ finden Sie unter [Typzuordnung für Parquet](develop-openrowset.md#type-mapping-for-parquet).

## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum nächsten Artikel, um Informationen zum [Abfragen geschachtelter Parquet-Typen](query-parquet-nested-types.md) zu erhalten.
