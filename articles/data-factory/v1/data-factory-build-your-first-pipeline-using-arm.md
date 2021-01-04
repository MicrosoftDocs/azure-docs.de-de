---
title: Erstellen der ersten Data Factory (Resource Manager-Vorlage)
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mithilfe einer Azure Resource Manager-Vorlage.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: e65039d39bea4063f717709f97b090e465c5e3c4
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508508"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Erstellen der ersten Azure Data Factory mit einer Azure Resource Manager-Vorlage
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, helfen Ihnen die Informationen unter [Schnellstart: Erstellen einer Data Factory und Pipeline mit dem .NET SDK](../quickstart-create-data-factory-dot-net.md) weiter.

In diesem Artikel verwenden Sie eine Azure Resource Manager-Vorlage, um Ihre erste Azure Data Factory zu erstellen. Falls Sie das Tutorial mit anderen Tools/SDKs absolvieren möchten, wählen Sie in der Dropdownliste eine andere Option aus.

Die Pipeline in diesem Tutorial enthält nur eine Aktivität: eine **HDInsight Hive-Aktivität**. Bei dieser Aktivität wird ein Hive-Skript in einem Azure HDInsight-Cluster ausgeführt, mit dem Eingabedaten transformiert werden, um Ausgabedaten zu erhalten. Die Pipeline zwischen dem Start- und Endzeitpunkt wird einmal pro Monat ausgeführt. 

> [!NOTE]
> Die Datenpipeline in diesem Tutorial transformiert Eingabedaten in Ausgabedaten. Ein Tutorial zum Kopieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Die Pipeline in diesem Tutorial enthält nur eine Aktivität: HDInsightHive. Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Planung und Ausführung in einer Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lesen Sie sich den Artikel mit der [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) durch, und führen Sie die Schritte zur Erfüllung der **Voraussetzungen** aus.
* Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) zum Installieren der neuesten Version von Azure PowerShell auf Ihrem Computer.
* Informationen zu Azure Resource Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) . 

## <a name="in-this-tutorial"></a>Dieses Lernprogramm umfasst folgende Punkte

| Entität | BESCHREIBUNG |
| --- | --- |
| Mit Azure Storage verknüpfter Dienst |Verknüpft Ihr Azure Storage-Konto mit der Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. |
| Bedarfsgesteuerter verknüpfter HDInsight-Dienst |Verknüpft einen bedarfsgesteuerten HDInsight-Cluster mit der Data Factory. Der Cluster wird automatisch für Sie erstellt, damit Sie Daten verarbeiten können, und nach Abschluss der Verarbeitung automatisch gelöscht. |
| Azure-Blob-Eingabedataset |Verweist auf den mit Azure Storage verknüpften Dienst. Der verknüpfte Dienst verweist auf ein Azure Storage-Konto, und das Azure-Blobdataset gibt den Container, Ordner und Dateinamen in dem Speicher an, der die Eingabedaten enthält. |
| Azure-Blob-Ausgabedataset |Verweist auf den mit Azure Storage verknüpften Dienst. Der verknüpfte Dienst verweist auf ein Azure Storage-Konto, und das Azure-Blobdataset gibt den Container, Ordner und Dateinamen in dem Speicher an, der die Ausgabedaten enthält. |
| Datenpipeline |Die Pipeline verfügt über eine Aktivität vom Typ „HDInsightHive“ zum Nutzen des Eingabedatasets und Erzeugen des Ausgabedatasets. |

Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Es gibt zwei Arten von Aktivitäten: [Datenverschiebungen](data-factory-data-movement-activities.md) und [Datentransformationen](data-factory-data-transformation-activities.md). In diesem Tutorial erstellen Sie eine Pipeline mit einer einzelnen Aktivität (Hive-Aktivität).

