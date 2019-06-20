---
title: 'Schnellstart: Erstellen einer Load Balancer Standard-Instanz – Azure PowerShell'
titlesuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit PowerShell eine Load Balancer Standard-Instanz erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 04db0232e14ccac7938d7062d77c36a54526489c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730470"
---
# <a name="quickstart-create-a-standard-load-balancer-using-azure-powershell"></a>Schnellstart: Erstellen einer Load Balancer Standard-Instanz mit Azure PowerShell

In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Azure PowerShell eine Load Balancer Standard-Instanz erstellen. Zum Testen des Lastenausgleichs stellen Sie drei virtuelle Computer (VMs) mit Windows Server bereit und führen für eine Web-App zwischen den VMs einen Lastenausgleich durch. Weitere Informationen zu Load Balancer Standard finden Sie unter [Übersicht: Azure Standard Load Balancer](load-balancer-standard-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie Ihren Lastenausgleich erstellen können, müssen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupSLB* am Standort *EastUS* erstellt:

```azurepowershell
$rgName='MyResourceGroupSLB'
$location='eastus'
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Um über das Internet auf Ihre App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Load Balancer. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IP-Adresse. Im folgenden Beispiel wird in der Ressourcengruppe *myResourceGroupSLB* eine öffentliche IP-Adresse mit dem Namen *myPublicIP* erstellt:

```azurepowershell
$publicIp = New-AzPublicIpAddress `
 -ResourceGroupName $rgName `
 -Name 'myPublicIP' `
 -Location $location `
 -AllocationMethod static `
 -SKU Standard
```

## <a name="create-standard-load-balancer"></a>Erstellen einer Load Balancer Standard-Instanz

In diesem Abschnitt konfigurieren Sie die Front-End-IP-Adresse und den Back-End-Adresspool für den Lastenausgleich und erstellen anschließend die Load Balancer Standard-Instanz.

### <a name="create-front-end-ip"></a>Erstellen der Front-End-IP-Adresse

Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse. Im folgenden Beispiel wird eine Front-End-IP-Konfiguration namens *myFrontEnd* erstellt und die Adresse *myPublicIP* angefügt:

```azurepowershell
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Konfigurieren des Back-End-Adresspools

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool. In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt. Im folgenden Beispiel wird ein Back-End-Adresspool namens *myBackEndPool* erstellt:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests
Damit der Load Balancer den Status Ihrer App überwachen kann, verwenden Sie einen Integritätstest. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Rotation durch den Integritätstest dynamisch virtuelle Computer hinzugefügt oder daraus entfernt. Falls bei einem virtuellen Computer innerhalb eines Intervalls von 15 Sekunden zwei aufeinanderfolgende Fehler auftreten, wird er standardmäßig aus der Load Balancer-Verteilung entfernt. Integritätstests werden auf der Grundlage eines Protokolls oder einer spezifischen Integritätsprüfungsseite für Ihre App erstellt.

Im folgenden Beispiel wird ein TCP-Test erstellt. Sie können auch benutzerdefinierte HTTP-Tests für differenziertere Integritätsprüfungen erstellen. Bei Verwendung eines benutzerdefinierten HTTP-Tests müssen Sie die Integritätsprüfungsseite erstellen, z.B. *healthcheck.aspx*. Der Test muss die HTTP-Antwort **200 OK** zurückgeben, damit der Load Balancer den Host nicht aus der Rotation entfernt.

Erstellen Sie mithilfe von [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen TCP-Integritätstest. Im folgenden Beispiel wird ein Integritätstest mit dem Namen *myHealthProbe* erstellt, der die einzelnen virtuellen Computer an *HTTP*-Port *80* überwacht:

```azurepowershell
$probe = New-AzLoadBalancerProbeConfig `
 -Name 'myHealthProbe' `
 -Protocol Http -Port 80 `
 -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel
Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Um sicherzustellen, dass nur fehlerfreie virtuelle Computer Datenverkehr empfangen, definieren Sie zudem den zu verwendenden Integritätstest.

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel. Im folgenden Beispiel wird eine Lastenausgleichsregel mit dem Namen *myLoadBalancerRule* erstellt und der Datenverkehr an *TCP*-Port *80* ausgeglichen:

```azurepowershell
$rule = New-AzLoadBalancerRuleConfig `
  -Name 'myLoadBalancerRuleWeb' -Protocol Tcp `
  -Probe $probe -FrontendPort 80 -BackendPort 80 `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bePool
```

### <a name="create-the-nat-rules"></a>Erstellen der NAT-Regeln

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) NAT-Regeln. Im folgenden Beispiel werden NAT-Regeln mit den Namen *myLoadBalancerRDP1* und *myLoadBalancerRDP2* erstellt, um RDP-Verbindungen mit den Back-End-Servern über die Ports 4221 und 4222 zuzulassen:

