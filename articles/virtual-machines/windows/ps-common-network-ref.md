---
title: Häufige PowerShell-Befehle für virtuelle Azure-Netzwerke | Microsoft-Dokumentation
description: Enthält häufig verwendete PowerShell-Befehle als Einstiegshilfe für die Erstellung eines virtuellen Netzwerks und der zugeordneten Ressourcen für VMs.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 020f2a4171a5bd656e53c91e59edb16931b20d0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597679"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Häufige PowerShell-Befehle für virtuelle Azure-Netzwerke

Zur Erstellung eines virtuellen Computers müssen Sie ein [virtuelles Netzwerk](../../virtual-network/virtual-networks-overview.md) erstellen oder über ein vorhandenes virtuelles Netzwerk verfügen, dem die VM hinzugefügt werden kann. Normalerweise müssen Sie beim Erstellen einer VM auch die in diesem Artikel beschriebenen Ressourcen erstellen.

Unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview) erfahren Sie, wie Sie die neueste Version von Azure PowerShell installieren, Ihr Abonnement auswählen und sich bei Ihrem Konto anmelden.

Einige Variablen könnten hilfreich sein, wenn Sie mehr als einen der Befehle aus diesem Artikel ausführen:

- $location – Der Speicherort der Netzwerkressourcen. Sie können [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) verwenden, um eine [geografische Region](https://azure.microsoft.com/regions/) zu suchen, die sich für Ihre Zwecke eignet.
- $myResourceGroup – Der Name der Ressourcengruppe, in der die Netzwerkressourcen gespeichert sind.

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

| Aufgabe | Get-Help |
| ---- | ------- |
| Erstellen von Subnetzkonfigurationen |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Ein typisches Netzwerk kann über ein Subnetz für einen [Load Balancer mit Internetzugriff](../../load-balancer/load-balancer-internet-overview.md) und ein separates Subnetz für einen [internen Load Balancer](../../load-balancer/load-balancer-internal-overview.md) verfügen. |
| Erstellen eines virtuellen Netzwerks |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Überprüfen auf einen eindeutigen Domänennamen |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Sie können einen DNS-Domänennamen für eine [öffentliche IP-Ressource](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) angeben. Dadurch erstellen Sie für domainname.location.cloudapp.azure.com eine Zuordnung zur öffentlichen IP-Adresse auf den von Azure verwalteten DNS-Servern. Der Name darf nur Buchstaben, Zahlen und Bindestriche enthalten. Das erste und letzte Zeichen muss jeweils ein Buchstabe oder eine Zahl sein, und der Domänenname muss für den Azure-Standort eindeutig sein. Wenn **TRUE** zurückgegeben wird, ist der vorgeschlagene Name global eindeutig. |
| Erstellen einer öffentlichen IP-Adresse |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Für die öffentliche IP-Adresse wird der zuvor getestete Domänenname verwendet. Sie wird bei der Front-End-Konfiguration des Load Balancers genutzt. |
| Erstellen einer Front-End-IP-Konfiguration |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Die Front-End-Konfiguration enthält die öffentliche IP-Adresse, die Sie zuvor für eingehenden Netzwerk-Datenverkehr erstellt haben. |
| Erstellen eines Back-End-Adresspools |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Stellt interne Adressen für das Back-End des Load Balancers bereit, auf die über eine Netzwerkschnittstelle zugegriffen wird. |
| Erstellen eines Tests |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Enthält Integritätstests, die zum Überprüfen der Verfügbarkeit von Instanzen virtueller Computer im Back-End-Adresspool verwendet werden. |
| Erstellen einer Lastenausgleichsregel |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Enthält Regeln, mit denen ein öffentlicher Port auf dem Load Balancer einem Port im Back-End-Adresspool zugewiesen wird. |
| Erstellen einer eingehenden NAT-Regel |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Enthält Regeln, mit denen ein öffentlicher Port auf dem Load Balancer einem Port für einen bestimmten virtuellen Computer im Back-End-Adresspool zugeordnet wird. |
| Einrichten eines Load Balancers |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Erstellen einer Netzwerkschnittstelle |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Erstellen Sie eine Netzwerkschnittstelle, indem Sie die öffentliche IP-Adresse und das zuvor erstellte virtuelle Netzwerksubnetz verwenden. |

## <a name="get-information-about-network-resources"></a>Abrufen von Informationen zu Netzwerkressourcen

| Aufgabe | Get-Help |
| ---- | ------- |
| Auflisten von virtuellen Netzwerken |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Listet alle virtuellen Netzwerke in der Ressourcengruppe auf. |
| Abrufen von Informationen zu einem virtuellen Netzwerk |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Auflisten von Subnetzen in einem virtuellen Netzwerk |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Abrufen von Informationen zu einem Subnetz |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Ruft Informationen zu dem Subnetz im angegebenen virtuellen Netzwerk ab. Der $vnet-Wert gibt das von „Get-AzVirtualNetwork“ zurückgegebene Objekt an. |
| Auflisten von IP-Adressen |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Listet die öffentlichen IP-Adressen in der Ressourcengruppe auf. |
| Auflisten von Load Balancern |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Listet alle Load Balancer in der Ressourcengruppe auf. |
| Auflisten von Netzwerkschnittstellen |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Listet alle Netzwerkschnittstellen in der Ressourcengruppe auf. |
| Abrufen von Informationen zu einer Netzwerkschnittstelle |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Ruft Informationen zu einer bestimmten Netzwerkschnittstelle ab. |
| Abrufen der IP-Konfiguration einer Netzwerkschnittstelle |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Ruft Informationen zur IP-Konfiguration der angegebenen Netzwerkschnittstelle ab. Der $nic-Wert gibt das von „Get-AzNetworkInterface“ zurückgegebene Objekt an. |

## <a name="manage-network-resources"></a>Verwalten von Netzwerkressourcen

| Aufgabe | Get-Help |
| ---- | ------- |
| Hinzufügen eines Subnetzes zu einem virtuellen Netzwerk |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Fügt einem vorhandenen virtuellen Netzwerk ein Subnetz hinzu. Der $vnet-Wert gibt das von „Get-AzVirtualNetwork“ zurückgegebene Objekt an. |
| Löschen eines virtuellen Netzwerks |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt das angegebene virtuelle Netzwerk aus der Ressourcengruppe. |
| Löschen einer Netzwerkschnittstelle |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt die angegebene Netzwerkschnittstelle aus der Ressourcengruppe. |
| Löschen eines Load Balancers |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt den angegebenen Load Balancer aus der Ressourcengruppe. |
| Löschen einer öffentlichen IP-Adresse |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Entfernt die angegebene öffentliche IP-Adresse aus der Ressourcengruppe. |

## <a name="next-steps"></a>Nächste Schritte
* Verwenden Sie die soeben erstellte Netzwerkschnittstelle beim [Erstellen einer VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Erfahren Sie, wie Sie eine [VM mit mehreren Netzwerkschnittstellen erstellen](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

