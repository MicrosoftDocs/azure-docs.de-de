---
title: Automatisieren der Funktions-App-Ressourcenbereitstellung in Azure
description: Hier erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage erstellen, die Ihre Funktions-App bereitstellt.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2403e3a4bfe7824659a9c34c407e55e6c56e6046
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994654"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisieren der Ressourcenbereitstellung für Ihre Funktions-App in Azure Functions

Sie können mithilfe einer Azure Resource Manager-Vorlage eine Funktions-App bereitstellen. In diesem Artikel werden die erforderlichen Ressourcen und die Parameter hierfür beschrieben. Abhängig von den [Triggern und Bindungen](functions-triggers-bindings.md) in Ihrer Funktions-App müssen Sie möglicherweise weitere Ressourcen bereitstellen.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md).

Beispielvorlagen finden Sie unter:
- [Funktions-App im Verbrauchsplan]
- [Funktions-App im Azure App Service-Plan]

## <a name="required-resources"></a>Erforderliche Ressourcen

Eine Azure Functions-Bereitstellung besteht in der Regel aus diesen Ressourcen:

| Resource                                                                           | Anforderung | Syntax- und Eigenschaftenverweis                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Eine Funktions-App                                                                     | Erforderlich    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| Ein [Azure Storage](../storage/index.yml)-Konto                                   | Erforderlich    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Eine [Application Insights](../azure-monitor/app/app-insights-overview.md)-Komponente | Optional    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| [Hostingplan](./functions-scale.md)                                             | Optional<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Ein Hostingplan ist nur erforderlich, wenn Sie Ihre Funktions-App im Rahmen eines [Premium-Plans](./functions-premium-plan.md) oder [App Service-Plans](../app-service/overview-hosting-plans.md) ausführen.

> [!TIP]
> Obwohl nicht erforderlich, sollten Sie unbedingt Application Insights für Ihre App konfigurieren.

<a name="storage"></a>
### <a name="storage-account"></a>Speicherkonto

