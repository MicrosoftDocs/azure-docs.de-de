---
title: 'Schnellstart: Erstellen eines Lastenausgleichs im Basic-Tarif: Azure PowerShell'
titlesuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie einen Load Balancer im Tarif „Basic“ mit PowerShell erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 5b77087557bc4ca1bea3cb4a36a50d787a5edf4d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729842"
---
# <a name="get-started"></a>Schnellstart: Erstellen eines öffentlichen Lastenausgleichs mit Azure PowerShell

In dieser Schnellstartanleitung wird gezeigt, wie Sie einen Load Balancer im Tarif „Basic“ mit Azure PowerShell erstellen. Zum Testen des Lastenausgleichs stellen Sie zwei virtuelle Computer (VMs) mit Windows Server bereit und führen für eine Web-App zwischen den VMs einen Lastenausgleich durch.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Bevor Sie Ihren Lastenausgleich erstellen können, müssen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe erstellen. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroupLB* am Standort *EastUS* erstellt:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse
Um über das Internet auf Ihre App zugreifen zu können, benötigen Sie eine öffentliche IP-Adresse für den Load Balancer. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IP-Adresse. Im folgenden Beispiel wird in der Ressourcengruppe *myResourceGroupLB* eine öffentliche IP-Adresse mit dem Namen *myPublicIP* erstellt:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>Erstellen eines Load Balancers im Tarif „Basic“

In diesem Abschnitt konfigurieren Sie die Front-End-IP-Adresse und den Back-End-Adresspool für den Lastenausgleich und erstellen anschließend den Lastenausgleich im Tarif „Basic“.

### <a name="create-front-end-ip"></a>Erstellen der Front-End-IP-Adresse

Erstellen Sie mit [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) eine Front-End-IP-Adresse. Im folgenden Beispiel wird eine Front-End-IP-Konfiguration mit dem Namen *myFrontEnd* erstellt und die Adresse *myPublicIP* angefügt:

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Konfigurieren des Back-End-Adresspools

Erstellen Sie mit [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) einen Back-End-Adresspool. In den verbleibenden Schritten werden die virtuellen Computer an diesen Back-End-Pool angefügt. Im folgenden Beispiel wird ein Back-End-Adresspool namens *myBackEndPool* erstellt:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests
Damit der Load Balancer den Status Ihrer App überwachen kann, verwenden Sie einen Integritätstest. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Rotation durch den Integritätstest dynamisch virtuelle Computer hinzugefügt oder daraus entfernt. Falls bei einem virtuellen Computer innerhalb eines Intervalls von 15 Sekunden zwei aufeinanderfolgende Fehler auftreten, wird er standardmäßig aus der Load Balancer-Verteilung entfernt. Integritätstests werden auf der Grundlage eines Protokolls oder einer spezifischen Integritätsprüfungsseite für Ihre App erstellt. 

Im folgenden Beispiel wird ein TCP-Test erstellt. Sie können auch benutzerdefinierte HTTP-Tests für differenziertere Integritätsprüfungen erstellen. Bei Verwendung eines benutzerdefinierten HTTP-Tests müssen Sie die Integritätsprüfungsseite erstellen, z.B. *healthcheck.aspx*. Der Test muss die HTTP-Antwort **200 OK** zurückgeben, damit der Load Balancer den Host nicht aus der Rotation entfernt.

Erstellen Sie mithilfe von [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) einen TCP-Integritätstest. Im folgenden Beispiel wird ein Integritätstest mit dem Namen *myHealthProbe* erstellt, der die einzelnen virtuellen Computer an *HTTP*-Port *80* überwacht:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Um sicherzustellen, dass nur fehlerfreie virtuelle Computer Datenverkehr empfangen, definieren Sie zudem den zu verwendenden Integritätstest.

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) eine Lastenausgleichsregel. Im folgenden Beispiel wird eine Lastenausgleichsregel mit dem Namen *myLoadBalancerRule* erstellt und der Datenverkehr an *TCP*-Port *80* ausgeglichen:

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Erstellen der NAT-Regeln

