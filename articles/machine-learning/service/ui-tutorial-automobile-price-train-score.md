---
title: 'Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche'
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie ein Machine Learning-Modell mithilfe einer grafischen Drag & Drop-Benutzeroberfläche trainieren, bewerten und bereitstellen. Dieses Tutorial ist der erste Teil einer zweiteiligen Reihe über das Prognostizieren von Automobilpreisen mithilfe der linearen Regression.
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: e37e99323c92adad0b9e897af8c276a8ac153371
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515635"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche

In diesem Tutorial befassen Sie sich eingehend mit dem Entwickeln einer Vorhersagelösung mithilfe der grafischen Benutzeroberfläche von Azure Machine Learning Service. Am Ende dieses Tutorials verfügen Sie über eine Lösung, mit der Sie den Preis jedes Autos basierend auf den von Ihnen an die Lösung gesendeten technischen Spezifikationen prognostizieren können.

Dieses Tutorial ist [eine Fortsetzung des Schnellstarts](ui-quickstart-run-experiment.md) und **der erste Teil einer zweiteiligen Tutorialreihe**. Sie müssen die Schnellstartanleitung jedoch nicht durcharbeiten, bevor Sie mit diesem Tutorial beginnen.

Im ersten Teil der Tutorialreihe lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren und Bereinigen von Daten (die gleichen Schritte wie im Schnellstart)
> * Trainieren eines Machine Learning-Modells
> * Bewerten und Auswerten eines Modells

Im [zweiten Teil](ui-tutorial-automobile-price-deploy.md) der Tutorialreihe erfahren Sie, wie Sie Ihr Vorhersagemodell als Azure-Webdienst bereitstellen.

> [!NOTE]
> Eine abgeschlossene Version dieses Tutorials ist als Beispielexperiment verfügbar.
> Navigieren Sie auf der Seite „Experimente“ zu **Neu hinzufügen** > **Beispiel 1 – Regression: Automobile Price Prediction(Basic)** (Automobilpreisvorhersage (Standard))


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Wenn Sie über einen Azure Machine Learning Service-Arbeitsbereich verfügen, fahren Sie mit dem [nächsten Abschnitt](#open-the-visual-interface-webpage) fort. Andernfalls erstellen Sie jetzt einen Arbeitsbereich.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>Öffnen der Webseite der grafischen Benutzeroberfläche

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).  

