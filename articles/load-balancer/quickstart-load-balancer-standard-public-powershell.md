---
title: 'Schnellstart: Erstellen eines öffentlichen Lastenausgleichs: Azure PowerShell'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell einen Lastenausgleich erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: 27a2b764ebf420c91c57c6f5f762f656b1040a91
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594551"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für virtuelle Computer mithilfe von Azure PowerShell

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie mithilfe von PowerShell einen öffentlichen Lastenausgleich und drei virtuelle Computer erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe:

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePubLBQS-rg' -Location 'eastus'

```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Für den Schnellstart erstellte Load Balancer Standard-Ressourcen." border="false":::

## <a name="create-a-public-ip-address---standard"></a>Erstellen einer öffentlichen IP-Adresse: Standard

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche IP-Adresse zu erstellen.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1,2,3
}
New-AzPublicIpAddress @publicip

```

Verwenden Sie den folgenden Befehl, um in Zone 1 eine zonale öffentliche IP-Adresse zu erstellen:

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1
}
New-AzPublicIpAddress @publicip

```

## <a name="create-standard-load-balancer"></a>Erstellen eines Standard-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

* Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse für den Front-End-IP-Pool. An dieser IP-Adresse wird der eingehende Datenverkehr für den Lastenausgleich empfangen.

* Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool für Datenverkehr, der vom Front-End des Lastenausgleichs gesendet wird. In diesem Pool werden Ihre virtuellen Back-End-Computer bereitgestellt.

* Erstellen Sie mit [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen Integritätstest, der die Integrität der Back-End-VM-Instanzen ermittelt.

* Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel, die die Verteilung von Datenverkehr an die virtuellen Computer definiert.

* Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) einen öffentlichen Lastenausgleich.


```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset -DisableOutboundSNAT

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---standard"></a>Konfigurieren des virtuellen Netzwerks: Standard

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer bereitstellen und Ihren Lastenausgleich testen.

Erstellen Sie ein virtuelles Netzwerk für die virtuellen Back-End-Computer.

Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Erstellen von virtuellem Netzwerk, Netzwerksicherheitsgruppe und Bastion-Host

* Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk.

* Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel.

* Erstellen Sie mit [New-AzBastion](/powershell/module/az.network/new-azbastion) einen Azure Bastion-Host.

* Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---standard"></a>Erstellen virtueller Computer: Standard

In diesem Abschnitt werden die drei virtuellen Computer für den Back-End-Pool des Lastenausgleichs erstellt.

* Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) drei Netzwerkschnittstellen.

* Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort des Administrators der VMs fest.

* Erstellen Sie die virtuellen Computer mit Folgendem:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

Die Bereitstellungen der virtuellen Computer und des Bastionhosts werden als PowerShell-Aufträge übermittelt. Verwenden Sie [Get-Job](/powershell/module/microsoft.powershell.core/get-job), um den Status der Aufträge anzuzeigen:

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

## <a name="create-outbound-rule-configuration"></a>Erstellen der Konfiguration für Ausgangsregeln
Durch Ausgangsregeln für den Lastenausgleich wird die ausgehende Übersetzung der Quellnetzwerkadresse (Source Network Address Translation, SNAT) für virtuelle Computer im Back-End-Pool konfiguriert. 

Weitere Informationen zu ausgehenden Verbindungen finden Sie unter [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address"></a>Erstellen einer öffentlichen Ausgangs-IP-Adresse

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine zonenredundante öffentliche IP-Standardadresse namens **myPublicIPOutbound** zu erstellen.

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1,2,3
}
New-AzPublicIpAddress @publicipout

```

Verwenden Sie den folgenden Befehl, um in Zone 1 eine zonale öffentliche IP-Adresse zu erstellen:

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = 1
}
New-AzPublicIpAddress @publicipout

```

### <a name="create-outbound-configuration"></a>Erstellen der Konfiguration für ausgehenden Datenverkehr

* Erstellen Sie mit [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig) eine neue Front-End-IP-Konfiguration.

* Erstellen Sie mit [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig) einen neuen ausgehenden Pool. 

* Wenden Sie mit [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) den Pool und die Front-End-IP-Adresse auf den Lastenausgleich an.
*  Erstellen Sie mit [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig) eine neue Ausgangsregel für den ausgehenden Back-End-Pool. 

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$pubip = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$publicIp = Get-AzPublicIpAddress @pubip

## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

## Create the frontend configuration ##
$fe = @{
    Name = 'myFrontEndOutbound'
    PublicIPAddress = $publicIP
}
$lb | Add-AzLoadBalancerFrontendIPConfig @fe | Set-AzLoadBalancer

## Create the outbound backend address pool ##
$be = @{
    Name = 'myBackEndPoolOutbound'
}
$lb | Add-AzLoadBalancerBackendAddressPoolConfig @be | Set-AzLoadBalancer

## Apply the outbound rule configuration to the load balancer. ##
$rule = @{
    Name = 'myOutboundRule'
    AllocatedOutboundPort = '10000'
    Protocol = 'All'
    IdleTimeoutInMinutes = '15'
    FrontendIPConfiguration = $lb.FrontendIpConfigurations[1]
    BackendAddressPool = $lb.BackendAddressPools[1]
}
$lb | Add-AzLoadBalancerOutBoundRuleConfig @rule | Set-AzLoadBalancer

