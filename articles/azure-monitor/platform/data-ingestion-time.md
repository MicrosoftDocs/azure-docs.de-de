---
title: Protokolldatenerfassungszeit in Azure Monitor | Microsoft-Dokumentation
description: Erläutert die verschiedenen Faktoren, die sich auf die Wartezeit beim Sammeln von Protokolldaten in Azure Monitor auswirken.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: d508ce217e3a97b3399435cb63295eb28965359a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65605603"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Protokolldatenerfassungszeit in Azure Monitor
Azure Monitor ist ein Hochleistungs-Datendienst, der Tausende Kunden bedient, die mit zunehmender Tendenz jeden Monat Terabytes von Daten senden. Häufig werden Fragen nach dem Zeitbedarf gestellt, der nach dem Sammeln der Protokolldaten bis zu ihrer Verfügbarkeit zu veranschlagen ist. Dieser Artikel erläutert die verschiedenen Faktoren, die sich auf diese Wartezeit auswirken.

## <a name="typical-latency"></a>Typische Wartezeit
Wartezeit bezeichnet hier die Zeitspanne zwischen der Erstellung der Daten auf dem überwachten System und dem Zeitpunkt, zu dem sie für die Analyse in Azure Monitor verfügbar werden. Die typische Wartezeit für die Erfassung von Protokolldaten liegt zwischen 2 und 5 Minuten. Die spezifische Wartezeit für bestimmte Daten hängt von einer Reihe von Faktoren ab, die nachfolgend erläutert werden.


## <a name="factors-affecting-latency"></a>Faktoren, die die Wartezeit beeinflussen
Die gesamte Erfassungszeit für ein bestimmtes Dataset kann in die folgenden allgemeinen Bereiche aufgeteilt werden. 

- Agentzeit: Die Zeit für die Erkennung eines Ereignisses, seine Erfassung und das Senden an den Azure Monitor-Erfassungspunkt als Protokolldatensatz. In den meisten Fällen wird dieser Prozess von einem Agent behandelt.
- Pipelinezeit: Die Zeit, die die Erfassungspipeline für die Verarbeitung des Protokolldatensatzes benötigt. Diese umfasst das Analysieren der Ereigniseigenschaften und potenziell das Hinzufügen berechneter Informationen.
- Indizierungszeit: Die Zeit für die Erfassung eines Protokolldatensatzes im Big Data-Speicher von Azure Monitor.

Detailinformationen zu den verschiedenen Wartezeiten in diesem Prozess werden nachfolgend beschrieben.

### <a name="agent-collection-latency"></a>Agent-Sammlungswartezeit
Agents und Managementlösungen verwenden verschiedene Strategien, um Daten eines virtuellen Computers zu erfassen, was sich auf die Wartezeit auswirken kann. Dies sind einige spezifische Beispiele:

- Windows-Ereignisse, Syslog-Ereignisse und Leistungsmetriken werden sofort erfasst. Linux-Leistungsindikatoren werden in 30-Sekunden-Intervallen abgerufen.
- IIS-Protokolle und benutzerdefinierte Protokolle werden bei jeder Änderung ihres Zeitstempels erfasst. Bei IIS-Protokollen wird dies durch den [in IIS konfigurierten Rolloverzeitplan](data-sources-iis-logs.md) beeinflusst. 
- Die Active Directory-Replikationslösung führt ihre Bewertung alle fünf Tage aus, während die Active Directory-Bewertungslösung eine wöchentliche Bewertung Ihrer Active Directory-Infrastruktur ausführt. Der Agent erfasst diese Protokolle nur, wenn die Bewertung abgeschlossen ist.

### <a name="agent-upload-frequency"></a>Uploadhäufigkeit des Agents
Um einen schlanken Log Analytics-Agent zu gewährleisten, speichert der Agent Protokolle zwischen und lädt sie in regelmäßigen Abständen nach Azure Monitor hoch. Die Uploadhäufigkeit schwankt zwischen 30 Sekunden und 2 Minuten, abhängig vom Datentyp. Die meisten Daten werden in unter 1 Minute hochgeladen. Die Netzwerkbedingungen können sich für diese Daten nachteilig auf die Wartezeit bis zum Erreichen des Azure Monitor-Erfassungspunkts auswirken.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Azure-Aktivitätsprotokolle, -Diagnoseprotokolle und -Metriken
Für Azure-Daten ist zusätzliche Zeit erforderlich, bis sie am Log Analytics-Erfassungspunkt zur Verarbeitung verfügbar sind:

