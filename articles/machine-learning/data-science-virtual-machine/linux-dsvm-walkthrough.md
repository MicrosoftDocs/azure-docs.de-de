---
title: Hier erfahren Sie, wie Sie die Linux Data Science Virtual Machine verwenden.
titleSuffix: Azure
description: Es wird beschrieben, wie Sie mehrere häufige Data Science-Aufgaben mit der Linux Data Science-VM ausführen.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: gokuma
ms.openlocfilehash: 6e8883870cc0f035df5122e91449f04203836218
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60516849"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Data Science mit einer Linux Data Science Virtual Machine in Azure
In dieser exemplarischen Vorgehensweise erfahren Sie, wie Sie mehrere häufige Data Science-Aufgaben mit der Linux Data Science-VM ausführen. Bei der Linux Data Science Virtual Machine (DSVM) handelt es sich um das Image eines virtuelles Computers, das unter Azure verfügbar ist und mit einer Sammlung von Tools vorinstalliert wird, die häufig für die Datenanalyse und Machine Learning verwendet werden. Die wichtigsten Softwarekomponenten sind im Thema [Bereitstellen der Linux Data Science Virtual Machine](linux-dsvm-intro.md) einzeln aufgeführt. Das VM-Image erleichtert Ihnen den Start, und für den Data Science-Vorgang sind nur wenige Minuten erforderlich, ohne dass die Tools einzeln installiert und konfiguriert werden müssen. Sie können die VM bei Bedarf leicht zentral hochskalieren und beenden, wenn sie nicht verwendet wird. Diese Ressource ist also sowohl flexibel als auch kosteneffizient.

Die Data Science-Aufgaben, die in dieser exemplarischen Vorgehensweise veranschaulicht werden, basieren auf den Schritten unter [Team Data Science-Prozess (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Dieser Prozess ermöglicht einen systematischen Data Science-Ansatz, mit dem Teams von Data Scientists bei der Erstellung von intelligenten Anwendungen effektiv miteinander kommunizieren können. Der Data Science-Prozess bietet zudem ein iteratives Framework für Data Science, das vom Benutzer verfolgt werden kann.

In dieser exemplarischen Vorgehensweise analysieren wir das Dataset [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Hierbei handelt es sich um eine Gruppe von E-Mails, die entweder als „Spam“ oder „Ham“ (also kein Spam) gekennzeichnet sind, und außerdem sind einige statistische Daten zum Inhalt der E-Mails enthalten. Diese statistischen Daten werden im übernächsten Abschnitt beschrieben.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie eine Linux Data Science Virtual Machine verwenden können, benötigen Sie Folgendes:

* Ein **Azure-Abonnement**. Wenn Sie noch kein Abonnement besitzen, helfen Ihnen die Informationen unter [Erstellen Sie noch heute Ihr kostenloses Azure-Konto](https://azure.microsoft.com/free/)weiter.
* Eine [**Linux Data Science-VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Informationen zur Bereitstellung dieses virtuellen Computers finden Sie unter [Bereitstellen der Linux Data Science Virtual Machine](linux-dsvm-intro.md).
* [X2Go](https://wiki.x2go.org/doku.php) auf dem Computer mit geöffneter XFCE-Sitzung. Informationen zur Installation und Konfiguration eines **X2Go-Clients** finden Sie unter [Installieren und Konfigurieren des X2Go-Clients](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Schalten Sie zum reibungsloseren Scrollen auf VMs mit Firefox-Browsern das Flag „gfx.xrender.enabled“ in „about:config“ um. [Weitere Informationen finden Sie hier.](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/) Sie könnten auch *mousewheel.enable_pixel_scrolling* auf „False“ festlegen. [Die entsprechenden Anweisungen finden Sie hier.](https://support.mozilla.org/en-US/questions/981140)
* Ein **AzureML-Konto**. Melden Sie sich auf der [AzureML-Startseite](https://studio.azureml.net/)für ein neues Konto an, wenn Sie noch kein Konto haben. Als Starthilfe wird ein kostenloser Tarif angeboten.

## <a name="download-the-spambase-dataset"></a>Herunterladen des Datasets „spambase“
Das Dataset [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) umfasst eine relativ kleine Gruppe von Daten, die nur 4601 Beispiele enthält. Dies ist eine gut geeignete Größe, um einige wichtige Features der Data Science-VM zu veranschaulichen, da der Umfang der Ressourcenanforderungen gering ist.

> [!NOTE]
> Diese exemplarische Vorgehensweise wurde auf einer Linux Data Science Virtual Machine (CentOS Edition) der Größe „D2 v2“ erstellt. Eine DSVM dieser Größe reicht aus, um die Verfahren bei dieser Vorgehensweise zu bewältigen.
>
>

Falls Sie mehr Speicherplatz benötigen, können Sie zusätzliche Datenträger erstellen und an Ihre VM anfügen. Für diese Datenträger wird dauerhafter Azure-Speicher verwendet, sodass die Daten auch dann beibehalten werden, wenn der Server aufgrund einer Größenänderung neu bereitgestellt oder heruntergefahren wird. Führen Sie die Anleitung unter [Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)aus, um einen Datenträger hinzuzufügen und an Ihre VM anzufügen. Für diese Schritte wird die Azure-Befehlszeilenschnittstelle (Azure CLI) verwendet, die auf der DSVM bereits installiert ist. Diese Verfahren können also vollständig über die VM selbst durchgeführt werden. Eine weitere Option zum Erhöhen des Speichers ist die Verwendung von [Azure-Dateien](../../storage/files/storage-how-to-use-files-linux.md).

Öffnen Sie zum Herunterladen der Daten ein Terminalfenster, und führen Sie diesen Befehl aus:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Die heruntergeladene Datei enthält keinen Header. Wir erstellen daher eine andere Datei, die über einen Header verfügt. Führen Sie diesen Befehl aus, um eine Datei mit den entsprechenden Headern zu erstellen:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Verketten Sie die beiden Dateien dann mit diesem Befehl:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Das Dataset enthält mehrere Arten von Statistiken für jede E-Mail:

* In Spalten wie ***word\_freq\_WORD*** wird der Prozentsatz der Wörter in der E-Mail angegeben, die mit *WORD* übereinstimmen. Beispiel: Wenn *word\_freq\_make* den Wert 1 hat, lauteten 1% aller Wörter in der E-Mail *make*.
* In Spalten wie ***char\_freq\_CHAR*** wird der Prozentsatz aller Zeichen in der E-Mail angegeben, die *CHAR* lauten.
* ***capital\_run\_length\_longest*** ist die längste Sequenz von Großbuchstaben.
* ***capital\_run\_length\_average*** ist die durchschnittliche Länge aller Sequenzen mit Großbuchstaben.
* ***capital\_run\_length\_total*** ist die Gesamtlänge aller Sequenzen mit Großbuchstaben.
* ***spam*** gibt an, ob die E-Mail als Spam eingestuft wurde (1 = Spam, 0 = kein Spam).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Untersuchen des Datasets mit Microsoft R Open
Wir untersuchen nun die Daten und führen einige einfache Machine Learning-Schritte mit R aus. Auf der Data Science-VM ist [Microsoft R Open](https://mran.revolutionanalytics.com/open/) vorinstalliert. Die mathematischen Multithread-Bibliotheken in dieser Version von R bieten eine bessere Leistung als verschiedene Singlethread-Versionen. Microsoft R Open ermöglicht auch die Reproduzierbarkeit anhand einer Momentaufnahme des CRAN-Paketrepositorys.

Um Kopien der Codebeispiele zu erhalten, die in dieser exemplarischen Vorgehensweise verwendet werden, klonen Sie das Repository **Azure-Machine-Learning-Data-Science** mit „git“. „git“ ist auf der VM vorinstalliert. Führen Sie an der git-Befehlszeile Folgendes aus:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Öffnen Sie ein Terminalfenster, und starten Sie eine neue R-Sitzung mit der interaktiven R-Konsole, oder verwenden Sie die auf dem Computer vorinstallierte RStudio-Instanz.


Führen Sie Folgendes aus, um die Daten zu importieren und die Umgebung einzurichten:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Zusammenfassende Statistiken zu jeder Spalte erhalten Sie wie folgt:

    summary(data)

Eine andere Ansicht der Daten:

    str(data)

Der Typ der einzelnen Variablen und die ersten Werte im Dataset werden angezeigt.

Die Spalte *spam* wurde als ganze Zahl gelesen, aber es ist eigentlich eine Kategorievariable (oder ein Faktor). So legen Sie den Typ fest

    data$spam <- as.factor(data$spam)

Verwenden Sie zum Durchführen einiger Untersuchungsanalysen das Paket [ggplot2](https://ggplot2.tidyverse.org/). Hierbei handelt es sich um eine beliebte Graphbibliothek für R, die auf der VM bereits installiert ist. Beachten Sie für die weiter oben dargestellten Zusammenfassungsdaten, dass zusammenfassende Statistiken zur Häufigkeit des Ausrufezeichens vorhanden sind. Wir zeigen diese Häufigkeiten hier mit den folgenden Befehlen an:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Da die Nullleiste das Plotergebnis verzerrt, blenden wir sie aus:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ein nicht trivialer Dichtewert größer als 1 sieht interessant aus. Wir sehen uns nur diese Daten an:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Dann unterteilen wir nach „Spam“ bzw. „Ham“:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Mithilfe dieser Beispiele sollten Sie in der Lage sein, ähnliche Plots für die anderen Spalten zu erstellen, um die darin enthaltenen Daten zu untersuchen.

## <a name="train-and-test-an-ml-model"></a>Trainieren und Testen eines ML-Modells
Wir trainieren nun eine Reihe von Machine Learning-Modellen, um die E-Mails im Dataset danach zu klassifizieren, ob es sich um „Spam“ oder „Ham“ handelt. Wir trainieren in diesem Abschnitt ein Entscheidungsbaummodell und ein Modell mit zufälliger Gesamtstruktur und testen dann die Genauigkeit der Vorhersagen.

> [!NOTE]
> Das Paket „rpart“ (Recursive Partitioning and Regression Trees), das im folgenden Code verwendet wird, ist auf der Data Science-VM bereits installiert.
>
>

Zuerst unterteilen wird das Dataset in Trainings- und Testsätze:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Anschließend erstellen wir einen Entscheidungsbaum zum Klassifizieren der E-Mails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Hier ist das Ergebnis:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Verwenden Sie den folgenden Code, um die Leistung für den Trainingssatz zu ermitteln:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Gehen Sie wie folgt vor, um die Leistung für den Testsatz zu ermitteln:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Wir probieren auch ein Modell mit einer zufälligen Gesamtstruktur aus. Mit zufälligen Gesamtstrukturen werden viele Entscheidungsbäume trainiert. Hierbei wird eine Klasse ausgegeben, bei der es sich um den Modus der Klassifizierungen aller einzelnen Entscheidungsbäume handelt. Dies ist ein leistungsfähigerer Machine Learning-Ansatz, da die Tendenz eines Entscheidungsbaummodells, für ein Trainingsdataset eine Überanpassung durchzuführen, korrigiert wird.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Bereitstellen eines Modells in Azure Machine Learning Studio
[Azure Machine Learning Studio](https://studio.azureml.net/) ist ein Clouddienst, der das Erstellen und Bereitstellen von Predictive Analytics-Modellen vereinfacht. Eine der sehr hilfreichen Funktionen von Azure Machine Learning Studio ist die Fähigkeit, beliebige R-Funktionen als Webdienst zu veröffentlichen. Das R-Paket von Azure Machine Learning Studio ermöglicht die einfache Bereitstellung direkt in unserer R-Sitzung auf der DSVM.

Zum Bereitstellen des Entscheidungsbaumcodes aus dem vorherigen Bereich müssen Sie sich an Azure Machine Learning Studio anmelden. Sie benötigen für die Anmeldung Ihre Arbeitsbereich-ID und ein Autorisierungstoken. Gehen Sie wie folgt vor, um diese Werte zu ermitteln und die Azure Machine Learning-Variablen damit zu initialisieren:

Wählen Sie links im Menü die Option **Einstellungen** . Notieren Sie sich Ihre **ARBEITSBEREICH-ID**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Wählen Sie oben im Menü die Option **Authorization Tokens**, und notieren Sie sich Ihr **Primary Authorization Token**.![3](./media/linux-dsvm-walkthrough/workspace-token.png)

Laden Sie das **AzureML** -Paket, und legen Sie dann Werte der Variablen mit Ihrem Token und der Arbeitsbereich-ID in der R-Sitzung auf der DSVM fest:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Wir vereinfachen das Modell, um die Implementierung dieser Demonstration zu erleichtern. Wählen Sie die drei Variablen in dem Entscheidungsbaum aus, der sich dem Stamm am nächsten befindet, und erstellen Sie nur mit diesen drei Variablen einen neuen Baum:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Wir benötigen eine Vorhersagefunktion, bei der die Features als Eingabe verwendet und die vorhergesagten Werte zurückgegeben werden:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


Veröffentlichen Sie die Funktion „predictSpam“ für AzureML mit der Funktion **publishWebService** :

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Bei dieser Funktion wird die Funktion **predictSpam** verwendet und ein Webdienst mit dem Namen **spamWebService** mit definierten Ein- und Ausgaben erstellt. Außerdem werden Informationen zum neuen Endpunkt zurückgegeben.

Zeigen Sie Details zum zuletzt veröffentlichten Webdienst, z.B. den zugehörigen API-Endpunkt und die Zugriffsschlüssel, mit dem folgenden Befehl an:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Probieren Sie dies für die ersten zehn Zeilen des Testsatzes aus:

    consume(ep, smallTestSet[1:10, ])


## <a name="use-other-tools-available"></a>Verwenden von anderen verfügbaren Tools
In den restlichen Abschnitten wird gezeigt, wie Sie die Tools verwenden, die auf der Linux Data Science-VM installiert sind. Hier ist eine Liste mit den beschriebenen Tools angegeben:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL und Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) ist ein Tool, das eine schnelle und präzise Boosted Tree-Implementierung ermöglicht.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost kann einen Aufruf auch über Python oder eine Befehlszeile durchführen.

## <a name="python"></a>Python
Für die Entwicklung mit Python wurden die Anaconda Python-Distributionen 2.7 und 3.5 auf der DSVM installiert.

> [!NOTE]
> Die Anaconda-Distribution enthält [Conda](https://conda.pydata.org/docs/index.html). Dieses System kann zum Erstellen von benutzerdefinierten Umgebungen für Python verwendet werden, für die verschiedene Versionen bzw. Pakete installiert sind.
>
>

Wir lesen nun einen Teil des Datasets „spambase“ ein und klassifizieren die E-Mails mit Support Vector Machines in „scikit-learn“:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Vorhersagen können Sie wie folgt erstellen:

    clf.predict(X.ix[0:20, :])

Um die Veröffentlichung eines AzureML-Endpunkts zu veranschaulichen, erstellen wir mit den drei Variablen ein einfacheres Modell als beim obigen Veröffentlichen des R-Modells.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Gehen Sie wie folgt vor, um das Modell für AzureML zu veröffentlichen:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Diese Vorgehensweise ist nur für Python 2.7 verfügbar und wird für Version 3.5 noch nicht unterstützt. Verwenden Sie **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>JupyterHub
Die Anaconda-Distribution auf der DSVM verfügt über ein Jupyter Notebook, eine plattformübergreifende Umgebung zum gemeinsamen Nutzen von Python-, R- oder Julia-Code und Analysefunktionen. Auf Jupyter Notebook wird über JupyterHub zugegriffen. Sie melden sich mit Ihrem lokalen Linux-Benutzernamen und dem dazugehörigen Kennwort unter ***https://\<VM-DNS-Name oder IP-Adresse\>:8000/*** an. Alle Konfigurationsdateien für JupyterHub befinden sich im Verzeichnis **/etc/jupyterhub**.

> [!NOTE]
> Wenn Sie in Jupyter Notebook im aktuellen Kernel den Python-Paket-Manager (über den Befehl `pip`) verwenden möchten, kann der folgende Befehl in einer Codezelle verwendet werden. Beispiel:
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 
> 
> [!NOTE]
> Wenn Sie in Jupyter Notebook im aktuellen Kernel das Conda-Installationsprogramm Manager (über den Befehl `conda`) verwenden möchten, kann der folgende Befehl in einer Codezelle verwendet werden. Beispiel:
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Einige Beispiele für Notebooks sind auf der VM bereits installiert:

* Unter [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) finden Sie ein Beispiel für ein Python Notebook.
* Ein Beispiel für ein [R](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) Notebook finden Sie unter **IntroTutorialinR** (Tutorial: Einführung in R).
* Unter [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) finden Sie ein weiteres Beispiel für ein **Python** Notebook.

> [!NOTE]
> Die Julia-Sprache ist auch über die Befehlszeile auf der Linux Data Science-VM verfügbar.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (R Analytical Tool To Learn Easily) ist ein grafisches R-Tool für Data Mining-Aufgaben. Es verfügt über eine intuitive Benutzeroberfläche, die Ihnen das Laden, Untersuchen und Transformieren von Daten und das Erstellen und Auswerten von Modellen erleichtert.  Der Artikel [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) (Rattle: Data Mining-GUI für R) enthält eine exemplarische Vorgehensweise zur Veranschaulichung der Features.

Sie können Rattle mit den folgenden Befehlen installieren und starten:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Auf der DSVM ist keine Installation erforderlich. Sie werden von Rattle beim Laden aber unter Umständen aufgefordert, weitere Pakete zu installieren.
>
>

Die Benutzeroberfläche von Rattle basiert auf Registerkarten. Die meisten Registerkarten entsprechen den Schritten unter [Data Science-Prozess](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), z.B. das Laden oder Untersuchen von Daten. Der Data Science-Prozess verläuft von links nach rechts durch die Registerkarten. Die letzte Registerkarte enthält dann ein Protokoll mit den R-Befehlen, die von Rattle ausgeführt wurden.

Gehen Sie wie folgt vor, um das Dataset zu laden und zu konfigurieren:

* Wählen Sie die Registerkarte **Data** (Daten), um die Datei zu laden.
* Wählen Sie im Auswahlbereich neben **Filename** (Dateiname) die Option **spambaseHeaders.data**.
* Um die Datei zu laden, wählen Sie in der oberen Zeile mit den Schaltflächen die Option **Execute** (Ausführen) aus. der identifizierte Datentyp, der Typ der Variablen (Eingabe, Ziel usw.) und die Anzahl von eindeutigen Werten.
* Rattle hat die Spalte **spam** richtig als Ziel identifiziert. Wählen Sie die Spalte „spam“ aus, und legen Sie **Target Data Type** (Zieldatentyp) auf **Categoric** (Kategorisch) fest.

Gehen Sie wie folgt vor, um die Daten zu untersuchen:

* Wählen Sie die Registerkarte **Explore** (Untersuchen).
* Klicken Sie auf **Summary** (Zusammenfassung) und dann auf **Execute** (Ausführen), um Informationen zu den Variablentypen und einige zusammenfassende Statistiken anzuzeigen.
* Wählen Sie andere Optionen wie **Describe** (Beschreiben) oder **Basics** (Grundlagen) aus, um andere Arten von Statistiken zu jeder Variablen anzuzeigen.

Auf der Registerkarte **Explore** (Untersuchen) können Sie außerdem viele Plots mit nützlichen Informationen generieren. Gehen Sie wie folgt vor, um ein Histogramm mit den Daten zu plotten:

* Wählen Sie **Distributions**(Verteilungen).
* Überprüfen Sie **Histogram** (Histogramm) auf **word_freq_remove** und **word_freq_you**.
* Wählen Sie **Execute**(Ausführen). Beide Dichteplotergebnisse werden zusammen in einem Graphfenster angezeigt, und es wird deutlich, dass das Wort „you“ in E-Mails viel häufiger als das Wort „remove“ enthalten ist.

Die Plots vom Typ „Correlation“ (Korrelation) sind auch interessant. Sie erstellen sie wie folgt:

* Wählen Sie unter **Type** (Typ) die Option **Correlation** (Korrelation).
* Wählen Sie **Execute**(Ausführen).
* Rattle warnt Sie, dass maximal 40 Variablen verwendet werden sollten. Wählen Sie **Yes** (Ja), um das Plotergebnis anzuzeigen.

Einige interessante Korrelationen werden angezeigt: Beispielsweise korreliert „technology“ stark mit „HP“ und „labs“. Außerdem besteht eine starke Korrelation mit „650“, da die Ortskennzahl der „Spender“ im Dataset 650 lautet.

Die numerischen Werte für die Korrelationen zwischen Wörtern sind im Fenster „Explore“ (Untersuchen) verfügbar. Es ist beispielsweise interessant, dass „technology“ für „your“ und „money“ eine negative Korrelation aufweist.

Rattle kann das Dataset transformieren, um einige häufig auftretende Probleme zu behandeln. Beispielsweise können Sie Features neu skalieren, fehlende Werte zuordnen, Ausreißer verarbeiten und Variablen oder Beobachtungen mit fehlenden Daten entfernen. Außerdem kann Rattle Zuordnungsregeln zwischen Beobachtungen und/oder Variablen identifizieren. Diese Registerkarten werden in dieser einführenden exemplarischen Vorgehensweise nicht behandelt.

Rattle kann auch eine Clusteranalyse durchführen. Wir schließen einige Features aus, damit die Ausgabe einfacher gelesen werden kann. Wählen Sie auf der Registerkarte **Data** (Daten) die Option **Ignore** (Ignorieren) neben allen Variablen, mit Ausnahme der folgenden zehn Elemente:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Wechseln Sie zurück zur Registerkarte **Cluster** , wählen Sie **KMeans**, und legen Sie die Option *Number of clusters* (Anzahl von Clustern) auf 4 fest. Klicken Sie anschließend auf **Execute**(Ausführen). Die Ergebnisse werden im Ausgabefenster angezeigt. Ein Cluster weist eine hohe Häufigkeit für „george“ und „hp“ auf und ist vermutlich eine legitime geschäftliche E-Mail.

Gehen Sie wie folgt vor, um ein einfaches Machine Learning-Modell mit Entscheidungsbaum zu erstellen:

* Wählen Sie die Registerkarte **Model** (Modell).
* Wählen Sie unter **Type** (Typ) die Option **Tree** (Baum) aus.
* Wählen Sie die Option **Execute** (Ausführen), um den Baum im Ausgabefenster in Textform anzuzeigen.
* Wählen Sie die Schaltfläche **Draw** (Zeichnen), um eine grafische Version anzuzeigen. Diese weist eine starke Ähnlichkeit mit dem Baum auf, den wir zuvor mit *rpart*beschafft haben.

Eines der sehr hilfreichen Features von Rattle ist die Möglichkeit, mehrere Machine Learning-Methoden auszuführen und schnell auszuwerten. Das Verfahren lautet wie folgt:

* Wählen Sie unter **Type** (Typ) die Option **All** (Alle).
* Wählen Sie **Execute**(Ausführen).
* Nach Abschluss der Ausführung können Sie auf einen einzelnen **Type**-Eintrag klicken, z.B. **SVM**, und die Ergebnisse anzeigen.
* Außerdem können Sie die Leistung der Modelle im Validierungssatz mit der Registerkarte **Evaluate** (Auswerten) vergleichen. In der Auswahl **Error Matrix** (Fehlermatrix) werden beispielsweise die Wahrheitsmatrix, der Gesamtfehler und der durchschnittliche Klassenfehler für jedes Modell des Validierungssatzes angezeigt.
* Sie können auch ROC-Kurven plotten, Empfindlichkeitsanalysen durchführen und andere Formen der Modellevaluierung nutzen.

Wählen Sie nach Abschluss der Modellerstellung die Registerkarte **Log** (Protokoll), um den R-Code anzuzeigen, der von Rattle während Ihrer Sitzung ausgeführt wird. Sie können die Schaltfläche **Export** (Exportieren) wählen, um diese Daten zu speichern.

> [!NOTE]
> Die aktuelle Version von Rattle enthält einen Fehler. Um das Skript zu ändern oder es zum Wiederholen der Schritte zu einem späteren Zeitpunkt zu verwenden, müssen Sie vor *Export this log ...* im Text des Protokolls das Zeichen „#“ einfügen.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL und Squirrel SQL
Auf der DSVM ist PostgreSQL vorinstalliert. PostgreSQL ist eine anspruchsvolle relationale Open-Source-Datenbank. In diesem Abschnitt wird veranschaulicht, wie Sie das Dataset „spam“ in PostgreSQL laden und dann abfragen.

Vor dem Laden der Daten müssen Sie die Kennwortauthentifizierung über den localhost zulassen. Geben Sie an der Eingabeaufforderung Folgendes ein:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Unten in der Config-Datei sind mehrere Zeilen mit Informationen zu den zulässigen Verbindungen enthalten:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Ändern Sie die Zeile „IPv4 local connections“, um „md5“ anstelle von „ident“ zu verwenden, damit die Anmeldung mit einem Benutzernamen und Kennwort möglich ist:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Starten Sie den postgres-Dienst neu:

    sudo systemctl restart postgresql

Führen Sie zum Starten von „psql“ (einem interaktiven Terminal für PostgreSQL) als integrierter postgres-Benutzer an einer Eingabeaufforderung den folgenden Befehl aus:

    sudo -u postgres psql

Erstellen Sie ein neues Benutzerkonto mit dem gleichen Namen für das Linux-Konto, mit dem Sie gerade angemeldet sind, und vergeben Sie ein Kennwort dafür:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Melden Sie sich dann als dieser Benutzer an „psql“ an:

    psql

Importieren Sie die Daten in eine neue Datenbank:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Wir sehen uns nun die Daten an und führen einige Abfragen mit **Squirrel SQL** aus. Hierbei handelt es sich um ein grafisches Tool, mit dem Sie über einen JDBC-Treiber mit Datenbanken interagieren können.

Starten Sie Squirrel SQL über das Menü „Applications“ (Anwendungen), um zu beginnen. Richten Sie den Treiber ein:

* Wählen Sie **Windows** und dann **View Drivers** (Treiber anzeigen).
* Klicken Sie mit der rechten Maustaste auf **PostgreSQL**, und wählen Sie die Option **Modify Driver** (Treiber ändern).
* Wählen Sie **Extra Class Path** (Zusätzlicher Klassenpfad) und dann **Add** (Hinzufügen).
* Geben Sie unter **File Name** (Dateiname) den Text ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** ein.
* Wählen Sie **Open**(Öffnen).
* Wählen Sie „List Drivers“ (Treiber auflisten) und dann unter **Class Name** (Klassenname) die Option **org.postgresql.Driver**. Klicken Sie anschließend auf **OK**.

Gehen Sie wie folgt vor, um die Verbindung mit dem lokalen Server einzurichten:

* Wählen Sie **Windows** und dann **View Aliases** (Aliase anzeigen).
* Wählen Sie die Schaltfläche **+** , um einen neuen Alias zu erstellen.
* Geben Sie ihm den Namen *Spam database*, und wählen Sie in der Dropdownliste **Driver** (Treiber) die Option **PostgreSQL**.
* Legen Sie die URL auf *jdbc:postgresql://localhost/spam* fest.
* Geben Sie Ihren *Benutzernamen* und Ihr *Kennwort* ein.
* Klicken Sie auf **OK**.
* Doppelklicken Sie zum Öffnen des Fensters **Connection** (Verbindung) auf den Alias ***Spam database***.
* Wählen Sie **Verbinden**aus.

Führen Sie einige Abfragen aus:

* Wählen Sie die Registerkarte **SQL** (SQL).
* Geben Sie eine einfache Abfrage, z.B. `SELECT * from data;`, in das Abfragetextfeld oben auf der Registerkarte „SQL“ ein.
* Drücken Sie **STRG+EINGABE** , um die Abfrage auszuführen. Standardmäßig gibt Squirrel SQL die ersten 100 Zeilen für die Abfrage zurück.

Es gibt noch viele weitere Abfragen, die Sie ausführen können, um diese Daten zu untersuchen. Wie unterscheidet sich die Häufigkeit des Worts *make* beispielsweise für „Spam“ und „Ham“?

    SELECT avg(word_freq_make), spam from data group by spam;

Oder: Wie lauten die Merkmale von E-Mails, die häufig den Ausdruck *3d*enthalten?

    SELECT * from data order by word_freq_3d desc;

Die meisten E-Mails mit einer hohen Häufigkeit von *3d* sind offensichtlich Spam, sodass dies ein guter Ansatzpunkt zum Erstellen eines Vorhersagemodells wäre, um die E-Mails zu klassifizieren.

Wenn Sie einen Machine Learning-Vorgang mit Daten durchführen möchten, die in einer PostgreSQL-Datenbank gespeichert sind, können Sie auch [MADlib](https://madlib.incubator.apache.org/)nutzen.

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse ist eine cloudbasierte Datenbank für das horizontale Hochskalieren, mit der sehr große Datenvolumen verarbeitet werden können, und zwar sowohl relational als auch nicht relational. Weitere Informationen finden Sie unter [Was ist Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Führen Sie an einer Eingabeaufforderung den folgenden Befehl aus, um eine Verbindung mit dem Data Warehouse herzustellen und die Tabelle zu erstellen:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

An der sqlcmd-Eingabeaufforderung:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Kopieren Sie Daten mit bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Die Zeilenenden in der heruntergeladenen Datei haben Windows-Format, und bcp erwartet das UNIX-Format. Wir müssen dies bcp also mit dem Flag „-r“ mitteilen.
>
>

Und eine Abfrage mit sqlcmd durchführen:

    select top 10 spam, char_freq_dollar from spam;
    GO

Sie können für die Abfrage auch Squirrel SQL nutzen. Die Schritte für PostgreSQL sind ähnlich. Verwenden Sie den Microsoft MSSQL Server-JDBC-Treiber unter ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Nächste Schritte
Eine Übersicht zu Themen, die Sie durch die Aufgaben führen, die den Data Science-Prozess in Azure umfassen, finden Sie unter [Team Data Science-Prozess (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Eine Beschreibung der anderen exemplarischen End-to-End-Vorgehensweisen, in denen die Schritte im Team Data Science-Prozess für bestimmte Szenarien veranschaulicht werden, finden Sie unter [Exemplarische Vorgehensweisen für den Team Data Science-Prozess](../team-data-science-process/walkthroughs.md). Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden.
