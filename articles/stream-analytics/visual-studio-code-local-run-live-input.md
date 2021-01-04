---
title: Lokales Testen von Azure Stream Analytics-Abfragen unter Verwendung einer Livestreameingabe mithilfe von Visual Studio Code
description: In diesem Artikel erfahren Sie, wie Sie Abfragen mithilfe von Azure Stream Analytics-Tools für Visual Studio Code lokal unter Verwendung einer Livestreameingabe testen.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: dd4966ee75e9dc0ff401823e4291f8d299c8893c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93122893"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Lokales Testen von Stream Analytics-Abfragen unter Verwendung einer Livestreameingabe mithilfe von Visual Studio Code

Mit Azure Stream Analytics-Tools für Visual Studio Code können Sie Ihre Stream Analytics-Aufträge lokal unter Verwendung einer Livestreameingabe testen. Die Eingabe kann aus einer Quelle wie Azure Event Hubs oder Azure IoT Hub stammen. Die Ausgabeergebnisse werden als JSON-Dateien an den Ordner **LocalRunOutputs** in Ihrem Projekt gesendet.

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download), und starten Sie Visual Studio Code neu.

* Informieren Sie sich anhand [dieser Schnellstartanleitung](quick-create-visual-studio-code.md), wie Sie mithilfe von Visual Studio Code einen Stream Analytics-Auftrag erstellen.

## <a name="define-a-live-stream-input"></a>Definieren einer Livestreameingabe

1. Klicken Sie in Ihrem Stream Analytics-Projekt mit der rechten Maustaste auf den Ordner **Inputs**. Wählen Sie anschließend **ASA: Eingabe hinzufügen** im Kontextmenü aus.

   ![Hinzufügen einer Eingabe aus dem Ordner „Inputs“](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Drücken Sie alternativ **STRG+UMSCHALT+P** , um die Befehlspalette zu öffnen, und geben Sie **ASA: Eingabe hinzufügen** ein.

   ![Hinzufügen einer Stream Analytics-Eingabe in Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Wählen Sie in der Dropdownliste einen Eingabequellentyp aus.

   ![Auswählen von „IoT Hub“ als Eingabeoption](./media/quick-create-visual-studio-code/iot-hub.png)

3. Wenn Sie die Eingabe über die Befehlspalette hinzugefügt haben, wählen Sie das Stream Analytics-Abfrageskript aus, von dem die Eingabe verwendet werden soll. Es sollte automatisch mit dem Dateipfad zu **myASAproj.asaql** aufgefüllt werden.

   ![Auswählen eines Stream Analytics-Skripts in Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Wählen Sie im Dropdownmenü die Option **Aus Ihren Azure-Abonnements auswählen** aus.

    ![Auswählen aus Abonnements](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Konfigurieren Sie die neu generierte JSON-Datei. Sie können das CodeLens-Feature verwenden, um sich bei der Eingabe einer Zeichenfolge, der Auswahl aus einer Dropdownliste oder der direkten Änderung von Text in der Datei unterstützen zu lassen. Der folgende Screenshot zeigt **Select from your Subscriptions** (Aus Ihren Abonnements auswählen) als Beispiel.

   ![Konfigurieren von Eingaben in Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Anzeigen einer Eingabevorschau

Wählen Sie in Ihrer Liveeingabe-Konfigurationsdatei in der oberen Zeile die Option **Datenvorschau** aus, um sich zu vergewissern, dass Eingabedaten eingehen. Eingabedaten von einem IoT-Hub gehen ein und werden im Vorschaufenster angezeigt. Es dauert möglicherweise ein paar Sekunden, bis die Vorschau angezeigt wird.

 ![Anzeigen einer Vorschau der Liveeingabe](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Lokales Ausführen von Abfragen

Gehen Sie zurück zum Abfrage-Editor, und wählen Sie **Lokal ausführen** aus. Wählen Sie dann in der Dropdownliste **Use Live Input** (Liveeingabe verwenden) aus.

![Auswählen von „Lokal ausführen“ im Abfrage-Editor](./media/vscode-local-run/run-locally.png)

![Auswählen von „Use Live Input“ (Liveeingabe verwenden)](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Das Ergebnis wird im rechten Fenster angezeigt und alle drei Sekunden aktualisiert. Durch Auswählen von **Ausführen** können Sie den Test erneut ausführen. Sie können auch **In Ordner öffnen** auswählen, um die Ergebnisdateien im Datei-Explorer anzuzeigen und mit Visual Studio Code oder einem Tool wie Excel zu öffnen. Beachten Sie, dass die Ergebnisdateien nur im JSON-Format verfügbar sind.

Die Standardzeit für den Beginn der Ausgabeerstellung des Auftrags ist auf **Jetzt** festgelegt. Sie können die Zeit anpassen, indem Sie im Ergebnisfenster die Schaltfläche **Startzeitpunkt der Ausgabe** auswählen.

![Anzeigen des Ergebnisses der lokalen Ausführung](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Nächste Schritte

* [Erkunden von Azure Stream Analytics mit Visual Studio Code (Vorschauversion)](visual-studio-code-explore-jobs.md)

* [Verwenden des CI/CD-npm-Pakets von Stream Analytics](./cicd-overview.md)