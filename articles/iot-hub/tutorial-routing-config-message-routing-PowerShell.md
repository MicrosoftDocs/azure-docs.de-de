---
title: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe von Azure PowerShell | Microsoft-Dokumentation
description: Konfigurieren des Nachrichtenroutings für Azure IoT Hub mithilfe von Azure PowerShell
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 51e9bc85c2ee843aa096674a25a1f634bd08b838
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162621"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Tutorial: Konfigurieren des IoT Hub-Nachrichtenroutings mithilfe von Azure PowerShell

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Herunterladen des Skripts (optional)

Im zweiten Teil dieses Tutorials laden Sie eine Visual Studio-Anwendung herunter und führen sie aus, um Nachrichten an IoT Hub zu senden. Der Download beinhaltet einen Ordner, der die Azure Resource Manager-Vorlage und die Parameterdatei sowie die Azure CLI- und PowerShell-Skripts enthält. 

Wenn Sie sich das fertige Skript ansehen möchten, laden Sie die [Azure IoT-Beispiele für C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) herunter. Entzippen Sie die Datei „master.zip“. Das Azure CLI-Skript (**iothub_routing_psh.ps1**) finden Sie unter „/iot-hub/Tutorials/Routing/SimulatedDevice/resources/“.

## <a name="create-your-resources"></a>Erstellen Ihrer Ressourcen

Erstellen Sie zunächst die Ressourcen mithilfe von PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Erstellen der grundlegenden Ressourcen mithilfe von PowerShell

Einige Ressourcennamen müssen global eindeutig sein. Hierzu zählen beispielsweise der IoT Hub-Name und der Name des Speicherkontos. Zur Vereinfachung wird an diese Ressourcennamen ein alphanumerischer Zufallswert namens *randomValue* angefügt. Der Zufallswert wird einmalig zu Beginn des Skripts generiert und innerhalb des gesamten Skripts nach Bedarf an die Ressourcennamen angefügt. Falls Sie keinen Zufallswert verwenden möchten, können Sie den Wert auf eine leere Zeichenfolge oder auf einen bestimmten Wert festlegen. 

> [!IMPORTANT]
> Die im Ausgangsskript festgelegten Variablen werden auch vom Routingskript verwendet. Führen Sie daher das gesamte Skript in der gleichen Cloud Shell-Sitzung aus. Wenn Sie eine neue Sitzung öffnen, um das Skript zum Einrichten des Routings auszuführen, verfügen einige der Variablen über keine Werte. 
>

Kopieren Sie das folgende Skript, fügen Sie es in Cloud Shell ein, und drücken Sie die EINGABETASTE. Daraufhin wird das Skript Zeile für Zeile ausgeführt. Der erste Abschnitt des Skripts erstellt die grundlegenden Ressourcen für dieses Tutorial (einschließlich Speicherkonto, IoT Hub, Service Bus-Namespace und Service Bus-Warteschlange). Kopieren Sie im weiteren Verlauf des Tutorials jeweils die einzelnen Skriptblöcke, und fügen Sie sie zur Ausführung in Cloud Shell ein.

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Erstellen Sie ein simuliertes Gerät.

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nachdem die grundlegenden Ressourcen eingerichtet wurden, können Sie das Nachrichtenrouting konfigurieren.

## <a name="set-up-message-routing"></a>Einrichten der Nachrichtenweiterleitung

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Verwenden Sie [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint), um einen Routingendpunkt zu erstellen. Verwenden Sie [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute), um die Messagingroute für den Endpunkt zu erstellen.

### <a name="route-to-a-storage-account"></a>Weiterleiten an ein Speicherkonto 

Richten Sie zuerst den Endpunkt für das Speicherkonto ein, und erstellen Sie dann die Nachrichtenroute.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Folgende Variablen werden festgelegt:

**resourceGroup**: Legen Sie beide Vorkommen dieses Felds auf Ihre Ressourcengruppe fest.

**name:** Der Name der IoT Hub-Instanz, für die das Routing gilt.

**endpointName**: Der Name zur Identifizierung des Endpunkts. 

