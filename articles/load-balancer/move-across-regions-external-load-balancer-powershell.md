---
title: Verschieben des externen Azure Load Balancers in eine andere Azure-Region mit Azure PowerShell
description: Verwenden einer Azure Resource Manager-Vorlage, um den externen Azure Load Balancer mit Azure PowerShell aus einer Azure-Region in eine andere zu verschieben.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: e43d8f1050f6b2b458c0926c674c05f7f18edc63
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018510"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Verschieben des externen Azure Load Balancers in eine andere Region mit Azure PowerShell

Es gibt verschiedene Szenarien, in denen Sie einen vorhandenen externen Azure Load Balancer aus einer Region in eine andere verschieben möchten. Beispielsweise können Sie für Testzwecke einen externen Load Balancer mit der gleichen Konfiguration erstellen. Möglicherweise möchten Sie einen externen Load Balancer auch im Rahmen der Planung der Notfallwiederherstellung in eine andere Region verschieben.

Externe Azure Load Balance können nicht aus einer Region in eine andere verschoben werden. Sie können jedoch eine Azure Resource Manager-Vorlage verwenden, um die vorhandene Konfiguration und die öffentliche IP-Adresse eines externen Load Balancers zu exportieren.  Anschließend können Sie die Ressource in einer anderen Region bereitstellen, indem Sie den Load Balancer und die öffentliche IP-Adresse in eine Vorlage exportieren, die Parameter so ändern, dass Sie der Zielregion entsprechen, und die Vorlagen dann in der neuen Region bereitstellen.  Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Exportieren von Ressourcengruppen in Vorlagen](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich der externe Azure Load Balancer in der Azure-Region befindet, aus der Sie ihn verschieben möchten.

- Externe Azure Load Balancer können nicht zwischen Regionen verschoben werden.  Sie müssen den neuen Load Balancer Ressourcen in der Zielregion zuordnen.

- Zum Exportieren der Konfiguration eines externen Load Balancers und Bereitstellen einer Vorlage zum Erstellen eines externen Load Balancers in einer anderen Region benötigen Sie mindestens die Rolle „Netzwerkmitwirkender“.
   
- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout umfasst Load Balancer, Netzwerksicherheitsgruppen, öffentliche IP-Adressen und virtuelle Netzwerke, ohne darauf beschränkt zu sein.

- Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen externer Load Balancer in der verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement über genügend Ressourcen verfügt, um das Hinzufügen von Load Balancern für diesen Prozess zu unterstützen.  Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
Die folgenden Schritte zeigen, wie Sie den externen Load Balancer für die Verschiebung mit einer Resource Manager-Vorlage vorbereiten und die Konfiguration des externen Load Balancers dann mithilfe von Azure PowerShell in die Zielregion verschieben.  Im Rahmen dieses Vorgangs muss die Konfiguration der öffentlichen IP-Adresse des externen Load Balancers eingeschlossen und vor dessen Verschieben zuerst durchgeführt werden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>Exportieren der Vorlage für die öffentliche IP-Adresse und Bereitstellen aus Azure PowerShell

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. Rufen Sie die Ressourcen-ID der öffentlichen IP-Adresse ab, die Sie in die Zielregion verschieben möchten, und platzieren Sie sie mithilfe von [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0) in einer Variablen:

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportieren Sie die öffentliche IP-Quelladresse in eine JSON-Datei in das Verzeichnis, in dem Sie den Befehl [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) ausführen:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. Die heruntergeladene Datei wird nach der Ressourcengruppe benannt, aus der die Ressource exportiert wurde.  Suchen Sie nach der Datei, die mit dem Befehl exportiert wurde und den Namen **\<resource-group-name>.json** hat, und öffnen Sie sie in einem Editor Ihrer Wahl:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Um den Parameter für den Namen der öffentlichen IP-Adresse zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Namens der öffentlichen IP-Adresse der Quelle in den Namen der öffentlichen IP-Adresse des Ziels. Stellen Sie sicher, dass der Name in Anführungszeichen eingeschlossen ist:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. Zum Bearbeiten der Zielregion, in die die öffentliche IP-Adresse verschoben wird, ändern Sie die Eigenschaft **location** unter „resources“:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. Zum Abrufen von Regionsstandortcodes können Sie das Azure PowerShell-Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) verwenden, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. Sie können wahlweise auch andere Parameter in der Vorlage ändern, die abhängig von Ihren Anforderungen optional sind:

    * **SKU**: Sie können die SKU der öffentlichen IP-Adresse in der Konfiguration von „standard“ in „basic“ oder von „basic“ in „standard“ ändern, indem Sie die Eigenschaft **sku** > **name** in der Datei **\<resource-group-name>.json** ändern:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Weitere Informationen zu den Unterschieden zwischen den SKUs „basic“ und „standard“ für öffentliche IP-Adressen finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](../virtual-network/virtual-network-public-ip-address.md).

    * **Zuweisungsmethode für öffentliche IP** und **Leerlauftimeout**: Sie können beide Optionen in der Vorlage ändern, indem Sie die Eigenschaft **publicIPAllocationMethod** aus **Dynamic** in **Static** oder aus **Static** in **Dynamic** ändern. Das Leerlauftimeout kann geändert werden, indem Sie die Eigenschaft **idleTimeoutInMinutes** auf den gewünschten Wert festlegen.  Der Standardwert ist **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        Weitere Informationen zu den Zuweisungsmethoden und den Werten für das Leerlauftimeout finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](../virtual-network/virtual-network-public-ip-address.md).


