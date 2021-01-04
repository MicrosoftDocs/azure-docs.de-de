---
title: Von Azure Monitor unterstützte Metriken nach Ressourcentyp
description: Liste der Metriken, die mit Azure Monitor für jeden Ressourcentyp zur Verfügung stehen.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 1741331c57427c57507376afdb878edc18259b72
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93306833"
---
# <a name="supported-metrics-with-azure-monitor"></a>Unterstützte Metriken von Azure Monitor

> [!NOTE]
> Die meisten Einträge dieser Liste werden automatisch von der REST-API für Azure Monitor-Metriken generiert. Jegliche Änderungen, die über GitHub an dieser Liste vorgenommen werden, können ohne Warnung überschrieben werden. Informationen dazu, wie die Liste dauerhaft aktualisiert werden kann, erhalten Sie beim Autor dieses Artikels.

Azure Monitor bietet verschiedene Methoden für die Interaktion mit Metriken, z.B. die Diagrammdarstellung im Portal, den Zugriff über die REST-API oder die Abfrage über PowerShell oder CLI. 

Dieser Artikel ist eine vollständige Liste aller Plattformmetriken (d. h. der automatisch erfassten), die derzeit mit der konsolidierten Metrikpipeline von Azure Monitor zur Verfügung stehen. Die Liste wurde am 27. März 2020 zuletzt aktualisiert. Nach diesem Datum geänderte oder hinzugefügte Metriken werden möglicherweise unten nicht angezeigt. Verwenden Sie die [API-Version 2018-01-01](/rest/api/monitor/metricdefinitions), um diese Metriken programmgesteuert abzufragen und auf die Liste zuzugreifen. Andere Metriken, die nicht in dieser Liste enthalten sind, stehen möglicherweise im Portal oder über Legacy-APIs zur Verfügung.

Die Metriken sind nach Ressourcenanbietern und Ressourcentyp geordnet. Eine Liste der Dienste und der Ressourcenanbieter, die zu diesen gehören, finden Sie unter [Ressourcenanbieter für Azure-Dienste](../../azure-resource-manager/management/azure-services-resource-providers.md). 

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportieren von Plattformmetriken an andere Speicherorte

Sie können die Plattformmetriken auf eine von zwei Arten aus der Azure Monitor-Pipeline an andere Speicherorte exportieren.
1. Sie können die [REST-API für Metriken](/rest/api/monitor/metrics/list) verwenden.
2. Verwenden Sie [Diagnoseeinstellungen](diagnostic-settings.md), um Plattformmetriken an folgende Dienste weiterzuleiten: 
    - Azure Storage
    - Azure Monitor Logs (und somit Log Analytics)
    - Event Hubs (zur Übertragung an nicht von Microsoft stammende Systeme) 

Die Verwendung von Diagnoseeinstellungen ist die einfachste Möglichkeit, um Metriken weiterzuleiten. Es gibt jedoch einige Einschränkungen: 

- **Nicht alle sind exportierbar:** Über die REST-API können zwar alle Metriken exportiert werden, doch einige können aufgrund von Feinheiten im Back-End von Azure Monitor nicht mithilfe von Diagnoseeinstellungen exportiert werden. Die Spalte *Über Diagnoseeinstellungen exportierbar?* in den folgenden Tabellen enthält die Metriken, die auf diese Weise exportiert werden können.  

- **Mehrdimensionale Metriken:** Das Senden mehrdimensionaler Metriken an andere Speicherorte über Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert. *Beispiel* : Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.

## <a name="guest-os-and-host-os-metrics"></a>Metriken für Gast- und Hostbetriebssysteme

> [!WARNING]
> Metriken für das Gastbetriebssystem (Gast-BS), das in Azure Virtual Machines, Service Fabric und Cloud Services ausgeführt wird, werden hier **nicht** aufgeführt. Die Metriken für das Gastbetriebssystem müssen stattdessen über einen oder mehrere Agents erfasst werden, die auf dem oder als Teil des Gastbetriebssystems ausgeführt werden.  Zu den Gastbetriebssystem-Metriken zählen Leistungsindikatoren, die den CPU-Prozentsatz oder die Arbeitsspeicherauslastung auf dem Gastbetriebssystem nachverfolgen. Diese werden häufig für die automatische Skalierung oder Warnungen verwendet. 
>
> **Für Hostbetriebssysteme sind Metriken verfügbar. Diese werden weiter unten aufgeführt.** Diese Metriken sind nicht identisch. Die Hostbetriebssystem-Metriken beziehen sich auf die Hyper-V-Sitzung, die die Gastbetriebssystem-Sitzung hosten. 

> [!TIP]
> Wenn Sie die Erweiterung [Azure-Diagnose](diagnostics-extension-overview.md) verwenden, besteht eine bewährte Methode darin, Leistungsmetriken für das Gastbetriebssystem an die Azure Monitor-Metrikdatenbank zu senden, in der Plattformmetriken gespeichert werden. Die Erweiterung leitet Gastbetriebssystem-Metriken über die API für [benutzerdefinierte Metriken](metrics-custom-overview.md) weiter. Anschließend können Sie Gastbetriebssystem-Metriken als Diagramm darstellen, mit Warnungen verknüpfen und anderweitig wie Plattformmetriken verwenden. Sie können den Log Analytics-Agent alternativ oder zusätzlich verwenden, um Gastbetriebssystem-Metriken an Azure Monitor Logs bzw. Log Analytics zu senden. Dort können Sie diese Metriken in Kombination mit anderen Daten abfragen. 

Weitere wichtige Informationen finden Sie unter [Übersicht über Azure Monitor-Agents](agents-overview.md).    

## <a name="table-formatting"></a>Tabellenformatierung

