---
title: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk mithilfe von PowerShell
description: In diesem Artikel erfahren Sie, wie Sie Azure Firewall über Azure PowerShell bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: e60c829831bde3b454ab180d1a39ec46cb346963
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658637"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk mit Azure PowerShell

Wenn Sie Ihr lokales Netzwerk mit einem virtuellen Azure-Netzwerk verbinden, um ein Hybridnetzwerk zu erstellen, ist das Steuern des Zugriffs auf Ihre Azure-Netzwerkressourcen ein wichtiger Bestandteil des globalen Sicherheitsplans.

Mit Azure Firewall können Sie den Netzwerkzugriff in einem Hybridnetzwerk mithilfe von Regeln steuern, die den zulässigen und verweigerten Netzwerkdatenverkehr definieren.

Für diesen Artikel erstellen Sie drei virtuelle Netzwerke:

- **VNet-Hub**: In diesem virtuellen Netzwerk befindet sich die Firewall.
- **VNet-Spoke**: Das virtuelle Spoke-Netzwerk stellt die Workload in Azure dar.
- **VNet-Onprem**: Das lokale virtuelle Netzwerk stellt ein lokales Netzwerk dar. Bei einer tatsächlichen Bereitstellung kann die Verbindung dafür entweder mit einer VPN- oder einer ExpressRoute-Verbindung hergestellt werden. Der Einfachheit halber wird in diesem Artikel eine VPN-Gatewayverbindung verwendet, und ein in Azure eingerichtetes virtuelles Netzwerk wird als lokales Netzwerk verwendet.

