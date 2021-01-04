---
title: Von Splunk zur Azure Monitor-Protokollabfrage | Microsoft-Dokumentation
description: Unterstützung für Benutzer, die mit Splunk vertraut sind, beim Erlernen von Azure Monitor-Protokollabfragen
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c59b5646e011afa6b8487e8145a1cb07e6e2a8ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015578"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Von Splunk zur Azure Monitor-Protokollabfrage

Dieser Artikel dient als Unterstützung für Benutzer, die mit Splunk vertraut sind, beim Erlernen der Abfragesprache Kusto zum Schreiben von Protokollabfragen in Azure Monitor. Die beiden Sprachen werden direkt miteinander verglichen, um wichtige Unterschiede sowie Ähnlichkeiten, die Ihnen eine Nutzung Ihrer vorhandenen Kenntnisse ermöglichen, zu veranschaulichen.

## <a name="structure-and-concepts"></a>Struktur und Konzepte

In der folgenden Tabelle werden Konzepte und Datenstrukturen von Splunk und Azure Monitor-Protokollen miteinander verglichen.

 | Konzept  | Splunk | Azure Monitor |  Comment
 | --- | --- | --- | ---
 | Bereitstellungseinheit  | cluster |  cluster |  Azure Monitor ermöglicht beliebige clusterübergreifende Abfragen. Bei Splunk ist das nicht der Fall. |
 | Datencaches |  buckets  |  Caching und Aufbewahrungsrichtlinien |  Steuert den Zeitraum und die Cachingebene für die Daten. Diese Einstellung wirkt sich direkt auf die Leistung von Abfragen und die Kosten der Bereitstellung aus. |
 | Logische Partition von Daten  |  Index  |  database  |  Ermöglicht die logische Trennung der Daten. Beide Implementierungen ermöglichen partitionsübergreifende Unions und Verknüpfungen. |
 | Strukturierte Ereignismetadaten | – | table |  Bei Splunk wird das Konzept der Suchsprache für Ereignismetadaten nicht verfügbar gemacht. Azure Monitor-Protokolle weisen das Konzept einer Tabelle mit Spalten auf. Jede Ereignisinstanz ist einer Zeile zugeordnet. |
 | Datensatz | Ereignis | row |  Nur Terminologieänderung. |
 | Datensatzattribut | Feld |  column |  In Azure Monitor ist dies als Teil der Tabellenstruktur vordefiniert. In Splunk hat jedes Ereignis einen eigenen Satz von Feldern. |
 | Typen | datatype |  datatype |  Azure Monitor-Datentypen sind expliziter, da sie für die Spalten festgelegt sind. Beide verfügen über die Möglichkeit, dynamisch mit Datentypen und einem sich weitgehend entsprechenden Satz von Datentypen, einschließlich JSON-Unterstützung, zu arbeiten. |
 | Abfrage und Suche  | search | Abfrage |  Die Konzepte sind bei Azure Monitor und Splunk im Wesentlichen identisch. |
 | Ereigniserfassungszeit | Systemzeit | ingestion_time() |  In Splunk erhält jedes Ereignis einen Systemzeitstempel des Zeitpunkts, zu dem das Ereignis indiziert wurde. In Azure Monitor können Sie eine Richtlinie namens „ingestion_time“ definieren, mit der eine Systemspalte bereitgestellt wird, auf die mit der Funktion „ingestion_time()“ verwiesen werden kann. |

## <a name="functions"></a>Functions

In der folgenden Tabelle sind Funktionen in Azure Monitor angegeben, die Splunk-Funktionen entsprechen.

|Splunk | Azure Monitor |Comment
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| replace | replace() | (1)<br> Beachten Sie auch, dass `replace()` zwar bei beiden Produkten drei Parameter verwendet, die Parameter aber unterschiedlich sind. |
| substr | substring() | (1)<br>Beachten Sie auch, dass Splunk einsbasierte Indizes verwendet. Azure Monitor verwendet nullbasierte Indizes. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |  (2)  |
| regex | matches regex | In Splunk ist `regex` ein Operator. In Azure Monitor ist es ein relationaler Operator. |
| searchmatch | == | In Splunk ermöglicht `searchmatch` eine Suche nach der exakten Zeichenfolge.
| random | rand()<br>rand(n) | Die Splunk-Funktion gibt eine Zahl zwischen 0 (null) und 2<sup>31</sup>-1 zurück. Azure Monitor gibt eine Zahl zwischen 0,0 und 1,0 oder bei angegebenem Parameter eine Zahl zwischen 0 und n-1 zurück.
| now | now() | (1)
| relative_time | totimespan() | (1)<br>Die Entsprechung der Splunk-Funktion „relative_time(datetimeVal, offsetVal)“ lautet in Azure Monitor „datetimeVal + totimespan(offsetVal)“.<br><code>search &#124; eval n=relative_time(now(), "-1d@d")</code> wird beispielsweise zu <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) In Splunk wird die Funktion mit dem `eval`-Operator aufgerufen. In Azure Monitor wird sie als Teil von `extend` oder `project` verwendet.<br>(2) In Splunk wird die Funktion mit dem `eval`-Operator aufgerufen. In Azure Monitor kann sie mit dem `where`-Operator verwendet werden.