9. Speichern Sie die Datei **\<resource-group-name>.json**.

10. Erstellen Sie mithilfe von [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) eine Ressourcengruppe in der Zielregion für die öffentliche IP-Zieladresse, die bereitgestellt werden soll.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Stellen Sie die bearbeitete Datei **\<resource-group-name>.json** in der Ressourcengruppe bereit, die Sie im vorherigen Schritt mit [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0) erstellt haben:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. Um zu überprüfen, ob die Ressourcen in der Zielregion erstellt wurden, verwenden Sie [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) und [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportieren der Vorlage des externen Load Balancers und Bereitstellen aus Azure PowerShell

1. Melden Sie sich mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Rufen Sie die Ressourcen-ID des externen Load Balancers ab, den Sie in die Zielregion verschieben möchten, und platzieren Sie sie mithilfe von [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0) in einer Variablen:

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportieren Sie die Quellkonfiguration des externen Load Balancers in eine JSON-Datei in das Verzeichnis, in dem Sie den Befehl [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) ausführen:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. Die heruntergeladene Datei wird nach der Ressourcengruppe benannt, aus der die Ressource exportiert wurde.  Suchen Sie nach der Datei, die mit dem Befehl exportiert wurde und den Namen **\<resource-group-name>.json** hat, und öffnen Sie sie in einem Editor Ihrer Wahl:
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. Um den Parameter für den Namen des externen Load Balancers zu bearbeiten, ändern Sie die Eigenschaft **defaultValue** des Quellnamens des externen Load Balancers in den Namen Ihres externen Ziel-Load Balancers. Stellen Sie dabei sicher, dass der Name in Anführungszeichen eingeschlossen wird:

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  Wenn Sie den Wert der zuvor verschobenen öffentlichen IP-Adresse des Ziels bearbeiten möchten, müssen Sie zunächst die Ressourcen-ID abrufen, kopieren und anschließend in die Datei **\<resource-group-name>.json** einfügen.  Um die ID abzurufen, verwenden Sie [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0):

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    Geben Sie die Variable ein, und drücken Sie die EINGABETASTE, um die Ressourcen-ID anzuzeigen.  Markieren Sie den Pfad der ID, und kopieren Sie ihn in die Zwischenablage:

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  Fügen Sie in der Datei **\<resource-group-name>.json** die **Ressourcen-ID** aus der Variablen anstelle des Werts **defaultValue** im zweiten Parameter als externe ID der öffentlichen IP-Adresse ein. Stellen Sie dabei sicher, dass Sie den Pfad in Anführungszeichen einschließen:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  Wenn Sie für den Load Balancer NAT-Ausgangsregeln konfiguriert haben, ist in dieser Datei ein dritter Eintrag für die externe ID der ausgehenden öffentlichen IP-Adresse vorhanden.  Wiederholen Sie die oben genannten Schritte in der **Zielregion**, um die ID der ausgehenden öffentlichen IP-Adresse zu ermitteln. Fügen Sie diesen Eintrag in die Datei **\<resource-group-name>.json** ein:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. Ändern Sie zum Bearbeiten der Zielregion, in die die Konfiguration des externen Load Balancers verschoben wird, in der Datei **\<resource-group-name>.json** unter **resources** die Eigenschaft **location**:

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
    
    * **SKU**: Sie können die SKU des externen Load Balancers in der Konfiguration von „Standard“ in „Basic“ ändern (oder umgekehrt), indem Sie in der Datei **\<resource-group-name>.json** die Eigenschaft **sku** > **name** ändern:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Weitere Informationen zu den Unterschieden zwischen Load Balancern der SKU „basic“ und „standard“ finden Sie unter [Übersicht über Azure Load Balancer Standard](./load-balancer-overview.md).

    * **Lastenausgleichsregeln**: Sie können Lastenausgleichsregeln in der Konfiguration hinzufügen oder entfernen, indem Sie Einträge im Abschnitt **loadBalancingRules** in der Datei **\<resource-group-name>.json** hinzufügen oder entfernen:

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

    * **Tests**: Sie können einen Test des Load Balancers in der Konfiguration hinzufügen oder entfernen, indem Sie Einträge im Abschnitt **Tests** in der Datei **\<resource-group-name>.json** hinzufügen oder entfernen:

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

    * **NAT-Regeln für eingehenden Datenverkehr**: Sie können NAT-Regeln für eingehenden Datenverkehr des Load Balancers hinzufügen oder entfernen, indem Sie Einträge im Abschnitt **inboundNatRules** in der Datei **\<resource-group-name>.json** hinzufügen oder entfernen:

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
        Damit das Hinzufügen oder Entfernen einer NAT-Regel für eingehenden Datenverkehr abgeschlossen ist, muss die Regel als Eigenschaft vom Typ **type** am Ende der Datei **\<resource-group-name>.json** vorhanden sein oder daraus entfernt werden:

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

    * **Regeln für ausgehenden Datenverkehr**: Sie können Regeln für ausgehenden Datenverkehr in der Konfiguration hinzufügen oder entfernen, indem Sie die Eigenschaft **outboundRules** in der Datei **\<resource-group-name>.json** bearbeiten:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Weitere Informationen zu Regeln für ausgehenden Datenverkehr finden Sie unter [Load Balancer-Ausgangsregeln](./load-balancer-outbound-connections.md#outboundrules).

13. Speichern Sie die Datei **\<resource-group-name>.json**.
    
10. Erstellen Sie mithilfe von [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) eine Ressourcengruppe in der Zielregion für den externen Ziel-Load Balancer, der bereitgestellt werden soll. Die oben genannte vorhandene Ressourcengruppe kann im Rahmen dieses Prozesses ebenfalls wiederverwendet werden:
    
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

Wenn Sie nach der Bereitstellung die öffentliche IP-Adresse und den Load Balancer im Ziel neu starten oder verwerfen möchten, löschen Sie die im Ziel erstellte Ressourcengruppe. Die verschobene öffentliche IP-Adresse und der Load Balancer werden dann gelöscht.  Um die Ressourcengruppe zu entfernen, verwenden Sie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>Bereinigung

Um die Änderungen zu übernehmen und die Verschiebung der NSG abzuschließen, löschen Sie die Quell-NSG oder die Ressourcengruppe, verwenden Sie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) oder [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) und [Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0).

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure-Netzwerksicherheitsgruppe aus einer Region in eine andere verschoben und die Quellressourcen bereinigt.  Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../site-recovery/azure-to-azure-tutorial-migrate.md)