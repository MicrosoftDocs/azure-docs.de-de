---
title: 'Auffüllen von Zeitlücken und Ergänzen von fehlenden Werten: Azure SQL Edge'
description: Erfahren Sie, wie Sie in Azure SQL Edge Zeitlücken auffüllen und fehlende Werte ergänzen.
keywords: SQL Edge, Zeitreihe
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185588"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Auffüllen von Zeitlücken und Ergänzen von fehlenden Werten 

Wenn Zeitreihendaten verarbeitet werden, ist es häufig möglich, dass in den Zeitreihendaten Werte für die Attribute fehlen. Es kann auch vorkommen, dass es wegen der Art der Daten oder aufgrund von Unterbrechungen bei der Datensammlung *Zeitlücken* im Dataset gibt.

Wenn z. B. Daten zum Energieverbrauch für ein intelligentes Gerät gesammelt werden, gibt es immer dann, wenn das Gerät nicht verwendet wird, Lücken in den Verbrauchsdaten. In ähnlicher Weise ist es in einem Szenario zur Erfassung von Maschinentelemetriedaten möglich, dass die verschiedenen Sensoren so konfiguriert sind, dass sie Daten in unterschiedlichen Frequenzen senden, was zu fehlenden Werten für die Sensoren führt. Wenn beispielsweise zwei Sensoren (für Spannung und Druck) vorhanden sind, die für eine 100-Hz- bzw. 10-Hz-Frequenz konfiguriert sind, gibt der Spannungssensor jede Hundertstelsekunde Daten aus, während der Drucksensor nur jede Zehntelsekunde Daten ausgibt.

In der folgenden Tabelle ist ein Maschinentelemetriedataset beschrieben, das in einem Eine-Sekunde-Intervall erfasst wurde. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Es gibt zwei wichtige Merkmale des voranstehenden Datasets. 

- Das Dataset enthält keine Datenpunkte für die Zeitstempel `2020-09-07 06:14:47.000`, `2020-09-07 06:14:48.000`, `2020-09-07 06:14:50.000`, `2020-09-07 06:14:53.000` und `2020-09-07 06:14:55.000`. Diese Zeitstempel sind *Lücken* im Dataset.  
- Für die Spannungs- und Druckmesswerte gibt es fehlende Werte, die als `null` dargestellt sind. 

## <a name="gap-filling"></a>Auffüllen von Lücken 

Das Auffüllen von Lücken ist eine Technik, mit der ein zusammenhängender, geordneter Satz von Zeitstempeln erstellt werden kann, um die Analyse von Zeitreihendaten zu vereinfachen. In Azure SQL Edge besteht die einfachste Möglichkeit, Lücken im Zeitreihendataset aufzufüllen, darin, eine temporäre Tabelle mit der gewünschten Zeitverteilung zu definieren und dann einen `LEFT OUTER JOIN`- oder `RIGHT OUTER JOIN`-Vorgang für die Datasettabelle auszuführen. 

Wenn die oben dargestellten `MachineTelemetry`-Daten als Beispiel verwendet werden, kann die folgende Abfrage verwendet werden, um einen zusammenhängenden geordneten Satz von Zeitstempeln für die Analyse zu generieren. 

> [!NOTE]
> In der folgenden Abfrage werden die fehlenden Zeilen mit den Zeitstempelwerten und `null`-Werten für die Attribute generiert. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
Die obige Abfrage erzeugt die folgende Ausgabe, die alle *sekündlichen* Zeitstempel im angegebenen Bereich enthält.

Im Folgenden wird das Resultset aufgeführt.

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Ergänzen von fehlenden Werten

Die vorherige Abfrage hat die fehlenden Zeitstempel für die Datenanalyse generiert, hat aber keinen der fehlenden Werte (dargestellt als „null“) für `voltage`- und `pressure`-Messwerte ersetzt. In Azure SQL Edge wurde den T-SQL-Funktionen `LAST_VALUE()` und `FIRST_VALUE()` eine neue Syntax hinzugefügt, die Mechanismen bereitstellt, um fehlende Werte anhand der vorangehenden oder folgenden Werte im Dataset zu ergänzen. 

Mit der neuen Syntax werden den Funktionen `LAST_VALUE()` und `FIRST_VALUE()` die Klauseln `IGNORE NULLS` und `RESPECT NULLS` hinzugefügt. In der folgenden Abfrage für das `MachineTelemetry`-Dataset werden die fehlenden Werte mit der LAST_VALUE-Funktion berechnet, wobei fehlende Werte durch den letzten beobachteten Wert im Dataset ersetzt werden.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Im Folgenden wird das Resultset aufgeführt.

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

In der folgenden Abfrage werden die fehlenden Werte über die Funktionen `LAST_VALUE()` und `FIRST_VALUE` ergänzt. In der Ausgabespalte `ImputedVoltage` werden die fehlenden Werte durch den letzten beobachteten Wert ersetzt, während für die Ausgabespalte `ImputedPressure` die fehlenden Werte durch den nächsten beobachteten Wert im Dataset ersetzt werden. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Im Folgenden wird das Resultset aufgeführt.

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> In der obigen Abfrage wird die `FIRST_VALUE()`-Funktion verwendet, um fehlende Werte durch den nächsten beobachteten Wert zu ersetzen. Dasselbe Ergebnis kann erzielt werden, indem die `LAST_VALUE()`-Funktion mit einer `ORDER BY <ordering_column> DESC`-Klausel verwendet wird.

## <a name="next-steps"></a>Nächste Schritte 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Aggregatfunktionen (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)