---
title: 'Tutorial: Vorhersagen von Autopreisen mit dem Designer'
titleSuffix: Azure Machine Learning
description: Trainieren Sie ein Machine Learning-Modell, um Automobilpreise mithilfe der linearen Regression vorherzusagen. Dieses Tutorial ist der erste Teil einer zweiteiligen Reihe.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: ca812fc7548e3c70f1faa1e1ed6a34afda3872af
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575974"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Tutorial: Prognostizieren von Automobilpreisen mit dem Designer


In diesem zweiteiligen Tutorial erfahren Sie, wie Sie mithilfe des Designers von Azure Machine Learning ein Machine Learning-Modell trainieren und bereitstellen, das den Preis eines beliebigen Autos prognostiziert. Bei dem Designer handelt es sich um ein Drag & Drop-Tool, mit dem Sie ganz ohne Programmieraufwand Machine Learning-Modelle erstellen können.

Im ersten Teil des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer neuen Pipeline
> * Importieren von Daten
> * Aufbereiten von Daten
> * Trainieren eines Machine Learning-Modells
> * Auswerten eines Machine Learning-Modells

Im [zweiten Teil](tutorial-designer-automobile-price-deploy.md) des Tutorials stellen Sie Ihr Modell als Echtzeit-Rückschlussendpunkt bereit, um damit den Preis eines beliebigen Autos basierend auf den von Ihnen an die Lösung gesendeten technischen Spezifikationen zu prognostizieren. 

> [!NOTE]
>Eine fertige Version dieses Tutorials ist als Beispielpipeline verfügbar.
>
>Diese finden Sie im Designer in Ihrem Arbeitsbereich. Wählen Sie im Abschnitt **Neue Pipeline** die Option **Sample 1 - Regression: Automobile Price Prediction(Basic)** (Beispiel 1: Regression: Automobilpreisvorhersage (Standard)) aus.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Erstellen einer neuen Pipeline

Azure Machine Learning-Pipelines fassen mehrere Machine Learning- und Datenverarbeitungsschritte in einer einzelnen Ressource zusammen. Pipelines ermöglichen die Strukturierung, Verwaltung und Wiederverwendung komplexer Machine Learning-Workflows für verschiedene Projekte und Benutzer.

Für die Erstellung einer Azure Machine Learning-Pipeline benötigen Sie einen Azure Machine Learning-Arbeitsbereich. In diesem Abschnitt erfahren Sie, wie Sie diese beiden Ressourcen erstellen.

### <a name="create-a-new-workspace"></a>Erstellen eines neuen Arbeitsbereichs

Sie benötigen einen Azure Machine Learning-Arbeitsbereich, um den Designer verwenden zu können. Der Arbeitsbereich ist die Ressource der obersten Ebene für Azure Machine Learning und bietet einen zentralen Ort für die Arbeit mit allen Artefakten, die Sie in Azure Machine Learning erstellen. Eine Anleitung zum Erstellen eines Arbeitsbereichs finden Sie unter [Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen](how-to-manage-workspace.md).

> [!NOTE]
> Wenn für Ihren Arbeitsbereich ein virtuelles Netzwerk verwendet wird, müssen Sie zusätzliche Konfigurationsschritte ausführen, um den Designer verwenden zu können. Weitere Informationen finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md).

### <a name="create-the-pipeline"></a>Erstellen der Pipeline

1. Melden Sie sich bei <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> an, und wählen Sie den gewünschten Arbeitsbereich aus.

