---
title: Optimieren von Protokollabfragen in Azure Monitor
description: Bewährte Methoden für das Optimieren von Protokollabfragen in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: a817c12a367d7c14f693389920e49b368a35cc06
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522871"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optimieren von Protokollabfragen in Azure Monitor
Azure Monitor-Protokolle verwendet [Azure Data Explorer (ADX)](/azure/data-explorer/), um Protokolldaten zu speichern und Abfragen zum Analysieren dieser Daten auszuführen. Es werden die ADX-Cluster für Sie erstellt, verwaltet und gepflegt sowie für Ihre Protokollanalyse-Workload optimiert. Wenn Sie eine Abfrage ausführen, wird diese optimiert und an den entsprechenden ADX-Cluster weitergeleitet, der die Arbeitsbereichsdaten speichert. Sowohl Azure Monitor-Protokolle als auch Azure Data Explorer nutzen eine Vielzahl automatischer Mechanismen zur Abfrageoptimierung. Automatische Optimierungen bieten bereits eine deutliche Steigerung, aber in einigen Fällen können Sie die Abfrageleistung erheblich verbessern. In diesem Artikel werden Leistungsaspekte sowie verschiedene Verfahren zur Behebung von Leistungsproblemen erläutert.

Die meisten der Verfahren gelten für Abfragen, die direkt in Azure Data Explorer und Azure Monitor-Protokolle ausgeführt werden. Es gibt jedoch einige Aspekte, die nur auf Azure Monitor-Protokolle zutreffen und hier erläutert werden. Weitere Tipps zur Optimierung von Azure Data Explorer finden Sie unter [Best Practices für Abfragen](/azure/kusto/query/best-practices).

Optimierte Abfragen bieten folgende Vorteile:

- Abfragen werden schneller ausgeführt, wodurch sich deren Gesamtausführungsdauer verringert.
- Die Wahrscheinlichkeit einer Drosselung oder Ablehnung ist geringer.

Beachten Sie insbesondere Abfragen, die für die wiederholte Verwendung und die Nutzung mit Spitzen eingesetzt werden, wie z. B. Dashboards, Warnungen, Logic Apps und Power BI. Die Auswirkung einer ineffektiven Abfrage ist in diesen Fällen beträchtlich.

## <a name="query-performance-pane"></a>Abfrageleistungsbereich
Nachdem Sie eine Abfrage in Log Analytics ausgeführt haben, klicken Sie über den Abfrageergebnissen auf den Pfeil nach unten, um den Abfrageleistungsbereich anzuzeigen, in dem die Ergebnisse mehrerer Leistungsindikatoren für die Abfrage angezeigt werden. Diese Leistungsindikatoren werden im folgenden Abschnitt beschrieben.

