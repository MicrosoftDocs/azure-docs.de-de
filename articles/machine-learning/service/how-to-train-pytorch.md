---
title: Trainieren von Modellen mit PyTorch
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem PyTorch-Estimator PyTorch-Modelle auf einem Knoten oder verteilt trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 11819730e05e425066e1f060769e14d5290f877d
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851978"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>Trainieren von PyTorch-Modellen mit Azure Machine Learning Service

Azure Machine Learning stellt für das DNN-Training (Deep Neural Networks) mit PyTorch die spezielle [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)-Klasse `Estimator` bereit. Mit dem `PyTorch`-Estimator aus dem Azure SDK können Sie PyTorch-Trainingsaufträge sowohl für die Ausführung auf einem Knoten als auch für die verteilte Ausführung auf Azure-Computezielen auf einfache Weise übermitteln.

## <a name="single-node-training"></a>Training auf einem einzelnen Knoten
Das Trainieren mithilfe des `PyTorch`-Estimators funktioniert in etwa so wie mit dem [Basis-`Estimator`](how-to-train-ml-models.md). Lesen Sie daher zuerst diesen Artikel, und machen Sie sich mit den dort vorgestellten Konzepten vertraut.
  
Instanziieren Sie ein `PyTorch`-Objekt, um einen PyTorch-Auftrag auszuführen. Das [Computeziel](how-to-set-up-training-targets.md#amlcompute)-Objekt `compute_target` und Ihr [Datenspeicherobjekt](how-to-access-data.md) `ds` sollten Sie bereits erstellt haben.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Hier geben Sie für den PyTorch-Konstruktor die folgenden Parameter an:

Parameter | BESCHREIBUNG
--|--
`source_directory` |  Lokales Verzeichnis, das den gesamten für den Trainingsauftrag erforderlichen Code enthält. Dieser Ordner wird von Ihrem lokalen Computer auf das Remotecomputeziel kopiert.
`script_params` |  Wörterbuch, in dem die Befehlszeilenargumente für Ihr Trainingsskript `entry_script` in Wertpaaren der Form <Befehlszeilenargument, Wert> festgelegt sind.  Um ein ausführliches Flag in `script_params` anzugeben, verwenden Sie `<command-line argument, "">`.
`compute_target` |  Remotecomputeziel, auf dem Ihr Trainingsskript ausgeführt wird, in diesem Fall ein Azure Machine Learning Compute-Cluster ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)).
`entry_script` |  Dateipfad des Trainingsskripts (relativ zu `source_directory`), das auf dem Remotecomputeziel ausgeführt werden soll. Diese Datei und alle von ihr abhängigen Dateien sollten sich in diesem Ordner befinden.
`conda_packages` |  Liste der Python-Pakete, die über conda installiert werden und für Ihr Trainingsskript erforderlich sind. Im Konstruktor kann zusätzlich der Parameter `pip_packages` für alle erforderlichen pip-Pakete angegeben werden.
`use_gpu` |  : Legen Sie dieses Flag auf `True` fest, um die GPU für das Training zu nutzen. Der Standardwert lautet `False`.

Da Sie den `PyTorch`-Estimator verwenden, enthält der Container, der für das Training verwendet wird, das PyTorch-Paket und alle zugehörigen Abhängigkeiten, die für das Training mit CPUs und GPUs erforderlich sind.

Übermitteln Sie anschließend den PyTorch-Auftrag:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Verteiltes Training
Mit dem `PyTorch`-Estimator können Sie Modelle in unterschiedlichen Größenordnungen trainieren und dabei das Training auf mehrere CPU- und GPU-Cluster Ihrer Azure-VMs aufteilen. Das verteilte PyTorch-Training lässt sich leicht mit wenigen API-Aufrufen ausführen. Azure Machine Learning verwaltet dabei intern die Infrastruktur und die Orchestrierung, die zur Ausführung dieser Workloads erforderlich sind.

Azure Machine Learning unterstützt derzeit MPI-basiertes verteiltes Training von PyTorch mit dem Horovod-Framework.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) ist ein Open Source-Ring-Allreduce-Framework, das von Uber für verteiltes Training entwickelt wurde.

Erstellen Sie das PyTorch-Objekt wie folgt, um das verteilte PyTorch-Training mithilfe des Horovod-Frameworks auszuführen:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

In diesem Code werden im PyTorch-Konstruktor die folgenden neuen Parameter angegeben:

Parameter | BESCHREIBUNG | Standard
--|--|--
`node_count` |  Die Anzahl der Knoten, die für Ihren Trainingsauftrag verwendet werden sollen. | `1`
`process_count_per_node` |  Die Anzahl der Prozesse (oder „Worker“), die auf jedem Knoten ausgeführt werden sollen. | `1`
`distributed_backend` |  Das über MPI vom Estimator bereitgestellte Back-End für den Start des verteilten Trainings.  Um mit MPI (und Horovod) parallele oder verteilte Trainings auszuführen (wenn also z.B. `node_count` > 1, `process_count_per_node` > 1 oder beides zutrifft), legen Sie `distributed_backend='mpi'` fest. Von Azure Machine Learning wird die MPI-Implementierung [Open MPI](https://www.open-mpi.org/) verwendet. | `None`

Im obigen Beispiel wird verteiltes Training mit zwei Workern, also einem Worker pro Knoten, ausgeführt.

Horovod und alle zugehörigen Abhängigkeiten werden automatisch installiert. Sie können das entsprechende Modul daher einfach wie folgt in das Trainingsskript `train.py` importieren:
```Python
import torch
import horovod
```

Am Ende übermitteln Sie Ihre verteilten PyTorch-Auftrag:
```Python
run = exp.submit(pt_est)
```

## <a name="export-to-onnx"></a>Exportieren nach ONNX

Um Rückschlüsse mit der [ONNX-Runtime](concept-onnx.md) zu optimieren, konvertieren Sie Ihr trainiertes PyTorch-Modell in dass ONNX-Format. Rückschlüsse oder Modellbewertungen bilden die Phase, in der das bereitgestellte Modell für die Vorhersage verwendet wird, meist für Produktionsdaten. Ein Beispiel finden Sie im [Tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb).

## <a name="examples"></a>Beispiele

Informationen zu Notebooks für verteiltes Deep Learning finden Sie unter:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nächste Schritte
* [Erfassen einer Ausführungsmetrik während des Trainings](how-to-track-experiments.md)
* [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
* [Bereitstellen eines trainierten Modells](how-to-deploy-and-where.md)
