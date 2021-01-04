---
title: Verschieben einer Azure-Netzwerksicherheitsgruppe (NSG) in eine andere Azure-Region mit Azure PowerShell
description: Verwenden einer Azure Resource Manager-Vorlage, um eine Azure-Netzwerksicherheitsgruppe über Azure PowerShell aus einer Azure-Region in eine andere zu verschieben.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 04abc051cec8a6fb38ce6aa8f5347ae06cb8bd1d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019751"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Verschieben einer Azure-Netzwerksicherheitsgruppe (NSG) in eine andere Region mit Azure PowerShell

Es gibt verschiedene Szenarien, in denen Sie Ihre vorhandenen NSGs aus einer Region in eine andere verschieben möchten. Beispielsweise könnte es sein, dass Sie für Testzwecke eine NSG mit derselben Konfiguration und denselben Sicherheitsregeln erstellen möchten. Möglicherweise möchten Sie eine NSG auch im Rahmen der Planung einer Notfallwiederherstellung in eine andere Region verschieben.

Azure-Sicherheitsgruppen können nicht aus einer Region in eine andere verschoben werden. Sie können jedoch eine Azure Resource Manager-Vorlage verwenden, um die vorhandene Konfiguration und die vorhandenen Sicherheitsregeln einer NSG zu exportieren.  Anschließend können Sie die Ressource in einer anderen Region stagen, indem Sie die NSG in eine Vorlage exportieren, die Parameter so ändern, dass sie der Zielregion entsprechen, und die Vorlage dann in der neuen Region bereitstellen.  Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Exportieren von Ressourcengruppen in Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Voraussetzungen

- Vergewissern Sie sich, dass sich die Azure-Netzwerksicherheitsgruppe in der Azure-Region befindet, aus der Sie sie verschieben möchten.

- Azure-Netzwerksicherheitsgruppen können nicht zwischen Regionen verschoben werden.  Sie müssen die neue NSG Ressourcen in der Zielregion zuordnen.

- Damit Sie eine NSG-Konfiguration exportieren und eine Vorlage bereitstellen können, um eine NSG in einer anderen Region zu erstellen, benötigen Sie mindestens die Rolle „Netzwerkmitwirkender“.
   
- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout umfasst Load Balancer, Netzwerksicherheitsgruppen, öffentliche IP-Adressen und virtuelle Netzwerke, ohne darauf beschränkt zu sein.

- Vergewissern Sie sich, dass Sie mit Ihrem Azure-Abonnement NSGs in der verwendeten Zielregion erstellen können. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement genügend Ressourcen hat, um das Hinzufügen von NSGs für diesen Prozess zu unterstützen.  Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
In den folgenden Schritten wird gezeigt, wie Sie eine Netzwerksicherheitsgruppe vorbereiten, damit deren Konfiguration und Sicherheitsregeln mit einer Resource Manager-Vorlage verschoben werden können, und wie Sie die NSG-Konfiguration und -Sicherheitsregeln mit Azure PowerShell in die Zielregion verschieben.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportieren der Vorlage und Bereitstellen über ein Skript

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Verwenden Sie [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0), um die Ressourcen-ID der NSG, die Sie in die Zielregion verschieben möchten, abzurufen und zu einer Variablen zuzuweisen:

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportieren Sie die Quell-NSG in eine JSON-Datei in dem Verzeichnis, in dem Sie den Befehl [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) ausführen:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. Die heruntergeladene Datei wird nach der Ressourcengruppe benannt, aus der die Ressource exportiert wurde.  Suchen Sie nach der Datei, die mit dem Befehl exportiert wurde und den Namen **\<resource-group-name>.json** hat, und öffnen Sie sie in einem Editor Ihrer Wahl:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. Um den Parameter für den NSG-Namen zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Namens der Quell-NSG in den Namen Ihrer Ziel-NSG, wobei Sie den Namen in Anführungszeichen einschließen müssen:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. Um die Zielregion zu bearbeiten, in die die NSG-Konfiguration und -Sicherheitsregeln verschoben werden, ändern Sie die Eigenschaft **location** unter **resources**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. Zum Abrufen von Regionsstandortcodes können Sie das Azure PowerShell-Cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) verwenden, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Sie können wahlweise auch andere Parameter in **\<resource-group-name>.json** ändern, die abhängig von Ihren Anforderungen optional sind:

    * **Sicherheitsregeln:** Sie können festlegen, welche Regeln in der Ziel-NSG bereitgestellt werden, indem Sie dem Abschnitt **securityRules** in der Datei **\<resource-group-name>.json** Regeln hinzufügen oder Regeln aus diesem Abschnitt entfernen:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        Um das Hinzufügen oder Entfernen von Regeln in der Ziel-NSG abzuschließen, müssen Sie außerdem die benutzerdefinierten Regeltypen am Ende der Datei **\<resource-group-name>.json** im Format des folgenden Beispiels bearbeiten:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Speichern Sie die Datei **\<resource-group-name>.json**.

10. Erstellen Sie mithilfe von [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) eine Ressourcengruppe in der Zielregion für die Ziel-NSG, die bereitgestellt werden soll:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Stellen Sie die bearbeitete Datei **\<resource-group-name>.json** in der Ressourcengruppe bereit, die Sie im vorherigen Schritt mit [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) erstellt haben:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Um zu überprüfen, ob die Ressourcen in der Zielregion erstellt wurden, verwenden Sie [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) und [Get-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/get-aznetworksecuritygroup?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwerfen 

Wenn Sie nach der Bereitstellung neu starten oder die NSG im Ziel verwerfen möchten, löschen Sie die im Ziel erstellte Ressourcengruppe. Die verschobene NSG wird dann gelöscht.  Um die Ressourcengruppe zu entfernen, verwenden Sie [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>Bereinigung

Um die Änderungen zu übernehmen und die Verschiebung der NSG abzuschließen, löschen Sie die Quell-NSG oder -Ressourcengruppe mit [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) oder [Remove-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworksecuritygroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure-Netzwerksicherheitsgruppe aus einer Region in eine andere verschoben und die Quellressourcen bereinigt.  Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Verschieben virtueller Azure-Computer in eine andere Region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
