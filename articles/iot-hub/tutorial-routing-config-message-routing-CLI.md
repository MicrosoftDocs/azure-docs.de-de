---
title: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe der Azure-Befehlszeilenschnittstelle
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6faa585f1ad38eb981e0bbffffef603c4aab0bc8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66163401"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Tutorial: Konfigurieren des IoT Hub-Nachrichtenroutings mithilfe der Azure-Befehlszeilenschnittstelle

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Herunterladen des Skripts (optional)

Im zweiten Teil dieses Tutorials laden Sie eine Visual Studio-Anwendung herunter und führen sie aus, um Nachrichten an IoT Hub zu senden. Der Download beinhaltet einen Ordner, der die Azure Resource Manager-Vorlage und die Parameterdatei sowie die Azure CLI- und PowerShell-Skripts enthält.

Wenn Sie sich das fertige Skript ansehen möchten, laden Sie die [Azure IoT-Beispiele für C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter. Entzippen Sie die Datei „master.zip“. Das Azure CLI-Skript (**iothub_routing_cli.azcli**) finden Sie unter „/iot-hub/Tutorials/Routing/SimulatedDevice/resources/“.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Erstellen Ihrer Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle

Einige Ressourcennamen müssen global eindeutig sein. Hierzu zählen beispielsweise der IoT Hub-Name und der Name des Speicherkontos. Zur Vereinfachung wird an diese Ressourcennamen ein alphanumerischer Zufallswert namens *randomValue* angefügt. Der Zufallswert wird einmalig zu Beginn des Skripts generiert und innerhalb des gesamten Skripts nach Bedarf an die Ressourcennamen angefügt. Falls Sie keinen Zufallswert verwenden möchten, können Sie den Wert auf eine leere Zeichenfolge oder auf einen bestimmten Wert festlegen. 

> [!IMPORTANT]
> Die im Ausgangsskript festgelegten Variablen werden auch vom Routingskript verwendet. Führen Sie daher das gesamte Skript in der gleichen Cloud Shell-Sitzung aus. Wenn Sie eine neue Sitzung öffnen, um das Skript zum Einrichten des Routings auszuführen, verfügen einige der Variablen über keine Werte.
>

Kopieren Sie das folgende Skript, fügen Sie es in Cloud Shell ein, und drücken Sie die EINGABETASTE. Daraufhin wird das Skript Zeile für Zeile ausgeführt. Der erste Abschnitt des Skripts erstellt die grundlegenden Ressourcen für dieses Tutorial (einschließlich Speicherkonto, IoT Hub, Service Bus-Namespace und Service Bus-Warteschlange). Kopieren Sie im weiteren Verlauf des Tutorials jeweils die einzelnen Skriptblöcke, und fügen Sie sie zur Ausführung in Cloud Shell ein.

> [!TIP]
> Ein Tipp zum Debuggen: Dieses Skript verwendet das Fortsetzungssymbol (umgekehrter Schrägstrich: `\`), um die Lesbarkeit des Skripts zu verbessern. Sollte beim Ausführen des Skripts ein Problem auftreten, vergewissern Sie sich, dass sich nach den umgekehrten Schrägstrichen keine Leerzeichen befinden.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Nachdem die grundlegenden Ressourcen eingerichtet wurden, können Sie das Nachrichtenrouting konfigurieren.

## <a name="set-up-message-routing"></a>Einrichten der Nachrichtenweiterleitung

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Verwenden Sie [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create), um einen Routingendpunkt zu erstellen. Verwenden Sie [az iot hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create), um die Nachrichtenroute für den Endpunkt zu erstellen.

### <a name="route-to-a-storage-account"></a>Weiterleiten an ein Speicherkonto

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Richten Sie zuerst den Endpunkt für das Speicherkonto und anschließend die Route ein. 

Folgende Variablen werden festgelegt:

**storageConnectionString**: Dieser Wert wird aus dem Speicherkonto abgerufen, das im vorherigen Skript eingerichtet wurde. Er wird beim Nachrichtenrouting verwendet, um auf das Speicherkonto zuzugreifen.

  **resourceGroup**: Legen Sie beide Vorkommen von „resourceGroup“ auf Ihre Ressourcengruppe fest.

**endpoint subscriptionID**: Die Azure-Abonnement-ID für den Endpunkt. 

**endpointType**: Der Endpunkttyp. Dieser Wert muss auf `azurestoragecontainer`, `eventhub`, `servicebusqueue` oder `servicebustopic` festgelegt werden. Legen Sie ihn für dieses Tutorial auf `azurestoragecontainer` fest.

**iotHubName**: Der Name des Hubs, der das Routing abwickelt.

**containerName**: Der Name des Containers im Speicherkonto, in den die Daten geschrieben werden.

**encoding**: `avro` oder `json`. Das Format der gespeicherten Daten.

**routeName**: Der Name der Route, die Sie einrichten. 

**endpointName**: Der Name zur Identifizierung des Endpunkts. 

**enabled**: Dieses Feld ist standardmäßig auf `true` festgelegt. Das bedeutet, dass die Nachrichtenroute nach der Erstellung aktiviert werden soll.

**condition**: Die Abfrage, mit der nach den Nachrichten gefiltert wird, die an diesen Endpunkt gesendet wurden. Die Abfragebedingung für die Nachrichten, die an den Speicher weitergeleitet werden, lautet `level="storage"`.

Kopieren Sie das folgende Skript, fügen Sie es in Ihr Cloud Shell-Fenster ein, und führen Sie es aus:

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Als Nächstes wird der Routingendpunkt für das Speicherkonto erstellt. Außerdem wird der Container angegeben, in dem die Ergebnisse gespeichert werden sollen. Der Container wurde zuvor gemeinsam mit dem Speicherkonto erstellt.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Erstellen Sie als Nächstes die Route für den Speicherendpunkt. Die Nachrichtenroute legt fest, wohin die Nachrichten gesendet werden sollen, die der Abfragespezifikation entsprechen. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Weiterleiten an eine Service Bus-Warteschlange

Richten Sie jetzt die Weiterleitung für die Service Bus-Warteschlange ein. Um die Verbindungszeichenfolge für die Service Bus-Warteschlange abrufen zu können, müssen Sie eine Autorisierungsregel mit korrekten Berechtigungen erstellen. Das folgende Skript erstellt eine Autorisierungsregel für die Service Bus-Warteschlange namens `sbauthrule` und legt die Rechte auf `Listen Manage Send` fest. Nachdem Sie diese Autorisierungsregel definiert haben, können Sie damit die Verbindungszeichenfolge für die Warteschlange abrufen.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Verwenden Sie nun die Autorisierungsregel, um die Verbindungszeichenfolge für die Service Bus-Warteschlange abzurufen.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Richten Sie nun den Routingendpunkt und die Nachrichtenroute für die Service Bus-Warteschlange ein. Folgende Variablen werden festgelegt:

**endpointName**: Der Name zur Identifizierung des Endpunkts. 

**endpointType**: Der Endpunkttyp. Dieser Wert muss auf `azurestoragecontainer`, `eventhub`, `servicebusqueue` oder `servicebustopic` festgelegt werden. Legen Sie ihn für dieses Tutorial auf `servicebusqueue` fest.

**routeName**: Der Name der Route, die Sie einrichten. 

**condition**: Die Abfrage, mit der nach den Nachrichten gefiltert wird, die an diesen Endpunkt gesendet wurden. Die Abfragebedingung für die Nachrichten, die an die Service Bus-Warteschlange weitergeleitet werden, lautet `level="critical"`.

Hier sehen Sie das Azure CLI-Skript für den Routingendpunkt und die Nachrichtenroute für die Service Bus-Warteschlange:

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Anzeigen des Nachrichtenroutings im Portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Ressourcen eingerichtet und die Nachrichtenrouten konfiguriert haben, können Sie sich nun im nächsten Tutorial darüber informieren, wie Sie Nachrichten an IoT Hub senden, die dann an die verschiedenen Ziele weitergeleitet werden. 

> [!div class="nextstepaction"]
> [Teil 2: Anzeigen der Ergebnisse des Nachrichtenroutings](tutorial-routing-view-message-routing-results.md)