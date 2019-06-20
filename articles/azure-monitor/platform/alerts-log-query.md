---
title: Abfragen von Protokollwarnungen in Azure Monitor | Microsoft-Dokumentation
description: Enthält Empfehlungen zum Schreiben effizienter Abfragen für Protokollwarnungen in Azure Monitor-Updates und einen Prozess zum Konvertieren vorhandener Abfragen.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60995960"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Abfragen von Protokollwarnungen in Azure Monitor
[Warnungsregeln, die auf Azure Monitor-Protokollen basieren](alerts-unified-log.md), werden in regelmäßigen Abständen ausgeführt. Sie sollten deshalb sicherstellen, dass sie so geschrieben sind, dass Mehraufwand und Latenz minimiert werden. Dieser Artikel enthält Empfehlungen zum Schreiben effizienter Abfragen für Protokollwarnungen und einen Prozess zum Konvertieren vorhandener Abfragen. 

## <a name="types-of-log-queries"></a>Typen von Protokollabfragen
[Protokollabfragen in Azure Monitor](../log-query/log-query-overview.md) beginnen entweder mit einer Tabelle oder einem [search](/azure/kusto/query/searchoperator)- oder [union](/azure/kusto/query/unionoperator)-Operator.

Beispielsweise ist die folgende Abfrage auf die Tabelle _SecurityEvent_ beschränkt und sucht nach einer bestimmten Ereignis-ID. Dies ist die einzige Tabelle, die von der Abfrage verarbeitet werden muss.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Mithilfe von Abfragen, die mit `search` oder `union` beginnen, können Sie übergreifend mehrere Spalten in einer Tabelle oder sogar mehrere Tabellen durchsuchen. Die folgenden Beispiele zeigen verschiedene Methoden zum Suchen nach dem Begriff _Memory_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Zwar sind `search` und `union` beim Durchsuchen von Daten hilfreich, da Begriffe im gesamten Datenmodell gesucht werden, doch sind sie nicht so effizient wie eine Tabelle, weil mehrere Tabellen durchsucht werden müssen. Da Abfragen in Warnungsregeln in regelmäßigen Abständen ausgeführt werden, kann dies zu übermäßigem Mehraufwand und somit zu größerer Latenz für die Warnung führen. Aufgrund dieses Mehraufwands sollten Abfragen für Protokollwarnungsregeln in Azure immer mit einer Tabelle beginnen, um einen klaren Bereich festzulegen. Dadurch verbessern sich die Abfrageleistung und die Relevanz der Ergebnisse.

## <a name="unsupported-queries"></a>Nicht unterstützte Abfragen
Ab dem 11. Januar 2019 wird das Erstellen oder Ändern von Protokollwarnungsregeln mit den Operatoren `search` oder `union` im Azure-Portal nicht mehr unterstützt. Bei Verwendung dieser Operatoren in einer Warnungsregel wird eine Fehlermeldung zurückgegeben. Vorhandene Warnungsregeln und Warnungsregeln, die mit der Log Analytics-API erstellt und bearbeitet werden, sind von dieser Änderung nicht betroffen. Sie sollten dennoch überlegen, ob Sie Warnungsregeln mit diesen Abfragetypen ändern, um deren Effizienz zu steigern.  

Protokollwarnungsregeln mit [ressourcenübergreifenden Abfragen](../log-query/cross-workspace-query.md) sind von dieser Änderung nicht betroffen, da ressourcenübergreifende Abfragen `union` verwenden, wodurch der Abfragebereich auf bestimmte Ressourcen beschränkt ist. Dies entspricht nicht dem `union *`-Operator, der nicht verwendet werden.  Das folgende Beispiel ist in einer Protokollwarnungsregel gültig:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Eine [ressourcenübergreifende Abfrage](../log-query/cross-workspace-query.md) in Protokollwarnungen wird in der neuen [scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) unterstützt. Standardmäßig verwendet Azure Monitor die [Legacywarnungs-API von Log Analytics](api-alerts.md) zum Erstellen neuer Protokollwarnungsregeln über das Azure-Portal, es sei denn, Sie nehmen einen Umstieg von der [Legacyprotokollwarnungen-API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) vor. Nach dem Umstieg wird die neue API standardmäßig für neue Warnungsregeln im Azure-Portal verwendet, und Sie können Protokollwarnungsregeln für ressourcenübergreifende Abfragen erstellen. Sie können Protokollwarnungsregeln für [ressourcenübergreifende Abfragen](../log-query/cross-workspace-query.md) erstellen, ohne den Umstieg vorzunehmen, indem Sie die [ARM-Vorlage für die scheduledQueryRules-API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) verwenden. Diese Warnungsregel wird jedoch über die [ scheduledQueryRules-API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) und nicht über das Azure-Portal verwaltet.

## <a name="examples"></a>Beispiele
Die folgenden Beispiele umfassen Protokollabfragen mit `search` und `union` und enthalten Schritte, mit denen Sie diese Abfragen für die Verwendung mit Warnungsregeln ändern können.

### <a name="example-1"></a>Beispiel 1
Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die Leistungsinformationen mithilfe von `search` abruft: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Zum Ändern dieser Abfrage beginnen Sie mit der folgenden Abfrage, um die Tabelle anzugeben, der die Eigenschaften angehören:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Das Ergebnis dieser Abfrage zeigt, dass die Eigenschaft _CounterName_ aus der Tabelle _Perf_ stammt. 

Basierend auf diesem Ergebnis können Sie die folgende Abfrage erstellen, die Sie für die Warnungsregel verwenden:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Beispiel 2
Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die Leistungsinformationen mithilfe von `search` abruft: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
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
| count 
```
 

### <a name="example-3"></a>Beispiel 3

Sie möchten eine Protokollwarnungsregel mit der folgenden Abfrage erstellen, die sowohl `search` als auch `union` zum Abrufen von Leistungsinformationen verwendet: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Zum Ändern dieser Abfrage beginnen Sie mit der folgenden Abfrage, um die Tabelle anzugeben, der die Eigenschaften im ersten Teil der Abfrage angehören: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Das Ergebnis dieser Abfrage zeigt, dass alle diese Eigenschaften aus der Tabelle _Perf_ stammen. 

Verwenden Sie jetzt `union` mit dem Befehl `withsource`, um anzugeben, welche Quelltabelle die jeweilige Zeile beigetragen hat.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
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
| count 
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
)  
on Hour 
| count 
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

 
Das Ergebnis zeigt, dass die Eigenschaften auf der rechten Seite der Verknüpfung zur Tabelle „Heartbeat“ gehören. 

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
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Protokollwarnungen](alerts-log.md) in Azure Monitor.
- Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md).

