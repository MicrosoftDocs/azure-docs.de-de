---
title: Erstellen und Verwenden externer Tabellen in einem serverlosen SQL-Pool
description: In diesem Abschnitt erfahren Sie, wie Sie externe Tabellen in einem serverlosen SQL-Pool erstellen und verwenden.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5dcd4b7b76752ca5396fc68afc8d4c8e4e1edca5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462295"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Erstellen und Verwenden externer Tabellen mithilfe eines serverlosen SQL-Pools in Azure Synapse Analytics

In diesem Abschnitt erfahren Sie, wie Sie [externe Tabellen](develop-tables-external-tables.md) in einem serverlosen SQL-Pool erstellen und verwenden. Externe Tabellen sind nützlich, wenn Sie den Zugriff auf externe Daten in einem serverlosen SQL-Pool steuern und Tools wie Power BI in Verbindung mit einem serverlosen SQL-Pool verwenden möchten. Externe Tabellen können auf zwei Speichertypen zugreifen:
- Öffentlicher Speicher, in dem Benutzer auf öffentliche Speicherdateien zugreifen.
- Geschützter Speicher, in dem Benutzer mithilfe von SAS-Anmeldeinformationen, Azure AD-Identität oder verwalteter Identität des Synapse-Arbeitsbereichs auf Speicherdateien zugreifen.

## <a name="prerequisites"></a>Voraussetzungen

Im ersten Schritt erstellen Sie eine Datenbank, in der die Tabellen erstellt werden sollen. Initialisieren Sie dann die Objekte, indem Sie das [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) für diese Datenbank ausführen. Mit diesem Skript werden die folgenden Objekte erstellt, die in diesem Beispiel verwendet werden:
- DATABASE SCOPED CREDENTIAL (Datenbankweit gültige Anmeldeinformationen) `sqlondemand`, die den Zugriff auf das SAS-geschützte `https://sqlondemandstorage.blob.core.windows.net` Azure-Speicherkonto ermöglichen.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- EXTERNAL DATA SOURCE (Externe Datenquelle) `sqlondemanddemo`, die auf das mit dem SAS-Schlüssel geschützte Demospeicherkonto verweist, und EXTERNAL DATA SOURCE `YellowTaxi`, die auf das öffentlich verfügbare Azure-Speicherkonto am Speicherort `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/` verweist.

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- Dateiformate `QuotedCSVWithHeaderFormat` und `ParquetFormat` zur Beschreibung von CSV- und Parquet-Dateitypen.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

Die Abfragen in diesem Artikel werden in Ihrer-Beispieldatenbank ausgeführt und verwenden diese Objekte. 

## <a name="create-an-external-table-on-protected-data"></a>Erstellen einer externen Tabelle für geschützte Daten

Sie können externe Tabellen für den Zugriff auf Daten in einem Azure-Speicherkonto erstellen, das den Zugriff auf Benutzer mit einer Azure AD-Identität oder einem SAS-Schlüssel erlaubt. Externe Tabellen können auf die gleiche Weise wie reguläre externe SQL Server-Tabellen erstellt werden. 

Mit der folgenden Abfrage wird eine externe Tabelle zum Lesen der Datei *population.csv* aus dem Azure-Speicherkonto der Demo „SynapseSQL“ erstellt, auf die mithilfe der Datenquelle `sqlondemanddemo` verwiesen und die mit den datenbankweit gültigen Anmeldeinformationen `sqlondemand` geschützt wird. 

Datenquelle und datenbankweit gültige Anmeldeinformationen werden im [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) erstellt.

> [!NOTE]
> Ändern Sie die erste Zeile in der Abfrage (d. h. [mydbname]), sodass die von Ihnen erstellte Datenbank verwendet wird. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>Erstellen einer externen Tabelle für öffentliche Daten

Sie können externe Tabellen erstellen, die Daten aus den in öffentlich verfügbarem Azure-Speicher abgelegten Dateien lesen. Mit diesem [Setupskript](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) wird eine Definition für eine öffentliche externe Datenquelle und ein Parquet-Dateiformat erstellt, die in der folgenden Abfrage verwendet wird:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```
## <a name="use-an-external-table"></a>Verwenden einer externen Tabelle

Sie können [externe Tabellen](develop-tables-external-tables.md) in Ihren Abfragen genauso wie in SQL Server-Abfragen verwenden.

Die folgende Abfrage veranschaulicht dies anhand der externen Tabelle *population*, die Sie im vorhergehenden Abschnitt erstellt haben. Sie gibt Länder-/Regionsnamen mit der entsprechenden Bevölkerung im Jahr 2019 in absteigender Reihenfolge zurück.

> [!NOTE]
> Ändern Sie die erste Zeile in der Abfrage (d. h. [mydbname]), sodass die von Ihnen erstellte Datenbank verwendet wird.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Nächste Schritte

Informationen dazu, wie die Ergebnisse einer Abfrage im Speicher gespeichert werden, finden Sie im Artikel [Speichern von Abfrageergebnissen im Speicher mithilfe von SQL On-Demand (Vorschauversion) und Azure Synapse Analytics](../sql/create-external-table-as-select.md).