## <a name="operators"></a>Operatoren

Die folgenden Abschnitte enthalten Beispiele für die Verwendung verschiedener Operatoren in Splunk und Azure Monitor.

> [!NOTE]
> Im folgenden Beispiel ist das Splunk-Feld _rule_ einer Tabelle in Azure Monitor zugeordnet, und der Standardzeitstempel von Splunk ist der Spalte _ingestion_time()_ in Log Analytics zugeordnet.

### <a name="search"></a>Suchen,
In Splunk können Sie das Schlüsselwort `search` auslassen und eine Zeichenfolge ohne Anführungszeichen eingeben. In Azure Monitor müssen Sie jede Abfrage mit `find` beginnen, eine nicht in Anführungszeichen eingeschlossene Zeichenfolge ist ein Spaltenname, und der Suchwert muss eine Zeichenfolge in Anführungszeichen sein. 

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| **Azure Monitor** | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filtern
Azure Monitor-Protokollabfragen beginnen in einem tabellarischen Resultset mit dem „where“-Filter. In Splunk ist Filtern der Standardvorgang für den aktuellen Index. Sie können auch den `where`-Operator in Splunk verwenden, doch wird dies nicht empfohlen.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| **Azure Monitor** | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Abrufen von n Ereignissen/Zeilen zur Überprüfung 
Azure Monitor-Protokollabfragen unterstützen auch `take` als Alias für `limit`. Wenn in Splunk die Ergebnisse sortiert sind, gibt `head` die ersten n Ergebnisse zurück. In Azure Monitor ist „limit“ nicht sortiert, doch werden die ersten n Zeilen zurückgegeben, die gefunden werden.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| **Azure Monitor** | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Abrufen der ersten n Ereignisse/Zeilen nach Feld/Spalte sortiert
Für niedrigste Ergebnisse in Splunk verwenden Sie `tail`. In Azure Monitor können Sie die Sortierrichtung mit `asc` angeben.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| **Azure Monitor** | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Erweitern des Resultset mit neuen Feldern/Spalten
Splunk verfügt auch über eine `eval`-Funktion, die nicht mit dem `eval`-Operator vergleichbar ist. Sowohl der `eval`-Operator in Splunk als auch der `extend`-Operator in Azure Monitor unterstützen nur skalare Funktionen und arithmetische Operatoren.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| **Azure Monitor** | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Umbenennen 
Azure Monitor verwendet zum Umbenennen eines Felds den `project-rename`-Operator. `project-rename` ermöglicht der Abfrage, alle für ein Feld vorab erstellten Indizes zu nutzen. Splunk verfügt für den gleichen Zweck über einen `rename`-Operator.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| **Azure Monitor** | **project-rename** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Formatieren von Ergebnissen/Projektion
Splunk scheint keinen Operator aufzuweisen, der `project-away` ähnelt. Sie können die Benutzeroberfläche verwenden, um Felder auszufiltern.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **Tabelle** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| **Azure Monitor** | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Aggregation
Informationen zu den verschiedenen Aggregationsfunktionen finden Sie unter [Aggregationen in Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations).

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| **Azure Monitor** | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Das Verknüpfen in Splunk weist wesentliche Einschränkungen auf. Für die Unterabfrage gilt ein Grenzwert von 10000 Ergebnissen (in der Bereitstellungskonfigurationsdatei festgelegt) sowie eine begrenzte Anzahl von Verknüpfungskonfigurationen.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| **Azure Monitor** | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Sortieren
In Splunk müssen Sie zum Sortieren in aufsteigender Reihenfolge den `reverse`-Operator verwenden. In Azure Monitor kann auch definiert werden, wo Nullen zu platzieren sind, am Anfang oder am Ende.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| **Azure Monitor** | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Erweiterung für mehrere Werte
Dies ist in Splunk und Azure Monitor ein ähnlicher Operator.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **mvexpand** |  `mvexpand foo` |
| **Azure Monitor** | **mvexpand** | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Ergebnisfacets, interessante Felder
In Log Analytics im Azure-Portal wird nur die erste Spalte verfügbar gemacht. Alle Spalten stehen über die API zur Verfügung.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| **Azure Monitor** | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Deduplizieren
Sie können stattdessen `summarize arg_min()` verwenden, um die Reihenfolge umzukehren, in der ein Datensatz ausgewählt wird.

| | Operator | Beispiel |
|:---|:---|:---|
| **Splunk** | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| **Azure Monitor** | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Nächste Schritte

- Arbeiten Sie eine Lektion über das [Schreiben von Protokollabfragen in Azure Monitor](get-started-queries.md) durch.