**endpointType**: Der Endpunkttyp. Dieser Wert muss auf `azurestoragecontainer`, `eventhub`, `servicebusqueue` oder `servicebustopic` festgelegt werden. Legen Sie ihn für dieses Tutorial auf `azurestoragecontainer` fest.

**subscriptionID**: Die Abonnement-ID für Ihr Azure-Konto.

**storageConnectionString**: Dieser Wert wird aus dem Speicherkonto abgerufen, das im vorherigen Skript eingerichtet wurde. Er wird beim Routing verwendet, um auf das Speicherkonto zuzugreifen.

**containerName**: Der Name des Containers im Speicherkonto, in den die Daten geschrieben werden.

**Encoding**: Legen Sie dieses Feld auf `AVRO` oder `JSON` fest. Dadurch wird das Format der gespeicherten Daten angegeben. Standardwert: AVRO.

**routeName**: Der Name der Route, die Sie einrichten. 

**condition**: Die Abfrage, mit der nach den Nachrichten gefiltert wird, die an diesen Endpunkt gesendet wurden. Die Abfragebedingung für die Nachrichten, die an den Speicher weitergeleitet werden, lautet `level="storage"`.

**enabled**: Dieses Feld ist standardmäßig auf `true` festgelegt. Das bedeutet, dass die Nachrichtenroute nach der Erstellung aktiviert werden soll.

Kopieren Sie das folgende Skript, und fügen Sie es in Ihr Cloud Shell-Fenster ein:

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Als Nächstes wird der Routingendpunkt für das Speicherkonto erstellt. Außerdem wird der Container angegeben, in dem die Ergebnisse gespeichert werden sollen. Der Container wurde zusammen mit dem Speicherkonto erstellt.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Erstellen Sie als Nächstes die Nachrichtenroute für den Speicherendpunkt. Die Nachrichtenroute legt fest, wohin die Nachrichten gesendet werden sollen, die der Abfragespezifikation entsprechen.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Weiterleiten an eine Service Bus-Warteschlange

Richten Sie jetzt die Weiterleitung für die Service Bus-Warteschlange ein. Um die Verbindungszeichenfolge für die Service Bus-Warteschlange abrufen zu können, müssen Sie eine Autorisierungsregel mit korrekten Berechtigungen erstellen. Das folgende Skript erstellt eine Autorisierungsregel für die Service Bus-Warteschlange namens `sbauthrule` und legt die Rechte auf `Listen Manage Send` fest. Nach Einrichtung dieser Autorisierungsregel können Sie damit die Verbindungszeichenfolge für die Warteschlange abrufen.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Verwenden Sie nun die Autorisierungsregel, um den Service Bus-Warteschlangenschlüssel abzurufen. Diese Autorisierungsregel wird weiter unten im Skript zum Abrufen der Verbindungszeichenfolge verwendet.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Richten Sie nun den Routingendpunkt und die Nachrichtenroute für die Service Bus-Warteschlange ein. Folgende Variablen werden festgelegt:

**endpointName**: Der Name zur Identifizierung des Endpunkts. 

**endpointType**: Der Endpunkttyp. Dieser Wert muss auf `azurestoragecontainer`, `eventhub`, `servicebusqueue` oder `servicebustopic` festgelegt werden. Legen Sie ihn für dieses Tutorial auf `servicebusqueue` fest.

**routeName**: Der Name der Route, die Sie einrichten. 

**condition**: Die Abfrage, mit der nach den Nachrichten gefiltert wird, die an diesen Endpunkt gesendet wurden. Die Abfragebedingung für die Nachrichten, die an die Service Bus-Warteschlange weitergeleitet werden, lautet `level="critical"`.

Hier sehen Sie das Azure PowerShell-Skript für das Nachrichtenrouting für die Service Bus-Warteschlange:

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Anzeigen des Nachrichtenroutings im Portal

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Ressourcen eingerichtet und die Nachrichtenrouten konfiguriert haben, können Sie sich nun im nächsten Tutorial darüber informieren, wie Sie Nachrichten an IoT Hub senden, die dann an die verschiedenen Ziele weitergeleitet werden. 

> [!div class="nextstepaction"]
> [Teil 2: Anzeigen der Ergebnisse des Nachrichtenroutings](tutorial-routing-view-message-routing-results.md)