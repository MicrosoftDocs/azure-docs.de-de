---
title: Trainieren von Modellen mit TensorFlow und Keras
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit den TensorFlow- und Keras-Estimatoren TensorFlow- und Keras-Modelle auf einem Knoten oder verteilt trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 82c9aa961221b582bb16438f30a0584232164393
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65915104"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Trainieren von TensorFlow- und Keras-Modellen mit Azure Machine Learning Service

Mit der [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)-Estimator-Klasse im Azure Machine Learning SDK können Sie ganz einfach TensorFlow-Trainingsjobs auf Azure-Computezielen ausführen. Der `TensorFlow`-Estimator leitet Azure Machine Learning Service zur Ausführung Ihres Jobs für einen TensorFlow-fähigen Container für Deep Neural Network-Training (DNN) weiter.

Der `TensorFlow`-Estimator stellt zudem eine Abstraktionsebene über der Ausführung bereit. Dadurch können Sie ganz einfach parametrisierte Ausführungen auf verschiedenen Computezielen konfigurieren, ohne Ihre Trainingsskripts zu ändern.

## <a name="get-started"></a>Erste Schritte

Da die Estimator-Klasse `TensorFlow` dem Basis-[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) ähnlich ist, empfehlen wir Ihnen, zuerst den [Artikel mit Anleitungen zum Basis-Estimator](how-to-train-ml-models.md) zu lesen, um die übergeordneten Konzepte zu verstehen.

Informationen zu den ersten Schritte mit Azure Machine Learning Service finden Sie im [Schnellstart](quickstart-run-cloud-notebook.md). Wenn Sie fertig sind, verfügen Sie über einen [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) und alle unsere [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml), einschließlich derjenigen für das Training von DNNs mit TensorFlow und Keras.

## <a name="single-node-training"></a>Training auf einem einzelnen Knoten

Instanziieren Sie zum Ausführen eines TensorFlow-Auftrags ein [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)-Objekt, und übermitteln es als Experiment.

Der folgende Code instanziiert einen TensorFlow-Estimator und übermittelt ihn als Experiment. Das Trainingsskript `train.py` wird mit den angegebenen Skriptparametern ausgeführt. Der Auftrag wird auf einem GPU-fähigen [Computeziel](how-to-set-up-training-targets.md) ausgeführt, und „SciKit-learn“ wird als Abhängigkeit für `train.py` installiert.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Verteiltes Training

Der [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)-Estimator unterstützt auch verteiltes Training für CPU- und GPU-Cluster. Sie können ganz einfach verteilte TensorFlow-Aufträge ausführen, und Azure Machine Learning Service verwaltet die Infrastruktur und die Orchestrierung für Sie.

Azure Machine Learning Service unterstützt zwei Methoden des verteilten Trainings in TensorFlow:

* Verteiltes Training auf Grundlage von [MPI](https://www.open-mpi.org/) mithilfe des [Horovod](https://github.com/uber/horovod)-Frameworks
* Natives [verteiltes TensorFlow-Training](https://www.tensorflow.org/deploy/distributed) über die Parameterservermethode

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) ist ein Open-Source-Framework, das von Uber für verteiltes Training entwickelt wurde. Es ermöglicht die einfache Erstellung von verteilten GPU-TensorFlow-Aufträgen.

Im folgenden Beispiel wird ein verteilter Trainingsauftrag mithilfe von Horovod mit zwei Workern ausgeführt, die auf zwei Knoten verteilt sind.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod und alle zugehörigen Abhängigkeiten werden automatisch installiert. Sie können das entsprechende Modul in Ihr Trainingsskript importieren.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Parameterserver

Sie können alternativ auch das [native verteilte TensorFlow-Training](https://www.tensorflow.org/deploy/distributed) ausführen, das auf das Parameterservermodell zurückgreift. Bei dieser Methode führen Sie das Training auf einem Cluster mit Parameterservern und Workern aus. Während des Trainings berechnen die Worker die Gradienten, und die Parameterserver aggregieren diese.

Im folgenden Beispiel wird ein verteilter Trainingsauftrag mithilfe der Parameterservermethode mit vier Workern ausgeführt, die auf zwei Knoten verteilt sind.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Hinweis zu `TF_CONFIG`

Sie benötigen für [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) zusätzlich die Netzwerkadressen und Ports des Clusters, damit Azure Machine Learning die `TF_CONFIG`-Umgebungsvariable automatisch festlegt.

Die `TF_CONFIG`-Umgebungsvariable ist eine JSON-Zeichenfolge. Im folgenden Codeausschnitt finden Sie ein Beispiel für eine Parameterservervariable:

```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Bei der High-Level-[`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator)-API von TensorFlow wird diese `TF_CONFIG`-Variable analysiert und die Clusterspezifikation erstellt.

Bei Low-Level-APIs von TensorFlow für das Training müssen Sie die `TF_CONFIG`-Variable analysieren und `tf.train.ClusterSpec` in Ihrem Trainingscode erstellen. In [diesem Beispiel](https://aka.ms/aml-notebook-tf-ps) würden Sie dazu **Ihr Trainingsskript** wie folgt anpassen:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="keras-support"></a>Unterstützung für Keras

[Keras](https://keras.io/) ist eine beliebte High-Level-DNN-Python-API, die TensorFlow, CNTK und Theano als Back-Ends unterstützt. Wenn Sie TensorFlow als Back-End verwenden, können Sie Keras so einfach hinzufügen wie einen `pip_package`-Konstruktorparameter.

Der folgende Code instanziiert einen [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)-Estimator und übermittelt ihn als Experiment. Der Estimator führt das Keras-Trainingsskript `keras_train.py` aus. Der Auftrag wird auf einem GPU-fähigen [Computeziel](how-to-set-up-training-targets.md) ausgeführt, und Keras wird als Abhängigkeit über pip installiert.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Exportieren nach ONNX

Um optimierte Rückschlüsse mit der [ONNX-Runtime](concept-onnx.md) zu erzielen, können Sie Ihr trainiertes TensorFlow-Modul ins ONNX-Format konvertieren. Ein entsprechendes Beispiel finden Sie [hier](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Beispiele

Codebeispiele für die Arbeit mit einzelnen Knoten und verteilten TensorFlow-Ausführungen über verschiedene Frameworks finden Sie auf [unserer GitHub-Seite](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen einer Ausführungsmetrik während des Trainings](how-to-track-experiments.md)
* [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
* [Bereitstellen eines trainierten Modells](how-to-deploy-and-where.md)
