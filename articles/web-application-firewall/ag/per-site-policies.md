---
title: Konfigurieren von websitespezifischen WAF-Richtlinien mithilfe von PowerShell
titleSuffix: Azure Web Application Firewall
description: Hier erfahren Sie, wie Sie mithilfe von Azure PowerShell websitespezifische Web Application Firewall-Richtlinien in einem Anwendungsgateway konfigurieren.
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 12/08/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: f282cfa6347dd6e6d591ac5cd8b1785e405c6c02
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905977"
---
# <a name="configure-per-site-waf-policies-using-azure-powershell"></a>Konfigurieren von websitespezifischen WAF-Richtlinien mithilfe von Azure PowerShell

WAF-Einstellungen (Web Application Firewall) sind in WAF-Richtlinien enthalten. Zum Ändern Ihrer WAF-Konfiguration bearbeiten Sie die WAF-Richtlinie.

Die Richtlinien und alle Einstellungen werden global wirksam, wenn sie Ihrem Anwendungsgateway zugeordnet werden. Wenn Sie also über fünf Websites hinter Ihrer Web Application Firewall verfügen, werden alle fünf durch dieselbe WAF-Richtlinie geschützt. Dies ist eine sehr gute Lösung, wenn Sie für alle Websites die gleichen Sicherheitseinstellungen benötigen. Sie können WAF-Richtlinien aber auch auf einzelne Listener anwenden, um eine websitespezifische WAF-Konfiguration zu ermöglichen.

Indem Sie WAF-Richtlinien auf einen Listener anwenden, können Sie WAF-Einstellungen für einzelne Websites konfigurieren, ohne dass die Änderungen sich auf alle Websites auswirken. Die spezifischste Richtlinie hat Vorrang. Wenn eine globale und eine websitespezifische Richtlinie (also eine WAF-Richtlinie, die einem Listener zugeordnet ist) vorhanden sind, setzt die websitespezifische Richtlinie die globale WAF-Richtlinie für diesen Listener außer Kraft. Für andere Listener ohne eigene Richtlinie gilt nur die globale WAF-Richtlinie.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Einrichten des Netzwerks
* Erstellen einer WAF-Richtlinie
* Erstellen eines Anwendungsgateways mit aktivierter WAF
* Anwenden der WAF-Richtlinie: global, websitespezifisch und URI-spezifisch 
* Erstellen einer Skalierungsgruppe für virtuelle Computer
* Erstellen eines Speicherkontos und Konfigurieren der Diagnose
* Testen des Anwendungsgateways

