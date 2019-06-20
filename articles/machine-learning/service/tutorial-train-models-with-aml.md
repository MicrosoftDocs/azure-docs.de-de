---
title: 'Tutorial zur Bildklassifizierung: Trainieren von Modellen'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie mit Azure Machine Learning Service ein Bildklassifizierungsmodell mit Scikit-learn in einem Python Jupyter Notebook trainiert wird. Dieses Tutorial ist der erste Teil einer zweiteiligen Reihe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: ed2b35c5a1a0a017cb6bea086601282c83956d88
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515551"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Tutorial: Trainieren von Bildklassifikationsmodellen mit MNIST-Daten und Scikit-learn mithilfe von Azure Machine Learning

In diesem Tutorial wird ein Modell für maschinelles Lernen auf Remotecomputeressourcen trainiert. Hierbei wird der Trainings- und Bereitstellungsworkflow für Azure Machine Learning Service (Vorschau) in einem Python Jupyter Notebook verwendet.  Anschließend können Sie das Notebook als Vorlage verwenden, um Ihr eigenes Machine Learning-Modell mit Ihren eigenen Daten zu trainieren. Dieses Tutorial ist der **erste Teil einer zweiteiligen Reihe**.  

In diesem Tutorial wird eine einfache logistische Regression anhand des [MNIST](http://yann.lecun.com/exdb/mnist/)-Datasets und [scikit-learn](https://scikit-learn.org) mit dem Azure Machine Learning Service trainiert. MNIST ist ein populäres Dataset, das aus 70.000 Graustufenbildern besteht. Jedes Bild ist eine handgeschriebene Ziffer von null bis neun im Format von 28 × 28 Pixeln. Das Ziel besteht darin, einen Multiklassen-Klassifizierer zu erstellen, um die in einem bestimmten Bild dargestellte Ziffer zu erkennen.

Erfahren Sie, wie Sie die folgenden Maßnahmen durchführen:

> [!div class="checklist"]
> * Einrichten Ihrer Entwicklungsumgebung.
> * Zugreifen auf und Untersuchen von Daten.
> * Trainieren Sie ein einfaches logistisches Regressionsmodell für einen Remotecluster.
> * Überprüfen der Trainingsergebnisse und Registrieren des besten Modells.

In [Teil 2 dieses Tutorials](tutorial-deploy-models-with-aml.md) erfahren Sie, wie Sie ein Modell auswählen und bereitstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

>[!NOTE]
> Der Code in diesem Artikel wurde mit Version 1.0.41 des Azure Machine Learning SDK getestet.

## <a name="prerequisites"></a>Voraussetzungen

Fahren Sie mit dem Abschnitt [Einrichten Ihrer Entwicklungsumgebung](#start) fort, um mehr zu den Schritten mit Notebooks zu erfahren, oder rufen Sie anhand der nachfolgenden Anweisungen das Notebook ab, und führen Sie es unter Azure Notebooks oder auf Ihrem eigenen Notebook-Server aus.  Für die Ausführung des Notebooks ist Folgendes erforderlich:

* Ein Python 3.6-Notebook-Server mit folgenden Komponenten:
    * Das Azure Machine Learning SDK für Python
    * `matplotlib` und `scikit-learn`
* Das Tutorial-Notebook und die Datei **utils.py**
* Ein Arbeitsbereich für maschinelles Lernen
* Die Konfigurationsdatei für den Arbeitsbereich muss sich im gleichen Verzeichnis wie das Notebook befinden.

Wie Sie diese erforderlichen Komponenten beziehen, erfahren Sie in den jeweiligen Abschnitten:

* Verwenden eines [cloudbasierten Notebook-Servers in Ihrem Arbeitsbereich](#azure)
* Verwenden [Ihres eigenen Notebook-Servers](#server)

### <a name="azure"></a>Verwenden eines cloudbasierten Notebook-Servers in Ihrem Arbeitsbereich

Die ersten Schritte mit Ihrem eigenen cloudbasierten Notebook-Server sind ganz einfach. Das [Azure Machine Learning SDK für Python](https://aka.ms/aml-sdk) wird für Sie installiert und konfiguriert, wenn Sie diese Cloudressource erstellen.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Öffnen Sie nach dem Aufrufen der Webseite des Notebooks das Notebook **tutorials/img-classification-part1-training.ipynb**.

### <a name="server"></a>Verwenden Ihres eigenen Jupyter Notebook-Servers

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 Führen Sie nach Ausführung der Schritte in Ihrem geklonten Verzeichnis das Notebook **tutorials/img-classification-part1-training.ipynb** aus.

## <a name="start"></a>Einrichten Ihrer Entwicklungsumgebung

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
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>Erstellen eines Experiments

Erstellen Sie ein Experiment, um die Ausführungen in Ihrem Arbeitsbereich nachzuverfolgen. Ein Arbeitsbereich kann über mehrere Experimente verfügen:

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Erstellen oder Anfügen einer vorhandenen Computeressource

Unter Verwendung von Azure Machine Learning Compute, einem verwalteten Dienst, können Datenanalysten Machine Learning-Modelle in Clustern von virtuellen Azure-Computern trainieren. Beispiele hierfür sind unter anderem VMs mit GPU-Unterstützung. In diesem Tutorial erstellen Sie Azure Machine Learning Compute als Trainingsumgebung. Mit dem folgenden Code werden die Computecluster für Sie erstellt, sofern sie in Ihrem Arbeitsbereich noch nicht vorhanden sind.

 **Die Erstellung der Computecluster dauert etwa fünf Minuten.** Wenn der Computecluster bereits im Arbeitsbereich enthalten ist, wird er vom Code verwendet, und der Erstellungsvorgang wird übersprungen.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes,
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)

     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Sie verfügen jetzt über die erforderlichen Pakete und Computeressourcen, die Sie zum Trainieren eines Modells in der Cloud benötigen.

## <a name="explore-data"></a>Erkunden von Daten

Bevor Sie ein Modell trainieren, müssen Sie die Daten verstehen, die zum Trainieren verwendet werden. Sie müssen außerdem die Daten in die Cloud kopieren. Dann sind sie für Ihre Cloudtrainingsumgebung zugänglich. In diesem Abschnitt erfahren Sie, wie Sie die folgenden Maßnahmen durchführen:

* Herunterladen des MNIST-Datasets.
* Anzeigen einiger Beispielbilder.
* Hochladen von Daten in die Cloud.

### <a name="download-the-mnist-dataset"></a>Laden des MNIST-Datasets

Laden Sie das MNIST-Dataset, und speichern Sie die Dateien lokal in einem `data`-Verzeichnis. Bilder und Bezeichnungen für Trainings und Tests werden heruntergeladen:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```

Eine Ausgabe ähnlich der folgenden wird angezeigt: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```.

### <a name="display-some-sample-images"></a>Anzeigen einiger Beispielbilder

Laden Sie die komprimierten Dateien in `numpy`-Arrays. Verwenden Sie dann `matplotlib`, um 30 zufällige Bilder aus dem Dataset mit den zugehörigen Bezeichnungen darüber zu zeichnen. Für diesen Schritt ist eine `load_data`-Funktion erforderlich, die in einer Datei `util.py` enthalten ist. Diese Datei befindet sich im Beispielordner. Achten Sie darauf, dass sie sich im gleichen Ordner wie dieses Notebook befindet. Die `load_data`-Funktion analysiert einfach die komprimierten Dateien in NumPy-Arrays:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
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

### <a name="upload-data-to-the-cloud"></a>Hochladen von Daten in die Cloud

Machen Sie die Daten jetzt für den Remotezugriff verfügbar, indem Sie die Daten von Ihrem lokalen Computer in Azure hochladen. Dann sind sie für das Remotetraining zugänglich. Der Datenspeicher ist ein praktisches Konstrukt, das Ihrem Arbeitsbereich zugeordnet wird, damit Sie Daten hoch- und herunterladen können. Sie können damit auch von Ihren Remotecomputezielen aus interagieren. Der Speicher wird durch das Azure Blob Storage-Konto unterstützt.

Die MNIST-Dateien werden in ein Verzeichnis namens `mnist` hochgeladen, das sich im Stammverzeichnis des Datenspeichers befindet:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
```

Jetzt haben Sie alles, was Sie zum Trainieren eines Modells benötigen.

## <a name="train-on-a-remote-cluster"></a>Trainieren in einem Remotecluster

Für diese Aufgabe übermitteln Sie den Auftrag an den Remotetrainingscluster, den Sie zuvor eingerichtet haben.  So senden Sie einen Auftrag
* Erstellen eines Verzeichnisses
* Erstellen eines Trainingsskripts
* Erstellen eines estimator-Objekts
* Übermitteln des Auftrags

### <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Erstellen Sie ein Verzeichnis, um den erforderlichen Code von Ihrem Computer an die Remoteressource zu übermitteln.

```python
import os
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Erstellen eines Trainingsskripts

Um den Auftrag an den Cluster zu übermitteln, erstellen Sie zuerst ein Trainingsskript. Führen Sie folgenden Code aus, um in dem soeben erstellten Verzeichnis ein Trainingsskript namens `train.py` zu erstellen.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
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

### <a name="create-an-estimator"></a>Erstellen eines Estimators

Ein [SKLearn Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)-Objekt wird verwendet, um die Ausführung zu übermitteln. Erstellen Sie Ihren Estimator durch Ausführen des folgenden Codes, um diese Elemente zu definieren:

* Den Namen des Estimator-Objekts, `est`.
* Das Verzeichnis, das Ihre Skripts enthält. Alle Dateien in dieses Verzeichnis werden zur Ausführung in die Clusterknoten hochgeladen.
* Das Computeziel. In diesem Fall verwenden Sie den von Ihnen erstellten Azure Machine Learning-Computecluster.
* Den Namen des Trainingsskripts, **train.py**.
* Die für das Trainingsskript erforderlichen Parameter.

In diesem Tutorial wird der AmlCompute-Cluster als Ziel verwendet. Alle Dateien im Skriptordner werden zur Ausführung in die Clusterknoten hochgeladen. **data_folder** wird auf die Verwendung des Datenspeichers (`ds.path('mnist').as_mount()`) festgelegt:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Übermitteln des Auftrags an den Cluster

Führen Sie das Experiment aus, indem Sie das Estimator-Objekt übermitteln:

```python
run = exp.submit(config=est)
run
```

Da der Aufruf asynchron erfolgt, wird direkt nach dem Start des Auftrags ein **Vorbereitung-** oder **Ausführungsstatus** zurückgegeben.

## <a name="monitor-a-remote-run"></a>Überwachen einer Remoteausführung

Insgesamt dauert die erste Ausführung **ungefähr 10 Minuten**. Solange sich die Skriptabhängigkeiten nicht ändern, wird bei nachfolgenden Ausführungen jedoch dasselbe Image wiederverwendet. Daher wird der Container wesentlich schneller gestartet.

Was geschieht, während Sie warten:

- **Erstellen von Images**: Ein Docker-Image wird erstellt, das der vom Estimator angegebenen Python-Umgebung entspricht. Das Image wird in den Arbeitsbereich hochgeladen. Das Erstellen und Hochladen des Images nimmt **etwa fünf Minuten** in Anspruch.

  Diese Phase erfolgt für jede Python-Umgebung einmal, weil der Container für nachfolgende Ausführungen zwischengespeichert wird. Während der Imageerstellung werden Protokolle in den Ausführungsverlauf gestreamt. Anhand dieser Protokolle können Sie den Fortschritt der Imageerstellung überwachen.

- **Skalierung**: Wenn der Remotecluster zum Ausführen mehr Knoten benötigt, als derzeit verfügbar sind, werden weitere Knoten automatisch hinzugefügt. Die Skalierung dauert normalerweise **etwa fünf Minuten**.

- **Running**: In dieser Phase werden die erforderlichen Skripts und Dateien an das Computeziel gesendet. Anschließend werden Datenspeicher eingebunden oder kopiert. Dann wird das **entry_script** ausgeführt. Während der Auftrag ausgeführt wird, werden **stdout** und das Verzeichnis **./logs** an den Ausführungsverlauf gestreamt. Anhand dieser Protokolle können Sie den Fortschritt der Ausführung überwachen.

- **Nachbearbeitung**: Das Verzeichnis **./outputs** der Ausführung wird über den Ausführungsverlauf in Ihrem Arbeitsbereich kopiert, sodass Sie auf diese Ergebnisse zugreifen können.

Sie können den Verlauf eines Auftrags während seiner Ausführung auf mehrere Arten überprüfen. In diesem Tutorial werden ein Jupyter-Widget und eine `wait_for_completion`-Methode verwendet.

### <a name="jupyter-widget"></a>Jupyter-Widget

Sehen Sie sich den Verlauf der Ausführung mit einem Jupyter-Widget an. Ebenso wie die Übermittlung der Ausführung ist das Widget asynchron und stellt alle 10 bis 15 Sekunden Liveupdates bereit, bis der Auftrag abgeschlossen ist:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Das Widget sieht am Ende des Trainings wie folgt aus:

![Notebook-Widget](./media/tutorial-train-models-with-aml/widget.png)

Wenn Sie eine Ausführung abbrechen möchten, befolgen Sie [diese Anweisungen](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Abrufen von Protokollergebnissen nach Abschluss

Das Modelltraining und die Überwachung werden im Hintergrund ausgeführt. Warten Sie, bis das Training des Modells abgeschlossen ist, bevor Sie weiteren Code ausführen. Verwenden Sie `wait_for_completion`, um anzuzeigen, wenn das Modelltraining abgeschlossen ist:

```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
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
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Sie können auch nur den Azure Machine Learning-Computecluster löschen. Die automatische Skalierung ist jedoch aktiviert, und die Clustermindestanzahl beträgt null. Deshalb entstehen durch diese spezielle Ressource bei Nichtverwendung keine zusätzlichen Computegebühren:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial für Azure Machine Learning Service haben Sie Python für folgende Aufgaben verwendet:

> [!div class="checklist"]
> * Einrichten Ihrer Entwicklungsumgebung.
> * Zugreifen auf und Untersuchen von Daten.
> * Trainieren mehrerer Modelle auf einem Remotecluster mit der beliebten Bibliothek „scikit-learn“ für maschinelles Lernen
> * Überprüfen der Trainingsdetails und Registrieren des besten Modells.

Jetzt können Sie dieses registrierte Modell anhand der Anweisungen im nächsten Teil der Tutorialreihe bereitstellen:

> [!div class="nextstepaction"]
> [Tutorial 2: Bereitstellen von Modellen](tutorial-deploy-models-with-aml.md)
