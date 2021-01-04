---
title: Konfigurieren einer Trainingsausführung
titleSuffix: Azure Machine Learning
description: Trainieren Sie Ihr Machine Learning-Modell in verschiedenen Trainingsumgebungen (Computeziele). Sie können problemlos zwischen Trainingsumgebungen wechseln.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 2197d5be91af4c93e9691e1dc2b953198669deaf
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027406"
---
# <a name="configure-and-submit-training-runs"></a>Konfigurieren und Übermitteln von Trainingsausführungen

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Ausführungen konfigurieren und übermitteln, um die Modelle zu trainieren. Codeausschnitte erklären die wichtigsten Teile der Konfiguration und Übermittlung eines Trainingsskripts.  Verwenden Sie dann eines der [Beispielnotebooks](#notebooks), um die vollständigen End-to-End-Arbeitsbeispiele zu finden.

Beim Training ist es üblich, auf dem lokalen Computer zu starten und später auf einen cloudbasierten Cluster horizontal hochzuskalieren. Mit Azure Machine Learning können Sie Ihr Skript auf unterschiedlichen Computezielen ausführen, ohne das Trainingsskript zu ändern.

Sie müssen lediglich die Umgebung für jedes einzelne Computeziel in einer **Skriptlaufzeitkonfiguration** definieren.  Wenn Sie Ihr Trainingsexperiment auf einem anderen Computeziel ausführen möchten, geben Sie die Laufzeitkonfiguration für diese Computeressource an.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.
* Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (mindestens 1.13.0)
* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md), `ws`
* Ein Computeziel, `my_compute_target`  [Erstellen eines Computeziels](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Was ist eine Skriptlaufzeitkonfiguration?
Eine [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) wird zum Konfigurieren der Informationen verwendet, die zum Übermitteln einer Trainingsausführung im Rahmen eines Experiments benötigt werden.

Sie übermitteln Ihr Trainingsexperiment mit einem ScriptRunConfig-Objekt.  Dieses Objekt enthält Folgendes:

* **source_directory**: Das Quellverzeichnis mit Ihrem Trainingsskript
* **script**: Das Trainingsskript, das ausgeführt werden soll
* **compute_target**: Das Computeziel für die Ausführung
* **environment**: Die Umgebung für die Ausführung des Skripts
* Und einige zusätzliche konfigurierbare Optionen. (Weitere Informationen finden Sie in der [Referenzdokumentation](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py).)

## <a name="train-your-model"></a><a id="submit"></a>Trainieren Ihres Modells

Das Codemuster für die Übermittlung einer Trainingsausführung ist für alle Arten von Computezielen gleich:

1. Erstellen eines Experiments zum Ausführen
1. Erstellen einer Umgebung zum Ausführen des Skripts
1. Erstellen einer ScriptRunConfig-Datei, die das Computeziel und die Umgebung angibt
1. Übermitteln der Ausführung
1. Warten, bis die Ausführung abgeschlossen ist

Alternative:

* Übermitteln Sie eine HyperDrive-Ausführung für die [Hyperparameteroptimierung](how-to-tune-hyperparameters.md).
* Übermitteln Sie ein Experiment über die [VS Code-Erweiterung](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Erstellen eines Experiments

Erstellen Sie ein Experiment in Ihrem Arbeitsbereich.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Auswählen eines Computeziels

Wählen Sie das Computeziel aus, auf dem das Trainingsskript ausgeführt werden soll. Wenn in der ScriptRunConfig-Datei kein Computeziel angegeben ist oder der Parameter `compute_target='local'` verwendet wird, wird das Skript von Azure ML lokal ausgeführt. 

Im Beispielcode in diesem Artikel wird davon ausgegangen, dass Sie bereits ein `my_compute_target`-Computeziel im Abschnitt „Voraussetzungen“ erstellt haben.

## <a name="create-an-environment"></a>Erstellen einer Umgebung
Azure Machine Learning-[Umgebungen](concept-environments.md) sind eine Kapselung der Umgebung, in der Ihr Training für das maschinelle Lernen stattfindet. Sie geben die Python-Pakete, ein Docker-Image, Umgebungsvariablen und Softwareeinstellungen für die Trainings- und Bewertungsskripts an. Sie geben auch Laufzeiten an (Python, Spark oder Docker).

Sie können entweder Ihre eigene Umgebung definieren oder eine zusammengestellte Azure ML-Umgebung verwenden. [Zusammengestellte Umgebungen](./how-to-use-environments.md#use-a-curated-environment) sind vordefinierte Umgebungen, die standardmäßig in Ihrem Arbeitsbereich verfügbar sind. Diese Umgebungen werden durch zwischengespeicherte Docker-Images unterstützt, wodurch die Kosten für die Vorbereitung der Ausführung reduziert werden. Die vollständige Liste der verfügbaren zusammengestellten Umgebungen finden Sie unter [Azure Machine Learning – zusammengestellte Umgebungen](./resource-curated-environments.md).

Für ein Remotecomputeziel können Sie zunächst eine dieser beliebten zusammengestellten Umgebungen verwenden:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Weitere Informationen und Details zu Umgebungen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Lokales Computeziel

Wenn Ihr Computeziel Ihr **lokaler Computer** ist, sind Sie dafür verantwortlich, dass alle notwendigen Pakete in der Python-Umgebung, in der das Skript ausgeführt wird, verfügbar sind.  Verwenden Sie `python.user_managed_dependencies`, um Ihre aktuelle Python-Umgebung (oder das Python in dem von Ihnen angegebenen Pfad) zu verwenden.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Erstellen der Skriptlaufzeitkonfiguration

Da Sie jetzt über ein Computeziel (`my_compute_target`) und eine Umgebung (`myenv`) verfügen, erstellen Sie eine Skriptlaufzeitkonfiguration, die Ihr Trainingsskript (`train.py`) ausführt, das sich in Ihrem `project_folder`-Verzeichnis befindet:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Wenn Sie keine Umgebung angeben, wird eine Standardumgebung für Sie erstellt.

Wenn Sie Befehlszeilenargumente an das Trainingsskript übergeben möchten, können Sie diese über den **`arguments`** -Parameter des ScriptRunConfig-Konstruktors angeben, z. B. `arguments=['--arg1', arg1_val, '--arg2', arg2_val]`.

Wenn Sie die für die Ausführung maximal zulässige Standardzeit überschreiben möchten, können Sie dies über den **`max_run_duration_seconds`** -Parameter tun. Das System versucht, die Ausführung automatisch abzubrechen, wenn er länger dauert als dieser Wert angibt.

### <a name="specify-a-distributed-job-configuration"></a>Festlegen einer Konfigurieren für verteilte Aufträge
Wenn Sie einen verteilten Trainingsauftrag ausführen möchten, geben Sie die für den verteilten Auftrag spezifische Konfiguration für den **`distributed_job_config`** -Parameter an. Zu den unterstützten Konfigurationstypen zählen [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) und [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Weitere Informationen und Beispiele zum Ausführen verteilter Horovod-, TensorFlow- und PyTorch-Aufträge finden Sie unter:

* [Trainieren von TensorFlow-Modellen](./how-to-train-tensorflow.md#distributed-training)
* [Trainieren von PyTorch-Modellen](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Übermitteln des Experiments

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Wenn Sie eine Trainingsausführung übermitteln, wird eine Momentaufnahme des Verzeichnisses, das Ihre Trainingsskripts enthält, erstellt und an das Computeziel gesendet. Sie wird auch als Teil des Experiments in Ihrem Arbeitsbereich gespeichert. Wenn Sie Dateien ändern und die Ausführung erneut übermitteln, werden nur die geänderten Dateien hochgeladen.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Weitere Informationen zu Momentaufnahmen finden Sie unter [Momentaufnahmen](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Spezielle Ordner** Zwei Ordner, *outputs* und *logs*, erhalten eine besondere Behandlung durch Azure Machine Learning. Wenn Sie während des Trainings Dateien in die Ordner *outputs* und *logs* schreiben, die zum Stammverzeichnis relativ sind (`./outputs` und `./logs`), werden diese Dateien automatisch in Ihren Ausführungsverlauf hochgeladen, damit Sie Zugriff darauf haben, wenn die Ausführung abgeschlossen ist.
>
> Um während des Trainings Artefakte zu erstellen (z.B. Modelldateien, Prüfpunkte, Datendateien oder gezeichnete Bilder), schreiben Sie diese in den `./outputs`-Ordner.
>
> Auf ähnliche Weise können Sie alle Protokolle aus Ihrer Trainingsausführung in den `./logs`-Ordner schreiben. Um die [TensorBoard-Integration](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) in Azure Machine Learning zu nutzen, stellen Sie sicher, dass Sie Ihre TensorBoard-Protokolle in diesen Ordner schreiben. Während Ihre Ausführung läuft, können Sie TensorBoard starten und diese Protokolle streamen.  Später können Sie auch die Protokolle aus Ihrer vorherigen Ausführungen wiederherstellen.
>
> So laden Sie beispielsweise eine Datei herunter, die nach Ihrer Remotetrainingausführung in den *outputs*-Ordner auf Ihrem lokalen Computer geschrieben wurde: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git-Nachverfolgung und -Integration

Wenn Sie eine Trainingsausführung starten, bei der das Quellverzeichnis ein lokales Git-Repository ist, werden Informationen über das Repository im Ausführungsverlauf gespeichert. Weitere Informationen finden Sie unter [Git-Integration für Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Notebook-Beispiele

In diesen Notebooks finden Sie Beispiele für die Konfiguration von Ausführungen für verschiedene Trainingsszenarios:
* [Training auf verschiedenen Computezielen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Training mit ML-Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md) verwendet ein verwaltetes Computeziel zum Trainieren eines Modells.
* Erfahren Sie, wie Modelle mit spezifischen ML-Frameworks wie [Scikit-learn](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md) und [PyTorch](how-to-train-pytorch.md) trainiert werden können.
* Erfahren Sie, wie [Hyperparameter optimiert werden](how-to-tune-hyperparameters.md), um bessere Modelle zu erstellen.
* Erfahren Sie nach der Erstellung eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* Zeigen Sie die SDK-Referenz der [ScriptRunConfig-Klasse](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) an.
* [Verwenden von Azure Machine Learning mit virtuellen Azure-Netzwerken](./how-to-network-security-overview.md)
