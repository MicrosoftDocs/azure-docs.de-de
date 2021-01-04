---
title: MLflow-Tracking für ML-Experimente
titleSuffix: Azure Machine Learning
description: Richten Sie MLflow mit Azure Machine Learning ein, um Metriken und Artefakte von ML-Modellen zu protokollieren, und stellen Sie Ihre ML-Modelle als Webdienst bereit.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 33ee8944aec043bf2b103ac3958a923b9876b749
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660133"
---
# <a name="track-experiment-runs-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Nachverfolgen von Experimentausführungen und Bereitstellen von ML-Modellen mit MLflow und Azure Machine Learning (Vorschauversion)

In diesem Artikel erfahren Sie, wie Sie den Nachverfolgungs-URI und die Protokollierungs-API von MLflow (zusammen als [MLflow-Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) bezeichnet) zum Verbinden von Azure Machine Learning als Back-End Ihrer MLflow-Experimente nutzen. 

Unterstützte Funktionen umfassen u. a.: 

+ Sie können die Metriken und Artefakte von Experimenten im [Azure Machine Learning-Arbeitsbereich](./concept-azure-machine-learning-architecture.md#workspace) nachverfolgen und protokollieren. Falls Sie MLflow-Tracking bereits für Ihre Experimente verwenden, ist der Arbeitsbereich ein zentraler, sicherer und skalierbarer Ort zum Speichern von Trainingsmetriken und -modellen.

+ Übermitteln Sie Trainingsaufträge mit MLflow-Projekten mit Azure Machine Learning Back-End-Unterstützung (Vorschau). Sie können Aufträge lokal über die Azure Machine Learning-Nachverfolgung übermitteln oder Ihre Ausführungen zur Cloud migrieren, z. B. über [Azure Machine Learning Compute](./how-to-create-attach-compute-cluster.md).

+ Sie können Modelle in MLflow und der Azure Machine Learning-Modellregistrierung nachverfolgen und verwalten.

+ Stellen Sie Ihre MLflow-Experimente als Azure Machine Learning-Webdienst bereit. Durch die Bereitstellung als Webdienst können Sie die Funktionen von Azure Machine Learning zur Überwachung und Erkennung von Datenabweichungen für Ihre Produktionsmodelle einsetzen. 

[MLflow](https://www.mlflow.org) ist eine Open-Source-Bibliothek zum Verwalten des Lebenszyklus Ihrer Machine Learning-Experimente. MLFlow-Nachverfolgung ist eine Komponente von MLflow, die Ihre Trainingsausführungsmetriken und Modellartefakte unabhängig von der Umgebung Ihres Experiments protokolliert und nachverfolgt: lokal auf Ihrem Computer, auf einem Remotecomputeziel, auf einem virtuellen Computer oder in einem [Azure Databricks-Cluster](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Als Open Source-Bibliothek wird MLflow häufig geändert. Daher sollte die Funktionalität, die über die Integration von Azure Machine Learning und MLflow zur Verfügung gestellt wird, als Vorschau betrachtet und nicht vollständig von Microsoft unterstützt werden.

Im folgenden Diagramm ist dargestellt, wie Sie mit MLflow-Tracking die Ausführungsmetriken eines Experiments nachverfolgen und Modellartefakte in Ihrem Azure Machine Learning-Arbeitsbereich speichern.

![MLflow mit Azure Machine Learning: Diagramm](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Die Informationen in diesem Dokument sind hauptsächlich für Datenanalysten und Entwickler gedacht, die den Modelltrainingsprozess überwachen möchten. Wenn Sie Administrator sind und sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Vergleich von MLflow mit Azure Machine Learning-Clients

 In der folgenden Tabelle sind die unterschiedlichen Clients, für die Azure Machine Learning genutzt werden kann, und die entsprechenden Funktionen zusammengefasst.

 MLflow-Tracking verfügt über Funktionen für Metrikprotokollierung und Artefaktspeicherung, die sonst nur über das [Azure Machine Learning-SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) zur Verfügung stehen.

| Funktion | MLflow-Tracking und -Bereitstellung | Python-SDK für Azure Machine Learning |  Azure Machine Learning-CLI | Azure Machine Learning Studio|
|---|---|---|---|---|
| Verwalten des Arbeitsbereichs |   | ✓ | ✓ | ✓ |
| Verwenden von Datenspeichern  |   | ✓ | ✓ | |
| Protokollieren von Metriken      | ✓ | ✓ |   | |
| Hochladen von Artefakten | ✓ | ✓ |   | |
| Anzeigen von Metriken     | ✓ | ✓ | ✓ | ✓ |
| Verwalten von Compute   |   | ✓ | ✓ | ✓ |
| Bereitstellen von Modellen    | ✓ | ✓ | ✓ | ✓ |
|Überwachen der Modellleistung||✓|  |   |
| Erkennen von Datenabweichungen |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie das `azureml-mlflow`-Paket. 
    * Mit diesem Paket wird automatisch `azureml-core` des [The Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) bereitgestellt, um MLflow den Zugriff auf Ihren Arbeitsbereich zu ermöglichen.
* [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich.](how-to-manage-workspace.md)

## <a name="track-local-runs"></a>Nachverfolgen von lokalen Ausführungen

Mit MLflow-Tracking mit Azure Machine Learning können Sie die protokollierten Metriken und Artefakte aus Ihren lokalen Ausführungen in Ihrem Azure Machine Learning-Arbeitsbereich speichern.

Importieren Sie die Klassen `mlflow` und [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py), um auf den Tracking-URI von MLflow zuzugreifen und Ihren Arbeitsbereich zu konfigurieren.

Im folgenden Code wird mit der `get_mlflow_tracking_uri()`-Methode dem Arbeitsbereich `ws` eine eindeutige Tracking-URI-Adresse zugewiesen, und mit `set_tracking_uri()` wird für den Tracking-URI von MLflow auf diese Adresse verwiesen.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Der Tracking-URI ist maximal eine Stunde lang gültig. Wenn Sie Ihr Skript nach einer Leerlaufzeit neu starten, sollten Sie die get_mlflow_tracking_uri-API verwenden, um einen neuen URI abzurufen.

Legen Sie den Namen des MLflow-Experiments mit `set_experiment()` fest, und starten Sie Ihre Trainingsausführung mit `start_run()`. Verwenden Sie anschließend `log_metric()`, um die API für die MLflow-Protokollierung zu aktivieren, und beginnen Sie mit dem Protokollieren Ihrer Metriken für die Trainingsausführungen.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Nachverfolgen von Remoteausführungen

Bei Remoteausführungen können Sie für das Trainieren Ihrer Modelle leistungsfähigere Computeumgebungen, z. B. GPU-fähige virtuelle Computer, oder Machine Learning Compute-Cluster verwenden. Informationen zu den verschiedenen Computeoptionen finden Sie unter [Übermitteln einer Trainingsausführung an ein Computeziel](how-to-set-up-training-targets.md).

Mit MLflow-Tracking mit Azure Machine Learning können Sie die protokollierten Metriken und Artefakte aus Ihren Remoteausführungen in Ihrem Azure Machine Learning-Arbeitsbereich speichern. Bei jeder Ausführung mit MLflow-Nachverfolgungscode werden automatisch Metriken im Arbeitsbereich protokolliert. 

Die folgende Conda-Beispielumgebung enthält `mlflow` und `azureml-mlflow` als PIP-Pakete. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

Konfigurieren Sie in Ihrem Skript die Umgebung für Computevorgänge und Trainingsausführungen mit der [`Environment`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)-Klasse. Erstellen Sie anschließend [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) mit Ihrer Remotecomputeumgebung als Computeziel.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Verwenden Sie bei dieser Konfiguration für Computevorgänge und Trainingsausführungen die `Experiment.submit()`-Methode, um eine Ausführung zu übermitteln. Mit dieser Methode wird der MLflow Tracking-URI automatisch festgelegt und die Protokollierung von MLflow an Ihren Arbeitsbereich geleitet.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Training mit MLflow-Projekten

[MLflow-Projekte](https://mlflow.org/docs/latest/projects.html) ermöglichen es Ihnen, Ihren Code zu organisieren und zu beschreiben, damit Sie von anderen Datenanalysten (oder automatisierten Tools) ausgeführt werden können. MLflow-Projekte mit Azure Machine Learning erlauben es, Trainingsausführungen in Ihrem Arbeitsbereich zu verfolgen und zu verwalten. 

Dieses Beispiel zeigt, wie Sie MLflow-Projekte mit Azure Machine Learning-Nachverfolgung lokal übermitteln.

Installieren Sie das Paket `azureml-mlflow`, um MLflow-Nachverfolgung mit Azure Machine Learning lokal für Ihre Experimente zu nutzen. Ihre Experimente können über ein Jupyter Notebook oder einen Code-Editor ausgeführt werden.

```shell
pip install azureml-mlflow
```

Importieren Sie die Klassen `mlflow` und [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py), um auf den Tracking-URI von MLflow zuzugreifen und Ihren Arbeitsbereich zu konfigurieren.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Legen Sie den Namen des MLflow-Experiments mit `set_experiment()` fest, und starten Sie Ihre Trainingsausführung mit `start_run()`. Verwenden Sie anschließend `log_metric()`, um die API für die MLflow-Protokollierung zu aktivieren, und beginnen Sie mit dem Protokollieren Ihrer Metriken für die Trainingsausführungen.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Erstellen Sie das Back-End-Konfigurationsobjekt, in dem die erforderlichen Informationen für die Integration gespeichert werden sollen, z. B. das Computeziel und den Typ der zu verwendenden verwalteten Umgebung.

```python
backend_config = {"USE_CONDA": False}
```
Fügen Sie das `azureml-mlflow`-Paket als PIP-Abhängigkeit Ihrer Umgebungskonfigurationsdatei hinzu, um Metriken und wichtige Artefakte in Ihrem Arbeitsbereich zu verfolgen. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Senden Sie die lokale Ausführung, und stellen Sie sicher, dass Sie den Parameter `backend = "azureml" ` festlegen. Mit dieser Einstellung können Sie Testläufe lokal senden und die zusätzliche Unterstützung für die automatische Ausgabenachverfolgung, Protokolldateien, Momentaufnahmen und im Arbeitsbereich ausgegebene Fehler nutzen. 

Zeigen Sie Ihre Ausführungen und Metriken in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) an. 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Anzeigen von Metriken und Artefakten in Ihrem Arbeitsbereich

Die Metriken und Artefakte aus dem MLflow-Protokoll werden in Ihrem Arbeitsbereich gespeichert. Sie können sie jederzeit anzeigen. Navigieren Sie dazu zu Ihrem Arbeitsbereich, und suchen Sie über den Namen nach dem Experiment in Ihrem Arbeitsbereich in [Azure Machine Learning-Studio](https://ml.azure.com).  Führen Sie alternativ den folgenden Befehl aus: 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Verwalten von Modellen 

Registrieren und verfolgen Sie Ihre Modelle mit der [Azure Machine Learning-Modellregistrierung](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere), die die MLflow-Modellregistrierung unterstützt. Azure Machine Learning-Modelle werden am MLflow-Modellschema ausgerichtet, sodass Sie diese Modelle problemlos exportieren und in verschiedene Workflows importieren können. Die Metadaten zu MLflow, z. B. die Ausführungs-ID, werden auch mit dem registrierten Modell gekennzeichnet, um sie besser nachverfolgen zu können. Benutzer können Trainingsausführungen senden, registrieren und bereitstellen, die aus MLflow-Ausführungen erstellt wurden. 

Wenn Sie Ihr Produktionsbereitstellungsmodell in einem Schritt bereitstellen und registrieren möchten, finden Sie weitere Informationen unter [Bereitstellen und Registrieren von MLflow-Modellen](#deploy-and-register-mlflow-models).

Führen Sie die folgenden Schritte aus, um ein Modell aus einer Ausführung zu registrieren und anzuzeigen:

1. Wenn die Ausführung beendet ist, rufen Sie die `register_model()`-Methode auf.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Zeigen Sie das registrierte Modell in Ihrem Arbeitsbereich in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) an.

    Im folgenden Beispiel sind die Metadaten für die MLflow-Nachverfolgung für das registrierte Modell `my-model` gekennzeichnet. 

    ![register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Wählen Sie die Registerkarte **Artefakte** aus, um alle Modelldateien anzuzeigen, die mit dem MLflow-Modellschema („conda.yaml“, MLmodel, „model.pkl“) übereinstimmen.

    ![Modellschema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Wählen Sie „MLmodel“ aus, um die von der Ausführung generierte MLmodel-Datei anzuzeigen.

    ![MLmodel-Schema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Bereitstellen und Registrieren von MLflow-Modellen 

Durch Bereitstellung Ihrer MLflow-Experimente als Azure Machine Learning-Webdienst können Sie die Azure Machine Learning-Funktionen zur Modellverwaltung und Datenabweichungserkennung nutzen und auf Ihre Produktionsmodelle anwenden.

Dazu ist Folgendes erforderlich:

1. Registrieren des Modells
1. Bestimmen der für Ihr Szenario zu verwendenden Bereitstellungskonfiguration

    1. [Azure Container Instance (ACI)](#deploy-to-aci) ist eine geeignete Wahl für eine schnelle Dev/Test-Bereitstellung.
    1. Für skalierbare Produktionsbereitstellungen sollten Sie [Azure Kubernetes Service (AKS)](#deploy-to-aks) verwenden.

Das folgende Diagramm veranschaulicht, wie Sie mit der MLflow-Bereitstellungs-API vorhandene MLflow-Modelle unabhängig vom Framework (PyTorch, Tensorflow, scikit-learn, ONNX usw.) als Azure Machine Learning-Webdienst bereitstellen und Ihre Produktionsmodelle im Arbeitsbereich verwalten können.

![ Bereitstellen von MLflow-Modellen mit Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Bereitstellen für ACI

Richten Sie Ihre Bereitstellungskonfiguration mit der Methode [deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) ein. Sie können auch Tags und Beschreibungen hinzufügen, um den Überblick über Ihren Webdienst zu behalten.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Registrieren Sie dann das Modell mithilfe der [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)-Methode des Azure Machine Learning SDK in einem Schritt bereit. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Bereitstellen für AKS

Zur Bereitstellung für AKS erstellen Sie zunächst einen AKS-Cluster. Erstellen Sie mit der [ComputeTarget.create()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-)-Methode einen AKS-Cluster. Die Erstellung eines neuen Clusters kann 20 bis 25 Minuten dauern.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Richten Sie Ihre Bereitstellungskonfiguration mit der Methode [deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) ein. Sie können auch Tags und Beschreibungen hinzufügen, um den Überblick über Ihren Webdienst zu behalten.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Führen Sie Registrierung und Bereitstellung des Modells mit der Azure Machine Learning SDK-Methode [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) in einem Schritt aus. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Die Bereitstellung des Diensts kann mehrere Minuten dauern.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beachten Sie Folgendes, falls Sie nicht planen, die protokollierten Metriken und Artefakte in Ihrem Arbeitsbereich zu verwenden: Das Löschen einzelner Einträge ist derzeit nicht möglich. Löschen Sie stattdessen die Ressourcengruppe, die das Speicherkonto und den Arbeitsbereich enthält, damit hierfür keine Gebühren anfallen:

1. Wählen Sie ganz links im Azure-Portal **Ressourcengruppen** aus.

   ![Löschen im Azure-Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie anschließend die Option **Löschen**.

## <a name="example-notebooks"></a>Beispielnotebooks

Die [Notebooks „MLflow mit Azure ML“](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstrieren und erklären die in diesem Artikel vorgestellten Konzepte.

> [!NOTE]
> Ein Communityrepository mit Beispielen, die MLflow verwenden, finden Sie unter https://github.com/Azure/azureml-examples.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten Ihrer Modelle](concept-model-management-and-deployment.md)
* Überwachen Ihrer Produktionsmodelle auf [Datenabweichungen](./how-to-enable-data-collection.md)
* [Nachverfolgen von Azure Databricks-Ausführungen mit MLflow](how-to-use-mlflow-azure-databricks.md)
