---
title: Standardspalten in Azure Monitor-Protokolldatensätzen | Microsoft-Dokumentation
description: Beschreibt Spalten, die mehreren Datentypen in Azure Monitor-Protokollen gemein sind.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 2370f76bacb8645f1b343da4f056c8bcf06a26dd
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95796719"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Standardspalten in Azure Monitor-Protokollen
Daten in Azure Monitor-Protokollen werden [als Gruppe von Datensätzen in einem Log Analytics-Arbeitsbereich oder einer Application Insights-Anwendung gespeichert](./data-platform-logs.md). Diese haben jeweils einen bestimmten Datentyp, der über eine eindeutigen Satz von Spalten verfügt. Viele Datentypen weisen Standardspalten auf, die sie mit mehreren Typen gemein haben. In diesem Artikel werden diese Spalten beschrieben, zusammen mit Beispielen für ihre Verwendung in Abfragen.

Arbeitsbereichsbasierte Anwendungen in Application Insights speichern ihre Daten in einem Log Analytics-Arbeitsbereich und verwenden dieselben Standardspalten wie andere Tabellen im Arbeitsbereich. Klassische Anwendungen speichern ihre Daten separat und weisen unterschiedliche Standardspalten auf, wie es in diesem Artikel beschrieben ist.

> [!NOTE]
> Einige der Standardspalten werden in der Schemaansicht oder in IntelliSense in Log Analytics nicht angezeigt und erscheinen auch nicht in Abfrageergebnissen, sofern die Spalte nicht explizit in der Ausgabe angegeben wird.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated und timestamp
Die Spalten **TimeGenerated** (Log Analytics-Arbeitsbereich) und **timestamp** (Application Insights-Anwendung) enthalten das Datum und die Uhrzeit der Erstellung des Datensatzes durch die Datenquelle. Weitere Informationen finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](data-ingestion-time.md).

**TimeGenerated** und **timestamp** bieten eine gemeinsame Spalte, die zum zeitbezogenen Filtern oder Zusammenfassen verwendet werden kann. Wenn Sie im Azure-Portal einen Zeitbereich für eine Ansicht oder ein Dashboard auswählen, wird „TimeGenerated“ oder „timestamp“ zum Filtern der Ergebnisse verwendet. 

### <a name="examples"></a>Beispiele

Die folgende Abfrage gibt die Anzahl der Fehlerereignisse zurück, die für jeden Tag der vorherigen Woche erstellt wurden.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Die folgende Abfrage gibt die Anzahl der Ausnahmen zurück, die für jeden Tag der vorherigen Woche erstellt wurden.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
Die Spalte **\_TimeReceived** enthält den Zeitpunkt (Datum und Uhrzeit), zu dem der Datensatz vom Azure Monitor-Erfassungspunkt in der Azure-Cloud empfangen wurde. Dies kann hilfreich sein, um Probleme im Zusammenhang mit der Wartezeit zwischen Datenquelle und Cloud zu ermitteln. Ein Beispiel wäre etwa ein Netzwerkproblem, das zu einer Verzögerung bei Daten führt, die von einem Agent gesendet werden. Weitere Informationen finden Sie unter [Protokolldatenerfassungszeit in Azure Monitor](data-ingestion-time.md).

Die folgende Abfrage gibt die durchschnittliche Wartezeit (auf Stundenbasis) für Ereignisdatensätze eines Agents zurück. Dies beinhaltet die Zeit zwischen Agent und Cloud sowie die Gesamtzeit bis zur Verfügbarkeit des Datensatzes für Protokollabfragen.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type und itemType
Die Spalten **Type** (Log Analytics-Arbeitsbereich) und **itemType** (Application Insights-Anwendung) enthalten den Namen der Tabelle, aus der der Datensatz abgerufen wurde, der auch als Datensatztyp betrachtet werden kann. Diese Spalte ist bei Abfragen hilfreich, die Datensätze aus mehreren Tabellen kombinieren, z. B. Abfragen mit dem Operator `search`, um zwischen Datensätzen verschiedener Typen zu unterscheiden. An einigen Stellen kann **$table** anstelle von **Type** verwendet werden.

### <a name="examples"></a>Beispiele
Die folgende Abfrage gibt die Anzahl der Datensätze nach Typ zurück, die in der letzten Stunde gesammelt wurden.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
Die Spalte **\_ItemId** enthält einen eindeutigen Bezeichner für den Datensatz.


## <a name="_resourceid"></a>\_ResourceId
Die Spalte **\_ResourceId** enthält einen eindeutigen Bezeichner für die Ressource, der der Datensatz zugeordnet ist. Damit haben Sie eine Standardspalte, die Sie verwenden können, um den Bereich Ihrer Abfrage auf Datensätze aus einer bestimmten Quelle einzuschränken oder verwandte Daten aus mehreren Tabellen zusammenzuführen.

Für Azure-Ressourcen ist der Wert von **_ResourceId** die [Azure-Ressourcen-ID-URL](../../azure-resource-manager/templates/template-functions-resource.md). Die Spalte ist auf Azure-Ressourcen (einschließlich [Azure Arc-Ressourcen](../../azure-arc/overview.md)) oder auf benutzerdefinierte Protokolle beschränkt, bei denen während der Erfassung die Ressourcen-ID angegeben wurde.

