---
title: 'Schnellstart: Erstellen eines internen Lastenausgleichs – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell einen internen Lastenausgleich erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696777"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Schnellstart: Erstellen eines internen Lastenausgleichs für den Lastenausgleich virtueller Computer mithilfe von Azure PowerShell

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie über Azure PowerShell einen internen Lastenausgleich und zwei virtuelle Computer erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe:

* Benannt als **CreateIntLBQS-rg**.
* Standort: **eastus**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer bereitstellen und Ihren Lastenausgleich testen.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Azure Bastion-Hosts

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk:

* Name: **myVNet**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Subnetzname: **myBackendSubnet**
* Virtuelles Netzwerk: **10.0.0.0/16**
* Subnetz: **10.0.0.0/24**
* Subnetz: **AzureBastionSubnet**
* Subnetz: **10.0.1.0/24**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Erstellen einer öffentlichen IP-Adresse für Azure Bastion-Host

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Name: **myPublicIPBastion**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Zuordnungsmethode: **Statisch**
* SKU: **Standard**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Erstellen des Azure Bastion-Hosts

Verwenden Sie [New-AzBastion](/powershell/module/az.network/new-azbastion), um einen Bastionhost zu erstellen:

* Name: **myBastion**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Zugeordnet zur öffentlichen IP-Adresse **myPublicIPBastion**

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.

### <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 80
Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel:

* Name: **myNSGRuleHTTP**
* Beschreibung: **Allow HTTP**
* Zugriff: **Zulassen**
* Protokoll **(*)**
* Richtung: **Eingehend**
* Priorität: **2000**
* Quelle: **Internet**
* Quellportbereich: **(*)**
* Zieladresspräfix: **(*)**
* Ziel: **Port 80**

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe:

* Name: **myNSG**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Mit den in vorherigen Schritten erstellten Sicherheitsregeln (gespeichert in einer Variablen)

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Erstellen eines Standard-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

  * Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  * Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  * Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  * Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-frontend-ip"></a>Erstellen der Front-End-IP-Adresse

Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse:

* Name: **myFrontEnd**
* Private IP-Adresse: **10.0.0.4**

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Konfigurieren des Back-End-Adresspools

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool: 

* Name: **myBackEndPool**
* In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Von einem Integritätstest werden alle VM-Instanzen überprüft, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. 

Ist der Test bei einem virtuellen Computer nicht erfolgreich, wird er aus dem Lastenausgleich entfernt. Nach Behebung des Fehlers wird der virtuelle Computer dem Lastenausgleich wieder hinzugefügt.

Erstellen Sie mit [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen Integritätstest:

* Überwacht die Integrität der virtuellen Computer
* Name: **myHealthProbe**
* Protokoll: **TCP**
* Überwachter Port: **80**

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel: 

* Name: **myHTTPRule**
* Lauscht am **Port 80** im Front-End-Pool **myFrontEnd**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** unter Verwendung von **Port 80** 
* Verwendet den Integritätstest **myHealthProbe**
* Protokoll: **TCP**
* Leerlaufzeitüberschreitung von **15 Minuten**.
* Aktivieren Sie die TCP-Zurücksetzung.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>Die virtuellen Computer im Back-End-Pool verfügen mit dieser Konfiguration nicht über ausgehende Internetkonnektivität. </br> Weitere Informationen zu ausgehender Konnektivität finden Sie unter: </br> **[Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md)**</br> Optionen zum Bereitstellen von Konnektivität: </br> **[Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)](egress-only.md)** </br> **[Was ist Virtual Network NAT?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) einen internen Lastenausgleich:

* Name: **myLoadBalancer**
* Standort: **eastus**
* In Ressourcengruppe **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Erstellen von Netzwerkschnittstellen

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) drei Netzwerkschnittstellen:

#### <a name="vm-1"></a>VM 1

