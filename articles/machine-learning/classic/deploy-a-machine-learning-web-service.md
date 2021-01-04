---
title: 'ML Studio (Classic): Bereitstellen eines Webdiensts – Azure'
description: So konvertieren Sie ein Trainingsexperiment in ein Vorhersageexperiment, bereiten es auf die Bereitstellung vor und stellen es dann als (klassischen) Azure Machine Learning Studio-Webdienst bereit.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: f77c0fb09dddd884335d31d630904ea6aeafeaf5
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495201"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Bereitstellen eines (klassischen) Azure Machine Learning Studio-Webdiensts

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Azure Machine Learning Studio (klassisch) ermöglicht Ihnen, Vorhersageanalyselösungen zu erstellen und zu testen. Dann können Sie die Lösung als Webdienst bereitstellen.

(Klassische) Machine Learning Studio-Webdienste stellen eine Schnittstelle zwischen einer Anwendung und einem Machine Learning Studio-Workflow-Bewertungsmodell dar. Eine externe Anwendung kann in Echtzeit mit einem (klassischen) Machine Learning Studio-Workflow-Bewertungsmodell kommunizieren. Mit einem Aufruf eines (klassischen) Machine Learning Studio-Webdiensts werden Vorhersageergebnisse an eine externe Anwendung zurückgegeben. Zur Durchführung eines Webdienstaufrufs übergeben Sie einen API-Schlüssel, der beim Bereitstellen des Webdiensts erstellt wurde. Ein (klassischer) Machine Learning Studio-Webdienst basiert auf REST, einer verbreiteten Architektur für Webprogrammierungsprojekte.

Azure Machine Learning Studio (klassisch) verfügt über zwei Arten von Webdiensten:

* Anforderung-/Antwort-Dienst (Request-Response Service, RRS): Eine geringe Latenz, hochgradig skalierbarer Dienst, der einen einzigen Datensatz bewertet.
* Batchausführungsdienst (Batch Execution Service, BES): Ein asynchroner Dienst für die Bewertung eines Batches von Datensätzen.

Die Eingabe für BES gleicht der für RRS verwendeten Dateneingabe. BES liest im Gegensatz zu RRS einen Block von Einträgen aus einer Vielzahl von Quellen, z.B. Azure Blob Storage, Azure Table Storage, Azure SQL-Datenbank, HDInsight (Hive-Abfrage) und HTTP-Quellen.

Allgemein betrachtet stellen Sie Ihr Modell in drei Schritten bereit:

* **[Erstellen eines Trainingsexperiments]**: In Studio (klassisch) können Sie ein Vorhersageanalysemodell mit Trainingsdaten, die Sie angeben, unter Verwendung eines großen Satzes von integrierten Machine Learning-Algorithmen trainieren und testen.
* **[Konvertieren in ein Vorhersageexperiment]**: Sobald Ihr Modell mit vorhandenen Daten trainiert wurde, können Sie es verwenden, um neue Daten zu bewerten. Sie bereiten das Experiment auf Vorhersagen vor und optimieren es.
* **Bereitstellung** als **[Neuer Webdienst]** oder **[Klassischer Webdienst]**: Wenn Sie Ihr Vorhersageexperiment als Azure-Webdienst bereitstellen, können Benutzer Daten an Ihr Modell senden und dessen Vorhersagen empfangen.

## <a name="create-a-training-experiment"></a>Erstellen eines Trainingsexperiments

Zum Trainieren eines Vorhersageanalysemodells erstellen Sie in Azure Machine Learning Studio (klassisch) ein Trainingsexperiment, in das Sie verschiedene Module zum Laden der Trainingsdaten, zum ggf. erforderlichen Vorbereiten der Daten, Anwenden von Lernalgorithmen und Auswerten der Ergebnisse einbinden. Sie können ein Experiment iterieren und unterschiedliche Lernalgorithmen zum Vergleichen und Bewerten der Ergebnisse ausprobieren.

Der Prozess der Erstellung und Verwaltung von Trainingsexperimenten wird an anderer Stelle ausführlicher behandelt. Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

