---
title: Ausführen von Batchvorhersagen für große Datenmengen
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Azure Machine Learning Service asynchron Batchvorhersagen für große Datenmengen erstellen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1e403ac0d2fbe9572a44fb3cde9d25e4df9b3db4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60818501"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Ausführen von Batchvorhersagen für große Datasets mit Azure Machine Learning Service

In diesem Artikel erfahren Sie, wie Sie mit Azure Machine Learning Service asynchrone Vorhersagen über große Datenmengen treffen können.

Die Batchvorhersage (oder Batchbewertung) bietet kosteneffiziente Rückschlüsse mit beispiellosem Durchsatz für asynchrone Anwendungen. Batchvorhersagepipelines können skaliert werden, um Rückschlüsse auf Terabyte von Produktionsdaten zu ermöglichen. Die Batchvorhersage ist für hohen Durchsatz, Fire-and-Forget-Vorhersagen für große Datenmengen optimiert.

>[!TIP]
> Wenn Ihr System eine Verarbeitung mit niedriger Wartezeit erfordert (zur schnellen Verarbeitung eines einzelnes Dokuments oder eines kleinen Satzes von Dokumenten), verwenden Sie die [Echtzeitbewertung](how-to-consume-web-service.md) anstelle der Batchvorhersage.

In den folgenden Schritten erstellen Sie eine [Machine Learning-Pipeline](concept-ml-pipelines.md), um ein vortrainiertes Modell für maschinelles Sehen zu registrieren ([Inception-V3](https://arxiv.org/abs/1512.00567)). Anschließend verwenden Sie das vortrainierte Modell, um eine Batchbewertung für Images durchzuführen, die in Ihrem Azure Blob Storage-Konto verfügbar sind. Diese zur Bewertung verwendeten Images sind nicht bezeichnete Images aus dem Dataset [ImageNet](http://image-net.org/).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

- Konfigurieren Sie Ihre Entwicklungsumgebung für die Installation des Azure Machine Learning-SDKs. Weitere Informationen finden Sie unter [Konfigurieren einer Entwicklungsumgebung für Azure Machine Learning](how-to-configure-environment.md).

- Erstellen Sie einen Azure Machine Learning-Arbeitsbereich, der Ihre gesamten Pipelineressourcen aufnimmt. Sie können den folgenden Code verwenden. Informationen zu weiteren Optionen finden Sie unter [Erstellen einer Konfigurationsdatei für den Arbeitsbereich](how-to-configure-environment.md#workspace).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Einrichten von Machine Learning-Ressourcen

Mit den folgenden Schritten werden die Ressourcen eingerichtet, die Sie zum Ausführen einer Pipeline benötigen:

- Greifen Sie auf den Datenspeicher zu, der bereits das vorab trainierte Modell, Eingabebezeichnungen und zu bewertende Images enthält (dies ist bereits für Sie eingerichtet).
- Richten Sie einen Datenspeicher zum Speichern Ihrer Ausgaben ein.
- Konfigurieren Sie `DataReference` -Objekte, um auf die Daten in den vorhergehenden Datenspeichern zu verweisen.
- Richten Sie Computer oder Cluster ein, auf denen die Pipelineschritte ausgeführt werden.

### <a name="access-the-datastores"></a>Zugreifen auf die Datenspeicher

Greifen Sie zunächst auf den Datenspeicher zu, der das Modell, die Bezeichnungen und Images enthält.

Sie verwenden einen öffentlichen Blobcontainer namens *sampledata* im Konto *pipelinedata*, der Images aus dem ImageNet-Auswertungssatz enthält. Der Datenspeichername für diesen öffentlichen Container lautet *images_datastore*. Registrieren Sie diesen Datenspeicher mit Ihrem Arbeitsbereich:

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Als nächstes richten Sie den Standarddatenspeicher für die Ausgaben ein.

Wenn Sie Ihren Arbeitsbereich erstellen, werden standardmäßig [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) und [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)  an den Arbeitsbereich angefügt. Azure Files ist der Standarddatenspeicher für einen Arbeitsbereich, aber Sie können auch Blob Storage als Datenspeicher verwenden. Weitere Informationen finden Sie unter [Azure-Speicheroptionen](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Konfigurieren von Datenverweisen

Verweisen Sie jetzt auf die Daten in Ihrer Pipeline als Eingabe für Pipelineschritte.

Eine Datenquelle in einer Pipeline wird durch ein [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) -Objekt dargestellt. Das `DataReference` -Objekt verweist auf Daten, die sich in einem Datenspeicher befinden oder über diesen zugänglich sind. Sie benötigen`DataReference` -Objekte für das für Eingabeimages verwendete Verzeichnis, für das Verzeichnis, in dem das vorab trainierte Modell gespeichert ist, für das Verzeichnis für Bezeichnungen und für das Ausgabeverzeichnis.

```python
input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")
                           
model_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")                          
                         
label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")                          
                         
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Einrichten von Computezielen

In Azure Machine Learning bezieht sich *Compute* (oder *Computeziel*) auf die Computer oder Cluster, die die Berechnungsschritte in Ihrer Machine Learning-Pipeline durchführen. Sie können z. B. ein `Azure Machine Learning compute` erstellen.

```python
compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, 
                        compute_name, 
                        provisioning_config)
    
    compute_target.wait_for_completion(
                     show_output=True, 
                     min_node_count=None, 
                     timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Vorbereiten des Modells

Bevor Sie das vorab trainierte Modell verwenden können, müssen Sie das Modell herunterladen und in Ihrem Arbeitsbereich registrieren.

### <a name="download-the-pretrained-model"></a>Herunterladen des vorab trainierten Modells

Laden Sie das vorab trainierte Computervisionsmodell (InceptionV3) von <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz> herunter. Extrahieren Sie es dann in den Unterordner `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registrieren des Modells

So wird das Modell registriert:

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>Schreiben Ihres Bewertungsskripts

>[!Warning]
>Der folgende Code ist nur ein Beispiel für den Inhalt der Datei [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py), die vom [Beispielnotebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb) verwendet wird. Sie müssen Ihr eigenes Bewertungsskript für Ihr Szenario erstellen.

Das Skript `batch_score.py` übernimmt eingegebene Images in  *dataset_path*, vorab trainierte Modelle in  *model_dir,* und gibt *results-label.txt*  in  *output_dir* aus.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here
  
def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
            
        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Erstellen und Ausführen der Pipeline für die Batchbewertung

Sie verfügen über alle erforderlichen Komponenten zum Erstellen der Pipeline. Jetzt müssen Sie nur alle Teile zusammenfügen.

### <a name="prepare-the-run-environment"></a>Vorbereiten der Ausführungsumgebung

Geben Sie die Conda-Abhängigkeiten für Ihr Skript an. Sie benötigen dieses Objekt später, wenn Sie den Pipelineschritt erstellen.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Angeben des Parameters für Ihre Pipeline

Erstellen Sie einen Pipelineparameter mit einem [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)-Objekt mit einem Standardwert.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Erstellen des Pipelineschritts

Erstellen Sie den Pipelineschritt mithilfe des Skripts, der Umgebungskonfiguration und der Parameter. Geben Sie das Computeziel an, das Sie bereits als Ziel für die Ausführung des Skripts an Ihren Arbeitsbereich angefügt haben. Erstellen Sie den Pipelineschritt mit [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py).

```python
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.

Führen Sie jetzt die Pipeline aus, und untersuchen Sie die von ihr erzeugte Ausgabe. Die Ausgabe weist eine Bewertung auf, die den einzelnen eingegebenen Images entspricht.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Veröffentlichen der Pipeline

Wenn Sie mit dem Ergebnis der Ausführung zufrieden sind, veröffentlichen Sie die Pipeline, damit Sie sie später mit verschiedenen Eingabewerten ausführen können. Wenn Sie eine Pipeline veröffentlichen, erhalten Sie einen REST-Endpunkt. Dieser Endpunkt akzeptiert den Aufruf der Pipeline mit dem Parametersatz, den Sie bereits mit [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) integriert haben.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Erneutes Ausführen der Pipeline mithilfe des REST-Endpunkts

Zum erneuten Ausführen der Pipeline benötigen Sie ein Headertoken für die Azure Active Directory-Authentifizierung, wie in [AzureCliAuthentication-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) beschrieben.

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Nächste Schritte

Um die Funktionsweise vollständig zu überprüfen, probieren Sie das Batchbewertungs-Notebook in [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) aus. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