* Name: **myNicVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**
* Angefügt an den Lastenausgleich **myLoadBalancer** in **myBackEndPool**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Name: **myNicVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**
* Angefügt an den Lastenausgleich **myLoadBalancer** in **myBackEndPool**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell
$cred = Get-Credential
```

Erstellen Sie die virtuellen Computer mit Folgendem:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Name: **myVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM1**
* Angefügt an den Lastenausgleich **myLoadBalancer**
* Zone: **Zone 1**
* Standort: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Name: **myVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM2**
* Angefügt an den Lastenausgleich **myLoadBalancer**
* Zone: **Zone 2**
* Standort: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer bereitstellen und Ihren Lastenausgleich testen.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Azure Bastion-Hosts

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk:

* Name: **myVNet**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Subnetzname: **myBackendSubnet**
* Virtuelles Netzwerk: **10.0.0.0/16**
* Subnetz: **10.0.0.0/24**
* Subnetz: **AzureBastionSubnet**
* Subnetz: **10.0.1.0/24**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Erstellen einer öffentlichen IP-Adresse für Azure Bastion-Host

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche IP-Adresse für den Bastionhost zu erstellen:

* Name: **myPublicIPBastion**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Zuordnungsmethode: **Statisch**
* SKU: **Standard**

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Erstellen des Azure Bastion-Hosts

Verwenden Sie [New-AzBastion](/powershell/module/az.network/new-azbastion), um einen Bastionhost zu erstellen:

* Name: **myBastion**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Virtuelles Netzwerk: **myVNet**
* Zugeordnet zur öffentlichen IP-Adresse **myPublicIPBastion**

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

Die Bereitstellung des Azure Bastion-Hosts kann einige Minuten dauern.


### <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe
Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 80
Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel:

* Name: **myNSGRuleHTTP**
* Beschreibung: **Allow HTTP**
* Zugriff: **Zulassen**
* Protokoll **(*)**
* Richtung: **Eingehend**
* Priorität: **2000**
* Quelle: **Internet**
* Quellportbereich: **(*)**
* Zieladresspräfix: **(*)**
* Ziel: **Port 80**

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe:

* Name: **myNSG**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Mit den in vorherigen Schritten erstellten Sicherheitsregeln (gespeichert in einer Variablen)

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Erstellen eines Basic-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

  * Front-End-IP-Pool, der den eingehenden Netzwerkdatenverkehr für den Lastenausgleich empfängt
  * Back-End-IP-Pool, an den der Front-End-Pool den Netzwerkdatenverkehr sendet, für den ein Lastenausgleich durchgeführt wurde
  * Integritätstest zum Ermitteln der Integrität der Back-End-VM-Instanzen
  * Lastenausgleichsregel, mit der definiert wird, wie Datenverkehr auf die virtuellen Computer verteilt werden soll

### <a name="create-frontend-ip"></a>Erstellen der Front-End-IP-Adresse

Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse:

* Name: **myFrontEnd**
* Private IP-Adresse: **10.0.0.4**

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Konfigurieren des Back-End-Adresspools

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool: 

* Name: **myBackEndPool**
* In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Erstellen des Integritätstests

Von einem Integritätstest werden alle VM-Instanzen überprüft, um sicherzustellen, dass sie Netzwerkdatenverkehr senden können. 

Ist der Test bei einem virtuellen Computer nicht erfolgreich, wird er aus dem Lastenausgleich entfernt. Nach Behebung des Fehlers wird der virtuelle Computer dem Lastenausgleich wieder hinzugefügt.

Erstellen Sie mit [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen Integritätstest:

* Überwacht die Integrität der virtuellen Computer
* Name: **myHealthProbe**
* Protokoll: **TCP**
* Überwachter Port: **80**

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Erstellen der Lastenausgleichsregel

Durch eine Lastenausgleichsregel wird Folgendes definiert:

* Front-End-IP-Konfiguration für den eingehenden Datenverkehr.
* Back-End-IP-Pool zum Empfangen des Datenverkehrs.
* Erforderliche Quell- und Zielports. 

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel: 

* Name: **myHTTPRule**
* Lauscht am **Port 80** im Front-End-Pool **myFrontEnd**
* Sendet Netzwerkdatenverkehr, für den ein Lastenausgleich durchgeführt wurde, an den Back-End-Adresspool **myBackEndPool** unter Verwendung von **Port 80** 
* Verwendet den Integritätstest **myHealthProbe**
* Protokoll: **TCP**
* Leerlaufzeitüberschreitung von **15 Minuten**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Erstellen der Lastenausgleichsressource

Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) einen öffentlichen Lastenausgleich:

* Name: **myLoadBalancer**
* Standort: **eastus**
* In Ressourcengruppe **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Erstellen von Netzwerkschnittstellen

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) drei Netzwerkschnittstellen:

#### <a name="vm-1"></a>VM 1

* Name: **myNicVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**
* Angefügt an den Lastenausgleich **myLoadBalancer** in **myBackEndPool**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Name: **myNicVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**
* Angefügt an den Lastenausgleich **myLoadBalancer** in **myBackEndPool**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Erstellen einer Verfügbarkeitsgruppe für virtuelle Computer

Erstellen Sie mit [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) eine Verfügbarkeitsgruppe für die virtuellen Computer:

* Name: **myAvSet**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell
$cred = Get-Credential
```

