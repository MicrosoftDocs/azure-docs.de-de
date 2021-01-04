---
title: 'Herstellen einer Point-to-Site-Verbindung zwischen einem Computer und einem virtuellen Netzwerk unter Verwendung der RADIUS-Authentifizierung: PowerShell | Azure'
description: Herstellen einer sicheren Verbindung von Windows und OS X-Clients mit einem virtuellen Netzwerk unter Verwendung von P2S und der RADIUS-Authentifizierung
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: cherylmc
ms.openlocfilehash: 9d962d3a4757b4c7b2d217f91aaf73d6ad4164d3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964846"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der RADIUS-Authentifizierung: PowerShell

In diesem Artikel wird erläutert, wie Sie ein VNET mit einer P2S-Verbindung erstellen, die RADIUS-Authentifizierung nutzt. Diese Konfiguration ist nur für das Resource Manager-Bereitstellungsmodell verfügbar.

Mit einem P2S-VPN-Gateway (Point-to-Site) können Sie von einem einzelnen Clientcomputer aus eine sichere Verbindung mit Ihrem virtuellen Netzwerk herstellen. Eine P2S-VPN-Verbindung ist nützlich, wenn Sie an einem Remotestandort (beispielsweise bei der Telearbeit zu Hause oder in einer Konferenz) eine Verbindung mit Ihrem VNET herstellen möchten. Wenn nur einige wenige Clients eine Verbindung mit einem VNET herstellen müssen, ist ein P2S-VPN (und nicht ein S2S-VPN) ebenfalls eine nützliche Lösung.

Eine P2S-VPN-Verbindung wird von Windows- und Mac-Geräten gestartet. Clients, die eine Verbindung herstellen, können die folgenden Authentifizierungsmethoden verwenden: 

* RADIUS-Server
* Native VPN-Gateway-Zertifikatauthentifizierung
* Native Azure Active Directory-Authentifizierung (nur Windows 10)

Dieser Artikel unterstützt Sie beim Konfigurieren einer P2S-Konfiguration mit Authentifizierung über einen RADIUS-Server. Wenn Sie stattdessen zum Authentifizieren erstellte Zertifikate und die native VPN-Gateway-Zertifikatauthentifizierung verwenden möchten, lesen Sie die Informationen unter [Konfigurieren einer Point-to-Site-VPN-Verbindung mit einem VNet unter Verwendung der nativen Azure-Zertifikatauthentifizierung: PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md) oder [Erstellen eines Azure Active Directory-Mandanten für Verbindungen mit dem P2S OpenVPN-Protokoll](openvpn-azure-ad-tenant.md) für die Azure Active Directory-Authentifizierung.

