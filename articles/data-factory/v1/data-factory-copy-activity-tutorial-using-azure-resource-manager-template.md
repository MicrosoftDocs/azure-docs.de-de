---
title: 'Tutorial: Erstellen einer Pipeline mithilfe einer Resource Manager-Vorlage '
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mithilfe einer Azure Resource Manager-Vorlage. Diese Pipeline kopiert Daten aus Azure Blob Storage in Azure SQL-Datenbank.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a007e64a7bd034397c2030c435a5ad349bd4acc7
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608747"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Tutorial: Verwenden einer Azure Resource Manager-Vorlage zum Erstellen einer Data Factory-Pipeline zum Kopieren von Daten 
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 

In diesem Tutorial erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage eine Azure Data Factory erstellen. Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Sie transformiert keine Eingabedaten in Ausgabedaten. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

In diesem Tutorial erstellen Sie eine Pipeline mit nur einer Aktivität: die Kopieraktivität. Die Kopieraktivität kopiert die Daten aus einem unterstützten Datenspeicher in einen unterstützten Senkendatenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md). 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lesen Sie sich die [Tutorialübersicht und die Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) durch, und führen Sie die Schritte zur Erfüllung der **Voraussetzungen** aus.
* Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) zum Installieren der neuesten Version von Azure PowerShell auf Ihrem Computer. In diesem Tutorial stellen Sie Data Factory-Entitäten mithilfe von PowerShell bereit. 
* Optional: Informationen zu Azure Resource Manager-Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md).

## <a name="in-this-tutorial"></a>Dieses Lernprogramm umfasst folgende Punkte
In diesem Tutorial erstellen Sie eine Data Factory mit den folgenden Data Factory-Entitäten:

| Entität | BESCHREIBUNG |
| --- | --- |
| Mit Azure Storage verknüpfter Dienst |Verknüpft Ihr Azure Storage-Konto mit der Data Factory. Azure Storage ist der Quelldatenspeicher und Azure SQL-Datenbank der Senkendatenspeicher für die Kopieraktivität im Tutorial. Der Dienst gibt das Speicherkonto an, das die Eingabedaten für die Kopieraktivität enthält. |
| Mit Azure SQL-Datenbank verknüpfter Dienst |Verknüpft die Datenbank in Azure SQL-Datenbank mit der Data Factory. Der Dienst gibt die Datenbank zum Speichern der Ausgabedaten für die Kopieraktivität an. |
| Azure-Blob-Eingabedataset |Verweist auf den mit Azure Storage verknüpften Dienst. Der verknüpfte Dienst verweist auf ein Azure Storage-Konto, und das Azure-Blobdataset gibt den Container, Ordner und Dateinamen in dem Speicher an, der die Eingabedaten enthält. |
| Azure SQL-Ausgabedataset |Verweist auf den mit Azure SQL verknüpften Dienst. Der mit Azure SQL verknüpfte Dienst verweist auf einen logischen SQL Server, und das Azure SQL-Dataset gibt den Namen der Tabelle mit den Ausgabedaten an. |
| Datenpipeline |Die Pipeline verfügt über eine einzelne Kopieraktivität mit dem Azure-Blob-Dataset als Eingabe und dem Azure SQL-Dataset als Ausgabe. Die Kopieraktivität kopiert Daten aus einem Azure-Blob in eine Tabelle in Azure SQL-Datenbank. |

Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Es gibt zwei Arten von Aktivitäten: [Datenverschiebungen](data-factory-data-movement-activities.md) und [Datentransformationen](data-factory-data-transformation-activities.md). In diesem Tutorial erstellen Sie eine Pipeline mit einer einzelnen Kopieraktivität.