Erstellen Sie mit [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) NAT-Regeln. Im folgenden Beispiel werden NAT-Regeln mit den Namen *myLoadBalancerRDP1* und *myLoadBalancerRDP2* erstellt, um RDP-Verbindungen zu den Back-End-Servern über die Ports 4221 und 4222 zuzulassen:

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Erstellen Sie mit [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) den Lastenausgleich im Tarif „Basic“. Im folgenden Beispiel wird ein öffentlicher Load Balancer im Tarif „Basic“ mit dem Namen myLoadBalancer erstellt, indem die in den obigen Schritten erstellte Front-End-IP-Konfiguration, der Back-End-Pool, der Integritätstest, die Lastenausgleichsregel und die NAT-Regeln verwendet werden:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Bevor Sie einige VMs bereitstellen und Ihren Load Balancer testen können, müssen Sie unterstützende Netzwerkressourcen erstellen: ein virtuelles Netzwerk und virtuelle NICs. 

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVnet* mit einem Subnetz namens *mySubnet* erstellt:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 3389

Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel, um RDP-Verbindungen über den Port 3389 zuzulassen.

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für Port 80

Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Netzwerksicherheitsgruppen-Regel, um eingehende Verbindungen über den Port 80 zuzulassen.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Erstellen von NICs

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) virtuelle NICs. Im folgenden Beispiel werden zwei virtuelle Netzwerkkarten erstellt. (jeweils eine virtuelle NIC pro virtuellem Computer, den Sie in den folgenden Schritten für Ihre App erstellen). Sie können jederzeit weitere virtuelle NICs und virtuelle Computer erstellen und dem Load Balancer hinzufügen:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Fügen Sie die virtuellen Computer in eine Verfügbarkeitsgruppe ein, um die Hochverfügbarkeit Ihrer App zu optimieren.

Erstellen Sie mithilfe von [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) eine Verfügbarkeitsgruppe. Im folgenden Beispiel wird eine Verfügbarkeitsgruppe namens *myAvailabilitySet* erstellt:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell-interactive
$cred = Get-Credential
```

Nun können Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) die virtuellen Computer erstellen. Im folgenden Beispiel werden zwei virtuelle Computer und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind. In diesem Beispiel werden die im vorherigen Schritt erstellten NICs (*VM1* und *VM2*) automatisch den virtuellen Computern *VM1* und *VM2* zugewiesen, da sie identische Namen besitzen und ihnen das gleiche virtuelle Netzwerk (*myVnet*) und Subnetz (*mySubnet*) zugewiesen sind. Darüber hinaus werden die virtuellen Computer automatisch dem Back-End-Pool hinzugefügt, da die NICs dem Back-End-Pool des Lastenausgleichs zugeordnet sind.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Mit dem Parameter `-AsJob` wird die VM als Hintergrundaufgabe erstellt, sodass die PowerShell-Aufforderungen für Sie zurückgegeben werden. Sie können die Details von Hintergrundaufträgen mit dem `Job`-Cmdlet anzeigen. Die Erstellung und Konfiguration der beiden virtuellen Computer dauert einige Minuten.

### <a name="install-iis-with-custom-web-page"></a>Installieren von IIS mit benutzerdefinierter Webseite
 
Installieren Sie IIS mit einer benutzerdefinierten Webseite wie folgt auf beiden virtuellen Back-End-Computern:

1. Rufen Sie die öffentliche IP-Adresse des Load Balancers ab. Verwenden Sie `Get-AzPublicIPAddress`, um die öffentliche IP-Adresse des Load Balancers abzurufen.

   ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
   ```
2. Erstellen Sie eine Remotedesktopverbindung mit VM1, indem Sie die öffentliche IP-Adresse verwenden, die Sie im vorherigen Schritt abgerufen haben. 

   ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
   ```
3. Geben Sie die Anmeldeinformationen für *VM1* ein, um die RDP-Sitzung zu starten.
4. Starten Sie Windows PowerShell auf VM1, und verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Schließen Sie die RDP-Verbindung mit *myVM1*.
6. Erstellen Sie eine RDP-Verbindung mit *myVM2*, indem Sie den Befehl `mstsc /v:PublicIpAddress:4222` ausführen, und wiederholen Sie Schritt 4 für *VM2*.

## <a name="test-load-balancer"></a>Testen des Load Balancers
Rufen Sie mit [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse Ihres Lastenausgleichs ab. Im folgenden Beispiel wird die IP-Adresse für *myPublicIP* abgerufen, die wir zuvor erstellt haben:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Geben Sie die öffentliche IP-Adresse in einem Webbrowser ein. Die Website wird mit dem Hostnamen der VM angezeigt, an die der Load Balancer den Datenverkehr wie im folgenden Beispiel verteilt hat:

![Testen des Load Balancers](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Sie können eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Load Balancer den Datenverkehr auf die beiden virtuellen Computer verteilt, auf denen Ihre App ausgeführt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Lastenausgleich im Tarif „Basic“ erstellt, virtuelle Computer angefügt, eine Datenverkehrsregel für den Lastenausgleich sowie einen Integritätstest konfiguriert und den Lastenausgleich getestet. Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials zu Azure Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-basic-internal-portal.md)