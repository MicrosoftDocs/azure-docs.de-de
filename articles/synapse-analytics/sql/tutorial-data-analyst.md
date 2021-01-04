---
title: 'Tutorial: Analysieren von Azure Open Datasets in Azure Synapse Studio mithilfe eines serverlosen SQL-Pools'
description: In diesem Tutorial wird gezeigt, wie Sie ganz einfach eine explorative Datenanalyse durchführen. Dabei werden mithilfe eines serverlosen SQL-Pools verschiedene Azure Open Datasets-Instanzen miteinander kombiniert und die Ergebnisse in Azure Synapse Studio visualisiert.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7b2d4953b3a42c5d66fca4a67b4a6d0f13700a35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459124"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool"></a>Tutorial: Untersuchen und Analysieren von Data Lakes mit einem serverlosen SQL-Pool

In diesem Tutorial erfahren Sie, wie Sie eine explorative Datenanalyse durchführen. Sie kombinieren verschiedene Azure Open Datasets-Instanzen über einen serverlosen SQL-Pools. Anschließend visualisieren Sie die Ergebnisse in Synapse Studio für Synapse Analytics.

Die OPENROWSET(BULK...)-Funktion ermöglicht den Zugriff auf Dateien in Azure Storage. Die [OPENROWSET](develop-openrowset.md)-Funktion liest den Inhalt einer Remotedatenquelle (z. B. eine Datei) und gibt den Inhalt als eine Reihe von Zeilen zurück.

## <a name="automatic-schema-inference"></a>Automatischer Schemarückschluss

Da die Daten im Parquet-Dateiformat vorliegen, steht der automatische Schemarückschluss zur Verfügung. Die Daten können somit problemlos abgefragt werden, ohne die Datentypen aller Spalten in den Dateien aufzulisten. Außerdem können Sie den Mechanismus für virtuelle Spalten sowie die Dateipfadfunktion verwenden, um einen Teil der Dateien herauszufiltern.

Führen Sie zunächst die folgende Abfrage aus, um sich mit den NYC-Taxidaten vertraut zu machen:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Das [Taxi-Dataset für New York City (NYC)](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) umfasst:

- Datum/Uhrzeit für Abholung und Ankunft
- Start- und Zielort 
- Fahrtentfernungen
- Einzelkosten
- Tarifarten
- Zahlungsarten 
- Vom Fahrer gemeldete Fahrgastzahlen

