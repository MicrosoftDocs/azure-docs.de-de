---
title: Erstellen von Grafiken und Diagrammen aus Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Beschreibt verschiedene Visualisierungen in Azure Monitor, um Ihre Protokolldaten auf verschiedene Weise anzuzeigen.
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
ms.openlocfilehash: 07d0866bd697587da170a00e8077a57035989d32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60594024"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Erstellen von Grafiken und Diagrammen aus Azure Monitor-Protokollabfragen

> [!NOTE]
> Vor der Durchführung dieser Lektion sollten Sie [Erweiterte Aggregationen in Azure Monitor-Protokollabfragen](advanced-aggregations.md) lesen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In diesem Artikel sind verschiedene Visualisierungen in Azure Monitor beschrieben, um Ihre Protokolldaten unterschiedlich anzuzeigen.

## <a name="charting-the-results"></a>Darstellen der Ergebnisse in Diagrammen
Beginnen Sie mit der Überprüfung, wie viele Computer pro Betriebssystem während der letzten Stunde vorhanden waren:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Standardmäßig werden Ergebnisse als Tabelle angezeigt:

![Table](media/charts/table-display.png)

Wählen Sie **Chart** (Diagramm) und dann die Option **Pie** (Kreisdiagramm), um die Ergebnisse anzuzeigen, damit Sie eine bessere Ansicht erhalten:

![Kreisdiagramm](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Zeitdiagramme
Zeigen Sie die durchschnittliche, 50. und 95. Quantile der Prozessorzeit in Containern von einer Stunde an. Die Abfrage generiert mehrere Reihen. Sie können auswählen, welche Reihe im Zeitdiagramm angezeigt wird.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wählen Sie die Anzeigeoption **Liniendiagramm** aus:

![Liniendiagramm](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Bezugslinie

Eine Bezugslinie kann Ihnen dabei helfen, einfach zu identifizieren, ob die Metrik einen bestimmten Schwellenwert überschritten hat. Erweitern Sie das Dataset mit einer konstanten Spalte, um dem Diagramm eine Zeile hinzuzufügen:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Bezugslinie](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Mehrere Dimensionen
Mit mehreren Ausdrücken in der `by`-Klausel von `summarize` werden mehrere Zeilen in den Ergebnissen erstellt, und zwar eine für jede Kombination der Werte.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Wenn Sie die Ergebnisse als Diagramm anzeigen, wird die erste Zeile aus der `by`-Klausel verwendet. Im folgenden Beispiel ist ein Diagramm mit gestapelten Säulen mithilfe der _EventID_ dargestellt. Dimensionen müssen vom Typ `string` sein. In diesem Beispiel wird die _EventID_ also in eine Zeichenfolge umgewandelt. 

![Balkendiagramm, EventID](media/charts/charts-and-diagrams-multiDimension1.png)

Sie können zwischen diesen wechseln, indem Sie die Dropdownliste mit dem Spaltennamen auswählen. 

![Balkendiagramm, AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Nächste Schritte
Informationen zur Verwendung der [Abfragesprache Kusto](/azure/kusto/query/) mit Azure Monitor-Protokolldaten finden Sie in folgenden weiteren Lektionen:

- [Zeichenfolgenvorgänge](string-operations.md)
- [Datums- und Uhrzeitvorgänge](datetime-operations.md)
- [Aggregationsfunktionen](aggregations.md)
- [Erweiterte Aggregationen](advanced-aggregations.md)
- [JSON und Datenstrukturen](json-data-structures.md)
- [Schreiben von erweiterten Abfragen](advanced-query-writing.md)
- [Verknüpfungen](joins.md)