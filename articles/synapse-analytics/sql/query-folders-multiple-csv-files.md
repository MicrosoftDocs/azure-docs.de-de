---
title: Abfragen von Ordnern und mehreren Dateien mit dem serverlosen SQL-Pool
description: Der serverlose SQL-Pool unterstützt das Lesen mehrerer Dateien/Ordner durch die Verwendung von Platzhalterzeichen, die den in Windows-Betriebssystemen verwendeten Platzhalterzeichen ähnlich sind.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 83c4d88e1a87f6b546e26dd55da338a36f16ebe4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462631"
---
# <a name="query-folders-and-multiple-files"></a>Abfragen von Ordnern und mehreren Dateien  

In diesem Artikel erfahren Sie, wie Sie eine Abfrage mit einem serverlosen SQL-Pool in Azure Synapse Analytics schreiben können.

Der serverlose SQL-Pool unterstützt das Lesen mehrerer Dateien/Ordner durch die Verwendung von Platzhalterzeichen, die den in Windows-Betriebssystemen verwendeten Platzhalterzeichen ähnlich sind. Allerdings ist die Flexibilität größer, da mehrere Platzhalterzeichen erlaubt sind.

## <a name="prerequisites"></a>Voraussetzungen

Im ersten Schritt **erstellen Sie eine Datenbank**, in der Sie die Abfragen ausführen. Initialisieren Sie dann die Objekte, indem Sie das [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) für diese Datenbank ausführen. Mit diesem Setupskript werden die Datenquellen, die für die gesamte Datenbank gültigen Anmeldeinformationen und externe Dateiformate erstellt, die in diesen Beispielen verwendet werden.

Sie verwenden den Ordner *csv/taxi*, um die Beispielabfragen nachzuverfolgen. Er enthält Daten der Fahrtenaufzeichnungen für „NYC Taxi – Yellow Taxi“ von Juli 2016 bis Juni 2018. Dateien in *csv/taxi* sind nach Jahr und Monat im folgenden Format benannt: yellow_tripdata_<year>-<month>.csv.

## <a name="read-all-files-in-folder"></a>Lesen aller Dateien im Ordner

Das folgende Beispiel liest alle Datendateien von „NYC Yellow Taxi“ aus dem Ordner *csv/taxi* und gibt die Gesamtzahl der Fahrgäste und Fahrten pro Jahr zurück. Außerdem wird die Verwendung von Aggregatfunktionen veranschaulicht.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        pickup_datetime DATETIME2 2, 
        passenger_count INT 4
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle Dateien, auf die mit dem einzelnen OPENROWSET zugegriffen wird, müssen dieselbe Struktur aufweisen (d. h. Anzahl der Spalten und Typ der Daten).

### <a name="read-subset-of-files-in-folder"></a>Lesen einer Teilmenge der Dateien im Ordner

Das folgende Beispiel liest die Datendateien von „NYC Yellow Taxi“ für 2017 aus dem Ordner *csv/taxi* unter Verwendung eines Platzhalterzeichens und gibt den Gesamtfahrpreis pro Zahlungsart zurück.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        payment_type INT 10,
        fare_amount FLOAT 11
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Alle Dateien, auf die mit dem einzelnen OPENROWSET zugegriffen wird, müssen dieselbe Struktur aufweisen (d. h. Anzahl der Spalten und Typ der Daten).

## <a name="read-folders"></a>Lesen von Ordnern

Der Pfad, den Sie für OPENROWSET bereitstellen, kann auch ein Pfad zu einem Ordner sein. Diese Abfragetypen sind in den folgenden Abschnitten enthalten.

### <a name="read-all-files-from-specific-folder"></a>Lesen aller Dateien aus einem bestimmten Ordner

