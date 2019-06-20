---
title: Protokollieren von Metriken während Trainingsausführungen
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie die Protokollierung zu Ihrem Trainingsskript hinzufügen, wie Sie das Experiment übermitteln, wie Sie den Fortschritt eines laufenden Auftrags überprüfen und wie Sie die Ergebnisse eines Durchlaufs anzeigen. Sie können Ihre Experimente verfolgen und Metriken überwachen, um den Prozess der Modellerstellung zu verbessern.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d3cbc2d5be1f7addf833162b23c5db0786e9d361
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297476"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Protokollieren von Metriken während Trainingsausführungen in Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie in Azure Machine Learning Service Protokollierung zu Ihrem Trainingsskript hinzufügen, eine Experimentausführung übermitteln, die Ausführung überwachen und die Ergebnisse einer Ausführung anzeigen können. Verbessern Sie den Modellerstellungsvorgang, indem Sie Ihre Experimente verfolgen und Metriken überwachen. 

## <a name="list-of-training-metrics"></a>Liste der Trainingsmetriken 

Die folgenden Metriken können während des Trainings eines Experiments zu einem Durchlauf hinzugefügt werden. Ausführliche Informationen dazu, was Sie bei einer Ausführung nachverfolgen können, finden Sie in der [Referenzdokumentation zur Run-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Python-Funktion | Notizen|
|----|:----|:----|
|Skalarwerte |Funktion:<br>`run.log(name, value, description='')`<br><br>Beispiel:<br>run.log("accuracy", 0.95) |Protokollieren Sie einen Zahlen- oder Zeichenfolgenwert für die Ausführung unter dem jeweiligen Namen. Wenn Sie eine Metrik für eine Ausführung protokollieren, wird diese Metrik in der Ausführungsaufzeichnung des Experiments gespeichert.  Sie können dieselbe Metrik innerhalb einer Ausführung mehrmals protokollieren, wobei das Ergebnis als Vektor dieser Metrik betrachtet wird.|
|Listen|Funktion:<br>`run.log_list(name, value, description='')`<br><br>Beispiel:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Protokollieren Sie eine Liste mit Werten für die Ausführung unter dem jeweiligen Namen.|
|Zeile|Funktion:<br>`run.log_row(name, description=None, **kwargs)`<br>Beispiel:<br>run.log_row("Y over X", x=1, y=0.4) | Mit *log_row* wird eine Metrik mit mehreren Spalten erstellt, wie in kwargs beschrieben. Jeder benannte Parameter erzeugt eine Spalte mit dem angegebenen Wert.  *log_row* kann einmal aufgerufen werden, um ein beliebiges Tupel zu protokollieren, oder mehrmals in einer Schleife, um eine vollständige Tabelle zu erzeugen.|
|Table|Funktion:<br>`run.log_table(name, value, description='')`<br><br>Beispiel:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Protokollieren Sie ein Wörterbuchobjekt für die Ausführung unter dem jeweiligen Namen. |
|Bilder|Funktion:<br>`run.log_image(name, path=None, plot=None)`<br><br>Beispiel:<br>`run.log_image("ROC", plt)` | Protokollieren Sie ein Image für die Ausführungsaufzeichnung. Verwenden Sie log_image, um eine Imagedatei oder einen Matplotlib-Plot für die Ausführung zu protokollieren.  Diese Images werden angezeigt und können mit der Ausführungsaufzeichnung verglichen werden.|
|Ausführung kennzeichnen|Funktion:<br>`run.tag(key, value=None)`<br><br>Beispiel:<br>run.tag("selected", "yes") | Kennzeichnen Sie die Ausführung mit einem Zeichenfolgenschlüssel und einem optionalen Zeichenfolgenwert.|
|Datei oder Verzeichnis hochladen|Funktion:<br>`run.upload_file(name, path_or_stream)`<br> <br> Beispiel:<br>run.upload_file("best_model.pkl", "./model.pkl") | Laden Sie eine Datei in die Ausführungsaufzeichnung hoch. Ausführungen erfassen die Datei im angegebenen Ausgabeverzeichnis. Die ist für die meisten Ausführungstypen standardmäßig „./outputs“.  Verwenden Sie upload_file nur, wenn zusätzliche Dateien hochgeladen werden müssen, oder kein Ausgabeverzeichnis angegeben ist. Wir empfehlen, `outputs` zum Namen hinzuzufügen, damit das Hochladen in das Ausgabeverzeichnis erfolgt. Sie können alle Dateien, die dieser Ausführungsaufzeichnung zugeordnet sind, mit `run.get_file_names()` auflisten.|

> [!NOTE]
> Metriken für Skalare, Listen, Zeilen und Tabellen können den Typ „float“, „integer“ oder „string“ haben.

## <a name="start-logging-metrics"></a>Protokollieren von Metriken starten

Wenn Sie Ihr Experiment nachverfolgen oder überwachen möchten, müssen Sie Code hinzufügen, um die Protokollierung zu starten, wenn Sie die Ausführung übermitteln. Im Folgenden werden Möglichkeiten beschrieben, wie Sie die Übermittlung auslösen können:
* __Run.start_logging__ – Fügen Sie Ihrem Trainingsskript Protokollierungsfunktionen hinzu und starten Sie eine interaktive Protokollierungssitzung im angegebenen Experiment. **Start_logging** erstellt eine interaktive Ausführung für die Verwendung in Szenarien wie z.B. Notebooks. Alle Metriken, die während der Sitzung protokolliert werden, werden der Ausführungsaufzeichnung im Experiment hinzugefügt.
* __ScriptRunConfig__ – Fügen Sie Ihrem Trainingsskript Protokollierungsfunktionen hinzu und laden Sie den gesamten Skriptordner mit der Ausführung.  **ScriptRunConfig** ist eine Klasse für das Einrichten der Konfigurationen für Skriptausführungen. Mit dieser Option können Sie Überwachungscode hinzufügen, um über den Abschluss informiert zu werden oder um ein visuelles Widget zur Überwachung zu erhalten.

## <a name="set-up-the-workspace"></a>Arbeitsbereich einrichten
Bevor Sie die Protokollierung hinzufügen und ein Experiment übermitteln, müssen Sie den Arbeitsbereich einrichten.

1. Laden Sie den Arbeitsbereich. Weitere Informationen zum Festlegen der Arbeitsbereichskonfiguration finden Sie, wenn Sie die Schritte in [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](setup-create-workspace.md#sdk) befolgen.

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Option 1: Verwenden von start_logging

**Start_logging** erstellt eine interaktive Ausführung für die Verwendung in Szenarien wie z.B. Notebooks. Alle Metriken, die während der Sitzung protokolliert werden, werden der Ausführungsaufzeichnung im Experiment hinzugefügt.

Das folgende Beispiel trainiert ein einfaches Sklearn Ridge-Modell lokal in einem lokalen Jupyter-Notebook. Weitere Informationen zur Übermittlung von Experimenten in verschiedenen Umgebungen finden Sie unter [Einrichtung von Computezielen zum Trainieren von Modellen mit dem Azure Machine Learning-Dienst](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Erstellen Sie ein Trainingsskript in einem lokalen Jupyter-Notebook. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Fügen Sie die Experimentnachverfolgung mit dem Azure Machine Learning Service SDK hinzu und laden Sie ein persistentes Modell in die Ausführungsaufzeichnung des Experiments hoch. Der folgende Code fügt Tags und Protokolle hinzu und lädt eine Modelldatei in die Ausführung des Experiments hoch.

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

Das Skript endet mit ```run.complete()```, wodurch angegeben wird, dass die Ausführung abgeschlossen ist.  Diese Funktion wird in der Regel in Szenarien mit einem interaktiven Notebook verwendet.

## <a name="option-2-use-scriptrunconfig"></a>Option 2: Verwenden von ScriptRunConfig

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) ist eine Klasse für das Einrichten der Konfigurationen für Skriptausführungen. Mit dieser Option können Sie Überwachungscode hinzufügen, um über den Abschluss informiert zu werden oder um ein visuelles Widget zur Überwachung zu erhalten.

Dieses Beispiel erweitert das grundlegende sklearn Ridge-Modell von oben. Es führt eine einfache Parametersuche durch, um Alphawerte des Modells zu überfliegen, um Metriken und trainierte Modelle in Ausführungen im Rahmen des Experiments zu erfassen. Das Beispiel wird lokal in einer von Benutzer verwalteten Umgebung ausgeführt. 

1. Erstellen Sie ein Trainingsskript`train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. Über diese `train.py`-Skriptreferenz`mylib.py` erhalten Sie eine Liste der Alphawerte, die im Ridge-Modell verwendet werden sollen.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Konfigurieren Sie eine vom Benutzer verwaltete lokale Umgebung.

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. Übermitteln Sie das ```train.py```-Skript, um in der vom Benutzer verwalteten Umgebung ausgeführt zu werden. Der gesamte Skriptordner wird für das Training übermittelt, einschließlich der ```mylib.py```-Datei.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>Verwalten einer Ausführung

Im Artikel [Starten, Überwachen und Abbrechen von Trainingsausführungen in Python](how-to-manage-runs.md) sind die speziellen Azure Machine Learning-Workflows beschrieben, in denen Sie Experimente verwalten.

## <a name="view-run-details"></a>Anzeigen von Ausführungsdetails

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Überwachen der Ausführung mit Jupyter-Notebook-Widgets
Wenn Sie die **ScriptRunConfig**-Methode verwenden, um Ausführungen zu übermitteln, können Sie den Fortschritt der Ausführung mit einem Jupyter-Notebook-Widget überwachen. Ebenso wie die Übermittlung der Ausführung ist das Widget asynchron und stellt alle 10 bis 15 Sekunden Liveupdates bereit, bis der Auftrag abgeschlossen ist.

1. Zeigen Sie das Jupyter-Widget an, während Sie darauf warten, dass die Ausführung abgeschlossen wird.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Screenshot des Jupyter-Notebook-Widgets](./media/how-to-track-experiments/widgets.PNG)

2. **[Für automatisierte Machine Learning-Ausführungen]** Für den Zugriff auf die Diagramme aus einer vorherigen Ausführung. Ersetzen Sie `<<experiment_name>>` durch den entsprechenden Experimentnamen:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter Notebook-Widget für automatisiertes Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Zum Anzeigen weiterer Details zu einer Pipeline klicken Sie in der Tabelle auf die Pipeline, die Sie untersuchen möchten. Die Diagramme werden dann in einem Popupfenster über das Azure-Portal gerendert.

### <a name="get-log-results-upon-completion"></a>Abrufen von Protokollergebnissen nach Abschluss

Modelltraining und -überwachung erfolgen im Hintergrund, sodass Sie währenddessen andere Aufgaben ausführen können. Sie können auch warten, bis das Training des Modells abgeschlossen ist, bevor Sie weiteren Code ausführen. Bei der Verwendung von **ScriptRunConfig** können Sie ```run.wait_for_completion(show_output = True)``` einsetzen,um anzuzeigen, wenn das Modelltraining abgeschlossen ist. Mithilfe des ```show_output```-Flags erhalten Sie eine ausführlichen Ausgabe. 
  
### <a name="query-run-metrics"></a>Metriken der Abfrageausführung

Mit ```run.get_metrics()``` können Sie die Metriken eines trainierten Modells anzeigen. Sie können nun alle Metriken abrufen, die im obigen Beispiel protokolliert wurden, um das beste Modell zu ermitteln.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Anzeigen des Experiments im Azure-Portal

Wenn ein Experiment abgeschlossen ist, können Sie zu der aufgezeichneten Ausführungsaufzeichnung des Experiments navigieren. Sie haben zwei Möglichkeiten, auf den Verlauf zuzugreifen:

* Direktes Abrufen der URL der Ausführung ```print(run.get_portal_url())```
* Anzeigen der Ausführungsdetails durch Übermittlung des Namens der Ausführung (in diesem Fall ```run```). Diese Vorgehensweise verweist auf den Experimentnamen, die ID, den Typ, den Status, die Detailseite, einen Link zum Azure-Portal und einen Link zur Dokumentation.

Der Link für die Ausführung leitet Sie direkt zur Detailseite für die Ausführung im Azure-Portal. Hier finden Sie alle Eigenschaften, nachverfolgten Metriken, Images und Diagramme, die im Experiment protokolliert werden. In diesem Fall protokollierten wir MSE und die Alphawerte.

  ![Ausführen von Details im Azure-Portal](./media/how-to-track-experiments/run-details-page-web.PNG)

Sie können auch alle Ausgaben oder Protokolle für die Ausführung anzeigen oder die Momentaufnahme des von Ihnen übermittelten Experiments herunterladen, damit Sie den Experimentordner mit anderen teilen können.

### <a name="viewing-charts-in-run-details"></a>Anzeigen von Diagrammen in Ausführungsdetails

Es gibt verschiedene Möglichkeiten, wie Sie die Protokollierungs-APIs während einer Ausführung zum Aufzeichnen verschiedener Arten von Metriken und zum Anzeigen als Diagramme im Azure-Portal nutzen können. 

|Protokollierter Wert|Beispielcode| Anzeigen im Portal|
|----|----|----|
|Protokollieren eines Arrays mit numerischen Werten| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Liniendiagramm mit nur einer Variablen|
|Protokollieren eines einzelnen numerischen Werts mit mehrfacher Verwendung desselben Metriknamens (z.B. in einer for-Schleife)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Liniendiagramm mit nur einer Variablen|
|Wiederholtes Protokollieren einer Zeile mit zwei numerischen Spalten|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Liniendiagramm mit zwei Variablen|
|Protokollieren einer Tabelle mit zwei numerischen Spalten|`run.log_table(name='Sine Wave', value=sines)`|Liniendiagramm mit zwei Variablen|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Grundlegendes zu automatisierten ML-Diagrammen

Nachdem Sie einen automatisierten ML-Auftrag an ein Notebook übermittelt haben, finden Sie einen Verlauf dieser Ausführungen in Ihrem Workspace für den Machine Learning-Dienst. 

Weitere Informationen:
+ [Diagramme und Kurven für Klassifizierungsmodelle](#classification)
+ [Charts und Diagramme für Regressionsmodelle](#regression)
+ [Modellerklärbarkeit](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Anzeigen der Ausführungsdiagramme

1. Wechseln Sie zu Ihrem Arbeitsbereich. 

1. Wählen Sie **Experimente** im am weitesten links liegenden Bereich Ihres Arbeitsbereichs aus.

   ![Screenshot des Menüs „Experiment“](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Wählen Sie das Experiment aus, an dem Sie interessiert sind.

   ![Experimentliste](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. Wählen Sie in der Tabelle die Ausführungsnummer aus.

   ![Experimentausführung](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. Wählen Sie in der Tabelle die Iterationsnummer für das Modell aus, das Sie weiter untersuchen möchten.

   ![Experimentmodell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>Classification

Für jedes Klassifizierungsmodell, das Sie mit den automatisierten Machine Learning-Funktionen von Azure Machine Learning erstellen, können Sie die folgenden Diagramme anzeigen: 
+ [Konfusionsmatrix](#confusion-matrix)
+ [Genauigkeit-Trefferquote-Diagramm](#precision-recall-chart)
+ [ROC-Kurve (Receiver Operating Characteristics)](#roc)
+ [Prognosegütekurve](#lift-curve)
+ [Gewinnkurve](#gains-curve)
+ [Kalibrierungsdiagramm](#calibration-plot)

#### <a name="confusion-matrix"></a>Konfusionsmatrix

Eine Konfusionsmatrix wird verwendet, um die Leistung eines Klassifizierungsmodells zu beschreiben. Jede Zeile zeigt die Instanzen der wahren Klasse an, und jede Spalte repräsentiert die Instanzen der vorhergesagten Klasse. Die Konfusionsmatrix zeigt die richtig klassifizierten Bezeichnungen und die falsch klassifizierten Bezeichnungen für ein bestimmtes Modell.

Für Klassifizierungsprobleme stellt Azure Machine Learning automatisch eine Konfusionsmatrix für jedes Modell zur Verfügung, das erstellt wird. Für jede Konfusionsmatrix zeigt das automatisierte ML die richtig klassifizierten Bezeichnungen als grün und die falsch klassifizierten Bezeichnungen als rot an. Die Größe des Kreises stellt die Anzahl der Samples in diesem Behälter dar. Darüber hinaus wird die Häufigkeitszählung jeder vorhergesagten Bezeichnung und jeder wahren Bezeichnung in den nebenstehenden Balkendiagrammen angezeigt. 

Beispiel 1: Ein Klassifizierungsmodell mit schlechter Genauigkeit ![Ein Klassifizierungsmodell mit schlechter Genauigkeit](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Beispiel 2: Ein Klassifizierungsmodell mit hoher Genauigkeit (ideal) ![Ein Klassifizierungsmodell mit hoher Genauigkeit](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Genauigkeit-Trefferquote-Diagramm

Mit diesem Diagramm können Sie die Genauigkeit-Trefferquote-Kurven für jedes Modell vergleichen, um festzustellen, welches Modell eine akzeptable Beziehung zwischen Genauigkeit und Trefferquote für Ihr spezielles Geschäftsproblem aufweist. Dieses Diagramm zeigt die durchschnittliche Makro-Genauigkeit-Trefferquote, die durchschnittliche Mikro-Genauigkeit-Trefferquote und die Genauigkeit-Trefferquote, die allen Klassen für ein Modell zugeordnet ist.

Der Begriff „Genauigkeit“ steht für die Fähigkeit eines Klassifizierers, alle Instanzen richtig zu kennzeichnen. „Trefferquote“ stellt die Möglichkeit für einen Klassifizierer dar, alle Instanzen einer bestimmten Bezeichnung zu finden. Die Genauigkeit-Trefferquote-Kurve zeigt die Beziehung zwischen diesen beiden Konzepten. Im Idealfall würde das Modell 100 % Genauigkeit und 100 % Trefferquote aufweisen.

Beispiel 1: Ein Klassifizierungsmodell mit geringer Genauigkeit und geringer Trefferquote ![Ein Klassifizierungsmodell mit geringer Genauigkeit und geringer Trefferquote](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Beispiel 2: Ein Klassifizierungsmodell mit ca. 100 % Genauigkeit und ca. 100 % Trefferquote (ideal) ![Ein Klassifizierungsmodell mit ca. 100 % Genauigkeit und ca. 100 % Trefferquote](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

Receiver Operating Characteristic (oder ROC) ist ein Diagramm der richtig klassifizierten Bezeichnungen im Vergleich zu den falsch klassifizierten Bezeichnungen für ein bestimmtes Modell. Die ROC-Kurve kann weniger aussagekräftig sein, wenn Modelle mit Datasets mit einem großen Bias trainiert werden, da sie die falsch-positiven Bezeichnungen nicht anzeigt.

Beispiel 1: Ein Klassifizierungsmodell mit geringen wahren Bezeichnungen und hohen falschen Bezeichnungen ![Ein Klassifizierungsmodell mit geringen wahren Bezeichnungen und hohen falschen Bezeichnungen](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Beispiel 2: Ein Klassifizierungsmodell mit hohen wahren Bezeichnungen und geringen falschen Bezeichnungen ![Ein Klassifizierungsmodell mit hohen wahren Bezeichnungen und geringen falschen Bezeichnungen](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Prognosegütekurve

Sie können die Prognosegüte des automatisch mit Azure Machine Learning erstellten Modells mit der Baseline vergleichen, um den Wertzuwachs dieses bestimmten Modells anzuzeigen.

Prognosegütediagramme werden verwendet, um die Leistung eines Klassifizierungsmodells auszuwerten. Sie zeigen, wie viel bessere Ergebnisse bei der Verwendung eines Modells im Vergleich dazu zu erwarten sind, wenn kein Modell verwendet wird. 

Beispiel 1: Das Modell schneidet schlechter ab als ein Zufallsauswahlmodell ![Ein Klassifizierungsmodell, das schlechter abschneidet als ein Zufallsauswahlmodell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Beispiel 2: Das Modell schneidet besser ab als ein Zufallsauswahlmodell ![Ein Klassifizierungsmodell, das besser abschneidet](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Gewinnkurve

Ein Gewinndiagramm wertet die Leistung eines Klassifizierungsmodells anhand jedes Teils der Daten aus. Es zeigt für jedes Perzentil des Datensatzes an, wie viel besser Sie im Vergleich zu einem Zufallsauswahlmodell abschneiden können.

Verwenden Sie das kumulierte Gewinndiagramm, um die Auswahl der Klassifizierungsgrenze anhand eines Prozentsatzes zu erleichtern, der einem gewünschten Gewinn aus dem Modell entspricht. Diese Informationen bieten eine weitere Möglichkeit, die Ergebnisse im zugehörigen Prognosegütediagramm zu untersuchen.

Beispiel 1: Ein Klassifizierungsmodell mit einem minimalen Gewinn ![Ein Klassifizierungsmodell mit einem minimalen Gewinn](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Beispiel 2: Ein Klassifizierungsmodell mit einem erheblichen Gewinn ![Ein Klassifizierungsmodell mit einem erheblichen Gewinn](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Kalibrierungsdiagramm

Für alle Klassifizierungsprobleme können Sie die Kalibrierungskurve auf Mikrodurchschnitt, Makrodurchschnitt und jede Klasse in einem bestimmten Vorhersagemodell überprüfen. 

Ein Kalibrierungsdiagramm wird verwendet, um die Sicherheit eines Vorhersagemodells anzuzeigen. Dies geschieht, indem es die Beziehung zwischen der vorhergesagten Wahrscheinlichkeit und der tatsächlichen Wahrscheinlichkeit zeigt, wobei die „Wahrscheinlichkeit“ die Wahrscheinlichkeit darstellt, dass eine bestimmte Instanz zu einer bestimmten Bezeichnung gehört. Ein gut kalibriertes Modell richtet sich nach der y=x-Linie aus, wo es in Bezug auf seine Vorhersagen recht zuverlässig ist. Ein übermäßig zuverlässiges Modell richtet sich nach der y=0-Linie aus, wobei die vorhergesagte Wahrscheinlichkeit vorhanden ist, aber keine tatsächliche Wahrscheinlichkeit vorliegt.

Beispiel 1: Ein gut kalibriertes Modell ![ Ein gut kalibriertes Modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Beispiel 2: Ein übermäßig zuverlässiges Modell ![Ein übermäßig zuverlässiges Modell](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Regression
Für jedes Regressionsmodell, das Sie mit den automatisierten Machine Learning-Funktionen von Azure Machine Learning erstellen, können Sie die folgenden Diagramme anzeigen: 
+ [Vorhergesagt im Vergleich zu TRUE](#pvt)
+ [Residualhistogramm](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Vorhergesagt im Vergleich zu True

Vorhergesagt im Vergleich zu TRUE zeigt die Beziehung zwischen einem vorhergesagten Wert und seinem korrelierenden wahren Wert für ein Regressionsproblem. Dieses Diagramm kann verwendet werden, um die Leistung eines Modells zu messen, da Folgendes gilt: Je näher die vorhergesagten Werte an der y=x-Linie liegen, desto besser ist die Genauigkeit eines Vorhersagemodells.

Nach jeder Ausführung wird für jedes Regressionsmodell ein Diagramm mit den vorhergesagten im Vergleich zu den wahren Werten angezeigt. Zum Schutz der Privatsphäre werden die Werte in Behältern zusammengeführt, und die Größe jedes Behälters wird als Balkendiagramm im unteren Teil des Diagrammbereichs angezeigt. Sie können das Vorhersagemodell (mit dem helleren Farbbereich, der die Fehlergrenzen anzeigt) mit dem Idealwert des Modells vergleichen.

Beispiel 1: Ein Regressionsmodell mit niedriger Genauigkeit bei Vorhersagen ![Ein Regressionsmodell mit niedriger Genauigkeit bei Vorhersagen](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Beispiel 2: Ein Regressionsmodell mit hoher Genauigkeit bei seinen Vorhersagen ![Ein Regressionsmodell mit hoher Genauigkeit bei seinen Vorhersagen](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Residualhistogramm

Ein Residual stellt ein beobachtetes y dar: das vorhergesagte y. Um eine Fehlerspanne mit geringem Bias darzustellen, sollte das Histogramm der Residualwerte als Glockenkurve geformt sein, die um 0 zentriert ist. 

Beispiel 1: Ein Regressionsmodell mit einem Bias in seinen Fehlern ![SA-Regressionsmodell mit einem Bias in seinen Fehlern](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Beispiel 2: Ein Regressionsmodell mit einer gleichmäßigeren Verteilung von Fehlern ![Ein Regressionsmodell mit einer gleichmäßigeren Verteilung von Fehlern](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Modellerklärbarkeit und Featuregewichtung

Die Featuregewichtung stellt eine Bewertung dar, die angibt, wie wertvoll jedes Feature bei der Konstruktion eines Modells war. Sie können die Featuregewichtung für das Modell insgesamt sowie pro Klasse für ein Vorhersagemodell überprüfen. Sie können pro Feature erkennen, wie sich die Gewichtung im Vergleich zu jeder Klasse und insgesamt darstellt.

![Featureerklärbarkeit](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Beispielnotebooks
Die folgenden Notebooks verdeutlichen die Konzepte in diesem Artikel:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie diese nächsten Schritte aus, um zu erfahren, wie Sie das Azure Machine Learning SDK für Python verwenden können:

* Ein Beispiel für die Registrierung des besten Modells und dessen Bereitstellung finden Sie im Tutorial [Trainieren eines Bildklassifizierungsmodells mit Azure Machine Learning](tutorial-train-models-with-aml.md).

* Erfahren Sie mehr zum [Trainieren von PyTorch-Modellen mit Azure Machine Learning](how-to-train-pytorch.md).
