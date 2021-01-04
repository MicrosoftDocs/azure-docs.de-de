---
title: Lokales Debuggen von Azure Stream Analytics-Abfragen mithilfe eines Auftragsdiagramms in Visual Studio
description: Dieser Artikel beschreibt, wie Abfragen mithilfe eines Auftragsdiagramms in Azure Stream Analytics-Tools für Visual Studio lokal debuggt werden.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: c32ba468979e4566d8e349d481eddca7d407b659
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130985"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Lokales Debuggen von Azure Stream Analytics-Abfragen mithilfe eines Auftragsdiagramms in Visual Studio

Aufträge, die kein Ergebnis oder unerwartete Ergebnisse ausgeben, stellen häufige Problembehandlungsszenarien bei Streamingabfragen dar. Sie können das Auftragsdiagramm verwenden, während Sie Ihre Abfrage lokal in Visual Studio testen, um das Zwischenresultset und die Zwischenmetriken für jeden Schritt zu überprüfen. Auftragsdiagramme können helfen, bei der Problembehandlung die Quelle eines Problems schnell zu isolieren.

## <a name="debug-a-query-using-job-diagram"></a>Debuggen einer Abfrage mithilfe des Auftragsdiagramms

Ein Azure Stream Analytics-Skript wird verwendet, um Eingabedaten in Ausgabedaten zu transformieren. Das Auftragsdiagramm zeigt, wie Daten aus Eingabequellen (Event Hub, IOT Hub usw.) über mehrere Abfrageschritte und schließlich zu Ausgabesenken fließen. Jeder Abfrageschritt wird einem temporären Resultset zugeordnet, das im Skript mithilfe einer `WITH`-Anweisung definiert ist. Sie können die Daten und Metriken der einzelnen Abfrage Schritte in jedem Zwischenresultset anzeigen, um die Ursache eines Problems zu ermitteln.

> [!NOTE]
> Dieses Auftragsdiagramm zeigt nur die Daten und Metriken für lokale Tests in einem einzelnen Knoten. Es sollte nicht für Leistungsoptimierung und Problembehandlung verwendet werden.

### <a name="start-local-testing"></a>Starten des lokalen Testens

