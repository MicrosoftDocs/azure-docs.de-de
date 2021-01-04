---
title: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe einer Azure Resource Manager-Vorlage
description: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe einer Azure Resource Manager-Vorlage
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: e5ae5948c8baf1573393c73026c84d0f62e8693e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92480104"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Tutorial: Konfigurieren des IoT Hub-Nachrichtenroutings mithilfe einer Azure Resource Manager-Vorlage

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Nachrichtenweiterleitung

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Herunterladen der Vorlage und der Parameterdatei

Im zweiten Teil dieses Tutorials laden Sie eine Visual Studio-Anwendung herunter und führen sie aus, um Nachrichten an IoT Hub zu senden. Der Download beinhaltet einen Ordner, der die Azure Resource Manager-Vorlage und die Parameterdatei sowie die Azure CLI- und PowerShell-Skripts enthält.

Laden Sie die [Azure IoT-Beispiele für C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter. Entzippen Sie die Datei „master.zip“. Die Resource Manager-Vorlage ( **template_iothub.json** ) und die Parameterdatei ( **template_iothub_parameters.json** ) befinden sich unter „/iot-hub/Tutorials/Routing/SimulatedDevice/resources/“.

## <a name="create-your-resources"></a>Erstellen Ihrer Ressourcen

Für die Ressourcenerstellung wird eine ARM-Vorlage (Azure Resource Manager) verwendet. Bei den Azure CLI- und PowerShell-Skripts können nacheinander einzelne Zeilenblöcke ausgeführt werden. Eine RM-Vorlage wird in einem einzelnen Schritt bereitgestellt. Zum besseren Verständnis werden die Abschnitte in diesem Artikel separat erläutert. Anschließend erfahren Sie, wie Sie die Vorlage bereitstellen und das virtuelle Gerät zu Testzwecken erstellen. Nachdem Sie die Vorlage bereitgestellt haben, können Sie die Nachrichtenroutingkonfiguration im Portal anzeigen.

Einige Ressourcennamen müssen global eindeutig sein. Hierzu zählen beispielsweise der IoT Hub-Name und der Name des Speicherkontos. Diese Ressourcennamen werden der Einfachheit halber mit einem alphanumerischen Zufallswert versehen, der auf dem aktuellen Datum und der aktuellen Uhrzeit basiert. 

In der Vorlage sehen Sie, wo Variablen für diese Ressourcen mit einer Kombination aus übergebenem Parameter und *randomValue* eingerichtet sind. 

Die verwendeten Parameter werden im folgenden Abschnitt erläutert:

### <a name="parameters"></a>Parameter

Die meisten dieser Parameter haben Standardwerte. Parameter mit der Endung **_in** werden mit *randomValue* verkettet, um sie global eindeutig zu machen. 

**randomValue** : Dieser Wert wird auf der Grundlage des aktuellen Datums und der aktuellen Uhrzeit generiert, wenn Sie die Vorlage bereitstellen. Dieses Feld wird in der Vorlage selbst erstellt und ist daher nicht in der Parameterdatei enthalten.

**subscriptionId** : Das Abonnement, in dem die Vorlage bereitgestellt wird. Dieses Feld wird automatisch festgelegt und ist daher nicht in der Parameterdatei enthalten.

**IoTHubName_in** : Der IoT Hub-Basisname. Er wird mit dem Zufallswert verkettet, um global eindeutig zu sein.

**location** : Die Azure-Zielregion für die Bereitstellung (beispielsweise „USA, Westen“).

**consumer_group** : Die Consumergruppe für Nachrichten, die den Routingendpunkt durchlaufen. Sie dient zum Filtern von Ergebnissen in Azure Stream Analytics. Ein Beispiel: Es gibt den gesamten Datenstrom, über den Sie sämtliche Daten erhalten. Für übermittelte Daten, bei denen die Consumergruppe auf **Contoso** festgelegt ist, können Sie einen Azure Stream Analytics-Datenstrom (und einen Power BI-Bericht) einrichten, um nur diese Einträge anzuzeigen. Dieses Feld wird im zweiten Teil des Tutorials verwendet.

**sku_name** : Die Skalierung für die IoT Hub-Instanz. Dieser Wert muss mindestens auf „S1“ festgelegt werden. Der Free-Tarif ist für dieses Tutorial nicht geeignet, da in diesem Tarif nicht mehrere Endpunkte genutzt werden können.

**sku_units** : Dieses Feld gehört zu **sku_name** und dient zum Angeben der Anzahl verwendbarer IoT Hub-Einheiten.

**d2c_partitions** : Die Anzahl verwendeter Partitionen für den Ereignisdatenstrom.

**storageAccountName_in** : Der Name des zu erstellenden Speicherkontos. Nachrichten werden an einen Container im Speicherkonto weitergeleitet. Dieses Feld wird mit dem Zufallswert verkettet, um es global eindeutig zu machen.

**storageContainerName** : Der Name des Containers, in dem die an das Speicherkonto weitergeleiteten Nachrichten gespeichert werden.

**storage_endpoint** : Der Name des Speicherkontoendpunkts für das Nachrichtenrouting.

**service_bus_namespace_in** : Der Name des zu erstellenden Service Bus-Namespace. Dieser Wert wird mit dem Zufallswert verkettet, um ihn global eindeutig zu machen.

**service_bus_queue_in** : Der Name der Service Bus-Warteschlange für das Nachrichtenrouting. Dieser Wert wird mit dem Zufallswert verkettet, um ihn global eindeutig zu machen.

**AuthRules_sb_queue** : Die Autorisierungsregeln für die Service Bus-Warteschlange, die zum Abrufen der Verbindungszeichenfolge für die Warteschlange verwendet werden.

### <a name="variables"></a>Variables

In der Vorlage werden folgende Werte verwendet. Die meisten davon sind von Parametern abgeleitet.

**queueAuthorizationRuleResourceId** : Die Ressourcen-ID der Autorisierungsregel für die Service Bus-Warteschlange. Die Ressourcen-ID wird wiederum zum Abrufen der Verbindungszeichenfolge für die Warteschlange verwendet.

**iotHubName** : Der Name der IoT Hub-Instanz nach Verkettung mit dem Zufallswert. 

**storageAccountName** : Der Name des Speicherkontos nach Verkettung mit dem Zufallswert. 

**service_bus_namespace** : Der Namespace nach Verkettung mit dem Zufallswert.

**service_bus_queue** : Der Name der Service Bus-Warteschlange nach Verkettung mit dem Zufallswert.

**sbVersion** : Die zu verwendende Version der Service Bus-API. In diesem Fall: 2017-04-01.

### <a name="resources-storage-account-and-container"></a>Ressourcen: Speicherkonto und Container

Als Erstes werden das Speicherkonto und der Container erstellt, an den Nachrichten weitergeleitet werden. Der Container ist eine Ressource unter dem Speicherkonto. Er verfügt über eine `dependsOn`-Klausel für das Speicherkonto, sodass das Speicherkonto vor dem Container erstellt werden muss.

Der entsprechende Abschnitt sieht wie folgt aus:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Ressourcen: Service Bus-Namespace und -Warteschlange

Als Nächstes werden der Service Bus-Namespace und die Service Bus-Warteschlange erstellt, an die Nachrichten weitergeleitet werden. Die SKU wird auf „Standard“ festgelegt. Die API-Version wird aus den Variablen abgerufen. Außerdem wird festgelegt, dass der Service Bus-Namespace aktiviert werden soll, wenn dieser Abschnitt bereitgestellt wird ("status": "Active"). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

Der folgende Abschnitt dient zum Erstellen der Service Bus-Warteschlange. Dieser Teil des Skripts enthält eine `dependsOn`-Klausel, um sicherzustellen, dass der Namespace vor der Warteschlange erstellt wird.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Ressourcen: IoT Hub und Nachrichtenrouting

Nach Erstellung des Speicherkontos und der Service Bus-Warteschlange wird die IoT Hub-Instanz für das Nachrichtenrouting erstellt. Mithilfe von `dependsOn`-Klauseln in der Ressource Manager-Vorlage wird verhindert, dass der Hub erstellt wird, bevor die Service Bus-Ressourcen und das Speicherkonto erstellt wurden. 

Hier sehen Sie den ersten Teil des IoT Hub-Abschnitts. Dieser Teil der Vorlage dient zum Einrichten der Abhängigkeiten und beginnt mit den Eigenschaften.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Der nächste Abschnitt enthält die Konfiguration des Nachrichtenroutings für die Iot Hub-Instanz. Er beginnt mit dem Abschnitt für die Endpunkte. In diesem Teil der Vorlage werden die Routingendpunkte für die Service Bus-Warteschlange und das Speicherkonto eingerichtet (einschließlich der Verbindungszeichenfolgen).

Zur Erstellung der Verbindungszeichenfolge für die Warteschlange benötigen Sie den inline abgerufenen QueueAuthorizationRulesResourcedId-Wert. Zur Erstellung der Verbindungszeichenfolge für das Speicherkonto müssen Sie den primären Speicherschlüssel abrufen und im Format für die Verbindungszeichenfolge verwenden.

Im Rahmen der Endpunktkonfiguration wird auch das Blobformat auf `AVRO` oder `JSON` festgelegt.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Der nächste Abschnitt dient zum Einrichten der Nachrichtenrouten zu den Endpunkten. Pro Endpunkt wird jeweils eine Route eingerichtet. Es gibt also eine Route für die Service Bus-Warteschlange und eine für den Speicherkontocontainer.

Zur Erinnerung: Die Abfragebedingung für an den Speicher weitergeleitete Nachrichten lautet `level="storage"`; für an die Service Bus-Warteschlange weitergeleitete Nachrichten lautet die Abfragebedingung `level="critical"`.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Der folgende JSON-Code zeigt den Rest des IoT Hub-Abschnitts mit Standardinformationen und der SKU für den Hub:

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Ressourcen: Autorisierungsregeln für die Service Bus-Warteschlange

Die Autorisierungsregel für die Service Bus-Warteschlange dient zum Abrufen der Verbindungszeichenfolge für die Service Bus-Warteschlange. Sie enthält eine `dependsOn`-Klausel, die sicherstellt, dass sie nicht vor dem Service Bus-Namespace und der Service Bus-Warteschlange erstellt wird.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Ressourcen: Consumergruppe

In diesem Abschnitt erstellen Sie eine Consumergruppe für die IoT Hub-Daten, die von Azure Stream Analytics im zweiten Teil dieses Tutorials verwendet werden.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Ressourcen: Ausgaben

Im Ausgabeabschnitt können Sie bei Bedarf einen Wert an das Bereitstellungsskript zurücksenden, um ihn anzuzeigen. Dieser Teil der Vorlage gibt die Verbindungszeichenfolge für die Service Bus-Warteschlange zurück. Die Rückgabe eines Werts ist nicht zwingend erforderlich und veranschaulicht hier lediglich anhand eines Beispiels, wie Ergebnisse an das aufrufende Skript zurückgegeben werden.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Bereitstellen der Resource Manager-Vorlage

Laden Sie die Vorlage und die Parameterdatei in Azure Cloud Shell hoch, und führen Sie dann ein Skript aus, um die Vorlage in Azure bereitzustellen. Öffnen Sie Azure Cloud Shell, und melden Sie sich an. In diesem Beispiel wird PowerShell verwendet.

Wählen Sie zum Hochladen der Dateien auf der Menüleiste das Symbol **Dateien hochladen/herunterladen** und anschließend die Option „Hochladen“ aus.

![Der Screenshot hebt das Symbol zum Hochladen/Herunterladen von Dateien hervor.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Suchen Sie im daraufhin angezeigten Datei-Explorer auf Ihrem lokalen Datenträger nach den Dateien, und wählen Sie sie aus. Wählen Sie anschließend **Öffnen** aus.

Nach Abschluss des Dateiuploads wird ein Dialogfeld angezeigt, das in etwa wie folgt aussieht:

![Cloud Shell-Menüleiste mit hervorgehobener Option zum Hoch-/Herunterladen von Dateien](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Die Dateien hochgeladen werden auf die Freigabe, die von Ihrer Cloud Shell-Instanz verwendet. 

Führen Sie das Skript aus, um die Bereitstellung durchzuführen. Die letzte Zeile dieses Skripts ruft die zurückzugebende Variable ab (die Verbindungszeichenfolge für die Service Bus-Warteschlange).

Vom Skript werden die folgenden Variablen festgelegt und verwendet:

**$RGName** : Der Name der Ressourcengruppe, in der die Vorlage bereitgestellt werden soll. Dieses Feld wird erstellt, bevor Sie die Vorlage bereitstellen.

**$location** : Der gewünschte Azure-Standort für die Vorlage (etwa „USA, Westen“).

**$deploymentname** : Ein Name, den Sie der Bereitstellung zuweisen, um den zurückgegebenen Variablenwert abzurufen.

Im Anschluss finden Sie das PowerShell. Kopieren Sie dieses PowerShell-Skript, fügen Sie es in das Cloud Shell-Fenster ein, und drücken Sie die EINGABETASTE, um es auszuführen.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Im Falle eines Skriptfehlers können Sie das Skript lokal bearbeiten, erneut in die Cloud Shell hochladen und erneut ausführen. Fahren Sie nach erfolgreicher Skriptausführung mit dem nächsten Schritt fort.

## <a name="create-simulated-device"></a>Erstellen eines simulierten Geräts

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Anzeigen des Nachrichtenroutings im Portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun die Ressourceneinrichtung abgeschlossen und die Nachrichtenrouten konfiguriert haben, erfahren Sie im nächsten Tutorial, wie Sie die Informationen zu den weitergeleiteten Nachrichten verarbeiten und anzeigen.

> [!div class="nextstepaction"]
> [Teil 2: Anzeigen der Ergebnisse des Nachrichtenroutings](tutorial-routing-view-message-routing-results.md)