![Kopieren von Daten aus dem Azure-Blob in Azure SQL-Datenbank](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

Der folgende Abschnitt enthält die vollständige Resource Manager-Vorlage zum Definieren von Data Factory-Entitäten, damit Sie das Tutorial schnell durchlaufen und die Vorlage testen können. Informationen zum Definieren der einzelnen Data Factory-Entitäten finden Sie im Abschnitt [Data Factory-Entitäten in der Vorlage](#data-factory-entities-in-the-template).

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
            "type": "Microsoft.DataFactory/datafactories",
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
Erstellen Sie im Ordner **C:\ADFGetStarted** eine JSON-Datei namens **ADFCopyTutorialARM.json** mit folgendem Inhalt:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the logical SQL server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the logical SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the logical SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
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
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
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
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
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
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Parameter (JSON)
Erstellen Sie eine JSON-Datei namens **ADFCopyTutorialARM-Parameters.json** mit Parametern für die Azure Resource Manager-Vorlage. 

> [!IMPORTANT]
> Geben Sie für „storageAccountName“ und „storageAccountKey“ den Namen und den Schlüssel Ihres Azure Storage-Kontos an.  
> 
> Geben Sie den logischen SQL-Server, die Datenbank, den Benutzer und das Kennwort für die Parameter „sqlServerName“, „databaseName“, „sqlServerUserName“ und „sqlServerPassword“ an.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the logical SQL server>" },
        "databaseName": { "value": "<Name of the database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
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
   * Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen:
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. Führen Sie den folgenden Befehl aus, um die Data Factory-Entitäten bereitzustellen, die Sie in Schritt 1 mit der Resource Manager-Vorlage erstellt haben.

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Überwachen der Pipeline

1. Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie im linken Menü auf **Data Factorys**, oder klicken Sie auf **Alle Dienste** und anschließend unter der Kategorie **INTELLIGENCE + ANALYSE** auf **Data Factorys**.
   
    ![Menü „Data Factorys“](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)

1. Suchen Sie auf der Seite **Data Factorys** nach Ihrer Data Factory (AzureBlobToAzureSQLDatabaseDF). 
   
    ![Suchen nach der Data Factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  

1. Klicken Sie auf Ihre Data Factory. Die Startseite für die Data Factory erscheint.
   
    ![Startseite für die Data Factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  

1. Befolgen Sie die Anweisungen unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md), um die in diesem Tutorial erstellte Pipeline und die erstellten Datasets zu überwachen. Visual Studio unterstützt derzeit keine Überwachung von Data Factory-Pipelines.

1. Wenn sich ein Slice im Zustand **Bereit** befindet, vergewissern Sie sich, dass die Daten in die Tabelle **emp** in der Azure SQL-Datenbank kopiert wurden.

Weitere Informationen zum Verwenden der Blätter im Azure-Portal zum Überwachen der in diesem Tutorial erstellten Pipeline und Datasets finden Sie unter [Überwachen von Datasets und Pipelines](data-factory-monitor-manage-pipelines.md).

Weitere Informationen zum Verwenden der Anwendung „Überwachung und Verwaltung“ zum Überwachen Ihrer Datenpipelines finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit der App „Überwachung und Verwaltung“](data-factory-monitor-manage-app.md).

## <a name="data-factory-entities-in-the-template"></a>Data Factory-Entitäten in der Vorlage

### <a name="define-data-factory"></a>Definieren einer Data Factory

Das folgende Beispiel zeigt, wie Sie in der Resource Manager-Vorlage eine Data Factory definieren:

```json
{
  "resources": [
    {
      "name": "[variables('dataFactoryName')]",
      "apiVersion": "2015-10-01",
      "type": "Microsoft.DataFactory/datafactories",
      "location": "West US"
    }
  ]
}
```

„dataFactoryName“ wird wie folgt definiert: 

```json
{
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
}
```

Hierbei handelt es sich um eine eindeutige Zeichenfolge auf Grundlage der Ressourcengruppen-ID.

### <a name="defining-data-factory-entities"></a>Definieren von Data Factory-Entitäten

In der JSON-Vorlage werden folgende Data Factory-Entitäten definiert: 

1. [Mit Azure Storage verknüpfter Dienst](#azure-storage-linked-service)
2. [Mit Azure SQL verknüpfter Dienst](#azure-sql-database-linked-service)
3. [Azure-Blobdataset](#azure-blob-dataset)
4. [Azure SQL-Dataset](#azure-sql-dataset)
5. [Datenpipeline mit einer Kopieraktivität](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst

Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und Daten in dieses Speicherkonto hochgeladen. In diesem Abschnitt geben Sie Name und Schlüssel Ihres Azure Storage-Kontos an. Informationen zu JSON-Eigenschaften zum Definieren eines mit Azure Storage verknüpften Diensts finden Sie unter [Mit Azure Storage verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-linked-service). 

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

Die Verbindungszeichenfolge (connectionString) enthält die Parameter „storageAccountName“ und „storageAccountKey“. Die Werte für diese Parameter werden mithilfe einer Konfigurationsdatei übergeben. In der Definition werden außerdem die in der Vorlage definierten Variablen „azureStorageLinkedService“ und „dataFactoryName“ verwendet. 

#### <a name="azure-sql-database-linked-service"></a>Mit Azure SQL-Datenbank verknüpfter Dienst

„AzureSqlLinkedService“ verknüpft Ihre Datenbank in Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) die Tabelle „emp“ in dieser Datenbank erstellt. In diesem Abschnitt geben Sie den Namen des logischen SQL Servers, den Datenbanknamen, den Benutzernamen und das Benutzerkennwort an. Informationen zu JSON-Eigenschaften zum Definieren eines mit Azure SQL verknüpften Diensts finden Sie unter [Mit Azure SQL verknüpfter Dienst](data-factory-azure-sql-connector.md#linked-service-properties).  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
      "type": "AzureSqlDatabase",
      "description": "Azure SQL linked service",
      "typeProperties": {
        "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
      }
    }
}
```

Die Verbindungszeichenfolge (connectionString) enthält die Parameter „sqlServerName“, „databaseName“, „sqlServerUserName“ und „sqlServerPassword“, deren Werte mithilfe einer Konfigurationsdatei übergeben werden. In der Definition werden außerdem folgende Variablen aus der Vorlage verwendet: „azureSqlLinkedServiceName“ und „dataFactoryName“.

#### <a name="azure-blob-dataset"></a>Azure-Blobdataset
Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. In der Definition des Azure-Blobdatasets geben Sie die Namen des Blobcontainers, des Ordners und der Datei mit den Eingabedaten an. Informationen zu JSON-Eigenschaften zum Definieren eines Azure-Blobdatasets finden Sie unter [Eigenschaften des Dataset-Typs „Azure-Blob“](data-factory-azure-blob-connector.md#dataset-properties). 

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
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Azure SQL-Dataset
In diesem Abschnitt geben Sie den Namen der Tabelle in Azure SQL-Datenbank an, die die kopierten Daten aus Azure Blob Storage enthält. Informationen zu JSON-Eigenschaften zum Definieren eines Azure SQL-Datasets finden Sie unter [Eigenschaften des Dataset-Typs „Azure SQL“](data-factory-azure-sql-connector.md#dataset-properties). 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Datenpipeline
In diesem Abschnitt definieren Sie eine Pipeline, die Daten aus dem Azure-Blobdataset in das Azure SQL-Dataset kopiert. Informationen zu JSON-Elementen zum Definieren einer Pipeline für dieses Beispiel finden Sie unter [Pipeline-JSON](data-factory-create-pipelines.md#pipeline-json). 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>Wiederverwenden der Vorlage
In diesem Tutorial haben Sie eine Vorlage zum Definieren von Data Factory-Entitäten und eine Vorlage zum Übergeben von Parameterwerten erstellt. Die Pipeline kopiert Daten aus einem Azure Storage-Konto in Azure SQL-Datenbank (angegeben mithilfe von Parametern). Mit der gleichen Vorlage können Sie Data Factory-Entitäten in unterschiedlichen Umgebungen bereitstellen. Hierzu müssen Sie lediglich eine Parameterdatei für die jeweilige Umgebung erstellen und beim Bereitstellen in dieser Umgebung verwenden.     

Beispiel:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Der erste Befehl verwendet eine Parameterdatei für die Entwicklungsumgebung, der zweite eine Parameterdatei für die Testumgebung und der dritte eine Parameterdatei für die Produktion.  

Mit der Vorlage können auch wiederholte Aufgaben durchgeführt werden. Angenommen, Sie müssen zahlreiche Data Factorys mit einer Pipeline oder mehreren Pipelines erstellen, die die gleiche Logik implementieren, aber die einzelnen Data Factorys verwenden jeweils andere Storage- und SQL-Datenbank-Konten. In diesem Szenario können Sie zur Erstellung der Data Factorys die gleiche Vorlage in der gleichen Umgebung (Entwicklungs-, Test- oder Produktionsumgebung) mit unterschiedlichen Parameterdateien verwenden.   

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zum Kopieren von Daten in einen bzw. aus einem Datenspeicher zu erhalten, klicken Sie in der Tabelle auf den Link für den Datenspeicher.
