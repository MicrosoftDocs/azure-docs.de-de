---
title: Einschränken des Webdatenverkehrs mit einer Web Application Firewall – Azure PowerShell
description: Erfahren Sie, wie Sie mit Azure PowerShell Webdatenverkehr mit einer Web Application Firewall für ein Anwendungsgateway einschränken.
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cb30f738d8a93f53c80697e7475b4f12f5bff63c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "66133338"
---
# <a name="enable-web-application-firewall-using-azure-powershell"></a>Aktivieren der Web Application Firewall mit Azure PowerShell

> [!div class="op_single_selector"]
>
> - [Azure-Portal](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](tutorial-restrict-web-traffic-cli.md)
>
> 

Sie können den Datenverkehr mit einer [Web Application Firewall](waf-overview.md) (WAF) für ein [Anwendungsgateway](overview.md) einschränken. Die WAF verwendet [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-Regeln, um Ihre Anwendung zu schützen. Diese Regeln beinhalten den Schutz vor Angriffen z.B. durch Einschleusung von SQL-Befehlen, siteübergreifendes Scripting und Sitzungsübernahmen. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways mit aktivierter WAF
> * Erstellen einer Skalierungsgruppe für virtuelle Computer
> * Erstellen eines Speicherkontos und Konfigurieren der Diagnose

![Web Application Firewall – Beispiel](./media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Sie können für dieses Tutorial auch die [Azure-Befehlszeilenschnittstelle](tutorial-restrict-web-traffic-cli.md) verwenden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe.  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen 

Erstellen Sie mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) die Subnetzkonfigurationen *myBackendSubnet* und *myAGSubnet*. Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) und den Subnetzkonfigurationen das virtuelle Netzwerk *myVNet*. Erstellen Sie abschließend mithilfe von [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) die öffentliche IP-Adresse mit dem Namen *myAGPublicIPAddress*. Diese Ressourcen werden verwendet, um Netzwerkkonnektivität für das Anwendungsgateway und die zugehörigen Ressourcen bereitzustellen.

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
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

In diesem Abschnitt erstellen Sie Ressourcen, die das Anwendungsgateway unterstützen, und schließlich das Anwendungsgateway selbst sowie eine WAF (Web Application Firewall). Die Ressourcen, die Sie erstellen, umfassen:

- *IP-Konfigurationen und Front-End-Port*: Ordnet das Subnetz, das Sie zuvor erstellt haben, dem Anwendungsgateway zu und weist einen Port für den Zugriff darauf zu.
- *Standardpool*: Alle Anwendungsgateways müssen mindestens einen Back-End-Pool mit Servern haben.
- *Standardlistener und Regel*: Der Standardlistener lauscht auf dem Port, der zugewiesen wurde, auf Datenverkehr, und die Standardregel sendet Datenverkehr an den Standardpool.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Erstellen der IP-Konfigurationen und des Front-End-Ports

Ordnen Sie das zuvor erstellte Subnetz *myAGSubnet* mithilfe von [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) dem Anwendungsgateway zu. Weisen Sie *myAGPublicIPAddress* mithilfe von [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) dem Anwendungsgateway zu.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

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

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an die Back-End-Adresspools weiterleiten kann. In diesem Beispiel erstellen Sie einen grundlegenden Listener, der an der Stamm-URL auf Datenverkehr lauscht. 

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

### <a name="create-the-application-gateway-with-the-waf"></a>Erstellen des Anwendungsgateways mit der WAF

Sie haben die erforderlichen unterstützenden Ressourcen erstellt. Geben Sie nun mithilfe von [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) Parameter für das Anwendungsgateway an. Geben Sie mithilfe von [New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) die WAF-Konfiguration an. Erstellen Sie dann mithilfe von [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) das Anwendungsgateway namens *myAppGateway*.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_Medium `
  -Tier WAF `
  -Capacity 2

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration `
  -Enabled $true `
  -FirewallMode "Detection"

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
  -Sku $sku `
  -WebApplicationFirewallConfig $wafConfig
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
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
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

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Erstellen eines Speicherkontos und Konfigurieren der Diagnose

In diesem Tutorial verwendet das Anwendungsgateway ein Speicherkonto, um Daten zum Zweck der Erkennung und Prävention zu speichern. Sie können auch Azure Monitor-Protokolle oder Event Hub verwenden, um Daten aufzuzeichnen.

### <a name="create-the-storage-account"></a>Erstellen des Speicherkontos

Erstellen Sie mithilfe von *New-AzStorageAccount* ein Speicherkonto mit dem Namen [myagstore1](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>Konfigurieren der Diagnose

Verwenden Sie [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting), um die Diagnose so zu konfigurieren, dass Daten in den Protokollen „ApplicationGatewayAccessLog“, „ApplicationGatewayPerformanceLog“ und „ApplicationGatewayFirewallLog“ aufgezeichnet werden.

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Sie können [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) verwenden, um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testen der Basis-URL im Anwendungsgateway](./media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Anwendungsgateway und alle zugehörigen Ressourcen nicht mehr benötigen, entfernen Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways mit aktivierter WAF
> * Erstellen einer Skalierungsgruppe für virtuelle Computer
> * Erstellen eines Speicherkontos und Konfigurieren der Diagnose

> [!div class="nextstepaction"]
> [Erstellen eines Anwendungsgateways mit SSL-Beendigung](./tutorial-ssl-powershell.md)