Analog dazu können Sie mithilfe der folgenden Abfrage das Dataset für gesetzliche Feiertage abfragen:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Und schließlich können Sie mithilfe der folgenden Abfrage das Dataset mit den Wetterdaten abfragen:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Weitere Informationen zur Bedeutung der einzelnen Spalten finden Sie in den Beschreibungen der Datasets: 
- [NYC Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Gesetzliche Feiertage](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Wetterdaten](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Zeitreihen, Saisonalität und Ausreißeranalyse

Mit der folgenden Abfrage können Sie ganz einfach die jährliche Anzahl von Taxifahrten zusammenfassen:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Der folgende Ausschnitt zeigt das Ergebnis für die jährliche Anzahl von Taxifahrten:

![Ergebnisausschnitt für die jährliche Anzahl von Taxifahrten](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Die Daten können in Synapse Studio visualisiert werden. Wechseln Sie hierzu von der **Tabellenansicht** zur **Diagrammansicht**. Sie können zwischen verschiedenen Diagrammtypen (**Flächendiagramm**, **Balkendiagramm**, **Säulendiagramm**, **Liniendiagramm**, **Kreisdiagramm** oder **Punktdiagramm**) wählen. Erstellen Sie in diesem Fall ein **Säulendiagramm**, und legen Sie die **Kategoriespalte** auf **current_year** fest:

![Säulendiagramm mit Fahrten pro Jahr](./media/tutorial-data-analyst/column-chart-rides-year.png)

In dieser Visualisierung können sehen, dass die Anzahl der Fahrten im Lauf der Jahre zurückgegangen ist. Dieser Rückgang ist vermutlich auf die zunehmende Beliebtheit von Carsharing-Unternehmen zurückzuführen.

> [!NOTE]
> Zum Zeitpunkt der Erstellung dieses Tutorials liegen für 2019 nur unvollständige Daten vor. Dies hat einen signifikanten Rückgang bei der Anzahl von Fahrten für dieses Jahr zur Folge.

Als Nächstes konzentrieren wir uns bei der Analyse auf ein einzelnes Jahr, beispielsweise 2016. Von der folgenden Abfrage wird die tägliche Anzahl von Fahrten in diesem Jahr zurückgegeben:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Der folgende Ausschnitt zeigt das Ergebnis für diese Abfrage:

![Ergebnisausschnitt für die tägliche Anzahl von Fahrten im Jahr 2016](./media/tutorial-data-analyst/daily-rides.png)

Auch diese Daten können ganz einfach in einem **Säulendiagramm** visualisiert werden, indem die **Kategoriespalte** auf **current_day** und die **Legendenspalte (Reihen)** auf **rides_per_day** festgelegt wird.

![Säulendiagramm mit täglicher Anzahl von Fahrten im Jahr 2016](./media/tutorial-data-analyst/column-chart-daily-rides.png)

Im ausgegebenen Diagramm ist zu sehen, dass es ein wöchentliches Muster gibt, wobei der jeweilige Samstag der Spitzentag ist. In den Sommermonaten werden urlaubsbedingt weniger Taxifahrten durchgeführt. Sie können außerdem einige signifikante Einbrüche bei der Anzahl von Taxifahrten sehen, ohne dass ein klares Muster für den Zeitpunkt und den Grund dieser Rückgänge erkennbar wäre.

Daher soll im nächsten Schritt geprüft werden, ob die Einbrüche mit gesetzlichen Feiertagen korrelieren. Um sehen zu können, ob es eine Korrelation gibt, wird das Dataset der NYC-Taxifahrten mit dem Dataset der gesetzlichen Feiertage verknüpft:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![Ergebnisvisualisierung für die Datasets der NYC-Taxifahrten und der gesetzlichen Feiertage](./media/tutorial-data-analyst/rides-public-holidays.png)

Diesmal soll die Anzahl von Taxifahrten während der öffentlichen Feiertage hervorgehoben werden. Hierzu werden **none** für die **Kategoriespalte** sowie **rides_per_day** und **holiday** als **Legendenspalten (Reihe)** ausgewählt.

![Diagramm zur Anzahl von Taxifahrten während gesetzlicher Feiertage](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

Wie Sie dem Diagramm entnehmen können, ist die Anzahl von Taxifahrten an gesetzlichen Feiertagen geringer. Es gibt jedoch immer noch einen deutlichen Einbruch am 23. Januar, der dadurch nicht zu erklären ist. Daher fragen wir als Nächstes das Dataset mit den Wetterdaten ab, um das Wetter in NYC an diesem Tag zu überprüfen:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Ergebnisvisualisierung für das Dataset mit den Wetterdaten](./media/tutorial-data-analyst/weather-data-set-visualization.png)

Die Ergebnisse der Abfrage zeigen, dass der Einbruch bei der Anzahl von Taxifahren auf Folgendes zurückzuführen ist:

- An diesem Tag gab es in NYC einen Schneesturm mit starkem Schneefall (~ 30 cm).
- Es war kalt (Temperatur unter Null Grad Celsius).
- Es war windig (~ 10 m/s).

In diesem Tutorial haben Sie gelernt, wie Datenanalysten schnell eine explorative Datenanalyse durchführen, mithilfe eines serverlosen SQL-Pools verschiedene Datasets miteinander kombinieren und die Ergebnisse mithilfe von Azure Synapse Studio visualisieren können.

## <a name="next-steps"></a>Nächste Schritte

Im Tutorial [Verwenden eines serverlosen SQL-Pools mit Power BI Desktop und Erstellen eines Berichts](tutorial-connect-power-bi-desktop.md) erfahren Sie, wie Sie einen serverlosen SQL-Pool mit Power BI Desktop verbinden und Berichte erstellen.

Informationen dazu, wie externe Tabellen in einem serverlosen SQL-Pool verwendet werden, finden Sie unter [Verwenden externer Tabellen mit Synapse SQL](develop-tables-external-tables.md?tabs=sql-pool).
 