Der folgende Abschnitt enthält die vollständige Resource Manager-Vorlage zum Definieren von Data Factory-Entitäten, damit Sie das Tutorial schnell durchlaufen und die Vorlage testen können. Informationen zum Definieren der einzelnen Data Factory-Entitäten finden Sie im Abschnitt [Data Factory-Entitäten in der Vorlage](#data-factory-entities-in-the-template). Weitere Informationen zur JSON-Syntax und Eigenschaften für Data Factory-Ressourcen in einer Vorlage finden Sie unter [Microsoft.DataFactory-Ressourcentypen](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json-template"></a>Data Factory-JSON-Vorlage
Das Grundgerüst einer Resource Manager-Vorlage zum Definieren einer Data Factory sieht wie folgt aus: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Erstellen Sie eine JSON-Datei mit dem Namen **ADFTutorialARM.json** im Ordner **C:\ADFGetStarted** mit dem folgenden Inhalt:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Ein weiteres Beispiel für eine Resource Manager-Vorlage zum Erstellen einer Azure Data Factory finden Sie unter [Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe einer Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  
> 
> 

## <a name="parameters-json"></a>Parameter (JSON)
Erstellen Sie eine JSON-Datei namens **ADFTutorialARM-Parameters.json** mit Parametern für die Azure Resource Manager-Vorlage.  

> [!IMPORTANT]
> Geben Sie für die Parameter **storageAccountName** und **storageAccountKey** in dieser Parameterdatei den Namen und den Schlüssel Ihres Azure Storage-Kontos an. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Für die Entwicklungs-, die Test- und die Produktionsumgebung können jeweils separate JSON-Parameterdateien mit der gleichen Data Factory-JSON-Vorlage verwendet werden. Mithilfe eines PowerShell-Skripts können Sie die Bereitstellung von Data Factory-Entitäten in diesen Umgebungen automatisieren. 
> 
> 

## <a name="create-data-factory"></a>Erstellen einer Data Factory
1. Starten Sie **Azure PowerShell** , und führen Sie den folgenden Befehl aus: 
   * Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:
     ```PowerShell
     Connect-AzAccount
     ```  
   * Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:
     ```PowerShell
     Get-AzSubscription
     ``` 
   * Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
     ```
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```   
2. Führen Sie den folgenden Befehl aus, um die Data Factory-Entitäten bereitzustellen, die Sie in Schritt 1 mit der Resource Manager-Vorlage erstellt haben. 

    ```PowerShell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Überwachen der Pipeline
1. Klicken Sie nach der Anmeldung beim [Azure-Portal](https://portal.azure.com/) auf **Durchsuchen**, und wählen Sie **Data Factorys** aus.
     ![Durchsuchen &gt; Data Factorys](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. Klicken Sie auf dem Blatt **Data Factorys** auf die von Ihnen erstellte Data Factory (**TutorialFactoryARM**).    
3. Klicken Sie auf dem Blatt **Data Factory** für Ihre Data Factory auf **Diagramm**.

     ![Kachel „Diagramm“](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. In der **Diagrammansicht** sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.
   
   ![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. Doppelklicken Sie in der Diagrammansicht auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.
   
    ![Screenshot des Datasets „AzureBlobOutput“](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Wenn die Verarbeitung abgeschlossen ist, wird der Slice mit dem Zustand **Bereit** angezeigt. Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel einige Zeit (etwa 20 Minuten). Daher ist damit zu rechnen, dass die Pipeline **etwa 30 Minuten** zum Verarbeiten des Slice benötigt.
   
    ![Dataset](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Sobald der Slice den Status **Bereit** hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.  

Unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md) finden Sie eine Anleitung zum Überwachen der in diesem Tutorial erstellten Pipeline und Datasets über die Blätter im Azure-Portal.

Sie können auch die App „Überwachung und Verwaltung“ verwenden, um Ihre Datenpipelines zu überwachen. Einzelheiten zur Verwendung der Anwendung finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md) . 

> [!IMPORTANT]
> Die Eingabedatei wird bei erfolgreicher Verarbeitung des Slice gelöscht. Wenn Sie den Slice erneut ausführen oder das Tutorial nochmals durchgehen möchten, laden Sie die Eingabedatei (input.log) daher in den Ordner „inputdata“ des Containers „adfgetstarted“ hoch.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Data Factory-Entitäten in der Vorlage
### <a name="define-data-factory"></a>Definieren einer Data Factory
Das folgende Beispiel zeigt, wie Sie in der Resource Manager-Vorlage eine Data Factory definieren:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/factories",
    "location": "West US"
}
```
„dataFactoryName“ wird wie folgt definiert: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
Hierbei handelt es sich um eine eindeutige Zeichenfolge auf Grundlage der Ressourcengruppen-ID.  

### <a name="defining-data-factory-entities"></a>Definieren von Data Factory-Entitäten
In der JSON-Vorlage werden folgende Data Factory-Entitäten definiert: 

* [Mit Azure Storage verknüpfter Dienst](#azure-storage-linked-service)
* [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](#hdinsight-on-demand-linked-service)
* [Azure-Blob-Eingabedataset](#azure-blob-input-dataset)
* [Azure-Blob-Ausgabedataset](#azure-blob-output-dataset)
* [Datenpipeline mit einer Kopieraktivität](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst
In diesem Abschnitt geben Sie Name und Schlüssel Ihres Azure Storage-Kontos an. Informationen zu JSON-Eigenschaften zum Definieren eines mit Azure Storage verknüpften Diensts finden Sie unter [Mit Azure Storage verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
Die Verbindungszeichenfolge (**connectionString**) enthält die Parameter „storageAccountName“ und „storageAccountKey“. Die Werte für diese Parameter werden mithilfe einer Konfigurationsdatei übergeben. In der Definition werden außerdem die in der Vorlage definierten Variablen „azureStorageLinkedService“ und „dataFactoryName“ verwendet. 

#### <a name="hdinsight-on-demand-linked-service"></a>Bedarfsgesteuerter verknüpfter HDInsight-Dienst
Der Artikel [Verknüpfte Computedienste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) enthält Details zu JSON-Eigenschaften, die zum Definieren eines bedarfsgesteuerten verknüpften HDInsight-Diensts verwendet werden.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```

Beachten Sie folgende Punkte: 

* Die Data Factory erstellt mit dem obigen JSON-Code einen **Linux-basierten** HDInsight-Cluster für Sie. Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
* Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie **Ihren eigenen HDInsight-Cluster** verwenden. Ausführliche Informationen finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
* Der HDInsight-Cluster erstellt einen **Standardcontainer** im Blobspeicher, den Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dieses Verhalten ist beabsichtigt. Durch den bedarfsgesteuerten, mit HDInsight verknüpften Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss, es sei denn, ein aktiver Cluster (**timeToLive**) ist vorhanden und wird gelöscht, nachdem die Verarbeitung abgeschlossen ist.
  
    Wenn mehr Segmente verarbeitet werden, werden in Azure Blob Storage viele Container angezeigt. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Die Namen dieser Container basieren auf dem folgenden Muster: „adf **ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel“. Verwenden Sie Tools wie den [Microsoft Azure Storage-Explorer](https://storageexplorer.com/), um Container in Ihrem Azure-Blobspeicher zu löschen.

Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

#### <a name="azure-blob-input-dataset"></a>Azure-Blob-Eingabedataset
In diesem Abschnitt geben Sie den Blobcontainer, den Ordner und die Datei mit den Eingabedaten an. Informationen zu JSON-Eigenschaften zum Definieren eines Azure-Blobdatasets finden Sie unter [Eigenschaften des Dataset-Typs „Azure-Blob“](data-factory-azure-blob-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
In dieser Definition werden die folgenden Parameter verwendet, die in der Parametervorlage definiert sind: blobContainer, inputBlobFolder und inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Azure-Blob-Ausgabedataset
Sie geben die Namen des Blobcontainers und des Ordners mit den Ausgabedaten an. Informationen zu JSON-Eigenschaften zum Definieren eines Azure-Blobdatasets finden Sie unter [Eigenschaften des Dataset-Typs „Azure-Blob“](data-factory-azure-blob-connector.md#dataset-properties).  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

In dieser Definition werden die folgenden Parameter verwendet, die in der Parametervorlage definiert sind: blobContainer und outputBlobFolder. 

#### <a name="data-pipeline"></a>Datenpipeline
Sie definieren eine Pipeline zum Transformieren von Daten, indem Sie Hive-Skript in einem bedarfsgesteuerten Azure HDInsight-Cluster ausführen. Informationen zu JSON-Elementen zum Definieren einer Pipeline für dieses Beispiel finden Sie unter [Pipeline-JSON](data-factory-create-pipelines.md#pipeline-json). 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                    "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                        "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                    }
                },
                "inputs": [
                    {
                        "name": "[variables('blobInputDatasetName')]"
                    }
                ],
                "outputs": [
                    {
                        "name": "[variables('blobOutputDatasetName')]"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>Wiederverwenden der Vorlage
In diesem Tutorial haben Sie eine Vorlage zum Definieren von Data Factory-Entitäten und eine Vorlage zum Übergeben von Parameterwerten erstellt. Mit der gleichen Vorlage können Sie Data Factory-Entitäten in unterschiedlichen Umgebungen bereitstellen. Hierzu müssen Sie lediglich eine Parameterdatei für die jeweilige Umgebung erstellen und beim Bereitstellen in dieser Umgebung verwenden.     

Beispiel:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Der erste Befehl verwendet eine Parameterdatei für die Entwicklungsumgebung, der zweite eine Parameterdatei für die Testumgebung und der dritte eine Parameterdatei für die Produktion.  

Mit der Vorlage können auch wiederholte Aufgaben durchgeführt werden. Angenommen, Sie müssen zahlreiche Data Factorys mit einer Pipeline oder mehreren Pipelines erstellen, die die gleiche Logik implementieren, aber die einzelnen Data Factorys verwenden jeweils ein anderes Azure Storage- und Azure SQL-Datenbank-Konto. In diesem Szenario können Sie zur Erstellung der Data Factorys die gleiche Vorlage in der gleichen Umgebung (Entwicklungs-, Test- oder Produktionsumgebung) mit unterschiedlichen Parameterdateien verwenden. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Resource Manager-Vorlage zum Erstellen eines Gateways
Hier ist eine Resource Manager-Beispielvorlage zum Erstellen eines logischen Gateways im Hintergrund angegeben. Installieren Sie ein Gateway auf Ihrem lokalen Computer oder Ihrer Azure IaaS-VM, und registrieren Sie das Gateway beim Data Factory-Dienst per Schlüssel. Ausführlichere Informationen finden Sie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/factories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
Mit dieser Vorlage wird eine Data Factory mit dem Namen „GatewayUsingArmDF“ und dem Gateway mit dem Namen „GatewayUsingARM“ erstellt. 

## <a name="see-also"></a>Weitere Informationen

| Thema | BESCHREIBUNG |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. |
| [Datasets](data-factory-create-datasets.md) |Dieser Artikel enthält Informationen zu Datasets in Azure Data Factory. |
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) |In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. |
| [Überwachen und Verwalten von Pipelines mit der Überwachungs-App](data-factory-monitor-manage-app.md) |In diesem Artikel wird das Überwachen, Verwalten und Debuggen von Pipelines mit der App für die Überwachung und Verwaltung beschrieben. |