```

### <a name="add-virtual-machines-to-outbound-pool"></a>Hinzufügen virtueller Computer zum ausgehenden Pool

Fügen Sie mit [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig) die VM-Netzwerkschnittstellen dem ausgehenden Pool des Lastenausgleichs hinzu:

```azurepowershell-interactive
## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

# For loop with variable to add virtual machines to backend outbound pool. ##
for ($i=1; $i -le 3; $i++)
{
$nic = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = "myNicVM$i"
}
$nicvm = Get-AzNetworkInterface @nic

## Apply the backend to the network interface ##
$be = @{
    Name = 'ipconfig1'
    LoadBalancerBackendAddressPoolId = $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id
}
$nicvm | Set-AzNetworkInterfaceIpConfig @be | Set-AzNetworkInterface
}

```

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen. Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Im Schnellstart erstellte Load Balancer Basic-Ressourcen." border="false":::

## <a name="create-a-public-ip-address---basic"></a>Erstellen einer öffentlichen IP-Adresse: Basic

Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um eine öffentliche IP-Adresse zu erstellen.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Basic'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-basic-load-balancer"></a>Erstellen eines Basic-Lastenausgleichs

In diesem Abschnitt erfahren Sie, wie Sie die folgenden Komponenten des Lastenausgleichs erstellen und konfigurieren:

* Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse für den Front-End-IP-Pool. An dieser IP-Adresse wird der eingehende Datenverkehr für den Lastenausgleich empfangen.

* Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool für Datenverkehr, der vom Front-End des Lastenausgleichs gesendet wird. In diesem Pool werden Ihre virtuellen Back-End-Computer bereitgestellt.

* Erstellen Sie mit [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen Integritätstest, der die Integrität der Back-End-VM-Instanzen ermittelt.

* Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel, die die Verteilung von Datenverkehr an die virtuellen Computer definiert.

* Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) einen öffentlichen Lastenausgleich.

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---basic"></a>Konfigurieren des virtuellen Netzwerks: Basic

Erstellen Sie zunächst die unterstützenden virtuellen Netzwerkressourcen, bevor Sie virtuelle Computer bereitstellen und Ihren Lastenausgleich testen.

Erstellen Sie ein virtuelles Netzwerk für die virtuellen Back-End-Computer.

Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Erstellen von virtuellem Netzwerk, Netzwerksicherheitsgruppe und Bastion-Host

* Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk.

* Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel.

* Erstellen Sie mit [New-AzBastion](/powershell/module/az.network/new-azbastion) einen Azure Bastion-Host.

* Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe.

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.1.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---basic"></a>Erstellen virtueller Computer: Basic

In diesem Abschnitt werden die virtuellen Computer für den Back-End-Pool des Lastenausgleichs erstellt.

* Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) drei Netzwerkschnittstellen.

* Legen Sie mit [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) den Benutzernamen und das Kennwort des Administrators der VMs fest.

* Erstellen Sie mit [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) eine Verfügbarkeitsgruppe für die virtuellen Computer.

* Erstellen Sie die virtuellen Computer mit Folgendem:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvSet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

Die Bereitstellungen der virtuellen Computer und des Bastionhosts werden als PowerShell-Aufträge übermittelt. Verwenden Sie [Get-Job](/powershell/module/microsoft.powershell.core/get-job), um den Status der Aufträge anzuzeigen:

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

---

## <a name="install-iis"></a>Installieren von IIS

Verwenden Sie [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension), um die benutzerdefinierte Skripterweiterung zu installieren. 

Die Erweiterung führt `PowerShell Add-WindowsFeature Web-Server` zum Installieren des IIS-Webservers aus und aktualisiert dann die Seite Default.htm mit dem Hostnamen der VM:

> [!IMPORTANT]
> Vergewissern Sie sich, dass die VM-Bereitstellungen aus den vorherigen Schritten abgeschlossen sind, bevor Sie fortfahren.  Verwenden Sie `Get-Job`, um den Status der VM-Bereitstellungsaufträge zu überprüfen.

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreatePubLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

Die Erweiterungen werden als PowerShell-Aufträge bereitgestellt. Verwenden Sie [Get-Job](/powershell/module/microsoft.powershell.core/get-job), um den Status der Installationsaufträge anzuzeigen:


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Rufen Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse des Lastenausgleichs ab:

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```

Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

   ![IIS-Webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Um zu sehen, wie Datenverkehr durch den Lastenausgleich auf alle drei virtuellen Computer verteilt wird, können Sie die Standardseite des IIS-Webservers der einzelnen virtuellen Computer anpassen und dann über den Clientcomputer eine Aktualisierung Ihres Webbrowsers erzwingen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) aus, um die Ressourcengruppe, den Lastenausgleich und alle übrigen Ressourcen zu löschen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePubLBQS-rg'

```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung gilt:

* Erstellen eines öffentlichen Lastenausgleichs (Standard oder Basic)
* Anfügen von virtuellen Computern 
* Konfigurieren der Datenverkehrsregel für den Lastenausgleich sowie des Integritätstests
* Testen des Lastenausgleichs

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren:
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)