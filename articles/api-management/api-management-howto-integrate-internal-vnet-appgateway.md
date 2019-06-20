---
title: Verwenden von Azure API Management im virtuellen Netzwerk mit Application Gateway | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Azure API Management im internen virtuellen Netzwerk mit Application Gateway (WAF) als Front-End einrichten und konfigurieren.
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: 4ee970f14a6da3d65849a79ff4afae68601f106f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141665"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrieren von API Management in ein internes VNET mit Application Gateway

## <a name="overview"></a> Übersicht

Der API Management-Dienst kann in einem virtuellen Netzwerk im internen Modus konfiguriert werden, sodass nur aus dem virtuellen Netzwerk darauf zugegriffen werden kann. Azure Application Gateway ist ein PaaS-Dienst, bei dem ein Layer 7-Lastenausgleich bereitgestellt wird. Er fungiert als Reverseproxydienst und verfügt unter anderem über eine Web Application Firewall (WAF).

Die Kombination aus einer Bereitstellung von API Management in einem internen VNET und dem Application Gateway-Front-End ermöglicht die folgenden Szenarien:

* Verwenden Sie dieselbe API Management-Ressource für die Nutzung durch interne und externe Consumer.
* Verwenden Sie nur eine API Management-Ressource, und halten Sie eine Teilmenge der in API Management definierten APIs für externe Consumer bereit.
* Schaffen Sie eine einfache Möglichkeit, mit der der Zugriff auf API Management über das öffentliche Internet ein- und ausgeschaltet werden kann.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zum Ausführen der in diesem Artikel beschriebenen Schritte benötigen Sie Folgendes:

* Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Zertifikate: PFX und CER für den API-Hostnamen und PFX für den Hostnamen des Entwicklerportals.

## <a name="scenario"></a> Szenario

In diesem Artikel wird beschrieben, wie Sie einen einzelnen API Management-Dienst sowohl für interne als auch für externe Consumer verwenden und als einzelnes Front-End für lokale und cloudbasierte APIs einrichten. Außerdem wird veranschaulicht, wie Sie nur eine Teilmenge Ihrer APIs (im Beispiel grün markiert) für die externe Nutzung verfügbar machen, indem Sie die Routingfunktionalität von Application Gateway verwenden.

Im ersten Setupbeispiel werden alle APIs ausschließlich aus Ihrem virtuellen Netzwerk verwaltet. Interne Consumer (orange gekennzeichnet) können auf alle internen und externen APIs zugreifen. Der Datenverkehr gelangt nie ins Internet. Hochleistungskonnektivität wird über ExpressRoute-Verbindungen bereitgestellt.

