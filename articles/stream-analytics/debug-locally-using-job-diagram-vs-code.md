---
title: Lokales Debuggen von Azure Stream Analytics-Abfragen mithilfe eines Auftragsdiagramms in Visual Studio Code
description: Dieser Artikel beschreibt, wie Abfragen mithilfe eines Auftragsdiagramms in der Azure Stream Analytics-Erweiterung für Visual Studio Code lokal debuggt werden.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 766d3f8d4d4c03fb4fe173dbb12eb85bb09cd71a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124168"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Lokales Debuggen von Azure Stream Analytics-Abfragen mithilfe eines Auftragsdiagramms in Visual Studio Code

Streamingaufträge, die kein Ergebnis oder unerwartete Ergebnisse ausgeben, benötigen häufig eine Problembehandlung. Mit der Visual Studio Code-Erweiterung für Azure Stream Analytics werden Auftragsdiagramme, Metriken, Diagnoseprotokolle und Zwischenergebnisse integriert, damit Sie die Quelle eines Problems schnell isolieren können. Sie können das Auftragsdiagramm verwenden, während Sie Ihre Abfrage lokal testen, um das Zwischenresultset und die Zwischenmetriken für jeden Schritt zu überprüfen.

## <a name="debug-a-query-using-job-diagram"></a>Debuggen einer Abfrage mithilfe des Auftragsdiagramms

Ein Azure Stream Analytics-Skript wird verwendet, um Eingabedaten in Ausgabedaten zu transformieren. Das Auftragsdiagramm zeigt, wie Daten aus Eingabequellen (wie Event Hub oder IoT Hub) über mehrere Abfrageschritte zu Ausgabesenken fließen. Jeder Abfrageschritt wird einem temporären Resultset zugeordnet, das im Skript mithilfe einer `WITH`-Anweisung definiert ist. Sie können die Daten und Metriken der einzelnen Abfrage Schritte in jedem Zwischenresultset anzeigen, um die Ursache eines Problems zu ermitteln.

> [!NOTE]
> Dieses Auftragsdiagramm zeigt nur die Daten und Metriken für lokale Tests in einem einzelnen Knoten. Es sollte nicht für Leistungsoptimierung und Problembehandlung verwendet werden.

### <a name="start-local-testing"></a>Starten des lokalen Testens

Verwenden Sie diesen [Schnellstart](quick-create-visual-studio-code.md), um zu erfahren, wie Sie einen Stream Analytics-Auftrag mithilfe von Visual Studio Code erstellen oder [einen vorhandenen Auftrag in ein lokales Projekt exportieren](visual-studio-code-explore-jobs.md). Anmeldeinformationen für Eingaben und Ausgaben werden für exportierte Aufträge automatisch aufgefüllt.

Wenn Sie die Abfrage mit lokalen Eingabedaten testen möchten, befolgen Sie diese [Anweisungen](visual-studio-code-local-run.md). Wenn Sie mit Liveeingaben testen möchten, [konfigurieren Sie Ihre Eingabe](stream-analytics-add-inputs.md), und fahren Sie mit dem nächsten Schritt fort. 

Öffnen Sie die Skriptdatei *\.asaql* , und wählen Sie **Lokal ausführen** aus. Wählen Sie dann **Lokale Eingabe verwenden** oder **Liveeingabe verwenden** aus. Das Auftragsdiagramm wird auf der rechten Seite des Fensters angezeigt.

### <a name="view-the-output-and-intermediate-result-set"></a>Anzeigen der Ausgabe und des Zwischenresultsets  

