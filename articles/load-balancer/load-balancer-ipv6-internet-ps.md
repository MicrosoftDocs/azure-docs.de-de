---
title: Erstellen eines Load Balancers mit Internetzugriff und IPv6 – PowerShell
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie einen Load Balancer mit Internetzugriff über IPv6 unter Verwendung von PowerShell für Resource Manager erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, Azure Load Balancer, dualer Stapel, öffentliche IP, natives IPv6, mobil, IoT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: e4bc889df008283f05be5f820b66415cd38c1595
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149277"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff über IPv6 unter Verwendung von PowerShell für Resource Manager

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure-Befehlszeilenschnittstelle](load-balancer-ipv6-internet-cli.md)
> * [Vorlage](load-balancer-ipv6-internet-template.md)


Ein Azure Load Balancer ist ein Layer-4-Load Balancer (TCP, UDP). Der Load Balancer sorgt für Hochverfügbarkeit, indem er eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einer Gruppe für den Lastenausgleich definiert wurden. Der Azure Load Balancer kann diese Dienste auch auf mehreren Ports, mehreren IP-Adressen oder beidem leisten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Beispielszenario für die Bereitstellung

Das folgende Diagramm veranschaulicht die Lösung mit Lastenausgleich, die in diesem Artikel bereitgestellt wird.

![Load Balancer-Szenarios](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

In diesem Szenario erstellen Sie die folgenden Azure-Ressourcen:

* einen Load Balancer mit Internetzugriff mit je einer öffentlichen IPv4- und IPv6-IP-Adresse
* zwei Lastenausgleichsregeln, um die öffentlichen virtuellen IP-Adressen den privaten Endpunkten zuzuordnen
* eine Verfügbarkeitsgruppe, die die zwei virtuellen Computer enthält
* zwei virtuelle Computer (VMs)
* eine virtuelle Netzwerkschnittstelle für jeden virtuellen Computer mit zugewiesenen IPv4- und IPv6-Adressen

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Bereitstellen der Lösung mithilfe von Azure PowerShell

Die folgenden Schritte zeigen, wie Sie einen internen Load Balancer mit Internetzugriff mit dem Azure Resource Manager und PowerShell erstellen. Mit Azure Resource Manager werden die einzelnen Ressourcen erstellt sowie individuell konfiguriert und dann zusammengeführt, um eine Ressource zu erstellen.

Zum Bereitstellen eines Load Balancers erstellen und konfigurieren Sie die folgenden Objekte:

* Front-End-IP-Konfiguration: Enthält öffentliche IP-Adressen für eingehenden Netzwerkdatenverkehr.
* Back-End-Adresspool: Enthält Netzwerkschnittstellen (NICs), die virtuellen Computern den Empfang von Netzwerkdatenverkehr des Load Balancers ermöglichen.
* Lastenausgleichsregeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port im Back-End-Adresspool.
* NAT-Eingangsregeln: Enthält Regeln für das Zuordnen eines öffentlichen Ports des Load Balancers zu einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool.
* Tests: Enthält Integritätstests zum Prüfen der Verfügbarkeit von VM-Instanzen im Back-End-Adresspool.

Weitere Informationen finden Sie unter [Unterstützung des Azure Resource Managers für Load Balancer](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Einrichten von PowerShell für die Verwendung des Resource Managers

Stellen Sie sicher, dass Sie über die neueste Produktionsversion des Azure Resource Manager-Moduls für PowerShell verfügen.

1. Anmelden bei Azure

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Geben Sie bei Aufforderung Ihre Anmeldeinformationen ein.

2. Überprüfen Sie die Abonnements für das Konto.

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Erstellen Sie eine Ressourcengruppe (überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden).

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Erstellen eines virtuellen Netzwerks und einer öffentlichen IP-Adresse für den Front-End-IP-Adresspool

1. Erstellen Sie ein virtuelles Netzwerk mit einem Subnetz.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Erstellen Sie öffentliche Azure-IP-Adressressourcen (PIP) für den Front-End-IP-Adresspool. Stellen Sie sicher, dass Sie den Wert für `-DomainNameLabel` ändern, bevor Sie die folgenden Befehle ausführen. Der Wert muss innerhalb der Azure-Region eindeutig sein.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Der Load Balancer verwendet die Domänenbezeichnung der öffentlichen IP-Adresse als Präfix seines vollqualifizierten Domänennamens (FQDN). In diesem Beispiel lauten die FQDNs *lbnrpipv4.westus.cloudapp.azure.com* und *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Erstellen einer Front-End-IP-Konfiguration und eines Back-End-Adresspools

1. Erstellen Sie die Front-End-Adresskonfiguration, die die öffentlichen IP-Adressen verwendet, die Sie erstellt haben.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Erstellen Sie die Back-End-Adresspools.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Erstellen von LB-Regeln, NAT-Regeln, Test und Load Balancer

In diesem Beispiel werden die folgenden Elemente erstellt:

* eine NAT-Regel, um sämtlichen eingehenden Datenverkehr an Port 443 für Port 4443 zu übersetzen
* eine Load Balancer-Regel für die gleichmäßige Verteilung des gesamten an Port 80 eingehenden Datenverkehrs an Port 80 der Adressen im Back-End-Pool.
* eine Load Balancer-Regel für das Zulassen von RDP-Verbindungen mit virtuellen Computern an Port 3389.
* eine Testregel, die den Integritätsstatus der Seite *HealthProbe.aspx* oder eines Diensts an Port 8080 überprüft
* ein Load Balancer, der alle diese Objekte verwendet

1. Erstellen Sie die NAT-Regeln.

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Erstellen Sie einen Integritätstest. Es gibt zwei Möglichkeiten, einen Test zu konfigurieren:

    HTTP-Test

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    oder TCP-Test

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    In diesem Beispiel verwenden wir die TCP-Tests.

3. Erstellen Sie eine Load Balancer-Regel.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Erstellen Sie den Load Balancer mithilfe der zuvor erstellten Objekte.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Erstellen von NICs für die Back-End-VMs

1. Rufen Sie das virtuelle Netzwerk und das zugehörige Subnetz ab, in denen die Netzwerkkarten erstellt werden sollen.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Erstellen Sie die IP-Konfigurationen und NICs für die virtuellen Computer.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Erstellen von virtuellen Computern und Zuweisen der neu erstellten NICs

Weitere Informationen zum Erstellen eines virtuellen Computers finden Sie unter [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit Resource Manager und Azure PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Erstellen Sie eine Verfügbarkeitsgruppe und ein Speicherkonto.

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Erstellen Sie die einzelnen virtuellen Computer, und weisen Sie die zuvor erstellten NICs zu.

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)
