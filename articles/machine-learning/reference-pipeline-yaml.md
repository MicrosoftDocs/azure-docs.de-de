---
title: Machine Learning-Pipeline – YAML
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie eine Machine Learning-Pipeline mithilfe einer YAML-Datei definieren. YAML-Pipelinedefinitionen werden zusammen mit der Machine Learning-Erweiterung für die Azure CLI verwendet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: bfeab990c841f6b65e665b4a8aabdfd8b251da60
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323915"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definieren von Machine Learning-Pipelines in YAML

Erfahren Sie, wie Sie Ihre Machine Learning-Pipelines in [YAML](https://yaml.org/) definieren. Wenn Sie die Machine Learning-Erweiterung für die Azure CLI verwenden, erwarten viele der auf die Pipeline ausgerichteten Befehle eine YAML-Datei, die die Pipeline definiert.

In der folgenden Tabelle ist aufgeführt, was beim Definieren einer Pipeline in YAML derzeit unterstützt bzw. nicht unterstützt wird:

| Schritttyp | Unterstützt? |
| ----- | :-----: |
| PythonScriptStep | Ja |
| ParallelRunStep | Ja |
| AdlaStep | Ja |
| AzureBatchStep | Ja |
| DatabricksStep | Ja |
| DataTransferStep | Ja |
| AutoMLStep | Nein |
| HyperDriveStep | Nein |
| ModuleStep | Ja |
| MPIStep | Nein |
| EstimatorStep | Nein |

## <a name="pipeline-definition"></a>Definition der Pipeline

In einer Pipelinedefinition werden die folgenden Schlüssel verwendet, die der [Pipelines](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?preserve-view=true&view=azure-ml-py)-Klasse entsprechen:

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `name` | Die Beschreibung der Pipeline. |
| `parameters` | Parameter für die Pipeline. |
| `data_reference` | Definiert, wie und wo Daten in einer Ausführung verfügbar sein sollen. |
| `default_compute` | Standardmäßiges Computeziel, bei dem alle Schritte in der Pipeline ausgeführt werden. |
| `steps` | Die in der Pipeline verwendeten Schritte. |

## <a name="parameters"></a>Parameter

Der Abschnitt `parameters` verwendet die folgenden Schlüssel, die der [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py)-Klasse entsprechen:

| YAML-Schlüssel | BESCHREIBUNG |
| ---- | ---- |
| `type` | Der Werttyp des Parameters. Gültige Typen sind `string`, `int`, `float`, `bool` oder `datapath`. |
| `default` | Der Standardwert. |

Jeder Parameter ist benannt. Der folgende YAML-Codeausschnitt definiert beispielsweise drei Parameter mit dem Namen `NumIterationsParameter`, `DataPathParameter` und `NodeCountParameter`:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Datenverweis

Der Abschnitt `data_references` verwendet die folgenden Schlüssel, die der [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference?preserve-view=true&view=azure-ml-py)-Klasse entsprechen:

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `datastore` | Der Datenspeicher, auf den verwiesen wird. |
| `path_on_datastore` | Der relative Pfad im Sicherungsspeicher für den Datenverweis. |

Jeder Datenverweis ist in einem Schlüssel enthalten. Der folgende YAML-Codeausschnitt definiert beispielsweise einen Datenverweis, der im Schlüssel namens `employee_data` gespeichert ist:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Schritte

Die Schritte definieren eine Compute-Umgebung und die Dateien, die in der Umgebung ausgeführt werden sollen. Zum Definieren des Typs eines Schritts verwenden Sie den Schlüssel `type`:

| Schritttyp | BESCHREIBUNG |
| ----- | ----- |
| `AdlaStep` | Führt ein U-SQL-Skript mit Azure Data Lake Analytics aus. Entspricht der [AdlaStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?preserve-view=true&view=azure-ml-py)-Klasse. |
| `AzureBatchStep` | Führt Aufträge mithilfe von Azure Batch aus. Entspricht der [AzureBatchStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?preserve-view=true&view=azure-ml-py)-Klasse. |
| `DatabricsStep` | Fügt ein Databricks-Notebook, ein Python-Skript oder JAR hinzu. Entspricht der [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?preserve-view=true&view=azure-ml-py)-Klasse. |
| `DataTransferStep` | Überträgt Daten zwischen verschiedenen Speicheroptionen. Entspricht der [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py)-Klasse. |
| `PythonScriptStep` | Führt ein Python-Skript aus. Entspricht der [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py)-Klasse. |
| `ParallelRunStep` | Führt ein Python-Skript aus, um große Datenmengen asynchron und parallel zu verarbeiten. Entspricht der [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py)-Klasse. |

### <a name="adla-step"></a>ADLA-Schritt

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `script_name` | Der Name des U-SQL-Skripts (relativ zum `source_directory`). |
| `compute_target` | Das für diesen Schritt zu verwendende Azure Data Lake-Computeziel. |
| `parameters` | [Parameter](#parameters) für die Pipeline. |
| `inputs` | Mögliche Eingaben sind [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py), [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py) oder [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | Mögliche Ausgaben sind [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) oder [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `source_directory` | Das Verzeichnis, das das Skript, Assemblys usw. enthält. |
| `priority` | Der für den aktuellen Auftrag zu verwendenden Prioritätswert. |
| `params` | Ein Wörterbuch von Name/Wert-Paaren. |
| `degree_of_parallelism` | Der für diesen Auftrag zu verwendende Grad an Parallelität. |
| `runtime_version` | Die Runtimeversion der Data Lake Analytics-Engine. |
| `allow_reuse` | Bestimmt, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen ausgeführt wird. |

Das folgende Beispiel enthält die Definition eines ADLA-Schritts:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch-Schritt

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `compute_target` | Das für diesen Schritt zu verwendende Azure Batch-Computeziel. |
| `inputs` | Mögliche Eingaben sind [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py), [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py) oder [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | Mögliche Ausgaben sind [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) oder [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `source_directory` | Verzeichnis, das die Binärdateien des Modul, die ausführbare Datei, die Assemblys usw. enthält. |
| `executable` | Der Name des Befehls/der ausführbaren Datei, der/die als Teil dieses Auftrags ausgeführt wird. |
| `create_pool` | Boolesches Flag, das angibt, ob der Pool vor der Ausführung des Auftrags erstellt werden soll. |
| `delete_batch_job_after_finish` | Boolesches Flag, das angibt, ob der Auftrag nach seinem Abschluss aus dem Batch-Konto gelöscht werden soll. |
| `delete_batch_pool_after_finish` | Boolesches Flag, das angibt, ob der Pool nach Abschluss des Auftrags gelöscht werden soll. |
| `is_positive_exit_code_failure` | Boolesches Flag, das angibt, ob der Auftrag fehlschlägt, wenn die Aufgabe mit einem positiven Code beendet wird. |
| `vm_image_urn` | Wenn `create_pool``True` ist und die VM `VirtualMachineConfiguration` verwendet. |
| `pool_id` | Die ID des Pools, in dem der Auftrag ausgeführt wird. |
| `allow_reuse` | Bestimmt, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen ausgeführt wird. |

Das folgende Beispiel enthält die Definition eines Azure Batch-Schritts:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks-Schritt

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `compute_target` | Das für diesen Schritt zu verwendende Azure Databricks-Computeziel. |
| `inputs` | Mögliche Eingaben sind [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py), [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py) oder [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | Mögliche Ausgaben sind [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) oder [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `run_name` | Der Name für diese Ausführung in Databricks. |
| `source_directory` | Das Verzeichnis, das das Skript und andere Dateien enthält. |
| `num_workers` | Die statische Anzahl von Workern für den Databricks-Cluster. |
| `runconfig` | Der Pfad zur Datei `.runconfig`. Diese Datei ist eine YAML-Darstellung der [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py)-Klasse. Weitere Informationen zur Struktur dieser Datei finden Sie unter [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Bestimmt, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen ausgeführt wird. |

Das folgende Beispiel enthält einen Databricks-Schritt:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Datenübertragungsschritt

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `compute_target` | Das für diesen Schritt zu verwendende Azure Data Factory-Computeziel. |
| `source_data_reference` | Eingabeverbindung, die als Quelle für Datenübertragungsvorgängen dient. Es werden die Werte [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py), [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py) oder [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py) unterstützt. |
| `destination_data_reference` | Eingabeverbindung, die als Ziel für Datenübertragungsvorgängen dient. Es werden die Werte [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) und [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py) unterstützt. |
| `allow_reuse` | Bestimmt, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen ausgeführt wird. |

Das folgende Beispiel enthält einen Datenübertragungsschritt:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-Skriptschritt

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `inputs` | Mögliche Eingaben sind [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?preserve-view=true&view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?preserve-view=true&view=azure-ml-py), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py), [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py) oder [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py). |
| `outputs` | Mögliche Ausgaben sind [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) oder [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `script_name` | Der Name des Python-Skripts (relativ zum `source_directory`). |
| `source_directory` | Verzeichnis, das das Skript, die Conda-Umgebung usw. enthält. |
| `runconfig` | Der Pfad zur Datei `.runconfig`. Diese Datei ist eine YAML-Darstellung der [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py)-Klasse. Weitere Informationen zur Struktur dieser Datei finden Sie unter [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Bestimmt, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen ausgeführt wird. |

Das folgende Beispiel enthält einen Python-Skriptschritt:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>Schritt zur parallelen Ausführung

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `inputs` | Eingaben können [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?preserve-view=true&view=azure-ml-py) oder [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?preserve-view=true&view=azure-ml-py) sein. |
| `outputs` | Mögliche Ausgaben sind [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py) oder [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?preserve-view=true&view=azure-ml-py). |
| `script_name` | Der Name des Python-Skripts (relativ zum `source_directory`). |
| `source_directory` | Verzeichnis, das das Skript, die Conda-Umgebung usw. enthält. |
| `parallel_run_config` | Der Pfad zur Datei `parallel_run_config.yml`. Diese Datei ist eine YAML-Darstellung der [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig?preserve-view=true&view=azure-ml-py)-Klasse. |
| `allow_reuse` | Bestimmt, ob bei dem Schritt vorherige Ergebnisse wiederverwendet werden sollen, wenn er mit den gleichen Einstellungen ausgeführt wird. |

Das folgende Beispiel enthält einen Schritt zur parallelen Ausführung:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Pipeline mit mehreren Schritten 

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `steps` | Folge von PipelineStep-Definitionen. Beachten Sie, dass die `destination`-Schlüssel der `outputs` eines Schritts zu den `source`-Schlüsseln der `inputs` des nächsten Schritts werden.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Zeitpläne

Bei der Definition des Zeitplans für eine Pipeline kann dieser entweder datenspeichergesteuert oder sich wiederholend basierend auf einem Zeitintervall ausgeführt werden. Im folgenden Abschnitt finden Sie die Schlüssel, die zum Definieren eines Zeitplans verwendet werden:

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `description` | Eine Beschreibung des Zeitplans. |
| `recurrence` | Enthält Serieneinstellungen, wenn sich der Zeitplan wiederholt. |
| `pipeline_parameters` | Jegliche Parameter, die für die Pipeline erforderlich sind. |
| `wait_for_provisioning` | Gibt an, ob auf die Bereitstellung des Zeitplans gewartet werden soll. |
| `wait_timeout` | Die Anzahl der Sekunden, die gewartet werden soll, bevor ein Timeout eintritt. |
| `datastore_name` | Der Datenspeicher, der hinsichtlich geänderter/hinzugefügter Blobdaten überwacht werden soll. |
| `polling_interval` | Gibt an, wie viel Zeit (in Minuten) zwischen dem Abruf von geänderten/hinzugefügten Blobdaten liegen soll. Standardwert: 5 Minuten. Wird nur für Datenspeicherzeitpläne unterstützt. |
| `data_path_parameter_name` | Der Name des Pipelineparameters des Datenpfads, der mit dem geänderten Blobpfad festgelegt werden soll. Wird nur für Datenspeicherzeitpläne unterstützt. |
| `continue_on_step_failure` | Gibt an, ob die Ausführung weiterer Schritte im übermittelten PipelineRun fortgesetzt werden soll, wenn ein Schritt nicht erfolgreich war. Wenn angegeben, wird die `continue_on_step_failure`-Einstellung der Pipeline überschrieben.
| `path_on_datastore` | Optional. Der Pfad im Datenspeicher, der hinsichtlich geänderter/hinzugefügter Blobdaten überwacht werden soll. Der Pfad befindet sich unter dem Container des Datenspeichers, sodass der tatsächliche Pfad, den der Zeitplan überwacht Container/`path_on_datastore` ist. Wenn nichts angegeben ist, wird der Datenspeichercontainer überwacht. Ergänzungen/Änderungen, die in einem Unterordner des `path_on_datastore` vorgenommen werden, werden nicht überwacht. Wird nur für Datenspeicherzeitpläne unterstützt. |

Das folgende Beispiel enthält die Definition für einen von einem Datenspeicher ausgelösten Zeitplan:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Wenn Sie einen **wiederkehrenden Zeitplan** definieren, verwenden Sie die folgenden Schlüssel unter `recurrence`:

| YAML-Schlüssel | BESCHREIBUNG |
| ----- | ----- |
| `frequency` | Gibt an, wie oft der Zeitplan wiederholt wird. Gültige Werte sind `"Minute"`, `"Hour"`, `"Day"`, `"Week"` oder `"Month"`. |
| `interval` | Gibt an, wie oft der Zeitplan ausgelöst wird. Der ganzzahlige Wert ist die Anzahl der Zeiteinheiten, die gewartet werden soll, bis der Zeitplan erneut ausgelöst wird. |
| `start_time` | Die Startzeit für den Zeitplan. Das Zeichenfolgenformat des Werts ist `YYYY-MM-DDThh:mm:ss`. Wenn keine Startzeit angegeben ist, wird der erste Workload sofort ausgeführt, und zukünftige Workloads werden basierend auf dem Zeitplan ausgeführt. Wenn die Startzeit in der Vergangenheit liegt, wird die erste Workload zur nächsten berechneten Laufzeit ausgeführt. |
| `time_zone` | Die Zeitzone für die Startzeit. Wenn keine Zeitzone angegeben ist, wird UTC verwendet. |
| `hours` | Wenn `frequency``"Day"` oder `"Week"` ist, können Sie eine ganze Zahl oder eine durch Kommas getrennte Liste mit ganzen Zahlen von 0 bis 23 als die Stunden des Tages angeben, zu denen die Pipeline ausgeführt werden soll. Es können nur `time_of_day` oder `hours` und `minutes` verwendet werden. |
| `minutes` | Wenn `frequency``"Day"` oder `"Week"` ist, können Sie eine ganze Zahl oder eine durch Kommas getrennte Liste mit ganzen Zahlen von 0 bis 59 als die Minuten der Stunde angeben, zu denen die Pipeline ausgeführt werden soll. Es können nur `time_of_day` oder `hours` und `minutes` verwendet werden. |
| `time_of_day` | Wenn `frequency``"Day"` oder `"Week"`ist, können Sie eine Uhrzeit angeben, zu der der Zeitplan ausgeführt werden soll. Das Zeichenfolgenformat des Werts ist `hh:mm`. Es können nur `time_of_day` oder `hours` und `minutes` verwendet werden. |
| `week_days` | Wenn `frequency``"Week"` ist, können Sie einen Tag oder eine durch Kommas getrennte Liste von Tagen angeben, an denen der Zeitplan ausgeführt werden soll. Gültige Werte sind `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"` und `"Sunday"`. |

Das folgende Beispiel enthält die Definition für einen sich wiederholenden Zeitplan:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zum [Verwenden der CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).