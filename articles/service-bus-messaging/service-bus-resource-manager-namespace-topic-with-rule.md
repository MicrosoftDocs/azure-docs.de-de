---
title: Erstellen eines Azure Service Bus-Themaabonnements und einer Regel anhand einer Azure Resource Manager-Vorlage | Microsoft-Dokumentation
description: Erstellen eines Service Bus-Namespace mit einem Thema, einem Abonnement und einer Regel anhand einer Azure Resource Manager-Vorlage
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: 5c6ad222110081cd8f8838208da407e0e1d50f75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "54851265"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Erstellen eines Service Bus-Namespace mit einem Thema, einem Abonnement und einer Regel anhand einer Azure Resource Manager-Vorlage

In diesem Artikel wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage verwenden, die einen Service Bus-Namespace mit einem Thema, einem Abonnement und einer Regel (Filter) erstellt. Dieser Artikel zeigt Ihnen, wie Sie angeben, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder Ihren Anforderungen anpassen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen][Authoring Azure Resource Manager templates].

Weitere Informationen zur Vorgehensweise und Muster zu Benennungskonventionen für Azure-Ressourcen finden Sie unter [Recommended naming conventions for Azure resources][Recommended naming conventions for Azure resources] (Empfohlene Benennungskonventionen für Azure-Ressourcen).

Die vollständige Vorlage finden Sie unter [Service Bus-Namespace mit Thema, Abonnement und Regel][Service Bus namespace with topic, subscription, and rule].

> [!NOTE]
> Die folgenden Azure Resource Manager-Vorlagen sind zum Download und zur Bereitstellung verfügbar.
> 
> * [Create a Service Bus authorization rule for namespace and queue using an Azure Resource Manager template](service-bus-resource-manager-namespace-auth-rule.md)
> * [Create a Service Bus namespace and a queue using an Azure Resource Manager template](service-bus-resource-manager-namespace-queue.md)
> * [Erstellen eines Service Bus-Namespaces](service-bus-resource-manager-namespace.md)
> * [Create a Service Bus namespace with topic and subscription using an Azure Resource Manager template (Erstellen eines Service Bus-Namespace mit Thema und Abonnement mit einer Azure Resource Manager-Vorlage)](service-bus-resource-manager-namespace-topic.md)
> 
> Die neuesten Vorlagen finden Sie, indem Sie im Katalog [Azure-Schnellstartvorlagen][Azure Quickstart Templates] nach „Service Bus“ suchen.
> 
> 

## <a name="what-do-you-deploy"></a>Was stellen Sie bereit?

Mit dieser Vorlage können Sie einen Service Bus-Namespace mit Thema, Abonnement und Regel (Filter) bereitstellen.

[Service Bus-Themen und -Abonnements](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) bieten eine 1:n-Kommunikation in einem Muster vom Typ *Veröffentlichen/Abonnieren*. Bei der Verwendung von Themen und Abonnements kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern sie tauschen Nachrichten über ein Thema aus, das als Zwischenelement dient. Durch das Abonnieren eines Themas wird eine virtuelle Warteschlange erstellt, die Kopien der an das Thema gesendeten Nachrichten empfängt. Durch einen Filter für ein Abonnement können Sie angeben, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

## <a name="what-are-rules-filters"></a>Was sind Regeln (Filter)?

In vielen Fällen müssen Nachrichten mit bestimmten Merkmalen auf unterschiedliche Weise verarbeitet werden. Damit diese benutzerdefinierte Verarbeitung möglich ist, können Sie Abonnements so konfigurieren, dass sie nach Nachrichten mit bestimmten Eigenschaften suchen und dann Änderungen an diesen Eigenschaften vornehmen. Auch wenn Service Bus-Abonnements alle Nachrichten angezeigt werden, die an das Thema gesendet wurden, können Sie nur eine Teilmenge dieser Nachrichten in die virtuelle Abonnementwarteschlange kopieren. Dies wird mithilfe von Abonnementfiltern erreicht. Weitere Informationen zu Regeln (Filtern) finden Sie unter [Regeln und Aktionen](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Klicken Sie auf folgende Schaltfläche, um die Bereitstellung automatisch auszuführen:

[![In Azure bereitstellen](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parameter

Definieren Sie mit Azure Resource Manager die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens `Parameters` , der alle Parameterwerte enthält. Definieren Sie einen Parameter für die Werte, die basierend auf dem bereitgestellten Projekt oder der bereitgestellten Umgebung variieren. Definieren Sie keine Parameter für Werte, die sich nicht ändern. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren.

Die Vorlage definiert die folgenden Parameter:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Der Name des zu erstellenden Service Bus-Namespace.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Der Name des Themas, das im Service Bus-Namespace erstellt wird.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Der Name des Abonnements, das im Service Bus-Namespace erstellt wird.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Der Name der Regel (des Filters), die im Service Bus-Namespace erstellt wird.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
Die Service Bus-API-Version der Vorlage.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Bereitzustellende Ressourcen
Erstellt einen Service Bus-Standardnamespace vom Typ **Messaging** mit Thema, Abonnement und Regeln.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

JSON-Syntax und Eigenschaften finden Sie unter [namespaces](/azure/templates/microsoft.servicebus/namespaces), [topics](/azure/templates/microsoft.servicebus/namespaces/topics), [subscriptions](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions) und [rules](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Befehle zum Ausführen der Bereitstellung
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun Ressourcen mit Azure Resource Manager erstellt und bereitgestellt haben, lesen Sie die folgenden Artikel, und erfahren Sie, wie Sie diese Ressourcen verwalten können:

* [Verwalten von Azure Service Bus](service-bus-management-libraries.md)
* [Verwalten von Service Bus mit PowerShell](service-bus-manage-with-ps.md)
* [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

