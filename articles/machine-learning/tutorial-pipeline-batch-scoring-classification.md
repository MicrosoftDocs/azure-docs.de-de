---
title: 'Tutorial: ML-Pipelines für die Batchbewertung'
titleSuffix: Azure Machine Learning
description: In diesem Tutorial erstellen Sie eine Machine Learning-Pipeline zum Durchführen der Batchbewertung. Konzentrieren Sie sich auf Machine Learning, ohne sich um die Infrastruktur und Automatisierung kümmern zu müssen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperf-fy20q4, devx-track-python
ms.openlocfilehash: b0b415cce37e464abcba9fab5ad4c1196b1b2e1b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033475"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Tutorial: Erstellen einer Azure Machine Learning-Pipeline für die Batchbewertung



In diesem Tutorial für Fortgeschrittene erfahren Sie, wie Sie eine [Azure Machine Learning-Pipeline](concept-ml-pipelines.md) erstellen, um einen Batchbewertungsauftrag auszuführen. Machine Learning-Pipelines optimieren Ihren Workflow durch eine Verbesserung in den Bereichen Geschwindigkeit, Portabilität und Wiederverwendung, sodass Sie sich auf Machine Learning konzentrieren können, anstatt sich um Infrastruktur und Automatisierung kümmern zu müssen. Nach dem Erstellen und Veröffentlichen einer Pipeline konfigurieren Sie einen REST-Endpunkt, mit dem Sie die Pipeline von einer beliebigen HTTP-Bibliothek auf einer beliebigen Plattform aus auslösen. 

