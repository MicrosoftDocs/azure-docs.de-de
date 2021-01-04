---
title: Visualisieren von Experimenten mit TensorBoard
titleSuffix: Azure Machine Learning
description: Starten Sie TensorBoard, um die Historie der Experimentläufe zu visualisieren und potenzielle Bereiche für Hyperparameter-Tuning und -Training zu identifizieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7642fe6642c1b938645e520c15ac367e12630f91
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316656"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualisieren von Experimentausführungen und -metriken mit TensorBoard und Azure Machine Learning


In diesem Artikel erfahren Sie, wie Sie Ihre Experimentläufe und Metriken in TensorBoard mit [dem `tensorboard`Paket](/python/api/azureml-tensorboard/?preserve-view=true&view=azure-ml-py) im Azure Machine Learning SDK anzeigen können. Sobald Sie Ihre Experimentausführungen überprüft haben, können Sie Ihre Machine Learning-Modelle besser optimieren und erneut trainieren.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) ist eine Sammlung von Webanwendungen zur Überprüfung und zum Verständnis Ihrer Experimentstruktur und -leistung.

Wie Sie TensorBoard mit Azure Machine Learning-Experimenten starten, hängt von der Art des Experiments ab:
+ Wenn Ihr Experiment nativ Protokolldateien ausgibt, die von TensorBoard verarbeitet werden können, wie z.B. PyTorch-, Chainer- und TensorFlow-Experimente, dann können Sie [TensorBoard direkt aus dem Ausführungsverlauf des Experiments starten](#launch-tensorboard). 

+ Für Experimente, die nativ keine für TensorBoard nutzbaren Dateien ausgeben, wie z.B. Scikit-learn- oder Azure Machine Learning-Experimente, verwenden Sie [die`export_to_tensorboard()`-Methode](#export), um die Ausführungsverläufe als TensorBoard-Protokolle zu exportieren und TensorBoard von dort aus zu starten. 

> [!TIP]
> Die Informationen in diesem Dokument sind hauptsächlich für Datenanalysten und Entwickler gedacht, die den Modelltrainingsprozess überwachen möchten. Wenn Sie Administrator sind und sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

## <a name="prerequisites"></a>Voraussetzungen

* Um TensorBoard zu starten und Ihre Experimentausführungsverläufe anzuzeigen, müssen Ihre Experimente zuvor die Protokollierung aktiviert haben, um ihre Metriken und Leistungen zu verfolgen.  
* Der Code in diesem Dokument kann in einer der folgenden Umgebungen ausgeführt werden: 
    * Azure Machine Learning-Compute-Instanz: keine Downloads oder Installationen erforderlich
        * Absolvieren Sie [Tutorial: Einrichten von Umgebung und Arbeitsbereich](tutorial-1st-experiment-sdk-setup.md), um einen dedizierten Notebookserver zu erstellen, auf dem das SDK und Beispielrepository vorinstalliert sind.
        * Suchen Sie im Beispieleordner auf dem Notebook-Server zwei fertige und erweiterte Notebooks, indem Sie zu diesen Verzeichnissen navigieren:
            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard > export-run-history-to-tensorboard > export-run-history-to-tensorboard.ipynb**
            * **how-to-use-azureml > track-and-monitor-experiments > tensorboard > tensorboard > tensorboard.ipynb**
    * Ihr eigener Jupyter-Notebook-Server
       * [Installieren Sie das Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) mit dem `tensorboard`-Zusatz
        * [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).  
        * [Erstellen Sie eine Konfigurationsdatei für den Arbeitsbereich.](how-to-configure-environment.md#workspace)

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Option 1: Direktes Anzeigen des Ausführungsverlaufs in TensorBoard

Diese Option eignet sich für Experimente, die nativ Protokolldateien ausgeben, die von TensorBoard verwendet werden können, etwa PyTorch-, Chainer- und TensorFlow-Experimente. Wenn dies bei Ihrem Experiment nicht der Fall ist, verwenden Sie stattdessen[ die `export_to_tensorboard()`-Methode](#export).

Der folgende Beispielcode verwendet das [MNIST-Demo-Experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) aus dem Repository von TensorFlow in einem Remotecomputeziel, Azure Machine Learning Compute. Als nächstes werden wir eine Ausführung zum Trainieren des TensorFlow-Modells konfigurieren und starten und dann TensorBoard für dieses TensorFlow-Experiment starten.

### <a name="set-experiment-name-and-create-project-folder"></a>Festlegen des Experimentnamens und Erstellen des Projektordners

Hier benennen wir das Experiment und erstellen seinen Ordner. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Herunterladen des Codes des TensorFlow-Demoexperiments

Das Repository von TensorFlow verfügt über eine MNIST-Demo mit umfangreicher TensorBoard-Instrumentierung. Es ist nicht erforderlich, irgendeine Änderung am Code dieser Demo vorzunehmen, damit er mit Azure Machine Learning funktioniert. Im folgenden Code laden wir den MNIST-Code herunter und speichern ihn in unserem neu erstellten Experimentordner.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Beachten Sie in der MNIST-Codedatei „mnist_with_summaries.py“, dass es Zeilen gibt, die `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` usw. aufrufen. Diese Methoden gruppieren, protokollieren und markieren Schlüsselmetriken Ihrer Experimente im Ausführungsverlauf. Die `tf.summary.FileWriter()`-Klasse ist besonders wichtig, da sie die Daten aus Ihren protokollierten Experimentmetriken serialisiert, was es TensorBoard ermöglicht, aus ihnen Visualisierungen zu generieren.

 ### <a name="configure-experiment"></a>Konfigurieren des Experiments

Im Folgenden konfigurieren wir unser Experiment und richten Verzeichnisse für Protokolle und Daten ein. Diese Protokolle werden in den Ausführungsverlauf hochgeladen, auf den TensorBoard später zugreifen wird.

> [!Note]
> Für dieses TensorFlow-Beispiel müssen Sie TensorFlow auf Ihrem lokalen Computer installieren. Außerdem muss das TensorBoard-Modul (d.h. das in TensorFlow enthaltene) für den Kernel dieses Notebooks zugänglich sein, da der lokale Computer derjenige ist, der TensorBoard ausführt.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Erstellen eines Clusters für Ihr Experiment
Wir erstellen einen AmlCompute-Cluster für dieses Experiment, aber Ihre Experimente können in jeder Umgebung erstellt werden, und Sie können TensorBoard weiterhin über den Experimentausführungsverlauf starten. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Konfigurieren und Übermitteln der Trainingsausführung

Konfigurieren Sie einen Trainingsauftrag, indem Sie ein ScriptRunConfig-Objekt erstellen.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>Starten von TensorBoard

Sie können TensorBoard während einer Ausführung oder nach deren Abschluss starten. Im nächsten Schritt wird eine TensorBoard-Objektinstanz (`tb`) erstellt, für die der Experimentausführungsverlauf übernommen wird, der in `run` geladen ist, und wird dann TensorBoard mit der `start()`-Methode gestartet. 
  
Der [TensorBoard-Konstruktor](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?preserve-view=true&view=azure-ml-py) übernimmt ein Array von Ausführungen, also müssen Sie darauf achten, dass Sie die Ausführung als Array mit nur einem Element übergeben.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> Während in diesem Beispiel TensorFlow verwendet wurde, kann TensorBoard genauso einfach mit PyTorch oder Chainer verwendet werden. TensorFlow muss auf dem Computer, auf dem TensorBoard ausgeführt wird, verfügbar sein, ist aber auf dem Computer, der PyTorch- oder Chainer-Berechnungen durchführt, nicht erforderlich. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Option 2: Exportieren des Verlaufs als Protokoll zur Anzeige in TensorBoard

Der folgende Code richtet ein Beispielexperiment ein, beginnt den Protokollierungsprozess mithilfe der Azure Machine Learning-Ausführungsverlauf-APIs und exportiert den Experimentausführungsverlauf in Protokolle, die von TensorBoard zur Visualisierung verwendet werden können. 

### <a name="set-up-experiment"></a>Einrichten des Experiments

Der folgende Code richtet ein neues Experiment ein und gibt `root_run` als Ausführungsverzeichnis an. 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Hier laden wir das Diabetes-Dataset (ein integriertes kleines Dataset, das mit Scikit-learn geliefert wird) und teilen es in Test- und Trainingssets auf.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Ausführen des Experiments und Protokollieren der Metriken

Für diesen Code trainieren wir ein lineares Regressionsmodell und protokollieren Schlüsselmetriken, den Alpha-Koeffizienten `alpha` und den mittleren quadratischen Fehler `mse` im Ausführungsverlauf.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportieren von Ausführungen nach TensorBoard

Mit der [export_to_tensorboard()](/python/api/azureml-tensorboard/azureml.tensorboard.export?preserve-view=true&view=azure-ml-py)-Methode des SDK können wir den Ausführungsverlauf unseres Azure-Machine Learning-Experiments in TensorBoard-Protokolle exportieren, so dass wir sie über TensorBoard anzeigen können.  

Im folgenden Code erstellen wir den Ordner`logdir` in unserem aktuellen Arbeitsverzeichnis. In diesen Ordner exportieren wir unseren Experimentausführungsverlauf und unsere Protokolle von `root_run`. Anschließend markieren wir diese Ausführung als abgeschlossen. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

> [!Note]
> Sie können eine bestimmte Ausführung auch nach TensorBoard exportieren, indem Sie den Namen der Ausführung angeben: `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Starten und Beenden von TensorBoard
Sobald unser Ausführungsverlauf für dieses Experiment exportiert ist, können wir TensorBoard mit der [start()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-start-browser-false-)-Methode starten. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Wenn Sie fertig sind, stellen Sie sicher, dass Sie die [stop()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?preserve-view=true&view=azure-ml-py#&preserve-view=truestop--)-Methode des TensorBoard-Objekts aufrufen. Andernfalls wird TensorBoard weiter ausgeführt, bis Sie den Notebook-Kernel herunterfahren. 

```python
tb.stop()
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie zwei Experimente erstellt und gelernt, wie Sie TensorBoard für deren Ausführungsverläufe starten können, um Bereiche für mögliches Optimieren und erneutes Trainieren zu identifizieren. 

* Wenn Sie mit Ihrem Modell zufrieden sind, wechseln Sie zum Artikel [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md). 
* Erfahren Sie mehr über das [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md).