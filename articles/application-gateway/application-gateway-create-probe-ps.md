---
title: Erstellen eines benutzerdefinierten Tests mit PowerShell
titleSuffix: Azure Application Gateway
description: Hier erfahren Sie, wie Sie einen benutzerdefinierten Test für Application Gateways mithilfe von PowerShell im Ressourcen-Manager erstellen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 63f4b28915a932398e5417f8ea9be3fe4d68e705
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397840"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Erstellen eines benutzerdefinierten Tests für Azure Application Gateway mithilfe von PowerShell für Azure-Ressourcen-Manager

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klassische Azure PowerShell](application-gateway-create-probe-classic-ps.md)

In diesem Artikel fügen Sie einen benutzerdefinierten Test zu einem vorhandenen Anwendungsgateway mit PowerShell hinzu. Benutzerdefinierte Tests sind für Anwendungen, die über eine bestimmte Seite für die Integritätsprüfung verfügen, oder für Anwendungen hilfreich, die keine erfolgreiche Antwort für die Standardwebanwendung bereitstellen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Erstellen eines Anwendungsgateways mit benutzerdefiniertem Test

### <a name="sign-in-and-create-resource-group"></a>Anmelden und Erstellen einer Ressourcengruppe

1. Verwenden Sie zum Authentifizieren `Connect-AzAccount`.

   ```powershell
   Connect-AzAccount
   ```

1. Rufen Sie die Abonnements für das Konto ab.

   ```powershell
   Get-AzSubscription
   ```

1. Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Erstellen Sie eine Ressourcengruppe. Dieser Schritt kann übersprungen werden, wenn Sie über eine vorhandene Ressourcengruppe verfügen.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Anwendungsgateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im obigen Beispiel haben wir eine Ressourcengruppe namens **appgw-RG** mit dem Standort **USA, Westen** erstellt.

### <a name="create-a-virtual-network-and-a-subnet"></a>Erstellen eines virtuellen Netzwerks und eines Subnetzes

Mit dem folgenden Beispiel erstellen Sie ein virtuelles Netzwerk und ein Subnetz für das Anwendungsgateway. Für die Verwendung des Anwendungsgateways ist ein eigenes Subnetz erforderlich. Aus diesem Grund sollte das für das Anwendungsgateway erstellte Subnetz kleiner sein als der Adressraum des VNETs, damit auch andere Subnetze erstellt und verwendet werden können.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

Erstellen Sie eine öffentliche IP-Ressource namens **publicIP01** in der Ressourcengruppe **appgw-rg** für die Region „USA, Westen“. Dieses Beispiel verwendet eine öffentliche IP-Adresse für die Front-End-IP-Adresse des Anwendungsgateways.  Das Anwendungsgateway erfordert, dass die öffentliche IP-Adresse einen dynamisch erstellten DNS-Namen aufweist, daher kann die `-DomainNameLabel` nicht während der Erstellung der öffentlichen IP-Adresse angegeben werden.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Sie richten alle Konfigurationselemente ein, bevor Sie das Anwendungsgateway erstellen. Das folgende Beispiel erstellt die Konfigurationselemente, die für eine Anwendungsgatewayressource benötigt werden.

| **Komponente** | **Beschreibung** |
|---|---|
| **Gateway-IP-Konfiguration** | Eine IP-Konfiguration für ein Anwendungsgateway.|
| **Back-End-Pool** | Ein Pool von IP-Adressen, FQDNs oder NICs, die zu den Anwendungsservern gehören, die die Webanwendung hosten.|
| **Integritätstest** | Eine benutzerdefinierter Test, mit dem die Integrität der Back-End-Poolmitglieder überwacht wird.|
| **HTTP-Einstellungen** | Eine Sammlung von Einstellungen, einschließlich, Port, Protokoll, cookiebasierte Affinität, Test und Timeout.  Diese Einstellungen bestimmen, wie Datenverkehr zu den Back-End-Poolmitgliedern geleitet wird.|
| **Front-End-Port** | Der Port, den das Anwendungsgateway hinsichtlich des Datenverkehrs überwacht.|
| **Listener** | Eine Kombination aus Protokoll, Front-End-IP-Konfiguration und Front-End-Port. Dieser überwacht auf eingehende Anforderungen.
|**Regel**| Leitet den Datenverkehr auf Basis von HTTP-Einstellungen an das entsprechende Back-End weiter.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Hinzufügen eines Tests zu einem vorhandenen Application Gateway

Der folgende Codeausschnitt fügt einen Test zu einem vorhandenen Anwendungsgateway hinzu.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Entfernen eines Tests aus einem vorhandenen Application Gateway

Der folgende Codeausschnitt entfernt einen Test aus einem vorhandenen Anwendungsgateway.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Abrufen des DNS-Namens des Anwendungsgateways

Nach dem Erstellen des Gateways wird das Front-End für die Kommunikation konfiguriert. Wenn Sie eine öffentliche IP-Adresse verwenden, wird das Anwendungsgateway ein dynamisch zugewiesener DNS-Namen benötigt, der kein Anzeigename ist. Um sicherzustellen, dass die Endbenutzer auf das Anwendungsgateway zugreifen können, kann mit einem CNAME-Eintrag auf den öffentlichen Endpunkt des Anwendungsgateways verwiesen werden. [Konfigurieren eines benutzerdefinierten Domänennamens in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md) Rufen Sie hierzu mithilfe des PublicIPAddress-Elements, das an das Anwendungsgateway angefügt ist, Details zum Anwendungsgateway und den zugeordneten IP/DNS-Namen ab. Verwenden Sie den DNS-Namen des Anwendungsgateways zum Erstellen eines CNAME-Eintrags, der die beiden Webanwendungen an diesen DNS-Namen verweist. Die Verwendung von A-Einträgen wird nicht empfohlen, da sich die VIP beim Neustart des Anwendungsgateways möglicherweise verändert.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Konfigurieren der TLS-Auslagerung unter: [Konfigurieren der TLS-Auslagerung](./tutorial-ssl-powershell.md)