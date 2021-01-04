---
title: Verwenden von Resource Manager-Vorlagen in Data Factory
description: Erfahren Sie, wie Sie Azure Resource Manager-Vorlagen erstellen und diese verwenden, um Data Factory-Entitäten zu erstellen.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4c840d50fb80a57e9c68645ade8baa22b455fa84
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495496"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Verwenden von Vorlagen zum Erstellen von Azure Data Factory-Entitäten
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. 

## <a name="overview"></a>Übersicht
Wenn Sie Azure Data Factory für die Integration Ihrer Daten verwenden, kann es vorkommen, dass Sie dasselbe Muster in verschiedenen Umgebungen verwenden oder dass Sie innerhalb einer Lösung wiederholt denselben Task implementieren. Diese Szenarios lassen sich mithilfe von Vorlagen einfach implementieren und verwalten. Vorlagen in Azure Data Factory sind ideal für Szenarios, bei denen Wiederverwendbarkeit und Wiederholungen möglich sind.

Nehmen wir an, ein Unternehmen verfügt weltweit über 10 Produktionsanlagen. Die Protokolle jeder einzelnen Anlage werden in einer separaten SQL Server-Datenbank gespeichert. Das Unternehmen möchte in der Cloud ein einzelnes Data Warehouse für Ad-hoc-Analysen erstellen. Außerdem sollen in Entwicklungs-, Test- und Produktionsumgebungen zwar dieselbe Logik, aber unterschiedliche Konfigurationen verwendet werden.

In diesem Fall muss eine Aufgabe zwar in derselben Umgebung, jedoch mit unterschiedlichen Werten für jede der 10 Data Factorys wiederholt werden. Es findet eine **Wiederholung** statt. Die Verwendung von Vorlagen ermöglicht die Abstraktion dieses generischen Ablaufs (= in den Pipelines aller Data Factorys finden dieselben Aktivitäten statt). Jedoch wird für jede Produktionsanlage eine eigene Parameterdatei verwendet.

Das Unternehmen möchte diese 10 Data Factorys darüber hinaus mehrfach in verschiedenen Umgebungen bereitstellen. Mithilfe von Vorlagen kann es sich die **Wiederverwendbarkeit** zunutze machen und verschiedene Parameterdateien für Entwicklungs-, Test- und Produktionsumgebungen verwenden.

## <a name="templating-with-azure-resource-manager"></a>Verwenden von Vorlagen in Azure Resource Manager
[Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/overview.md) stellen eine hervorragende Möglichkeit dar, in Azure Data Factory mit Vorlagen zu arbeiten. Resource Manager-Vorlagen definieren die Infrastruktur und Konfiguration Ihrer Azure-Lösung über eine JSON-Datei. Da Azure Resource Manager-Vorlagen für (beinahe) alle Azure-Dienste zur Verfügung stehen, können Sie in vielen Bereichen genutzt werden, um alle Ressourcen Ihrer Azure-Assets zu verwalten. Weitere (allgemeinere) Informationen über Resource Manager-Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md).

## <a name="tutorials"></a>Tutorials
Schritt-für-Schritt-Anleitungen zum Erstellen von Data Factory-Entitäten mithilfe von Resource Manager-Vorlagen finden Sie in den folgenden Tutorials:

* [Tutorial: Create a pipeline to copy data by using Azure Resource Manager template](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md) (Tutorial: Erstellen einer Pipeline zum Kopieren von Daten mithilfe einer Azure Resource Manager-Vorlage)
* [Tutorial: Create a pipeline to process data by using Azure Resource Manager template](data-factory-build-your-first-pipeline.md) (Tutorial: Erstellen einer Pipeline zum Verarbeiten von Daten mithilfe einer Azure Resource Manager-Vorlage)

## <a name="data-factory-templates-on-github"></a>Data Factory-Vorlagen auf GitHub
Sehen Sie sich die folgenden Azure-Schnellstartvorlagen auf GitHub an:

