---
title: Verschieben eines virtuellen Azure-Netzwerks in eine andere Azure-Region mit Azure PowerShell
description: Verschieben Sie ein virtuelles Azure-Netzwerk aus einer Azure-Region in eine andere, indem Sie eine Resource Manager-Vorlage und Azure PowerShell verwenden.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: e13164c3ec6049a8ae3954528a02d20e313dd883
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008122"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Verschieben eines virtuellen Azure-Netzwerks in eine andere Region mit Azure PowerShell

Es gibt verschiedene Szenarien, in denen ein vorhandenes virtuelles Azure-Netzwerk aus einer Region in eine andere verschoben wird. Möglicherweise möchten Sie für Testzwecke oder Verfügbarkeitsprüfungen ein virtuelles Netzwerk erstellen, das dieselbe Konfiguration wie Ihr vorhandenes virtuelles Netzwerk aufweist. Oder Sie möchten im Rahmen der Planung der Notfallwiederherstellung ein virtuelles Produktionsnetzwerk in eine andere Region verschieben.

Sie können zum Verschieben des virtuellen Netzwerks in eine andere Region eine Azure Resource Manager-Vorlage verwenden. Dazu exportieren Sie das virtuelle Netzwerk in eine Vorlage, ändern die Parameter entsprechend der Zielregion und stellen die Vorlage dann in der neuen Region bereit. Weitere Informationen zu Resource Manager-Vorlagen finden Sie unter [Exportieren von Ressourcengruppen in Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich das virtuelle Netzwerk in der Azure-Region befindet, aus der Sie es verschieben möchten.

- Damit Sie ein virtuelles Netzwerk exportieren und eine Vorlage bereitstellen können, um ein virtuelles Netzwerk in einer anderen Region zu erstellen, benötigen Sie mindestens die Rolle „Netzwerkmitwirkender“.

- Peerings virtueller Netzwerke werden nicht neu erstellt und schlagen fehl, wenn sie noch in der Vorlage vorhanden sind. Vor dem Exportieren der Vorlage müssen Sie alle Peerings virtueller Netzwerke entfernen. Sie können diese nach dem Verschieben des virtuellen Netzwerks wiederherstellen.
    
- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout schließt u. a. Load Balancers, Netzwerksicherheitsgruppen (NSGs) und öffentliche IP-Adressen ein.

- Vergewissern Sie sich, dass Sie mit Ihrem Azure-Abonnement virtuelle Netzwerke in der Zielregion erstellen können. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement über genügend Ressourcen verfügt, um das Hinzufügen virtueller Netzwerke für diesen Prozess zu unterstützen. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Vorbereiten der Verschiebung
In diesem Abschnitt bereiten Sie das virtuelle Netzwerk für die Verschiebung vor, indem Sie eine Resource Manager-Vorlage verwenden. Anschließend verschieben Sie das virtuelle Netzwerk mithilfe von Azure PowerShell-Befehlen in die Zielregion.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gehen Sie folgendermaßen vor, um das virtuelle Netzwerk zu exportieren und das virtuelle Zielnetzwerk mit PowerShell bereitzustellen:

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) bei Ihrem Azure-Abonnement an, und befolgen Sie dann die Anweisungen auf dem Bildschirm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Rufen Sie die Ressourcen-ID des virtuellen Netzwerks ab, das Sie in die Zielregion verschieben möchten, und platzieren Sie sie dann mithilfe von [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) in einer Variablen:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exportieren Sie das virtuelle Quellnetzwerk in eine JSON-Datei in dem Verzeichnis, in dem Sie den Befehl [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) ausführen:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. Die heruntergeladene Datei weist den gleichen Namen wie die Ressourcengruppe auf, aus der die Ressource exportiert wurde. Suchen Sie die Datei *\<resource-group-name>.json*, die Sie mit dem Befehl exportiert haben, und öffnen Sie sie dann im Editor:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. Um den Parameter des Namens des virtuellen Netzwerk zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Namens des virtuellen Quellnetzwerks in den Namen des virtuellen Zielnetzwerks. Schließen Sie den Namen unbedingt in Anführungszeichen ein.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. Zum Bearbeiten der Zielregion, in die das virtuelle Netzwerk verschoben wird, ändern Sie die Eigenschaft **location** unter „resources“:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. Zum Abrufen von Regionsstandortcodes können Sie das Azure PowerShell-Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) verwenden, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (Optional) Sie können abhängig von Ihren Anforderungen auch andere Parameter in der Datei *\<resource-group-name>.json* ändern:

    * **Adressraum**: Bevor Sie die Datei speichern, können Sie den Adressraum des virtuellen Netzwerks ändern, indem Sie den Abschnitt **resources** > **addressSpace** und die Eigenschaft **addressPrefixes** ändern:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Subnetz**: Sie können den Subnetznamen und den Adressraum des Subnetzes ändern oder hinzufügen, indem Sie den Abschnitt **subnets** der Datei ändern. Sie können den Namen des Subnetzes ändern, indem Sie die Eigenschaft **name** ändern. Sie können den Adressraum des Subnetzes ebenfalls ändern, indem Sie die Eigenschaft **addressPrefix** ändern:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Um das Adresspräfix zu ändern, bearbeiten Sie die Datei an zwei Stellen: im Code im vorherigen Abschnitt und im Abschnitt **type** des folgenden Codes. Ändern Sie die Eigenschaft **addressPrefix** im folgenden Code so, dass sie mit der Eigenschaft **addressPrefix** im Code im vorherigen Abschnitt übereinstimmt.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Speichern Sie die Datei *\<resource-group-name>.json*.

1. Erstellen Sie mithilfe von [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) eine Ressourcengruppe in der Zielregion für das virtuelle Zielnetzwerk, das bereitgestellt werden soll:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Stellen Sie die bearbeitete Datei *\<resource-group-name>.json* in der Ressourcengruppe bereit, die Sie im vorherigen Schritt mit [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) erstellt haben:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. Um zu überprüfen, ob die Ressourcen in der Zielregion erstellt wurden, verwenden Sie [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) und [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>Löschen des virtuellen Netzwerks oder der Ressourcengruppe 

Nachdem Sie das virtuelle Netzwerk bereitgestellt haben, löschen Sie zum Neuaufsetzen oder Verwerfen des virtuellen Netzwerks in der Zielregion die Ressourcengruppe, die Sie in der Zielregion erstellt haben. Das verschobene virtuelle Netzwerk wird dann gelöscht. 

Um die Ressourcengruppe zu entfernen, verwenden Sie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>Bereinigung

Führen Sie einen der folgenden Schritte aus, um die Änderungen zu übernehmen und die Verschiebung des virtuellen Netzwerks abzuschließen:

* Löschen Sie die Ressourcengruppe mit [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Löschen Sie das virtuelle Quellnetzwerk, indem Sie [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) verwenden:  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mithilfe von PowerShell ein virtuelles Netzwerk aus einer Region in eine andere verschoben und dann die nicht benötigten Quellressourcen bereinigt. Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie in folgenden Artikeln:

- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Verschieben von virtuellen Azure-Computern in eine andere Region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
