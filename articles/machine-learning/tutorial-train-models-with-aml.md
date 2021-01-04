---
title: 'Tutorial zur Bildklassifizierung: Trainieren von Modellen'
titleSuffix: Azure Machine Learning
description: Verwenden Sie Azure Machine Learning, um ein Bildklassifizierungsmodell mit scikit-learn in einer Python Jupyter Notebook-Instanz zu trainieren. Dieses Tutorial ist das erste einer zweiteiligen Reihe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: d1dbe51dd095290c296699bbb4bc6bd3a8caf7bf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862427"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Tutorial: Trainieren von Bildklassifizierungsmodellen mit MNIST-Daten und scikit-learn 


In diesem Tutorial wird ein Modell für maschinelles Lernen auf Remotecomputeressourcen trainiert. Hierbei wird der Trainings- und Bereitstellungsworkflow für Azure Machine Learning in einer Python Jupyter Notebook-Instanz verwendet.  Anschließend können Sie das Notebook als Vorlage verwenden, um Ihr eigenes Machine Learning-Modell mit Ihren eigenen Daten zu trainieren. Dieses Tutorial ist der **erste Teil einer zweiteiligen Reihe**.  

In diesem Tutorial wird eine einfache logistische Regression anhand des [MNIST](http://yann.lecun.com/exdb/mnist/)-Datasets und [scikit-learn](https://scikit-learn.org) mit Azure Machine Learning trainiert. MNIST ist ein populäres Dataset, das aus 70.000 Graustufenbildern besteht. Jedes Bild ist eine handgeschriebene Ziffer von null bis neun im Format von 28 × 28 Pixeln. Das Ziel besteht darin, einen Multiklassen-Klassifizierer zu erstellen, um die in einem bestimmten Bild dargestellte Ziffer zu erkennen.

Erfahren Sie, wie Sie die folgenden Maßnahmen durchführen:

> [!div class="checklist"]
> * Einrichten Ihrer Entwicklungsumgebung.
> * Zugreifen auf und Untersuchen von Daten.
> * Trainieren Sie ein einfaches logistisches Regressionsmodell für einen Remotecluster.
> * Überprüfen der Trainingsergebnisse und Registrieren des besten Modells.

In [Teil 2 dieses Tutorials](tutorial-deploy-models-with-aml.md) erfahren Sie, wie Sie ein Modell auswählen und bereitstellen.

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

>[!NOTE]
> Der Code in diesem Artikel wurde mit Version 1.13.0 des Azure [Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) getestet.

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie [Tutorial: Erste Schritte beim Erstellen Ihres ersten ML-Experiments mit dem Python SDK](tutorial-1st-experiment-sdk-setup.md), um folgende Aktionen auszuführen:
    * Erstellen eines Arbeitsbereichs
    * Klonen Sie das Tutorial-Notebook in Ihrem Ordner im Arbeitsbereich.
    * Erstellen Sie eine cloudbasierte Computeinstanz.

* Öffnen Sie im geklonten Ordner *tutorials/image-classification-mnist-data* das Notebook *img-classification-part1-training.ipynb*. 


Das Tutorial und die zugehörige Datei **utils.py** sind auch auf [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) verfügbar, falls Sie sie in Ihrer eigenen [lokalen Umgebung](how-to-configure-environment.md#local) verwenden möchten. Führen Sie `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` aus, um Abhängigkeiten für dieses Tutorial zu installieren.

> [!Important]
> Der Rest dieses Artikels enthält denselben Inhalt, den Sie auch im Notebook sehen.  
>
> Wechseln Sie nun zur Jupyter Notebook-Instanz, wenn Sie während der Ausführung des Codes mitlesen möchten. 
> Klicken Sie zum Ausführen einer einzelnen Codezelle in einem Notebook auf die gewünschte Codezelle, und drücken Sie **UMSCHALT+EINGABE**. Oder führen Sie das gesamte Notebook aus, indem Sie auf der oberen Symbolleiste **Alle ausführen** auswählen.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Einrichten Ihrer Entwicklungsumgebung

Das gesamte Setup für Ihre Entwicklungsarbeit kann in einem Python Notebook erfolgen. Die Einrichtung umfasst die folgenden Aktionen:

* Importieren von Python-Paketen.
* Herstellen einer Verbindung mit einem Arbeitsbereich, damit lokale Computer mit Remoteressourcen kommunizieren können.
* Erstellen eines Experiments zum Nachverfolgen aller Ihrer Ausführungen.
* Erstellen eines Remotecomputeziels für das Training.

### <a name="import-packages"></a>Importieren von Paketen

Importieren Sie die Python-Pakete, die Sie in dieser Sitzung benötigen. Zeigen Sie außerdem die Azure Machine Learning SDK-Version an:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Stellen Sie eine Verbindung mit einem Arbeitsbereich her.

Erstellen Sie ein Arbeitsbereichsobjekt aus dem vorhandenen Arbeitsbereich. `Workspace.from_config()` liest die Datei **config.JSON** und lädt die Details in ein Objekt namens `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Erstellen eines Experiments

Erstellen Sie ein Experiment, um die Ausführungen in Ihrem Arbeitsbereich nachzuverfolgen. Ein Arbeitsbereich kann über mehrere Experimente verfügen:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Erstellen oder Anfügen eines vorhandenen Computeziels

Unter Verwendung von Azure Machine Learning Compute, einem verwalteten Dienst, können Datenanalysten Machine Learning-Modelle in Clustern von virtuellen Azure-Computern trainieren. Beispiele hierfür sind unter anderem VMs mit GPU-Unterstützung. In diesem Tutorial erstellen Sie Azure Machine Learning Compute als Trainingsumgebung. Sie übermitteln Python-Code, der auf diesem virtuellen Computer ausgeführt wird, weiter unten in diesem Tutorial. 

Mit dem folgenden Code werden die Computecluster für Sie erstellt, sofern sie in Ihrem Arbeitsbereich noch nicht vorhanden sind. Ein Cluster wird eingerichtet, der auf 0 herunterskaliert wird, wenn er nicht verwendet wird, und der auf maximal vier Knoten hochskaliert werden kann. 

 **Die Erstellung des Computeziels dauert etwa fünf Minuten.** Wenn die Computeressource bereits im Arbeitsbereich enthalten ist, wird sie vom Code verwendet, und der Erstellungsvorgang wird übersprungen.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Sie verfügen jetzt über die erforderlichen Pakete und Computeressourcen, die Sie zum Trainieren eines Modells in der Cloud benötigen. 

## <a name="explore-data"></a>Durchsuchen von Daten

Bevor Sie ein Modell trainieren, müssen Sie die Daten verstehen, die zum Trainieren verwendet werden. In diesem Abschnitt lernen Sie Folgendes:

* Herunterladen des MNIST-Datasets.
* Anzeigen einiger Beispielbilder.

### <a name="download-the-mnist-dataset"></a>Laden des MNIST-Datasets

Verwenden Sie Azure Open Datasets, um die unformatierten MNIST-Datendateien abzurufen. [Öffentliche Azure-Datasets](../open-datasets/overview-what-are-open-datasets.md) sind kuratierte öffentliche Datasets, mit denen Sie Lösungen mit maschinellem Lernen szenariospezifische Features hinzufügen können, um genauere Modelle zu erzielen. Jedes Dataset verfügt über eine entsprechende Klasse (hier `MNIST`), um die Daten auf unterschiedliche Weise abzurufen.

Mit diesem Code werden die Daten als `FileDataset`-Objekt abgerufen, bei dem es sich um eine Unterklasse von `Dataset` handelt. Ein `FileDataset`-Objekt verweist auf eine einzelne oder mehrere Dateien beliebigen Formats in Ihren Datenspeichern oder unter öffentlichen URLs. Bei dieser Klasse haben Sie die Möglichkeit, die Dateien für Ihre Computeumgebung herunterzuladen oder bereitzustellen, indem Sie einen Verweis auf den Speicherort der Datenquelle erstellen. Außerdem registrieren Sie das Dataset unter Ihrem Arbeitsbereich, um das einfache Abrufen während des Trainings zu ermöglichen.

Verwenden Sie den [Leitfaden](how-to-create-register-datasets.md), um weitere Informationen zu Datasets und ihrer Nutzung im SDK zu erhalten.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Anzeigen einiger Beispielbilder

Laden Sie die komprimierten Dateien in `numpy`-Arrays. Verwenden Sie dann `matplotlib`, um 30 zufällige Bilder aus dem Dataset mit den zugehörigen Bezeichnungen darüber zu zeichnen. Für diesen Schritt ist eine `load_data`-Funktion erforderlich, die in einer Datei `utils.py` enthalten ist. Diese Datei befindet sich im Beispielordner. Achten Sie darauf, dass sie sich im gleichen Ordner wie dieses Notebook befindet. Die `load_data`-Funktion analysiert einfach die komprimierten Dateien in NumPy-Arrays.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Eine zufällige Stichprobe von Bildern wird angezeigt:

![Zufällige Stichprobe von Bildern](./media/tutorial-train-models-with-aml/digits.png)

Sie haben jetzt einen Eindruck vom Aussehen dieser Bilder und vom erwarteten Vorhersageergebnis.

## <a name="train-on-a-remote-cluster"></a>Trainieren in einem Remotecluster

Für diese Aufgabe übermitteln Sie den Auftrag zur Ausführung im Remotetrainingscluster, den Sie zuvor eingerichtet haben.  So senden Sie einen Auftrag
* Erstellen eines Verzeichnisses
* Erstellen eines Trainingsskripts
* Erstellen einer Skriptausführungskonfiguration
* Übermitteln des Auftrags

### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie ein Verzeichnis, um den erforderlichen Code von Ihrem Computer an die Remoteressource zu übermitteln.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Erstellen eines Trainingsskripts

Um den Auftrag an den Cluster zu übermitteln, erstellen Sie zuerst ein Trainingsskript. Führen Sie folgenden Code aus, um in dem soeben erstellten Verzeichnis ein Trainingsskript namens `train.py` zu erstellen.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Beachten Sie, wie das Skript Daten abruft und Modelle speichert:

+ Das Trainingsskript liest ein Argument, um das Verzeichnis mit den Daten zu finden. Wenn Sie den Auftrag später senden, verweisen Sie auf den Datenspeicher für dieses Argument: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Das Trainingsskript speichert Ihr Modell in einem Verzeichnis namens **outputs**. Alles, was in dieses Verzeichnis geschrieben wurde, wird automatisch in Ihren Arbeitsbereich hochgeladen. Von diesem Verzeichnis aus greifen Sie im weiteren Verlauf dieses Tutorials auf Ihr Modell zu. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Das Trainingsskript erfordert die Datei `utils.py`, damit das Dataset ordnungsgemäß geladen wird. Der folgende Code kopiert `utils.py` in `script_folder`, damit in der Remoteressource auf die Datei und das Trainingsskript zugegriffen werden kann.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>Konfigurieren des Trainingsauftrags

Erstellen Sie ein [ScriptRunConfig-](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) Objekt, um die Konfigurationsdetails Ihres Trainingsauftrags anzugeben, einschließlich Ihres Trainingsskripts, der zu verwendenden Umgebung und des Computeziels für die Ausführung. Konfigurieren Sie ScriptRunConfig, indem Sie Folgendes angeben:

* Das Verzeichnis, das Ihre Skripts enthält. Alle Dateien in dieses Verzeichnis werden zur Ausführung in die Clusterknoten hochgeladen.
* Das Computeziel. In diesem Fall verwenden Sie den von Ihnen erstellten Azure Machine Learning-Computecluster.
* Den Namen des Trainingsskripts, **train.py**.
* Eine Umgebung, die die erforderlichen Bibliotheken zum Ausführen des Skripts enthält.
* Die für das Trainingsskript erforderlichen Argumente.

In diesem Tutorial wird der AmlCompute-Cluster als Ziel verwendet. Alle Dateien im Skriptordner werden zur Ausführung in die Clusterknoten hochgeladen. **--data_folder** wird auf die Verwendung des Datasets festgelegt.

Erstellen Sie zunächst eine Umgebung, die Folgendes enthält: die Scikit-learn-Bibliothek, azureml-dataset-runtime (erforderlich für den Zugriff auf das Dataset) und azureml-defaults (enthält die Abhängigkeiten für die Protokollierung von Metriken). Das azureml-defaults-Paket enthält auch die Abhängigkeiten, die in Teil 2 des Tutorials für die Bereitstellung des Modells als Webdienst benötigt werden.

Nachdem Sie die Umgebung definiert haben, registrieren Sie sie im Arbeitsbereich, damit Sie sie in Teil 2 des Tutorials wiederverwenden können.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Erstellen Sie dann die ScriptRunConfig-Datei, indem Sie das Trainingsskript, das Computeziel und die Umgebung angeben.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Übermitteln des Auftrags an den Cluster

Führen Sie das Experiment aus, indem Sie das ScriptRunConfig-Objekt übermitteln:

```python
run = exp.submit(config=src)
run
```

Da der Aufruf asynchron erfolgt, wird direkt nach dem Start des Auftrags ein **Vorbereitung-** oder **Ausführungsstatus** zurückgegeben.

## <a name="monitor-a-remote-run"></a>Überwachen einer Remoteausführung

Insgesamt dauert die erste Ausführung **ungefähr 10 Minuten**. Solange sich die Skriptabhängigkeiten nicht ändern, wird bei nachfolgenden Ausführungen jedoch dasselbe Image wiederverwendet. Daher wird der Container wesentlich schneller gestartet.

Was geschieht, während Sie warten:

- **Erstellen von Images**: Ein Docker-Image wird erstellt, das der von der Azure ML-Umgebung angegebenen Python-Umgebung entspricht. Das Image wird in den Arbeitsbereich hochgeladen. Das Erstellen und Hochladen des Images nimmt **etwa fünf Minuten** in Anspruch.

  Diese Phase erfolgt für jede Python-Umgebung einmal, weil der Container für nachfolgende Ausführungen zwischengespeichert wird. Während der Imageerstellung werden Protokolle in den Ausführungsverlauf gestreamt. Anhand dieser Protokolle können Sie den Fortschritt der Imageerstellung überwachen.

- **Skalierung**: Wenn der Remotecluster zum Ausführen mehr Knoten benötigt, als derzeit verfügbar sind, werden weitere Knoten automatisch hinzugefügt. Die Skalierung dauert normalerweise **etwa fünf Minuten**.

- **Wird ausgeführt:** In dieser Phase werden die erforderlichen Skripts und Dateien an das Computeziel gesendet. Anschließend werden Datenspeicher eingebunden oder kopiert. Dann wird das **entry_script** ausgeführt. Während der Auftrag ausgeführt wird, werden **stdout** und das Verzeichnis **./logs** an den Ausführungsverlauf gestreamt. Anhand dieser Protokolle können Sie den Fortschritt der Ausführung überwachen.

- **Nachbearbeitung**: Das Verzeichnis **./outputs** der Ausführung wird über den Ausführungsverlauf in Ihrem Arbeitsbereich kopiert, sodass Sie auf diese Ergebnisse zugreifen können.

Sie können den Verlauf eines Auftrags während seiner Ausführung auf mehrere Arten überprüfen. In diesem Tutorial werden ein Jupyter-Widget und eine `wait_for_completion`-Methode verwendet.

### <a name="jupyter-widget"></a>Jupyter-Widget

Sehen Sie sich den Verlauf der Ausführung mit einem [Jupyter-Widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) an. Ebenso wie die Übermittlung der Ausführung ist das Widget asynchron und stellt alle 10 bis 15 Sekunden Liveupdates bereit, bis der Auftrag abgeschlossen ist:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Das Widget sieht am Ende des Trainings wie folgt aus:

![Notebook-Widget](./media/tutorial-train-models-with-aml/widget.png)

Wenn Sie eine Ausführung abbrechen möchten, befolgen Sie [diese Anweisungen](./how-to-manage-runs.md).

### <a name="get-log-results-upon-completion"></a>Abrufen von Protokollergebnissen nach Abschluss

Das Modelltraining und die Überwachung werden im Hintergrund ausgeführt. Warten Sie, bis das Training des Modells abgeschlossen ist, bevor Sie weiteren Code ausführen. Verwenden Sie `wait_for_completion`, um anzuzeigen, wenn das Modelltraining abgeschlossen ist:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Anzeigen von Ausführungsergebnissen

Sie verfügen jetzt über ein Modell, das in einem Remotecluster trainiert wurde. Rufen Sie die Genauigkeit des Modells ab:

```python
print(run.get_metrics())
```

Die Ausgabe zeigt, dass das Remotemodell eine Genauigkeit von 0,9204 aufweist:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

Im nächsten Tutorial untersuchen Sie dieses Modell noch ausführlicher.

## <a name="register-model"></a>Registrieren des Modells

Im letzten Schritt des Trainingsskripts wurde die Datei `outputs/sklearn_mnist_model.pkl` in ein Verzeichnis namens `outputs` auf dem virtuellen Computer des Clusters geschrieben, in dem der Auftrag ausgeführt wird. `outputs` ist ein spezielles Verzeichnis, weil alle Inhalte dieses Verzeichnisses automatisch in Ihren Arbeitsbereich hochgeladen werden. Diese Inhalte werden in der Ausführungsaufzeichnung im Experiment unter Ihrem Arbeitsbereich angezeigt. Daher steht die Modelldatei jetzt auch in Ihrem Arbeitsbereich zur Verfügung.

Sie können Dateien anzeigen, die dieser Ausführung zugeordnet sind:

```python
print(run.get_file_names())
```

Registrieren Sie das Modell im Arbeitsbereich, damit Sie (oder andere Mitarbeiter) dieses Modell später abfragen, untersuchen und bereitstellen können:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Sie können auch nur den Azure Machine Learning-Computecluster löschen. Die automatische Skalierung ist jedoch aktiviert, und die Clustermindestanzahl beträgt null. Deshalb entstehen durch diese spezielle Ressource bei Nichtverwendung keine zusätzlichen Computegebühren:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial für Azure Machine Learning haben Sie Python für folgende Aufgaben verwendet:

> [!div class="checklist"]
> * Einrichten Ihrer Entwicklungsumgebung.
> * Zugreifen auf und Untersuchen von Daten.
> * Trainieren mehrerer Modelle auf einem Remotecluster mit der beliebten Bibliothek „scikit-learn“ für maschinelles Lernen
> * Überprüfen der Trainingsdetails und Registrieren des besten Modells.

Jetzt können Sie dieses registrierte Modell anhand der Anweisungen im nächsten Teil der Tutorialreihe bereitstellen:

> [!div class="nextstepaction"]
> [Tutorial 2: Bereitstellen von Modellen](tutorial-deploy-models-with-aml.md)
