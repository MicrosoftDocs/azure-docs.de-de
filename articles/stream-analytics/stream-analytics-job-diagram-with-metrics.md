---
title: Datengesteuertes Debuggen in Azure Stream Analytics
description: Dieser Artikel beschreibt die Behandlung von Problemen in Azure Stream Analytics-Aufträgen mithilfe von Auftragsdiagrammen und Metriken im Azure-Portal.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: e4ee868125ee30d40ef0d9ca5a42881ad003e9a8
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123981"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Datengesteuertes Debuggen mithilfe des Auftragsdiagramms

Mithilfe des Auftragsdiagramms auf dem Blatt **Überwachung** im Azure-Portal können Sie Ihre Auftrags-Pipeline visualisieren. Sie zeigt Ein- und Ausgaben sowie Abfrageschritte an. Mithilfe des Auftragsdiagramms können Sie die Metriken für jeden Schritt untersuchen, um die Ursache eines Problems bei der Problembehandlung schneller zu isolieren.

## <a name="using-the-job-diagram"></a>Verwendung des Auftragsdiagramms

Wählen Sie im Azure-Portal bei geöffnetem Stream Analytics-Auftrag unter **SUPPORT + PROBLEMBEHANDLUNG** die Option **Auftragsdiagramm** :

![Auftragsdiagramm mit Metriken – Speicherort](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Durch Auswählen der einzelnen Abfrageschritte wird ein entsprechender Abschnitt in einem Bereich zur Bearbeitung der Abfrage angezeigt. Eine Metrikdiagramm für den jeweiligen Schritt wird im unteren Bereich der Seite angezeigt.

![Auftragsdiagramm mit Metriken – Grundlegender Auftrag](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Um die Partitionen der Azure Event Hub-Eingabe anzuzeigen, wählen Sie **. . .** Ein Kontextmenü wird angezeigt. Zudem wird die Eingabefusion angezeigt.

![Auftragsdiagramm mit Metriken – Erweiterung der Partition](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Um das Metrikdiagramm für nur eine einzelne Partition anzuzeigen, wählen Sie den Partitionsknoten aus. Die Metriken werden im unteren Bereich der Seite angezeigt.

![Auftragsdiagramm mit Metriken – Weitere Metriken](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Um das Metrikdiagramm für eine Fusion anzuzeigen, wählen Sie den Fusionsknoten aus. Im folgenden Diagramm wird angezeigt, dass keine Ereignisse verworfen oder angepasst wurden.

![Auftragsdiagramm mit Metriken – Raster](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Um die Details und den Zeitpunkt des Metrikwerts anzuzeigen, zeigen Sie auf das Diagramm.

![Auftragsdiagramm mit Metriken – Darauf zeigen](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Problembehandlung mithilfe von Metriken

Die Metrik **QueryLastProcessedTime** gibt an, wann ein bestimmter Schritt Daten empfangen hat. Anhand der Topologie können Sie sich vom Ausgabeprozessor aus in umgekehrter Richtung bis zu dem Schritt vorarbeiten, in dem keine Daten empfangen werden. Wenn ein Schritt keine Daten erhält, navigieren Sie unmittelbar davor zum Abfrageschritt. Überprüfen Sie, ob der vorherige Abfrageschritt ein Zeitfenster aufweist und ob genügend Zeit für die Ausgabe von Daten verstrichen ist. (Beachten Sie, dass Zeitfenster auf Stunden ausgerichtet sind.)
 
Handelt es sich beim vorherigen Abfrageschritt um einen Eingabeprozessor, beantworten Sie mithilfe der Eingabemetriken die folgenden gezielten Fragen. Dadurch können Sie feststellen, ob ein Auftrag Daten von den jeweiligen Eingabequellen erhält. Wenn die Abfrage partitioniert ist, überprüfen Sie jede Partition.
 
### <a name="how-much-data-is-being-read"></a>Wie viele Daten werden gelesen?

*   **InputEventsSourcesTotal** gibt die Anzahl gelesener Dateneinheiten an. Beispiel: die Anzahl von Blobs.
*   **InputEventsTotal** gibt die Anzahl gelesener Ereignisse an. Diese Metrik steht pro Partition zur Verfügung.
*   **InputEventsInBytesTotal** gibt die Anzahl gelesener Bytes an.
*   **InputEventsLastArrivalTime** wird mit der Zeit aktualisiert, zu der die einzelnen empfangenen Ereignisse in die Warteschlange eingereiht wurden.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Erhöht sich der Zeitwert? Werden tatsächliche Ereignisse gelesen, werden möglicherweise keine Interpunktion ausgegeben.

*   **InputEventsLastPunctuationTime** gibt an, wann ein Satzzeichen ausgegeben wurde, damit sich der Zeitwert weiterhin erhöht. Wenn keine Interpunktion ausgegeben wird, kann der Datenfluss blockiert werden.
 
### <a name="are-there-any-errors-in-the-input"></a>Enthält die Eingabe Fehler?

*   **InputEventsEventDataNullTotal** gibt eine Anzahl von Ereignissen mit Null-Daten an.
*   **InputEventsSerializerErrorsTotal** gibt eine Anzahl von Ereignissen an, die nicht korrekt deserialisiert werden konnten.
*   **InputEventsDegradedTotal** enthält eine Anzahl von Ereignissen, bei denen andere Probleme als Deserialisierungsprobleme aufgetreten sind.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Werden Ereignisse verworfen oder angepasst?

*   **InputEventsEarlyTotal** gibt die Anzahl von Ereignissen mit einem Anwendungszeitstempel an, der unter dem hohen Grenzwert liegt.
*   **InputEventsLateTotal** gibt die Anzahl von Ereignissen mit einem Anwendungszeitstempel an, der über dem hohen Grenzwert liegt.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** gibt die Anzahl von Ereignissen an, die vor der Auftragsstartzeit verworfen wurden.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Gibt es eine Verzögerung beim Lesen der Daten?

*   **Eingabeereignisse im Rückstand (gesamt)** gibt die Anzahl von Nachrichten an, die für Event Hubs- und Azure IoT Hub-Eingaben noch gelesen werden müssen. Wenn diese Zahl größer als 0 ist, bedeutet dies, dass der Auftrag die Daten nicht so schnell verarbeiten kann, wie sie eintreffen. In diesem Fall müssen Sie die Anzahl der Streamingeinheiten erhöhen und/oder sicherstellen, dass Ihr Auftrag parallelisiert werden kann. Weitere Informationen hierzu finden Sie auf der Seite zur [Parallelisierung von Abfragen](./stream-analytics-parallelization.md). 


## <a name="get-help"></a>Hier erhalten Sie Hilfe
Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Referenz zur Stream Analytics-Abfragesprache](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur REST-API für die Stream Analytics-Verwaltung](/rest/api/streamanalytics/)