> [!IMPORTANT] 
> Mit dem aktuellen Update wird eine neue Spalte hinzugefügt, und die Metriken werden alphabetisch sortiert. Das bedeutet, dass je nach Größe Ihres Browserfensters ggf. eine horizontale Scrollleiste bei den unten aufgeführten Tabellen vorhanden ist. Wenn Sie bestimmte Informationen nicht finden könnten, können Sie mithilfe der Scrollleiste die gesamte Tabelle anzeigen.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Ja|Memory: Bereinigung – aktueller Preis|Anzahl|Average|Aktueller Preis des Arbeitsspeichers, $/Byte/Zeit, normalisiert auf 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Ja|Memory: Bereinigung – nicht verkleinerbarer Arbeitsspeicher|Byte|Average|Die Menge des Arbeitsspeichers in Byte, die nicht durch den Hintergrundbereinigungsprozess bereinigt wird.|ServerResourceType|
|CleanerMemoryShrinkable|Ja|Memory: Bereinigung – verkleinerbarer Arbeitsspeicher|Byte|Average|Die Menge des Arbeitsspeichers in Byte, die durch den Hintergrundbereinigungsprozess bereinigt wird.|ServerResourceType|
|CommandPoolBusyThreads|Ja|Threads: Ausgelastete Threads im Befehlspool|Anzahl|Average|Anzahl ausgelasteter Threads im Befehlsthreadpool.|ServerResourceType|
|CommandPoolIdleThreads|Ja|Threads: Leerlaufthreads im Befehlspool|Anzahl|Average|Anzahl von Leerlaufthreads im Befehlsthreadpool.|ServerResourceType|
|CommandPoolJobQueueLength|Ja|Warteschlangenlänge für Aufträge im Befehlspool|Anzahl|Average|Gibt die Anzahl von Aufträgen in der Warteschlange des Befehlsthreadpools an.|ServerResourceType|
|CurrentConnections|Ja|Verbindung: Aktuelle Verbindungen|Anzahl|Average|Aktuelle Anzahl hergestellter Clientverbindungen.|ServerResourceType|
|CurrentUserSessions|Ja|Aktuelle Benutzersitzungen|Anzahl|Average|Aktuelle Anzahl von eingerichteten Benutzersitzungen|ServerResourceType|
|LongParsingBusyThreads|Ja|Threads: Ausgelastete lange Analysethreads|Anzahl|Average|Anzahl ausgelasteter Threads im Pool für lange Analysethreads.|ServerResourceType|
|LongParsingIdleThreads|Ja|Threads: Lange Analysethreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads im Pool für lange Analysethreads.|ServerResourceType|
|LongParsingJobQueueLength|Ja|Threads: Warteschlangenlänge für lange Analyseaufträge|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Pools für lange Analysethreads.|ServerResourceType|
|mashup_engine_memory_metric|Ja|M-Engine – Arbeitsspeicher|Byte|Average|Arbeitsspeichernutzung durch Mashup-Engine-Prozesse|ServerResourceType|
|mashup_engine_private_bytes_metric|Ja|Private Bytes für M-Engine|Byte|Average|Nutzung privater Bytes durch Prozesse der Mashup-Engine.|ServerResourceType|
|mashup_engine_qpu_metric|Ja|M-Engine – QPU|Anzahl|Average|QPU-Nutzung durch Mashup-Engine-Prozesse|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Ja|Virtuelle Bytes für M-Engine|Byte|Average|Nutzung virtueller Bytes durch Prozesse der Mashup-Engine.|ServerResourceType|
|memory_metric|Ja|Arbeitsspeicher|Byte|Average|Arbeitsspeicher. Bereich: 0–25 GB für S1, 0–50 GB für S2 und 0–100 GB für S4|ServerResourceType|
|memory_thrashing_metric|Ja|Arbeitsspeicherüberlastung|Percent|Average|Durchschnittliche Arbeitsspeicherüberlastung.|ServerResourceType|
|MemoryLimitHard|Ja|Memory: Grenzwert für den festen Speicher|Byte|Average|Grenzwert für den festen Speicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitHigh|Ja|Memory: Obere Arbeitsspeichergrenze|Byte|Average|Oberer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitLow|Ja|Memory: Untere Arbeitsspeichergrenze|Byte|Average|Unterer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryLimitVertiPaq|Ja|Memory: VertiPaq-Arbeitsspeichergrenze|Byte|Average|In-Memory-Grenzwert gemäß Konfigurationsdatei.|ServerResourceType|
|MemoryUsage|Ja|Memory: Speicherauslastung|Byte|Average|Speicherauslastung des Serverprozesses, wie bei der Berechnung des Arbeitsspeicherpreises für die Bereinigung verwendet. Entspricht dem Indikator "Process\PrivateBytes" zuzüglich der Größe der im Speicher abgebildeten Daten. Von der xVelocity-Engine für Datenanalyse im Arbeitsspeicher (VertiPaq) abgebildeter oder belegter Arbeitsspeicher, der über die xVelocity-Arbeitsspeichergrenze hinausgeht, wird dabei ignoriert.|ServerResourceType|
|private_bytes_metric|Ja|Private Bytes|Byte|Average|Private Bytes.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Ja|Threads: Ausgelastete Threads für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von Threads, die E/A-Aufträge im Verarbeitungsthreadpool ausführen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Ja|Threads: Ausgelastete Nicht-E/A-Threads im Verarbeitungspool|Anzahl|Average|Anzahl von Threads, die Nicht-E/A-Aufträge im Verarbeitungsthreadpool ausführen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Ja|Threads: Leerlaufthreads für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Ja|Threads: Nicht-E/A-Leerlaufthreads im Verarbeitungspool|Anzahl|Average|Anzahl von Leerlaufthreads im Verarbeitungsthreadpool, die für Nicht-E/A-Aufträge vorgesehen sind.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Ja|Threads: Warteschlangenlänge für E/A-Aufträge im Verarbeitungspool|Anzahl|Average|Anzahl von E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools.|ServerResourceType|
|ProcessingPoolJobQueueLength|Ja|Warteschlangenlänge für Verarbeitungspoolaufträge|Anzahl|Average|Anzahl von Nicht-E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools|ServerResourceType|
|qpu_metric|Ja|QPU|Anzahl|Average|QPU. Bereich: 0–100 für S1, 0–200 für S2 und 0–400 für S4|ServerResourceType|
|QueryPoolBusyThreads|Ja|Ausgelastete Abfragepoolthreads|Anzahl|Average|Anzahl von ausgelasteten Threads im Abfragethreadpool|ServerResourceType|
|QueryPoolIdleThreads|Ja|Threads: Abfragepoolthreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|ServerResourceType|
|QueryPoolJobQueueLength|Ja|Threads: Auftragswarteschlangenlänge für Abfragepool|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools.|ServerResourceType|
|Kontingent|Ja|Memory: Kontingent|Byte|Average|Aktuelles Arbeitsspeicherkontingent in Byte. Das Arbeitsspeicherkontingent wird auch als Speicherzuweisung oder Speicherreservierung bezeichnet.|ServerResourceType|
|QuotaBlocked|Ja|Memory: Kontingent blockiert|Anzahl|Average|Aktuelle Anzahl von Kontingentanforderungen, die blockiert werden, bis andere Arbeitsspeicherkontingente freigegeben werden.|ServerResourceType|
|RowsConvertedPerSec|Ja|Verarbeitung: Konvertierte Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der Zeilen, die bei der Verarbeitung konvertiert werden.|ServerResourceType|
|RowsReadPerSec|Ja|Verarbeitung: Gelesene Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der aus allen relationalen Datenbanken gelesenen Zeilen.|ServerResourceType|
|RowsWrittenPerSec|Ja|Verarbeitung: Geschriebene Zeilen pro Sekunde|Anzahl pro Sekunde|Average|Rate der Zeilen, die bei der Verarbeitung geschrieben werden.|ServerResourceType|
|ShortParsingBusyThreads|Ja|Threads: Ausgelastete kurze Analysethreads|Anzahl|Average|Anzahl ausgelasteter Threads im Pool für kurze Analysethreads.|ServerResourceType|
|ShortParsingIdleThreads|Ja|Threads: Kurze Analysethreads im Leerlauf|Anzahl|Average|Anzahl von Leerlaufthreads im Pool für kurze Analysethreads.|ServerResourceType|
|ShortParsingJobQueueLength|Ja|Threads: Warteschlangenlänge für kurze Analyseaufträge|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Pools für kurze Analysethreads.|ServerResourceType|
|SuccessfullConnectionsPerSec|Ja|Erfolgreiche Verbindungen pro Sekunde|Anzahl pro Sekunde|Average|Rate der erfolgreichen Verbindungsabschlüsse|ServerResourceType|
|TotalConnectionFailures|Ja|Verbindungsfehler gesamt|Anzahl|Average|Gesamtanzahl von fehlerhaften Verbindungsversuchen|ServerResourceType|
|TotalConnectionRequests|Ja|Total Connection Requests (Verbindungsanforderungen gesamt)|Anzahl|Average|Gesamtanzahl von Verbindungsanforderungen. Dies sind erhaltene Anforderungen.|ServerResourceType|
|VertiPaqNonpaged|Ja|Memory: Nicht ausgelagerte VertiPaq-Daten|Byte|Average|Bytes von Arbeitsspeicher, die im Arbeitssatz zur Verwendung durch die In-Memory-Engine gesperrt sind.|ServerResourceType|
|VertiPaqPaged|Ja|Memory: Ausgelagerte VertiPaq-Daten|Byte|Average|Bytes von ausgelagertem Arbeitsspeicher, die für In-Memory-Daten verwendet werden.|ServerResourceType|
|virtual_bytes_metric|Ja|Virtuelle Bytes|Byte|Average|Virtuelle Bytes.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BackendDuration|Ja|Dauer von Back-End-Anforderungen|Millisekunden|Average|Dauer von Back-End-Anforderungen in Millisekunden|Speicherort, Hostname|
|Capacity|Ja|Capacity|Percent|Average|Auslastungsmetrik für ApiManagement-Dienst|Standort|
|Duration|Ja|Gesamtdauer von Gatewayanforderungen|Millisekunden|Average|Gesamtdauer von Gatewayanforderungen in Millisekunden|Speicherort, Hostname|
|EventHubDroppedEvents|Ja|Gelöschte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl übersprungener Ereignisse aufgrund Erreichen der maximalen Warteschlangengröße|Standort|
|EventHubRejectedEvents|Ja|Abgelehnte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl abgelehnter EventHub-Ereignisse (falsche Konfiguration oder nicht autorisiert)|Standort|
|EventHubSuccessfulEvents|Ja|Erfolgreiche EventHub-Ereignisse|Anzahl|Gesamt|Anzahl erfolgreicher EventHub-Ereignisse|Standort|
|EventHubThrottledEvents|Ja|Gedrosselte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl gedrosselter EventHub-Ereignisse|Standort|
|EventHubTimedoutEvents|Ja|EventHub-Ereignisse mit Zeitüberschreitung|Anzahl|Gesamt|Anzahl von EventHub-Ereignissen mit Zeitüberschreitung|Standort|
|EventHubTotalBytesSent|Ja|Größe von EventHub-Ereignissen|Byte|Gesamt|Gesamtgröße von EventHub-Ereignissen in Byte|Standort|
|EventHubTotalEvents|Ja|Gesamtzahl von EventHub-Ereignissen|Anzahl|Gesamt|Anzahl von an EventHub gesendeten Ereignissen|Standort|
|EventHubTotalFailedEvents|Ja|Fehlerhafte EventHub-Ereignisse|Anzahl|Gesamt|Anzahl fehlerhafter EventHub-Ereignisse|Standort|
|FailedRequests|Ja|Fehlerhafte Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von Fehlern bei Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|NetworkConnectivity|Ja|Netzwerkkonnektivitätsstatus von Ressourcen (Vorschau)|Anzahl|Average|Netzwerkkonnektivitätsstatus abhängiger Ressourcentypen des API Management-Diensts|Location, ResourceType|
|OtherRequests|Ja|Andere Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von anderen Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|Requests|Ja|Requests|Anzahl|Gesamt|Gatewayanforderungsmetriken mit mehreren Dimensionen|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Ja|Erfolgreiche Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von erfolgreichen Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|TotalRequests|Ja|Gatewayanforderungen insgesamt (veraltet)|Anzahl|Gesamt|Anzahl von Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|
|UnauthorizedRequests|Ja|Nicht autorisierte Gatewayanforderungen (veraltet)|Anzahl|Gesamt|Anzahl von nicht autorisierten Gatewayanforderungen – verwenden Sie stattdessen die multidimensionale Anforderungsmetrik mit der GatewayResponseCodeCategory-Dimension.|Speicherort, Hostname|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Ja|HttpIncomingRequestCount|Anzahl|Anzahl|Gesamtanzahl eingehender HTTP-Anforderungen|StatusCode, Authentication|
|HttpIncomingRequestDuration|Ja|HttpIncomingRequestDuration|Anzahl|Average|Latenz bei einer HTTP-Anforderung|StatusCode, Authentication|
|ThrottledHttpRequestCount|Ja|ThrottledHttpRequestCount|Anzahl|Anzahl|Gedrosselte HTTP-Anforderungen|Keine Dimensionen|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|jvm.gc.live.data.size|Ja|jvm.gc.live.data.size|Byte|Average|Größe des Arbeitsspeicherpools der alten Generation nach einer vollständigen Garbage Collection|Deployment, AppName, Pod|
|jvm.gc.max.data.size|Ja|jvm.gc.max.data.size|Byte|Average|Maximale Größe des Arbeitsspeicherpools der alten Generation|Deployment, AppName, Pod|
|jvm.gc.memory.allocated|Ja|jvm.gc.memory.allocated|Byte|Maximum|Inkrementiert zur Erhöhung der Größe des Speicherpools der neuen Generation nach einer Garbage Collection gegenüber dem Zeitpunkt vor der nächsten|Deployment, AppName, Pod|
|jvm.gc.memory.promoted|Ja|jvm.gc.memory.promoted|Byte|Maximum|Anzahl der positiven Erhöhungen der Größe des Speicherpools der alten Generation vor der Garbage Collection gegenüber nach der GC|Deployment, AppName, Pod|
|jvm.gc.pause.total.count|Ja|jvm.gc.pause.total.count|Anzahl|Gesamt|Anzahl der GC-Pausen|Deployment, AppName, Pod|
|jvm.gc.pause.total.time|Ja|jvm.gc.pause.total.time|Millisekunden|Gesamt|Gesamtzeit der GC-Pausen|Deployment, AppName, Pod|
|jvm.memory.committed|Ja|jvm.memory.committed|Byte|Average|JVM zugewiesener Arbeitsspeicher in Byte|Deployment, AppName, Pod|
|jvm.memory.max|Ja|jvm.memory.max|Byte|Maximum|Die maximale Speichermenge (in Byte), die für die Arbeitsspeicherverwaltung verwendet werden kann|Deployment, AppName, Pod|
|jvm.memory.used|Ja|jvm.memory.used|Byte|Average|Verwendeter App-Arbeitsspeicher in Byte|Deployment, AppName, Pod|
|process.cpu.usage|Ja|process.cpu.usage|Percent|Average|Prozentualer Anteil der App-JVM-CPU-Auslastung|Deployment, AppName, Pod|
|system.cpu.usage|Ja|system.cpu.usage|Percent|Average|Aktuelle CPU-Auslastung für das gesamte System|Deployment, AppName, Pod|
|tomcat.global.error|Ja|tomcat.global.error|Anzahl|Gesamt|Globale Tomcat-Fehler|Deployment, AppName, Pod|
|tomcat.global.received|Ja|tomcat.global.received|Byte|Gesamt|Insgesamt empfangene Bytes für Tomcat|Deployment, AppName, Pod|
|tomcat.global.request.avg.time|Ja|tomcat.global.request.avg.time|Millisekunden|Average|Durchschnittliche Zeit für Tomcat-Anforderungen|Deployment, AppName, Pod|
|tomcat.global.request.max|Ja|tomcat.global.request.max|Millisekunden|Maximum|Maximale Zeit für Tomcat-Anforderung|Deployment, AppName, Pod|
|tomcat.global.request.total.count|Ja|tomcat.global.request.total.count|Anzahl|Gesamt|Gesamtanzahl von Tomcat-Anforderungen|Deployment, AppName, Pod|
|tomcat.global.request.total.time|Ja|tomcat.global.request.total.time|Millisekunden|Gesamt|Tomcat Request Total Time (Gesamtzeit für Tomcat-Anforderungen)|Deployment, AppName, Pod|
|tomcat.global.sent|Ja|tomcat.global.sent|Byte|Gesamt|Insgesamt gesendete Bytes für Tomcat|Deployment, AppName, Pod|
|tomcat.sessions.active.current|Ja|tomcat.sessions.active.current|Anzahl|Gesamt|Tomcat Session Active Count (Maximale Anzahl aktiver Tomcat-Sitzungen)|Deployment, AppName, Pod|
|tomcat.sessions.active.max|Ja|tomcat.sessions.active.max|Anzahl|Gesamt|Maximale Anzahl aktiver Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.alive.max|Ja|tomcat.sessions.alive.max|Millisekunden|Maximum|Maximale aktive Zeit für Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.created|Ja|tomcat.sessions.created|Anzahl|Gesamt|Anzahl erstellter Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.expired|Ja|tomcat.sessions.expired|Anzahl|Gesamt|Anzahl abgelaufener Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.sessions.rejected|Ja|tomcat.sessions.rejected|Anzahl|Gesamt|Anzahl abgewiesener Tomcat-Sitzungen|Deployment, AppName, Pod|
|tomcat.threads.config.max|Ja|tomcat.threads.config.max|Anzahl|Gesamt|Tomcat Config Max Thread Count (Maximale Threadanzahl der Tomcat-Konfiguration)|Deployment, AppName, Pod|
|tomcat.threads.current|Ja|tomcat.threads.current|Anzahl|Gesamt|Tomcat Current Thread Count (Aktuelle Threadanzahl in Tomcat)|Deployment, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|TotalJob|Ja|Gesamtzahl an Einzelvorgängen (Jobs)|Anzahl|Gesamt|Die Gesamtzahl an Einzelvorgängen (Jobs)|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Ja|Gesamtzahl von Updatebereitstellungsausführungen auf dem Computer|Anzahl|Gesamt|Gesamtzahl von Updatebereitstellungsausführungen für Software auf dem Computer in einer Updatebereitstellungsausführung für Software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Ja|Gesamtzahl von Updatebereitstellungsausführungen|Anzahl|Gesamt|Gesamtzahl von Updatebereitstellungsausführungen für Software|SoftwareUpdateConfigurationName, Status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CoreCount|Nein|Dedizierte Anzahl von Kernen|Anzahl|Gesamt|Gesamtzahl der dedizierten Kerne im Batch-Konto|Keine Dimensionen|
|CreatingNodeCount|Nein|Anzahl erstellter Knoten|Anzahl|Gesamt|Anzahl von Knoten, die gerade erstellt werden|Keine Dimensionen|
|IdleNodeCount|Nein|Anzahl von Knoten im Leerlauf|Anzahl|Gesamt|Anzahl von Knoten im Leerlauf|Keine Dimensionen|
|JobDeleteCompleteEvent|Ja|Ereignisse zum Abschluss des Löschvorgangs von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich gelöschten Aufträge|jobId|
|JobDeleteStartEvent|Ja|Ereignisse zum Starten des Löschvorgangs von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der zum Löschen angeforderten Aufträge|jobId|
|JobDisableCompleteEvent|Ja|Ereignisse zum Abschluss der Deaktivierung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich deaktivierten Aufträge|jobId|
|JobDisableStartEvent|Ja|Ereignisse zum Starten der Deaktivierung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der zum Deaktivieren angeforderten Aufträge|jobId|
|JobStartEvent|Ja|Ereignisse zum Starten von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich gestarteten Aufträge|jobId|
|JobTerminateCompleteEvent|Ja|Ereignisse zum Abschluss der Beendigung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der erfolgreich beendeten Aufträge|jobId|
|JobTerminateStartEvent|Ja|Ereignisse zum Starten der Beendigung von Aufträgen|Anzahl|Gesamt|Gesamtanzahl der zum Beenden angeforderten Aufträge|jobId|
|LeavingPoolNodeCount|Nein|Anzahl von Knoten, die den Pool verlassen|Anzahl|Gesamt|Anzahl von Knoten, die den Pool verlassen|Keine Dimensionen|
|LowPriorityCoreCount|Nein|Anzahl von LowPriority-Kernen|Anzahl|Gesamt|Gesamtzahl der Kerne mit niedriger Priorität im Batch-Konto|Keine Dimensionen|
|OfflineNodeCount|Nein|Anzahl von Offlineknoten|Anzahl|Gesamt|Anzahl offline geschalteter Knoten|Keine Dimensionen|
|PoolCreateEvent|Ja|Poolerstellungsereignisse|Anzahl|Gesamt|Gesamtanzahl erstellter Pools|poolId|
|PoolDeleteCompleteEvent|Ja|Ereignisse zum Abschluss des Löschvorgangs von Pools|Anzahl|Gesamt|Gesamtanzahl abgeschlossener Poollöschvorgänge|poolId|
|PoolDeleteStartEvent|Ja|Ereignisse zum Starten des Löschvorgangs von Pools|Anzahl|Gesamt|Gesamtanzahl gestarteter Poollöschvorgänge|poolId|
|PoolResizeCompleteEvent|Ja|Ereignisse zum Abschluss der Größenänderung von Pools|Anzahl|Gesamt|Gesamtanzahl abgeschlossener Größenänderungen von Pools|poolId|
|PoolResizeStartEvent|Ja|Ereignisse zum Start der Größenänderung von Pools|Anzahl|Gesamt|Gesamtanzahl gestarteter Größenänderungen von Pools|poolId|
|PreemptedNodeCount|Nein|Anzahl der vorzeitig entfernten Knoten|Anzahl|Gesamt|Die Anzahl der vorzeitig entfernten Knoten|Keine Dimensionen|
|RebootingNodeCount|Nein|Anzahl neu gestarteter Knoten|Anzahl|Gesamt|Anzahl von Knoten, die neu gestartet werden|Keine Dimensionen|
|ReimagingNodeCount|Nein|Anzahl von Knoten mit Reimaging|Anzahl|Gesamt|Anzahl von Knoten, für die ein Reimaging durchgeführt wird|Keine Dimensionen|
|RunningNodeCount|Nein|Anzahl ausgeführter Knoten|Anzahl|Gesamt|Anzahl ausgeführter Knoten|Keine Dimensionen|
|StartingNodeCount|Nein|Anzahl gestarteter Knoten|Anzahl|Gesamt|Anzahl von Knoten, die gerade gestartet werden|Keine Dimensionen|
|StartTaskFailedNodeCount|Nein|Anzahl von Knoten mit Starttaskfehlern|Anzahl|Gesamt|Anzahl von Knoten, bei denen der Starttask nicht durchgeführt werden konnte|Keine Dimensionen|
|TaskCompleteEvent|Ja|Taskabschlussereignisse|Anzahl|Gesamt|Gesamtanzahl abgeschlossener Tasks|poolId, jobId|
|TaskFailEvent|Ja|Taskfehlerereignisse|Anzahl|Gesamt|Gesamtanzahl von Tasks, die mit Fehlerstatus abgeschlossen wurden|poolId, jobId|
|TaskStartEvent|Ja|Taskstartereignisse|Anzahl|Gesamt|Gesamtanzahl gestarteter Tasks|poolId, jobId|
|TotalLowPriorityNodeCount|Nein|Anzahl der Knoten mit niedriger Priorität|Anzahl|Gesamt|Gesamtzahl der Knoten mit niedriger Priorität im Batchkonto|Keine Dimensionen|
|TotalNodeCount|Nein|Dedizierte Knotenanzahl|Anzahl|Gesamt|Gesamtzahl der dedizierten Knoten im Batch-Konto|Keine Dimensionen|
|UnusableNodeCount|Nein|Anzahl nicht verwendbarer Knoten|Anzahl|Gesamt|Anzahl nicht verwendbarer Knoten|Keine Dimensionen|
|WaitingForStartTaskNodeCount|Nein|Anzahl von Knoten, die auf den Starttask warten|Anzahl|Gesamt|Anzahl von Knoten, die auf den Abschluss des Starttasks warten|Keine Dimensionen|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Aktive Kerne|Ja|Aktive Kerne|Anzahl|Average|Gesamtanzahl der aktiven Kerne|Scenario, ClusterName|
|Aktive Knoten|Ja|Aktive Knoten|Anzahl|Average|Anzahl ausgeführter Knoten|Scenario, ClusterName|
|Kerne im Leerlauf|Ja|Kerne im Leerlauf|Anzahl|Average|Gesamtanzahl der Kerne im Leerlauf|Scenario, ClusterName|
|Knoten im Leerlauf|Ja|Knoten im Leerlauf|Anzahl|Average|Anzahl von Knoten im Leerlauf|Scenario, ClusterName|
|Auftrag abgeschlossen|Ja|Auftrag abgeschlossen|Anzahl|Gesamt|Anzahl der abgeschlossenen Aufträge|Scenario, ClusterName, ResultType|
|Auftrag übermittelt|Ja|Auftrag übermittelt|Anzahl|Gesamt|Anzahl der übermittelten Aufträge|Scenario, ClusterName|
|Ausscheidende Kerne|Ja|Ausscheidende Kerne|Anzahl|Average|Anzahl der ausscheidenden Kerne|Scenario, ClusterName|
|Ausscheidende Knoten|Ja|Ausscheidende Knoten|Anzahl|Average|Anzahl der ausscheidenden Knoten|Scenario, ClusterName|
|Vorzeitig entfernte Kerne|Ja|Vorzeitig entfernte Kerne|Anzahl|Average|Anzahl der vorzeitig entfernten Kerne|Scenario, ClusterName|
|Vorzeitig entfernte Knoten|Ja|Vorzeitig entfernte Knoten|Anzahl|Average|Die Anzahl der vorzeitig entfernten Knoten|Scenario, ClusterName|
|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Ja|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Anzahl|Average|Prozentualer Anteil der genutzten Kontingente|Scenario, ClusterName, VmFamilyName, VmPriority|
|Kerne insgesamt|Ja|Kerne insgesamt|Anzahl|Average|Gesamtanzahl von Kernen|Scenario, ClusterName|
|Knoten insgesamt|Ja|Knoten insgesamt|Anzahl|Average|Gesamtanzahl von Knoten|Scenario, ClusterName|
|Nicht verwendbare Kerne|Ja|Nicht verwendbare Kerne|Anzahl|Average|Anzahl der nicht verwendbaren Kerne|Scenario, ClusterName|
|Unusable Nodes (Nicht verwendbare Knoten)|Ja|Unusable Nodes (Nicht verwendbare Knoten)|Anzahl|Average|Anzahl nicht verwendbarer Knoten|Scenario, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Ja|Anzahl verarbeiteter Broadcasts|Anzahl|Average|Anzahl verarbeiteter Transaktionen|Node, channel, type, status|
|ConnectionAccepted|Ja|Akzeptierte Verbindungen|Anzahl|Gesamt|Akzeptierte Verbindungen|Node|
|ConnectionActive|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Node|
|ConnectionHandled|Ja|Behandelte Verbindungen|Anzahl|Gesamt|Behandelte Verbindungen|Node|
|ConsensusEtcdraftCommittedBlockNumber|Ja|Consensus Etcdraft – Committete Blocknummer|Anzahl|Average|Blocknummer des letzten committeten Blocks|Node, channel|
|CpuUsagePercentageInDouble|Ja|Prozentuale CPU-Auslastung|Percent|Maximum|Prozentuale CPU-Auslastung|Node|
|EndorserEndorsementFailures|Ja|Empfehlungsfehler|Anzahl|Average|Anzahl der fehlerhaften Empfehlungen|Node, channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Ja|Übergeordneter Gossip/übergeordnete Instanz|Anzahl|Gesamt|Peer ist übergeordnet (1) oder untergeordnet (0)|Node, channel|
|GossipMembershipTotalPeersKnown|Ja|Gossipmitgliedschaft – Bekannte Peers insgesamt|Anzahl|Average|Insgesamt bekannte Peers|Node, channel|
|GossipStateHeight|Ja|Gossipstatus – Höhe|Anzahl|Average|Aktuelle Ledgerhöhe|Node, channel|
|IOReadBytes|Ja|Gelesene E/A-Bytes|Byte|Gesamt|Gelesene E/A-Bytes|Node|
|IOWriteBytes|Ja|Geschriebene E/A-Bytes|Byte|Gesamt|Geschriebene E/A-Bytes|Node|
|LedgerTransactionCount|Ja|Anzahl der Ledgertransaktionen|Anzahl|Average|Anzahl verarbeiteter Transaktionen|Node, channel, transaction_type, chaincode, validation_code|
|MemoryLimit|Ja|Arbeitsspeicherlimit|Byte|Average|Arbeitsspeicherlimit|Node|
|MemoryUsage|Ja|Speicherauslastung|Byte|Average|Speicherauslastung|Node|
|MemoryUsagePercentageInDouble|Ja|Prozentuale Arbeitsspeicherauslastung|Percent|Average|Prozentuale Arbeitsspeicherauslastung|Node|
|PendingTransactions|Ja|Ausstehende Transaktionen|Anzahl|Average|Ausstehende Transaktionen|Node|
|ProcessedBlocks|Ja|Verarbeitete Blöcke|Anzahl|Gesamt|Verarbeitete Blöcke|Node|
|ProcessedTransactions|Ja|Verarbeitete Transaktionen|Anzahl|Gesamt|Verarbeitete Transaktionen|Node|
|QueuedTransactions|Ja|Transaktionen in Warteschlange|Anzahl|Average|Transaktionen in Warteschlange|Node|
|RequestHandled|Ja|Behandelte Anforderungen|Anzahl|Gesamt|Behandelte Anforderungen|Node|
|StorageUsage|Ja|Speicherauslastung|Byte|Average|Speicherauslastung|Node|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|cachehits|Ja|Cachetreffer|Anzahl|Gesamt||ShardId|
|cachehits0|Ja|Cachetreffer (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits1|Ja|Cachetreffer (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits2|Ja|Cachetreffer (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits3|Ja|Cachetreffer (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits4|Ja|Cachetreffer (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits5|Ja|Cachetreffer (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits6|Ja|Cachetreffer (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits7|Ja|Cachetreffer (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits8|Ja|Cachetreffer (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|cachehits9|Ja|Cachetreffer (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|cacheLatency|Ja|Cachewartezeit in Mikrosekunden (Vorschau)|Anzahl|Average||ShardId|
|cachemisses|Ja|Cachefehler|Anzahl|Gesamt||ShardId|
|cachemisses0|Ja|Cachefehler (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses1|Ja|Cachefehler (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses2|Ja|Cachefehler (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses3|Ja|Cachefehler (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses4|Ja|Cachefehler (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses5|Ja|Cachefehler (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses6|Ja|Cachefehler (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses7|Ja|Cachefehler (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses8|Ja|Cachefehler (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|cachemisses9|Ja|Cachefehler (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|cachemissrate|Ja|Cachefehlerrate|Percent|cachemissrate||ShardId|
|cacheRead|Ja|Cache-Lesevorgänge|Bytes pro Sekunde|Maximum||ShardId|
|cacheRead0|Ja|Cachelesevorgänge (Shard 0)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead1|Ja|Cachelesevorgänge (Shard 1)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead2|Ja|Cachelesevorgänge (Shard 2)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead3|Ja|Cachelesevorgänge (Shard 3)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead4|Ja|Cachelesevorgänge (Shard 4)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead5|Ja|Cachelesevorgänge (Shard 5)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead6|Ja|Cachelesevorgänge (Shard 6)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead7|Ja|Cachelesevorgänge (Shard 7)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead8|Ja|Cachelesevorgänge (Shard 8)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheRead9|Ja|Cachelesevorgänge (Shard 9)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite|Ja|Cache-Schreibvorgänge|Bytes pro Sekunde|Maximum||ShardId|
|cacheWrite0|Ja|Cacheschreibvorgänge (Shard 0)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite1|Ja|Cacheschreibvorgänge (Shard 1)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite2|Ja|Cacheschreibvorgänge (Shard 2)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite3|Ja|Cacheschreibvorgänge (Shard 3)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite4|Ja|Cacheschreibvorgänge (Shard 4)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite5|Ja|Cacheschreibvorgänge (Shard 5)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite6|Ja|Cacheschreibvorgänge (Shard 6)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite7|Ja|Cacheschreibvorgänge (Shard 7)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite8|Ja|Cacheschreibvorgänge (Shard 8)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|cacheWrite9|Ja|Cacheschreibvorgänge (Shard 9)|Bytes pro Sekunde|Maximum||Keine Dimensionen|
|connectedclients|Ja|Verbundene Clients|Anzahl|Maximum||ShardId|
|connectedclients0|Ja|Verbundene Clients (Shard 0)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients1|Ja|Verbundene Clients (Shard 1)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients2|Ja|Verbundene Clients (Shard 2)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients3|Ja|Verbundene Clients (Shard 3)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients4|Ja|Verbundene Clients (Shard 4)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients5|Ja|Verbundene Clients (Shard 5)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients6|Ja|Verbundene Clients (Shard 6)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients7|Ja|Verbundene Clients (Shard 7)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients8|Ja|Verbundene Clients (Shard 8)|Anzahl|Maximum||Keine Dimensionen|
|connectedclients9|Ja|Verbundene Clients (Shard 9)|Anzahl|Maximum||Keine Dimensionen|
|errors|Ja|Errors|Anzahl|Maximum||ShardId, ErrorType|
|evictedkeys|Ja|Entfernte Schlüssel|Anzahl|Gesamt||ShardId|
|evictedkeys0|Ja|Entfernte Schlüssel (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys1|Ja|Entfernte Schlüssel (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys2|Ja|Entfernte Schlüssel (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys3|Ja|Entfernte Schlüssel (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys4|Ja|Entfernte Schlüssel (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys5|Ja|Entfernte Schlüssel (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys6|Ja|Entfernte Schlüssel (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys7|Ja|Entfernte Schlüssel (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys8|Ja|Entfernte Schlüssel (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|evictedkeys9|Ja|Entfernte Schlüssel (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys|Ja|Abgelaufene Schlüssel|Anzahl|Gesamt||ShardId|
|expiredkeys0|Ja|Abgelaufene Schlüssel (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys1|Ja|Abgelaufene Schlüssel (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys2|Ja|Abgelaufene Schlüssel (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys3|Ja|Abgelaufene Schlüssel (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys4|Ja|Abgelaufene Schlüssel (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys5|Ja|Abgelaufene Schlüssel (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys6|Ja|Abgelaufene Schlüssel (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys7|Ja|Abgelaufene Schlüssel (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys8|Ja|Abgelaufene Schlüssel (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|expiredkeys9|Ja|Abgelaufene Schlüssel (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands|Ja|get-Vorgänge|Anzahl|Gesamt||ShardId|
|getcommands0|Ja|Get-Vorgänge (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands1|Ja|Get-Vorgänge (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands2|Ja|Get-Vorgänge (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands3|Ja|Get-Vorgänge (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands4|Ja|Get-Vorgänge (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands5|Ja|Get-Vorgänge (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands6|Ja|Get-Vorgänge (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands7|Ja|Get-Vorgänge (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands8|Ja|Get-Vorgänge (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|getcommands9|Ja|Get-Vorgänge (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|operationsPerSecond|Ja|Vorgänge pro Sekunde|Anzahl|Maximum||ShardId|
|operationsPerSecond0|Ja|Vorgänge pro Sekunde (Shard 0)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond1|Ja|Vorgänge pro Sekunde (Shard 1)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond2|Ja|Vorgänge pro Sekunde (Shard 2)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond3|Ja|Vorgänge pro Sekunde (Shard 3)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond4|Ja|Vorgänge pro Sekunde (Shard 4)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond5|Ja|Vorgänge pro Sekunde (Shard 5)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond6|Ja|Vorgänge pro Sekunde (Shard 6)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond7|Ja|Vorgänge pro Sekunde (Shard 7)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond8|Ja|Vorgänge pro Sekunde (Shard 8)|Anzahl|Maximum||Keine Dimensionen|
|operationsPerSecond9|Ja|Vorgänge pro Sekunde (Shard 9)|Anzahl|Maximum||Keine Dimensionen|
|percentProcessorTime|Ja|CPU|Percent|Maximum||ShardId|
|percentProcessorTime0|Ja|CPU (Shard 0)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime1|Ja|CPU (Shard 1)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime2|Ja|CPU (Shard 2)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime3|Ja|CPU (Shard 3)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime4|Ja|CPU (Shard 4)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime5|Ja|CPU (Shard 5)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime6|Ja|CPU (Shard 6)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime7|Ja|CPU (Shard 7)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime8|Ja|CPU (Shard 8)|Percent|Maximum||Keine Dimensionen|
|percentProcessorTime9|Ja|CPU (Shard 9)|Percent|Maximum||Keine Dimensionen|
|serverLoad|Ja|Serverlast|Percent|Maximum||ShardId|
|serverLoad0|Ja|Serverauslastung (Shard 0)|Percent|Maximum||Keine Dimensionen|
|serverLoad1|Ja|Serverauslastung (Shard 1)|Percent|Maximum||Keine Dimensionen|
|serverLoad2|Ja|Serverauslastung (Shard 2)|Percent|Maximum||Keine Dimensionen|
|serverLoad3|Ja|Serverauslastung (Shard 3)|Percent|Maximum||Keine Dimensionen|
|serverLoad4|Ja|Serverauslastung (Shard 4)|Percent|Maximum||Keine Dimensionen|
|serverLoad5|Ja|Serverauslastung (Shard 5)|Percent|Maximum||Keine Dimensionen|
|serverLoad6|Ja|Serverauslastung (Shard 6)|Percent|Maximum||Keine Dimensionen|
|serverLoad7|Ja|Serverauslastung (Shard 7)|Percent|Maximum||Keine Dimensionen|
|serverLoad8|Ja|Serverauslastung (Shard 8)|Percent|Maximum||Keine Dimensionen|
|serverLoad9|Ja|Serverauslastung (Shard 9)|Percent|Maximum||Keine Dimensionen|
|setcommands|Ja|Sets|Anzahl|Gesamt||ShardId|
|setcommands0|Ja|Set-Vorgänge (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands1|Ja|Set-Vorgänge (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands2|Ja|Set-Vorgänge (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands3|Ja|Set-Vorgänge (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands4|Ja|Set-Vorgänge (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands5|Ja|Set-Vorgänge (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands6|Ja|Set-Vorgänge (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands7|Ja|Set-Vorgänge (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands8|Ja|Set-Vorgänge (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|setcommands9|Ja|Set-Vorgänge (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed|Ja|Vorgänge gesamt|Anzahl|Gesamt||ShardId|
|totalcommandsprocessed0|Ja|Vorgänge gesamt (Shard 0)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed1|Ja|Vorgänge gesamt (Shard 1)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed2|Ja|Vorgänge gesamt (Shard 2)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed3|Ja|Vorgänge gesamt (Shard 3)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed4|Ja|Vorgänge gesamt (Shard 4)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed5|Ja|Vorgänge gesamt (Shard 5)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed6|Ja|Vorgänge gesamt (Shard 6)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed7|Ja|Vorgänge gesamt (Shard 7)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed8|Ja|Vorgänge gesamt (Shard 8)|Anzahl|Gesamt||Keine Dimensionen|
|totalcommandsprocessed9|Ja|Vorgänge gesamt (Shard 9)|Anzahl|Gesamt||Keine Dimensionen|
|totalkeys|Ja|Schlüssel insgesamt|Anzahl|Maximum||ShardId|
|totalkeys0|Ja|Schlüssel gesamt (Shard 0)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys1|Ja|Schlüssel gesamt (Shard 1)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys2|Ja|Schlüssel gesamt (Shard 2)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys3|Ja|Schlüssel gesamt (Shard 3)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys4|Ja|Schlüssel gesamt (Shard 4)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys5|Ja|Schlüssel gesamt (Shard 5)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys6|Ja|Schlüssel gesamt (Shard 6)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys7|Ja|Schlüssel gesamt (Shard 7)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys8|Ja|Schlüssel gesamt (Shard 8)|Anzahl|Maximum||Keine Dimensionen|
|totalkeys9|Ja|Schlüssel gesamt (Shard 9)|Anzahl|Maximum||Keine Dimensionen|
|usedmemory|Ja|Verwendeter Arbeitsspeicher|Byte|Maximum||ShardId|
|usedmemory0|Ja|Verwendeter Arbeitsspeicher (Shard 0)|Byte|Maximum||Keine Dimensionen|
|usedmemory1|Ja|Verwendeter Arbeitsspeicher (Shard 1)|Byte|Maximum||Keine Dimensionen|
|usedmemory2|Ja|Verwendeter Arbeitsspeicher (Shard 2)|Byte|Maximum||Keine Dimensionen|
|usedmemory3|Ja|Verwendeter Arbeitsspeicher (Shard 3)|Byte|Maximum||Keine Dimensionen|
|usedmemory4|Ja|Verwendeter Arbeitsspeicher (Shard 4)|Byte|Maximum||Keine Dimensionen|
|usedmemory5|Ja|Verwendeter Arbeitsspeicher (Shard 5)|Byte|Maximum||Keine Dimensionen|
|usedmemory6|Ja|Verwendeter Arbeitsspeicher (Shard 6)|Byte|Maximum||Keine Dimensionen|
|usedmemory7|Ja|Verwendeter Arbeitsspeicher (Shard 7)|Byte|Maximum||Keine Dimensionen|
|usedmemory8|Ja|Verwendeter Arbeitsspeicher (Shard 8)|Byte|Maximum||Keine Dimensionen|
|usedmemory9|Ja|Verwendeter Arbeitsspeicher (Shard 9)|Byte|Maximum||Keine Dimensionen|
|usedmemorypercentage|Ja|Prozentsatz der Arbeitsspeicherverwendung|Percent|Maximum||ShardId|
|usedmemoryRss|Ja|Verwendeter Arbeitsspeicher (RSS)|Byte|Maximum||ShardId|
|usedmemoryRss0|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 0)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss1|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 1)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss2|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 2)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss3|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 3)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss4|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 4)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss5|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 5)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss6|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 6)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss7|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 7)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss8|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 8)|Byte|Maximum||Keine Dimensionen|
|usedmemoryRss9|Ja|Verwendeter Arbeitsspeicher (RSS, Shard 9)|Byte|Maximum||Keine Dimensionen|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Ja|Anforderungsanzahl für die Web Application Firewall|Anzahl|Gesamt|Die Anzahl der von der Web Application Firewall verarbeiteten Clientanforderungen|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|Nein|Datenträgerlesevorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|RoleInstanceId|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|RoleInstanceId|
|Disk Write Bytes/Sec|Nein|Datenträgerschreibvorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|RoleInstanceId|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|RoleInstanceId|
|Netzwerk eingehend|Ja|Netzwerk eingehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|RoleInstanceId|
|Netzwerk ausgehend|Ja|Netzwerk ausgehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|RoleInstanceId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|Nein|Datenträgerlesevorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Disk Write Bytes/Sec|Nein|Datenträgerschreibvorgänge|Bytes pro Sekunde|Average|Durchschnitt an Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Netzwerk eingehend|Ja|Netzwerk eingehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Netzwerk ausgehend|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Nein|Verwendete Kapazität|Byte|Average|Vom Konto verwendete Kapazität|Keine Dimensionen|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|BlobCapacity|Nein|Blob-Kapazität|Byte|Average|Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte.|BlobType, Tier|
|BlobCount|Nein|Anzahl von Blobs|Anzahl|Average|Die Anzahl von Blobs im Blob-Dienst des Speicherkontos.|BlobType, Tier|
|ContainerCount|Ja|Anzahl von Blob-Containern|Anzahl|Average|Die Anzahl von Containern im Blob-Dienst des Speicherkontos.|Keine Dimensionen|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|IndexCapacity|Nein|Indexkapazität|Byte|Average|Die vom ADLS Gen2-Index (hierarchisch) verwendete Speichermenge in Byte.|Keine Dimensionen|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication, FileShare|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|Nein|Dateikapazität|Byte|Average|Die Größe des vom Dateidienst des Speicherkontos genutzten Speichers in Byte.|FileShare|
|FileCount|Nein|Dateianzahl|Anzahl|Average|Die Anzahl von Dateien im Dateidienst des Speicherkontos|FileShare|
|FileShareCount|Nein|Anzahl von Dateifreigaben|Anzahl|Average|Die Anzahl von Dateifreigaben im Dateidienst des Speicherkontos|Keine Dimensionen|
|FileShareQuota|Nein|Kontingentgröße für Dateifreigabe|Byte|Average|Die Obergrenze für die Speichermenge zur Verwendung durch den Azure Files-Dienst, angegeben in Byte.|FileShare|
|FileShareSnapshotCount|Nein|Anzahl von Momentaufnahmen in Dateifreigabe|Anzahl|Average|Die Anzahl der enthaltenen Momentaufnahmen im Dateidienst des Speicherkontos in der Freigabe|FileShare|
|FileShareSnapshotSize|Nein|Größe der Momentaufnahmen in Dateifreigabe|Byte|Average|Die Speichermenge in Byte, die von den Momentaufnahmen im Dateidienst des Speicherkontos verwendet wird.|FileShare|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication, FileShare|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Ja|Warteschlangenkapazität|Byte|Average|Die Größe des vom Warteschlangendienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|QueueCount|Ja|Anzahl von Warteschlangen|Anzahl|Average|Die Anzahl von Warteschlangen im Warteschlangendienst des Speicherkontos|Keine Dimensionen|
|QueueMessageCount|Ja|Anzahl von Warteschlangennachrichten|Anzahl|Average|Die ungefähre Anzahl von Warteschlangennachrichten im Warteschlangendienst des Speicherkontos|Keine Dimensionen|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die Menge der Ausgangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die End-to-End-Latenz in Millisekunden für erfolgreiche Anforderungen, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die Latenz in Millisekunden für die Verarbeitung einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|TableCapacity|Ja|Tabellenkapazität|Byte|Average|Die Größe des vom Tabellendienst des Speicherkontos genutzten Speichers in Byte.|Keine Dimensionen|
|TableCount|Ja|Anzahl von Tabellen|Anzahl|Average|Die Anzahl von Tabellen im Tabellendienst des Speicherkontos|Keine Dimensionen|
|TableEntityCount|Ja|Anzahl von Tabellenentitäten|Anzahl|Average|Die Anzahl von Tabellenentitäten im Tabellendienst des Speicherkontos|Keine Dimensionen|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BlockedCalls|Ja|Blockierte Aufrufe|Anzahl|Gesamt|Anzahl von Aufrufen, die das Raten- oder Kontingentlimit überschritten haben|ApiName, OperationName, Region|
|CharactersTrained|Ja|Trainierte Zeichen|Anzahl|Gesamt|Gesamtzahl trainierter Zeichen|ApiName, OperationName, Region|
|CharactersTranslated|Ja|Übersetzte Zeichen|Anzahl|Gesamt|Gesamtanzahl von Zeichen in einer eingehenden Textanforderung|ApiName, OperationName, Region|
|ClientErrors|Ja|Clientfehler|Anzahl|Gesamt|Anzahl von Aufrufen mit Fehler auf Clientseite (HTTP-Antwortcode 4xx)|ApiName, OperationName, Region|
|DataIn|Ja|Eingehende Daten|Byte|Gesamt|Menge eingehender Daten in Byte|ApiName, OperationName, Region|
|DataOut|Ja|Datenausgabe|Byte|Gesamt|Menge ausgehender Daten in Byte|ApiName, OperationName, Region|
|Latency|Ja|Latency|Millisekunden|Average|Latenz in Millisekunden|ApiName, OperationName, Region|
|ProcessedImages|Ja|Verarbeitete Bilder|Anzahl|Gesamt| Anzahl von Transaktionen für die Bildverarbeitung.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors|Ja|Serverfehler|Anzahl|Gesamt|Anzahl von Aufrufen mit internem Dienstfehler (HTTP-Antwortcode 5xx)|ApiName, OperationName, Region|
|SpeechSessionDuration|Ja|Dauer der Sprachsitzung|Sekunden|Gesamt|Gesamtdauer der Sprachsitzung in Sekunden|ApiName, OperationName, Region|
|SuccessfulCalls|Ja|Erfolgreiche Aufrufe|Anzahl|Gesamt|Anzahl erfolgreicher Aufrufe|ApiName, OperationName, Region|
|TotalCalls|Ja|Aufrufe gesamt|Anzahl|Gesamt|Gesamtanzahl von Aufrufen|ApiName, OperationName, Region|
|TotalErrors|Ja|Fehler insgesamt|Anzahl|Gesamt|Gesamtzahl von Aufrufen mit Fehlerantwort (HTTP-Antwortcode 4xx oder 5xx)|ApiName, OperationName, Region|
|TotalTokenCalls|Ja|Tokenaufrufe gesamt|Anzahl|Gesamt|Gesamtanzahl von Tokenaufrufen|ApiName, OperationName, Region|
|TotalTransactions|Ja|Transaktionen gesamt|Anzahl|Gesamt|Gesamtanzahl von Transaktionen|Keine Dimensionen|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verbrauchte CPU-Guthaben|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die vom virtuellen Computer verwendet werden|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die für den Burst verfügbar sind|Keine Dimensionen|
|Warteschlangentiefe für Datenträger|Ja|Warteschlangentiefe für Datenträger (Vorschauversion)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN|
|Vom Datenträger gelesene Bytes/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN|
|Auf den Datenträger geschriebene Bytes/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Eingehende Datenflüsse|Ja|Eingehende Datenflüsse|Anzahl|Average|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für eingehende Datenflüsse|Ja|Maximale Erstellungsrate für eingehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Netzwerk eingehend|Ja|Abrechenbarer eingehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren empfangenen Bytes für alle Netzwerkschnittstellen der VMs (eingehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Abrechenbarer ausgehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren gesendeten Bytes für alle Netzwerkschnittstellen der VMs (ausgehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|Warteschlangentiefe für Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger (Vorschauversion)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Warteschlangentiefe pro Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger (veraltet)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Gelesene Bytes pro Betriebssystemdatenträger/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge pro Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Geschriebene Bytes pro Betriebssystemdatenträger/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge pro Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Ausgehende Datenflüsse|Ja|Ausgehende Datenflüsse|Anzahl|Average|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Ja|Maximale Erstellungsrate für ausgehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Warteschlangentiefe pro Datenträger|Ja|Warteschlangentiefe für Datenträger (veraltet)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|SlotId|
|Gelesene Bytes pro Datenträger/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Lesevorgänge pro Datenträger/Sek.|Ja|Datenträgerlesevorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Geschriebene Bytes pro Datenträger/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Schreibvorgänge pro Datenträger/Sek.|Ja|Datenträgerschreibvorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verbrauchte CPU-Guthaben|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die vom virtuellen Computer verwendet werden|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die für den Burst verfügbar sind|Keine Dimensionen|
|Warteschlangentiefe für Datenträger|Ja|Warteschlangentiefe für Datenträger (Vorschauversion)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN, VMName|
|Vom Datenträger gelesene Bytes/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN, VMName|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN, VMName|
|Auf den Datenträger geschriebene Bytes/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN, VMName|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN, VMName|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|VMName|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|VMName|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|VMName|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|VMName|
|Eingehende Datenflüsse|Ja|Eingehende Datenflüsse|Anzahl|Average|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|VMName|
|Maximale Erstellungsrate für eingehende Datenflüsse|Ja|Maximale Erstellungsrate für eingehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|VMName|
|Netzwerk eingehend|Ja|Abrechenbarer eingehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren empfangenen Bytes für alle Netzwerkschnittstellen der VMs (eingehender Datenverkehr) (veraltet)|VMName|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|VMName|
|Netzwerk ausgehend|Ja|Abrechenbarer ausgehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren gesendeten Bytes für alle Netzwerkschnittstellen der VMs (ausgehender Datenverkehr) (veraltet)|VMName|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|VMName|
|Warteschlangentiefe für Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger (Vorschauversion)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|VMName|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|VMName|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|VMName|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|VMName|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|VMName|
|Warteschlangentiefe pro Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger (veraltet)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Gelesene Bytes pro Betriebssystemdatenträger/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge pro Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Geschriebene Bytes pro Betriebssystemdatenträger/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge pro Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Ausgehende Datenflüsse|Ja|Ausgehende Datenflüsse|Anzahl|Average|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|VMName|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Ja|Maximale Erstellungsrate für ausgehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|VMName|
|Warteschlangentiefe pro Datenträger|Ja|Warteschlangentiefe für Datenträger (veraltet)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|SlotId|
|Gelesene Bytes pro Datenträger/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Lesevorgänge pro Datenträger/Sek.|Ja|Datenträgerlesevorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Geschriebene Bytes pro Datenträger/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Schreibvorgänge pro Datenträger/Sek.|Ja|Datenträgerschreibvorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|VMName|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN, VMName|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN, VMName|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|VMName|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verbrauchte CPU-Guthaben|Ja|Verbrauchte CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die vom virtuellen Computer verwendet werden|Keine Dimensionen|
|Verbleibende CPU-Guthaben|Ja|Verbleibende CPU-Guthaben|Anzahl|Average|Gesamtanzahl von Guthaben, die für den Burst verfügbar sind|Keine Dimensionen|
|Warteschlangentiefe für Datenträger|Ja|Warteschlangentiefe für Datenträger (Vorschauversion)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|LUN|
|Vom Datenträger gelesene Bytes/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|LUN|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|LUN|
|Auf den Datenträger geschriebene Bytes/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|LUN|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums vom Datenträger gelesen werden|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerlesevorgänge in IOPS|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Bytes, die während des Überwachungszeitraums auf den Datenträger geschrieben werden|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Datenträgerschreibvorgänge in IOPS|Keine Dimensionen|
|Eingehende Datenflüsse|Ja|Eingehende Datenflüsse|Anzahl|Average|Eingehende Datenflüsse sind die Anzahl aktueller Datenflüsse in eingehender Richtung (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für eingehende Datenflüsse|Ja|Maximale Erstellungsrate für eingehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für eingehende Datenflüsse (eingehender Datenverkehr im virtuellen Computer)|Keine Dimensionen|
|Netzwerk eingehend|Ja|Abrechenbarer eingehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren empfangenen Bytes für alle Netzwerkschnittstellen der VMs (eingehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Eingehender Netzwerkverkehr gesamt|Ja|Eingehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen empfangen werden (eingehender Datenverkehr)|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Abrechenbarer ausgehender Netzwerkdatenverkehr (veraltet)|Byte|Gesamt|Die Anzahl von abrechenbaren gesendeten Bytes für alle Netzwerkschnittstellen der VMs (ausgehender Datenverkehr) (veraltet)|Keine Dimensionen|
|Ausgehender Netzwerkverkehr gesamt|Ja|Ausgehender Netzwerkverkehr gesamt|Byte|Gesamt|Die Anzahl von Bytes, die von den virtuellen Computern an allen Netzwerkschnittstellen gesendet werden (ausgehender Datenverkehr)|Keine Dimensionen|
|Warteschlangentiefe für Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger (Vorschauversion)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Vom Betriebssystemdatenträger gelesene Bytes/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (Vorschauversion)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek.|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek.|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Warteschlangentiefe pro Betriebssystemdatenträger|Ja|Warteschlangentiefe für Betriebssystemdatenträger (veraltet)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Betriebssystemdatenträger|Keine Dimensionen|
|Gelesene Bytes pro Betriebssystemdatenträger/Sek.|Ja|Vom Betriebssystemdatenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für den Betriebssystemdatenträger pro Sekunde auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Lesevorgänge pro Betriebssystemdatenträger/Sek.|Ja|Lesevorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einem einzelnen Datenträger gelesen wurden|Keine Dimensionen|
|Geschriebene Bytes pro Betriebssystemdatenträger/Sek.|Ja|Auf den Betriebssystemdatenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums für einen Betriebssystemdatenträger pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Schreibvorgänge pro Betriebssystemdatenträger/Sek.|Ja|Schreibvorgänge auf Betriebssystemdatenträger/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums für einen Betriebssystemdatenträger auf einen einzelnen Datenträger geschrieben wurden|Keine Dimensionen|
|Ausgehende Datenflüsse|Ja|Ausgehende Datenflüsse|Anzahl|Average|Ausgehende Datenflüsse sind die Anzahl aktueller Datenflüsse in ausgehender Richtung (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Maximale Erstellungsrate für ausgehende Datenflüsse|Ja|Maximale Erstellungsrate für ausgehende Datenflüsse|Anzahl pro Sekunde|Average|Die maximale Erstellungsrate für ausgehende Datenflüsse (ausgehender Datenverkehr vom virtuellen Computer)|Keine Dimensionen|
|Warteschlangentiefe pro Datenträger|Ja|Warteschlangentiefe für Datenträger (veraltet)|Anzahl|Average|Warteschlangentiefe (oder Warteschlangenlänge) für Datenträger|SlotId|
|Gelesene Bytes pro Datenträger/Sek.|Ja|Vom Datenträger gelesene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Lesevorgänge pro Datenträger/Sek.|Ja|Datenträgerlesevorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einem einzelnen Datenträger gelesen wurden|SlotId|
|Geschriebene Bytes pro Datenträger/Sek.|Ja|Auf den Datenträger geschriebene Bytes/Sek. (veraltet)|Anzahl pro Sekunde|Average|Bytes, die während des Überwachungszeitraums pro Sekunde auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|Schreibvorgänge pro Datenträger/Sek.|Ja|Datenträgerschreibvorgänge/Sek. (veraltet)|Anzahl pro Sekunde|Average|IOPS, die während des Überwachungszeitraums auf einen einzelnen Datenträger geschrieben wurden|SlotId|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Der Prozentsatz der zugewiesenen Computeeinheiten, die derzeit von den virtuellen Computern verwendet werden|Keine Dimensionen|
|Treffer beim Cachelesevorgang auf Premium-Datenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Fehler beim Cachelesevorgang auf Premium-Datenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Datenträger (Vorschauversion)|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Datenträger|LUN|
|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Percent|Average|Treffer beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|
|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Ja|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger (Vorschauversion)|Percent|Average|Fehler beim Cachelesevorgang auf Premium-Betriebssystemdatenträger|Keine Dimensionen|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CpuUsage|Ja|CPU-Auslastung|Anzahl|Average|CPU-Auslastung für alle Kerne in Millicores|containerName|
|MemoryUsage|Ja|Speicherauslastung|Byte|Average|Gesamtspeicherauslastung in Byte|containerName|
|NetworkBytesReceivedPerSecond|Ja|Empfangene Netzwerkbytes pro Sekunde|Byte|Average|Die pro Sekunde empfangenen Netzwerkbytes.|Keine Dimensionen|
|NetworkBytesTransmittedPerSecond|Ja|Übertragene Netzwerkbytes pro Sekunde|Byte|Average|Die pro Sekunde übertragenen Netzwerkbytes|Keine Dimensionen|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Ja|AgentPool – CPU-Zeit|Sekunden|Gesamt|CPU-Zeit von AgentPool in Sekunden|Keine Dimensionen|
|RunDuration|Ja|Ausführungsdauer|Millisekunden|Gesamt|Ausführungsdauer in Millisekunden|Keine Dimensionen|
|SuccessfulPullCount|Ja|Anzahl erfolgreicher Pullvorgänge|Anzahl|Average|Anzahl erfolgreicher Image-Pullvorgänge|Keine Dimensionen|
|SuccessfulPushCount|Ja|Anzahl erfolgreicher Puchvorgänge|Anzahl|Average|Anzahl erfolgreicher Image-Pushvorgänge|Keine Dimensionen|
|TotalPullCount|Ja|Gesamtzahl von Pullvorgängen|Anzahl|Average|Anzahl von Image-Pullvorgängen insgesamt|Keine Dimensionen|
|TotalPushCount|Ja|Gesamtzahl von Pushvorgängen|Anzahl|Average|Anzahl von Image-Pushvorgängen insgesamt|Keine Dimensionen|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Nein|Gesamtzahl der verfügbaren CPU-Kerne in einem verwalteten Cluster|Anzahl|Average|Gesamtzahl der verfügbaren CPU-Kerne in einem verwalteten Cluster|Keine Dimensionen|
|kube_node_status_allocatable_memory_bytes|Nein|Gesamtzahl des verfügbaren Speicherplatzes in einem verwalteten Cluster|Byte|Average|Gesamtzahl des verfügbaren Speicherplatzes in einem verwalteten Cluster|Keine Dimensionen|
|kube_node_status_condition|Nein|Status für verschiedene Knotenbedingungen|Anzahl|Average|Status für verschiedene Knotenbedingungen|Bedingung, Status, Status2, Knoten|
|kube_pod_status_phase|Nein|Anzahl der Pods nach Phase|Anzahl|Average|Anzahl der Pods nach Phase|Phase, Namespace, Pod|
|kube_pod_status_ready|Nein|Anzahl der Pods mit dem Status „Bereit“|Anzahl|Average|Anzahl der Pods mit dem Status „Bereit“|Namespace, Pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|FailedRequests|Ja|Anforderungsfehler|Anzahl|Gesamt|Ruft die verfügbaren Protokolle für benutzerdefinierte Ressourcenanbieter ab|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Ja|Erfolgreiche Anforderungen|Anzahl|Gesamt|Erfolgreiche Anforderungen durch den benutzerdefinierten Anbieter|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AvailableCapacity|Ja|Verfügbare Kapazität|Byte|Average|Die verfügbare Kapazität in Byte während des Berichtszeitraums.|Keine Dimensionen|
|BytesUploadedToCloud|Ja|Hochgeladene Cloudbytes (Gerät)|Byte|Average|Die Gesamtzahl von Bytes, die in Azure von einem Gerät während des Berichtszeitraum hochgeladen wird.|Keine Dimensionen|
|BytesUploadedToCloudPerShare|Ja|Hochgeladene Cloudbytes (Freigabe)|Byte|Average|Die Gesamtzahl von Bytes, die in Azure von einer Freigabe während des Berichtszeitraum hochgeladen wird.|Freigabe|
|CloudReadThroughput|Ja|Clouddownloaddurchsatz|Bytes pro Sekunde|Average|Der Clouddownloaddurchsatz in Azure während des Berichtszeitraums.|Keine Dimensionen|
|CloudReadThroughputPerShare|Ja|Clouddownloaddurchsatz (Freigabe)|Bytes pro Sekunde|Average|Der Downloaddurchsatz in Azure von einer Freigabe während des Berichtszeitraums.|Freigabe|
|CloudUploadThroughput|Ja|Clouduploaddurchsatz|Bytes pro Sekunde|Average|Der Clouduploaddurchsatz in Azure während des Berichtszeitraums.|Keine Dimensionen|
|CloudUploadThroughputPerShare|Ja|Clouduploaddurchsatz (Freigabe)|Bytes pro Sekunde|Average|Der Uploaddurchsatz in Azure von einer Freigabe während des Berichtszeitraums.|Freigabe|
|HyperVMemoryUtilization|Ja|Edgecomputing – Arbeitsspeichernutzung|Percent|Average|Größe des genutzten Arbeitsspeichers|InstanceName|
|HyperVVirtualProcessorUtilization|Ja|Edgecomputing – CPU in Prozent|Percent|Average|Prozentsatz der CPU-Auslastung|InstanceName|
|NICReadThroughput|Ja|Lesedurchsatz (Netzwerk)|Bytes pro Sekunde|Average|Der Lesedurchsatz der Netzwerkschnittstelle auf dem Gerät im Berichtszeitraum für alle Volumes im Gateway.|InstanceName|
|NICWriteThroughput|Ja|Schreibdurchsatz (Netzwerk)|Bytes pro Sekunde|Average|Der Schreibdurchsatz der Netzwerkschnittstelle auf dem Gerät im Berichtszeitraum für alle Volumes im Gateway.|InstanceName|
|TotalCapacity|Ja|Gesamtkapazität|Byte|Average|Gesamtkapazität|Keine Dimensionen|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|FailedRuns|Ja|Fehlerhafte Ausführungen|Anzahl|Gesamt||pipelineName, activityName|
|SuccessfulRuns|Ja|Erfolgreiche Ausführungen|Anzahl|Gesamt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Ja|Metriken zu abgebrochenen Aktivitätsausführungen|Anzahl|Gesamt||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Ja|Metriken zu fehlerhaften Aktivitätsausführungen|Anzahl|Gesamt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Ja|Metriken zu erfolgreichen Aktivitätsausführungen|Anzahl|Gesamt||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Ja|Gesamtgröße der Factory (Einheit: GB)|Anzahl|Maximum||Keine Dimensionen|
|IntegrationRuntimeAvailableMemory|Ja|Verfügbarer Speicher für Integration Runtime|Byte|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Ja|Anzahl verfügbarer Knoten für Integration Runtime|Anzahl|Average||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Ja|Dauer der Integration Runtime-Warteschlange|Sekunden|Average||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Ja|CPU-Auslastung der Integration Runtime|Percent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Ja|Länge der Integration Runtime-Warteschlange|Anzahl|Average||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Ja|Maximal zulässige Factorygröße (Einheit: GB)|Anzahl|Maximum||Keine Dimensionen|
|MaxAllowedResourceCount|Ja|Anzahl maximal zulässiger Entitäten|Anzahl|Maximum||Keine Dimensionen|
|PipelineCancelledRuns|Ja|Metriken zu abgebrochenen Pipelineausführungen|Anzahl|Gesamt||FailureType, Name|
|PipelineFailedRuns|Ja|Metriken zu fehlerhaften Pipelineausführungen|Anzahl|Gesamt||FailureType, Name|
|PipelineSucceededRuns|Ja|Metriken zu erfolgreichen Pipelineausführungen|Anzahl|Gesamt||FailureType, Name|
|ResourceCount|Ja|Gesamtzahl von Entitäten|Anzahl|Maximum||Keine Dimensionen|
|TriggerCancelledRuns|Ja|Metriken zu abgebrochenen Triggerausführungen|Anzahl|Gesamt||Name, FailureType|
|TriggerFailedRuns|Ja|Metriken zu fehlerhaften Triggerausführungen|Anzahl|Gesamt||Name, FailureType|
|TriggerSucceededRuns|Ja|Metriken zu erfolgreichen Triggerausführungen|Anzahl|Gesamt||Name, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DataRead|Ja|Gelesene Daten|Byte|Gesamt|Gesamtmenge der im Konto gelesenen Daten|Keine Dimensionen|
|DataWritten|Ja|Geschriebene Daten|Byte|Gesamt|Gesamtmenge der in das Konto geschriebenen Daten|Keine Dimensionen|
|ReadRequests|Ja|Leseanforderungen|Anzahl|Gesamt|Anzahl der Datenleseanforderungen für das Konto|Keine Dimensionen|
|TotalStorage|Ja|Gesamtspeicher|Byte|Maximum|Gesamtmenge der im Konto gespeicherten Daten|Keine Dimensionen|
|WriteRequests|Ja|Schreibanforderungen|Anzahl|Gesamt|Anzahl der Datenschreibanforderungen für das Konto|Keine Dimensionen|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Average|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|seconds_behind_master|Ja|Replikationsverzögerung in Sekunden|Anzahl|Maximum|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|serverlog_storage_limit|Ja|Begrenzung des Serverprotokollspeichers|Byte|Average|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|serverlog_storage_percent|Ja|Serverprotokollspeicher in Prozent|Percent|Average|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Ja|Verwendeter Serverprotokollspeicher|Byte|Average|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Average|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|seconds_behind_master|Ja|Replikationsverzögerung in Sekunden|Anzahl|Maximum|Replikationsverzögerung in Sekunden|Keine Dimensionen|
|serverlog_storage_limit|Ja|Begrenzung des Serverprotokollspeichers|Byte|Maximum|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|serverlog_storage_percent|Ja|Serverprotokollspeicher in Prozent|Percent|Average|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Ja|Verwendeter Serverprotokollspeicher|Byte|Average|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|backup_storage_used|Ja|Verwendeter Sicherungsspeicher|Byte|Average|Verwendeter Sicherungsspeicher|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|io_consumption_percent|Ja|E/A in Prozent|Percent|Average|E/A in Prozent|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|pg_replica_log_delay_in_bytes|Ja|Maximale Verzögerung zwischen Replikaten|Byte|Maximum|Verzögerung in Byte beim Replikat mit der größten Verzögerung|Keine Dimensionen|
|pg_replica_log_delay_in_seconds|Ja|Replikatverzögerung|Sekunden|Maximum|Replikatverzögerung in Sekunden|Keine Dimensionen|
|serverlog_storage_limit|Ja|Begrenzung des Serverprotokollspeichers|Byte|Maximum|Begrenzung des Serverprotokollspeichers|Keine Dimensionen|
|serverlog_storage_percent|Ja|Serverprotokollspeicher in Prozent|Percent|Average|Serverprotokollspeicher in Prozent|Keine Dimensionen|
|serverlog_storage_usage|Ja|Verwendeter Serverprotokollspeicher|Byte|Average|Verwendeter Serverprotokollspeicher|Keine Dimensionen|
|storage_limit|Ja|Speicherbegrenzung|Byte|Maximum|Speicherbegrenzung|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|iops|Ja|IOPS|Anzahl|Average|E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_connections|Ja|Die aktiven Verbindungen.|Anzahl|Average|Die aktiven Verbindungen.|Keine Dimensionen|
|connections_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|connections_succeeded|Ja|Erfolgreiche Verbindungen|Anzahl|Gesamt|Erfolgreiche Verbindungen|Keine Dimensionen|
|cpu_percent|Ja|CPU in Prozent|Percent|Average|CPU in Prozent|Keine Dimensionen|
|iops|Ja|IOPS|Anzahl|Average|E/A-Vorgänge pro Sekunde|Keine Dimensionen|
|maximum_used_transactionIDs|Ja|Maximale Anzahl von verwendeten Transaktions-IDs|Anzahl|Average|Maximale Anzahl von verwendeten Transaktions-IDs|Keine Dimensionen|
|memory_percent|Ja|Arbeitsspeicher in Prozent|Percent|Average|Arbeitsspeicher in Prozent|Keine Dimensionen|
|network_bytes_egress|Ja|Netzwerk ausgehend|Byte|Gesamt|Ausgehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|network_bytes_ingress|Ja|Netzwerk eingehend|Byte|Gesamt|Eingehender Netzwerkdatenverkehr über aktive Verbindungen|Keine Dimensionen|
|storage_percent|Ja|Speicher in Prozent|Percent|Average|Speicher in Prozent|Keine Dimensionen|
|storage_used|Ja|Verwendeter Speicher|Byte|Average|Verwendeter Speicher|Keine Dimensionen|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Ja|Abgebrochene C2D-Nachrichten|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgebrochen wurden|Keine Dimensionen|
|c2d.commands.egress.complete.success|Ja|Abgeschlossene C2D-Nachrichtenübermittlungen|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichtenübermittlungen, die vom Gerät erfolgreich abgeschlossen wurden|Keine Dimensionen|
|c2d.commands.egress.reject.success|Ja|Abgelehnte C2D-Nachrichten|Anzahl|Gesamt|Anzahl von Cloud-zu-Gerät-Nachrichten, die vom Gerät abgelehnt wurden|Keine Dimensionen|
|c2d.methods.failure|Ja|Failed direct method invocations (Nicht erfolgreiche direkte Methodenaufrufe)|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe nicht erfolgreich waren.|Keine Dimensionen|
|c2d.methods.requestSize|Ja|Request size of direct method invocations (Anforderungsgröße von direkten Methodenaufrufen)|Byte|Average|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen direkten Methodenaufrufe an.|Keine Dimensionen|
|c2d.methods.responseSize|Ja|Response size of direct method invocations (Antwortgröße von direkten Methodenaufrufen)|Byte|Average|Gibt den Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Antworten für die direkte Methode an.|Keine Dimensionen|
|c2d.methods.success|Ja|Successful direct method invocations (Erfolgreiche direkte Methodenaufrufvorgänge)|Anzahl|Gesamt|Gibt an, wie viele direkte Methodenaufrufe erfolgreich durchgeführt wurden.|Keine Dimensionen|
|c2d.twin.read.failure|Ja|Failed twin reads from back end (Nicht erfolgreiche Zwillingslesevorgänge vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.size|Ja|Response size of twin reads from back end (Antwortgröße von Zwillingslesevorgängen vom Back-End)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.read.success|Ja|Successful twin reads from back end (Erfolgreiche Zwillingslesevorgänge vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.failure|Ja|Failed twin updates from back end (Nicht erfolgreiche Zwillingsaktualisierungen vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.size|Ja|Size of twin updates from back end (Größe der Zwillingsaktualisierungen vom Back-End)|Byte|Average|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Back-End initiiert wurden.|Keine Dimensionen|
|c2d.twin.update.success|Ja|Successful twin updates from back end (Erfolgreiche Zwillingsaktualisierungen vom Back-End)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Back-End initiiert wurden.|Keine Dimensionen|
|C2DMessagesExpired|Ja|Abgelaufene C2D-Nachrichten (Vorschau)|Anzahl|Gesamt|Anzahl von abgelaufenen Cloud-zu-Gerät-Nachrichten|Keine Dimensionen|
|Konfigurationen|Ja|Konfigurationsmetriken|Anzahl|Gesamt|Metriken für Konfigurationsvorgänge|Keine Dimensionen|
|connectedDeviceCount|Nein|Verbundene Geräte (Vorschau)|Anzahl|Average|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|d2c.endpoints.egress.builtIn.events|Ja|Routing: An Nachrichten/Ereignisse übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an den integrierter Endpunkt (Nachrichten/Ereignisse) übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.eventHubs|Ja|Routing: An Event Hub übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Event Hub-Endpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusQueues|Ja|Routing: An Service Bus-Warteschlange übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Warteschlangenendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.serviceBusTopics|Ja|Routing: An Service Bus-Thema übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Service Bus-Themaendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage|Ja|Routing: An den Speicher übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing erfolgreich Nachrichten an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage.blobs|Ja|Routing: An den Speicher übermittelte Blobs|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Blobs an Speicherendpunkte übermittelt hat.|Keine Dimensionen|
|d2c.endpoints.egress.storage.bytes|Ja|Routing: An den Speicher übermittelte Daten|Byte|Gesamt|Die Datenmenge (Bytes), die das IoT Hub-Routing an die Speicherendpunkte übermittelt.|Keine Dimensionen|
|d2c.endpoints.latency.builtIn.events|Ja|Routing: Nachrichtenwartezeit für Nachrichten/Ereignisse|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Telemetrienachricht beim integrierten Endpunkt (Nachrichten/Ereignisse).|Keine Dimensionen|
|d2c.endpoints.latency.eventHubs|Ja|Routing: Nachrichtenwartezeit für Event Hub|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Event Hub-Endpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusQueues|Ja|Routing: Nachrichtenwartezeit für Service Bus-Warteschlange|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Warteschlangenendpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.serviceBusTopics|Ja|Routing: Nachrichtenwartezeit für Service Bus-Thema|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Service Bus-Themaendpunkt.|Keine Dimensionen|
|d2c.endpoints.latency.storage|Ja|Routing: Nachrichtenwartezeit für Speicher|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht beim IoT Hub und dem Eingang der Nachricht bei einem Speicherendpunkt.|Keine Dimensionen|
|d2c.telemetry.egress.dropped|Ja|Routing: Verworfene Telemetrienachrichten |Anzahl|Gesamt|Die Anzahl der Nachrichten, die vom IoT Hub-Routing aufgrund von inaktiven Endpunkten gelöscht wurden. Dieser Wert zählt nicht die Nachrichten, die an die Fallbackroute übermittelt werden, da gelöschte Nachrichten dort nicht übermittelt werden.|Keine Dimensionen|
|d2c.telemetry.egress.fallback|Ja|Routing: An den Fallback übermittelte Nachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten an den mit der Fallbackroute verbundenen Endpunkt übermittelt hat.|Keine Dimensionen|
|d2c.telemetry.egress.invalid|Ja|Routing: Nicht kompatible Telemetrienachrichten|Anzahl|Gesamt|Die Häufigkeit, mit der das IoT Hub-Routing Nachrichten aufgrund einer Inkompatibilität mit dem Endpunkt nicht übermitteln konnte. Dieser Wert umfasst keine Wiederholungen.|Keine Dimensionen|
|d2c.telemetry.egress.orphaned|Ja|Routing: Verwaiste Telemetrienachrichten |Anzahl|Gesamt|Die Häufigkeit, mit der Nachrichten durch das IoT Hub-Routing verwaist wurden, da sie mit keinen Routingregeln (einschließlich der Fallbackregel) übereinstimmten. |Keine Dimensionen|
|d2c.telemetry.egress.success|Ja|Routing: Übermittelte Telemetrienachrichten|Anzahl|Gesamt|Die Anzahl der erfolgreichen Nachrichtenübermittlungen an alle Endpunkte über das IoT Hub-Routing Wenn eine Nachricht an mehrere Endpunkte weitergeleitet wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins Wenn eine Nachricht mehrmals an denselben Endpunkt übermittelt wird, erhöht sich dieser Wert für jede erfolgreiche Übermittlung um eins|Keine Dimensionen|
|d2c.telemetry.Ingress.allProtocol|Ja|Telemetry message send attempts (Sendeversuche für Telemetrienachrichten)|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die an Ihren IoT Hub gesendet werden sollten|Keine Dimensionen|
|d2c.telemetry.ingress.sendThrottle|Ja|Anzahl von Drosselungsfehlern|Anzahl|Gesamt|Anzahl von Drosselungsfehlern aufgrund von Drosselungen des Gerätedurchsatzes|Keine Dimensionen|
|d2c.telemetry.ingress.success|Ja|Telemetry messages sent (Gesendete Telemetrienachrichten)|Anzahl|Gesamt|Anzahl von Telemetrienachrichten vom Gerät an die Cloud, die erfolgreich an Ihren IoT Hub gesendet wurden|Keine Dimensionen|
|d2c.twin.read.failure|Ja|Failed twin reads from devices (Nicht erfolgreiche Zwillingslesevorgänge von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.size|Ja|Response size of twin reads from devices (Antwortgröße von Zwillingslesevorgängen von Geräten)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert für alle erfolgreichen Zwillingslesevorgänge, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.read.success|Ja|Successful twin reads from devices (Erfolgreiche Zwillingslesevorgänge von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingslesevorgängen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.failure|Ja|Failed twin updates from devices (Nicht erfolgreiche Zwillingsaktualisierungen von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.size|Ja|Size of twin updates from devices (Größe der Zwillingsaktualisierungen von Geräten)|Byte|Average|Durchschnitts-, Minimal- und Maximalgröße für alle erfolgreichen Zwillingsaktualisierungen, die vom Gerät initiiert wurden.|Keine Dimensionen|
|d2c.twin.update.success|Ja|Successful twin updates from devices (Erfolgreiche Zwillingsaktualisierungen von Geräten)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Zwillingsaktualisierungen an, die vom Gerät initiiert wurden.|Keine Dimensionen|
|dailyMessageQuotaUsed|Ja|Gesamtzahl verwendeter Nachrichten|Anzahl|Maximum|Gesamtzahl der heute verwendeten Nachrichten|Keine Dimensionen|
|deviceDataUsage|Ja|Gerätedatennutzung gesamt|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|deviceDataUsageV2|Ja|Gerätedatennutzung gesamt (Vorschau)|Byte|Gesamt|Zu und von allen mit IotHub verbundenen Geräten übertragene Bytes|Keine Dimensionen|
|devices.connectedDevices.allProtocol|Ja|Verbundene Geräte (veraltet) |Anzahl|Gesamt|Die Anzahl von Geräten, die mit dem IoT Hub verbunden sind|Keine Dimensionen|
|devices.totalDevices|Ja|Geräte gesamt (veraltet)|Anzahl|Gesamt|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|EventGridDeliveries|Ja|Event Grid-Übermittlungen (Vorschau)|Anzahl|Gesamt|Die Anzahl von IoT Hub-Ereignissen, die in Event Grid veröffentlicht werden. Verwenden Sie die Dimension „Result“ für die Anzahl der erfolgreichen und fehlerhaften Anforderungen. Die „EventType“-Dimension zeigt den Typ des Ereignisses an(https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|Ja|Event Grid-Wartezeit (Vorschau)|Millisekunden|Average|Die durchschnittliche Wartezeit (in Millisekunden) für den Zeitraum zwischen der Generierung des Iot Hub-Ereignisses und der Veröffentlichung des Ereignisses in Event Grid. Diese Zahl ist ein Durchschnittswert für alle Ereignistypen. Verwenden Sie die Dimension „EventType“, um die Wartezeit für einen bestimmten Ereignistyp anzuzeigen.|EventType|
|jobs.cancelJob.failure|Ja|Failed job cancellations (Nicht erfolgreiche Auftragsabbrüche)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.cancelJob.success|Ja|Successful job cancellations (Erfolgreiche Auftragsabbrüche)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auftragsabbrüchen durchgeführt wurden.|Keine Dimensionen|
|jobs.completed|Ja|Abgeschlossene Aufträge|Anzahl|Gesamt|Gibt die Anzahl von abgeschlossenen Aufträgen an.|Keine Dimensionen|
|jobs.createDirectMethodJob.failure|Ja|Failed creations of method invocation jobs (Nicht erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von nicht erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createDirectMethodJob.success|Ja|Successful creations of method invocation jobs (Erfolgreiche Erstellungen von Methodenaufrufaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Erstellungen von Aufträgen für direkte Methodenaufrufe an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.failure|Ja|Failed creations of twin update jobs (Nicht erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von allen nicht erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.createTwinUpdateJob.success|Ja|Successful creations of twin update jobs (Erfolgreiche Erstellungen von Zwillingsaktualisierungsaufträgen)|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Erstellungen von Zwillingsaktualisierungsaufträgen an.|Keine Dimensionen|
|jobs.failed|Ja|Fehlerhafte Aufträge|Anzahl|Gesamt|Gibt die Anzahl aller fehlerhaften Aufträge an.|Keine Dimensionen|
|jobs.listJobs.failure|Ja|Failed calls to list jobs (Nicht erfolgreiche Aufrufe von Auflistungsaufträgen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.listJobs.success|Ja|Successful calls to list jobs (Erfolgreiche Aufrufe von Auflistungsaufträgen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Auflistungsaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.failure|Ja|Failed job queries (Nicht erfolgreiche Auftragsabfragen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|jobs.queryJobs.success|Ja|Successful job queries (Erfolgreiche Auftragsabfragen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Aufrufe von Abfrageaufträgen durchgeführt wurden.|Keine Dimensionen|
|RoutingDataSizeInBytesDelivered|Ja|Größe der Routingzustellungsnachrichten in Byte (Vorschau)|Byte|Gesamt|Die Gesamtgröße der Nachrichten in Byte, die von IoT Hub an einen Endpunkt übermittelt werden: Sie können die Dimensionen EndpointName und EndpointType verwenden, um die Größe der Nachrichten in Byte anzuzeigen, die verschiedenen Endpunkten zugestellt wurden. Der Metrikwert wird für jede zugestellte Nachricht erhöht. Das gilt auch, wenn die Nachricht an mehrere Endpunkte oder mehrfach an denselben Endpunkt übermittelt wird.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Ja|Routingübermittlungen (Vorschau)|Anzahl|Gesamt|Gibt an, wie oft IoT Hub versucht hat, Nachrichten per Routing an alle Endpunkte zu übermitteln. Verwenden Sie die Dimension „Result“, um die Anzahl von erfolgreichen Versuchen bzw. die Anzahl von Versuchen mit Fehlern abzurufen. Der Grund für einen Fehler (z. B. ungültig, verworfen oder verwaist) kann mit der Dimension „FailureReasonCategory“ abgerufen werden. Sie können auch die Dimensionen „EndpointName“ und „EndpointType“ verwenden, um Informationen dazu zu erhalten, wie viele Nachrichten an Ihre verschiedenen Endpunkte übermittelt wurden. Der Metrikwert wird für jeden Übermittlungsversuch um 1 erhöht. Dies gilt auch, wenn die Nachricht an mehrere Endpunkte oder mehrfach an denselben Endpunkt übermittelt wird.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Ja|Routingübermittlungslatenz (Vorschau)|Millisekunden|Average|Durchschnittliche Wartezeit (Millisekunden) zwischen dem Eingang der Nachricht bei IoT Hub und dem Eingang der Telemetrienachricht bei einem Endpunkt. Sie können auch die Dimensionen „EndpointName“ und „EndpointType“ verwenden, um Informationen zu den Latenzzeiten zu Ihren verschiedenen Endpunkten zu erhalten.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|Nein|Geräte gesamt (Vorschau)|Anzahl|Average|Die Anzahl von Geräten, die beim IoT Hub registriert sind|Keine Dimensionen|
|twinQueries.failure|Ja|Failed twin queries (Nicht erfolgreiche Zwillingsabfragen)|Anzahl|Gesamt|Gibt an, wie viele nicht erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|
|twinQueries.resultSize|Ja|Twin queries result size (Ergebnisgröße von Zwillingsabfragen)|Byte|Average|Durchschnitts-, Minimal- und Maximalwert der Ergebnisgröße aller erfolgreichen Zwillingsabfragen.|Keine Dimensionen|
|twinQueries.success|Ja|Successful twin queries (Erfolgreiche Zwillingsabfragen)|Anzahl|Gesamt|Gibt an, wie viele erfolgreiche Zwillingsabfragen durchgeführt wurden.|Keine Dimensionen|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AttestationAttempts|Ja|Nachweisversuche|Anzahl|Gesamt|Anzahl von versuchten Gerätenachweisen|ProvisioningServiceName, Status, Protokoll|
|DeviceAssignments|Ja|Zugewiesene Geräte|Anzahl|Gesamt|Anzahl von Geräten, die einem IoT-Hub zugewiesen sind|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Ja|Registrierungsversuche|Anzahl|Gesamt|Anzahl von versuchten Geräteregistrierungen|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AddRegion|Ja|Region hinzugefügt|Anzahl|Anzahl|Region hinzugefügt|Region|
|AutoscaleMaxThroughput|Nein|Autoskalierung – Maximaler Durchsatz|Anzahl|Maximum|Maximaler Durchsatz für Autoskalierung|DatabaseName, CollectionName|
|AvailableStorage|Nein|Verfügbarer Speicher (veraltet)|Byte|Gesamt|Die Metrik „Verfügbarer Speicher“ wird Ende September 2020 aus Azure Monitor entfernt. Die Speichergröße für Cosmos DB-Sammlungen ist jetzt unbegrenzt. Die einzige Einschränkung besteht darin, dass die Speichergröße für jeden logischen Partitionsschlüssel 20 GB beträgt. Sie können PartitionKeyStatistics im Diagnoseprotokoll aktivieren, um den Speicherverbrauch der wichtigsten Partitionsschlüssel zu ermitteln. Weitere Informationen zum Speicherkontingent für Cosmos DB finden Sie unter https://docs.microsoft.com/azure/cosmos-db/concepts-limits. Nach diesem Datum werden verbleibende Warnungsregeln, die noch für die veraltete Metrik definiert sind, automatisch deaktiviert.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Nein|Abschluss von Cassandra-Verbindungen|Anzahl|Gesamt|Anzahl von Cassandra-Verbindungen, die geschlossen wurden, gemeldet mit einer Granularität von einer Minute|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Nein|Cassandra-Connector – Durchschnittliche Replikationslatenz|Millisekunden|Average|Durchschnittliche Replikationslatenz im Cassandra-Connector|Keine Dimensionen|
|CassandraConnectorReplicationHealthStatus|Nein|Cassandra-Connector – Integritätsstatus|Anzahl|Anzahl|Integritätsstatus des Cassandra-Connectors|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|Nein|Cassandra-Keyspace erstellt|Anzahl|Anzahl|Cassandra-Keyspace erstellt|ResourceName, |
|CassandraKeyspaceDelete|Nein|Cassandra-Keyspace gelöscht|Anzahl|Anzahl|Cassandra-Keyspace gelöscht|ResourceName, |
|CassandraKeyspaceThroughputUpdate|Nein|Durchsatz eines Cassandra-Keyspace aktualisiert|Anzahl|Anzahl|Durchsatz eines Cassandra-Keyspace aktualisiert|ResourceName, |
|CassandraKeyspaceUpdate|Nein|Cassandra-Keyspace aktualisiert|Anzahl|Anzahl|Cassandra-Keyspace aktualisiert|ResourceName, |
|CassandraRequestCharges|Nein|Gebühren für Cassandra-Anforderungen|Anzahl|Gesamt|Genutzte Anforderungseinheiten für gesendete Cassandra-Anforderungen|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Nein|Cassandra-Anforderungen|Anzahl|Anzahl|Anzahl ausgeführter Cassandra-Anforderungen|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Nein|Cassandra-Tabelle erstellt|Anzahl|Anzahl|Cassandra-Tabelle erstellt|ResourceName, ChildResourceName, |
|CassandraTableDelete|Nein|Cassandra-Tabelle gelöscht|Anzahl|Anzahl|Cassandra-Tabelle gelöscht|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Nein|Durchsatz der Cassandra-Tabelle aktualisiert|Anzahl|Anzahl|Durchsatz der Cassandra-Tabelle aktualisiert|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Nein|Cassandra-Tabelle aktualisiert|Anzahl|Anzahl|Cassandra-Tabelle aktualisiert|ResourceName, ChildResourceName, |
|CreateAccount|Ja|Konto erstellt|Anzahl|Anzahl|Konto erstellt|Keine Dimensionen|
|DataUsage|Nein|Datennutzung|Byte|Gesamt|Gemeldete Gesamtdatennutzung mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|DeleteAccount|Ja|Konto gelöscht|Anzahl|Anzahl|Konto gelöscht|Keine Dimensionen|
|DocumentCount|Nein|Dokumentanzahl|Anzahl|Gesamt|Gemeldete Gesamtdokumentanzahl mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|DocumentQuota|Nein|Dokumentenkontingent|Byte|Gesamt|Gemeldetes Gesamtspeicherkontingent mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|GremlinDatabaseCreate|Nein|Gremlin-Datenbank erstellt|Anzahl|Anzahl|Gremlin-Datenbank erstellt|ResourceName, |
|GremlinDatabaseDelete|Nein|Gremlin-Datenbank gelöscht|Anzahl|Anzahl|Gremlin-Datenbank gelöscht|ResourceName, |
|GremlinDatabaseThroughputUpdate|Nein|Durchsatz der Gremlin-Datenbank aktualisiert|Anzahl|Anzahl|Durchsatz der Gremlin-Datenbank aktualisiert|ResourceName, |
|GremlinDatabaseUpdate|Nein|Gremlin-Datenbank aktualisiert|Anzahl|Anzahl|Gremlin-Datenbank aktualisiert|ResourceName, |
|GremlinGraphCreate|Nein|Gremlin-Diagramm erstellt|Anzahl|Anzahl|Gremlin-Diagramm erstellt|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Nein|Gremlin-Diagramm gelöscht|Anzahl|Anzahl|Gremlin-Diagramm gelöscht|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Nein|Durchsatz des Gremlin-Diagramms aktualisiert|Anzahl|Anzahl|Durchsatz des Gremlin-Diagramms aktualisiert|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Nein|Gremlin-Diagramm aktualisiert|Anzahl|Anzahl|Gremlin-Diagramm aktualisiert|ResourceName, ChildResourceName, |
|IndexUsage|Nein|Indexnutzung|Byte|Gesamt|Gemeldete Gesamtindexnutzung mit 5-Minuten-Granularität|CollectionName, DatabaseName, Region|
|MetadataRequests|Nein|Anforderungen von Metadaten|Anzahl|Anzahl|Anzahl der Metadatenanforderungen. Cosmos DB unterhält eine Sammlung von Systemmetadaten für jedes Konto, wodurch Sie Sammlungen, Datenbanken usw. und deren Konfigurationen ohne anfallende Kosten auflisten können.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionCreate|Nein|Mongo-Sammlung erstellt|Anzahl|Anzahl|Mongo-Sammlung erstellt|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Nein|Mongo-Sammlung gelöscht|Anzahl|Anzahl|Mongo-Sammlung gelöscht|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Nein|Durchsatz der Mongo-Sammlung aktualisiert|Anzahl|Anzahl|Durchsatz der Mongo-Sammlung aktualisiert|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Nein|Mongo-Sammlung aktualisiert|Anzahl|Anzahl|Mongo-Sammlung aktualisiert|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Nein|Mongo-Datenbank gelöscht|Anzahl|Anzahl|Mongo-Datenbank gelöscht|ResourceName, |
|MongoDatabaseThroughputUpdate|Nein|Durchsatz der Mongo-Datenbank aktualisiert|Anzahl|Anzahl|Durchsatz der Mongo-Datenbank aktualisiert|ResourceName, |
|MongoDBDatabaseCreate|Nein|Mongo-Datenbank erstellt|Anzahl|Anzahl|Mongo-Datenbank erstellt|ResourceName, |
|MongoDBDatabaseUpdate|Nein|Mongo-Datenbank aktualisiert|Anzahl|Anzahl|Mongo-Datenbank aktualisiert|ResourceName, |
|MongoRequestCharge|Ja|Kosten der Mongo-Anforderung|Anzahl|Gesamt|Verbrauchte Mongo-Anforderungseinheiten|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|Ja|Mongo-Anforderungen|Anzahl|Anzahl|Anzahl der ausgegebenen Mongo-Anforderungen|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|Nein|Mongo-Anforderungsrate|Anzahl pro Sekunde|Average|Anzahl von Mongo-Anforderungen pro Sekunde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|Nein|Mongo Delete Request Rate (Rate von Mongo-Löschanforderung)|Anzahl pro Sekunde|Average|Mongo-Löschanforderungen pro Sekunde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|Nein|Mongo Insert Request Rate (Rate von Mongo-Einfügeanforderung)|Anzahl pro Sekunde|Average|Anzahl von Mongo-Einfügungen pro Sekunde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|Nein|Mongo Query Request Rate (Rate von Mongo-Abfrageanforderung)|Anzahl pro Sekunde|Average|Mongo-Abfrageanforderungen pro Sekunde|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|Nein|Mongo-Aktualisierungsanforderungsrate|Anzahl pro Sekunde|Average|Mongo-Aktualisierungsanforderungen pro Sekunde|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|Nein|Normalisierter RU-Verbrauch|Percent|Maximum|Maximaler RU-Verbrauch in Prozent pro Minute|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|Nein|Bereitgestellter Durchsatz|Anzahl|Maximum|Bereitgestellter Durchsatz|DatabaseName, CollectionName|
|RegionFailover|Ja|Failover der Region|Anzahl|Anzahl|Failover der Region|Keine Dimensionen|
|RemoveRegion|Ja|Region entfernt|Anzahl|Anzahl|Region entfernt|Region|
|ReplicationLatency|Ja|P99-Replikationswartezeit|Millisekunden|Average|P99-Replikationswartezeit für Quell- und Zielregionen für geofähiges Konto|SourceRegion, TargetRegion|
|ServerSideLatency|Nein|Serverseitige Latenz|Millisekunden|Average|Serverseitige Latenz|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Nein|Dienstverfügbarkeit|Percent|Average|Verfügbarkeit der Kontoanforderungen mit einer Granularität von einer Stunde, einem Tag oder einem Monat|Keine Dimensionen|
|SqlContainerCreate|Nein|SQL-Container erstellt|Anzahl|Anzahl|SQL-Container erstellt|ResourceName, ChildResourceName, |
|SqlContainerDelete|Nein|SQL-Container gelöscht|Anzahl|Anzahl|SQL-Container gelöscht|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Nein|Durchsatz des SQL-Containers aktualisiert|Anzahl|Anzahl|Durchsatz des SQL-Containers aktualisiert|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Nein|SQL-Container aktualisiert|Anzahl|Anzahl|SQL-Container aktualisiert|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|Nein|SQL-Datenbank erstellt|Anzahl|Anzahl|SQL-Datenbank erstellt|ResourceName, |
|SqlDatabaseDelete|Nein|SQL-Datenbank gelöscht|Anzahl|Anzahl|SQL-Datenbank gelöscht|ResourceName, |
|SqlDatabaseThroughputUpdate|Nein|Durchsatz der SQL-Datenbank aktualisiert|Anzahl|Anzahl|Durchsatz der SQL-Datenbank aktualisiert|ResourceName, |
|SqlDatabaseUpdate|Nein|SQL-Datenbank aktualisiert|Anzahl|Anzahl|SQL-Datenbank aktualisiert|ResourceName, |
|TableTableCreate|Nein|AzureTable-Tabelle erstellt|Anzahl|Anzahl|AzureTable-Tabelle erstellt|ResourceName, |
|TableTableDelete|Nein|AzureTable-Tabelle gelöscht|Anzahl|Anzahl|AzureTable-Tabelle gelöscht|ResourceName, |
|TableTableThroughputUpdate|Nein|Durchsatz der AzureTable-Tabelle aktualisiert|Anzahl|Anzahl|Durchsatz der AzureTable-Tabelle aktualisiert|ResourceName, |
|TableTableUpdate|Nein|AzureTable-Tabelle aktualisiert|Anzahl|Anzahl|AzureTable-Tabelle aktualisiert|ResourceName, |
|TotalRequests|Ja|Anzahl von Anforderungen|Anzahl|Anzahl|Anzahl von gesendeten Anforderungen|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|Ja|Anforderungseinheiten gesamt|Anzahl|Gesamt|Verbrauchte Anforderungseinheiten|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|Ja|Kontoschlüssel aktualisiert|Anzahl|Anzahl|Kontoschlüssel aktualisiert|KeyType|
|UpdateAccountNetworkSettings|Ja|Netzwerkeinstellungen für Konto aktualisiert|Anzahl|Anzahl|Netzwerkeinstellungen für Konto aktualisiert|Keine Dimensionen|
|UpdateAccountReplicationSettings|Ja|Replikationseinstellungen für Konto aktualisiert|Anzahl|Anzahl|Replikationseinstellungen für Konto aktualisiert|Keine Dimensionen|
|UpdateDiagnosticsSettings|Nein|Diagnoseeinstellungen des Kontos aktualisiert|Anzahl|Anzahl|Diagnoseeinstellungen des Kontos aktualisiert|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|Topic, ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Thema|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|Keine Dimensionen|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|Keine Dimensionen|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|Keine Dimensionen|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|EventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|EventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Unzustellbare Ereignisse|Anzahl|Gesamt|Gesamtanzahl der unzustellbaren Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nein|Ereignisse mit Übermittlungsfehler|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die nicht an dieses Ereignisabonnement übermittelt werden konnten|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Übermittelte Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die an dieses Ereignisabonnement übermittelt wurden|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nein|Zielverarbeitungsdauer|Millisekunden|Average|Zielverarbeitungsdauer in Millisekunden|EventSubscriptionName|
|DroppedEventCount|Ja|Gelöschte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der gelöschten Ereignisse, die mit diesem Ereignisabonnement übereinstimmen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Übereinstimmende Ereignisse|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die diesem Ereignisabonnement zugeordnet sind|EventSubscriptionName|
|PublishFailCount|Ja|Ereignisse mit Veröffentlichungsfehler|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema fehlerhaft veröffentlichten Ereignisse|ErrorType, Error|
|PublishSuccessCount|Ja|Veröffentlichte Ereignisse|Anzahl|Gesamt|Gesamtanzahl der zu diesem Thema veröffentlichten Ereignisse|Keine Dimensionen|
|PublishSuccessLatencyInMs|Ja|Latenz erfolgreicher Veröffentlichungen|Millisekunden|Gesamt|Latenz erfolgreicher Veröffentlichungen in Millisekunden|Keine Dimensionen|
|UnmatchedEventCount|Ja|Ereignisse ohne Übereinstimmung|Anzahl|Gesamt|Gesamtzahl der Ereignisse, die mit keinem der Ereignisabonnements für dieses Thema übereinstimmen|Keine Dimensionen|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Average|Aktive Verbindungen gesamt für Microsoft.EventHub.|Keine Dimensionen|
|AvailableMemory|Nein|Verfügbarer Arbeitsspeicher|Percent|Maximum|Verfügbarer Arbeitsspeicher für den Event Hub-Cluster als Prozentsatz des Gesamtarbeitsspeichers.|Role|
|CaptureBacklog|Nein|Backlog erfassen.|Anzahl|Gesamt|Erfassen des Backlogs für Microsoft.EventHub.|Keine Dimensionen|
|CapturedBytes|Nein|Erfasste Bytes.|Byte|Gesamt|Erfasste Bytes für Microsoft.EventHub.|Keine Dimensionen|
|CapturedMessages|Nein|Erfasste Nachrichten.|Anzahl|Gesamt|Erfasste Nachrichten für Microsoft.EventHub.|Keine Dimensionen|
|ConnectionsClosed|Nein|Geschlossene Verbindungen.|Anzahl|Average|Geschlossene Verbindungen für Microsoft.EventHub.|Keine Dimensionen|
|ConnectionsOpened|Nein|Geöffnete Verbindungen.|Anzahl|Average|Geöffnete Verbindungen für Microsoft.EventHub.|Keine Dimensionen|
|CPU|Nein|CPU|Percent|Maximum|CPU-Auslastung für den Event Hub-Cluster in Prozent|Role|
|IncomingBytes|Ja|Eingehende Bytes.|Byte|Gesamt|Eingehende Bytes für Microsoft.EventHub.|Keine Dimensionen|
|IncomingMessages|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Eingehende Nachrichten für Microsoft.EventHub.|Keine Dimensionen|
|IncomingRequests|Ja|Eingehende Anforderungen|Anzahl|Gesamt|Eingehende Anforderungen für Microsoft.EventHub.|Keine Dimensionen|
|OutgoingBytes|Ja|Ausgehende Bytes.|Byte|Gesamt|Ausgehende Bytes für Microsoft.EventHub.|Keine Dimensionen|
|OutgoingMessages|Ja|Ausgehende Nachrichten|Anzahl|Gesamt|Ausgehende Nachrichten für Microsoft.EventHub.|Keine Dimensionen|
|QuotaExceededErrors|Nein|Fehler aufgrund von Kontingentüberschreitung.|Anzahl|Gesamt|Fehler aufgrund von Kontingentüberschreitung für Microsoft.EventHub.|Keine Dimensionen|
|ServerErrors|Nein|Serverfehler.|Anzahl|Gesamt|Serverfehler für Microsoft.EventHub.|Keine Dimensionen|
|Size|Nein|Size|Byte|Average|Größe eines EventHub in Bytes.|Role|
|SuccessfulRequests|Nein|Erfolgreiche Anforderungen|Anzahl|Gesamt|Erfolgreiche Anforderungen für Microsoft.EventHub.|Keine Dimensionen|
|ThrottledRequests|Nein|Gedrosselte Anforderungen.|Anzahl|Gesamt|Gedrosselte Anforderungen für Microsoft.EventHub.|Keine Dimensionen|
|UserErrors|Nein|Benutzerfehler.|Anzahl|Gesamt|Benutzerfehler für Microsoft.EventHub.|Keine Dimensionen|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Average|Aktive Verbindungen gesamt für Microsoft.EventHub.|Keine Dimensionen|
|CaptureBacklog|Nein|Backlog erfassen.|Anzahl|Gesamt|Erfassen des Backlogs für Microsoft.EventHub.|EntityName|
|CapturedBytes|Nein|Erfasste Bytes.|Byte|Gesamt|Erfasste Bytes für Microsoft.EventHub.|EntityName|
|CapturedMessages|Nein|Erfasste Nachrichten.|Anzahl|Gesamt|Erfasste Nachrichten für Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Nein|Geschlossene Verbindungen.|Anzahl|Average|Geschlossene Verbindungen für Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Nein|Geöffnete Verbindungen.|Anzahl|Average|Geöffnete Verbindungen für Microsoft.EventHub.|EntityName|
|EHABL|Ja|Archivierte Backlognachrichten (veraltet)|Anzahl|Gesamt|Event Hub-Archivnachrichten im Backlog für einen Namespace (veraltet)|Keine Dimensionen|
|EHAMBS|Ja|Durchsatz von Archivnachrichten (veraltet)|Byte|Gesamt|Event Hub-Durchsatz archivierter Nachrichten in einem Namespace (veraltet)|Keine Dimensionen|
|EHAMSGS|Ja|Archivnachrichten (veraltet)|Anzahl|Gesamt|Event Hub-Archivnachrichten in einem Namespace (veraltet)|Keine Dimensionen|
|EHINBYTES|Ja|Eingehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz eingehender Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHINMBS|Ja|Eingehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz eingehender Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für eingehende Bytes. (veraltet)|Keine Dimensionen|
|EHINMSGS|Ja|Eingehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von eingehenden Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHOUTBYTES|Ja|Ausgehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz ausgehender Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|EHOUTMBS|Ja|Ausgehende Bytes (veraltet)|Byte|Gesamt|Event Hub-Durchsatz ausgehender Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für ausgehende Bytes. (veraltet)|Keine Dimensionen|
|EHOUTMSGS|Ja|Ausgehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von ausgehenden Nachrichten für einen Namespace (veraltet)|Keine Dimensionen|
|FAILREQ|Ja|Fehlerhafte Anforderungen (veraltet)|Anzahl|Gesamt|Gesamtzahl fehlerhafter Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|IncomingBytes|Ja|Eingehende Bytes.|Byte|Gesamt|Eingehende Bytes für Microsoft.EventHub.|EntityName|
|IncomingMessages|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Eingehende Nachrichten für Microsoft.EventHub.|EntityName|
|IncomingRequests|Ja|Eingehende Anforderungen|Anzahl|Gesamt|Eingehende Anforderungen für Microsoft.EventHub.|EntityName|
|INMSGS|Ja|Eingehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von eingehenden Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für eingehende Nachrichten. (veraltet)|Keine Dimensionen|
|INREQS|Ja|Eingehende Anforderungen (veraltet)|Anzahl|Gesamt|Gesamtzahl eingehender Sendeanforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|INTERR|Ja|Interne Serverfehler (veraltet)|Anzahl|Gesamt|Gesamtzahl interner Serverfehler für einen Namespace (veraltet)|Keine Dimensionen|
|MISCERR|Ja|Andere Fehler (veraltet)|Anzahl|Gesamt|Gesamtzahl fehlerhafter Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|OutgoingBytes|Ja|Ausgehende Bytes.|Byte|Gesamt|Ausgehende Bytes für Microsoft.EventHub.|EntityName|
|OutgoingMessages|Ja|Ausgehende Nachrichten|Anzahl|Gesamt|Ausgehende Nachrichten für Microsoft.EventHub.|EntityName|
|OUTMSGS|Ja|Ausgehende Nachrichten (veraltet)|Anzahl|Gesamt|Gesamtzahl von ausgehenden Nachrichten für einen Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Metrik für ausgehende Nachrichten. (veraltet)|Keine Dimensionen|
|QuotaExceededErrors|Nein|Fehler aufgrund von Kontingentüberschreitung.|Anzahl|Gesamt|Fehler aufgrund von Kontingentüberschreitung für Microsoft.EventHub.|EntityName, |
|ServerErrors|Nein|Serverfehler.|Anzahl|Gesamt|Serverfehler für Microsoft.EventHub.|EntityName, |
|Size|Nein|Size|Byte|Average|Größe eines EventHub in Bytes.|EntityName|
|SuccessfulRequests|Nein|Erfolgreiche Anforderungen|Anzahl|Gesamt|Erfolgreiche Anforderungen für Microsoft.EventHub.|EntityName, |
|SUCCREQ|Ja|Erfolgreiche Anforderungen (veraltet)|Anzahl|Gesamt|Gesamtzahl erfolgreicher Anforderungen für einen Namespace (veraltet)|Keine Dimensionen|
|SVRBSY|Ja|Fehler durch ausgelasteten Server (veraltet)|Anzahl|Gesamt|Gesamtzahl der Fehler durch ausgelasteten Server für einen Namespace (veraltet)|Keine Dimensionen|
|ThrottledRequests|Nein|Gedrosselte Anforderungen.|Anzahl|Gesamt|Gedrosselte Anforderungen für Microsoft.EventHub.|EntityName, |
|UserErrors|Nein|Benutzerfehler.|Anzahl|Gesamt|Benutzerfehler für Microsoft.EventHub.|EntityName, |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Ja|Kategorisierte Gatewayanforderungen|Anzahl|Gesamt|Anzahl von Gatewayanforderungen nach Kategorien (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Ja|Gatewayanforderungen|Anzahl|Gesamt|Anzahl von Gatewayanforderungen|HttpStatus|
|NumActiveWorkers|Ja|Anzahl aktiver Worker|Anzahl|Maximum|Anzahl aktiver Worker|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|MetricThreshold|Ja|Schwellenwert der Metrik|Anzahl|Average|Der bei der Ausführung der automatischen Skalierung konfigurierte Schwellenwert für die automatische Skalierung.|MetricTriggerRule|
|ObservedCapacity|Ja|Beobachtete Kapazität|Anzahl|Average|Die Kapazität, die beim Ausführen der automatischen Skalierung gemeldet wurde.|Keine Dimensionen|
|ObservedMetricValue|Ja|Beobachteter Metrikwert|Anzahl|Average|Der Wert, der beim Ausführen der automatischen Skalierung berechnet wurde.|MetricTriggerSource|
|ScaleActionsInitiated|Ja|Initiierte Skalierungsaktionen|Anzahl|Gesamt|Die Richtung des Skalierungsvorgangs.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Ja|Verfügbarkeit|Percent|Average|Prozentsatz erfolgreich abgeschlossener Verfügbarkeitstests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Nein|Verfügbarkeitstests|Anzahl|Anzahl|Anzahl von Verfügbarkeitstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Ja|Dauer von Verfügbarkeitstests|Millisekunden|Average|Dauer von Verfügbarkeitstests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Ja|Netzwerkverbindungszeit zum Laden der Seite|Millisekunden|Average|Zeit zwischen Benutzeranforderung und Netzwerkverbindung. Umfasst DNS-Suche und Transportverbindung.|Keine Dimensionen|
|browserTimings/processingDuration|Ja|Clientverarbeitungszeit|Millisekunden|Average|Zeit zwischen dem Empfang des letzten Byte eines Dokuments und dem Laden des DOM. Asynchrone Anforderungen werden möglicherweise immer noch verarbeitet.|Keine Dimensionen|
|browserTimings/receiveDuration|Ja|Empfängt Antwortzeit|Millisekunden|Average|Zeit zwischen dem ersten und letzten Byte, oder bis zur Verbindungstrennung.|Keine Dimensionen|
|browserTimings/sendDuration|Ja|Anforderungszeit senden|Millisekunden|Average|Zeit zwischen der Netzwerkverbindung und dem Empfang des ersten Byte.|Keine Dimensionen|
|browserTimings/totalDuration|Ja|Browser-Seitenladezeit|Millisekunden|Average|Zeit ab der Benutzeranforderung, bis DOM, Stylesheets, Skripts und Bilder geladen werden.|Keine Dimensionen|
|dependencies/count|Nein|Abhängigkeitsaufrufe|Anzahl|Anzahl|Die Anzahl der von der Anwendung an externe Ressourcen ausgeführten Aufrufe.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Ja|Dauer der Abhängigkeit|Millisekunden|Average|Die Dauer der von der Anwendung an externe Ressourcen ausgeführten Aufrufe.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Nein|Fehler bei Abhängigkeitsaufrufen|Anzahl|Anzahl|Anzahl fehlerhafter Abhängigkeitsaufrufe von der Anwendung an externe Ressourcen.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|exceptions/browser|Nein|Browserausnahmen|Anzahl|Anzahl|Anzahl nicht erfasster Ausnahmen, die im Browser ausgelöst wurden.|cloud/roleName|
|exceptions/count|Ja|Ausnahmen|Anzahl|Anzahl|Die kombinierte Anzahl nicht abgefangener Ausnahmen.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|Nein|Serverausnahmen|Anzahl|Anzahl|Anzahl nicht erfasster Ausnahmen, die in der Serveranwendung ausgelöst wurden.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Ja|Seitenaufrufe|Anzahl|Anzahl|Anzahl der Seitenaufrufe|operation/synthetic, cloud/roleName|
|pageViews/duration|Ja|Ladezeit der Seitenansicht|Millisekunden|Average|Ladezeit der Seitenansicht|operation/synthetic, cloud/roleName|
|performanceCounters/exceptionsPerSecond|Ja|Ausnahmerate|Anzahl pro Sekunde|Average|Die Anzahl der behandelten Ausnahmen und Ausnahmefehler, die an Windows gemeldet werden, einschließlich .NET-Ausnahmen und nicht verwalteten Ausnahmen, die in .NET-Ausnahmen konvertiert werden.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Ja|Verfügbarer Arbeitsspeicher|Byte|Average|Physischer Speicher ist sofort für die Zuordnung zu einem Prozess oder für die Systemnutzung verfügbar.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Ja|Prozess-CPU|Percent|Average|Der Prozentsatz der verstrichenen Zeit für alle Prozessthreads, die den Prozessor zur Ausführung von Anweisungen verwendet haben. Dies kann zwischen 0 und 100 variieren. Diese Metrik gibt ausschließlich die Leistung des w3wp-Prozesses an.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Ja|E/A-Rate für Prozess|Bytes pro Sekunde|Average|Gesamtanzahl von pro Sekunde in Dateien, im Netzwerk und auf Geräten gelesenen und geschriebenen Bytes.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Ja|Prozessorzeit|Percent|Average|Der Prozentsatz der Zeit, die der Prozessor nicht im Leerlauf in Threads verbringt.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Ja|Private Bytes für Prozess|Byte|Average|Speicher, der exklusiv den überwachten Anwendungsprozessen zugewiesen ist.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Ja|Ausführungszeit der HTTP-Anforderung|Millisekunden|Average|Ausführungszeit der aktuellen Anforderung.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Ja|HTTP-Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Länge der Anwendungsanforderungswarteschleife.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Ja|HTTP-Anforderungsrate|Anzahl pro Sekunde|Average|Rate aller Anforderungen an die Anwendung pro Sekunde von ASP.NET.|cloud/roleInstance|
|requests/count|Nein|Serveranforderungen|Anzahl|Anzahl|Anzahl der abgeschlossenen HTTP-Anforderungen.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|Ja|Serverantwortzeit|Millisekunden|Average|Zeit zwischen dem Empfang einer HTTP-Anforderung und dem Abschluss des Sendevorgangs der Antwort.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Nein|Anforderungsfehler|Anzahl|Anzahl|Anzahl der als fehlerhaft markierten HTTP-Anforderungen. In den meisten Fällen sind dies Anforderungen mit dem Antwortcode >= 400 und ungleich 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Nein|Serveranforderungsrate|Anzahl pro Sekunde|Average|Rate der Serveranforderungen pro Sekunde|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Ja|Traces|Anzahl|Anzahl|Dokumentanzahl nachverfolgen|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|c2d.property.read.failure|Ja|Fehlerhafte Lesevorgänge für Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftslesevorgänge, die über IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.read.success|Ja|Erfolgreiche Lesevorgänge für Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftslesevorgänge, die über IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.update.failure|Ja|Fehlerhafte Aktualisierungen von Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftsaktualisierungen, die über IoT Central initiiert wurden|Keine Dimensionen|
|c2d.property.update.success|Ja|Erfolgreiche Aktualisierungen von Geräteeigenschaften über IoT Central|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftsaktualisierungen, die über IoT Central initiiert wurden|Keine Dimensionen|
|connectedDeviceCount|Nein|Gesamtzahl der verbundenen Geräte|Anzahl|Average|Anzahl von Geräten, die mit IoT Central verbunden sind|Keine Dimensionen|
|d2c.property.read.failure|Ja|Fehlerhafte Lesevorgänge für Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftslesevorgänge, die von Geräten initiiert wurden|Keine Dimensionen|
|d2c.property.read.success|Ja|Erfolgreiche Lesevorgänge für Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftslesevorgänge, die von Geräten initiiert wurden|Keine Dimensionen|
|d2c.property.update.failure|Ja|Fehlerhafte Aktualisierungen von Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller fehlerhaften Eigenschaftsaktualisierungen, die über Geräte initiiert wurden|Keine Dimensionen|
|d2c.property.update.success|Ja|Erfolgreiche Aktualisierungen von Geräteeigenschaften über Geräte|Anzahl|Gesamt|Die Anzahl aller erfolgreichen Eigenschaftsaktualisierungen, die über Geräte initiiert wurden|Keine Dimensionen|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Tresorverfügbarkeit insgesamt|Percent|Average|Verfügbarkeit von Tresoranforderungen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Nein|Tresorauslastung insgesamt|Percent|Average|Verwendete Tresorkapazität|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Ja|Dienst-API-Treffer, gesamt|Anzahl|Anzahl|Gesamtanzahl der Dienst-API-Treffer|ActivityType, ActivityName|
|ServiceApiLatency|Ja|Gesamtwartezeit für Dienst-API|Millisekunden|Average|Gesamtwartezeit für Dienst-API-Anforderungen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Ja|Dienst-API-Ergebnisse, gesamt|Anzahl|Anzahl|Gesamtanzahl der Dienst-API-Ergebnisse|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BatchBlobCount|Ja|Batchblob – Anzahl|Anzahl|Average|Anzahl der Datenquellen in einem aggregierten Batch für die Erfassung|Datenbank|
|BatchDuration|Ja|Batchdauer|Sekunden|Average|Dauer der Aggregationsphase im Erfassungsflow|Datenbank|
|BatchesProcessed|Ja|Verarbeitete Batches|Anzahl|Average|Anzahl der für die Erfassung aggregierten Batches Grund für Batchabschluss: Batchverarbeitungszeit oder Limit für Datengröße/Dateianzahl in Batchrichtlinie erreicht|Database, SealReason|
|BatchSize|Ja|Batchgröße|Byte|Average|Nicht komprimierte erwartete Datengröße in einem aggregierten Batch für die Erfassung|Datenbank|
|CacheUtilization|Ja|Cacheauslastung|Percent|Average|Auslastungsgrad im Clusterbereich|Keine Dimensionen|
|ContinuousExportMaxLatenessMinutes|Ja|Fortlaufender Export – maximale Verzögerung|Anzahl|Maximum|Die Verzögerung (in Minuten), die von Aufträgen für den fortlaufenden Export im Cluster gemeldet wurde|Keine Dimensionen|
|ContinuousExportNumOfRecordsExported|Ja|Fortlaufender Export – Anzahl der exportierten Datensätze|Anzahl|Gesamt|Anzahl der exportierten Datensätze, ausgelöst für jedes Speicherartefakt, das während des Exportvorgangs geschrieben wird|ContinuousExportName, Database|
|ContinuousExportPendingCount|Ja|ContinuousExportPendingCount|Anzahl|Maximum|Die Anzahl ausstehender fortlaufender Exportaufträge, die zur Ausführung bereit sind.|Keine Dimensionen|
|ContinuousExportResult|Ja|Ergebnis des fortlaufenden Exports|Anzahl|Anzahl|Gibt an, ob der fortlaufende Export erfolgreich war|ContinuousExportName, Result, Database|
|CPU|Ja|CPU|Percent|Average|CPU-Auslastungsgrad|Keine Dimensionen|
|EventsProcessedForEventHubs|Ja|Verarbeitete Ereignisse (für Event/IoT Hub)|Anzahl|Gesamt|Anzahl der vom Cluster verarbeiteten Ereignisse beim Erfassen aus Event/IoT Hub|EventStatus|
|ExportUtilization|Ja|Exportauslastung|Percent|Maximum|Exportauslastung|Keine Dimensionen|
|IngestionLatencyInSeconds|Ja|Erfassungslatenz (in Sekunden)|Sekunden|Average|Erfassungszeit von der Quelle (z. B. Nachricht in EventHub) zum Cluster in Sekunden|Keine Dimensionen|
|IngestionResult|Ja|Ergebnis der Datenerfassung|Anzahl|Anzahl|Anzahl der Erfassungsvorgänge|IngestionResultDetails|
|IngestionUtilization|Ja|Datenerfassungsauslastung|Percent|Average|Verhältnis der verwendeten Datenerfassungsslots im Cluster|Keine Dimensionen|
|IngestionVolumeInMB|Ja|Datenerfassungsvolumen (in MB)|Anzahl|Gesamt|Gesamtvolumen der im Cluster erfassten Daten (in MB)|Keine Dimensionen|
|InstanceCount|Ja|Anzahl der Instanzen|Anzahl|Average|Gesamtzahl der Instanzen|Keine Dimensionen|
|KeepAlive|Ja|Keep-Alive|Anzahl|Average|Die Integritätsprüfung zeigt an, dass der Cluster auf Abfragen reagiert.|Keine Dimensionen|
|QueryDuration|Ja|Abfragedauer|Millisekunden|Average|Abfragedauer in Sekunden|QueryStatus|
|SteamingIngestRequestRate|Ja|Anforderungsrate der Streamingerfassung|Anzahl|RateRequestsPerSecond|Anforderungsrate der Streamingerfassung (Anforderungen pro Sekunde)|Keine Dimensionen|
|StreamingIngestDataRate|Ja|Datenrate der Streamingerfassung|Anzahl|Average|Datenrate der Streamingerfassung (MB pro Sekunde)|Keine Dimensionen|
|StreamingIngestDuration|Ja|Dauer der Streamingerfassung|Millisekunden|Average|Dauer der Streamingerfassung in Millisekunden|Keine Dimensionen|
|StreamingIngestResults|Ja|Ergebnis der Streamingerfassung|Anzahl|Average|Ergebnis der Streamingerfassung|Ergebnis|
|TotalNumberOfConcurrentQueries|Ja|Gesamtanzahl gleichzeitiger Abfragen|Anzahl|Gesamt|Gesamtanzahl gleichzeitiger Abfragen|Keine Dimensionen|
|TotalNumberOfExtents|Ja|Gesamtanzahl von Erweiterungen|Anzahl|Gesamt|Gesamtanzahl von Datenerweiterungen|Keine Dimensionen|
|TotalNumberOfThrottledCommands|Ja|Gesamtanzahl gedrosselter Befehle|Anzahl|Gesamt|Gesamtanzahl gedrosselter Befehle|CommandType|
|TotalNumberOfThrottledQueries|Ja|Gesamtanzahl gedrosselter Abfragen|Anzahl|Gesamt|Gesamtanzahl gedrosselter Abfragen|Keine Dimensionen|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActionLatency|Ja|Aktionslatenz |Sekunden|Average|Latenz abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsCompleted|Ja|Abgeschlossene Aktionen |Anzahl|Gesamt|Anzahl abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsFailed|Ja|Aktionsfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowaktionen.|Keine Dimensionen|
|ActionsSkipped|Ja|Übersprungene Aktionen |Anzahl|Gesamt|Anzahl übersprungener Workflowaktionen.|Keine Dimensionen|
|ActionsStarted|Ja|Gestartete Aktionen |Anzahl|Gesamt|Anzahl gestarteter Workflowaktionen.|Keine Dimensionen|
|ActionsSucceeded|Ja|Erfolgreiche Aktionen |Anzahl|Gesamt|Anzahl erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionSuccessLatency|Ja|Latenz erfolgreicher Aktionen |Sekunden|Average|Latenz erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionThrottledEvents|Ja|Ereignisse zu gedrosselten Aktionen|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen.|Keine Dimensionen|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Ja|Connector-Arbeitsspeichernutzung für Integrationsdienstumgebung|Percent|Average|Connector-Arbeitsspeichernutzung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Ja|Connector-Prozessorauslastung für Integrationsdienstumgebung|Percent|Average|Connector-Prozessorauslastung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Ja|Workflow-Arbeitsspeichernutzung für Integrationsdienstumgebung|Percent|Average|Workflow-Arbeitsspeichernutzung für Integrationsdienstumgebung|Keine Dimensionen|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Ja|Workflow-Prozessorauslastung für Integrationsdienstumgebung|Percent|Average|Workflow-Prozessorauslastung für Integrationsdienstumgebung|Keine Dimensionen|
|RunFailurePercentage|Ja|Prozentsatz der Ausführungsfehler|Percent|Gesamt|Prozentsatz fehlerhafter Workflowausführungen|Keine Dimensionen|
|RunLatency|Ja|Ausführungslatenz|Sekunden|Average|Latenz abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsCancelled|Ja|Abgebrochene Ausführungen|Anzahl|Gesamt|Anzahl abgebrochener Workflowausführungen.|Keine Dimensionen|
|RunsCompleted|Ja|Abgeschlossene Ausführungen|Anzahl|Gesamt|Anzahl abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsFailed|Ja|Ausführungsfehler|Anzahl|Gesamt|Anzahl fehlerhafter Workflowausführungen.|Keine Dimensionen|
|RunsStarted|Ja|Gestartete Ausführungen|Anzahl|Gesamt|Anzahl gestarteter Workflowausführungen.|Keine Dimensionen|
|RunsSucceeded|Ja|Erfolgreiche Ausführungen|Anzahl|Gesamt|Anzahl erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunStartThrottledEvents|Ja|Ereignisse zu gedrosselten Ausführungsstarts|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflow-Ausführungsstarts.|Keine Dimensionen|
|RunSuccessLatency|Ja|Latenz erfolgreicher Ausführungen|Sekunden|Average|Latenz erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunThrottledEvents|Ja|Ereignisse zu gedrosselten Ausführungen|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen oder Triggern.|Keine Dimensionen|
|TriggerFireLatency|Ja|Latenz beim Auslösen von Triggern |Sekunden|Average|Latenz ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerLatency|Ja|Triggerlatenz |Sekunden|Average|Latenz abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersCompleted|Ja|Abgeschlossene Trigger |Anzahl|Gesamt|Anzahl abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersFailed|Ja|Triggerfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowtrigger.|Keine Dimensionen|
|TriggersFired|Ja|Ausgelöste Trigger |Anzahl|Gesamt|Anzahl ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggersSkipped|Ja|Übersprungene Trigger|Anzahl|Gesamt|Anzahl übersprungener Workflowtrigger.|Keine Dimensionen|
|TriggersStarted|Ja|Gestartete Trigger |Anzahl|Gesamt|Anzahl gestarteter Workflowtrigger.|Keine Dimensionen|
|TriggersSucceeded|Ja|Erfolgreiche Trigger |Anzahl|Gesamt|Anzahl erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerSuccessLatency|Ja|Latenz erfolgreicher Trigger |Sekunden|Average|Latenz erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerThrottledEvents|Ja|Ereignisse zu gedrosselten Triggern|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowtriggern.|Keine Dimensionen|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActionLatency|Ja|Aktionslatenz |Sekunden|Average|Latenz abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsCompleted|Ja|Abgeschlossene Aktionen |Anzahl|Gesamt|Anzahl abgeschlossener Workflowaktionen.|Keine Dimensionen|
|ActionsFailed|Ja|Aktionsfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowaktionen.|Keine Dimensionen|
|ActionsSkipped|Ja|Übersprungene Aktionen |Anzahl|Gesamt|Anzahl übersprungener Workflowaktionen.|Keine Dimensionen|
|ActionsStarted|Ja|Gestartete Aktionen |Anzahl|Gesamt|Anzahl gestarteter Workflowaktionen.|Keine Dimensionen|
|ActionsSucceeded|Ja|Erfolgreiche Aktionen |Anzahl|Gesamt|Anzahl erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionSuccessLatency|Ja|Latenz erfolgreicher Aktionen |Sekunden|Average|Latenz erfolgreicher Workflowaktionen.|Keine Dimensionen|
|ActionThrottledEvents|Ja|Ereignisse zu gedrosselten Aktionen|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen.|Keine Dimensionen|
|BillableActionExecutions|Ja|Ausführungen abrechenbarer Aktionen|Anzahl|Gesamt|Anzahl der Workflowaktionsausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|BillableTriggerExecutions|Ja|Ausführungen abrechenbarer Trigger|Anzahl|Gesamt|Anzahl der Workflowtriggerausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|BillingUsageNativeOperation|Ja|Nutzungsabrechnung für Ausführungen nativer Vorgänge|Anzahl|Gesamt|Anzahl von Ausführungen nativer Vorgänge, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStandardConnector|Ja|Nutzungsabrechnung für Ausführungen standardmäßiger Connectors|Anzahl|Gesamt|Anzahl von Ausführungen standardmäßiger Connectors, die in Rechnung gestellt werden.|Keine Dimensionen|
|BillingUsageStorageConsumption|Ja|Nutzungsabrechnung für Ausführungen mit Speicherverbrauch|Anzahl|Gesamt|Anzahl von Ausführungen mit Speicherverbrauch, die in Rechnung gestellt werden.|Keine Dimensionen|
|RunFailurePercentage|Ja|Prozentsatz der Ausführungsfehler|Percent|Gesamt|Prozentsatz fehlerhafter Workflowausführungen|Keine Dimensionen|
|RunLatency|Ja|Ausführungslatenz|Sekunden|Average|Latenz abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsCancelled|Ja|Abgebrochene Ausführungen|Anzahl|Gesamt|Anzahl abgebrochener Workflowausführungen.|Keine Dimensionen|
|RunsCompleted|Ja|Abgeschlossene Ausführungen|Anzahl|Gesamt|Anzahl abgeschlossener Workflowausführungen.|Keine Dimensionen|
|RunsFailed|Ja|Ausführungsfehler|Anzahl|Gesamt|Anzahl fehlerhafter Workflowausführungen.|Keine Dimensionen|
|RunsStarted|Ja|Gestartete Ausführungen|Anzahl|Gesamt|Anzahl gestarteter Workflowausführungen.|Keine Dimensionen|
|RunsSucceeded|Ja|Erfolgreiche Ausführungen|Anzahl|Gesamt|Anzahl erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunStartThrottledEvents|Ja|Ereignisse zu gedrosselten Ausführungsstarts|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflow-Ausführungsstarts.|Keine Dimensionen|
|RunSuccessLatency|Ja|Latenz erfolgreicher Ausführungen|Sekunden|Average|Latenz erfolgreicher Workflowausführungen.|Keine Dimensionen|
|RunThrottledEvents|Ja|Ereignisse zu gedrosselten Ausführungen|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowaktionen oder Triggern.|Keine Dimensionen|
|TotalBillableExecutions|Ja|Gesamtzahl der abrechenbaren Ausführungen|Anzahl|Gesamt|Anzahl der Workflowausführungen, die in Rechnung gestellt werden|Keine Dimensionen|
|TriggerFireLatency|Ja|Latenz beim Auslösen von Triggern |Sekunden|Average|Latenz ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggerLatency|Ja|Triggerlatenz |Sekunden|Average|Latenz abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersCompleted|Ja|Abgeschlossene Trigger |Anzahl|Gesamt|Anzahl abgeschlossener Workflowtrigger.|Keine Dimensionen|
|TriggersFailed|Ja|Triggerfehler |Anzahl|Gesamt|Anzahl fehlerhafter Workflowtrigger.|Keine Dimensionen|
|TriggersFired|Ja|Ausgelöste Trigger |Anzahl|Gesamt|Anzahl ausgelöster Workflowtrigger.|Keine Dimensionen|
|TriggersSkipped|Ja|Übersprungene Trigger|Anzahl|Gesamt|Anzahl übersprungener Workflowtrigger.|Keine Dimensionen|
|TriggersStarted|Ja|Gestartete Trigger |Anzahl|Gesamt|Anzahl gestarteter Workflowtrigger.|Keine Dimensionen|
|TriggersSucceeded|Ja|Erfolgreiche Trigger |Anzahl|Gesamt|Anzahl erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerSuccessLatency|Ja|Latenz erfolgreicher Trigger |Sekunden|Average|Latenz erfolgreicher Workflowtrigger.|Keine Dimensionen|
|TriggerThrottledEvents|Ja|Ereignisse zu gedrosselten Triggern|Anzahl|Gesamt|Anzahl von Ereignissen zu gedrosselten Workflowtriggern.|Keine Dimensionen|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Aktive Kerne|Ja|Aktive Kerne|Anzahl|Average|Gesamtanzahl der aktiven Kerne|Scenario, ClusterName|
|Aktive Knoten|Ja|Aktive Knoten|Anzahl|Average|Anzahl der aktiven Knoten. Dabei handelt es sich um die Knoten, auf denen aktiv ein Auftrag ausgeführt wird.|Scenario, ClusterName|
|Ausführungen mit angefordertem Abbruch|Ja|Ausführungen mit angefordertem Abbruch|Anzahl|Gesamt|Anzahl von Ausführungen, bei denen ein Abbruch für diesen Arbeitsbereich angefordert wurde – die Anzahl wird aktualisiert, wenn eine Abbruchanforderung für eine Ausführung empfangen wurde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Abgebrochene Ausführungen|Ja|Abgebrochene Ausführungen|Anzahl|Gesamt|Anzahl abgebrochener Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung erfolgreich abgebrochen wurde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Abgeschlossene Ausführungen|Ja|Abgeschlossene Ausführungen|Anzahl|Gesamt|Anzahl erfolgreich abgeschlossener Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung abgeschlossen und die Ausgabe erfasst wurde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilization|Ja|CpuUtilization|Anzahl|Average|Arbeitsspeicherauslastung auf einem CPU-Knoten in Prozent – die Auslastung wird in Intervallen von einer Minute gemeldet.|Scenario, runId, NodeId, ClusterName|
|Errors|Ja|Errors|Anzahl|Gesamt|Anzahl von Ausführungsfehlern in diesem Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei der Ausführung ein Fehler auftritt.|Szenario|
|Fehlerhafte Ausführungen|Ja|Fehlerhafte Ausführungen|Anzahl|Gesamt|Anzahl fehlerhafter Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei einer Ausführung ein Fehler auftritt.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Ausführungen, die abgeschlossen werden|Ja|Ausführungen, die abgeschlossen werden|Anzahl|Gesamt|Anzahl von Ausführungen, die für diesen Arbeitsbereich abgeschlossen wurden – die Anzahl wird aktualisiert, wenn eine Ausführung abgeschlossen wurde, aber die Ausgabe noch erfasst wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilization|Ja|GpuUtilization|Anzahl|Average|Arbeitsspeicherauslastung auf einem GPU-Knoten in Prozent – die Auslastung wird in Intervallen von einer Minute gemeldet.|Scenario, runId, NodeId, DeviceId, ClusterName|
|Kerne im Leerlauf|Ja|Kerne im Leerlauf|Anzahl|Average|Gesamtanzahl der Kerne im Leerlauf|Scenario, ClusterName|
|Knoten im Leerlauf|Ja|Knoten im Leerlauf|Anzahl|Average|Anzahl von Knoten im Leerlauf. Knoten im Leerlauf sind die Knoten, auf denen keine Aufträge ausgeführt werden, die aber einen neuen Auftrag bei Verfügbarkeit akzeptieren können.|Scenario, ClusterName|
|Ausscheidende Kerne|Ja|Ausscheidende Kerne|Anzahl|Average|Anzahl der ausscheidenden Kerne|Scenario, ClusterName|
|Ausscheidende Knoten|Ja|Ausscheidende Knoten|Anzahl|Average|Anzahl der ausscheidenden Knoten. Ausscheidende Knoten sind die Knoten, auf denen die Verarbeitung eines Auftrags gerade abgeschlossen wurde und die in den Leerlaufzustand wechseln.|Scenario, ClusterName|
|Model Deploy Failed (Fehler bei der Modellimplementierung)|Ja|Model Deploy Failed (Fehler bei der Modellimplementierung)|Anzahl|Gesamt|Anzahl der fehlerhaften Modellimplementierungen in diesem Arbeitsbereich|Scenario, StatusCode|
|Gestartete Modellimplementierungen|Ja|Gestartete Modellimplementierungen|Anzahl|Gesamt|Anzahl der gestarteten Modellimplementierungen in diesem Arbeitsbereich|Szenario|
|Erfolgreiche Modellimplementierungen|Ja|Erfolgreiche Modellimplementierungen|Anzahl|Gesamt|Anzahl der erfolgreichen Modellimplementierungen in diesem Arbeitsbereich|Szenario|
|Fehler bei der Modellregistrierung|Ja|Fehler bei der Modellregistrierung|Anzahl|Gesamt|Anzahl der fehlerhaften Modellregistrierungen in diesem Arbeitsbereich|Scenario, StatusCode|
|Erfolgreiche Modellregistrierungen|Ja|Erfolgreiche Modellregistrierungen|Anzahl|Gesamt|Anzahl der erfolgreichen Modellregistrierungen in diesem Arbeitsbereich|Szenario|
|Ausführungen ohne Rückmeldung|Ja|Ausführungen ohne Rückmeldung|Anzahl|Gesamt|Anzahl nicht reagierender Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung zum Status „Keine Rückmeldung“ übergeht.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Nicht gestartete Ausführungen|Ja|Nicht gestartete Ausführungen|Anzahl|Gesamt|Anzahl von Ausführungen mit Status „Nicht gestartet“ für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Anforderung zur Initiierung einer Ausführung eingeht, aber die Ausführungsinformationen nicht aufgefüllt wurden. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Vorzeitig entfernte Kerne|Ja|Vorzeitig entfernte Kerne|Anzahl|Average|Anzahl der vorzeitig entfernten Kerne|Scenario, ClusterName|
|Vorzeitig entfernte Knoten|Ja|Vorzeitig entfernte Knoten|Anzahl|Average|Anzahl der vorzeitig entfernten Knoten. Dabei handelt es sich um die Knoten mit niedriger Priorität, die aus dem Pool verfügbarer Knoten entfernt werden.|Scenario, ClusterName|
|Ausführungen in Vorbereitung|Ja|Ausführungen in Vorbereitung|Anzahl|Gesamt|Anzahl von Ausführungen, die für diesen Arbeitsbereich vorbereitet werden – die Anzahl wird aktualisiert, wenn eine Ausführung in den Status „Wird vorbereitet“ übergeht, während die Ausführungsumgebung vorbereitet wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Ausführungen in Bereitstellung|Ja|Ausführungen in Bereitstellung|Anzahl|Gesamt|Anzahl von Ausführungen, die sich für diesen Arbeitsbereich in Bereitstellung befinden – die Anzahl wird aktualisiert, wenn eine Ausführung darauf wartet, dass ein Computeziel erstellt oder bereitgestellt wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Ausführungen in Warteschlange|Ja|Ausführungen in Warteschlange|Anzahl|Gesamt|Anzahl von Ausführungen, die sich für diesen Arbeitsbereich in der Warteschlange befinden – die Anzahl wird aktualisiert, wenn eine Ausführung im Computeziel in die Warteschlange eingereiht wird. Dieser Fall kann eintreten, wenn darauf gewartet wird, dass erforderliche Computeknoten bereit sind.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Ja|Quota Utilization Percentage (Prozentsatz der Kontingentnutzung)|Anzahl|Average|Prozentualer Anteil der genutzten Kontingente|Scenario, ClusterName, VmFamilyName, VmPriority|
|Gestartete Ausführungen|Ja|Gestartete Ausführungen|Anzahl|Gesamt|Anzahl von Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, wenn eine Ausführung mit den erforderlichen Ressourcen gestartet wird.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Ausführungen, die gestartet werden|Ja|Ausführungen, die gestartet werden|Anzahl|Gesamt|Anzahl gestarteter Ausführungen für diesen Arbeitsbereich – die Anzahl wird aktualisiert, nachdem eine Anforderung zur Initiierung der Ausführung eingegangen ist und die Ausführungsinformationen (z. B. runId) aufgefüllt wurden.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Kerne insgesamt|Ja|Kerne insgesamt|Anzahl|Average|Gesamtanzahl von Kernen|Scenario, ClusterName|
|Knoten insgesamt|Ja|Knoten insgesamt|Anzahl|Average|Gesamtanzahl von Knoten. Diese Summe umfasst Teile von aktiven Knoten, Leerlaufknoten, nicht verwendbaren Knoten, vorzeitig entfernten Knoten, ausscheidenden Knoten.|Scenario, ClusterName|
|Nicht verwendbare Kerne|Ja|Nicht verwendbare Kerne|Anzahl|Average|Anzahl der nicht verwendbaren Kerne|Scenario, ClusterName|
|Unusable Nodes (Nicht verwendbare Knoten)|Ja|Unusable Nodes (Nicht verwendbare Knoten)|Anzahl|Average|Anzahl nicht verwendbarer Knoten. Nicht verwendbare Knoten sind aufgrund eines nicht auflösbaren Problems nicht funktionsfähig. Azure recycelt diese Knoten.|Scenario, ClusterName|
|Warnungen|Ja|Warnungen|Anzahl|Gesamt|Anzahl von Ausführungswarnungen in diesem Arbeitsbereich – die Anzahl wird aktualisiert, wenn bei einer Ausführung eine Warnung auftritt.|Szenario|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Verfügbarkeit der APIs|ApiCategory, ApiName|
|Verwendung|Nein|Verwendung|Anzahl|Anzahl|Anzahl von API-Aufrufen|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AssetCount|Ja|Anzahl der Medienobjekte|Anzahl|Average|Anzahl der bereits im aktuellen Media Services-Konto erstellten Ressourcen|Keine Dimensionen|
|AssetQuota|Ja|Kontingent der Medienobjekte|Anzahl|Average|Anzahl der zulässigen Ressourcen für das aktuelle Media Services-Konto|Keine Dimensionen|
|AssetQuotaUsedPercentage|Ja|Prozentsatz des verwendeten Kontingents der Medienobjekte|Percent|Average|Prozentualer Anteil der verwendeten Ressourcen im aktuellen Media Services-Konto|Keine Dimensionen|
|ContentKeyPolicyCount|Ja|Anzahl der Richtlinien für Inhaltsschlüssel|Anzahl|Average|Anzahl der bereits im aktuellen Media Services-Konto erstellten Inhaltsschlüssel-Richtlinien|Keine Dimensionen|
|ContentKeyPolicyQuota|Ja|Kontingent der Richtlinien für Inhaltsschlüssel|Anzahl|Average|Anzahl der zulässigen Inhaltsschlüssel-Richtlinien für das aktuelle Media Services-Konto|Keine Dimensionen|
|ContentKeyPolicyQuotaUsedPercentage|Ja|Prozentsatz des Kontingents der Richtlinien für Inhaltsschlüssel|Percent|Average|Prozentualer Anteil der verwendeten Inhaltsschlüssel-Richtlinien im aktuellen Media Services-Konto|Keine Dimensionen|
|StreamingPolicyCount|Ja|Anzahl der Streamingrichtlinien|Anzahl|Average|Anzahl der im aktuellen Media Services-Konto bereits erstellten Streamingrichtlinien|Keine Dimensionen|
|StreamingPolicyQuota|Ja|Kontingent der Streamingrichtlinien|Anzahl|Average|Anzahl der zulässigen Streamingrichtlinien für das aktuelle Media Services-Konto|Keine Dimensionen|
|StreamingPolicyQuotaUsedPercentage|Ja|Prozentsatz des verwendeten Kontingents der Streamingrichtlinien|Percent|Average|Prozentualer Anteil der verwendeten Streamingrichtlinien im aktuellen Media Services-Konto|Keine Dimensionen|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Menge der Ausgangsdaten in Byte.|OutputFormat|
|Requests|Ja|Requests|Anzahl|Gesamt|Anforderungen an einen Streamingendpunkt|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Ja|End-to-End-Wartezeit bei Erfolg|Millisekunden|Average|Durchschnittliche Latenz für erfolgreiche Anforderungen in Millisekunden.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Ja|Zugeordnete Poolgröße|Byte|Average|Bereitgestellte Poolgröße|Keine Dimensionen|
|VolumePoolAllocatedUsed|Ja|Zu Volumegröße zugeordneter Pool|Byte|Average|Zugeordnete verwendete Größe des Pools|Keine Dimensionen|
|VolumePoolTotalLogicalSize|Ja|Vom Pool genutzte Größe|Byte|Average|Summe der logischen Größe aller zum Pool gehörenden Volumes|Keine Dimensionen|
|VolumePoolTotalSnapshotSize|Ja|Gesamtgröße der Momentaufnahme für den Pool|Byte|Average|Summe der Momentaufnahmegrößen aller Volumes in diesem Pool|Keine Dimensionen|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageReadLatency|Ja|Durchschnittliche Wartezeit beim Lesevorgang|Millisekunden|Average|Durchschnittliche Wartezeit beim Lesen in Millisekunden pro Vorgang|Keine Dimensionen|
|AverageWriteLatency|Ja|Durchschnittliche Wartezeit beim Schreibvorgang|Millisekunden|Average|Durchschnittliche Wartezeit beim Schreiben in Millisekunden pro Vorgang|Keine Dimensionen|
|CbsVolumeBackupActive|Ja|Volumesicherung – Status „Aktiv“|Anzahl|Average|Gibt an, ob die Sicherung für das Volume derzeit angehalten ist|Keine Dimensionen|
|CbsVolumeLogicalBackupBytes|Ja|Gesicherte logische Bytes|Byte|Average|Insgesamt nicht komprimierte/verschlüsselte gesicherte Bytes für dieses Volume|Keine Dimensionen|
|CbsVolumeOperationComplete|Ja|Vorgangsstatus|Anzahl|Average|Gibt an, ob der letzte Sicherungs-/Wiederherstellungsvorgang erfolgreich war|Keine Dimensionen|
|CbsVolumeOperationTransferredBytes|Ja|Für den Vorgang übertragene Bytes|Byte|Average|Insgesamt übertragene Bytes für den letzten Sicherungs-/Wiederherstellungsvorgang|Keine Dimensionen|
|CbsVolumeProtected|Ja|Volumestatus „Geschützt“|Anzahl|Average|Gibt an, ob das Volume durch den Cloudsicherungsdienst geschützt wird|Keine Dimensionen|
|ReadIops|Ja|Lese-IOPS|Anzahl pro Sekunde|Average|E/A-Vorgänge lesen pro Sekunde|Keine Dimensionen|
|VolumeAllocatedSize|Ja|Zugeordnete Größe des Volume|Byte|Average|Die bereitgestellte Größe eines Volumes|Keine Dimensionen|
|VolumeLogicalSize|Ja|Vom Volume genutzte Größe|Byte|Average|Logische Größe des Volume (verwendete Bytes)|Keine Dimensionen|
|VolumeSnapshotSize|Ja|Größe der Volumenmomentaufnahme|Byte|Average|Größe aller Momentaufnahmen im Volumen|Keine Dimensionen|
|WriteIops|Ja|Schreib-IOPS|Anzahl pro Sekunde|Average|E/A-Vorgänge schreiben pro Sekunde|Keine Dimensionen|
|XregionReplicationHealthy|Ja|Gibt an, ob der Volumereplikationsstatus „Fehlerfrei“ lautet|Anzahl|Average|Beziehungsbedingung: 1 oder 0|Keine Dimensionen|
|XregionReplicationLagTime|Ja|Verzögerungszeit bei der Volumereplikation|Sekunden|Average|Die Zeit in Sekunden, um die die Daten in der Spiegelung im Vergleich zur Quelle verzögert sind|Keine Dimensionen|
|XregionReplicationLastTransferDuration|Ja|Übertragungsdauer der letzten Volumereplikation|Sekunden|Average|Die Dauer der letzten Übertragung in Sekunden|Keine Dimensionen|
|XregionReplicationLastTransferSize|Ja|Übertragungsgröße der letzten Volumereplikation|Byte|Average|Bei der letzten Übertragung insgesamt übertragene Bytes|Keine Dimensionen|
|XregionReplicationRelationshipProgress|Ja|Fortschritt der Volumereplikation|Byte|Average|Insgesamt beim aktuellen Übertragungsvorgang übertragene Daten|Keine Dimensionen|
|XregionReplicationRelationshipTransferring|Ja|Gibt an, ob die Übertragung der Volumereplikation ausgeführt wird|Anzahl|Average|Gibt an, ob der Status der Volumereplikation „Wird übertragen“ lautet|Keine Dimensionen|
|XregionReplicationTotalTransferBytes|Ja|Insgesamt bei der Volumereplikation übertragene Bytes|Byte|Average|Kumulative für die Beziehung übertragene Bytes|Keine Dimensionen|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Nein|Application Gateway-Gesamtzeit|Millisekunden|Average|Durchschnittliche Zeit, bis eine Anforderung verarbeitet und die zugehörige Antwort gesendet wurde. Dies wird berechnet als durchschnittliches Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt, zu dem der Vorgang zum Senden der Antwort abgeschlossen ist. Beachten Sie, dass dies in der Regel die Application Gateway-Verarbeitungszeit, die Zeit für das Durchlaufen des Netzwerks durch die Anforderungs- und Antwortpakete und die Zeit bis zur Antwort des Back-End-Servers einschließt.|Listener|
|AvgRequestCountPerHealthyHost|Nein|Anforderungen pro Minute pro fehlerfreiem Host|Anzahl|Average|Durchschnittliche Anzahl von Anforderungen pro Minute pro fehlerfreiem Back-End-Host in einem Pool|BackendSettingsPool|
|BackendConnectTime|Nein|Back-End-Verbindungszeit|Millisekunden|Average|Zeitaufwand für das Herstellen einer Verbindung mit einem Back-End-Server|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Nein|Antwortzeit für erstes Byte des Back-Ends|Millisekunden|Average|Das Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des ersten Bytes des Antwortheaders und somit eine Abschätzung der Verarbeitungszeit des Back-End-Servers|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Nein|Antwortzeit für letztes Byte des Back-Ends|Millisekunden|Average|Das Zeitintervall zwischen dem Herstellen einer Verbindung mit dem Back-End-Server und dem Empfang des letzten Bytes des Antworttexts|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Ja|Back-End-Antwortstatus|Anzahl|Gesamt|Anzahl der von den Back-End-Membern generierten HTTP-Antwortcodes. Dies schließt nicht die von Application Gateway generierten Antwortcodes ein.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Ja|Regelverteilung für blockierte Web Application Firewall-Anforderungen|Anzahl|Gesamt|Regelverteilung für blockierte Web Application Firewall-Anforderungen|RuleGroup, RuleId|
|BlockedReqCount|Ja|Anzahl blockierter Web Application Firewall-Anforderungen|Anzahl|Gesamt|Anzahl blockierter Web Application Firewall-Anforderungen|Keine Dimensionen|
|BytesReceived|Ja|Empfangene Bytes|Byte|Gesamt|Gesamtanzahl der von Application Gateway von den Clients empfangenen Bytes|Listener|
|BytesSent|Ja|Gesendete Bytes|Byte|Gesamt|Gesamtanzahl der von Application Gateway an die Clients gesendeten Bytes|Listener|
|CapacityUnits|Nein|Aktuelle Kapazitätseinheiten|Anzahl|Average|Verbrauchte Kapazitätseinheiten|Keine Dimensionen|
|ClientRtt|Nein|Client-RTT|Millisekunden|Average|Durchschnittliche Roundtripzeit zwischen Clients und Application Gateway. Diese Metrik gibt an, wie lange es dauert, Verbindungen herzustellen und Bestätigungen zurückzugeben.|Listener|
|ComputeUnits|Nein|Aktuelle Compute-Einheiten|Anzahl|Average|Verbrauchte Compute-Einheiten|Keine Dimensionen|
|CpuUtilization|Nein|CPU-Auslastung|Percent|Average|Aktuelle CPU-Auslastung der Application Gateway-Instanz|Keine Dimensionen|
|CurrentConnections|Ja|Aktuelle Verbindungen|Anzahl|Gesamt|Anzahl von aktuellen Verbindungen mit Application Gateway|Keine Dimensionen|
|EstimatedBilledCapacityUnits|Nein|Geschätzte abgerechnete Kapazitätseinheiten|Anzahl|Average|Geschätzte Kapazitätseinheiten, die in Rechnung gestellt werden|Keine Dimensionen|
|FailedRequests|Ja|Anforderungsfehler|Anzahl|Gesamt|Anzahl von fehlerhaften Anforderungen über Application Gateway|BackendSettingsPool|
|FixedBillableCapacityUnits|Nein|Feste abrechenbare Kapazitätseinheiten|Anzahl|Average|Mindestkapazitätseinheiten, die in Rechnung gestellt werden|Keine Dimensionen|
|HealthyHostCount|Ja|Anzahl von fehlerfreien Hosts|Anzahl|Average|Anzahl von fehlerfreien Back-End-Hosts|BackendSettingsPool|
|MatchedCount|Ja|Gesamtregelverteilung in Web Application Firewall|Anzahl|Gesamt|Gesamtregelverteilung in Web Application Firewall für eingehenden Datenverkehr|RuleGroup, RuleId|
|NewConnectionsPerSecond|Nein|Neue Verbindungen pro Sekunde|Anzahl pro Sekunde|Average|Neue Verbindungen, die pro Sekunde mit Application Gateway hergestellt werden|Keine Dimensionen|
|ResponseStatus|Ja|Antwortstatus|Anzahl|Gesamt|Von Application Gateway zurückgegebener HTTP-Antwortstatus|HttpStatusGroup|
|Throughput|Nein|Throughput|Bytes pro Sekunde|Average|Anzahl von Bytes pro Sekunde, die das Application Gateway bereitgestellt hat|Keine Dimensionen|
|TlsProtocol|Ja|Client-TLS-Protokoll|Anzahl|Gesamt|Anzahl von TLS- und Nicht-TLS-Anforderungen, die von dem Client initiiert wurden, der eine Verbindung mit Application Gateway hergestellt hat. Um die Verteilung des TLS-Protokolls anzuzeigen, filtern Sie nach dem Dimensions-TLS-Protokoll.|Listener, TlsProtocol|
|TotalRequests|Ja|Anzahl von Anforderungen|Anzahl|Gesamt|Anzahl von erfolgreichen Anforderungen über Application Gateway|BackendSettingsPool|
|UnhealthyHostCount|Ja|Anzahl von fehlerhaften Hosts|Anzahl|Average|Anzahl von fehlerhaften Back-End-Hosts|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Ja|Trefferanzahl für Anwendungsregeln|Anzahl|Gesamt|Häufigkeit, mit der Anwendungsregeln aufgerufen wurden|Status, Grund, Protokoll|
|DataProcessed|Ja|Verarbeitete Datenmenge|Byte|Gesamt|Gesamtmenge der von dieser Firewall verarbeiteten Daten|Keine Dimensionen|
|FirewallHealth|Ja|Integritätszustand der Firewall|Percent|Average|Gibt die Gesamtintegrität dieser Firewall an|Status, Ursache|
|NetworkRuleHit|Ja|Trefferanzahl für Netzwerkregeln|Anzahl|Gesamt|Häufigkeit, mit der Netzwerkregeln aufgerufen wurden|Status, Grund, Protokoll|
|SNATPortUtilization|Ja|SNAT-Portnutzung|Percent|Average|Derzeit verwendete ausgehende SNAT-Ports in Prozent|Protocol|
|Throughput|Nein|Throughput|BitsPerSecond|Average|Von dieser Firewall verarbeiteter Durchsatz|Keine Dimensionen|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Ja|BitsInPerSecond|Anzahl pro Sekunde|Average|In Azure eingehende Bits pro Sekunde|Keine Dimensionen|
|BitsOutPerSecond|Ja|BitsOutPerSecond|Anzahl pro Sekunde|Average|Aus Azure ausgehende Bits pro Sekunde|Keine Dimensionen|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|QueryVolume|Ja|Abfragevolume|Anzahl|Gesamt|Anzahl von Abfragen für eine DNS-Zone|Keine Dimensionen|
|RecordSetCapacityUtilization|Nein|Kapazitätsauslastung von Datensatzgruppen|Percent|Maximum|Von einer DNS-Zone genutzte Kapazität einer Datensatzgruppe in Prozent|Keine Dimensionen|
|RecordSetCount|Ja|Anzahl von Datensatzgruppen|Anzahl|Maximum|Anzahl von Datensatzgruppen in einer DNS-Zone|Keine Dimensionen|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ArpAvailability|Ja|ARP-Verfügbarkeit|Percent|Average|ARP-Verfügbarkeit von MSEE für alle Peers.|PeeringType, Peer|
|BgpAvailability|Ja|BGP-Verfügbarkeit|Percent|Average|BGP-Verfügbarkeit von MSEE für alle Peers.|PeeringType, Peer|
|BitsInPerSecond|Nein|BitsInPerSecond|Anzahl pro Sekunde|Average|In Azure eingehende Bits pro Sekunde|PeeringType|
|BitsOutPerSecond|Nein|BitsOutPerSecond|Anzahl pro Sekunde|Average|Aus Azure ausgehende Bits pro Sekunde|PeeringType|
|GlobalReachBitsInPerSecond|Nein|GlobalReachBitsInPerSecond|Anzahl pro Sekunde|Average|In Azure eingehende Bits pro Sekunde|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Nein|GlobalReachBitsOutPerSecond|Anzahl pro Sekunde|Average|Aus Azure ausgehende Bits pro Sekunde|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Nein|DroppedInBitsPerSecond|Anzahl pro Sekunde|Average|Verworfene Dateneingangsbits pro Sekunde|Keine Dimensionen|
|QosDropBitsOutPerSecond|Nein|DroppedOutBitsPerSecond|Anzahl pro Sekunde|Average|Verworfene Datenausgangsbits pro Sekunde|Keine Dimensionen|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Ja|BitsInPerSecond|Anzahl pro Sekunde|Average|In Azure eingehende Bits pro Sekunde|Keine Dimensionen|
|BitsOutPerSecond|Ja|BitsOutPerSecond|Anzahl pro Sekunde|Average|Aus Azure ausgehende Bits pro Sekunde|Keine Dimensionen|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Nein|BitsInPerSecond|Anzahl pro Sekunde|Average|In Azure eingehende Bits pro Sekunde|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Nein|BitsOutPerSecond|Anzahl pro Sekunde|Average|Aus Azure ausgehende Bits pro Sekunde|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdminState|Ja|AdminState|Anzahl|Average|Administratorstatus des Ports|Link|
|LineProtocol|Ja|LineProtocol|Anzahl|Average|Zeilenprotokollstatus des Ports|Link|
|PortBitsInPerSecond|Ja|BitsInPerSecond|Anzahl pro Sekunde|Average|In Azure eingehende Bits pro Sekunde|Link|
|PortBitsOutPerSecond|Ja|BitsOutPerSecond|Anzahl pro Sekunde|Average|Aus Azure ausgehende Bits pro Sekunde|Link|
|RxLightLevel|Ja|RxLightLevel|Anzahl|Average|Empfangener Lichtpegel in dBm|Link, Lane|
|TxLightLevel|Ja|TxLightLevel|Anzahl|Average|Gesendeter Lichtpegel in dBm|Link, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Ja|Prozentsatz der Back-End-Integrität|Percent|Average|Der Prozentsatz der erfolgreichen Integritätstests vom HTTP/S-Proxy zu Back-Ends|Backend, BackendPool|
|BackendRequestCount|Ja|Back-End-Anforderungsanzahl|Anzahl|Gesamt|Die Anzahl der vom HTTP/S-Proxy an Back-Ends gesendeten Anforderungen|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Ja|Latenz der Back-End-Anforderung|Millisekunden|Average|Die Zeitspanne zwischen dem Senden der Anforderung durch den HTTP/S-Proxy an das Back-End und dem Empfangen des letzten Antwortbytes durch den HTTP/S-Proxy vom Back-End|Back-End|
|BillableResponseSize|Ja|Größe von abrechnungsfähigen Antworten|Byte|Gesamt|Die Anzahl der abrechnungsfähigen Bytes (mindestens 2 KB pro Anforderung), die als Antworten vom HTTP/S-Proxy an Clients gesendet wurden.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Ja|Anzahl der Anforderungen|Anzahl|Gesamt|Die Anzahl der vom HTTP/S-Proxy bereitgestellten Clientanforderungen|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Ja|Anforderungsgröße|Byte|Gesamt|Die Anzahl der von Clients als Anforderungen an den HTTP/S-Proxy gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Ja|Antwortgröße|Byte|Gesamt|Die Anzahl der vom HTTP/S-Proxy als Antworten an Clients gesendeten Bytes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Ja|Gesamtlatenz|Millisekunden|Average|Die Zeitspanne zwischen dem Empfang der Clientanforderung durch den HTTP/S-Proxy und der Bestätigung des letzten Antwortbytes vom HTTP/S-Proxy durch den Client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Ja|Anforderungsanzahl für die Web Application Firewall|Anzahl|Gesamt|Die Anzahl der von der Web Application Firewall verarbeiteten Clientanforderungen|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Nein|Zugeordnete SNAT-Ports|Anzahl|Average|Gesamtanzahl von SNAT-Ports, die im Zeitraum zugeordnet wurden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Ja|Byteanzahl|Anzahl|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Ja|Integritätsteststatus|Anzahl|Average|Durchschnittlicher Load Balancer-Integritätsteststatus pro Zeitdauer|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Ja|Paketzahl|Anzahl|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Ja|Anzahl von SNAT-Verbindungen|Anzahl|Gesamt|Gesamtanzahl von neuen SNAT-Verbindungen, die innerhalb des Zeitraums erstellt wurden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Ja|SYN-Anzahl|Anzahl|Gesamt|Gesamtanzahl von SYN-Paketen, die im Zeitraum übertragen wurden|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|Nein|Verwendete SNAT-Ports|Anzahl|Average|Gesamtanzahl von SNAT-Ports, die im Zeitraum verwendet wurden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Ja|Datenpfadverfügbarkeit|Anzahl|Average|Durchschnittliche Load Balancer-Datenpfadverfügbarkeit pro Zeitdauer|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Ja|Empfangene Bytes|Byte|Gesamt|Anzahl der durch die Netzwerkschnittstelle empfangenen Bytes|Keine Dimensionen|
|BytesSentRate|Ja|Gesendete Bytes|Byte|Gesamt|Anzahl der durch die Netzwerkschnittstelle gesendeten Bytes|Keine Dimensionen|
|PacketsReceivedRate|Ja|Empfangene Pakete|Anzahl|Gesamt|Anzahl der durch die Netzwerkschnittstelle empfangenen Pakete|Keine Dimensionen|
|PacketsSentRate|Ja|Gesendete Pakete|Anzahl|Gesamt|Anzahl der durch die Netzwerkschnittstelle gesendeten Pakete|Keine Dimensionen|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Ja|Durchschn. Roundtripzeit (ms)|Millisekunden|Average|Durchschnittliche Netzwerk-Roundtripzeit (ms) für die Konnektivitätsüberwachung von Testdatenverkehr, der zwischen Quelle und Ziel gesendet wurde.|Keine Dimensionen|
|ChecksFailedPercent|Ja|Fehler bei Überprüfungen in Prozent (Vorschau)|Percent|Average|Fehlerhafte Tests bei Konnektivitätsüberwachung in Prozent|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Ja|Fehlerhafte Tests in Prozent|Percent|Average|Fehlerhafte Tests bei Konnektivitätsüberwachung in Prozent|Keine Dimensionen|
|RoundTripTimeMs|Ja|Roundtripzeit (ms) (Vorschau)|Millisekunden|Average|Roundtripzeit in Millisekunden für die Tests bei Konnektivitätsüberwachung|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ByteCount|Ja|Byteanzahl|Anzahl|Gesamt|Gesamtanzahl von Bytes, die im Zeitraum übertragen wurden|Port, Richtung|
|BytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte Bytes|Keine Dimensionen|
|BytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Bytes|Keine Dimensionen|
|BytesInDDoS|Ja|Als DDoS eingehende Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende Bytes|Keine Dimensionen|
|DDoSTriggerSYNPackets|Ja|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende SYN-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|DDoSTriggerTCPPackets|Ja|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende TCP-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|DDoSTriggerUDPPackets|Ja|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Anzahl pro Sekunde|Maximum|Eingehende UDP-Pakete, um die DDoS-Entschärfung auszulösen|Keine Dimensionen|
|IfUnderDDoSAttack|Ja|Unter DDoS-Angriff oder nicht|Anzahl|Maximum|Unter DDoS-Angriff oder nicht|Keine Dimensionen|
|PacketCount|Ja|Paketzahl|Anzahl|Gesamt|Gesamtanzahl von Paketen, die im Zeitraum übertragen wurden|Port, Richtung|
|PacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte Pakete|Keine Dimensionen|
|PacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende Pakete|Keine Dimensionen|
|PacketsInDDoS|Ja|Als DDoS eingehende Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende Pakete|Keine Dimensionen|
|SynCount|Ja|SYN-Anzahl|Anzahl|Gesamt|Gesamtanzahl von SYN-Paketen, die im Zeitraum übertragen wurden|Port, Richtung|
|TCPBytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Bytes|Keine Dimensionen|
|TCPBytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Bytes|Keine Dimensionen|
|TCPBytesInDDoS|Ja|Als DDoS eingehende TCP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende TCP-Bytes|Keine Dimensionen|
|TCPPacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte TCP-Pakete|Keine Dimensionen|
|TCPPacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende TCP-Pakete|Keine Dimensionen|
|TCPPacketsInDDoS|Ja|Als DDoS eingehende TCP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende TCP-Pakete|Keine Dimensionen|
|UDPBytesDroppedDDoS|Ja|Als DDoS eingehende gelöschte UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Bytes|Keine Dimensionen|
|UDPBytesForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Bytes|Keine Dimensionen|
|UDPBytesInDDoS|Ja|Als DDoS eingehende UDP-Bytes|Bytes pro Sekunde|Maximum|Als DDoS eingehende UDP-Bytes|Keine Dimensionen|
|UDPPacketsDroppedDDoS|Ja|Als DDoS eingehende gelöschte UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende gelöschte UDP-Pakete|Keine Dimensionen|
|UDPPacketsForwardedDDoS|Ja|Weitergeleitete als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Weitergeleitete als DDoS eingehende UDP-Pakete|Keine Dimensionen|
|UDPPacketsInDDoS|Ja|Als DDoS eingehende UDP-Pakete|Anzahl pro Sekunde|Maximum|Als DDoS eingehende UDP-Pakete|Keine Dimensionen|
|VipAvailability|Ja|Datenpfadverfügbarkeit|Anzahl|Average|Durchschnittliche Verfügbarkeit der IP-Adresse pro Zeitdauer|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Ja|Endpunktstatus nach Endpunkt|Anzahl|Maximum|1, wenn der Teststatus des Endpunkts „Aktiviert“ ist, andernfalls 0.|EndpointName|
|QpsByEndpoint|Ja|Abfragen nach zurückgegebenem Endpunkt|Anzahl|Gesamt|Häufigkeit, mit der ein Endpunkt des Traffic Managers innerhalb des angegebenen Zeitrahmens zurückgegeben wurde|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageBandwidth|Ja|S2S-Gatewaybandbreite|Bytes pro Sekunde|Average|Durchschnittliche Site-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Keine Dimensionen|
|P2SBandwidth|Ja|P2S-Gatewaybandbreite|Bytes pro Sekunde|Average|Durchschnittliche Point-to-Site-Bandbreite eines Gateways in Bytes pro Sekunde|Keine Dimensionen|
|P2SConnectionCount|Ja|Anzahl der P2S-Verbindungen|Anzahl|Maximum|Anzahl der Point-to-Site-Verbindung eines Gateways|Protocol|
|TunnelAverageBandwidth|Ja|Bandbreite des Tunnels|Bytes pro Sekunde|Average|Durchschnittliche Bandbreite eines Tunnels in Bytes pro Sekunde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Ja|Vom Tunnel ausgehende Bytes|Byte|Gesamt|Ausgehende Bytes eines Tunnels|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Ja|Ausgehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Anzahl|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten ausgehenden Pakete|ConnectionName, RemoteIP|
|TunnelEgressPackets|Ja|Vom Tunnel ausgehende Pakete|Anzahl|Gesamt|Ausgehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Ja|In Tunnel eingehende Bytes|Byte|Gesamt|Eingehende Bytes eines Tunnels|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Ja|Eingehende gelöschte Pakete eines Tunnels durch einen TS-Konflikt|Anzahl|Gesamt|Anzahl der durch einen Konflikt mit dem Datenverkehrsselektor eines Tunnels gelöschten eingehenden Pakete|ConnectionName, RemoteIP|
|TunnelIngressPackets|Ja|In Tunnel eingehende Pakete|Anzahl|Gesamt|Eingehende Paketanzahl für einen Tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Ja|Roundtripzeit für Pings an eine VM|Millisekunden|Average|Roundtripzeit für an eine Ziel-VM gesendete Pings|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Ja|Fehlerhafte Pings an einen virtuellen Computer|Percent|Average|Prozentualer Anteil der Anzahl fehlerhafter Pings an der Gesamtanzahl der gesendeten Pings einer Ziel-VM|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|incoming|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Übermittlungs-API-Aufrufen an. |Keine Dimensionen|
|incoming.all.failedrequests|Ja|Alle eingehenden fehlerhaften Anforderungen|Anzahl|Gesamt|Gesamtzahl eingehender fehlerhafter Anforderungen für einen Notification Hub|Keine Dimensionen|
|incoming.all.requests|Ja|Alle eingehenden Anforderungen|Anzahl|Gesamt|Gesamtzahl eingehender Anforderungen für einen Notification Hub|Keine Dimensionen|
|incoming.scheduled|Ja|Geplante Pushbenachrichtigungen (gesendet)|Anzahl|Gesamt|Geplante Pushbenachrichtigungen (abgebrochen)|Keine Dimensionen|
|incoming.scheduled.cancel|Ja|Geplante Pushbenachrichtigungen (abgebrochen)|Anzahl|Gesamt|Geplante Pushbenachrichtigungen (abgebrochen)|Keine Dimensionen|
|installation.all|Ja|Installationsverwaltungsvorgänge|Anzahl|Gesamt|Installationsverwaltungsvorgänge|Keine Dimensionen|
|installation.delete|Ja|Installationsvorgänge löschen|Anzahl|Gesamt|Installationsvorgänge löschen|Keine Dimensionen|
|installation.get|Ja|Installationsvorgänge abrufen|Anzahl|Gesamt|Installationsvorgänge abrufen|Keine Dimensionen|
|installation.patch|Ja|Patchinstallationsvorgänge|Anzahl|Gesamt|Patchinstallationsvorgänge|Keine Dimensionen|
|installation.upsert|Ja|Installationsvorgänge erstellen oder aktualisieren|Anzahl|Gesamt|Installationsvorgänge erstellen oder aktualisieren|Keine Dimensionen|
|notificationhub.pushes|Ja|Alle ausgehenden Benachrichtigungen|Anzahl|Gesamt|Alle ausgehenden Benachrichtigungen des Benachrichtigungs-Hub|Keine Dimensionen|
|outgoing.allpns.badorexpiredchannel|Ja|Kanalfehler (unzulässig oder abgelaufen)|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der Kanal, das Token oder die Registrierungs-ID in der Registrierung abgelaufen oder ungültig war.|Keine Dimensionen|
|outgoing.allpns.channelerror|Ja|Kanalfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der Kanal ungültig, nicht der richtigen App zugeordnet, gedrosselt oder abgelaufen war.|Keine Dimensionen|
|outgoing.allpns.invalidpayload|Ja|Nutzlastfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil vom PNS ein Nutzlastfehler zurückgegeben wurde.|Keine Dimensionen|
|outgoing.allpns.pnserror|Ja|Fehler des externen Benachrichtigungssystems|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil ein Problem bei der Kommunikation mit dem PNS vorlag (ausschließlich Authentifizierungsprobleme).|Keine Dimensionen|
|outgoing.allpns.success|Ja|Erfolgreiche Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.apns.badchannel|Ja|APNS-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das Token ungültig ist (APNS-Statuscode: 8).|Keine Dimensionen|
|outgoing.apns.expiredchannel|Ja|APNS-Fehler durch abgelaufenen Kanal|Anzahl|Gesamt|Die Anzahl von Token, die aufgrund des APNS-Feedbackkanals ungültig gemacht wurden.|Keine Dimensionen|
|outgoing.apns.invalidcredentials|Ja|APNS-Autorisierungsfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.apns.invalidnotificationsize|Ja|APNS-Fehler durch ungültige Benachrichtigungsgröße|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast zu groß war (APNS-Statuscode: 7).|Keine Dimensionen|
|outgoing.apns.pnserror|Ja|APNS-Fehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit APNS aufgetreten sind.|Keine Dimensionen|
|outgoing.apns.success|Ja|Erfolgreiche APNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.gcm.authenticationerror|Ja|GCM-Authentifizierungsfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die angegebenen Anmeldeinformationen vom PNS nicht akzeptiert wurden, die Anmeldeinformationen blockiert wurden oder die SenderId in der App nicht richtig konfiguriert wurde (GCM-Ergebnis: MismatchedSenderId).|Keine Dimensionen|
|outgoing.gcm.badchannel|Ja|GCM-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung nicht erkannt wurde (GCM-Ergebnis: Ungültige Registrierung).|Keine Dimensionen|
|outgoing.gcm.expiredchannel|Ja|GCM-Fehler durch abgelaufenen Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung abgelaufen war (GCM-Ergebnis: NotRegistered).|Keine Dimensionen|
|outgoing.gcm.invalidcredentials|Ja|GCM-Autorisierungsfehler (ungültige Anmeldeinformationen)|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.gcm.invalidnotificationformat|Ja|Ungültiges GCM-Benachrichtigungsformat|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast nicht richtig formatiert war (GCM-Ergebnis: InvalidDataKey oder InvalidTtl).|Keine Dimensionen|
|outgoing.gcm.invalidnotificationsize|Ja|GCM-Fehler durch ungültige Benachrichtigungsgröße|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast zu groß war (GCM-Ergebnis: MessageTooBig).|Keine Dimensionen|
|outgoing.gcm.pnserror|Ja|GCM-Fehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit GCM aufgetreten sind.|Keine Dimensionen|
|outgoing.gcm.success|Ja|Erfolgreiche GCM-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.gcm.throttled|Ja|Gedrosselte GCM-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App von GCM gedrosselt wurde (GCM-Statuscode: 501 - 599 oder result:Unavailable).|Keine Dimensionen|
|outgoing.gcm.wrongchannel|Ja|GCM-Fehler durch falschen Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die registrationId in der Registrierung nicht der aktuellen App zugeordnet war (GCM-Ergebnis: InvalidPackageName).|Keine Dimensionen|
|outgoing.mpns.authenticationerror|Ja|MPNS-Authentifizierungsfehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.mpns.badchannel|Ja|MPNS-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der ChannelURI in der Registrierung nicht erkannt wurde (MPNS-Status: 404 – Nicht gefunden).|Keine Dimensionen|
|outgoing.mpns.channeldisconnected|Ja|MPNS-Kanal nicht verbunden|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Verbindung für den ChannelURI in der Registrierung getrennt wurde (MPNS-Status: 412 – Nicht gefunden).|Keine Dimensionen|
|outgoing.mpns.dropped|Ja|Verworfene MPNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die von MPNS verworfen wurden (MPNS-Antwortheader: X-NotificationStatus: QueueFull oder Suppressed).|Keine Dimensionen|
|outgoing.mpns.invalidcredentials|Ja|Ungültige MPNS-Anmeldeinformationen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden.|Keine Dimensionen|
|outgoing.mpns.invalidnotificationformat|Ja|Ungültiges MPNS-Benachrichtigungsformat|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil die Nutzlast der Benachrichtigung zu groß war.|Keine Dimensionen|
|outgoing.mpns.pnserror|Ja|MPNS-Fehler|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil Fehler bei der Kommunikation mit MPNS aufgetreten sind.|Keine Dimensionen|
|outgoing.mpns.success|Ja|Erfolgreiche MPNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.mpns.throttled|Ja|Gedrosselte MPNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App vom MPNS gedrosselt wird (WNS-MPNS: 406 – Nicht annehmbar).|Keine Dimensionen|
|outgoing.wns.authenticationerror|Ja|WNS-Authentifizierungsfehler|Anzahl|Gesamt|Die Benachrichtigung wurde nicht übermittelt, weil Fehler bei der Kommunikation mit Windows Live aufgetreten sind, die Anmeldeinformationen ungültig waren oder das falsche Token verwendet wurde.|Keine Dimensionen|
|outgoing.wns.badchannel|Ja|WNS-Fehler durch fehlerhaften Kanal|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil der ChannelURI in der Registrierung nicht erkannt wurde (WNS-Status: 404 – Nicht gefunden).|Keine Dimensionen|
|outgoing.wns.channeldisconnected|Ja|WNS-Kanal nicht verbunden|Anzahl|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde (WNS-Antwortheader: X-WNS-DeviceConnectionStatus: disconnected).|Keine Dimensionen|
|outgoing.wns.channelthrottled|Ja|WNS-Kanal gedrosselt|Anzahl|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde (WNS-Antwortheader: X-WNS-NotificationStatus:channelThrottled).|Keine Dimensionen|
|outgoing.wns.dropped|Ja|Verworfene WNS-Benachrichtigungen|Anzahl|Gesamt|Die Benachrichtigung wurde verworfen, weil der ChannelURI in der Registrierung gedrosselt wurde („X-WNS-NotificationStatus: dropped“, aber nicht „X-WNS-DeviceConnectionStatus: disconnected“).|Keine Dimensionen|
|outgoing.wns.expiredchannel|Ja|WNS-Fehler durch abgelaufenen Kanal|Anzahl|Gesamt|Die Anzahl von nicht erfolgreichen Pushvorgängen, weil der ChannelURI abgelaufen ist (WNS-Status: 410 – Fehlend).|Keine Dimensionen|
|outgoing.wns.invalidcredentials|Ja|WNS-Autorisierungsfehler (ungültige Anmeldeinformationen)|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil das PNS die angegebenen Anmeldeinformationen nicht akzeptiert hat oder die Anmeldeinformationen blockiert wurden. (Windows Live erkennt die Anmeldeinformationen nicht.)|Keine Dimensionen|
|outgoing.wns.invalidnotificationformat|Ja|Ungültiges WNS-Benachrichtigungsformat|Anzahl|Gesamt|Das Format der Benachrichtigung ist ungültig (WNS-Status: 400). Beachten Sie, dass von WNS nicht alle ungültigen Nutzlasten abgelehnt werden.|Keine Dimensionen|
|outgoing.wns.invalidnotificationsize|Ja|WNS-Fehler durch ungültige Benachrichtigungsgröße|Anzahl|Gesamt|Die Benachrichtigungsnutzlast ist zu groß (WNS-Status: 413).|Keine Dimensionen|
|outgoing.wns.invalidtoken|Ja|WNS-Autorisierungsfehler (ungültiges Token)|Anzahl|Gesamt|Das für WNS bereitgestellte Token ist nicht gültig (WNS-Status: 401 – Nicht autorisiert).|Keine Dimensionen|
|outgoing.wns.pnserror|Ja|WNS-Fehler|Anzahl|Gesamt|Die Benachrichtigung wurde aufgrund von Fehlern bei der Kommunikation mit WNS nicht übermittelt.|Keine Dimensionen|
|outgoing.wns.success|Ja|Erfolgreiche WNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von erfolgreichen Benachrichtigungen an.|Keine Dimensionen|
|outgoing.wns.throttled|Ja|Gedrosselte WNS-Benachrichtigungen|Anzahl|Gesamt|Gibt die Anzahl von Pushvorgängen an, die nicht erfolgreich waren, weil diese App vom WNS gedrosselt wird (WNS-Status: 406 – Nicht annehmbar).|Keine Dimensionen|
|outgoing.wns.tokenproviderunreachable|Ja|WNS-Autorisierungsfehler (nicht erreichbar)|Anzahl|Gesamt|Windows Live ist nicht erreichbar.|Keine Dimensionen|
|outgoing.wns.wrongtoken|Ja|WNS-Autorisierungsfehler (falsches Token)|Anzahl|Gesamt|Das für WNS bereitgestellte Token ist gültig, gilt aber für eine andere Anwendung (WNS-Status: 403 – Verboten). Dieser Fall kann eintreten, wenn der ChannelURI in der Registrierung einer anderen App zugeordnet wird. Stellen Sie sicher, dass die Client-App der App zugeordnet ist, deren Anmeldeinformationen sich im Notification Hub befinden.|Keine Dimensionen|
|registration.all|Ja|Registrierungsvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsvorgängen an (Erstellungen, Aktualisierungen, Abfragen und Löschungen). |Keine Dimensionen|
|registration.create|Ja|Registrierungserstellungsvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungserstellungen an.|Keine Dimensionen|
|registration.delete|Ja|Registrierungslöschvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungslöschungen an.|Keine Dimensionen|
|registration.get|Ja|Registrierungslesevorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsabfragen an.|Keine Dimensionen|
|registration.update|Ja|Registrierungsaktualisierungsvorgänge|Anzahl|Gesamt|Gibt die Anzahl von allen erfolgreichen Registrierungsaktualisierungen an.|Keine Dimensionen|
|scheduled.pending|Ja|Ausstehende Pushbenachrichtigungen|Anzahl|Gesamt|Ausstehende Pushbenachrichtigungen|Keine Dimensionen|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Average_% Available Memory|Ja|% verfügbarer Speicher|Anzahl|Average|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Ja|% verfügbarer Auslagerungsbereich|Anzahl|Average|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Ja|Zugesicherte verwendete Bytes (%)|Anzahl|Average|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Ja|% DPC-Zeit|Anzahl|Average|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|Ja|% freie Inodes|Anzahl|Average|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Ja|% freier Speicher|Anzahl|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Ja|% Leerlaufzeit|Anzahl|Average|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Ja|% Interruptzeit|Anzahl|Average|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Ja|% E/a-Wartezeit|Anzahl|Average|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Ja|% Nice-Zeit|Anzahl|Average|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Ja|% privilegierte Zeit|Anzahl|Average|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Ja|% Prozessorzeit|Anzahl|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Ja|% verwendete Inodes|Anzahl|Average|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Ja|% verwendeter Arbeitsspeicher|Anzahl|Average|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Ja|% verwendeter Speicher|Anzahl|Average|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Ja|% verwendeter Auslagerungsbereich|Anzahl|Average|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Ja|% Benutzerzeit|Anzahl|Average|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Ja|Verfügbare MB|Anzahl|Average|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Ja|Verfügbarer Speicher in MB|Anzahl|Average|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Ja|Verfügbarer Auslagerungsbereich in MB|Anzahl|Average|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/gelesen|Ja|Durchschn. Datenträger s/gelesen|Anzahl|Average|Average_Avg. Datenträger s/gelesen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/übertragen|Ja|Durchschn. Datenträger s/übertragen|Anzahl|Average|Average_Avg. Datenträger s/übertragen|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Datenträger s/geschrieben|Ja|Durchschn. Datenträger s/geschrieben|Anzahl|Average|Average_Avg. Datenträger s/geschrieben|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Ja|Empfangene Byte/Sek.|Anzahl|Average|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Ja|Gesendete Byte/Sek.|Anzahl|Average|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Ja|Byte gesamt/Sek.|Anzahl|Average|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Ja|Aktuelle Warteschlangenlänge|Anzahl|Average|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Ja|Byte gelesen/s|Anzahl|Average|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Ja|Lesevorgänge/s|Anzahl|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Ja|Übertragungen/s|Anzahl|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Ja|Byte geschrieben/s|Anzahl|Average|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Ja|Schreibvorgänge/s|Anzahl|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Ja|Freie Megabytes|Anzahl|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Ja|Freier physischer Speicher|Anzahl|Average|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Ja|Freier Speicherplatz in Auslagerungsdateien|Anzahl|Average|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Ja|Freier virtueller Arbeitsspeicher|Anzahl|Average|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Ja|Logischer Datenträger Bytes/s|Anzahl|Average|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Ja|Seitenlesevorgänge/s|Anzahl|Average|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Ja|Schreibvorgänge/s|Anzahl|Average|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Ja|Seiten/s|Anzahl|Average|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Ja|PCT-privilegierte Zeit|Anzahl|Average|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Ja|PCT-Benutzerzeit|Anzahl|Average|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Ja|Physischer Datenträger Bytes/s|Anzahl|Average|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Ja|Prozesse|Anzahl|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Ja|Länge der Prozessorwarteschlange|Anzahl|Average|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Ja|Genutzter Speicherplatz in Auslagerungsdateien|Anzahl|Average|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Ja|Summe Bytes|Anzahl|Average|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Ja|Summe empfangene Bytes|Anzahl|Average|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Ja|Summe übertragene Bytes|Anzahl|Average|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Ja|Summe Konflikte|Anzahl|Average|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Ja|Summe empfangene Pakete|Anzahl|Average|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Ja|Summe übermittelte Pakete|Anzahl|Average|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Ja|Summe Rx-Fehler|Anzahl|Average|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Ja|Summe Tx-Fehler|Anzahl|Average|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Ja|Betriebszeit|Anzahl|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Ja|Verwendeter Auslagerungsbereich in MB|Anzahl|Average|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Ja|Verwendeter Arbeitsspeicher in KB|Anzahl|Average|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Ja|Verwendeter Speicher in MB|Anzahl|Average|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Ja|Benutzer|Anzahl|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Ja|Virtueller gemeinsamer Speicher|Anzahl|Average|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Ereignis|Ja|Ereignis|Anzahl|Average|Ereignis|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Heartbeat|Ja|Heartbeat|Anzahl|Gesamt|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aktualisieren|Ja|Aktualisieren|Anzahl|Average|Aktualisieren|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Ja|Rate des ausgehenden Datenverkehrs|BitsPerSecond|Average|Rate des ausgehenden Datenverkehrs in Bit pro Sekunde|ConnectionId|
|IngressTrafficRate|Ja|Rate des eingehenden Datenverkehrs|BitsPerSecond|Average|Rate des eingehenden Datenverkehrs in Bit pro Sekunde|ConnectionId|
|SessionAvailabilityV4|Ja|V4-Sitzungsverfügbarkeit|Percent|Average|Verfügbarkeit der V4-Sitzung|ConnectionId|
|SessionAvailabilityV6|Ja|Sitzungsverfügbarkeit V6|Percent|Average|Verfügbarkeit der V6-Sitzung|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|PrefixLatency|Ja|Präfixlatenz|Millisekunden|Average|Medianwert der Präfixlatenz|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|memory_metric|Ja|Arbeitsspeicher|Byte|Average|Arbeitsspeicher. Bereich von 0–3 GB für A1, 0–5 GB für A2, 0–10 GB für A3, 0–25 GB für A4, 0–50 GB für A5 und 0–100 GB für A6|Keine Dimensionen|
|memory_thrashing_metric|Ja|Arbeitsspeicherüberlastung (Datasets)|Percent|Average|Durchschnittliche Arbeitsspeicherüberlastung.|Keine Dimensionen|
|qpu_high_utilization_metric|Ja|Hohe QPU-Auslastung|Anzahl|Gesamt|Hohe QPU-Auslastung in der letzten Minute; 1 bei hoher QPU-Auslastung, andernfalls 0|Keine Dimensionen|
|QueryDuration|Ja|Abfragedauer (Datasets)|Millisekunden|Average|DAX-Abfragedauer im letzten Intervall|Keine Dimensionen|
|QueryPoolJobQueueLength|Ja|Auftragswarteschlangenlänge für Abfragepool (Datasets)|Anzahl|Average|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools.|Keine Dimensionen|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Gesamt|Gesamtzahl der ActiveConnections für Microsoft.Relay.|EntityName|
|ActiveListeners|Nein|ActiveListeners|Anzahl|Gesamt|Gesamtzahl der ActiveListeners für Microsoft.Relay.|EntityName|
|BytesTransferred|Ja|BytesTransferred|Anzahl|Gesamt|Gesamtzahl der BytesTransferred für Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|Nein|ListenerConnections-ClientError|Anzahl|Gesamt|ClientError bei ListenerConnections für Microsoft.Relay.|EntityName, |
|ListenerConnections-ServerError|Nein|ListenerConnections-ServerError|Anzahl|Gesamt|ServerError bei ListenerConnections für Microsoft.Relay.|EntityName, |
|ListenerConnections-Success|Nein|ListenerConnections-Success|Anzahl|Gesamt|Erfolgreiche ListenerConnections für Microsoft.Relay.|EntityName, |
|ListenerConnections-TotalRequests|Nein|ListenerConnections-TotalRequests|Anzahl|Gesamt|Gesamtzahl der ListenerConnections für Microsoft.Relay.|EntityName|
|ListenerDisconnects|Nein|ListenerDisconnects|Anzahl|Gesamt|Gesamtzahl der ListenerDisconnects für Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|Nein|SenderConnections-ClientError|Anzahl|Gesamt|ClientError bei SenderConnections für Microsoft.Relay.|EntityName, |
|SenderConnections-ServerError|Nein|SenderConnections-ServerError|Anzahl|Gesamt|ServerError bei SenderConnections für Microsoft.Relay.|EntityName, |
|SenderConnections-Success|Nein|SenderConnections-Success|Anzahl|Gesamt|Erfolgreiche SenderConnections für Microsoft.Relay.|EntityName, |
|SenderConnections-TotalRequests|Nein|SenderConnections-TotalRequests|Anzahl|Gesamt|Gesamtzahl der SenderConnections-Anforderungen für Microsoft.Relay.|EntityName|
|SenderDisconnects|Nein|SenderDisconnects|Anzahl|Gesamt|Gesamtzahl der SenderDisconnects für Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|SearchLatency|Ja|Suchlatenz|Sekunden|Average|Durchschnittliche Suchlatenz für den Suchdienst|Keine Dimensionen|
|SearchQueriesPerSecond|Ja|Suchabfragen pro Sekunde|Anzahl pro Sekunde|Average|Suchabfragen pro Sekunde für den Suchdienst|Keine Dimensionen|
|ThrottledSearchQueriesPercentage|Ja|Gedrosselte Suchabfragen in Prozent|Percent|Average|Prozentsatz der Suchabfragen, die für den Suchdienst gedrosselt wurden|Keine Dimensionen|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveConnections|Nein|ActiveConnections|Anzahl|Gesamt|Aktive Verbindungen gesamt für Microsoft.ServiceBus.|Keine Dimensionen|
|ActiveMessages|Nein|Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von aktiven Nachrichten in einer Warteschlange/einem Thema|EntityName|
|ConnectionsClosed|Nein|Geschlossene Verbindungen.|Anzahl|Average|Geschlossene Verbindungen für Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|Nein|Geöffnete Verbindungen.|Anzahl|Average|Geöffnete Verbindungen für Microsoft.ServiceBus.|EntityName|
|CPUXNS|Nein|CPU (veraltet)|Percent|Maximum|CPU-Auslastungsmetrik für Service Bus-Premium-Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die CPU-Metrik (NamespaceCpuUsage).|Keine Dimensionen|
|DeadletteredMessages|Nein|Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von unzustellbaren Nachrichten in einer Warteschlange/einem Thema|EntityName|
|IncomingMessages|Ja|Eingehende Nachrichten|Anzahl|Gesamt|Eingehende Nachrichten für Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Ja|Eingehende Anforderungen|Anzahl|Gesamt|Eingehende Anforderungen für Microsoft.ServiceBus.|EntityName|
|Meldungen|Nein|Anzahl von Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von Nachrichten in einer Warteschlange/einem Thema|EntityName|
|NamespaceCpuUsage|Nein|CPU|Percent|Maximum|CPU-Auslastungsmetrik für Service Bus-Premium-Namespace|Keine Dimensionen|
|NamespaceMemoryUsage|Nein|Speicherauslastung|Percent|Maximum|Speicherauslastungsmetrik für Service Bus-Premium-Namespace|Keine Dimensionen|
|OutgoingMessages|Ja|Ausgehende Nachrichten|Anzahl|Gesamt|Ausgehende Nachrichten für Microsoft.ServiceBus.|EntityName|
|ScheduledMessages|Nein|Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema|Anzahl|Average|Anzahl von geplanten Nachrichten in einer Warteschlange/einem Thema|EntityName|
|ServerErrors|Nein|Serverfehler.|Anzahl|Gesamt|Serverfehler für Microsoft.ServiceBus.|EntityName, |
|Size|Nein|Size|Byte|Average|Größe einer Warteschlange/eines Themas in Bytes|EntityName|
|SuccessfulRequests|Nein|Erfolgreiche Anforderungen|Anzahl|Gesamt|Gesamtzahl der erfolgreichen Anforderungen für einen Namespace|EntityName, |
|ThrottledRequests|Nein|Gedrosselte Anforderungen.|Anzahl|Gesamt|Gedrosselte Anforderungen für Microsoft.ServiceBus.|EntityName, |
|UserErrors|Nein|Benutzerfehler.|Anzahl|Gesamt|Benutzerfehler für Microsoft.ServiceBus.|EntityName, |
|WSXNS|Nein|Speicherauslastung (Deprecated)|Percent|Maximum|Speicherauslastungsmetrik für Service Bus-Premium-Namespace Diese Metrik ist veraltet. Verwenden Sie stattdessen die Speicherauslastingsmetrik (NamespaceMemoryUsage).|Keine Dimensionen|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActualCpu|Nein|ActualCpu|Anzahl|Average|Tatsächliche CPU-Auslastung in Millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|Nein|ActualMemory|Byte|Average|Tatsächliche Arbeitsspeichernutzung in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|Nein|AllocatedCpu|Anzahl|Average|Diesem Container zugeordnete CPU in Millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|Nein|AllocatedMemory|Byte|Average|Diesem Container zugeordneter Arbeitsspeicher in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|Nein|ApplicationStatus|Anzahl|Average|Status der Service Fabric Mesh-Anwendung|ApplicationName, Status|
|ContainerStatus|Nein|ContainerStatus|Anzahl|Average|Status des Containers in der Service Fabric Mesh-Anwendung|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|CpuUtilization|Nein|CpuUtilization|Percent|Average|Auslastung der CPU für diesen Container als Prozentsatz von AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|Nein|MemoryUtilization|Percent|Average|Auslastung der CPU für diesen Container als Prozentsatz von AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|Nein|RestartCount|Anzahl|Average|Anzahl Neustarts eines Containers in Service Fabric Mesh-Anwendung|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|Nein|ServiceReplicaStatus|Anzahl|Average|Integritätsstatus eines Dienstreplikats in Service Fabric Mesh-Anwendung|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ServiceStatus|Nein|ServiceStatus|Anzahl|Average|Integritätsstatus eines Diensts in Service Fabric Mesh-Anwendung|ApplicationName, Status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ConnectionCount|Ja|Anzahl der Verbindungen|Anzahl|Maximum|Die Anzahl der Benutzerverbindungen.|Endpunkt|
|InboundTraffic|Ja|Eingehender Datenverkehr|Byte|Gesamt|Der eingehende Datenverkehr des Diensts|Keine Dimensionen|
|MessageCount|Ja|Nachrichtenanzahl|Anzahl|Gesamt|Die Gesamtmenge der Nachrichten.|Keine Dimensionen|
|OutboundTraffic|Ja|Ausgehender Datenverkehr|Byte|Gesamt|Der ausgehende Datenverkehr des Diensts|Keine Dimensionen|
|SystemErrors|Ja|Systemfehler|Percent|Maximum|Der Prozentsatz der Systemfehler|Keine Dimensionen|
|UserErrors|Ja|Benutzerfehler|Percent|Maximum|Der Prozentsatz der Benutzerfehler|Keine Dimensionen|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Ja|Durchschnittlicher CPU-Prozentsatz|Percent|Average|Durchschnittlicher CPU-Prozentsatz|Keine Dimensionen|
|io_bytes_read|Ja|Gelesene E/A-Bytes|Byte|Average|Gelesene E/A-Bytes|Keine Dimensionen|
|io_bytes_written|Ja|Geschriebene E/A-Bytes|Byte|Average|Geschriebene E/A-Bytes|Keine Dimensionen|
|io_requests|Ja|Anzahl von E/A-Anforderungen|Anzahl|Average|Anzahl von E/A-Anforderungen|Keine Dimensionen|
|reserved_storage_mb|Ja|Reservierter Speicherplatz|Anzahl|Average|Reservierter Speicherplatz|Keine Dimensionen|
|storage_space_used_mb|Ja|Belegter Speicherplatz|Anzahl|Average|Belegter Speicherplatz|Keine Dimensionen|
|virtual_core_count|Ja|Anzahl virtueller Kerne|Anzahl|Average|Anzahl virtueller Kerne|Keine Dimensionen|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|active_queries|Ja|Aktive Abfragen|Anzahl|Gesamt|Aktive Abfragen in allen Arbeitsauslastungsgruppen. Gilt nur für Data Warehouses.|Keine Dimensionen|
|allocated_data_storage|Ja|Zugeordneter Datenspeicherplatz|Byte|Average|Zugeordneter Datenspeicher. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|app_cpu_billed|Ja|Abgerechnete App-CPU|Anzahl|Gesamt|Abgerechnete App-CPU. Gilt für serverlose Datenbanken.|Keine Dimensionen|
|app_cpu_percent|Ja|App-CPU-Prozentsatz|Percent|Average|App-CPU-Prozentsatz. Gilt für serverlose Datenbanken.|Keine Dimensionen|
|app_memory_percent|Ja|App-Arbeitsspeicherprozentsatz|Percent|Average|App-Arbeitsspeicherprozentsatz. Gilt für serverlose Datenbanken.|Keine Dimensionen|
|base_blob_size_bytes|Ja|Basisspeichergröße für Blobs|Byte|Maximum|Basisspeichergröße für Blobs. Gilt für Hyperscale-Datenbanken.|Keine Dimensionen|
|blocked_by_firewall|Ja|Von der Firewall blockiert|Anzahl|Gesamt|Von der Firewall blockiert|Keine Dimensionen|
|cache_hit_percent|Ja|Prozentsatz der Cachetreffer|Percent|Maximum|Prozentsatz der Cachetreffer. Gilt nur für Data Warehouses.|Keine Dimensionen|
|cache_used_percent|Ja|Cacheverwendung in Prozent|Percent|Maximum|Cacheverwendung in Prozent. Gilt nur für Data Warehouses.|Keine Dimensionen|
|connection_failed|Ja|Verbindungsfehler|Anzahl|Gesamt|Verbindungsfehler|Keine Dimensionen|
|connection_successful|Ja|Erfolgreiche Verbindungen|Anzahl|Gesamt|Erfolgreiche Verbindungen|Keine Dimensionen|
|cpu_limit|Ja|CPU-Grenzwert|Anzahl|Average|CPU-Grenzwert. Gilt für V-Kern-basierte Datenbanken.|Keine Dimensionen|
|cpu_percent|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Keine Dimensionen|
|cpu_used|Ja|Verwendete CPU|Anzahl|Average|Verwendete CPU. Gilt für V-Kern-basierte Datenbanken.|Keine Dimensionen|
|deadlock|Ja|Deadlocks|Anzahl|Gesamt|Deadlocks. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|diff_backup_size_bytes|Ja|Speichergröße für differenzielle Sicherungen|Byte|Maximum|Speichergröße für kumulative differenzielle Sicherungen Gilt für V-Kern-basierte Datenbanken. Gilt nicht für Hyperscale-Datenbanken.|Keine Dimensionen|
|dtu_consumption_percent|Ja|DTU-Prozentsatz|Percent|Average|DTU-Prozentsatz. Gilt für DTU-basierte Datenbanken.|Keine Dimensionen|
|dtu_limit|Ja|DTU-Grenzwert|Anzahl|Average|DTU-Grenzwert. Gilt für DTU-basierte Datenbanken.|Keine Dimensionen|
|dtu_used|Ja|DTU-Verbrauch|Anzahl|Average|DTU-Verbrauch. Gilt für DTU-basierte Datenbanken.|Keine Dimensionen|
|dw_backup_size_gb|Ja|Data Storage Size (GB) (Größe des Datenspeichers (GB))|Anzahl|Gesamt|Die Größe des Datenspeichers setzt sich aus der Größe Ihrer Daten und des Transaktionsprotokolls zusammen. Die Metrik wird in Ihrer Rechnung unter „Speicher“ erfasst. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dw_geosnapshot_size_gb|Ja|Speichergröße für die Notfallwiederherstellung (GB)|Anzahl|Gesamt|Die Speichergröße für die Notfallwiederherstellung wird in Ihrer Rechnung als „Notfallwiederherstellungsspeicher“ aufgeführt. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dw_snapshot_size_gb|Ja|Speichergröße für Momentaufnahmen (GB)|Anzahl|Gesamt|Die Größe der inkrementellen Änderungen, die von Momentaufnahmen erfasst werden, um benutzerdefinierte und automatische Wiederherstellungspunkte zu erstellen. Die Metrik wird in Ihrer Rechnung unter „Speicher“ erfasst. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dwu_consumption_percent|Ja|DWU in Prozent|Percent|Maximum|DWU in Prozent. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dwu_limit|Ja|DWU-Grenzwert|Anzahl|Maximum|DWU-Grenzwert. Gilt nur für Data Warehouses.|Keine Dimensionen|
|dwu_used|Ja|DWU-Verbrauch|Anzahl|Maximum|DWU-Verbrauch. Gilt nur für Data Warehouses.|Keine Dimensionen|
|full_backup_size_bytes|Ja|Speichergröße für vollständige Sicherungen|Byte|Maximum|Speichergröße für kumulative vollständige Sicherungen Gilt für V-Kern-basierte Datenbanken. Gilt nicht für Hyperscale-Datenbanken.|Keine Dimensionen|
|local_tempdb_usage_percent|Ja|Lokaler tempdb-Prozentsatz|Percent|Average|Lokaler tempdb-Prozentsatz. Gilt nur für Data Warehouses.|Keine Dimensionen|
|log_backup_size_bytes|Ja|Speichergröße für Protokollsicherungen|Byte|Maximum|Speichergröße für kumulative Protokollsicherungen Gilt für auf virtuellen Kernen basierende Datenbanken und Hyperscale-Datenbanken.|Keine Dimensionen|
|log_write_percent|Ja|E/A-Prozentsatz für Protokoll|Percent|Average|E/A-Prozentsatz für Protokoll. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|memory_usage_percent|Ja|Arbeitsspeicherprozentsatz|Percent|Maximum|Arbeitsspeicherprozentsatz. Gilt nur für Data Warehouses.|Keine Dimensionen|
|physical_data_read_percent|Ja|E/A-Prozentsatz für Daten|Percent|Average|E/A-Prozentsatz für Daten|Keine Dimensionen|
|queued_queries|Ja|Abfragen in Warteschlange|Anzahl|Gesamt|Abfragen in Warteschlange in allen Arbeitsauslastungsgruppen. Gilt nur für Data Warehouses.|Keine Dimensionen|
|sessions_percent|Ja|Sitzungen in Prozent|Percent|Average|Sitzungen in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|snapshot_backup_size_bytes|Ja|Speichergröße für Momentaufnahmesicherungen|Byte|Maximum|Speichergröße für kumulative Momentaufnahmesicherungen. Gilt für Hyperscale-Datenbanken.|Keine Dimensionen|
|sqlserver_process_core_percent|Ja|SQL Server-Prozess: Kern (in Prozent)|Percent|Maximum|CPU-Auslastung als Prozentsatz des SQL-DB-Prozesses. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|sqlserver_process_memory_percent|Ja|SQL Server-Prozess: Arbeitsspeicher (in Prozent)|Percent|Maximum|Speicherauslastung als Prozentsatz des SQL-DB-Prozesses. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|storage|Ja|Genutzter Datenspeicherplatz|Byte|Maximum|Genutzter Datenspeicherplatz. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|storage_percent|Ja|Genutzter Datenspeicherplatz in Prozent|Percent|Maximum|Genutzter Datenspeicherplatz in Prozent. Gilt nicht für Data Warehouses oder Hyperscale-Datenbanken.|Keine Dimensionen|
|tempdb_data_size|Ja|Größe der tempdb-Datendatei in Kilobytes|Anzahl|Maximum|Die Größe der tempdb-Datendatei in Kilobytes. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|tempdb_log_size|Ja|Größe der tempdb-Protokolldatei in Kilobytes|Anzahl|Maximum|Die Größe der tempdb-Protokolldatei in Kilobytes. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|tempdb_log_used_percent|Ja|Nutzung des tempdb-Protokolls in Prozent|Percent|Maximum|Die Nutzung des tempdb-Protokolls in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|wlg_active_queries|Ja|Aktive Abfragen von Arbeitsauslastungsgruppen|Anzahl|Gesamt|Aktive Abfragen in der Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Ja|Abfragetimeouts für Arbeitsauslastungsgruppen|Anzahl|Gesamt|Abfragen, für die ein Timeout aufgetreten ist, für die Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Ja|Zuordnung von Arbeitsauslastungsgruppen nach Systemprozentsatz|Percent|Maximum|Die prozentuale Zuordnung von Ressourcen in Relation zum gesamten System pro Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Ja|Arbeitsauslastungsgruppenzuordnung nach Ressourcenlimit (Prozent)|Percent|Maximum|Die prozentuale Zuordnung von Ressourcen in Relation zum angegebenen Ressourcenlimit pro Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Ja|Effektives Ressourcenlimit (Prozent)|Percent|Maximum|Eine feste Beschränkung des Prozentsatzes der Ressourcen, auf die die Arbeitsauslastungsgruppe zugreifen kann, unter Berücksichtigung des Wertes, der anderen Arbeitsauslastungsgruppen für die effektive Mindestanzahl von Ressourcen in Prozent zugeordnet ist. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Ja|Effektive Mindestanzahl von Ressourcen (Prozent)|Percent|Maximum|Der Mindestprozentsatz von Ressourcen, die unter Berücksichtigung des Servicelevelminimums für die Arbeitsauslastungsgruppe reserviert und isoliert sind. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Ja|In der Warteschlange befindliche Abfragen der Arbeitsauslastungsgruppe|Anzahl|Gesamt|Abfragen in Warteschlange in der Arbeitsauslastungsgruppe. Gilt nur für Data Warehouses.|WorkloadGroupName, IsUserDefined|
|workers_percent|Ja|Worker in Prozent|Percent|Average|Worker in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|
|xtp_storage_percent|Ja|In-Memory-OLTP-Speicher in Prozent|Percent|Average|In-Memory-OLTP-Speicher in Prozent. Gilt nicht für Data Warehouses.|Keine Dimensionen|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|allocated_data_storage|Ja|Zugeordneter Datenspeicherplatz|Byte|Average|Zugeordneter Datenspeicherplatz|Keine Dimensionen|
|allocated_data_storage_percent|Ja|Zugeordneter Datenspeicherplatz in Prozent|Percent|Maximum|Zugeordneter Datenspeicherplatz in Prozent|Keine Dimensionen|
|cpu_limit|Ja|CPU-Grenzwert|Anzahl|Average|CPU-Grenzwert. Gilt für V-Kern-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|cpu_percent|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Keine Dimensionen|
|cpu_used|Ja|Verwendete CPU|Anzahl|Average|Verwendete CPU. Gilt für V-Kern-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|database_allocated_data_storage|Nein|Zugeordneter Datenspeicherplatz|Byte|Average|Zugeordneter Datenspeicherplatz|DatabaseResourceId|
|database_cpu_limit|Nein|CPU-Grenzwert|Anzahl|Average|CPU-Grenzwert|DatabaseResourceId|
|database_cpu_percent|Nein|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|DatabaseResourceId|
|database_cpu_used|Nein|Verwendete CPU|Anzahl|Average|Verwendete CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Nein|DTU-Prozentsatz|Percent|Average|DTU-Prozentsatz|DatabaseResourceId|
|database_eDTU_used|Nein|eDTU-Verbrauch|Anzahl|Average|eDTU-Verbrauch|DatabaseResourceId|
|database_log_write_percent|Nein|E/A-Prozentsatz für Protokoll|Percent|Average|E/A-Prozentsatz für Protokoll|DatabaseResourceId|
|database_physical_data_read_percent|Nein|E/A-Prozentsatz für Daten|Percent|Average|E/A-Prozentsatz für Daten|DatabaseResourceId|
|database_sessions_percent|Nein|Sitzungen in Prozent|Percent|Average|Sitzungen in Prozent|DatabaseResourceId|
|database_storage_used|Nein|Genutzter Datenspeicherplatz|Byte|Average|Genutzter Datenspeicherplatz|DatabaseResourceId|
|database_workers_percent|Nein|Worker in Prozent|Percent|Average|Worker in Prozent|DatabaseResourceId|
|dtu_consumption_percent|Ja|DTU-Prozentsatz|Percent|Average|DTU-Prozentsatz. Gilt für DTU-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|eDTU_limit|Ja|eDTU-Grenzwert|Anzahl|Average|eDTU-Grenzwert. Gilt für DTU-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|eDTU_used|Ja|eDTU-Verbrauch|Anzahl|Average|eDTU-Verbrauch. Gilt für DTU-basierte Pools für elastische Datenbanken.|Keine Dimensionen|
|log_write_percent|Ja|E/A-Prozentsatz für Protokoll|Percent|Average|E/A-Prozentsatz für Protokoll|Keine Dimensionen|
|physical_data_read_percent|Ja|E/A-Prozentsatz für Daten|Percent|Average|E/A-Prozentsatz für Daten|Keine Dimensionen|
|sessions_percent|Ja|Sitzungen in Prozent|Percent|Average|Sitzungen in Prozent|Keine Dimensionen|
|sqlserver_process_core_percent|Ja|SQL Server-Prozess: Kern (in Prozent)|Percent|Maximum|CPU-Auslastung als Prozentsatz des SQL-DB-Prozesses. Bezieht sich auf Pools für elastische Datenbanken.|Keine Dimensionen|
|sqlserver_process_memory_percent|Ja|SQL Server-Prozess: Arbeitsspeicher (in Prozent)|Percent|Maximum|Speicherauslastung als Prozentsatz des SQL-DB-Prozesses. Bezieht sich auf Pools für elastische Datenbanken.|Keine Dimensionen|
|storage_limit|Ja|Maximale Datengröße|Byte|Average|Maximale Datengröße|Keine Dimensionen|
|storage_percent|Ja|Genutzter Datenspeicherplatz in Prozent|Percent|Average|Genutzter Datenspeicherplatz in Prozent|Keine Dimensionen|
|storage_used|Ja|Genutzter Datenspeicherplatz|Byte|Average|Genutzter Datenspeicherplatz|Keine Dimensionen|
|tempdb_data_size|Ja|Größe der tempdb-Datendatei in Kilobytes|Anzahl|Maximum|Größe der tempdb-Datendatei in Kilobytes|Keine Dimensionen|
|tempdb_log_size|Ja|Größe der tempdb-Protokolldatei in Kilobytes|Anzahl|Maximum|Größe der tempdb-Protokolldatei in Kilobytes|Keine Dimensionen|
|tempdb_log_used_percent|Ja|Nutzung des tempdb-Protokolls in Prozent|Percent|Maximum|Nutzung des tempdb-Protokolls in Prozent|Keine Dimensionen|
|workers_percent|Ja|Worker in Prozent|Percent|Average|Worker in Prozent|Keine Dimensionen|
|xtp_storage_percent|Ja|In-Memory-OLTP-Speicher in Prozent|Percent|Average|In-Memory-OLTP-Speicher in Prozent|Keine Dimensionen|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Nein|Verwendete Kapazität|Byte|Average|Die vom Speicherkonto beanspruchte Speichermenge. Bei Standardspeicherkonten ist das die Summe der von Blob, Table, File und Queue beanspruchten Kapazität. Bei Premium-Speicherkonten und Blob Storage-Konten ist diese mit BlobCapacity oder FileCapacity identisch.|Keine Dimensionen|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|BlobCapacity|Nein|Blob-Kapazität|Byte|Average|Die Größe des vom Blob-Dienst des Speicherkontos genutzten Speichers in Byte.|BlobType, Tier|
|BlobCount|Nein|Anzahl von Blobs|Anzahl|Average|Die Anzahl von im Speicherkonto gespeicherten Blobs.|BlobType, Tier|
|BlobProvisionedSize|Nein|Größe des bereitgestellten Blobs|Byte|Average|Die Speichermenge in Byte, die im Blob-Dienst des Speicherkontos bereitgestellt wird|BlobType, Tier|
|ContainerCount|Ja|Anzahl von Blob-Containern|Anzahl|Average|Die Anzahl von Containern im Speicherkonto.|Keine Dimensionen|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|IndexCapacity|Nein|Indexkapazität|Byte|Average|Die Menge an Speicher, die vom hierarchischen Index in Azure Data Lake Storage Gen2 verwendet wird|Keine Dimensionen|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication, FileShare|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|Nein|Dateikapazität|Byte|Average|Der vom Speicherkonto beanspruchte File-Speicher.|FileShare|
|FileCount|Nein|Dateianzahl|Anzahl|Average|Die Anzahl von Dateien im Speicherkonto.|FileShare|
|FileShareCapacityQuota|Nein|Kontingent für Dateifreigabekapazität|Byte|Average|Die Obergrenze für die Speichermenge zur Verwendung durch den Azure Files-Dienst, angegeben in Byte.|FileShare|
|FileShareCount|Nein|Anzahl von Dateifreigaben|Anzahl|Average|Die Anzahl von Dateifreigaben im Speicherkonto.|Keine Dimensionen|
|FileShareProvisionedIOPS|Nein|Bereitgestellte IOPS für Dateifreigaben|Byte|Average|Die Baseline der bereitgestellten IOPS für die Premium-Dateifreigabe im Premium-Dateispeicherkonto – diese Zahl wird anhand der bereitgestellten Größe (Kontingent) der Freigabekapazität berechnet.|FileShare|
|FileShareSnapshotCount|Nein|Anzahl von Momentaufnahmen in Dateifreigabe|Anzahl|Average|Die Anzahl der enthaltenen Momentaufnahmen im Dateidienst des Speicherkontos in der Freigabe|FileShare|
|FileShareSnapshotSize|Nein|Größe der Momentaufnahmen in Dateifreigabe|Byte|Average|Die Speichermenge in Byte, die von den Momentaufnahmen im Dateidienst des Speicherkontos verwendet wird.|FileShare|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication, FileShare|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Ja|Warteschlangenkapazität|Byte|Average|Der vom Speicherkonto beanspruchte Queue-Speicher.|Keine Dimensionen|
|QueueCount|Ja|Anzahl von Warteschlangen|Anzahl|Average|Die Anzahl von Warteschlangen im Speicherkonto.|Keine Dimensionen|
|QueueMessageCount|Ja|Anzahl von Warteschlangennachrichten|Anzahl|Average|Die Anzahl nicht abgelaufener Warteschlangennachrichten im Speicherkonto.|Keine Dimensionen|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Verfügbarkeit|Ja|Verfügbarkeit|Percent|Average|Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Die Verfügbarkeit wird berechnet, indem der Wert „TotalBillableRequests“ durch die Anzahl von zutreffenden Anforderungen, einschließlich der, die unerwartete Fehler erzeugt haben, geteilt wird. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang.|GeoType, ApiName, Authentication|
|Ausgehende Daten|Ja|Ausgehende Daten|Byte|Gesamt|Die ausgehende Datenmenge. Dieser Wert umfasst von Azure Storage an einen externen Client ausgehende Daten und ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar.|GeoType, ApiName, Authentication|
|Eingehende Daten|Ja|Eingehende Daten|Byte|Gesamt|Die Menge der Eingangsdaten in Byte. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Ja|E2E-Latenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche End-to-End-Latenz für erfolgreiche Anforderungen in Millisekunden, die an einen Speicherdienst oder den angegebenen API-Vorgang gesendet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Ja|Serverlatenz (erfolgreich)|Millisekunden|Average|Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz.|GeoType, ApiName, Authentication|
|TableCapacity|Ja|Tabellenkapazität|Byte|Average|Der vom Speicherkonto beanspruchte Table-Speicher.|Keine Dimensionen|
|TableCount|Ja|Anzahl von Tabellen|Anzahl|Average|Die Anzahl von Tabellen im Speicherkonto.|Keine Dimensionen|
|TableEntityCount|Ja|Anzahl von Tabellenentitäten|Anzahl|Average|Die Anzahl von Tabellenentitäten im Speicherkonto.|Keine Dimensionen|
|Transaktionen|Ja|Transaktionen|Anzahl|Gesamt|Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und fehlgeschlagene Anforderungen sowie Anforderungen, die Fehler erzeugt haben. Verwenden Sie die Dimension „ResponseType“ für die Anzahl von verschiedenen Antworttypen.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Ja|Ergebnis der Synchronisierungssitzung|Anzahl|Average|Metrik, die jedes Mal dann einen Wert von 1 protokolliert, wenn der Serverendpunkt eine Synchronisierungssitzung mit dem Cloudendpunkt erfolgreich abgeschlossen hat.|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Ja|Bytes synchronisiert|Byte|Gesamt|Für Synchronisierungssitzungen übertragene Gesamtdateigröße|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Ja|Cloudtiering-Rückrufgröße nach Anwendung|Byte|Gesamt|Größe der zurückgerufenen Daten nach Anwendung|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Ja|Cloudtiering-Rückrufgröße|Byte|Gesamt|Größe der zurückgerufenen Daten|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Ja|Cloudtieringrückruf|Byte|Gesamt|Gesamtgröße der vom Server zurückgerufenen Daten|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Ja|Cloudtiering-Rückrufdurchsatz|Bytes pro Sekunde|Average|Größe des Datenrückruf-Durchsatzes|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Ja|Onlinestatus des Servers|Anzahl|Maximum|Metrik, die jedes Mal dann einen Wert von 1 protokolliert, wenn der registrierte Server erfolgreich einen Heartbeat mit dem Cloudendpunkt erfasst.|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Ja|Synchronisierte Dateien|Anzahl|Gesamt|Anzahl synchronisierter Dateien|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Ja|Dateien ohne Synchronisierung|Anzahl|Gesamt|Anzahl von Dateien mit fehlerhafter Synchronisierung|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Ja|Onlinestatus des Servers|Anzahl|Maximum|Metrik, die jedes Mal dann einen Wert von 1 protokolliert, wenn der registrierte Server erfolgreich einen Heartbeat mit dem Cloudendpunkt erfasst.|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Ja|Cloudtieringrückruf|Byte|Gesamt|Gesamtgröße der vom Server zurückgerufenen Daten|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Ja|Bytes synchronisiert|Byte|Gesamt|Für Synchronisierungssitzungen übertragene Gesamtdateigröße|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Ja|Synchronisierte Dateien|Anzahl|Gesamt|Anzahl synchronisierter Dateien|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Ja|Dateien ohne Synchronisierung|Anzahl|Gesamt|Anzahl von Dateien mit fehlerhafter Synchronisierung|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Ja|Bytes synchronisiert|Byte|Gesamt|Für Synchronisierungssitzungen übertragene Gesamtdateigröße|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Ja|Synchronisierte Dateien|Anzahl|Gesamt|Anzahl synchronisierter Dateien|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Ja|Dateien ohne Synchronisierung|Anzahl|Gesamt|Anzahl von Dateien mit fehlerhafter Synchronisierung|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Ja|Fehlerhafte Funktionsanforderungen|Anzahl|Gesamt|Fehlerhafte Funktionsanforderungen|LogicalName, PartitionId|
|AMLCalloutInputEvents|Ja|Funktionsereignisse|Anzahl|Gesamt|Funktionsereignisse|LogicalName, PartitionId|
|AMLCalloutRequests|Ja|Funktionsanforderungen|Anzahl|Gesamt|Funktionsanforderungen|LogicalName, PartitionId|
|ConversionErrors|Ja|Konvertierungsfehler|Anzahl|Gesamt|Konvertierungsfehler|LogicalName, PartitionId|
|DeserializationError|Ja|Eingabefehler bei Deserialisierung|Anzahl|Gesamt|Eingabefehler bei Deserialisierung|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Ja|Ereignisse für falsche Reihenfolge|Anzahl|Gesamt|Ereignisse für falsche Reihenfolge|LogicalName, PartitionId|
|EarlyInputEvents|Ja|Frühe Eingabeereignisse|Anzahl|Gesamt|Frühe Eingabeereignisse|LogicalName, PartitionId|
|Errors|Ja|Laufzeitfehler|Anzahl|Gesamt|Laufzeitfehler|LogicalName, PartitionId|
|InputEventBytes|Ja|Eingabeereignisbytes|Byte|Gesamt|Eingabeereignisbytes|LogicalName, PartitionId|
|InputEvents|Ja|Eingabeereignisse|Anzahl|Gesamt|Eingabeereignisse|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Ja|Eingabeereignisse im Rückstand|Anzahl|Maximum|Eingabeereignisse im Rückstand|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Ja|Empfangene Eingabequellen|Anzahl|Gesamt|Empfangene Eingabequellen|LogicalName, PartitionId|
|LateInputEvents|Ja|Ereignisse bei verspäteter Eingabe|Anzahl|Gesamt|Ereignisse bei verspäteter Eingabe|LogicalName, PartitionId|
|OutputEvents|Ja|Ausgabeereignisse|Anzahl|Gesamt|Ausgabeereignisse|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Ja|Wasserzeichenverzögerung|Sekunden|Maximum|Wasserzeichenverzögerung|LogicalName, PartitionId|
|ResourceUtilization|Ja|Speichereinheitnutzung in %|Percent|Maximum|Speichereinheitnutzung in %|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|Nein|Beendete Aktivitätsausführungen|Anzahl|Gesamt|Anzahl von Orchestrierungsaktivitäten, die erfolgreich waren, bei denen ein Fehler aufgetreten ist oder die abgebrochen wurden|Result, FailureType, Activity, ActivityType, Pipeline|
|OrchestrationPipelineRunsEnded|Nein|Beendete Pipelineausführungen|Anzahl|Gesamt|Anzahl von Orchestrierungspipelineausführungen, die erfolgreich waren, bei denen ein Fehler aufgetreten ist oder die abgebrochen wurden|Result, FailureType, Pipeline|
|OrchestrationTriggersEnded|Nein|Beendete Trigger|Anzahl|Gesamt|Anzahl von Orchestrierungstriggern, die erfolgreich waren, bei denen ein Fehler aufgetreten ist oder die abgebrochen wurden|Result, FailureType, Trigger|
|SQLOnDemandLoginAttempts|Nein|Anmeldeversuche|Anzahl|Gesamt|Anzahl von Anmeldeversuchen, die erfolgreich waren oder bei denen ein Fehler aufgetreten ist|Ergebnis|
|SQLOnDemandQueriesEnded|Nein|Beendete Abfragen|Anzahl|Gesamt|Anzahl von Abfragen, die erfolgreich waren, bei denen ein Fehler aufgetreten ist oder die abgebrochen wurden|Ergebnis|
|SQLOnDemandQueryProcessedBytes|Nein|Verarbeitete Datenmenge|Byte|Gesamt|Gesamtmenge an Daten, die von Abfragen verarbeitet wurden|Keine Dimensionen|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CoresCapacity|Nein|Kernekapazität|Anzahl|Maximum|Kernekapazität|Keine Dimensionen|
|MemoryCapacityGB|Nein|Arbeitsspeicherkapazität (GB)|Anzahl|Maximum|Arbeitsspeicherkapazität (GB)|Keine Dimensionen|
|SparkJobsEnded|Ja|Beendete Anwendungen|Anzahl|Gesamt|Anzahl von beendeten Anwendungen|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|Nein|Prozentsatz der Treffer für adaptiven Cache|Percent|Maximum|Misst, wie gut Arbeitsauslastungen den adaptiven Cache nutzen. Verwenden Sie diese Metrik mit der Metrik für den Prozentsatz der Cachetreffer, um zu ermitteln, ob eine Skalierung zur Bereitstellung zusätzlicher Kapazität oder eine erneute Ausführung von Arbeitsauslastungen zum Aktualisieren des Caches erforderlich ist|Keine Dimensionen|
|AdaptiveCacheUsedPercent|Nein|Verwendung des adaptiven Cache in Prozent|Percent|Maximum|Misst, wie gut Arbeitsauslastungen den adaptiven Cache nutzen. Verwenden Sie diese Metrik mit der Metrik zur Cacheverwendung in Prozent, um zu ermitteln, ob eine Skalierung zur Bereitstellung zusätzlicher Kapazität oder eine erneute Ausführung von Arbeitsauslastungen zum Aktualisieren des Caches erforderlich ist|Keine Dimensionen|
|Verbindungen|Ja|Verbindungen|Anzahl|Gesamt|Gesamtanzahl der Anmeldungen beim dedizierten SQL-Pool|Ergebnis|
|ConnectionsBlockedByFirewall|Nein|Durch die Firewall blockierte Verbindungen|Anzahl|Gesamt|Anzahl von Verbindungen, die durch die Firewall blockiert wurden. Überprüfen Sie die Zugriffssteuerungsrichtlinien für Ihren dedizierten SQL-Pool, und überwachen Sie diese Verbindungen bei einer hohen Anzahl.|Keine Dimensionen|
|DWULimit|Nein|DWU-Grenzwert|Anzahl|Maximum|Servicelevelziel des dedizierten SQL-Pools|Keine Dimensionen|
|DWUUsed|Nein|DWU-Verbrauch|Anzahl|Maximum|Eine allgemeine Darstellung der Nutzung für den dedizierten SQL-Pool. Wird anhand von DWU-Limit × DWU-Prozentsatz berechnet|Keine Dimensionen|
|DWUUsedPercent|Nein|DWU-Verbrauch in Prozent|Percent|Maximum|Eine allgemeine Darstellung der Nutzung für den dedizierten SQL-Pool. Wird anhand des Höchstwerts zwischen CPU-Prozentsatz und E/A-Prozentsatz für Daten berechnet|Keine Dimensionen|
|LocalTempDBUsedPercent|Nein|Lokale tempdb-Auslastung in Prozent|Percent|Maximum|Lokale tempdb-Auslastung für alle Computeknoten, Werte werden alle fünf Minuten ausgegeben|Keine Dimensionen|
|MemoryUsedPercent|Nein|Verwendeter Arbeitsspeicher in Prozent|Percent|Maximum|Arbeitsspeichernutzung aller Knoten im dedizierten SQL-Pool|Keine Dimensionen|
|wlg_effective_min_resource_percent|Ja|Effektive Mindestanzahl von Ressourcen (Prozent)|Percent|Minimum|Die Einstellung für die effektive Mindestressourcenmenge in Prozent, die unter Berücksichtigung des Servicelevels und der Einstellungen der Arbeitsauslastungsgruppe zulässig ist. „effective min_percentage_resource“ kann bei niedrigeren Servicelevels erhöht werden|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|Nein|Aktive Abfragen von Arbeitsauslastungsgruppen|Anzahl|Gesamt|Die aktiven Abfragen in der Arbeitsauslastungsgruppe. Bei Verwenden dieser Metrik ohne Filterung und Aufteilung werden alle aktiven Abfragen angezeigt, die im System ausgeführt werden|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Nein|Abfragetimeouts für Arbeitsauslastungsgruppen|Anzahl|Gesamt|Abfragen für die Arbeitsauslastungsgruppe, für die ein Timeout aufgetreten ist. Die von dieser Metrik gemeldeten Abfragetimeouts sind erst nach dem Start der Abfrage aufgetreten (Wartezeiten aufgrund von Sperren oder Ressourcenwartezeiten sind nicht enthalten).|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Nein|Zuordnung von Arbeitsauslastungsgruppen nach maximalem Ressourcenprozentsatz|Percent|Maximum|Zeigt die prozentuale Zuordnung von Ressourcen in Relation zum effektiven Ressourcenlimit (Prozent) pro Arbeitsauslastungsgruppe an. Diese Metrik informiert über die effektive Auslastung der Arbeitsauslastungsgruppe|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Nein|Zuordnung von Arbeitsauslastungsgruppen nach Systemprozentsatz|Percent|Maximum|Die prozentuale Zuordnung von Ressourcen in Relation zum gesamten System|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Ja|Effektives Ressourcenlimit (Prozent)|Percent|Maximum|Das effektive Ressourcenlimit in Prozent für die Arbeitsauslastungsgruppe. Wenn andere Arbeitsauslastungsgruppen mit „min_percentage_resource“ > 0 vorhanden sind, wird „effective_cap_percentage_resource“ proportional gesenkt|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Nein|In der Warteschlange befindliche Abfragen der Arbeitsauslastungsgruppe|Anzahl|Gesamt|Kumulierte Anzahl von Anforderungen, die in die Warteschlange gestellt wurden, nachdem das Parallelitätslimit erreicht wurde|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Ja|Eingang empfangene Bytes|Byte|Gesamt|Die Anzahl der aus sämtlichen Ereignisquellen gelesenen Bytes|Keine Dimensionen|
|IngressReceivedInvalidMessages|Ja|Eingang empfangene ungültige Nachrichten|Anzahl|Gesamt|Die Anzahl der aus allen Event Hub- oder IoT Hub-Ereignisquellen gelesenen ungültigen Nachrichten|Keine Dimensionen|
|IngressReceivedMessages|Ja|Eingang empfangene Nachrichten|Anzahl|Gesamt|Die Anzahl der aus allen Event Hub- oder IoT Hub-Ereignisquellen gelesenen Nachrichten|Keine Dimensionen|
|IngressReceivedMessagesCountLag|Ja|Ingress-Anzahlrückstand empfangener Nachrichten|Anzahl|Average|Unterschied zwischen der Sequenznummer der Nachricht, die in der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der am Eingang verarbeiteten Nachricht|Keine Dimensionen|
|IngressReceivedMessagesTimeLag|Ja|Ingress-Zeitabstand empfangener Nachrichten|Sekunden|Maximum|Der Unterschied zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung in Ingress|Keine Dimensionen|
|IngressStoredBytes|Ja|Eingang gespeicherte Bytes|Byte|Gesamt|Die Gesamtgröße der erfolgreich verarbeiteten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressStoredEvents|Ja|Gespeicherte Ingress-Ereignisse|Anzahl|Gesamt|Die Gesamtgröße der vereinfachten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|WarmStorageMaxProperties|Ja|Maximale Eigenschaften von Warm Storage|Anzahl|Maximum|Maximale Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU zugelassen werden, und maximale Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU zugelassen werden|Keine Dimensionen|
|WarmStorageUsedProperties|Ja|Verwendete Eigenschaften von Warm Storage |Anzahl|Maximum|Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU verwendet werden, und Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU verwendet werden|Keine Dimensionen|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Ja|Eingang empfangene Bytes|Byte|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen Bytes|Keine Dimensionen|
|IngressReceivedInvalidMessages|Ja|Eingang empfangene ungültige Nachrichten|Anzahl|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen ungültigen Nachrichten|Keine Dimensionen|
|IngressReceivedMessages|Ja|Eingang empfangene Nachrichten|Anzahl|Gesamt|Die Anzahl der von der Ereignisquelle gelesenen Nachrichten|Keine Dimensionen|
|IngressReceivedMessagesCountLag|Ja|Ingress-Anzahlrückstand empfangener Nachrichten|Anzahl|Average|Unterschied zwischen der Sequenznummer der Nachricht, die in der Ereignisquelle zuletzt in die Warteschlange eingereiht wurde, und der Sequenznummer der am Eingang verarbeiteten Nachricht|Keine Dimensionen|
|IngressReceivedMessagesTimeLag|Ja|Ingress-Zeitabstand empfangener Nachrichten|Sekunden|Maximum|Der Unterschied zwischen dem Zeitpunkt, zu dem die Nachricht in der Ereignisquelle in die Warteschlange eingereiht wird, und dem Zeitpunkt der Verarbeitung in Ingress|Keine Dimensionen|
|IngressStoredBytes|Ja|Eingang gespeicherte Bytes|Byte|Gesamt|Die Gesamtgröße der erfolgreich verarbeiteten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|IngressStoredEvents|Ja|Gespeicherte Ingress-Ereignisse|Anzahl|Gesamt|Die Gesamtgröße der vereinfachten und für Abfragen verfügbaren Ereignisse|Keine Dimensionen|
|WarmStorageMaxProperties|Ja|Maximale Eigenschaften von Warm Storage|Anzahl|Maximum|Maximale Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU zugelassen werden, und maximale Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU zugelassen werden|Keine Dimensionen|
|WarmStorageUsedProperties|Ja|Verwendete Eigenschaften von Warm Storage |Anzahl|Maximum|Anzahl von Eigenschaften, die von der Umgebung für S1/S2-SKU verwendet werden, und Anzahl von Eigenschaften, die von Warm Store für PAYG-SKU verwendet werden|Keine Dimensionen|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|Datenträgerlesevorgänge in Bytes|Ja|Datenträgerlesevorgänge in Bytes|Byte|Gesamt|Gesamter Datenträgerdurchsatz aufgrund von Lesevorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|Datenträgerlesevorgänge/Sek.|Ja|Datenträgerlesevorgänge/Sek.|Anzahl pro Sekunde|Average|Die durchschnittliche Anzahl von E/A-Lesevorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|Datenträgerschreibvorgänge in Bytes|Ja|Datenträgerschreibvorgänge in Bytes|Byte|Gesamt|Gesamter Datenträgerdurchsatz aufgrund von Schreibvorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|Datenträgerschreibvorgänge/Sek.|Ja|Datenträgerschreibvorgänge/Sek.|Anzahl pro Sekunde|Average|Die durchschnittliche Anzahl von E/A-Schreibvorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|DiskReadBytesPerSecond|Ja|Disk Read Bytes/Sec|Bytes pro Sekunde|Average|Durchschnittlicher Datenträgerdurchsatz aufgrund von Lesevorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|DiskReadLatency|Ja|Wartezeit beim Datenträgerlesevorgang|Millisekunden|Average|Gesamtwartezeit beim Lesevorgang. Die Summe der Wartezeiten bei Lesevorgängen auf Gerät und Kernel.|Keine Dimensionen|
|DiskReadOperations|Ja|Datenträgerlesevorgänge|Anzahl|Gesamt|Die Anzahl von E/A-Lesevorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|DiskWriteBytesPerSecond|Ja|Disk Write Bytes/Sec|Bytes pro Sekunde|Average|Durchschnittlicher Datenträgerdurchsatz aufgrund von Schreibvorgängen über den Stichprobenzeitraum.|Keine Dimensionen|
|DiskWriteLatency|Ja|Wartezeit beim Datenträgerschreibvorgang|Millisekunden|Average|Gesamtwartezeit beim Schreibvorgang. Die Summe der Wartezeiten bei Schreibvorgängen auf Gerät und Kernel.|Keine Dimensionen|
|DiskWriteOperations|Ja|Datenträgerschreibvorgänge|Anzahl|Gesamt|Die Anzahl von E/A-Schreibvorgängen im vorherigen Stichprobenzeitraum. Beachten Sie, dass diese Vorgänge unterschiedlich groß sein können.|Keine Dimensionen|
|MemoryActive|Ja|Aktiver Arbeitsspeicher|Byte|Average|Die Menge an Arbeitsspeicher, die vom virtuellen Computer im letzten kleinen Zeitfenster verwendet wurde. Dies ist die „wirkliche“ Anzahl des derzeit vom virtuellen Computer benötigten Arbeitsspeichers. Zusätzlicher, ungenutzter Arbeitsspeicher kann ausgelagert oder ausgeweitet werden, ohne dass dies Auswirkungen auf die Leistung für den Gast hat.|Keine Dimensionen|
|MemoryGranted|Ja|Zugewiesener Arbeitsspeicher|Byte|Average|Die Menge an Arbeitsspeicher, die dem virtuellen Computer vom Host zugewiesen wurde. Arbeitsspeicher wird dem Host erst bei der ersten Verwendung zugewiesen, und zugewiesener Speicher kann ausgelagert oder ausgeweitet werden, wenn der VMkernel den Speicher benötigt.|Keine Dimensionen|
|MemoryUsed|Ja|Verwendeter Arbeitsspeicher|Byte|Average|Die Menge an Computerarbeitsspeicher, die vom virtuellen Computer verwendet wird.|Keine Dimensionen|
|Netzwerk eingehend|Ja|Netzwerk eingehend|Byte|Gesamt|Gesamter Netzwerkdurchsatz für empfangenen Datenverkehr.|Keine Dimensionen|
|Netzwerk ausgehend|Ja|Netzwerk ausgehend|Byte|Gesamt|Gesamter Netzwerkdurchsatz für gesendeten Datenverkehr.|Keine Dimensionen|
|NetworkInBytesPerSecond|Ja|Eingehender Netzwerkverkehr in Bytes/Sek.|Bytes pro Sekunde|Average|Durchschnittliche Netzwerkdurchsatz für empfangenen Datenverkehr.|Keine Dimensionen|
|NetworkOutBytesPerSecond|Ja|Ausgehender Netzwerkverkehr in Bytes/Sek.|Bytes pro Sekunde|Average|Durchschnittliche Netzwerkdurchsatz für gesendeten Datenverkehr.|Keine Dimensionen|
|CPU in Prozent|Ja|CPU in Prozent|Percent|Average|Die CPU-Auslastung. Dieser Wert wird mit 100 % gemeldet und stellt alle Prozessorkerne im System dar. Beispielsweise verwendet ein virtueller 2-Wege-Computer, der 50 % eines Systems mit vier Kernen nutzt, zwei dieser Kerne vollständig.|Keine Dimensionen|
|PercentageCpuReady|Ja|Prozentsatz der CPU-Bereitschaft|Millisekunden|Gesamt|Die Bereitschaftszeit ist die Zeit, über die im letzten Updateintervall auf die Verfügbarkeit von CPUs gewartet wurde.|Keine Dimensionen|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|ActiveRequests|Ja|Aktive Anforderungen|Anzahl|Gesamt|Aktive Anforderungen|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit|Sekunden|Average|Durchschnittliche Antwortzeit|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Instanz|
|DiskQueueLength|Ja|Warteschlangenlänge des Datenträgers|Anzahl|Average|Warteschlangenlänge des Datenträgers|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|HTTP 101|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|HTTP 2xx|Instanz|
|Http3xx|Ja|HTTP 3xx|Anzahl|Gesamt|HTTP 3xx|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|HTTP 401|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|HTTP 403|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|HTTP 404|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|HTTP 406|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|HTTP 4xx|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|HTTP-Serverfehler|Instanz|
|HttpQueueLength|Ja|Länge der HTTP-Warteschlange|Anzahl|Average|Länge der HTTP-Warteschlange|Instanz|
|LargeAppServicePlanInstances|Ja|Große Worker im App Service-Plan|Anzahl|Average|Große Worker im App Service-Plan|Keine Dimensionen|
|MediumAppServicePlanInstances|Ja|Mittlere Worker im App Service-Plan|Anzahl|Average|Mittlere Worker im App Service-Plan|Keine Dimensionen|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|Arbeitsspeicherprozentsatz|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Requests|Instanz|
|SmallAppServicePlanInstances|Ja|Kleine Worker im App Service-Plan|Anzahl|Average|Kleine Worker im App Service-Plan|Keine Dimensionen|
|TotalFrontEnds|Ja|Gesamtanzahl an Front-Ends|Anzahl|Average|Gesamtanzahl an Front-Ends|Keine Dimensionen|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Instanz|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|Arbeitsspeicherprozentsatz|Instanz|
|WorkersAvailable|Ja|Verfügbare Worker|Anzahl|Average|Verfügbare Worker|Keine Dimensionen|
|WorkersTotal|Ja|Gesamtanzahl an Workern|Anzahl|Average|Gesamtanzahl an Workern|Keine Dimensionen|
|WorkersUsed|Ja|Verwendete Worker|Anzahl|Average|Verwendete Worker|Keine Dimensionen|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|CpuPercentage|Ja|CPU-Prozentsatz|Percent|Average|CPU-Prozentsatz|Instanz|
|DiskQueueLength|Ja|Warteschlangenlänge des Datenträgers|Anzahl|Average|Warteschlangenlänge des Datenträgers|Instanz|
|HttpQueueLength|Ja|Länge der HTTP-Warteschlange|Anzahl|Average|Länge der HTTP-Warteschlange|Instanz|
|MemoryPercentage|Ja|Arbeitsspeicherprozentsatz|Percent|Average|Arbeitsspeicherprozentsatz|Instanz|
|SocketInboundAll|Ja|SocketInboundAll|Anzahl|Average|SocketInboundAll|Instanz|
|SocketLoopback|Ja|SocketLoopback|Anzahl|Average|SocketLoopback|Instanz|
|SocketOutboundAll|Ja|SocketOutboundAll|Anzahl|Average|SocketOutboundAll|Instanz|
|SocketOutboundEstablished|Ja|SocketOutboundEstablished|Anzahl|Average|SocketOutboundEstablished|Instanz|
|SocketOutboundTimeWait|Ja|SocketOutboundTimeWait|Anzahl|Average|SocketOutboundTimeWait|Instanz|
|TcpCloseWait|Ja|Warten auf Schließen der TCP-Verbindung|Anzahl|Average|Warten auf Schließen der TCP-Verbindung|Instanz|
|TcpClosing|Ja|TCP-Verbindung wird geschlossen|Anzahl|Average|TCP-Verbindung wird geschlossen|Instanz|
|TcpEstablished|Ja|TCP-Verbindung hergestellt|Anzahl|Average|TCP-Verbindung hergestellt|Instanz|
|TcpFinWait1|Ja|Warten 1 auf Beendigung der TCP-Verbindung|Anzahl|Average|Warten 1 auf Beendigung der TCP-Verbindung|Instanz|
|TcpFinWait2|Ja|Warten 2 auf Beendigung der TCP-Verbindung|Anzahl|Average|Warten 2 auf Beendigung der TCP-Verbindung|Instanz|
|TcpLastAck|Ja|Letzte TCP-Bestätigung|Anzahl|Average|Letzte TCP-Bestätigung|Instanz|
|TcpSynReceived|Ja|TCP-Synchronisierung empfangen|Anzahl|Average|TCP-Synchronisierung empfangen|Instanz|
|TcpSynSent|Ja|TCP-Synchronisierung gesendet|Anzahl|Average|TCP-Synchronisierung gesendet|Instanz|
|TcpTimeWait|Ja|TCP-Wartezeit|Anzahl|Average|TCP-Wartezeit|Instanz|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (Funktionen ausgenommen) 

> [!NOTE]
> **Dateisystemnutzung** ist eine neue Metrik, die global eingeführt wird. Es werden keine Daten erwartet, es sei denn, Sie wurden für die private Vorschau in die Whitelist aufgenommen.

> [!IMPORTANT]
> **Durchschnittliche Antwortzeit** wird eingestellt, um Verwechslungen mit Metrikaggregationen zu vermeiden. Verwenden Sie **Antwortzeit** als Ersatz.

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AppConnections|Ja|Verbindungen|Anzahl|Average|Verbindungen|Instanz|
|AverageMemoryWorkingSet|Ja|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Average|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit **(veraltet)**|Sekunden|Average|Durchschnittliche Antwortzeit|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|CpuTime|Ja|CPU-Zeit|Sekunden|Gesamt|CPU-Zeit|Instanz|
|CurrentAssemblies|Ja|Aktuelle Assemblys|Anzahl|Average|Aktuelle Assemblys|Instanz|
|FileSystemUsage|Ja|Dateisystemnutzung|Byte|Average|Dateisystemnutzung|Keine Dimensionen|
|Gen0Collections|Ja|Garbage Collections der Generation 0|Anzahl|Gesamt|Garbage Collections der Generation 0|Instanz|
|Gen1Collections|Ja|Garbage Collections der Generation 1|Anzahl|Gesamt|Garbage Collections der Generation 1|Instanz|
|Gen2Collections|Ja|Garbage Collections der Generation 2|Anzahl|Gesamt|Garbage Collections der Generation 2|Instanz|
|Ziehpunkte|Ja|Anzahl von Handles|Anzahl|Average|Anzahl von Handles|Instanz|
|HealthCheckStatus|Ja|Status der Integritätsüberprüfung|Anzahl|Average|Status der Integritätsüberprüfung|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|HTTP 101|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|HTTP 2xx|Instanz|
|Http3xx|HTTP 3xx|Anzahl|Gesamt|HTTP 3xx|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|HTTP 401|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|HTTP 403|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|HTTP 404|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|HTTP 406|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|HTTP 4xx|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|HTTP-Serverfehler|Instanz|
|HttpResponseTime|Ja|Antwortzeit|Sekunden|Average|Antwortzeit|Instanz|
|IoOtherBytesPerSecond|Ja|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Bytes pro Sekunde|Instanz|
|IoOtherOperationsPerSecond|Ja|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Vorgänge pro Sekunde|Instanz|
|IoReadBytesPerSecond|Ja|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Gelesene Bytes pro Sekunde|Instanz|
|IoReadOperationsPerSecond|Ja|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Lesevorgänge pro Sekunde|Instanz|
|IoWriteBytesPerSecond|Ja|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Geschriebene Bytes pro Sekunde|Instanz|
|IoWriteOperationsPerSecond|Ja|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Schreibvorgänge pro Sekunde|Instanz|
|MemoryWorkingSet|Ja|Arbeitssatz für Arbeitsspeicher|Byte|Average|Arbeitssatz für Arbeitsspeicher|Instanz|
|PrivateBytes|Ja|Private Bytes|Byte|Average|Private Bytes|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Requests|Instanz|
|RequestsInApplicationQueue|Ja|Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Anforderungen in der Anwendungswarteschlange|Instanz|
|Threads|Ja|Threadanzahl|Anzahl|Average|Threadanzahl|Instanz|
|TotalAppDomains|Ja|App-Domänen insgesamt|Anzahl|Average|App-Domänen insgesamt|Instanz|
|TotalAppDomainsUnloaded|Ja|Entladene App-Domänen insgesamt|Anzahl|Average|Entladene App-Domänen insgesamt|Instanz|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Funktionen)

> [!NOTE]
> **Dateisystemnutzung** ist eine neue Metrik, die global eingeführt wird. Es werden keine Daten erwartet, es sei denn, Sie wurden für die private Vorschau in die Whitelist aufgenommen.

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|Ja|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Average|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|CurrentAssemblies|Ja|Aktuelle Assemblys|Anzahl|Average|Aktuelle Assemblys|Instanz|
|FileSystemUsage|Ja|Dateisystemnutzung|Byte|Average|Dateisystemnutzung|Keine Dimensionen|
|FunctionExecutionCount|Ja|Ausführungsanzahl für Funktion|Anzahl|Gesamt|Ausführungsanzahl für Funktion|Instanz|
|FunctionExecutionUnits|Ja|Ausführungseinheiten für Funktion|Anzahl|Gesamt|[Ausführungseinheiten für Funktion](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instanz|
|Gen0Collections|Ja|Garbage Collections der Generation 0|Anzahl|Gesamt|Garbage Collections der Generation 0|Instanz|
|Gen1Collections|Ja|Garbage Collections der Generation 1|Anzahl|Gesamt|Garbage Collections der Generation 1|Instanz|
|Gen2Collections|Ja|Garbage Collections der Generation 2|Anzahl|Gesamt|Garbage Collections der Generation 2|Instanz|
|HealthCheckStatus|Ja|Status der Integritätsüberprüfung|Anzahl|Average|Status der Integritätsüberprüfung|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|HTTP-Serverfehler|Instanz|
|IoOtherBytesPerSecond|Ja|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Bytes pro Sekunde|Instanz|
|IoOtherOperationsPerSecond|Ja|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Vorgänge pro Sekunde|Instanz|
|IoReadBytesPerSecond|Ja|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Gelesene Bytes pro Sekunde|Instanz|
|IoReadOperationsPerSecond|Ja|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Lesevorgänge pro Sekunde|Instanz|
|IoWriteBytesPerSecond|Ja|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Geschriebene Bytes pro Sekunde|Instanz|
|IoWriteOperationsPerSecond|Ja|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Schreibvorgänge pro Sekunde|Instanz|
|MemoryWorkingSet|Ja|Arbeitssatz für Arbeitsspeicher|Byte|Average|Arbeitssatz für Arbeitsspeicher|Instanz|
|PrivateBytes|Ja|Private Bytes|Byte|Average|Private Bytes|Instanz|
|RequestsInApplicationQueue|Ja|Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Anforderungen in der Anwendungswarteschlange|Instanz|
|TotalAppDomains|Ja|App-Domänen insgesamt|Anzahl|Average|App-Domänen insgesamt|Instanz|
|TotalAppDomainsUnloaded|Ja|Entladene App-Domänen insgesamt|Anzahl|Average|Entladene App-Domänen insgesamt|Instanz|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrik|Über Diagnoseeinstellungen exportierbar?|Metrikanzeigename|Einheit|Aggregationstyp|BESCHREIBUNG|Dimensionen|
|---|---|---|---|---|---|---|
|AppConnections|Ja|Verbindungen|Anzahl|Average|Verbindungen|Instanz|
|AverageMemoryWorkingSet|Ja|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Byte|Average|Durchschnittlicher Arbeitssatz für Arbeitsspeicher|Instanz|
|AverageResponseTime|Ja|Durchschnittliche Antwortzeit|Sekunden|Average|Durchschnittliche Antwortzeit|Instanz|
|BytesReceived|Ja|Eingehende Daten|Byte|Gesamt|Eingehende Daten|Instanz|
|BytesSent|Ja|Datenausgabe|Byte|Gesamt|Datenausgabe|Instanz|
|CpuTime|Ja|CPU-Zeit|Sekunden|Gesamt|CPU-Zeit|Instanz|
|CurrentAssemblies|Ja|Aktuelle Assemblys|Anzahl|Average|Aktuelle Assemblys|Instanz|
|FileSystemUsage|Ja|Dateisystemnutzung|Byte|Average|Dateisystemnutzung|Keine Dimensionen|
|FunctionExecutionCount|Ja|Ausführungsanzahl für Funktion|Anzahl|Gesamt|Ausführungsanzahl für Funktion|Instanz|
|FunctionExecutionUnits|Ja|Ausführungseinheiten für Funktion|Anzahl|Gesamt|Ausführungseinheiten für Funktion|Instanz|
|Gen0Collections|Ja|Garbage Collections der Generation 0|Anzahl|Gesamt|Garbage Collections der Generation 0|Instanz|
|Gen1Collections|Ja|Garbage Collections der Generation 1|Anzahl|Gesamt|Garbage Collections der Generation 1|Instanz|
|Gen2Collections|Ja|Garbage Collections der Generation 2|Anzahl|Gesamt|Garbage Collections der Generation 2|Instanz|
|Ziehpunkte|Ja|Anzahl von Handles|Anzahl|Average|Anzahl von Handles|Instanz|
|HealthCheckStatus|Ja|Status der Integritätsüberprüfung|Anzahl|Average|Status der Integritätsüberprüfung|Instanz|
|Http101|Ja|HTTP 101|Anzahl|Gesamt|HTTP 101|Instanz|
|Http2xx|Ja|HTTP 2xx|Anzahl|Gesamt|HTTP 2xx|Instanz|
|Http3xx|Ja|HTTP 3xx|Anzahl|Gesamt|HTTP 3xx|Instanz|
|Http401|Ja|HTTP 401|Anzahl|Gesamt|HTTP 401|Instanz|
|Http403|Ja|HTTP 403|Anzahl|Gesamt|HTTP 403|Instanz|
|Http404|Ja|HTTP 404|Anzahl|Gesamt|HTTP 404|Instanz|
|Http406|Ja|HTTP 406|Anzahl|Gesamt|HTTP 406|Instanz|
|Http4xx|Ja|HTTP 4xx|Anzahl|Gesamt|HTTP 4xx|Instanz|
|Http5xx|Ja|HTTP-Serverfehler|Anzahl|Gesamt|HTTP-Serverfehler|Instanz|
|HttpResponseTime|Ja|Antwortzeit|Sekunden|Average|Antwortzeit|Instanz|
|IoOtherBytesPerSecond|Ja|E/A: Andere Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Bytes pro Sekunde|Instanz|
|IoOtherOperationsPerSecond|Ja|E/A: Andere Vorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Andere Vorgänge pro Sekunde|Instanz|
|IoReadBytesPerSecond|Ja|E/A: Gelesene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Gelesene Bytes pro Sekunde|Instanz|
|IoReadOperationsPerSecond|Ja|E/A: Lesevorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Lesevorgänge pro Sekunde|Instanz|
|IoWriteBytesPerSecond|Ja|E/A: Geschriebene Bytes pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Geschriebene Bytes pro Sekunde|Instanz|
|IoWriteOperationsPerSecond|Ja|E/A: Schreibvorgänge pro Sekunde|Bytes pro Sekunde|Gesamt|E/A: Schreibvorgänge pro Sekunde|Instanz|
|MemoryWorkingSet|Ja|Arbeitssatz für Arbeitsspeicher|Byte|Average|Arbeitssatz für Arbeitsspeicher|Instanz|
|PrivateBytes|Ja|Private Bytes|Byte|Average|Private Bytes|Instanz|
|Requests|Ja|Requests|Anzahl|Gesamt|Requests|Instanz|
|RequestsInApplicationQueue|Ja|Anforderungen in der Anwendungswarteschlange|Anzahl|Average|Anforderungen in der Anwendungswarteschlange|Instanz|
|Threads|Ja|Threadanzahl|Anzahl|Average|Threadanzahl|Instanz|
|TotalAppDomains|Ja|App-Domänen insgesamt|Anzahl|Average|App-Domänen insgesamt|Instanz|
|TotalAppDomainsUnloaded|Ja|Entladene App-Domänen insgesamt|Anzahl|Average|Entladene App-Domänen insgesamt|Instanz|


## <a name="next-steps"></a>Nächste Schritte
* [Informationen zu Metriken in Azure Monitor](data-platform.md)
* [Erstellen von Warnungen zu Metriken](alerts-overview.md)
* [Exportieren von Metriken in Storage, Event Hub oder Log Analytics](platform-logs-overview.md)
