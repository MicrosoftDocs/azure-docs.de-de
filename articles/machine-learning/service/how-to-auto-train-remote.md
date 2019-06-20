---
title: Automatisierte ML-Remotecomputeziele
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Modelle mit automatisiertem maschinellem Lernen auf einem Azure Machine Learning-Remotecomputeziel mit Azure Machine Learning Service erstellen.
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6a18bdf3a2a1ccd60ff20d21ebd99f4f6e15e38f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65551333"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Trainieren von Modellen mit automatisiertem maschinellem Lernen in der Cloud

In Azure Machine Learning trainieren Sie Ihr Modell mit verschiedenen, von Ihnen verwalteten Arten von Computeressourcen. Beim Computeziel kann es sich um einen lokalen Computer oder einen Computer in der Cloud handeln.

Sie können Ihr Machine Learning-Experiment bequem durch Hinzufügen zusätzlicher Computeziele wie Azure Machine Learning Compute (AmlCompute) vertikal oder horizontal hochskalieren. AmlCompute ist eine verwaltete Compute-Infrastruktur, mit der Sie ganz einfach ein Computeziel mit einem oder mehreren Knoten erstellen können.

In diesem Artikel erfahren Sie, wie Sie ein Modell mit automatisiertem Machine Learning mit AmlCompute erstellen.

## <a name="how-does-remote-differ-from-local"></a>Remote und lokal – wo liegen die Unterschiede?

Im Tutorial [Trainieren eines Klassifizierungsmodells mit automatisiertem maschinellem Lernen](tutorial-auto-train-models.md) erfahren Sie, wie Sie einen lokalen Computer zum Trainieren von Modellen mit automatisiertem maschinellem Lernen verwenden.  Der Workflow zum lokalen Trainieren trifft ebenfalls auf Remoteziele zu. Bei Remotecomputezielen werden jedoch die Experimentiterationen mit automatisiertem maschinellem Lernen asynchron ausgeführt. Diese Funktion ermöglicht es, eine bestimmte Iteration abzubrechen, den Ausführungsstatus anzuzeigen oder im Jupyter Notebook an anderen Zellen weiterzuarbeiten. Damit Sie Ihr Modell remote trainieren können, müssen Sie zunächst ein Remotecomputeziel wie AmlCompute erstellen. Konfigurieren Sie dann die Remoteressource, und übermitteln Sie dort Ihren Code.

Dieser Artikel zeigt die weiteren erforderlichen Schritte, um ein Experiment mit automatisiertem Machine Learning auf einem AmlCompute-Remoteziel auszuführen. Das Arbeitsbereichsobjekt `ws` aus dem Tutorial wird hier im gesamten Code verwendet.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Erstellen von Ressourcen

Erstellen Sie das AmlCompute-Ziel in Ihrem Arbeitsbereich (`ws`), wenn es noch nicht vorhanden ist.  

**Geschätzter Zeitaufwand**: Ein Erstellen des AmlCompute-Ziels dauert etwa fünf Minuten.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl" #Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", 
                                                            # for GPU, use "STANDARD_NC6"
                                                            #vm_priority = 'lowpriority', # optional
                                                            max_nodes = 6)

compute_target = ComputeTarget.create(ws, amlcompute_cluster_name, provisioning_config)
    
# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(show_output = True, min_node_count = None, timeout_in_minutes = 20)
```

Sie können das `compute_target`-Objekt jetzt als Remotecomputeziel verwenden.

Zu den Einschränkungen für einen Clusternamen gehören:
+ Muss kürzer sein als 64 Zeichen.  
+ Darf keines der folgenden Zeichen enthalten: `\` ~! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-getdata-file"></a>Zugreifen auf Daten mit einer „get_data“-Datei

Gewähren Sie der Remoteressource Zugriff auf Ihre Trainingsdaten. Bei automatisierten Machine Learning-Experimenten, die über Remotecomputing ausgeführt werden, müssen die Daten mithilfe einer `get_data()`-Funktion abgerufen werden.  

Zum Gewähren von Zugriff ist Folgendes erforderlich:
+ Erstellen Sie die Datei „get_data.py“, die eine `get_data()`-Funktion enthält. 
+ Speichern Sie diese Datei in einem Verzeichnis, auf das als absoluter Pfad zugegriffen werden kann. 

Sie können Code zum Lesen von Daten aus einem Blobspeicher oder einem lokalen Datenträger in der Datei „get_data.py“ kapseln. Im folgenden Codebeispiel stammen die Daten aus dem sklearn-Paket.

>[!Warning]
>Wenn Sie ein Remotecomputeziel verwenden, müssen Sie für Ihre Datentransformationen `get_data()` nutzen. Wenn Sie zusätzliche Bibliotheken für Datentransformationen als Teil von „get_data()“ installieren müssen, sind zusätzliche Schritte erforderlich. Weitere Informationen finden Sie im [Beispielnotebook auto-ml-dataprep](https://aka.ms/aml-auto-ml-data-prep ).


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Konfigurieren des Experiments

Legen Sie die Einstellungen für `AutoMLConfig` fest.  (Siehe dazu eine [vollständige Liste der Parameter](how-to-configure-auto-train.md#configure-experiment) und ihrer möglichen Werte.)

In den Einstellungen ist `run_configuration` auf das `run_config` -Objekt festgelegt, das die Einstellungen und Konfiguration für die DSVM enthält.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Aktivieren von Modellerklärungen

Legen Sie den optionalen `model_explainability`-Parameter im `AutoMLConfig` Konstruktor fest. Darüber hinaus muss ein Dataframe-Überprüfungsobjekt als Parameter `X_valid` übergeben werden, um das Modellerklärungsfeature verwenden zu können.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = compute_target,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Übermitteln des Trainingsexperiments

Übermitteln Sie nun die Konfiguration, um automatisch den Algorithmus und die Hyperparameter festzulegen und das Modell zu trainieren.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Es wird eine Ausgabe angezeigt, die in etwa wie im folgenden Beispiel aussieht:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Überprüfen der Ergebnisse

Sie können das gleiche Jupyter-Widget wie in diesem [Trainings-Tutorial](tutorial-auto-train-models.md#explore-the-results) verwenden, um ein Diagramm und eine Tabelle mit Ergebnissen anzuzeigen.

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Hier ist ein statisches Bild des Widgets.  Klicken Sie im Notebook auf eine beliebige Zeile in der Tabelle, um die Ausführungseigenschaften und Ausgabeprotokolle für diese Ausführung anzuzeigen.   Sie können auch die Dropdownliste über dem Diagramm verwenden, um ein Diagramm der einzelnen verfügbaren Metriken für jede Iteration anzuzeigen.

![Widget-Tabelle](./media/how-to-auto-train-remote/table.png)
![Widget-Plot](./media/how-to-auto-train-remote/plot.png)

Das Widget zeigt eine URL an, die Sie zum Anzeigen und Überprüfen der Details der einzelnen Ausführungen verwenden können.
 
### <a name="view-logs"></a>Anzeigen von Protokollen

Suchen Sie unter `/tmp/azureml_run/{iterationid}/azureml-logs` nach Protokollen zur DSVM.

## <a name="best-model-explanation"></a>Erklärung des besten Modells

Das Abrufen von Modellerklärungsdaten ermöglicht es Ihnen, detaillierte Informationen zu den Modellen anzuzeigen, um die Transparenz der Vorgänge zu erhöhen, die auf dem Back-End ausgeführt werden. In diesem Beispiel führen Sie Modellerklärungen nur für das am besten passende Modell aus. Eine Ausführung für alle Modelle in der Pipeline würde zu erheblichen Ausführungszeiten führen. Eine Modellerklärung enthält die folgenden Informationen:

* shap_values: Die von der shap-Bibliothek generierten Erklärungsinformationen.
* expected_values: Der erwartete Wert des Modells, der auf den Satz von X_train-Daten angewendet wird.
* overall_summary: Die Featuregewichtungswerte auf Modellebene, sortiert in absteigender Reihenfolge.
* overall_imp: Die Featurenamen, sortiert in derselben Reihenfolge wie in overall_summary.
* per_class_summary: Die Featuregewichtungswerte auf Klassenebene, sortiert in absteigender Reihenfolge. Nur für den Klassifizierungsfall verfügbar.
* per_class_imp: Die Featurenamen, sortiert in der gleichen Reihenfolge wie in per_class_summary. Nur für den Klassifizierungsfall verfügbar.

Verwenden Sie den folgenden Code, um die beste Pipeline aus Ihren Iterationen auszuwählen. Die `get_output`-Methode gibt die beste Ausführung und das angepasste Modell für den letzten passenden Aufruf zurück.

```python
best_run, fitted_model = remote_run.get_output()
```

Importieren Sie die `retrieve_model_explanation`-Funktion, und führen Sie sie für das beste Modell aus.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Geben Sie die Ergebnisse für die `best_run`-Erklärungsvariablen aus, die Sie anzeigen möchten.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Das Ausgeben der `best_run`-Erklärungszusammenfassungsvariablen führt zur folgenden Ausgabe.

![Konsolenausgabe der Modellerklärung](./media/how-to-auto-train-remote/expl-print.png)

Sie können die Featuregewichtung auch über die Widget-Benutzeroberfläche sowie über die Webbenutzeroberfläche im Azure-Portal in Ihrem Arbeitsbereich visualisieren.

![Benutzeroberfläche der Modellerklärung](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Beispiel

Das Notebook [how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb) veranschaulicht die Konzepte in diesem Artikel. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, [wie Sie die Einstellungen für das automatische Training konfigurieren](how-to-configure-auto-train.md).
