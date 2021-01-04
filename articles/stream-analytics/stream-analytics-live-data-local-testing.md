---
title: Testen mit Livedaten mit Azure Stream Analytics für Visual Studio
description: Informationen zum lokalen Testen Ihres Azure Stream Analytics-Auftrags mithilfe von Livestreamingdaten
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 0788d8b4f659364fcedc690185c9159a24434125
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123913"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Lokales Testen von Livedaten mithilfe von Azure Stream Analytics-Tools für Visual Studio (Vorschauversion)

Azure Stream Analytics-Tools für Visual Studio ermöglichen Ihnen das lokale Testen von Aufträgen in der IDE mithilfe von Live-Ereignisdatenströmen aus Azure Event Hub, IoT Hub und Blob Storage. Das lokale Testen von Livedaten kann die in der Cloud verfügbaren [Leistungs- und Skalierbarkeitstests](stream-analytics-streaming-unit-consumption.md) zwar nicht ersetzen, Sie können jedoch während der Funktionstests Zeit sparen, da nicht bei jedem Testen Ihres Stream Analytics-Auftrags eine Übermittlung in die Cloud erforderlich ist. Dieses Feature befindet sich in der Vorschauphase und darf nicht für Produktionsworkloads verwendet werden.

## <a name="testing-options"></a>Testoptionen

Die folgenden lokalen Testoptionen werden unterstützt:

|**Input** (Eingabe)  |**Ausgabe**  |**Auftragstyp**  |
|---------|---------|---------|
|Lokale statische Daten   |  Lokale statische Daten   |   Cloud/Edge |
|Live-Eingabedaten   |  Lokale statische Daten   |   Cloud |
|Live-Eingabedaten   |  Live-Ausgabedaten   |   Cloud |

## <a name="local-testing-with-live-data"></a>Lokales Testen mit Livedaten

1. Nachdem Sie ein [Azure Stream Analytics-Cloudprojekt in Visual Studio](stream-analytics-quick-create-vs.md) erstellt haben, öffnen Sie **script.asaql**. Die lokalen Tests verwendet standardmäßig lokale Ein- und Ausgaben.

   ![Azure Stream Analytics Visual Studio mit lokaler Ein- und Ausgabe](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Zum Testen von Livedaten wählen Sie im Dropdownfeld die Option **Use Cloud Input** (Cloudeingabe verwenden) aus.

   ![Azure Stream Analytics Visual Studio mit Live-Cloudeingabe](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Legen Sie die **Startzeit** fest, um zu definieren, wann der Auftrag mit der Verarbeitung von Eingabedaten beginnt. Der Auftrag muss Eingabedaten u.U. vorab lesen, um genaue Ergebnisse sicherzustellen. Die Standardzeit ist auf 30 Minuten in der Zukunft festgelegt.

   ![Azure Stream Analytics Visual Studio – Anfangszeit der Livedaten](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klicken Sie auf **Lokal ausführen**. Ein Konsolenfenster mit dem Ausführungsfortschritt und Auftragsmetriken wird angezeigt. Wenn Sie den Prozess beenden möchten, können Sie das manuell tun. 

   ![Azure Stream Analytics Visual Studio – Livedaten-Prozessfenster](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Die Ausgabeergebnisse werden im Ergebnisfenster der lokalen Ausführung alle drei Sekunden mit den ersten 500 Ausgabezeilen aktualisiert, und die Ausgabedateien werden unter Ihrem Projektpfad im Ordner **ASALocalRun** abgelegt. Sie können die Ausgabedateien auch öffnen, indem Sie im Ergebnisfenster der lokalen Ausführung auf die Schaltfläche **Ergebnisordner öffnen** klicken.

   ![Azure Stream Analytics Visual Studio mit geöffnetem Ergebnisordner](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Wenn die Ergebnisse in Ihren Cloudausgabesenken ausgegeben werden sollen, wählen Sie im zweiten Dropdownfeld die Option **Output to Cloud** (Ausgabe in der Cloud). Power BI und Azure Data Lake Storage sind keine unterstützten Ausgabesenken.

   ![Azure Stream Analytics Visual Studio – Ausgabe von Livedaten in die Cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Einschränkungen

* Power BI und Azure Data Lake Storage werden aufgrund von Einschränkungen des Authentifizierungsmodells nicht als Ausgabesenken unterstützt.

* Nur Cloudeingabeoptionen unterstützen [Zeitrichtlinien](./stream-analytics-time-handling.md), lokale Eingabeoptionen nicht.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Stream Analytics-Auftrags mithilfe des Azure Stream Analytics-Tools für Visual Studio](stream-analytics-quick-create-vs.md)
* [Lokales Testen von Stream Analytics-Abfragen mit Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Anzeigen von Azure Stream Analytics-Aufträgen mit Visual Studio](stream-analytics-vs-tools.md)