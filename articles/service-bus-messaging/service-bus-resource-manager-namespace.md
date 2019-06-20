---
title: Erstellen eines Service Bus Messaging-Namespace mithilfe einer Azure Resource Manager-Vorlage | Microsoft-Dokumentation
description: Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Service Bus Messaging-Namespace
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 4471c9d5b6c09bcf4d9100cccfa725f36cf9a3f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111234"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Erstellen eines Service Bus-Namespace mithilfe einer Azure Resource Manager-Vorlage
In diesem Schnellstart erstellen Sie eine Azure Resource Manager-Vorlage, die einen Service Bus-Namespace vom Typ **Messaging** mit einer **Standard**-SKU erstellt. Der Artikel definiert auch die Parameter, die für die Ausführung der Bereitstellung angegeben werden. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen. Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen][Authoring Azure Resource Manager templates]. Die vollständige Vorlage finden Sie unter [Service Bus namespace template][Service Bus namespace template] (Service Bus-Namespacevorlage) auf GitHub.

> [!NOTE]
> Die folgenden Azure Resource Manager-Vorlagen sind zum Download und zur Bereitstellung verfügbar. 
> 
> * [Create a Service Bus namespace and a queue using an Azure Resource Manager template](service-bus-resource-manager-namespace-queue.md)
> * [Create a Service Bus namespace with topic and subscription using an Azure Resource Manager template (Erstellen eines Service Bus-Namespace mit Thema und Abonnement mit einer Azure Resource Manager-Vorlage)](service-bus-resource-manager-namespace-topic.md)
> * [Create a Service Bus authorization rule for namespace and queue using an Azure Resource Manager template](service-bus-resource-manager-namespace-auth-rule.md)
> * [Create a Service Bus namespace with topic, subscription, and rule (Erstellen eines Service Bus-Namespace mit Thema, Abonnement und Regel)](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Die neuesten Vorlagen finden Sie, indem Sie im Katalog [Azure-Schnellstartvorlagen][Azure Quickstart Templates] nach „Service Bus“ suchen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="quick-deployment"></a>Schnelle Bereitstellung
Um das Beispiel auszuführen, ohne JSON-Code zu schreiben oder PowerShell-/CLI-Befehle auszuführen, wählen Sie die folgende Schaltfläche aus:

[![In Azure bereitstellen](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Für ein manuelles Erstellen und Bereitstellen gehen Sie die folgenden Abschnitte in diesem Artikel durch.

## <a name="prerequisites"></a>Voraussetzungen
Für diese Schnellstartanleitung benötigen Sie ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

Wenn Sie die Resource Manager-Vorlage mithilfe von **Azure PowerShell** bereitstellen möchten, [installieren Sie Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Wenn Sie die Resource Manager-Vorlage über die **Azure-Befehlszeilenschnittstelle** bereitstellen möchten, [installieren Sie die Azure-Befehlszeilenschnittstelle]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Erstellen der JSON-Datei für die Resource Manager-Vorlage 
Erstellen Sie die JSON-Datei **MyServiceBusNamespace.json** mit folgendem Inhalt: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Diese Vorlage erstellt einen Service Bus-Standardnamespace. JSON-Syntax und Eigenschaften finden Sie in der [Namespace](/azure/templates/microsoft.servicebus/namespaces)-Vorlagenreferenz.

## <a name="create-the-parameters-json"></a>Erstellen der JSON-Parameter
Die im vorherigen Schritt erstellte Vorlage weist den Unterabschnitt `Parameters` auf. Sie definieren Parameter für die Werte anhand des bereitgestellten Projekts oder der Zielumgebung. In dieser Vorlage werden die folgenden Parameter definiert: **serviceBusNamespaceName**, **serviceBusSku** und **location**. Weitere Informationen zu Service Bus-SKUs finden Sie unter [Service Bus-SKUs](https://azure.microsoft.com/pricing/details/service-bus/).

Erstellen Sie die JSON-Datei **MyServiceBusNamespace-Parameters.json** mit folgendem Inhalt: 

> [!NOTE] 
> Geben Sie einen Namen für Ihren Service Bus-Namespace an. 


```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Bereitstellen der Vorlage mit Azure PowerShell

### <a name="sign-in-to-azure"></a>Anmelden bei Azure
1. Starten von Azure PowerShell

2. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

   ```azurepowershell
   Login-AzAccount
   ```
3. Führen Sie die folgenden Befehle aus, um den aktuellen Abonnementkontext festzulegen:

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Bereitstellen von Ressourcen
Um die Ressourcen mit Azure PowerShell bereitzustellen, wechseln Sie zu dem Ordner, in dem Sie die JSON-Dateien gespeichert haben, und führen Sie die folgenden Befehle aus:

> [!IMPORTANT]
> Geben Sie vor dem Ausführen der Befehle einen Namen für die Azure-Ressourcengruppe als Wert für „$resourceGroupName“ an. 

1. Deklarieren Sie eine Variable für den Namen der Ressourcengruppe, und geben Sie einen Wert für sie an. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Erstellen einer Azure-Ressourcengruppe.

    ```azurepowershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```
3. Bereitstellen der Resource Manager-Vorlage Geben Sie die Namen der Bereitstellung, der Ressourcengruppe, der JSON-Datei für die Vorlage und der JSON-Datei für Parameter an.

    ```azurepowershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Bereitstellen der Vorlage über die Azure-Befehlszeilenschnittstelle

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Führen Sie den folgenden Befehl aus, um sich bei Azure anzumelden:

    ```azurecli
    az login
    ```
2. Legen Sie den aktuellen Abonnementkontext fest. Ersetzen Sie `MyAzureSub` durch den Namen des Azure-Abonnements, das Sie verwenden möchten:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Bereitstellen von Ressourcen
Wechseln Sie zum Bereitstellen der Ressourcen über die Azure-Befehlszeilenschnittstelle zum Ordner mit den JSON-Dateien, und führen Sie die folgenden Befehle aus:

> [!IMPORTANT]
> Geben Sie im Befehl „az group create“ einen Namen für die Azure-Ressourcengruppe an. .

1. Erstellen einer Azure-Ressourcengruppe. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Bereitstellen der Resource Manager-Vorlage Geben Sie die Namen der Ressourcengruppe, der Bereitstellung, der JSON-Datei für die Vorlage und der JSON-Datei für Parameter an.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie einen Service Bus-Namespace erstellt. Informationen zum Erstellen von Warteschlangen und Themen/Abonnements sowie zu deren Verwendung finden Sie in den anderen Schnellstarts: 

- [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
- [Erste Schritte mit Service Bus-Themen](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: https://azure.microsoft.com/pricing/details/service-bus/
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
