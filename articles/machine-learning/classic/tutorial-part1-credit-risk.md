---
title: 'ML Studio (Classic) – Tutorial: Vorhersagen des Kreditrisikos – Azure'
description: Ein ausführliches Tutorial zum Erstellen einer Predictive Analytics-Lösung für die Kreditrisikobewertung in Azure Machine Learning Studio (klassisch) Dieses Tutorial ist der erste Teil einer dreiteiligen Reihe.  Es wird beschrieben, wie Sie einen Arbeitsbereich erstellen, Daten hochladen und ein Experiment erstellen.
keywords: Kreditrisiko, Predictive Analytics-Lösung, Risikobewertung
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 571925586bf3b70c38260392bae49c5352f10194
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517517"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Tutorial 1: Vorhersagen des Kreditrisikos: Azure Machine Learning Studio (klassisch)

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Dieses Tutorial befasst sich eingehend mit der Entwicklung einer Predictive Analytics-Lösung. Hierzu wird in Machine Learning Studio (klassisch) ein einfaches Modell entwickelt.  Anschließend wird das Modell als Azure Machine Learning-Webdienst bereitgestellt.  Dieses bereitgestellte Modell kann auf der Grundlage neuer Daten Vorhersagen generieren. Dieses Tutorial ist der **erste Teil einer dreiteiligen Reihe**.

Stellen Sie sich vor, Sie müssen das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag vorhersagen.  

Die Bewertung des Kreditrisikos ist allerdings ein komplexes Problem und wurde daher in diesem Tutorial etwas vereinfacht. Diese Aufgabenstellung dient als Beispiel dafür, wie Sie eine Predictive Analytics-Lösung mit Microsoft Azure Machine Learning Studio (klassisch) erstellen können. Für diese Lösung werden Azure Machine Learning Studio (klassisch) und ein Machine Learning-Webdienst verwendet.  

In diesem dreiteiligen Tutorial werden zunächst öffentlich verfügbare Kreditrisikodaten verwendet.  Als Nächstes entwickeln und trainieren Sie ein Vorhersagemodell.  Abschließend stellen Sie das Modell als Webdienst bereit.

In diesem Teil des Tutorials führen Sie die folgenden Schritte aus: 
 
> [!div class="checklist"]
> * Erstellen eines (klassischen) Machine Learning Studio-Arbeitsbereichs
> * Hochladen vorhandener Daten
> * Erstellen eines Experiments

