---
title: Arbeiten mit Werten für Datum und Uhrzeit in Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Beschreibt das Arbeiten mit Daten für Datum und Uhrzeit in Azure Monitor-Protokollabfragen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 402511ba3c45e8bd12cb7f92ecd54f6084c8ada2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62112356"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Arbeiten mit Werten für Datum und Uhrzeit in Azure Monitor-Protokollabfragen

> [!NOTE]
> Sie sollten zunächst [Erste Schritte mit dem Analytics-Portal](get-started-portal.md) und [Erste Schritte mit Abfragen](get-started-queries.md) lesen, bevor Sie mit diesem Tutorial beginnen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In diesem Artikel wird das Arbeiten mit Daten für Datum und Uhrzeit in Azure Monitor-Protokollabfragen beschrieben.


## <a name="date-time-basics"></a>Grundlagen zu Datum und Uhrzeit
Die Abfragesprache Kusto verfügt über zwei Hauptdatentypen, die Datums- und Uhrzeitangaben zugeordnet sind: datetime und timespan. Alle Datumsangaben werden in UTC ausgedrückt. Es werden zwar mehrere datetime-Formate unterstützt, jedoch wird das ISO8601-Format bevorzugt. 

Zeiträume werden als Dezimalwert gefolgt von einer Zeiteinheit ausgedrückt:

|Kurzform   | Zeiteinheit    |
|:---|:---|
|d           | day          |
|h.           | hour         |
|m           | Minute       |
|s           | Sekunde       |
|ms          | Millisekunde  |
|Mikrosekunde | Mikrosekunde  |
|Takt        | Nanosekunde   |

datetime-Werte können durch Umwandeln einer Zeichenfolge mithilfe des Operators `todatetime` erstellt werden. Beispielsweise verwenden Sie zur Überprüfung der in einer bestimmten Zeitspanne gesendeten VM-Heartbeats den Operator `between`, um einen Zeitraum anzugeben.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Ein weiteres gängiges Szenario besteht in dem Vergleich eines datetime-Werts mit dem vorhandenen Wert. Wenn Sie beispielsweise sämtliche Heartbeats der letzten zwei Minuten anzeigen möchten, können Sie den Operator `now` zusammen mit einem timespan-Wert verwenden, der zwei Minuten darstellt:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Für diese Funktion ist auch eine Verknüpfung verfügbar:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Die kürzeste und am besten lesbare Methode besteht jedoch in der Verwendung des Operators `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Angenommen, Ihnen ist die Start- und Endzeit nicht bekannt, sondern die Startzeit und die Dauer. Sie können die Abfrage wie folgt erneut generieren:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konvertieren von Zeiteinheiten
Sie können einen datetime- oder timespan-Wert in einer anderen als der Standardzeiteinheit ausdrücken. Wenn Sie beispielsweise Fehlerereignisse der letzten 30 Minuten überprüfen und eine berechnete Spalte benötigen, in der angezeigt wird, wie viel Zeit seit Auftreten des Ereignisses vergangen ist, gehen Sie wie folgt vor:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Die Spalte `timeAgo` enthält Werte wie z.B. „00:09:31.5118992“. Dies bedeutet, dass sie im Format „hh:mm:ss.fffffff“ vorliegen. Wenn Sie diese Werte in `numver` von Minuten seit der Startzeit formatieren möchten, teilen Sie diesen Wert durch „1 Minute“:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregationen und Zuordnung von Buckets nach Zeitintervallen
Ein weiteres gängiges Szenario besteht in der Notwendigkeit, Statistiken über einen bestimmten Zeitraum in einem bestimmten Aggregationsintervall abzurufen. Für dieses Szenario kann ein `bin`-Operator als Teil einer Summarize-Klausel verwendet werden.

Mit der folgenden Abfrage können Sie die Anzahl der Ereignisse abrufen, die in der letzten halben Stunde alle 5 Minuten aufgetreten sind:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Diese Abfrage erzeugt die folgende Tabelle:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Eine weitere Möglichkeit zum Erstellen von Buckets für die Ergebnisse besteht in der Verwendung von Funktionen, wie z.B. `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Diese Abfrage erzeugt die folgenden Ergebnisse:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7\.136|
|2018-07-29T00:00:00.000|12\.315|
|2018-07-30T00:00:00.000|16\.847|
|2018-07-31T00:00:00.000|12\.616|
|2018-08-01T00:00:00.000|5\.416|


## <a name="time-zones"></a>Zeitzonen
Da alle datetime-Werte in UTC ausgedrückt werden, ist es häufig hilfreich, diese Werte in die lokale Zeitzone zu konvertieren. Verwenden Sie beispielsweise die folgende Berechnung zum Konvertieren von UTC- in PST-Zeiten:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Zugehörige Funktionen

| Category (Kategorie) | Funktion |
|:---|:---|
| Konvertieren von Datentypen | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Runden eines Werts auf die bin-Größe | [bin](/azure/kusto/query/binfunction) |
| Abrufen einer bestimmten Datums oder einer bestimmten Uhrzeit | [ago](/azure/kusto/query/agofunction) [now](/azure/kusto/query/nowfunction)   |
| Abrufen eines Teils des Werts | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Abrufen eines relativen Datumswerts  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Nächste Schritte
Informationen zur Verwendung der [Abfragesprache Kusto](/azure/kusto/query/) mit Azure Monitor-Protokolldaten finden Sie in folgenden weiteren Lektionen:

- [Zeichenfolgenvorgänge](string-operations.md)
- [Aggregationsfunktionen](aggregations.md)
- [Erweiterte Aggregationen](advanced-aggregations.md)
- [JSON und Datenstrukturen](json-data-structures.md)
- [Schreiben von erweiterten Abfragen](advanced-query-writing.md)
- [Verknüpfungen](joins.md)
- [Diagramme](charts.md)