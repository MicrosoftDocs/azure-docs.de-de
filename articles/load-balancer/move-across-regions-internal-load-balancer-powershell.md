---
title: Verschieben des internen Azure Load Balancers in eine andere Azure-Region mit Azure PowerShell
description: Verwenden einer Azure Resource Manager-Vorlage, um den internen Azure Load Balancer mit Azure PowerShell aus einer Azure-Region in eine andere zu verschieben
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 73a9356de555e33996b92f05c3bbbabb651f1c9f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014226"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>Verschieben des internen Azure Load Balancers in eine andere Region mit PowerShell

Es gibt verschiedene Szenarien, in denen Sie einen vorhandenen Azure Load Balancer aus einer Region in eine andere verschieben möchten. Beispielsweise können Sie einen internen Load Balancer mit der gleichen Konfiguration für Testzwecke erstellen. Möglicherweise möchten Sie einen internen Load Balancer auch im Rahmen der Planung der Notfallwiederherstellung in eine andere Region verschieben.

Interne Azure Load Balance können nicht aus einer Region in eine andere verschoben werden. Sie können jedoch eine Azure Resource Manager-Vorlage verwenden, um die vorhandene Konfiguration und das virtuelle Netzwerk eines internen Load Balancers zu exportieren.  Anschließend können Sie die Ressource in einer anderen Region bereitstellen, indem Sie den Load Balancer und das virtuelle Netzwerk in eine Vorlage exportieren, die Parameter so ändern, dass Sie der Zielregion entsprechen, und die Vorlagen dann in der neuen Region bereitstellen.  Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Exportieren von Ressourcengruppen in Vorlagen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich der interne Azure Load Balancer in der Azure-Region befindet, aus der Sie ihn verschieben möchten.

- Interne Azure Load Balancer können nicht zwischen Regionen verschoben werden.  Sie müssen den neuen Load Balancer Ressourcen in der Zielregion zuordnen.

- Zum Exportieren einer internen Load Balancer-Konfiguration und Bereitstellen einer Vorlage zum Erstellen eines internen Load Balancers in einer anderen Region benötigen Sie die Rolle „Netzwerkmitwirkender“ oder höher.
   
- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout umfasst Load Balancer, Netzwerksicherheitsgruppen, virtuelle Computer und virtuelle Netzwerke, kann sich aber auch auf weitere Komponenten beziehen.

- Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen interner Load Balancer in der verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement über genügend Ressourcen verfügt, um das Hinzufügen von Load Balancern für diesen Prozess zu unterstützen.  Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
Die folgenden Schritte zeigen, wie Sie den internen Load Balancer für die Verschiebung mit einer Resource Manager-Vorlage vorbereiten und die Konfiguration des internen Load Balancers dann mithilfe von Azure PowerShell in die Zielregion verschieben.  Im Rahmen dieses Vorgangs muss die virtuelle Netzwerkkonfiguration des internen Load Balancers eingeschlossen und vor dem Verschieben des internen Load Balancers zuerst durchgeführt werden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Exportieren der Vorlage für virtuelle Netzwerke und Bereitstellen aus Azure PowerShell

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  Rufen Sie die Ressourcen-ID des virtuellen Netzwerks ab, das Sie in die Zielregion verschieben möchten, und platzieren Sie sie mithilfe von [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0) in einer Variablen:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportieren Sie das virtuelle Quellnetzwerk in eine JSON-Datei in dem Verzeichnis, in dem Sie den Befehl [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) ausführen:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Die heruntergeladene Datei wird nach der Ressourcengruppe benannt, aus der die Ressource exportiert wurde.  Suchen Sie nach der Datei, die mit dem Befehl exportiert wurde und den Namen **\<resource-group-name>.json** hat, und öffnen Sie sie in einem Editor Ihrer Wahl:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Um den Parameter des Namens des virtuellen Netzwerk zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Namens des virtuellen Quellnetzwerks in den Namen des virtuellen Zielnetzwerks. Stellen Sie sicher, dass der Name in Anführungszeichen eingeschlossen ist:
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  Zum Bearbeiten der Zielregion, in die das VNET verschoben wird, ändern Sie die Eigenschaft **location** unter „resources“:

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
  