![Abfrageleistungsbereich](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Abfrageleistungsindikatoren

Die folgenden Abfrageleistungsindikatoren sind für jede ausgeführte Abfrage verfügbar:

- [CPU gesamt](#total-cpu): Gesamtcomputezeit, die zum Verarbeiten der Abfrage auf allen Computeknoten benötigt wurde. Dies stellt die Zeit dar, die für die Berechnung, die Analyse und den Datenabruf aufgewendet wurde. 

- [Für die verarbeitete Abfrage verwendete Daten](#data-used-for-processed-query): Gesamtdaten, auf die zum Verarbeiten der Abfrage zugegriffen wurde. Beeinflusst durch die Größe der Zieltabelle, die verwendete Zeitspanne, die angewendeten Filter und die Anzahl der referenzierten Spalten.

- [Zeitspanne der verarbeiteten Abfrage](#time-span-of-the-processed-query): Der Abstand zwischen den neuesten und den ältesten Daten, auf die zum Verarbeiten der Abfrage zugegriffen wurde. Beeinflusst durch den für die Abfrage angegebenen expliziten Zeitbereich.

- [Alter der verarbeiteten Daten](#age-of-processed-data): Der Abstand zwischen dem jetzigen Zeitpunkt und den ältesten Daten, auf die zum Verarbeiten der Abfrage zugegriffen wurde. Dies wirkt sich stark auf die Effizienz beim Datenabruf aus.

- [Anzahl von Arbeitsbereichen](#number-of-workspaces): Die Anzahl der Arbeitsbereiche, auf die während der Abfrageverarbeitung aufgrund impliziter oder expliziter Auswahl zugegriffen wurde.

- [Anzahl von Regionen](#number-of-regions): Die Anzahl der Regionen, auf die während der Abfrageverarbeitung aufgrund impliziter oder expliziter Auswahl von Arbeitsbereichen zugegriffen wurde. Abfragen in mehreren Regionen sind wesentlich weniger effizient, und Leistungsindikatoren bieten nur teilweise Abdeckung.

- [Parallelität](#parallelism): Gibt an, inwieweit das System diese Abfrage auf mehreren Knoten ausführen konnte. Nur für Abfragen mit hoher CPU-Auslastung relevant. Beeinflusst durch die Verwendung bestimmter Funktionen und Operatoren.


## <a name="total-cpu"></a>CPU gesamt
Die tatsächliche CPU-Computezeit, die für die Verarbeitung dieser Abfrage auf allen Abfrageverarbeitungsknoten aufgewendet wurde. Da die meisten Abfragen auf einer großen Anzahl von Knoten ausgeführt werden, ist dieser Wert in der Regel viel höher als die tatsächliche Ausführungsdauer der Abfrage. 

Abfragen, die mehr als 100 CPU-Sekunden nutzen, werden als Abfragen mit übermäßigem Ressourcenverbrauch betrachtet. Abfragen, die mehr als 1.000 CPU-Sekunden nutzen, werden als missbräuchliche Abfragen betrachtet und möglicherweise gedrosselt.

Die Abfrageverarbeitungszeit beinhaltet Folgendes:
- Datenabruf: Das Abrufen alter Daten beansprucht mehr Zeit als das Abrufen neuer Daten.
- Datenverarbeitung: Logik und Auswertung der Daten. 

Abgesehen von der Zeit, die für die Abfrageverarbeitungsknoten aufgewendet wird, gibt es zusätzliche Zeit, die von Azure Monitor-Protokolle für Folgendes benötigt wird: Authentifizieren des Benutzers und Überprüfen, ob dieser auf diese Daten zugreifen darf, Suchen des Datenspeichers, Analysieren der Abfrage und Zuordnen der Abfrageverarbeitungsknoten. Diese Zeit ist in der CPU-Gesamtzeit der Abfrage nicht enthalten.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Frühes Filtern von Datensätzen vor der Verwendung von hohen CPU-Funktionen

Einige der Abfragebefehle und -funktionen bewirken eine hohe CPU-Auslastung. Dies gilt insbesondere für Befehle, mit denen JSON und XML analysiert oder komplexe reguläre Ausdrücke extrahiert werden. Eine solche Analyse kann explizit über die Funktionen [parse_json()](/azure/kusto/query/parsejsonfunction) oder [parse_xml()](/azure/kusto/query/parse-xmlfunction) erfolgen oder auch implizit, wenn auf dynamische Spalten verwiesen wird.

Diese Funktionen bewirken eine CPU-Auslastung proportional zur Anzahl der verarbeiteten Zeilen. Die effizienteste Optimierung besteht darin, frühzeitig in der Abfrage WHERE-Bedingungen hinzuzufügen, mit denen so viele Datensätze wie möglich herausgefiltert werden können, bevor die CPU-intensive Funktion ausgeführt wird.

Beispielsweise wird mit den folgenden Abfragen genau das gleiche Ergebnis erzielt, allerdings ist die zweite wesentlich effizienter, da mit der [where](/azure/kusto/query/whereoperator)-Bedingung vor der Analyse viele Datensätze ausgeschlossen werden:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Vermeiden von ausgewerteten WHERE-Klauseln

Abfragen, die [where](/azure/kusto/query/whereoperator)-Klauseln für eine ausgewertete Spalte enthalten und nicht für Spalten, die physisch im Dataset vorhanden sind, verlieren an Effizienz. Durch das Filtern nach ausgewerteten Spalten werden einige Systemoptimierungen beim Verarbeiten umfangreicher Datenmengen verhindert.
Beispielsweise wird mit den folgenden Abfragen genau das gleiche Ergebnis erzielt, doch ist die zweite effizienter, da sich die [where](/azure/kusto/query/whereoperator)-Bedingung auf eine integrierte Spalte bezieht:

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

In einigen Fällen wird die ausgewertete Spalte implizit von der Abfrageverarbeitungs-Engine erstellt, da der Filter nicht nur auf das Feld angewendet wird:
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Verwenden effektiver Aggregationsbefehle und -dimensionen bei join- und summarize-Operatoren

Während einige Aggregationsbefehle wie [max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction) und [avg()](/azure/kusto/query/avg-aggfunction) aufgrund ihrer Logik nur geringe CPU-Auswirkung haben, sind andere komplexer und umfassen Heuristik und Schätzungen, die eine effiziente Ausführung ermöglichen. Beispielsweise verwendet [dcount()](/azure/kusto/query/dcount-aggfunction) den HyperLogLog-Algorithmus, um eine genaue Schätzung für die eindeutige Anzahl großer Datenmengen zu liefern, ohne jeden Wert tatsächlich zu zählen. Mit den Perzentilfunktionen werden ähnliche Annäherungen mithilfe des Algorithmus für das Perzentil des nächsten Rangs durchgeführt. Einige der Befehle enthalten optionale Parameter, um ihre Auswirkung zu verringern. Beispielsweise verfügt die [makeset()](/azure/kusto/query/makeset-aggfunction)-Funktion über einen optionalen Parameter zum Definieren der maximalen festgelegten Größe, was sich erheblich auf CPU und Arbeitsspeicher auswirkt.

[join](/azure/kusto/query/joinoperator?pivots=azuremonitor)- und [summarize](/azure/kusto/query/summarizeoperator)-Befehle können zu einer hohen CPU-Auslastung führen, wenn mit ihnen eine große Datenmenge verarbeitet wird. Ihre Komplexität steht in direktem Bezug zur Anzahl möglicher Werte (*Kardinalität* genannt) der Spalten, die als `by` in „summarize“ oder als „join“-Attribute verwendet werden. Eine Erläuterung und Informationen zur Optimierung von „join“ und „summarize“ finden Sie in den Artikeln und Optimierungstipps in der jeweiligen Dokumentation.

Beispielsweise wird mit den folgenden Abfragen genau das gleiche Ergebnis erzielt, da **CounterPath** immer **CounterName** und **ObjectName** eins zu eins zugeordnet wird. Die zweite Abfrage ist effizienter, da die Aggregationsdimension kleiner ist:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

Die CPU-Auslastung kann auch durch where-Bedingungen oder erweiterte Spalten beeinflusst werden, die intensives Computing erfordern. Alle einfachen Zeichenfolgenvergleiche wie [equal ==](/azure/kusto/query/datatypes-string-operators) und [startswith](/azure/kusto/query/datatypes-string-operators) haben ungefähr dieselben CPU-Auswirkungen, während erweiterte Textabgleiche größere Auswirkungen haben. Insbesondere der [has](/azure/kusto/query/datatypes-string-operators)-Operator ist effizienter als der [contains](/azure/kusto/query/datatypes-string-operators)-Operator. Aufgrund der Verfahren zur Zeichenfolgenverarbeitung ist es effizienter, nach Zeichenfolgen, die länger als vier Zeichen sind, als nach kurzen Zeichenfolgen zu suchen.

Beispielsweise werden mit den folgenden Abfragen je nach Benennungsrichtlinie für „Computer“ ähnliche Ergebnisse erzielt, doch ist die zweite effizienter:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Dieser Indikator zeigt nur die CPU-Auslastung des unmittelbaren Clusters an. Bei einer Abfrage in mehreren Regionen würde nur eine der Regionen dargestellt. Bei einer Abfrage in mehreren Arbeitsbereichen sind möglicherweise nicht alle Arbeitsbereiche enthalten.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Vermeiden der vollständigen XML- und JSON-Analyse bei funktionierender Zeichenfolgenanalyse
Die vollständige Analyse eines XML- oder JSON-Objekts kann hohe CPU- und Speicherressourcen beanspruchen. In vielen Fällen, in denen nur ein oder zwei Parameter erforderlich sind und die XML- oder JSON-Objekte einfach sind, ist es einfacher, diese mithilfe des [parse-Operators](/azure/kusto/query/parseoperator) oder anderen [Textanalysetechniken](./parse-text.md) als Zeichenfolgen zu analysieren. Die Leistungssteigerung wird noch deutlicher, wenn die Anzahl der Datensätze im XML- oder JSON-Objekt zunimmt. Es ist von entscheidender Bedeutung, wenn die Anzahl der Datensätze zehn Millionen erreicht.

Beispielsweise gibt die folgende Abfrage genau die gleichen Ergebnisse zurück wie die obigen Abfragen, ohne dabei eine vollständige XML-Analyse durchzuführen. Beachten Sie, dass sich für die XML-Dateistruktur einige Annahmen ergeben. Das FilePath-Element kommt beispielsweise nach FileHash, und keines der Elemente verfügt über Attribute. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Für die verarbeitete Abfrage verwendete Daten

Ein kritischer Faktor bei der Verarbeitung der Abfrage ist die Menge an Daten, die für die Abfrageverarbeitung überprüft und verwendet werden. Azure Data Explorer verwendet aggressive Optimierungen, mit denen das Datenvolumen im Vergleich zu anderen Datenplattformen drastisch reduziert wird. Dennoch gibt es kritische Faktoren in der Abfrage, die sich auf das verwendete Datenvolumen auswirken können.

Abfragen, die mehr als 2.000 KB Daten verarbeiten, werden als Abfragen mit übermäßigem Ressourcenverbrauch betrachtet. Abfragen, die mehr als 20.000 KB Daten verarbeiten, werden als missbräuchliche Abfragen betrachtet und möglicherweise gedrosselt.

In Azure Monitor-Protokolle wird die Spalte **TimeGenerated** als Methode zum Indizieren der Daten verwendet. Wenn Sie die **TimeGenerated**-Werte auf einen möglichst kleinen Bereich beschränken, wird die Abfrageleistung wesentlich verbessert, da die Menge der zu verarbeitenden Daten erheblich eingeschränkt wird.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Vermeiden der unnötigen Verwendung von search- und union-Operatoren

Ein weiterer Faktor, der die Menge der verarbeiteten Daten erhöht, ist die Verwendung einer großen Anzahl von Tabellen. Dies ist normalerweise bei den Befehlen `search *` und `union *` der Fall. Durch diese Befehle wird das System gezwungen, Daten aus allen Tabellen im Arbeitsbereich zu bewerten und zu überprüfen. In einigen Fällen können Hunderte von Tabellen im Arbeitsbereich vorhanden sein. Vermeiden Sie nach Möglichkeit die Verwendung von „search *“ oder einer anderen Suche ohne Beschränkung auf eine bestimmte Tabelle.

Beispielsweise wird mit den folgenden Abfragen genau das gleiche Ergebnis erzielt, allerdings ist die letzte wesentlich effizienter:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Hinzufügen früher Filter zur Abfrage

Eine andere Methode, um das Datenvolumen zu verringern, ist eine frühzeitige Verwendung von [where](/azure/kusto/query/whereoperator)-Bedingungen in der Abfrage. Die Azure Data Explorer-Plattform umfasst einen Cache, durch den angegeben wird, welche Partitionen Daten enthalten, die für eine bestimmte where-Bedingung relevant sind. Wenn eine Abfrage z. B. `where EventID == 4624` enthält, wird die Abfrage nur an Knoten verteilt, die Partitionen mit übereinstimmenden Ereignissen verarbeiten.

Mit den folgenden Beispielabfragen wird genau das gleiche Ergebnis erzielt, doch ist die zweite Abfrage effizienter:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>Vermeiden mehrerer Überprüfungen derselben Quelldaten mithilfe bedingter Aggregationsfunktionen und der materialize-Funktion
Wenn eine Abfrage mehrere Unterabfragen enthält, die mithilfe von join- oder union-Operatoren zusammengeführt werden, überprüft jede Unterabfrage die gesamte Quelle separat und führt dann die Ergebnisse zusammen. Dadurch vervielfacht sich die Anzahl der Datenüberprüfungen – ein kritischer Faktor bei sehr großen Datasets.

Eine Technik, um dies zu vermeiden, ist die Verwendung der bedingten Aggregationsfunktionen. Die meisten der [Aggregationsfunktionen](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions), die im summarize-Operator verwendet werden, verfügen über eine bedingte Version, bei der Sie einen einzelnen summarize-Operator mit mehreren Bedingungen verwenden können. 

Die folgenden Abfragen zeigen beispielsweise die Anzahl der Anmeldeereignisse und die Anzahl der Prozessausführungsereignisse für jedes Konto. Sie geben die gleichen Ergebnisse zurück, doch werden bei der ersten Abfrage die Daten zweimal überprüft, bei der zweiten nur einmal:

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

Ein weiterer Fall, in dem Unterabfragen unnötig sind, ist das Vorfiltern für den [parse-Operator](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor), um sicherzustellen, dass nur Datensätze verarbeitet werden, die einem bestimmten Muster entsprechen. Dies ist unnötig, da der parse-Operator und andere ähnliche Operatoren leere Ergebnisse zurückgeben, wenn das Muster nicht übereinstimmt. Nachfolgend sind zwei Abfragen aufgeführt, die genau die gleichen Ergebnisse zurückgeben, doch werden bei der zweiten Abfrage die Daten nur einmal überprüft. Bei der zweiten Abfrage ist jeder parse-Befehl nur für die jeweiligen Ereignisse relevant. Der darauf folgende extend-Operator zeigt, wie auf leere Daten Bezug genommen wird.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

Wenn im Fall oben die Verwendung von Unterabfragen nicht vermieden werden kann, besteht eine weitere Technik darin, die Abfrage-Engine mithilfe der [materialize()-Funktion](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor) darauf hinzuweisen, dass jeweils die gleichen Quelldaten verwenden werden. Dies ist nützlich, wenn die Quelldaten aus einer Funktion stammen, die mehrmals innerhalb der Abfrage verwendet wird. „materialize“ wird angewendet, wenn die Ausgabe der Unterabfrage viel kleiner als die Eingabe ist. Die Ausgabe wird von der Abfrage-Engine zwischengespeichert und in allen Vorkommen wiederverwendet.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Reduzieren der Anzahl von abgerufenen Spalten

Da es sich bei Azure Data Explorer um einen spaltenbasierten Datenspeicher handelt, ist das Abrufen jeder Spalten von den anderen Spalten unabhängig. Die Anzahl der abgerufenen Spalten wirkt sich unmittelbar auf das Gesamtdatenvolumen aus. Sie sollten nur die benötigten Spalten in die Ausgabe einschließen, indem Sie die Ergebnisse mit [summarize](/azure/kusto/query/summarizeoperator) zusammenfassen oder die jeweiligen Spalten mit [project](/azure/kusto/query/projectoperator) projizieren. Azure Data Explorer bietet mehrere Optimierungen, um die Anzahl der abgerufenen Spalten zu verringern. Wenn festgestellt wird, dass eine Spalte nicht erforderlich ist (z. B. wenn im [summarize](/azure/kusto/query/summarizeoperator)-Befehl nicht darauf verwiesen wird), wird sie auch nicht abgerufen.

Beispielsweise werden mit der zweiten Abfrage möglicherweise dreimal mehr Daten verarbeitet, da nicht nur eine Spalte, sondern drei Spalten abgerufen werden müssen:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Zeitspanne der verarbeiteten Abfrage

Alle Protokolle in Azure Monitor-Protokolle werden gemäß der Spalte **TimeGenerated** partitioniert. Die Anzahl der Partitionen, auf die zugegriffen wird, stehen in direktem Bezug zur Zeitspanne. Eine Verringerung des Zeitbereichs ist die effizienteste Methode, um eine schnelle Abfrageausführung zu gewährleisten.

Abfragen mit einer Zeitspanne von mehr als 15 Tagen werden als Abfragen mit übermäßigem Ressourcenverbrauch betrachtet. Abfragen mit einer Zeitspanne von mehr als 90 Tagen werden als missbräuchliche Abfragen betrachtet und möglicherweise gedrosselt.

Der Zeitbereich kann mithilfe der Zeitbereichsauswahl im Log Analytics-Bildschirm festgelegt werden, wie es unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](scope.md#time-range) beschrieben ist. Dies ist die empfohlene Methode, da der ausgewählte Zeitbereich mithilfe der Abfragemetadaten an das Back-End übermittelt wird. 

Eine alternative Methode ist das explizite Einschließen einer [where](/azure/kusto/query/whereoperator)-Bedingung für **TimeGenerated** in die Abfrage. Sie sollten diese Methode verwenden, da dann auch bei Verwendung der Abfrage über eine andere Schnittstelle eine feste Zeitspanne gewährleistet ist.
Sie sollten sicherstellen, dass alle Teile der Abfrage über **TimeGenerated**-Filter verfügen. Wenn eine Abfrage Unterabfragen zum Abrufen von Daten aus verschiedenen Tabellen oder derselben Tabelle enthält, muss jede dieser Abfragen eine eigene [where](/azure/kusto/query/whereoperator)-Bedingung enthalten.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Stellen Sie sicher, dass alle Unterabfragen über den TimeGenerated-Filter verfügen.

Beispielsweise wird in der folgenden Abfrage die Tabelle **Perf** zwar nur für den letzten Tag überprüft, aber die Tabelle **Heartbeat** wird über den gesamten Verlauf überprüft, was einen Zeitraum von bis zu zwei Jahren umfassen kann:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Ein solcher Fehler tritt häufig auf, wenn [arg_max()](/azure/kusto/query/arg-max-aggfunction) für die Suche nach dem letzten Auftreten verwendet wird. Beispiel:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Dies kann problemlos korrigiert werden, indem ein Zeitfilter in der inneren Abfrage hinzugefügt wird:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Ein weiteres Beispiel für diesen Fehler ist das Ausführen der Zeitbereichsfilterung unmittelbar nach einer [union](/azure/kusto/query/unionoperator?pivots=azuremonitor)-Anweisung über mehrere Tabellen. Beim Ausführen der union-Anweisung sollte jede Unterabfrage einen Bereich aufweisen. Sie können eine [let](/azure/kusto/query/letstatement)-Anweisung verwenden, um Bereichskonsistenz zu gewährleisten.

Mit der folgenden Abfrage werden beispielsweise alle Daten in den Tabellen *Heartbeat* und *Perf* und nicht nur der letzte Tag überprüft:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Diese Abfrage sollte folgendermaßen korrigiert werden:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Einschränkungen bei der Zeitspannenmessung

Die Messung ist immer größer als die tatsächlich angegebene Zeit. Wenn der Filter für die Abfrage z. B. 7 Tage beträgt, überprüft das System möglicherweise 7,5 oder 8,1 Tage. Das liegt daran, dass die Daten vom System in Blöcke mit variabler Größe partitioniert werden. Um sicherzustellen, dass alle relevanten Datensätze überprüft werden, wird die gesamte Partition überprüft, die mehrere Stunden und auch mehr als einen Tag umfassen kann.

Es gibt mehrere Fälle, in denen das System keine genaue Messung des Zeitbereichs liefern kann. Dies geschieht meistens, wenn die Abfrage weniger als einen Tag umfasst, oder bei Abfragen mit mehreren Arbeitsbereichen.


> [!IMPORTANT]
> Dieser Indikator zeigt nur Daten an, die im unmittelbaren Cluster verarbeitet werden. Bei einer Abfrage in mehreren Regionen würde nur eine der Regionen dargestellt. Bei einer Abfrage in mehreren Arbeitsbereichen sind möglicherweise nicht alle Arbeitsbereiche enthalten.

## <a name="age-of-processed-data"></a>Alter der verarbeiteten Daten
Azure Data Explorer verwendet mehrere Speicherebenen: In-Memory, lokale SSD-Datenträger und wesentlich langsamere Azure-Blobs. Je neuer die Daten sind, desto höher ist die Wahrscheinlichkeit, dass sie auf einer leistungsfähigeren Ebene mit geringerer Latenz gespeichert sind, wodurch Abfragedauer und CPU-Auslastung reduziert werden. Abgesehen von den Daten selbst verfügt das System auch über einen Cache für Metadaten. Je älter die Daten sind, desto geringer ist die Wahrscheinlichkeit, dass deren Metadaten im Cache gespeichert sind.

Abfragen, die Daten mit einem Alter von mehr als 14 Tagen verarbeiten, werden als Abfragen mit übermäßigem Ressourcenverbrauch betrachtet.


Zwar müssen für einige Abfragen alte Daten verwendet werden, doch gibt es auch Fälle, in denen alte Daten versehentlich verwendet werden. Dies geschieht, wenn Abfragen ohne Angabe eines Zeitbereichs in den Metadaten ausgeführt werden und nicht alle Tabellenverweise Filter für die Spalte **TimeGenerated** enthalten. In diesen Fällen überprüft das System alle Daten, die in dieser Tabelle gespeichert sind. Wenn eine lange Datenaufbewahrungsdauer festgelegt ist, kann diese lange Zeitbereiche und damit Daten umfassen, die den gesamten Aufbewahrungszeitraum abdecken.

Hier einige Beispiele für solche Fälle:

- Keine Festlegung des Zeitbereichs in Log Analytics mit einer Unterabfrage, die nicht beschränkt ist. Siehe Beispiel oben.
- Verwendung der API ohne die optionalen Parameter für den Zeitbereich.
- Verwendung eines Clients, der keinen Zeitbereich erzwingt (z. B. der Power BI-Connector)

Sehen Sie sich die Beispiele und Hinweise im vorherigen Abschnitt an, da sie auch in diesem Fall zutreffen.

## <a name="number-of-regions"></a>Number of regions (Anzahl von Regionen)
Es gibt mehrere Situationen, in denen eine einzelne Abfrage über verschiedene Regionen ausgeführt werden kann:

- Wenn mehrere Arbeitsbereiche explizit aufgelistet sind und sich diese in verschiedenen Regionen befinden.
- Wenn eine Abfrage mit Ressourcenbereich Daten abruft und die Daten in mehreren Arbeitsbereichen gespeichert sind, die sich in verschiedenen Regionen befinden.

Für eine regionsübergreifende Abfrageausführung muss das System im Back-End große Zwischendatenblöcke serialisieren und übertragen, die in der Regel wesentlich größer sind als die letztendlichen Ergebnisse der Abfrage. Außerdem wird die Fähigkeit des Systems beschränkt, Optimierungen und Heuristik durchzuführen sowie Caches zu nutzen.
Wenn es keinen wirklichen Grund für das Durchsuchen all dieser Regionen gibt, sollten Sie den Bereich so anpassen, dass er weniger Regionen umfasst. Wenn der Ressourcenbereich minimiert ist, aber dennoch viele Bereiche verwendet werden, kann eine Fehlkonfiguration die Ursache sein. Beispielsweise werden Überwachungsprotokolle und Diagnoseeinstellungen an verschiedene Arbeitsbereiche in unterschiedlichen Regionen gesendet oder es sind mehrere Konfigurationen für Diagnoseeinstellungen vorhanden. 

Abfragen, die sich über mehr als 3 Regionen erstrecken, werden als Abfragen mit übermäßigem Ressourcenverbrauch betrachtet. Abfragen, die sich über mehr als 6 Regionen erstrecken, werden als missbräuchliche Abfragen betrachtet und möglicherweise gedrosselt.

> [!IMPORTANT]
> Wenn eine Abfrage über mehrere Regionen ausgeführt wird, sind die CPU- und Datenmessungen nicht exakt, und die Messungen werden nur für eine der Regionen dargestellt.

## <a name="number-of-workspaces"></a>Anzahl von Arbeitsbereichen
Arbeitsbereiche sind logische Container, die zum Trennen und Verwalten von Protokolldaten verwendet werden. Das Back-End optimiert die Platzierung von Arbeitsbereichen in physischen Clustern innerhalb der ausgewählten Region.

Die Verwendung mehrerer Arbeitsbereiche kann folgende Ursachen haben: 

- Es sind mehrere Arbeitsbereiche explizit aufgelistet.
- Eine Abfrage mit Ressourcenbereich ruft Daten ab, und die Daten sind in mehreren Arbeitsbereichen gespeichert.
 
Für eine regions- und clusterübergreifende Ausführung von Abfragen muss das System im Back-End große Zwischendatenblöcke serialisieren und übertragen, die in der Regel wesentlich größer sind als die letztendlichen Ergebnisse der Abfrage. Außerdem wird die Fähigkeit des Systems beschränkt, Optimierungen und Heuristik durchzuführen sowie Caches zu nutzen.

Abfragen, die sich über mehr als 5 Arbeitsbereiche erstrecken, werden als Abfragen mit übermäßigem Ressourcenverbrauch betrachtet. Abfragen können sich nicht über mehr als 100 Arbeitsbereiche erstrecken.

> [!IMPORTANT]
> In einigen Szenarien mit mehreren Arbeitsbereichen sind die CPU- und Datenmessungen nicht exakt und die Messungen werden nur für wenige der Arbeitsbereiche dargestellt.

## <a name="parallelism"></a>Parallelität
Azure Monitor-Protokolle verwendet große Azure Data Explorer-Cluster zum Ausführen von Abfragen, und diese Cluster sind unterschiedlich groß und können bis zu Dutzende von Rechenknoten umfassen. Das System skaliert die Cluster automatisch gemäß der Platzierungslogik und Kapazität von Arbeitsbereichen.

Zum effizienten Ausführen einer Abfrage wird sie basierend auf den Daten, die für die Verarbeitung benötigt werden, partitioniert und an Computeknoten verteilt. In einigen Situationen kann dies vom System nicht effizient durchgeführt werden. Dadurch kann es zu einer langen Abfragedauer kommen. 

Hier einige Abfrageverhalten, die zur einer Verringerung der Parallelität führen können:

- Verwendung von Serialisierungs- und Fensterfunktionen, z. B. dem [serialize-Operator](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction) und den [row](/azure/kusto/query/rowcumsumfunction)-Funktionen. In einigen dieser Fälle können Zeitreihen- und Benutzeranalysefunktionen verwendet werden. Eine ineffiziente Serialisierung kann auch auftreten, wenn die folgenden Operatoren nicht am Ende der Abfrage verwendet werden: [range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    Durch Verwendung der Aggregationsfunktion [dcount()](/azure/kusto/query/dcount-aggfunction) wird das System gezwungen, eine zentrale Kopie der eindeutigen Werte zu verwenden. Wenn die Datenmenge groß ist, sollten Sie die optionalen Parameter der dcount-Funktion verwenden, um die Genauigkeit zu verringern.
-    In vielen Fällen verringert der [join](/azure/kusto/query/joinoperator?pivots=azuremonitor)-Operator die Gesamtparallelität. Prüfen Sie Shuffle-Join als Alternative, wenn die Leistung problematisch ist.
-    Bei Abfragen im Ressourcenbereich können sich die Kubernetes RBAC- oder Azure RBAC-Prüfungen vor der Ausführung in Situationen mit einer sehr großen Anzahl von Azure-Rollenzuweisungen verzögern. Dies kann zu längeren Überprüfungen führen, was wiederum eine geringere Parallelität bewirkt. Beispielsweise wird eine Abfrage für ein Abonnement ausgeführt, bei dem Tausende von Ressourcen vorhanden sind, und jede Ressource verfügt über viele Rollenzuweisungen auf der Ressourcenebene und nicht auf Ebene des Abonnements oder der Ressourcengruppe.
-    Wenn eine Abfrage kleine Datenblöcke verarbeitet, ist die Parallelität gering, da sie vom System nicht auf viele Computeknoten verteilt wird.



## <a name="next-steps"></a>Nächste Schritte

- [Referenzdokumentation für die Abfragesprache Kusto](/azure/kusto/query/)