1. Alle Auftragsausgaben werden im Ergebnisfenster in der unteren rechten Ecke des Visual Studio Code-Fensters angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Auftragsausgabeergebnisse](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Wählen Sie den Abfrageschritt aus, um zum Skript zu navigieren. Sie werden automatisch an das entsprechende Skript im Editor auf der linken Seite weitergeleitet. Die Zwischenergebnisse werden im Ergebnisfenster in der unteren rechten Ecke des Visual Studio Code-Fensters angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Vorschau des Auftragsdiagrammergebnisses](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Anzeigen von Metriken

In diesem Abschnitt untersuchen Sie die für die einzelnen Teile des Diagramms verfügbaren Metriken.

1. Wählen Sie die Registerkarte **Metriken** neben der Registerkarte **Ergebnis** in der unteren rechten Ecke des Visual Studio Code-Fensters aus.

2. Wählen Sie in der Dropdownliste **Auftrag** aus. Sie können einen beliebigen leeren Bereich in einem Diagrammknoten auswählen, um zu Metriken auf Auftragsebene zu navigieren. Diese Ansicht enthält alle Metriken, die alle 10 Sekunden aktualisiert werden, wenn der Auftrag ausgeführt wird. Sie können die Metriken auf der rechten Seite aktivieren oder deaktivieren, um Sie in den Diagrammen anzuzeigen.

   > [!div class="mx-imgBorder"]
   > ![Auftragsdiagrammmetriken](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Wählen Sie in der Dropdownliste den Namen der Eingabedatenquelle aus, um Eingabemetriken anzuzeigen. Die Eingabequelle im folgenden Screenshot heißt *quotes* (Zitate). Weitere Informationen zu Eingabemetriken finden Sie unter [Grundlegendes zur Stream Analytics-Auftragsüberwachung und zum Überwachen von Abfragen](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Eingabemetriken für Auftragsdiagramm](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Wählen Sie einen Abfrageschritt aus dem Auftragsdiagramm aus, oder wählen Sie in der Dropdownliste den Schrittnamen aus, um Metriken auf Schrittebene anzuzeigen. Wasserzeichenverzögerung ist die einzige verfügbare Schrittmetrik.

   > [!div class="mx-imgBorder"]
   > ![Schrittmetriken](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Wählen Sie eine Ausgabe im Diagramm oder in der Dropdownliste aus, um ausgabebezogene Metriken anzuzeigen. Weitere Informationen zu Ausgabemetriken finden Sie unter [Grundlegendes zur Stream Analytics-Auftragsüberwachung und zum Überwachen von Abfragen](stream-analytics-monitoring.md). Liveausgabesenken werden nicht unterstützt.

   > [!div class="mx-imgBorder"]
   > ![Ausgabemetriken](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Anzeigen von Diagnoseprotokollen

Diagnoseprotokolle auf Auftragsebene enthalten Diagnoseinformationen für Eingabedatenquellen und Ausgabesenken. Wenn Sie einen Eingabe- oder Ausgabeknoten auswählen, werden nur die entsprechenden Protokolle angezeigt. Wenn Sie einen Abfrageschritt auswählen, werden keine Protokolle angezeigt. Sie können alle Protokolle auf Auftragsebene finden und die Protokolle nach Schweregrad und Uhrzeit filtern.

   > [!div class="mx-imgBorder"]
   > ![Diagnoseprotokolle](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Wählen Sie einen Protokolleintrag aus, um die gesamte Meldung anzuzeigen.

   > [!div class="mx-imgBorder"]
   > ![Diagnoseprotokollmeldung](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Weitere Auftragsdiagrammfunktionen

Sie können bei Bedarf in der Symbolleiste  **Beenden** oder **Anhalten** auswählen. Nachdem der Auftrag angehalten wurde, können Sie ihn mit der letzten Ausgabe fortsetzen.

> [!div class="mx-imgBorder"]
> ![Auftrag beenden oder anhalten](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Wählen Sie in der oberen rechten Ecke des Auftragsdiagramms **Auftragszusammenfassung** aus, um Eigenschaften und Konfigurationen für Ihren lokalen Auftrag anzuzeigen.

> [!div class="mx-imgBorder"]
> ![Zusammenfassung für lokalen Auftrag](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Einschränkungen

* Liveausgabesenken werden für lokale Ausführungen nicht unterstützt.

* Die lokale Ausführung eines Auftrags mit der JavaScript-Funktion wird nur unter Windows-Betriebssystemen unterstützt.

* Benutzerdefinierter C#-Code und Azure Machine Learning-Funktionen werden nicht unterstützt. 

* [Zeitrichtlinien](./stream-analytics-time-handling.md) werden nur für Cloudeingabeoptionen unterstützt, nicht für lokale Eingabeoptionen.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Visual Studio Code](quick-create-visual-studio-code.md)
* [Erkunden von Azure Stream Analytics mit Visual Studio Code](visual-studio-code-explore-jobs.md)
* [Lokales Testen von Stream Analytics-Abfragen mit Beispieldaten mithilfe von Visual Studio Code](visual-studio-code-local-run.md)
* [Lokales Testen von Azure Stream Analytics-Aufträgen mit Liveeingabe unter Verwendung von Visual Studio Code](visual-studio-code-local-run-live-input.md)