![Web Application Firewall – Beispiel](../media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe.  

```azurepowershell-interactive
$rgname = New-AzResourceGroup -Name myResourceGroupAG -Location eastus
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
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

In diesem Abschnitt erstellen Sie Ressourcen zur Unterstützung des Anwendungsgateways und anschließend das Anwendungsgateway selbst sowie eine WAF-Instanz (Web Application Firewall). Die Ressourcen, die Sie erstellen, umfassen:

- *IP-Konfigurationen und Front-End-Port*: Ordnet das Subnetz, das Sie zuvor erstellt haben, dem Anwendungsgateway zu und weist einen Port für den Zugriff darauf zu.
- *Standardpool*: Alle Anwendungsgateways müssen mindestens einen Back-End-Pool mit Servern haben.
- *Standardlistener und Regel*: Der Standardlistener lauscht auf dem Port, der zugewiesen wurde, auf Datenverkehr, und die Standardregel sendet Datenverkehr an den Standardpool.

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

$frontendport80 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
  
$frontendport8080 = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 8080
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

### <a name="create-two-waf-policies"></a>Erstellen von zwei WAF-Richtlinien

Erstellen Sie zwei WAF-Richtlinien – eine globale und eine websitespezifische –, und fügen Sie benutzerdefinierte Regeln hinzu. 

Die websitespezifische Richtlinie begrenzt den Dateiupload auf 5 MB. Alle anderen Einstellungen sind gleich.

```azurepowershell-interactive
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "globalAllow" 
$rule = New-AzApplicationGatewayFirewallCustomRule -Name globalAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition -Action Allow

$variable1 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition1 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable1 -Operator Contains -MatchValue "globalBlock" 
$rule1 = New-AzApplicationGatewayFirewallCustomRule -Name globalBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition1 -Action Block

$variable2 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition2 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable2 -Operator Contains -MatchValue "siteAllow" 
$rule2 = New-AzApplicationGatewayFirewallCustomRule -Name siteAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition2 -Action Allow

$variable3 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition3 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable3 -Operator Contains -MatchValue "siteBlock" 
$rule3 = New-AzApplicationGatewayFirewallCustomRule -Name siteBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition3 -Action Block

$variable4 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition4 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable4 -Operator Contains -MatchValue "URIAllow" 
$rule4 = New-AzApplicationGatewayFirewallCustomRule -Name URIAllow -Priority 5 -RuleType MatchRule -MatchCondition $condition4 -Action Allow

$variable5 = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestUri
$condition5 = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable5 -Operator Contains -MatchValue "URIBlock" 
$rule5 = New-AzApplicationGatewayFirewallCustomRule -Name URIBlock -Priority 10 -RuleType MatchRule -MatchCondition $condition5 -Action Block

$policySettingGlobal = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 256

$wafPolicyGlobal = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicyGlobal `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingGlobal `
  -CustomRule $rule, $rule1

$policySettingSite = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicySite = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingSite `
  -CustomRule $rule2, $rule3
```

### <a name="create-the-default-listener-and-rule"></a>Erstellen des Standardlisteners und einer Regel

Ein Listener ist erforderlich, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an die Back-End-Adresspools weiterleiten kann. In diesem Beispiel erstellen Sie einen grundlegenden Listener, der an der Stamm-URL auf Datenverkehr lauscht. 

Erstellen Sie einen Listener namens *mydefaultListener* mit [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener), der zuvor erstellten Front-End-Konfiguration und dem zuvor erstellten Front-End-Port. Für den Listener ist eine Regel erforderlich, damit bekannt ist, welcher Back-End-Pool für eingehenden Datenverkehr verwendet werden soll. Erstellen Sie mit [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) eine grundlegende Regel namens *rule1*.

```azurepowershell-interactive
$globalListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport80

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $globallistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
  
$siteListener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport8080 `
  -FirewallPolicy $wafPolicySite
  
$frontendRuleSite = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $siteListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>Erstellen des Anwendungsgateways mit der WAF

Sie haben die erforderlichen unterstützenden Ressourcen erstellt. Geben Sie nun mithilfe von [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) Parameter für das Anwendungsgateway an. Geben Sie die Firewallrichtlinie mithilfe von [New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/new-azapplicationgatewayfirewallpolicy) an. Erstellen Sie dann mithilfe von [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) das Anwendungsgateway namens *myAppGateway*.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_v2 `
  -Tier WAF_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport80 `
  -HttpListeners $globallistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -FirewallPolicy $wafPolicyGlobal
```

### <a name="apply-a-per-uri-policy"></a>Anwenden einer URI-spezifischen Richtlinie

Um eine URI-spezifische Richtlinie anzuwenden, erstellen Sie einfach eine neue Richtlinie und wenden sie auf die Pfadregelkonfiguration an. 

```azurepowershell-interactive
$policySettingURI = New-AzApplicationGatewayFirewallPolicySetting `
  -Mode Prevention `
  -State Enabled `
  -MaxRequestBodySizeInKb 100 `
  -MaxFileUploadInMb 5

$wafPolicyURI = New-AzApplicationGatewayFirewallPolicy `
  -Name wafpolicySite `
  -ResourceGroup myResourceGroupAG `
  -Location eastus `
  -PolicySetting $PolicySettingURI `
  -CustomRule $rule4, $rule5

$Gateway = Get-AzApplicationGateway -Name "myAppGateway"

$PathRuleConfig = New-AzApplicationGatewayPathRuleConfig -Name "base" `
  -Paths "/base" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings `
  -FirewallPolicy $wafPolicyURI

$PathRuleConfig1 = New-AzApplicationGatewayPathRuleConfig `
  -Name "base" -Paths "/test" `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings

$URLPathMap = New-AzApplicationGatewayUrlPathMapConfig -Name "PathMap" `
  -PathRules $PathRuleConfig, $PathRuleConfig1 `
  -DefaultBackendAddressPoolId $defaultPool.Id `
  -DefaultBackendHttpSettingsId $poolSettings.Id

Add-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $AppGw `
  -Name "RequestRoutingRule" `
  -RuleType PathBasedRouting `
  -HttpListener $siteListener `
  -UrlPathMap $URLPathMap
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

In diesem Artikel verwendet das Anwendungsgateway ein Speicherkonto, um Daten zum Zweck der Erkennung und Prävention zu speichern. Sie können auch Azure Monitor-Protokolle oder Event Hub verwenden, um Daten aufzuzeichnen.

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
  -Category ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Sie können [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) verwenden, um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. Verwenden Sie diese IP-Adresse dann zur curl-Ausführung (ersetzen Sie die unten gezeigte Zahlenreihe 1.1.1.1). 

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress

#should be blocked
curl 1.1.1.1/globalBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/globalAllow?1=1

#should be blocked
curl 1.1.1.1:8080/siteBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1:8080/siteAllow?1=1

#should be blocked
curl 1.1.1.1/URIBlock
curl 1.1.1.1/?1=1

#should be allowed
curl 1.1.1.1/URIAllow?1=1
```

![Testen der Basis-URL im Anwendungsgateway](../media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Anwendungsgateway und alle zugehörigen Ressourcen nicht mehr benötigen, entfernen Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

[Anpassen von Web Application Firewall-Regeln mit dem Azure-Portal](application-gateway-customize-waf-rules-portal.md)
