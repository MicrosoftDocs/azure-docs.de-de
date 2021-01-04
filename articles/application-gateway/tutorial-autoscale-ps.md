---
title: 'Tutorial: Verbessern des Zugriffs auf die Webanwendung: Azure Application Gateway'
description: In diesem Tutorial erfahren Sie, wie Sie mit Azure PowerShell eine zonenredundante Anwendungsgatewayinstanz mit automatischer Skalierung und einer reservierten IP-Adresse erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5731b65892877e5c363220d84a0bddeb5f958cee
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396871"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Tutorial: Erstellen eines Anwendungsgateways, das den Zugriff auf die Webanwendung verbessert

Wenn Sie als IT-Administrator für die Verbesserung des Zugriffs auf die Webanwendung verantwortlich sind, können Sie Ihr Anwendungsgateway optimieren, sodass es der Kundennachfrage gemäß skaliert wird und mehrere Verfügbarkeitszonen abdeckt. Dieses Tutorial hilft Ihnen beim Konfigurieren der Azure Application Gateway-Features für automatische Skalierung, Zonenredundanz und reservierte virtuellen IP-Adressen (VIPs) bzw. statische IP-Adressen. Sie verwenden Azure PowerShell-Cmdlets und das Azure Resource Manager-Bereitstellungsmodell, um das Problem zu lösen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines selbstsignierten Zertifikats
> * Erstellen eines virtuellen Netzwerks mit automatischer Skalierung
> * Erstellen einer reservierten öffentlichen IP-Adresse
> * Einrichten der Anwendungsgatewayinfrastruktur
> * Konfigurieren der automatischen Skalierung
> * Erstellen des Anwendungsgateways
> * Testen des Anwendungsgateways

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Für dieses Tutorial müssen Sie Azure PowerShell lokal ausführen. Sie müssen mindestens Version 1.0.0 des Azure PowerShell-Moduls installiert haben. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Führen Sie nach dem Überprüfen der PowerShell-Version `Connect-AzAccount` aus, um eine Verbindung mit Azure zu erstellen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie eine Ressourcengruppe in einer der verfügbaren Regionen.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Für die Produktion sollten Sie ein gültiges, von einem vertrauenswürdigen Anbieter signiertes Zertifikat importieren. Für dieses Tutorial erstellen Sie mit [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) ein selbstsigniertes Zertifikat. Sie können [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) mit dem zurückgegebenen Fingerabdruck verwenden, um eine PFX-Datei aus dem Zertifikat zu exportieren.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Die Ausgabe sollte in etwa wie folgendes Ergebnis aussehen:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Verwenden Sie den Fingerabdruck, um die PFX-Datei zu erstellen:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie ein virtuelles Netzwerk mit einem dedizierten Subnetz für ein Anwendungsgateway mit automatischer Skalierung. Derzeit kann in jedem dedizierten Subnetz nur ein Anwendungsgateway mit automatischer Skalierung bereitgestellt werden.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Erstellen einer reservierten öffentlichen IP-Adresse

Geben Sie als Zuordnungsmethode für PublicIPAddress **Statisch** an. Die virtuelle IP eines Anwendungsgateways mit automatischer Skalierung kann nur statisch sein. Dynamische IPs werden nicht unterstützt. Es wird nur die standardmäßige „PublicIpAddress“-SKU unterstützt.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Abrufen von Details

Rufen Sie Details zur Ressourcengruppe, zum Subnetz und zur IP-Adresse in einem lokalen Objekt ab, um die IP-Konfigurationsdetails für das Anwendungsgateway zu erstellen.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurieren der Infrastruktur

Konfigurieren Sie IP, Front-End-IP, Back-End-Pool, HTTP-Einstellungen, Zertifikat, Port, Listener und Regel im gleichen Format wie das vorhandene Standardanwendungsgateway. Die neue SKU folgt dem gleichen Objektmodell wie die Standard-SKU.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Konfigurieren der automatischen Skalierung

Jetzt können Sie die Konfiguration der automatischen Skalierung für das Anwendungsgateway angeben. Zwei Konfigurationstypen werden für die automatische Skalierung unterstützt:

* **Modus mit fester Kapazität** : In diesem Modus kann die Application Gateway-Instanz keine automatische Skalierung vornehmen. Sie wird mit einer festen Kapazität für die Skalierungseinheit ausgeführt.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Modus mit automatischer Skalierung** : In diesem Modus nimmt das Anwendungsgateway basierend auf dem Muster des Anwendungsdatenverkehrs eine automatische Skalierung vor.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Erstellen Sie das Anwendungsgateway, und beziehen Sie Redundanzzonen und die Konfiguration der automatischen Skalierung ein.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Verwenden Sie „Get-AzPublicIPAddress“, um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. Kopieren Sie die öffentliche IP-Adresse oder den DNS-Namen, und fügen Sie den kopierten Inhalt in die Adressleiste des Browsers ein.

`Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Untersuchen Sie zuerst die Ressourcen, die mit dem Anwendungsgateway erstellt wurden. Wenn sie nicht mehr benötigt werden, können Sie Ressourcengruppe, Anwendungsgateway und alle zugehörigen Ressourcen mit dem Befehl `Remove-AzResourceGroup` entfernen.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis](./tutorial-url-route-powershell.md)