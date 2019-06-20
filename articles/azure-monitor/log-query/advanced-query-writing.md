---
title: Erweiterte Abfragen in Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel enthält ein Tutorial, in dem das Schreiben von Abfragen in Azure Monitor über das Analytics-Portal veranschaulicht wird.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 65713ed9c2d0635e776a7a7e5f205b6d55438ed4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589591"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Schreiben erweiterter Abfragen in Azure Monitor

> [!NOTE]
> Sie sollten zunächst [Erste Schritte mit Azure Monitor Log Analytics](get-started-portal.md) und [Erste Schritte mit Abfragen](get-started-queries.md) lesen, bevor Sie mit dieser Lektion beginnen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Wiederverwenden von Code mit let
Sie können mit `let` einer Variable Ergebnisse zuweisen und später in der Abfrage darauf verweisen:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Sie können Variablen auch konstante Werte zuweisen. Dies unterstützt eine Methode zum Einrichten von Parametern für die Felder, die Sie bei jeder Ausführung der Abfrage ändern müssen. Ändern Sie die Parameter nach Bedarf. Geben Sie beispielsweise zum Berechnen des freien Speicherplatzes auf dem Datenträger und des freien Arbeitsspeichers (in Perzentil) in einem bestimmten Zeitfenster Folgendes ein:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Dies erleichtert Ihnen das Ändern der Start- und Endzeit bei der nächsten Ausführung der Abfrage.

### <a name="local-functions-and-parameters"></a>Lokale Funktionen und Parameter
Sie können mit `let`-Anweisungen Funktionen erstellen, die in derselben Abfrage verwendet werden können. Definieren Sie beispielsweise eine Funktion, die ein datetime-Feld (im UTC-Format) auswählt und in ein US-Standardformat konvertiert. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>print
`print` gibt eine Tabelle mit einer einzelnen Spalte und einer einzelnen Zeile zurück, in der das Ergebnis einer Berechnung angezeigt wird. Diese Funktion wird häufig in Fällen verwendet, in denen Sie eine einfache Berechnung benötigen. Geben Sie beispielsweise Folgendes ein, um die aktuelle Uhrzeit in PST zu finden und eine Spalte mit EST hinzuzufügen:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>datatable
`datatable` ermöglicht Ihnen das Definieren einer Reihe von Daten. Sie geben ein Schema und eine Reihe von Werten an und übergeben die Tabelle anschließend in beliebige andere Abfrageelemente. Geben Sie beispielsweise Folgendes ein, um eine Tabelle zur RAM-Nutzung zu erstellen und den Durchschnittswert pro Stunde zu berechnen:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

datatable-Konstrukte sind zudem bei der Erstellung einer Nachschlagetabelle sehr nützlich. Erstellen Sie beispielsweise zum Zuordnen von Tabellendaten, wie z.B. Ereignis-IDs, aus der Tabelle _SecurityEvent_ zu anderswo aufgeführten Ereignistypen mithilfe von `datatable` eine Nachschlagetabelle mit den Ereignistypen, und verknüpfen Sie diese Datentabelle mit _SecurityEvent_-Daten:

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Nächste Schritte
Informationen zur Verwendung der [Abfragesprache Kusto](/azure/kusto/query/) mit Azure Monitor-Protokolldaten finden Sie in folgenden weiteren Lektionen:

- [Zeichenfolgenvorgänge](string-operations.md)
- [Datums- und Uhrzeitvorgänge](datetime-operations.md)
- [Aggregationsfunktionen](aggregations.md)
- [Erweiterte Aggregationen](advanced-aggregations.md)
- [JSON und Datenstrukturen](json-data-structures.md)
- [Verknüpfungen](joins.md)
- [Diagramme](charts.md)
