---
title: Erstellen eines Azure Application Gateways und nochmaliges Generieren eines HTTP-Headers
description: Dieser Artikel enthält Informationen zum Erstellen eines Azure Application Gateways und zum erneuten Generieren eines HTTP-Headers mithilfe von Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 4a1a122eb7b5b0abcc47cd321c74267a1a4aecda
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396854"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Erstellen eines Application Gateways und erneutes Generieren eines HTTP-Headers

Sie können Azure PowerShell verwenden, wenn Sie das neue [zonenredundante Anwendungs-SKU-Gateway mit automatischer Skalierung](./application-gateway-autoscaling-zone-redundant.md) erstellen, um [Regeln zum erneuten Generieren einer HTTP-Anforderung und eines Antwort-Headers](rewrite-http-headers.md) zu konfigurieren.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen eines virtuellen Netzwerks mit automatischer Skalierung
* Erstellen einer reservierten öffentlichen IP-Adresse
* Einrichten der Anwendungsgatewayinfrastruktur
* Festlegen Ihrer Regelkonfiguration für das erneute Generieren eines HTTP-Headers
* Konfigurieren der automatischen Skalierung
* Erstellen des Anwendungsgateways
* Testen des Anwendungsgateways

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für diesem Artikel müssen Sie Azure PowerShell lokal ausführen. Sie müssen Version 1.0.0 oder höher des Az-Moduls installiert haben. Führen Sie `Import-Module Az` und dann `Get-Module Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Führen Sie nach dem Überprüfen der PowerShell-Version `Login-AzAccount` aus, um eine Verbindung mit Azure zu erstellen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie eine Ressourcengruppe in einer der verfügbaren Regionen.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
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

Konfigurieren Sie die IP, die Front-End-IP, den Back-End-Pool, die HTTP-Einstellungen, das Zertifikat, den Port und den Listener im gleichen Format wie das vorhandene Standardanwendungsgateway. Die neue SKU folgt dem gleichen Objektmodell wie die Standard-SKU.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Legen Sie Ihre Regelkonfiguration für das erneute Generieren eines HTTP-Headers fest.

Konfigurieren Sie die neuen Objekte, die benötigt werden, um die HTTP-Header neu zu generieren:

- **RequestHeaderConfiguration:** Dieses Objekt wird dazu verwendet, die Anforderungsheaderfelder zu bestimmen, die Sie neu generieren möchten, sowie den neuen Wert, den die ursprünglichen Header benötigen, um neu generiert werden zu können.
- **ResponseHeaderConfiguration:** Dieses Objekt wird dazu verwendet, die Antwortheaderfelder, die Sie neu generieren möchten, und den neuen Wert zu bestimmen, den die ursprünglichen Header benötigen, um neu generiert werden zu können.
- **ActionSet:** Dieses Objekt beinhaltet die Konfigurationen der Anforderungs- und Antwortheader, die oben bestimmt wurden. 
- **RewriteRule:** Dieses Objekt beinhaltet alle *actionSets* , die oben bestimmt wurden. 
- **RewriteRuleSet:** Dieses Objekt beinhaltet alle *rewriteRules* und muss an die Anforderungsroutingregel angefügt werden (Standard oder pfadbasiert).

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Festlegen der Routingregel

Erstellen einer Anforderungsroutingregel Die erstellte Konfiguration der erneuten Generierung wird anhand der Routingregel an den Quelllistener angefügt. Bei Verwendung einer einfachen Routingregel wird die Konfiguration der erneuten Generierung eines Headers einem Quelllistener zugeordnet und fungiert als erneute Generierung eines globalen Headers. Wenn eine pfadbasierte Routingregel verwendet wird, wird die Konfiguration der erneuten Generierung eines Headers in der URL-Pfadzuordnung definiert. Daher gilt sie nur für den bestimmten Pfadbereich einer Site. Im Folgenden wird eine einfache Routingregel erstellt und die Regeln für das erneute Generieren werden angefügt.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
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
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testen des Anwendungsgateways

Verwenden Sie „Get-AzPublicIPAddress“, um die öffentliche IP-Adresse des Anwendungsgateways abzurufen. Kopieren Sie die öffentliche IP-Adresse oder den DNS-Namen, und fügen Sie den kopierten Inhalt in die Adressleiste des Browsers ein.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Untersuchen Sie zuerst die Ressourcen, die mit dem Anwendungsgateway erstellt wurden. Wenn sie nicht mehr benötigt werden, können Sie Ressourcengruppe, Anwendungsgateway und alle zugehörigen Ressourcen mit dem Befehl `Remove-AzResourceGroup` entfernen.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Anwendungsgateways mit Routingregeln auf URL-Pfadbasis](./tutorial-url-route-powershell.md)