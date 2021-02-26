---
title: Aktualisieren von Machine Learning-Modellen mithilfe von Azure Data Factory
description: Dieser Artikel beschreibt das Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory v1 und Azure Machine Learning Studio (klassisch).
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7a27ed657ba21d9e2125df903b40d74cd81eacf6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379300"
---
# <a name="updating-azure-machine-learning-studio-classic-models-using-update-resource-activity"></a>Aktualisieren von Azure Machine Learning Studio (klassisch)-Modellen mithilfe der Ressourcenaktualisierungsaktivität

> [!div class="op_single_selector" title1="Transformationsaktivitäten"]
> * [Hive-Aktivität](data-factory-hive-activity.md) 
> * [Pig-Aktivität](data-factory-pig-activity.md)
> * [MapReduce-Aktivität](data-factory-map-reduce.md)
> * [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md)
> * [Spark-Aktivität](data-factory-spark.md)
> * [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-batch-execution-activity.md)
> * [Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md)
> * [U-SQL-Aktivität für Data Lake Analytics](data-factory-usql-activity.md)
> * [Benutzerdefinierte .NET-Aktivität](data-factory-use-custom-activities.md)


> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [update machine learning models in Data Factory](../update-machine-learning-models.md) (Aktualisieren von Machine Learning-Modellen in Data Factory).

Dieser Artikel ergänzt den Hauptartikel zu Azure Data Factory: Integration von Azure Machine Learning Studio (Classic): [Erstellen einer Vorhersagepipeline mithilfe von Azure Machine Learning Studio (Classic) und Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md) Wenn Sie dies noch nicht getan haben, lesen Sie zunächst den Hauptartikel, bevor Sie diesen Artikel lesen. 

## <a name="overview"></a>Übersicht
Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten für Azure Machine Learning Studio (klassisch) mit neuen Eingabedatasets neu trainiert werden. Wenn Sie mit dem erneuten Trainieren fertig sind, sollten Sie den Bewertungswebdienst mit dem neu trainierten ML-Modell aktualisieren. Typische Schritte, um das erneute Trainieren und das Aktualisieren von Modellen für Studio (klassisch) über Webdienste zu ermöglichen:

