---
title: 'Schnellstart: Konfigurieren von Datenflussprotokollen von Netzwerksicherheitsgruppen mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage)'
description: Hier erfahren Sie, wie Sie NSG-Datenflussprotokolle (Netzwerksicherheitsgruppe) programmgesteuert mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) und unter Verwendung von Azure PowerShell aktivieren.
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 563f111a656376899fcd0201b42f87bfea445865
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488051"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Schnellstart: Konfigurieren von Datenflussprotokollen von Netzwerksicherheitsgruppen mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung erfahren Sie, wie Sie [NSG-Datenflussprotokolle (Netzwerksicherheitsgruppe)](network-watcher-nsg-flow-logging-overview.md) mithilfe einer [Azure Resource Manager](../azure-resource-manager/management/overview.md)-Vorlage (ARM-Vorlage) und unter Verwendung von Azure PowerShell aktivieren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wir beginnen mit einer Übersicht über die Eigenschaften des NSG-Flussprotokollobjekts. Wir stellen Ihnen Beispielvorlagen zur Verfügung. Dann verwenden wir eine lokale Azure PowerShell-Instanz, um die Vorlage bereitzustellen.

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Diese Ressourcen sind in der Vorlage definiert:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG-Flussprotokollobjekt

Der folgende Code zeigt ein NSG-Flussprotokollobjekt und seine Parameter an. Fügen Sie zum Erstellen einer Ressource vom Typ `Microsoft.Network/networkWatchers/flowLogs` diesen Code im Ressourcenabschnitt Ihrer Vorlage hinzu:

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

Eine umfassende Übersicht über die Eigenschaften des NSG-Flussprotokollobjekts finden Sie unter [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Erstellen der Vorlage

Wenn Sie ARM-Vorlagen zum ersten Mal verwenden, lesen Sie die folgenden Artikel, um mehr über ARM-Vorlagen zu erfahren:

- [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Das folgende Beispiel ist eine vollständige Vorlage. Es ist auch die einfachste Version der Vorlage. Das Beispiel enthält die Mindestparameter, die zur Einrichtung von NSG-Datenflussprotokollen übergeben werden. Weitere Beispiele finden Sie im Übersichtsartikel [Konfigurieren von NSG-Flussprotokollen aus einer Azure Resource Manager-Vorlage](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Beispiel

Die folgende Vorlage ermöglicht Datenflussprotokolle für eine NSG und speichert die Protokolle dann in einem bestimmten Speicherkonto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Der Ressourcenname verwendet das Format _ParentResource_ChildResource_. In unserem Beispiel ist die übergeordnete Ressource die regionale Azure Network Watcher-Instanz:
>    - **Format**: NetworkWatcher_RegionName
>    - **Beispiel:** NetworkWatcher_centraluseuap
> - `targetResourceId` ist die Ressourcen-ID der Ziel-NSG.
> - `storageId` ist die Ressourcen-ID des Zielspeicherkontos.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

In diesem Tutorial wird davon ausgegangen, dass Sie über eine Ressourcengruppe und eine NSG verfügen, in der Sie die Flussprotokollierung aktivieren können.

Sie können jede der in diesem Artikel gezeigten Beispielvorlagen lokal als *azuredeploy.json* speichern. Aktualisieren Sie die Eigenschaftswerte so, dass sie auf gültige Ressourcen in Ihrem Abonnement verweisen.

Führen Sie den folgenden Befehl in Azure PowerShell aus, um die Vorlage bereitzustellen.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Diese Befehle stellen eine Ressource für die NetworkWatcherRG-Beispielressourcengruppe bereit und nicht für die Ressourcengruppe, die die NSG enthält.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Sie haben zwei Möglichkeiten, um zu prüfen, ob Ihre Bereitstellung erfolgreich war:

- Ihre PowerShell-Konsole zeigt `ProvisioningState` als `Succeeded` an.
- Wechseln Sie zur [Portalseite für NSG-Datenflussprotokolle](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs), um Ihre Änderungen zu bestätigen. 

Falls bei der Bereitstellung Probleme aufgetreten sind, lesen Sie den Artikel [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Azure-Ressourcen unter Verwendung des vollständigen Bereitstellungsmodus löschen. Zum Löschen einer Ressource des Datenflussprotokolls geben Sie eine Bereitstellung im Modus „Vollständig“ ohne Angabe der zu löschenden Ressource an. Erfahren Sie mehr über den [Bereitstellungsmodus „Vollständig“](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Sie können ein NSG-Datenflussprotokoll auch im Azure-Portal deaktivieren:

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie **Alle Dienste** aus. Geben Sie im Feld **Filter** die Zeichenfolge **Network Watcher** ein. Wählen Sie in den Suchergebnissen **Network Watcher** aus.
1. Wählen Sie unter **Protokolle** die Option **NSG-Datenflussprotokolle** aus.
1. Wählen Sie in der NSG-Liste die Netzwerksicherheitsgruppe aus, für die Sie Datenflussprotokolle deaktivieren möchten.
1. Wählen Sie unter **Flowprotokolleinstellungen** die Option **Aus** aus.
1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie NSG-Datenflussprotokolle mithilfe einer ARM-Vorlage aktivieren. Als nächstes erfahren Sie, wie Sie Ihre NSG-Datenflussdaten mithilfe einer dieser Optionen visualisieren können:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Open-Source-Tools](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
