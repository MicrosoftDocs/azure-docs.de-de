---
title: Überwachen der Datenbankleistung mit Intelligent Insights
description: Die integrierte Intelligent Insights-Logik in Azure SQL-Datenbank und verwalteten Azure SQL-Instanzen überwacht kontinuierlich die Datenbanknutzung durch künstliche Intelligenz und ermittelt Störungen, die zu schlechter Leistung führen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 0f3dce3ca79b12b05325a1d8284dd3304653d5fa
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488866"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights mit KI für die Überwachung und Problembehandlung der Datenbankleistung (Vorschau)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Intelligent Insights in Azure SQL-Datenbank und Azure SQL Managed Instance informiert Sie über die Leistung Ihrer Datenbank.

Die in Intelligent Insights integrierte Logik überwacht kontinuierlich die Datenbanknutzung durch künstliche Intelligenz und ermittelt Störungen, die zu mangelhafter Leistung führen. Nach der Ermittlung wird eine detaillierte Analyse durchgeführt, die ein Intelligent Insights-Ressourcenprotokoll (namens SQLInsights) mit einer intelligenten Bewertung des Problems generiert. Die Bewertung besteht aus einer Analyse der Grundursache des Leistungsproblems der Datenbank und nach Möglichkeit Empfehlungen für Leistungsverbesserungen.

## <a name="what-can-intelligent-insights-do-for-you"></a>Was kann Intelligent Insights für Sie tun?

Bei Intelligent Insights handelt es sich um eine einzigartige Funktion der in Azure integrierten Logik, die folgende Werte bereitstellt:

- Proaktive Überwachung
- Angepasste Einblicke in die Leistung
- Frühe Erkennung von Leistungsminderungen der Datenbank
- Analyse der Grundursache der erkannten Probleme
- Empfehlungen für Leistungsverbesserungen
- Funktion für das Aufskalieren von einigen Hunderttausend Datenbanken
- Positive Auswirkung auf DevOps-Ressourcen und die Gesamtkosten

## <a name="how-does-intelligent-insights-work"></a>Wie funktioniert Intelligent Insights?

Intelligent Insights analysiert die Datenbankleistung, indem die Datenbankworkload der letzten Stunde mit der Baselineworkload der letzten sieben Tage verglichen wird. Die Datenbankworkload besteht aus den Abfragen, die für die Datenbankleistung am wichtigsten sind, etwa die am meisten wiederholten und die größten Abfragen. Da jede Datenbank aufgrund ihrer Struktur, Daten, Nutzung und Anwendung eindeutig ist, ist jede generierte Workloadbaseline für die jeweilige Workload spezifisch und eindeutig. Intelligent Insights überwacht unabhängig von der Workloadbaseline auch die absoluten Betriebsschwellenwerte und erkennt Probleme mit übermäßigen Wartezeiten, kritische Ausnahmen und Probleme mit der Abfrageparametrisierung, die sich auf die Leistung auswirken können.

Nachdem eine beeinträchtigte Leistung aus mehreren erfassten Metriken mithilfe von künstlicher Intelligenz erkannt wird, erfolgt eine Analyse. Ein Diagnoseprotokoll wird erzeugt und ermöglicht einen intelligenten Einblick in die Vorgänge mit Ihrer Datenbank. Durch Intelligent Insights kann das Problem mit der Datenbankleistung vom ersten Auftreten bis hin zu dessen Behebung leicht nachverfolgt werden. Jedes ermittelte Problem wird während seines Lebenszyklus von der ersten Erkennung des Problems und der Überprüfung der Leistungssteigerung bis hin zur Behebung nachverfolgt.

