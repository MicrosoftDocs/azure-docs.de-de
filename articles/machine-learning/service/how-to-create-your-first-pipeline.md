---
title: Erstellen, Ausführen und Nachverfolgen von ML-Pipelines
titleSuffix: Azure Machine Learning service
description: Erstellen Sie eine Machine Learning-Pipeline mit dem Azure Machine Learning SDK für Python, und führen Sie sie aus. Pipelines werden zum Erstellen und Verwalten von Workflows verwendet, die Machine Learning-Phasen (ML) zusammenfügen. Zu diesen Phasen gehören beispielsweise Datenaufbereitung, Modelltraining, Modellimplementierung und Rückschluss/Bewertung.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: c87685673052d2cfbc961d3bb717c9cd274872af
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059242"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Erstellen und Ausführen einer Machine Learning-Pipeline mit dem Azure Machine Learning SDK

In diesem Artikel erfahren Sie, wie Sie eine [Machine Learning-Pipeline](concept-ml-pipelines.md) mit dem [Azure Machine Learning SDK](https://aka.ms/aml-sdk) erstellen, veröffentlichen, ausführen und verfolgen.  Diese Pipelines helfen beim Erstellen und Verwalten der Workflows, die verschiedene Machine Learning-Phasen zusammenfügen. Jede Phase einer Pipeline, z. B. Datenvorbereitung und Modelltraining, kann einen oder mehrere Schritte umfassen.

Die von Ihnen erstellten Pipelines sind für die Mitglieder Ihres Azure Machine Learning Service-[Arbeitsbereichs](how-to-manage-workspace.md) sichtbar. 

Pipelines verwenden Remote-Computeziele für die Berechnung und Speicherung der mit dieser Pipeline verbundenen Zwischen- und Enddaten. Pipelines können Daten in unterstützten [Azure Storage](https://docs.microsoft.com/azure/storage/)-Speicherorten lesen und schreiben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

## <a name="prerequisites"></a>Voraussetzungen

* [Konfigurieren Sie Ihre Entwicklungsumgebung](how-to-configure-environment.md) für die Installation des Azure Machine Learning-SDKs.

* Erstellen Sie einen [Azure Machine Learning-Arbeitsbereich](how-to-configure-environment.md#workspace), um Ihre gesamten Pipelineressourcen aufzunehmen. 

  ```python
  from azureml.core import Workspace
  
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Einrichten von Machine Learning-Ressourcen

Erstellen Sie die Ressourcen, die zum Ausführen einer Pipeline erforderlich sind:

* Richten Sie einen Datenspeicher ein, über den auf die in den Pipelineschritten benötigten Daten zugegriffen werden kann.

* Konfigurieren Sie ein `DataReference`-Objekt, um auf Daten zu verweisen, die sich in einem Datenspeicher befinden oder dort zugänglich sind.

* Richten Sie die [Computeziele](concept-azure-machine-learning-architecture.md#compute-targets) ein, auf denen Ihre Pipelineschritte ausgeführt werden.

### <a name="set-up-a-datastore"></a>Einrichten eines Datenspeichers
Ein Datenspeicher speichert die Daten für den Zugriff durch die Pipeline. Jeder Arbeitsbereich verfügt über einen Standarddatenspeicher. Sie können weitere Datenspeicher registrieren. 

Wenn Sie Ihren Arbeitsbereich erstellen, werden [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) und [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) standardmäßig an den Arbeitsbereich angefügt. Azure Files ist der Standarddatenspeicher für einen Arbeitsbereich, aber Sie können auch Blob Storage als Datenspeicher verwenden. Weitere Informationen finden Sie unter [Entscheidung zwischen Azure-Blobs, Azure Files und Azure-Datenträgern](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Laden Sie Datendateien oder Verzeichnisse in den Datenspeicher hoch, damit sie von Ihren Pipelines aus zugänglich sind. In diesem Beispiel wird die Blob Storage-Version des Datenspeichers verwendet:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups", 
    overwrite=True)
```

Eine Pipeline besteht aus mindestens einem Schritt. Ein Schritt ist eine Einheit, die auf einem Computeziel ausgeführt wird. Schritte können Datenquellen verbrauchen und „Zwischendaten“ erzeugen. Ein Schritt kann Daten wie ein Modell, ein Verzeichnis mit Modell- und abhängigen Dateien oder temporäre Daten erstellen. Diese Daten stehen dann später für weitere Schritte in der Pipeline zur Verfügung.

### <a name="configure-data-reference"></a>Konfigurieren eines Datenverweises

Sie haben gerade eine Datenquelle erstellt, auf die in einer Pipeline als Eingabe für einen Schritt verwiesen werden kann. Eine Datenquelle in einer Pipeline wird durch ein [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference)-Objekt dargestellt. Das -`DataReference`Objekt verweist auf Daten, die sich in einem Datenspeicher befinden oder über diesen zugänglich sind.

```python
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Zwischendaten (oder die Ausgabe eines Schritts) werden durch ein [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)-Objekt dargestellt. `output_data1` wird als Ausgabe eines Schritts erstellt und als Eingabe für einen oder mehrere zukünftige Schritte verwendet. `PipelineData` führt eine Datenabhängigkeit zwischen den Schritten ein und erzeugt eine implizite Ausführungsreihenfolge in der Pipeline.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Einrichten von Computezielen

In Azure Machine Learning bezieht sich der Begriff __Compute__ (oder __Computeziel__) auf die Computer oder Cluster, die die Berechnungsschritte in Ihrer Machine Learning-Pipeline durchführen.   Eine vollständige Liste der Computeziele sowie Informationen zum Erstellen und Anfügen an Ihren Arbeitsbereich finden Sie unter [Computeziele für das Modelltraining](how-to-set-up-training-targets.md).  Das Vorgehen zum Erstellen und Anfügen eines Computeziels ist immer dasselbe, ganz gleich, ob Sie ein Modell trainieren oder einen Pipelineschritt ausführen. Verwenden Sie nach dem Erstellen und Anfügen des Computeziels das `ComputeTarget`-Objekt in Ihrem [Pipelineschritt](#steps).

> [!IMPORTANT]
> Das Anwenden von Verwaltungsvorgängen auf Computeziele wird innerhalb von Remoteaufträgen nicht unterstützt. Da Pipelines für maschinelles Lernen als Remoteauftrag übermittelt werden, sollten Sie innerhalb der Pipeline keine Verwaltungsvorgänge auf Computeziele anwenden.

Nachfolgend sind Beispiele zum Erstellen und Anfügen von Computezielen für Folgendes aufgeführt:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute

Sie können eine Azure Machine Learning-Computeressource zum Ausführen Ihrer Schritte erstellen.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks ist eine Apache Spark-basierte Umgebung in der Azure-Cloud. Sie kann mit einer Azure Machine Learning-Pipeline als Computeziel verwendet werden.

Erstellen Sie vor der Verwendung einen Azure Databricks-Arbeitsbereich. Informationen zum Erstellen dieser Ressource finden Sie im Dokument [Ausführen eines Spark-Auftrags in Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Geben Sie zum Anfügen von Azure Databricks als Computeziel die folgenden Informationen an:

* __Databricks-Computename__: Der Name, der dieser Computeressource zugewiesen werden soll.
* __Name des Databricks-Arbeitsbereichs__: Der Name des Azure Databricks-Arbeitsbereichs.
* __Databricks-Zugriffstoken__: Das zur Authentifizierung bei Azure Databricks verwendete Zugriffstoken. Informationen zum Generieren eines Zugriffstokens finden Sie im Dokument [Authentifizierung](https://docs.azuredatabricks.net/api/latest/authentication.html).

Der folgende Code veranschaulicht, wie Azure Databricks mit dem Azure Machine Learning-SDK als Computeziel angefügt wird:

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics ist eine umfangreiche Datenanalyseplattform in der Azure-Cloud. Sie kann mit einer Azure Machine Learning-Pipeline als Computeziel verwendet werden.

Erstellen Sie vor der Verwendung ein Azure Data Lake Analytics-Konto. Informationen zum Erstellen dieser Ressource finden Sie im Dokument [Erste Schritte mit Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Um Data Lake Analytics als Computeziel anzufügen, müssen Sie das Azure Machine Learning SDK verwenden und die folgenden Informationen angeben:

* __Computename__: Der Name, der dieser Computeressource zugewiesen werden soll.
* __Ressourcengruppe__: Die Ressourcengruppe, die das Data Lake Analytics-Konto enthält.
* __Kontoname__: Der Name des Data Lake Analytics-Kontos.

Der folgende Code veranschaulicht, wie Data Lake Analytics als Computeziel angefügt wird:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure Machine Learning-Pipelines können nur ausgeführt werden, wenn die Daten im Standarddatenspeicher des Data Lake Analytics-Kontos gespeichert werden. Wenn die Daten, die Sie verwenden möchten, in einem nicht standardmäßigen Speicher gespeichert sind, können Sie die Daten mithilfe von [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) vor dem Trainieren kopieren.

## <a id="steps"></a>Erstellen Ihrer Pipelineschritte

Nachdem Sie ein Computeziel erstellt und an Ihren Arbeitsbereich angefügt haben, können Sie einen Pipelineschritt definieren. Über das Azure Machine Learning SDK stehen viele integrierte Schritte zur Verfügung. Der grundlegendste dieser Schritte ist [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), der ein Python-Skript an einem bestimmten Computeziel ausführt:

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Die Wiederverwendung von vorherigen Ergebnissen (`allow_reuse`) ist entscheidend, wenn Pipelines in einer Umgebung für die Zusammenarbeit verwendet werden, weil die Beseitigung von nicht benötigten erneuten Ausführungen zu einer höheren Flexibilität führt. Dies ist das Standardverhalten, wenn „script_name“, Eingaben und die Parameter eines Schritts gleich bleiben. Wenn die Ausgabe des Schritts wiederverwendet wird, wird der Auftrag nicht an den Computevorgang übermittelt, sondern die Ergebnisse aus der vorherigen Ausführung sind sofort für die Ausführung des nächsten Schritts verfügbar. Bei der Einstellung auf „false“ wird für diesen Schritt während der Pipelineausführung immer eine neue Ausführung generiert. 

Nachdem Sie die Schritte definiert haben, erstellen Sie die Pipeline mit einigen oder allen dieser Schritte.

> [!NOTE]
> Es werden keine Dateien oder Daten in Azure Machine Learning Service hochgeladen, wenn Sie die Schritte definieren oder die Pipeline erstellen.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Im folgenden Beispiel wird das Azure Databricks-Computeziel verwendet, das weiter oben erstellt wurde: 

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

Weitere Informationen finden Sie unter [azure-pipeline-steps-Paket](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) und in der Referenz zur [Pipeline-Klasse](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Übermitteln der Pipeline

Wenn Sie die Pipeline übermitteln, prüft Azure Machine Learning Service die Abhängigkeiten für die einzelnen Schritte und lädt eine Momentaufnahme des als Quellverzeichnis angegebenen Ordners hoch. Wenn kein Quellverzeichnis angegeben ist, wird das aktuelle lokale Verzeichnis hochgeladen. Die Momentaufnahme wird auch als Teil des Experiments in Ihrem Arbeitsbereich gespeichert.

> [!IMPORTANT]
> Wenn Sie verhindern möchten, dass Dateien von der Momentaufnahme erfasst werden, müssen Sie eine [GITIGNORE](https://git-scm.com/docs/gitignore)- oder `.amlignore`-Datei im Verzeichnis erstellen und diesem die Dateien hinzufügen. Für die `.amlignore`-Datei werden die gleiche Syntax und die gleichen Muster wie für die [GITIGNORE](https://git-scm.com/docs/gitignore)-Datei verwendet. Wenn beide Dateien vorhanden sind, hat die `.amlignore`-Datei Vorrang.
>
> Weitere Informationen finden Sie unter [Momentaufnahmen](concept-azure-machine-learning-architecture.md#snapshots).

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Wenn Sie eine Pipeline erstmals ausführen, geht Azure Machine Learning folgendermaßen vor:

* Die Momentaufnahme des Projekts wird aus dem Blobspeicher, der dem Arbeitsbereich zugeordnet ist, auf das Computeziel heruntergeladen.
* Für jeden Schritt in der Pipeline wird ein Docker-Image erstellt.
* Das Docker-Image für die einzelnen Schritte wird aus der Containerregistrierung auf das Computeziel heruntergeladen.
* Wenn in einem Schritt ein `DataReference`-Objekt angegeben ist, wird der Datenspeicher eingebunden. Wenn das Einbinden nicht unterstützt wird, werden die Daten stattdessen in das Computeziel kopiert.
* Der Schritt wird in dem in der Schrittdefinition angegebenen Computeziel ausgeführt. 
* Artefakte wie Protokolle, stdout und stderr, Metriken und Ausgaben, die durch den Schritt angegeben werden, werden erstellt. Diese Artefakte werden dann hochgeladen und im Standarddatenspeicher des Benutzers gespeichert.

![Diagramm zum Ausführen eines Experiments als Pipeline](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Weitere Informationen finden Sie in der Referenz zur [Experiment-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py).

## <a name="github-tracking-and-integration"></a>GitHub-Nachverfolgung und -Integration

Wenn Sie eine Trainingsausführung starten, bei der das Quellverzeichnis ein lokales Git-Repository ist, werden Informationen über das Repository im Ausführungsverlauf gespeichert. Zum Beispiel wird die aktuelle Commit-ID für das Repository als Teil des Verlaufs protokolliert.

## <a name="publish-a-pipeline"></a>Veröffentlichen einer Pipeline

Sie können eine Pipeline veröffentlichen, um sie später mit verschiedenen Eingaben auszuführen. Damit der REST-Endpunkt einer bereits veröffentlichten Pipeline Parameter akzeptiert, muss die Pipeline vor der Veröffentlichung parametrisiert werden. 

1. Um einen Pipelineparameter zu erstellen, verwenden Sie ein [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py)-Objekt mit einem Standardwert.

   ```python
   pipeline_param = PipelineParameter(
     name="pipeline_arg", 
     default_value=10)
   ```

2. Fügen Sie dieses `PipelineParameter`-Objekt als Parameter zu einem der Schritte in der Pipeline wie folgt hinzu:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],    
     target=compute_target, 
     source_directory=project_folder)
   ```

3. Veröffentlichen Sie diese Pipeline, die beim Aufruf einen Parameter akzeptiert.

   ```python
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>Ausführen einer veröffentlichten Pipeline

Alle veröffentlichten Pipelines weisen einen REST-Endpunkt auf. Dieser Endpunkt ruft die Ausführung der Pipeline über externe Systeme wie Nicht-Python-Clients auf. Dieser Endpunkt ermöglicht „verwaltete Wiederholbarkeit“ in Szenarien für die Batchbewertung und für erneutes Training.

Zum Aufrufen der Ausführung der vorhergehenden Pipeline benötigen Sie ein Headertoken für die Azure Active Directory-Authentifizierung, wie in [AzureCliAuthentication-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) beschrieben. Oder rufen Sie im Notebook zur [Authentifizierung in Azure Machine Learning](https://aka.ms/pl-restep-auth) weitere Details ab.

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="view-results"></a>Anzeigen der Ergebnisse

Hier finden Sie die Liste aller Ihrer Pipelines und deren Ausführungsdetails:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.  

1. [Zeigen Sie Ihren Arbeitsbereich](how-to-manage-workspace.md#view) an, um die Liste der Pipelines zu finden.
 ![Liste der Machine Learning-Pipelines](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Wählen Sie eine bestimmte Pipeline aus, um die Ausführungsergebnisse anzuzeigen.

## <a name="caching--reuse"></a>Zwischenspeichern und Wiederverwenden  

Zur Optimierung und Anpassung des Verhaltens Ihrer Pipelines können Sie einige Optionen für die Zwischenspeicherung und Wiederverwendung nutzen. Sie haben beispielsweise folgende Optionen:
+ **Deaktivieren Sie die standardmäßige Wiederverwendung der Ausgabe nach Ausführung des Schritts**, indem Sie bei der [Schrittdefinition](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) `allow_reuse=False` festlegen. Die Wiederverwendung ist entscheidend, wenn Pipelines in einer Umgebung für die Zusammenarbeit verwendet werden, weil die Beseitigung von nicht benötigten Ausführungen zu einer höheren Flexibilität führt. Sie können dies aber auch deaktivieren.
+ **Erweitern Sie das Hashing über das Skript hinaus**, um in das Quellverzeichnis auch einen absoluten Pfad oder relative Pfade zu anderen Dateien und Verzeichnissen einzufügen, indem Sie `hash_paths=['<file or directory']` verwenden. 
+ **Erzwingen Sie die erneute Generierung der Ausgabe für alle Schritte einer Ausführung** mit `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`.

Standardmäßig ist `allow-reuse` für die Schritte aktiviert, und nur die Hauptskriptdatei verfügt über einen Hashwert. Wenn das Skript für einen Schritt unverändert bleibt (`script_name`, Eingaben und Parameter), wird also die Ausgabe eines vorherigen Schritts wiederverwendet, der Auftrag wird nicht an den Computevorgang übermittelt, und die Ergebnisse der vorherigen Ausführung sind stattdessen sofort für den nächsten Schritt verfügbar.  

```python
step = PythonScriptStep(name="Hello World", 
                        script_name="hello_world.py",  
                        compute_target=aml_compute,  
                        source_directory= source_directory, 
                        allow_reuse=False, 
                        hash_paths=['hello_world.ipynb']) 
```
 

## <a name="next-steps"></a>Nächste Schritte
- Verwenden Sie [diese Jupyter-Notebooks auf GitHub](https://aka.ms/aml-pipeline-readme), um die Pipelines für maschinelles Lernen eingehender zu erkunden.
- Lesen Sie die SDK-Referenzhilfe für die Pakete [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) und [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