1. Wählen Sie in Ihrem Arbeitsbereich die Option **Grafische Benutzeroberfläche** aus.  Wählen Sie dann **Grafische Benutzeroberfläche starten** aus.  

    ![Screenshot des Azure-Portals, in dem dargestellt ist, wie der Zugriff auf die grafische Benutzeroberfläche über einen Machine Learning Service-Arbeitsbereich erfolgt](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

    Die Webseite der Benutzeroberfläche wird auf einer neuen Browserseite geöffnet.  

## <a name="import-and-clean-your-data"></a>Importieren und Bereinigen Ihrer Daten

Als Erstes müssen Sie die Daten bereinigen. Wenn Sie den Schnellstart abgeschlossen haben, können Sie Ihr Experiment mit den vorbereiteten Daten hier wiederverwenden. Wenn Sie den Schnellstart nicht abgeschlossen haben, überspringen Sie den nächsten Abschnitt, und [starten Sie mit einem neuen Experiment](#start-from-a-new-experiment).

### <a name="reuse-the-quickstart-experiment"></a>Wiederverwenden des Schnellstartexperiments

1. Öffnen Sie das Schnellstartexperiment.

1. Wählen Sie unten im Fenster die Option **Save As** (Speichern unter) aus.

1. Geben Sie im daraufhin angezeigten Popupdialogfeld einen neuen Namen ein.

    ![Screenshot, in dem das Umbenennen eines Experiments in „Tutorial – Predict Automobile Price“ (Tutorial – Automobilpreisvorhersage) dargestellt ist](./media/ui-tutorial-automobile-price-train-score/save-a-copy.png)

1. Das Experiment sollte in etwa wie folgt aussehen:

    ![Screenshot des erwarteten Zustands des Experiments. Das Automobildataset wird mit dem Modul zum Auswählen von Spalten verbunden, das wiederum mit dem Modul zum Bereinigen fehlender Daten verbunden wird.](./media/ui-tutorial-automobile-price-train-score/save-copy-result.png)

Wenn Sie das Schnellstartexperiment erfolgreich wiederverwendet haben, überspringen Sie den nächsten Abschnitt, um mit dem [Trainieren Ihres Modells](#train-the-model) zu beginnen.

### <a name="start-from-a-new-experiment"></a>Starten mit einem neuen Experiment

Wenn Sie den Schnellstart nicht abgeschlossen haben, führen Sie die folgenden Schritte aus, um schnell ein neues Experiment zu erstellen, das das Automobildataset importiert und bereinigt.

1. Erstellen Sie ein neues Experiment, indem Sie unten im Fenster der grafischen Benutzeroberfläche die Option **+Neu** auswählen.

1. Wählen Sie **Experimente** >  **Blank Experiment** (Leeres Experiment) aus.

1. Wählen Sie oben im Canvas-Panel den Standardexperimentnamen **Experimented Created on ...** (Experiment erstellt am ...) aus, und geben Sie einen aussagekräftigen Namen ein. Beispielsweise **Automobile price prediction** (Automobilpreisvorhersage). Der Name muss nicht eindeutig sein.

1. Links vom Experimentbereich finden Sie eine Palette mit Datensätzen und Modulen. Um nach Modulen zu suchen, verwenden Sie das Suchfeld am oberen Rand der Modulpalette. Geben Sie im Suchfeld das Wort **automobile** ein, um nach dem Dataset mit dem Namen **Automobile price data (Raw)** (Automobilpreisdaten (Roh)) zu suchen. Ziehen Sie das Dataset in den Experimentbereich.

    ![Screenshot der Suche nach dem Automobilpreis-Dataset](./media/ui-tutorial-automobile-price-train-score/automobile-dataset.png)

    Da Sie jetzt über die Daten verfügen, können Sie ein Modul hinzufügen, mit dem die Spalte **normalized-losses** (normalisierte Verluste) vollständig entfernt wird. Fügen Sie dann ein weiteres Modul hinzu, mit dem alle Zeilen entfernt werden, in denen Daten fehlen.

1. Geben Sie im Suchfeld die Wörter **select columns** (Spalten auswählen) ein, um nach dem Modul **Select Columns in Dataset** (Spalten im Dataset auswählen) zu suchen. Ziehen Sie es dann in den Experimentbereich. Mit diesem Modul können Sie auswählen, welche Datenspalten in das Modell eingeschlossen bzw. daraus ausgeschlossen werden sollen.

1. Verbinden Sie den Ausgabeport des Datasets **Automobile price data (Raw)** (Automobilpreisdaten (roh)) mit dem Eingabeport von „Select Columns in Dataset“ (Spalten in Dataset auswählen).

    ![Animierte GIF, die zeigt, wie das Modul für Automobilpreisdaten mit dem Modul zum Auswählen von Spalten verbunden wird](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. Wählen Sie das Modul „Select Columns in Dataset“ (Spalten im Dataset auswählen) aus, und wählen Sie dann im Bereich **Properties** (Eigenschaften) die Option **Launch column selector** (Spaltenauswahl starten) aus.

   1. Wählen Sie auf der linken Seite die Option **With rules** (Mit Regeln) aus.

   1. Wählen Sie neben **Beginnen mit** die Option **Alle Spalten** aus. Mit diesen Regeln wird **Select Columns in Dataset** angewiesen, alle Spalten zu durchlaufen (mit Ausnahme derer, die wir jetzt ausschließen werden).

   1. Wählen Sie in den Dropdownlisten die Optionen **Ausschließen** und **Spaltennamen** aus, und geben Sie dann im Textfeld die Zeichenfolge **normalized-losses** ein.

   1. Wählen Sie die Schaltfläche „OK“ aus, um die Spaltenauswahl zu schließen (unten rechts).

     Der Eigenschaftenbereich für **Select Columns in Dataset** zeigt nun an, dass mit Ausnahme von **normalized-losses** alle Spalten des Datasets übergeben werden.

1. Fügen Sie dem Modul **Select Columns in Dataset** einen Kommentar hinzu, indem Sie auf das Modul doppelklicken und „normalized-losses ausschließen“ eingeben. Auf diese Weise können Sie auf einen Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt.

    ![Screenshot der richtigen Konfiguration des Moduls zum Auswählen von Spalten](./media/ui-tutorial-automobile-price-train-score/select-columns.png)

1. Geben Sie im Suchfeld das Wort **Clean** ein, um nach dem Modul **Clean Missing Data** (Fehlende Daten bereinigen) zu suchen. Ziehen Sie das Modul **Clean Missing Data** in den Experimentbereich, und verbinden Sie es mit dem Modul **Select Columns in Dataset**.

1. Klicken Sie im **Eigenschaftenbereich** unter **Cleaning mode** (Reinigungsmodus) auf die Option **Remove entire row** (Gesamte Zeile entfernen). Mit diesen Optionen wird **Clean Missing Data** (Fehlende Daten bereinigen) angewiesen, Daten durch das Entfernen von Zeilen mit fehlenden Werten zu bereinigen. Doppelklicken Sie auf das Modul, und geben Sie den Kommentar "Remove missing value rows" ein.

![Screenshot der richtigen Konfiguration des Moduls „Clean Missing Data“](./media/ui-tutorial-automobile-price-train-score/clean-missing-data.png)

## <a name="train-the-model"></a>Modelltraining

Da die Daten jetzt vorbereitet wurden, können Sie ein Vorhersagemodell erstellen. Zum Trainieren des Modells verwenden Sie Ihre Daten. Dann testen Sie das Modell, um festzustellen, wie genau die Preisvorhersagen sind.

**Klassifizierung** und **Regression** sind zwei Algorithmen für beaufsichtigtes maschinelles Lernen. Mit der **Klassifizierung** wird eine Antwort aus einem definierten Satz von Kategorien vorhergesagt, z. B. eine Farbe (Rot, Blau oder Grün). Die **Regression** wird verwendet, um eine Zahl vorherzusagen.

Da Sie einen Preis (also eine Zahl) vorhersagen möchten, können Sie einen Regressionsalgorithmus verwenden. In diesem Beispiel verwenden Sie ein lineares Regressionsmodell.

Trainieren Sie das Modell, indem Sie einen Datensatz mit dem Preis einspeisen. Das Modell überprüft die Daten und sucht nach Korrelationen zwischen den Features eines Autos und seinem Preis. Testen Sie dann das Modell, indem Sie einen Satz von Features und Funktionen für bekannte Automobile eingeben und prüfen, wie genau das Modell den bekannten Preis vorhersagt.

Verwenden Sie Ihre Daten sowohl zum Trainieren als auch zum Testen des Modells, indem Sie die Daten in separate Trainings- und Testdatasets aufteilen.

1. Geben Sie im Suchfeld die Wörter **split data** (Daten aufteilen) ein, um nach dem Modul **Split Data** (Daten aufteilen) zu suchen, und verbinden Sie es mit dem linken Port des Moduls **Clean Missing Data** (Fehlende Daten bereinigen).

1. Wählen Sie das Modul **Split Data** (Daten aufteilen) aus, das Sie eben verbunden haben. Legen Sie im Bereich „Properties“ (Eigenschaften) den Anteil der Zeilen im ersten Ausgabedatensatz auf „0,7“ fest. Mit dieser Einstellung verwenden wir 70 Prozent der Daten zum Trainieren des Modells und halten 30 Prozent für Tests zurück.

    ![Screenshot der richtigen Konfiguration des Eigenschaftenbereichs. Für „Split Data“ (Daten aufteilen) sollten die Werte „Split Rows“ (Zeilen aufteilen), „0,7“, „Randomized split“ (Zufällige Aufteilung), „0“ und „False“ (Falsch) verwendet werden.](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. Doppelklicken Sie auf **Split Data** (Daten aufteilen), und geben Sie den Kommentar „Split the dataset into training set(0.7) and test set(0.3)“ (Dataset in Trainingssatz (0,7) und Testsatz (0,3) aufteilen) ein.

1. Löschen Sie zum Auswählen des Lernalgorithmus das Suchfeld der Modulpalette.

1. Erweitern Sie **Machine Learning**, und erweitern Sie dann **Initialize Model** (Modell initialisieren). Daraufhin werden verschiedene Kategorien von Modulen angezeigt, die zur Initialisierung eines Algorithmus für maschinelles Lernen verwendet werden können.

1. Wählen Sie für dieses Experiment die Optionen **Regression** > **Linear Regression** (Lineare Regression) aus, und ziehen Sie das Modul in den Experimentbereich.

    ![Screenshot der richtigen Konfiguration des Eigenschaftenbereichs. Für „Split Data“ (Daten aufteilen) sollten die Werte „Split Rows“ (Zeilen aufteilen), „0,7“, „Randomized split“ (Zufällige Aufteilung), „0“ und „False“ (Falsch) verwendet werden.](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. Suchen Sie nach dem Modul **Train Model** (Modell trainieren), und ziehen Sie es in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls „Linear Regression“ mit der linken Eingabe des Moduls „Train Model“, und verbinden Sie die Ausgabe der Trainingsdaten (den linken Port) des Moduls **Split Data** mit der rechten Eingabe des Moduls **Train Model**.

    ![Screenshot der richtigen Konfiguration des Moduls „Train Model“. Das Modul „Linear Regression“ ist mit dem linken Port des Moduls „Train Model“ verbunden, und das Modul „Split Data“ ist mit dem rechten Port von „Train Model“ verbunden.](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. Wählen Sie das Modul **Train Model** (Modell trainieren) aus. Wählen Sie im Bereich „Properties“ (Eigenschaften) die Option „Launch column selector“ (Spaltenauswahl starten) aus, und geben Sie dann neben **Include column names** (Spaltennamen einschließen) das Wort **price** (Preis) ein. „Price“ ist der Wert, den unser Modell vorhersagen wird.

    ![Screenshot der richtigen Konfiguration des Moduls für die Spaltenauswahl. With rules (Mit Regeln) > Include column names (Spaltennamen einschließen) > „price“ (Preis)](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    Das Experiment sollte jetzt wie folgt aussehen:
    ![Screenshot der richtigen Konfiguration des Experiments nach dem Hinzufügen des Moduls „Train Model“.](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>Ausführen des Trainingsexperiments

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>Bewerten und Auswerten des Modells

Nachdem Sie das Modell mit 70 Prozent Ihrer Daten trainiert haben, können Sie unter Verwendung der restlichen 30 Prozent der Daten bewerten, wie gut das Modell funktioniert.

1. Geben Sie im Suchfeld die Wörter **score model** (Modell bewerten) ein, um nach dem Modul **Score Model** (Modell bewerten) zu suchen, und ziehen Sie das Modul in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls **Train Model** mit dem linken Eingabeport des Moduls **Score Model**. Verbinden Sie die Testdatenausgabe (den rechten Port) des Moduls **Split Data** mit dem rechten Eingabeport des Moduls **Score Model**.

1. Geben Sie im Suchfeld das Wort **evaluate** (auswerten) ein, um nach dem Modul **Evaluate Model** (Modell auswerten) zu suchen, und ziehen Sie das Modul in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls **Score Model** mit der linken Eingabe des Moduls **Evaluate Model**. Das endgültige Experiment sollte in etwa wie folgt aussehen:

    ![Screenshot der endgültigen richtigen Konfiguration des Experiments.](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. Führen Sie das Experiment mit dem gleichen Computeziel aus, das Sie zuvor verwendet haben.

1. Zeigen Sie die Ausgabe des Moduls **Score Model** an, indem Sie den Ausgabeport des Moduls **Score Model** und dann **Visualize** (Visualisieren) auswählen. Die Ausgabe zeigt die vorhergesagten Preiswerte zusammen mit den bekannten Werten aus den Testdaten an.

    ![Screenshot der Ausgabevisualisierung mit hervorgehobener Spalte „Scored Labels“](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. Um die Ausgabe des Moduls „Evaluate Model“ anzuzeigen, wählen Sie den Ausgabeport aus, und wählen Sie dann „Visualize“ (Visualisieren) aus.

    ![Screenshot der Auswertungsergebnisse für das endgültige Experiment.](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

Die folgenden Statistiken werden für Ihr Modell angezeigt:

* **Mean Absolute Error (MAE)** (Mittlerer absoluter Fehler): Der Mittelwert der absoluten Fehler (ein Fehler ist die Differenz zwischen dem prognostizierten und dem tatsächlichen Wert).
* **Root Mean Squared Error (RMSE)** (Wurzel des mittleren quadratischen Fehlers): Die Quadratwurzel des Durchschnitts des Quadrats der Prognosefehler für das Testdataset.
* **Relative Absolute Error**: Der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
* **Relative Squared Error**: Der Durchschnitt der quadrierten Fehler relativ zur quadrierten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
* **Coefficient of Determination**: Dieser auch als „R-Quadrat“ bezeichnete Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.

Für jede Fehlerstatistik sind kleinere Werte besser. Ein kleinerer Wert gibt an, dass die Vorhersagen genauer mit den tatsächlichen Werten übereinstimmen. Für den Bestimmungskoeffizienten gilt Folgendes: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>Verwalten von Experimenten im Azure Machine Learning Service-Arbeitsbereich

Die von Ihnen auf der grafischen Benutzeroberfläche erstellten Experimente können im Azure Machine Learning Service-Arbeitsbereich verwaltet werden. Verwenden Sie den Arbeitsbereich, um detailliertere Informationen anzuzeigen, z. B. einzelne Experimentausführungen, Diagnoseprotokolle, Ausführungsdiagramme und mehr.

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).  

1. Wählen Sie in Ihrem Arbeitsbereich die Option **Experiments** (Experimente) aus. Wählen Sie dann das Experiment aus, das Sie erstellt haben.

    ![Screenshot, der zeigt, wie Sie im Azure-Portal zu Experimenten navigieren](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    Auf dieser Seite werden eine Übersicht über das Experiment und die letzten Ausführungen angezeigt.

    ![Screenshot einer Übersicht über die Experimentstatistik im Azure-Portal](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. Wählen Sie eine Ausführungsnummer aus, um weitere Details zu einer bestimmten Ausführung anzuzeigen.

    ![Screenshot eines detaillierten Ausführungsberichts](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    Der Ausführungsbericht wird in Echtzeit aktualisiert. Wenn Sie in Ihrem Experiment das Modul **Execute Python Script** (Python-Skript ausführen) verwendet haben, können Sie die auszugebenden Skriptprotokolle auf der Registerkarte **Logs** (Protokolle) angeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil dieses Tutorial haben Sie die folgenden Schritte ausgeführt:

* Wiederverwenden des im Schnellstart erstellten Experiments
* Vorbereiten der Daten
* Modelltraining
* Bewerten und Auswerten des Modells

Im zweiten Teil erfahren Sie, wie Sie Ihr Modell als Azure-Webdienst bereitstellen.

> [!div class="nextstepaction"]
> [Mit dem Bereitstellen von Modellen fortfahren](ui-tutorial-automobile-price-deploy.md)
