---
title: Vorbereiten des Modells für die Bereitstellung
titleSuffix: Azure Machine Learning Studio
description: Sie erfahren, wie Sie das trainierte Modell für die Bereitstellung als Webdienst vorbereiten, indem Sie Ihr Machine Learning Studio-Trainingsexperiment in ein Vorhersageexperiment konvertieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: 2a318edada5cdc4124e221fdc8c441ab323a9289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751969"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Vorbereiten des Modells für die Bereitstellung in Azure Machine Learning Studio

Azure Machine Learning Studio bietet Ihnen die Tools, die Sie benötigen, um ein Vorhersageanalysenmodell zu entwickeln und dann zu operationalisieren, indem Sie es als Azure-Webdienst bereitstellen.

Dabei verwenden Sie Studio zum Erstellen eines als *Trainingsexperiment* bezeichneten Experiments, in dem Sie das Modell trainieren, bewerten und bearbeiten. Sobald Sie zufrieden sind, bereiten Sie das Modell auf die Bereitstellung vor, indem Sie Ihr Trainingsexperiment in ein *Vorhersageexperiment* konvertieren, das zum Bewerten von Benutzerdaten konfiguriert ist.

Ein Beispiel für diesen Vorgang finden Sie unter [Tutorial 1: Vorhersagen des Kreditrisikos](tutorial-part1-credit-risk.md).

Dieser Artikel bietet fundierte Einblicke in die Details des Vorgangs, mit dem ein Trainingsexperiment in ein Vorhersageexperiment konvertiert wird, und in die Bereitstellung dieses Vorhersageexperiments. Wenn Sie diese Details verstehen, können Sie lernen, wie Sie das bereitgestellte Modell so konfigurieren, dass es wirksamer ist.



## <a name="overview"></a>Übersicht 

Der Prozess der Konvertierung eines Trainingsexperiments in ein Vorhersageexperiment umfasst drei Schritte:

1. Ersetzen Sie die Machine Learning-Algorithmusmodule durch Ihr trainiertes Modell.
2. Reduzieren des Experiments auf die Module, die für die Bewertung erforderlich sind. Ein Trainingsexperiment enthält mehrere Module, die für das Training erforderlich sind, jedoch nicht mehr benötigt werden, nachdem das Modell entsprechend trainiert wurde.
3. Definieren Sie, wie Ihr Modell Daten des Webdienstbenutzers akzeptiert, und welche Daten zurückgegeben werden.

> [!TIP]
> In Ihrem Trainingsexperiment haben Sie Ihr Modell mit Ihren eigenen Daten trainiert und bewertet. Aber nach der Bereitstellung werden Benutzer neue Daten an Ihr Modell senden, und es wird Vorhersageergebnisse zurückgeben. Berücksichtigen Sie also bei der Konvertierung Ihres Trainingsexperiments in ein Vorhersageexperiment, um es für die Bereitstellung vorzubereiten, wie andere Benutzer das Modell verwenden werden.
> 
> 

## <a name="set-up-web-service-button"></a>Schaltfläche zur Webdiensteinrichtung
Klicken Sie nach dem Ausführen Ihres Experiments (unten im Experimentbereich auf **RUN** klicken) auf die Schaltfläche **Set Up Web Service** (Option **Predictive Web Service** wählen). **Set Up Web Service** führt für Sie die drei Schritte zum Konvertieren Ihres Trainingsexperiments in ein Vorhersageexperiment aus:

1. Damit wird Ihr trainiertes Modell im Abschnitt **Trained Models** der Modulpalette gespeichert (auf der linken Seite des Experimentbereichs). Dann werden der Machine Learning-Algorithmus und [Train Model][train-model]-Module durch das gespeicherte trainierte Modell ersetzt.
2. Ihr Experiment wird analysiert, und es werden Module entfernt, die eindeutig nur für das Training verwendet wurden und nicht mehr benötigt werden.
3. Die Module _Web service input_ und _Web service output_ werden in Standardspeicherorte Ihres Experiments eingefügt (diese Module akzeptieren Benutzerdaten und geben sie zurück).

