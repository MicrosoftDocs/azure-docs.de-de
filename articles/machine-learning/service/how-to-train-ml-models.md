---
title: Trainieren von ML-Modellen mit einem Estimator
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Trainings auf einem einzelnen Knoten und verteilte Trainings konventioneller Machine Learning- und Deep Learning-Modelle mithilfe der Estimator-Klasse von Azure Machine Learning Service ausführen.
ms.author: minxia
author: mx-iao
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 04/19/2019
ms.custom: seodec18
ms.openlocfilehash: 98f7dc2e295c0c994db9a0189814b0ef2a19b758
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66153613"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Trainieren von Azure Machine Learning-Modellen mit einem Estimator

Mit Azure Machine Learning können Sie Ihr Trainingsskript ganz einfach an [verschiedene Computeziele](how-to-set-up-training-targets.md#compute-targets-for-training) übermitteln, indem Sie das [RunConfiguration-Objekt](how-to-set-up-training-targets.md#whats-a-run-configuration) und [ScriptRunConfig-Objekt](how-to-set-up-training-targets.md#submit) verwenden. Dieses Muster bietet Ihnen ein hohes Maß an Flexibilität und maximale Kontrolle.

Um das Deep Learning-Modelltraining zu erleichtern, bietet das Python SDK für Azure Machine Learning eine alternative allgemeine Abstraktion, die Estimator-Klasse, die es Benutzern ermöglicht, Laufzeitkonfigurationen mühelos zu erstellen. Sie können einen allgemeinen [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) erstellen und verwenden, um ein Trainingsskript mit einem beliebigen Lernframework (z.B. scikit-learn) zu übermitteln, das Sie auf einem beliebigen Computeriel unabhängig davon ausführen können, ob es sich um Ihren lokalen Computer, eine einzelne VM in Azure oder einen GPU-Cluster in Azure handelt. Für PyTorch-, TensorFlow- und Chainer-Aufgaben bietet Azure Machine Learning außerdem die passenden [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)-, [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)- und [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)-Estimatoren, um die Verwendung dieser Frameworks zu vereinfachen.

## <a name="train-with-an-estimator"></a>Trainieren mit einem Estimator

Nach der Erstellung Ihres [Arbeitsbereichs](concept-workspace.md) und Einrichten Ihrer [Entwicklungsumgebung](how-to-configure-environment.md) umfasst das Trainieren eines Modells in Azure Machine Learning die folgenden Schritte:  
1. Erstellen eines [Remotecomputeziels](how-to-set-up-training-targets.md) (Sie können auch einen lokalen Computer als Computeziel verwenden)
2. Hochladen Ihrer [Trainingsdaten](how-to-access-data.md) in den Datenspeicher (optional)
3. Erstellen Ihres [Trainingsskripts](tutorial-train-models-with-aml.md#create-a-training-script)
4. Erstellen eines `Estimator`-Objekts
5. Übermitteln des Estimators an ein Experimentobjekt im Arbeitsbereich

Der Schwerpunkt dieses Artikels liegt auf den Schritten 4-5. Für die Schritte 1 bis 3 finden Sie im [Trainieren von Modellen-Tutorial](tutorial-train-models-with-aml.md) ein Beispiel.

### <a name="single-node-training"></a>Training auf einem einzelnen Knoten

Verwenden Sie einen `Estimator` für die Ausführung eines Trainings auf einem Einzelknoten auf einem Remotecomputeziel in Azure für ein SciKit-Lernmodell. Das [Computeziel](how-to-set-up-training-targets.md#amlcompute)-Objekt `compute_target` und Ihr [Datenspeicherobjekt](how-to-access-data.md) `ds` sollten Sie bereits erstellt haben.

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Der Codeausschnitt gibt die folgenden Parameter für den `Estimator`-Konstruktor an.

Parameter | BESCHREIBUNG
--|--
`source_directory`| Lokales Verzeichnis, das den gesamten für den Trainingsauftrag erforderlichen Code enthält. Dieser Ordner wird von Ihrem lokalen Computer auf das Remotecomputeziel kopiert. 
`script_params`| Wörterbuch, in dem die Befehlszeilenargumente für Ihr Trainingsskript `entry_script` in Wertpaaren der Form <Befehlszeilenargument, Wert> festgelegt sind. Um ein ausführliches Flag in `script_params` anzugeben, verwenden Sie `<command-line argument, "">`.
`compute_target`| Remotecomputeziel, auf dem Ihr Trainingsskript ausgeführt wird, in diesem Fall ein Azure Machine Learning Compute-Cluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)). (Hinweis: Auch wenn der AmlCompute-Cluster das gängige Ziel ist, besteht auch die Möglichkeit, andere Computezieltypen wie Azure-VMs oder sogar lokale Computer auszuwählen.)
`entry_script`| Dateipfad des Trainingsskripts (relativ zu `source_directory`), das auf dem Remotecomputeziel ausgeführt werden soll. Diese Datei und alle von ihr abhängigen Dateien sollten sich in diesem Ordner befinden.
`conda_packages`| Liste der Python-Pakete, die über conda installiert werden und für Ihr Trainingsskript erforderlich sind.  

Im Konstruktor kann zusätzlich der Parameter `pip_packages` für alle erforderlichen pip-Pakete angegeben werden.

Nun haben Sie Ihr `Estimator`-Objekt erstellt und können den auszuführenden Trainingsauftrag auf dem Remotecomputeziel über einen Aufruf der `submit`-Funktion auf Ihrem [Experiment](concept-azure-machine-learning-architecture.md#experiment)-Objekt `experiment` ausführen. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Spezielle Ordner** Zwei Ordner, *outputs* und *logs*, erhalten eine besondere Behandlung durch Azure Machine Learning. Wenn Sie während des Trainings Dateien in die Ordner *outputs* und *logs* schreiben, die zum Stammverzeichnis relativ sind (`./outputs` und `./logs`), werden diese Dateien automatisch in Ihren Ausführungsverlauf hochgeladen, damit Sie Zugriff darauf haben, wenn die Ausführung abgeschlossen ist.
>
> Um während des Trainings Artefakte zu erstellen (z.B. Modelldateien, Prüfpunkte, Datendateien oder gezeichnete Bilder), schreiben Sie diese in den `./outputs`-Ordner.
>
> Auf ähnliche Weise können Sie alle Protokolle aus Ihrer Trainingsausführung in den `./logs`-Ordner schreiben. Um die [TensorBoard-Integration](https://aka.ms/aml-notebook-tb) in Azure Machine Learning zu nutzen, stellen Sie sicher, dass Sie Ihre TensorBoard-Protokolle in diesen Ordner schreiben. Während Ihre Ausführung läuft, können Sie TensorBoard starten und diese Protokolle streamen.  Später können Sie auch die Protokolle aus Ihrer vorherigen Ausführungen wiederherstellen.
>
> So laden Sie beispielsweise eine Datei herunter, die nach Ihrer Remotetrainingausführung in den *outputs*-Ordner auf Ihrem lokalen Computer geschrieben wurde: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Verteiltes Training und benutzerdefinierte Docker-Images

Es gibt zwei zusätzliche Trainingsszenarien, die Sie mit dem `Estimator` durchführen können:
* Verwenden eines benutzerdefinierten Docker-Images
* Verteiltes Training in einem Cluster mit mehreren Knoten

Der folgende Code zeigt, wie Sie ein verteiltes Training für ein Keras-Modell ausführen können. Darüber hinaus wird anstelle der standardmäßigen Azure Machine Learning-Images ein benutzerdefiniertes Docker-Image vom Docker Hub `continuumio/miniconda` für das Training angegeben.

Das [Computeziel](how-to-set-up-training-targets.md#amlcompute)-Objekt `compute_target` sollten Sie bereits erstellt haben. Sie können den Estimator wie folgt erstellen:

```Python
from azureml.train.estimator import Estimator

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',     
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_base_image='continuumio/miniconda')
```

Im obigen Code werden im `Estimator`-Konstruktor die folgenden neuen Parameter angegeben:

Parameter | BESCHREIBUNG | Standard
--|--|--
`custom_docker_base_image`| Der Name des zu verwendenden Images. Geben Sie nur Images an, die in öffentlichen Docker-Repositorys (in diesem Fall Docker Hub) verfügbar sind. Um ein Image aus einem privaten Docker-Repository zu verwenden, verwenden Sie stattdessen den `environment_definition`-Parameter des Konstruktors. [Siehe das Beispiel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Die Anzahl der Knoten, die für Ihren Trainingsauftrag verwendet werden sollen. | `1`
`process_count_per_node`| Die Anzahl der Prozesse (oder „Worker“), die auf jedem Knoten ausgeführt werden sollen. In diesem Fall verwenden Sie die auf jedem Knoten verfügbaren `2`-GPUs.| `1`
`distributed_backend`| Das über MPI vom Estimator bereitgestellte Back-End für den Start des verteilten Trainings.  Um parallele oder verteilte Trainings auszuführen (wenn also z.B. `node_count` > 1, `process_count_per_node` > 1 oder beides zutrifft), legen Sie `distributed_backend='mpi'` fest. Die von AML verwendete MPI-Implementierung ist [Open MPI](https://www.open-mpi.org/).| `None`

Übermitteln Sie abschließend den Trainingsauftrag:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>GitHub-Nachverfolgung und -Integration

Wenn Sie eine Trainingsausführung starten, bei der das Quellverzeichnis ein lokales Git-Repository ist, werden Informationen über das Repository im Ausführungsverlauf gespeichert. Zum Beispiel wird die aktuelle Commit-ID für das Repository als Teil des Verlaufs protokolliert.

## <a name="examples"></a>Beispiele
Ein Notebook, das die grundlegenden Einstellungen des Estimator-Musters zeigt, finden Sie unter:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Ein Notebook zum Trainieren eines scikit-learn-Modells finden Sie unter:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Notebooks zu Trainingsmodellen, die bestimmte für „deep-learning-framework“ spezifische Estimatoren verwenden, finden Sie unter:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen einer Ausführungsmetrik während des Trainings](how-to-track-experiments.md)
* [Trainieren von PyTorch-Modellen](how-to-train-pytorch.md)
* [Trainieren von TensorFlow-Modellen](how-to-train-tensorflow.md)
* [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
* [Bereitstellen eines trainierten Modells](how-to-deploy-and-where.md)
