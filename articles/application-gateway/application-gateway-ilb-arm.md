---
title: Verwenden mit internem Lastenausgleich –Azure Application Gateway
description: Diese Seite enthält Anweisungen zum Erstellen, Konfigurieren, Starten und Löschen eines Azure Application Gateways mit internem Lastenausgleich (ILB) mit Azure-Ressourcen-Manager.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 3d663dc4e2bd860ec9494785ecbf6dbf10a4c5b5
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397755"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)

Ein Azure Application Gateway kann mit einer VIP mit Internetzugriff oder mit einem internen Endpunkt konfiguriert werden, der nicht über das Internet erreichbar ist. Dies wird auch als Endpunkt für internen Lastenausgleich (Internal Load Balancer, ILB) bezeichnet. Das Konfigurieren des Gateways mit einem ILB ist für interne Branchenanwendungen nützlich, die nicht für das Internet verfügbar gemacht werden. Es ist auch hilfreich für die Dienste und Ebenen in einer Anwendung mit mehreren Ebenen, die sich innerhalb einer Sicherheitsgrenze befinden und nicht für das Internet verfügbar gemacht werden, aber dennoch eine Round-Robin-Lastverteilung, Sitzungsbindungen oder Transport Layer Security (TLS), früher als SSL-Auslagerung (Secure Sockets Layer) bekannt, erfordern.

Dieser Artikel führt Sie durch die Schritte zum Konfigurieren eines Application Gateways mit einem ILB.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installieren Sie die aktuelle Version des Azure PowerShell-Moduls, indem Sie die [Installationsanweisungen](/powershell/azure/install-az-ps) befolgen.
2. Sie erstellen ein virtuelles Netzwerk und ein Subnetz für das Application Gateway. Stellen Sie sicher, dass keine virtuellen Maschinen oder Cloudbereitstellungen das Subnetz verwenden. Das Anwendungsgateway muss sich allein im Subnetz eines virtuellen Netzwerks befinden.
3. Die Server, die Sie für die Verwendung des Anwendungsgateways konfigurieren, müssen vorhanden sein oder Endpunkte aufweisen, die im virtuellen Netzwerk erstellt wurden oder denen eine öffentliche IP-Adresse/VIP zugewiesen wurde.

## <a name="what-is-required-to-create-an-application-gateway"></a>Was ist zum Erstellen eines Anwendungsgateways erforderlich?

* **Back-End-Serverpool:** Die Liste der IP-Adressen der Back-End-Server. Die aufgelisteten IP-Adressen sollten entweder zum virtuellen Netzwerk gehören, dann aber in einem anderen Subnetz für das Application Gateway, oder es sollte sich um eine öffentliche IP/VIP handeln.
* **Einstellungen für den Back-End-Serverpool:** Jeder Pool besitzt Einstellungen wie Port, Protokoll und cookiebasierte Affinität. Diese Einstellungen sind an einen Pool gebunden und gelten für alle Server innerhalb des Pools.
* **Front-End-Port:** Der öffentliche Port, der im Anwendungsgateway geöffnet ist. Datenverkehr erreicht diesen Port und wird dann an einen der Back-End-Server umgeleitet.
* **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll (Http oder Https, jeweils mit Beachtung der Groß-/Kleinschreibung) und den Namen des SSL-Zertifikats (falls die SSL-Auslagerung konfiguriert wird).
* **Regel:** Mit der Regel werden der Listener und der Back-End-Serverpool gebunden, und es wird definiert, an welchen Back-End-Serverpool der Datenverkehr gesendet werden soll, wenn er einen bestimmten Listener erreicht. Derzeit wird nur die Regel *basic* unterstützt. Die Regel *basic* ist eine Round-Robin-Lastverteilung.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Der Unterschied zwischen dem klassischen Azure-Modell und Azure Resource Manager besteht in der Reihenfolge, in der Sie ein Anwendungsgateway und die Elemente erstellen, die konfiguriert werden müssen.
Beim Resource Manager werden alle Elemente, die ein Application Gateway bilden, einzeln konfiguriert und anschließend zusammengesetzt, um die Application Gateway-Ressource zu erstellen.

Hier sind die erforderlichen Schritte zum Erstellen eines Anwendungsgateways angegeben:

1. Erstellen einer Ressourcengruppe für den Resource Manager
2. Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway.
3. Erstellen eines Konfigurationsobjekts für das Anwendungsgateway
4. Erstellen einer Application Gateway-Ressource

## <a name="create-a-resource-group-for-resource-manager"></a>Erstellen einer Ressourcengruppe für den Resource Manager

Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die Azure-Ressourcen-Manager-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

### <a name="step-1"></a>Schritt 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Schritt 2

Überprüfen Sie die Abonnements für das Konto.

```powershell
Get-AzSubscription
```

Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert.

### <a name="step-3"></a>Schritt 3

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Schritt 4

