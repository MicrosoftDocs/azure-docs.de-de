---
title: Bereitstellen einer IPv6-Anwendung mit dualem Stapel und interner Load Balancer Standard-Instanz in Azure – PowerShell
titlesuffix: Azure Virtual Network
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell eine IPv6-Anwendung mit dualem Stapel und interner Load Balancer Standard-Instanz in einem virtuellen Azure-Netzwerk bereitstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: 759bd94aee98aa04dee56acf0e50ca90cd0541b8
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "96000605"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Bereitstellen einer IPv6-Anwendung mit dualem Stapel und interner Load Balancer Standard-Instanz in Azure – PowerShell (Vorschauversion)

In diesem Artikel erfahren Sie, wie Sie in Azure eine Anwendung mit dualem Stapel (IPv4 und IPv6) bereitstellen, die ein virtuelles Netzwerk mit dualem Stapel und Subnetz, eine interne Load Balancer Standard-Instanz mit dualen Front-End-Konfigurationen (IPv4 und IPv6), virtuelle Computer mit NICs mit dualer IP-Konfiguration, Netzwerksicherheitsgruppenregeln und öffentliche IP-Adressen umfasst.

Die Vorgehensweise zum Erstellen einer IPv6-fähigen internen Load Balancer-Instanz ist nahezu identisch mit der [hier](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) beschriebenen Vorgehensweise zum Erstellen einer IPv6-Load Balancer-Instanz mit Internetzugriff. Bei der Erstellung eines internen Lastenausgleichs unterscheidet sich lediglich die Front-End-Konfiguration, wie im folgenden PowerShell-Beispiel zu sehen:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "fd00:db8:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

Folgende Änderungen machen die obige Front-End-Konfiguration zu einer Konfiguration für einen internen Lastenausgleich:
- `PrivateIpAddressVersion` wird als „IPv6“ angegeben.
- Das Argument `-PublicIpAddress` wurde entweder weggelassen oder durch `-PrivateIpAddress` ersetzt. Die private Adresse muss sich im Bereich des Subnetz-IP-Adressraums befinden, in dem der interne Lastenausgleich bereitgestellt wird. Ohne Angabe einer statischen privaten IP-Adresse (`-PrivateIpAddress`) wird die nächste freie IPv6-Adresse aus dem Subnetz ausgewählt, in dem der interne Lastenausgleich bereitgestellt wird.
- Das Subnetz mit dualem Stapel, in dem der interne Lastenausgleich bereitgestellt wird, wird mit einem Argument vom Typ `-Subnet` oder `-SubnetId` angegeben.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 6.9.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie eine Anwendung mit dualem Stapel in Azure bereitstellen, müssen Sie Ihr Abonnement für diese Previewfunktion mit dem folgenden Azure PowerShell-Befehl konfigurieren:

Führen Sie die Registrierung wie folgt aus:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Es dauert bis zu 30 Minuten, bis die Featureregistrierung abgeschlossen ist. Sie können den Registrierungsstatus überprüfen, indem Sie den folgenden Azure PowerShell-Befehl ausführen: Überprüfen Sie die Registrierung wie folgt:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Führen Sie im Anschluss an die Registrierung den folgenden Befehl aus:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie Ihr virtuelles Netzwerk mit dualem Stapel erstellen können, müssen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe namens *dsStd_ILB_RG* am Standort *USA, Osten* erstellt:

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Erstellen der öffentlichen IPv4- und IPv6-Adressen
Um über das Internet auf Ihre virtuellen Computer zugreifen zu können, benötigen Sie öffentliche IPv4- und IPv6-Adressen für die virtuellen Computer. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) öffentliche IP-Adressen. Im folgende Beispiel werden öffentliche IPv4- und IPv6-Adressen (*RdpPublicIP_1* und *RdpPublicIP_2*) in der Ressourcengruppe *dsStd_ILB_RG* erstellt:

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Erstellen des virtuellen Netzwerks und des Subnetzes