Das folgende Experiment trainiert z. B. ein Boosted Decision Tree-Zwei-Klassen-Modell mit Beispiel-Erhebungsdaten:

![Trainingsexperiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Die Module in diesem Experiment führen im Wesentlichen vier verschiedene Funktionen aus:

![Modulfunktionen](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Wenn Sie dieses Trainingsexperiment in ein Vorhersageexperiment konvertieren, werden einige dieser Module nicht mehr benötigt oder dienen nun einem anderen Zweck:

* **Data**: Die Daten in diesem Beispieldataset werden nicht während der Bewertung verwendet. Der Benutzer des Webdiensts stellt die Daten für die Bewertung bereit. Die Metadaten aus dem DataSet, z. B. Datentypen, werden jedoch vom trainierten Modell verwendet. Daher müssen Sie das DataSet im Vorhersageexperiment beibehalten, damit diese Metadaten bereitgestellt werden können.

* **Prep**: Abhängig von den zum Bewerten übermittelten Benutzerdaten können diese Module zum Verarbeiten der eingehenden Daten erforderlich sein oder auch nicht. Die **Set Up Web Service**-Schaltfläche berührt diese nicht – Sie müssen entscheiden, wie sie mit Ihnen umgehen möchten.
  
    In diesem Beispiel könnten im Beispieldataset Werte fehlen, sodass ein [Clean Missing Data][clean-missing-data]-Modul für den Umgang mit ihnen einbezogen wurde. Darüber hinaus enthält das Beispieldataset Spalten, die zum Trainieren des Modells nicht benötigt werden. Daher wurde ein [Select Columns in Dataset][select-columns]-Modul einbezogen, um diese zusätzlichen Spalten aus dem Datenfluss auszuschließen. Wenn Sie wissen, dass in den zum Bewerten durch den Webdienst übermittelten Daten keine Werte fehlen, können Sie das Modul [Clean Missing Data][clean-missing-data] entfernen. Da das Modul [Select Columns in Dataset][select-columns] beim Festlegen der Datenspalten hilft, die das trainierte Modells erwartet, muss dieses Modul beibehalten werden.

* **Train** – Diese Module werden zum Trainieren des Modells verwendet. Wenn Sie auf **Set Up Web Service** klicken, werden diese Module durch ein einzelnes Modul ersetzt, das das von Ihnen trainierte Modell enthält. Dieses neue Modul wird im Abschnitt **Trained Models** der Modulpalette gespeichert.

* **Score**: In diesem Beispiel wird das Modul [Split Data][split] zum Unterteilen des Datenstroms in Testdaten und Trainingsdaten verwendet. Im Vorhersageexperiment trainieren wir nicht mehr, sodass [Split Data][split] entfernt werden kann. Ebenso werden das zweite Modul [Score Model][score-model] und das Modul [Evaluate Model][evaluate-model] verwendet, um Ergebnisse aus den Testdaten zu vergleichen, und daher im Vorhersageexperiment nicht benötigt. Das verbleibende Modul [Score Model][score-model] ist jedoch erforderlich, um über den Webdienst ein Bewertungsergebnis zurückzugeben.

So sieht das Beispiel nach dem Klicken auf **Webdienst einrichten**aus:

![Konvertiertes Vorhersageexperiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Die Arbeit von **Set Up Web Service** kann ausreichend sein, um das Experiment zur Bereitstellung als Webdienst vorzubereiten. Allerdings sollten Sie einige zusätzliche Aufgaben für das Experiment durchführen.

### <a name="adjust-input-and-output-modules"></a>Anpassen von Eingabe- und Ausgabemodulen
Im Trainingsexperiment haben Sie einen Satz von Trainingsdaten verwendet und dann durch Verarbeitung die Daten in einem Formular abgerufen, die für den Algorithmus für maschinelles Lernen erforderlich sind. Wenn für die voraussichtlich über den Webdienst empfangenen Daten keine Verarbeitung erforderlich ist, können Sie dies umgehen: stellen Sie die Verbindung der Ausgabe des Moduls **Web service input** mit einem anderen Knoten im Experiment her. Die Benutzerdaten werden jetzt im Modell an dieser Stelle eingehen.

Beispielsweise setzt **Set Up Web Service** standardmäßig das **Web service input**-Modul an den Beginn des Datenflusses, wie in der Abbildung oben dargestellt. Aber wir können das **Web service input**-Modul manuell hinter den Datenverarbeitungsmodulen positionieren:

![Verschieben des Web Service Input](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Die über den Webdienst bereitgestellten Eingabedaten werden jetzt ohne Vorverarbeitung direkt an das "Score Model"-Modul übergeben.

Auf ähnliche Weise setzt **Webdienst einrichten** standardmäßig das Webdienstausgabe-Modul an das Ende des Datenflusses. In diesem Beispiel gibt der Webdienst die Ausgabe des Moduls [Score Model][score-model], die den vollständigen Eingabedatenvektor sowie die Bewertungsergebnisse enthält, an den Benutzer zurück.
Falls Sie jedoch andere Daten zurückgeben möchten, können Sie zusätzliche Module vor dem Modul **Web Service Output** hinzufügen. 

Um beispielsweise nur die Bewertungsergebnisse und nicht den gesamten Eingabedatenvektor zurückzugeben, fügen Sie ein Modul [Select Columns in Dataset][select-columns] hinzu, um alle Spalten mit Ausnahme der Bewertungsergebnisse auszuschließen. Anschließend verschieben Sie das Modul **Web service output** zur Ausgabe des Moduls [Select Columns in Dataset][select-columns]. Das Experiment sieht wie folgt aus:

![Verschieben des Web Service Output](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Hinzufügen oder Entfernen zusätzlicher Datenverarbeitungsmodule
Wenn in Ihrem Experiment mehrere Module vorhanden sind, von denen Sie wissen, dass sie für die Bewertung nicht benötigt werden, können diese entfernt werden. Da wir das Modul **Web service input** an einen Punkt hinter den Datenverarbeitungsmodulen verschoben haben, können wir beispielsweise das Modul [Clean Missing Data][clean-missing-data] aus dem Vorhersageexperiment entfernen.

Unser Vorhersageexperiment sieht nun folgendermaßen aus:

![Entfernen zusätzlicher Module](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Hinzufügen von optionalen Webdienst-Parametern
In einigen Fällen empfiehlt es sich, dem Benutzer des Webdiensts das Ändern des Modulverhaltens zu erlauben, wenn auf den Dienst zugegriffen wird. *Webdienstparameter* erlauben Ihnen, dies zu tun.

Ein typisches Beispiel ist das Einrichten eines Moduls [Import Data][import-data], damit die Benutzer des bereitgestellten Webdiensts beim Zugreifen auf den Webdienst eine andere Datenquelle angeben können. Ein weiteres Beispiel ist die Konfiguration eines Moduls [Export Data][export-data], damit ein anderes Ziel angegeben werden kann.

Sie können Webdienstparameter definieren und einem oder mehreren Modulparametern zuordnen, und Sie können angeben, ob diese Parameter erforderlich oder optional sind. Der Benutzer des Webdiensts gibt beim Zugreifen auf den Dienst Werte für diese Parameter an, und das Verhalten des Moduls ändert sich entsprechend.

Weitere Informationen zu Webdienstparametern und ihrer Verwendung finden Sie unter [Verwenden von Azure Machine Learning-Webdienstparametern][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Bereitstellung des Vorhersageexperiments als Webdienst
Nachdem das Vorhersageexperiment ausreichend vorbereitet wurde, können Sie es als Azure-Webdienst bereitstellen. Mithilfe des Webdiensts können Benutzer Daten an das Modell senden, und das Modell gibt seine Vorhersagen zurück.

Weitere Informationen zum vollständigen Bereitstellungsprozess finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts][deploy].

[deploy]: publish-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
