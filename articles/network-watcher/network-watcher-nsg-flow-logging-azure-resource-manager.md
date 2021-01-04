---
title: Network Watcher – Erstellen von NSG-Flussprotokollen mithilfe einer Azure Resource Manager-Vorlage
description: Richten Sie mit einer Azure Resource Manager-Vorlage und PowerShell ganz einfach NSG-Flussprotokolle ein.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 8db4d4ae56a5a0ee0c92de5d9822614a3270b1c5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948679"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Erstellen von NSG-Flussprotokollen aus einer Azure Resource Manager-Vorlage

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) ist die native und leistungsfähige Azure-Methode zum Verwalten Ihrer [Infrastruktur als Code](/azure/devops/learn/what-is-infrastructure-as-code).

In diesem Artikel wird gezeigt, wie Sie [NSG-Flussprotokolle](./network-watcher-nsg-flow-logging-overview.md) programmgesteuert mithilfe einer Azure Resource Manager-Vorlage und Azure PowerShell aktivieren. Zunächst stellen wir eine Übersicht über die Eigenschaften des NSG-Flussprotokollobjekts bereit, gefolgt von einigen Beispielvorlagen. Anschließend stellen wir die Vorlage mithilfe einer lokalen PowerShell-Instanz bereit.


## <a name="nsg-flow-logs-object"></a>NSG-Flussprotokollobjekt

Das NSG-Flussprotokollobjekt mit allen Parametern ist unten dargestellt.
Eine umfassende Übersicht über die Eigenschaften finden Sie in der [Vorlagenreferenz zu NSG-Flussprotokollen](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Fügen Sie zum Erstellen einer Ressource vom Typ Microsoft.Network/networkWatchers/flowLogs dem Abschnitt „resources“ Ihrer Vorlage den obigen JSON-Code hinzu.


## <a name="creating-your-template"></a>Erstellen der Vorlage

Wenn Sie zum ersten Mal Azure Resource Manager-Vorlagen verwenden, finden Sie unter den nachstehenden Links weitere Informationen.

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
* [Tutorial: Erstellen und Bereitstellen Ihrer ersten Azure Resource Manager-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Nachstehend finden Sie zwei Beispiele für vollständige Vorlagen zum Einrichten von NSG-Flussprotokollen.

**Beispiel 1:**  Die einfachste Version mit minimalen übergebenen Parametern. Mit der nachstehenden Vorlage werden NSG-Flussprotokolle in einer Ziel-NSG aktiviert und in einem angegebenen Speicherkonto gespeichert.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Der Name der Ressource weist das Format „übergeordnete Ressource_untergeordnete Ressource“ auf. Hier ist die übergeordnete Ressource die regionale Network Watcher Instanz (Format: NetworkWatcher_RegionName. Beispiel: NetworkWatcher_centraluseuap)
> * targetResourceId ist die Ressourcen-ID der Ziel-NSG.
> * storageId ist die Ressourcen-ID des Zielspeicherkontos.

**Beispiel 2:** Mit den folgenden Vorlagen werden NSG-Flussprotokolle (Version 2) mit einer Aufbewahrungsdauer von 5 Tagen aktiviert. Dadurch wird Traffic Analytics mit einem Verarbeitungsintervall von 10 Minuten aktiviert.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Bereitstellen der Azure Resource Manager-Vorlage

In diesem Tutorial wird davon ausgegangen, dass Sie über eine Ressourcengruppe und eine NSG verfügen, in der Sie die Flussprotokollierung aktivieren können.
Sie können jede der oben aufgeführten Beispielvorlagen lokal als `azuredeploy.json` speichern. Aktualisieren Sie die Eigenschaftswerte so, dass sie auf gültige Ressourcen in Ihrem Abonnement verweisen.

Führen Sie den folgenden Befehl in PowerShell aus, um die Vorlage bereitzustellen.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Mit den obigen Befehlen wird eine Ressource für die Ressourcengruppe „NetworkWatcherRG“ bereitgestellt, nicht für die Ressourcengruppe, die die NSG enthält.


## <a name="verifying-your-deployment"></a>Überprüfen der Bereitstellung

Für die Überprüfung, ob die Bereitstellung erfolgreich war, gibt es mehrere Möglichkeiten. In Ihrer PowerShell-Konsole sollte „ProvisioningState“ als „erfolgreich“ angezeigt werden. Darüber hinaus können Sie die Änderungen auf der [Portalseite für NSG-Flussprotokolle](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) bestätigen. Falls bei der Bereitstellung Probleme aufgetreten sind, lesen Sie den Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Löschen Ihrer Ressource
Azure ermöglicht das Löschen von Ressourcen über den Bereitstellungsmodus „Vollständig“. Zum Löschen einer Ressource des Flowprotokolls geben Sie eine Bereitstellung im Modus „Vollständig“ ohne Angabe der zu löschenden Ressource an. Erfahren Sie mehr über den [Bereitstellungsmodus „Vollständig“](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Ihre NSG-Flussprotokolle mit folgenden Hilfsmitteln visualisieren:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Open-Source-Tools](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](./traffic-analytics.md)