![Workflow der Datenbankleistungsanalyse](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Die Metriken zum Messen und Erkennen von Leistungsproblemen bei Datenbanken basieren auf der Abfragedauer, Anforderungen mit Zeitüberschreitung, übermäßigen Wartezeiten und fehlerhaften Anforderungen. Weitere Informationen zu Metriken finden Sie unter [Erkennungsmetriken](#detection-metrics).

Festgestellte Leistungsbeeinträchtigungen der Datenbank werden im SQLInsights-Protokoll mit intelligenten Einträgen aufgezeichnet, die folgende Eigenschaften enthalten:

| Eigenschaft | Details |
| :------------------- | ------------------- |
| Datenbankinformationen | Metadaten zu einer Datenbank, für die ein Einblick erkannt wurde, z.B. ein Ressourcen-URI |
| Beobachteter Zeitbereich | Start- und Endzeit für den Zeitraum des erkannten Einblicks |
| Betroffene Metriken | Metriken, die das Generieren eines Einblicks verursachten: <ul><li>Erhöhte Abfragedauer [Sekunden]</li><li>Übermäßige Wartezeiten [Sekunden]</li><li>Zeitüberschreitung bei Anforderungen [Sekunden]</li><li>Fehler bei Anforderungen [Sekunden]</li></ul>|
| Auswirkungswert | Gemessener Wert einer Metrik |
| Betroffene Abfragen und Fehlercodes | Abfragehash oder Fehlercode Diese können für eine einfache Korrelation mit betroffenen Abfragen verwendet werden. Metriken werden bereitgestellt, die aus erhöhten Abfragedauern, Wartezeiten, Zeitüberschreitungen oder Fehlercodes bestehen. |
| Erkennungen | Während eines Ereignisses wurde eine Erkennung auf der Datenbank identifiziert. Es gibt 15 Erkennungsmuster. Nähere Informationen finden Sie unter [Troubleshoot database performance issues with Intelligent Insights (Behandeln von Problemen mit der Leistung mithilfe von Intelligent Insights)](intelligent-insights-troubleshoot-performance.md). |
| Analyse der Grundursache | Die Analyse der Grundursache des Problems, die in einem von Menschen lesbaren Format identifiziert wurde. Einige Einblicke enthalten nach Möglichkeit Empfehlungen zur Leistungsverbesserung. |
|||

Einen praktischen Überblick über die Verwendung von Intelligent Insights mit der Azure SQL-Analyse sowie typische Anwendungsszenarien finden Sie in diesem Video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights glänzt bei der Erkennung und Fehlerbehebung von Datenbankleistungsproblemen. Informationen, wie Intelligent Insights verwendet werden kann, um Probleme mit der Datenbankleistung zu behandeln, finden Sie unter [Behandeln von Leistungsproblemen mithilfe von Intelligent Insights](intelligent-insights-troubleshoot-performance.md).

## <a name="intelligent-insights-options"></a>Intelligent Insights-Optionen

Die folgenden Intelligent Insights-Optionen sind verfügbar:

| Intelligent Insights-Option | Unterstützung für Azure-SQL-Datenbank | Unterstützung verwalteter Azure SQL-Instanzen |
| :----------------------------- | ----- | ----- |
| **Configure Intelligent Insights** (Intelligent Insights konfigurieren): Konfigurieren Sie eine Intelligent Insights-Analyse für Ihre Datenbanken. | Ja | Ja |
| **Stream insights to Azure SQL Analytics** (Erkenntnisse an die Azure SQL-Analyse streamen): Streamen Sie Erkenntnisse an Azure SQL-Analyse. | Ja | Ja |
| **Stream insights to Azure Event Hubs** (Erkenntnisse an Azure Event Hubs streamen): Streamen Sie Erkenntnisse an Event Hubs, damit weitere benutzerdefinierte Integrationen erfolgen können. | Ja | Ja |
| **Stream insights to Azure Storage** (Erkenntnisse an Azure Storage streamen): Streamen Sie Erkenntnisse an Azure Storage, damit diese genauer analysiert und langfristig archiviert werden können. | Ja | Ja |

> [!NOTE]
> Intelligent Insights ist eine Vorschaufunktion, die in den folgenden Regionen nicht verfügbar ist: „Europa, Westen“, „Europa, Norden“, „USA, Westen 1“ und „USA, Osten 1“.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Konfigurieren des Exports des Intelligent Insights-Protokolls

Die Ausgabe von Intelligent Insights kann zur Analyse an eines von mehreren Zielen gestreamt werden:

- Die an einen Log Analytics-Arbeitsbereich gestreamte Ausgabe kann mit [Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md) verwendet werden, um sich auf der Benutzeroberfläche des Azure-Portals Einblicke zu verschaffen. Diese integrierte Azure-Lösung ist die am häufigsten verwendete Anzeigemöglichkeit für Erkenntnisse.
- Die an Azure Event Hubs gestreamte Ausgabe kann für die Entwicklung benutzerdefinierter Überwachungs- und Benachrichtigungsszenarien verwendet werden.
- Die an Azure Storage gestreamte Ausgabe kann für die Entwicklung benutzerdefinierter Anwendungen genutzt werden, wie z. B. benutzerdefinierte Berichte, langfristige Datenarchivierung usw.

Die Integration von Azure SQL-Analyse, Azure Event Hubs, Azure Storage oder Drittanbieterprodukten erfolgt, indem zunächst auf dem Blatt „Diagnoseeinstellungen“ einer Datenbank die Intelligent Insights-Protokollierung (das Protokoll „SQLInsights“) aktiviert wird. Anschließend müssen die Intelligent Insights-Protokolldaten für das Streaming an eines dieser Ziele konfiguriert werden.

Weitere Informationen dazu, wie die Intelligent Insights-Protokollierung aktiviert wird und wie Metrik- und Ressourcenprotokolldaten für das Streaming an ein nutzendes Produkt konfiguriert werden, finden Sie unter [Protokollierung von Metriken und Diagnosen](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Einrichten mit Azure SQL-Analyse

Die Lösung „Azure SQL-Analyse“ bietet eine grafische Benutzeroberfläche sowie unter Verwendung der Intelligent Insights-Ressourcenprotokolldaten Berichts- und Warnfunktionen zur Datenbankleistung.

Fügen Sie Azure SQL-Analyse über Marketplace Ihrem Azure-Portaldashboard hinzu, und erstellen Sie einen Arbeitsbereich. Informationen dazu finden Sie unter [Konfigurieren der Azure SQL-Analyse](../../azure-monitor/insights/azure-sql.md#configuration).

Um Intelligent Insights mit der Azure SQL-Analyse zu verwenden, konfigurieren Sie Intelligent Insights-Protokolldaten so, dass sie an den Arbeitsbereich der Azure SQL-Analyse übertragen werden, den Sie im vorhergehenden Schritt erstellt haben. Informationen hierzu finden Sie unter [Protokollierung von Metriken und Diagnosen](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

Im folgenden Beispiel wird ein Intelligent Insights-Bericht in der Azure SQL-Analyse dargestellt:

![Intelligent Insights-Bericht](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Einrichten mit Event Hubs

Um Intelligent Insights mit Event Hubs zu verwenden, konfigurieren Sie Intelligent Insights-Protokolldaten für das Streaming an Event Hubs. Informationen dazu finden Sie unter [Protokollierung von Metriken und Diagnosen](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) und [Streamen von Azure-Diagnoseprotokollen an Event Hubs](../../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs).

Wenn Sie Event Hubs zum Einrichten von benutzerdefinierten Überwachungen und Warnungen verwenden möchten, lesen Sie [Welche Vorgänge können mit Metrik- und Diagnoseprotokollen in Event Hubs ausgeführt werden?](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)

### <a name="set-up-with-azure-storage"></a>Einrichten mit Azure Storage

Um Intelligent Insights mit Azure Storage zu verwenden, konfigurieren Sie Intelligent Insights-Protokolldaten für das Streaming an Log Analytics. Informationen dazu finden Sie unter [Protokollierung von Metriken und Diagnosen](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) und [Streamen nach Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Benutzerdefinierte Integrationen von Intelligent Insights-Protokollen

Informationen zum Verwenden von Intelligent Insights mit Drittanbietertools oder zum Entwickeln von benutzerdefinierten Warnungen und Überwachungen finden Sie unter [Verwenden des Intelligent Insights-Diagnoseprotokolls für Leistungsprobleme bei Azure SQL-Datenbank und Azure SQL Managed Instance](intelligent-insights-use-diagnostics-log.md).

## <a name="detection-metrics"></a>Erkennungsmetriken

Metriken, die für Erkennungsmodelle verwendet werden, die intelligente Einblicke generieren, basieren auf der Überwachung der:

- Abfragedauer
- Anforderungen mit Zeitüberschreitung
- Übermäßigen Wartezeiten
- Fehlerhaften Anforderungen

Die Abfragedauer und Anforderungen mit Zeitüberschreitung werden als primäre Modelle beim Erkennen von Problemen mit der Workloadleistung der Datenbank verwendet. Sie werden verwendet, da sie direkt die Vorgänge in der Workload messen. Zum Erkennen aller möglichen Fälle von Leistungsminderungen der Workload werden übermäßige Wartezeiten und fehlerhafte Anforderungen als zusätzliche Modelle verwendet, die Probleme mit Auswirkung auf die Leistung der Workload angeben.

Das System berücksichtigt automatisch Änderungen an der Workload und Änderungen an der Anzahl von Abfrageanforderungen, die an die Datenbank gesendet werden, um die normalen und abweichenden Schwellenwerte der Datenbankleistung dynamisch zu bestimmen.

Alle Metriken werden in verschiedenen Beziehungen über ein wissenschaftlich abgeleitetes Datenmodell berücksichtigt, das die erkannten Leistungsprobleme kategorisiert. Über Intelligent Insights bereitgestellte Informationen umfassen:

- Details zum erkannten Leistungsproblem
- Eine Analyse der Grundursache des erkannten Problems
- Empfehlungen zu möglichen Verbesserungen der Leistung der überwachten Datenbank

## <a name="query-duration"></a>Abfragedauer

Das Modell für die Leistungsminderung durch die Abfragedauer analysiert einzelne Abfragen und erkennt erhöhte Kompilier- und Ausführungszeiten für eine Abfrage im Vergleich zur Leistungsbaseline.

Wenn die integrierte Logik eine beträchtliche Zunahme der Kompilier- oder Ausführungszeit einer Abfrage erkennt, die die Workloadleistung beeinträchtigt, werden diese Abfragen mit Leistungsminderungsproblemen bei der Abfrage gekennzeichnet.

Das Intelligent Insights-Diagnoseprotokoll gibt den Abfragehash der Abfrage aus, deren Leistung beeinträchtigt ist. Der Abfragehash gibt an, ob die Leistungsminderung auf eine Erhöhung der Kompilierungs- oder der Ausführungsdauer und dadurch eine Erhöhung der Abfragedauer zurückzuführen ist.

## <a name="timeout-requests"></a>Anforderungen mit Zeitüberschreitung

Das Modell für die Leistungsminderung durch Abfragen mit Zeitüberschreitung analysiert einzelne Abfragen und erkennt erhöhte Zeitüberschreitungen auf Ausführungsebene der Abfragen sowie die gesamten Zeitüberschreitungen von Anforderungen auf Datenbankebene im Vergleich zum Zeitraum der Leistungsbaseline.

Bei einigen Abfragen tritt eventuell eine Zeitüberschreitung auf, bevor sie in die Ausführungsphase gelangen. Mithilfe der Mittelwerte der abgebrochenen Worker im Vergleich zu den stattgefundenen Anforderungen misst und analysiert die integrierte Logik alle Abfragen, die die Datenbank erreicht haben, unabhängig davon, ob sie in die Ausführungsphase gelangt sind.

Nachdem die Anzahl von Zeitüberschreitungen für ausgeführte Abfragen oder von abgebrochenen Anforderungsworkern den vom System verwalteten Schwellenwert überschreitet, wird ein Diagnoseprotokoll mit intelligenten Einblicken aufgefüllt.

Die generierten Einblicke enthalten die Anzahl der Anforderungen mit Zeitüberschreitungen und die Anzahl der Abfragen mit Zeitüberschreitungen. Es wird eine Angabe bereitgestellt, ob die Leistungsminderung im Zusammenhang mit der vermehrten Zeitüberschreitungen in der Ausführungsphase steht, oder die allgemeine Datenbankebene wird bereitgestellt. Wenn die vermehrten Zeitüberschreitungen als relevant für die Datenbankleistung angesehen werden, werden diese Abfragen mit Leistungsproblemen durch Zeitüberschreitung gekennzeichnet.

## <a name="excessive-wait-times"></a>Übermäßige Wartezeiten

Das Modell für übermäßige Wartezeiten überwacht einzelne Datenbankabfragen. Es erkennt ungewöhnlich hohe Wartezeiten für Abfragen, die die vom System verwalteten absoluten Schwellenwerte überschritten haben. Die folgenden Metriken für übermäßige Abfragewartezeiten werden mithilfe der [Wartestatistiken des Abfragedatenspeichers (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql) beobachtet:

- Erreichen von Ressourcengrenzwerten
- Erreichen von Ressourcenbegrenzungen von Pools für elastische Datenbanken
- Übermäßige Anzahl von Worker- oder Sitzungsthreads
- Übermäßiges Sperren der Datenbank
- Hohe Arbeitsspeicherauslastung
- Andere Wartestatistiken

Das Erreichen von Ressourcengrenzwerten von Abonnements oder Pools für elastische Datenbanken weist darauf hin, dass die Nutzung der verfügbaren Ressourcen eines Abonnements oder eines Pools für elastische Datenbanken absolute Schwellenwerte überschritten hat. Diese Statistiken zeigen eine Minderung der Workloadleistung an. Eine übermäßige Anzahl von Worker- oder Sitzungsthreads weist darauf hin, dass einige initiierte Workerthreads oder Sitzungen absolute Schwellenwerte überschritten haben. Diese Statistiken zeigen eine Minderung der Workloadleistung an.

Ein übermäßiges Sperren der Datenbank weist darauf hin, dass die Anzahl von Sperren für eine Datenbank absolute Schwellenwerte überschritten hat. Diese Statistik zeigt eine Minderung der Workloadleistung an. Bei einer hohen Arbeitsspeicherauslastung hat die Anzahl der Threads, die eine Speicherzuweisung angefordert haben, einen absoluten Schwellenwert überschritten. Diese Statistik zeigt eine Minderung der Workloadleistung an.

Andere Wartestatistiken geben an, dass verschiedene Metriken, die über die Wartestatistiken des Abfragespeichers gemessen wurden, einen absoluten Schwellenwert überschritten haben. Diese Statistiken zeigen eine Minderung der Workloadleistung an.

Nachdem übermäßige Wartezeiten erkannt wurden, gibt das Diagnoseprotokoll von Intelligent Insights abhängig von den verfügbaren Daten die Hashes von beeinträchtigenden und beeinträchtigten Abfragen aus, deren Leistung gemindert ist, sowie Details zu den Metriken, die die Wartezeiten der Abfragen in der Ausführung verursachen, sowie die gemessenen Wartezeiten.

## <a name="errored-requests"></a>Fehlerhafte Anforderungen

Das Modell für Leistungsminderung durch fehlerhafte Anforderungen überwacht einzelne Abfragen und erkennt eine Erhöhung der Anzahl von Abfragen, bei denen im Vergleich zum Zeitraum der Baseline Fehler aufgetreten sind. Das Modell überwacht auch kritische Ausnahmen, die von der integrierten Logik verwalteten absoluten Schwellenwerte überschritten haben. Das System berücksichtigt automatisch die Anzahl der Abfrageanforderungen für Workloadänderungen im überwachten Zeitraum, die an die Datenbank und die Konten gesendet wurden.

Wenn die gemessene Erhöhung der Anforderungen mit Fehlern bezogen auf Gesamtzahl der gesendeten Anforderungen als relevant für die Workloadleistung angesehen wird, werden die betroffenen Abfragen mit Problemen mit der Leistungsminderung durch fehlerhafte Anforderungen gekennzeichnet.

Das Intelligent Insights-Protokoll gibt die Anzahl der Anforderungen mit Fehlern zurück. Es gibt an, ob die Leistungsminderung zu einem Anstieg an Anforderungen mit Fehlern oder zum Überschreiten eines überwachten kritischen Schwellenwerts geführt hat, und weist die gemessene Zeit der Leistungsminderung auf.

Wenn eine der überwachten kritischen Ausnahmen die vom System verwalteten absoluten Schwellenwerte überschreitet, wird ein intelligenter Einblick mit Details zu den kritischen Ausnahmen generiert.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Überwachen von Datenbanken mithilfe von SQL-Analyse](../../azure-monitor/insights/azure-sql.md).
- Erfahren Sie mehr über das [Behandeln von Leistungsproblemen mithilfe von Intelligent Insights](intelligent-insights-troubleshoot-performance.md).