* [Erstellen eines einfachen Experiments in Azure Machine Learning Studio (klassisch)](create-experiment.md)
* [Entwickeln einer Vorhersagelösung mit Azure Machine Learning Studio (klassisch)](tutorial-part1-credit-risk.md)
* [Importieren von Trainingsdaten in Azure Machine Learning Studio (klassisch)](import-data.md)
* [Verwalten von Experimentiterationen in Azure Machine Learning Studio (klassisch)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertieren des Trainingsexperiments in ein Vorhersageexperiment

Nachdem Sie das Modell trainiert haben, können Sie Ihr Trainingsexperiment in ein Vorhersageexperiment konvertieren, um neue Daten zu bewerten.

Durch die Konvertierung in ein Vorhersageexperiment wird das trainierte Modell darauf vorbereitet, als Bewertungswebdienst bereitgestellt zu werden. Benutzer des Webdiensts können Eingabedaten an das Modell senden, und das Modell sendet Vorhersageergebnisse zurück. Während der Konvertierung in ein Vorhersageexperiment sollten Sie berücksichtigen, wie das Modell aus Ihrer Sicht erwartungsgemäß von anderen Benutzern verwendet wird.

Der Prozess der Konvertierung eines Trainingsexperiments in ein Vorhersageexperiment umfasst drei Schritte:

1. Ersetzen Sie die Machine Learning-Algorithmusmodule durch Ihr trainiertes Modell.
2. Reduzieren des Experiments auf die Module, die für die Bewertung erforderlich sind. Ein Trainingsexperiment enthält mehrere Module, die für das Training erforderlich sind, jedoch nicht mehr benötigt werden, nachdem das Modell entsprechend trainiert wurde.
3. Definieren Sie, wie Ihr Modell Daten des Webdienstbenutzers akzeptiert, und welche Daten zurückgegeben werden.

> [!TIP]
> In Ihrem Trainingsexperiment haben Sie Ihr Modell mit Ihren eigenen Daten trainiert und bewertet. Aber nach der Bereitstellung werden Benutzer neue Daten an Ihr Modell senden, und es wird Vorhersageergebnisse zurückgeben. Berücksichtigen Sie also bei der Konvertierung Ihres Trainingsexperiments in ein Vorhersageexperiment, um es für die Bereitstellung vorzubereiten, wie andere Benutzer das Modell verwenden werden.

![Konvertieren in ein Bewertungsexperiment](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Schaltfläche zur Webdiensteinrichtung
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

* **Score**: In diesem Beispiel wird das Modul [Split Data][split] zum Unterteilen des Datenstroms in Testdaten und Trainingsdaten verwendet. Im Vorhersageexperiment trainieren wir nicht mehr, sodass [Split Data][split] entfernt werden kann. Ebenso werden das zweite [Score Model][score-model]-Modul und das [Evaluate Model][evaluate-model]-Modul verwendet, um Ergebnisse aus den Testdaten zu vergleichen, und daher im Vorhersageexperiment nicht benötigt. Das verbleibende Modul [Score Model][score-model] ist jedoch erforderlich, um über den Webdienst ein Bewertungsergebnis zurückzugeben.

So sieht das Beispiel nach dem Klicken auf **Webdienst einrichten** aus:

![Konvertiertes Vorhersageexperiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Die Arbeit von **Set Up Web Service** kann ausreichend sein, um das Experiment zur Bereitstellung als Webdienst vorzubereiten. Allerdings sollten Sie einige zusätzliche Aufgaben für das Experiment durchführen.

#### <a name="adjust-input-and-output-modules"></a>Anpassen von Eingabe- und Ausgabemodulen
Im Trainingsexperiment haben Sie einen Satz von Trainingsdaten verwendet und dann durch Verarbeitung die Daten in einem Formular abgerufen, die für den Algorithmus für maschinelles Lernen erforderlich sind. Wenn für die voraussichtlich über den Webdienst empfangenen Daten keine Verarbeitung erforderlich ist, können Sie dies umgehen: stellen Sie die Verbindung der Ausgabe des Moduls **Web service input** mit einem anderen Knoten im Experiment her. Die Benutzerdaten werden jetzt im Modell an dieser Stelle eingehen.

Beispielsweise setzt **Set Up Web Service** standardmäßig das **Web service input**-Modul an den Beginn des Datenflusses, wie in der Abbildung oben dargestellt. Aber wir können das **Web service input**-Modul manuell hinter den Datenverarbeitungsmodulen positionieren:

![Verschieben des Web Service Input](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Die über den Webdienst bereitgestellten Eingabedaten werden jetzt ohne Vorverarbeitung direkt an das "Score Model"-Modul übergeben.

Auf ähnliche Weise setzt **Webdienst einrichten** standardmäßig das Webdienstausgabe-Modul an das Ende des Datenflusses. In diesem Beispiel gibt der Webdienst die Ausgabe des Moduls [Score Model][score-model], die den vollständigen Eingabedatenvektor sowie die Bewertungsergebnisse enthält, an den Benutzer zurück.
Falls Sie jedoch andere Daten zurückgeben möchten, können Sie zusätzliche Module vor dem Modul **Web Service Output** hinzufügen. 

Um beispielsweise nur die Bewertungsergebnisse und nicht den gesamten Eingabedatenvektor zurückzugeben, fügen Sie ein Modul [Select Columns in Dataset][select-columns] hinzu, um alle Spalten mit Ausnahme der Bewertungsergebnisse auszuschließen. Anschließend verschieben Sie das Modul **Web service output** zur Ausgabe des Moduls [Select Columns in Dataset][select-columns]. Das Experiment sieht wie folgt aus:

![Verschieben des Web Service Output](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Hinzufügen oder Entfernen zusätzlicher Datenverarbeitungsmodule
Wenn in Ihrem Experiment mehrere Module vorhanden sind, von denen Sie wissen, dass sie für die Bewertung nicht benötigt werden, können diese entfernt werden. Da wir das Modul **Web service input** an einen Punkt hinter den Datenverarbeitungsmodulen verschoben haben, können wir beispielsweise das Modul [Clean Missing Data][clean-missing-data] aus dem Vorhersageexperiment entfernen.

Unser Vorhersageexperiment sieht nun folgendermaßen aus:

![Entfernen zusätzlicher Module](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Hinzufügen von optionalen Webdienst-Parametern
In einigen Fällen empfiehlt es sich, dem Benutzer des Webdiensts das Ändern des Modulverhaltens zu erlauben, wenn auf den Dienst zugegriffen wird. *Webdienstparameter* erlauben Ihnen, dies zu tun.

Ein typisches Beispiel ist das Einrichten eines Moduls [Import Data][import-data], damit die Benutzer des bereitgestellten Webdiensts beim Zugreifen auf den Webdienst eine andere Datenquelle angeben können. Ein weiteres Beispiel ist die Konfiguration eines Moduls [Export Data][export-data], damit ein anderes Ziel angegeben werden kann.

Sie können Webdienstparameter definieren und einem oder mehreren Modulparametern zuordnen, und Sie können angeben, ob diese Parameter erforderlich oder optional sind. Der Benutzer des Webdiensts gibt beim Zugreifen auf den Dienst Werte für diese Parameter an, und das Verhalten des Moduls ändert sich entsprechend.

Weitere Informationen zu Webdienstparametern und ihrer Verwendung finden Sie unter [Verwenden von Azure Machine Learning-Webdienstparametern][webserviceparameters].

Die folgenden Schritte zeigen, wie Sie ein Vorhersageexperiment als neuen Webdienst bereitstellen. Sie können das Experiment auch als klassischen Webdienst bereitstellen.

## <a name="deploy-it-as-a-new-web-service"></a>Bereitstellen als neuer Webdienst

Nachdem das Vorhersageexperiment vorbereitet wurde, können Sie es als neuen Azure-Webdienst (auf Resource Manager-Basis) bereitstellen. Mithilfe des Webdiensts können Benutzer Daten an das Modell senden, und das Modell gibt seine Vorhersagen zurück.

Um das Vorhersageexperiment bereitzustellen, klicken Sie unten im Experimentbereich auf **Run** . Sobald das Experiment ausgeführt wurde, klicken Sie auf **Deploy Web Service**, und wählen Sie **Deploy Web Service [neuen]** aus.  Die Bereitstellungsseite des Webdienstportals von Machine Learning Studio (klassisch) wird geöffnet.

> [!NOTE] 
> Zum Bereitstellen eines neuen Webdiensts müssen Sie über ausreichende Berechtigungen in dem Abonnement verfügen, an das Sie den Webdienst bereitstellen. Weitere Informationen finden Sie unter [Verwalten eines Webdiensts im Azure Machine Learning Web Services-Portal](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Webdienstportal: Seite „Deploy Experiment“

Geben Sie auf der Seite „Deploy Experiment“ einen Namen für den Webdienst ein.
Wählen Sie einen Preisplan aus. Wenn Sie bereits einen Preisplan gewählt haben, können Sie diesen auswählen. Andernfalls müssen Sie einen Preisplan für den Dienst wählen.

1. Wählen Sie in der Dropdownliste **Price Plan** einen Preisplan aus, oder wählen Sie die Option **Select new plan** aus.
2. Geben Sie in **Plan Name** einen Namen ein, der den Plan in Ihrer Rechnung bestimmt.
3. Wählen Sie in **Monthly Plan Tiers** eine Option aus. Die Plantarife sind standardmäßig auf die Pläne für Ihre Standardregion festgelegt, und Ihr Webdienst wird in dieser Region bereitgestellt.

Klicken Sie auf **Deploy**. Die Seite **Quickstart** für Ihren Webdienst wird geöffnet.

Die Seite „Quickstart“ des Webdiensts bietet Zugriff auf und Anleitungen für die gängigsten Aufgaben, die Sie nach dem Erstellen eines neuen Webdiensts ausführen. Hier können Sie auch auf die Seiten „Test“ und „Consume“ zugreifen.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testen des neuen Webdiensts

Klicken Sie zum Testen Ihres neuen Webdiensts unter „Common Tasks“ auf **Test web service** . Auf der Seite „Test“ können Sie Ihren Webdienst als Anforderung-/Antwort-Dienst (Request-Response Service, RRS) oder Stapelausführungsdienst (Batch Execution Service, BES) testen.

Die RRS-Testseite zeigt die Eingaben, Ausgaben und alle globalen Parameter, die Sie für das Experiment definiert haben. Um den Webdienst zu testen, können Sie als Eingaben geeignete Werte manuell eingeben oder eine CSV-Datei mit den Testwerten angeben.

Zum Testen mit dem RRS geben Sie im Listenansichtsmodus für die Eingaben geeignete Werte ein und klicken dann auf **Test Request-Response**. Ihre Vorhersageergebnisse werden in der Ausgabespalte links angezeigt.

![Eingeben der entsprechenden Werte zum Testen Ihres Webdiensts](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Klicken Sie zum Testen Ihres BES auf **Batch**. Klicken Sie auf der Testseite „Batch“ unter Ihrer Eingabe auf „Browse“, und wählen Sie eine CSV-Datei mit den entsprechenden Beispielwerten aus. Wenn Sie keine CSV-Datei haben und Ihr Vorhersageexperiment mithilfe von Machine Learning Studio (klassisch) erstellt haben, können Sie das Dataset für Ihr Vorhersageexperiment herunterladen und nutzen.

Um das Dataset herunterzuladen, öffnen Sie Machine Learning Studio (klassisch). Öffnen Sie das Vorhersageexperiment, und klicken Sie mit der rechten Maustaste auf die Eingabe für das Experiment. Wählen Sie im Kontextmenü **Dataset** und dann **Download** aus.

![Herunterladen Ihres Datasets aus dem (klassischen) Studio-Canvas](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klicken Sie auf **Test**. Der Status Ihres Batchausführungsauftrags wird rechts unter **Test Batch Jobs** angezeigt.

![Testen Ihres Batchausführungsauftrags mit dem Web Services-Portal](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Auf der Seite **CONFIGURATION** können Sie die Beschreibung und den Titel ändern, den Speicherkontoschlüssel aktualisieren und Beispieldaten für Ihren Webdienst aktivieren.

![Konfigurieren Ihres Webdiensts](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Zugreifen auf den neuen Webdienst

Nachdem Sie den Webdienst in Machine Learning Studio (klassisch) bereitgestellt haben, können Sie Daten an den Dienst senden und programmgesteuert Antworten erhalten.

Die Seite **Consume** bietet alle Informationen, die Sie benötigen, um auf den Webdienst zuzugreifen. Beispielsweise wird der API-Schlüssel bereitgestellt, um den autorisierten Zugriff auf den Dienst zuzulassen.

Weitere Informationen zum Zugreifen auf einen (klassischen) Machine Learning Studio-Webdienst finden Sie unter [Nutzen eines (klassischen) Azure Machine Learning Studio-Webdiensts](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Verwalten des neuen Webdiensts

Sie können Ihre neuen Webdienste über das (klassische) Webdienstportal von Machine Learning Studio verwalten. Klicken Sie auf der [Hauptseite des Portals](https://services.azureml.net/) auf **Web Services**. Auf der Seite „Web Services“ können Sie einen Dienst löschen oder kopieren. Um einen bestimmten Dienst zu überwachen, klicken Sie auf den Dienst und dann auf **Dashboard**. Klicken Sie zum Überwachen von Batchaufträgen im Zusammenhang mit dem Webdienst auf **Batch Request Log**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Bereitstellen Ihres neuen Webdiensts in mehreren Regionen

Sie können mühelos einen neuen Webdienst in mehreren Regionen bereitstellen, ohne dass mehrere Abonnements oder Arbeitsbereiche erforderlich sind.

Die Preise sind regionsspezifisch. Daher müssen Sie einen Abrechnungsplan für jede Region definieren, in der der Webdienst bereitgestellt wird.

#### <a name="create-a-plan-in-another-region"></a>Erstellen eines Plans in einer anderen Region

1. Melden Sie sich bei [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/) an.
2. Klicken Sie auf die Menüoption **Plans** .
3. Klicken Sie auf der Übersichtsseite „Plans“ auf **New**.
4. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem sich der neue Plan befinden soll.
5. Wählen Sie in der Dropdownliste **Region** eine Region für den neuen Plan aus. Im Abschnitt **Plan Options** auf der Seite werden die Planoptionen für die ausgewählte Region angezeigt.
6. Wählen Sie in der Dropdownliste **Resource Group** eine Ressourcengruppe für den Plan aus. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. Geben Sie in **Plan Name** den Namen des Plans ein.
8. Klicken Sie unter **Plan Options** auf den Abrechnungstarif des neuen Plans.
9. Klicken Sie auf **Erstellen**.

#### <a name="deploy-the-web-service-to-another-region"></a>Bereitstellen des Webdiensts in einer anderen Region

1. Klicken Sie auf der Microsoft Azure Machine Learning Web Services-Seite auf die Menüoption **Web Services (Webdienste)** .
2. Wählen Sie den Webdienst aus, den Sie in einer neuen Region bereitstellen.
3. Klicken Sie auf **Kopieren**.
4. Geben Sie in **Web Service Name** einen neuen Namen für den Webdienst ein.
5. Geben Sie in **Web service description** eine Beschreibung des Webdiensts ein.
6. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem sich der neue Webdienst befinden soll.
7. Wählen Sie in der Dropdownliste **Resource Group** eine Ressourcengruppe für den Webdienst aus. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. Wählen Sie in der Dropdownliste **Region** die Region aus, in der der Webdienst bereitgestellt werden soll.
9. Wählen Sie in der Dropdownliste **Storage account** ein Speicherkonto aus, in dem der Webdienst gespeichert werden soll.
10. Wählen Sie in der Dropdownliste **Price Plan** einen Plan für die Region aus, die Sie in Schritt 8 gewählt haben.
11. Klicken Sie auf **Kopieren**.

## <a name="deploy-it-as-a-classic-web-service"></a>Bereitstellen als klassischen Webdienst

Nachdem das Vorhersageexperiment ausreichend vorbereitet wurde, können Sie es als klassischen Azure-Webdienst bereitstellen. Mithilfe des Webdiensts können Benutzer Daten an das Modell senden, und das Modell gibt seine Vorhersagen zurück.

Um das Vorhersageexperiment bereitzustellen, klicken Sie unten im Experimentbereich auf **Run** und anschließend auf **Deploy Web Service**. Der Webdienst wird eingerichtet, und Sie werden zum Webdienst-Dashboard weitergeleitet.

![Bereitstellen Ihres Webdiensts aus Studio (klassisch)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testen des klassischen Webdiensts

Sie können den Webdienst im Webdienstportal von (klassischem) Machine Learning Studio oder in (klassischem) Machine Learning Studio testen.

Klicken Sie zum Testen des Anforderung-/Antwort-Webdiensts im Dashboard des Webdiensts auf die Schaltfläche **Test**. Ein Dialogfeld wird geöffnet, in dem nach den Eingabedaten für den Dienst gefragt werden. Hierbei handelt es sich um die Spalten, die vom Bewertungsexperiment erwartet werden. Geben Sie eine Reihe von Daten ein, und klicken Sie auf **OK**. Die vom Webdienst generierten Ergebnisse werden jetzt unten im Dashboard angezeigt.

Sie können auf den Vorschaulink **Test (Testen)** klicken, um Ihren Dienst im Azure Machine Learning Studio Web Services-Portal (klassisch) zu testen, wie bereits im Abschnitt „Neuer Webdienst“ gezeigt.

Klicken Sie zum Testen des Stapelausführungsdiensts auf den Vorschaulink **Test**. Klicken Sie auf der Testseite „Batch“ unter Ihrer Eingabe auf „Browse“, und wählen Sie eine CSV-Datei mit den entsprechenden Beispielwerten aus. Wenn Sie keine CSV-Datei haben und Ihr Vorhersageexperiment mithilfe von Machine Learning Studio (klassisch) erstellt haben, können Sie das Dataset für Ihr Vorhersageexperiment herunterladen und nutzen.

![Testen des Webdiensts](./media/publish-a-machine-learning-web-service/figure-3.png)

Auf der Registerkarte **CONFIGURATION** können Sie den Anzeigenamen des Diensts ändern und eine Beschreibung eingeben. Der Name und die Beschreibung werden im [Azure-Portal](https://portal.azure.com/) angezeigt, in dem Sie Ihre Webdienste verwalten.

Sie können eine Beschreibung für die Eingabedaten, Ausgabedaten und Webdienstparameter bereitstellen, indem Sie Zeichenfolgen für jede Spalte unter **INPUT SCHEMA**, **OUTPUT SCHEMA** und **WEB SERVICE PARAMETER** eingeben. Diese Beschreibungen werden in der Beispielcodedokumentation für den Webdienst verwendet.

Sie können die Protokollierung aktivieren, um Fehler zu diagnostizieren, die angezeigt werden, wenn auf den Webdienst zugegriffen wird. Weitere Informationen hierzu finden Sie unter [Aktivieren der Protokollierung für (klassische) Machine Learning Studio-Webdienste](web-services-logging.md).

![Aktivieren der Protokollierung im Webdiensteportal](./media/publish-a-machine-learning-web-service/figure-4.png)

Sie können auch die Endpunkte für den Webdienst im Azure Machine Learning-Webdienstportal konfigurieren (wie im zuvor im Abschnitt „Neuer Webdienst“ gezeigten Verfahren). Die Optionen sind unterschiedlich. Sie können die Dienstbeschreibung hinzufügen oder ändern, die Protokollierung aktivieren und Beispieldaten für Tests aktivieren.

### <a name="access-your-classic-web-service"></a>Zugreifen auf den klassischen Webdienst

Nachdem Sie den Webdienst in Azure Machine Learning Studio (klassisch) bereitgestellt haben, können Sie Daten an den Dienst senden und programmgesteuert Antworten erhalten.

Das Dashboard bietet alle Informationen, die Sie benötigen, um auf den Webdienst zuzugreifen. Zum Beispiel wird der API-Schlüssel bereitgestellt, um den autorisierten Zugriff auf den Dienst zu ermöglichen, und API-Hilfeseiten werden bereitgestellt, um das Schreiben von Code zu erleichtern.

Weitere Informationen zum Zugreifen auf einen (klassischen) Machine Learning Studio-Webdienst finden Sie unter [Nutzen eines (klassischen) Azure Machine Learning Studio-Webdiensts](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Verwalten des klassischen Webdiensts

Es gibt verschiedene Aktionen, die Sie durchführen können, um einen Webdienst zu überwachen. Sie können ihn aktualisieren und löschen. Sie können einem klassischen Webdienst auch zusätzlich zum Standardendpunkt, der beim Bereitstellen erstellt wird, weitere Endpunkte hinzufügen.

Weitere Informationen finden Sie unter [Verwalten eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs](manage-workspace.md) und [Verwalten eines Webdiensts im Webdienstportal von (klassischem) Azure Machine Learning Studio](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualisieren des Webdiensts
Sie können Änderungen am Webdienst vornehmen, z. B. das Modell mit zusätzlichen Trainingsdaten aktualisieren, und ihn dann erneut bereitstellen, wobei der ursprüngliche Webdienst überschrieben wird.

Um den Webdienst zu aktualisieren, öffnen Sie das ursprüngliche Vorhersageexperiment, das Sie zum Bereitstellen des Webdiensts verwendet haben, und klicken Sie auf **SPEICHERN UNTER**, um eine bearbeitbare Kopie zu erstellen. Nehmen Sie die gewünschten Änderungen vor, und klicken Sie dann auf **Webdienst bereitstellen**.

Da Sie dieses Experiment zuvor bereitgestellt haben, werden Sie gefragt, ob Sie den vorhandenen Dienst überschreiben (klassischer Webdienst) oder aktualisieren (neuer Webdienst) möchten. Wenn Sie auf **YES** oder **Update** klicken, wird der vorhandene Webdienst beendet und das neue Vorhersageexperiment an seiner Stelle bereitgestellt.

> [!NOTE]
> Wenn Sie Konfigurationsänderungen im ursprünglichen Webdienst vorgenommen haben, z. B. einen anderen Anzeigenamen oder eine andere Beschreibung eingegeben haben, dann müssen Sie diese Werte erneut eingeben.

Eine Option zum Aktualisieren des Webdiensts ist das programmgesteuerte erneute Trainieren des Modells. Weitere Informationen finden Sie unter [Programmgesteuertes erneutes Trainieren von (klassischen) Machine Learning Studio-Modellen](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere technische Informationen zur Funktion der Bereitstellung finden Sie unter [Entwicklung eines (klassischen) Machine Learning Studio-Modells von einem Experiment zu einem betriebsbereiten Webdienst](model-progression-experiment-to-web-service.md).

* Weitere Informationen dazu, wie Sie Ihr Modell auf die Bereitstellung vorbereiten, finden Sie unter [Vorbereiten des Modells für die Bereitstellung in Azure Machine Learning Studio (klassisch)](deploy-a-machine-learning-web-service.md).

* Es gibt mehrere Möglichkeiten, die REST-API zu nutzen und auf den Webdienst zuzugreifen. Siehe [Nutzen eines (klassischen) Azure Machine Learning Studio-Webdiensts](consume-web-services.md).

<!-- internal links -->
[Erstellen eines Trainingsexperiments]: #create-a-training-experiment
[Konvertierten in ein Vorhersageexperiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Neuer Webdienst]: #deploy-it-as-a-new-web-service
[Klassischer Webdienst]: #deploy-it-as-a-classic-web-service
[neuen]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data