![URL-Route](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Voraussetzungen

* Stellen Sie sicher, dass Sie die neueste Version von Azure PowerShell verwenden. Anweisungen zur Installation finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Was ist erforderlich, um eine Integration zwischen API Management und Application Gateway zu erstellen?

* **Back-End-Serverpool:** Dies ist die interne virtuelle IP-Adresse des API Management-Diensts.
* **Einstellungen für den Back-End-Serverpool:** Jeder Pool besitzt Einstellungen wie Port, Protokoll und cookiebasierte Affinität. Diese Einstellungen werden auf alle Server innerhalb des Pools angewendet.
* **Front-End-Port:** Dies ist der öffentliche Port, der im Anwendungsgateway geöffnet ist. Datenverkehr, der an diesem Port eintrifft, wird an einen der Back-End-Server umgeleitet.
* **Listener:** Der Listener verfügt über einen Front-End-Port, ein Protokoll („Http“ oder „Https“; jeweils unter Beachtung der Groß-/Kleinschreibung) und den Namen des SSL-Zertifikats (falls die SSL-Auslagerung konfiguriert wird).
* **Regel:** Mit der Regel wird ein Listener an einen Back-End-Serverpool gebunden.
* **Benutzerdefinierter Integritätstest:** Für Application Gateway werden standardmäßig auf IP-Adressen basierende Tests verwendet, um zu ermitteln, welche Server im BackendAddressPool aktiv sind. Der API Management-Dienst reagiert nur auf Anforderungen mit den richtigen Hostheadern, sodass bei den Standardtests ein Fehler auftritt. Ein benutzerdefinierter Integritätstest muss definiert werden, damit das Application Gateway ermitteln kann, dass der Dienst aktiv ist und Anforderungen weitergeleitet werden können.
* **Benutzerdefinierte Domänenzertifikate:** Zum Zugreifen auf API Management über das Internet müssen Sie eine CNAME-Zuordnung des Hostnamens zum DNS-Namen des Application Gateway-Front-Ends erstellen. So wird sichergestellt, dass die Daten zum Hostnamenheader und Zertifikat für das Application Gateway, die an API Management weitergeleitet werden, von APIM als gültig erkannt werden können. In diesem Beispiel werden zwei Zertifikate verwendet – eines für das Back-End und eines für das Entwicklerportal.  

## <a name="overview-steps"></a> Erforderliche Schritte zur Integration von API Management und Application Gateway

1. Erstellen einer Ressourcengruppe für den Ressourcen-Manager
2. Erstellen eines virtuelles Netzwerks, Subnetzes und einer öffentlichen IP-Adresse für das Application Gateway Erstellen eines anderen Subnetzes für API Management
3. Erstellen eines API Management-Diensts im oben erstellten VNET-Subnetz und Sicherstellen, dass der interne Modus verwendet wird
4. Richten Sie einen benutzerdefinierten Domänennamen im API Management-Dienst ein.
5. Erstellen eines Konfigurationsobjekts für das Application Gateway
6. Erstellen einer Application Gateway-Ressource
7. Erstellen eines CNAME-Eintrags aus dem öffentlichen DNS-Namen des Application Gateway für den API Management-Proxyhostnamen

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Externes Verfügbarmachen des Entwicklerportals über Application Gateway

In diesem Leitfaden wird das **Entwicklerportal** über Application Gateway für externe Zielgruppen verfügbar gemacht. Hierfür sind zusätzliche Schritte zum Erstellen des Listeners, des Tests, der Einstellungen und der Regeln für das Entwicklerportal erforderlich. Alle Details finden Sie in den entsprechenden Schritten.

> [!WARNING]
> Wenn Sie Azure AD oder die Authentifizierung eines Drittanbieters verwenden, aktivieren Sie das Feature [Cookiebasierte Sitzungsaffinität](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) in Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Erstellen einer Ressourcengruppe für den Resource Manager

### <a name="step-1"></a>Schritt 1

Anmelden an Azure

```powershell
Connect-AzAccount
```

Authentifizieren Sie sich mit Ihren Anmeldeinformationen.

### <a name="step-2"></a>Schritt 2

Wählen Sie das gewünschte Abonnement aus.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Schritt 3

Erstellen Sie eine Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Stellen Sie sicher, dass alle Befehle, mit denen ein Anwendungsgateway erstellt wird, die gleiche Ressourcengruppe verwenden.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Erstellen eines virtuellen Netzwerks und eines Subnetzes für das Application Gateway

Das folgende Beispiel zeigt, wie Sie mit dem Resource Manager ein virtuelles Netzwerk erstellen:

### <a name="step-1"></a>Schritt 1

Weisen Sie den Adressbereich 10.0.0.0/24 der Subnetzvariablen zur Verwendung für das Application Gateway beim Erstellen eines virtuellen Netzwerks zu.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Schritt 2

Weisen Sie den Adressbereich 10.0.1.0/24 der Subnetzvariablen zur Verwendung für API Management beim Erstellen eines virtuellen Netzwerks zu.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Schritt 3

Erstellen Sie ein virtuelles Netzwerk mit dem Namen **appgwvnet** in der Ressourcengruppe **apim-appGw-RG** für die Region „USA, Westen“. Verwenden Sie das Präfix „10.0.0.0/16“ mit den Subnetzen „10.0.0.0/24“ und „10.0.1.0/24“.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Schritt 4

Zuweisen einer Subnetzvariablen für die nächsten Schritte

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Erstellen eines API Management-Diensts in einem VNET, das im internen Modus konfiguriert ist

Im folgenden Beispiel wird veranschaulicht, wie Sie einen API Management-Dienst in einem VNET erstellen, für das nur der interne Zugriff konfiguriert ist.

### <a name="step-1"></a>Schritt 1

Erstellen Sie ein Objekt vom Typ „Virtuelles API Management-Netzwerk“, indem Sie das oben erstellte Subnetz „$apimsubnetdata“ verwenden.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie im virtuellen Netzwerk einen API Management-Dienst.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Nachdem der obige Befehl erfolgreich ausgeführt wurde, können Sie die Informationen unter [DNS-Konfiguration](api-management-using-with-internal-vnet.md#apim-dns-configuration) (für den Zugriff auf den internen VNET-API Management-Dienst) nutzen, um darauf zuzugreifen. Dieser Schritt kann über eine halbe Stunde dauern.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Einrichten eines benutzerdefinierten Domänennamens in API Management

### <a name="step-1"></a>Schritt 1

Initialisieren Sie die folgenden Variablen mit den Details der Zertifikate mit privaten Schlüsseln für die Domänen. In diesem Beispiel werden `api.contoso.net` und `portal.contoso.net` verwendet.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie die Hostnamenkonfigurationsobjekte für den Proxy und das Portal und legen Sie diese fest.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType Portal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

Erstellen Sie eine Ressource für öffentliche IP-Adressen namens **publicIP01** in der Ressourcengruppe.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

Dem Anwendungsgateway wird beim Starten des Diensts eine IP-Adresse zugewiesen.

## <a name="create-application-gateway-configuration"></a>Erstellen einer Application Gateway-Konfiguration

Sie müssen alle Konfigurationselemente einrichten, bevor Sie das Anwendungsgateway erstellen. Die folgenden Schritten erstellen die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.

### <a name="step-1"></a>Schritt 1

Erstellen Sie für das Anwendungsgateway eine IP-Konfiguration mit dem Namen **gatewayIP01**. Beim Starten des Anwendungsgateways wird eine IP-Adresse aus dem konfigurierten Subnetz ausgewählt, und der Netzwerkdatenverkehr wird an die IP-Adressen im Back-End-IP-Pool weitergeleitet. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Schritt 2

Konfigurieren Sie den Front-End-IP-Port für den öffentlichen IP-Endpunkt. Dieser Port ist der Port, mit dem Endbenutzer eine Verbindung herstellen.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Schritt 3

Konfigurieren Sie die Front-End-IP-Adresse mit dem öffentlichen IP-Endpunkt.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Schritt 4

Konfigurieren Sie das Zertifikat für den Application Gateway-Dienst, der zum Entschlüsseln und Verschlüsseln des durchlaufenden Datenverkehrs verwendet wird.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Schritt 5

Erstellen Sie die HTTP-Listener für Application Gateway. Weisen Sie ihnen die Front-End-IP-Konfiguration, den Port und die SSL-Zertifikate zu.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Schritt 6

Erstellen Sie benutzerdefinierte Tests für den `ContosoApi`-Proxydomänenendpunkt des API Management-Diensts. Der Pfad `/status-0123456789abcdef` ist ein Standard-Integritätsendpunkt, der von allen API Management-Diensten gehostet wird. Legen Sie `api.contoso.net` als benutzerdefinierten Testhostnamen fest, den Sie mit einem SSL-Zertifikat schützen möchten.

> [!NOTE]
> Der Hostname `contosoapi.azure-api.net` ist der Proxy-Standardhostname, der konfiguriert wird, wenn im öffentlichen Azure-Bereich der Dienst `contosoapi` erstellt wird.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Schritt 7

Laden Sie das Zertifikat hoch, das auf den SSL-fähigen Ressourcen des Back-End-Pools verwendet werden soll. Dies ist das gleiche Zertifikat, das Sie in Schritt 4 bereitgestellt haben.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Schritt 8

Konfigurieren Sie die HTTP-Back-End-Einstellungen für das Application Gateway. Dies umfasst auch das Festlegen eines zeitlichen Grenzwerts für Back-End-Anforderungen, die abgebrochen werden, wenn der Grenzwert erreicht wird. Dieser Wert unterscheidet sich vom Testtimeout.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Schritt 9

Konfigurieren Sie einen Back-End-IP-Adresspool namens **apimbackend** mit der internen virtuellen IP-Adresse des oben erstellten API Management-Diensts.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Schritt 10

Erstellen Sie Regeln für Application Gateway für die Verwendung von grundlegendem Routing.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Ändern Sie den -RuleType und das Routing so, dass der Zugriff auf bestimmte Seiten im Entwicklerportal beschränkt ist.

### <a name="step-11"></a>Schritt 11

Konfigurieren Sie die Anzahl von Instanzen und die Größe für das Application Gateway. In diesem Beispiel verwenden wir die [WAF-SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md), um eine höhere Sicherheit für die API Management-Ressource zu erzielen.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Schritt 12

Konfigurieren Sie für die WAF den „Präventionsmodus“.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Erstellen eines Application Gateways

Erstellen Sie ein Application Gateway mit allen Konfigurationsobjekten aus den vorherigen Schritten.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Erstellen eines CNAME-Eintrags für den API Management-Proxyhostnamen zum Namen des öffentlichen DNS der Application Gateway-Ressource

Nach dem Erstellen des Gateways wird das Front-End für die Kommunikation konfiguriert. Wenn Sie eine öffentliche IP-Adresse verwenden, wird für das Application Gateway ein dynamisch zugewiesener DNS-Namen benötigt, der ggf. nicht benutzerfreundlich ist.

Verwenden Sie den DNS-Namen des Application Gateway zum Erstellen eines CNAME-Eintrags, mit dem für den APIM-Proxyhostnamen (z.B. `api.contoso.net` in den obigen Beispielen) auf diesen DNS-Namen verwiesen wird. Rufen Sie zum Konfigurieren des CNAME-Eintrags für die Front-End-IP mithilfe des PublicIPAddress-Elements Details zum Application Gateway und zum zugeordneten IP/DNS-Namen ab. Die Verwendung von A-Einträgen wird nicht empfohlen, da sich die VIP beim Neustart des Gateways unter Umständen ändert.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a> Zusammenfassung
Bei der Konfiguration von Azure API Management in einem VNET wird eine einzelne Gatewayschnittstelle für alle konfigurierten APIs bereitgestellt. Hierbei spielt es keine Rolle, ob sie lokal oder in der Cloud gehostet werden. Mit der Integration des Application Gateway in API Management verfügen Sie über die Flexibilität, für bestimmte APIs den Zugriff über das Internet selektiv zu aktivieren, und können eine Web Application Firewall als Front-End für Ihre API Management-Instanz bereitstellen.

## <a name="next-steps"> </a> Nächste Schritte
* Weitere Informationen zu Azure Application Gateway
  * [Übersicht über Application Gateway](../application-gateway/application-gateway-introduction.md)
  * [Web Application Firewall für Application Gateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Anwendungsgateways mit pfadbasiertem Routing](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Weitere Informationen zu API Management und VNETs
  * [Verwenden von Azure API Management mit internen virtuellen Netzwerken](api-management-using-with-internal-vnet.md)
  * [Verwenden von API Management im VNET](api-management-using-with-vnet.md)