Verwenden Sie diesen [Schnellstart](stream-analytics-quick-create-vs.md), um zu erfahren, wie Sie einen Stream Analytics-Auftrag mithilfe von Visual Studio erstellen oder [einen vorhandenen Auftrag in ein lokales Projekt exportieren](stream-analytics-vs-tools.md#export-jobs-to-a-project). Wenn Sie die Abfrage mit lokalen Eingabedaten testen möchten, befolgen Sie diese [Anweisungen](stream-analytics-live-data-local-testing.md). Wenn Sie mit Liveeingaben testen möchten, fahren Sie mit dem nächsten Schritt fort.

> [!NOTE]
> Wenn Sie einen Auftrag in ein lokales Projekt exportieren und mit einem Liveeingabestream testen möchten, müssen Sie die Anmeldeinformationen für alle Eingaben erneut angeben.  

Wählen Sie im Skript-Editor die Eingabe- und Ausgabequelle aus, und wählen Sie **Lokal ausführen** aus. Das Auftragsdiagramm wird auf der rechten Seite angezeigt.

### <a name="view-the-intermediate-result-set"></a>Anzeigen des Zwischenresultsets  

1. Wählen Sie den Abfrageschritt aus, um zum Skript zu navigieren. Sie werden automatisch an das entsprechende Skript im Editor auf der linken Seite weitergeleitet.

   ![Skript für Auftragsdiagrammnavigation](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Wählen Sie den Abfrageschritt aus, und wählen Sie Popup-Dialogfeld **Vorschau** aus. Das Resultset wird auf einer Registerkarte im unteren Ergebnisfenster angezeigt.

   ![Vorschau des Auftragsdiagrammergebnisses](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Anzeigen von Schrittmetriken

In diesem Abschnitt untersuchen Sie die für die einzelnen Teile des Diagramms verfügbaren Metriken.

#### <a name="input-sources-live-stream"></a>Eingabequellen (Livestream)

![Liveeingabequellen für das Auftragsdiagramm](./media/debug-locally-using-job-diagram/live-input.png)

|Metrik|BESCHREIBUNG|
|-|-|
|**TaxiRide**| Der Name der Eingabe.|
|**Event Hub** | Eingabequellentyp.|
|**Ereignisse**|Die Anzahl der gelesenen Ereignisse.|
|**Ereignisquellen im Rückstand (Backlog)**|Die Anzahl von Nachrichten, die für Event Hubs- und Azure IoT Hub-Eingaben noch gelesen werden müssen.|
|**Ereignisse in Bytes**|Die Anzahl der gelesenen Bytes.|
| **Ereignisse mit Beeinträchtigung**|Die Anzahl von Ereignissen, bei denen andere Probleme als Deserialisierungsprobleme aufgetreten sind.|
|**Frühe Ereignisse**| Die Anzahl von Ereignissen mit einem Anwendungszeitstempel, der vor dem hohen Grenzwert liegt.|
|**Verspätete Ereignisse**| Die Anzahl von Ereignissen mit einem Anwendungszeitstempel, der nach dem hohen Grenzwert liegt.|
|**Ereignisquellen**| Die Anzahl gelesener Dateneinheiten. Beispiel: die Anzahl von Blobs.|

#### <a name="input-sources-local-input"></a>Eingabequellen (lokale Eingabe)

![Lokale Eingabequellen des Auftragsdiagramms](./media/debug-locally-using-job-diagram/local-input.png)

|Metrik|BESCHREIBUNG|
|-|-|
|**TaxiRide**| Der Name der Eingabe.|
|**Zeilenanzahl**| Die Anzahl der aus diesem Schritt generierten Zeilen.|
|**Datengröße**| Die Größe der aus diesem Schritt generierten Daten.|
|**Lokale Eingabe**| Lokale Daten als Eingabe verwenden.|

#### <a name="query-steps"></a>Abfrageschritte

![Abfrageschritt im Auftragsdiagramm](./media/debug-locally-using-job-diagram/query-step.png)

|Metrik|BESCHREIBUNG|
|-|-|
|**TripData**|Der Name des temporären Resultsets.|
|**Zeilenanzahl**| Die Anzahl der aus diesem Schritt generierten Zeilen.|
|**Datengröße**| Die Größe der aus diesem Schritt generierten Daten.|
  
#### <a name="output-sinks-live-output"></a>Ausgabesenken (Liveausgabe)

![Auftragsdiagramm, das die lokalen Ausgabesenken zeigt.](./media/debug-locally-using-job-diagram/live-output.png)

|Metrik|BESCHREIBUNG|
|-|-|
|**regionaggEH**|Der Name der Ausgabe.|
|**Ereignisse**|Die Anzahl der an Senken ausgegebenen Ereignisse.|

#### <a name="output-sinks-local-output"></a>Ausgabesenken (lokale Ausgabe)

![Lokale Ausgabesenken des Auftragsdiagramms](./media/debug-locally-using-job-diagram/local-output.png)

|Metrik|BESCHREIBUNG|
|-|-|
|**regionaggEH**|Der Name der Ausgabe.|
|**Lokale Ausgabe**| Ergebnisausgabe in eine lokale Datei.|
|**Zeilenanzahl**| Die Anzahl der in die lokale Datei ausgegebenen Zeilen.|
|**Datengröße**| Die Größe der in die lokale Datei ausgegebenen Daten.|

### <a name="close-job-diagram"></a>Schließen des Auftragsdiagramms

Wenn Sie das Auftragsdiagramm nicht mehr benötigen, wählen Sie in der oberen rechten Ecke **Schließen** aus. Nachdem Sie das Diagrammfenster geschlossen haben, müssen Sie die lokalen Tests erneut starten, um es anzuzeigen.

### <a name="view-job-level-metrics-and-stop-running"></a>Metriken auf Auftragsebene anzeigen und die Ausführung abbrechen

In der Popupkonsole werden weiter Metriken auf Auftragsebene angezeigt. Drücken Sie in der Konsole **STRG+C** , wenn Sie den Auftrag beenden möchten.

![Beenden des Auftrags im Auftragsdiagramm](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Einschränkungen

* Power BI- und Azure Data Lake Storage Gen1-Ausgabesenken werden aufgrund von Einschränkungen des Authentifizierungsmodells nicht unterstützt.

* Nur Cloudeingabeoptionen unterstützen [Zeitrichtlinien](./stream-analytics-time-handling.md), lokale Eingabeoptionen nicht.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Anzeigen von Azure Stream Analytics-Aufträgen mit Visual Studio](stream-analytics-vs-tools.md)
* [Lokales Testen von Livedaten mithilfe von Azure Stream Analytics-Tools für Visual Studio (Vorschauversion)](stream-analytics-live-data-local-testing.md)