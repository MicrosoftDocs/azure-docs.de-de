---
title: Testen eines Azure Stream Analytics-Auftrags mit Beispieldaten
description: In diesem Artikel wird beschrieben, wie Sie mithilfe des Azure-Portals einen Azure Stream Analytics-Auftrag mit Beispieleingabedaten testen und Beispieldaten hochladen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: 8e08c4c34495b58c105560dba9d818be9ebf5e34
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94490966"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testen eines Azure Stream Analytics-Auftrags im Portal

In Azure Stream Analytics können Sie Ihre Abfrage testen, ohne den Auftrag zu starten oder zu beenden. Sie können Abfragen mit eingehenden Daten von Ihren Streamingquellen testen oder Beispieldaten aus einer lokalen Datei im Azure-Portal hochladen. Sie können Abfragen auch lokal mit Ihren lokalen Beispieldaten oder Livedaten in [Visual Studio](stream-analytics-live-data-local-testing.md) und [Visual Studio Code](visual-studio-code-local-run-live-input.md) testen.

## <a name="automatically-sample-incoming-data-from-input"></a>Automatisches Nehmen von Stichproblem von Beispieleingangsdaten aus der Eingabe

Azure Stream Analytics ruft automatisch Ereignisse aus Ihrer Streamingeingabe ab. Sie können Abfragen mit dem Standardbeispiel ausführen oder einen bestimmten Zeitraum für das Beispiel festlegen.

1. Melden Sie sich beim Azure-Portal an.

2. Suchen Sie Ihren vorhandenen Stream Analytics-Auftrag, und wählen Sie ihn aus.

3. Wählen Sie auf der Stream Analytics-Auftragsseite unter der Überschrift **Auftragstopologie** die Option **Abfrage** aus, um das Fenster des Abfrage-Editors zu öffnen. 