![Firewall in einem Hybridnetzwerk](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Deklarieren der Variablen
* Erstellen des virtuellen Firewall-Hub-Netzwerks
* Erstellen des virtuellen Spoke-Netzwerks
* Erstellen des lokalen virtuellen Netzwerks
* Konfigurieren und Bereitstellen der Firewall
* Erstellen und Verbinden der VPN-Gateways
* Durchführen des Peerings für die virtuellen Hub- und Spoke-Netzwerke
* Erstellen der Routen
* Erstellen der virtuellen Computer
* Testen der Firewall

Wenn Sie das Azure-Portal verwenden möchten, statt dieses Tutorial durchzuarbeiten, lesen Sie [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall in einem Hybridnetzwerk über das Azure-Portal](tutorial-hybrid-portal.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel müssen Sie PowerShell lokal ausführen. Das Azure PowerShell-Modul muss installiert sein. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Führen Sie nach dem Überprüfen der PowerShell-Version `Login-AzAccount` aus, um eine Verbindung mit Azure zu erstellen.

Es gibt drei wichtige Anforderungen, die erfüllt sein müssen, damit dieses Szenario richtig funktioniert:

- Eine benutzerdefinierte Route (User Defined Route, UDR) im Spoke-Subnetz, das auf die Azure Firewall-IP-Adresse als Standardgateway verweist. Die Routenverteilung für das Gateway für virtuelle Netzwerke muss für diese Routentabelle auf **Deaktiviert** festgelegt werden.
- Eine UDR im Hub-Gatewaysubnetz muss auf die Firewall-IP-Adresse als nächsten Hop auf dem Weg zu den Spoke-Netzwerken verweisen.

   Für das Azure Firewall-Subnetz ist keine UDR erforderlich, da es die Routen über BGP erlernt.
- Stellen Sie sicher, dass **AllowGatewayTransit** für das Peering von VNet-Hub mit VNet-Spoke und **UseRemoteGateways** für das Peering von VNet-Spoke mit VNet-Hub festgelegt ist.

Informationen zur Erstellung dieser Routen finden Sie in diesem Artikel im Abschnitt [Erstellen von Routen](#create-the-routes).

>[!NOTE]
>Azure Firewall muss über eine direkte Internetverbindung verfügen. Wenn Ihr Subnetz „AzureFirewallSubnet“ eine Standardroute zu Ihrem lokalen Netzwerk über BGP erfasst, müssen Sie diese mit der benutzerdefinierten Route 0.0.0.0/0 überschreiben. Legen Sie dabei den Wert **NextHopType** auf **Internet** fest, um die direkte Internetkonnektivität beizubehalten.
>
>Azure Firewall kann so konfiguriert werden, dass die Tunnelerzwingung unterstützt wird. Weitere Informationen finden Sie unter [Azure Firewall-Tunnelerzwingung](forced-tunneling.md).

>[!NOTE]
>Der Datenverkehr zwischen per direktem Peering verbundenen VNETs wird direkt weitergeleitet, auch wenn eine UDR auf Azure Firewall als Standardgateway verweist. Um in diesem Szenario Subnetz-zu-Subnetz-Datenverkehr an die Firewall zu senden, muss eine UDR explizit das Zielsubnetzwerk-Präfix in beiden Subnetzen enthalten.

Die entsprechende Azure PowerShell-Referenzdokumentation finden Sie [hier](/powershell/module/az.network/new-azfirewall).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="declare-the-variables"></a>Deklarieren der Variablen

Im folgenden Beispiel werden die Variablen mit den Werten für diesen Artikel deklariert. In einigen Fällen müssen Sie zur Verwendung Ihres Abonnements bestimmte Werte durch Ihre eigenen Werte ersetzen. Ändern Sie die Variablen ggf., kopieren Sie sie, und fügen Sie sie in die PowerShell-Konsole ein.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Erstellen des virtuellen Firewall-Hub-Netzwerks

Erstellen Sie zunächst die Ressourcengruppe für die in diesem Artikel verwendeten Ressourcen:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Definieren Sie die im virtuellen Netzwerk hinzuzufügenden Subnetze:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Erstellen Sie nun das virtuelle Firewall-Hub-Netzwerk:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Fordern Sie eine öffentliche IP-Adresse an, die dem VPN-Gateway zugeordnet wird, das Sie für Ihr virtuelles Netzwerk erstellen. Beachten Sie, dass *AllocationMethod* auf **Dynamic** festgelegt ist. Es ist nicht möglich, die IP-Adresse selbst anzugeben. Sie wird Ihrem VPN-Gateway dynamisch zugewiesen.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Erstellen des virtuellen Spoke-Netzwerks

Definieren Sie die im virtuellen Spoke-Netzwerk hinzuzufügenden Subnetze:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Erstellen Sie das virtuelle Spoke-Netzwerk:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Erstellen des lokalen virtuellen Netzwerks

Definieren Sie die im virtuellen Netzwerk hinzuzufügenden Subnetze:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Erstellen Sie nun das lokale virtuelle Netzwerk:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Fordern Sie eine öffentliche IP-Adresse an, die dem Gateway zugeordnet wird, das Sie für das virtuelle Netzwerk erstellen. Beachten Sie, dass *AllocationMethod* auf **Dynamic** festgelegt ist. Es ist nicht möglich, die IP-Adresse selbst anzugeben. Sie wird Ihrem Gateway dynamisch zugewiesen.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurieren und Bereitstellen der Firewall

Stellen Sie nun die Firewall im virtuellen Hub-Netzwerk bereit.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Konfigurieren von Netzwerkregeln

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Erstellen und Verbinden der VPN-Gateways

Das virtuelle Hub-Netzwerk und das lokale virtuelle Netzwerk werden über VPN-Gateways verbunden.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Erstellen eines VPN-Gateways für das virtuelle Hub-Netzwerk

Erstellen Sie die VPN-Gatewaykonfiguration. In der Konfiguration des VPN-Gateways werden das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse definiert.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Erstellen Sie nun das VPN-Gateway für das virtuelle Hub-Netzwerk. Netzwerk-zu-Netzwerk-Konfigurationen erfordern einen routenbasierten VPN-Typ. Häufig kann die Erstellung eines VPN-Gateways je nach ausgewählter VPN-Gateway-SKU mindestens 45 Minuten dauern.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Erstellen eines VPN-Gateways für das lokale virtuelle Netzwerk

Erstellen Sie die VPN-Gatewaykonfiguration. In der Konfiguration des VPN-Gateways werden das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse definiert.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Erstellen Sie nun das VPN-Gateway für das lokale virtuelle Netzwerk. Netzwerk-zu-Netzwerk-Konfigurationen erfordern einen routenbasierten VPN-Typ. Häufig kann die Erstellung eines VPN-Gateways je nach ausgewählter VPN-Gateway-SKU mindestens 45 Minuten dauern.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Erstellen der VPN-Verbindungen

Jetzt können Sie die VPN-Verbindungen zwischen dem Hub-Gateway und dem lokalen Gateway erstellen.

#### <a name="get-the-vpn-gateways"></a>Abrufen der VPN-Gateways

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Erstellen der Verbindungen

In diesem Schritt erstellen Sie die Verbindung vom virtuellen Hub-Netzwerk zum lokalen virtuellen Netzwerk. In den Beispielen wird auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können eigene Werte für den gemeinsam verwendeten Schlüssel verwenden. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Erstellen Sie die Verbindung vom lokalen virtuellen Netzwerk zum virtuellen Hub-Netzwerk. Dieser Schritt ähnelt dem vorherigen Schritt, hier erstellen Sie jedoch die Verbindung vom lokalen virtuellen Netzwerk zum virtuellen Hub-Netzwerk. Stellen Sie sicher, dass die freigegebenen Schlüssel übereinstimmen. Nach einigen Minuten wird die Verbindung hergestellt.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Überprüfen der Verbindung

Sie können überprüfen, ob die Verbindungserstellung erfolgreich war, indem Sie das Cmdlet *Get-AzVirtualNetworkGatewayConnection* mit oder ohne den Zusatz *-Debug* verwenden. Verwenden Sie das folgende Cmdlet-Beispiel, und konfigurieren Sie die Werte so, dass sie Ihren eigenen Werten entsprechen. Wählen Sie bei entsprechender Aufforderung **A** (für **Alle**), um alles auszuführen. In dem Beispiel verweist *-Name* auf den Namen der Verbindung, die Sie testen möchten.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Zeigen Sie die Werte an, nachdem der Cmdlet-Vorgang beendet wurde. Im folgenden Beispiel ist der Verbindungsstatus als *Connected* (Verbunden) angegeben, und Sie sehen die Eingangs- und Ausgangsbytes.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Durchführen des Peerings für die virtuellen Hub- und Spoke-Netzwerke

Führen Sie nun das Peering für die virtuellen Hub- und Spoke-Netzwerke durch.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Erstellen der Routen

Erstellen Sie als Nächstes zwei Routen:

- Eine Route vom Hub-Gatewaysubnetz zum Spoke-Subnetz über die Firewall-IP-Adresse
- Eine Standardroute vom Spoke-Subnetz über die Firewall-IP-Adresse

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie nun die VM für die Spoke-Workload und die lokale VM, und platzieren Sie sie in den entsprechenden Subnetzen.

### <a name="create-the-workload-virtual-machine"></a>Erstellen des virtuellen Workloadcomputers

Erstellen Sie im virtuellen Spoke-Netzwerk eine VM, die IIS ausführt, keine öffentliche IP-Adresse besitzt und eingehende Pings zulässt.
Geben Sie bei entsprechender Aufforderung einen Benutzernamen und das Kennwort für den virtuellen Computer ein.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Erstellen der lokalen VM

Dies ist eine einfache VM, die Sie verwenden, um über Remotedesktop eine Verbindung mit der öffentlichen IP-Adresse herzustellen. Von dort aus stellen Sie dann über die Firewall eine Verbindung mit dem lokalen Server her. Geben Sie bei entsprechender Aufforderung einen Benutzernamen und das Kennwort für den virtuellen Computer ein.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testen der Firewall

Rufen Sie zuerst die private IP-Adresse für die VM **VM-spoke-01** ab, und notieren Sie sie.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Stellen Sie über das Azure-Portal eine Verbindung mit dem virtuellen Computer **VM-Onprem** her.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Öffnen Sie auf **VM-Onprem** einen Webbrowser, und navigieren Sie zu „http://\<VM-spoke-01 private IP\>“.

Die Standardseite von Internetinformationsdienste sollte angezeigt werden.

Öffnen Sie auf **VM-Onprem** eine Remotedesktopverbindung unter der privaten IP-Adresse für **VM-spoke-01**.

Die Verbindungsherstellung sollte erfolgreich sein, und Sie sollten sich mit Ihrem gewähltem Benutzernamen und dem Kennwort anmelden können.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

<!---- You can ping the server on the spoke VNet.--->
- Sie können den Webserver im virtuellen Spoke-Netzwerk durchsuchen.
- Sie können per Remotedesktopprotokoll (RDP) eine Verbindung mit dem Server im virtuellen Spoke-Netzwerk herstellen.

Ändern Sie als Nächstes die Aktion zum Sammeln von Firewallnetzwerkregeln in **Verweigern**, um zu überprüfen, ob die Firewallregeln wie erwartet funktionieren. Führen Sie das folgende Skript aus, um die Aktion für die Regelsammlung in **Verweigern** zu ändern.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Führen Sie die Tests nun erneut durch. Dieses Mal sollten alle Tests fehlschlagen. Schließen Sie alle vorhandenen Remotedesktops, bevor Sie die geänderten Regeln testen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **FW-Hybrid-Test** löschen, wenn Sie sie nicht mehr benötigen. Hierdurch werden alle firewallbezogenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie die Azure Firewall-Protokolle überwachen.

[Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md)