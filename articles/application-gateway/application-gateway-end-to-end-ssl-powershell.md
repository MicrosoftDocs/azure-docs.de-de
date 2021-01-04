---
title: Konfigurieren von End-to-End-TLS mit Azure Application Gateway
description: Dieser Artikel beschreibt das Konfigurieren von End-to-End-TLS mit Azure Application Gateway unter Verwendung von PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397806"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Konfigurieren von End-to-End-TLS mit Application Gateway mithilfe von PowerShell

## <a name="overview"></a>Übersicht

Azure Application Gateway unterstützt die End-to-End-Verschlüsselung des Datenverkehrs. Application Gateway terminiert die TLS/SSL-Verbindung am Anwendungsgateway. Das Gateway wendet dann die Routingregeln auf den Datenverkehr an, verschlüsselt das Paket erneut und leitet das Paket basierend auf den definierten Routingregeln an den entsprechenden Back-End-Server weiter. Antworten vom Webserver durchlaufen denselben Prozess zurück an den Endbenutzer.

Application Gateway unterstützt das Definieren benutzerdefinierter TLS-Optionen. Es unterstützt auch das Deaktivieren der folgenden Protokollversionen: **TLSv1.0** , **TLSv1.1** und **TLSv1.2** ; außerdem werden das Definieren der zu verwendenden Verschlüsselungssammlungen und die jeweilige Priorität unterstützt. Weitere Informationen zu konfigurierbaren TLS-Optionen finden Sie in der [Übersicht über TLS-Richtlinien](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 und SSL 3.0 sind standardmäßig deaktiviert und können nicht aktiviert werden. Sie werden als unsicher eingestuft und können mit Application Gateway nicht verwendet werden.

![Szenarioabbildung][scenario]

## <a name="scenario"></a>Szenario

In diesem Szenario erfahren Sie, wie Sie mithilfe von PowerShell ein Anwendungsgateway mit End-to-End-TLS erstellen.

Dieses Szenario umfasst Folgendes:

* Erstellen einer Ressourcengruppe namens **appgw-rg**
* Erstellen eines virtuellen Netzwerks namens **appgwvnet** mit dem Adressraum **10.0.0.0/16**
* Erstellen zweier Subnetze namens **appgwsubnet** und **appsubnet**.
* Erstellen eines kleinen Anwendungsgateways mit Unterstützung der End-to-End-TLS-Verschlüsselung und Einschränkung bestimmter TLS-Protokollversionen und Verschlüsselungssammlungen

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zum Konfigurieren von End-to-End-TLS mit einem Anwendungsgateway wird ein Zertifikat für das Gateway benötigt. Außerdem sind weitere Zertifikate für die Back-End-Server erforderlich. Das Gatewayzertifikat wird zum Ableiten eines symmetrischen Schlüssels gemäß der Spezifikation des TLS-Protokolls verwendet. Der symmetrische Schlüssel wird dann zum Verschlüsseln und Entschlüsseln des Datenverkehrs mit dem Gateway verwendet. Das Gatewayzertifikat muss im PFX-Format (Personal Information Exchange, privater Informationsaustausch) vorliegen. In diesem Dateiformat können Sie den privaten Schlüssel exportieren. Das ist erforderlich, damit das Anwendungsgateway die Ver- und Entschlüsselung des Datenverkehrs durchführen kann.

Für die End-to-End-TLS-Verschlüsselung muss das Back-End explizit vom Anwendungsgateway zugelassen werden. Laden Sie das öffentliche Zertifikat der Back-End-Server auf das Anwendungsgateway hoch. Durch das Hinzufügen des Zertifikats wird sichergestellt, dass das Anwendungsgateway nur mit bekannten Back-End-Instanzen kommuniziert. Außerdem wird auf diese Weise die End-to-End-Kommunikation gesichert.

Der Konfigurationsprozess wird in den folgenden Abschnitten beschrieben.

## <a name="create-the-resource-group"></a>Ressourcengruppe erstellen

Dieser Abschnitt führt Sie durch die Erstellung einer Ressourcengruppe, die das Anwendungsgateway enthält.

1. Melden Sie sich bei Ihrem Azure-Konto an.

   ```powershell
   Connect-AzAccount
   ```

2. Wählen Sie das Abonnement aus, das für dieses Szenario verwendet werden soll.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Erstellen Sie eine Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Erstellen eines virtuelles Netzwerkes und eines Subnetzes für das Application Gateway.

Im folgenden Beispiel werden ein virtuelles Netzwerk und zwei Subnetze erstellt. In einem Subnetz befindet sich das Anwendungsgateway. Das andere Subnetz wird für die Back-Ends verwendet, die die Webanwendung hosten.

1. Weisen Sie einen Adressbereich für das Subnetz zu, das für das Anwendungsgateway verwendet werden soll.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Die Größen der für ein Anwendungsgateway konfigurierten Subnetze sollten richtig bemessen sein. Ein Anwendungsgateway kann für bis zu 10 Instanzen konfiguriert werden. Jede Instanz erhält dabei genau eine IP-Adresse aus dem Subnetz. Ein zu kleines Subnetz kann sich negativ auf die Erweiterungsmöglichkeiten eines Anwendungsgateways auswirken.
   >

2. Weisen Sie einen Adressbereich zu, der für den Back-End-Adresspool verwendet werden soll.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Erstellen Sie ein virtuelles Netzwerk mit den Subnetzen, die in den vorhergehenden Schritten definiert wurden.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Rufen Sie die virtuelle Netzwerkressource und die Subnetzressourcen ab, die in den folgenden Schritten verwendet werden sollen.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Erstellen der öffentlichen IP-Adresse für die Front-End-Konfiguration

Erstellen Sie eine öffentliche IP-Ressource, die für das Anwendungsgateway verwendet werden soll. Diese öffentliche IP-Adresse wird in einem der folgenden Schritte verwendet.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway unterstützt nicht die Verwendung einer öffentlichen IP-Adresse, die mit einer definierten Domänenbezeichnung erstellt wird. Nur eine öffentliche IP-Adresse mit einer dynamisch erstellten Domänenbezeichnung wird unterstützt. Wenn Sie einen benutzerfreundlichen DNS-Namen für das Anwendungsgateway benötigen, empfiehlt es sich, einen CNAME-Eintrag als Alias zu verwenden.

## <a name="create-an-application-gateway-configuration-object"></a>Erstellen eines Konfigurationsobjekts für das Anwendungsgateway

Vor dem Erstellen des Anwendungsgateways werden die Konfigurationselemente festgelegt. Die folgenden Schritten erstellen die Konfigurationselemente, die für eine Application Gateway-Ressource benötigt werden.

1. Erstellen einer IP-Konfiguration für das Anwendungsgateway Diese Einstellung konfiguriert, welches der Subnetze das Anwendungsgateway verwendet. Beim Starten des Anwendungsgateways wird eine IP-Adresse aus dem konfigurierten Subnetz ausgewählt, und der Netzwerkdatenverkehr wird an die IP-Adressen im Back-End-IP-Pool weitergeleitet. Beachten Sie, dass jede Instanz eine eigene IP-Adresse benötigt.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Erstellen Sie eine Front-End-IP-Konfiguration. Diese Einstellung ordnet dem Front-End des Anwendungsgateways eine private oder öffentliche IP-Adresse zu. Im folgenden Schritt wird die öffentliche IP-Adresse aus dem vorherigen Schritt der Front-End-IP-Konfiguration zugeordnet.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurieren Sie den Back-End-IP-Adresspool mit den IP-Adressen der Back-End-Webserver. Dies sind die IP-Adressen, die den Netzwerkdatenverkehr vom Front-End-IP-Endpunkt empfangen. Ersetzen Sie die IP-Adressen im Beispiel durch Ihre eigenen IP-Adressendpunkte der Anwendung.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Ein vollqualifizierter Domänenname (FQDN) ist ebenfalls ein gültiger Wert anstelle einer IP-Adresse für die Back-End-Server. Die Aktivierung erfolgt über den Schalter **-BackendFqdns**. 

4. Konfigurieren Sie den Front-End-IP-Port für den öffentlichen IP-Endpunkt. Dieser Port ist der Port, mit dem Endbenutzer eine Verbindung herstellen.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurieren Sie das Zertifikat für das Anwendungsgateway. Dieses Zertifikat wird zum Entschlüsseln und erneuten Verschlüsseln des Datenverkehrs auf dem Anwendungsgateway verwendet.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > In diesem Beispiel wird das Zertifikat für TLS-Verbindungen konfiguriert. Das Zertifikat muss im PFX-Format vorliegen, und das Kennwort muss zwischen 4 und 12 Zeichen umfassen.

6. Erstellen Sie den HTTP-Listener für das Anwendungsgateway. Weisen Sie die zu verwendende Front-End-IP-Konfiguration, den Port und das TLS/SSL-Zertifikat zu.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Laden Sie das Zertifikat hoch, das von den TLS-fähigen Ressourcen des Back-End-Pools verwendet werden soll.

   > [!NOTE]
   > Der Standardtest ruft den öffentlichen Schlüssel aus der *standardmäßigen* TLS-Bindung in der IP-Adresse des Back-Ends ab und vergleicht den Wert dieses öffentlichen Schlüssels mit dem Wert des öffentlichen Schlüssels, den Sie hier bereitstellen. 
   > 
   > Wenn Sie Hostheader und Servernamensanzeige (SNI) auf dem Back-End verwenden, ist der abgerufene öffentliche Schlüssel nicht zwingend der Zielort für den Datenverkehr. Öffnen Sie im Zweifelsfall auf den Back-End-Servern die Seite https://127.0.0.1/, um sich zu vergewissern, welches Zertifikat für die *standardmäßige* TLS-Bindung verwendet wird. Verwenden Sie den öffentlichen Schlüssel aus der Aufforderung in diesem Abschnitt. Wenn Sie Hostheader und SNI in HTTPS-Bindungen verwenden und durch eine manuelle Browseranforderung in https://127.0.0.1/ auf den Back-End-Servern keine Antwort und kein Zertifikat erhalten, müssen Sie eine Standard-TLS-Bindung auf diesen Servern einrichten. Andernfalls sind die Tests nicht erfolgreich, und das Back-End wird nicht zugelassen.
   
   Weitere Informationen zu SNI in Application Gateway finden Sie unter [Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](ssl-overview.md).

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Das im vorherigen Schritt bereitgestellte Zertifikat sollte dem öffentlichen Schlüssel des PFX-Zertifikats entsprechen, das auf dem Back-End vorliegt. Exportieren Sie das auf dem Back-End-Server im CER-Format (Claim, Evidence, and Reasoning) installierte Zertifikat (nicht das Stammzertifikat), und verwenden Sie es für diesen Schritt. Durch diesen Schritt wird das Back-End beim Anwendungsgateway zugelassen.

   Wenn Sie die Application Gateway v2-SKU verwenden, erstellen Sie anstelle eines Authentifizierungszertifikats ein vertrauenswürdiges Stammzertifikat. Weitere Informationen finden Sie unter [Übersicht über End-to-End-TLS mit Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurieren Sie die HTTP-Einstellungen für das Back-End des Anwendungsgateways. Weisen Sie den HTTP-Einstellungen das Zertifikat zu, das im vorherigen Schritt hochgeladen wurde.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Verwenden Sie den folgenden Befehl für die Application Gateway v2-SKU:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Erstellen Sie eine Routingregel für den Lastenausgleich, durch die das Verhalten des Lastenausgleichs konfiguriert wird. In diesem Beispiel wird eine Roundrobin-Basisregel erstellt.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurieren Sie die Instanzgröße des Anwendungsgateways. Die verfügbaren Größen lauten **Standard\_Small** , **Standard\_Medium** und **Standard\_Large**.  Für die Kapazität stehen die Werte **1** bis **10** zur Verfügung.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Zu Testzwecken kann für die Anzahl von Instanzen der Wert 1 ausgewählt werden. Beachten Sie, dass eine Anzahl von weniger als zwei Instanzen nicht durch die SLA abgedeckt ist und daher nicht empfohlen wird. Gateways mit niedriger Nutzungsdauer sind für Dev/Test-Umgebungen vorgesehen und sollten nicht zu Produktionszwecken verwendet werden.

11. Konfigurieren Sie die TLS-Richtlinie, die auf dem Anwendungsgateway verwendet werden soll. Application Gateway ermöglicht das Festlegen einer Mindestversion für das TLS-Protokoll.

    Die folgenden Werte sind eine Liste mit definierbaren Protokollversionen:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    Das folgende Beispiel legt die Mindestprotokollversion auf **TLSv1_2** fest und aktiviert nur **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256** , **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** und **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**.

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Erstellen des Anwendungsgateways

Erstellen Sie das Anwendungsgateway mithilfe der oben aufgeführten Schritte. Die Erstellung des Gateways ist ein Prozess, dessen Ausführung eine lange Zeit in Anspruch nimmt.

Verwenden Sie für die V1-SKU den folgenden Befehl:
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Verwenden Sie für die V2-SKU den folgenden Befehl:
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Anwenden eines neuen Zertifikats, wenn das Back-End-Zertifikat abgelaufen ist

Gehen Sie wie folgt vor, um ein neues Zertifikat anzuwenden, wenn das Back-End-Zertifikat abgelaufen ist.

1. Rufen Sie das Anwendungsgateway ab, das aktualisiert werden soll.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Fügen Sie die neue Zertifikatsressource aus der CER-Datei hinzu, die den öffentlichen Schlüssel des Zertifikats enthält. Dieses Zertifikat kann mit demjenigen identisch sein, das dem Listener für die TLS-Terminierung auf dem Anwendungsgateway hinzugefügt wurde.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Laden Sie das neue Authentifizierungszertifikatobjekt in eine Variable (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Weisen Sie das neue Zertifikat in der **BackendHttp** -Einstellung zu, und verweisen Sie mit der $AuthCert-Variablen auf das Zertifikat. (Geben Sie den Namen der HTTP-Einstellung an, die Sie ändern möchten.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Committen Sie die Änderung in das Anwendungsgateway, und übergeben Sie die neue Konfiguration, die in der Variablen $out enthalten ist.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Entfernen eines nicht verwendeten abgelaufenen Zertifikats aus den HTTP-Einstellungen

Gehen Sie wie folgt vor, um ein nicht verwendetes abgelaufenes Zertifikat aus den HTTP-Einstellungen zu entfernen.

1. Rufen Sie das Anwendungsgateway ab, das aktualisiert werden soll.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Listen Sie den Namen des Authentifizierungszertifikats auf, das Sie entfernen möchten.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Entfernen Sie das Authentifizierungszertifikat aus einem Anwendungsgateway.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Führen Sie für die Änderung einen Commit aus.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>Beschränken von TLS-Protokollversionen für ein vorhandenes Anwendungsgateway

Mit den bisher erläuterten Schritten haben Sie eine Anwendung mit End-to-End-TLS erstellt und bestimmte Versionen des TLS-Protokolls deaktiviert. Das folgende Beispiel deaktiviert bestimmte TLS-Richtlinien auf einem vorhandenen Anwendungsgateway.

1. Rufen Sie das Anwendungsgateway ab, das aktualisiert werden soll.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definieren Sie eine TLS-Richtlinie. Im folgenden Beispiel werden **TLSv1.0** und **TLSv1.1** deaktiviert und nur die Verschlüsselungssammlungen **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256** , **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** und **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** zugelassen.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Zum Schluss aktualisieren Sie das Gateway. Dieser letzte Schritt nimmt einen längeren Zeitraum in Anspruch. Nach Abschluss des Vorgangs ist End-to-End-TLS auf dem Anwendungsgateway konfiguriert.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Abrufen des DNS-Namens eines Anwendungsgateways

Nach dem Erstellen des Gateways wird das Front-End für die Kommunikation konfiguriert. Application Gateway erfordert einen dynamisch zugewiesenen DNS-Namen, wenn eine öffentliche IP-Adresse verwendet wird, die nicht benutzerfreundlich ist. Um zu gewährleisten, dass Endbenutzer auf das Anwendungsgateway zugreifen können, können Sie mit einem CNAME-Eintrag auf den öffentlichen Endpunkt des Anwendungsgateways verweisen. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Rufen Sie zum Konfigurieren eines Alias mithilfe des **PublicIPAddress** -Elements, das an das Anwendungsgateway angefügt ist, Details zum Anwendungsgateway sowie die zugeordnete IP-Adresse/den zugeordneten DNS-Namen ab. Verwenden Sie den DNS-Namen des Anwendungsgateways zum Erstellen eines CNAME-Eintrags, der die beiden Webanwendungen an diesen DNS-Namen verweist. Die Verwendung von A-Einträgen wird nicht empfohlen, da sich die VIP beim Neustart des Anwendungsgateways möglicherweise ändert.

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

Weitere Informationen zum Verstärken der Sicherheit Ihrer Webanwendungen mit Web Application Firewall über Application Gateway finden Sie unter [Web Application Firewall – Übersicht](../web-application-firewall/ag/ag-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png