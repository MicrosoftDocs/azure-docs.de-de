---
title: Optimieren von Protokollwarnungsabfragen | Microsoft-Dokumentation
description: Empfehlungen zum Schreiben effizienter Warnungsabfragen
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: e7c9c76816b5d1ee2eedfb7e54645e056906feef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186625"
---
# <a name="optimizing-log-alert-queries"></a>Optimieren von Protokollwarnungsabfragen
In diesem Artikel wird beschrieben, wie Abfragen für [Protokollwarnungen](alerts-unified-log.md) zum Erzielen optimaler Leistung geschrieben und konvertiert werden. Optimierte Abfragen verringern die Latenz und die Auslastung durch Warnungen, die häufig ausgeführt werden.

## <a name="how-to-start-writing-an-alert-log-query"></a>Schreiben einer Warnungsprotokollabfrage

Warnungsabfragen beginnen mit dem [Abfragen der Protokolldaten in Log Analytics](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal), die auf das Problem hinweisen. Unter [Gespeicherte Abfragen in Azure Monitor Log Analytics](../log-query/example-queries.md) können Sie sich anhand von Beispielen mit den Ermittlungsoptionen vertraut machen. Sie können auch [mit dem Schreiben einer eigenen Abfrage beginnen](../log-query/log-analytics-tutorial.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>Abfragen, die auf das Problem und nicht die Warnung hinweisen

Der Warnungsablauf wurde zum Transformieren der Ergebnisse erstellt, die auf das Problem für eine Warnung hinweisen. Ein Beispiel hierfür ist eine Abfrage der folgenden Art:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Wenn die Absicht des Benutzers beim Auftreten dieses Ereignistyps eine Warnung ist, wird von der Warnungslogik `count` an die Abfrage angefügt. Die folgende Abfrage wird ausgeführt:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Es ist nicht erforderlich, der Abfrage Warnungslogik hinzuzufügen. Dies kann sogar zu Problemen führen. Wenn Sie im obigen Beispiel `count` in Ihre Abfrage einfügen, führt dies immer zum Wert 1, weil vom Warnungsdienst `count` für `count` durchgeführt wird.

### <a name="avoid-limit-and-take-operators"></a>Vermeiden der Operatoren `limit` und `take`

Die Verwendung von `limit` und `take` in Abfragen kann die Latenz und die Auslastung durch Warnungen erhöhen, da die Ergebnisse im Laufe der Zeit nicht konsistent sind. Es wird empfohlen, diese nur bei Bedarf zu verwenden.

## <a name="log-query-constraints"></a>Beschränkungen für Protokollabfragen
[Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md) beginnen entweder mit einer Tabelle, einem [`search`](/azure/kusto/query/searchoperator)- oder einem [`union`](/azure/kusto/query/unionoperator)-Operator.

Abfragen für Protokollwarnungsregeln sollten immer mit einer Tabelle beginnen, um einen klaren Bereich festzulegen. Dadurch verbessern sich die Abfrageleistung und die Relevanz der Ergebnisse. Abfragen in Warnungsregeln werden häufig ausgeführt. Daher kann die Verwendung von `search` und `union` zu übermäßigem Mehraufwand und somit zu größerer Latenz für die Warnung führen, da eine Überprüfung mehrerer Tabellen erforderlich ist. Diese Operatoren verringern auch die Fähigkeit des Warnungsdiensts zur Optimierung der Abfrage.

Das Erstellen oder Ändern von Protokollwarnungsregeln, die `search`- oder `union`-Operatoren verwenden, wird außer für ressourcenübergreifende Abfragen nicht unterstützt.

Beispielsweise ist die folgende Warnungsabfrage auf die Tabelle _SecurityEvent_ beschränkt und sucht nach einer bestimmten Ereignis-ID. Dies ist die einzige Tabelle, die von der Abfrage verarbeitet werden muss.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Protokollwarnungsregeln mit [ressourcenübergreifenden Abfragen](../log-query/cross-workspace-query.md) sind von dieser Änderung nicht betroffen, da ressourcenübergreifende Abfragen einen `union`-Typ verwenden, durch den der Abfragebereich auf bestimmte Ressourcen beschränkt ist. Das folgende Beispiel ist eine gültige Protokollwarnungsabfrage:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> [Ressourcenübergreifende Abfragen](../log-query/cross-workspace-query.md) werden in der neuen [scheduledQueryRules-API](/rest/api/monitor/scheduledqueryrules) unterstützt. Wenn Sie noch die [Legacywarnungs-API von Log Analytics](api-alerts.md) zum Erstellen von Protokollwarnungen verwenden, finden Sie [hier](alerts-log-api-switch.md) Informationen für einen Wechsel.

## <a name="examples"></a>Beispiele
Die folgenden Beispiele umfassen Protokollabfragen mit `search` und `union` und enthalten Schritte, mit denen Sie diese Abfragen für die Verwendung in Warnungsregeln ändern können.

### <a name="example-1"></a>Beispiel 1
Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die Leistungsinformationen mithilfe von `search` abruft: 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

Zum Ändern dieser Abfrage beginnen Sie mit der folgenden Abfrage, um die Tabelle anzugeben, der die Eigenschaften angehören:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

Das Ergebnis dieser Abfrage zeigt, dass die Eigenschaft _CounterName_ aus der Tabelle _Perf_ stammt.

Basierend auf diesem Ergebnis können Sie die folgende Abfrage erstellen, die Sie für die Warnungsregel verwenden:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>Beispiel 2
Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die Leistungsinformationen mithilfe von `search` abruft: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

Zum Ändern dieser Abfrage beginnen Sie mit der folgenden Abfrage, um die Tabelle anzugeben, der die Eigenschaften angehören:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

Das Ergebnis dieser Abfrage zeigt, dass die Eigenschaften _ObjectName_ und _CounterName_ aus der Tabelle _Perf_ stammen.

Basierend auf diesem Ergebnis können Sie die folgende Abfrage erstellen, die Sie für die Warnungsregel verwenden:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>Beispiel 3

Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die sowohl `search` als auch `union` zum Abrufen von Leistungsinformationen verwendet: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

Zum Ändern dieser Abfrage beginnen Sie mit der folgenden Abfrage, um die Tabelle anzugeben, der die Eigenschaften im ersten Teil der Abfrage angehören: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

Das Ergebnis dieser Abfrage zeigt, dass alle diese Eigenschaften aus der Tabelle _Perf_ stammen.

Verwenden Sie jetzt `union` mit dem Befehl `withsource`, um anzugeben, welche Quelltabelle die jeweilige Zeile beigetragen hat.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

Das Ergebnis dieser Abfrage zeigt, dass diese Eigenschaften ebenfalls aus der Tabelle _Perf_ stammen.

Basierend auf diesen Ergebnissen können Sie die folgende Abfrage erstellen, die Sie für die Warnungsregel verwenden: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>Beispiel 4
Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die die Ergebnisse von zwei `search`-Abfragen verknüpft:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

Zum Ändern der Abfrage beginnen Sie mit der folgenden Abfrage, um die Tabelle anzugeben, die die Eigenschaften auf der linken Seite der Verknüpfung enthält: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Das Ergebnis zeigt, dass die Eigenschaften auf der linken Seite der Verknüpfung zur Tabelle _SecurityEvent_ gehören. 

Verwenden Sie jetzt die folgende Abfrage, um die Tabelle anzugeben, die die Eigenschaften auf der rechten Seite der Verknüpfung enthält: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Das Ergebnis zeigt, dass die Eigenschaften auf der rechten Seite der Verknüpfung zur Tabelle _Heartbeat_ gehören.

Basierend auf diesen Ergebnissen können Sie die folgende Abfrage erstellen, die Sie für die Warnungsregel verwenden: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Protokollwarnungen](alerts-log.md) in Azure Monitor.
- Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md).