Für eine Funktions-App wird ein Azure Storage-Konto benötigt. Sie benötigen ein Konto für allgemeine Zwecke, das Blobs, Tabellen, Warteschlangen und Dateien unterstützt. Weitere Informationen finden Sie unter [Anforderungen an das Speicherkonto](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Darüber hinaus muss die Eigenschaft `AzureWebJobsStorage` als App-Einstellung in der Standortkonfiguration angegeben werden. Wenn die Funktions-App nicht Application Insights für die Überwachung verwendet, müssen Sie auch `AzureWebJobsDashboard` als App-Einstellung angeben.

Die Azure Functions-Laufzeit verwendet die Verbindungszeichenfolge `AzureWebJobsStorage` zum Erstellen interner Warteschlangen.  Wenn Application Insights nicht aktiviert ist, verwendet die Runtime die Verbindungszeichenfolge `AzureWebJobsDashboard` zur Anmeldung beim Azure Table-Speicher und zur Nutzung der Registerkarte **Überwachen** im Portal.

Diese Eigenschaften werden in der `appSettings`-Sammlung im `siteConfig`-Objekt angegeben:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Sie sollten Application Insights zur Überwachung Ihrer Funktions-Apps verwenden. Die Application Insights-Ressource wird mit dem Typ **Microsoft.Insights/components** und der Art **web** definiert:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Darüber hinaus muss der Instrumentierungsschlüssel für die Funktions-App mit der Anwendungseinstellung `APPINSIGHTS_INSTRUMENTATIONKEY` bereitgestellt werden. Diese Eigenschaft wird in der `appSettings`-Sammlung im `siteConfig`-Objekt angegeben:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Hostingplan

Die Definition des Hostingplans variiert und kann eine der folgenden sein:
* [Verbrauchsplan](#consumption) (Standard)
* [Premium-Plan](#premium)
* [App Service-Plan](#app-service-plan)

### <a name="function-app"></a>Funktionen-App

Die Ressource für die Funktions-App wird mithilfe einer Ressource vom Typ **Microsoft.Web/sites** und Art **functionapp** definiert:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Wenn Sie explizit einen Hostingplan definieren, würde ein zusätzliches Element im dependsOn-Array erforderlich sein: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Eine Funktions-App muss diese Anwendungseinstellungen enthalten:

| Einstellungsname                 | BESCHREIBUNG                                                                               | Beispielwerte                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Eine Verbindungszeichenfolge für eine Verbindung mit einem Speicherkonto, das die Functions-Runtime für die interne Warteschlange verwendet | Siehe [Speicherkonto](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Die Version der Azure Functions-Runtime                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Der Sprachstapel für Funktionen in dieser App                                   | `dotnet`, `node`, `java`, `python` oder `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Nur erforderlich, wenn Sie den `node`-Sprachstapel verwenden, gibt die zu verwendende Version an              | `10.14.1`                             |

Diese Eigenschaften werden in der `appSettings`-Sammlung in der `siteConfig`-Eigenschaft angegeben:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Bereitstellen im Verbrauchsplan

Der Verbrauchsplan weist automatisch Computeleistung zu, wenn Ihr Code ausgeführt wird, und skaliert diese bei Bedarf horizontal hoch, um die Last zu verarbeiten. Wenn der Code nicht mehr ausgeführt wird, wird die Leistung wieder horizontal herunterskaliert. Sie müssen für VMs im Leerlauf nicht bezahlen und auch keine Kapazitäten im Voraus reservieren. Weitere Informationen finden Sie unter [Skalierung und Hosting von Azure Functions](functions-scale.md#consumption-plan).

Eine Beispielvorlage für den Azure Resource Manager finden Sie unter [Funktions-App im Verbrauchsplan].

### <a name="create-a-consumption-plan"></a>Erstellen eines Verbrauchsplans

Ein Verbrauchsplan muss nicht definiert werden. Er wird automatisch erstellt oder auf Regionsbasis ausgewählt, wenn Sie die Funktions-App-Ressource selbst erstellen.

Der Verbrauchsplan ist ein besonderer Typ einer „Serverfarm“-Ressource. Für Windows können Sie ihn mithilfe des `Dynamic`-Werts für die Eigenschaften `computeMode` und `sku` angeben:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Der Verbrauchsplan kann nicht explizit für Linux definiert werden. Er wird automatisch erstellt.

Wenn Sie Ihren Verbrauchstarif explizit definieren, müssen Sie die `serverFarmId`-Eigenschaft der App so festlegen, dass sie auf die Ressourcen-ID des Plans verweist. Sie sollten sicherstellen, dass die Funktions-App auch eine `dependsOn`-Einstellung für den Plan hat.

### <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

#### <a name="windows"></a>Windows

Unter Windows erfordert ein Verbrauchsplan zwei zusätzliche Einstellungen in der Standortkonfiguration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` und `WEBSITE_CONTENTSHARE`. Diese Eigenschaften konfigurieren das Speicherkonto und den Dateipfad zu dem Speicherort von Funktions-App-Code und Konfiguration.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

Unter Linux muss `kind` der Funktions-App auf `functionapp,linux` festgelegt werden, und die `reserved`-Eigenschaft muss auf `true` festgelegt werden:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Bereitstellen im Premium-Plan

Der Premium-Tarif bietet die gleiche Skalierung wie der Verbrauchstarif, umfasst jedoch dedizierte Ressourcen und zusätzliche Funktionen. Weitere Informationen finden Sie unter [Premium-Tarif für Azure Functions](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Erstellen eines Premium-Plans

Ein Premium-Plan ist eine besondere Art von „Serverfarm“-Ressource. Sie können ihn angeben, indem Sie entweder `EP1`, `EP2` oder `EP3` für den `Name`-Eigenschaftswert im `sku`-[Beschreibungsobjekt](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object) angeben.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Für die `serverFarmId`-Eigenschaft einer Funktions-App in einem Premium-Plan muss die Ressourcen-ID des zuvor erstellten Plans festgelegt werden. Darüber hinaus erfordert ein Premium-Plan zwei zusätzliche Einstellungen in der Standortkonfiguration: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` und `WEBSITE_CONTENTSHARE`. Diese Eigenschaften konfigurieren das Speicherkonto und den Dateipfad zu dem Speicherort von Funktions-App-Code und Konfiguration.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Bereitstellen in einem App Service-Plan

In einem App Service-Plan wird Ihre Funktions-App ähnlich wie Web-Apps auf dedizierten virtuellen Computern für Basic-, Standard- und Premium-SKUs ausgeführt. Weitere Informationen zur Funktionsweise von App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](../app-service/overview-hosting-plans.md).

Eine Beispielvorlage für den Azure Resource Manager finden Sie unter [Funktions-App im Azure App Service-Plan].

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

Ein App Service-Plan ist als „Serverfarm“-Ressource definiert.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Um Ihre App unter Linux auszuführen, müssen Sie auch `kind` als `Linux` festlegen:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

Für die `serverFarmId`-Eigenschaft einer Funktions-App in einem App Service-Plan muss die Ressourcen-ID des zuvor erstellten Plans festgelegt werden.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Linux-Apps sollten auch eine `linuxFxVersion`-Eigenschaft unter `siteConfig` enthalten. Wenn Sie nur Code bereitstellen, wird der Wert hierfür durch Ihren gewünschten Runtimestapel bestimmt:

| Stapel            | Beispielwert                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Wenn Sie [ein benutzerdefiniertes Containerimage bereitstellen](./functions-create-function-linux-custom-image.md), müssen Sie es mit `linuxFxVersion` angeben und eine Konfiguration einbeziehen, die das Pullen Ihres Images ermöglicht, wie in [App Service unter Linux – Dokumentation](../app-service/index.yml). Setzen Sie außerdem `WEBSITES_ENABLE_APP_SERVICE_STORAGE` auf `false`, da Ihr App-Inhalt im Container selbst bereitgestellt wird:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Anpassen einer Bereitstellung

Eine Funktions-App verfügt über viele untergeordnete Ressourcen, die Sie in Ihrer Bereitstellung verwenden können. Hierzu zählen beispielsweise App-Einstellungen und Quellcodeverwaltungsoptionen. Sie können die untergeordnete Ressource **sourcecontrols** auch entfernen und stattdessen eine andere [Bereitstellungsoption](functions-continuous-deployment.md) verwenden.

> [!IMPORTANT]
> Um Ihre Anwendung erfolgreich mithilfe von Azure Resource Manager bereitzustellen, müssen Sie mit der Bereitstellung von Ressourcen in Azure vertraut sein. Im folgenden Beispiel werden mithilfe von **siteConfig** Konfigurationen auf oberster Ebene angewendet. Diese Konfigurationen müssen auf der obersten Ebene festgelegt werden, da sie Informationen für die Laufzeit- und Azure CLI 2.0 Preview von Functions bereitstellen. Informationen auf oberster Ebene werden benötigt, bevor die untergeordnete Ressource **sourcecontrols/web** angewendet wird. Die Einstellungen könnten zwar auch in der untergeordneten Ressource **config/appSettings** angewendet werden, in bestimmten Fällen müssen Ihre Funktions-App und die Funktionen jedoch bereitgestellt werden, *bevor* **config/appSettings** angewendet wird. Wenn Sie z.B. Funktionen mit [Logic Apps](../logic-apps/index.yml) verwenden, handelt es sich bei Ihren Funktionen um eine Abhängigkeit einer anderen Ressource.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> In dieser Vorlage wird der App-Einstellungswert [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) verwendet. Dieser Wert legt das Basisverzeichnis fest, in dem die Bereitstellungs-Engine von Functions (Kudu) nach bereitstellbarem Code sucht. In unserem Repository befinden sich die Funktionen in einem Unterordner des Ordners **src**. Daher legen wir den App-Einstellungswert im vorherigen Beispiel auf `src` fest. Falls sich Ihre Funktionen im Stammverzeichnis des Repositorys befinden oder Sie Ihre Bereitstellung nicht über die Quellcodeverwaltung durchführen, können Sie diesen App-Einstellungswert entfernen.

## <a name="deploy-your-template"></a>Bereitstellen der Vorlage

Sie können Ihre Vorlage mit einer der folgenden Methoden bereitstellen:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure-Befehlszeilenschnittstelle](../azure-resource-manager/templates/deploy-cli.md)
* [Azure portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST-API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Schaltfläche zum Bereitstellen in Azure

Ersetzen Sie ```<url-encoded-path-to-azuredeploy-json>``` durch eine [URL-codierte](https://www.bing.com/search?q=url+encode) Version des Rohpfads Ihrer Datei `azuredeploy.json` in GitHub.

Hier ist ein Beispiel unter Verwendung von Markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Hier ist ein Beispiel unter Verwendung von HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Bereitstellen mit PowerShell

Mit den folgenden PowerShell-Befehlen wird eine Ressourcengruppe erstellt und eine Vorlage bereitgestellt, über die eine Funktions-App mit deren erforderlichen Ressourcen erstellt wird. Um diese Befehle lokal ausführen zu können, müssen Sie [Azure PowerShell](/powershell/azure/install-az-ps) installiert haben. Führen Sie [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) aus, um sich anzumelden.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Um diese Bereitstellung zu testen, können Sie eine [Vorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) wie die folgende verwenden, mit der eine Funktions-App unter Windows in einem Verbrauchsplan erstellt wird. Ersetzen Sie `<function-app-name>` durch einen eindeutigen Namen für Ihre Funktions-App.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich näher mit dem Entwickeln und Konfigurieren von Azure Functions vertraut.

* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
* [Konfigurieren von Azure-Funktions-App-Einstellungen](functions-how-to-use-azure-function-app-settings.md)
* [Erstellen Sie Ihre erste Funktion in Azure Functions](functions-create-first-azure-function.md)

<!-- LINKS -->

[Funktions-App im Verbrauchsplan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Funktions-App im Azure App Service-Plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json