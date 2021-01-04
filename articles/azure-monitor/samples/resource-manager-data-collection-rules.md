---
title: Resource Manager-Beispielvorlagen für Datensammlungsregeln
description: Azure Resource Manager-Beispielvorlagen zum Erstellen von Zuordnungen zwischen Datensammlungsregeln und virtuellen Computern in Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 12e6aa85f7d8c2c4aebe80efc92f771917e6a1d5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746011"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Resource Manager-Beispielvorlagen für Datensammlungsregeln in Azure Monitor
Dieser Artikel enthält Beispiele für [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) zum Bereitstellen und Konfigurieren des [Log Analytics-Agents](../platform/log-analytics-agent.md) und der [Diagnoseerweiterung](../platform/diagnostics-extension-overview.md) für virtuelle Computer in Azure Monitor. Jedes Beispiel umfasst eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Erstellen einer Zuordnung mit einem virtuellen Azure-Computer

Im folgenden Beispiel wird der Azure Monitor-Agent auf einem virtuellen Azure-Computer unter Windows installiert. Es wird eine Zuordnung zwischen einem virtuellen Azure-Computer und einer Datensammlungsregel erstellt.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-ith-azure-arc"></a>Erstellen einer Zuordnung mit Azure Arc

Im folgenden Beispiel wird der Azure Monitor-Agent auf einem virtuellen Azure-Computer unter Windows installiert. Es wird eine Zuordnung zwischen einem Servercomputer mit Azure Arc-Unterstützung und einer Datensammlungsregel erstellt.

### <a name="template-file"></a>Vorlagendatei

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Nächste Schritte

* [Rufen Sie weitere Beispielvorlagen für Azure Monitor ab](resource-manager-samples.md).
* [Erfahren Sie mehr über den Log Analytics-Agent](../platform/log-analytics-agent.md).
* [Erfahren Sie mehr über die Diagnoseerweiterung](../platform/diagnostics-extension-overview.md).
