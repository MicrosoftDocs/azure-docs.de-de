---
title: Aggregationen in Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Beschreibt die Aggregationsfunktionen in Azure Monitor-Protokollabfragen, die nützliche Methoden zum Analysieren von Daten bieten.
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
ms.openlocfilehash: fd8e886a78d0689ca60d8ea7c4d16639c81d5733
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602730"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Aggregationen in Azure Monitor-Protokollabfragen

> [!NOTE]
> Sie sollten zunächst [Erste Schritte mit dem Analytics-Portal](get-started-portal.md) und [Erste Schritte mit Abfragen](get-started-queries.md) lesen, bevor Sie mit diesem Tutorial beginnen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Dieser Artikel beschreibt die Aggregationsfunktionen in Azure Monitor-Protokollabfragen, die nützliche Methoden zum Analysieren von Daten bieten. Diese Funktionen arbeiten alle mit dem `summarize`-Operator, der eine Tabelle mit aggregierten Ergebnissen der Eingabetabelle erzeugt.

## <a name="counts"></a>Anzahl

### <a name="count"></a>count
Zählen Sie die Anzahl der Zeilen im Resultset, nachdem alle Filter angewendet wurden. Das folgende Beispiel gibt die Gesamtanzahl der Zeilen in der Tabelle _Perf_ der letzten 30 Minuten aus. Das Ergebnis wird in einer Spalte namens *count_* zurückgegeben, sofern Sie keinen bestimmten Namen zuweisen:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Eine Zeitdiagrammvisualisierung kann hilfreich sein, um im Zeitverlauf einen Trend zu erkennen:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Die Ausgabe in diesem Beispiel zeigt die Trendlinie der Anzahl der Perf-Datensätze in Intervallen von 5 Minuten:

![Anzahl der Trends](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Verwenden Sie `dcount` und `dcountif` zum Zählen von bestimmten Werten in einer bestimmten Spalte. Die folgende Abfrage wertet aus, wie viele unterschiedliche Computer in der letzten Stunde Takte gesendet haben:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Verwenden Sie `dcountif`, um nur die Linux-Computer zu zählen, die Heartbeats gesendet haben:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Auswerten von Untergruppen
Verwenden Sie das Schlüsselwort `by`, um eine Zählung oder Aggregationen auf Untergruppen in Ihren Daten auszuführen. Beispielsweise um die Anzahl der unterschiedlichen Linux-Computer zu zählen, die in jedem Land bzw. jeder Region Heartbeats gesendet haben:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|USA    | 19                  |
|Kanada           | 3                   |
|Irland          | 0                   |
|Vereinigtes Königreich   | 0                   |
|Niederlande      | 2                   |


Um noch kleineren Untergruppen von Daten zu analysieren, fügen Sie zusätzliche Spaltennamen in den Abschnitt `by` ein. Beispielsweise möchten Sie die unterschiedlichen Computer von jedem Land bzw. jeder Region pro OSType zählen:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Quantile und Varianz
Beim Auswerten von numerischen Werten ist es eine gängige Methode mithilfe von `summarize avg(expression)` den Mittelwert zu ermitteln. Durchschnittswerte werden von Extremwerten beeinflusst, die nur in wenigen Fällen vorkommen. Um dieses Problem zu beheben, können Sie weniger empfindliche Funktionen wie z.B. `median` oder `variance` verwenden.

### <a name="percentile"></a>Perzentil
Um den Median zu ermitteln, verwenden Sie die `percentile`-Funktion mit einem Wert zum Angeben des Quantils:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Sie können auch verschiedene Quantile angeben, um für jedes ein aggregiertes Ergebnis zu erhalten:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Dadurch wird möglicherweise deutlich, dass einige CPUs des Computers einen ähnlichen Medianwert haben. Während sich dieser bei manchen stabil in der Nähe des Medians bewegt, melden andere viel niedrigere und höhere CPU-Werte, die von Spitzen verursacht wurden.

### <a name="variance"></a>Variance
Um die Varianz eines Werts direkt zu evaluieren, verwenden Sie die Standardabweichung und Varianzmethoden:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Eine gute Möglichkeit zur Analyse der Stabilität der CPU-Auslastung besteht darin, stdev mit der Berechnung des Medians zu kombinieren:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Informationen zur Verwendung der [Abfragesprache Kusto](/azure/kusto/query/) mit Azure Monitor-Protokolldaten finden Sie in folgenden weiteren Lektionen:

- [Zeichenfolgenvorgänge](string-operations.md)
- [Datums- und Uhrzeitvorgänge](datetime-operations.md)
- [Erweiterte Aggregationen](advanced-aggregations.md)
- [JSON und Datenstrukturen](json-data-structures.md)
- [Schreiben von erweiterten Abfragen](advanced-query-writing.md)
- [Verknüpfungen](joins.md)
- [Diagramme](charts.md)