7. Zum Abrufen von Regionsstandortcodes können Sie das Azure PowerShell-Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) verwenden, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  Sie können auch andere Parameter in der **\<resource-group-name>.json**-Datei ändern, die abhängig von Ihren Anforderungen optional sind:

    * **Adressraum**: Der Adressraum des virtuellen Netzwerks kann vor dem Speichern geändert werden, indem Sie den Abschnitt **resources** > **addressSpace** und die Eigenschaft **addressPrefixes** in der Datei **\<resource-group-name>.json** ändern:

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

    * **Subnetz**: Der Subnetzname und der Subnetzadressraum können geändert oder ergänzt werden, indem Sie den Abschnitt **subnets** der Datei **\<resource-group-name>.json** ändern. Der Name des Subnetzes kann geändert werden, indem Sie die Eigenschaft **name** ändern. Der Subnetzadressraum kann geändert werden, indem Sie die Eigenschaft **addressPrefix** in der Datei **\<resource-group-name>.json** ändern:

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

         Die Datei **\<resource-group-name>.json** muss zum Ändern des Adresspräfixes an zwei Stellen bearbeitet werden: im oben aufgeführten Abschnitt und im unten aufgeführten Abschnitt **type**.  Ändern Sie die Eigenschaft **addressPrefix** so, dass Sie mit der oben genannten Eigenschaft übereinstimmt:

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

9.  Speichern Sie die Datei **\<resource-group-name>.json**.

