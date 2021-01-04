---
title: 'ML Studio (Classic): Schnellstart: Erstellen eines Data Science-Experiments – Azure'
description: In dieser Machine Learning-Schnellstartanleitung werden die Schritte eines einfachen Data Science-Experiments beschrieben. Wir sagen den Preis für ein Auto vorher, indem wir einen Regressionsalgorithmus verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 81842fb3a9a68e818a3e8c94aa6db690cb1d94f5
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95486278"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-machine-learning-studio-classic"></a>Schnellstart: Erstellen Ihres ersten Data Science-Experiments in Machine Learning Studio (Classic)

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

In dieser Schnellstartanleitung erstellen Sie ein Machine Learning-Experiment in [Azure Machine Learning Studio (klassisch)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio), mit dem der Preis eines Autos basierend auf verschiedenen Variablen vorhergesagt wird, z. B. Marke und technische Daten.

Falls maschinelles Lernen neu für Sie ist, hilft Ihnen die Videoreihe [Data Science für Anfänger](data-science-for-beginners-the-5-questions-data-science-answers.md) weiter. Hierbei handelt es sich um eine gute Einführung in maschinelles Lernen in alltäglicher Sprache und mit entsprechenden Konzepten.

In dieser Schnellstartanleitung wird der Standardworkflow für ein Experiment verfolgt:

1. **Erstellen des Modells**
    - [Abrufen der Daten]
    - [Aufbereiten der Daten]
    - [Definieren der Funktionen]
1. **Modelltraining**
    - [Auswählen und Anwenden eines Algorithmus]
1. **Bewerten und Testen des Modells**
    - [Erstellen von Preisprognosen für neue Fahrzeuge]

[Abrufen der Daten]: #get-the-data
[Aufbereiten der Daten]: #prepare-the-data
[Definieren der Funktionen]: #define-features
[Auswählen und Anwenden eines Algorithmus]: #choose-and-apply-an-algorithm
[Erstellen von Preisprognosen für neue Fahrzeuge]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Abrufen von Daten

Grundvoraussetzung für maschinelles Lernen sind Daten.
Studio (klassisch) enthält bereits einige Beispieldatasets, die Sie verwenden können. Alternativ hierzu können Sie auch Daten aus vielen anderen Quellen importieren. Für dieses Beispiel verwenden wir das Beispieldataset **Automobile price data (Raw)** (Automobilpreisdaten (roh)), das in Ihrem Arbeitsbereich zu finden ist.
Dieses Dataset enthält Einträge für eine Reihe verschiedener Automobile, einschließlich Informationen wie Marke, Modell, technische Angaben und Preis.

