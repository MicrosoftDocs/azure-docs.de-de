---
title: Von Azure Monitor unterstützte Metriken nach Ressourcentyp
description: Liste der Metriken, die mit Azure Monitor für jeden Ressourcentyp zur Verfügung stehen.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: f3907cbc46bd226ef8e90d2061f2d36b2afabf8a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957158"
---
# <a name="supported-metrics-with-azure-monitor"></a>Unterstützte Metriken von Azure Monitor

Azure Monitor bietet verschiedene Methoden für die Interaktion mit Metriken, z.B. die Diagrammdarstellung im Portal, den Zugriff über die REST-API oder die Abfrage über PowerShell oder CLI. Unten ist eine vollständige Liste aller Metriken aufgeführt, die derzeit mit der Metrikpipeline von Azure Monitor verfügbar sind. Weitere Metriken stehen möglicherweise im Portal oder über Legacy-APIs zur Verfügung. Die unten angegebene Liste enthält nur Metriken, die über die konsolidierte Azure Monitor-Metrikpipeline verfügbar sind. Verwenden Sie die [API-Version 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions), um diese Metriken abzufragen und darauf zuzugreifen.

> [!NOTE]
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel*: Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Durchschnitt|QPU. Bereich: 0–100 für S1, 0–200 für S2 und 0–400 für S4|ServerResourceType|
|memory_metric|Arbeitsspeicher|Byte|Durchschnitt|Arbeitsspeicher. Bereich: 0–25 GB für S1, 0–50 GB für S2 und 0–100 GB für S4|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests (Verbindungsanforderungen gesamt)|Count|Durchschnitt|Gesamtanzahl von Verbindungsanforderungen. Dies sind erhaltene Anforderungen.|ServerResourceType|
|SuccessfullConnectionsPerSec|Erfolgreiche Verbindungen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der erfolgreichen Verbindungsabschlüsse|ServerResourceType|
|TotalConnectionFailures|Verbindungsfehler gesamt|Count|Durchschnitt|Gesamtanzahl von fehlerhaften Verbindungsversuchen|ServerResourceType|
|CurrentUserSessions|Aktuelle Benutzersitzungen|Count|Durchschnitt|Aktuelle Anzahl von eingerichteten Benutzersitzungen|ServerResourceType|
|QueryPoolBusyThreads|Ausgelastete Abfragepoolthreads|Count|Durchschnitt|Anzahl von ausgelasteten Threads im Abfragethreadpool|ServerResourceType|
|CommandPoolJobQueueLength|Warteschlangenlänge für Aufträge im Befehlspool|Count|Durchschnitt|Gibt die Anzahl von Aufträgen in der Warteschlange des Befehlsthreadpools an.|ServerResourceType|
|ProcessingPoolJobQueueLength|Warteschlangenlänge für Verarbeitungspoolaufträge|Count|Durchschnitt|Anzahl von Nicht-E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools|ServerResourceType|
|CurrentConnections|Verbindung: Aktuelle Verbindungen|Count|Durchschnitt|Aktuelle Anzahl hergestellter Clientverbindungen.|ServerResourceType|
|CleanerCurrentPrice|Arbeitsspeicher: Bereinigung – aktueller Preis|Count|Durchschnitt|Aktueller Preis des Arbeitsspeichers, $/Byte/Zeit, normalisiert auf 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Arbeitsspeicher: Bereinigung – verkleinerbarer Arbeitsspeicher|Byte|Durchschnitt|Die Menge des Arbeitsspeichers in Byte, die durch den Hintergrundbereinigungsprozess bereinigt wird.|ServerResourceType|
|CleanerMemoryNonshrinkable|Arbeitsspeicher: Bereinigung – nicht verkleinerbarer Arbeitsspeicher|Byte|Durchschnitt|Die Menge des Arbeitsspeichers in Byte, die nicht durch den Hintergrundbereinigungsprozess bereinigt wird.|ServerResourceType|
|MemoryUsage|Arbeitsspeicher: Speicherauslastung|Byte|Durchschnitt|Speicherauslastung des Serverprozesses, wie bei der Berechnung des Arbeitsspeicherpreises für die Bereinigung verwendet. Entspricht dem Indikator „Process\PrivateBytes“ zuzüglich der Größe der im Speicher abgebildeten Daten. Von der xVelocity-In-Memory-Analyse-Engine (VertiPaq) abgebildeter oder belegter Arbeitsspeicher, der über die Arbeitsspeichergrenze der xVelocity-Engine hinausgeht, wird dabei ignoriert.|ServerResourceType|
|MemoryLimitHard|Arbeitsspeicher: Grenzwert für den festen Speicher|Byte|Durchschnitt|Grenzwert für den festen Speicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitHigh|Arbeitsspeicher: Obere Arbeitsspeichergrenze|Byte|Durchschnitt|Oberer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitLow|Arbeitsspeicher: Untere Arbeitsspeichergrenze|Byte|Durchschnitt|Unterer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitVertiPaq|Arbeitsspeicher: VertiPaq-Arbeitsspeichergrenze|Byte|Durchschnitt|In-Memory-Grenzwert gemäß Konfigurationsdatei.|ServerResourceType|
|Kontingent|Arbeitsspeicher: Kontingent|Byte|Durchschnitt|Aktuelles Arbeitsspeicherkontingent in Byte. Das Arbeitsspeicherkontingent wird auch als Speicherzuweisung oder Speicherreservierung bezeichnet.|ServerResourceType|
|QuotaBlocked|Arbeitsspeicher: Kontingent blockiert|Count|Durchschnitt|Aktuelle Anzahl von Kontingentanforderungen, die blockiert werden, bis andere Arbeitsspeicherkontingente freigegeben werden.|ServerResourceType|
|VertiPaqNonpaged|Arbeitsspeicher: Nicht ausgelagerte VertiPaq-Daten|Byte|Durchschnitt|Bytes von Arbeitsspeicher, die im Arbeitssatz zur Verwendung durch die In-Memory-Engine gesperrt sind.|ServerResourceType|
|VertiPaqPaged|Arbeitsspeicher: Ausgelagerte VertiPaq-Daten|Byte|Durchschnitt|Bytes von ausgelagertem Arbeitsspeicher, die für In-Memory-Daten verwendet werden.|ServerResourceType|
|RowsReadPerSec|Verarbeitung: Gelesene Zeilen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der aus allen relationalen Datenbanken gelesenen Zeilen.|ServerResourceType|
|RowsConvertedPerSec|Verarbeitung: Konvertierte Zeilen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der Zeilen, die bei der Verarbeitung konvertiert werden.|ServerResourceType|
|RowsWrittenPerSec|Verarbeitung: Geschriebene Zeilen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der Zeilen, die bei der Verarbeitung geschrieben werden.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Ausgelastete Threads im Befehlspool|Count|Durchschnitt|Anzahl ausgelasteter Threads im Befehlsthreadpool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Leerlaufthreads im Befehlspool|Count|Durchschnitt|Anzahl von Leerlaufthreads im Befehlsthreadpool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Ausgelastete lange Analysethreads|Count|Durchschnitt|Anzahl ausgelasteter Threads im Pool für lange Analysethreads.|ServerResourceType|
|LongParsingIdleThreads|Threads: Lange Analysethreads im Leerlauf|Count|Durchschnitt|Anzahl von Leerlaufthreads im Pool für lange Analysethreads.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Warteschlangenlänge für lange Analyseaufträge|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Pools für lange Analysethreads.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Ausgelastete Threads für E/A-Aufträge im Verarbeitungspool|Count|Durchschnitt|Anzahl von Threads, die E/A-Aufträge im Verarbeitungsthreadpool ausführen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Ausgelastete Nicht-E/A-Threads im Verarbeitungspool|Count|Durchschnitt|Anzahl von Threads, die Nicht-E/A-Aufträge im Verarbeitungsthreadpool ausführen.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Warteschlangenlänge für E/A-Aufträge im Verarbeitungspool|Count|Durchschnitt|Anzahl von E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Leerlaufthreads für E/A-Aufträge im Verarbeitungspool|Count|Durchschnitt|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Nicht-E/A-Leerlaufthreads im Verarbeitungspool|Count|Durchschnitt|Anzahl von Leerlaufthreads im Verarbeitungsthreadpool, die für Nicht-E/A-Aufträge vorgesehen sind.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Abfragepoolthreads im Leerlauf|Count|Durchschnitt|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Auftragswarteschlangenlänge für Abfragepool|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Ausgelastete kurze Analysethreads|Count|Durchschnitt|Anzahl ausgelasteter Threads im Pool für kurze Analysethreads.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Kurze Analysethreads im Leerlauf|Count|Durchschnitt|Anzahl von Leerlaufthreads im Pool für kurze Analysethreads.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Warteschlangenlänge für kurze Analyseaufträge|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Pools für kurze Analysethreads.|ServerResourceType|
|memory_thrashing_metric|Arbeitsspeicherüberlastung|Prozent|Durchschnitt|Durchschnittliche Arbeitsspeicherüberlastung.|ServerResourceType|
|mashup_engine_qpu_metric|M-Engine – QPU|Count|Durchschnitt|QPU-Nutzung durch Mashup-Engine-Prozesse|ServerResourceType|
|mashup_engine_memory_metric|M-Engine – Arbeitsspeicher|Byte|Durchschnitt|Arbeitsspeichernutzung durch Mashup-Engine-Prozesse|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests (Gatewayanforderungen gesamt)|Count|Gesamt|Anzahl von Gatewayanforderungen|Speicherort, Hostname|
|SuccessfulRequests|Successful Gateway Requests (Erfolgreiche Gatewayanforderungen)|Count|Gesamt|Anzahl von erfolgreichen Gatewayanforderungen|Speicherort, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests (Nicht autorisierte Gatewayanforderungen)|Count|Gesamt|Anzahl von nicht autorisierten Gatewayanforderungen|Speicherort, Hostname|
|FailedRequests|Failed Gateway Requests (Fehlgeschlagene Gatewayanforderungen)|Count|Gesamt|Anzahl von Fehlern bei Gatewayanforderungen|Speicherort, Hostname|
|OtherRequests|Other Gateway Requests (Sonstige Gatewayanforderungen)|Count|Gesamt|Anzahl von anderen Gatewayanforderungen|Speicherort, Hostname|
|Duration|Gesamtdauer von Gatewayanforderungen|Millisekunden|Durchschnitt|Gesamtdauer von Gatewayanforderungen in Millisekunden|Speicherort, Hostname|
|Capacity|Capacity|Prozent|Durchschnitt|Auslastungsmetrik für ApiManagement-Dienst|Location|
|EventHubTotalEvents|Gesamtzahl von EventHub-Ereignissen|Count|Gesamt|Anzahl von an EventHub gesendeten Ereignissen|Location|
|EventHubSuccessfulEvents|Erfolgreiche EventHub-Ereignisse|Count|Gesamt|Anzahl erfolgreicher EventHub-Ereignisse|Location|
|EventHubTotalFailedEvents|Fehlerhafte EventHub-Ereignisse|Count|Gesamt|Anzahl fehlerhafter EventHub-Ereignisse|Location|
|EventHubRejectedEvents|Abgelehnte EventHub-Ereignisse|Count|Gesamt|Anzahl abgelehnter EventHub-Ereignisse (falsche Konfiguration oder nicht autorisiert)|Location|
|EventHubThrottledEvents|Gedrosselte EventHub-Ereignisse|Count|Gesamt|Anzahl gedrosselter EventHub-Ereignisse|Location|
|EventHubTimedoutEvents|EventHub-Ereignisse mit Zeitüberschreitung|Count|Gesamt|Anzahl von EventHub-Ereignissen mit Zeitüberschreitung|Location|
|EventHubDroppedEvents|Gelöschte EventHub-Ereignisse|Count|Gesamt|Anzahl übersprungener Ereignisse aufgrund Erreichen der maximalen Warteschlangengröße|Location|
|EventHubTotalBytesSent|Größe von EventHub-Ereignissen|Byte|Gesamt|Gesamtgröße von EventHub-Ereignissen in Byte|Location|
|Requests|Requests|Count|Gesamt|Gatewayanforderungen|Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|TotalJob|Gesamtzahl an Einzelvorgängen (Jobs)|Count|Gesamt|Die Gesamtzahl an Einzelvorgängen (Jobs)|Runbook, Status|
|TotalUpdateDeploymentRuns|Gesamtzahl von Updatebereitstellungsausführungen|Count|Gesamt|Gesamtzahl von Updatebereitstellungsausführungen für Software|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Gesamtzahl von Updatebereitstellungsausführungen auf dem Computer|Count|Gesamt|Gesamtzahl von Updatebereitstellungsausführungen für Software auf dem Computer in einer Updatebereitstellungsausführung für Software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CoreCount|Dedizierte Anzahl von Kernen|Count|Gesamt|Gesamtzahl der dedizierten Kerne im Batch-Konto|Keine Dimensionen|
|TotalNodeCount|Dedizierte Knotenanzahl|Count|Gesamt|Gesamtzahl der dedizierten Knoten im Batch-Konto|Keine Dimensionen|
|LowPriorityCoreCount|Anzahl von LowPriority-Kernen|Count|Gesamt|Gesamtzahl der Kerne mit niedriger Priorität im Batch-Konto|Keine Dimensionen|
|TotalLowPriorityNodeCount|Anzahl der Knoten mit niedriger Priorität|Count|Gesamt|Gesamtzahl der Knoten mit niedriger Priorität im Batchkonto|Keine Dimensionen|
|CreatingNodeCount|Anzahl erstellter Knoten|Count|Gesamt|Anzahl von Knoten, die gerade erstellt werden|Keine Dimensionen|
|StartingNodeCount|Anzahl gestarteter Knoten|Count|Gesamt|Anzahl von Knoten, die gerade gestartet werden|Keine Dimensionen|
|WaitingForStartTaskNodeCount|Anzahl von Knoten, die auf den Starttask warten|Count|Gesamt|Anzahl von Knoten, die auf den Abschluss des Starttasks warten|Keine Dimensionen|
|StartTaskFailedNodeCount|Anzahl von Knoten mit Starttaskfehlern|Count|Gesamt|Anzahl von Knoten, bei denen der Starttask nicht durchgeführt werden konnte|Keine Dimensionen|
|IdleNodeCount|Anzahl von Knoten im Leerlauf|Count|Gesamt|Anzahl von Knoten im Leerlauf|Keine Dimensionen|
|OfflineNodeCount|Anzahl von Offlineknoten|Count|Gesamt|Anzahl offline geschalteter Knoten|Keine Dimensionen|
|RebootingNodeCount|Anzahl neu gestarteter Knoten|Count|Gesamt|Anzahl von Knoten, die neu gestartet werden|Keine Dimensionen|
|ReimagingNodeCount|Anzahl von Knoten mit Reimaging|Count|Gesamt|Anzahl von Knoten, für die ein Reimaging durchgeführt wird|Keine Dimensionen|
|RunningNodeCount|Anzahl ausgeführter Knoten|Count|Gesamt|Anzahl ausgeführter Knoten|Keine Dimensionen|
|LeavingPoolNodeCount|Anzahl von Knoten, die den Pool verlassen|Count|Gesamt|Anzahl von Knoten, die den Pool verlassen|Keine Dimensionen|
|UnusableNodeCount|Anzahl nicht verwendbarer Knoten|Count|Gesamt|Anzahl nicht verwendbarer Knoten|Keine Dimensionen|
|PreemptedNodeCount|Anzahl der vorzeitig entfernten Knoten|Count|Gesamt|Die Anzahl der vorzeitig entfernten Knoten|Keine Dimensionen|
|TaskStartEvent|Taskstartereignisse|Count|Gesamt|Gesamtanzahl gestarteter Tasks|Keine Dimensionen|
|TaskCompleteEvent|Taskabschlussereignisse|Count|Gesamt|Gesamtanzahl abgeschlossener Tasks|Keine Dimensionen|
|TaskFailEvent|Taskfehlerereignisse|Count|Gesamt|Gesamtanzahl von Tasks, die mit Fehlerstatus abgeschlossen wurden|Keine Dimensionen|
|PoolCreateEvent|Poolerstellungsereignisse|Count|Gesamt|Gesamtanzahl erstellter Pools|Keine Dimensionen|
|PoolResizeStartEvent|Ereignisse zum Start der Größenänderung von Pools|Count|Gesamt|Gesamtanzahl gestarteter Größenänderungen von Pools|Keine Dimensionen|
|PoolResizeCompleteEvent|Ereignisse zum Abschluss der Größenänderung von Pools|Count|Gesamt|Gesamtanzahl abgeschlossener Größenänderungen von Pools|Keine Dimensionen|
|PoolDeleteStartEvent|Ereignisse zum Starten des Löschvorgangs von Pools|Count|Gesamt|Gesamtanzahl gestarteter Poollöschvorgänge|Keine Dimensionen|
|PoolDeleteCompleteEvent|Ereignisse zum Abschluss des Löschvorgangs von Pools|Count|Gesamt|Gesamtanzahl abgeschlossener Poollöschvorgänge|Keine Dimensionen|
|JobDeleteCompleteEvent|Ereignisse zum Abschluss des Löschvorgangs von Aufträgen|Count|Gesamt|Gesamtanzahl der erfolgreich gelöschten Aufträge|Keine Dimensionen|
|JobDeleteStartEvent|Ereignisse zum Starten des Löschvorgangs von Aufträgen|Count|Gesamt|Gesamtanzahl der zum Löschen angeforderten Aufträge|Keine Dimensionen|
|JobDisableCompleteEvent|Ereignisse zum Abschluss der Deaktivierung von Aufträgen|Count|Gesamt|Gesamtanzahl der erfolgreich deaktivierten Aufträge|Keine Dimensionen|
|JobDisableStartEvent|Ereignisse zum Starten der Deaktivierung von Aufträgen|Count|Gesamt|Gesamtanzahl der zum Deaktivieren angeforderten Aufträge|Keine Dimensionen|
|JobStartEvent|Ereignisse zum Starten von Aufträgen|Count|Gesamt|Gesamtanzahl der erfolgreich gestarteten Aufträge|Keine Dimensionen|
|JobTerminateCompleteEvent|Ereignisse zum Abschluss der Beendigung von Aufträgen|Count|Gesamt|Gesamtanzahl der erfolgreich beendeten Aufträge|Keine Dimensionen|
|JobTerminateStartEvent|Ereignisse zum Starten der Beendigung von Aufträgen|Count|Gesamt|Gesamtanzahl der zum Beenden angeforderten Aufträge|Keine Dimensionen|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|connectedclients|Verbundene Clients|Count|Maximum||ShardId|
|totalcommandsprocessed|Vorgänge gesamt|Count|Gesamt||ShardId|
|cachehits|Cachetreffer|Count|Gesamt||ShardId|
|cachemisses|Cachefehler|Count|Gesamt||ShardId|
|getcommands|get-Vorgänge|Count|Gesamt||ShardId|
|setcommands|set-Vorgänge|Count|Gesamt||ShardId|
|operationsPerSecond|Vorgänge pro Sekunde|Count|Maximum||ShardId|
|evictedkeys|Entfernte Schlüssel|Count|Gesamt||ShardId|
|totalkeys|Schlüssel insgesamt|Count|Maximum||ShardId|
|expiredkeys|Abgelaufene Schlüssel|Count|Gesamt||ShardId|
|usedmemory|Verwendeter Arbeitsspeicher|Byte|Maximum||ShardId|
|usedmemorypercentage|Prozentsatz der Arbeitsspeicherverwendung|Prozent|Maximum||ShardId|
|usedmemoryRss|Verwendeter Arbeitsspeicher (RSS)|Byte|Maximum||ShardId|
|serverLoad|Serverlast|Prozent|Maximum||ShardId|
|cacheWrite|Cache-Schreibvorgänge|Bytes pro Sekunde|Maximum||ShardId|
|cacheRead|Cache-Lesevorgänge|Bytes pro Sekunde|Maximum||ShardId|
|percentProcessorTime|CPU|Prozent|Maximum||ShardId|
|cacheLatency|Cachewartezeit in Mikrosekunden (Vorschau)|Count|Durchschnitt||ShardId, SampleType|
|errors|Errors|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Verbundene Clients (Shard 0)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed0|Vorgänge gesamt (Shard 0)|Count|Gesamt||Keine Dimensionen|
|cachehits0|Cachetreffer (Shard 0)|Count|Gesamt||Keine Dimensionen|
|cachemisses0|Cachefehler (Shard 0)|Count|Gesamt||Keine Dimensionen|
|getcommands0|Get-Vorgänge (Shard 0)|Count|Gesamt||Keine Dimensionen|
|setcommands0|Set-Vorgänge (Shard 0)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond0|Vorgänge pro Sekunde (Shard 0)|Count|Maximum||Keine Dimensionen|
|evictedkeys0|Entfernte Schlüssel (Shard 0)|Count|Gesamt||Keine Dimensionen|
|totalkeys0|Schlüssel gesamt (Shard 0)|Count|Maximum||Keine Dimensionen|
|expiredkeys0|Abgelaufene Schlüssel (Shard 0)|Count|Gesamt||Keine Dimensionen|
|usedmemory0|Verwendeter Arbeitsspeicher (Shard 0)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss0|Verwendeter Arbeitsspeicher (RSS, Shard 0)|Byte|Maximum||Keine Dimensionen|
|serverLoad0|Serverauslastung (Shard 0)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite0|Cacheschreibvorgänge (Shard 0)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead0|Cachelesevorgänge (Shard 0)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime0|CPU (Shard 0)|Prozent|Maximum||Keine Dimensionen|
|connectedclients1|Verbundene Clients (Shard 1)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed1|Vorgänge gesamt (Shard 1)|Count|Gesamt||Keine Dimensionen|
|cachehits1|Cachetreffer (Shard 1)|Count|Gesamt||Keine Dimensionen|
|cachemisses1|Cachefehler (Shard 1)|Count|Gesamt||Keine Dimensionen|
|getcommands1|Get-Vorgänge (Shard 1)|Count|Gesamt||Keine Dimensionen|
|setcommands1|Set-Vorgänge (Shard 1)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond1|Vorgänge pro Sekunde (Shard 1)|Count|Maximum||Keine Dimensionen|
|evictedkeys1|Entfernte Schlüssel (Shard 1)|Count|Gesamt||Keine Dimensionen|
|totalkeys1|Schlüssel gesamt (Shard 1)|Count|Maximum||Keine Dimensionen|
|expiredkeys1|Abgelaufene Schlüssel (Shard 1)|Count|Gesamt||Keine Dimensionen|
|usedmemory1|Verwendeter Arbeitsspeicher (Shard 1)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss1|Verwendeter Arbeitsspeicher (RSS, Shard 1)|Byte|Maximum||Keine Dimensionen|
|serverLoad1|Serverauslastung (Shard 1)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite1|Cacheschreibvorgänge (Shard 1)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead1|Cachelesevorgänge (Shard 1)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime1|CPU (Shard 1)|Prozent|Maximum||Keine Dimensionen|
|connectedclients2|Verbundene Clients (Shard 2)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed2|Vorgänge gesamt (Shard 2)|Count|Gesamt||Keine Dimensionen|
|cachehits2|Cachetreffer (Shard 2)|Count|Gesamt||Keine Dimensionen|
|cachemisses2|Cachefehler (Shard 2)|Count|Gesamt||Keine Dimensionen|
|getcommands2|Get-Vorgänge (Shard 2)|Count|Gesamt||Keine Dimensionen|
|setcommands2|Set-Vorgänge (Shard 2)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond2|Vorgänge pro Sekunde (Shard 2)|Count|Maximum||Keine Dimensionen|
|evictedkeys2|Entfernte Schlüssel (Shard 2)|Count|Gesamt||Keine Dimensionen|
|totalkeys2|Schlüssel gesamt (Shard 2)|Count|Maximum||Keine Dimensionen|
|expiredkeys2|Abgelaufene Schlüssel (Shard 2)|Count|Gesamt||Keine Dimensionen|
|usedmemory2|Verwendeter Arbeitsspeicher (Shard 2)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss2|Verwendeter Arbeitsspeicher (RSS, Shard 2)|Byte|Maximum||Keine Dimensionen|
|serverLoad2|Serverauslastung (Shard 2)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite2|Cacheschreibvorgänge (Shard 2)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead2|Cachelesevorgänge (Shard 2)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime2|CPU (Shard 2)|Prozent|Maximum||Keine Dimensionen|
|connectedclients3|Verbundene Clients (Shard 3)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed3|Vorgänge gesamt (Shard 3)|Count|Gesamt||Keine Dimensionen|
|cachehits3|Cachetreffer (Shard 3)|Count|Gesamt||Keine Dimensionen|
|cachemisses3|Cachefehler (Shard 3)|Count|Gesamt||Keine Dimensionen|
|getcommands3|Get-Vorgänge (Shard 3)|Count|Gesamt||Keine Dimensionen|
|setcommands3|Set-Vorgänge (Shard 3)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond3|Vorgänge pro Sekunde (Shard 3)|Count|Maximum||Keine Dimensionen|
|evictedkeys3|Entfernte Schlüssel (Shard 3)|Count|Gesamt||Keine Dimensionen|
|totalkeys3|Schlüssel gesamt (Shard 3)|Count|Maximum||Keine Dimensionen|
|expiredkeys3|Abgelaufene Schlüssel (Shard 3)|Count|Gesamt||Keine Dimensionen|
|usedmemory3|Verwendeter Arbeitsspeicher (Shard 3)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss3|Verwendeter Arbeitsspeicher (RSS, Shard 3)|Byte|Maximum||Keine Dimensionen|
|serverLoad3|Serverauslastung (Shard 3)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite3|Cacheschreibvorgänge (Shard 3)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead3|Cachelesevorgänge (Shard 3)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime3|CPU (Shard 3)|Prozent|Maximum||Keine Dimensionen|
|connectedclients4|Verbundene Clients (Shard 4)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed4|Vorgänge gesamt (Shard 4)|Count|Gesamt||Keine Dimensionen|
|cachehits4|Cachetreffer (Shard 4)|Count|Gesamt||Keine Dimensionen|
|cachemisses4|Cachefehler (Shard 4)|Count|Gesamt||Keine Dimensionen|
|getcommands4|Get-Vorgänge (Shard 4)|Count|Gesamt||Keine Dimensionen|
|setcommands4|Set-Vorgänge (Shard 4)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond4|Vorgänge pro Sekunde (Shard 4)|Count|Maximum||Keine Dimensionen|
|evictedkeys4|Entfernte Schlüssel (Shard 4)|Count|Gesamt||Keine Dimensionen|
|totalkeys4|Schlüssel gesamt (Shard 4)|Count|Maximum||Keine Dimensionen|
|expiredkeys4|Abgelaufene Schlüssel (Shard 4)|Count|Gesamt||Keine Dimensionen|
|usedmemory4|Verwendeter Arbeitsspeicher (Shard 4)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss4|Verwendeter Arbeitsspeicher (RSS, Shard 4)|Byte|Maximum||Keine Dimensionen|
|serverLoad4|Serverauslastung (Shard 4)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite4|Cacheschreibvorgänge (Shard 4)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead4|Cachelesevorgänge (Shard 4)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime4|CPU (Shard 4)|Prozent|Maximum||Keine Dimensionen|
|connectedclients5|Verbundene Clients (Shard 5)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed5|Vorgänge gesamt (Shard 5)|Count|Gesamt||Keine Dimensionen|
|cachehits5|Cachetreffer (Shard 5)|Count|Gesamt||Keine Dimensionen|
|cachemisses5|Cachefehler (Shard 5)|Count|Gesamt||Keine Dimensionen|
|getcommands5|Get-Vorgänge (Shard 5)|Count|Gesamt||Keine Dimensionen|
|setcommands5|Set-Vorgänge (Shard 5)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond5|Vorgänge pro Sekunde (Shard 5)|Count|Maximum||Keine Dimensionen|
|evictedkeys5|Entfernte Schlüssel (Shard 5)|Count|Gesamt||Keine Dimensionen|
|totalkeys5|Schlüssel gesamt (Shard 5)|Count|Maximum||Keine Dimensionen|
|expiredkeys5|Abgelaufene Schlüssel (Shard 5)|Count|Gesamt||Keine Dimensionen|
|usedmemory5|Verwendeter Arbeitsspeicher (Shard 5)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss5|Verwendeter Arbeitsspeicher (RSS, Shard 5)|Byte|Maximum||Keine Dimensionen|
|serverLoad5|Serverauslastung (Shard 5)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite5|Cacheschreibvorgänge (Shard 5)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead5|Cachelesevorgänge (Shard 5)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime5|CPU (Shard 5)|Prozent|Maximum||Keine Dimensionen|
|connectedclients6|Verbundene Clients (Shard 6)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed6|Vorgänge gesamt (Shard 6)|Count|Gesamt||Keine Dimensionen|
|cachehits6|Cachetreffer (Shard 6)|Count|Gesamt||Keine Dimensionen|
|cachemisses6|Cachefehler (Shard 6)|Count|Gesamt||Keine Dimensionen|
|getcommands6|Get-Vorgänge (Shard 6)|Count|Gesamt||Keine Dimensionen|
|setcommands6|Set-Vorgänge (Shard 6)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond6|Vorgänge pro Sekunde (Shard 6)|Count|Maximum||Keine Dimensionen|
|evictedkeys6|Entfernte Schlüssel (Shard 6)|Count|Gesamt||Keine Dimensionen|
|totalkeys6|Schlüssel gesamt (Shard 6)|Count|Maximum||Keine Dimensionen|
|expiredkeys6|Abgelaufene Schlüssel (Shard 6)|Count|Gesamt||Keine Dimensionen|
|usedmemory6|Verwendeter Arbeitsspeicher (Shard 6)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss6|Verwendeter Arbeitsspeicher (RSS, Shard 6)|Byte|Maximum||Keine Dimensionen|
|serverLoad6|Serverauslastung (Shard 6)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite6|Cacheschreibvorgänge (Shard 6)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead6|Cachelesevorgänge (Shard 6)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime6|CPU (Shard 6)|Prozent|Maximum||Keine Dimensionen|
|connectedclients7|Verbundene Clients (Shard 7)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed7|Vorgänge gesamt (Shard 7)|Count|Gesamt||Keine Dimensionen|
|cachehits7|Cachetreffer (Shard 7)|Count|Gesamt||Keine Dimensionen|
|cachemisses7|Cachefehler (Shard 7)|Count|Gesamt||Keine Dimensionen|
|getcommands7|Get-Vorgänge (Shard 7)|Count|Gesamt||Keine Dimensionen|
|setcommands7|Set-Vorgänge (Shard 7)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond7|Vorgänge pro Sekunde (Shard 7)|Count|Maximum||Keine Dimensionen|
|evictedkeys7|Entfernte Schlüssel (Shard 7)|Count|Gesamt||Keine Dimensionen|
|totalkeys7|Schlüssel gesamt (Shard 7)|Count|Maximum||Keine Dimensionen|
|expiredkeys7|Abgelaufene Schlüssel (Shard 7)|Count|Gesamt||Keine Dimensionen|
|usedmemory7|Verwendeter Arbeitsspeicher (Shard 7)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss7|Verwendeter Arbeitsspeicher (RSS, Shard 7)|Byte|Maximum||Keine Dimensionen|
|serverLoad7|Serverauslastung (Shard 7)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite7|Cacheschreibvorgänge (Shard 7)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead7|Cachelesevorgänge (Shard 7)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime7|CPU (Shard 7)|Prozent|Maximum||Keine Dimensionen|
|connectedclients8|Verbundene Clients (Shard 8)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed8|Vorgänge gesamt (Shard 8)|Count|Gesamt||Keine Dimensionen|
|cachehits8|Cachetreffer (Shard 8)|Count|Gesamt||Keine Dimensionen|
|cachemisses8|Cachefehler (Shard 8)|Count|Gesamt||Keine Dimensionen|
|getcommands8|Get-Vorgänge (Shard 8)|Count|Gesamt||Keine Dimensionen|
|setcommands8|Set-Vorgänge (Shard 8)|Count|Gesamt||Keine Dimensionen|
|operationsPerSecond8|Vorgänge pro Sekunde (Shard 8)|Count|Maximum||Keine Dimensionen|
|evictedkeys8|Entfernte Schlüssel (Shard 8)|Count|Gesamt||Keine Dimensionen|
|totalkeys8|Schlüssel gesamt (Shard 8)|Count|Maximum||Keine Dimensionen|
|expiredkeys8|Abgelaufene Schlüssel (Shard 8)|Count|Gesamt||Keine Dimensionen|
|usedmemory8|Verwendeter Arbeitsspeicher (Shard 8)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss8|Verwendeter Arbeitsspeicher (RSS, Shard 8)|Byte|Maximum||Keine Dimensionen|
|serverLoad8|Serverauslastung (Shard 8)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite8|Cacheschreibvorgänge (Shard 8)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead8|Cachelesevorgänge (Shard 8)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime8|CPU (Shard 8)|Prozent|Maximum||Keine Dimensionen|
|connectedclients9|Verbundene Clients (Shard 9)|Count|Maximum||Keine Dimensionen|
|totalcommandsprocessed9|Vorgänge gesamt (Shard 9)|Count|Gesamt||Keine Dimensionen|
|cachehits9|Cachetreffer (Shard 9)|Count|Gesamt||Keine Dimensionen|
|cachemisses9|Cachefehler (Shard 9)|Count|Gesamt||Keine Dimensionen|
|getcommands9|Get-Vorgänge (Shard 9)|Count|Gesamt||Keine Dimensionen|
|setcommands9|Set-Vorgänge (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|operationsPerSecond9|Vorgänge pro Sekunde (Shard 9)|Count|Maximum||Keine Dimensionen|
|evictedkeys9|Entfernte Schlüssel (Shard 9)|Count|Gesamt||Keine Dimensionen|
|totalkeys9|Schlüssel gesamt (Shard 9)|Count|Maximum||Keine Dimensionen|
|expiredkeys9|Abgelaufene Schlüssel (Shard 9)|Count|Gesamt||Keine Dimensionen|
|usedmemory9|Verwendeter Arbeitsspeicher (Shard 9)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss9|Verwendeter Arbeitsspeicher (RSS, Shard 9)|Byte|Maximum||Keine Dimensionen|
|serverLoad9|Serverauslastung (Shard 9)|Prozent|Maximum||Keine Dimensionen|
|cacheWrite9|Cacheschreibvorgänge (Shard 9)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead9|Cachelesevorgänge (Shard 9)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|percentProcessorTime9|CPU (Shard 9)|Prozent|Maximum||Keine Dimensionen|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CPU in Prozent|CPU in Prozent|Prozent|Durchschnitt|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|
|Netzwerk eingehend|Netzwerk eingehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Netzwerk ausgehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|Disk Read Bytes/Sec|Datenträgerlesevorgänge|Bytes pro Sekunde|Durchschnitt|Durchschnitt an Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Disk Write Bytes/Sec|Datenträgerschreibvorgänge|Bytes pro Sekunde|Durchschnitt|Durchschnitt an Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CPU in Prozent|CPU in Prozent|Prozent|Durchschnitt|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|RoleInstanceId|
|Netzwerk eingehend|Netzwerk eingehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|RoleInstanceId|
|Netzwerk ausgehend|Netzwerk ausgehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|RoleInstanceId|
|Disk Read Bytes/Sec|Datenträgerlesevorgänge|Bytes pro Sekunde|Durchschnitt|Durchschnitt an Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|RoleInstanceId|
|Disk Write Bytes/Sec|Datenträgerschreibvorgänge|Bytes pro Sekunde|Durchschnitt|Durchschnitt an Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|RoleInstanceId|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerlesevorgänge in IOPS|RoleInstanceId|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerschreibvorgänge in IOPS|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|TotalCalls|Aufrufe gesamt|Count|Gesamt|Gesamtanzahl von Aufrufen|ApiName, OperationName, Region|
|SuccessfulCalls|Erfolgreiche Aufrufe|Count|Gesamt|Anzahl erfolgreicher Aufrufe|ApiName, OperationName, Region|
|TotalErrors|Fehler insgesamt|Count|Gesamt|Gesamtzahl von Aufrufen mit Fehlerantwort (HTTP-Antwortcode 4xx oder 5xx)|ApiName, OperationName, Region|
|BlockedCalls|Blockierte Aufrufe|Count|Gesamt|Anzahl von Aufrufen, die das Raten- oder Kontingentlimit überschritten haben|ApiName, OperationName, Region|
|ServerErrors|Serverfehler|Count|Gesamt|Anzahl von Aufrufen mit internem Dienstfehler (HTTP-Antwortcode 5xx)|ApiName, OperationName, Region|
|ClientErrors|Clientfehler|Count|Gesamt|Anzahl von Aufrufen mit Fehler auf Clientseite (HTTP-Antwortcode 4xx)|ApiName, OperationName, Region|
|DataIn|Eingehende Daten|Byte|Gesamt|Menge eingehender Daten in Byte|ApiName, OperationName, Region|
|DataOut|Datenausgabe|Byte|Gesamt|Menge ausgehender Daten in Byte|ApiName, OperationName, Region|
|Latency|Latency|Millisekunden|Durchschnitt|Latenz in Millisekunden|ApiName, OperationName, Region|
|CharactersTranslated|Übersetzte Zeichen|Count|Gesamt|Gesamtanzahl von Zeichen in einer eingehenden Textanforderung|ApiName, OperationName, Region|
|CharactersTrained|Trainierte Zeichen|Count|Gesamt|Gesamtzahl trainierter Zeichen|ApiName, OperationName, Region|
|SpeechSessionDuration|Dauer der Sprachsitzung|Sekunden|Gesamt|Gesamtdauer der Sprachsitzung in Sekunden|ApiName, OperationName, Region|
|TotalTransactions|Transaktionen gesamt|Count|Gesamt|Gesamtanzahl von Transaktionen|Keine Dimensionen|
|TotalTokenCalls|Tokenaufrufe gesamt|Count|Gesamt|Gesamtanzahl von Tokenaufrufen|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CPU in Prozent|CPU in Prozent|Prozent|Durchschnitt|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|
|Netzwerk eingehend|Abrechenbarer eingehender Netzwerkverkehr|Byte|Gesamt|Die Anzahl abrechenbarer Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Abrechenbarer ausgehender Netzwerkverkehr|Byte|Gesamt|Die Anzahl abrechenbarer Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|Datenträgerlesevorgänge in Bytes|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Verbleibende CPU-Guthaben|Count|Durchschnitt|Gesamtanzahl von Guthaben, die für den Burst verfügbar sind|Keine Dimensionen|
|Verbrauchte CPU-Guthaben|Verbrauchte CPU-Guthaben|Count|Durchschnitt|Gesamtanzahl von Guthaben, die vom virtuellen Computer verwendet werden|Keine Dimensionen|
|Gelesene Bytes pro Datenträger/Sek.|Vom Datenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Geschriebene Bytes pro Datenträger/Sek.|Auf den Datenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Lesevorgänge pro Datenträger/Sek.|Datenträgerlesevorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Schreibvorgänge pro Datenträger/Sek.|Datenträgerschreibvorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Warteschlangentiefe pro Datenträger|Warteschlangentiefe für Datenträger (veraltet)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|SlotId|
|Gelesene Bytes pro Betriebssystemdatenträger/Sek.|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Geschriebene Bytes pro Betriebssystemdatenträger/Sek.|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Lesevorgänge pro Betriebssystemdatenträger/Sek.|Lesevorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Schreibvorgänge pro Betriebssystemdatenträger/Sek.|Schreibvorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Warteschlangentiefe pro Betriebssystemdatenträger|Warteschlangentiefe für Betriebssystemdatenträger (veraltet)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Vom Datenträger gelesene Bytes/Sek.|Vom Datenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN|
|Auf den Datenträger geschriebene Bytes/Sek.|Auf den Datenträger geschriebene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Warteschlangentiefe für Datenträger|Warteschlangentiefe für Datenträger (Vorschauversion)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Warteschlangentiefe für Betriebssystemdatenträger|Warteschlangentiefe für Betriebssystemdatenträger (Vorschauversion)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Eingehende Datenflüsse|Eingehende Datenflüsse (Vorschauversion)|Count|Durchschnitt|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Ausgehende Datenflüsse|Eingehende Datenflüsse (Vorschauversion)|Count|Durchschnitt|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für eingehende Datenflüsse|Maximale Erstellungsrate für eingehende Datenflüsse (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Maximale Erstellungsrate für ausgehende Datenflüsse (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Treffer beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Prozent|Durchschnitt|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Fehler beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Prozent|Durchschnitt|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Prozent|Durchschnitt|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Prozent|Durchschnitt|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Eingehender Netzwerkverkehr gesamt|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Ausgehender Netzwerkverkehr gesamt|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CPU in Prozent|CPU in Prozent|Prozent|Durchschnitt|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|VMName|
|Netzwerk eingehend|Abrechenbarer eingehender Netzwerkverkehr|Byte|Gesamt|Die Anzahl abrechenbarer Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|VMName|
|Netzwerk ausgehend|Abrechenbarer ausgehender Netzwerkverkehr|Byte|Gesamt|Die Anzahl abrechenbarer Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|VMName|
|Datenträgerlesevorgänge in Bytes|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|VMName|
|Datenträgerschreibvorgänge in Bytes|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|VMName|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerlesevorgänge in IOPS|VMName|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Datenträgerschreibvorgänge in IOPS|VMName|
|Verbleibende CPU-Guthaben|Verbleibende CPU-Guthaben|Count|Durchschnitt|Gesamtanzahl von Guthaben, die für den Burst verfügbar sind|Keine Dimensionen|
|Verbrauchte CPU-Guthaben|Verbrauchte CPU-Guthaben|Count|Durchschnitt|Gesamtanzahl von Guthaben, die vom virtuellen Computer verwendet werden|Keine Dimensionen|
|Gelesene Bytes pro Datenträger/Sek.|Vom Datenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Geschriebene Bytes pro Datenträger/Sek.|Auf den Datenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Lesevorgänge pro Datenträger/Sek.|Datenträgerlesevorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Schreibvorgänge pro Datenträger/Sek.|Datenträgerschreibvorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Warteschlangentiefe pro Datenträger|Warteschlangentiefe für Datenträger (veraltet)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|SlotId|
|Gelesene Bytes pro Betriebssystemdatenträger/Sek.|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Geschriebene Bytes pro Betriebssystemdatenträger/Sek.|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Lesevorgänge pro Betriebssystemdatenträger/Sek.|Lesevorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Schreibvorgänge pro Betriebssystemdatenträger/Sek.|Schreibvorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Warteschlangentiefe pro Betriebssystemdatenträger|Warteschlangentiefe für Betriebssystemdatenträger (veraltet)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Vom Datenträger gelesene Bytes/Sek.|Vom Datenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN, VMName|
|Auf den Datenträger geschriebene Bytes/Sek.|Auf den Datenträger geschriebene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN, VMName|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN, VMName|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN, VMName|
|Warteschlangentiefe für Datenträger|Warteschlangentiefe für Datenträger (Vorschauversion)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN, VMName|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|VMName|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Anzahl pro Sekunde|Durchschnitt|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|VMName|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|VMName|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Durchschnitt|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|VMName|
|Warteschlangentiefe für Betriebssystemdatenträger|Warteschlangentiefe für Betriebssystemdatenträger (Vorschauversion)|Count|Durchschnitt|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|VMName|
|Eingehende Datenflüsse|Eingehende Datenflüsse (Vorschauversion)|Count|Durchschnitt|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|VMName|
|Ausgehende Datenflüsse|Eingehende Datenflüsse (Vorschauversion)|Count|Durchschnitt|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|VMName|
|Maximale Erstellungsrate für eingehende Datenflüsse|Maximale Erstellungsrate für eingehende Datenflüsse (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|VMName|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Maximale Erstellungsrate für ausgehende Datenflüsse (Vorschauversion)|Anzahl pro Sekunde|Durchschnitt|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|VMName|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Treffer beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Prozent|Durchschnitt|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN, VMName|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Fehler beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Prozent|Durchschnitt|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN, VMName|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Prozent|Durchschnitt|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|VMName|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Prozent|Durchschnitt|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|VMName|
|Eingehender Netzwerkverkehr gesamt|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|VMName|
|Ausgehender Netzwerkverkehr gesamt|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CpuUsage|CPU-Auslastung|Count|Durchschnitt|CPU-Auslastung für alle Kerne in Millicores|containerName|
|MemoryUsage|Speicherauslastung|Byte|Durchschnitt|Gesamtspeicherauslastung in Byte|containerName|
|NetworkBytesReceivedPerSecond|Empfangene Netzwerkbytes pro Sekunde|Byte|Durchschnitt|Die pro Sekunde empfangenen Netzwerkbytes.|Keine Dimensionen|
|NetworkBytesTransmittedPerSecond|Übertragene Netzwerkbytes pro Sekunde|Byte|Durchschnitt|Die pro Sekunde übertragenen Netzwerkbytes|Keine Dimensionen|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|TotalPullCount|Gesamtzahl von Pullvorgängen|Count|Durchschnitt|Anzahl von Image-Pullvorgängen insgesamt|Keine Dimensionen|
|SuccessfulPullCount|Anzahl erfolgreicher Pullvorgänge|Count|Durchschnitt|Anzahl erfolgreicher Image-Pullvorgänge|Keine Dimensionen|
|TotalPushCount|Gesamtzahl von Pushvorgängen|Count|Durchschnitt|Anzahl von Image-Pushvorgängen insgesamt|Keine Dimensionen|
|SuccessfulPushCount|Anzahl erfolgreicher Puchvorgänge|Count|Durchschnitt|Anzahl erfolgreicher Image-Pushvorgänge|Keine Dimensionen|
|RunDuration|Ausführungsdauer|Millisekunden|Gesamt|Ausführungsdauer in Millisekunden|Keine Dimensionen|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Gesamtzahl der verfügbaren CPU-Kerne in einem verwalteten Cluster|Count|Gesamt|Gesamtzahl der verfügbaren CPU-Kerne in einem verwalteten Cluster|Keine Dimensionen|
|kube_node_status_allocatable_memory_bytes|Gesamtzahl des verfügbaren Speicherplatzes in einem verwalteten Cluster|Byte|Gesamt|Gesamtzahl des verfügbaren Speicherplatzes in einem verwalteten Cluster|Keine Dimensionen|
|kube_pod_status_ready|Anzahl der Pods mit dem Status „Bereit“|Count|Gesamt|Anzahl der Pods mit dem Status „Bereit“|Namespace, Pod|
|kube_node_status_condition|Status für verschiedene Knotenbedingungen|Count|Gesamt|Status für verschiedene Knotenbedingungen|Bedingung, Status, Status2, Knoten|
|kube_pod_status_phase|Anzahl der Pods nach Phase|Count|Gesamt|Anzahl der Pods nach Phase|Phase, Namespace, Pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights-API-Aufrufe|Count|Gesamt||Keine Dimensionen|
|DCIMappingImportOperationSuccessfulLines|Erfolgreiche Zeilen beim Zuordnen von Importvorgängen|Count|Gesamt||Keine Dimensionen|
|DCIMappingImportOperationFailedLines|Fehlgeschlagene Zeilen beim Zuordnen von Importvorgängen|Count|Gesamt||Keine Dimensionen|
|DCIMappingImportOperationTotalLines|Gesamte Zeilen beim Zuordnen von Importvorgängen|Count|Gesamt||Keine Dimensionen|
|DCIMappingImportOperationRuntimeInSeconds|Laufzeit in Sekunden beim Zuordnen von Importvorgängen|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundProfileExportSucceeded|Ausgehender Profilexport erfolgreich|Count|Gesamt||Keine Dimensionen|
|DCIOutboundProfileExportFailed|Fehler beim ausgehenden Profilexport|Count|Gesamt||Keine Dimensionen|
|DCIOutboundProfileExportDuration|Dauer des ausgehenden Profilexports|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundKpiExportSucceeded|Ausgehender KPI-Export erfolgreich|Count|Gesamt||Keine Dimensionen|
|DCIOutboundKpiExportFailed|Fehler beim ausgehenden KPI-Export|Count|Gesamt||Keine Dimensionen|
|DCIOutboundKpiExportDuration|Dauer des ausgehenden KPI-Exports|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundKpiExportStarted|Ausgehender KPI-Export gestartet|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundKpiRecordCount|Anzahl der ausgehenden KPI-Datensätze|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundProfileExportCount|Anzahl der ausgehenden Profilexporte|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundInitialProfileExportFailed|Fehler beim ausgehenden ursprünglichen Profilexport|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundInitialProfileExportSucceeded|Ausgehender ursprünglicher Profilexport erfolgreich|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundInitialKpiExportFailed|Fehler beim ausgehenden ursprünglichen KPI-Export|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundInitialKpiExportSucceeded|Ausgehender ursprünglicher KPI-Export erfolgreich|Sekunden|Gesamt||Keine Dimensionen|
|DCIOutboundInitialProfileExportDurationInSeconds|Dauer des ausgehenden ursprünglichen Profilexports in Sekunden|Sekunden|Gesamt||Keine Dimensionen|
|AdlaJobForStandardKpiFailed|Fehler beim ADLA-Vorgang für Standard-KPI in Sekunden|Sekunden|Gesamt||Keine Dimensionen|
|AdlaJobForStandardKpiTimeOut|Timeout bei ADLA-Vorgang für Standard-KPI in Sekunden|Sekunden|Gesamt||Keine Dimensionen|
|AdlaJobForStandardKpiCompleted|ADLA-Vorgang für Standard-KPI abgeschlossen in Sekunden|Sekunden|Gesamt||Keine Dimensionen|
|ImportASAValuesFailed|Anzahl der fehlgeschlagenen Importe von ASA-Werten|Count|Gesamt||Keine Dimensionen|
|ImportASAValuesSucceeded|Anzahl der erfolgreichen Importe von ASA-Werten|Count|Gesamt||Keine Dimensionen|
|DCIProfilesCount|Anzahl von Profilinstanzen|Count|Last (Letzter)||Keine Dimensionen|
|DCIInteractionsPerMonthCount|Anzahl von Interaktionen pro Monat|Count|Last (Letzter)||Keine Dimensionen|
|DCIKpisCount|Anzahl von KPIs|Count|Last (Letzter)||Keine Dimensionen|
|DCISegmentsCount|Anzahl von Segmenten|Count|Last (Letzter)||Keine Dimensionen|
|DCIPredictiveMatchPoliciesCount|Anzahl von vorhersagbaren Treffern|Count|Last (Letzter)||Keine Dimensionen|
|DCIPredictionsCount|Anzahl von Vorhersagen|Count|Last (Letzter)||Keine Dimensionen|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|NICReadThroughput|Lesedurchsatz (Netzwerk)|Bytes pro Sekunde|Durchschnitt|Der Lesedurchsatz der Netzwerkschnittstelle auf dem Gerät im Berichtszeitraum für alle Volumes im Gateway.|InstanceName|
|NICWriteThroughput|Schreibdurchsatz (Netzwerk)|Bytes pro Sekunde|Durchschnitt|Der Schreibdurchsatz der Netzwerkschnittstelle auf dem Gerät im Berichtszeitraum für alle Volumes im Gateway.|InstanceName|
|CloudReadThroughputPerShare|Clouddownloaddurchsatz (Freigabe)|Bytes pro Sekunde|Durchschnitt|Der Downloaddurchsatz in Azure von einer Freigabe während des Berichtszeitraums.|Freigabe|
|CloudUploadThroughputPerShare|Clouduploaddurchsatz (Freigabe)|Bytes pro Sekunde|Durchschnitt|Der Uploaddurchsatz in Azure von einer Freigabe während des Berichtszeitraums.|Freigabe|
|BytesUploadedToCloudPerShare|Hochgeladene Cloudbytes (Freigabe)|Byte|Durchschnitt|Die Gesamtzahl von Bytes, die in Azure von einer Freigabe während des Berichtszeitraum hochgeladen wird.|Freigabe|
|TotalCapacity|Gesamtkapazität|Byte|Durchschnitt|Gesamtkapazität|Keine Dimensionen|
|AvailableCapacity|Verfügbare Kapazität|Byte|Durchschnitt|Die verfügbare Kapazität in Byte während des Berichtszeitraums.|Keine Dimensionen|
|CloudUploadThroughput|Clouduploaddurchsatz|Bytes pro Sekunde|Durchschnitt|Der Clouduploaddurchsatz in Azure während des Berichtszeitraums.|Keine Dimensionen|
|CloudReadThroughput|Clouddownloaddurchsatz|Bytes pro Sekunde|Durchschnitt|Der Clouddownloaddurchsatz in Azure während des Berichtszeitraums.|Keine Dimensionen|
|BytesUploadedToCloud|Hochgeladene Cloudbytes (Gerät)|Byte|Durchschnitt|Die Gesamtzahl von Bytes, die in Azure von einem Gerät während des Berichtszeitraum hochgeladen wird.|Keine Dimensionen|
|HyperVVirtualProcessorUtilization|Edgecomputing – CPU in Prozent|Prozent|Durchschnitt|Prozentsatz der CPU-Auslastung|InstanceName|
|HyperVMemoryUtilization|Edgecomputing – Arbeitsspeichernutzung|Prozent|Durchschnitt|Größe des genutzten Arbeitsspeichers|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|FailedRuns|Fehlerhafte Ausführungen|Count|Gesamt||pipelineName, activityName|
|SuccessfulRuns|Erfolgreiche Ausführungen|Count|Gesamt||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metriken zu fehlerhaften Pipelineausführungen|Count|Gesamt||FailureType, Name|
|PipelineSucceededRuns|Metriken zu erfolgreichen Pipelineausführungen|Count|Gesamt||FailureType, Name|
|ActivityFailedRuns|Metriken zu fehlerhaften Aktivitätsausführungen|Count|Gesamt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Metriken zu erfolgreichen Aktivitätsausführungen|Count|Gesamt||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Metriken zu fehlerhaften Triggerausführungen|Count|Gesamt||Name, FailureType|
|TriggerSucceededRuns|Metriken zu erfolgreichen Triggerausführungen|Count|Gesamt||Name, FailureType|
|IntegrationRuntimeCpuPercentage|CPU-Auslastung der Integration Runtime|Prozent|Durchschnitt||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Verfügbarer Speicher für Integration Runtime|Byte|Durchschnitt||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Anzahl maximal zulässiger Entitäten|Count|Maximum||Keine Dimensionen|
|MaxAllowedFactorySizeInGbUnits|Maximal zulässige Factorygröße (Einheit: GB)|Count|Maximum||Keine Dimensionen|
|ResourceCount|Gesamtzahl von Entitäten|Count|Maximum||Keine Dimensionen|
|FactorySizeInGbUnits|Gesamtgröße der Factory (Einheit: GB)|Count|Maximum||Keine Dimensionen|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|JobEndedSuccess|Erfolgreiche Vorgänge|Count|Gesamt|Die Anzahl der erfolgreichen Vorgänge|Keine Dimensionen|
|JobEndedFailure|Fehlgeschlagene Vorgänge|Count|Gesamt|Die Anzahl der fehlgeschlagenen Vorgänge|Keine Dimensionen|
|JobEndedCancelled|Abgebrochene Aufträge|Count|Gesamt|Die Anzahl der abgebrochenen Aufträge.|Keine Dimensionen|
|JobAUEndedSuccess|Erfolgreiche AU-Zeit|Sekunden|Gesamt|Die Gesamt-AU-Zeit für erfolgreiche Vorgänge|Keine Dimensionen|
|JobAUEndedFailure|Fehlgeschlagene AU-Zeit|Sekunden|Gesamt|Die Gesamt-AU-Zeit für fehlgeschlagene Vorgänge|Keine Dimensionen|
|JobAUEndedCancelled|Abgebrochene AU-Zeit|Sekunden|Gesamt|AU-Gesamtzeit für abgebrochene Aufträge.|Keine Dimensionen|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|TotalStorage|Gesamtspeicher|Byte|Maximum|Gesamtmenge der im Konto gespeicherten Daten|Keine Dimensionen|
|DataWritten|Geschriebene Daten|Byte|Gesamt|Gesamtmenge der in das Konto geschriebenen Daten|Keine Dimensionen|
|DataRead|Gelesene Daten|Byte|Gesamt|Gesamtmenge der im Konto gelesenen Daten|Keine Dimensionen|
|WriteRequests|Schreibanforderungen|Count|Gesamt|Anzahl der Datenschreibanforderungen für das Konto|Keine Dimensionen|
|ReadRequests|Leseanforderungen|Count|Gesamt|Anzahl der Datenleseanforderungen für das Konto|Keine Dimensionen|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|cpu_percent|CPU in Prozent|Prozent|Durchschnitt|CPU in Prozent|Keine Dimensionen|
|memory_percent|Arbeitsspeicher in Prozent|Prozent|Durchschnitt|Arbeitsspeicher in Prozent|Keine Dimensionen|
|io_consumption_percent|E/A in Prozent|Prozent|Durchschnitt|E/A in Prozent|Keine Dimensionen|
|storage_percent|Speicher in Prozent|Prozent|Durchschnitt|Speicher in Prozent|Keine Dimensionen|
|storage_used|Verwendeter Speicher|Byte|Durchschnitt|Verwendeter Speicher|Keine Dimensionen|
|storage_limit|Speicherbegrenzung|Byte|Durchschnitt|Speicherbegrenzung|Keine Dimensionen|
|serverlog_storage_percent|Serverprotokollspeicher in Prozent|Prozent|Durchschnitt|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Verwendeter Serverprotokollspeicher|Byte|Durchschnitt|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|serverlog_storage_limit|Begrenzung des Serverprotokollspeichers|Byte|Durchschnitt|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|active_connections|Die aktiven Verbindungen.|Count|Durchschnitt|Die aktiven Verbindungen.|Keine Dimensionen|
|connections_failed|Verbindungsfehler|Count|Gesamt|Verbindungsfehler|Keine Dimensionen|
|seconds_behind_master|Replikationsverzögerung in Sekunden|Count|Durchschnitt|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|backup_storage_used|Verwendeter Sicherungsspeicher|Byte|Durchschnitt|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|network_bytes_egress|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|cpu_percent|CPU in Prozent|Prozent|Durchschnitt|CPU in Prozent|Keine Dimensionen|
|memory_percent|Arbeitsspeicher in Prozent|Prozent|Durchschnitt|Arbeitsspeicher in Prozent|Keine Dimensionen|
|io_consumption_percent|E/A in Prozent|Prozent|Durchschnitt|E/A in Prozent|Keine Dimensionen|
|storage_percent|Speicher in Prozent|Prozent|Durchschnitt|Speicher in Prozent|Keine Dimensionen|
|storage_used|Verwendeter Speicher|Byte|Durchschnitt|Verwendeter Speicher|Keine Dimensionen|
|storage_limit|Speicherbegrenzung|Byte|Durchschnitt|Speicherbegrenzung|Keine Dimensionen|
|serverlog_storage_percent|Serverprotokollspeicher in Prozent|Prozent|Durchschnitt|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Verwendeter Serverprotokollspeicher|Byte|Durchschnitt|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|serverlog_storage_limit|Begrenzung des Serverprotokollspeichers|Byte|Durchschnitt|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|active_connections|Die aktiven Verbindungen.|Count|Durchschnitt|Die aktiven Verbindungen.|Keine Dimensionen|
|connections_failed|Verbindungsfehler|Count|Gesamt|Verbindungsfehler|Keine Dimensionen|
|seconds_behind_master|Replikationsverzögerung in Sekunden|Count|Durchschnitt|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|backup_storage_used|Verwendeter Sicherungsspeicher|Byte|Durchschnitt|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|network_bytes_egress|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|cpu_percent|CPU in Prozent|Prozent|Durchschnitt|CPU in Prozent|Keine Dimensionen|
|memory_percent|Arbeitsspeicher in Prozent|Prozent|Durchschnitt|Arbeitsspeicher in Prozent|Keine Dimensionen|
|io_consumption_percent|E/A in Prozent|Prozent|Durchschnitt|E/A in Prozent|Keine Dimensionen|
|storage_percent|Speicher in Prozent|Prozent|Durchschnitt|Speicher in Prozent|Keine Dimensionen|
|storage_used|Verwendeter Speicher|Byte|Durchschnitt|Verwendeter Speicher|Keine Dimensionen|
|storage_limit|Speicherbegrenzung|Byte|Durchschnitt|Speicherbegrenzung|Keine Dimensionen|
|serverlog_storage_percent|Serverprotokollspeicher in Prozent|Prozent|Durchschnitt|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Verwendeter Serverprotokollspeicher|Byte|Durchschnitt|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|serverlog_storage_limit|Begrenzung des Serverprotokollspeichers|Byte|Durchschnitt|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|active_connections|Die aktiven Verbindungen.|Count|Durchschnitt|Die aktiven Verbindungen.|Keine Dimensionen|
|connections_failed|Verbindungsfehler|Count|Gesamt|Verbindungsfehler|Keine Dimensionen|
|backup_storage_used|Verwendeter Sicherungsspeicher|Byte|Durchschnitt|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|network_bytes_egress|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|pg_replica_log_delay_in_seconds|Replikatverzögerung|Sekunden|Maximum|Replikatverzögerung in Sekunden|Keine Dimensionen|
|pg_replica_log_delay_in_bytes|Maximale Verzögerung zwischen Replikaten|Byte|Maximum|Verzögerung in Byte beim Replikat mit der größten Verzögerung|Keine Dimensionen|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|cpu_percent|CPU in Prozent|Prozent|Durchschnitt|CPU in Prozent|Keine Dimensionen|
|memory_percent|Arbeitsspeicher in Prozent|Prozent|Durchschnitt|Arbeitsspeicher in Prozent|Keine Dimensionen|
|iops|IOPS|Count|Durchschnitt|E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|storage_percent|Speicher in Prozent|Prozent|Durchschnitt|Speicher in Prozent|Keine Dimensionen|
|storage_used|Verwendeter Speicher|Byte|Durchschnitt|Verwendeter Speicher|Keine Dimensionen|
|active_connections|Die aktiven Verbindungen.|Count|Durchschnitt|Die aktiven Verbindungen.|Keine Dimensionen|
|network_bytes_egress|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)|Count|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die an Ihren IoT Hub gesendet werden sollten|Keine Dimensionen|
|d2c.telemetry.ingress.success|Telemetry messages sent (Gesendete Telemetrienachrichten)|Count|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die erfolgreich an Ihren IoT Hub gesendet wurden|Keine Dimensionen|
|c2d.commands.egress.complete.success|Commands completed (Abgeschlossene Befehle)|Count|Gesamt|Anzahl von Befehlen von der Cloud an das Gerät, die vom Gerät erfolgreich abgeschlossen wurden|Keine Dimensionen|
|c2d.commands.egress.abandon.success|Commands abandoned (Abgebrochene Befehle)|Count|Gesamt|Anzahl von Befehlen von der Cloud an das Gerät, die vom Gerät abgebrochen wurden|Keine Dimensionen|
|c2d.commands.egress.reject.success|Commands rejected (Abgelehnte Befehle)|Count|Gesamt|Anzahl von Befehlen von der Cloud an das Gerät, die vom Gerät abgelehnt wurden|Keine Dimensionen|
|devices.totalDevices|Geräte gesamt (veraltet)|Count|Gesamt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|devices.connectedDevices.allProtocol|Verbundene Geräte (veraltet) |Count|Gesamt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|d2c.telemetry.egress.success|Routing: Übermittelte Telemetrienachrichten|Count|Gesamt|Die Anzahl der erfolgreichen Nachrichtenübermittlungen an alle Endpunkte über das IoT Hub-Routing Wenn eine Nachricht an mehrere Endpunkte weitergeleitet wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins Wenn eine Nachricht mehrmals an denselben Endpunkt übermittelt wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins|Keine Dimensionen|
|d2c.telemetry.egress.dropped|Routing: Verworfene Telemetrienachrichten |Count|Gesamt|Die Anzahl der Nachrichten, die vom IoT Hub-Routing aufgrund von inaktiven Endpunkten gelöscht wurden. Dieser Wert zählt nicht die Nachrichten, die an die Fallbackroute übermittelt werden, da gelöschte Nachrichten dort nicht übermittelt werden.|Keine Dimensionen|
|d2c.telemetry.egress.orphaned|Routing: Verwaiste Telemetrienachrichten |Count|Gesamt|Die Häufigkeit, mit der Nachrichten durch das IoT Hub-Routing verwaist wurden, da sie mit keinen Routingregeln (einschließlich der Fallbackregel) übereinstimmten. |Keine Dimensionen|
|d2c.telemetry.egress.invalid|Routing: Nicht kompatible Telemetrienachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten aufgrund einer Inkompatibilität mit dem Endpunkt nicht übermitteln konnte. Dieser Wert umfasst keine Wiederholungen.|Keine Dimensionen|
|d2c.telemetry.egress.fallback|Routing: An den Fallback übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten an den mit der Fallbackroute verbundenen Endpunkt übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.eventHubs|Routing: An Event Hub übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Event Hub-Endpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.latency.eventHubs|Routing: Nachrichtenwartezeit für Event Hub|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Event Hub-Endpunkt.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusQueues|Routing: An Service Bus-Warteschlange übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Warteschlangenendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusQueues|Routing: Nachrichtenwartezeit für Service Bus-Warteschlange|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Warteschlangenendpunkt.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusTopics|Routing: An Service Bus-Thema übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Themaendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusTopics|Routing: Nachrichtenwartezeit für Service Bus-Thema|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Themaendpunkt.|Keine Dimensionen|
|d2c.endpoints.egress.builtIn.events|Routing: An Nachrichten/Ereignisse übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an den integrierter Endpunkt (Nachrichten/Ereignisse) übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.latency.builtIn.events|Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Telemetrienachricht beim integrierten Endpunkt (Nachrichten/Ereignisse).|Keine Dimensionen|
|d2c.endpoints.egress.storage|Routing: An den Speicher übermittelte Nachrichten|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.latency.storage|Routing: Nachrichtenwartezeit für Speicher|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Speicherendpunkt.|Keine Dimensionen|
|d2c.endpoints.egress.storage.bytes|Routing: An den Speicher übermittelte Daten|Byte|Gesamt|Die Datenmenge (Bytes), die das IoT Hub-Routing an die Speicherendpunkte übermittelt.|Keine Dimensionen|
|d2c.endpoints.egress.storage.blobs|Routing: An den Speicher übermittelte Blobs|Count|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Blobs an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|EventGridDeliveries|Event Grid-Übermittlungen (Vorschau)|Count|Gesamt|Die Anzahl von Anforderungen für IoT Hub zum Ausgeben von Ereignissen an Event Grid. Diese Anzahl umfasst erfolgreiche und fehlerhafte Anforderungen. Verwenden Sie die Dimension „Result“ für die Anzahl von verschiedenen Antworttypen. Verwenden Sie die Dimension „EventType“, um festzustellen, woher die Anforderungen stammen.|Result, EventType|
|EventGridLatency|Event Grid-Latenz (Vorschau)|Millisekunden|Durchschnitt|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang des Ereignisses beim IoT Hub und dem Eingang des Ereignisses in Event Grid. Diese Zahl ist ein Durchschnittswert für alle Ereignistypen. Verwenden Sie die Dimension „EventType“, um die Wartezeit für einen bestimmten Ereignistyp anzuzeigen.|EventType|
|d2c.twin.read.success|Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.failure|Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.size|Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.success|Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.failure|Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.size|Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Gerät initiiert wurden.|Keine Dimensionen|
|c2d.methods.success|Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)|Count|Gesamt|Gibt an, wie viele direkte Methodenaufrufe erfolgreich durchgeführt wurden.|Keine Dimensionen|
|c2d.methods.failure|Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)|Count|Gesamt|Gibt an, wie viele direkte Methodenaufrufe nicht erfolgreich waren.|Keine Dimensionen|
|c2d.methods.requestSize|Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)|Byte|Durchschnitt|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen direkten Methodenaufrufe an.|Keine Dimensionen|
|c2d.methods.responseSize|Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)|Byte|Durchschnitt|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Antworten für die direkte Methode an.|Keine Dimensionen|
|c2d.twin.read.success|Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.failure|Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.size|Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.success|Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.failure|Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.size|Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Back-End initiiert wurden.|Keine Dimensionen|
|twinQueries.success|Successful twin queries (Erfolgreiche Zwillingsabfragen)|Count|Gesamt|Gibt an, wie viele erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries.failure|Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries.resultSize|Twin queries result size (Ergebnisgröße von Zwillingsabfragen)|Byte|Durchschnitt|Durchschnitts-, Minimal- und Maximalwert der Ergebnisgröße aller erfolgreichen Zwillingsabfragen.|Keine Dimensionen|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Count|Gesamt|Gibt die Anzahl von allen nicht erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Count|Gesamt|Gibt die Anzahl von erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Count|Gesamt|Gibt die Anzahl von nicht erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.listJobs.success|Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)|Count|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.listJobs.failure|Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.cancelJob.success|Successful job cancellations (Erfolgreiche Auftragsabbrüche)|Count|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.cancelJob.failure|Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.success|Successful job queries (Erfolgreiche Auftragsabfragen)|Count|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.failure|Failed job queries (Nicht erfolgreiche Auftragsabfragen)|Count|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.completed|Abgeschlossene Aufträge|Count|Gesamt|Gibt die Anzahl von abgeschlossenen Aufträgen an.|Keine Dimensionen|
|jobs.failed|Fehlerhafte Aufträge|Count|Gesamt|Gibt die Anzahl aller fehlerhaften Aufträge an.|Keine Dimensionen|
|d2c.telemetry.ingress.sendThrottle|Anzahl von Drosselungsfehlern|Count|Gesamt|Anzahl von Drosselungsfehlern aufgrund von Drosselungen des Gerätedurchsatzes|Keine Dimensionen|
|dailyMessageQuotaUsed|Gesamtzahl verwendeter Nachrichten|Count|Durchschnitt|Gesamtzahl der heute verwendeten Nachrichten. Dies ist ein kumulativer Wert, der jeden Tag um 00:00 Uhr UTC auf 0 zurückgesetzt wird.|Keine Dimensionen|
|deviceDataUsage|Gerätedatennutzung gesamt|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|totalDeviceCount|Geräte gesamt (Vorschau)|Count|Durchschnitt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|connectedDeviceCount|Verbundene Geräte (Vorschau)|Count|Durchschnitt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|Konfigurationen|Konfigurationsmetriken|Count|Gesamt|Metriken für Konfigurationsvorgänge|Keine Dimensionen|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|RegistrationAttempts|Registrierungsversuche|Count|Gesamt|Anzahl von versuchten Geräteregistrierungen|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Zugewiesene Geräte|Count|Gesamt|Anzahl von Geräten, die einem IoT-Hub zugewiesen sind|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Nachweisversuche|Count|Gesamt|Anzahl von versuchten Gerätenachweisen|ProvisioningServiceName, Status, Protokoll|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|AvailableStorage|Verfügbarer Speicher|Byte|Gesamt|Gemeldeter Gesamtspeicher mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Abschluss von Cassandra-Verbindungen|Count|Gesamt|Anzahl von Cassandra-Verbindungen, die geschlossen wurden, gemeldet mit einer Granularität von einer Minute|Region, ClosureReason|
|CassandraRequestCharges|Gebühren für Cassandra-Anforderungen|Count|Gesamt|Genutzte Anforderungseinheiten für gesendete Cassandra-Anforderungen|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra-Anforderungen|Count|Count|Anzahl ausgeführter Cassandra-Anforderungen|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Datennutzung|Byte|Gesamt|Gemeldete Gesamtdatennutzung mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|DocumentCount|Dokumentanzahl|Count|Gesamt|Gemeldete Gesamtdokumentanzahl mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|DocumentQuota|Dokumentenkontingent|Byte|Gesamt|Gemeldetes Gesamtspeicherkontingent mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|IndexUsage|Indexnutzung|Byte|Gesamt|Gemeldete Gesamtindexnutzung mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|MetadataRequests|Anforderungen von Metadaten|Count|Count|Anzahl der Metadatenanforderungen. Cosmos DB unterhält eine Sammlung von Systemmetadaten für jedes Konto, wodurch Sie Sammlungen, Datenbanken usw. und deren Konfigurationen ohne anfallende Kosten auflisten können.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Kosten der Mongo-Anforderung|Count|Gesamt|Verbrauchte Mongo-Anforderungseinheiten|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo-Anforderungen|Count|Count|Anzahl der ausgegebenen Mongo-Anforderungen|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Bereitgestellter Durchsatz|Count|Maximum|Bereitgestellter Durchsatz|DatabaseName, CollectionName|
|ReplicationLatency|P99-Replikationswartezeit|Millisekunden|Durchschnitt|P99-Replikationswartezeit für Quell- und Zielregionen für geofähiges Konto|SourceRegion, TargetRegion|
|ServiceAvailability|Dienstverfügbarkeit|Prozent|Durchschnitt|Verfügbarkeit der Kontoanforderungen mit einer Granularität von einer Stunde, einem Tag oder einem Monat|Keine Dimensionen|
|TotalRequestUnits|Anforderungseinheiten gesamt|Count|Gesamt|Verbrauchte Anforderungseinheiten|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Anzahl von Anforderungen|Count|Count|Anzahl von gesendeten Anforderungen|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|PublishSuccessCount|Veröffentlichte Ereignisse|Count|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishFailCount|Ereignisse mit Veröffentlichungsfehler|Count|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|UnmatchedEventCount|Ereignisse ohne Übereinstimmung|Count|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|
|PublishSuccessLatencyInMs|Latenz erfolgreicher Veröffentlichungen|Count|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|MatchedEventCount|Übereinstimmende Ereignisse|Count|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|Keine Dimensionen|
|DeliveryAttemptFailCount|Ereignisse mit Übermittlungsfehler|Count|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType|
|DeliverySuccessCount|Übermittelte Ereignisse|Count|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|Keine Dimensionen|
|DestinationProcessingDurationInMs|Zielverarbeitungsdauer|Millisekunden|Durchschnitt|Zielverarbeitungsdauer in Millisekunden|Keine Dimensionen|
|DroppedEventCount|Gelöschte Ereignisse|Count|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason|
|DeadLetteredCount|Unzustellbare Ereignisse|Count|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|PublishSuccessCount|Veröffentlichte Ereignisse|Count|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishFailCount|Fehlerhafte Ereignisse|Count|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|UnmatchedEventCount|Ereignisse ohne Übereinstimmung|Count|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|
|PublishSuccessLatencyInMs|Latenz erfolgreicher Veröffentlichungen|Count|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|SuccessfulRequests|Erfolgreiche Anforderungen|Count|Gesamt|Erfolgreiche Anforderungen für Microsoft.EventHub.|EntityName, |
|ServerErrors|Serverfehler.|Count|Gesamt|Serverfehler für Microsoft.EventHub.|EntityName, |
|UserErrors|Benutzerfehler.|Count|Gesamt|Benutzerfehler für Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Fehler aufgrund von Kontingentüberschreitung.|Count|Gesamt|Fehler aufgrund von Kontingentüberschreitung für Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Gedrosselte Anforderungen.|Count|Gesamt|Gedrosselte Anforderungen für Microsoft.EventHub.|EntityName, |
|IncomingRequests|Eingehende Anforderungen|Count|Gesamt|Eingehende Anforderungen für Microsoft.EventHub.|EntityName|
|IncomingMessages|Eingehende Nachrichten|Count|Gesamt|Eingehende Nachrichten für Microsoft.EventHub.|EntityName|
|OutgoingMessages|Ausgehende Nachrichten|Count|Gesamt|Ausgehende Nachrichten für Microsoft.EventHub.|EntityName|
|IncomingBytes|Eingehende Bytes.|Byte|Gesamt|Eingehende Bytes für Microsoft.EventHub.|EntityName|
|OutgoingBytes|Ausgehende Bytes.|Byte|Gesamt|Ausgehende Bytes für Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Durchschnitt|Aktive Verbindungen gesamt für Microsoft.EventHub.|Keine Dimensionen|
|ConnectionsOpened|Geöffnete Verbindungen.|Count|Durchschnitt|Geöffnete Verbindungen für Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Geschlossene Verbindungen.|Count|Durchschnitt|Geschlossene Verbindungen für Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog erfassen.|Count|Gesamt|Erfassen des Backlogs für Microsoft.EventHub.|EntityName|
|CapturedMessages|Erfasste Nachrichten.|Count|Gesamt|Erfasste Nachrichten für Microsoft.EventHub.|EntityName|
|CapturedBytes|Erfasste Bytes.|Byte|Gesamt|Erfasste Bytes für Microsoft.EventHub.|EntityName|
|Größe|Größe|Byte|Durchschnitt|Größe eines EventHub in Bytes.|EntityName|
|INREQS|Eingehende Anforderungen (veraltet)|Count|Gesamt|Gesamtzahl eingehender Sendeanforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|SUCCREQ|Erfolgreiche Anforderungen (veraltet)|Count|Gesamt|Gesamtzahl erfolgreicher Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|FAILREQ|Fehlerhafte Anforderungen (veraltet)|Count|Gesamt|Gesamtzahl fehlerhafter Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|SVRBSY|Fehler durch ausgelasteten Server (veraltet)|Count|Gesamt|Gesamtzahl der Fehler durch ausgelasteten Server für einen Namespace (veraltet)|Keine Dimensionen|
|INTERR|Interne Serverfehler (veraltet)|Count|Gesamt|Gesamtzahl interner Serverfehler für einen Namespace (veraltet)|Keine Dimensionen|
|MISCERR|Andere Fehler (veraltet)|Count|Gesamt|Gesamtzahl fehlerhafter Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|INMSGS|Eingehende Nachrichten (veraltet)|Count|Gesamt|Gesamtzahl von eingehenden Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für eingehende Nachrichten. (veraltet)|Keine Dimensionen|
|EHINMSGS|Eingehende Nachrichten (veraltet)|Count|Gesamt|Gesamtzahl von eingehenden Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|OUTMSGS|Ausgehende Nachrichten (veraltet)|Count|Gesamt|Gesamtzahl von ausgehenden Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für ausgehende Nachrichten. (veraltet)|Keine Dimensionen|
|EHOUTMSGS|Ausgehende Nachrichten (veraltet)|Count|Gesamt|Gesamtzahl von ausgehenden Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHINMBS|Eingehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz eingehender Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für eingehende Bytes. (veraltet)|Keine Dimensionen|
|EHINBYTES|Eingehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz eingehender Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHOUTMBS|Ausgehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz ausgehender Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für ausgehende Bytes. (veraltet)|Keine Dimensionen|
|EHOUTBYTES|Ausgehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz ausgehender Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHABL|Archivierte Backlognachrichten (veraltet)|Count|Gesamt|Event Hub-Archivnachrichten im Backlog für einen Namespace (veraltet)|Keine Dimensionen|
|EHAMSGS|Archivnachrichten (veraltet)|Count|Gesamt|Event Hub-Archivnachrichten in einem Namespace (veraltet)|Keine Dimensionen|
|EHAMBS|Durchsatz von Archivnachrichten (veraltet)|Byte|Gesamt|Event Hub-Durchsatz archivierter Nachrichten in einem Namespace (veraltet)|Keine Dimensionen|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|SuccessfulRequests|Erfolgreiche Anforderungen (Vorschau)|Count|Gesamt|Erfolgreiche Anforderungen für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|ServerErrors|Serverfehler. (Vorschau)|Count|Gesamt|Serverfehler für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|UserErrors|Benutzerfehler. (Vorschau)|Count|Gesamt|Benutzerfehler für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|QuotaExceededErrors|Fehler aufgrund von Kontingentüberschreitung. (Vorschau)|Count|Gesamt|Fehler aufgrund von Kontingentüberschreitung für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|ThrottledRequests|Gedrosselte Anforderungen. (Vorschau)|Count|Gesamt|Gedrosselte Anforderungen für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|IncomingRequests|Eingehende Anforderungen (Vorschau)|Count|Gesamt|Eingehende Anforderungen für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|IncomingMessages|Eingehende Nachrichten (Vorschau)|Count|Gesamt|Eingehende Nachrichten für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|OutgoingMessages|Ausgehende Nachrichten (Vorschau)|Count|Gesamt|Ausgehende Nachrichten für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|IncomingBytes|Eingehende Bytes. (Vorschau)|Byte|Gesamt|Eingehende Bytes für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|OutgoingBytes|Ausgehende Bytes. (Vorschau)|Byte|Gesamt|Ausgehende Bytes für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|ActiveConnections|Aktive Verbindungen (Vorschau)|Count|Durchschnitt|Aktive Verbindungen gesamt für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|ConnectionsOpened|Geöffnete Verbindungen. (Vorschau)|Count|Durchschnitt|Geöffnete Verbindungen für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|ConnectionsClosed|Geschlossene Verbindungen. (Vorschau)|Count|Durchschnitt|Geschlossene Verbindungen für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|CaptureBacklog|Backlog erfassen. (Vorschau)|Count|Gesamt|Erfassen des Backlogs für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|CapturedMessages|Erfasste Nachrichten. (Vorschau)|Count|Gesamt|Erfasste Nachrichten für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|CapturedBytes|Erfasste Bytes. (Vorschau)|Byte|Gesamt|Erfasste Bytes für Microsoft.EventHub. (Vorschau)|Keine Dimensionen|
|CPU|CPU (Vorschau)|Prozent|Maximum|CPU-Auslastung für den Event Hub-Cluster in Prozent|Rolle|
|AvailableMemory|Verfügbarer Arbeitsspeicher (Vorschau)|Count|Maximum|Verfügbarer Arbeitsspeicher für den Event Hub-Cluster in Byte|Rolle|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|GatewayRequests|Gatewayanforderungen|Count|Gesamt|Anzahl von Gatewayanforderungen|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Kategorisierte Gatewayanforderungen|Count|Gesamt|Anzahl von Gatewayanforderungen nach Kategorien (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Anzahl aktiver Worker|Count|Maximum|Anzahl aktiver Worker|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ObservedMetricValue|Beobachteter Metrikwert|Count|Durchschnitt|Der Wert, der beim Ausführen der automatischen Skalierung berechnet wurde.|MetricTriggerSource|
|MetricThreshold|Schwellenwert der Metrik|Count|Durchschnitt|Der bei der Ausführung der automatischen Skalierung konfigurierte Schwellenwert für die automatische Skalierung.|MetricTriggerRule|
|ObservedCapacity|Beobachtete Kapazität|Count|Durchschnitt|Die Kapazität, die beim Ausführen der automatischen Skalierung gemeldet wurde.|Keine Dimensionen|
|ScaleActionsInitiated|Initiierte Skalierungsaktionen|Count|Gesamt|Die Richtung des Skalierungsvorgangs.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Öffentliche Vorschau)

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Verfügbarkeit|Prozent|Durchschnitt|Prozentsatz erfolgreich abgeschlossener Verfügbarkeitstests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Verfügbarkeitstests|Count|Count|Anzahl von Verfügbarkeitstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Dauer von Verfügbarkeitstests|Millisekunden|Durchschnitt|Dauer von Verfügbarkeitstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Netzwerkverbindungszeit zum Laden der Seite|Millisekunden|Durchschnitt|Zeit zwischen Benutzeranforderung und Netzwerkverbindung. Umfasst DNS-Suche und Transportverbindung.|Keine Dimensionen|
|browserTimings/processingDuration|Clientverarbeitungszeit|Millisekunden|Durchschnitt|Zeit zwischen dem Empfang des letzten Byte eines Dokuments und dem Laden des DOM. Asynchrone Anforderungen werden möglicherweise immer noch verarbeitet.|Keine Dimensionen|
|browserTimings/receiveDuration|Empfängt Antwortzeit|Millisekunden|Durchschnitt|Zeit zwischen dem ersten und letzten Byte, oder bis zur Verbindungstrennung.|Keine Dimensionen|
|browserTimings/sendDuration|Anforderungszeit senden|Millisekunden|Durchschnitt|Zeit zwischen der Netzwerkverbindung und dem Empfang des ersten Byte.|Keine Dimensionen|
|browserTimings/totalDuration|Browser-Seitenladezeit|Millisekunden|Durchschnitt|Zeit ab der Benutzeranforderung, bis DOM, Stylesheets, Skripts und Bilder geladen werden.|Keine Dimensionen|
|dependencies/count|Abhängigkeitsaufrufe|Count|Count|Die Anzahl der von der Anwendung an externe Ressourcen ausgeführten Aufrufe.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dauer der Abhängigkeit|Millisekunden|Durchschnitt|Die Dauer der von der Anwendung an externe Ressourcen ausgeführten Aufrufe.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Fehler bei Abhängigkeitsaufrufen|Count|Count|Anzahl fehlerhafter Abhängigkeitsaufrufe von der Anwendung an externe Ressourcen.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Seitenaufrufe|Count|Count|Anzahl der Seitenaufrufe|operation/synthetic|
|pageViews/duration|Ladezeit der Seitenansicht|Millisekunden|Durchschnitt|Ladezeit der Seitenansicht|operation/synthetic|
|performanceCounters/requestExecutionTime|Ausführungszeit der HTTP-Anforderung|Millisekunden|Durchschnitt|Ausführungszeit der aktuellen Anforderung.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP-Anforderungen in der Anwendungswarteschlange|Count|Durchschnitt|Länge der Anwendungsanforderungswarteschleife.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP-Anforderungsrate|Anzahl pro Sekunde|Durchschnitt|Rate aller Anforderungen an die Anwendung pro Sekunde von ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Ausnahmerate|Anzahl pro Sekunde|Durchschnitt|Die Anzahl der behandelten Ausnahmen und Ausnahmefehler, die an Windows gemeldet werden, einschließlich .NET-Ausnahmen und nicht verwalteten Ausnahmen, die in .NET-Ausnahmen konvertiert werden.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|E/A-Rate für Prozess|Bytes pro Sekunde|Durchschnitt|Gesamtanzahl von pro Sekunde in Dateien, im Netzwerk und auf Geräten gelesenen und geschriebenen Bytes.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Prozess-CPU|Prozent|Durchschnitt|Der Prozentsatz der verstrichenen Zeit für alle Prozessthreads, die den Prozessor zur Ausführung von Anweisungen verwendet haben. Dies kann zwischen 0 und 100 variieren. Diese Metrik gibt ausschließlich die Leistung des w3wp-Prozesses an.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Prozessorzeit|Prozent|Durchschnitt|Der Prozentsatz der Zeit, die der Prozessor nicht im Leerlauf in Threads verbringt.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Verfügbarer Arbeitsspeicher|Byte|Durchschnitt|Physischer Speicher ist sofort für die Zuordnung zu einem Prozess oder für die Systemnutzung verfügbar.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Private Bytes für Prozess|Byte|Durchschnitt|Speicher, der exklusiv den überwachten Anwendungsprozessen zugewiesen ist.|cloud/roleInstance|
|requests/duration|Serverantwortzeit|Millisekunden|Durchschnitt|Zeit zwischen dem Empfang einer HTTP-Anforderung und dem Abschluss des Sendevorgangs der Antwort.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Serveranforderungen|Count|Count|Anzahl der abgeschlossenen HTTP-Anforderungen.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Anforderungsfehler|Count|Count|Anzahl der als fehlerhaft markierten HTTP-Anforderungen. In den meisten Fällen sind dies Anforderungen mit dem Antwortcode >= 400 und ungleich 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Serveranforderungsrate|Anzahl pro Sekunde|Durchschnitt|Rate der Serveranforderungen pro Sekunde|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Ausnahmen|Count|Count|Die kombinierte Anzahl nicht abgefangener Ausnahmen.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Browserausnahmen|Count|Count|Anzahl nicht erfasster Ausnahmen, die im Browser ausgelöst wurden.|Keine Dimensionen|
|exceptions/server|Serverausnahmen|Count|Count|Anzahl nicht erfasster Ausnahmen, die in der Serveranwendung ausgelöst wurden.|cloud/roleName, cloud/roleInstance|
|traces/count|Ablaufverfolgungen|Count|Count|Dokumentanzahl nachverfolgen|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ServiceApiHit|Dienst-API-Treffer, gesamt|Count|Count|Gesamtanzahl der Dienst-API-Treffer|ActivityType, ActivityName|
|ServiceApiLatency|Gesamtwartezeit für Dienst-API|Millisekunden|Durchschnitt|Gesamtwartezeit für Dienst-API-Anforderungen|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Dienst-API-Ergebnisse, gesamt|Count|Count|Gesamtanzahl der Dienst-API-Ergebnisse|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Cacheauslastung|Prozent|Durchschnitt|Auslastungsgrad im Clusterbereich|Keine Dimensionen|
|QueryDuration|Abfragedauer|Millisekunden|Durchschnitt|Abfragedauer in Sekunden|QueryStatus|
|IngestionsLoadFactor|Datenerfassungsauslastung|Prozent|Durchschnitt|Verhältnis der verwendeten Datenerfassungsslots im Cluster|Keine Dimensionen|
|IsEngineAnsweringQuery|Keep-Alive|Count|Durchschnitt|Die Integritätsprüfung zeigt an, dass der Cluster auf Abfragen reagiert.|Keine Dimensionen|
|IngestCommandOriginalSizeInMb|Datenerfassungsvolumen (in MB)|Count|Gesamt|Gesamtvolumen der im Cluster erfassten Daten (in MB)|Keine Dimensionen|
|IngestedEventAgeSeconds|Erfassungswartezeit (in Sekunden)|Sekunden|Durchschnitt|Erfassungszeit von der Quelle (z. B. Nachricht in EventHub) zum Cluster in Sekunden|Keine Dimensionen|
|EventRecievedFromEventHub|Verarbeitete Ereignisse (für Event Hubs)|Count|Gesamt|Anzahl der vom Cluster verarbeiteten Ereignisse beim Erfassen aus Event Hub|Keine Dimensionen|
|IngestionResult|Ergebnis der Datenerfassung|Count|Count|Anzahl der Erfassungsvorgänge|IngestionResultDetails|
|EngineCPU|CPU|Prozent|Durchschnitt|CPU-Auslastungsgrad|Keine Dimensionen|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Verwendung|Verwendung|Count|Count|Anzahl von API-Aufrufen|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|RunsStarted|Gestartete Ausführungen|Count|Gesamt|Anzahl gestarteter Workflowausführungen.|Keine Dimensionen|
|RunsCompleted|Abgeschlossene Ausführungen|Count|Gesamt|Anzahl abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsSucceeded|Erfolgreiche Ausführungen|Count|Gesamt|Anzahl erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunsFailed|Ausführungsfehler|Count|Gesamt|Anzahl fehlerhafter Workflowausführungen.|Keine Dimensionen|
|RunsCancelled|Abgebrochene Ausführungen|Count|Gesamt|Anzahl abgebrochener Workflowausführungen.|Keine Dimensionen|
|RunLatency|Ausführungslatenz|Sekunden|Durchschnitt|Latenz abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunSuccessLatency|Latenz erfolgreicher Ausführungen|Sekunden|Durchschnitt|Latenz erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunThrottledEvents|Ereignisse zu gedrosselten Ausführungen|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen oder Triggern.|Keine Dimensionen|
|RunFailurePercentage|Prozentsatz der Ausführungsfehler|Prozent|Gesamt|Prozentsatz fehlerhafter Workflowausführungen|Keine Dimensionen|
|ActionsStarted|Gestartete Aktionen |Count|Gesamt|Anzahl gestarteter Workflowaktionen.|Keine Dimensionen|
|ActionsCompleted|Abgeschlossene Aktionen |Count|Gesamt|Anzahl abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsSucceeded|Erfolgreiche Aktionen |Count|Gesamt|Anzahl erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionsFailed|Aktionsfehler|Count|Gesamt|Anzahl fehlerhafter Workflowaktionen.|Keine Dimensionen|
|ActionsSkipped|Übersprungene Aktionen |Count|Gesamt|Anzahl übersprungener Workflowaktionen.|Keine Dimensionen|
|ActionLatency|Aktionslatenz |Sekunden|Durchschnitt|Latenz abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionSuccessLatency|Latenz erfolgreicher Aktionen |Sekunden|Durchschnitt|Latenz erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionThrottledEvents|Ereignisse zu gedrosselten Aktionen|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen.|Keine Dimensionen|
|TriggersStarted|Gestartete Trigger |Count|Gesamt|Anzahl gestarteter Workflowtrigger.|Keine Dimensionen|
|TriggersCompleted|Abgeschlossene Trigger |Count|Gesamt|Anzahl abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersSucceeded|Erfolgreiche Trigger |Count|Gesamt|Anzahl erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggersFailed|Triggerfehler |Count|Gesamt|Anzahl fehlerhafter Workflowtrigger.|Keine Dimensionen|
|TriggersSkipped|Übersprungene Trigger|Count|Gesamt|Anzahl übersprungener Workflowtrigger.|Keine Dimensionen|
|TriggersFired|Ausgelöste Trigger |Count|Gesamt|Anzahl ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerLatency|Triggerlatenz |Sekunden|Durchschnitt|Latenz abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggerFireLatency|Latenz beim Auslösen von Triggern |Sekunden|Durchschnitt|Latenz ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerSuccessLatency|Latenz erfolgreicher Trigger |Sekunden|Durchschnitt|Latenz erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerThrottledEvents|Ereignisse zu gedrosselten Triggern|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowtriggern.|Keine Dimensionen|
|BillableActionExecutions|Ausführungen abrechenbarer Aktionen|Count|Gesamt|Anzahl der Workflowaktionsausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|BillableTriggerExecutions|Ausführungen abrechenbarer Trigger|Count|Gesamt|Anzahl der Workflowtriggerausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|TotalBillableExecutions|Gesamtzahl der abrechenbaren Ausführungen|Count|Gesamt|Anzahl der Workflowausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|BillingUsageNativeOperation|Nutzungsabrechnung für Ausführungen nativer Vorgänge|Count|Gesamt|Anzahl von Ausführungen nativer Vorgänge, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStandardConnector|Nutzungsabrechnung für Ausführungen standardmäßiger Connectors|Count|Gesamt|Anzahl von Ausführungen standardmäßiger Connectors, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStorageConsumption|Nutzungsabrechnung für Ausführungen mit Speicherverbrauch|Count|Gesamt|Anzahl von Ausführungen mit Speicherverbrauch, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageNativeOperation|Nutzungsabrechnung für Ausführungen nativer Vorgänge|Count|Gesamt|Anzahl von Ausführungen nativer Vorgänge, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStandardConnector|Nutzungsabrechnung für Ausführungen standardmäßiger Connectors|Count|Gesamt|Anzahl von Ausführungen standardmäßiger Connectors, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStorageConsumption|Nutzungsabrechnung für Ausführungen mit Speicherverbrauch|Count|Gesamt|Anzahl von Ausführungen mit Speicherverbrauch, die in Rechnung gestellt werden.|Keine Dimensionen|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|RunsStarted|Gestartete Ausführungen|Count|Gesamt|Anzahl gestarteter Workflowausführungen.|Keine Dimensionen|
|RunsCompleted|Abgeschlossene Ausführungen|Count|Gesamt|Anzahl abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsSucceeded|Erfolgreiche Ausführungen|Count|Gesamt|Anzahl erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunsFailed|Ausführungsfehler|Count|Gesamt|Anzahl fehlerhafter Workflowausführungen.|Keine Dimensionen|
|RunsCancelled|Abgebrochene Ausführungen|Count|Gesamt|Anzahl abgebrochener Workflowausführungen.|Keine Dimensionen|
|RunLatency|Ausführungslatenz|Sekunden|Durchschnitt|Latenz abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunSuccessLatency|Latenz erfolgreicher Ausführungen|Sekunden|Durchschnitt|Latenz erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunThrottledEvents|Ereignisse zu gedrosselten Ausführungen|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen oder Triggern.|Keine Dimensionen|
|RunStartThrottledEvents|Ereignisse zu gedrosselten Ausführungsstarts|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflow-Ausführungsstarts.|Keine Dimensionen|
|RunFailurePercentage|Prozentsatz der Ausführungsfehler|Prozent|Gesamt|Prozentsatz fehlerhafter Workflowausführungen|Keine Dimensionen|
|ActionsStarted|Gestartete Aktionen |Count|Gesamt|Anzahl gestarteter Workflowaktionen.|Keine Dimensionen|
|ActionsCompleted|Abgeschlossene Aktionen |Count|Gesamt|Anzahl abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsSucceeded|Erfolgreiche Aktionen |Count|Gesamt|Anzahl erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionsFailed|Aktionsfehler |Count|Gesamt|Anzahl fehlerhafter Workflowaktionen.|Keine Dimensionen|
|ActionsSkipped|Übersprungene Aktionen |Count|Gesamt|Anzahl übersprungener Workflowaktionen.|Keine Dimensionen|
|ActionLatency|Aktionslatenz |Sekunden|Durchschnitt|Latenz abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionSuccessLatency|Latenz erfolgreicher Aktionen |Sekunden|Durchschnitt|Latenz erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionThrottledEvents|Ereignisse zu gedrosselten Aktionen|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen.|Keine Dimensionen|
|TriggersStarted|Gestartete Trigger |Count|Gesamt|Anzahl gestarteter Workflowtrigger.|Keine Dimensionen|
|TriggersCompleted|Abgeschlossene Trigger |Count|Gesamt|Anzahl abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersSucceeded|Erfolgreiche Trigger |Count|Gesamt|Anzahl erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggersFailed|Triggerfehler |Count|Gesamt|Anzahl fehlerhafter Workflowtrigger.|Keine Dimensionen|
|TriggersSkipped|Übersprungene Trigger|Count|Gesamt|Anzahl übersprungener Workflowtrigger.|Keine Dimensionen|
|TriggersFired|Ausgelöste Trigger |Count|Gesamt|Anzahl ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerLatency|Triggerlatenz |Sekunden|Durchschnitt|Latenz abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggerFireLatency|Latenz beim Auslösen von Triggern |Sekunden|Durchschnitt|Latenz ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerSuccessLatency|Latenz erfolgreicher Trigger |Sekunden|Durchschnitt|Latenz erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerThrottledEvents|Ereignisse zu gedrosselten Triggern|Count|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowtriggern.|Keine Dimensionen|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow-Prozessorauslastung für Integrationsdienstumgebung|Prozent|Durchschnitt|Workflow-Prozessorauslastung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow-Arbeitsspeichernutzung für Integrationsdienstumgebung|Prozent|Durchschnitt|Workflow-Arbeitsspeichernutzung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector-Prozessorauslastung für Integrationsdienstumgebung|Prozent|Durchschnitt|Connector-Prozessorauslastung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector-Arbeitsspeichernutzung für Integrationsdienstumgebung|Prozent|Durchschnitt|Connector-Arbeitsspeichernutzung für Integrationsdienstumgebung|Keine Dimensionen|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Abgeschlossene Ausführungen|Abgeschlossene Ausführungen|Count|Gesamt|Anzahl erfolgreich abgeschlossener Ausführungen für diesen Arbeitsbereich|Szenario|
|Gestartete Ausführungen|Gestartete Ausführungen|Count|Gesamt|Anzahl gestarteter Ausführungen für diesen Arbeitsbereich|Szenario|
|Fehlerhafte Ausführungen|Fehlerhafte Ausführungen|Count|Gesamt|Anzahl fehlerhafter Ausführungen für diesen Arbeitsbereich|Szenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Verwendung|Verwendung|Count|Count|Anzahl von API-Aufrufen|ApiCategory, ApiName, ResultType, ResponseCode|
|Verfügbarkeit|Verfügbarkeit|Prozent|Durchschnitt|Verfügbarkeit der APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|AverageOtherLatency|Durchschnittliche sonstige Wartezeit|ms/op|Durchschnitt|Durchschnittliche sonstige Wartezeit (in der nicht gelesen oder geschrieben wird) in Millisekunden pro Vorgang|Keine Dimensionen|
|AverageReadLatency|Durchschnittliche Wartezeit beim Lesevorgang|ms/op|Durchschnitt|Durchschnittliche Wartezeit beim Lesen in Millisekunden pro Vorgang|Keine Dimensionen|
|AverageTotalLatency|Durchschnittliche Gesamtwartezeit|ms/op|Durchschnitt|Durchschnittliche Gesamtwartezeit in Millisekunden pro Vorgang|Keine Dimensionen|
|AverageWriteLatency|Durchschnittliche Wartezeit beim Schreibvorgang|ms/op|Durchschnitt|Durchschnittliche Wartezeit beim Schreiben in Millisekunden pro Vorgang|Keine Dimensionen|
|FilesystemOtherOps|Sonstige Vorgänge des Dateisystems|ops|Durchschnitt|Anzahl sonstiger Vorgänge des Dateisystems (keine Lese- oder Schreibvorgänge)|Keine Dimensionen|
|FilesystemReadOps|Lesevorgänge des Dateisystems|ops|Durchschnitt|Anzahl der Lesevorgänge des Dateisystems|Keine Dimensionen|
|FilesystemTotalOps|Gesamte Vorgänge des Dateisystems|ops|Durchschnitt|Summe aller Dateisystemvorgänge|Keine Dimensionen|
|FilesystemWriteOps|Schreibvorgänge des Dateisystems|ops|Durchschnitt|Anzahl der Schreibvorgänge des Dateisystems|Keine Dimensionen|
|IoBytesPerOtherOps|E/A-Byte pro sonstigem Vorgang|bytes/op|Durchschnitt|Anzahl der E/A-Byte pro sonstigem Vorgang (keine Lese- oder Schreibvorgänge)|Keine Dimensionen|
|IoBytesPerReadOps|E/A-Byte pro Lesevorgang|bytes/op|Durchschnitt|Anzahl der E/A-Byte pro Lesevorgang|Keine Dimensionen|
|IoBytesPerTotalOps|E/A-Byte pro Vorgang über alle Vorgänge hinweg|bytes/op|Durchschnitt|Summe aller E/A-Byte pro Vorgang|Keine Dimensionen|
|IoBytesPerWriteOps|E/A-Byte pro Schreibvorgang|bytes/op|Durchschnitt|Anzahl der E/A-Byte pro Schreibvorgang|Keine Dimensionen|
|OtherIops|Sonstige IOPS|operations/second|Durchschnitt|Sonstige E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|OtherThroughput|Sonstiger Durchsatz|MBit/s|Durchschnitt|Sonstiger Durchsatz (kein Lese- oder Schreibvorgang) in Megabyte pro Sekunde|Keine Dimensionen|
|ReadIops|Lese-IOPS|operations/second|Durchschnitt|E/A-Vorgänge lesen pro Sekunde|Keine Dimensionen|
|ReadThroughput|Lesedurchsatz|MBit/s|Durchschnitt|Lesedurchsatz in Megabyte pro Sekunde|Keine Dimensionen|
|TotalIops|IOPS gesamt|operations/second|Durchschnitt|Summe aller E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|TotalThroughput|Durchsatz gesamt|MBit/s|Durchschnitt|Summe aller Durchsätze in Megabyte pro Sekunde|Keine Dimensionen|
|VolumeAllocatedSize|Zugeordnete Größe des Volume|Byte|Durchschnitt|Zugeordnete Größe des Volume (nicht die tatsächlich verwendeten Bytes)|Keine Dimensionen|
|VolumeLogicalSize|Logische Größe des Volume|Byte|Durchschnitt|Logische Größe des Volume (verwendete Bytes)|Keine Dimensionen|
|VolumeSnapshotSize|Größe der Volumenmomentaufnahme|Byte|Durchschnitt|Größe aller Momentaufnahmen im Volumen|Keine Dimensionen|
|WriteIops|Schreib-IOPS|operations/second|Durchschnitt|E/A-Vorgänge schreiben pro Sekunde|Keine Dimensionen|
|WriteThroughput|Schreibdurchsatz|MBit/s|Durchschnitt|Schreibdurchsatz in Megabyte pro Sekunde|Keine Dimensionen|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Zugeordnete Größe des Volumepools|Byte|Durchschnitt|Zugeordnete Größe des Pools (nicht die tatsächlich verwendeten Bytes)|Keine Dimensionen|
|VolumePoolAllocatedUsed|Verwendeter zugeordneter Volumepool|Byte|Durchschnitt|Zugeordnete verwendete Größe des Pools|Keine Dimensionen|
|VolumePoolTotalLogicalSize|Gesamte logische Größe des Volumepools|Byte|Durchschnitt|Summe der logischen Größe aller zum Pool gehörenden Volumes|Keine Dimensionen|
|VolumePoolTotalSnapshotSize|Größe der Gesamtmomentaufnahme des Volumepools|Byte|Durchschnitt|Summe aller Momentaufnahmen im Pool|Keine Dimensionen|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|BytesSentRate|Gesendete Bytes|Count|Gesamt|Anzahl der durch die Netzwerkschnittstelle gesendeten Bytes|Keine Dimensionen|
|BytesReceivedRate|Empfangene Bytes|Count|Gesamt|Anzahl der durch die Netzwerkschnittstelle empfangenen Bytes|Keine Dimensionen|
|PacketsSentRate|Gesendete Pakete|Count|Gesamt|Anzahl der durch die Netzwerkschnittstelle gesendeten Pakete|Keine Dimensionen|
|PacketsReceivedRate|Empfangene Pakete|Count|Gesamt|Anzahl der durch die Netzwerkschnittstelle empfangenen Pakete|Keine Dimensionen|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|VipAvailability|Datenpfadverfügbarkeit|Count|Durchschnitt|Durchschnittliche Load Balancer-Datenpfadverfügbarkeit pro Zeitdauer|FrontendIPAddress, FrontendPort|
|DipAvailability|Integritätsteststatus|Count|Durchschnitt|Durchschnittlicher Load Balancer-Integritätsteststatus pro Zeitdauer|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byteanzahl|Count|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Paketzahl|Count|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN-Anzahl|Count|Gesamt|Gesamtanzahl von SYN-Paketen, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Anzahl von SNAT-Verbindungen|Count|Gesamt|Gesamtanzahl von neuen SNAT-Verbindungen, die innerhalb des Zeitraums erstellt wurden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Zugeordnete SNAT-Ports (Vorschau)|Count|Gesamt|Gesamtanzahl von SNAT-Ports, die im Zeitraum zugeordnet wurden|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Verwendete SNAT-Ports (Vorschau)|Count|Gesamt|Gesamtanzahl von SNAT-Ports, die im Zeitraum verwendet wurden|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|QueryVolume|Abfragevolume|Count|Gesamt|Anzahl von Abfragen für eine DNS-Zone|Keine Dimensionen|
|RecordSetCount|Anzahl von Datensatzgruppen|Count|Maximum|Anzahl von Datensatzgruppen in einer DNS-Zone|Keine Dimensionen|
|RecordSetCapacityUtilization|Kapazitätsauslastung von Datensatzgruppen|Prozent|Maximum|Von einer DNS-Zone genutzte Kapazität einer Datensatzgruppe in Prozent|Keine Dimensionen|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|PacketsInDDoS|Als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende Pakete|Keine Dimensionen|
|PacketsDroppedDDoS|Als DDoS eingehende gelöschte Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte Pakete|Keine Dimensionen|
|PacketsForwardedDDoS|Weitergeleitete als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Pakete|Keine Dimensionen|
|TCPPacketsInDDoS|Als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende TCP-Pakete|Keine Dimensionen|
|TCPPacketsDroppedDDoS|Als DDoS eingehende gelöschte TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Pakete|Keine Dimensionen|
|TCPPacketsForwardedDDoS|Weitergeleitete als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Pakete|Keine Dimensionen|
|UDPPacketsInDDoS|Als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende UDP-Pakete|Keine Dimensionen|
|UDPPacketsDroppedDDoS|Als DDoS eingehende gelöschte UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Pakete|Keine Dimensionen|
|UDPPacketsForwardedDDoS|Weitergeleitete als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Pakete|Keine Dimensionen|
|BytesInDDoS|Als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende Bytes|Keine Dimensionen|
|BytesDroppedDDoS|Als DDoS eingehende gelöschte Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte Bytes|Keine Dimensionen|
|BytesForwardedDDoS|Weitergeleitete als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Bytes|Keine Dimensionen|
|TCPBytesInDDoS|Als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende TCP-Bytes|Keine Dimensionen|
|TCPBytesDroppedDDoS|Als DDoS eingehende gelöschte TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Bytes|Keine Dimensionen|
|TCPBytesForwardedDDoS|Weitergeleitete als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Bytes|Keine Dimensionen|
|UDPBytesInDDoS|Als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende UDP-Bytes|Keine Dimensionen|
|UDPBytesDroppedDDoS|Als DDoS eingehende gelöschte UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Bytes|Keine Dimensionen|
|UDPBytesForwardedDDoS|Weitergeleitete als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Bytes|Keine Dimensionen|
|IfUnderDDoSAttack|Unter DDoS-Angriff oder nicht|Count|Maximum|Unter DDoS-Angriff oder nicht|Keine Dimensionen|
|DDoSTriggerTCPPackets|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|DDoSTriggerUDPPackets|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|DDoSTriggerSYNPackets|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|VipAvailability|Datenpfadverfügbarkeit|Count|Durchschnitt|Durchschnittliche Verfügbarkeit der IP-Adresse pro Zeitdauer|Port|
|ByteCount|Byteanzahl|Count|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|Port, Richtung|
|PacketCount|Paketzahl|Count|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|Port, Richtung|
|SynCount|SYN-Anzahl|Count|Gesamt|Gesamtanzahl von SYN-Paketen, die im Zeitraum übertragen wurden|Port, Richtung|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ApplicationRuleHit|Trefferanzahl für Anwendungsregeln|Count|Gesamt|Häufigkeit, mit der Anwendungsregeln aufgerufen wurden|Status, Grund, Protokoll|
|NetworkRuleHit|Trefferanzahl für Netzwerkregeln|Count|Gesamt|Häufigkeit, mit der Netzwerkregeln aufgerufen wurden|Status, Grund, Protokoll|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Throughput|Throughput|Bytes pro Sekunde|Gesamt|Anzahl von Bytes pro Sekunde, die das Application Gateway bereitgestellt hat|Keine Dimensionen|
|UnhealthyHostCount|Anzahl von fehlerhaften Hosts|Count|Durchschnitt|Anzahl von fehlerhaften Back-End-Hosts|BackendSettingsPool|
|HealthyHostCount|Anzahl von fehlerfreien Hosts|Count|Durchschnitt|Anzahl von fehlerfreien Back-End-Hosts|BackendSettingsPool|
|TotalRequests|Anzahl von Anforderungen|Count|Gesamt|Anzahl von erfolgreichen Anforderungen über Application Gateway|BackendSettingsPool|
|FailedRequests|Anforderungsfehler|Count|Gesamt|Anzahl von fehlerhaften Anforderungen über Application Gateway|BackendSettingsPool|
|ResponseStatus|Antwortstatus|Count|Gesamt|Von Application Gateway zurückgegebener HTTP-Antwortstatus|HttpStatusGroup|
|CurrentConnections|Aktuelle Verbindungen|Count|Gesamt|Anzahl von aktuellen Verbindungen mit Application Gateway|Keine Dimensionen|
|CapacityUnits|Aktuelle Kapazitätseinheiten|Count|Durchschnitt|Verbrauchte Kapazitätseinheiten|Keine Dimensionen|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|AverageBandwidth|S2S-Gatewaybandbreite|Bytes pro Sekunde|Durchschnitt|Durchschnittliche Site-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Keine Dimensionen|
|P2SBandwidth|P2S-Gatewaybandbreite|Bytes pro Sekunde|Durchschnitt|Durchschnittliche Point-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Keine Dimensionen|
|P2SConnectionCount|Anzahl der P2S-Verbindungen|Count|Maximum|Anzahl der Point-to-Site-Verbindung eines Gateways|Protocol|
|TunnelAverageBandwidth|Bandbreite des Tunnels|Bytes pro Sekunde|Durchschnitt|Durchschnittliche Bandbreite eines Tunnels in Bytes pro Sekunde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Vom Tunnel ausgehende Bytes|Byte|Gesamt|Ausgehende Bytes eines Tunnels|ConnectionName, RemoteIP|
|TunnelIngressBytes|In Tunnel eingehende Bytes|Byte|Gesamt|Eingehende Bytes eines Tunnels|ConnectionName, RemoteIP|
|TunnelEgressPackets|Vom Tunnel ausgehende Pakete|Count|Gesamt|Ausgehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|In Tunnel eingehende Pakete|Count|Gesamt|Eingehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Count|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten ausgehenden Pakete|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Count|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten eingehenden Pakete|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Anzahl pro Sekunde|Durchschnitt|In Azure eingehende Bits pro Sekunde|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Anzahl pro Sekunde|Durchschnitt|Aus Azure ausgehende Bits pro Sekunde|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Anzahl pro Sekunde|Durchschnitt|In Azure eingehende Bits pro Sekunde|Keine Dimensionen|
|BitsOutPerSecond|BitsOutPerSecond|Anzahl pro Sekunde|Durchschnitt|Aus Azure ausgehende Bits pro Sekunde|Keine Dimensionen|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Anzahl pro Sekunde|Durchschnitt|In Azure eingehende Bits pro Sekunde|Keine Dimensionen|
|BitsOutPerSecond|BitsOutPerSecond|Anzahl pro Sekunde|Durchschnitt|Aus Azure ausgehende Bits pro Sekunde|Keine Dimensionen|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|QpsByEndpoint|Abfragen nach zurückgegebenem Endpunkt|Count|Gesamt|Häufigkeit, mit der ein Endpunkt des Traffic Managers innerhalb des angegebenen Zeitrahmens zurückgegeben wurde|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpunktstatus nach Endpunkt|Count|Maximum|1, wenn der Teststatus des Endpunkts „Aktiviert“ ist, andernfalls 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ProbesFailedPercent|Fehlerhafte Tests in Prozent|Prozent|Durchschnitt|Fehlerhafte Tests bei Konnektivitätsüberwachung in Prozent|Keine Dimensionen|
|AverageRoundtripMs|Durchschn. Roundtripzeit (ms)|Millisekunden|Durchschnitt|Durchschnittliche Netzwerk-Roundtripzeit (ms) für die Konnektivitätsüberwachung von Testdatenverkehr, der zwischen Quelle und Ziel gesendet wurde.|Keine Dimensionen|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|RequestCount|Anzahl der Anforderungen|Count|Gesamt|Die Anzahl der vom HTTP/S-Proxy bereitgestellten Clientanforderungen|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Anforderungsgröße|Byte|Gesamt|Die Anzahl der von Clients als Anforderungen an den HTTP/S-Proxy gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Antwortgröße|Byte|Gesamt|Die Anzahl der vom HTTP/S-Proxy als Antworten an Clients gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Back-End-Anforderungsanzahl|Count|Gesamt|Die Anzahl der vom HTTP/S-Proxy an Back-Ends gesendeten Anforderungen|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Latenz der Back-End-Anforderung|Millisekunden|Durchschnitt|Die Zeitspanne zwischen dem Senden der Anforderung durch den HTTP/S-Proxy an das Back-End und dem Empfangen des letzten Antwortbytes durch den HTTP/S-Proxy vom Back-End|Back-End|
|TotalLatency|Gesamtlatenz|Millisekunden|Durchschnitt|Die Zeitspanne zwischen dem Empfang der Clientanforderung durch den HTTP/S-Proxy und der Bestätigung des letzten Antwortbytes vom HTTP/S-Proxy durch den Client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Prozentsatz der Back-End-Integrität|Prozent|Durchschnitt|Der Prozentsatz der erfolgreichen Integritätstests vom HTTP/S-Proxy zu Back-Ends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Anforderungsanzahl für die Web Application Firewall|Count|Gesamt|Die Anzahl der von der Web Application Firewall verarbeiteten Clientanforderungen|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|registration.all|Registrierungsvorgänge|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsvorgängen an (Erstellungen, Aktualisierungen, Abfragen und Löschungen). |Keine Dimensionen|
|registration.create|Registrierungserstellungsvorgänge|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungserstellungen an.|Keine Dimensionen|
|registration.update|Registrierungsaktualisierungsvorgänge|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsaktualisierungen an.|Keine Dimensionen|
|registration.get|Registrierungslesevorgänge|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsabfragen an.|Keine Dimensionen|
|registration.delete|Registrierungslöschvorgänge|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungslöschungen an.|Keine Dimensionen|
|incoming|Eingehende Nachrichten|Count|Gesamt|Gibt die Anzahl von allen erfolgreichen Übermittlungs-API-Aufrufen an. |Keine Dimensionen|
|incoming.scheduled|Geplante Pushbenachrichtigungen (gesendet)|Count|Gesamt|Geplante Pushbenachrichtigungen (abgebrochen)|Keine Dimensionen|
|incoming.scheduled.cancel|Geplante Pushbenachrichtigungen (abgebrochen)|Count|Gesamt|Geplante Pushbenachrichtigungen (abgebrochen)|Keine Dimensionen|
|scheduled.pending|Ausstehende Pushbenachrichtigungen|Count|Gesamt|Ausstehende Pushbenachrichtigungen|Keine Dimensionen|
|installation.all|Installationsverwaltungsvorgänge|Count|Gesamt|Installationsverwaltungsvorgänge|Keine Dimensionen|
|installation.get|Installationsvorgänge abrufen|Count|Gesamt|Installationsvorgänge abrufen|Keine Dimensionen|
|installation.upsert|Installationsvorgänge erstellen oder aktualisieren|Count|Gesamt|Installationsvorgänge erstellen oder aktualisieren|Keine Dimensionen|
|installation.patch|Patchinstallationsvorgänge|Count|Gesamt|Patchinstallationsvorgänge|Keine Dimensionen|
|installation.delete|Installationsvorgänge löschen|Count|Gesamt|Installationsvorgänge löschen|Keine Dimensionen|
|outgoing.allpns.success|Erfolgreiche Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.allpns.invalidpayload|Nutzlastfehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil vom PNS ein Nutzlastfehler zurückgegeben wurde.|Keine Dimensionen|
|outgoing.allpns.pnserror|Fehler des externen Benachrichtigungssystems|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil ein Problem bei der Kommunikation mit dem PNS vorlag (ausschließlich Authentifizierungsprobleme).|Keine Dimensionen|
|outgoing.allpns.channelerror|Kanalfehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der Kanal ungültig, nicht der richtigen App zugeordnet, gedrosselt oder abgelaufen war.|Keine Dimensionen|
|outgoing.allpns.badorexpiredchannel|Kanalfehler (unzulässig oder abgelaufen)|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der Kanal, das Token oder die Registrierungs-ID in der Registrierung abgelaufen oder ungültig war.|Keine Dimensionen|
|outgoing.wns.success|Erfolgreiche WNS-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.wns.invalidcredentials|WNS-Autorisierungsfehler (ungültige Anmeldeinformationen)|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden. (Windows Live erkennt die Anmeldeinformationen nicht.)|Keine Dimensionen|
|outgoing.wns.badchannel|WNS-Fehler durch fehlerhaften Kanal|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der ChannelURI in der Registrierung nicht erkannt wurde (WNS-Status: 404 – Nicht gefunden).|Keine Dimensionen|
|outgoing.wns.expiredchannel|WNS-Fehler durch abgelaufenen Kanal|Count|Gesamt|Die Anzahl von nicht erfolgreichen Pushvorgängen, weil der ChannelURI abgelaufen ist (WNS-Status: 410 – Fehlend).|Keine Dimensionen|
|outgoing.wns.throttled|Gedrosselte WNS-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App vom WNS gedrosselt wird (WNS-Status: 406 – Nicht annehmbar).|Keine Dimensionen|
|outgoing.wns.tokenproviderunreachable|WNS-Autorisierungsfehler (nicht erreichbar)|Count|Gesamt|Windows Live ist nicht erreichbar.|Keine Dimensionen|
|outgoing.wns.invalidtoken|WNS-Autorisierungsfehler (ungültiges Token)|Count|Gesamt|Das für WNS bereitgestellte Token ist nicht gültig (WNS-Status: 401 – Nicht autorisiert).|Keine Dimensionen|
|outgoing.wns.wrongtoken|WNS-Autorisierungsfehler (falsches Token)|Count|Gesamt|Das für WNS bereitgestellte Token ist gültig, gilt aber für eine andere Anwendung (WNS-Status: 403 – Verboten). Dieser Fall kann eintreten, wenn der ChannelURI in der Registrierung einer anderen App zugeordnet wird. Stellen Sie sicher, dass die Client-App der App zugeordnet ist, deren Anmeldeinformationen sich im Notification Hub befinden.|Keine Dimensionen|
|outgoing.wns.invalidnotificationformat|Ungültiges WNS-Benachrichtigungsformat|Count|Gesamt|Das Format der Benachrichtigung ist ungültig (WNS-Status: 400). Beachten Sie, dass von WNS nicht alle ungültigen Nutzlasten abgelehnt werden.|Keine Dimensionen|
|outgoing.wns.invalidnotificationsize|WNS-Fehler durch ungültige Benachrichtigungsgröße|Count|Gesamt|Die Benachrichtigungsnutzlast ist zu groß (WNS-Status: 413).|Keine Dimensionen|
|outgoing.wns.channelthrottled|WNS-Kanal gedrosselt|Count|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde (WNS-Antwortheader: X-WNS-NotificationStatus:channelThrottled).|Keine Dimensionen|
|outgoing.wns.channeldisconnected|WNS-Kanal nicht verbunden|Count|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde (WNS-Antwortheader: X-WNS-DeviceConnectionStatus: disconnected).|Keine Dimensionen|
|outgoing.wns.dropped|Verworfene WNS-Benachrichtigungen|Count|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde („X-WNS-NotificationStatus: dropped“, aber nicht „X-WNS-DeviceConnectionStatus: disconnected“).|Keine Dimensionen|
|outgoing.wns.pnserror|WNS-Fehler|Count|Gesamt|Die Benachrichtigung wurde aufgrund von Fehlern bei der Kommunikation mit WNS nicht übermittelt.|Keine Dimensionen|
|outgoing.wns.authenticationerror|WNS-Authentifizierungsfehler|Count|Gesamt|Die Benachrichtigung wurde nicht übermittelt, weil Fehler bei der Kommunikation mit Windows Live aufgetreten sind, die Anmeldeinformationen ungültig waren oder das falsche Token verwendet wurde.|Keine Dimensionen|
|outgoing.apns.success|Erfolgreiche APNS-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.apns.invalidcredentials|APNS-Autorisierungsfehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.apns.badchannel|APNS-Fehler durch fehlerhaften Kanal|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das Token ungültig ist (APNS-Statuscode: 8).|Keine Dimensionen|
|outgoing.apns.expiredchannel|APNS-Fehler durch abgelaufenen Kanal|Count|Gesamt|Die Anzahl von Token, die aufgrund des APNS-Feedbackkanals ungültig gemacht wurden.|Keine Dimensionen|
|outgoing.apns.invalidnotificationsize|APNS-Fehler durch ungültige Benachrichtigungsgröße|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast zu groß war (APNS-Statuscode: 7).|Keine Dimensionen|
|outgoing.apns.pnserror|APNS-Fehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit APNS aufgetreten sind.|Keine Dimensionen|
|outgoing.gcm.success|Erfolgreiche GCM-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.gcm.invalidcredentials|GCM-Autorisierungsfehler (ungültige Anmeldeinformationen)|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.gcm.badchannel|GCM-Fehler durch fehlerhaften Kanal|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung nicht erkannt wurde (GCM-Ergebnis: Ungültige Registrierung).|Keine Dimensionen|
|outgoing.gcm.expiredchannel|GCM-Fehler durch abgelaufenen Kanal|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung abgelaufen war (GCM-Ergebnis: NotRegistered).|Keine Dimensionen|
|outgoing.gcm.throttled|Gedrosselte GCM-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App von GCM gedrosselt wurde (GCM-Statuscode: 501 - 599 oder result:Unavailable).|Keine Dimensionen|
|outgoing.gcm.invalidnotificationformat|Ungültiges GCM-Benachrichtigungsformat|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast nicht richtig formatiert war (GCM-Ergebnis: InvalidDataKey oder InvalidTtl).|Keine Dimensionen|
|outgoing.gcm.invalidnotificationsize|GCM-Fehler durch ungültige Benachrichtigungsgröße|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast zu groß war (GCM-Ergebnis: MessageTooBig).|Keine Dimensionen|
|outgoing.gcm.wrongchannel|GCM-Fehler durch falschen Kanal|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung nicht der aktuellen App zugeordnet war (GCM-Ergebnis: InvalidPackageName).|Keine Dimensionen|
|outgoing.gcm.pnserror|GCM-Fehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit GCM aufgetreten sind.|Keine Dimensionen|
|outgoing.gcm.authenticationerror|GCM-Authentifizierungsfehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die angegebenen Anmeldeinformationen vom PNS nicht akzeptiert wurden, die Anmeldeinformationen blockiert wurden oder die SenderId in der App nicht richtig konfiguriert wurde (GCM-Ergebnis: MismatchedSenderId).|Keine Dimensionen|
|outgoing.mpns.success|Erfolgreiche MPNS-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.mpns.invalidcredentials|Ungültige MPNS-Anmeldeinformationen|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.mpns.badchannel|MPNS-Fehler durch fehlerhaften Kanal|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der ChannelURI in der Registrierung nicht erkannt wurde (MPNS-Status: 404 – Nicht gefunden).|Keine Dimensionen|
|outgoing.mpns.throttled|Gedrosselte MPNS-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App vom MPNS gedrosselt wird (WNS-MPNS: 406 – Nicht annehmbar).|Keine Dimensionen|
|outgoing.mpns.invalidnotificationformat|Ungültiges MPNS-Benachrichtigungsformat|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast der Benachrichtigung zu groß war.|Keine Dimensionen|
|outgoing.mpns.channeldisconnected|MPNS-Kanal nicht verbunden|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Verbindung für den ChannelURI in der Registrierung getrennt wurde (MPNS-Status: 412 – Nicht gefunden).|Keine Dimensionen|
|outgoing.mpns.dropped|Verworfene MPNS-Benachrichtigungen|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die von MPNS verworfen wurden (MPNS-Antwortheader: X-NotificationStatus: QueueFull oder Suppressed).|Keine Dimensionen|
|outgoing.mpns.pnserror|MPNS-Fehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit MPNS aufgetreten sind.|Keine Dimensionen|
|outgoing.mpns.authenticationerror|MPNS-Authentifizierungsfehler|Count|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|notificationhub.pushes|Alle ausgehenden Benachrichtigungen|Count|Gesamt|Alle ausgehenden Benachrichtigungen des Benachrichtigungs-Hub|Keine Dimensionen|
|incoming.all.requests|Alle eingehenden Anforderungen|Count|Gesamt|Gesamtzahl eingehender Anforderungen für einen Notification Hub|Keine Dimensionen|
|incoming.all.failedrequests|Alle eingehenden fehlerhaften Anforderungen|Count|Gesamt|Gesamtzahl eingehender fehlerhafter Anforderungen für einen Notification Hub|Keine Dimensionen|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Average_% Free Inodes|% freie Inodes|Count|Durchschnitt|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% freier Speicher|Count|Durchschnitt|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% verwendete Inodes|Count|Durchschnitt|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% verwendeter Speicher|Count|Durchschnitt|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Byte gelesen/s|Count|Durchschnitt|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Lesevorgänge/s|Count|Durchschnitt|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Übertragungen/s|Count|Durchschnitt|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Byte geschrieben/s|Count|Durchschnitt|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Schreibvorgänge/s|Count|Durchschnitt|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Freie Megabytes|Count|Durchschnitt|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logischer Datenträger Bytes/s|Count|Durchschnitt|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% verfügbarer Speicher|Count|Durchschnitt|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% verfügbarer Auslagerungsbereich|Count|Durchschnitt|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% verwendeter Arbeitsspeicher|Count|Durchschnitt|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% verwendeter Auslagerungsbereich|Count|Durchschnitt|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Verfügbarer Speicher in MB|Count|Durchschnitt|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Verfügbarer Auslagerungsbereich in MB|Count|Durchschnitt|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Seitenlesevorgänge/s|Count|Durchschnitt|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Schreibvorgänge/s|Count|Durchschnitt|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Seiten/s|Count|Durchschnitt|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Verwendeter Auslagerungsbereich in MB|Count|Durchschnitt|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Verwendeter Speicher in MB|Count|Durchschnitt|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Summe übertragene Bytes|Count|Durchschnitt|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Summe empfangene Bytes|Count|Durchschnitt|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Summe Bytes|Count|Durchschnitt|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Summe übermittelte Pakete|Count|Durchschnitt|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Summe empfangene Pakete|Count|Durchschnitt|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Summe Rx-Fehler|Count|Durchschnitt|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Summe Tx-Fehler|Count|Durchschnitt|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Summe Konflikte|Count|Durchschnitt|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/gelesen|Durchschn. Datenträger s/gelesen|Count|Durchschnitt|Average_Avg. Datenträger s/gelesen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/übertragen|Durchschn. Datenträger s/übertragen|Count|Durchschnitt|Average_Avg. Datenträger s/übertragen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/geschrieben|Durchschn. Datenträger s/geschrieben|Count|Durchschnitt|Average_Avg. Datenträger s/geschrieben|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physischer Datenträger Bytes/s|Count|Durchschnitt|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|PCT-privilegierte Zeit|Count|Durchschnitt|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|PCT-Benutzerzeit|Count|Durchschnitt|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Verwendeter Arbeitsspeicher in KB|Count|Durchschnitt|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtueller gemeinsamer Speicher|Count|Durchschnitt|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC-Zeit|Count|Durchschnitt|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Leerlaufzeit|Count|Durchschnitt|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interruptzeit|Count|Durchschnitt|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% E/a-Wartezeit|Count|Durchschnitt|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice-Zeit|Count|Durchschnitt|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% privilegierte Zeit|Count|Durchschnitt|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Prozessorzeit|Count|Durchschnitt|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% Benutzerzeit|Count|Durchschnitt|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Freier physischer Speicher|Count|Durchschnitt|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Freier Speicherplatz in Auslagerungsdateien|Count|Durchschnitt|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Freier virtueller Arbeitsspeicher|Count|Durchschnitt|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Prozesse|Count|Durchschnitt|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Genutzter Speicherplatz in Auslagerungsdateien|Count|Durchschnitt|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Betriebszeit|Count|Durchschnitt|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Benutzer|Count|Durchschnitt|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/gelesen|Durchschn. Datenträger s/gelesen|Count|Durchschnitt|Average_Avg. Datenträger s/gelesen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/geschrieben|Durchschn. Datenträger s/geschrieben|Count|Durchschnitt|Average_Avg. Datenträger s/geschrieben|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Aktuelle Warteschlangenlänge|Count|Durchschnitt|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Lesevorgänge/s|Count|Durchschnitt|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Übertragungen/s|Count|Durchschnitt|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Schreibvorgänge/s|Count|Durchschnitt|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Freie Megabytes|Count|Durchschnitt|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% freier Speicher|Count|Durchschnitt|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Verfügbare MB|Count|Durchschnitt|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Zugesicherte verwendete Bytes (%)|Count|Durchschnitt|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Empfangene Byte/Sek.|Count|Durchschnitt|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Gesendete Byte/Sek.|Count|Durchschnitt|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Byte gesamt/Sek.|Count|Durchschnitt|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Prozessorzeit|Count|Durchschnitt|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Länge der Prozessorwarteschlange|Count|Durchschnitt|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Count|Gesamt|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aktualisieren|Aktualisieren|Count|Durchschnitt|Aktualisieren|Computer, Product, Classification, UpdateState, Optional, Approved|
|Ereignis|Ereignis|Count|Durchschnitt|Ereignis|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|QueryDuration|Abfragedauer|Millisekunden|Durchschnitt|DAX-Abfragedauer im letzten Intervall|Keine Dimensionen|
|QueryPoolJobQueueLength|Threads: Auftragswarteschlangenlänge für Abfragepool|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools.|Keine Dimensionen|
|qpu_high_utilization_metric|Hohe QPU-Auslastung|Count|Gesamt|Hohe QPU-Auslastung in der letzten Minute; 1 bei hoher QPU-Auslastung, andernfalls 0|Keine Dimensionen|
|memory_metric|Arbeitsspeicher|Byte|Durchschnitt|Arbeitsspeicher. Bereich von 0–3 GB für A1, 0–5 GB für A2, 0–10 GB für A3, 0–25 GB für A4, 0–50 GB für A5 und 0–100 GB für A6|Keine Dimensionen|
|memory_thrashing_metric|Arbeitsspeicherüberlastung|Prozent|Durchschnitt|Durchschnittliche Arbeitsspeicherüberlastung.|Keine Dimensionen|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Gesamt|Erfolgreiche ListenerConnections für Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Gesamt|ClientError bei ListenerConnections für Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Gesamt|ServerError bei ListenerConnections für Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Gesamt|Erfolgreiche SenderConnections für Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Gesamt|ClientError bei SenderConnections für Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Gesamt|ServerError bei SenderConnections für Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Gesamt|Gesamtzahl der ListenerConnections für Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Gesamt|Gesamtzahl der SenderConnections-Anforderungen für Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Gesamt|Gesamtzahl der ActiveConnections für Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Gesamt|Gesamtzahl der ActiveListeners für Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Gesamt|Gesamtzahl der BytesTransferred für Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Gesamt|Gesamtzahl der ListenerDisconnects für Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Gesamt|Gesamtzahl der SenderDisconnects für Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|SearchLatency|Suchlatenz|Sekunden|Durchschnitt|Durchschnittliche Suchlatenz für den Suchdienst|Keine Dimensionen|
|SearchQueriesPerSecond|Suchabfragen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Suchabfragen pro Sekunde für den Suchdienst|Keine Dimensionen|
|ThrottledSearchQueriesPercentage|Gedrosselte Suchabfragen in Prozent|Prozent|Durchschnitt|Prozentsatz der Suchabfragen, die für den Suchdienst gedrosselt wurden|Keine Dimensionen|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|SuccessfulRequests|Erfolgreiche Anforderungen (Vorschau)|Count|Gesamt|Gesamtzahl der erfolgreichen Anforderungen für einen Namespace (Vorschau)|EntityName|
|ServerErrors|Serverfehler. (Vorschau)|Count|Gesamt|Serverfehler für Microsoft.ServiceBus. (Vorschau)|EntityName|
|UserErrors|Benutzerfehler. (Vorschau)|Count|Gesamt|Benutzerfehler für Microsoft.ServiceBus. (Vorschau)|EntityName|
|ThrottledRequests|Gedrosselte Anforderungen. (Vorschau)|Count|Gesamt|Gedrosselte Anforderungen für Microsoft.ServiceBus. (Vorschau)|EntityName|
|IncomingRequests|Eingehende Anforderungen (Vorschau)|Count|Gesamt|Eingehende Anforderungen für Microsoft.ServiceBus. (Vorschau)|EntityName|
|IncomingMessages|Eingehende Nachrichten (Vorschau)|Count|Gesamt|Eingehende Nachrichten für Microsoft.ServiceBus. (Vorschau)|EntityName|
|OutgoingMessages|Ausgehende Nachrichten (Vorschau)|Count|Gesamt|Ausgehende Nachrichten für Microsoft.ServiceBus. (Vorschau)|EntityName|
|ActiveConnections|Aktive Verbindungen (Vorschau)|Count|Gesamt|Aktive Verbindungen gesamt für Microsoft.ServiceBus. (Vorschau)|Keine Dimensionen|
|Größe|Größe (Vorschau)|Byte|Durchschnitt|Größe einer Warteschlange/eines Themas in Bytes (Vorschau)|EntityName|
|Meldungen|Anzahl von Nachrichten in einer Warteschlange/einem Thema (Vorschau)|Count|Durchschnitt|Anzahl von Nachrichten in einer Warteschlange/einem Thema (Vorschau)|EntityName|
|ActiveMessages|Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema (Vorschau)|Count|Durchschnitt|Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema (Vorschau)|EntityName|
|DeadletteredMessages|Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema (Vorschau)|Count|Durchschnitt|Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema (Vorschau)|EntityName|
|ScheduledMessages|Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema (Vorschau)|Count|Durchschnitt|Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema (Vorschau)|EntityName|
|CPUXNS|CPU-Auslastung pro Namespace|Prozent|Maximum|CPU-Auslastungsmetrik für Service Bus-Premium-Namespace|Keine Dimensionen|
|WSXNS|Auslastung der Arbeitsspeichergröße pro Namespace|Prozent|Maximum|Speicherauslastungsmetrik für Service Bus-Premium-Namespace|Keine Dimensionen|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Durchschnitt|Diesem Container zugeordnete CPU in Millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Durchschnitt|Diesem Container zugeordneter Arbeitsspeicher in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Durchschnitt|Tatsächliche CPU-Auslastung in Millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Durchschnitt|Tatsächliche Arbeitsspeichernutzung in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Prozent|Durchschnitt|Auslastung der CPU für diesen Container als Prozentsatz von AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Prozent|Durchschnitt|Auslastung der CPU für diesen Container als Prozentsatz von AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Durchschnitt|Status der Service Fabric Mesh-Anwendung|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Count|Durchschnitt|Integritätsstatus eines Diensts in Service Fabric Mesh-Anwendung|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Durchschnitt|Integritätsstatus eines Dienstreplikats in Service Fabric Mesh-Anwendung|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Durchschnitt|Status des Containers in der Service Fabric Mesh-Anwendung|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Durchschnitt|Anzahl Neustarts eines Containers in Service Fabric Mesh-Anwendung|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ConnectionCount|Anzahl der Verbindungen|Count|Maximum|Die Anzahl der Benutzerverbindungen.|Endpunkt|
|MessageCount|Nachrichtenanzahl|Count|Gesamt|Die Gesamtmenge der Nachrichten.|Keine Dimensionen|
|InboundTraffic|Eingehender Datenverkehr|Byte|Gesamt|Der eingehende Datenverkehr des Diensts|Keine Dimensionen|
|OutboundTraffic|Ausgehender Datenverkehr|Byte|Gesamt|Der ausgehende Datenverkehr des Diensts|Keine Dimensionen|
|UserErrors|Benutzerfehler|Prozent|Maximum|Der Prozentsatz der Benutzerfehler|Keine Dimensionen|
|SystemErrors|Systemfehler|Prozent|Maximum|Der Prozentsatz der Systemfehler|Keine Dimensionen|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|cpu_percent|CPU-Prozentsatz|Prozent|Durchschnitt|CPU-Prozentsatz|Keine Dimensionen|
|physical_data_read_percent|E/A-Prozentsatz für Daten|Prozent|Durchschnitt|E/A-Prozentsatz für Daten|Keine Dimensionen|
|log_write_percent|E/A-Prozentsatz für Protokoll|Prozent|Durchschnitt|E/A-Prozentsatz für Protokoll|Keine Dimensionen|
|dtu_consumption_percent|DTU-Prozentsatz|Prozent|Durchschnitt|DTU-Prozentsatz|Keine Dimensionen|
|storage|Genutzter Datenspeicherplatz|Byte|Maximum|Datenbankgröße gesamt|Keine Dimensionen|
|connection_successful|Erfolgreiche Verbindungen|Count|Gesamt|Erfolgreiche Verbindungen|Keine Dimensionen|
|connection_failed|Verbindungsfehler|Count|Gesamt|Verbindungsfehler|Keine Dimensionen|
|blocked_by_firewall|Von der Firewall blockiert|Count|Gesamt|Von der Firewall blockiert|Keine Dimensionen|
|deadlock|Deadlocks|Count|Gesamt|Deadlocks|Keine Dimensionen|
|storage_percent|Genutzter Datenspeicherplatz in Prozent|Prozent|Maximum|Datenbankgröße als Prozentsatz|Keine Dimensionen|
|xtp_storage_percent|In-Memory-OLTP-Speicher in Prozent|Prozent|Durchschnitt|In-Memory-OLTP-Speicher in Prozent|Keine Dimensionen|
|workers_percent|Worker in Prozent|Prozent|Durchschnitt|Worker in Prozent|Keine Dimensionen|
|sessions_percent|Sitzungen in Prozent|Prozent|Durchschnitt|Sitzungen in Prozent|Keine Dimensionen|
|dtu_limit|DTU-Grenzwert|Count|Durchschnitt|DTU-Grenzwert|Keine Dimensionen|
|dtu_used|DTU-Verbrauch|Count|Durchschnitt|DTU-Verbrauch|Keine Dimensionen|
|cpu_limit|CPU-Grenzwert|Count|Durchschnitt|CPU-Grenzwert|Keine Dimensionen|
|cpu_used|Verwendete CPU|Count|Durchschnitt|Verwendete CPU|Keine Dimensionen|
|dwu_limit|DWU-Grenzwert|Count|Maximum|DWU-Grenzwert|Keine Dimensionen|
|dwu_consumption_percent|DWU in Prozent|Prozent|Maximum|DWU in Prozent|Keine Dimensionen|
|dwu_used|DWU-Verbrauch|Count|Maximum|DWU-Verbrauch|Keine Dimensionen|
|dw_cpu_percent|DW-Knotenebene, CPU-Prozentsatz|Prozent|Durchschnitt|DW-Knotenebene, CPU-Prozentsatz|DwLogicalNodeId|
|dw_physical_data_read_percent|DW-Knotenebene, E/A-Prozentsatz für Daten|Prozent|Durchschnitt|DW-Knotenebene, E/A-Prozentsatz für Daten|DwLogicalNodeId|
    |cache_hit_percent|Prozentsatz der Cachetreffer|Prozent|Maximum|Prozentsatz der Cachetreffer|Keine Dimensionen|
