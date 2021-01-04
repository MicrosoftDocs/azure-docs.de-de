---
title: Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen – Azure CLI
titleSuffix: Azure Load Balancer
description: In diesem Artikel erfahren Sie etwas über den Lastenausgleich über primäre und sekundäre IP-Konfigurationen hinweg mithilfe der Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: a78bfc2b4f0c372c915647c0afa40263079af8e5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746068"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen mit PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


In diesem Artikel wird beschrieben, wie Sie Azure Load Balancer mit mehreren IP-Adressen an einer sekundären Netzwerkschnittstellenkarte (Network Interface Card, NIC) verwenden. In diesem Szenario verwenden wir zwei virtuelle Computer unter Windows mit jeweils einer primären und einer sekundären NIC. Die sekundären NICs haben jeweils zwei IP-Konfigurationen. Jeder virtuelle Computer hostet die beiden Websites „contoso.com“ und „fabrikam.com“. Jede Website ist an eine der IP-Konfigurationen der sekundären NIC gebunden. Wir verwenden Azure Load Balancer, um zwei Front-End-IP-Adressen verfügbar zu machen (jeweils eine für jede Website), damit der Datenverkehr an die entsprechende IP-Konfiguration für die Website verteilt werden kann. Dieses Szenario verwendet die gleiche Portnummer auf beiden Front-Ends sowie beide Back-End-Pool-IP-Adressen.

![Abbildung zum LB-Szenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Schritte für den Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen

Führen Sie die folgenden Schritte aus, um das in diesem Artikel beschriebene Szenario umzusetzen:

1. Installieren Sie Azure PowerShell. Unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) erfahren Sie, wie Sie die neueste Version von Azure PowerShell installieren, Ihr Abonnement auswählen und sich bei Ihrem Konto anmelden.
2. Erstellen Sie mithilfe der folgenden Einstellungen eine Ressourcengruppe:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Weitere Informationen finden Sie in Schritt 2 unter [Erstellen einer Ressourcengruppe](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Erstellen Sie eine Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) mit Ihren virtuellen Computern. Führen Sie für dieses Szenario den folgenden Befehl aus:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Führen Sie die Anweisungsschritte 3 bis 5 im Artikel [Erstellen einer Windows-VM mit dem Resource Manager und PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json) aus, um die Erstellung eines virtuellen Computers mit einer NIC vorzubereiten. Führen Sie Schritt 6.1 aus, und verwenden Sie anstelle von Schritt 6.2 Folgendes:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Führen Sie anschließend die Schritte 6.3 bis 6.8 unter [Erstellen einer Windows-VM mit dem Resource Manager und PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fload-balancer%2ftoc.json) aus.

5. Fügen Sie jedem virtuellen Computer eine zweite IP-Konfiguration hinzu. Folgen Sie den Anweisungen im Artikel [Zuweisen von mehreren IP-Adressen zu virtuellen Computern](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Verwenden Sie die folgenden Konfigurationseinstellungen:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Sie müssen in diesem Tutorial den sekundären IP-Konfigurationen keine öffentlichen IPs zuweisen. Bearbeiten Sie den Befehl, um den Teil zur Zuweisung der öffentlichen IP zu entfernen.

6. Führen Sie die Schritte 4 bis 6 dieses Artikels erneut für den zweiten virtuellen Computer (VM2) aus. Ersetzen Sie dabei unbedingt den Namen des virtuellen Computers durch den Namen des zweiten virtuellen Computers. Beachten Sie, dass Sie kein virtuelles Netzwerk für den zweiten virtuellen Computer erstellen müssen. Sie können abhängig von Ihrem Anwendungsfall ein neues Subnetz erstellen.

7. Erstellen Sie zwei öffentliche IP-Adressen, und speichern Sie sie wie gezeigt in den entsprechenden Variablen:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Erstellen Sie zwei Front-End-IP-Konfigurationen:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Erstellen Sie die Back-End-Adresspools, einen Test und die Lastenausgleichsregeln:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Erstellen Sie nach der Erstellung dieser Ressourcen den Lastenausgleich:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Fügen sie den zweiten Back-End-Adresspool und die Front-End-IP-Konfiguration zum neu erstellten Lastenausgleich hinzu:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Die unten aufgeführten Befehle rufen die NICs ab und fügen anschließend beide IP-Konfigurationen der sekundären NICs zum Back-End-Adresspool des Lastenausgleichs hinzu:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Abschließend müssen Sie DNS-Ressourceneinträge konfigurieren, um auf die entsprechende Front-End-IP-Adresse des Lastenausgleichs zu verweisen. Sie können Ihre Domänen in Azure DNS hosten. Weitere Informationen zur Verwendung von Azure DNS mit Lastenausgleich finden Sie unter [Verwenden von Azure DNS mit anderen Azure-Diensten](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie in [Verwenden von Lastenausgleichsdiensten in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md), wie Sie Lastenausgleichsdienste in Azure kombinieren.
- Erfahren Sie in [Azure Monitor-Protokolle für den Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md), wie Sie in Azure verschiedene Protokolltypen verwenden, um den Load Balancer zu verwalten und eventuelle Fehler zu beheben.