Erstellen Sie eine neue Ressourcengruppe (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Anwendungsgateway erstellt wird, die gleiche Ressourcengruppe verwenden.

Im obigen Beispiel haben wir eine Ressourcengruppe namens „appgw-rg“ mit dem Standort „USA, Westen“ erstellt.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway.

Das folgende Beispiel zeigt, wie Sie mit dem Ressourcen-Manager ein virtuelles Netzwerk erstellen:

### <a name="step-1"></a>Schritt 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Der Adressbereich 10.0.0.0/24 wird einer Subnetzvariablen zugewiesen, die zum Erstellen eines virtuellen Netzwerks verwendet wird.

### <a name="step-2"></a>Schritt 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Ein virtuelles Netzwerk mit dem Namen „appgwvnet“ wird in der Ressourcengruppe „appgw-rg“ für die Region „USA, Westen“ mit dem Präfix 10.0.0.0/16 und dem Subnetz 10.0.0.0/24 erstellt.

### <a name="step-3"></a>Schritt 3

```powershell
$subnet = $vnet.subnets[0]
```

Der Variablen „$subnet“ wird das Subnetzobjekt für die nächsten Schritte zugewiesen.

## <a name="create-an-application-gateway-configuration-object"></a>Erstellen eines Konfigurationsobjekts für das Anwendungsgateway

### <a name="step-1"></a>Schritt 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Für das Anwendungsgateway wird eine IP-Konfiguration namens „gatewayIP01“ erstellt. Beim Starten des Anwendungsgateways wird eine IP-Adresse aus dem konfigurierten Subnetz ausgewählt, und der Netzwerkdatenverkehr wird an die IP-Adressen im Back-End-IP-Pool weitergeleitet. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.

### <a name="step-2"></a>Schritt 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Der Back-End-IP-Adresspool „pool01“ wird mit den IP-Adressen 10.1.1.8, 10.1.1.9 und 10.1.1.10 konfiguriert. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Endpunkt empfangen. Ersetzen Sie die obigen IP-Adressen durch die IP-Adressendpunkte Ihrer eigenen Anwendung.

### <a name="step-3"></a>Schritt 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Die Anwendungsgatewayeinstellung „poolsetting01“ für den Lastenausgleich des Netzwerkdatenverkehrs im Back-End-Pool wird erstellt.

### <a name="step-4"></a>Schritt 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Für den ILB wird der Front-End-IP-Port namens „frontendport01“ konfiguriert.

### <a name="step-5"></a>Schritt 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Die Front-End-IP-Adresskonfiguration „fipconfig01“ wird erstellt und einer privaten IP-Adresse im aktuellen Subnetz des virtuellen Netzwerks zugewiesen.

### <a name="step-6"></a>Schritt 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Der Listener „listener01“ wird erstellt, und der Front-End-Port wird der Front-End-IP-Konfiguration zugewiesen.

### <a name="step-7"></a>Schritt 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Die Lastenausgleichsrouting-Regel namens „rule01“, mit der das Verhalten des Lastenausgleichs konfiguriert wird, wird erstellt.

### <a name="step-8"></a>Schritt 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Die Instanzgröße des Anwendungsgateways wird konfiguriert.

> [!NOTE]
> Der Standardwert für „Kapazität“ ist 2. Sie können für „SKU-Name“ entweder Standard_Small, Standard_Medium oder Standard_Large wählen.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Erstellen eines Anwendungsgateways mit dem New-AzureApplicationGateway-Cmdlet

Erstellen Sie ein Anwendungsgateway mit allen Konfigurationselementen aus den vorherigen Schritten. In diesem Beispiel heißt das Anwendungsgateway „appgwtest“.

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

In diesem Schritt wird ein Anwendungsgateway mit allen Konfigurationselementen aus den vorherigen Schritten erstellt. Im Beispiel heißt das Application Gateway „appgwtest“.

## <a name="delete-an-application-gateway"></a>Löschen eines Application Gateways

Zum Löschen eines Anwendungsgateways müssen die folgenden Schritte in der angegebenen Reihenfolge ausgeführt werden:

1. Verwenden Sie das Cmdlet `Stop-AzApplicationGateway` zum Beenden des Gateways.
2. Verwenden Sie das Cmdlet `Remove-AzApplicationGateway` zum Entfernen des Gateways.
3. Überprüfen Sie mit dem Cmdlet `Get-AzureApplicationGateway`, ob das Gateway entfernt wurde.

### <a name="step-1"></a>Schritt 1

Rufen Sie das Anwendungsgatewayobjekt ab, und ordnen Sie es einer „$getgw“-Variablen zu.

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Schritt 2

Verwenden Sie `Stop-AzApplicationGateway`, um das Anwendungsgateway anzuhalten. Dieses Beispiel zeigt das Cmdlet `Stop-AzApplicationGateway` in der ersten Zeile, gefolgt von der Ausgabe.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Sobald das Anwendungsgateway angehalten wurde, verwenden Sie das Cmdlet `Remove-AzApplicationGateway`, um den Dienst zu entfernen.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> Mit dem optionalen Switch **-force** kann diese Bestätigungsmeldung unterdrückt werden.

Mithilfe des Cmdlets `Get-AzApplicationGateway` können Sie sicherstellen, dass der Dienst entfernt wurde. Dieser Schritt ist nicht erforderlich.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie die SSL-Auslagerung konfigurieren möchten, finden Sie weitere Informationen im Abschnitt [Konfigurieren eines Application Gateways für die SSL-Auslagerung](./tutorial-ssl-powershell.md).

Weitere Informationen zu Lastenausgleichsoptionen im Allgemeinen finden Sie unter:

* [Azure-Lastenausgleich](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)