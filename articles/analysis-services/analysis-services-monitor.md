---
title: Überwachen von Servermetriken in Azure Analysis Services | Microsoft-Dokumentation
description: Informationen zum Überwachen von Analysis Services-Servermetriken im Azure-Portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61065047"
---
# <a name="monitor-server-metrics"></a>Überwachen von Servermetriken

Analysis Services bietet Metriken zum Überwachen von Leistung und Integrität Ihres Servers. Überwachen Sie z.B. Arbeitsspeicher- und CPU-Nutzung, Anzahl der Clientverbindungen und Ressourcenverbrauch für Abfragen. Analysis Services verwendet das gleiche Überwachungsframework wie die meisten anderen Azure-Dienste. Weitere Informationen finden Sie unter [Überblick über Metriken in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Um eine ausführlichere Diagnose auszuführen, Leistung nachzuverfolgen und mehrere Dienstressourcen in einer Ressourcengruppe oder einem Abonnement übergreifende Trends zu identifizieren, verwenden Sie [Azure Monitor](https://azure.microsoft.com/services/monitor/). Die Leistungen des Diensts Azure Monitor werden möglicherweise in Rechnung gestellt.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>So überwachen Sie Metriken für einen Analysis Services-Server

1. Wählen Sie im Azure-Portal die Option **Metriken** aus.

    ![Überwachen im Azure-Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Wählen Sie in **Verfügbare Metriken** die Metriken aus, die Sie in Ihr Diagramm aufnehmen möchten. 

    ![Überwachungsdiagramm](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Servermetriken

Bestimmen Sie mit dieser Tabelle, welche Metriken für Ihr Überwachungsszenario am besten geeignet sind. Nur Metriken derselben Einheit können im selben Diagramm angezeigt werden.

|Metrik|Metrikanzeigename|Unit|Aggregationstyp|BESCHREIBUNG|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Warteschlangenlänge für Aufträge im Befehlspool|Count|Durchschnitt|Gibt die Anzahl von Aufträgen in der Warteschlange des Befehlsthreadpools an.|
|CurrentConnections|Verbindung: Aktuelle Verbindungen|Count|Durchschnitt|Aktuelle Anzahl hergestellter Clientverbindungen.|
|CurrentUserSessions|Aktuelle Benutzersitzungen|Count|Durchschnitt|Aktuelle Anzahl von eingerichteten Benutzersitzungen|
|mashup_engine_memory_metric|M-Engine – Arbeitsspeicher|Byte|Durchschnitt|Arbeitsspeichernutzung durch Mashup-Engine-Prozesse|
|mashup_engine_qpu_metric|M-Engine – QPU|Count|Durchschnitt|QPU-Nutzung durch Mashup-Engine-Prozesse|
|memory_metric|Arbeitsspeicher|Byte|Durchschnitt|Arbeitsspeicher. Bereich: 0–25 GB für S1, 0–50 GB für S2 und 0–100 GB für S4|
|memory_thrashing_metric|Arbeitsspeicherüberlastung|Percent|Durchschnitt|Durchschnittliche Arbeitsspeicherüberlastung.|
|CleanerCurrentPrice|Arbeitsspeicher: Bereinigung – aktueller Preis|Count|Durchschnitt|Aktueller Preis des Arbeitsspeichers, $/Byte/Zeit, normalisiert auf 1000.|
|CleanerMemoryNonshrinkable|Arbeitsspeicher: Bereinigung – nicht verkleinerbarer Arbeitsspeicher|Byte|Durchschnitt|Die Menge des Arbeitsspeichers in Byte, die nicht durch den Hintergrundbereinigungsprozess bereinigt wird.|
|CleanerMemoryShrinkable|Arbeitsspeicher: Bereinigung – verkleinerbarer Arbeitsspeicher|Byte|Durchschnitt|Die Menge des Arbeitsspeichers in Byte, die durch den Hintergrundbereinigungsprozess bereinigt wird.|
|MemoryLimitHard|Arbeitsspeicher: Grenzwert für den festen Speicher|Byte|Durchschnitt|Grenzwert für den festen Speicher gemäß Konfigurationsdatei.|
|MemoryLimitHigh|Arbeitsspeicher: Obere Arbeitsspeichergrenze|Byte|Durchschnitt|Oberer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|
|MemoryLimitLow|Arbeitsspeicher: Untere Arbeitsspeichergrenze|Byte|Durchschnitt|Unterer Grenzwert für den Arbeitsspeicher gemäß Konfigurationsdatei.|
|MemoryLimitVertiPaq|Arbeitsspeicher: VertiPaq-Arbeitsspeichergrenze|Byte|Durchschnitt|In-Memory-Grenzwert gemäß Konfigurationsdatei.|
|MemoryUsage|Arbeitsspeicher: Speicherauslastung|Byte|Durchschnitt|Speicherauslastung des Serverprozesses, wie bei der Berechnung des Arbeitsspeicherpreises für die Bereinigung verwendet. Entspricht dem Indikator „Process\PrivateBytes“ zuzüglich der Größe der im Speicher abgebildeten Daten. Von der In-Memory-Analyse-Engine (VertiPaq) abgebildeter oder belegter Arbeitsspeicher, der über die Arbeitsspeichergrenze der Engine hinausgeht, wird dabei ignoriert.|
|Kontingent|Arbeitsspeicher: Kontingent|Byte|Durchschnitt|Aktuelles Arbeitsspeicherkontingent in Byte. Das Arbeitsspeicherkontingent wird auch als Speicherzuweisung oder Speicherreservierung bezeichnet.|
|QuotaBlocked|Arbeitsspeicher: Kontingent blockiert|Count|Durchschnitt|Aktuelle Anzahl von Kontingentanforderungen, die blockiert werden, bis andere Arbeitsspeicherkontingente freigegeben werden.|
|VertiPaqNonpaged|Arbeitsspeicher: Nicht ausgelagerte VertiPaq-Daten|Byte|Durchschnitt|Bytes von Arbeitsspeicher, die im Arbeitssatz zur Verwendung durch die In-Memory-Engine gesperrt sind.|
|VertiPaqPaged|Arbeitsspeicher: Ausgelagerte VertiPaq-Daten|Byte|Durchschnitt|Bytes von ausgelagertem Arbeitsspeicher, die für In-Memory-Daten verwendet werden.|
|ProcessingPoolJobQueueLength|Warteschlangenlänge für Verarbeitungspoolaufträge|Count|Durchschnitt|Anzahl von Nicht-E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools|
|RowsConvertedPerSec|Verarbeitung: Konvertierte Zeilen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der Zeilen, die bei der Verarbeitung konvertiert werden.|
|RowsReadPerSec|Verarbeitung: Gelesene Zeilen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der aus allen relationalen Datenbanken gelesenen Zeilen.|
|RowsWrittenPerSec|Verarbeitung: Geschriebene Zeilen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der Zeilen, die bei der Verarbeitung geschrieben werden.|
|qpu_metric|QPU|Count|Durchschnitt|QPU. Bereich: 0–100 für S1, 0–200 für S2 und 0–400 für S4|
|QueryPoolBusyThreads|Ausgelastete Abfragepoolthreads|Count|Durchschnitt|Anzahl von ausgelasteten Threads im Abfragethreadpool|
|SuccessfullConnectionsPerSec|Erfolgreiche Verbindungen pro Sekunde|Anzahl pro Sekunde|Durchschnitt|Rate der erfolgreichen Verbindungsabschlüsse|
|CommandPoolBusyThreads|Threads: Ausgelastete Threads im Befehlspool|Count|Durchschnitt|Anzahl ausgelasteter Threads im Befehlsthreadpool.|
|CommandPoolIdleThreads|Threads: Leerlaufthreads im Befehlspool|Count|Durchschnitt|Anzahl von Leerlaufthreads im Befehlsthreadpool.|
|LongParsingBusyThreads|Threads: Ausgelastete lange Analysethreads|Count|Durchschnitt|Anzahl ausgelasteter Threads im Pool für lange Analysethreads.|
|LongParsingIdleThreads|Threads: Lange Analysethreads im Leerlauf|Count|Durchschnitt|Anzahl von Leerlaufthreads im Pool für lange Analysethreads.|
|LongParsingJobQueueLength|Threads: Warteschlangenlänge für lange Analyseaufträge|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Pools für lange Analysethreads.|
|ProcessingPoolIOJobQueueLength|Threads: Warteschlangenlänge für E/A-Aufträge im Verarbeitungspool|Count|Durchschnitt|Anzahl von E/A-Aufträgen in der Warteschlange des Verarbeitungsthreadpools.|
|ProcessingPoolBusyIOJobThreads|Threads: Ausgelastete Threads für E/A-Aufträge im Verarbeitungspool|Count|Durchschnitt|Anzahl von Threads, die E/A-Aufträge im Verarbeitungsthreadpool ausführen.|
|ProcessingPoolBusyNonIOThreads|Threads: Ausgelastete Nicht-E/A-Threads im Verarbeitungspool|Count|Durchschnitt|Anzahl von Threads, die Nicht-E/A-Aufträge im Verarbeitungsthreadpool ausführen.|
|ProcessingPoolIdleIOJobThreads|Threads: Leerlaufthreads für E/A-Aufträge im Verarbeitungspool|Count|Durchschnitt|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|
|ProcessingPoolIdleNonIOThreads|Threads: Nicht-E/A-Leerlaufthreads im Verarbeitungspool|Count|Durchschnitt|Anzahl von Leerlaufthreads im Verarbeitungsthreadpool, die für Nicht-E/A-Aufträge vorgesehen sind.|
|QueryPoolIdleThreads|Threads: Abfragepoolthreads im Leerlauf|Count|Durchschnitt|Anzahl von Leerlaufthreads für E/A-Aufträge im Verarbeitungsthreadpool.|
|QueryPoolJobQueueLength|Threads: Auftragswarteschlangenlänge für Abfragepool|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Abfragethreadpools.|
|ShortParsingBusyThreads|Threads: Ausgelastete kurze Analysethreads|Count|Durchschnitt|Anzahl ausgelasteter Threads im Pool für kurze Analysethreads.|
|ShortParsingIdleThreads|Threads: Kurze Analysethreads im Leerlauf|Count|Durchschnitt|Anzahl von Leerlaufthreads im Pool für kurze Analysethreads.|
|ShortParsingJobQueueLength|Threads: Warteschlangenlänge für kurze Analyseaufträge|Count|Durchschnitt|Anzahl von Aufträgen in der Warteschlange des Pools für kurze Analysethreads.|
|TotalConnectionFailures|Verbindungsfehler gesamt|Count|Durchschnitt|Gesamtanzahl von fehlerhaften Verbindungsversuchen|
|TotalConnectionRequests|Total Connection Requests (Verbindungsanforderungen gesamt)|Count|Durchschnitt|Gesamtanzahl von Verbindungsanforderungen. |

## <a name="next-steps"></a>Nächste Schritte
[Überwachung in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Überblick über Metriken in Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metriken](/rest/api/monitor/metrics)