In diesem Beispiel wird ein vorab trainiertes Modell [Inception-V3](https://arxiv.org/abs/1512.00567) eines künstlichen neuronalen Netzes (Convolutional Neural Network, CNN) verwendet, das in TensorFlow implementiert wird, um Bilder ohne Bezeichnung zu klassifizieren. 

In diesem Tutorial führen Sie die folgenden Aufgaben durch:

> [!div class="checklist"]
> * Konfigurieren des Arbeitsbereichs 
> * Herunterladen und Speichern von Beispieldaten
> * Erstellen von Datasetobjekten zum Abrufen und Ausgeben von Daten
> * Herunterladen, Vorbereiten und Registrieren des Modells in Ihrem Arbeitsbereich
> * Bereitstellen von Computezielen und Erstellen eines Bewertungsskripts
> * Verwenden der `ParallelRunStep`-Klasse für die asynchrone Batchbewertung
> * Erstellen, Ausführen und Veröffentlichen einer Pipeline
> * Aktivieren eines REST-Endpunkts für die Pipeline

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie [Teil 1 des Einrichtungstutorials](tutorial-1st-experiment-sdk-setup.md), falls Sie noch nicht über einen Azure Machine Learning-Arbeitsbereich oder einen virtuellen Notebook-Computer verfügen.
* Verwenden Sie nach Abschluss des Einrichtungstutorials den gleichen Notebook-Server, um das Notebook *tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb* zu öffnen.

Falls Sie das Einrichtungstutorial in Ihrer eigenen [lokalen Umgebung](how-to-configure-environment.md#local) ausführen möchten, können Sie über [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) auf das Tutorial zugreifen. Führen Sie `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` aus, um die erforderlichen Pakete abzurufen.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurieren eines Arbeitsbereichs und Erstellen eines Datenspeichers

Erstellen Sie ein Arbeitsbereichsobjekt auf der Grundlage des vorhandenen Azure Machine Learning-Arbeitsbereichs.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> In diesem Codeausschnitt wird davon ausgegangen, dass die Arbeitsbereichskonfiguration im aktuellen Verzeichnis oder in dessen übergeordnetem Verzeichnis gespeichert wird. Weitere Informationen zum Erstellen eines Arbeitsbereichs finden Sie unter [Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen](how-to-manage-workspace.md). Weitere Informationen zum Speichern der Konfiguration in einer Datei finden Sie unter [Konfigurieren einer Entwicklungsumgebung für Azure Machine Learning](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Erstellen eines Datenspeichers für Beispielbilder

Rufen Sie aus dem öffentlichen Blobcontainer `sampledata` für das Konto `pipelinedata` das öffentliche ImageNet-Bewertungsdatenbeispiel ab. Rufen Sie `register_azure_blob_container()` auf, um die Daten im Arbeitsbereich unter dem Namen `images_datastore` verfügbar zu machen. Legen Sie dann den Standarddatenspeicher des Arbeitsbereichs als Ausgabedatenspeicher fest. Verwenden Sie den Ausgabedatenspeicher, um die Ausgabe in der Pipeline zu bewerten.

Weitere Informationen zum Datenzugriff finden Sie unter [Zugreifen auf Daten](./how-to-access-data.md).

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Erstellen von Datasetobjekten

Bei der Pipelineerstellung werden Objekte vom Typ `Dataset` zum Lesen von Daten aus Arbeitsbereich-Datenspeichern und Objekte vom Typ `PipelineData` zum Übertragen von Zwischendaten zwischen Pipelineschritten verwendet.

> [!Important]
> Das Beispiel für die Batchbewertung in diesem Tutorial verwendet nur einen Pipelineschritt. In Anwendungsfällen mit mehreren Schritten umfasst der typische Flow die folgenden Schritte:
>
> 1. Verwendung von Objekten vom Typ `Dataset` als *Eingaben* (zum Abrufen von Rohdaten), Ausführung von Transformationen sowie anschließende *Ausgabe* eines Objekts vom Typ `PipelineData`
>
> 2. Verwendung des *Ausgabeobjekts* `PipelineData` aus dem vorherigen Schritt als *Eingabeobjekt*. Wiederholen Sie diesen Vorgang für nachfolgende Schritte.

In diesem Szenario werden Objekte vom Typ `Dataset` erstellt, die den Datenspeicherverzeichnissen für Eingabebilder und Klassifizierungsbezeichnungen (y-Testwerte) entsprechen. Außerdem wird ein Objekt vom Typ `PipelineData` für die Ausgabedaten der Batchbewertung erstellt.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Registrieren Sie die Datasets im Arbeitsbereich, wenn Sie sie später wiederverwenden möchten. Dieser Schritt ist optional.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Herunterladen und Registrieren des Modells

Laden Sie das vortrainierte TensorFlow-Modell herunter, um es für die Batchbewertung in einer Pipeline zu verwenden. Erstellen Sie zunächst ein lokales Verzeichnis zum Speichern des Modells. Laden Sie das Modell anschließend herunter, und extrahieren Sie es.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Registrieren Sie als Nächstes das Modell bei Ihrem Arbeitsbereich, um es im Pipelineprozess problemlos abrufen zu können. In der statischen Funktion `register()` ist der Parameter `model_name` der Schlüssel, den Sie innerhalb des SDK verwenden, um Ihr Modell zu finden.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Erstellen und Anfügen des Remotecomputeziels

Machine Learning-Pipelines können nicht lokal ausgeführt werden, daher führen Sie sie in Cloudressourcen oder auf *Remotecomputezielen* aus. Ein Remotecomputeziel ist eine wiederverwendbare virtuelle Compute-Umgebung, in der Sie Experimente und Machine Learning-Workflows ausführen. 

Führen Sie den folgenden Code aus, um ein GPU-fähiges Ziel vom Typ [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) zu erstellen, und fügen Sie es an Ihren Arbeitsbereich an. Weitere Informationen zu Computezielen finden Sie in [diesem Konzeptartikel](./concept-compute-target.md).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Schreiben eines Bewertungsskripts

Erstellen Sie für die Bewertung ein Batchbewertungsskript namens `batch_scoring.py`, und schreiben Sie es in das aktuelle Verzeichnis. Das Skript nimmt Eingabebilder entgegen, wendet das Klassifizierungsmodell an und gibt die Vorhersagen in eine Ergebnisdatei aus.

Das Skript `batch_scoring.py` akzeptiert folgende Parameter. Sie werden vom später erstellten `ParallelRunStep`-Element übergeben:

- `--model_name`: Der Name des verwendeten Modells
- `--labels_dir`: Der Speicherort der Datei `labels.txt`.

Die Pipelineinfrastruktur verwendet die Klasse `ArgumentParser`, um Parameter an Pipelineschritte zu übergeben. Im folgenden Code erhält beispielsweise das erste Argument (`--model_name`) den Eigenschaftsbezeichner `model_name`. In der Funktion `init()` wird `Model.get_model_path(args.model_name)` für den Zugriff auf diese Eigenschaft verwendet.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Die Pipeline in diesem Tutorial enthält nur einen Schritt, und die Ausgabe wird in eine Datei geschrieben. Für Pipelines mit mehreren Schritten verwenden Sie zudem `ArgumentParser` zum Definieren eines Verzeichnisses, um Ausgabedaten für die Eingabe in nachfolgende Schritte zu schreiben. Im `ArgumentParser`Notebook[ finden Sie ein Beispiel für die Übergabe von Daten zwischen mehreren Pipelineschritten unter Verwendung des Entwurfsmusters ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Erstellen der Pipeline

Bevor Sie die Pipeline ausführen können, müssen Sie ein Objekt erstellen, das die Python-Umgebung definiert und die erforderlichen Abhängigkeiten für Ihr Skript (`batch_scoring.py`) erstellt. Die wichtigste erforderliche Abhängigkeit ist TensorFlow, aber Sie installieren auch `azureml-core` und `azureml-dataprep[fuse]`, die für ParallelRunStep erforderlich sind. Geben Sie auch Docker- und Docker-GPU-Unterstützung an.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Erstellen der Konfiguration zum Umschließen des Skripts

Erstellen Sie den Pipelineschritt mithilfe des Skripts, der Umgebungskonfiguration und der Parameter. Geben Sie das Computeziel an, das Sie bereits an Ihren Arbeitsbereich angefügt haben.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Erstellen des Pipelineschritts

Ein Pipelineschritt ist ein Objekt, das alles kapselt, was Sie zum Ausführen einer Pipeline benötigen:

* Umgebungs- und Abhängigkeitseinstellungen
* Die Computeressource für die Pipelineausführung
* Eingabe- und Ausgabedaten sowie ggf. benutzerdefinierte Parameter
* Verweis auf ein Skript oder eine SDK-Logik, die während des Schritts ausgeführt werden soll

Mehrere Klassen erben von der übergeordneten Klasse [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py). Sie können Klassen auswählen, um bestimmte Frameworks oder Stapel zum Erstellen eines Schritts zu verwenden. In diesem Beispiel wird die Klasse `ParallelRunStep` verwendet, um Ihre Schrittlogik mithilfe eines benutzerdefinierten Python-Skripts zu definieren. Wenn es sich bei einem Argument für Ihr Skript um eine Eingabe für den Schritt oder um eine Ausgabe des Schritts handelt, muss es *sowohl* im Array `arguments`*als auch* im Parameter `input` bzw. `output` definiert werden. 

In Szenarien mit mehreren Schritten wird ein Objektverweis im Array `outputs` als *Eingabe* für einen nachfolgenden Pipelineschritt verfügbar.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Eine Liste mit allen Klassen für verschiedene Schritttypen finden Sie im [Schrittpaket](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Übermitteln der Pipeline

Führen Sie die Pipeline nun aus. Erstellen Sie zunächst ein Objekt vom Typ `Pipeline` mit einem Verweis auf Ihren Arbeitsbereich sowie mit dem erstellten Pipelineschritt. Der Parameter `steps` ist ein Array mit Schritten. In diesem Fall ist allerdings nur ein Schritt für die Batchbewertung vorhanden. Wenn Sie Pipelines mit mehreren Schritten erstellen möchten, müssen Sie die Schritte in der gewünschten Reihenfolge in diesem Array platzieren.

Verwenden Sie als Nächstes die Funktion `Experiment.submit()`, um die Pipeline für die Ausführung zu übermitteln. Die Funktion `wait_for_completion` gibt während der Pipelineerstellung Protokolle aus. Anhand der Protokolle können Sie den aktuellen Status anzeigen.

> [!IMPORTANT]
> Die erste Pipelineausführung dauert ungefähr *15 Minuten*. Alle Abhängigkeiten müssen heruntergeladen werden, ein Docker-Image wird erstellt, und die Python-Umgebung wird bereitgestellt und erstellt. Die erneute Ausführung der Pipeline ist dagegen deutlich schneller, da diese Ressourcen wiederverwendet und nicht neu erstellt werden. Die Gesamtlaufzeit der Pipeline hängt jedoch von der Arbeitsauslastung Ihrer Skripts sowie von den Prozessen ab, die in den einzelnen Pipelineschritten ausgeführt werden.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Herunterladen und Überprüfen der Ausgabe

Führen Sie den folgenden Code aus, um die durch das Skript `batch_scoring.py` erstellte Ausgabedatei herunterzuladen. Sehen Sie sich anschließend die Bewertungsergebnisse an.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Veröffentlichen und Ausführen über einen REST-Endpunkt

Führen Sie den folgenden Code aus, um die Pipeline in Ihrem Arbeitsbereich zu veröffentlichen. In Ihrem Arbeitsbereich in Azure Machine Learning werden Metadaten für die Pipeline angezeigt. Hierzu zählen unter anderem Ausführungsverlauf und -dauer. Über Studio kann die Pipeline auch manuell ausgeführt werden.

Durch die Veröffentlichung der Pipeline wird ein REST-Endpunkt aktiviert, über den Sie die Pipeline von einer beliebigen HTTP-Bibliothek auf einer beliebigen Plattform aus ausführen können.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Um die Pipeline über den REST-Endpunkt ausführen zu können, benötigen Sie einen OAuth2-Bearer-Authentifizierungsheader. Im folgenden Beispiel wird zur Veranschaulichung eine interaktive Authentifizierung verwendet. In den meisten Produktionsszenarios, die eine automatisierte oder monitorlose Authentifizierung erfordern, wird dagegen eine Dienstprinzipalauthentifizierung verwendet, wie in [diesem Artikel](how-to-setup-authentication.md) beschrieben.

Für die Dienstprinzipalauthentifizierung muss eine *App-Registrierung* in *Azure Active Directory* erstellt werden. Sie generieren zunächst einen geheimen Clientschlüssel und gewähren dem Dienstprinzipal anschließend *Rollenzugriff* auf Ihren Machine Learning-Arbeitsbereich. Verwenden Sie zur Verwaltung des Authentifizierungsflusses die Klasse [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py). 

[`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?preserve-view=true&view=azure-ml-py) und `ServicePrincipalAuthentication` erben jeweils von `AbstractAuthentication`. In beiden Fällen wird die Funktion [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-authentication-header--) auf die gleiche Weise verwendet, um den Header abzurufen:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Rufen Sie die REST-URL aus der Eigenschaft `endpoint` des veröffentlichten Pipelineobjekts ab. Sie finden die REST-URL auch in Ihrem Arbeitsbereich in Azure Machine Learning Studio. 

Erstellen Sie eine HTTP POST-Anforderung für den Endpunkt. Geben Sie Ihren Authentifizierungsheader in der Anforderung an. Fügen Sie ein JSON-Nutzlastobjekt mit dem Namen des Experiments hinzu.

Übermitteln Sie die Anforderung, um die Ausführung auszulösen. Fügen Sie Code ein, um zum Abrufen des Werts der Ausführungs-ID auf den Schlüssel `Id` aus dem Antwortwörterbuch zuzugreifen.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Verwenden Sie die Ausführungs-ID, um den Status der neuen Ausführung zu überwachen. Die neue Ausführung dauert ungefähr weitere zehn bis 15 Minuten. 

Sie gleicht der weiter oben in diesem Tutorial ausgeführten Pipeline. Sie können festlegen, dass nicht die vollständige Ausgabe angezeigt wird.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie weitere Azure Machine Learning-Tutorials absolvieren möchten.

### <a name="stop-the-compute-instance"></a>Beenden der Compute-Instanz

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles löschen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie sie, damit Ihnen keine Kosten entstehen:

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppen** aus.
1. Wählen Sie in der Liste der Ressourcengruppen die von Ihnen erstellte Ressourcengruppe aus.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie dann **Löschen** aus.

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial zu Machine Learning-Pipelines haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Erstellen einer Pipeline mit Umgebungsabhängigkeiten, die auf einer Remote-GPU-Computeressource ausgeführt werden kann
> * Erstellen eines Bewertungsskripts zum Ausführen von Batchvorhersagen mit einem vortrainierten TensorFlow-Modell
> * Veröffentlichen einer Pipeline und Ermöglichen der Ausführung über einen REST-Endpunkt

Weitere Beispiele zum Erstellen von Pipelines mithilfe des Machine Learning SDK finden Sie im [Notebook-Repository](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
