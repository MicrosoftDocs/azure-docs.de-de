---
title: 'ML Studio (Classic) – Tutorial 3: Bereitstellen von Kreditrisikomodellen – Azure'
description: Ein ausführliches Tutorial zum Erstellen einer Predictive Analytics-Lösung für die Kreditrisikobewertung in Azure Machine Learning Studio (klassisch) Dieses Tutorial ist der dritte Teil einer dreiteiligen Reihe. Hier erfahren Sie, wie Sie ein Modell als Webdienst bereitstellen.
keywords: Kreditrisiko, Predictive Analytics-Lösung, Risikobewertung, bereitstellen, Webdienst
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 3afcf87c360651ac314450910fbf5ab72afd289a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503915"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Tutorial 3: Bereitstellen eines Kreditrisikomodells – Azure Machine Learning Studio (klassisch)

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Dieses Tutorial befasst sich eingehend mit der Entwicklung einer Predictive Analytics-Lösung. Hierzu wird in Machine Learning Studio (klassisch) ein einfaches Modell entwickelt.  Anschließend wird das Modell als Azure Machine Learning-Webdienst bereitgestellt.  Dieses bereitgestellte Modell kann auf der Grundlage neuer Daten Vorhersagen generieren. Dieses Tutorial ist der **dritte Teil einer dreiteiligen Reihe**.

Stellen Sie sich vor, Sie müssen das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag vorhersagen.  

Die Bewertung des Kreditrisikos ist allerdings ein komplexes Problem und wurde daher in diesem Tutorial etwas vereinfacht. Diese Aufgabenstellung dient als Beispiel dafür, wie Sie eine Predictive Analytics-Lösung mit Microsoft Azure Machine Learning Studio (klassisch) erstellen können. Für diese Lösung werden Azure Machine Learning Studio (klassisch) und ein Machine Learning-Webdienst verwendet. 

In diesem dreiteiligen Tutorial werden zunächst öffentlich verfügbare Kreditrisikodaten verwendet.  Als Nächstes entwickeln und trainieren Sie ein Vorhersagemodell.  Abschließend stellen Sie das Modell als Webdienst bereit.

Im [ersten Teil des Tutorials](tutorial-part1-credit-risk.md) haben Sie einen Arbeitsbereich in Machine Learning Studio (klassisch) erstellt, Daten hochgeladen und ein Experiment erstellt.

Im [zweiten Teil des Tutorials](tutorial-part2-credit-risk-train.md) haben Sie Modelle trainiert und ausgewertet.

In diesem Teil des Tutorials führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten der Bereitstellung
> * Bereitstellen des Webdiensts
> * Testen des Webdiensts
> * Verwalten des Webdiensts
> * Zugreifen auf den Webdienst

## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie den [zweiten Teil des Tutorials](tutorial-part2-credit-risk-train.md) ab.

## <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung
Um anderen die Möglichkeit zu geben, das Vorhersagemodell zu nutzen, das Sie in diesem Tutorial entwickelt haben, können Sie es in Azure als Webdienst bereitstellen.

Bis jetzt haben Sie mit dem Trainieren des Modells experimentiert. Der bereitgestellte Dienst führt jedoch kein Training mehr durch, sondern generiert neue Vorhersagen durch Bewerten der Benutzereingaben basierend auf unserem Modell. Wir führen einige vorbereitende Schritte durch, um dieses Experiment von einem ***Trainingsexperiment** _ in ein _*_Vorhersageexperiment_*_ umzuwandeln. 

Die Bereitstellungsvorbereitung umfasst drei Schritte:  

1. Entfernen eines der Modelle
1. Konvertieren des erstellten _Trainingsexperiments* in ein *Vorhersageexperiment*
1. Bereitstellung des Vorhersageexperiments als Webdienst

### <a name="remove-one-of-the-models"></a>Entfernen eines der Modelle

Zunächst muss das Experiment etwas optimiert werden. Aktuell enthält das Experiment noch zwei Modelle, doch nur eines davon soll als Webdienst bereitgestellt werden.  

Angenommen, Sie haben entschieden, dass das Boosted Tree-Modell eine bessere Leistung bietet als das SVM-Modell. Als Erstes müssen Sie nun das Modul [Two-Class Support Vector Machine][two-class-support-vector-machine] sowie die Module entfernen, mit denen dieses Modul trainiert wurde. Am besten erstellen Sie zunächst eine Kopie des Experiments, indem Sie unten in der Canvas des Experiments auf **Save As** klicken.

