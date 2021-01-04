---
title: Bereitstellen einer IPv6-Anwendung mit dualem Stapel – Load Balancer Basic –PowerShell
titlesuffix: Azure Virtual Network
description: Dieser Artikel zeigt, wie eine IPv6-Anwendung mit dualem Stapel in Azure Virtual Network mit Azure PowerShell bereitgestellt wird.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 9a8b9e82f71be83254a263ae28598a43dd7c8b9f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95994450"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-basic-load-balancer---powershell"></a>Bereitstellen einer IPv6-Anwendung mit dualem Stapel und Basic-Load Balancer – PowerShell

In diesem Artikel wird veranschaulicht, wie Sie eine Anwendung mit dualem Stapel (IPv4 und IPv6) mit einem Basic-Load Balancer unter Verwendung von Azure PowerShell bereitstellen. Sie umfasst ein virtuelles Netzwerk mit dualem Stapel und ein Subnetz, einen Basic-Load Balancer mit dualen Front-End-Konfigurationen (IPv4 und IPv6), VMs mit NICs und einer dualen IP-Konfiguration, eine Netzwerksicherheitsgruppe und öffentliche IP-Adressen.

Informationen zum Bereitstellen einer Anwendung mit dualem Stapel (IPv4 und IPv6) mithilfe von Load Balancer Standard finden Sie unter dem Thema zum [Bereitstellen einer IPv6-Anwendung mit dualem Stapel und Load Balancer Standard mithilfe von Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 6.9.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.


## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie Ihr virtuelles Netzwerk mit dualem Stapel erstellen können, müssen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Das folgende Beispiel erstellt eine Ressourcengruppe namens *myRGDualStack* am Standort *east us*:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Erstellen der öffentlichen IPv4- und IPv6-Adressen
Um im Internet auf Ihre virtuellen Computer zugreifen zu können, benötigen Sie öffentliche IPv4- und IPv6-Adressen für den Lastenausgleich. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) öffentliche IP-Adressen. Das folgende Beispiel erstellt öffentliche IPv4- und IPv6-Adressen mit den Namen *dsPublicIP_v4* und *dsPublicIP_v6* in der Ressourcengruppe *dsRG1*:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Um über eine RDP-Verbindung auf Ihre virtuellen Computer zuzugreifen, erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IPV4-Adresse für die virtuellen Computer.

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Erstellen eines Load Balancers im Tarif „Basic“

In diesem Abschnitt konfigurieren Sie die duale Front-End-IP-Adresse (IPv4 ind IPv6) und den Back-End-Adresspool für den Load Balancer und erstellen anschließend den Load Balancer im Tarif „Basic“.

### <a name="create-front-end-ip"></a>Erstellen der Front-End-IP-Adresse

Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse. Im folgenden Beispiel werden die Front-End-IP-Konfigurationen für IPv4 und IPv6 mit den Namen *dsLbFrontEnd_v4* und *dsLbFrontEnd_v6* erstellt:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Konfigurieren des Back-End-Adresspools

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool. In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt. Das folgende Beispiel erstellt Back-End-Adresspools mit den Namen *dsLbBackEndPool_v4* und *dsLbBackEndPool_v6*, um VMs mit sowohl IPv4- als auch IPv6-NIC-Konfigurationen einzubeziehen:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```
### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests
Fügen Sie [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) hinzu, um einen Integritätstest zum Überwachen der Integrität der VMs zu erstellen.
```azurepowershell
$probe = New-AzLoadBalancerProbeConfig -Name MyProbe -Protocol tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
```
### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Um sicherzustellen, dass nur fehlerfreie virtuelle Computer Datenverkehr empfangen, können Sie optional einen Integritätstest definieren. Der grundlegende Lastenausgleich verwendet einen IPv4-Test, um die Integrität sowohl für IPv4- als auch für IPv6-Endpunkte auf den virtuellen Computern zu bewerten. Der Standardlastenausgleich unterstützt explizite IPv6-Integritätstests.

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel. Das folgende Beispiel erstellt Load Balancer-Regeln mit den Namen *dsLBrule_v4* und *dsLBrule_v6* und führt Lastenausgleich des Datenverkehrs an *TCP*-Port *80* zu den IPv4- und IPv6-Front-End-IP-Konfigurationen aus:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -probe $probe
```

### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) den Lastenausgleich im Tarif „Basic“. Im folgenden Beispiel wird mit den in den vorherigen Schritten erstellten IPv4- und IPv6-Front-End-IP-Konfigurationen, Back-End-Pools und Lastenausgleichsregeln ein öffentlicher Lastenausgleich im Basic-Tarif mit dem Namen *myLoadBalancer* erstellt:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen
Bevor Sie einige VMs bereitstellen und Ihren Lastenausgleich testen können, müssen Sie unterstützende Netzwerkressourcen erstellen: Verfügbarkeitsgruppe, Netzwerksicherheitsgruppe, virtuelles Netzwerk und virtuelle NICs. 
### <a name="create-an-availability-set"></a>Verfügbarkeitsgruppe erstellen
Fügen Sie die virtuellen Computer in eine Verfügbarkeitsgruppe ein, um die Hochverfügbarkeit Ihrer App zu optimieren.

Erstellen Sie mithilfe von [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) eine Verfügbarkeitsgruppe. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens *myAvailabilitySet* erstellt:

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVnet* mit einem Subnetz namens *mySubnet* erstellt:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","fd00:db8:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","fd00:db8:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Erstellen von NICs

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) virtuelle NICs. Das folgende Beispiel erstellt zwei virtuelle Netzwerkkarten mit IPv4- und IPv6-Konfigurationen. (jeweils eine virtuelle NIC pro virtuellem Computer, den Sie in den folgenden Schritten für Ihre App erstellen).

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
      
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

Nun können Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) die virtuellen Computer erstellen. Im folgenden Beispiel werden zwei virtuelle Computer und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind. 

```azurepowershell-interactive
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

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Ermitteln der IP-Adressen der IPv4- und IPv6-Endpunkte
Rufen Sie alle Netzwerkschnittstellen-Objekte in der Ressourcengruppe ab, um die in dieser Bereitstellung mit `get-AzNetworkInterface` verwendeten IP-Adressen zusammenzufassen. Rufen Sie außerdem die Front-End-Adressen der IPv4- und IPv6-Endpunkte des Lastenausgleichs mit `get-AzpublicIpAddress` ab.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
Die folgende Abbildung zeigt eine Beispielausgabe, die die privaten IPv4- und IPv6-Adressen der beiden virtuellen Computer sowie die IPv4- und IPv6-IP-Adressen des Front-Ends für den Lastenausgleich auflistet.

![IP-Zusammenfassung der Bereitstellung einer Anwendung mit dualem Stapel (IPv4/IPv6) in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Anzeigen des virtuellen IPv6-Dual Stack-Netzwerks im Azure-Portal
Sie können das virtuelle IPv6-Dual Stack-Netzwerk im Azure-Portal wie folgt anzeigen:
1. Geben Sie in der Suchleiste des Portals *dsVnet* ein.
2. Wenn **myVirtualNetwork** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen. Dadurch wird die Seite **Übersicht** des virtuellen Dual Stack-Netzwerks namens *dsVnet* gestartet. Das virtuelle Dual Stack-Netzwerk zeigt die beiden NICs mit IPv4- und IPv6-Konfigurationen im Dual Stack-Subnetz namens *dsSubnet*.

  ![Virtuelles IPv6-Dual Stack-Netzwerk in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Load Balancer im Tarif „Basic“ mit einer dualen Front-End-IP-Konfiguration (IPv4 und IPv6) erstellt. Sie haben außerdem zwei virtuelle Computer mit NICs mit dualen IP-Konfigurationen (IPV4 und IPv6) erstellt, die dem Back-End-Pool des Load Balancers hinzugefügt wurden. Weitere Informationen zur IPv6-Unterstützung in virtuellen Azure-Netzwerken finden Sie unter [Was ist IPv6 für Azure Virtual Network?](ipv6-overview.md)