![Darstellung der P2S-Konfiguration mit einer Authentifizierung unter Verwendung eines RADIUS-Servers](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Point-to-Site-Verbindungen erfordern weder ein VPN-Gerät noch eine öffentliche IP-Adresse. P2S erstellt die VPN-Verbindung entweder über SSTP (Secure Socket Tunneling Protocol), OpenVPN oder IKEv2.

* SSTP ist ein TLS-basierter VPN-Tunnel, der nur auf Windows-Clientplattformen unterstützt wird. Er kann Firewalls durchdringen und ist daher optimal für die ortsunabhängige Verbindungsherstellung mit Azure geeignet. Auf Serverseite werden die SSTP-Versionen 1.0, 1.1 und 1.2 unterstützt. Der Client entscheidet, welche Version verwendet wird. Unter Windows 8.1 und höher wird standardmäßig SSTP 1.2 verwendet.

* OpenVPN®-Protokoll, ein auf SSL/TLS basierendes VPN-Protokoll. Eine TLS-VPN-Lösung kann Firewalls durchdringen, da die meisten Firewalls den von TLS verwendeten TCP-Port 443 für ausgehenden Datenverkehr öffnen. OpenVPN kann zum Herstellen einer Verbindung von Android-, iOS- (Versionen 11.0 und höher), Windows-, Linux- und Mac-Geräten (OSX-Version 10.13 und höher) verwendet werden.

* IKEv2-VPN, eine standardbasierte IPsec-VPN-Lösung. IKEv2-VPN kann zum Herstellen einer Verbindung von Mac-Geräten (OSX-Version 10.11 und höher) verwendet werden.

P2S-Verbindungen erfordern Folgendes:

* Ein RouteBased-VPN-Gateway. 
* Einen RADIUS-Server für die Benutzerauthentifizierung. Der RADIUS-Server kann lokal oder im Azure-VNET bereitgestelltwerden. Für eine Hochverfügbarkeit können Sie auch zwei RADIUS-Server konfigurieren.
* Ein VPN-Clientkonfigurationspaket für die Windows-Geräte, die eine Verbindung mit dem VNET herstellen. Ein VPN-Clientkonfigurationspaket enthält die Einstellungen, die ein VPN-Client zum Herstellen einer P2S-Verbindung benötigt.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Informationen zur Active Directory-Domänenauthentifizierung (AD) für P2S-VPNs

Die AD-Domänenauthentifizierung ermöglicht Benutzern die Anmeldung bei Azure mit ihren Anmeldeinformationen für die Organisationsdomäne. Dafür ist ein RADIUS-Server erforderlich, der in den AD-Server integriert wird. Organisationen können auch ihre vorhandene RADIUS-Bereitstellung nutzen.
 
Der RADIUS-Server kann sich in der lokalen Umgebung oder im Azure-VNET befiniden. Während der Authentifizierung fungiert das VPN-Gateway als Vermittler und leitet Authentifizierungsnachrichten zwischen dem RADIUS-Server und dem Gerät weiter, das eine Verbindung herstellt. Es ist wichtig, dass das VPN-Gateway den RADIUS-Server erreicht. Wenn sich der RADIUS-Server in der lokalen Umgebung befindet, ist eine VPN-S2S-Verbindung zwischen Azure und dem lokalen Standort erforderlich.

Abgesehen von Active Directory kann ein RADIUS-Server auch in andere externe Identitätssysteme integriert werden. Dadurch ergeben sich zahlreiche Authentifizierungsoptionen für P2S-VPNs, einschließlich MFA-Optionen. Sehen Sie sich in der Dokumentation des RADIUS-Serveranbieters die Liste der Identitätssysteme an, in die der Server integriert werden kann.

![Verbindungsdiagramm – RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Mit einem lokalen RADIUS-Server kann nur eine VPN-S2S-Verbindung hergestellt werden. Eine ExpressRoute-Verbindung kann nicht verwendet werden.
>
>

## <a name="before-beginning"></a><a name="before"></a>Vorbereitungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Beispielwerte

Sie können die Beispielwerte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen. Sie können diese Schritte entweder als exemplarische Vorgehensweise durchlaufen und die Werte nicht ändern, oder Sie können die Werte Ihrer Umgebung entsprechend ändern.

* **Name: VNet1**
* **Adressraum: 192.168.0.0/16** und **10.254.0.0/16**<br>In diesem Beispiel verwenden wir mehrere Adressräume, um zu veranschaulichen, dass diese Konfiguration mit mehreren Adressräumen funktioniert. Für diese Konfiguration sind aber nicht mehrere Adressräume erforderlich.
* **Subnetzname: FrontEnd**
  * **Subnetzadressbereich: 192.168.1.0/24**
* **Subnetzname: BackEnd**
  * **Subnetzadressbereich: 10.254.1.0/24**
* **Subnetzname: GatewaySubnet**<br>Der Subnetzname *GatewaySubnet* ist erforderlich, damit das VPN-Gateway funktioniert.
  * **Adressbereich des Gatewaysubnetzes: 192.168.200.0/24** 
* **VPN-Clientadresspool: 172.16.201.0/24**<br>VPN-Clients, die sich über diese Punkt-zu-Standort-Verbindung mit dem VNet verbinden, erhalten eine IP-Adresse aus dem VPN-Clientadresspool.
* **Abonnement:** Falls Sie über mehrere Abonnements verfügen, sollten Sie sich vergewissern, dass Sie das richtige Abonnement verwenden.
* **Ressourcengruppe: TestRG**
* **Standort: USA, Osten**
* **DNS-Server:** IP-Adresse des DNS-Servers, der für die Namensauflösung des VNET verwendet werden soll. (Optional)
* **GW-Name: Vnet1GW**
* **Name der öffentlichen IP: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. Festlegen der Variablen

Deklarieren Sie die gewünschten Variablen. Verwenden Sie das unten gezeigte Beispiel, und ersetzen Sie die Werte nach Bedarf durch Ihre eigenen. Wenn Sie Ihre PowerShell-/Cloud Shell-Sitzung zu einem beliebigen Zeitpunkt während der Übung schließen, kopieren Sie einfach die Werte noch mal, und fügen Sie sie ein, um die Variablen erneut zu deklarieren.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a>Erstellen der Ressourcengruppe, des VNET und der öffentlichen IP-Adresse

Mit den folgenden Schritten werden eine Ressourcengruppe und ein virtuelles Netzwerk in der Ressourcengruppe mit drei Subnetzen erstellt. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

1. Erstellen Sie eine Ressourcengruppe.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Erstellen Sie die Subnetzkonfigurationen für das virtuelle Netzwerk, und benennen Sie sie mit *FrontEnd*, *BackEnd* und *GatewaySubnet*. Diese Präfixe müssen im deklarierten VNet-Adressraum enthalten sein.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Erstellen des virtuellen Netzwerks

   In diesem Beispiel ist der Serverparameter „-DnsServer“ optional. Wenn ein Wert angegeben wird, wird kein neuer DNS-Server erstellt. Die IP-Adresse des angegebenen DNS-Servers muss dazu in der Lage sein, die Namen für die Ressourcen aufzulösen, mit denen Sie eine Verbindung von Ihrem VNET aus herstellen möchten. In diesem Beispiel wurde eine private IP-Adresse verwendet. Bei dieser IP-Adresse handelt es sich aber wahrscheinlich nicht um die IP-Adresse Ihres DNS-Servers. Achten Sie darauf, dass Sie Ihre eigenen Werte verwenden. Der angegebene Wert wird nicht von der P2S-Verbindung, sondern von den Ressourcen verwendet, die Sie im VNET bereitstellen.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Ein VPN-Gateway muss über eine öffentliche IP-Adresse verfügen. Sie fordern zuerst die IP-Adressressource an und verweisen dann beim Erstellen des Gateways des virtuellen Netzwerks darauf. Die IP-Adresse wird bei der Erstellung des VPN-Gateways der Ressource dynamisch zugewiesen. VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Die Zuweisung einer statischen öffentlichen IP-Adresse kann nicht angefordert werden. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

   Geben Sie die Variablen an, um eine dynamisch zugewiesene öffentliche IP-Adresse anzufordern.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a>Einrichten des RADIUS-Servers

Vor dem Erstellen und Konfigurieren des Gateways für virtuelle Netzwerke muss der RADIUS-Server ordnungsgemäß für die Authentifizierung konfiguriert werden.

1. Wenn kein RADIUS-Server bereitgestellt wurde, stellen Sie einen bereit. Informationen zu den Bereitstellungsschritten finden Sie im Einrichtungshandbuch des RADIUS-Anbieters.  
2. Konfigurieren Sie das VPN-Gateway als RADIUS-Client auf dem RADIUS-Server. Geben Sie beim Hinzufügen dieses RADIUS-Clients das erstellte Gatewaysubnetzt für virtuelle Netzwerke an. 
3. Rufen Sie nach der Einrichtung des RADIUS-Servers die IP-Adresse und das gemeinsame Geheimnis des RADIUS-Servers ab, die RADIUS-Clients für die Kommunikation mit dem RADIUS-Server verwenden sollen. Wenn sich der RADIUS-Server im Azure-VNET befindet, verwenden Sie die Zertifizierungsstellen-IP des virtuellen Computers mit dem RADIUS-Server.

Der Artikel [Network Policy Server (NPS)](/windows-server/networking/technologies/nps/nps-top) (Netzwerkrichtlinienserver (Network Policy Server, NPS)) enthält Anweisungen zum Konfigurieren eines Windows-RADIUS-Servers (NPS) für AD-Domänenauthentifizierung.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a>Erstellen des VPN-Gateways

Konfigurieren und erstellen Sie das VPN-Gateway für Ihr VNET.

* „-GatewayType“ muss „Vpn“ sein, und „-VpnType“ muss „RouteBased“ lauten.
* Je nach ausgewählter  [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)  kann die Erstellung eines VPN-Gateways bis zu 45 Minuten dauern.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a>Hinzufügen des RADIUS-Servers und des Clientadresspools
 
* Für „-RadiusServer“ kann ein Name oder eine IP-Adresse angegeben werden. Wenn Sie den Namen angeben und sich der Server in der lokalen Umgebung befindet, kann das VPN-Gateway den Namen unter Umständen nicht auflösen. In diesem Fall ist es besser, die IP-Adresse des Servers anzugeben. 
* „-RadiusSecret“ muss dem für den RADIUS-Server konfigurierten Wert entsprechen.
* Bei „-VpnClientAddressPool“ handelt es sich um den Bereich, aus dem die VPN-Clients, die eine Verbindung herstellen, eine IP-Adresse erhalten. Verwenden Sie einen privaten IP-Adressbereich, der sich nicht mit dem lokalen Standort überschneidet, aus dem Sie Verbindungen herstellen möchten. Der Bereich darf sich auch nicht mit dem VNET überschneiden, mit dem Sie Verbindungen herstellen möchten. Stellen Sie sicher, dass der konfigurierte Adresspool groß genug ist.  

1. Erstellen Sie eine sichere Zeichenfolge für das RADIUS-Geheimnis.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Sie werden zur Eingabe des RADIUS-Geheimnisses aufgefordert. Die eingegebenen Zeichen werden nicht angezeigt, sondern stattdessen durch „*“ ersetzt.

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Fügen Sie den VPN-Clientadresspool und die RADIUS-Serverinformationen hinzu.

   Für SSTP-Konfigurationen:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Für OpenVPN®-Konfigurationen:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Für IKEv2-Konfigurationen:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Für SSTP und IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Verwenden Sie die folgende Syntax, um **zwei** RADIUS-Server anzugeben. Ändern Sie den Wert **-VpnClientProtocol** nach Bedarf.

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a>Herunterladen des VPN-Clientkonfigurationspakets und Einrichten des VPN-Clients

Die VPN-Clientkonfiguration ermöglicht Geräten das Herstellen einer P2S-Verbindung mit einem VNET. Informationen zum Erstellen eines VPN-Clientkonfigurationspakets und zum Einrichten des VPN-Clients finden Sie unter [Create and install VPN client configuration files for P2S RADIUS authentication (Preview)](point-to-site-vpn-client-configuration-radius.md) (Erstellen und Installieren von VPN-Clientkonfigurationsdateien für P2S-RADIUS-Authentifizierung (Vorschauversion)).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Herstellen einer Verbindung mit Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>So stellen Sie eine Verbindung von einem Windows-VPN-Client her

1. Um eine Verbindung mit Ihrem VNet herzustellen, navigieren Sie auf dem Clientcomputer zu „VPN-Verbindungen“ und suchen nach der VPN-Verbindung, die Sie erstellt haben. Sie hat den gleichen Namen wie das virtuelle Netzwerk. Geben Sie Ihre Domänenanmeldeinformationen ein, und klicken Sie auf „Verbinden“. Eine Popupmeldung wird angezeigt, in der Sie zur Eingabe erhöhter Rechte aufgefordert werden. Akzeptieren Sie sie, und geben Sie die Anmeldeinformationen ein.

   ![Verbindungsherstellung zwischen VPN-Client und Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Die Verbindung wurde hergestellt.

   ![Verbindung hergestellt](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Herstellen einer Verbindung von einem Mac-VPN-Client

Navigieren Sie im Dialogfeld „Netzwerk“ zum gewünschten Clientprofil, und klicken Sie auf **Verbinden**.

  ![Mac-Verbindung](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>So überprüfen Sie Ihre Verbindung

1. Um sicherzustellen, dass die VPN-Verbindung aktiv ist, öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie *Ipconfig/all* aus.
2. Zeigen Sie die Ergebnisse an. Beachten Sie, dass die erhaltene IP-Adresse eine der Adressen aus dem Clientadresspool des Punkt-zu-Standort-VPN ist, den Sie in Ihrer Konfiguration angegeben haben. Die Ergebnisse sehen in etwa wie in diesem Beispiel aus:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Informationen zur Problembehandlung für eine P2S-Verbindung finden Sie unter [Problembehandlung: Azure Punkt-zu-Standort-Verbindungsprobleme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Stellen Sie sicher, dass das VPN-Clientkonfigurationspaket generiert wurde, nachdem die IP-Adressen des DNS-Server für das VNET angegeben wurden. Wenn Sie die IP-Adressen des DNS-Servers aktualisiert haben, generieren Sie ein neues VPN-Clientkonfigurationspaket und installieren es.

* Überprüfen Sie mit „ipconfig“ die IPv4-Adresse, die dem Ethernet-Adapter auf dem Computer zugewiesen ist, von dem aus Sie die Verbindung herstellen. Wenn sich die IP-Adresse im Adressbereich des VNETs befindet, mit dem Sie die Verbindung herstellen, oder im Adressbereich von „VPNClientAddressPool“ liegt, wird dies als sich überschneidender Adressraum bezeichnet. Falls sich Ihr Adressraum auf diese Weise überschneidet, kommt der Netzwerkdatenverkehr nicht bei Azure an, sondern verbleibt im lokalen Netzwerk.

## <a name="faq"></a><a name="faq"></a>Häufig gestellte Fragen

Diese häufig gestellten Fragen beziehen sich auf P2S mit RADIUS-Authentifizierung

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) . Weitere Informationen zu Netzwerken und virtuellen Computern finden Sie unter [Azure- und Linux-VM-Netzwerke (Übersicht)](../virtual-machines/network-overview.md).
