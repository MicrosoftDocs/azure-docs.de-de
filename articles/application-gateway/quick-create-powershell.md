---
title: 'Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure PowerShell | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie die Azure PowerShell zum Erstellen einer Azure Application Gateway-Instanz verwenden, mit der Webdatenverkehr an virtuelle Computer in einem Back-End-Pool geleitet wird.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/11/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6c472c30514e6acd3b21822e31f2cefc0da5bc98
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729652"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure PowerShell

In dieser Schnellstartanleitung wird gezeigt, wie Sie mit Azure PowerShell schnell ein Anwendungsgateway erstellen können.  Testen Sie das Anwendungsgateway, nachdem Sie es erstellt haben, um sicherzustellen, dass es richtig funktioniert. Mit Azure Application Gateway leiten Sie den Webdatenverkehr Ihrer Anwendungen an bestimmte Ressourcen weiter, indem Sie Ports Listener zuweisen, Regeln erstellen und Ressourcen zu einem Back-End-Pool hinzufügen. Der Einfachheit halber wird in diesem Artikel ein einfaches Setup mit einer öffentlichen Front-End-IP-Adresse, einem grundlegenden Listener zum Hosten einer einzelnen Website auf diesem Anwendungsgateway, zwei virtuellen Computern für den Back-End-Pool und einer Routingregel für grundlegende Anforderungen verwendet.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-powershell-module"></a>Azure PowerShell-Modul

Wenn Sie Azure PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden.

1. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. 
2. Führen Sie `Login-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

### <a name="resource-group"></a>Ressourcengruppe

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. In diesem Beispiel erstellen wir mit dem Cmdlet [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) wie folgt eine neue Ressourcengruppe: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Erforderliche Netzwerkressourcen

Für die Kommunikation in Azure zwischen den von Ihnen erstellten Ressourcen ist ein virtuelles Netzwerk erforderlich.  Das Subnetz für das Anwendungsgateway kann nur Anwendungsgateways enthalten. Andere Ressourcen sind nicht zulässig.  Sie können entweder ein neues Subnetz für Application Gateway erstellen oder ein bereits vorhandenes verwenden. In diesem Beispiel erstellen Sie zwei Subnetze: eines für das Anwendungsgateway und eines für die Back-End-Server. Je nach Anwendungsfall können Sie die Front-End-IP-Adresse von Application Gateway als öffentliche oder als private IP-Adresse konfigurieren. In diesem Beispiel wählen wir eine öffentliche Front-End-IP-Adresse.

1. Erstellen Sie durch Aufrufen von [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig) die Subnetzkonfigurationen.
2. Erstellen Sie durch Aufrufen von [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork) das virtuelle Netzwerk mit den Subnetzkonfigurationen. 
3. Erstellen Sie durch Aufrufen von [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress) die öffentliche IP-Adresse. 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
### <a name="backend-servers"></a>Back-End-Server

Das Back-End kann Netzwerkadapter, VM-Skalierungsgruppen, öffentliche IP-Adressen, interne IP-Adressen, vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDN) und Back-Ends mit mehreren Mandanten wie Azure App Service umfassen. In diesem Beispiel erstellen Sie zwei virtuelle Computer, die von Azure als Back-End-Server für das Anwendungsgateway verwendet werden. Sie installieren außerdem IIS auf den virtuellen Computern, um zu überprüfen, ob Azure das Anwendungsgateway erfolgreich erstellt hat.

#### <a name="create-two-virtual-machines"></a>Erstellen von zwei virtuellen Computern

1. Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface) eine Netzwerkschnittstelle. 
2. Erstellen Sie mit [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig) eine VM-Konfiguration.
3. Erstellen Sie mit [New-AzVM](/powershell/module/Az.compute/new-Azvm) den virtuellen Computer.

Wenn Sie das folgende Codebeispiel zum Erstellen der virtuellen Computer ausführen, werden Sie von Azure zur Eingabe von Anmeldeinformationen aufgefordert. Geben Sie als Benutzername *azureuser* und als Kennwort *Azure123456!* ein:
    
```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $subnet.Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

### <a name="create-the-ip-configurations-and-frontend-port"></a>Erstellen der IP-Konfigurationen und des Front-End-Ports

1. Erstellen Sie mit [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) die Konfiguration, die das zuvor erstellte Subnetz dem Anwendungsgateway zuweist. 
2. Erstellen Sie mit [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) die Konfiguration, die die zuvor erstellte öffentliche IP-Adresse dem Anwendungsgateway zuweist. 
3. Verwenden Sie [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport), um Port 80 für den Zugriff auf das Anwendungsgateway festzulegen.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>Erstellen des Back-End-Pools

1. Erstellen Sie mit [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) den Back-End-Pool für das Anwendungsgateway. 
2. Konfigurieren Sie mit [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting) die Einstellungen für den Back-End-Pool.

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Erstellen des Listeners und Hinzufügen einer Regel

Azure erfordert einen Listener, damit das Anwendungsgateway Datenverkehr in geeigneter Weise an den Back-End-Pool weiterleiten kann. Azure erfordert auch eine Regel für den Listener, damit bekannt ist, welcher Back-End-Pool für eingehenden Datenverkehr verwendet werden soll. 

1. Erstellen Sie mit [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) einen Listener mit der zuvor erstellten Front-End-Konfiguration und dem zuvor erstellten Front-End-Port. 
2. Erstellen Sie mit *New-AzApplicationGatewayRequestRoutingRule* eine Regel mit dem Namen [rule1](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule). 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Nachdem Sie nun die erforderlichen unterstützenden Ressourcen erstellt haben, erstellen Sie das Anwendungsgateway:

1. Verwenden Sie [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) zum Angeben von Parametern für das Anwendungsgateway.
2. Verwenden Sie [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) zum Erstellen des Anwendungsgateways.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

IIS ist für die Erstellung des Anwendungsgateways zwar nicht erforderlich, Sie haben die Installation in dieser Schnellstartanleitung aber ausgeführt, um zu überprüfen, ob Azure das Anwendungsgateway erfolgreich erstellt hat. Testen des Anwendungsgateways mit IIS:

1. Führen Sie [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) aus, um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. 
2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Wenn Sie den Browser aktualisieren, sollte der Name des virtuellen Computers angezeigt werden. Eine gültige Antwort überprüft, ob das Anwendungsgateway erfolgreich erstellt wurde und erfolgreich eine Verbindung mit dem Back-End herstellen kann.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testen des Anwendungsgateways](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit dem Anwendungsgateway erstellten Ressourcen nicht mehr benötigen, entfernen Sie die Ressourcengruppe. Beim Entfernen der Ressourcengruppe werden auch das Anwendungsgateway und alle zugehörigen Ressourcen entfernt. 

Rufen Sie das Cmdlet [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) wie folgt auf, um die Ressourcengruppe zu entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Webdatenverkehr mit einem Anwendungsgateway per Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