```azurepowershell
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP1' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp -FrontendPort 4221 `
  -BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP2' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4222 `
  -BackendPort 3389

$natrule3 = New-AzLoadBalancerInboundNatRuleConfig `
  -Name 'myLoadBalancerRDP3' `
  -FrontendIpConfiguration $feip `
  -Protocol tcp `
  -FrontendPort 4223 `
  -BackendPort 3389
```

### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) die Load Balancer Standard-Instanz. Im folgenden Beispiel wird unter Verwendung der Front-End-IP-Konfiguration, des Back-End-Pools, des Integritätstests, der Lastenausgleichsregel und der NAT-Regeln aus den obigen Schritten eine öffentliche Load Balancer Standard-Instanz namens „myLoadBalancer“ erstellt:

```azurepowershell
$lb = New-AzLoadBalancer `
  -ResourceGroupName $rgName `
  -Name 'MyLoadBalancer' `
  -SKU Standard `
  -Location $location `
  -FrontendIpConfiguration $feip `
  -BackendAddressPool $bepool `
  -Probe $probe `
  -LoadBalancingRule $rule `
  -InboundNatRule $natrule1,$natrule2,$natrule3
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen
Bevor Sie einige VMs bereitstellen und Ihren Load Balancer testen können, müssen Sie unterstützende Netzwerkressourcen erstellen: ein virtuelles Netzwerk und virtuelle NICs. 

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks
Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVnet* mit einem Subnetz namens *mySubnet* erstellt:

```azurepowershell
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupSLB" `
  -Location $location `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Erstellen öffentlicher IP-Adressen für die virtuellen Computer

Für den Zugriff auf Ihre virtuellen Computer über eine RDP-Verbindung benötigen Sie eine öffentliche IP-Adresse für die virtuellen Computer. Da in diesem Szenario eine Load Balancer Standard-Instanz verwendet wird, müssen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) öffentliche Standard-IP-Adressen für die virtuellen Computer erstellen.

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static
 

$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static


$RdpPublicIP_3 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_3" `
  -ResourceGroupName $RgName `
  -Location $location  `
  -SKU Standard `
  -AllocationMethod static

```
### <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 3389
Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel, um RDP-Verbindungen über den Port 3389 zuzulassen.

```azurepowershell

$rule1 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleRDP' -Description 'Allow RDP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 1000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 80
Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel, um eingehende Verbindungen über den Port 80 zuzulassen.

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig -Name 'myNetworkSecurityGroupRuleHTTP' -Description 'Allow HTTP' `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 2000 `
  -SourceAddressPrefix Internet -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe.

```azurepowershell
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RgName -Location $location `
-Name 'myNetworkSecurityGroup' -SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Erstellen von NICs
Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) virtuelle Netzwerkadapter, und weisen Sie ihnen die öffentliche IP-Adresse und Netzwerksicherheitsgruppen zu, die Sie zuvor erstellt haben. Im folgenden Beispiel werden drei virtuelle NICs erstellt (jeweils eine virtuelle NIC pro virtuellem Computer, den Sie in den folgenden Schritten für Ihre App erstellen). Sie können jederzeit weitere virtuelle NICs und virtuelle Computer erstellen und dem Load Balancer hinzufügen:

```azurepowershell
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic1' -PublicIpAddress $RdpPublicIP_1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule1 -Subnet $vnet.Subnets[0]

$nicVM2 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic2' -PublicIpAddress $RdpPublicIP_2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule2 -Subnet $vnet.Subnets[0]