Sie können dieses Experiment dann nutzen, um [in Teil 2 Modelle zu trainieren](tutorial-part2-credit-risk-train.md) und diese dann [in Teil 3 bereitzustellen](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird vorausgesetzt, dass Sie Machine Learning Studio (klassisch) bereits mindestens einmal verwendet haben und über Grundkenntnisse in Bezug auf Machine Learning-Konzepte verfügen. Es wird aber nicht davon ausgegangen, dass Sie Experte in diesen Bereichen sind.

Wenn Sie **Azure Machine Learning Studio (klassisch)** noch nicht verwendet haben, sollten Sie mit der Schnellstartanleitung [Erstellen Ihres ersten Data Science-Experiments in Azure Machine Learning Studio (klassisch)](create-experiment.md) beginnen. In dieser Schnellstartanleitung wird die erstmalige Verwendung von Machine Learning Studio (klassisch) Schritt für Schritt beschrieben. Es vermittelt die Grundlagen, wie Sie Module per Drag & Drop in Ihr Experiment aufnehmen, sie miteinander verbinden, das Experiment ausführen und die Ergebnisse anzeigen.


> [!TIP] 
> Eine Arbeitskopie des Experiments, das Sie in diesem Tutorial entwickeln, finden Sie im [Azure KI-Katalog](https://gallery.azure.ai). Navigieren Sie zu **[Tutorial – Predict credit risk](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** (Tutorial: Vorhersagen des Kreditrisikos), und klicken Sie auf **Open in Studio** (In Studio öffnen), um eine Kopie des Experiments in Ihren Arbeitsbereich von Machine Learning Studio (klassisch) herunterzuladen.
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Erstellen eines (klassischen) Machine Learning Studio-Arbeitsbereichs

Um Machine Learning Studio (klassisch) verwenden zu können, benötigen Sie einen (klassischen) Microsoft Azure Machine Learning Studio-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.  

Informationen zum Erstellen eines Arbeitsbereichs finden Sie unter [Erstellen und Freigeben eines Azure Machine Learning Studio-Arbeitsbereichs (klassisch)](create-workspace.md).

Nachdem Ihr Arbeitsbereich erstellt wurde, öffnen Sie Machine Learning Studio (klassisch) unter [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Wenn Sie über mehrere Arbeitsbereiche verfügen, können Sie den Arbeitsbereich im Fenster oben rechts auf der Symbolleiste auswählen.

![Auswählen eines Arbeitsbereichs in Studio (klassisch)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Wenn Sie der Besitzer des Arbeitsbereichs sind, können Sie die Experimente, an denen Sie arbeiten, mit anderen Personen teilen. Laden Sie diese Personen hierfür in den Arbeitsbereich ein. Dies können Sie in Machine Learning Studio (klassisch) auf der Seite **EINSTELLUNGEN** vornehmen. Sie benötigen nur das Microsoft-Konto oder Organisationskonto des betreffenden Benutzers.
> 
> Klicken Sie auf der Seite **EINSTELLUNGEN** auf **BENUTZER** und dann unten in Fenster auf **INVITE MORE USERS** (WEITERE BENUTZER EINLADEN).
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Hochladen vorhandener Daten

Um ein Vorhersagemodell für Kreditrisiken zu entwickeln, benötigen Sie Daten, die Sie zum Trainieren und anschließenden Testen des Modells verwenden können. In diesem Tutorial verwenden Sie den Datensatz „UCI Statlog (German Credit Data)“ aus dem UC Irvine Machine Learning-Repository. Sie finden den Datensatz unter folgender URL:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Sie verwenden die Datei mit dem Namen **german.data**. Laden Sie die Datei auf Ihre lokale Festplatte herunter.  

Das Dataset **german.data** enthält Zeilen mit 20 Variablen für 1.000 Kreditantragsteller aus der Vergangenheit. Diese 20 Variablen stellen den Featuresatz (*Featurevektor*) des Datasets dar, der Identifikationseigenschaften für die einzelnen Kreditantragsteller enthält. Eine zusätzliche Spalte in jeder Zeile enthält das berechnete Kreditrisiko der Antragsteller. 700 der Antragsteller wurden mit niedrigem Risiko klassifiziert und 300 mit hohem Risiko.

Auf der UCI-Website finden Sie eine Beschreibung der Attribute des Funktionsvektors für diese Daten. Diese Daten umfassen beispielsweise Finanzinformationen, Bonitätsgeschichte, Beschäftigungsstatus und persönliche Daten. Für jeden Antragsteller wurde eine binäre Bewertung vergeben, um zwischen niedrigem und hohem Kreditrisiko zu unterscheiden. 

Sie trainieren ein Vorhersageanalytikmodell anhand dieser Daten. Anschließend sollte Ihr Modell einen Funktionsvektor für neue Personen akzeptieren und vorhersagen können, ob diese ein niedriges oder hohes Kreditrisiko darstellen.  

Hier ein interessantes Extra.

In der Beschreibung des Datasets auf der UCI-Website wird auf die Kosten einer Fehlklassifizierung des Kreditrisikos einer Person hingewiesen.
Wenn mit dem Modell ein hohes Kreditrisiko für eine Person vorhergesagt wird, die eigentlich ein niedriges Kreditrisiko aufweist, liegt eine Fehlklassifizierung des Modells vor.

Die umgekehrte Fehlklassifizierung, d.h. die Vorhersage eines niedrigen Kreditrisikos für eine Person, die eigentlich ein hohes Kreditrisiko aufweist, ist jedoch fünfmal so teuer für die Finanzinstitution.

Ihr Modell sollte daher so trainiert werden, dass die Kosten dieser letzteren Fehlklassifizierung fünfmal höher als die der Fehlklassifizierung in der anderen Richtung ausfallen.

Dies kann beim Trainieren des Modells in Ihrem Experiment auf einfache Weise berücksichtigt werden, indem die entsprechenden Einträge von Personen mit einem hohen Kreditrisiko (fünffach) dupliziert werden. 

Wenn mit dem Modell eine Person dann fälschlicherweise als Person mit einem niedrigen Kreditrisiko klassifiziert wird, wird diese Fehlklassifizierung im Modell fünfmal ausgeführt, d.h. einmal pro Duplikat. Auf diese Weise werden die Kosten für diesen Fehler in den Trainingsergebnissen erhöht.


### <a name="convert-the-dataset-format"></a>Konvertieren des Datensatzformats

Der Originaldatensatz verwendet ein Format mit Trennung durch Leerzeichen. Machine Learning Studio (klassisch) funktioniert besser mit durch Trennzeichen getrennten Dateien (CSV). Daher konvertieren Sie den Datensatz, indem Sie die Leerzeichen durch Kommas ersetzen.  

Es gibt viele Möglichkeiten zum Konvertieren dieser Daten. Eine ist die Verwendung des folgenden Windows PowerShell-Befehls:   

```powershell
cat german.data | %{$_ -replace " ",","} | sc german.csv  
```

Eine andere ist die Verwendung des sed-Befehls unter Unix:  

```console
sed 's/ /,/g' german.data > german.csv
```

In beiden Fällen haben Sie eine durch Kommas getrennte Version der Daten in der Datei **german.csv** erstellt, die Sie in Ihrem Experiment verwenden können.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Hochladen des Datasets in Machine Learning Studio (klassisch)

Nach dem Konvertieren der Daten in das CSV-Format müssen Sie sie in Machine Learning Studio (klassisch) hochladen. 

1. Öffnen Sie die Startseite von Machine Learning Studio (klassisch) unter [https://studio.azureml.net](https://studio.azureml.net). 

2. Klicken Sie links oben im Fenster auf das Menü![Menüsymbol (drei gestapelte Linien)](./media/tutorial-part1-credit-risk/menu.png), klicken Sie auf **Azure Machine Learning**, wählen Sie **Studio** aus, und melden Sie sich an.

3. Klicken Sie im unteren Seitenbereich auf **+NEU** .

4. Wählen Sie **DATASET**.

5. Klicken Sie auf **AUS LOKALER DATEI**.

    ![Hinzufügen eines Datasets aus einer lokalen Datei](./media/tutorial-part1-credit-risk/add-dataset.png)

6. Klicken Sie im Dialogfeld **Neuen Datensatz hochladen** auf „Durchsuchen“, und suchen Sie nach der zuvor erstellten Datei **german.csv**.

7. Geben Sie einen Namen für das Dataset ein. Nennen Sie es in diesem Tutorial „UCI German Credit Card Data“.

8. Wählen Sie den Datentyp **Generic CSV File With no header (.nh.csv)** aus.

9. Fügen Sie bei Bedarf eine Beschreibung hinzu.

10. Klicken Sie auf das Häkchen **OK**.  

    ![Hochladen des DataSets](./media/tutorial-part1-credit-risk/upload-dataset.png)

Daraufhin werden die Daten in ein Datasetmodul hochgeladen, das Sie in einem Experiment verwenden können.

Zum Verwalten von Datasets, die Sie in Studio (klassisch) hochgeladen haben, klicken Sie auf der linken Seite des Fensters von Studio (klassisch) auf die Registerkarte **DATASETS**.

![Verwalten von Datasets](./media/tutorial-part1-credit-risk/dataset-list.png)

Weitere Informationen zum Importieren anderer Datentypen in einem Experiment finden Sie unter [Importieren von Trainingsdaten in Azure Machine Learning Studio (klassisch)](import-data.md).

## <a name="create-an-experiment"></a>Erstellen eines Experiments

Der nächste Schritt in diesem Tutorial ist die Erstellung eines Experiments in Machine Learning Studio (klassisch), in dem das von Ihnen hochgeladene Dataset verwendet wird.  

1. Klicken Sie in Studio (klassisch) unten auf der Seite auf **+NEW**.
1. Wählen Sie **EXPERIMENT** und anschließend "Blank Experiment" aus. 

    ![Erstellen eines neuen Experiments](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Wählen Sie oben in der Canvas den Namen des Standardexperiments aus, und geben Sie einen aussagekräftigen Namen ein.

    ![Umbenennen von Experimenten](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Es ist eine bewährte Methode, die Felder **Summary** und **Description** für das Experiment im Bereich **Properties** auszufüllen. Diese Eigenschaften geben Ihnen die Möglichkeit, das Experiment zu dokumentieren. Wenn Benutzer später darauf zugreifen, können sie Ihre Ziele und die Methodik verstehen.
   > 
   > ![Eigenschaften von Experimenten](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Erweitern Sie in der Modulpalette links vom Experimentbereich **Gespeicherte Datasets**.
1. Suchen Sie das Dataset, das Sie unter **Meine Datasets** erstellt haben, und ziehen Sie es in den Bereich. Sie können auch nach dem Dataset suchen, indem Sie den Namen in das Feld **Suchen** oberhalb der Palette eingeben.  

    ![Hinzufügen des Datasets zum Experiment](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Vorbereiten der Daten

Sie können die ersten 100 Datenzeilen sowie einige statistische Informationen für das ganze Dataset anzeigen: Klicken Sie hierzu auf den Ausgabeport des Datasets (den kleinen Kreis unten), und wählen Sie die Option **Visualize**.  

Da die Datendatei keine Spaltenüberschriften aufweist, hat Studio (klassisch) allgemeine Überschriften (Col1, Col2 *usw.* ) bereitgestellt. Aussagekräftige Überschriften haben keine Bedeutung für die Erstellung eines Modells, erleichtern aber die Arbeit mit den Daten im Experiment. Wenn Sie dieses Modell später in einem Webdienst veröffentlichen, kann der Benutzer des Diensts die Spalten anhand der Überschriften außerdem leichter identifizieren.  

Verwenden Sie das Modul [Edit Metadata][edit-metadata], um Spaltenüberschriften hinzuzufügen.

Sie verwenden das Modul [Edit Metadata][edit-metadata] zum Ändern der Metadaten, die einem Dataset zugeordnet sind. In diesem Fall nutzen Sie es, um aussagekräftigere Namen für die Spaltenüberschriften anzugeben. 

Zum Verwenden von [Edit Metadata][edit-metadata] müssen Sie zuerst die zu ändernden Spalten angeben (in diesem Fall alle). Als Nächstes geben Sie die Aktion an, die auf diese Spalten angewendet werden soll (in diesem Fall das Ändern der Spaltenüberschriften).

1. Geben Sie in der Modulpalette „Metadaten“ in das Feld **Suchen** ein. [Edit Metadata][edit-metadata] wird in der Modulliste angezeigt.

1. Klicken und ziehen Sie das Modul [Edit Metadata][edit-metadata] in den Experimentbereich, und legen Sie es unter dem zuvor hinzugefügten Dataset ab.

1. Verbinden Sie das Dataset mit [Edit Metadata][edit-metadata]: Klicken Sie auf den Ausgabeport des Datasets (den kleinen Kreis unten im Dataset), ziehen Sie ihn zum Eingabeport von [Edit Metadata][edit-metadata] (den kleinen Kreis oben im Modul), und lassen Sie die Maustaste los. Das Dataset und das Modul bleiben verbunden, auch wenn Sie diese im Bereich verschieben.
 
    Das Experiment sollte in etwa wie folgt aussehen:  

    ![Hinzufügen von „Edit Metadata“](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Das rote Ausrufezeichen gibt an, dass Sie die Eigenschaften für dieses Modul noch nicht festgelegt haben. Diesen Schritt führen Sie als Nächstes durch.

    > [!TIP]
    > Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. Doppelklicken Sie hierfür auf das Modul [Edit Metadata][edit-metadata], und geben Sie den Kommentar „Spaltenüberschriften hinzufügen“ ein. Klicken Sie auf eine Stelle des Experimentbereichs, um das Textfeld zu schließen. Klicken Sie auf den nach unten zeigenden Pfeil für das Modul, um den Kommentar anzuzeigen.
    > 
    > ![Modul „Edit Metadata“ mit hinzugefügtem Kommentar](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Wählen Sie [Edit Metadata][edit-metadata] aus, und klicken Sie im Bereich **Properties** rechts neben dem Experimentbereich auf **Launch column selector**.

1. Wählen Sie im Dialogfeld **Select columns** in **Available Columns** alle Zeilen aus, und klicken Sie auf „>“, um sie in **Selected Columns** zu verschieben.
   Das Dialogfeld sollte wie folgt aussehen:

   ![Spaltenauswahl, alle Spalten ausgewählt](./media/tutorial-part1-credit-risk/select-columns.png)


1. Klicken Sie auf das Häkchen **OK**.

1. Suchen Sie im Bereich **Properties** nach dem Parameter **New column name**. Geben Sie in diesem Feld eine Liste der Namen für die 21 Spalten im Dataset ein, durch Kommas getrennt und in der Reihenfolge der Spalten. Die Spaltennamen können Sie der Datasetdokumentation auf der UCI-Website entnehmen, oder Sie können einfach die folgende Liste kopieren und einfügen:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   Der Bereich „Eigenschaften“ sieht wie folgt aus:

   ![Eigenschaften für „Edit Metadata“](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Wenn Sie die Spaltenüberschriften überprüfen möchten, führen Sie das Experiment aus (klicken Sie unter dem Experimentbereich auf **RUN** ). Wenn die Ausführung abgeschlossen ist (in [Edit Metadata][edit-metadata] wird ein grünes Häkchen angezeigt), klicken Sie auf den Ausgabeport des Moduls [Edit Metadata][edit-metadata], und wählen Sie **Visualize** aus. Auf die gleiche Weise können Sie die Ausgabe jedes anderen Moduls anzeigen, um den Datenfortschritt im Experiment zu sehen.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Erstellen von Trainings- und Testdatasets

Sie benötigen Daten zum Trainieren des Modells und Daten zum Testen des Modells.
Daher unterteilen Sie das Dataset im nächsten Schritt des Experiments in zwei separate Datasets: eines zum Trainieren und das andere zum Testen des Modells.

Hierfür verwenden Sie das Modul [Split Data][split].  

1. Wechseln Sie zum Modul [Split Data][split], ziehen Sie es in den Experimentbereich, und verbinden Sie es mit dem Modul [Edit Metadata][edit-metadata].

1. Standardmäßig beträgt das Aufteilungsverhältnis 0,5, und der Parameter **Zufällige Aufteilung** ist festgelegt. Dies bedeutet, dass eine zufällig ausgewählte Hälfte der Daten über einen Port des Moduls [Split Data][split] und die andere Hälfte über den anderen Port ausgegeben wird. Sie können diese Parameter und den Parameter **Random seed** anpassen, um die Aufteilung zwischen Trainings- und Bewertungsdaten zu ändern. Für dieses Beispiel lassen Sie die Werte unverändert.
   
   > [!TIP]
   > Die Eigenschaft **Fraction of rows in the first output dataset** bestimmt, welcher Anteil der Daten über den *linken* Ausgabeport ausgegeben wird. Wenn Sie z. B. ein Aufteilungsverhältnis von 0,7 festlegen, werden 70 % der Daten über den linken Port und 30 % der Daten über den rechten Port ausgegeben.  
   > 
   > 

1. Doppelklicken Sie auf das Modul [Split Data][split], und geben Sie den Kommentar „Aufteilung zwischen Trainings- und Testdaten 50 %“ ein. 

Sie können die Ausgaben des Moduls [Split Data][split] beliebig verwenden. In diesem Fall wählen wir die linke Ausgabe als Trainingsdaten und die rechte Ausgabe als Testdaten.  

Wie im [vorherigen Schritt](tutorial-part1-credit-risk.md#upload) erwähnt, sind die Kosten einer Fehlklassifizierung eines hohen Kreditrisikos als niedriges Risiko fünfmal höher als die Kosten einer Fehlklassifizierung eines niedrigen Kreditrisikos als hohes Risiko. Um dies zu berücksichtigen, generieren Sie ein neues Dataset, das diese Kostenfunktion darstellt. Im neuen Dataset wird jedes Hochrisikobeispiel fünfmal repliziert, während keines der Niedrigrisikobeispiele repliziert wird.   

Für diese Replikation können Sie den R-Code verwenden:  

1. Suchen Sie nach dem Modul [Execute R Script][execute-r-script], und ziehen Sie es in den Experimentbereich. 

1. Verbinden Sie den linken Ausgabeport des Moduls [Split Data][split] mit dem ersten Eingabeport („Dataset1“) des Moduls [Execute R Script][execute-r-script].

1. Doppelklicken Sie auf das Modul [Execute R Script][execute-r-script], und geben Sie den Kommentar „Kostenanpassung festlegen“ ein.

1. Löschen Sie im Bereich mit den **Eigenschaften** den Standardtext im Parameter **R Script**, und geben Sie dieses Skript ein:
   
    ```r
    dataset1 <- maml.mapInputPort(1)
    data.set<-dataset1[dataset1[,21]==1,]
    pos<-dataset1[dataset1[,21]==2,]
    for (i in 1:5) data.set<-rbind(data.set,pos)
    maml.mapOutputPort("data.set")
    ```

    ![R-Skript im Modul „Execute R Script“](./media/tutorial-part1-credit-risk/execute-r-script.png)

Sie müssen den gleichen Replikationsvorgang für jede Ausgabe des Moduls [Split Data][split] durchführen, damit die Trainings- und die Testdaten die gleiche Kostenanpassung aufweisen. Dies erfolgt am einfachsten durch das Duplizieren des eben erstellten Moduls [Execute R Script][execute-r-script] und das Verbinden dieses Moduls mit dem anderen Ausgabeport des Moduls [Split Data][split].

1. Klicken Sie mit der rechten Maustaste auf das Modul [Execute R Script][execute-r-script], und wählen Sie die Option **Kopieren** aus.

1. Klicken Sie mit der rechten Maustaste in den Experimentbereich, und wählen Sie **Einfügen** aus.

1. Ziehen Sie das neue Modul an die richtige Position, und verbinden Sie den rechten Ausgabeport des Moduls [Split Data][split] dann mit dem ersten Eingabeport des neuen Moduls [Execute R Script][execute-r-script]. 

1. Klicken Sie am unteren Rand des Experimentbereichs auf **Run**. 

> [!TIP]
> Die Kopie des Moduls "Execute R Script" enthält das gleiche Skript wie das Originalmodul. Wenn Sie ein Modul kopieren und im Bereich einfügen, behält die Kopie alle Eigenschaften des Originals bei.  
> 
>

Unser Experiment sieht nun in etwa wie folgt aus:

![Hinzufügen des Aufteilungsmoduls und der R-Skripte](./media/tutorial-part1-credit-risk/experiment.png)

Weitere Informationen zum Verwenden von R-Skripts in Ihren Experimenten finden Sie unter [Erweitern Sie Ihr Experiment mit R](index.yml).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 
 
> [!div class="checklist"]
> * Erstellen eines (klassischen) Machine Learning Studio-Arbeitsbereichs
> * Hochladen vorhandener Daten in den Arbeitsbereich
> * Erstellen eines Experiments

Sie können jetzt Modelle für diese Daten trainieren und bewerten.

> [!div class="nextstepaction"]
> [Tutorial 2: Trainieren und Bewerten von Modellen](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data