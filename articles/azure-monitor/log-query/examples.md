---
title: Beispiele für Protokollabfragen in Azure Monitor | Microsoft-Dokumentation
description: Beispiele für Protokollabfragen in Azure Monitor unter Verwendung der Abfragesprache Kusto.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: 2c35bc4026c81cbc8b95225e688a3922bc320554
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759910"
---
# <a name="azure-monitor-log-query-examples"></a>Beispiele für Protokollabfragen in Azure Monitor
Dieser Artikel enthält mehrere Beispiele für [Abfragen](log-query-overview.md) unter Verwendung der [Abfragesprache Kusto](/azure/kusto/query/), mit denen verschiedene Arten von Protokolldaten aus Azure Monitor abgerufen werden. Da Daten auf unterschiedliche Weise konsolidiert und analysiert werden, können Sie anhand dieser Beispiele verschiedene Strategien ermitteln, die ggf. für Ihre eigenen Anforderungen geeignet sind.  

Ausführliche Informationen zu den Schlüsselwörtern aus diesen Beispielen finden Sie in der [Kusto-Sprachreferenz](https://docs.microsoft.com/azure/kusto/query/). Sollten Sie noch keine Erfahrung mit Azure Monitor haben, machen Sie sich anhand [dieser Lektion](get-started-queries.md) mit dem Erstellen von Abfragen vertraut.

## <a name="events"></a>Events

### <a name="search-application-level-events-described-as-cryptographic"></a>Suchen nach Ereignissen auf Anwendungsebene, die als „kryptographisch“ beschrieben werden
In diesem Beispiel wird die Tabelle **Events** nach Datensätzen durchsucht, in denen **EventLog** den Wert _Application_ besitzt und **RenderedDescription** die Zeichenfolge _cryptographic_ enthält. Dabei werden Datensätze der letzten 24 Stunden berücksichtigt.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Suchen nach Ereignissen im Zusammenhang mit Unmarshaling
Hier werden die Tabellen **Event** und **SecurityEvents** nach Datensätzen durchsucht, in denen _unmarshaling_ erwähnt wird.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Heartbeat

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Erstellen eines wochenbasierten Diagramms für die Anzahl von Computern, die Daten senden

Im folgenden Beispiel wird ein wochenbasiertes Diagramm für die Anzahl individueller Computer erstellt, die Heartbeats gesendet haben.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Suchen nach veralteten Computern

Im folgenden Beispiel wird nach Computern gesucht, die am letzten Tag aktiv waren, aber in der letzten Stunde keinen Heartbeat gesendet haben.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Abrufen des neuesten Heartbeat-Datensatzes pro Computer-IP-Adresse

In diesem Beispiel wird für jede Computer-IP-Adresse der neueste Heartbeat-Datensatz zurückgegeben.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Abgleichen von Schutzstatusdatensätzen mit Heartbeat-Datensätzen

In diesem Beispiel werden zusammengehörige Schutzstatus- und Heartbeat-Datensätze gesucht und auf der Grundlage von Computer und Zeit abgeglichen.
Beachten Sie, dass das Zeitfeld minutengenau gerundet wird. Hierzu wurde die bin-Laufzeitberechnung verwendet: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Serververfügbarkeitsrate

Berechnet die Serververfügbarkeitsrate auf der Grundlage von Heartbeat-Datensätzen. Verfügbarkeit ist als „mindestens ein Heartbeat pro Stunde“ definiert.
Wenn ein Server also 98 von 100 Stunden verfügbar war, liegt die Verfügbarkeitsrate bei 98 Prozent.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Mehrere Datentypen

### <a name="chart-the-record-count-per-table"></a>Erstellen eines Diagramms mit der Datensatzanzahl pro Tabelle
Im folgenden Beispiel werden sämtliche Datensätze aller Tabellen aus den letzten fünf Stunden erfasst, und es wird ermittelt, wie viele Datensätze jeweils in den Tabellen enthalten waren. Die Ergebnisse werden in einem Zeitdiagramm dargestellt.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Zählen aller Protokolle, die in der letzten Stunde erfasst wurden (nach Typ)
Im folgenden Beispiel wird alles, was in der letzten Stunde gemeldet wurde, durchsucht und die Anzahl von Datensätzen in den einzelnen Tabellen nach **Typ** ermittelt. Die Ergebnisse werden in einem Balkendiagramm dargestellt.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Zählen der Azure-Diagnosedatensätze pro Kategorie
In diesem Beispiel werden alle Azure-Diagnosedatensätze für jede individuelle Kategorie gezählt.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Abrufen eines willkürlichen Datensatzes für jede individuelle Kategorie
In diesem Beispiel wird nach dem Zufallsprinzip für jede individuelle Kategorie ein einzelner Azure-Diagnosedatensatz abgerufen.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Abrufen des neuesten Datensatzes pro Kategorie
In diesem Beispiel wird der neueste Azure-Diagnosedatensatz aus jeder individuellen Kategorie abgerufen.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Netzwerküberwachung

### <a name="computers-with-unhealthy-latency"></a>Computer mit nicht ordnungsgemäßer Wartezeit
In diesem Beispiel wird eine Liste individueller Computer mit nicht ordnungsgemäßer Wartezeit erstellt.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Leistung

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Verknüpfen von Leistungsdatensätzen zum Korrelieren von Arbeitsspeicher und CPU
In diesem Beispiel werden Datensätze vom Typ **perf** eines bestimmten Computers korreliert und zwei Zeitdiagramme (durchschnittliche CPU-Nutzung und maximale Arbeitsspeichernutzung) erstellt.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>CPU-Auslastungsdiagramm pro Computer
In diesem Beispiel wird die CPU-Auslastung von Computern, die mit _Contoso_ beginnen, berechnet und in einem Diagramm dargestellt.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Schutzstatus

### <a name="computers-with-non-reporting-protection-status-duration"></a>Computer mit dem Schutzstatus „Keine Berichterstattung“ (Dauer)
In diesem Beispiel werden Computer, die den Schutzstatus _Not Reporting_ (Keine Berichterstattung) hatten, zusammen mit der jeweiligen Dauer dieses Status aufgelistet.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Abgleichen von Schutzstatusdatensätzen mit Heartbeat-Datensätzen
In diesem Beispiel werden zusammengehörige Schutzstatus- und Heartbeat-Datensätze gesucht und auf der Grundlage von Computer und Zeit abgeglichen.
Das Zeitfeld wird mithilfe von **bin** minutengenau gerundet.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Sicherheitsdatensätze

### <a name="count-security-events-by-activity-id"></a>Zählen von Sicherheitsereignissen nach Aktivitäts-ID


Dieses Beispiel basiert auf der festen Struktur der Spalte **Activity**: \<ID\>-\<Name\>.
Es unterteilt den Wert **Activity** in zwei neue Spalten und zählt die Vorkommen der einzelnen Aktivitäts-IDs (**activityID**).

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Zählen von Sicherheitsereignissen im Zusammenhang mit Berechtigungen
In diesem Beispiel wird die Anzahl von Datensätzen des Typs **securityEvent** angezeigt, in denen die Spalte **Activity** den Begriff _Permissions_ enthält. Die Abfrage gilt für Datensätze, die innerhalb der letzten 30 Minuten erstellt wurden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Suchen nach Konten mit nicht erfolgreicher Anmeldung über Computer mit Sicherheitserkennung
In diesem Beispiel wird nach Konten gesucht, bei denen die Anmeldung über Computer, auf denen eine Sicherheitserkennung vorhanden ist, nicht erfolgreich war, und es wird die Anzahl dieser Konten ermittelt.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Sind meine Sicherheitsdaten verfügbar?
Das Durchsuchen von Daten beginnt häufig mit einer Überprüfung der Datenverfügbarkeit. In diesem Beispiel wird die Anzahl von Datensätzen des Typs **securityEvent** der letzten 30 Minuten angezeigt.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Analysieren von Aktivitätsname und -ID
Die beiden folgenden Beispiele basieren auf der festen Struktur der Spalte **Activity**: \<ID\>-\<Name\>. Im ersten Beispiel wird der Operator **parse** verwendet, um zwei neuen Spalten Werte zuzuweisen: **activityID** und **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

In diesem Beispiel wird der Operator **split** verwendet, um ein Array von separaten Werten zu erstellen.
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Prozesse für explizite Anmeldeinformationen
Das folgende Beispiel zeigt ein Kreisdiagramm für Prozesse, bei denen in der letzten Woche explizite Anmeldeinformationen verwendet wurden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Wichtigste ausgeführte Prozesse

Das folgende Beispiel zeigt eine Aktivitätszeitachse für die fünf gängigsten Prozesse der letzten drei Tage.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Suchen nach mehrmals nicht erfolgreichen Anmeldeversuchen des gleichen Kontos über unterschiedliche IP-Adressen

Im folgenden Beispiel wird nach nicht erfolgreichen Anmeldungen des gleichen Kontos gesucht, die innerhalb der letzten sechs Stunden von mehr als fünf verschiedenen IP-Adressen aus versucht wurden.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Suchen nach Benutzerkonten mit nicht erfolgreicher Anmeldung 
Im folgenden Beispiel wird ermittelt, bei welchen Benutzerkonten die Anmeldung innerhalb des letzten Tages mehr als fünf Mal nicht erfolgreich war und wann der letzten Anmeldeversuch stattgefunden hat.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Mithilfe der Anweisungen **join** und **let** lässt sich überprüfen, ob bei den gleichen verdächtigen Konten später eine erfolgreiche Anmeldung möglich war.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Verwendung

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Berechnen der durchschnittlichen Größe von Leistungsnutzungsberichten pro Computer

In diesem Beispiel wird die durchschnittliche Größe von Leistungsnutzungsberichten pro Computer für die letzten drei Stunden berechnet.
Die Ergebnisse werden in einem Balkendiagramm dargestellt.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Erstellen eines Zeitdiagramms für das 50. und 95. Wartezeit-Perzentil

In diesem Beispiel werden das 50. und das 95. Perzentil der gemeldeten durchschnittlichen Wartezeit (**avgLatency**) pro Stunde aus den letzten 24 Stunden berechnet und in einem Diagramm dargestellt.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Nutzung bestimmter Computer am aktuellen Tag
In diesem Beispiel werden für den letzten Tag Nutzungsdaten (**Usage**) für Computernamen abgerufen, die die Zeichenfolge _ContosoFile_ enthalten. Die Ergebnisse werden nach **TimeGenerated** sortiert.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Aktualisierungen

### <a name="computers-still-missing-updates"></a>Computer mit immer noch fehlenden Updates
In diesem Beispiel wird eine Liste mit Computern angezeigt, bei denen vor einigen Tagen mindestens ein kritisches Update fehlte und immer noch Updates fehlen.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zur Kusto-Sprache finden Sie in der entsprechenden [Sprachreferenz](/azure/kusto/query).
- Machen Sie sich mit dem [Schreiben von Protokollabfragen in Azure Monitor](get-started-queries.md) vertraut.
