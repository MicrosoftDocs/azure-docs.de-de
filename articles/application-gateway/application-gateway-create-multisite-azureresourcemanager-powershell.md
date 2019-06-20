---
title: Erstellen eines Anwendungsgateways zum Hosten mehrerer Websites – Azure PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit Azure PowerShell ein Anwendungsgateway erstellen, mit dem mehrere Websites gehostet werden.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 11962d8958811bc87fb94dc6fe78cb760fdaa20e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729707"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-azure-powershell"></a>Erstellen eines Anwendungsgateways zum Hosten mehrerer Websites mit Azure PowerShell

Sie können mit Azure PowerShell ein [Hosting mehrerer Websites](application-gateway-multi-site-overview.md) konfigurieren, wenn Sie ein [Anwendungsgateway](application-gateway-introduction.md) erstellen. In diesem Tutorial erstellen Sie Back-End-Pools mithilfe von VM-Skalierungsgruppen. Anschließend konfigurieren Sie Listener und Regeln basierend auf Domänen in Ihrem Besitz, um sicherzustellen, dass Webdatenverkehr von geeigneten Servern in den Pools empfangen wird. In diesem Tutorial wird vorausgesetzt, dass Sie mehrere Domänen besitzen und die Beispiele zu *www.contoso.com* und *www.fabrikam.com* verwenden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Erstellen der Listener und Routingregeln
> * Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne

![Beispiel zum Routing für mehrere Websites](./media/application-gateway-create-multisite-azureresourcemanager-powershell/scenario.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

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
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

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

### <a name="create-the-backend-pools-and-settings"></a>Erstellen der Back-End-Pools und Einstellungen

Erstellen Sie mit [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) die Back-End-Pools *contosoPool* und *fabrikamPool* für das Anwendungsgateway. Konfigurieren Sie mit [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) die Einstellungen für den Pool.

```azurepowershell-interactive
$contosoPool = New-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool 
$fabrikamPool = New-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listeners-and-rules"></a>Erstellen der Listener und Regeln

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an die Back-End-Pools weiterleiten kann. In diesem Tutorial erstellen Sie Listener für jede Ihrer zwei Domänen. In diesem Beispiel werden Listener für die Domänen *www.contoso.com* und *www.fabrikam.com* erstellt.

Erstellen Sie mit [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) die Listener *contosoListener* und *fabrikamListener*. Verwenden Sie hierbei die zuvor erstellte Front-End-Konfiguration und den zuvor erstellten Front-End-Port. Für die Listener sind Regeln erforderlich, damit bekannt ist, welcher Back-End-Pool für den eingehenden Datenverkehr verwendet werden soll. Erstellen Sie mithilfe von [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) Basisregeln namens *contosoRule* und *fabrikamRule*.

```azurepowershell-interactive
$contosolistener = New-AzApplicationGatewayHttpListener `
  -Name contosoListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.contoso.com"
$fabrikamlistener = New-AzApplicationGatewayHttpListener `
  -Name fabrikamListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -HostName "www.fabrikam.com"
$contosoRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name contosoRule `
  -RuleType Basic `
  -HttpListener $contosoListener `
  -BackendAddressPool $contosoPool `
  -BackendHttpSettings $poolSettings
$fabrikamRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name fabrikamRule `
  -RuleType Basic `
  -HttpListener $fabrikamListener `
  -BackendAddressPool $fabrikamPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Sie haben die erforderlichen unterstützenden Ressourcen erstellt. Geben Sie nun mit [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) Parameter für das Anwendungsgateway *myAppGateway* an, und erstellen Sie es mit [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $contosoPool, $fabrikamPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $contosoListener, $fabrikamListener `
  -RequestRoutingRules $contosoRule, $fabrikamRule `
  -Sku $sku
```

## <a name="create-virtual-machine-scale-sets"></a>Erstellen von VM-Skalierungsgruppen

In diesem Beispiel erstellen Sie zwei VM-Skalierungsgruppen, die die zwei von Ihnen erstellten Back-End-Pools unterstützen. Die erstellten Skalierungsgruppen erhalten die Namen *myvmss1* und *myvmss2*. Jede Skalierungsgruppe enthält zwei VM-Instanzen, auf denen Sie IIS installieren. Sie weisen die Skalierungsgruppe dem Back-End-Pool zu, wenn Sie die IP-Einstellungen konfigurieren.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$contosoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name contosoPool `
  -ApplicationGateway $appgw
$fabrikamPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name fabrikamPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 2; $i++)
{
  if ($i -eq 1) 
  {
    $poolId = $contosoPool.Id
  }
  if ($i -eq 2)
  {
    $poolId = $fabrikamPool.Id
  }
  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId
  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic
  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i
  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig
  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Installieren von IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 2; $i++)
{
  $vmss = Get-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings
  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="create-cname-record-in-your-domain"></a>Erstellen eines CNAME-Eintrags in Ihrer Domäne

Nachdem das Anwendungsgateway mit der zugehörigen öffentlichen IP-Adresse erstellt wurde, können Sie die DNS-Adresse abrufen und zum Erstellen eines CNAME-Eintrags in Ihrer Domäne verwenden. Um die DNS-Adresse des Anwendungsgateways abzurufen, können Sie [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) verwenden. Kopieren Sie den *fqdn*-Wert der DNSSettings, und verwenden Sie ihn als Wert für den erstellten CNAME-Eintrag. Die Verwendung von A-Einträgen wird nicht empfohlen, weil sich die VIP beim Neustart des Anwendungsgateways möglicherweise ändert.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Geben Sie Ihren Domänennamen in die Adressleiste Ihres Browsers ein. Z.B. http://www.contoso.com.

![Testen der Contoso-Website im Anwendungsgateway](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest.png)

Ändern Sie die Adresse in Ihre andere Domäne. Die Ausgabe sollte in etwa wie folgt aussehen:

![Testen der Fabrikam-Website im Anwendungsgateway](./media/application-gateway-create-multisite-azureresourcemanager-powershell/application-gateway-iistest2.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten des Netzwerks
> * Erstellen eines Anwendungsgateways
> * Erstellen der Listener und Routingregeln
> * Erstellen von VM-Skalierungsgruppen mit den Back-End-Pools
> * Erstellen eines CNAME-Eintrags in Ihrer Domäne

> [!div class="nextstepaction"]
> [Erfahren Sie mehr darüber, was Sie mit dem Anwendungsgateway tun können.](application-gateway-introduction.md)