$nicVM3 = New-AzNetworkInterface -ResourceGroupName $rgName -Location $location `
  -Name 'MyNic3' -PublicIpAddress $RdpPublicIP_3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg `
  -LoadBalancerInboundNatRule $natrule3 -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell
$cred = Get-Credential
```

Nun können Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) die virtuellen Computer erstellen. Im folgenden Beispiel werden zwei virtuelle Computer und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind. In diesem Beispiel werden die im vorherigen Schritt erstellten Netzwerkadapter (*MyNic1*, *MyNic2* und *MyNic3*) den virtuellen Computern *myVM1*, *myVM2* und *VM3* zugewiesen. Darüber hinaus werden die virtuellen Computer automatisch dem Back-End-Pool hinzugefügt, da die NICs dem Back-End-Pool des Lastenausgleichs zugeordnet sind.

```azurepowershell

# ############## VM1 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM1' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM1' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM1.Id

# Create a virtual machine
$vm1 = New-AzVM -ResourceGroupName $rgName -Zone 1 -Location $location -VM $vmConfig

# ############## VM2 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM2' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM2' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
 | Add-AzVMNetworkInterface -Id $nicVM2.Id

# Create a virtual machine
$vm2 = New-AzVM -ResourceGroupName $rgName -Zone 2 -Location $location -VM $vmConfig

# ############## VM3 ###############

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 `
 | Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred `
 | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2019-Datacenter -Version latest `
| Add-AzVMNetworkInterface -Id $nicVM3.Id

# Create a virtual machine
$vm3 = New-AzVM -ResourceGroupName $rgName -Zone 3 -Location $location -VM $vmConfig
```

Die Erstellung und Konfiguration der drei virtuellen Computer dauert einige Minuten.

### <a name="install-iis-with-a-custom-web-page"></a>Installieren der IIS mit benutzerdefinierter Webseite

Installieren Sie IIS mit einer benutzerdefinierten Webseite auf beiden virtuellen Back-End-Computern:

1. Rufen Sie die öffentlichen IP-Adressen der drei virtuellen Computer mithilfe von `Get-AzPublicIPAddress` ab.

   ```azurepowershell
     $vm1_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_1").IpAddress
     $vm2_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_2").IpAddress
     $vm3_rdp_ip = (Get-AzPublicIPAddress -ResourceGroupName $rgName -Name "RdpPublicIP_3").IpAddress
    ```
2. Erstellen Sie wie folgt mithilfe der öffentlichen IP-Adressen der virtuellen Computer Remotedesktopverbindungen mit *myVM1*, *myVM2* und *myVM3*: 

   ```azurepowershell    
     mstsc /v:$vm1_rdp_ip
     mstsc /v:$vm2_rdp_ip
     mstsc /v:$vm3_rdp_ip
   
    ```

3. Geben Sie die Anmeldeinformationen für die einzelnen virtuellen Computer ein, um die RDP-Sitzung zu starten.
4. Starten Sie Windows PowerShell auf jedem virtuellen Computer, und verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.

    ```azurepowershell
    # Install IIS
     Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
    ```

5. Schließen Sie die RDP-Verbindungen mit *myVM1*, *myVM2* und *myVM3*.


## <a name="test-load-balancer"></a>Testen des Load Balancers
Rufen Sie mit [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres Lastenausgleichs ab. Im folgenden Beispiel wird die IP-Adresse für *myPublicIP* abgerufen, die wir zuvor erstellt haben:

```azurepowershell
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupSLB" `
  -Name "myPublicIP" | select IpAddress
```

Geben Sie die öffentliche IP-Adresse in einem Webbrowser ein. Die Website wird mit dem Hostnamen der VM angezeigt, an die der Load Balancer den Datenverkehr wie im folgenden Beispiel verteilt hat:

![Testen des Load Balancers](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Sie können eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Load Balancer den Datenverkehr auf alle drei virtuellen Computer verteilt, auf denen Ihre App ausgeführt wird. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroupSLB
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Lastenausgleich im Standard-Tarif erstellt, virtuelle Computer angefügt, die Datenverkehrsregel für den Lastenausgleich sowie einen Integritätstest konfiguriert und den Lastenausgleich getestet. Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials zu Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-basic-internal-portal.md)
