---
title: 'Tutorial: Erstellen und Ausführen von Jupyter Notebook in Azure'
description: Erfahren Sie, wie Sie eine Jupyter Notebook-Datei in Azure Notebooks erstellen und ausführen, die den Prozess der linearen Regression in der Data Science veranschaulicht.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: 09d4038e705fb3bc4ff2c82daf5dc4c07f346f94
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751757"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutorial: Erstellen und Ausführen einer Jupyter Notebook-Datei mit Python

In diesem Tutorial durchlaufen Sie den Prozess zur Verwendung von Azure Notebooks zum Erstellen einer vollständigen Jupyter Notebook-Datei, die eine einfache lineare Regression veranschaulicht. Im Laufe dieses Tutorials machen Sie sich mit der Benutzeroberfläche von Jupyter Notebook vertraut, die das Erstellen verschiedener Zellen, das Ausführen von Zellen und eine Anzeige des Notebooks als Diaschau umfasst.

Das vollständige Notebook finden Sie unter [GitHub - Azure Notebooks Samples (GitHub: Azure Notebooks-Beispiele)](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Dieses Tutorial beginnt jedoch mit einem neuen Projekt und einem leeren Notebook, damit Sie die Erstellung Schritt für Schritt kennenlernen können.

## <a name="create-the-project"></a>Erstellen des Projekts

1. Navigieren Sie zu [Azure Notebooks](https://notebooks.azure.com), und melden Sie sich an. (Ausführliche Informationen hierzu finden Sie unter [Schnellstart: Anmelden bei Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Wählen Sie oben auf Ihrer öffentlichen Profilseite **Meine Projekte** aus:

    ![Link „Meine Projekte“ am oberen Rand des Browserfensters](media/quickstarts/my-projects-link.png)

1. Klicken Sie auf der Seite **My Projects** (Meine Projekte) auf **+ New Project** (+ Neues Projekt, Tastenkombination: n). Wenn Ihr Browserfenster schmal ist, wird möglicherweise nur **+** für die Schaltfläche angezeigt:

    ![Befehl „New Project“ (Neues Projekt) auf der Seite „My Projects“ (Meine Projekte)](media/quickstarts/new-project-command.png)

1. Geben Sie folgende Informationen im angezeigten Popupfenster **Create New Project** (Neues Projekt erstellen) an, und klicken Sie anschließend auf **Create** (Erstellen):

    - **Project name** (Projektname): Linear Regression Example - Cricket Chirps
    - **Project ID** (Projekt-ID): linear-regression-example
    - **Public project** (Öffentliches Projekt): (deaktiviert)
    - **Create a README.md** (README-Datei erstellen): (nicht aktiviert)

1. Kurz darauf navigiert Azure Notebooks zum neuen Projekt.

## <a name="create-the-data-file"></a>Erstellen der Datendatei

Mit dem linearen Regressionsmodell, das Sie im Notebook erstellen, werden die Daten aus einer Datei namens *cricket_chirps.csv* in das Projekt importiert. Diese Datei können Sie entweder aus [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps) (GitHub: Azure Notebooks-Beispiele) kopieren, oder Sie erstellen sie, indem Sie die Daten direkt eingeben. In den folgenden Abschnitten werden beide Ansätze beschrieben.

### <a name="upload-the-data-file"></a>Hochladen der Datendatei

1. Klicken Sie im Projektdashboard in Azure Notebooks auf **Upload** > **From URL** (Hochladen > per URL).
1. Geben Sie im Popupfenster die folgende URL für **File URL** (Datei-URL) und *cricket_chirps.csv* für **File Name** (Dateiname) ein, und klicken Sie dann auf **Done** (Fertig).

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Die Datei *cricket_chirps.csv* sollte nun in der Dateiliste Ihres Projekts angezeigt werden:

    ![Neu erstellte CSV-Datei in der Projektdateiliste](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Erstellen einer Datei von Grund auf

1. Klicken Sie im Projektdashboard in Azure Notebooks auf **New** > **Blank File** (Neu > Leere Datei).
1. In der Projektdateiliste wird ein Feld angezeigt. Geben Sie *cricket_chirps.csv* ein, und drücken Sie die EINGABETASTE.
1. Klicken Sie mit der rechten Maustaste auf *cricket_chirps.csv*, und klicken Sie auf **Edit File** (Datei bearbeiten).
1. Geben Sie folgende Daten im angezeigten Editor ein:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Klicken Sie auf **Save File** (Datei speichern), um die Datei zu speichern und zurück zum Projektdashboard zu gelangen.

## <a name="install-project-level-packages"></a>Installieren von Paketen auf Projektebene

Befehle wie `!pip install` können Sie innerhalb eines Notebooks immer in einer Codezelle verwenden, um erforderliche Pakete zu installieren. Diese Befehle werden jedoch jedes Mal ausgeführt, wenn Sie die Codezellen des Notebooks ausführen. Dies kann viel Zeit in Anspruch nehmen. Aus diesem Grund können Sie Pakete mithilfe einer `requirements.txt`-Datei auf der Projektebene installieren.

1. Verwenden Sie die unter [Erstellen einer Datei von Grund auf](#create-a-file-from-scratch) beschriebene Vorgehensweise, um eine Datei names `requirements.txt` mit folgendem Inhalt zu erstellen:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Alternativ können Sie wie unter [Hochladen der Datendatei](#upload-the-data-file) beschrieben eine `requirements.txt`-Datei auch von Ihrem lokalen Computer hochladen.

1. Klicken Sie auf dem Projektdashboard auf **Project Settings** (Projekteinstellungen).
1. Wählen Sie im angezeigten Popupfenster die Registerkarte **Environment** (Umgebung) aus, und klicken Sie dann auf **+Add** (+ Hinzufügen).
1. Wählen Sie unter **Environment Setup Steps** (Schritte für das Setup der Umgebung) im ersten Dropdown-Steuerelement (Vorgang) die Datei **Requirements.txt** aus.
1. Wählen Sie im zweiten Dropdown-Steuerelement (Dateiname) die Datei *requirements.txt* (die Sie erstellt haben) aus.
1. Wählen Sie im dritten Dropdown-Steuerelement (Python-Version) die Option **Python Version 3.6** aus.
1. Wählen Sie **Speichern** aus.

![Die Datei „requirements.txt“ auf der Registerkarte „Environment“ (Umgebung) in den Projekteinstellungen](media/tutorial/tutorial-requirements-txt.png)

Nachdem Sie diesen Setupschritt durchgeführt haben, wird jedes Notebook, das Sie im Projekt ausführen, in einer Umgebung ausgeführt, in der diese Pakete installiert sind.

## <a name="create-and-run-a-notebook"></a>Erstellen und Ausführen eines Notebooks

Da die Datendatei nun bereit ist und die Projektumgebung festgelegt ist, können Sie das Notebook nun erstellen und öffnen.

1. Klicken Sie auf dem Projektdashboard auf **+ New** > **Notebook** (+ Neu > Notebook).
1. Geben Sie im Popupfenster für **Item Name** (Elementname) *Linear Regression Example - Cricket Chirps.ipynb* ein, wählen Sie **Python 3.6** als Sprache aus, und klicken Sie dann auf **New** (Neu).
1. Das neue Notebook wird in der Dateiliste angezeigt, klicken Sie darauf, um es zu starten. Im Browser wird automatisch eine neue Browserregisterkarte geöffnet.
1. Da Sie in den Umgebungseinstellungen über eine *requirements.txt*-Datei verfügen, wird die folgende Meldung angezeigt: „Waiting for your container to finish being prepared“ (Auf die Vorbereitung Ihres Containers wird gewartet). Sie können auf **OK** klicken, um die Meldung zu schließen und mit der Arbeit im Notebook fortzufahren. Allerdings können Sie keine Codezellen ausführen, bis die Umgebung vollständig eingerichtet ist.
1. Das Notebook wird standardmäßig mit einer einzelnen leeren Codezelle in der Jupyter-Schnittstelle geöffnet.

    [![Anfängliche Ansicht eines neuen Notebooks in Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Einführung in die Notebook-Schnittstelle

Im ausgeführten Notebook können Sie Code- und Markdown-Zellen hinzufügen, diese Zellen ausführen und den Betrieb des Notebooks verwalten. Zunächst sollten Sie sich jedoch mit der Benutzeroberfläche vertraut machen. Eine vollständige Dokumentation steht Ihnen über den Menübefehl **Help** > **Notebook Help** (Hilfe > Notebook-Hilfe) zur Verfügung.

Am oberen Rand des Fensters finden Sie folgende Elemente:

(A) Der Name Ihres Notebooks. Diesen können Sie bearbeiten, indem Sie auf ihn klicken.
(B) Schaltflächen für die Navigation zum enthaltenden Projekt und zum Projektdashboard, die in neuen Browserregisterkarten geöffnet werden.
(C) Ein Menü mit Befehlen für die Arbeit mit dem Notebook.
(D) Eine Symbolleiste mit Verknüpfungen für allgemeine Aktionen.
(E) Die Bearbeitungsfläche mit den Zellen.
(F) Ein Indikator, der angibt, ob das Notebook vertrauenswürdig ist (der Standardwert ist **Not Trusted** (Nicht vertrauenswürdig)).
(G) Der Kernel, der zum Ausführen des Notebooks verwendet wird, mit einem Indikator für die Aktivität.

[![Hauptbereiche der Benutzeroberfläche der Jupyter-Schnittstelle](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter stellt eine integrierte Einführung für die primären Benutzeroberflächenelemente bereit. Diese Einführung können Sie starten, indem Sie auf **Help** > **User Interface Tour** (Hilfe > Einführung in die Benutzeroberfläche) klicken. Dann können Sie sich durch die angezeigten Popupfenster klicken.

Im Folgenden werden die Gruppen der Menübefehle aufgeführt:

| Menü | BESCHREIBUNG |
| --- | --- |
| Datei | Befehle für die Verwaltung der Notebook-Datei, einschließlich Befehle zum Erstellen und Kopieren von Notebooks, Anzeigen einer Druckvorschau und Herunterladen des Notebooks in verschiedenen Formaten. |
| Edit (Bearbeiten) | Herkömmliche Befehle zum Ausschneiden, Kopieren und Einfügen von Zellen, Suchen und Ersetzen von Werten, Verwalten von Zellanhängen und Einfügen von Bildern.  |
| Sicht | Befehle zum Steuern der Sichtbarkeit verschiedener Abschnitte der Jupyter-Benutzeroberfläche. |
| Einfügen | Befehle zum Einfügen einer neuen Zelle ober- oder unterhalb der aktuellen Zelle. Diese Befehle werden Sie beim Erstellen eines Notebooks häufig verwenden. |
| Cell (Zelle) | Die verschiedenen**Run**-Befehle (Ausführen) werden in einer Zelle oder mehreren Zellen in unterschiedlichen Kombinationen ausgeführt. Die Befehle **Cell Type** (Zellentyp) ändern den Typ einer Zelle zwischen **Code**, **Markdown** und **unformatierte NBConvert**-Zellen (Nur-Text). Die Befehle **Current Outputs** (Aktuelle Ausgaben) und **All Outputs** (Alle Ausgaben) steuern, wie die Ausgabe des ausgeführten Codes angezeigt wird. Zudem wird ein Befehl zum Löschen der gesamten Ausgabe eingefügt. |
| Kernel | Befehle, mit denen verwaltet werden kann, wie Code im Kernel zusammen mit **Change kernel** (Kernel ändern) ausgeführt wird, um die Sprache der Python-Version zu ändern, die zum Ausführen des Notebook verwendet wird. |
| Daten | Befehle zum Hochladen und Herunterladen von Dateien aus dem Projekt oder der Sitzung. Weitere Informationen finden Sie unter [Arbeiten mit Datendateien](work-with-project-data-files.md). |
| Widgets | Befehle zum Verwalten von [Jupyter Widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), die zusätzliche Funktionen zur Visualisierung, Zuordnung und zum Zeichnen bieten.|
| Hilfe | Befehle, die Hilfe und Dokumentation für die Jupyter-Schnittstelle bereitstellen. |

Die meisten Befehle auf der Symbolleiste haben entsprechende Menübefehle. Eine Ausnahme ist jedoch **Enter/Edit RISE Slideshow** (RISE-Diaschau eingeben/bearbeiten). Dieser Befehl wird unter [Zeigen einer Bildschirmpräsentation](present-jupyter-notebooks-slideshow.md) weiter erläutert.

Sie verwenden mehrere von diesen Befehlen, wenn Sie das Notebook in den folgenden Abschnitten auffüllen.

## <a name="create-a-markdown-cell"></a>Erstellen einer Markdownzelle

1. Klicken Sie in die erste leere Zelle, die im Notebook-Canvas angezeigt wird. In der Standardeinstellung ist eine Zelle ein **Codetyp**. Das bedeutet, dass dieser dazu konzipiert ist,ausführbaren Code für den ausgewählten Kernel (Python, R oder F#) zu enthalten. Der aktuelle Typ wird im Dropdownmenü für den Typ auf der Symbolleiste angezeigt:

    ![Dropdownmenü auf der Symbolleiste für den Zellentyp](media/tutorial/tutorial-cell-type-drop-down.png)

1. Ändern Sie den Zellentyp über das Dropdownmenü auf der Symbolleiste in **Markdown**. Verwenden Sie alternativ den Menübefehl **Cell** > **Cell Type** > **Markdown** (Zelle > Zellentyp > Markdown).

    ![Menübefehl für den Zellentyp](media/tutorial/tutorial-cell-type-menu.png)

1. Klicken Sie in die Zelle, um mit der Bearbeitung zu beginnen. Geben Sie anschließend den folgenden Markdowncode ein:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Klicken Sie auf der Symbolleiste auf den Befehl **Run** (Ausführen), oder verwenden Sie den Befehl **Cell** > **Run Cells** (Zelle > Zellen ausführen), um den Markdowncode in HTML zu rendern. Der Markdowncode zum Formatieren und Links werden nun wie gewünscht in einem Browser angezeigt.

1. Wenn Sie die letzte Zelle im Notebook ausführen, erstellt Jupyter automatisch eine neue Zelle unter der, die Sie ausgeführt haben. Fügen Sie weiteren Markdowncode in diese Zelle ein, indem Sie die Schritte in diesem Abschnitt mit dem folgenden Markdowncode wiederholen:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Doppelklicken Sie in die gerenderte Zelle, um den Markdown nochmals zu bearbeiten. Führen Sie die Zelle aus, um den HTML-Code erneut zu rendern, nachdem Sie Änderungen ausgeführt haben.

## <a name="create-a-code-cell-with-commands"></a>Erstellen einer Codezelle mit Befehlen

Wie in der vorherigen Markdownzelle erläutert, können sie Befehle direkt im Notebook einfügen. Sie können Befehle verwenden, um Pakete zu installieren, um cURL oder Wget zum Abrufen von Daten auszuführen oder um andere Aktionen durchzuführen. Jupyter-Notebooks werden innerhalb einer Linux-VM effektiv ausgeführt. Ihnen steht also der gesamte Linux-Befehlssatz zur Verfügung.

1. Geben Sie die Befehle unten in die Codezelle ein, die angezeigt wird, nachdem sie den **Run**-Befehl für die vorherige Markdownzelle verwendet haben. Wenn keine neue Zelle angezeigt wird, erstellen Sie über **Insert** > **Insert Cell Below** (Einfügen > Zelle unten einfügen) eine Zelle, oder verwenden Sie die Schaltfläche **+** auf der Symbolleiste.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Bevor Sie die Zelle ausführen, erstellen Sie über die Schaltfläche **+** auf der Symbolleiste eine neue Zelle, legen Sie sie auf Markdown fest, und geben Sie die folgende Erläuterung ein:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Wählen Sie den Befehl **Cell** > **Run All** (Zelle > Alle ausführen) aus, um alle Zellen im Notebook auszuführen. Beachten Sie, dass die Markdownzellen als HTML gerendert werden und der Befehl im Kernel ausgeführt wird. Beobachten Sie den Kernelindikator wie im Markdowncode beschrieben:

    ![Auslastungsindikator für den Notebook-Kernel](media/tutorial/tutorial-kernel-busy.png)

1. Es dauert etwas, bis alle `pip install`-Befehle ausgeführt werden. Da Sie zudem diese Pakete bereits in der Projektumgebung installiert haben (und weil diese ebenso standardmäßig in Azure Notebooks enthalten sind), werden viele Meldungen wie die folgende ausgegeben: „Requirement already satisfied“ (Anforderung bereits erfüllt). Damit diese Ausgabe optisch nicht so ablenkend ist, wählen Sie diese Zelle mit nur einem Klick aus, und klicken Sie dann auf **Cell** > **Cell Outputs** > **Toggle** (Zelle > Zellenausgaben > Umschalten), um die Ausgabe auszublenden. Sie können auch den Befehl **Clear** (Löschen) über das gleiche Untermenü auswählen, um die Ausgabe vollständig zu entfernen.

    Mit dem Befehl **Toggle** (Umschalten) wird nur die neueste Ausgabe der Zelle ausgeblendet. Wenn Sie die Zelle erneut ausführen, wird die Ausgabe wieder angezeigt.

1. Da die Pakete in der Projektumgebung installiert werden, kommentieren Sie die `! pip install`-Befehle mithilfe von `#` aus. So können diese im Notebook als Begleitmaterial verbleiben, sie benötigen jedoch keine Ausführzeit mehr und erzeugen keine unnötige Ausgabe. Wenn die kommentierten Befehle im Notebook verbleiben, werden in diesem Fall auch die Abhängigkeiten des Notebooks angegeben.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Erstellen der verbleibenden Zellen

Damit Sie den Rest des Notebooks auffüllen können, müssen Sie eine Reihe von Markdown- und Codezellen erstellen. Erstellen Sie für jede unten aufgeführte Zelle zunächst die neue Zelle, legen Sie dann den Typ fest, und fügen Sie sie in den Inhalt ein.

Obwohl Sie mit der Ausführung des Notebooks warten können, bis Sie jede Zelle erstellt haben, ist es doch interessant, jede Zelle gleich bei der Erstellung auszuführen. Nicht alle Zellen zeigen Ausgaben an. Wenn keine Fehler ausgelöst werden, kann man davon ausgehen, dass die Zelle ordnungsgemäß ausgeführt wurde.

Jede Codezelle hängt vom Code ab, der in vorherigen Zellen ausgeführt wurde. Wenn Sie versäumen, eine Zelle auszuführen, können spätere Zellen Fehler verursachen. Wenn Sie bemerken, dass Sie eine Zelle vergessen haben, verwenden Sie **Cell** > **Run All Above** (Zelle > Alle vorherigen ausführen), bevor Sie die aktuelle Zelle ausführen.

Wenn unerwartete Ergebnisse angezeigt werden (was aller Wahrscheinlichkeit nach der Fall sein wird), überprüfen Sie, ob jede Zelle nach Bedarf auf „Code“ oder „Markdown“ festgelegt ist. Beispielsweise wird der Fehler „Ungültige Syntax“ in der Regel angezeigt, wenn Sie Markdown in einer Codezelle eingegeben haben.

1. Markdownzelle:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Die Codezelle zeigt die Tabelleninhalte als Ausgabe, wenn sie ausgeführt wird. Sie können die Ausgabe unterdrücken, indem Sie die `print`-Anweisung auskommentieren.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Es können Warnungen von diesem Code, wie etwa "numpy.dtype size changed" (numpy.dtype-Größe geändert), ausgegeben werden. Diese können jedoch bedenkenlos ignoriert werden.

1. Markdownzelle:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Die Codezelle verfügt über keine Ausgabe, wenn sie ausgeführt wird.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdownzelle:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Die Codezelle zeigt die Ausgabe `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` an, wenn sie ausgeführt wird.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdownzelle:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Die Codezelle zeigt Ergebnisse wie `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` an, wenn sie ausgeführt wird.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdownzelle:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdownzelle:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Die Codezelle erzeugt einen grafischen Plot, wenn sie ausgeführt wird. Wenn der Plot nach der ersten Ausführung nicht angezeigt wird (und stattdessen "Figure size 640x480 with 1 Axes" (Größe der Abbildung 640x480 mit 1 Achse)), führen Sie die Zelle erneut aus.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Plotausgabe über den matplotlib-Code](media/tutorial/tutorial-plot-output.png)

1. Markdownzelle:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Löschen von Ausgaben und erneutes Ausführen von Zellen

Wenn Sie die im vorherigen Abschnitt angegebenen Schritte zum Auffüllen des gesamten Notebooks befolgt haben, haben sie Code erstellt, der im Kontext eines vollständigen Tutorials zu linearer Regression ausgeführt wird. Diese unmittelbare Kombination aus Code und Text ist eines der größten Vorteile von Notebooks.

Versuchen Sie nun, das gesamte Notebook erneut auszuführen:

1. Löschen Sie alle Sitzungsdaten des Kernels sowie die gesamte Zellenausgabe, indem Sie **Kernel** > **Restart & Clear Output** (Kernel > Neu starten und Ausgabe löschen) auswählen. Dieser Befehl eignet sich immer, wenn Sie ein Notebook abgeschlossen haben. Damit können Sie sicherstellen, dass Sie keine seltsamen Abhängigkeiten zwischen Codezellen erstellt haben.

1. Führen Sie das Notebook erneut über **Cell** > **Run All** (Zelle > Alle ausführen) aus. Beachten Sie, dass der Kernelindikator ausgefüllt wird, während der Code ausgeführt wird.

    Wenn Sie Code besitzen, der zu lange ausgeführt wird oder anderweitig hängen bleibt, können Sie den Kernel anhalten, indem Sie den Befehl **Kernel** > **Interrupt** (Kernel > Unterbrechen) ausführen.

1. Scrollen Sie durch das Notebook, um die Ergebnisse zu untersuchen. (Wenn der Plot nicht angezeigt wird, führen Sie diese Zelle erneut aus.)

## <a name="save-halt-and-close-the-notebook"></a>Speichern Sie das Notebook, halten Sie es an, und schließen Sie es.

Während des Zeitraums, in dem Sie ein Notebook bearbeiten, können Sie dessen aktuellen Zustand mithilfe des Befehls **File** > **Save and Checkpoint** (Datei > Speichern und Prüfpunkt) oder über die Schaltfläche „Speichern“ auf der Symbolleiste speichern. Ein „Prüfpunkt“ erstellt eine Momentaufnahme, die Sie jederzeit während der Sitzung wiederherstellen können. Mithilfe von Prüfpunkten können Sie experimentelle Änderungen vornehmen. Sollten diese Änderungen nicht funktionieren, können Sie auch nur mithilfe des Befehls **File** > **Revert to Checkpoint** (Datei > Auf Prüfpunkt wiederherstellen) zu einem Prüfpunkt zurückkehren. Ein alternativer Ansatz ist die Erstellung zusätzlicher Zellen und das Auskommentieren von Code, der nicht ausgeführt werden soll. Beide Ansätze funktionieren gut.

Sie können auch jederzeit den Befehl **File** > **Make a Copy** (Datei > Kopie erstellen) verwenden, um eine Kopie des aktuellen Zustands des Notebooks zu erstellen und diese in einer neuer Datei in Ihrem Projekt zu speichern. Diese Kopie öffnet automatisch eine neue Browserregisterkarte.

Wenn Sie mit einem Notebook fertig sind, verwenden Sie den Befehl **File** > **Close and halt** (Datei > Schließen und anhalten), wodurch das Notebook geschlossen und der Kernel, der es ausgeführt hat, heruntergefahren wird. Azure Notebooks schließt dann automatisch die Browserregisterkarte.

## <a name="debug-notebooks-using-visual-studio-code"></a>Debuggen von Notebooks über Visual Studio Code

Wenn sich Codezellen im Notebook nicht so verhalten, wie Sie es von ihnen erwarten, haben Sie möglicherweise Fehler im Code oder andere Fehler. Eine typische Jupyter-Umgebung bietet außer den `print`-Anweisungen zum Anzeigen der Werte von Variablen jedoch keine Debugfunktionen.

Sie können glücklicherweise die *IPYNB*-Datei des Notebooks herunterladen und dann in Visual Studio Code unter Verwendung der Python-Erweiterung öffnen. Die Erweiterung importiert ein Notebook direkt als einzelne Codedatei, die Markdown-Zellen in den Kommentaren beibehält. Sobald Sie das Notebook importiert haben, können Sie den Visual Studio Code-Debugger ausführen, um Ihren Code schrittweise zu durchlaufen, Haltepunkte festzulegen, den Zustand zu untersuchen usw. Nachdem Sie Ihren Code korrigiert haben, exportieren Sie die *IPYNB*-Datei aus Visual Studio Code und laden sie wieder in Azure Notebooks hoch.

Weitere Informationen finden Sie in der Visual Studio Code-Dokumentation unter [Debug a Jupyter notebook (Debuggen eines Jupyter-Notebooks)](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook).

Zusätzliche Funktionen von Visual Studio Code für Jupyter-Notebooks werden auch in [Working with Jupyter Notebooks in Visual Studio Code (Arbeiten mit Jupyter-Notebooks in Visual Studio Code)](https://code.visualstudio.com/docs/python/jupyter-support) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

- [Erkunden von Beispielnotebooks](azure-notebooks-samples.md)

Gewusst-wie-Artikel:

- [Erstellen und Klonen von Projekten](create-clone-jupyter-notebooks.md)
- [Konfigurieren und Verwalten von Projekten](configure-manage-azure-notebooks-projects.md)
- [Installieren von Paketen aus einem Notebook](install-packages-jupyter-notebook.md)
- [Zeigen einer Bildschirmpräsentation](present-jupyter-notebooks-slideshow.md)
- [Arbeiten mit Datendateien](work-with-project-data-files.md)
- [Zugreifen auf Datenressourcen](access-data-resources-jupyter-notebooks.md)
- [Verwenden von Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