4. Um eine Beispielliste eingehender Ereignisse anzuzeigen, wählen Sie das Symbol „Eingabe mit Datei“ aus, und die Beispielereignisse werden automatisch in der **Eingabevorschau** angezeigt.

   a. Der Serialisierungstyp für Ihre Daten wird automatisch erkannt, wenn es sich um JSON oder CSV handelt. Sie können ihn manuell in JSON, CSV oder AVRO ändern, indem Sie die Option im Dropdownmenü ändern.
    
   b. Verwenden Sie die Auswahl, um Ihre Daten im **Tabellen**- oder **Roh** format anzuzeigen.
    
   c. Wenn die angezeigten Daten nicht aktuell sind, wählen Sie **Aktualisieren** aus, um die neuesten Ereignisse anzuzeigen.

   Die folgende Tabelle ist ein Beispiel für Daten im **Tabellenformat**:

   ![Azure Stream Analytics: Beispieleingabe im Tabellenformat](./media/stream-analytics-test-query/asa-sample-table.png)

   Die folgende Tabelle ist ein Beispiel für Daten im **Rohformat**:

   ![Azure Stream Analytics: Beispieleingabe im Rohformat](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Um Ihre Abfrage mit eingehenden Daten zu testen, wählen Sie **Abfrage testen** aus. Ergebnisse werden auf der Registerkarte **Testergebnisse** angezeigt. Sie können auch **Ergebnisse herunterladen** auswählen, um die Ergebnisse herunterzuladen.

   ![Azure Stream Analytics: Ergebnisse einer Beispieltestabfrage](./media/stream-analytics-test-query/asa-test-query.png)

6. Um Ihre Abfrage mit einem bestimmten Zeitraum eingehender Ereignisse zu testen, wählen Sie **Zeitbereich auswählen** aus.
   
   ![Azure Stream Analytics: Zeitbereich für eingehende Beispielereignisse](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Legen Sie den Zeitbereich für die Ereignisse, die Sie zum Testen der Abfrage verwenden möchten, fest, und wählen Sie **Beispiel** aus. Innerhalb dieses Zeitraums können Sie bis zu 1000 Ereignisse oder 1 MB abrufen, je nachdem, was zuerst eintritt.

   ![Festgelegter Azure Stream Analytics-Zeitbereich für eingehende Beispielereignisse](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Sobald die Ereignisse für den ausgewählten Zeitraum erfasst wurden, werden Sie auf der Registerkarte **Eingabevorschau** angezeigt.

   ![Azure Stream Analytics: Anzeigen von Testergebnissen](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Wählen Sie **Zurücksetzen** aus, um die Beispielliste eingehender Ereignisse anzuzeigen. Wenn Sie **Zurücksetzen** auswählen, geht Ihre Zeitbereichsauswahl verloren. Wählen Sie **Abfrage testen** aus, um Ihre Abfrage zu testen, und überprüfen Sie die Ergebnisse auf der Registerkarte **Testergebnisse**.

10. Wenn Sie Änderungen an Ihrer Abfrage vornehmen, wählen Sie **Abfrage speichern** aus, um die neue Abfragelogik zu testen. Dies gestattet Ihnen, Ihre Abfrage iterativ zu ändern und sie erneut zu testen, um festzustellen, wie sich die Ausgabe ändert.

11. Nachdem Sie die im Browser angezeigten Ergebnisse überprüft haben, sind Sie bereit, den Auftrag zu **Starten**.

## <a name="upload-sample-data-from-a-local-file"></a>Hochladen von Beispieldaten aus einer lokalen Datei

Anstatt Livedaten zu verwenden, können Sie Beispieldaten aus einer lokalen Datei verwenden, um Ihre Azure Stream Analytics-Abfrage zu testen.

1. Melden Sie sich beim Azure-Portal an.
   
2. Suchen Sie Ihren vorhandenen Stream Analytics-Auftrag, und wählen Sie ihn aus.

3. Wählen Sie auf der Stream Analytics-Auftragsseite unter der Überschrift **Auftragstopologie** die Option **Abfrage** aus, um das Fenster des Abfrage-Editors zu öffnen.

4. Um Ihre Abfrage mit einer lokalen Datei zu testen, wählen Sie auf der Registerkarte **Eingabevorschau** die Option **Beispieleingabe hochladen** aus. 

   ![Screenshot: Option „Beispieleingabe hochladen“](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Laden Sie Ihre lokale Datei hoch, um die Abfrage zu testen. Sie können nur Dateien mit den Formaten JSON, CSV oder AVRO hochladen. Klicken Sie auf **OK**.

   ![Screenshot: Dialogfeld „Beispieldaten hochladen“, in dem Sie eine Datei auswählen können](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Sobald Sie die Datei hochladen, können Sie auch den Inhalt der Datei im Formular als Tabelle oder im Rohformat sehen. Wenn Sie **Zurücksetzen** auswählen, kehren die Beispieldaten zu den eingehenden Eingabedaten zurück, die im vorherigen Abschnitt erläutert wurden. Sie können jede beliebige andere Datei hochladen, um die Abfrage jederzeit zu testen.

7. Wählen Sie **Abfrage testen** aus, um Ihre Abfrage anhand der hochgeladenen Beispieldatei zu testen.

8. Testergebnisse werden auf Grundlage Ihrer Abfrage angezeigt. Sie können Ihre Abfrage ändern und **Abfrage speichern** auswählen, um die neue Abfragelogik zu testen. Dies gestattet Ihnen, Ihre Abfrage iterativ zu ändern und sie erneut zu testen, um festzustellen, wie sich die Ausgabe ändert.

9. Wenn Sie mehrere Ausgaben in der Abfrage verwenden, werden die Ergebnisse basierend auf der ausgewählten Ausgabe angezeigt. 

   ![Azure Stream Analytics: ausgewählte Ausgabe](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Nachdem Sie die im Browser angezeigten Ergebnisse überprüft haben, können Sie den Auftrag **Starten**.

## <a name="limitations"></a>Einschränkungen

1.  Zeitrichtlinien werden bei Portaltests nicht unterstützt:

    * Falsche Reihenfolge: alle eingehenden Ereignisse werden sortiert.
    * Verspätetes Eintreffen: Es wird kein verspätetes Eintreffen geben, da Stream Analytics nur vorhandene Daten für Tests verwenden kann.
   
2.  C#-UDF wird nicht unterstützt.

3.  Alle Tests werden mit einem Auftrag ausgeführt, der über eine Streamingeinheit verfügt.

4.  Der Timeoutwert beträgt eine Minute. Somit kann jede Abfrage mit einer Fenstergröße von mehr als einer Minute keine Daten erhalten.

5.  Maschinelles Lernen wird nicht unterstützt.

6. Die Beispieldaten-API wird nach fünf Anforderungen für 15 Minuten gedrosselt. Nach Ende dieses Zeitfensters können Sie weitere Anforderungen von Beispieldaten ausführen. Diese Einschränkung wird auf Abonnementebene angewendet.

## <a name="troubleshooting"></a>Problembehandlung

1.  Wenn Sie den Fehler „There was a network connectivity issue when fetching the results. Please check your network and firewall settings.“ (Beim Abrufen der Ergebnisse ist ein Fehler aufgetreten. Überprüfen Sie die Netzwerk- und Firewalleinstellungen.) erhalten, führen Sie die folgenden Schritte aus:

  * Öffnen Sie [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) in einem Browser, um die Verbindung zum Dienst zu überprüfen. Wenn Sie diesen Link nicht öffnen können, aktualisieren Sie die Firewalleinstellungen.
  
2. Wenn Sie den Fehler „The request size is too big. Please reduce the input data size and try again“ (Die Anforderung ist zu groß. Verkleinern Sie die Eingabedaten, und versuchen Sie es noch einmal.) erhalten, führen Sie die folgenden Schritte aus:

  * Verringern der Eingabegröße: Testen Sie Ihre Abfrage mit einer kleineren Beispieldatei oder einem kleineren Zeitbereich.
  * Reduzieren der Abfragegröße: Wählen Sie einen Teil der Abfrage aus, und klicken Sie dann auf **Test selected query** (Ausgewählte Abfrage testen), um einen ausgewählten Teil der Abfrage zu testen.


## <a name="next-steps"></a>Nächste Schritte
* [Erstellen einer IoT-Lösung mithilfe von Stream Analytics:](./stream-analytics-build-an-iot-solution-using-stream-analytics.md) In diesem Tutorial wird erläutert, wie Sie eine End-to-End-Lösung mit einem Datengenerator erstellen, die den Datenverkehr an einer Mautstation simuliert.

* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)

* [Abfragebeispiele für gängige Stream Analytics-Verwendungsmuster](stream-analytics-stream-analytics-query-patterns.md)

* [Grundlegendes zu Eingaben für Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Grundlegendes zu den Ausgaben von Azure Stream Analytics](stream-analytics-define-outputs.md)
