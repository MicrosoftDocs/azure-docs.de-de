---
title: Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure PowerShell
description: In diesem Artikel erfahren Sie, wie Sie Azure Firewall unter Verwendung von Azure PowerShell bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.openlocfilehash: 7c30e0aa0ae9735f5d08e1a2c4d6e6d36d778e27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410241"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure PowerShell

Die Steuerung des ausgehenden Netzwerkzugriffs ist ein wichtiger Teil eines umfassenden Netzwerksicherheitsplans. Vielleicht möchten Sie beispielsweise den Zugriff auf Websites einschränken. Mitunter kann es auch empfehlenswert sein, die verfügbaren Ports einzuschränken.

Eine Möglichkeit zur Steuerung des ausgehenden Netzwerkzugriffs aus einem Subnetz ist Azure Firewall. Mit Azure Firewall können Sie Folgendes konfigurieren:

* Anwendungsregeln, die vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) definieren, auf die von einem Subnetz aus zugegriffen werden kann.
* Netzwerkregeln, die die Quelladresse, das Protokoll, den Zielport und die Zieladresse definieren.

Die konfigurierten Firewallregeln werden auf den Netzwerkdatenverkehr angewendet, wenn Sie Ihren Netzwerkdatenverkehr an die Firewall als Subnetz-Standardgateway weiterleiten.

In diesem Artikel erstellen Sie der Einfachheit halber ein einzelnes vereinfachtes VNET mit drei Subnetzen. Für Produktionsbereitstellungen wird ein [Hub-Spoke-Modell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) empfohlen, bei dem sich die Firewall in einem eigenen VNET befindet. Die Workloadserver befinden sich in per Peering verknüpften VNETs in derselben Region mit einem oder mehreren Subnetzen.

* **AzureFirewallSubnet:** Das Subnetz mit der Firewall.
* **Workload-SN:** Das Subnetz mit dem Workloadserver. Der Netzwerkdatenverkehr dieses Subnetzes durchläuft die Firewall.
* **Jump-SN:** Das Subnetz mit dem Sprungserver. Der Sprungserver besitzt eine öffentliche IP-Adresse, mit der Sie eine Remotedesktopverbindung herstellen können. Von dort aus können Sie dann über einen weiteren Remotedesktop eine Verbindung mit dem Workloadserver herstellen.

![Netzwerkinfrastruktur des Tutorials](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Einrichten einer Netzwerkumgebung zu Testzwecken
> * Bereitstellen einer Firewall
> * Erstellen einer Standardroute
> * Konfigurieren einer Anwendungsregel zum Zulassen des Zugriffs auf „www.google.com“
> * Konfigurieren einer Netzwerkregel, um den Zugriff auf externe DNS-Server zuzulassen
> * Testen der Firewall

Sie können dieses Verfahren auch im [Azure-Portal](tutorial-firewall-deploy-portal.md) durchführen, wenn Sie das vorziehen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Verfahren müssen Sie PowerShell lokal ausführen. Das Azure PowerShell-Modul muss installiert sein. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu finden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-Az-ps) Informationen dazu. Führen Sie nach dem Überprüfen der PowerShell-Version `Connect-AzAccount` aus, um eine Verbindung mit Azure zu erstellen.

## <a name="set-up-the-network"></a>Einrichten des Netzwerks

Erstellen Sie zunächst eine Ressourcengruppe für die Ressourcen, die zum Bereitstellen der Firewall benötigt werden. Erstellen Sie dann ein VNet, Subnetze und Testserver.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die Ressourcengruppe enthält alle Ressourcen für die Bereitstellung.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Erstellen eines VNET

Dieses virtuelle Netzwerk umfasst drei Subnetze:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/24
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```

> [!NOTE]
> Die Mindestgröße des Subnetzes „AzureFirewallSubnet“ beträgt /26.

Erstellen Sie jetzt das virtuelle Netzwerk:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie nun die virtuellen Sprung- und Workloadcomputer, und platzieren Sie sie in den entsprechenden Subnetzen.
Geben Sie bei entsprechender Aufforderung einen Benutzernamen und das Kennwort für den virtuellen Computer ein.

Erstellen Sie den virtuellen Srv-Jump-Computer.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Erstellen Sie einen virtuellen Workloadcomputer ohne öffentliche IP-Adresse.
Geben Sie bei entsprechender Aufforderung einen Benutzernamen und das Kennwort für den virtuellen Computer ein.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Bereitstellen der Firewall

Stellen Sie nun die Firewall im virtuellen Netzwerk bereit.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Notieren Sie sich die private IP-Adresse. Diese wird später für die Erstellung der Standardroute benötigt.

## <a name="create-a-default-route"></a>Erstellen einer Standardroute

Erstellen einer Tabelle mit deaktivierter BGP-Routenverteilung

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Konfigurieren einer Anwendungsregel

Die Anwendungsregel lässt ausgehenden Zugriff auf „www.google.com“ zu.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Azure Firewall enthält eine integrierte Regelsammlung für Infrastruktur-FQDNs, die standardmäßig zulässig sind. Diese FQDNs sind plattformspezifisch und können nicht für andere Zwecke verwendet werden. Weitere Informationen finden Sie unter [Infrastruktur-FQDNs](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurieren einer Netzwerkregel

Die Netzwerkregel lässt ausgehenden Zugriff auf zwei IP-Adressen am Port 53 (DNS) zu.

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändern der primären und sekundären DNS-Adresse für die Netzwerkschnittstelle **Srv-Work**

In diesem Verfahren konfigurieren Sie zu Testzwecken die primäre und sekundäre DNS-Adresse des Servers. Hierbei handelt es sich nicht um eine generelle Azure Firewall-Anforderung.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testen der Firewall

Testen Sie nun die Firewall, um sicherzustellen, dass sie wie erwartet funktioniert.

1. Notieren Sie die private IP-Adresse des virtuellen Computers **Srv-Work**:

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Verbinden Sie einen Remotedesktop mit der VM **Srv-Jump**, und melden Sie sich an. Öffnen Sie auf der VM eine Remotedesktopverbindung mit der privaten IP-Adresse von **Srv-Work**, und melden Sie sich an.

3. Öffnen Sie auf **SRV-Work** ein PowerShell-Fenster, und führen Sie die folgenden Befehle aus:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Für beide Befehle sollten Antworten zurückgegeben werden, was zeigt, dass Ihre DNS-Abfragen die Firewall durchqueren.

1. Führen Sie die folgenden Befehle aus:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Die Anforderungen an „www.google.com“ sollten erfolgreich sein, die an „www.microsoft.com“ sollten zu einem Fehler führen. Dies zeigt, dass Ihre Firewall-Regeln wie erwartet funktionieren.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

* Sie können DNS-Namen mithilfe des konfigurierten externen DNS-Servers auflösen.
* Sie können zum einzigen zulässigen FQDN navigieren, aber nicht zu anderen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **Test-FW-RG** löschen, wenn Sie sie nicht mehr benötigen. Dadurch werden alle firewallbezogenen Ressourcen gelöscht:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Überwachen von Azure Firewall-Protokollen](./tutorial-diagnostics.md)
