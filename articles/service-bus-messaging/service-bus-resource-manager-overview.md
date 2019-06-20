---
title: Erstellen von Azure Service Bus-Ressourcen mithilfe von Resource Manager-Vorlagen | Microsoft-Dokumentation
description: Verwenden von Azure Resource Manager-Vorlagen, um die Erstellung von Service Bus-Ressourcen zu automatisieren
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 196b00f1268eada20d0e35473dc6eb43c9e48df6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66111138"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Erstellen von Service Bus-Ressourcen mithilfe von Azure Resource Manager-Vorlagen

Dieser Artikel erläutert das Erstellen und Bereitstellen von Service Bus-Ressourcen mithilfe von Azure Resource Manager-Vorlagen, PowerShell und dem Service Bus-Ressourcenanbieter.

Azure Resource Manager-Vorlagen helfen Ihnen dabei, die für eine Lösung bereitzustellenden Ressourcen zu definieren und die Parameter und Variablen anzugeben, die Sie zur Eingabe von Werten für verschiedene Umgebungen benötigen. Die Vorlage besteht aus JSON-Code und Ausdrücken, mit denen Sie Werte für Ihre Bereitstellung erstellen können. Detaillierte Informationen zum Erstellen von Azure Resource Manager-Vorlagen sowie eine Beschreibung des Vorlagenformats finden Sie unter [Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Die Beispiele in diesem Artikel zeigen, wie Sie Azure Resource Manager verwenden, um einen Service Bus-Namespace und eine Messagingentität (Warteschlange) zu erstellen. Um Beispiele für andere Vorlagen zu finden, rufen Sie den [Katalog mit Azure-Schnellstartvorlagen][Azure Quickstart Templates gallery] auf und suchen dort nach **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Resource Manager-Vorlagen für Service Bus

Diese Azure Resource Manager-Vorlagen für Service Bus sind zum Download und zur Bereitstellung verfügbar. Klicken Sie auf die folgenden Links, um Details zu jeder Vorlage abzurufen (auf jeder Seite finden Sie Links zu den Vorlagen in GitHub):

* [Erstellen eines Service Bus-Namespaces](service-bus-resource-manager-namespace.md)
* [Create a Service Bus namespace and a queue using an Azure Resource Manager template (Erstellen eines Service Bus-Namespace und einer Warteschlange mit einer Azure Resource Manager-Vorlage)](service-bus-resource-manager-namespace-queue.md)
* [Create a Service Bus namespace with topic and subscription using an Azure Resource Manager template (Erstellen eines Service Bus-Namespace mit Thema und Abonnement mit einer Azure Resource Manager-Vorlage)](service-bus-resource-manager-namespace-topic.md)
* [Create a Service Bus authorization rule for namespace and queue using an Azure Resource Manager template](service-bus-resource-manager-namespace-auth-rule.md)
* [Create a Service Bus namespace with topic, subscription, and rule (Erstellen eines Service Bus-Namespace mit Thema, Abonnement und Regel)](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Bereitstellen mit PowerShell

Das folgende Verfahren beschreibt die Verwendung von PowerShell, um eine Azure Resource Manager-Vorlage bereitzustellen, die einen Service Bus-Namespace im Standard-Tarif sowie eine Warteschlange innerhalb dieses Namespace erstellt. Dieses Beispiel basiert auf der Vorlage [Create a Service Bus namespace with queue](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) (Erstellen eines Service Bus-Namespace mit Warteschlange). Der Workflow sieht in etwa folgendermaßen aus:

1. Installieren Sie PowerShell.
2. Erstellen Sie die Vorlage und (optional) eine Parameterdatei.
3. Melden Sie sich in PowerShell bei Ihrem Azure-Konto an.
4. Erstellen Sie eine neue Ressourcengruppe, falls noch keine vorhanden ist.
5. Testen Sie die Bereitstellung.
6. Legen Sie ggf. den Bereitstellungsmodus fest.
7. Stellen Sie die Vorlage bereit.

Vollständige Informationen zur Bereitstellung von Azure Resource Manager-Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Installieren von PowerShell

Installieren Sie Azure PowerShell gemäß den Anweisungen unter [Erste Schritte mit Azure PowerShell-Cmdlets](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Erstellen einer Vorlage

KlonenSie das Repository oder kopieren Sie die Vorlage [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) aus GitHub:

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
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Erstellen einer Parameterdatei (optional)

Um eine optionale Parameterdatei zu verwenden, kopieren Sie die Datei [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json). Ersetzen Sie den Wert von `serviceBusNamespaceName` durch den Namen des Service Bus-Namespace, den Sie in dieser Bereitstellung erstellen möchten, und ersetzen Sie den Wert von `serviceBusQueueName` durch den Namen der Warteschlange, die Sie erstellen möchten.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Weitere Informationen finden Sie im Artikel [Parameter](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Anmelden bei Azure und Festlegen des Azure-Abonnements

Führen Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl aus:

```powershell
Connect-AzAccount
```

Sie werden aufgefordert, sich bei Ihrem Azure-Konto anzumelden. Führen Sie nach der Anmeldung den folgenden Befehl aus, um Ihre verfügbaren Abonnements anzuzeigen:

```powershell
Get-AzSubscription
```

Dieser Befehl gibt eine Liste der verfügbaren Azure-Abonnements zurück. Wählen Sie ein Abonnement für die aktuelle Sitzung aus, indem Sie folgenden Befehl ausführen. Ersetzen Sie `<YourSubscriptionId>` durch die GUID des Azure-Abonnements, das Sie verwenden möchten:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Festlegen der Ressourcengruppe

Wenn noch keine Ressourcengruppe vorhanden ist, erstellen Sie mit dem Befehl **New-AzResourceGroup** eine neue Ressourcengruppe. Geben Sie den Namen der gewünschten Ressourcengruppe und den gewünschten Speicherort ein. Beispiel:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Nach erfolgreicher Ausführung wird eine Zusammenfassung der neuen Ressourcengruppe angezeigt:

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testen der Bereitstellung

Überprüfen Sie Ihre Bereitstellung, indem Sie das Cmdlet `Test-AzResourceGroupDeployment` ausführen. Geben Sie beim Testen der Bereitstellung die Parameter exakt so an wie beim Ausführen der Bereitstellung.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Erstellen der Bereitstellung

Um die neue Bereitstellung zu erstellen, führen Sie das Cmdlet `New-AzResourceGroupDeployment` aus, und geben Sie bei entsprechender Aufforderung die erforderlichen Parameter an. Die Parameter enthalten einen Namen für Ihre Bereitstellung, den Namen Ihrer Ressourcengruppe und den Pfad oder die URL zur Vorlagendatei. Wenn der Parameter **Mode** nicht angegeben wurde, wird der Standardwert **Incremental** verwendet. Weitere Informationen finden Sie unter [Inkrementelle und vollständige Bereitstellungen](../azure-resource-manager/deployment-modes.md).

Der folgende Befehl fordert die Angabe der drei Parameter im PowerShell-Fenster:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Um stattdessen eine Parameterdatei zu verwenden, geben Sie folgenden Befehl ein:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Sie können beim Ausführen des Bereitstellungs-Cmdlets auch Inlineparameter verwenden. Der Befehl lautet wie folgt:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Legen Sie zum Ausführen einer [vollständigen](../azure-resource-manager/deployment-modes.md) Bereitstellung den Parameter **Mode** auf **Complete** fest:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Überprüfen der Bereitstellung
Wenn die Ressourcen erfolgreich bereitgestellt wurden, wird im PowerShell-Fenster eine Zusammenfassung der Bereitstellung angezeigt:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Nächste Schritte
Sie haben nun den grundlegenden Workflow und die grundlegenden Befehle für die Bereitstellung einer Azure Resource Manager-Vorlage kennengelernt. Detaillierte Informationen finden Sie unter folgenden Links:

* [Übersicht über den Azure Resource Manager][Azure Resource Manager overview]
* [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md)
* [Microsoft.ServiceBus-Ressourcentypen](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
