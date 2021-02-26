---
title: Resource Manager-Vorlagenbeispiele für Azure-Funktions-App und Application Insights-Ressourcen
description: Beispiele für Azure Resource Manager-Vorlagen zum Bereitstellen einer Azure-Funktions-App mit einer Application Insights-Ressource.
ms.subservice: application-insights
ms.topic: sample
author: lgayhardt
ms.author: lagayhar
ms.date: 08/06/2020
ms.openlocfilehash: 85829052f2e9179b0a721efce720763f82d6402c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597899"
---
# <a name="resource-manager-template-sample-for-creating-azure-function-apps-with-application-insights-monitoring"></a>Resource Manager-Vorlagenbeispiel zum Erstellen von Azure-Funktions-Apps mit Application Insights-Überwachung

Dieser Artikel enthält exemplarische [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) zum Bereitstellen und Konfigurieren [klassischer Application Insights-Ressourcen](../app/create-new-resource.md) in Verbindung mit einer Azure-Funktions-App. Das Beispiel umfasst eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="azure-function-app"></a>Azure-Funktionen-App

Im folgenden Beispiel werden eine .NET Core 3.1-basierte Azure-Funktions-App unter einem App Service-Plan für Windows und eine [klassische Application Insights-Ressource](../app/create-new-resource.md) mit aktivierter Überwachung erstellt. 

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "kind": "functionapp",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/function-app-01",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "FUNCTIONS_EXTENSION_VERSION",
                            "value": "~3"
                        },
                        {
                            "name": "FUNCTIONS_WORKER_RUNTIME",
                            "value": "dotnet"
                        },
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/function-app-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/function-app-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "AzureWebJobsStorage",
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
                        }
                    ],
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "function-app-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        },
        {
            "apiVersion": "2019-06-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "tags": {},
            "sku": {
                "name": "Standard_LRS"
            },
            "properties": {
                "supportsHttpsTrafficOnly": true
            }
        }
    ]
}
```

### <a name="parameters-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "function-app-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "storageAccountName": {
      "value": "storageaccountest93"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Rufen Sie weitere Beispielvorlagen für Azure Monitor ab](../resource-manager-samples.md).
* [Weitere klassische Application Insights-Ressourcen](../app/create-new-resource.md).
