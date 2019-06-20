---
title: Erweiterte Aggregationen in Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: Beschreibt einige der erweiterten Aggregationsoptionen, die für Azure Monitor-Protokollabfragen verfügbar sind.
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
ms.openlocfilehash: 56e87da0353a41504035a070d4c10bab0dda2279
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551752"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Erweiterte Aggregationen in Azure Monitor-Protokollabfragen

> [!NOTE]
> Vor der Durchführung dieser Lektion sollten Sie [Aggregationen in Azure Monitor-Protokollabfragen](./aggregations.md) lesen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In diesem Artikel werden einige der erweiterten Aggregationsoptionen beschrieben, die für Azure Monitor-Protokollabfragen verfügbar sind.

## <a name="generating-lists-and-sets"></a>Generieren von Listen und Sätzen
Sie können mithilfe von `makelist` Daten nach der Reihenfolge der Werte in einer bestimmten Spalte pivotieren. So sollten Sie beispielsweise die Auftragsereignisse untersuchen, die am häufigsten auf Ihren Computern ausgeführt werden. Sie können die Daten im Wesentlichen nach der Reihenfolge der Ereignis-IDs auf den einzelnen Computern pivotieren. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` generiert eine Liste in der Reihenfolge, in der Daten an sie übergeben wurden. Verwenden Sie zum Sortieren von Ereignissen vom ältesten bis zum neuesten in der Auftragsanweisung `asc` anstelle von `desc`. 

Es ist auch sinnvoll, eine Liste der unterschiedlichen Werte zu erstellen. Diese wird als _Satz_ bezeichnet und kann mit `makeset` generiert werden:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Wie `makelist` kann `makeset` auch mit sortierten Daten ausgeführt werden und generiert die Arrays basierend auf der Reihenfolge der übergebenen Zeilen.

## <a name="expanding-lists"></a>Erweitern von Listen
Der Inverse-Vorgang von `makelist` oder `makeset` lautet `mvexpand` und erweitert eine Liste von Werten zum Separieren von Zeilen. Der Vorgang kann, bei JSON und Array, eine Erweiterung über eine beliebige Anzahl von dynamischen Spalten erzielen. Sie könnten beispielsweise die Tabelle *Heartbeat* auf Lösungen überprüfen, indem Sie Daten von Computern senden, die in der vergangenen Stunde einen Heartbeat gesendet haben:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Lösungen | 
|--------------|----------------------|
| computer1 | „security“, „updates“, „changeTracking“ |
| computer2 | „security“, „updates“ |
| computer3 | „antiMalware“, „changeTracking“ |
| ... | ... |

Mit `mvexpand` können Sie die einzelnen Werte in einer separaten Zeile statt in einer durch Trennzeichen getrennten Liste anzeigen:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Lösungen | 
|--------------|----------------------|
| computer1 | „security“ |
| computer1 | „updates“ |
| computer1 | „changeTracking“ |
| computer2 | „security“ |
| computer2 | „updates“ |
| computer3 | „antiMalware“ |
| computer3 | „changeTracking“ |
| ... | ... |


Anschließend können Sie erneut `makelist` verwenden, um Elemente zu gruppieren. Zeigen Sie dieses Mal die Liste der Computer pro Lösung an:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Lösungen | list_Computer |
|--------------|----------------------|
| „security“ | ["computer1", "computer2"] |
| „updates“ | ["computer1", "computer2"] |
| „changeTracking“ | ["computer1", "computer3"] |
| „antiMalware“ | [„computer3“] |
| ... | ... |

## <a name="handling-missing-bins"></a>Behandeln fehlender Klassen
Eine nützliche Anwendung von `mvexpand` ist die Notwendigkeit, Standardwerte für fehlende Klassen einzugeben. Angenommen beispielsweise, Sie suchen nach der Betriebszeit eines bestimmten Computers, indem Sie dessen Heartbeat untersuchen. Sie möchten auch die Quelle des Heartbeats sehen, die in der Spalte _Kategorie_ zu finden ist. Normalerweise würde eine einfache Summarize-Anweisung wie folgt verwendet:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category (Kategorie) | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt-Agent | 2017-06-06T17:00:00Z | 15 |
| Direkt-Agent | 2017-06-06T18:00:00Z | 60 |
| Direkt-Agent | 2017-06-06T20:00:00Z | 55 |
| Direkt-Agent | 2017-06-06T21:00:00Z | 57 |
| Direkt-Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

In diesen Ergebnissen fehlt jedoch der Bucket, der „2017-06-06T19:00:00Z“ zugeordnet wurde, da für diese Stunde keine Heartbeatdaten vorhanden sind. Mit der Funktion `make-series` können Sie leeren Buckets einen Standardwert zuweisen. Dadurch wird für jede Kategorie eine Zeile mit zwei zusätzlichen Arrayspalten generiert, von denen eine Spalte für Werte und die andere für übereinstimmende Zeitrahmen vorgesehen ist:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category (Kategorie) | count_ | TimeGenerated |
|---|---|---|
| Direkt-Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Das dritte Element des Arrays *count_* ist wie erwartet eine 0 (null). Zudem gibt es im Array _TimeGenerated_ einen übereinstimmenden Zeitrahmen von „2017-06-06T19:00:00.0000000Z“. Dieses Arrayformat ist jedoch schwer zu lesen. Verwenden Sie `mvexpand`, um die Arrays zu erweitern und die gleiche Formatausgabe zu generieren, die von `summarize` generiert wurde:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category (Kategorie) | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direkt-Agent | 2017-06-06T17:00:00Z | 15 |
| Direkt-Agent | 2017-06-06T18:00:00Z | 60 |
| Direkt-Agent | 2017-06-06T19:00:00Z | 0 |
| Direkt-Agent | 2017-06-06T20:00:00Z | 55 |
| Direkt-Agent | 2017-06-06T21:00:00Z | 57 |
| Direkt-Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Einschränken von Ergebnissen auf eine Reihe von Elementen: `let`, `makeset`, `toscalar`, `in`
In einem häufig auftretenden Szenario werden die Namen einiger bestimmter Entitäten basierend auf einer Reihe von Kriterien ausgewählt. Anschließend wird ein anderes Dataset für diese Reihe von Entitäten weiter gefiltert. Sie könnten beispielsweise nach Computern suchen, auf denen bekanntermaßen Updates fehlen, und IP-Adressen identifizieren, die von diesen Computern aufgerufen wurden:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
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