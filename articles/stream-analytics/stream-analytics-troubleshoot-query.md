---
title: Problembehandlung von Azure Stream Analytics-Abfragen
description: In diesem Artikel werden Verfahren zur Problembehandlung Ihrer Abfragen in Azure Stream Analytics-Aufträgen beschrieben.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: ef03560cff704255d2779a747d124e0b39a1c657
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491306"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Problembehandlung von Azure Stream Analytics-Abfragen

In diesem Artikel werden häufige Probleme bei der Entwicklung von Stream Analytics-Abfragen und deren Behebung beschrieben.

Dieser Artikel beschreibt häufige Probleme bei der Entwicklung von Azure Stream Analytics-Abfragen, die Behandlung von Abfrageproblemen und die Behebung der Probleme. Viele Troubleshootingschritte erfordern die Aktivierung von Ressourcenprotokollen für Ihren Stream Analytics-Auftrag. Wenn Sie Ressourcenprotokolle nicht aktiviert haben, finden Sie weitere Informationen unter [Problembehandlung von Azure Stream Analytics mit Ressourcenprotokollen](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>Die Abfrage erzeugt nicht die erwartete Ausgabe.

1.  Untersuchen Sie die Fehler durch lokales Testen:

    - Wählen Sie im Azure-Portal auf der Registerkarte **Abfrage** die Option **Test** aus. Verwenden Sie die heruntergeladenen Beispieldaten, um die [Abfrage zu testen](stream-analytics-test-query.md). Untersuchen Sie alle Fehler, und versuchen Sie, diese zu korrigieren.   
    - Sie können Ihre [Abfrage auch lokal testen](stream-analytics-live-data-local-testing.md), indem Sie die Azure Stream Analytics-Tools für Visual Studio oder [Visual Studio Code](visual-studio-code-local-run-live-input.md) verwenden. 

2.  [Debuggen Sie Abfragen nach und nach lokal anhand des Auftragsdiagramms](debug-locally-using-job-diagram-vs-code.md) in Azure Stream Analytics-Tools für Visual Studio Code. Das Auftragsdiagramm zeigt, wie Daten aus Eingabequellen (Event Hub, IOT Hub usw.) über mehrere Abfrageschritte und schließlich zu Ausgabesenken fließen. Jeder Abfrageschritt wird einem temporären Resultset zugeordnet, das im Skript mithilfe der WITH-Anweisung definiert ist. Sie können die Daten und Metriken in jedem Zwischenresultset anzeigen, um die Ursache des Problems zu ermitteln.

    ![Vorschau des Auftragsdiagrammergebnisses](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Stellen Sie bei Verwendung von [**Zeitstempel nach**](/stream-analytics-query/timestamp-by-azure-stream-analytics) sicher, dass die Zeitstempel der Ereignisse aktueller sind als die [Startzeit des Auftrags](./stream-analytics-time-handling.md).

4.  Beseitigen Sie häufige Fallen wie Folgende:
    - Eine [**WHERE**](/stream-analytics-query/where-azure-stream-analytics)-Klausel in der Abfrage hat alle Ereignisse herausgefiltert, die das Generieren von Ausgaben verhindert.
    - Der Auftrag ist aufgrund einer fehlerhaften [**CAST**](/stream-analytics-query/cast-azure-stream-analytics)-Funktion nicht erfolgreich. Typumwandlungsfehler lassen sich durch die Verwendung von [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics) vermeiden.
    - Warten Sie bei der Verwendung von Fensterfunktionen die gesamte Fensterdauer ab, um die Ausgabe der Abfrage zu sehen.
    - Der Zeitstempel für Ereignisse steht vor der Startzeit des Auftrags und Ereignisse werden abgelegt.
    - [**JOIN**](/stream-analytics-query/join-azure-stream-analytics)-Bedingungen stimmen nicht überein. Wenn keine Übereinstimmungen vorhanden sind, gibt es keine Ausgabe.

5.  Stellen Sie sicher, dass die Richtlinien für die Ereignisreihenfolge wie erwartet konfiguriert sind. Wechseln Sie zu **Einstellungen**, und wählen Sie [**Ereignisreihenfolge**](./stream-analytics-time-handling.md) aus. Die Richtlinie wird *nicht* angewendet, wenn Sie die Abfrage mithilfe der Schaltfläche **Testen** testen. Dieses Ergebnis ist ein Unterschied zwischen dem Testen im Browser und der tatsächlichen Ausführung des Auftrags. 

6. Debuggen mithilfe von Aktivitäts- und Ressourcenprotokollen:
    - Verwenden Sie [Aktivitätsprotokolle](../azure-resource-manager/management/view-activity-logs.md), und filtern Sie diese, um Fehler zu ermitteln und zu debuggen.
    - Verwenden Sie [Auftragsressourcenprotokolle](stream-analytics-job-diagnostic-logs.md), um Fehler zu ermitteln und zu debuggen.

## <a name="resource-utilization-is-high"></a>Die Ressourcenverwendung ist hoch

Stellen Sie sicher, dass Sie die Vorteile der Parallelisierung in Azure Stream Analytics nutzen. Erfahren Sie, wie Sie die [Skalierung mit Abfrageparallelisierung](stream-analytics-parallelization.md) von Stream Analytics-Aufträgen betreiben, indem Sie Eingabepartitionen konfigurieren und die Definition der Analyseabfrage anpassen.

Wenn die Ressourcenauslastung konstant mehr als 80 % beträgt, die Verzögerung des Wasserzeichens steigt und die Anzahl der protokollierten Ereignisse zunimmt, erhöhen Sie ggf. die Streamingeinheiten. Eine hohe Auslastung deutet darauf hin, dass der Auftrag fast die maximale Anzahl der bereitgestellten Ressourcen verwendet.

## <a name="debug-queries-progressively"></a>Progressives Debuggen von Abfragen

Bei der Datenverarbeitung in Echtzeit kann es hilfreich sein zu wissen, wie Daten in der Mitte der Abfrage aussehen. Sie können dies anhand des Auftragsdiagramms in Visual Studio erkennen. Wenn Sie nicht über Visual Studio verfügen, können Sie zusätzliche Schritte zur Ausgabe von Zwischendaten durchführen.

Da Eingaben oder Schritte in einen Azure Stream Analytics-Auftrag mehrmals gelesen werden können, lassen sich zusätzliche SELECT INTO-Anweisungen schreiben. Dabei werden Zwischendaten in den Speicher ausgegeben, die Sie auf Richtigkeit untersuchen können, was beim Debuggen eines Programms mithilfe von *Überwachungsvariablen* erfolgt.

Die folgende Beispielabfrage in einem Azure Stream Analytics-Auftrag weist eine Datenstromeingabe, zwei Verweisdateneingaben und eine Ausgabe in Azure Table Storage auf. Die Abfrage verknüpft Daten aus dem Event Hub und aus zwei Verweisblobs, um den Namen und Informationen zur Kategorie abzurufen:

![SELECT INTO-Beispielabfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Beachten Sie, dass der Auftrag ausgeführt wird, aber keine Ereignisse in der Ausgabe erzeugt werden. Auf der hier gezeigten Kachel **Überwachung** sehen Sie, dass die Eingabe Daten erzeugt, aber Sie wissen nicht, welcher Schritt des **JOIN**-Vorgangs das Löschen aller Ereignisse verursacht hat.

![Die Kachel „Stream Analytics-Überwachung“](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

In diesem Fall können Sie einige zusätzliche SELECT INTO-Anweisungen zum Protokollieren der JOIN-Zwischenergebnisse und der Daten hinzufügen, die in der Eingabe gelesen werden.

In diesem Beispiel haben wir zwei neue „temporäre Ausgaben“ hinzugefügt. Dabei kann es sich um eine beliebige Senke handeln. Hier wird Azure Storage als Beispiel verwendet:

![Hinzufügen von zusätzlichen SELECT INTO-Anweisungen zu einer Stream Analytics-Abfrage](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Sie können dann die Abfrage wie folgt umschreiben:

![Neu geschriebene SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Starten Sie nun den Auftrag erneut, und führen Sie ihn ein paar Minuten aus. Fragen Sie dann „temp1“ und „temp2“ mit dem Visual Studio-Cloud-Explorer ab, um die folgenden Tabellen zu generieren:

**Tabelle „temp1“** 
![Tabelle „temp1“ der SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabelle „temp2“** 
![Tabelle „temp2“ der SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Wie Sie sehen können, enthalten temp1 und temp2 Daten, und die Namensspalte wird in temp2 ordnungsgemäß aufgefüllt. Da jedoch in der Ausgabe weitere keine Daten vorhanden sind, ist etwas falsch:

![Tabelle „output1“ ohne Daten nach SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Nachdem Datenstichproben erstellt wurden, können Sie fast sicher sein, dass der zweite JOIN-Vorgang das Problem ist. Sie können die Verweisdaten aus dem Blob heruntergeladen und untersuchen:

![Referenztabelle für SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Wie Sie sehen können, unterscheidet sich das Format der GUID in diesen Verweisdaten vom Format der Spalte [from] in temp2. Deshalb sind die Daten nicht wie erwartet in output1 gelandet.

Sie können das Format der Daten korrigieren, sie in das Verweisblob hochladen und es erneut versuchen:

![Temporäre Tabelle für SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Dieses Mal werden die Daten in der Ausgabe wie erwartet formatiert und aufgefüllt.

![Endgültige Tabelle für SELECT INTO-Abfrage in Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)