1. Erstellen Sie ein Experiment in [Azure Machine Learning Studio (klassisch)](https://studio.azureml.net).
2. Wenn Sie mit dem Modell zufrieden sind, verwenden Sie Azure Machine Learning Studio (klassisch), um Webdienste für das **Trainingsexperiment** und das Bewertungs-/**Vorhersageexperiment** zu veröffentlichen.

In der folgenden Tabelle werden die in diesem Beispiel verwendeten Webdienste beschrieben.  Details finden Sie unter [Erneutes Trainieren und Bereitstellen eines Machine Learning-Modells](../../machine-learning/classic/retrain-machine-learning-model.md).

- **Trainingswebdienst**: Empfängt Trainingsdaten und erzeugt trainierte Modelle. Die Ausgabe des erneuten Trainierens ist eine ILEARNER-Datei in Azure Blob Storage. Der **Standardendpunkt** wird automatisch erstellt, wenn Sie das Trainingsexperiment als Webdienst veröffentlichen. Sie können weitere Endpunkte erstellen, aber im Beispiel wird nur der Standardendpunkt verwendet.
- **Bewertungswebdienst**: Empfängt Datenbeispiele ohne Bezeichnung und macht Vorhersagen. Die Ausgabe der Vorhersage kann verschiedene Formen aufweisen, z. B. eine CSV-Datei oder Zeilen in Azure SQL-Datenbank. Dies ist abhängig von der Konfiguration des Experiments. Der Standardendpunkt wird automatisch erstellt, wenn Sie das Vorhersageexperiment als Webdienst veröffentlichen. 

Die folgende Abbildung zeigt die Beziehung zwischen Trainings- und Bewertungsendpunkten in Azure Machine Learning Studio (klassisch):

![WEB SERVICES](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Sie können den **Trainingswebdienst** mithilfe der **Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)** aufrufen. Das Aufrufen eines Trainingswebdiensts entspricht dem Aufrufen eines Webdiensts für Azure Machine Learning Studio (klassisch) (Bewertungswebdienst) für Bewertungsdaten. In den oben aufgeführten Abschnitten wird detailliert beschrieben, wie ein Webdienst für Azure Machine Learning Studio (klassisch) über eine Azure Data Factory-Pipeline aufgerufen wird. 

Sie können den **Bewertungswebdienst** aufrufen, indem Sie die **Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)** verwenden, um den Webdienst mit dem neu trainierten Modell zu aktualisieren. Die folgenden Beispiele enthalten Definitionen von verknüpften Diensten: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Der Bewertungswebdienst ist ein klassischer Webdienst.
Wenn der Bewertungswebdienst ein **klassischer Webdienst** ist, erstellen Sie den zweiten **nicht standardmäßigen und aktualisierbaren Endpunkt** mithilfe des Azure-Portals. Die erforderlichen Schritte finden Sie im Artikel [Erstellen von Endpunkten](../../machine-learning/classic/create-endpoint.md). Führen Sie folgende Schritte aus, nachdem Sie den nicht standardmäßigen aktualisierbaren Endpunkt erstellt haben:

* Klicken Sie auf **BATCHAUSFÜHRUNG**, um den URI-Wert für die **mlEndpoint**-JSON-Eigenschaft zu erhalten.
* Klicken Sie auf den Link **RESSOURCE AKTUALISIEREN**, um den URI-Wert für die **updateResourceEndpoint**-JSON-Eigenschaft abzurufen. Den API-Schlüssel finden Sie auf der Seite des Endpunkts (unten rechts).

![Aktualisierbarer Endpunkt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Das folgende Beispiel enthält eine JSON-Beispieldefinition für den mit AzureML verknüpften Dienst. Der verknüpfte Dienst verwendet die apiKey-Variable für die Authentifizierung.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Der Bewertungswebdienst ist ein Azure Resource Manager-Webdienst. 
Wenn der Webdienst der neue Webdiensttyp ist, der einen Azure Resource Manager-Endpunkt verfügbar macht, müssen Sie den zweiten **nicht standardmäßigen** Endpunkt nicht hinzufügen. Das **updateResourceEndpoint**-Element im verknüpften Dienst hat das Format: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Beim Abfragen des Webdiensts im [Azure Machine Learning Studio (Classic) Web Services-Portal](https://services.azureml.net/) können Sie Werte für Platzhalter in der URL abrufen. Der neue Typ der Ressourcenendpunktaktualisierung erfordert ein AAD (Azure Active Directory)-Token. Geben Sie im mit Studio (klassisch) verknüpften Dienst **servicePrincipalId** und **servicePrincipalKey** an. Informationen dazu finden Sie im Artikel zum [Erstellen eines Dienstprinzipals und Zuweisen von Berechtigungen zum Verwalten einer Azure-Ressource](../../active-directory/develop/howto-create-service-principal-portal.md). Hier sehen Sie ein Beispiel für eine Definition des verknüpften AzureML-Diensts: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Das folgende Szenario enthält weitere Details hierzu. Es enthält ein Beispiel für das erneute Trainieren und das Aktualisieren von Modellen für Studio (klassisch) über eine Azure Data Factory-Pipeline.

## <a name="scenario-retraining-and-updating-a-studio-classic-model"></a>Szenario: Erneutes Trainieren und Aktualisieren eines Modells für Studio (klassisch)
Dieser Abschnitt enthält eine Beispielpipeline, bei der die **Batchausführungsaktivität von Azure Machine Learning Studio (Classic)** zum erneuten Trainieren des Modells verwendet wird. Für die Pipeline wird außerdem die **Aktivität zur Ressourcenaktualisierung von Azure Machine Learning Studio (Classic)** verwendet, um das Modell im Bewertungswebdienst zu aktualisieren. Darüber hinaus enthält der Abschnitt JSON-Codeausschnitte für alle verknüpften Dienste, Datasets und Pipelines im Beispiel.

Im Folgenden ist die Diagrammansicht der Beispielpipeline dargestellt. Wie Sie sehen können, übernimmt die Batchausführungsaktivität für Studio (klassisch) die Trainingseingabe und erzeugt eine Trainingsausgabe (iLearner-Datei). Die Ressourcenaktualisierungsaktivität für Studio (klassisch) verwendet diese Trainingsausgabe und aktualisiert das Modell im Bewertungswebdienst-Endpunkt. Die Ressourcenaktualisierungsaktivität erzeugt keine Ausgabe. „placeholderBlob“ ist nur ein Platzhalter für ein Ausgabedataset, das für den Azure Data Factory-Dienst zum Ausführen der Pipeline erforderlich ist.

![Pipelinediagramm](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Mit Azure Blob Storage verknüpfter Dienst:
Der Azure-Speicher enthält die folgenden Daten:

* Trainingsdaten: Die Eingabedaten für den Trainingswebdienst für Studio (klassisch)  
* iLearner-Datei: Die Ausgabe des Trainingswebdiensts für Studio (klassisch). Die Datei dient darüber hinaus als Eingabe für die Aktivität zur Ressourcenaktualisierung.  

Dies ist die JSON-Beispieldefinition des verknüpften-Diensts:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Trainingseingabedataset:
Das folgende Dataset stellt die Trainingseingabedaten für den Trainingswebdienst für Studio (klassisch) dar. Die Batchausführungsaktivität für Studio (klassisch) verwendet dieses Dataset als Eingabe.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Trainingsausgabedataset:
Das folgende Dataset stellt die iLearner-Ausgabedatei des Trainingswebdiensts für Azure Machine Learning Studio (klassisch) dar. Die Batchausführungsaktivität für Azure Machine Learning Studio (Classic) generiert dieses Dataset. Dieses Dataset ist darüber hinaus die Eingabe für die Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch).

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-studio-classic-training-endpoint"></a>Verknüpfter Dienst für den Trainingsendpunkt für Studio (klassisch)
Der folgende JSON-Codeausschnitt definiert einen mit Studio (klassisch) verknüpften Dienst, der auf den Standardendpunkt des Trainingswebdiensts verweist.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

Führen Sie in **Azure Machine Learning Studio (klassisch)** folgende Schritte aus, um Werte für **mlEndpoint** und **apiKey** abzurufen:

1. Klicken Sie im linken Menü auf **WEB SERVICES** .
2. Klicken Sie in der Liste der Webdienste auf den **Trainingswebdienst** .
3. Klicken Sie neben dem Textfeld **API-Schlüssel** auf „Kopieren“. Fügen Sie den Schlüssel aus der Zwischenablage in den Data Factory-JSON-Editor ein.
4. Klicken Sie in **Azure Machine Learning Studio (klassisch)** auf den Link **BATCHAUSFÜHRUNG**.
5. Kopieren Sie den **Anforderungs-URI** im Abschnitt **Anforderung**, und fügen Sie ihn in den Data Factory-JSON-Editor ein.   

### <a name="linked-service-for-studio-classic-updatable-scoring-endpoint"></a>Verknüpfter Dienst für den aktualisierbaren Bewertungsendpunkt von Studio (klassisch):
Der folgende JSON-Codeausschnitt definiert einen mit Studio (klassisch) verknüpften Dienst, der auf den nicht standardmäßigen aktualisierbaren Endpunkt des Bewertungswebdiensts verweist.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Platzhalter-Ausgabedataset:
Die Ressourcenaktualisierungsaktivität für Studio (klassisch) generiert keine Ausgabe. Für Azure Data Factory ist aber ein Ausgabedataset für den Zeitplan einer Pipeline erforderlich. Aus diesem Grund verwenden wir in diesem Beispiel ein Dummy- bzw. Platzhalter-Dataset.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Die Pipeline enthält zwei Aktivitäten: **AzureMLBatchExecution** und **AzureMLUpdateResource**. Die Batchausführungsaktivität für Azure Machine Learning Studio (klassisch) verwendet die Trainingsdaten als Eingabe und erzeugt eine iLearner-Datei als Ausgabe. Die Aktivität ruft den Trainingswebdienst (das als Webdienst bereitgestellte Trainingsexperiment) mit den Trainingseingabedaten auf und empfängt die iLearner-Datei vom Webdienst. „placeholderBlob“ ist nur ein Platzhalter für ein Ausgabedataset, das für den Azure Data Factory-Dienst zum Ausführen der Pipeline erforderlich ist.

![Pipelinediagramm](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```