10. Erstellen Sie mithilfe von [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) eine Ressourcengruppe in der Zielregion für das Ziel-VNET, das bereitgestellt werden soll.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Stellen Sie die bearbeitete Datei **\<resource-group-name>.json** in der Ressourcengruppe bereit, die Sie im vorherigen Schritt mit [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) erstellt haben:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. Um zu überprüfen, ob die Ressourcen in der Zielregion erstellt wurden, verwenden Sie [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) und [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportieren der Vorlage des internen Load Balancers und Bereitstellen aus Azure PowerShell

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Rufen Sie die Ressourcen-ID des internen Load Balancers ab, den Sie in die Zielregion verschieben möchten, und platzieren Sie sie mithilfe von [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0) in einer Variablen:

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportieren Sie die Quellkonfiguration des internen Load Balancers in eine JSON-Datei in das Verzeichnis, in dem Sie den Befehl [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) ausführen:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. Die heruntergeladene Datei wird nach der Ressourcengruppe benannt, aus der die Ressource exportiert wurde.  Suchen Sie nach der Datei, die mit dem Befehl exportiert wurde und den Namen **\<resource-group-name>.json** hat, und öffnen Sie sie in einem Editor Ihrer Wahl:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Um den Parameter für den Namen des internen Load Balancers zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Quellnamens des internen Load Balancers in den Namen Ihres internen Ziel-Load Balancers. Stellen Sie dabei sicher, dass der Name in Anführungszeichen eingeschlossen wird:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. Wenn Sie den Wert des zuvor verschobenen virtuellen Zielnetzwerks bearbeiten möchten, müssen Sie zunächst die Ressourcen-ID abrufen und kopieren und sie dann in die Datei **\<resource-group-name>.json** einfügen.  Um die ID abzurufen, verwenden Sie [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0):
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Geben Sie die Variable ein, und drücken Sie die EINGABETASTE, um die Ressourcen-ID anzuzeigen.  Markieren Sie den Pfad der ID, und kopieren Sie ihn in die Zwischenablage:

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  Fügen Sie in der Datei **\<resource-group-name>.json** die **Ressourcen-ID** aus der Variablen anstelle des **defaultValue** im zweiten Parameter als ID des virtuellen Zielnetzwerks ein, und stellen Sie dabei sicher, dass Sie den Pfad in Anführungszeichen einschließen:
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. Um die Zielregion zu bearbeiten, in die die Konfiguration des internen Lastenausgleichsmoduls verschoben wird, ändern Sie in der Datei **\<resource-group-name>.json** unter **resources** die Eigenschaft **location**:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. Zum Abrufen von Regionsstandortcodes können Sie das Azure PowerShell-Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) verwenden, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Sie können wahlweise auch andere Parameter in der Vorlage ändern, die abhängig von Ihren Anforderungen optional sind:
    
    * **SKU**: Sie können die SKU des internen Lastenausgleichsmoduls in der Konfiguration von „Standard“ in „Basic“ ändern (oder umgekehrt), indem Sie in der Datei **\<resource-group-name>.json** die Eigenschaft **sku** > **name** ändern:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Weitere Informationen zu den Unterschieden zwischen Load Balancern der SKU „basic“ und „standard“ finden Sie unter [Übersicht über Azure Load Balancer Standard](./load-balancer-overview.md).

    * **Lastenausgleichsregeln**: Sie können Lastenausgleichsregeln zur Konfiguration hinzufügen oder daraus entfernen, indem Sie in der Datei **\<resource-group-name>.json** im Abschnitt **loadBalancingRules** Einträge hinzufügen oder entfernen:

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Weitere Informationen zu Lastenausgleichsregeln finden Sie unter [Was ist Azure Load Balancer?](./load-balancer-overview.md)

    * **Tests**: Sie können einen Test des Lastenausgleichsmoduls zur Konfiguration hinzufügen oder daraus entfernen, indem Sie in der Datei **\<resource-group-name>.json** im Abschnitt **Tests** Einträge hinzufügen oder entfernen:

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Weitere Informationen zu Azure Load Balancer-Integritätstests finden Sie unter [Load Balancer-Integritätstests](./load-balancer-custom-probe-overview.md).

    * **NAT-Regeln für eingehenden Datenverkehr**: Sie können NAT-Regeln für eingehenden Datenverkehr des Lastenausgleichsmoduls hinzufügen oder entfernen, indem Sie in der Datei **\<resource-group-name>.json** im Abschnitt **inboundNatRules** Einträge hinzufügen oder entfernen:

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Um das Hinzufügen oder Entfernen einer NAT-Regel für eingehenden Datenverkehr abzuschließen, müssen Sie die Regel als Eigenschaft vom Typ **type** am Ende der Datei **\<resource-group-name>.json** hinzufügen oder daraus entfernen:

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Weitere Informationen zu NAT-Regeln für eingehenden Datenverkehr finden Sie unter [Was ist Azure Load Balancer?](./load-balancer-overview.md)
    
13. Speichern Sie die Datei **\<resource-group-name>.json**.
    
10. Erstellen Sie mithilfe von [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) eine Ressourcengruppe in der Zielregion für den internen Ziel-Load Balancer, der bereitgestellt werden soll. Die oben genannte vorhandene Ressourcengruppe kann im Rahmen dieses Prozesses ebenfalls wiederverwendet werden:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Stellen Sie die bearbeitete Datei **\<resource-group-name>.json** in der Ressourcengruppe bereit, die Sie im vorherigen Schritt mit [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) erstellt haben:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Um zu überprüfen, ob die Ressourcen in der Zielregion erstellt wurden, verwenden Sie [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) und [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Verwerfen 

Wenn Sie nach der Bereitstellung das virtuelle Netzwerk und den Load Balancer im Ziel neu starten oder verwerfen möchten, löschen Sie die im Ziel erstellte Ressourcengruppe. Das verschobene virtuelle Netzwerk und der Load Balancer werden dann gelöscht.  Um die Ressourcengruppe zu entfernen, verwenden Sie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Bereinigung

Um die Änderungen zu übernehmen und die Verschiebung der NSG abzuschließen, löschen Sie die Quell-NSG oder die Ressourcengruppe, verwenden Sie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) oder [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) und [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0).

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen internen Azure Load Balancer aus einer Region in eine andere verschoben und die Quellressourcen bereinigt.  Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../site-recovery/azure-to-azure-tutorial-migrate.md)