|cache_used_percent|Cacheverwendung in Prozent|Prozent|Maximum|Cacheverwendung in Prozent|Keine Dimensionen|
|local_tempdb_usage_percent|Lokaler tempdb-Prozentsatz|Prozent|Durchschnitt|Lokaler tempdb-Prozentsatz|Keine Dimensionen|
|app_cpu_billed|Abgerechnete App-CPU|Count|Gesamt|Abgerechnete App-CPU|Keine Dimensionen|
|app_cpu_percent|App-CPU-Prozentsatz|Prozent|Durchschnitt|App-CPU-Prozentsatz|Keine Dimensionen|
|app_memory_percent|Verwendeter App-Arbeitsspeicher in Prozent|Prozent|Durchschnitt|Verwendeter App-Arbeitsspeicher in Prozent|Keine Dimensionen|
|allocated_data_storage|Zugeordneter Datenspeicherplatz|Byte|Durchschnitt|Zugeordneter Datenspeicherplatz|Keine Dimensionen|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|cpu_percent|CPU-Prozentsatz|Prozent|Durchschnitt|CPU-Prozentsatz|Keine Dimensionen|
|physical_data_read_percent|E/A-Prozentsatz für Daten|Prozent|Durchschnitt|E/A-Prozentsatz für Daten|Keine Dimensionen|
|log_write_percent|E/A-Prozentsatz für Protokoll|Prozent|Durchschnitt|E/A-Prozentsatz für Protokoll|Keine Dimensionen|
|dtu_consumption_percent|DTU-Prozentsatz|Prozent|Durchschnitt|DTU-Prozentsatz|Keine Dimensionen|
|storage_percent|Genutzter Datenspeicherplatz in Prozent||Prozent|Durchschnitt|Speicher in Prozent|Keine Dimensionen|
|workers_percent|Worker in Prozent|Prozent|Durchschnitt|Worker in Prozent|Keine Dimensionen|
|sessions_percent|Sitzungen in Prozent|Prozent|Durchschnitt|Sitzungen in Prozent|Keine Dimensionen|
|eDTU_limit|eDTU-Grenzwert|Count|Durchschnitt|eDTU-Grenzwert|Keine Dimensionen|
|storage_limit|Maximale Datengröße|Byte|Durchschnitt|Speicherbegrenzung|Keine Dimensionen|
|eDTU_used|eDTU-Verbrauch|Count|Durchschnitt|eDTU-Verbrauch|Keine Dimensionen|
|storage_used|Genutzter Datenspeicherplatz|Byte|Durchschnitt|Verwendeter Speicher|Keine Dimensionen|
|xtp_storage_percent|In-Memory-OLTP-Speicher in Prozent|Prozent|Durchschnitt|In-Memory-OLTP-Speicher in Prozent|Keine Dimensionen|
|cpu_limit|CPU-Grenzwert|Count|Durchschnitt|CPU-Grenzwert|Keine Dimensionen|
|cpu_used|Verwendete CPU|Count|Durchschnitt|Verwendete CPU|Keine Dimensionen|
|allocated_data_storage|Zugeordneter Datenspeicherplatz|Byte|Durchschnitt|Zugeordneter Datenspeicherplatz|Keine Dimensionen|
|allocated_data_storage_percent|Zugeordneter Datenspeicherplatz in Prozent|Prozent|Maximum|Zugeordneter Datenspeicherplatz in Prozent|Keine Dimensionen|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|virtual_core_count|Anzahl virtueller Kerne|Count|Durchschnitt|Anzahl virtueller Kerne|Keine Dimensionen|
|avg_cpu_percent|Durchschnittlicher CPU-Prozentsatz|Prozent|Durchschnitt|Durchschnittlicher CPU-Prozentsatz|Keine Dimensionen|
|reserved_storage_mb|Reservierter Speicherplatz|Count|Durchschnitt|Reservierter Speicherplatz|Keine Dimensionen|
|storage_space_used_mb|Belegter Speicherplatz|Count|Durchschnitt|Belegter Speicherplatz|Keine Dimensionen|
|io_requests|Anzahl von E/A-Anforderungen|Count|Durchschnitt|Anzahl von E/A-Anforderungen|Keine Dimensionen|
|io_bytes_read|Gelesene E/A-Bytes|Byte|Durchschnitt|Gelesene E/A-Bytes|Keine Dimensionen|
|io_bytes_written|Geschriebene E/A-Bytes|Byte|Durchschnitt|Geschriebene E/A-Bytes|Keine Dimensionen|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|UsedCapacity|Verwendete Kapazität|Byte|Durchschnitt|Vom Konto verwendete Kapazität|Keine Dimensionen|
|Transaktionen|Transaktionen|Count|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|Eingehende Daten|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Diese Anzahl umfasst die ausgehenden Daten von einem externen Client in Azure Storage sowie die ausgehenden Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Serverlatenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|E2E-Latenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|Verfügbarkeit|Verfügbarkeit|Prozent|Durchschnitt|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|BlobCapacity|Blob-Kapazität|Byte|Durchschnitt|Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte.|BlobType, Tier|
|BlobCount|Anzahl von Blobs|Count|Gesamt|Die Anzahl von Blobs im Blob-Dienst des Speicherkontos.|BlobType|       |BlobCount|Anzahl von Blobs|Count|Durchschnitt|Die Anzahl von Blobs im Blob-Dienst des Speicherkontos.|BlobType, Tier|
|ContainerCount|Anzahl von Blob-Containern|Count|Durchschnitt|Die Anzahl von Containern im Blob-Dienst des Speicherkontos.|Keine Dimensionen|
|IndexCapacity|Indexkapazität|Byte|Durchschnitt|Die vom ADLS Gen2-Index (hierarchisch) verwendete Speichermenge in Byte.|Keine Dimensionen|
|Transaktionen|Transaktionen|Count|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|Eingehende Daten|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Diese Anzahl umfasst die ausgehenden Daten von einem externen Client in Azure Storage sowie die ausgehenden Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Serverlatenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|E2E-Latenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|Verfügbarkeit|Verfügbarkeit|Prozent|Durchschnitt|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|FileCapacity|Dateikapazität|Byte|Durchschnitt|Die Größe des vom Dateidienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|FileCount|Dateianzahl|Count|Durchschnitt|Die Anzahl von Dateien im Dateidienst des Speicherkontos|Keine Dimensionen|
|FileShareCount|Anzahl von Dateifreigaben|Count|Durchschnitt|Die Anzahl von Dateifreigaben im Dateidienst des Speicherkontos|Keine Dimensionen|
|Transaktionen|Transaktionen|Count|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|Eingehende Daten|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Diese Anzahl umfasst die ausgehenden Daten von einem externen Client in Azure Storage sowie die ausgehenden Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Serverlatenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|E2E-Latenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|Verfügbarkeit|Verfügbarkeit|Prozent|Durchschnitt|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|QueueCapacity|Warteschlangenkapazität|Byte|Durchschnitt|Die Größe des vom Warteschlangendienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|QueueCount|Anzahl von Warteschlangen|Count|Durchschnitt|Die Anzahl von Warteschlangen im Warteschlangendienst des Speicherkontos|Keine Dimensionen|
|QueueMessageCount|Anzahl von Warteschlangennachrichten|Count|Durchschnitt|Die ungefähre Anzahl von Warteschlangennachrichten im Warteschlangendienst des Speicherkontos|Keine Dimensionen|
|Transaktionen|Transaktionen|Count|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|Eingehende Daten|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Diese Anzahl umfasst die ausgehenden Daten von einem externen Client in Azure Storage sowie die ausgehenden Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Serverlatenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|E2E-Latenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|Verfügbarkeit|Verfügbarkeit|Prozent|Durchschnitt|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|TableCapacity|Tabellenkapazität|Byte|Durchschnitt|Die Größe des vom Tabellendienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|TableCount|Anzahl von Tabellen|Count|Durchschnitt|Die Anzahl von Tabellen im Tabellendienst des Speicherkontos|Keine Dimensionen|
|TableEntityCount|Anzahl von Tabellenentitäten|Count|Durchschnitt|Die Anzahl von Tabellenentitäten im Tabellendienst des Speicherkontos|Keine Dimensionen|
|Transaktionen|Transaktionen|Count|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|Eingehende Daten|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Diese Anzahl umfasst die ausgehenden Daten von einem externen Client in Azure Storage sowie die ausgehenden Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Serverlatenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche Latenz in Millisekunden, die von Azure Storage verwendet wurde, um eine erfolgreiche Anforderung zu verarbeiten. Dieser Wert enthält nicht die in „AverageE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|E2E-Latenz (erfolgreich)|Millisekunden|Durchschnitt|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|Verfügbarkeit|Verfügbarkeit|Prozent|Durchschnitt|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Ergebnis der Synchronisierungssitzung|Count|Durchschnitt|Metrik, die jedes Mal dann einen Wert von 1 protokolliert, wenn der Serverendpunkt eine Synchronisierungssitzung mit dem Cloudendpunkt erfolgreich abgeschlossen hat.|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronisierte Dateien|Count|Gesamt|Anzahl synchronisierter Dateien|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Dateien ohne Synchronisierung|Count|Gesamt|Anzahl von Dateien mit fehlerhafter Synchronisierung|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synchronisiert|Byte|Gesamt|Für Synchronisierungssitzungen übertragene Gesamtdateigröße|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Onlinestatus des Servers|Count|Maximum|Metrik, die jedes Mal dann einen Wert von 1 protokolliert, wenn der registrierte Server erfolgreich einen Heartbeat mit dem Cloudendpunkt erfasst.|Servername|
|StorageSyncRecallIOTotalSizeBytes|Cloudtieringrückruf|Byte|Gesamt|Gesamtgröße der vom Server zurückgerufenen Daten|Servername|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|ResourceUtilization|Speichereinheitnutzung in %|Prozent|Maximum|Speichereinheitnutzung in %|LogicalName, PartitionId|
|InputEvents|Eingabeereignisse|Count|Gesamt|Eingabeereignisse|LogicalName, PartitionId|
|InputEventBytes|Eingabeereignisbytes|Byte|Gesamt|Eingabeereignisbytes|LogicalName, PartitionId|
|LateInputEvents|Ereignisse bei verspäteter Eingabe|Count|Gesamt|Ereignisse bei verspäteter Eingabe|LogicalName, PartitionId|
|OutputEvents|Ausgabeereignisse|Count|Gesamt|Ausgabeereignisse|LogicalName, PartitionId|
|ConversionErrors|Konvertierungsfehler|Count|Gesamt|Konvertierungsfehler|LogicalName, PartitionId|
|Errors|Laufzeitfehler|Count|Gesamt|Laufzeitfehler|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Ereignisse für falsche Reihenfolge|Count|Gesamt|Ereignisse für falsche Reihenfolge|LogicalName, PartitionId|
|AMLCalloutRequests|Funktionsanforderungen|Count|Gesamt|Funktionsanforderungen|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Fehlerhafte Funktionsanforderungen|Count|Gesamt|Fehlerhafte Funktionsanforderungen|LogicalName, PartitionId|
|AMLCalloutInputEvents|Funktionsereignisse|Count|Gesamt|Funktionsereignisse|LogicalName, PartitionId|
|DeserializationError|Eingabefehler bei Deserialisierung|Count|Gesamt|Eingabefehler bei Deserialisierung|LogicalName, PartitionId|
|EarlyInputEvents|Frühe Eingabeereignisse|Count|Gesamt|Frühe Eingabeereignisse|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Wasserzeichenverzögerung|Sekunden|Maximum|Wasserzeichenverzögerung|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eingabeereignisse im Rückstand|Count|Maximum|Eingabeereignisse im Rückstand|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Empfangene Eingabequellen|Count|Gesamt|Empfangene Eingabequellen|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|IngressReceivedMessages|Eingang empfangene Nachrichten|Count|Gesamt|Die Anzahl der aus allen Event Hub- oder IoT Hub-Ereignisquellen gelesenen Nachrichten|Keine Dimensionen|
|IngressReceivedInvalidMessages|Eingang empfangene ungültige Nachrichten|Count|Gesamt|Die Anzahl der aus allen Event Hub- oder IoT Hub-Ereignisquellen gelesenen ungültigen Nachrichten|Keine Dimensionen|
|IngressReceivedBytes|Eingang empfangene Bytes|Byte|Gesamt|Die Anzahl der aus sämtlichen Ereignisquellen gelesenen Bytes|Keine Dimensionen|
|IngressStoredBytes|Eingang gespeicherte Bytes|Byte|Gesamt|Die Gesamtgröße der erfolgreich verarbeiteten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressStoredEvents|Gespeicherte Ingress-Ereignisse|Count|Gesamt|Die Gesamtgröße der vereinfachten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressReceivedMessagesTimeLag|Ingress-Zeitabstand empfangener Nachrichten|Sekunden|Maximum|Der Unterschied zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung in Ingress|Keine Dimensionen|
|IngressReceivedMessagesCountLag|Ingress-Anzahlrückstand empfangener Nachrichten|Count|Durchschnitt|Der Unterschied zwischen der Sequenznummer der Nachricht, die in der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der in Ingress verarbeiteten Nachricht|Keine Dimensionen|
|WarmStorageMaxProperties|Maximale Eigenschaften von Warm Storage|Count|Maximum|Maximale Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU zugelassen werden, und maximale Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU zugelassen werden|Keine Dimensionen|
|WarmStorageUsedProperties|Verwendete Eigenschaften von Warm Storage |Count|Maximum|Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU verwendet werden, und Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU verwendet werden|Keine Dimensionen|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|IngressReceivedMessages|Eingang empfangene Nachrichten|Count|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen Nachrichten|Keine Dimensionen|
|IngressReceivedInvalidMessages|Eingang empfangene ungültige Nachrichten|Count|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen ungültigen Nachrichten|Keine Dimensionen|
|IngressReceivedBytes|Eingang empfangene Bytes|Byte|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen Bytes|Keine Dimensionen|
|IngressStoredBytes|Eingang gespeicherte Bytes|Byte|Gesamt|Die Gesamtgröße der erfolgreich verarbeiteten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressStoredEvents|Gespeicherte Ingress-Ereignisse|Count|Gesamt|Die Gesamtgröße der vereinfachten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressReceivedMessagesTimeLag|Ingress-Zeitabstand empfangener Nachrichten|Sekunden|Maximum|Der Unterschied zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung in Ingress|Keine Dimensionen|
|IngressReceivedMessagesCountLag|Ingress-Anzahlrückstand empfangener Nachrichten|Count|Durchschnitt|Der Unterschied zwischen der Sequenznummer der Nachricht, die in der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der in Ingress verarbeiteten Nachricht|Keine Dimensionen|
|WarmStorageMaxProperties|Maximale Eigenschaften von Warm Storage|Count|Maximum|Maximale Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU zugelassen werden, und maximale Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU zugelassen werden|Keine Dimensionen|
|WarmStorageUsedProperties|Verwendete Eigenschaften von Warm Storage |Count|Maximum|Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU verwendet werden, und Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU verwendet werden|Keine Dimensionen|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Bytes pro Sekunde|Durchschnitt|Durchschnittlicher Datenträgerdurchsatz aufgrund von Lesevorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Bytes pro Sekunde|Durchschnitt|Durchschnittlicher Datenträgerdurchsatz aufgrund von Schreibvorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|Datenträgerlesevorgänge in Bytes|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Gesamter Datenträgerdurchsatz aufgrund von Lesevorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Gesamter Datenträgerdurchsatz aufgrund von Schreibvorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|DiskReadOperations|Datenträgerlesevorgänge|Count|Gesamt|Die Anzahl von E/A-Lesevorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|DiskWriteOperations|Datenträgerschreibvorgänge|Count|Gesamt|Die Anzahl von E/A-Schreibvorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Die durchschnittliche Anzahl von E/A-Lesevorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Durchschnitt|Die durchschnittliche Anzahl von E/A-Schreibvorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|DiskReadLatency|Wartezeit beim Datenträgerlesevorgang|Millisekunden|Durchschnitt|Gesamtwartezeit beim Lesevorgang. Die Summe der Wartezeiten bei Lesevorgängen auf Gerät und Kernel.|Keine Dimensionen|
|DiskWriteLatency|Wartezeit beim Datenträgerschreibvorgang|Millisekunden|Durchschnitt|Gesamtwartezeit beim Schreibvorgang. Die Summe der Wartezeiten bei Schreibvorgängen auf Gerät und Kernel.|Keine Dimensionen|
|NetworkInBytesPerSecond|Eingehender Netzwerkverkehr in Bytes/Sek.|Bytes pro Sekunde|Durchschnitt|Durchschnittliche Netzwerkdurchsatz für empfangenen Datenverkehr.|Keine Dimensionen|
|NetworkOutBytesPerSecond|Ausgehender Netzwerkverkehr in Bytes/Sek.|Bytes pro Sekunde|Durchschnitt|Durchschnittliche Netzwerkdurchsatz für gesendeten Datenverkehr.|Keine Dimensionen|
|Netzwerk eingehend|Netzwerk eingehend|Byte|Gesamt|Gesamter Netzwerkdurchsatz für empfangenen Datenverkehr.|Keine Dimensionen|
|Netzwerk ausgehend|Netzwerk ausgehend|Byte|Gesamt|Gesamter Netzwerkdurchsatz für gesendeten Datenverkehr.|Keine Dimensionen|
|MemoryUsed|Verwendeter Arbeitsspeicher|Byte|Durchschnitt|Die Menge an Computerarbeitsspeicher, die vom virtuellen Computer verwendet wird.|Keine Dimensionen|
|MemoryGranted|Zugewiesener Arbeitsspeicher|Byte|Durchschnitt|Die Menge an Arbeitsspeicher, die dem virtuellen Computer vom Host zugewiesen wurde. Arbeitsspeicher wird dem Host erst bei der ersten Verwendung zugewiesen, und zugewiesener Speicher kann ausgelagert oder ausgeweitet werden, wenn der VMkernel den Speicher benötigt.|Keine Dimensionen|
|MemoryActive|Aktiver Arbeitsspeicher|Byte|Durchschnitt|Die Menge an Arbeitsspeicher, die vom virtuellen Computer im letzten kleinen Zeitfenster verwendet wurde. Dies ist die „wirkliche“ Anzahl des derzeit vom virtuellen Computer benötigten Arbeitsspeichers. Zusätzlicher, ungenutzter Arbeitsspeicher kann ausgelagert oder ausgeweitet werden, ohne dass dies Auswirkungen auf die Leistung für den Gast hat.|Keine Dimensionen|
|CPU in Prozent|CPU in Prozent|Prozent|Durchschnitt|Die CPU-Auslastung. Dieser Wert wird mit 100 % gemeldet und stellt alle Prozessorkerne im System dar. Beispielsweise verwendet ein virtueller 2-Wege-Computer, der 50 % eines Systems mit vier Kernen nutzt, zwei dieser Kerne vollständig.|Keine Dimensionen|
|PercentageCpuReady|Prozentsatz der CPU-Bereitschaft|Millisekunden|Gesamt|Die Bereitschaftszeit ist die Zeit, über die im letzten Updateintervall auf die Verfügbarkeit von CPUs gewartet wurde.|Keine Dimensionen|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CpuPercentage|CPU-Prozentsatz|Prozent|Durchschnitt|CPU-Prozentsatz|Instanz|
|MemoryPercentage|Arbeitsspeicherprozentsatz|Prozent|Durchschnitt|Arbeitsspeicherprozentsatz|Instanz|
|DiskQueueLength|Warteschlangenlänge des Datenträgers|Count|Durchschnitt|Warteschlangenlänge des Datenträgers|Instanz|
|HttpQueueLength|Länge der HTTP-Warteschlange|Count|Durchschnitt|Länge der HTTP-Warteschlange|Instanz|
|BytesReceived|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (Funktionen ausgenommen)

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CpuTime|CPU-Zeit|Sekunden|Gesamt|CPU-Zeit|Instanz|
|Requests|Requests|Count|Gesamt|Requests|Instanz|
|BytesReceived|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|Http101|HTTP 101|Count|Gesamt|HTTP 101|Instanz|
|Http2xx|HTTP 2xx|Count|Gesamt|HTTP 2xx|Instanz|
|Http3xx|HTTP 3xx|Count|Gesamt|HTTP 3xx|Instanz|
|Http401|HTTP 401|Count|Gesamt|HTTP 401|Instanz|
|Http403|HTTP 403|Count|Gesamt|HTTP 403|Instanz|
|Http404|HTTP 404|Count|Gesamt|HTTP 404|Instanz|
|Http406|HTTP 406|Count|Gesamt|HTTP 406|Instanz|
|Http4xx|HTTP 4xx|Count|Gesamt|HTTP 4xx|Instanz|
|Http5xx|HTTP-Serverfehler|Count|Gesamt|HTTP-Serverfehler|Instanz|
|MemoryWorkingSet|Arbeitssatz für Arbeitsspeicher|Byte|Durchschnitt|Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageMemoryWorkingSet|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Durchschnitt|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageResponseTime|Durchschnittliche Antwortzeit|Sekunden|Durchschnitt|Durchschnittliche Antwortzeit|Instanz|
|AppConnections|Verbindungen|Count|Durchschnitt|Verbindungen|Instanz|
|Handles|Anzahl von Handles|Count|Durchschnitt|Anzahl von Handles|Instanz|
|Threads|Threadanzahl|Count|Durchschnitt|Threadanzahl|Instanz|
|PrivateBytes|Private Bytes|Byte|Durchschnitt|Private Bytes|Instanz|
|IoReadBytesPerSecond|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Gelesene Bytes pro Sekunde|Instanz|
|IoWriteBytesPerSecond|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Geschriebene Bytes pro Sekunde|Instanz|
|IoOtherBytesPerSecond|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Bytes pro Sekunde|Instanz|
|IoReadOperationsPerSecond|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Lesevorgänge pro Sekunde|Instanz|
|IoWriteOperationsPerSecond|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Schreibvorgänge pro Sekunde|Instanz|
|IoOtherOperationsPerSecond|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Vorgänge pro Sekunde|Instanz|
|RequestsInApplicationQueue|Anforderungen in der Anwendungswarteschlange|Count|Durchschnitt|Anforderungen in der Anwendungswarteschlange|Instanz|
|CurrentAssemblies|Aktuelle Assemblys|Count|Durchschnitt|Aktuelle Assemblys|Instanz|
|TotalAppDomains|App-Domänen insgesamt|Count|Durchschnitt|App-Domänen insgesamt|Instanz|
|TotalAppDomainsUnloaded|Entladene App-Domänen insgesamt|Count|Durchschnitt|Entladene App-Domänen insgesamt|Instanz|
|Gen0Collections|Garbage Collections der Generation 0|Count|Gesamt|Garbage Collections der Generation 0|Instanz|
|Gen1Collections|Garbage Collections der Generation 1|Count|Gesamt|Garbage Collections der Generation 1|Instanz|
|Gen2Collections|Garbage Collections der Generation 2|Count|Gesamt|Garbage Collections der Generation 2|Instanz|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Funktionen)

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|BytesReceived|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|Http5xx|HTTP-Serverfehler|Count|Gesamt|HTTP-Serverfehler|Instanz|
|MemoryWorkingSet|Arbeitssatz für Arbeitsspeicher|Byte|Durchschnitt|Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageMemoryWorkingSet|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Durchschnitt|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Instanz|
|FunctionExecutionUnits|Ausführungseinheiten für Funktion|MB/Millisekunden|Gesamt|[Ausführungseinheiten für Funktion](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instanz|
|FunctionExecutionCount|Ausführungsanzahl für Funktion|Count|Gesamt|Ausführungsanzahl für Funktion|Instanz|
|PrivateBytes|Private Bytes|Byte|Durchschnitt|Private Bytes|Instanz|
|IoReadBytesPerSecond|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Gelesene Bytes pro Sekunde|Instanz|
|IoWriteBytesPerSecond|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Geschriebene Bytes pro Sekunde|Instanz|
|IoOtherBytesPerSecond|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Bytes pro Sekunde|Instanz|
|IoReadOperationsPerSecond|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Lesevorgänge pro Sekunde|Instanz|
|IoWriteOperationsPerSecond|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Schreibvorgänge pro Sekunde|Instanz|
|IoOtherOperationsPerSecond|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Vorgänge pro Sekunde|Instanz|
|RequestsInApplicationQueue|Anforderungen in der Anwendungswarteschlange|Count|Durchschnitt|Anforderungen in der Anwendungswarteschlange|Instanz|
|CurrentAssemblies|Aktuelle Assemblys|Count|Durchschnitt|Aktuelle Assemblys|Instanz|
|TotalAppDomains|App-Domänen insgesamt|Count|Durchschnitt|App-Domänen insgesamt|Instanz|
|TotalAppDomainsUnloaded|Entladene App-Domänen insgesamt|Count|Durchschnitt|Entladene App-Domänen insgesamt|Instanz|
|Gen0Collections|Garbage Collections der Generation 0|Count|Gesamt|Garbage Collections der Generation 0|Instanz|
|Gen1Collections|Garbage Collections der Generation 1|Count|Gesamt|Garbage Collections der Generation 1|Instanz|
|Gen2Collections|Garbage Collections der Generation 2|Count|Gesamt|Garbage Collections der Generation 2|Instanz|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|CpuTime|CPU-Zeit|Sekunden|Gesamt|CPU-Zeit|Instanz|
|Requests|Requests|Count|Gesamt|Requests|Instanz|
|BytesReceived|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|Http101|HTTP 101|Count|Gesamt|HTTP 101|Instanz|
|Http2xx|HTTP 2xx|Count|Gesamt|HTTP 2xx|Instanz|
|Http3xx|HTTP 3xx|Count|Gesamt|HTTP 3xx|Instanz|
|Http401|HTTP 401|Count|Gesamt|HTTP 401|Instanz|
|Http403|HTTP 403|Count|Gesamt|HTTP 403|Instanz|
|Http404|HTTP 404|Count|Gesamt|HTTP 404|Instanz|
|Http406|HTTP 406|Count|Gesamt|HTTP 406|Instanz|
|Http4xx|HTTP 4xx|Count|Gesamt|HTTP 4xx|Instanz|
|Http5xx|HTTP-Serverfehler|Count|Gesamt|HTTP-Serverfehler|Instanz|
|MemoryWorkingSet|Arbeitssatz für Arbeitsspeicher|Byte|Durchschnitt|Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageMemoryWorkingSet|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Durchschnitt|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageResponseTime|Durchschnittliche Antwortzeit|Sekunden|Durchschnitt|Durchschnittliche Antwortzeit|Instanz|
|FunctionExecutionUnits|Ausführungseinheiten für Funktion|Count|Gesamt|Ausführungseinheiten für Funktion|Instanz|
|FunctionExecutionCount|Ausführungsanzahl für Funktion|Count|Gesamt|Ausführungsanzahl für Funktion|Instanz|
|AppConnections|Verbindungen|Count|Durchschnitt|Verbindungen|Instanz|
|Handles|Anzahl von Handles|Count|Durchschnitt|Anzahl von Handles|Instanz|
|Threads|Threadanzahl|Count|Durchschnitt|Threadanzahl|Instanz|
|PrivateBytes|Private Bytes|Byte|Durchschnitt|Private Bytes|Instanz|
|IoReadBytesPerSecond|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Gelesene Bytes pro Sekunde|Instanz|
|IoWriteBytesPerSecond|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Geschriebene Bytes pro Sekunde|Instanz|
|IoOtherBytesPerSecond|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Bytes pro Sekunde|Instanz|
|IoReadOperationsPerSecond|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Lesevorgänge pro Sekunde|Instanz|
|IoWriteOperationsPerSecond|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Schreibvorgänge pro Sekunde|Instanz|
|IoOtherOperationsPerSecond|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Vorgänge pro Sekunde|Instanz|
|RequestsInApplicationQueue|Anforderungen in der Anwendungswarteschlange|Count|Durchschnitt|Anforderungen in der Anwendungswarteschlange|Instanz|
|CurrentAssemblies|Aktuelle Assemblys|Count|Durchschnitt|Aktuelle Assemblys|Instanz|
|TotalAppDomains|App-Domänen insgesamt|Count|Durchschnitt|App-Domänen insgesamt|Instanz|
|TotalAppDomainsUnloaded|Entladene App-Domänen insgesamt|Count|Durchschnitt|Entladene App-Domänen insgesamt|Instanz|
|Gen0Collections|Garbage Collections der Generation 0|Count|Gesamt|Garbage Collections der Generation 0|Instanz|
|Gen1Collections|Garbage Collections der Generation 1|Count|Gesamt|Garbage Collections der Generation 1|Instanz|
|Gen2Collections|Garbage Collections der Generation 2|Count|Gesamt|Garbage Collections der Generation 2|Instanz|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|Requests|Requests|Count|Gesamt|Requests|Instanz|
|BytesReceived|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|Http101|HTTP 101|Count|Gesamt|HTTP 101|Instanz|
|Http2xx|HTTP 2xx|Count|Gesamt|HTTP 2xx|Instanz|
|Http3xx|HTTP 3xx|Count|Gesamt|HTTP 3xx|Instanz|
|Http401|HTTP 401|Count|Gesamt|HTTP 401|Instanz|
|Http403|HTTP 403|Count|Gesamt|HTTP 403|Instanz|
|Http404|HTTP 404|Count|Gesamt|HTTP 404|Instanz|
|Http406|HTTP 406|Count|Gesamt|HTTP 406|Instanz|
|Http4xx|HTTP 4xx|Count|Gesamt|HTTP 4xx|Instanz|
|Http5xx|HTTP-Serverfehler|Count|Gesamt|HTTP-Serverfehler|Instanz|
|AverageResponseTime|Durchschnittliche Antwortzeit|Sekunden|Durchschnitt|Durchschnittliche Antwortzeit|Instanz|
|CpuPercentage|CPU-Prozentsatz|Prozent|Durchschnitt|CPU-Prozentsatz|Instanz|
|MemoryPercentage|Arbeitsspeicherprozentsatz|Prozent|Durchschnitt|Arbeitsspeicherprozentsatz|Instanz|
|DiskQueueLength|Warteschlangenlänge des Datenträgers|Count|Durchschnitt|Warteschlangenlänge des Datenträgers|Instanz|
|HttpQueueLength|Länge der HTTP-Warteschlange|Count|Durchschnitt|Länge der HTTP-Warteschlange|Instanz|
|ActiveRequests|Aktive Anforderungen|Count|Gesamt|Aktive Anforderungen|Instanz|
|TotalFrontEnds|Gesamtanzahl an Front-Ends|Count|Durchschnitt|Gesamtanzahl an Front-Ends|Keine Dimensionen|
|SmallAppServicePlanInstances|Kleine Worker im App Service-Plan|Count|Durchschnitt|Kleine Worker im App Service-Plan|Keine Dimensionen|
|MediumAppServicePlanInstances|Mittlere Worker im App Service-Plan|Count|Durchschnitt|Mittlere Worker im App Service-Plan|Keine Dimensionen|
|LargeAppServicePlanInstances|Große Worker im App Service-Plan|Count|Durchschnitt|Große Worker im App Service-Plan|Keine Dimensionen|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|
|WorkersTotal|Gesamtanzahl an Workern|Count|Durchschnitt|Gesamtanzahl an Workern|Keine Dimensionen|
|WorkersAvailable|Verfügbare Worker|Count|Durchschnitt|Verfügbare Worker|Keine Dimensionen|
|WorkersUsed|Verwendete Worker|Count|Durchschnitt|Verwendete Worker|Keine Dimensionen|
|CpuPercentage|CPU-Prozentsatz|Prozent|Durchschnitt|CPU-Prozentsatz|Instanz|
|MemoryPercentage|Arbeitsspeicherprozentsatz|Prozent|Durchschnitt|Arbeitsspeicherprozentsatz|Instanz|

## <a name="next-steps"></a>Nächste Schritte
* [Informationen zu Metriken in Azure Monitor](data-platform.md)
* [Erstellen von Warnungen zu Metriken](alerts-overview.md)
* [Exportieren von Metriken in Storage, Event Hub oder Log Analytics](diagnostic-logs-overview.md)