Folgende Module müssen gelöscht werden:  

* [Zweiklassige Support Vector Machine][two-class-support-vector-machine]
* Die damit verbundenen Module [Train Model][train-model] und [Score Model][score-model]
* [Normalize Data][normalize-data] (beide)
* [Evaluate Model][evaluate-model] (da wir das Bewerten der Modelle abgeschlossen haben)

Wählen Sie jedes Modul aus, und drücken Sie die ENTF-Taste, oder klicken Sie mit der rechten Maustaste auf das Modul, und wählen Sie **Delete** aus. 

![Markierung, welche Module gelöscht werden sollen, um das Support Vector Machine-Modell (Stützvektormethode) zu entfernen](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Unser Modell sollte jetzt etwa wie folgt aussehen:

![Resultierendes Experiment, wenn das Support Vector Machine-Modell gelöscht wurde](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Jetzt sind wir bereit, dieses Modells mit dem Modul [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] bereitzustellen.

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertieren des Trainingsexperiments in ein Vorhersageexperiment

Um dieses Modell auf die Bereitstellung vorzubereiten, muss das Trainingsexperiment in ein Vorhersageexperiment umgewandelt werden. Dies umfasst drei Schritte:

1. Speichern des trainierten Modells und Ersetzen der Trainingsmodule
1. Optimieren des Experiments, um Module zu entfernen, die nur zu Trainingszwecken benötigt wurden
1. Festlegen, wo der Webdienst Eingaben akzeptiert und wo er Ausgaben generiert

Dieser Prozess könnte zwar auch manuell ausgeführt werden, glücklicherweise können Sie aber alle drei Schritte ausführen, indem Sie einfach unten im Experimentbereich auf **Set Up Web Service** (Webdienst einrichten) klicken und die Option **Predictive Web Service** (Vorhersagewebdienst) auswählen.

> [!TIP]
> Wenn Sie weitere Details dazu erhalten möchten, was geschieht, wenn Sie ein Trainingsexperiment in ein Vorhersageexperiment konvertieren, lesen Sie [Vorbereiten der Bereitstellung Ihres Modells in Azure Machine Learning Studio (klassisch)](deploy-a-machine-learning-web-service.md).

Beim Klicken auf **Set Up Web Service** geschehen mehrere Dinge:

* Das trainierte Modell wird in ein einzelnes **Trained Model**-Modul konvertiert und in der Modulpalette gespeichert, die sich links neben dem Experimentbereich befindet. (Sie finden sie unter **Trained Models**.)
* Module, die zum Training verwendet wurden, werden entfernt. Das gilt insbesondere für:
  * [Verstärkter Entscheidungsbaum mit zwei Klassen][two-class-boosted-decision-tree]
  * [Train Model][train-model] (Modell trainieren)
  * [Aufteilen von Daten][split]
  * Das zweite [Execute R Script][execute-r-script]-Modul, das für die Testdaten verwendet wurde
* Das gespeicherte trainierte Modell wird dem Experiment wieder hinzugefügt
* Die Module **Web service input** und **Web service output** werden hinzugefügt. (Sie dienen zum Bestimmen, wo die Daten des Benutzers in das Modell eingehen und welche Daten zurückgegeben werden, wenn auf den Webdienst zugegriffen wird.)

> [!NOTE]
> Sie können erkennen, dass das Experiment in zwei Teilen unter Registerkarten gespeichert wird, die oben im Experimentbereich hinzugefügt wurden. Das ursprüngliche Trainingsexperiment befindet sich unter der Registerkarte **Training Experiment**. Das neu erstellte Vorhersageexperiment befindet sich unter **Predictive Experiment**. Sie möchten das Vorhersageexperiment als Webdienst bereitstellen.

Bei diesem Experiment ist ein zusätzlicher Schritt erforderlich.
Sie haben zwei Module vom Typ [Execute R Script][execute-r-script] hinzugefügt, um die Daten mit einer Gewichtungsfunktion zu versehen. Das war nur ein Trick, der zum Trainieren und Testen erforderlich war. Die Module können nun also aus dem endgültigen Modell entfernt werden.
Machine Learning Studio (klassisch) hat beim Entfernen des [Split][split]-Moduls eines der [Execute R Script][execute-r-script]-Module entfernt. Nun können Sie auch das andere entfernen und [Metadata Editor][metadata-editor] direkt mit [Score Model][score-model] verbinden.    

Unser Experiment sollte nun wie folgt aussehen:  

![Bewerten des trainierten Modells](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> Sie fragen sich vielleicht, warum der Datensatz „UCI German Credit Card Data“ im Vorhersageexperiment verblieben ist. Der Dienst bewertet die Daten des Benutzers, nicht das Originaldataset. Weshalb also das Originaldataset im Modell belassen?
> 
> Es stimmt zwar, dass der Dienst die Originalkreditkartendaten nicht benötigt. Er benötigt aber das Schema für diese Daten, darunter Angaben zur Anzahl der vorhandenen Spalten, und welche Spalten numerisch sind. Diese Schemainformationen sind erforderlich, um die Benutzerdaten zu interpretieren. Die Verbindung dieser Komponenten wird beibehalten, damit das Bewertungsmodul über das Datasetschema verfügt, wenn der Dienst ausgeführt wird. Es werden nicht die Daten verwendet, sondern nur das Schema.  
> 
>Es ist wichtig zu beachten, dass das erwartete Schema aus der Webeingabe auch eine Spalte mit der Bezeichnung erwartet, wenn Ihr ursprüngliches Dataset die Bezeichnung enthielt! Eine Umgehung dieses Problems besteht darin, die Bezeichnung und alle anderen Daten zu entfernen, die sich im Trainingsdataset befanden, jedoch nicht in den Webeingaben enthalten sein werden, bevor Sie die Webeingabe und das Trainingsdataset in einem allgemeinen Modul verbinden. 
> 

Führen Sie das Experiment ein letztes Mal aus (klicken Sie auf **Run**). Wenn Sie überprüfen möchten, ob das Modell noch funktioniert, klicken Sie auf die Ausgabe des Moduls [Score Model][score-model] und wählen **Ergebnisse anzeigen** aus. Sie sehen, dass die Originaldaten zusammen mit dem Wert für das Kreditrisiko („Bewertete Beschriftungen“)' und dem Bewertungswahrscheinlichkeitswert („Bewertete Wahrscheinlichkeiten“) angezeigt werden. 

## <a name="deploy-the-web-service"></a>Bereitstellen des Webdiensts
Sie können das Experiment entweder als klassischen Webdienst oder neuen auf Azure Resource Manager basierenden Webdienst bereitstellen.

### <a name="deploy-as-a-classic-web-service"></a>Bereitstellen als klassischen Webdienst
Zum Bereitstellen eines aus dem Experiment abgeleiteten klassischen Webdiensts klicken Sie unter dem Experimentbereich auf **Deploy Web Service** und wählen dann **Deploy Web Service [Classic]** . Machine Learning Studio (klassisch) stellt das Experiment als Webdienst bereit und führt Sie zum Dashboard dieses Webdiensts. Von hier aus können Sie zum Experiment zurückkehren (**Momentaufnahme anzeigen** oder **View latest** (Neuestes anzeigen)) und einen einfachen Test des Webdiensts ausführen (siehe **Testen des Webdiensts** weiter unten). Dort befinden sich auch Informationen zum Erstellen von Anwendungen, die auf den Webdienst zugreifen können (mehr dazu im nächsten Schritt dieses Tutorials).

![Dashboard des Webdiensts](./media/tutorial-part3-credit-risk-deploy/publish6.png)


Sie können den Dienst konfigurieren, indem Sie auf die Registerkarte **KONFIGURATION** klicken. Hier können Sie den Dienstnamen ändern (er erhält standardmäßig den Namen des Experiments) und eine Beschreibung hinzufügen. Sie können auch benutzerfreundlichere Beschriftungen für die Eingabe- und Ausgabedaten festlegen.  

![Konfigurieren des Webdiensts](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Bereitstellen als neuer Webdienst

> [!NOTE] 
> Zum Bereitstellen eines neuen Webdiensts müssen Sie über ausreichende Berechtigungen in dem Abonnement verfügen, im dem Sie den Webdienst bereitstellen. Weitere Informationen finden Sie unter [Verwalten eines Webdiensts im Azure Machine Learning Web Services-Portal](manage-new-webservice.md). 

So stellen wir einen aus unserem Experiment abgeleiteten neuen Webdienst bereit

1. Klicken Sie unter dem Experimentbereich auf **Deploy Web Service**, und wählen Sie **Deploy Web Service [New]** . Machine Learning Studio (klassisch) leitet Sie zur Seite **Deploy Experiment** (Experiment bereitstellen) von Azure Machine Learning-Webdienste weiter.

1. Geben Sie einen Namen für den Webdienst ein. 

1. Für **Price Plan** können Sie einen vorhandenen Tarif auswählen. Oder wählen Sie „Create new“, geben Sie dem neuen Tarif einen Namen, und wählen Sie die Tarifoption „Monthly“. Die Plantarife sind standardmäßig auf die Pläne für Ihre Standardregion festgelegt, und Ihr Webdienst wird in dieser Region bereitgestellt.

1. Klicken Sie auf **Bereitstellen**.

Die Seite **Quickstart** für Ihren Webdienst wird nach ein paar Minuten geöffnet.

Sie können den Dienst konfigurieren, indem Sie auf die Registerkarte **Configure** klicken. Hier können Sie den Titel des Diensts ändern und ihm eine Beschreibung geben. 

Um den Webdienst zu testen, klicken Sie auf die Registerkarte **Test** (siehe **Testen des Webdiensts** unten). Informationen zum Erstellen von Anwendungen, die auf den Webdienst zugreifen können, erhalten Sie durch Klicken auf die Registerkarte **Consume** (Nutzen) (mehr dazu im nächsten Schritt dieses Tutorials).

> [!TIP]
> Sie können den Webdienst nach der Bereitstellung aktualisieren. Wenn Sie das Modell ändern möchten, bearbeiten Sie einfach die Modellparameter im Trainingsexperiment und klicken auf **Deploy Web Service**. Wählen Sie **Deploy Web Service [Classic]** oder **Deploy Web Service [New]** . Wenn Sie das Experiment erneut bereitstellen, wird der Webdienst ersetzt und das aktualisierte Modell verwendet.  
> 
> 

## <a name="test-the-web-service"></a>Testen des Webdiensts

Nach dem Zugriff auf den Webdienst durchlaufen die Daten des Benutzers das Modul **Web Service Input**, aus dem sie an das Modul [Score Model][score-model] übergeben und bewertet werden. Aufgrund der Konfiguration des Vorhersageexperiments erwartet das Modell Daten in einem Format, das dem des ursprünglichen Kreditrisiko-Datasets entspricht.
Die Ergebnisse werden danach vom Webdienst über das Modul **Web service output** an den Benutzer zurückgegeben.

> [!TIP]
> Aufgrund der Konfiguration des Vorhersageexperiments gibt das Modul [Score Model][score-model] sämtliche Ergebnisse zurück. Dies schließt alle Eingabedaten sowie den Kreditrisikowert und die Bewertungswahrscheinlichkeit ein. Doch Sie können nach Wunsch etwas anderes zurückgeben, z.B. nur den Kreditrisikowert. Fügen Sie hierzu ein [Select Columns][select-columns]-Modul zwischen [Score Model][score-model] und **Web service output** ein, um Spalten auszuschließen, die der Webdienst nicht zurückgeben soll. 
> 
> 

Sie können einen klassischen Webdienst entweder in **Machine Learning Studio (klassisch)** oder im Portal der **Azure Machine Learning-Webdienste** testen.
Einen neuen Webdienst können Sie nur im **Machine Learning Web Services**-Portal testen.

> [!TIP]
> Beim Testen im Azure Machine Learning Web Services-Portal können Sie das Portal Beispieldaten erstellen lassen, mit deren Hilfe Sie den Request-Response-Dienst testen können. Wählen Sie auf der Seite **Configure** „Yes“ für **Sample Data Enabled?** aus. Beim Öffnen der Registerkarte „Request-Response“ auf der Seite **Test** füllt das Portal die Beispieldaten aus, die dem ursprünglichen Dataset „Credit Risk“ entnommen wurden.

### <a name="test-a-classic-web-service"></a>Testen eines klassischen Webdiensts

Sie können einen klassischen Webdienst in Machine Learning Studio (klassisch) oder im Portal der Machine Learning-Webdienste testen. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testen in Machine Learning Studio (klassisch)

1. Klicken Sie auf der Seite **DASHBOARD** des Webdiensts unter **Default Endpoint** auf die Schaltfläche **Test**. Ein Dialogfeld wird geöffnet, in dem nach den Eingabedaten für den Dienst gefragt werden. Dies sind die gleichen Spalten, die im Originaldataset „Credit Risk“ angezeigt wurden.  

1. Geben Sie eine Reihe von Daten ein, und klicken Sie auf **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testen im Machine Learning Web Services-Portal

1. Klicken Sie auf der Seite **DASHBOARD** des Webdiensts unter **Default Endpoint** auf den Link **Test preview**. Die Testseite im Azure Machine Learning Web Services-Portal für den Webdienstendpunkt wird geöffnet und fordert Sie zur Angabe der Eingabedaten für den Dienst auf. Dies sind die gleichen Spalten, die im Originaldataset „Credit Risk“ angezeigt wurden.

2. Klicken Sie auf **Test Request-Response**. 

### <a name="test-a-new-web-service"></a>Testen eines neuen Webdiensts

Einen neuen Webdienst können Sie nur im Machine Learning Web Services-Portal testen.

1. Klicken Sie im [Azure Machine Learning Web Services](https://services.azureml.net/quickstart)-Portal oben auf der Seite auf **Test**. Die Seite **Test** wird geöffnet, auf der Sie Daten für den Dienst eingeben können. Die angezeigten Eingabefelder entsprechen den Spalten im Originaldataset „Credit Risk“. 

1. Geben Sie eine Reihe von Daten ein, und klicken Sie auf **Test Request-Response**.

Die Ergebnisse des Tests werden rechts auf der Seite in der Ausgabespalte angezeigt. 


## <a name="manage-the-web-service"></a>Verwalten des Webdiensts

Nachdem Sie Ihren neuen oder klassischen Webdienst bereitgestellt haben, können Sie ihn im [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)-Portal verwalten.

So überwachen Sie die Leistung des Webdiensts

1. Melden Sie sich beim [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart)-Portal an.
1. Klicken Sie auf **Web Services**.
1. Klicken Sie auf Ihren Webdienst.
1. Klicken Sie auf das **Dashboard**.

## <a name="access-the-web-service"></a>Zugreifen auf den Webdienst

Im vorherigen Schritt in dieses Tutorials haben Sie einen Webdienst bereitgestellt, der Ihr Vorhersagemodell für Kreditrisiken verwendet. Jetzt sind Benutzer in der Lage, Daten an ihn zu senden und Ergebnisse zu erhalten. 

Der Webdienst ist ein Azure-Webdienst, der Daten auf eine von zwei Arten über REST-APIs empfangen und zurückgeben kann:  

* **Anfrage/Antwort:** Der Benutzer sendet über das HTTP-Protokoll eine oder mehrere Zeilen Kreditdaten an den Dienst, und dieser antwortet mit einem oder mehreren Sätzen von Ergebnissen.
* **Batchausführung:** Der Benutzer speichert Zeilen von Kreditdaten in einem Azure-Blob und sendet den Speicherorts des Blobs dann an den Dienst. Der Dienst bewertet alle Datenzeilen im Eingabeblob, speichert die Ergebnisse in einem anderen Blob und gibt die URL dieses Containers zurück. 

> [!NOTE]
> Featurespaltennamen in Studio (Classic) berücksichtigen die **Groß-/Kleinschreibung**. Stellen Sie sicher, dass Ihre Eingabedaten für den Aufruf des Webdiensts dieselben Spaltennamen wie im Trainingsdataset aufweisen.

Weitere Informationen zum Zugriff auf den Webdienst und zu seiner Nutzung finden Sie unter [Verwenden eines Azure Machine Learning-Webdiensts mit einer Web-App-Vorlage](./consume-web-services.md).



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Schritte ausgeführt:

> [!div class="checklist"]
> * Vorbereiten der Bereitstellung
> * Bereitstellen des Webdiensts
> * Testen des Webdiensts
> * Verwalten des Webdiensts
> * Zugreifen auf den Webdienst

Sie können auch eine benutzerdefinierte Anwendung entwickeln, die mithilfe von in den Programmiersprachen R, C# und Python bereitgestelltem Startcode auf den Webdienst zugreift.

> [!div class="nextstepaction"]
> [Nutzen eines Azure Machine Learning Studio-Webdiensts](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[metadata-editor]: /azure/machine-learning/studio-module-reference/edit-metadata
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset