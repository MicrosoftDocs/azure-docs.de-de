---
title: Problembehandlung von Azure Stream Analytics-Abfragen
description: In diesem Artikel werden Verfahren zur Problembehandlung Ihrer Abfragen in Azure Stream Analytics-Aufträgen beschrieben.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7a1e440a8dc8f518e272df9e126771df54390ed5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60762387"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Problembehandlung von Azure Stream Analytics-Abfragen

In diesem Artikel werden häufige Probleme bei der Entwicklung von Stream Analytics-Abfragen und deren Behebung beschrieben.

## <a name="query-is-not-producing-expected-output"></a>Die Abfrage erzeugt nicht die erwartete Ausgabe. 
1.  Untersuchen Sie die Fehler durch lokales Testen:
    - Wählen Sie auf der Registerkarte **Abfrage** die Option **Test** aus. Verwenden Sie die heruntergeladenen Beispieldaten, um die [Abfrage zu testen](stream-analytics-test-query.md). Untersuchen Sie alle Fehler, und versuchen Sie, diese zu korrigieren.   
    - Sie können Ihre Abfrage auch [direkt anhand der Live-Eingabe](stream-analytics-live-data-local-testing.md) testen, indem Sie die Stream Analytics-Tools für Visual Studio verwenden.

2.  Stellen Sie bei Verwendung von [**Zeitstempel nach**](https://msdn.microsoft.com/library/azure/mt573293.aspx) sicher, dass die Zeitstempel der Ereignisse aktueller sind als die [Startzeit des Auftrags](stream-analytics-out-of-order-and-late-events.md).

3.  Beseitigen Sie häufige Fallen wie Folgende:
    - Eine [**WHERE**](https://msdn.microsoft.com/library/azure/dn835048.aspx)-Klausel in der Abfrage hat alle Ereignisse herausgefiltert, die das Generieren von Ausgaben verhindert.
    - Der Auftrag ist aufgrund einer fehlerhaften [**CAST**](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics)-Funktion nicht erfolgreich. Typumwandlungsfehler lassen sich durch die Verwendung von [**TRY_CAST**](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) vermeiden.
    - Warten Sie bei der Verwendung von Fensterfunktionen die gesamte Fensterdauer ab, um die Ausgabe der Abfrage zu sehen.
    - Der Zeitstempel für Ereignisse liegt vor der Startzeit des Auftrags, und daher werden Ereignisse verworfen.

4.  Stellen Sie sicher, dass die Richtlinien für die Ereignisreihenfolge wie erwartet konfiguriert sind. Wechseln Sie zu **Einstellungen**, und wählen Sie [**Ereignisreihenfolge**](stream-analytics-out-of-order-and-late-events.md) aus. Die Richtlinie wird *nicht* angewendet, wenn Sie die Abfrage mithilfe der Schaltfläche **Testen** testen. Dieses Ergebnis ist ein Unterschied zwischen dem Testen im Browser und der tatsächlichen Ausführung des Auftrags. 

5. Führen Sie ein Debuggen mithilfe von Überwachungs- und Diagnoseprotokollen durch:
    - Verwenden Sie [Überwachungsprotokolle](../azure-resource-manager/resource-group-audit.md), und filtern Sie diese, um Fehler festzustellen und zu debuggen.
    - Verwenden Sie [Auftragsdiagnoseprotokolle](stream-analytics-job-diagnostic-logs.md), um Fehler festzustellen und zu debuggen.

## <a name="job-is-consuming-too-many-streaming-units"></a>Der Auftrag verbraucht zu viele Streamingeinheiten.
Stellen Sie sicher, dass Sie die Vorteile der Parallelisierung in Azure Stream Analytics nutzen. Erfahren Sie, wie Sie die [Skalierung mit Abfrageparallelisierung](stream-analytics-parallelization.md) von Stream Analytics-Aufträgen betreiben, indem Sie Eingabepartitionen konfigurieren und die Definition der Analyseabfrage anpassen.

## <a name="debug-queries-progressively"></a>Progressives Debuggen von Abfragen

Bei der Datenverarbeitung in Echtzeit kann es hilfreich sein zu wissen, wie Daten in der Mitte der Abfrage aussehen. Da Eingaben oder Schritte in einen Azure Stream Analytics-Auftrag mehrmals gelesen werden können, lassen sich zusätzliche SELECT INTO-Anweisungen schreiben. Dabei werden Zwischendaten in den Speicher ausgegeben, die Sie auf Richtigkeit untersuchen können, was beim Debuggen eines Programms mithilfe von *Überwachungsvariablen* erfolgt.

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

Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)