---
title: MLflow Tracking für ML-Experimente in Azure Databricks
titleSuffix: Azure Machine Learning
description: Richten Sie MLflow von Azure Machine Learning ein, um Metriken und Artefakte aus ML-Experimenten in Azure Databricks zu protokollieren.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e72784dbdcf08d672a8498609ca3a5bbd11e632d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319028"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Nachverfolgen für ML-Experimente in Azure Databricks mit MLflow und Azure Machine Learning (Vorschau)

In diesem Artikel erfahren Sie, wie Sie den Nachverfolgungs-URI und die Protokollierungs-API von MLflow (zusammen als [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) bezeichnet) verwenden, um Azure Databricks-Experimente, MLflow und Azure Machine Learning zu verknüpfen.

[MLflow](https://www.mlflow.org) ist eine Open-Source-Bibliothek zum Verwalten des Lebenszyklus Ihrer Machine Learning-Experimente. MLflow Tracking ist eine Komponente von MLflow, die Metriken zu Trainingsausführungen und Modellartefakte protokolliert und nachverfolgt. Erfahren Sie mehr zu [Azure Databricks und MLflow](/azure/databricks/applications/mlflow/). 

Weitere Informationen zur Integration von MLflow- und Azure Machine Learning Funktionen finden Sie unter [Nachverfolgen von Experimentausführungen und Erstellen von Endpunkten mit MLflow und Azure Machine Learning](how-to-use-mlflow.md).

>[!NOTE]
> Als Open Source-Bibliothek wird MLflow häufig geändert. Daher sollte die Funktionalität, die über die Integration von Azure Machine Learning und MLflow zur Verfügung gestellt wird, als Vorschau betrachtet und nicht vollständig von Microsoft unterstützt werden.

> [!TIP]
> Die Informationen in diesem Dokument sind hauptsächlich für Datenanalysten und Entwickler gedacht, die den Modelltrainingsprozess überwachen möchten. Wenn Sie Administrator sind und sich für die Überwachung der Nutzung und Ereignisse von Azure Machine Learning (z. B. Kontingente, abgeschlossene Trainingsausführungen oder abgeschlossene Modellimplementierungen) interessieren, helfen Ihnen die Informationen im Artikel [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md) weiter.

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie das `azureml-mlflow`-Paket. 
    * Mit diesem Paket wird automatisch `azureml-core` des [The Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) bereitgestellt, um MLflow den Zugriff auf Ihren Arbeitsbereich zu ermöglichen.
* Sie benötigen einen [Azure Databricks-Arbeitsbereich und -Cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Erstellen Sie einen Azure Machine Learning-Arbeitsbereich.](how-to-manage-workspace.md)

## <a name="track-azure-databricks-runs"></a>Nachverfolgen von Azure Databricks-Ausführungen

Mit MLflow Tracking in Azure Machine Learning können Sie die protokollierten Metriken und Artefakte aus Ihren Azure Databricks-Ausführungen in den folgenden Arbeitsbereichen speichern: 

* Azure Databricks-Arbeitsbereich
* Azure Machine Learning-Arbeitsbereich

Installieren Sie in Ihrem Azure Databricks-Cluster die Bibliothek 

1. *azureml-mlflow* von PyPi, um sicherzustellen, dass Ihr Cluster Zugriff auf die notwendigen Funktionen und Klassen hat, nachdem Sie den Azure Databricks-Arbeitsbereich und -Cluster erstellt haben.

1. Richten Sie Ihr Notebook für das Experiment ein.

1. Verbinden Sie Ihre Azure Databricks- und Azure Machine Learning-Arbeitsbereiche.

Weitere Erläuterungen zu diesen Schritten finden Sie in den folgenden Abschnitten, damit Sie Ihre MLflow-Experimente mit Azure Databricks erfolgreich ausführen können. 

## <a name="install-libraries"></a>Installieren von Bibliotheken

Navigieren Sie zum Installieren von Bibliotheken in Ihrem Cluster zur Registerkarte **Bibliotheken** , und klicken Sie auf **Neue installieren**.

 ![MLflow mit Azure Databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

Geben Sie im Feld **Paket** den Namen „azureml-mlflow“ ein, und klicken Sie dann auf „Installieren“. Wiederholen Sie diesen Schritt bei Bedarf, um für Ihr Experiment zusätzliche Pakete in Ihrem Cluster zu installieren.

 ![Installieren der Azure DB-MLflow-Bibliothek](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Einrichten Ihres Notebooks 

Klicken Sie in der linken Navigation auf 
1. **Arbeitsbereiche** , wenn Ihr Azure Databricks-Cluster eingerichtet wurde. 
1. Erweitern Sie das Dropdown Menü „Arbeitsbereiche“, und klicken Sie auf **Importieren**.
1. Ziehen Sie das Experimentnotebook dorthin, oder suchen Sie danach, um den Azure Databricks-Arbeitsbereich zu importieren.
1. Wählen Sie **Importieren** aus. Ihr Experimentnotebook wird automatisch geöffnet.
1. Wählen Sie unter dem Notebooktitel oben links den Cluster aus, der an Ihr Experimentnotebook angehängt werden soll. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Verbinden Ihres Azure Databricks- und Ihres Azure Machine Learning-Arbeitsbereichs

Wenn Sie Ihren Azure Databricks-Arbeitsbereich mit Ihrem Azure Machine Learning-Arbeitsbereich verknüpfen, können Sie Ihre Experimentdaten im Azure Machine Learning-Arbeitsbereich nachverfolgen.

So können Sie den Azure Databricks-Arbeitsbereich mit einem neuen oder vorhandenen Azure Machine Learning-Arbeitsbereich verknüpfen: 
1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com)an.
1. Navigieren Sie zur **Übersicht** Ihres Azure Databricks-Arbeitsbereichs.
1. Klicken Sie unten rechts auf **Link Azure Machine Learning Arbeitsbereichs** (Azure Machine Learning-Arbeitsbereich verknüpfen). 

 ![Verknüpfen von Azure Databricks- und Azure Machine Learning-Arbeitsbereichen](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>MLflow Tracking in Ihrem Arbeitsbereich

Nachdem Sie den Arbeitsbereich instanziiert haben, wird MLflow Tracking automatisch für alle folgenden Orte aktiviert:

* Verknüpfter Azure Machine Learning-Arbeitsbereich
* Ursprünglicher ADB-Arbeitsbereich 

Danach werden alle Ihre Experimente in den verwalteten Azure Machine Learning-Nachverfolgungsdienst übertragen.

Der folgende Code sollte sich in Ihrem Experimentnotebook befinden, um Ihren verknüpften Azure Machine Learning-Arbeitsbereich zu erhalten. 

Für diesen Code gilt Folgendes: 

*  Mit diesem Code werden die Details Ihres Azure-Abonnements abgerufen, um Ihren Azure Machine Learning-Arbeitsbereich zu instanziieren. 

* Der Code geht davon aus, dass Sie über eine Ressourcengruppe und einen Azure Machine Learning-Arbeitsbereich verfügen. Andernfalls können Sie [diese erstellen](how-to-manage-workspace.md). 

* Der Code legt den Namen des Experiments fest. Das `user_name`-Objekt hier stimmt mit dem `user_name`-Objekt überein, das dem Azure Databricks-Arbeitsbereich zugeordnet ist.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Beschränken der MLflow-Nachverfolgung auf den Azure Machine Learning-Arbeitsbereich

Wenn Sie Ihre nachverfolgten Experimente an einem zentralen Ort verwalten möchten, können Sie für die MLflow-Überwachung festlegen, dass die Nachverfolgung **nur** in Ihrem Azure Machine Learning-Arbeitsbereich erfolgt. 

Fügen Sie den folgenden Code in Ihr Skript ein:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importieren Sie in Ihrem Trainingsskript `mlflow`, um die APIs für die MLflow-Protokollierung zu verwenden und mit dem Protokollieren Ihrer Ausführungsmetriken zu beginnen. Das folgende Beispiel protokolliert die Epochenverlustmetrik. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Registrieren von Modellen mit MLflow

Nachdem Ihr Modell trainiert wurde, können Sie Ihre Modelle auf dem Back-End-Nachverfolgungsserver mit der `mlflow.<model_flavor>.log_model()`-Methode protokollieren und registrieren. `<model_flavor>` ist das Framework, das dem Modell zugeordnet ist. [Erfahren Sie, welche Modellvarianten unterstützt werden](https://mlflow.org/docs/latest/models.html#model-api). 

Der Back-End-Nachverfolgungsserver ist standardmäßig der Azure Databricks-Arbeitsbereich. Wenn Sie [nicht festgelegt haben, dass MLflow Tracking nur im Azure Machine Learning-Arbeitsbereich verfolgt werden soll](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), ist der Back-End-Nachverfolgungsserver der Azure Machine Learning-Arbeitsbereich.   

* **Falls kein registriertes Modell mit dem angegebenen Namen vorhanden ist** , registriert die Methode ein neues Modell, erstellt die erste Version und gibt das MLflow-Objekt „ModelVersion“ zurück. 

* **Wenn bereits ein registriertes Modell mit dem Namen vorhanden ist** , erstellt die Methode eine neue Modellversion und gibt das Versionsobjekt zurück. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Erstellen von Endpunkten für MLflow-Modelle

Wenn Sie bereit sind, einen Endpunkt für die ML-Modelle zu erstellen, können Sie Folgendes bereitstellen: 

* Einen Azure Machine Learning-Webdienst für die Anforderung/Antwort-Kommunikation zur interaktiven Bewertung. Mit dieser Bereitstellung können Sie die Azure Machine Learning-Modellverwaltung und die Datendrift-Erkennungsfunktionen für Ihre Produktionsmodelle nutzen und auf diese anwenden. 

* MLflow-Modellobjekte, die in Streaming- oder Batchpipelines als Python-Funktionen oder Pandas-UDFs in einem Azure Databricks-Arbeitsbereich verwendet werden können.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Bereitstellen von Modellen für Azure Machine Learning-Endpunkte 
Sie können die [mlflow.azureml.deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy)-API nutzen, um ein Modell in Ihrem Azure Machine Learning-Arbeitsbereich bereitzustellen. Wenn Sie das Modell nur im Azure Databricks-Arbeitsbereich registriert haben, wie im Abschnitt [Registrieren von Modellen mit MLflow](#register-models-with-mlflow) beschrieben, geben Sie den `model_name`-Parameter an, um das Modell im Azure Machine Learning-Arbeitsbereich zu registrieren. 

Azure Databricks-Ausführungen können an den folgenden Endpunkten bereitgestellt werden: 
* [Azure Container Instance](how-to-use-mlflow.md#deploy-to-aci)
* [Azure Kubernetes Service](how-to-use-mlflow.md#deploy-to-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Bereitstellen von Modellen für Azure Databricks-Endpunkte zur Batchbewertung 

Sie können Azure Databricks-Cluster zur Batchbewertung auswählen. Das MLflow-Modell wird geladen und als Spark Pandas-UDF verwendet, um neue Daten zu bewerten. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Beachten Sie Folgendes, falls Sie nicht planen, die protokollierten Metriken und Artefakte in Ihrem Arbeitsbereich zu verwenden: Das Löschen einzelner Einträge ist derzeit nicht möglich. Löschen Sie stattdessen die Ressourcengruppe, die das Speicherkonto und den Arbeitsbereich enthält, damit hierfür keine Gebühren anfallen:

1. Wählen Sie ganz links im Azure-Portal **Ressourcengruppen** aus.

   ![Löschen im Azure-Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie anschließend die Option **Löschen**.

## <a name="example-notebooks"></a>Beispielnotebooks

In den [Notebooks „MLflow mit Azure Machine Learning“](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) werden die in diesem Artikel vorgestellten Konzepte weiter erläutert und demonstriert.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten Ihrer Modelle](concept-model-management-and-deployment.md)
* [Verfolgen von Experimentausführungen und Erstellen von Endpunkten mit MLflow und Azure Machine Learning](how-to-use-mlflow.md) 
* Erfahren Sie mehr zu [Azure Databricks und MLflow](/azure/databricks/applications/mlflow/).