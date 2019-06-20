---
title: 'Schnellstart: Aufbereiten und Visualisieren von Daten, ohne Code zu schreiben'
titleSuffix: Azure Machine Learning service
description: Erstellen Sie ein Machine Learning-Experiment, um Ihre Daten mit einer Drag & Drop-Benutzeroberfläche aufzubereiten und zu visualisieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 07b86138a95853673b5d54e272b40af41d58f418
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475946"
---
# <a name="quickstart-prepare-and-visualize-data-without-writing-code-in-azure-machine-learning"></a>Schnellstart: Vorbereiten und Visualisieren von Daten in Azure Machine Learning ohne Schreiben von Code

Bereiten Sie Ihre Daten auf der Visuellen Drag-&-Drop-Oberfläche in (Vorschauversion) für Azure Machine Learning vor, und visualisieren Sie sie. Die Daten, die Sie verwenden, enthalten Einträge für eine Reihe verschiedener Automobile, einschließlich Informationen wie Marke, Modell, technische Angaben und Preis. Nachdem Sie diese Schnellstartanleitung abgeschlossen haben, können Sie mithilfe dieser Daten den Preis eines Autos vorhersagen. 

Bevor Sie ein Machine Learning-Modell trainieren, müssen Sie die Daten verstehen und vorbereiten.  In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

- Erstellen Ihres ersten Experiments zum Hinzufügen von Daten und Anzeigen einer Vorschau
- Aufbereiten der Daten durch das Entfernen von fehlenden Werten
- Ausführen des Experiments
- Visualisieren der sich ergebenden Daten