1. Wählen Sie **Designer** aus.

    ![Screenshot des visuellen Arbeitsbereichs: Vorgehensweise für den Zugriff auf den Designer](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Wählen Sie **Easy-to-use prebuilt modules** (Benutzerfreundliche vorgefertigte Module) aus.

1. Wählen Sie im oberen Bereich der Canvas den Standardpipelinenamen **Pipeline-Created-on** aus. Ändern Sie den Namen in *Automobile price prediction* (Automobilpreisvorhersage). Der Name muss nicht eindeutig sein.

## <a name="set-the-default-compute-target"></a>Festlegen des Standardcomputeziels

Ein Pipeline wird auf einem Computeziel ausgeführt. Dabei handelt es sich um eine an Ihren Arbeitsbereich angefügte Computeressource. Nachdem Sie ein Computeziel erstellt haben, können Sie es für künftige Ausführungen wiederverwenden.

Sie können ein **Standardcomputeziel** für die gesamte Pipeline festlegen, wodurch jedes Modul standardmäßig das gleiche Computeziel verwendet. Sie können aber auch modulspezifische Computeziele angeben.

1. Wählen Sie im oberen Bereich der Canvas neben dem Namen der Pipeline das **Zahnradsymbol** ![Screenshot des Zahnradsymbols](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) aus, um den Bereich **Einstellungen** zu öffnen.

1. Wählen Sie rechts neben der Canvas im Bereich **Einstellungen** die Option **Computeziel auswählen** aus.

    Falls Sie bereits über ein verfügbares Computeziel verfügen, können Sie es auswählen, um diese Pipeline auszuführen.

    > [!NOTE]
    > Der Designer kann nur Trainingsexperimente in Azure Machine Learning Compute ausführen, andere Computeziele werden jedoch nicht angezeigt.

1. Geben Sie einen Namen für die Computeressource ein.

1. Wählen Sie **Speichern** aus.

    > [!NOTE]
    > Die Erstellung einer Computeressource dauert etwa fünf Minuten. Nach der Erstellung der Ressource können Sie sie wiederverwenden und diese Wartezeit in künftigen Ausführungen vermeiden.
    >
    > Eine im Leerlauf befindliche Computeressource wird automatisch auf 0 Knoten skaliert, um Kosten zu sparen. Wenn Sie sie nach einer Verzögerung erneut verwenden, müssen Sie unter Umständen etwa fünf Minuten warten, bis sie wieder hochskaliert wurde.

## <a name="import-data"></a>Daten importieren

Im Designer stehen mehrere Beispieldatasets zur Verfügung, mit denen Sie experimentieren können. Verwenden Sie für dieses Tutorial **Automobile Price Data (Raw)** (Automobilpreisdaten (Rohdaten)). 

1. Links neben der Pipelinecanvas befindet sich eine Palette mit Datasets und Modulen. Wählen Sie **Beispieldatasets** aus, um die verfügbaren Beispieldatasets anzuzeigen.

1. Wählen Sie das Dataset **Automobile Price Data (Raw)** (Automobilpreisdaten (Rohdaten)) aus, und ziehen Sie es auf die Canvas.

   ![Ziehen von Daten in den Experimentbereich](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>Visualisieren der Daten

Sie können die Daten visualisieren, um sich mit dem zu verwendenden Dataset vertraut zu machen.

1. Klicken Sie mit der rechten Maustaste auf **Automobile Price Data (Raw)** , und wählen Sie **Visualisierung**.

1. Wählen Sie die verschiedenen Spalten im Datenfenster aus, um Informationen zur jeweiligen Spalte zu erhalten.

    Jede Zeile steht für ein Fahrzeug, und die Variablen, die den einzelnen Fahrzeugen zugeordnet sind, werden als Spalten angezeigt. Es gibt 205 Zeilen und 26 Spalten in diesem Dataset.

## <a name="prepare-data"></a>Vorbereiten von Daten

Für Datasets ist vor der Analyse in der Regel eine Vorverarbeitung erforderlich. Bei der Untersuchung des Datasets sind Ihnen unter Umständen einige fehlende Werte aufgefallen. Damit das Modell die Daten korrekt analysieren kann, müssen diese fehlenden Werte bereinigt werden.

### <a name="remove-a-column"></a>Entfernen einer Spalte

Wenn Sie ein Modell trainieren, müssen Sie etwas gegen fehlende Daten tun. In diesem Dataset fehlen in der Spalte **normalized-losses** zahlreiche Werte. Daher schließen wir diese Spalte ganz aus dem Modell aus.

1. Erweitern Sie in der Modulpalette auf der linken Seite der Canvas den Abschnitt **Datentransformation**, und suchen Sie nach dem Modul **Select Columns in Dataset** (Spalten im Dataset auswählen).

1. Ziehen Sie das Modul **Select Columns in Dataset** (Spalten im Dataset auswählen) auf die Canvas. Legen Sie das Modul unter dem Datasetmodul ab.

1. Verbinden Sie das Dataset **Automobile price data (Raw)** (Automobilpreisdaten (Rohdaten)) mit dem Modul **Select Columns in Dataset** (Spalten in Dataset auswählen). Ziehen Sie eine Linie vom Ausgabeport des Datasets zum Eingabeport von **Select Columns in Dataset** (Spalten im Dataset auswählen) – also von dem kleinen Kreis am unteren Rand des Datasets auf der Canvas zu dem kleinen Kreis am oberen Rand des Moduls.

    > [!TIP]
    > Um einen Datenfluss für Ihre Pipeline zu erstellen, verbinden Sie den Ausgabeport eines Moduls mit dem Eingabeport eines anderen Moduls.
    >

    ![Verbinden von Modulen](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Wählen Sie das Modul **Select Columns in Dataset** aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails die Option **Spalte bearbeiten** aus.

1. Erweitern Sie neben **Einschließen** die Dropdownliste **Spaltennamen**, und wählen Sie **Alle Spalten** aus.

1. Wählen Sie **+** aus, um eine neue Regel hinzuzufügen.

1. Wählen Sie in den Dropdownmenüs die Optionen **Ausschließen** und **Spaltennamen** aus.
    
1. Geben Sie *normalized-losses* in das Textfeld ein.

1. Wählen Sie rechts unten **Speichern** aus, um die Spaltenauswahl zu schließen.

    ![Ausschließen einer Spalte](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Wählen Sie das Modul **Select Columns in Dataset** aus. 

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails das Textfeld **Kommentar** aus, und geben Sie *Exclude normalized losses* (Normalisierte Verluste ausschließen) ein.

    Kommentare werden im Diagramm angezeigt, um Sie bei der Strukturierung Ihrer Pipeline zu unterstützen.

### <a name="clean-missing-data"></a>Fehlende Daten bereinigen

Auch nach dem Entfernen der Spalte **normalized-losses** weist Ihr Dataset noch fehlende Werte auf. Die restlichen fehlenden Daten können mithilfe des Moduls **Clean Missing Data** (Fehlende Daten bereinigen) entfernt werden.

> [!TIP]
> Die Bereinigung fehlender Werte in den Eingabedaten wird bei den meisten Modulen im Designer vorausgesetzt.

1. Erweitern Sie in der Modulpalette auf der linken Seite der Canvas den Abschnitt **Datentransformation**, und suchen Sie nach dem Modul **Clean Missing Data** (Fehlende Daten bereinigen).

1. Ziehen Sie das Modul **Clean Missing Data** (Fehlende Daten bereinigen) auf die Pipelinecanvas. Verbinden Sie es mit dem Modul **Select Columns in Dataset** (Spalten im Dataset auswählen). 

1. Wählen Sie das Modul **Clean Missing Data** (Fehlende Daten bereinigen) aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails die Option **Spalte bearbeiten** aus.

1. Erweitern Sie im angezeigten Fenster **Columns to be cleaned** (Zu bereinigende Spalten) das Dropdownmenü neben **Einschließen**. Wählen Sie **Alle Spalten** aus.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails unter **Cleaning mode** (Bereinigungsmodus) die Option **Remove entire row** (Gesamte Zeile entfernen) aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails das Textfeld **Kommentar** aus, und geben Sie *Remove missing value rows* (Zeilen mit fehlenden Werten entfernen) ein. 

    Ihre Pipeline sollte nun in etwa wie folgt aussehen:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png"alt-text="Select-column":::

## <a name="train-a-machine-learning-model"></a>Trainieren eines Machine Learning-Modells

Nachdem Sie nun über die Module für die Datenverarbeitung verfügen, können Sie die Trainingsmodule einrichten.

Da Sie einen Preis (also eine Zahl) vorhersagen möchten, können Sie einen Regressionsalgorithmus verwenden. In diesem Beispiel verwenden Sie ein lineares Regressionsmodell.

### <a name="split-the-data"></a>Teilen der Daten

Das Aufteilen von Daten ist eine gängige Aufgabe beim maschinellen Lernen. Ihre Daten werden in zwei separate Datasets aufgeteilt: eins zum Trainieren des Modells und eins zum Testen der Ergebnisqualität des Modells.

1. Erweitern Sie in der Modulpalette den Abschnitt **Datentransformation**, und suchen Sie nach dem Modul **Split Data** (Daten aufteilen).

1. Ziehen Sie das Modul **Split Data** (Daten aufteilen) auf die Pipelinecanvas.

1. Verbinden Sie den linken Port des Moduls **Clean Missing Data** (Fehlende Daten bereinigen) mit dem Modul **Split Data** (Daten aufteilen).

    > [!IMPORTANT]
    > Achten Sie darauf, dass der linke Ausgabeport von **Clean Missing Data** (Fehlende Daten bereinigen) mit **Split Data** (Daten aufteilen) verbunden ist. Der linke Port enthält die bereinigten Daten. Der rechte Port enthält die verworfenen Daten.

1. Wählen Sie das Modul **Split Data** (Daten aufteilen) aus.

1. Legen Sie rechts neben der Canvas im Bereich mit den Moduldetails die Option **Fraction of rows in the first output dataset** (Anteil der Zeilen im ersten Ausgabedataset) auf „0,7“ fest.

    Mit dieser Option werden 70 Prozent der Daten zum Trainieren des Modells und 30 Prozent zum Testen verwendet. Auf das Dataset mit den 70 Prozent kann über den linken Ausgabeport zugegriffen werden. Die restlichen Daten sind über den rechten Ausgabeport verfügbar.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails das Textfeld **Kommentar** aus, und geben Sie *Split the dataset into training set (0.7) and test set (0.3)* (Dataset in Trainingssatz (0,7) und Testsatz (0,3) unterteilen) ein.

### <a name="train-the-model"></a>Trainieren des Modells

Trainieren Sie das Modell, indem Sie ein Dataset mit Preis bereitstellen. Durch den Algorithmus wird ein Modell erstellt, das die Beziehung zwischen den Features und dem Preis aus den Trainingsdaten erklärt.

1. Erweitern Sie in der Modulpalette den Abschnitt **Machine Learning-Algorithmen**.
    
    Mit dieser Option werden verschiedene Kategorien von Modulen angezeigt, die Sie zur Initialisierung von Lernalgorithmen verwenden können.

1. Wählen Sie **Regression** > **Linear Regression** (Lineare Regression) aus, und ziehen Sie sie auf die Pipelinecanvas.

1. Erweitern Sie in der Modulpalette den Abschnitt **Module training** (Modultraining), und ziehen Sie das Modul **Train Model** (Modell trainieren) auf die Canvas.

1. Verbinden Sie die Ausgabe des Moduls **Linear Regression** (Lineare Regression) mit der linken Eingabe des Moduls **Train Model** (Modell trainieren).

1. Verbinden Sie die Trainingsdatenausgabe (linker Port) des Moduls **Split Data** (Daten aufteilen) mit der rechten Eingabe des Moduls **Train Model** (Modell trainieren).
    
    > [!IMPORTANT]
    > Achten Sie darauf, dass der linke Ausgabeport von **Split Data** (Daten aufteilen) mit **Train Model** (Modell trainieren) verbunden ist. Der linke Port enthält den Trainingssatz. Der rechte Port enthält den Testsatz.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png"alt-text="Screenshot: Richtige Konfiguration des Moduls „Train Model“ (Modell trainieren). Das Modul „Linear Regression“ (Lineare Regression) ist mit dem linken Port des Moduls „Train Model“ verbunden, und das Modul „Split Data“ (Daten aufteilen) ist mit dem rechten Port von „Train Model“ verbunden.":::

1. Wählen Sie das Modul **Train Model** (Modell trainieren) aus.

1. Wählen Sie rechts neben der Canvas im Bereich mit den Moduldetails den Selektor **Spalte bearbeiten** aus.

1. Erweitern Sie im Dialogfeld **Bezeichnungsspalte** das Dropdownmenü, und wählen Sie **Spaltennamen** aus. 

1. Geben Sie *price* (Preis) in das Textfeld ein, um den Wert anzugeben, der von Ihrem Modell prognostiziert wird.

    >[!IMPORTANT]
    > Achten Sie darauf, dass Sie den Spaltennamen genau eingeben. Schreiben Sie **price** nicht groß. 

    Ihre Pipeline sollte wie folgt aussehen:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png"alt-text="Screenshot: Richtige Pipelinekonfiguration nach dem Hinzufügen des Moduls „Train Model“ (Modell trainieren)":::

### <a name="add-the-score-model-module"></a>Hinzufügen des Moduls „Score Model“ (Modell bewerten)

Nachdem Sie das Modell mit 70 Prozent der Daten trainiert haben, können Sie unter Verwendung der restlichen 30 Prozent bewerten, wie gut das Modell funktioniert.

1. Geben Sie im Suchfeld den Begriff *score model* ein, um nach dem Modul **Score Model** (Bewertungsmodell) zu suchen. Ziehen Sie das Modul auf die Pipelinecanvas. 

1. Verbinden Sie die Ausgabe des Moduls **Train Model** mit dem linken Eingabeport des Moduls **Score Model**. Verbinden Sie die Testdatenausgabe (den rechten Port) des Moduls **Split Data** mit dem rechten Eingabeport des Moduls **Score Model**.

### <a name="add-the-evaluate-model-module"></a>Hinzufügen des Moduls „Evaluate Model“

Verwenden Sie das Modul **Evaluate Model** (Modell auswerten), um auszuwerten, wie gut das Testdataset von Ihrem Modell bewertet wurde.

1. Geben Sie im Suchfeld den Begriff *evaluate* ein, um nach dem Modul **Evaluate Model** (Modell auswerten) zu suchen. Ziehen Sie das Modul auf die Pipelinecanvas. 

1. Verbinden Sie die Ausgabe des Moduls **Score Model** mit der linken Eingabe des Moduls **Evaluate Model**. 

    Die fertige Pipeline sollte in etwa wie folgt aussehen:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png"alt-text="Screenshot: Richtige Pipelinekonfiguration":::

## <a name="submit-the-pipeline"></a>Übermitteln der Pipeline

Nachdem die Einrichtung Ihrer Pipeline jetzt abgeschlossen ist, können Sie eine Pipelineausführung übermitteln, um Ihr Machine Learning-Modell zu trainieren. Sie können jederzeit eine gültige Pipelineausführung übermitteln, mit der während der Entwicklung Änderungen an ihrer Pipeline überprüft werden können.

1. Wählen Sie im oberen Bereich der Canvas die Option **Senden** aus.

1. Wählen Sie im Dialogfeld **Pipelineausführung einrichten** die Option **Neu erstellen** aus.

    > [!NOTE]
    > Experimente fassen ähnliche Pipelineausführungen in einer Gruppe zusammen. Wenn Sie eine Pipeline mehrmals ausführen, können Sie für weitere Ausführungen das gleiche Experiment auswählen.

    1. Geben Sie unter **Neuer Experimentname** einen aussagekräftigen Namen ein.

    1. Klicken Sie auf **Submit** (Senden).
    
    Ausführungsstatus und Details können rechts oben auf der Canvas angezeigt werden.
    
    Falls dies die erstmalige Ausführung ist, kann es bis zu 20 Minuten dauern, bis der Ausführungsvorgang für die Pipeline vollständig abgeschlossen ist. In den Standardcomputeeinstellungen ist eine minimale Knotengröße von 0 festgelegt. Das bedeutet, dass der Designer Ressourcen nach dem Leerlauf zuordnen muss. Wiederholte Pipelineausführungen werden schneller abgeschlossen, da die Computeressourcen bereits zugeordnet sind. Außerdem verwendet der Designer für jedes Modul zwischengespeicherte Ergebnisse, um die Effizienz weiter zu steigern.

### <a name="view-scored-labels"></a>Anzeigen bewerteter Bezeichnungen

Nach Abschluss der Ausführung können Sie sich die Ergebnisse ansehen. Sehen Sie sich zuerst die vom Regressionsmodell generierten Vorhersagen an.

1. Klicken Sie mit der rechten Maustaste auf das Modul **Score Model** und wählen Sie **Visualisieren** aus, um seine Ausgabe anzuzeigen.

    Hier sehen Sie die vorhergesagten Preise und die tatsächlichen Preise aus den Testdaten.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png"alt-text="Screenshot: Ausgabevisualisierung mit hervorgehobener Spalte „Scored Labels“ (Bewertete Bezeichnungen)":::

### <a name="evaluate-models"></a>Auswerten von Modellen

Verwenden Sie das Modul **Evaluate Model** (Modell auswerten), um zu prüfen, wie gut das trainierte Modell beim Testdataset abgeschnitten hat.

1. Klicken Sie mit der rechten Maustaste auf das Modul **Evaluate Model** und wählen Sie **Visualisieren** aus, um seine Ausgabe anzuzeigen.

Die folgenden Statistiken werden für Ihr Modell angezeigt:

* **Mean Absolute Error (MAE)** (Mittlerer absoluter Fehler): Der Mittelwert der absoluten Fehler. Ein Fehler ist die Differenz zwischen dem prognostizierten und dem tatsächlichen Wert.
* **Root Mean Squared Error (RMSE)** (Wurzel des mittleren quadratischen Fehlers): Die Quadratwurzel des Durchschnitts des Quadrats der Prognosefehler für das Testdataset.
* **Relative Absolute Error**: Der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
* **Relative Squared Error**: Der Durchschnitt der quadrierten Fehler relativ zur quadrierten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
* **Coefficient of Determination**: Dieser auch als „R-Quadrat“ bezeichnete Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.

Für jede Fehlerstatistik sind kleinere Werte besser. Ein kleinerer Wert gibt an, dass die Vorhersagen näher bei den tatsächlichen Werten liegen. Für den Bestimmungskoeffizienten gilt Folgendes: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie mit dem zweiten Teil des Tutorials ([Bereitstellen eines Machine Learning-Modells mit dem Designer](tutorial-designer-automobile-price-deploy.md)) fortfahren möchten.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Im zweiten Teil erfahren Sie, wie Sie Ihr Modell als Echtzeitendpunkt bereitstellen.

> [!div class="nextstepaction"]
> [Mit dem Bereitstellen von Modellen fortfahren](tutorial-designer-automobile-price-deploy.md)
