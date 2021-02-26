---
title: 'Tutorial: Erstellen eines regionsübergreifenden Lastenausgleichs mit Azure PowerShell'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial stellen Sie zunächst mit Azure PowerShell eine regionsübergreifende Azure Load Balancer-Instanz bereit.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: b1a249bac4a1a46e52bf52eccd56649153eefe8e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360923"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Tutorial: Erstellen einer regionsübergreifenden Azure Load Balancer-Instanz mit Azure PowerShell

Ein regionsübergreifender Lastenausgleich stellt sicher, dass ein Dienst global in mehreren Azure-Regionen verfügbar ist. Wenn eine Region ausfällt, wird der Datenverkehr an den nächstgelegenen fehlerfreien Lastenausgleich weitergeleitet.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines regionsübergreifenden Lastenausgleichs
> * Erstellen Sie eine Load Balancer-Regel.
> * Erstellen eines Back-End-Pools mit zwei regionalen Lastenausgleichsmodulen
> * Testen des Lastenausgleichs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement.
- Zwei Azure Load Balancer-Instanzen der SKU **Standard** mit Back-End-Pool in zwei verschiedenen Azure-Regionen
    - Informationen zum Erstellen einer regionalen Load Balancer Standard-Instanz virtueller Computer für Back-End-Pools finden Sie unter [Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für virtuelle Computer mithilfe von Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Fügen Sie den Namen der Lastenausgleichsmodule und virtuellen Computer in jeder Region mit **-R1** und **-R2** an. 
- Azure PowerShell (lokale Installation) oder Azure Cloud Shell.


Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-cross-region-load-balancer"></a>Erstellen eines regionsübergreifenden Lastenausgleichs


### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Erstellen von regionsübergreifenden Lastenausgleichsressourcen

In diesem Abschnitt erstellen Sie die benötigten Ressourcen für den regionsübergreifenden Lastenausgleich.

Eine öffentliche IP-Adresse einer globalen Standard-SKU wird für das Front-End des regionsübergreifenden Lastenausgleichs verwendet.

* Verwenden Sie [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress), um die öffentliche IP-Adresse zu erstellen.

* Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Konfiguration.

* Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool.

* Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel.

* Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) einen regionsübergreifenden Lastenausgleich.

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Konfigurieren des Back-End-Pools

In diesem Abschnitt fügen Sie dem Back-End-Pool des regionsübergreifenden Lastenausgleichs zwei regionale Load Balancer Standard-Instanzen hinzu.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Ausführen dieser Schritte, dass zwei regionale Lastenausgleichsmodule mit Back-End-Pools in Ihrem Abonnement bereitgestellt wurden.  Weitere Informationen finden Sie unter **[Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für virtuelle Computer mithilfe von Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)** .

* Verwenden Sie [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) und [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig), um die Informationen zum regionalen Lastenausgleich in Variablen zu speichern.

* Verwenden Sie [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig), um die Back-End-Adresspoolkonfiguration für den Lastenausgleich zu erstellen.

* Verwenden Sie [Set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool), um dem regionsübergreifenden Back-End-Pool das regionale Load Balancer-Front-End hinzuzufügen.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

In diesem Abschnitt testen Sie den regionsübergreifenden Lastenausgleich. Sie stellen in einem Webbrowser eine Verbindung mit der öffentlichen IP-Adresse her.  Sie halten die virtuellen Computer in den Back-End-Pools eines der regionalen Lastenausgleiche an und beobachten das Failover.

1. Rufen Sie mit [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse des Lastenausgleichs ab:

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

3. Beenden Sie die virtuellen Computer im Back-End-Pool eines der regionalen Lastenausgleichsmodule.

4. Aktualisieren Sie den Webbrowser, und beobachten Sie das Failover der Verbindung zum anderen regionalen Lastenausgleich.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) aus, um die Ressourcengruppe, den Lastenausgleich und alle übrigen Ressourcen zu löschen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial führen Sie Folgendes durch:

* Erstellen einer globalen IP-Adresse
* einen regionsübergreifenden Lastenausgleich erstellt
* eine Lastenausgleichsregel erstellt
* dem Back-End-Pool des regionsübergreifenden Lastenausgleichs regionale Lastenausgleichsmodule hinzugefügt
* Testen des Lastenausgleichs


Fahren Sie mit dem nächsten Artikel fort, und lernen Sie das...
> [!div class="nextstepaction"]
> [Durchführen eines verfügbarkeitszonenübergreifenden Lastenausgleichs für virtuelle Computer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
