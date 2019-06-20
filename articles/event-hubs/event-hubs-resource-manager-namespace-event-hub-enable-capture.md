---
title: 'Erstellen eines Event Hubs, für den Capture aktiviert ist: Azure Event Hubs | Microsoft-Dokumentation'
description: Erstellen eines Azure Event Hubs-Namespace mit einem Event Hub und Aktivieren der von Capture über eine Azure Resource Manager-Vorlage
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/06/2019
ms.author: shvija
ms.openlocfilehash: 804ef04f0066a8b8dd9df82aef03fcfc32d7cb43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60343727"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>Erstellen eines Namespace mit Event Hub und Aktivieren von Capture mithilfe einer Vorlage

In diesem Artikel erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage verwenden, die einen [Event Hubs](event-hubs-what-is-event-hubs.md)-Namespace mit einer einzelnen Event Hub-Instanz erstellt und das [Capture-Feature](event-hubs-capture-overview.md) für die Event Hub-Instanz aktiviert. Der Artikel erklärt Ihnen, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen.

Außerdem erfahren Sie in diesem Artikel, wie Sie angeben, dass Ereignisse abhängig vom gewählten Ziel entweder in Azure Storage Blobs oder in einer Azure Data Lake Store-Instanz erfasst werden sollen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen][Authoring Azure Resource Manager templates]. Informationen zur JSON-Syntax und zu den Eigenschaften, die in einer Vorlage verwendet werden sollen, finden Sie unter [Microsoft.EventHub-Ressourcentypen](/azure/templates/microsoft.eventhub/allversions).

Weitere Informationen zu Mustern und Verfahren für Benennungskonventionen für Azure-Ressourcen finden Sie unter [Benennungskonventionen für Azure-Ressourcen][Azure Resources naming conventions].

Die vollständigen Vorlagen finden Sie unter den folgenden GitHub-Links:

- [Event Hub und Aktivierung der Erfassung in Storage (Vorlage)][Event Hub and enable Capture to Storage template] 
- [Event Hub und Aktivierung der Erfassung in Azure Data Lake Store (Vorlage)][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> Die neuesten Vorlagen finden Sie, indem Sie im Katalog [Azure-Schnellstartvorlagen][Azure Quickstart Templates] nach „Event Hubs“ suchen.
> 
> 

## <a name="what-will-you-deploy"></a>Was möchten Sie bereitstellen?

Mit dieser Vorlage können Sie einen Event Hubs-Namespace mit einem Event Hub bereitstellen und [Event Hubs Capture ](event-hubs-capture-overview.md) aktivieren. Mit Event Hubs Capture können Sie die Streamingdaten in Event Hubs automatisch innerhalb eines Zeit- oder Größenintervalls Ihrer Wahl an Azure Blob Storage oder Azure Data Lake Store übermitteln. Klicken Sie auf die folgende Schaltfläche, um Event Hubs Capture mit Azure Storage als Ziel zu aktivieren:

[![In Azure bereitstellen](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Klicken Sie auf die folgende Schaltfläche, um Event Hubs Capture mit Azure Data Lake Store als Ziel zu aktivieren:

[![In Azure bereitstellen](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Parameter

Mit Azure Resource Manager definieren Sie die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens `Parameters` , der alle Parameterwerte enthält. Definieren Sie einen Parameter für die Werte, die basierend auf dem bereitgestellten Projekt oder der bereitgestellten Umgebung variieren. Definieren Sie keine Parameter für Werte, die sich nicht ändern. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren.

Die Vorlage definiert die folgenden Parameter:

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Der Name des zu erstellenden Event Hubs-Namespace.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Der Name des im Event Hubs-Namespace erstellten Event Hubs.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Die Anzahl der Tage für die Beibehaltung von Nachrichten im Event Hub. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Die Anzahl der im Event Hub zu erstellenden Partitionen.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Aktiviert Capture für den Event Hub.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Das Codierungsformat, das Sie zum Serialisieren der Ereignisdaten angeben.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Das Zeitintervall, in dem Event Hubs Capture mit der Datenerfassung beginnt.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Das Größenintervall, in dem Capture mit der Datenerfassung beginnt.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Das Namensformat, das von Event Hubs Capture beim Schreiben der Avro-Dateien verwendet wird. Ein Capture-Namensformat muss die Felder `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` und `{Second}` umfassen. Diese können in beliebiger Reihenfolge sowie mit oder ohne Trennzeichen verwendet werden.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

Die API-Version der Vorlage.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Wenn Sie Azure Storage als Ziel wählen, verwenden Sie die folgenden Parameter:

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Capture benötigt eine Azure Storage- Kontoressourcen-ID, um die Erfassung in Ihrem gewünschten Storage-Konto zu aktivieren.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Der BLOB-Container, in dem die Ereignisdaten erfasst werden sollen.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Wenn Sie Azure Data Lake Store als Ziel wählen, verwenden Sie die folgenden Parameter: Sie müssen Berechtigungen für Ihren Data Lake Store-Pfad festlegen, an dem Sie das Ereignis erfassen möchten. Informationen zum Festlegen von Berechtigungen finden Sie in [diesem Artikel](event-hubs-capture-enable-through-portal.md#capture-data-to-an-azure-data-lake-store-account).

### <a name="subscriptionid"></a>subscriptionId

Abonnement-ID für den Event Hubs-Namespace und für Azure Data Lake Store. Beide Ressourcen müssen sich unter der gleichen Abonnement-ID befinden.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

Der Azure Data Lake Store-Name für die erfassten Ereignisse.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

Der Zielordnerpfad für die erfassten Ereignisse. In diesem Ordner in Ihrer Data Lake Store-Instanz werden die Ereignisse während des Erfassungsvorgangs abgelegt. Informationen zum Festlegen von Berechtigungen für diesen Ordner finden Sie unter [Verwenden von Azure Data Lake Store zum Erfassen von Daten von Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md).

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Bereitzustellende Ressourcen, wenn Azure Storage als Ziel für erfasste Ereignisse verwendet wird

Erstellt einen Namespace vom Typ **EventHub** mit einem einzelnen Event Hub und aktiviert Capture in Azure Blob Storage.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Bereitzustellende Ressourcen, wenn Azure Data Lake Store als Ziel verwendet wird

Erstellt einen Namespace vom Typ **EventHubs** mit einem einzelnen Event Hub und aktiviert Capture in Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> Sie können die Ausgabe leerer Dateien, wenn während des Erfassungszeitfensters keine Ereignisse auftreten, mithilfe der Eigenschaft **skipEmptyArchives** aktivieren oder deaktivieren. 

## <a name="commands-to-run-deployment"></a>Befehle zum Ausführen der Bereitstellung

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Stellen Sie Ihre Vorlage bereit, um Event Hubs Capture mit Azure Storage als Ziel zu aktivieren:
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Stellen Sie Ihre Vorlage bereit, um Event Hubs Capture mit Azure Data Lake Store als Ziel zu aktivieren:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Azure Blob Storage als Ziel:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store als Ziel:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Nächste Schritte

Sie können Event Hubs Capture auch über das [Azure-Portal](https://portal.azure.com) konfigurieren. Weitere Informationen finden Sie unter [Aktivieren von Event Hubs Capture über das Azure-Portal](event-hubs-capture-enable-through-portal.md).

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Links:

* [Übersicht über Event Hubs](event-hubs-what-is-event-hubs.md)
* [Erstellen eines Event Hubs](event-hubs-create.md)
* [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