> [!TIP]
> Eine Arbeitskopie des folgenden Experiments finden Sie im [Azure KI-Katalog](https://gallery.azure.ai). Wechseln Sie zu **[Ihr erstes Data Science-Experiment – Vorhersage von Automobilpreisen](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** , und klicken Sie auf **In Studio öffnen**, um eine Kopie des Experiments in Ihren Machine Learning Studio-Arbeitsbereich (klassisch) herunterzuladen.

Hier wird erklärt, wie Sie das Dataset in Ihr Experiment importieren.

1. Erstellen Sie ein neues Experiment, indem Sie unten im Machine Learning Studio-Fenster (klassisch) auf **+ NEU** klicken. Wählen Sie **EXPERIMENT** >  **Blank Experiment** (Leeres Experiment).

1. Dieses Experiment erhält einen Standardnamen, der oben im Zeichenbereich angezeigt wird. Wählen Sie den Text aus, und benennen Sie das Experiment mit einem aussagekräftigen Namen um, z.B. **Automobile price prediction** (Vorhersage für Automobilpreise). Der Name muss nicht eindeutig sein.

    ![Umbenennen des Experiments](./media/create-experiment/rename-experiment.png)

1. Links vom Experimentbereich finden Sie eine Palette mit Datensätzen und Modulen. Geben Sie im oberen Bereich dieser Palette **automobile** in das Suchfeld ein, um das Dataset mit dem Namen **Automobile price data (Raw)** zu finden. Ziehen Sie das Dataset in den Experimentbereich.

    ![Suchen Sie nach dem Automobil-Dataset, und ziehen Sie es in den Experimentbereich.](./media/create-experiment/type-automobile.png)

Sie können auf den Ausgabeport im unteren Bereich des Automobil-Datasets klicken und dann **Visualisieren** wählen, um die enthaltenen Daten anzuzeigen.

![Klicken auf den Ausgabeport und Auswählen von „Visualisieren“](./media/create-experiment/select-visualize.png)

> [!TIP]
> Datasets und Module verfügen über Ein- und Ausgabe-Anschlüsse, die in Form kleiner Kreise dargestellt werden: Eingabe-Anschlüsse oben und Ausgabe-Anschlüsse unten.
Zum Erstellen eines Datenflusses über Ihr Experiment verbinden Sie einen Ausgabeport eines Moduls mit dem Eingabeport eines anderen Moduls.
Sie können zu einem beliebigen Zeitpunkt auf den Ausgabe-Anschluss eines Datasets oder eines Moduls klicken, um die Daten an diesem Punkt im Datenfluss anzuzeigen.

In diesem Dataset steht jede Zeile für ein Fahrzeug, und die Variablen, die den einzelnen Fahrzeugen zugeordnet sind, werden als Spalten angezeigt. Wir sagen den Preis in der äußersten rechten Spalte vorher (Spalte 26 mit dem Titel „Price“ (Preis)), indem wir die Variablen für ein bestimmtes Fahrzeug verwenden.

![Anzeigen der Fahrzeugdaten im Fenster für die Datenvisualisierung](./media/create-experiment/visualize-auto-data.png)

Schließen Sie das Visualisierungsfenster, indem Sie auf das "**X**" in der oberen rechten Ecke klicken.

## <a name="prepare-the-data"></a>Vorbereiten der Daten

DataSets müssen vor der Analyse normalerweise vorverarbeitet werden. Möglicherweise sind Ihnen bereits die fehlenden Werte in den Spalten verschiedener Zeilen aufgefallen. Damit das Modell die Daten richtig analysieren kann, müssen diese fehlenden Werte bereinigt werden. Wir entfernen alle Zeilen, in denen Werte fehlen. Außerdem enthält die Spalte **normalisierte Verluste** viele fehlende Werte, daher schließen wir diese Spalte komplett aus dem Modell aus.

> [!TIP]
> Die Bereinigung fehlender Werte aus den Eingabedaten ist eine Voraussetzung für die Verwendung der meisten Module.

Zuerst fügen wir ein Modul hinzu, mit dem die Spalte **normalized-losses** vollständig entfernt wird. Anschließend fügen wir ein weiteres Modul hinzu, mit dem alle Zeilen entfernt werden, in denen Daten fehlen.

1. Geben Sie im oberen Bereich der Modulpalette **select columns** (Spalten auswählen) in das Suchfeld ein, um nach dem Modul [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen) zu suchen. Ziehen Sie es dann in den Experimentbereich. Mit diesem Modul können wir auswählen, welche Daten wir in unserem Modell ein- bzw. ausschließen möchten.

1. Verbinden Sie den Ausgabeport des Datasets **Automobile price data (Raw)** (Automobilpreisdaten (roh)) mit dem Eingabeport von „Select Columns in Dataset“ (Spalten in Dataset auswählen).

    ![Hinzufügen des Moduls „Select Columns in Dataset“ zum Experimentbereich und Herstellen einer Verbindung](./media/create-experiment/type-select-columns.png)

1. Klicken Sie auf das Modul [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen) und dann im Bereich **Eigenschaften** auf **Spaltenauswahl starten**.

   - Klicken Sie auf der linken Seite auf **With rules**
   - Klicken Sie unter **Begin With** (Beginnen mit) auf **All columns** (Alle Spalten). Mit diesen Regeln wird [Select Columns in Dataset][select-columns] angewiesen, alle Spalten zu durchlaufen (mit Ausnahme derer, die wir jetzt ausschließen werden).
   - Wählen Sie in den Dropdownlisten die Optionen **Ausschließen** und **Spaltennamen** aus, und klicken Sie auf das Textfeld. Eine Liste von Spalten wird angezeigt. Wählen Sie **normalized-losses** aus. Daraufhin wird die Spalte dem Textfeld hinzugefügt.
   - Klicken Sie auf die Schaltfläche mit dem Häkchen („OK“), um die Spaltenauswahl zu schließen (unten rechts).

     ![Starten der Spaltenauswahl und Ausschließen der Spalte „normalized-losses“](./media/create-experiment/launch-column-selector.png)

     Der Eigenschaftenbereich für **Select Columns in Dataset** zeigt nun an, dass mit Ausnahme von **normalized-losses** alle Spalten des Datasets übergeben werden.

     ![Eigenschaftenbereich: Anzeige, dass die Spalte „normalized-losses“ ausgeschlossen wurde](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. Doppelklicken Sie in diesem Fall auf das Modul [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen), und geben Sie den Kommentar „Exclude normalized losses“ (Normalisierte Verluste ausschließen) ein.

     ![Doppelklicken auf ein Modul, um einen Kommentar hinzuzufügen](./media/create-experiment/add-comment.png)

1. Ziehen Sie das Modul [Clean Missing Data][clean-missing-data] in den Experimentbereich, und verbinden Sie es mit dem Modul [Select Columns in Dataset][select-columns]. Klicken Sie im **Eigenschaftenbereich** unter **Cleaning mode** (Reinigungsmodus) auf die Option **Remove entire row** (Gesamte Zeile entfernen). Mit diesen Optionen wird [Clean Missing Data][clean-missing-data] (Fehlende Daten bereinigen) angewiesen, Daten durch das Entfernen von Zeilen mit fehlenden Werten zu bereinigen. Doppelklicken Sie auf das Modul, und geben Sie den Kommentar "Remove missing value rows" ein.

    ![Festlegen des Reinigungsmodus für das Modul „Clean Missing Data“ auf „Remove entire row“](./media/create-experiment/set-remove-entire-row.png)

1. Führen Sie das Experiment, indem Sie am unteren Rand der Seite auf **AUSFÜHREN** klicken.

    Nach Abschluss des Experiments sind alle Module mit einem grünen Häkchen markiert, um anzuzeigen, dass diese erfolgreich abgeschlossen wurden. Beachten Sie auch den Status **Finished running** in der oberen rechten Ecke.

    ![Nach der Ausführung sollte das Experiment in etwa wie folgt aussehen:](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Warum haben wir das Experiment nun ausgeführt? Durch Ausführen des Experiments übergeben die Spaltendefinitionen für unsere Daten aus dem Dataset die Module [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen) und [Clean Missing Data][clean-missing-data] (Fehlende Daten bereinigen). Dies bedeutet, dass alle Module, die wir mit [Clean Missing Data][clean-missing-data] (Fehlende Daten bereinigen) verbinden, über dieselben Informationen verfügen.

Wir verfügen jetzt über bereinigte Daten. Wenn Sie das bereinigte Dataset anzeigen möchten, klicken Sie auf den linken Ausgabeport des Moduls [Clean Missing Data][clean-missing-data] (Fehlende Daten bereinigen), und wählen Sie **Visualize** (Visualisieren) aus. Beachten Sie, dass die Spalte **normalized-losses** nicht mehr aufgeführt wird und keine fehlenden Werte auftreten.

Nach der Bereinigung der Daten können wir nun angeben, welche Funktionen wir im Vorhersagemodell verwenden möchten.

## <a name="define-features"></a>Definieren der Funktionen

Bei Machine Learning sind *Funktionen* einzeln messbare Eigenschaften des untersuchten Gesamtobjekts. In unserem DataSet stellt jede Zeile ein Automobil dar, und jede Spalte ist eine Funktion dieses Automobils.

Einen guten Satz von Funktionen für die Erstellung eines Vorhersagemodells finden Sie durch Ausprobieren und Kenntnisse des zu lösenden Problems. Manche Funktionen eignen sich besser für die Vorhersage des Ziels als andere. Einige Funktionen weisen eine starke Korrelation mit anderen Funktionen auf und können entfernt werden. So stehen „city-mpg“ und „highway-mpg“ z.B. in enger Beziehung. Wir können also eines behalten und das andere ohne bedeutende Auswirkungen auf die Vorhersage entfernen.

Wir werden ein Modell erstellen, das eine Teilmenge der Funktionen in unserem Datensatz verwendet. Sie können später jederzeit andere Funktionen auswählen, das Experiment erneut ausführen und versuchen, bessere Ergebnisse zu erhalten. Hier probieren wir vorerst die folgenden Funktionen aus:

> make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Ziehen Sie ein weiteres Modul vom Typ [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen) in den Experimentbereich. Verbinden Sie den linken Ausgabeport des Moduls [Clean Missing Data][clean-missing-data] (Fehlende Daten bereinigen) mit dem Eingabeport des Moduls [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen).

    ![Verbinden des Moduls „Select Columns in Dataset“ mit dem Modul „Clean Missing Data“](./media/create-experiment/connect-clean-to-select.png)

1. Doppelklicken Sie auf das Modul, und geben Sie "Select features for prediction" ein.

1. Klicken Sie im Bereich **Eigenschaften** auf **Spaltenauswahl starten**.

1. Klicken Sie auf **With rules**(Mit Regeln).

1. Klicken Sie unter **Begin With** (Beginnen mit) auf **No columns** (Alle Spalten). Wählen Sie in der Filterzeile **Include** (Einbeziehen) und **column names** (Spaltennamen) aus, und wählen Sie im Textfeld unsere Liste mit Spaltennamen aus. Mit diesem Filter wird das Modul angewiesen, keine Spalten (Funktionen) außer den angegebenen zu übergeben.

1. Klicken Sie auf das Häkchen ("OK").

    ![Auswählen der Spalten (Funktionen), die in die Vorhersage einbezogen werden sollen](./media/create-experiment/select-columns-to-include.png)

Mit diesem Modul wird ein gefiltertes Dataset erstellt, das nur diejenigen Funktionen enthält, die wir an den im nächsten Schritt verwendeten Lernalgorithmus übergeben möchten. Sie können den Vorgang später jederzeit mit einer anderen Auswahl an Funktionen wiederholen.

## <a name="choose-and-apply-an-algorithm"></a>Auswählen und Anwenden eines Algorithmus

Nachdem die Daten vorbereitet sind, können Sie das Vorhersagemodell anhand von Training und Tests erarbeiten. Wir werden das Modell zunächst mit unseren Daten trainieren und dann testen, wie genau seine Preisvorhersagen zutreffen.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Klassifizierung* und *Regression* sind zwei Algorithmen für beaufsichtigtes maschinelles Lernen. Mit der Klassifizierung wird eine Antwort aus einem definierten Satz mit Kategorien vorhergesagt, z.B. eine Farbe (Rot, Blau oder Grün). Die Regression wird verwendet, um eine Zahl vorherzusagen.

Da wir einen Preis vorhersagen möchten (also eine Zahl), verwenden wir einen Regressionsalgorithmus. In diesem Beispiel verwenden wir ein *lineares Regressionsmodell*.


Wir trainieren das Modell, indem wir einen Datensatz mit dem Preis einspeisen. Das Modell überprüft die Daten und sucht nach Korrelationen zwischen den Funktionen eines Automobils und seinem Preis. Anschließend testen wir das Modell: Wir geben einen Satz von Funktionen für bekannte Automobile ein und testen, wie exakt das Modell den bekannten Preis vorhersagt.

Wir verwenden unsere Daten sowohl für das Trainieren als auch das Testen des Modells, indem die Daten in separate Trainings- und Testdatasets aufgeteilt werden.

1. Ziehen Sie das Modul [Split Data][split] (Daten aufteilen) in den Experimentbereich, und verbinden Sie es mit dem letzten Modul vom Typ [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen).

1. Klicken Sie auf das Modul [Split Data][split] (Daten aufteilen), um es auszuwählen. Suchen Sie unter **Fraction of rows in the first output dataset** (Bruchteil der Zeilen im ersten Ausgabedataset) (im **Eigenschaftenbereich** rechts neben dem Zeichenbereich), und legen Sie die Einstellung auf 0,75 fest. Mit dieser Einstellung verwenden wir 75 Prozent der Daten zum Trainieren des Modells und halten 25 Prozent für Tests zurück.

    ![Festlegen des Aufteilungsanteils des Moduls „Split Data“ auf 0,75](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > Sie können den Parameter **Zufälliger Ausgangswert** ändern, um unterschiedliche zufällige Proben für Training und Tests zu erstellen. Dieser Parameter steuert den Ausgangswert des Pseudo-Zufallszahlengenerators.

1. Führen Sie das Experiment aus. Wenn das Experiment ausgeführt wird, können die Module [Select Columns in Dataset][select-columns] (Spalten im Dataset auswählen) und [Split Data][split] (Daten aufteilen) die Spaltendefinitionen an die Module übergeben, die wir als Nächstes hinzufügen.  

1. Um den Lernalgorithmus auszuwählen, erweitern Sie die Kategorie **Machine Learning** in der Modulpalette links vom Experimentbereich, und erweitern Sie anschließend **Modell initialisieren**. Daraufhin werden verschiedene Kategorien von Modulen angezeigt, die zur Initialisierung eines Algorithmus für maschinelles Lernen verwendet werden können. Wählen Sie für dieses Experiment in der Kategorie **Regression** das Modul [Linear Regression][linear-regression] (Lineare Regression) aus, und ziehen Sie es in den Experimentbereich. (Sie können das Modul auch suchen, indem Sie „linear regression“ in das Suchfeld der Palette eingeben.)

1. Suchen Sie nach dem Modul [Train Model][train-model] (Modell trainieren), und ziehen Sie es in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls [Linear Regression][linear-regression] (Lineare Regression) mit der linken Eingabe des Moduls [Train Model][train-model] (Modell trainieren), und verbinden Sie die Ausgabe der Trainingsdaten (den linken Port) des Moduls [Split Data][split] (Daten aufteilen) mit der rechten Eingabe des Moduls [Train Model][train-model] (Modell trainieren).

    ![Verbinden des Moduls „Train Model“mit den Modulen „Linear Regression“ und „Split Data“](./media/create-experiment/connect-train-model.png)

1. Klicken Sie auf das Modul [Train Model][train-model] (Modell trainieren), klicken Sie im Bereich **Eigenschaften** auf **Spaltenauswahl starten**, und wählen Sie dann die Spalte **price** aus. **price** ist der Wert, den unser Modell vorhersagen wird.

    Wählen Sie die Spalte **Price** in der Spaltenauswahl aus, indem Sie sie aus der Liste **Available columns** (Verfügbare Spalten) in die Liste **Selected columns** (Ausgewählte Spalten) verschieben.

    ![Auswählen der Spalte „price“ für das Modul „Train Model“](./media/create-experiment/select-price-column.png)

1. Führen Sie das Experiment aus.

Als Ergebnis erhalten Sie ein trainiertes Regressionsmodell, mit dem Sie neue Automobildaten bewerten können, um Preisvorhersagen zu treffen.

![Nach der Ausführung sollte das Experiment in etwa wie folgt aussehen:](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Erstellen von Preisprognosen für neue Fahrzeuge

Wir haben das Modell nun unter Verwendung von 75 Prozent unserer Daten trainiert und können die restlichen 25 Prozent der Daten dafür aufwenden, zu bewerten, wie gut unser Modell funktioniert.

1. Suchen Sie nach dem Modul [Score Model][score-model] (Modell bewerten), und ziehen Sie es in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls [Train Model][train-model] mit dem linken Eingabeport des Moduls [Score Model][score-model]. Verbinden Sie die Testdatenausgabe (den rechten Port) des Moduls [Split Data][split] mit dem rechten Eingabeport des Moduls [Score Model][score-model].

    ![Verbinden des Moduls „Score Model“mit den Modulen „Train Model“ und „Split Data“](./media/create-experiment/connect-score-model.png)

1. Führen Sie das Experiment aus, und zeigen Sie die Ausgabe des Moduls [Score Model][score-model] (Modell bewerten) an. Klicken Sie hierzu auf den Ausgabeport des Moduls [Score Model][score-model] (Modell bewerten), und wählen Sie **Visualize** (Visualisieren) aus. Die Ausgabe zeigt die vorhergesagten Preiswerte zusammen mit den bekannten Werten aus den Testdaten an.  

    ![Ausgabe des Moduls „Score Model“](./media/create-experiment/score-model-output.png)

1. Schließlich testen wir die Qualität der Ergebnisse. Wählen Sie das Modul [Evaluate Model][evaluate-model] (Modell auswerten) aus, und ziehen Sie es in den Experimentbereich. Verbinden Sie die Ausgabe des Moduls [Score Model][score-model] (Modell bewerten) mit der linken Eingabe des Moduls [Evaluate Model][evaluate-model] (Modell auswerten). Das endgültige Experiment sollte in etwa wie folgt aussehen:

    ![Endgültiges Experiment](./media/create-experiment/complete-linear-regression-experiment.png)

1. Führen Sie das Experiment aus.

Um die Ausgabe des Moduls [Evaluate Model][evaluate-model] (Modell auswerten) anzuzeigen, klicken Sie auf den Ausgabeport und wählen dann **Visualize** (Visualisieren) aus.

![Auswertungsergebnisse für das Experiment](./media/create-experiment/evaluation-results.png)

Die folgenden Statistiken werden für unser Modell angezeigt:

- **Mean Absolute Error** (MAE): Der Mittelwert der absoluten Fehler. (Ein *Fehler* ist die Differenz zwischen dem prognostizierten und dem tatsächlichen Wert.)
- **Root Mean Squared Error** (RMSE): Die Quadratwurzel des Durchschnitts des Quadrats der Prognosefehler für das Testdataset.
- **Relative Absolute Error**: Der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
- **Relative Squared Error**: Der Durchschnitt der quadrierten Fehler relativ zur quadrierten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
- **Coefficient of Determination**: Dieser auch als **R-Quadrat** bezeichnete Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.

Für jede Fehlerstatistik sind kleinere Werte besser. Ein kleinerer Wert gibt an, dass die Vorhersagen genauer mit den tatsächlichen Werten übereinstimmen. Für den **Bestimmungskoeffizienten** gilt: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie anhand eines Beispieldatasets ein einfaches Experiment erstellt. Fahren Sie mit dem Tutorial zur prädiktiven Lösung fort, um sich eingehender mit dem Prozess zum Erstellen und Bereitstellen eines Modells zu beschäftigen.

> [!div class="nextstepaction"]
> [Tutorial: Entwickeln einer prädiktiven Lösung in Studio (klassisch)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model