Falls Machine Learning neu für Sie ist, hilft Ihnen die Videoreihe [Data Science für Anfänger](https://docs.microsoft.com/azure/machine-learning/studio/data-science-for-beginners-the-5-questions-data-science-answers) weiter. Hierbei handelt es sich um eine gute Einführung in Machine Learning.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version des Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

### <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Wenn Sie über einen Azure Machine Learning Service-Arbeitsbereich verfügen, fahren Sie mit dem [nächsten Abschnitt](#start) fort. Andernfalls erstellen Sie jetzt einen Arbeitsbereich.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="start"></a> Öffnen der Webseite der grafischen Benutzeroberfläche

1. Öffnen Sie Ihren Arbeitsbereich im [Azure-Portal](https://portal.azure.com/).  

1. Wählen Sie in Ihrem Arbeitsbereich die Option **Grafische Benutzeroberfläche** aus.  Wählen Sie dann **Grafische Benutzeroberfläche starten** aus.  
 
    ![Starten der grafische Benutzeroberfläche](./media/ui-quickstart-run-experiment/launch-ui.png)

    Die Webseite der Benutzeroberfläche wird auf einer neuen Browserseite geöffnet.  

## <a name="create-your-first-experiment"></a>Erstellen Ihres ersten Experiments

Die grafische Benutzeroberfläche stellt Ihnen einen interaktiven, visuellen Arbeitsbereich zur Verfügung, in dem Sie ein Vorhersageanalysemodell ganz einfach erstellen, testen und durchlaufen können. Sie fügen mithilfe von Drag & Drop Datasets und Analysemodule in einen interaktiven Experimentbereich ein und verbinden sie zu einem _Experiment_.  Erstellen Sie jetzt Ihr erstes Experiment.

1. Wählen Sie in der unteren linken Ecke **Neu hinzufügen** aus.
![Hinzufügen eines neuen Experiments](./media/ui-quickstart-run-experiment/add-new.png)

1. Wählen Sie **Leeres Experiment** aus.

1. Für das Experiment wird ein Standardname vergeben. Wählen Sie diesen Text aus, und benennen Sie das Experiment in „Quickstart-explore data“ um. Der Name muss nicht eindeutig sein.

1. Die **Miniaturkarte** am unteren Rand des Bildschirms eignet sich für die Anzeige von großen Experimenten.  Sie benötigen sie in diesem Schnellstart nicht, also klicken Sie oben auf den Pfeil, um sie zu minimieren.  

    ![Umbenennen von Experimenten](./media/ui-quickstart-run-experiment/rename.png)

## <a name="add-data"></a>Hinzufügen von Daten

Grundvoraussetzung für Machine Learning sind Daten. Diese Benutzeroberfläche enthält bereits einige Beispieldatasets, die Sie verwenden können. Alternativ hierzu können Sie auch Daten aus vielen anderen Quellen importieren. Für dieses Beispiel verwenden wir das Beispieldataset **Automobile Price Data (Raw)** (Automobilpreisdaten (Rohdaten)). 

1. Links vom Experimentbereich finden Sie eine Palette mit Datensätzen und Modulen. Wählen Sie **Saved Datasets** (Gespeicherte Datasets) und dann **Samples** (Beispiele) aus, um die verfügbaren Beispieldatasets anzuzeigen.

1. Wählen Sie das Dataset **Automobile Price Data (raw)** aus, und ziehen Sie es in den Experimentbereich.

   ![Ziehen von Daten in den Experimentbereich](./media/ui-quickstart-run-experiment/drag-data.png)


## <a name="select-columns"></a>Spalten auswählen

Wählen Sie die Datenspalten aus, mit denen Sie arbeiten möchten.  Konfigurieren Sie das Modul zunächst so, dass alle verfügbaren Spalten angezeigt werden.

> [!TIP]
> Wenn Sie den Namen der gewünschten Daten oder des Moduls kennen, verwenden Sie die Suchleiste oben in der Palette, um sie schnell zu finden.  Der Rest des Schnellstarts verwendet diese Verknüpfung.

1. Geben Sie im Suchfeld **Select** (Auswählen) ein, um nach dem Modul **Select Columns in Dataset** (Spalten im Dataset auswählen) zu suchen.

1. Klicken Sie auf **Select Columns in Dataset**, und ziehen Sie das Modul in den Experimentbereich. Legen Sie das Modul unter dem Dataset ab, das Sie zuvor hinzugefügt haben.

1. Verbinden Sie das Dataset mit dem Modul **Select Columns in Dataset**: Klicken Sie auf den Ausgabeport des Datasets, ziehen Sie ihn auf den Eingabeport des Moduls **Select Columns in Dataset**, und lassen Sie die Maustaste dann los. Das Dataset und das Modul bleiben verbunden, auch wenn Sie diese im Bereich verschieben.

    > [!TIP]
    > Datasets und Module verfügen über Ein- und Ausgabe-Anschlüsse, die in Form kleiner Kreise dargestellt werden: Eingabe-Anschlüsse oben und Ausgabe-Anschlüsse unten. Zum Erstellen eines Datenflusses über Ihr Experiment verbinden Sie einen Ausgabeport eines Moduls mit dem Eingabeport eines anderen Moduls.
    >
    > Wenn Sie Probleme beim Verbinden von Modulen haben, versuchen Sie sie vollständig auf den Knoten zu ziehen, den Sie verbinden.

    ![Verbinden von Modulen](./media/ui-quickstart-run-experiment/connect-modules.gif) 

    Das rote Ausrufezeichen gibt an, dass Sie die Eigenschaften für das Modul noch nicht festgelegt haben. Diesen Schritt führen Sie als Nächstes durch.
   
1. Wählen Sie das Modul **Select Columns in Dataset** aus.

1. Wählen Sie im Bereich **Eigenschaften** rechts neben der Canvas **Spalten bearbeiten** aus.

    Wählen Sie im Dialogfeld **Select columns** (Spalten auswählen) die Option **ALL COLUMNS** (Alle Spalten) aus, und schließen Sie **alle Features** ein. Das Dialogfeld sollte wie folgt aussehen:

     ![Spaltenauswahl](./media/ui-quickstart-run-experiment/select-all.png)

1. Wählen Sie unten rechts **OK** aus, um die Spaltenauswahl zu schließen.

## <a name="run-the-experiment"></a>Ausführen des Experiments

Sie können zu einem beliebigen Zeitpunkt auf den Ausgabeport eines Datasets oder eines Moduls klicken, um die Daten an diesem Punkt im Datenfluss anzuzeigen.  Wenn die Option **Visualize** (Visualisieren) deaktiviert ist, müssen Sie das Experiment zuerst ausführen.  Diesen Schritt führen Sie als Nächstes durch.

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

Nachdem das Computeziel verfügbar ist, wird das Experiment ausgeführt. Wenn die Ausführung abgeschlossen ist, wird ein grünes Häkchen für jedes Modul angezeigt.

![Anzeigen des Status](./media/ui-quickstart-run-experiment/status1.png)

## <a name="preview-the-data"></a>Vorschau der Daten

Nachdem Sie nun Ihr erstes Experiment durchgeführt haben, können Sie die Daten visualisieren, um mehr über die Informationen zu erfahren, mit denen Sie arbeiten müssen.

1. Wählen Sie den Ausgabeport am unteren Rand des Moduls **Select Columns in Dataset** aus, und wählen Sie dann **Visualize** (Visualisieren) aus.

1. Klicken Sie im Datenfenster auf verschiedene Spalten, um Informationen zu dieser Spalte anzuzeigen.  

    In diesem Dataset steht jede Zeile für ein Fahrzeug, und die Variablen, die den einzelnen Fahrzeugen zugeordnet sind, werden als Spalten angezeigt.    Es gibt 205 Zeilen und 26 Spalten in diesem Dataset.

     Jedes Mal, wenn Sie auf eine Datenspalte klicken, werden links die Informationen **Statistics** (Statistik) und das Bild **Visualization** (Visualisierung) dieser Spalte angezeigt.  Wenn Sie beispielsweise auf **num-of-doors** klicken, sehen Sie, dass diese Spalte 2 eindeutige Werte und 2 fehlende Werte aufweist.  Scrollen Sie nach unten, um die Werte anzuzeigen: zwei und vier Türen.

     ![Vorschau der Daten](./media/ui-quickstart-run-experiment/preview-data.gif)

1. Klicken Sie auf jede Spalte, um weitere Informationen zum Dataset zu erhalten, und überlegen Sie, ob die Spalten hilfreich sind, um den Preis eines Autos vorherzusagen.

## <a name="prepare-data"></a>Vorbereiten von Daten

DataSets müssen vor der Analyse normalerweise vorverarbeitet werden. Möglicherweise sind Ihnen bereits die fehlenden Werte in den Spalten verschiedener Zeilen aufgefallen. Damit das Modell die Daten richtig analysieren kann, müssen diese fehlenden Werte bereinigt werden. Sie entfernen alle Zeilen, in denen Werte fehlen. Außerdem enthält die Spalte **normalized-losses** viele fehlende Werte, daher schließen wir diese Spalte ganz aus dem Modell aus.

> [!TIP]
> Die Bereinigung fehlender Werte aus den Eingabedaten ist eine Voraussetzung für die Verwendung der meisten Module.  

### <a name="remove-column"></a>Entfernen der Spalte

Entfernen Sie zunächst die Spalte **normalized-losses** vollständig.

1. Wählen Sie das Modul **Select Columns in Dataset** aus.

1. Wählen Sie im Bereich **Eigenschaften** rechts neben der Canvas **Spalten bearbeiten** aus.

    * Lassen Sie **With rules** (Mit Regeln) und **ALL COLUMNS** (Alle Spalten) ausgewählt.

    * Wählen Sie in den Dropdownlisten die Optionen **Ausschließen** und **Spaltennamen** aus, und klicken Sie auf das Textfeld. Geben Sie **normalized-losses** ein.

    * Wählen Sie unten rechts **OK** aus, um die Spaltenauswahl zu schließen.

    ![Ausschließen einer Spalte](./media/ui-quickstart-run-experiment/exclude-column.png)
        
    Der Eigenschaftenbereich für „Select Columns in Dataset“ zeigt nun an, dass mit Ausnahme von **normalized-losses** alle Spalten des Datasets übergeben werden.
        
    Der Eigenschaftenbereich zeigt, dass die Spalte **normalized-losses** ausgeschlossen wurde.
        
    ![Eigenschaftenbereich](./media/ui-quickstart-run-experiment/property-pane.png)
        
    Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. 

1. Doppelklicken Sie auf das Modul **Select Columns in Dataset**, und geben Sie den Kommentar „Exclude normalized losses“ (Normalisierte Verluste ausschließen) ein. 
    
    Nachdem Sie den Kommentar eingeben haben, klicken Sie außerhalb des Moduls.  Ein Pfeil nach unten wird angezeigt, um anzugeben, dass das Modul einen Kommentar enthält.

1. Klicken Sie auf den Pfeil nach unten, um den Kommentar anzuzeigen.

    Das Modul zeigt jetzt einen Pfeil nach oben an, mit dem der Kommentar ausgeblendet werden kann.
        
    ![Kommentare](./media/ui-quickstart-run-experiment/comments.png)

### <a name="clean-missing-data"></a>Fehlende Daten bereinigen

Wenn Sie ein Modell trainieren, müssen Sie etwas bezüglich der fehlenden Daten unternehmen.  In diesem Fall fügen Sie ein Modul hinzu, um alle restlichen Zeilen zu entfernen, in denen Daten fehlen.  

1. Geben Sie im Suchfeld das Wort **Clean** ein, um nach dem Modul **Clean Missing Data** (Fehlende Daten bereinigen) zu suchen.

1. Ziehen Sie das Modul **Clean Missing Data** in den Experimentbereich, und verbinden Sie es mit dem Modul **Select Columns in Dataset**. 

1. Klicken Sie im Eigenschaftenbereich unter **Cleaning mode** (Bereinigungsmodus) auf die Option **Remove entire row** (Gesamte Zeile entfernen).

    Mit diesen Optionen wird **Clean Missing Data** (Fehlende Daten bereinigen) angewiesen, Daten durch das Entfernen von Zeilen mit fehlenden Werten zu bereinigen.

1. Doppelklicken Sie auf das Modul, und geben Sie den Kommentar "Remove missing value rows" ein.
 
    ![Entfernen von Zeilen](./media/ui-quickstart-run-experiment/remove-rows.png)

    Ihr Experiment sollte in etwa wie folgt aussehen:
    
    ![select-column](./media/ui-quickstart-run-experiment/experiment-clean.png)

## <a name="visualize-the-results"></a>Visualisieren der Ergebnisse

Da Sie Änderungen an den Modulen in Ihrem Experiment vorgenommen haben, hat sich der Status in „In draft“ (Entwurf) geändert.  Um die neuen bereinigten Daten zu visualisieren, müssen Sie zuerst das Experiment erneut ausführen.

1. Wählen Sie unten **Run** (Ausführen) aus, um das Experiment auszuführen.

    Dieses Mal können Sie das Computeziel wiederverwenden, das Sie zuvor erstellt haben.  

1. Wählen Sie **Run**  im Dialogfeld aus.

   ![Ausführen des Experiments](./media/ui-quickstart-run-experiment/select-compute.png)

1. Wenn die Ausführung abgeschlossen ist, klicken Sie mit der rechten Maustaste auf das Modul **Clean Missing Data**, um die neuen bereinigten Daten zu visualisieren.  

    ![Visualisieren bereinigter Daten](./media/ui-quickstart-run-experiment/visualize-cleaned.png)

1. Klicken Sie auf die verschiedenen Spalten im Fenster mit den bereinigten Daten, um sich anzusehen, wie sich die Daten geändert haben.  

    ![Visualisieren bereinigter Daten](media/ui-quickstart-run-experiment/visualize-result.png)

    Es gibt jetzt 193 Zeilen und 25 Spalten.

    Wenn Sie auf **num-of-doors** klicken, sehen Sie, dass diese Spalte noch immer 2 eindeutige Werte aufweist, aber 0 fehlende Werte. Klicken Sie auf die restlichen Spalten, um zu überprüfen, ob das Dataset noch fehlende Daten aufweist. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

- Erstellen Ihres ersten Experiments zum Hinzufügen von Daten und Anzeigen einer Vorschau
- Aufbereiten der Daten durch das Entfernen von fehlenden Werten
- Visualisieren der vorbereiteten Daten

Fahren Sie mit dem folgenden Tutorial fort, um diese Daten zu verwenden, um den Preis eines Autos vorherzusagen.

> [!div class="nextstepaction"]
> [Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md)