* [Create a Data factory to copy data from Azure Blob Storage to Azure SQL Database (Erstellen einer Data Factory für das Kopieren von Daten von Azure Blob Storage zu Azure SQL-Datenbank)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Create a Data factory with Hive activity on Azure HDInsight cluster (Erstellen einer Data Factory mit Hive-Aktivität auf einem Azure HDInsight-Cluster)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Create a Data factory to copy data from Salesforce to Azure Blobs (englischsprachiger Artikel über das Erstellen einer Data Factory für das Kopieren von Daten von Salesforce zu Azure-Blobs)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Create a Data factory that chains activities (Erstellen einer Data Factory, die Aktivitäten verkettet): Kopiert Daten von einem FTP-Server in Azure-Blobs, ruft ein Hive-Skript für einen bedarfsgesteuerten HDInsight-Cluster zum Transformieren der Daten auf, und kopiert das Ergebnis in Azure SQL-Datenbank.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Sie können Ihre Azure Data Factory-Vorlagen auch im [Verzeichnis der Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) freigeben. Bitten lesen Sie sich während der Entwicklung von Vorlagen, die über dieses Repository freigegeben werden sollen, den Abschnitt [contribution guide (Richtlinien zur Freigabe)](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) durch.

Der nachfolgende Abschnitt enthält detaillierte Informationen zum Definieren der Data Factory-Ressourcen in einer Resource Manager-Vorlage.

## <a name="defining-data-factory-resources-in-templates"></a>Definieren von Data Factory-Ressourcen in Vorlagen
Das Grundgerüst einer Vorlage zum Definieren einer Data Factory sieht folgendermaßen aus:

```JSON
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
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definieren einer Data Factory
Das folgende Beispiel zeigt, wie Sie in der Resource Manager-Vorlage eine Data Factory definieren:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
„dataFactoryName“ wird unter „variables“ wie folgt definiert:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definieren verknüpfter Dienste

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Ausführliche Informationen zu den JSON-Eigenschaften eines spezifischen verknüpften Dienstes, der bereitgestellt werden soll, finden Sie unter [Mit Azure Storage verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-linked-service) oder unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Der Parameter „dependsOn“ gibt den Namen der entsprechenden Data Factory an. In der nachstehenden JSON-Definition finden Sie ein Beispiel für das Definieren eines mit Azure Storage verknüpften Diensts:

### <a name="define-datasets"></a>Definieren von Datasets

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Ausführliche Informationen zu den JSON-Eigenschaften eines spezifischen Datasettyps, der bereitgestellt werden soll, finden Sie unter [Unterstützte Datenspeicher und Formate](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Bitte beachten Sie, dass der Parameter „dependsOn“ den Namen der entsprechenden Data Factory und des mit Storage verknüpften Diensts angibt. Die nachstehende JSON-Definition enthält ein Beispiel für das Definieren des Datensatztyps „Azure Blob Storage“:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definieren von Pipelines

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Ausführliche Informationen zu den JSON-Eigenschaften für das Definieren einer spezifischen Pipeline und spezifischer Aktivitäten, die bereitgestellt werden sollen, finden Sie unter [Pipeline-JSON](data-factory-create-pipelines.md#pipeline-json). Bitte beachten Sie, dass der Parameter „dependsOn“ den Namen der Data Factory und aller dazugehörigen, verknüpften Dienste oder Datasets angibt. Der folgende JSON-Ausschnitt zeigt ein Beispiel für eine Pipeline, die Daten von Azure Blob Storage zu Azure SQL-Datenbank kopiert:

```JSON
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
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parametrisieren einer Data Factory-Vorlage
Informationen zu bewährten Vorgehensweisen bei der Parametrisierung finden Sie unter [Bewährte Methoden für das Erstellen von Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-best-practices.md). Im Allgemeinen sollten so wenig Parameter wie möglich verwendet werden. Dies gilt insbesondere dann, wen stattdessen Variablen verwendet werden können. Verwenden Sie Parameter nur für folgende Szenarios:

* Einstellungen variieren nach Umgebung (Beispiel: Entwicklung, Test und Produktion)
* Geheime Schlüssel (z.B. Kennwörter)

Möchten Sie beim Bereitstellen von Azure Data Factory-Entitäten mithilfe von Pull Geheimnisse aus [Azure Key Vault](../../key-vault/general/overview.md) übertragen, müssen Sie **keyVault** und **secretName** wie im folgenden Beispiel gezeigt angeben:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Bisher ist es nicht möglich, Vorlagen für bestehende Data Factorys zu exportieren. Diese Funktion befindet sich jedoch in Entwicklung.
>
>