Sie können alle Dateien in einem Ordner lesen, indem Sie das Platzhalterzeichen auf Dateiebene verwenden, wie in [Lesen aller Dateien im Ordner](#read-all-files-in-folder) gezeigt. Es gibt jedoch eine Möglichkeit, einen Ordner abzufragen und alle Dateien in diesem Ordner zu verwenden.

Wenn der in OPENROWSET angegebene Pfad auf einen Ordner verweist, werden alle Dateien in diesem Ordner als Quelle für die Abfrage verwendet. Mit der folgenden Abfrage werden alle Dateien im Ordner *csv/taxi* gelesen.

> [!NOTE]
> Beachten Sie die Angabe von „/“ am Pfadende in der folgenden Abfrage. Es bezeichnet einen Ordner. Wenn „/“ ausgelassen wird, wird die Abfrage stattdessen auf eine Datei mit dem Namen *taxi* ausgerichtet.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle Dateien, auf die mit dem einzelnen OPENROWSET zugegriffen wird, müssen dieselbe Struktur aufweisen (d. h. Anzahl der Spalten und Typ der Daten).

### <a name="read-all-files-from-multiple-folders"></a>Lesen aller Dateien aus mehreren Ordnern

Es ist möglich, Dateien aus mehreren Ordnern zu lesen, indem Sie ein Platzhalterzeichen verwenden. Die folgende Abfrage liest alle Dateien aus allen Ordnern, die sich im Ordner *csv* befinden und deren Namen mit *t* beginnen und mit *i* enden.

> [!NOTE]
> Beachten Sie die Angabe von „/“ am Pfadende in der folgenden Abfrage. Es bezeichnet einen Ordner. Wenn „/“ ausgelassen wird, wird die Abfrage stattdessen auf Dateien mit dem Namen *t&ast;i* ausgerichtet.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle Dateien, auf die mit dem einzelnen OPENROWSET zugegriffen wird, müssen dieselbe Struktur aufweisen (d. h. Anzahl der Spalten und Typ der Daten).

Da Sie nur über einen Ordner verfügen, der den Kriterien entspricht, ist das Abfrageergebnis dasselbe wie unter [Lesen aller Dateien im Ordner](#read-all-files-in-folder).

## <a name="traverse-folders-recursively"></a>Rekursives Durchsuchen von Ordnern

Der serverlose SQL-Pool kann Ordner rekursiv durchsuchen, wenn Sie „/**“ am Ende des Pfads angeben. Mit der folgenden Abfrage werden alle Dateien aus allen Ordnern und Unterordnern gelesen, die im Ordner *csv* gespeichert sind.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/taxi/**', 
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle Dateien, auf die mit dem einzelnen OPENROWSET zugegriffen wird, müssen dieselbe Struktur aufweisen (d. h. Anzahl der Spalten und Typ der Daten).

## <a name="multiple-wildcards"></a>Mehrere Platzhalterzeichen

Sie können mehrere Platzhalterzeichen auf verschiedenen Pfadebenen verwenden. Sie können z. B. vorherige Abfragen ergänzen, um Dateien nur mit Daten von 2017 zu lesen, und zwar aus allen Ordnern, deren Namen mit *t* beginnen und mit *i* enden.

> [!NOTE]
> Beachten Sie die Angabe von „/“ am Pfadende in der folgenden Abfrage. Es bezeichnet einen Ordner. Wenn „/“ ausgelassen wird, wird die Abfrage stattdessen auf Dateien mit dem Namen *t&ast;i* ausgerichtet.
> Es gibt eine Obergrenze von zehn Platzhalterzeichen pro Abfrage.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
        BULK 'csv/t*i/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Alle Dateien, auf die mit dem einzelnen OPENROWSET zugegriffen wird, müssen dieselbe Struktur aufweisen (d. h. Anzahl der Spalten und Typ der Daten).

Da Sie nur über einen Ordner verfügen, der den Kriterien entspricht, ist das Abfrageergebnis dasselbe wie unter [Lesen einer Teilmenge von Dateien im Ordner](#read-subset-of-files-in-folder) und [Lesen aller Dateien aus einem bestimmten Ordner](#read-all-files-from-specific-folder). Komplexere Szenarien für die Verwendung von Platzhalterzeichen werden unter [Abfragen von Parquet-Dateien](query-parquet-files.md) behandelt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im Artikel [Abfragen von bestimmten Dateien](query-specific-files.md).