> [!NOTE]
> Einige Datentypen verfügen bereits über Felder, die die Azure-Ressourcen-ID oder zumindest Teile davon wie die Abonnement-ID enthalten. Während diese Felder aus Gründen der Abwärtskompatibilität beibehalten werden, wird empfohlen, „_ResourceId“ zu verwenden, um eine Kreuzkorrelation durchzuführen, da sie konsistenter ist.

### <a name="examples"></a>Beispiele
Die folgende Abfrage führt die Leistungs- und Ereignisdaten für die einzelnen Computer zusammen. Sie zeigt alle Ereignisse mit der ID _101_ und einer Prozessorauslastung von mehr als 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Die folgende Abfrage verknüpft die _AzureActivity_-Datensätze mit _SecurityEvent_-Datensätzen. Sie zeigt alle Aktivitätsvorgänge mit Benutzern, die an diesen Computern angemeldet waren.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Mit der folgenden Abfrage werden **_ResourceId** analysiert und abgerechnete Datenvolumen pro Azure-Ressourcengruppe aggregiert.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Verwenden Sie diese `union withsource = tt *`-Abfragen mit Bedacht, da umfassende Scans verschiedener Datentypen kostenintensiv sind.

Es ist immer effizienter, die Spalte „\_SubscriptionId“ zu verwenden, als sie durch Analysieren der Spalte „\_ResourceId“ zu extrahieren.

## <a name="_substriptionid"></a>\_SubstriptionId
Die Spalte **\_SubscriptionId** enthält die Abonnement-ID der Ressource, der der Datensatz zugeordnet ist. Mit dieser Standardspalte können Sie den Bereich Ihrer Abfrage auf Datensätze aus einem bestimmten Abonnement einschränken oder verschiedene Abonnements vergleichen.

Bei Azure-Ressourcen ist der Wert von **__SubscriptionId** der Abonnementteil der [Azure-Ressourcen-ID-URL](../../azure-resource-manager/templates/template-functions-resource.md). Die Spalte ist auf Azure-Ressourcen (einschließlich [Azure Arc-Ressourcen](../../azure-arc/overview.md)) oder auf benutzerdefinierte Protokolle beschränkt, bei denen während der Erfassung die Ressourcen-ID angegeben wurde.

> [!NOTE]
> Einige Datentypen umfassen bereits Felder, die die Azure-Abonnement-ID enthalten. Diese Felder werden aus Gründen der Abwärtskompatibilität beibehalten, es wird jedoch empfohlen, die Spalte „\_SubscriptionId“ zu verwenden, um eine Kreuzkorrelation durchzuführen, da sie konsistenter ist.
### <a name="examples"></a>Beispiele
Mit der folgenden Abfrage werden die Leistungsdaten für Computer eines bestimmten Abonnements überprüft. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

Mit der folgenden Abfrage werden **_ResourceId** analysiert und abgerechnete Datenvolumen pro Azure-Abonnement aggregiert.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Verwenden Sie diese `union withsource = tt *`-Abfragen mit Bedacht, da umfassende Scans verschiedener Datentypen kostenintensiv sind.


## <a name="_isbillable"></a>\_IsBillable
Die Spalte **\_IsBillable** gibt an, ob erfasste Daten gebührenpflichtig sind. Daten, für die **\_IsBillable** gleich `false` ist, werden kostenlos gesammelt und Ihrem Azure-Konto nicht in Rechnung gestellt.

### <a name="examples"></a>Beispiele
Um eine Liste von Computern abzurufen, die kostenpflichtige Datentypen senden, führen Sie die folgende Abfrage aus:

> [!NOTE]
> Verwenden Sie Abfragen mit `union withsource = tt *` mit Bedacht, da Scans über verschiedene Datentypen kostenintensiv sind. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Diese Abfrage kann so erweitert werden, dass die Anzahl von Computern pro Stunde zurückgegeben wird, die kostenpflichtige Datentypen senden:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
Die Spalte **\_BilledSize** gibt die Größe in Datenbytes an, die Ihrem Azure-Konto in Rechnung gestellt wird, wenn **\_IsBillable** gleich „true“ ist.


### <a name="examples"></a>Beispiele
Um die Größe der pro Computer erfassten abrechenbaren Ereignisse anzuzeigen, verwenden Sie die Spalte `_BilledSize`, die die Größe in Bytes bereitstellt:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Verwenden Sie die folgende Abfrage, um die Größe von erfassten abrechenbaren Ereignissen pro Abonnement anzuzeigen:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Verwenden Sie die folgende Abfrage, um die Größe von erfassten abrechenbaren Ereignissen pro Ressourcengruppe anzuzeigen:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Um die Anzahl von erfassten Ereignissen pro Computer anzuzeigen, führen Sie die folgende Abfrage aus:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Um die Anzahl von erfassten abrechenbaren Ereignissen pro Computer anzuzeigen, führen Sie die folgende Abfrage aus: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Verwenden Sie die folgende Abfrage, um die Anzahl von abrechenbaren Datentypen von einem bestimmten Computer anzuzeigen:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zum [Speichern von Azure Monitor-Protokolldaten](../log-query/log-query-overview.md).
- Arbeiten Sie eine Lektion zum [Schreiben von Protokollabfragen ](../log-query/get-started-queries.md) durch.
- Arbeiten Sie eine Lektion zum [Verknüpfen von Tabellen in Protokollabfragen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins) durch.