- Das Senden von Daten aus Diagnoseprotokollen nimmt je nach Azure-Dienst 2 bis 15 Minuten in Anspruch. Mit der [folgenden Abfrage](#checking-ingestion-time) können Sie die Wartezeit in Ihrer Umgebung ermitteln.
- Das Senden von Metriken der Azure-Plattform an den Log Analytics-Erfassungspunkt nimmt drei Minuten in Anspruch.
- Das Senden von Daten des Aktivitätsprotokolls an den Log Analytics-Erfassungspunkt nimmt 10 bis 15 Minuten in Anspruch.

Sobald die Daten am Erfassungspunkt verfügbar sind, vergehen weitere zwei bis fünf Minuten, bis die Daten für Abfragen zur Verfügung stehen.

### <a name="management-solutions-collection"></a>Sammlung von Verwaltungslösungen
Einige Lösungen sammeln ihre Daten nicht mithilfe eines Agents, sondern verwenden eine Erfassungsmethode, die zusätzliche Wartezeit mit sich bringt. Einige Lösungen erfassen Daten in regelmäßigen Intervallen, ohne eine Sammlung nahezu in Echtzeit zu versuchen. Dies sind einige spezifische Beispiele:

- Die Office 365-Lösung ruft Aktivitätsprotokolle mithilfe der Office 365-Verwaltungsaktivitäts-API ab, die aktuell keine Garantien für Wartezeiten bietet, die einer Echtzeiterfassung nahekommen.
- Daten aus Windows Analytics-Lösungen (z.B. Updatekonformität) werden von der Lösung mit täglicher Häufigkeit erfasst.

Informationen zur Erfassungshäufigkeit der einzelnen Lösungen finden Sie in der Dokumentation der jeweiligen Lösung.

### <a name="pipeline-process-time"></a>Pipeline-Verarbeitungszeit
Sobald Protokolldatensätze in der Azure Monitor-Pipeline erfasst werden, werden sie in einen temporären Speicher geschrieben, um Mandantenisolation und Schutz vor Datenverlust sicherzustellen. Dieser Vorgang wirkt sich normalerweise mit zusätzlichen 5–15 Sekunden aus. Einige Verwaltungslösungen implementieren aufwändigere Algorithmen zum Aggregieren von Daten und Ableiten von Erkenntnissen aus im Datenstrom eingehenden Daten. Beispielsweise aggregiert die Netzwerkleistungsüberwachung Daten über 3-Minuten-Intervalle, wodurch sich die Wartezeit um 3 Minuten verlängert. Ein anderer Prozess, durch den die Latenz erhöht wird, ist der Prozess, der benutzerdefinierte Protokolle verarbeitet. In manchen Fällen kann dieser Prozess eine Latenz von einigen Minuten zu Protokollen hinzufügen, die vom Agent aus Dateien gesammelt werden.

### <a name="new-custom-data-types-provisioning"></a>Bereitstellung von neuen, benutzerdefinierten Datentypen
Wenn aus einem [benutzerdefinierten Protokoll](data-sources-custom-logs.md) oder der [Datensammler-API](data-collector-api.md) ein neuer benutzerdefinierter Datentyp erstellt wird, erstellt das System einen dedizierten Speichercontainer. Dies bedingt einen einmaligen Mehraufwand, der nur beim ersten Auftreten dieses Datentyps eintritt.

### <a name="surge-protection"></a>Schutz vor Überflutung
Die oberste Priorität für Azure Monitor besteht darin, sicherzustellen, dass keine Kundendaten verloren gehen, daher weist das System einen integrierten Schutz vor Datenüberflutung auf. Dieser schließt Puffer ein, um sicherzustellen, dass das System selbst unter extremer Last noch funktioniert. Unter normalen Lastverhältnissen wird durch diese Steuerungsmechanismen weniger als eine Minute hinzugefügt, jedoch können sie unter extremen Bedingungen und bei Ausfällen zu erheblicher Verlängerung der Wartezeit führen, während sie die Sicherheit der Daten gewährleisten.

### <a name="indexing-time"></a>Indizierungszeit
Jede Big Data-Plattform vollzieht einen Kompromiss zwischen dem Bereitstellen von Analysen und erweiterten Suchfunktionen einerseits und sofortigem Zugriff auf die Daten andererseits. Azure Monitor ermöglicht Ihnen das Ausführen leistungsstarker Abfragen über Milliarden von Datensätzen und ruft Ergebnisse innerhalb weniger Sekunden ab. Dies wird dadurch ermöglicht, dass die Infrastruktur die Daten während ihrer Erfassung erheblich transformiert und sie in einzigartig kompakten Strukturen speichert. Das System puffert die Daten, bis genügend davon vorhanden sind, um diese Strukturen zu erstellen. Dieser Vorgang muss abgeschlossen sein, bevor der Protokolldatensatz in den Suchergebnissen angezeigt wird.

Dieser Prozess nimmt derzeit ungefähr 5 Minuten bei geringem Datenaufkommen in Anspruch, weniger bei hohem Datendurchsatz. Dies scheint der Intuition zu widersprechen, jedoch ermöglicht dieser Prozess die Optimierung der Wartezeit für Produktionsworkloads mit großem Datenaufkommen.



## <a name="checking-ingestion-time"></a>Überprüfen der Erfassungszeit
Die Erfassungszeit kann für verschiedene Ressourcen unter verschiedenen Umständen variieren. Mithilfe von Protokollabfragen können Sie das spezifische Verhalten Ihrer Umgebung ermitteln.

### <a name="ingestion-latency-delays"></a>Verzögerungen der Erfassungswartezeit
Sie können die Wartezeit eines bestimmten Datensatzes messen, indem Sie das Ergebnis der Funktion [ingestion_time()](/azure/kusto/query/ingestiontimefunction) mit dem Feld _TimeGenerated_ vergleichen. Diese Daten können mit verschiedenen Aggregationen verwendet werden, um das Verhalten der Erfassungswartezeit zu ermitteln. Untersuchen Sie ein Perzentil der Erfassungszeit, um Einblicke für große Datenmengen zu erhalten. 

Die folgende Abfrage zeigt beispielsweise, welche Computer am aktuellen Tag die höchste Erfassungszeit aufwiesen: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Wenn Sie detaillierte Informationen zur Erfassungszeit für einen bestimmten Computer über einen Zeitraum anzeigen möchten, verwenden Sie die folgende Abfrage, mit der die Daten zudem in einem Diagramm visualisiert werden: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Mit der folgenden Abfrage können Sie die Erfassungszeit von Computern nach dem Land oder der Region anzeigen, in dem bzw. der sich diese basierend auf der IP-Adresse befinden: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Verschiedene Datentypen, die vom Agent stammen, weisen möglicherweise unterschiedliche Erfassungswartezeiten auf. Daher können die vorherigen Abfragen mit anderen Typen verwendet werden. Mit der folgenden Abfrage können Sie die Erfassungszeit verschiedener Azure-Dienste überprüfen: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Ressourcen, die nicht mehr reagieren 
In einigen Fällen kann eine Ressource das Senden von Daten beendet. Um festzustellen, ob eine Ressource Daten sendet oder nicht, sehen Sie sich den letzten Datensatz an, der anhand des Standardfelds _TimeGenerated_ ermittelt werden kann.  

Prüfen Sie anhand der Tabelle _Heartbeat_ die Verfügbarkeit eines virtuellen Computers, da einmal pro Minute ein Heartbeat vom Agent gesendet wird. Mit der folgenden Abfrage können Sie die aktiven Computer auflisten, die kürzlich keinen Heartbeat gemeldet haben: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie die [Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) für Azure Monitor.