Erstellen Sie mithilfe von [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk mit dualem Stapel und mithilfe von [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *dsVnet* mit *dsSubnet* erstellt:

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Erstellen einer Load Balancer Standard-Instanz

In diesem Abschnitt konfigurieren Sie die duale Front-End-IP-Adresse (IPv4 und IPv6) und den Back-End-Adresspool für den Lastenausgleich und erstellen anschließend einen Load Balancer Standard-Lastenausgleich.

### <a name="create-front-end-ip"></a>Erstellen der Front-End-IP-Adresse

Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse. Im folgenden Beispiel werden die Front-End-IP-Konfigurationen für IPv4 und IPv6 mit den Namen *dsLbFrontEnd_v4* und *dsLbFrontEnd_v6* erstellt:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "fd00:db8:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Konfigurieren des Back-End-Adresspools

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool. In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt. Das folgende Beispiel erstellt Back-End-Adresspools mit den Namen *dsLbBackEndPool_v4* und *dsLbBackEndPool_v6*, um VMs mit sowohl IPv4- als auch IPv6-NIC-Konfigurationen einzubeziehen:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Um sicherzustellen, dass nur fehlerfreie virtuelle Computer Datenverkehr empfangen, können Sie optional einen Integritätstest definieren. Der grundlegende Lastenausgleich verwendet einen IPv4-Test, um die Integrität sowohl für IPv4- als auch für IPv6-Endpunkte auf den virtuellen Computern zu bewerten. Der Standardlastenausgleich unterstützt explizite IPv6-Integritätstests.

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel. Das folgende Beispiel erstellt Load Balancer-Regeln mit den Namen *dsLBrule_v4* und *dsLBrule_v6* und führt Lastenausgleich des Datenverkehrs an *TCP*-Port *80* zu den IPv4- und IPv6-Front-End-IP-Konfigurationen aus:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Erstellen Sie mit dem Befehl [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) einen Load Balancer Standard-Lastenausgleich. Im folgenden Beispiel wird eine öffentliche Load Balancer Standard-Instanz namens *myInternalLoadBalancer* unter Verwendung der IPv4- und IPv6-Front-End-IP-Konfigurationen, Back-End-Pools und Lastenausgleichsregeln erstellt, die Sie in den vorherigen Schritten erstellt haben:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen
Um virtuelle Computer bereitstellen und Ihren Lastenausgleich testen zu können, müssen Sie zunächst unterstützende Netzwerkressourcen (Verfügbarkeitsgruppe, Netzwerksicherheitsgruppe und virtuelle NICs) erstellen. 

### <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen
Platzieren Sie die virtuellen Computer in einer Verfügbarkeitsgruppe, um die Hochverfügbarkeit Ihrer Anwendung zu optimieren.

Erstellen Sie mithilfe von [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) eine Verfügbarkeitsgruppe. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens *dsAVset* erstellt:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie eine Netzwerksicherheitsgruppe für die Regeln, durch die die eingehende und ausgehende Kommunikation in Ihrem VNET gesteuert wird.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 3389

Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel, um RDP-Verbindungen über den Port 3389 zuzulassen.

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 80

Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel, um Internetverbindungen über Port 80 zuzulassen.

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe.

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Erstellen von NICs

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) virtuelle NICs. Das folgende Beispiel erstellt zwei virtuelle Netzwerkkarten mit IPv4- und IPv6-Konfigurationen. (jeweils eine virtuelle NIC pro virtuellem Computer, den Sie in den folgenden Schritten für Ihre App erstellen).

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

Nun können Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) die virtuellen Computer erstellen. Im folgenden Beispiel werden zwei virtuelle Computer und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Anzeigen des virtuellen IPv6-Dual Stack-Netzwerks im Azure-Portal
Sie können das virtuelle IPv6-Dual Stack-Netzwerk im Azure-Portal wie folgt anzeigen:
1. Geben Sie in der Suchleiste des Portals *dsVnet* ein.
2. Wenn **dsVnet** in den Suchergebnissen angezeigt wird, wählen Sie diesen Eintrag aus. Dadurch wird die Seite **Übersicht** des virtuellen Dual Stack-Netzwerks namens *dsVnet* gestartet. Das virtuelle Dual Stack-Netzwerk zeigt die beiden NICs mit IPv4- und IPv6-Konfigurationen im Dual Stack-Subnetz namens *dsSubnet*.

![Virtuelles IPv6-Netzwerk mit dualem Stapel und interner Load Balancer Standard-Instanz](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Das virtuelle IPv6-Netzwerk für Azure ist im Azure-Portal schreibgeschützt für diese Vorschauversion verfügbar.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Nächste Schritte

Im Rahmen dieses Artikels haben Sie einen Load Balancer Standard-Lastenausgleich mit einer dualen Front-End-IP-Konfiguration (IPv4 und IPv6) erstellt. Sie haben außerdem zwei virtuelle Computer mit NICs mit dualen IP-Konfigurationen (IPV4 und IPv6) erstellt, die dem Back-End-Pool des Load Balancers hinzugefügt wurden. Weitere Informationen zur IPv6-Unterstützung in virtuellen Azure-Netzwerken finden Sie unter [Was ist IPv6 für Azure Virtual Network?](ipv6-overview.md)