Erstellen Sie die virtuellen Computer mit Folgendem:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Name: **myVM1**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM1**
* Angefügt an den Lastenausgleich **myLoadBalancer**
* Standort: **eastus**
* Verfügbarkeitsgruppe: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Name: **myVM2**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicVM2**
* Angefügt an den Lastenausgleich **myLoadBalancer**
* Standort: **eastus**
* Verfügbarkeitsgruppe: **myAvSet**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

Die Erstellung und Konfiguration der drei virtuellen Computer dauert einige Minuten.

---

## <a name="install-iis"></a>Installieren von IIS

Verwenden Sie [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest), um die benutzerdefinierte Skripterweiterung zu installieren. 

Die Erweiterung führt „PowerShell Add-WindowsFeature Web-Server“ zum Installieren des IIS-Webservers aus und aktualisiert dann die Seite „Default.htm“ mit dem Hostnamen der VM:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

### <a name="create-network-interface"></a>Erstellen einer Netzwerkschnittstelle

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) eine Netzwerkschnittstelle:

#### <a name="mytestvm"></a>myTestVM

* Name: **myNicTestVM**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Standort: **eastus**
* Virtuelles Netzwerk: **myVNet**
* Subnetz: **myBackendSubnet**
* Netzwerksicherheitsgruppe: **myNSG**

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers

Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort des Administrators des virtuellen Computers fest:

```azurepowershell
$cred = Get-Credential
```

Erstellen Sie mithilfe der folgenden Befehle den virtuellen Computer:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Name: **myTestVM**
* In Ressourcengruppe **CreateIntLBQS-rg**.
* Angefügt an die Netzwerkschnittstelle **myNicTestVM**
* Standort: **eastus**

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Test

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Bildschirm **Übersicht** nach der privaten IP-Adresse für den Lastenausgleich. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend **myLoadBalancer** aus.

2. Notieren Sie sich in der **Übersicht** von **myLoadBalancer** die Adresse neben **Private IP-Adresse**, oder kopieren Sie sie.

3. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myTestVM** (in der Ressourcengruppe **CreateIntLBQS-rg**) aus.

4. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

6. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

7. Öffnen Sie **Internet Explorer** auf **myTestVM**.

8. Geben Sie die IP-Adresse aus dem vorherigen Schritt in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Erstellen eines internen Standard-Lastenausgleichs" border="true":::
   
Um zu sehen, wie Datenverkehr durch den Lastenausgleich auf alle drei virtuellen Computer verteilt wird, können Sie die Standardseite des IIS-Webservers der einzelnen virtuellen Computer anpassen und dann über den Clientcomputer eine Aktualisierung Ihres Webbrowsers erzwingen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) aus, um die Ressourcengruppe, den Lastenausgleich und alle übrigen Ressourcen zu löschen.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde Folgendes vermittelt:

* Erstellen eines internen Lastenausgleichs (Standard oder Basic)
* Anfügen von virtuellen Computern 
* Konfigurieren der Datenverkehrsregel für den Lastenausgleich sowie des Integritätstests
* Testen des Lastenausgleichs

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren:
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)