---
title: Verwalten von Webdatenverkehr – Azure PowerShell
description: Erfahren Sie, wie Sie unter Verwendung von Azure PowerShell ein Anwendungsgateway mit einer VM-Skalierungsgruppe zum Verwalten von Webdatenverkehr erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/19/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7e410218117aa0d21167b6d615a3835aeec470e7
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397109"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-azure-powershell"></a>Verwalten von Webdatenverkehr mit einem Anwendungsgateway per Azure PowerShell

Mit dem Anwendungsgateway wird der Webdatenverkehr mit Servern, die Sie verwalten, verwaltet und abgesichert. Sie können mit Azure PowerShell ein [Anwendungsgateway](overview.md) erstellen, das eine [VM-Skalierungsgruppe](../virtual-machine-scale-sets/overview.md) für Back-End-Server zum Verwalten des Webdatenverkehrs verwendet. In diesem Beispiel enthält die Skalierungsgruppe zwei VM-Instanzen, die zum standardmäßigen Back-End-Pool des Anwendungsgateways hinzugefügt werden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Einrichten des Netzwerks
* Erstellen eines Anwendungsgateways
* Erstellen einer VM-Skalierungsgruppe mit dem standardmäßigen Back-End-Pool

Sie können für dieses Verfahren auch die [Azure-Befehlszeilenschnittstelle](tutorial-manage-web-traffic-cli.md) verwenden, wenn Ihnen dies lieber ist.

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen 

Konfigurieren Sie mithilfe von [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) die Subnetze namens *myBackendSubnet* und *myAGSubnet*. Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) und den Subnetzkonfigurationen das virtuelle Netzwerk *myVNet*. Erstellen Sie abschließend mithilfe von [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) die öffentliche IP-Adresse mit dem Namen *myAGPublicIPAddress*. Diese Ressourcen werden verwendet, um Netzwerkkonnektivität für das Anwendungsgateway und die zugehörigen Ressourcen bereitzustellen.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

In diesem Abschnitt erstellen Sie Ressourcen, die das Anwendungsgateway unterstützen, und schließlich das Anwendungsgateway selbst. Die Ressourcen, die Sie erstellen, umfassen:

- *IP-Konfigurationen und Front-End-Port* : Ordnet das Subnetz, das Sie zuvor erstellt haben, dem Anwendungsgateway zu und weist einen Port für den Zugriff darauf zu.
- *Standardpool* : Alle Anwendungsgateways müssen mindestens einen Back-End-Pool mit Servern haben.
- *Standardlistener und Regel* : Der Standardlistener lauscht auf dem Port, der zugewiesen wurde, auf Datenverkehr, und die Standardregel sendet Datenverkehr an den Standardpool.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Erstellen der IP-Konfigurationen und des Front-End-Ports

Ordnen Sie das zuvor erstellte Subnetz *myAGSubnet* mithilfe von [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) dem Anwendungsgateway zu. Weisen Sie *myAGPublicIPAddress* mithilfe von [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) dem Anwendungsgateway zu.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>Erstellen des Back-End-Pools und der Einstellungen

Erstellen Sie mit [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) den Back-End-Pool *appGatewayBackendPool* für das Anwendungsgateway. Konfigurieren Sie mit [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) die Einstellungen für die Back-End-Adresspools.

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Erstellen des Standardlisteners und einer Regel

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an den Back-End-Pool weiterleiten kann. In diesem Beispiel erstellen Sie einen grundlegenden Listener, der an der Stamm-URL auf Datenverkehr lauscht. 

Erstellen Sie einen Listener namens *mydefaultListener* mit [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener), der zuvor erstellten Front-End-Konfiguration und dem zuvor erstellten Front-End-Port. Für den Listener ist eine Regel erforderlich, damit bekannt ist, welcher Back-End-Pool für eingehenden Datenverkehr verwendet werden soll. Erstellen Sie mit [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) eine grundlegende Regel namens *rule1*.

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Sie haben die erforderlichen unterstützenden Ressourcen erstellt. Geben Sie nun mit [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) Parameter für das Anwendungsgateway an, und erstellen Sie es mit [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="create-a-virtual-machine-scale-set"></a>Erstellen einer Skalierungsgruppe für virtuelle Computer

In diesem Beispiel erstellen Sie eine VM-Skalierungsgruppe, um Server für den Back-End-Pool im Anwendungsgateway bereitzustellen. Sie weisen die Skalierungsgruppe dem Back-End-Pool zu, wenn Sie die IP-Einstellungen konfigurieren.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[0].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2_v2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Installieren von IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Verwenden Sie [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress), um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-manage-web-traffic-powershell/tutorial-iistest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Anwendungsgateway und alle zugehörigen Ressourcen nicht mehr benötigen, entfernen Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

[Einschränken des Webdatenverkehrs mit einer Web Application Firewall](../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md)