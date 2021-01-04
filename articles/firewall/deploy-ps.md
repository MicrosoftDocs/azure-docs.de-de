---
title: Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure PowerShell
description: In diesem Artikel erfahren Sie, wie Sie Azure Firewall unter Verwendung von Azure PowerShell bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 11/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 18a7da6402d7835be8dbad0551973a262ab335c8
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660235"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure PowerShell

Die Steuerung des ausgehenden Netzwerkzugriffs ist ein wichtiger Teil eines umfassenden Netzwerksicherheitsplans. Vielleicht möchten Sie beispielsweise den Zugriff auf Websites einschränken. Mitunter kann es auch empfehlenswert sein, die verfügbaren Ports einzuschränken.

Eine Möglichkeit zur Steuerung des ausgehenden Netzwerkzugriffs aus einem Subnetz ist Azure Firewall. Mit Azure Firewall können Sie Folgendes konfigurieren:

* Anwendungsregeln, die vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) definieren, auf die von einem Subnetz aus zugegriffen werden kann.
* Netzwerkregeln, die die Quelladresse, das Protokoll, den Zielport und die Zieladresse definieren.

Die konfigurierten Firewallregeln werden auf den Netzwerkdatenverkehr angewendet, wenn Sie Ihren Netzwerkdatenverkehr an die Firewall als Subnetz-Standardgateway weiterleiten.

In diesem Artikel erstellen Sie der Einfachheit halber ein einzelnes vereinfachtes VNET mit drei Subnetzen. Für Produktionsbereitstellungen wird ein [Hub-Spoke-Modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) empfohlen, bei dem sich die Firewall in einem eigenen VNET befindet. Die Workloadserver befinden sich in per Peering verknüpften VNETs in derselben Region mit einem oder mehreren Subnetzen.

* **AzureFirewallSubnet:** Das Subnetz mit der Firewall.
* **Workload-SN:** Das Subnetz mit dem Workloadserver. Der Netzwerkdatenverkehr dieses Subnetzes durchläuft die Firewall.
* **AzureBastionSubnet:** Das für Azure Bastion verwendete Subnetz. Wird für die Verbindungsherstellung mit dem Workloadserver verwendet. Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](../bastion/bastion-overview.md).

![Netzwerkinfrastruktur des Tutorials](media/deploy-ps/tutorial-network.png)

In diesem Artikel werden folgende Vorgehensweisen behandelt:


* Einrichten einer Netzwerkumgebung zu Testzwecken
* Bereitstellen einer Firewall
* Erstellen einer Standardroute
* Konfigurieren einer Anwendungsregel zum Zulassen des Zugriffs auf www.google.com
* Konfigurieren einer Netzwerkregel, um den Zugriff auf externe DNS-Server zuzulassen
* Testen der Firewall

Sie können dieses Verfahren auch im [Azure-Portal](tutorial-firewall-deploy-portal.md) durchführen, wenn Sie das vorziehen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Verfahren müssen Sie PowerShell lokal ausführen. Das Azure PowerShell-Modul muss installiert sein. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Führen Sie nach dem Überprüfen der PowerShell-Version `Connect-AzAccount` aus, um eine Verbindung mit Azure zu erstellen.

## <a name="set-up-the-network"></a>Einrichten des Netzwerks

Erstellen Sie zunächst eine Ressourcengruppe für die Ressourcen, die zum Bereitstellen der Firewall benötigt werden. Erstellen Sie dann ein VNet, Subnetze und Testserver.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die Ressourcengruppe enthält alle Ressourcen für die Bereitstellung.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Erstellen eines virtuellen Netzwerks und eines Azure Bastion-Hosts

Dieses virtuelle Netzwerk verfügt über vier Subnetze:

> [!NOTE]
> Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurepowershell
$Bastionsub = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.0.0/27
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
```
Erstellen Sie jetzt das virtuelle Netzwerk:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $Bastionsub, $FWsub, $Worksub
```
### <a name="create-public-ip-address-for-azure-bastion-host"></a>Erstellen einer öffentlichen IP-Adresse für Azure Bastion-Host

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName Test-FW-RG -Location "East US" `
   -Name Bastion-pip -AllocationMethod static -Sku standard
```

### <a name="create-azure-bastion-host"></a>Erstellen des Azure Bastion-Hosts

```azurepowershell
New-AzBastion -ResourceGroupName Test-FW-RG -Name Bastion-01 -PublicIpAddress $publicip -VirtualNetwork $testVnet
```
### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie nun den virtuellen Workloadcomputer, und platzieren Sie ihn im passenden Subnetz.
Geben Sie bei entsprechender Aufforderung einen Benutzernamen und das Kennwort für den virtuellen Computer ein.


Erstellen Sie einen virtuellen Workloadcomputer.
Geben Sie bei entsprechender Aufforderung einen Benutzernamen und das Kennwort für den virtuellen Computer ein.

```azurepowershell
#Create the NIC
$wsn = Get-AzVirtualNetworkSubnetConfig -Name  Workload-SN -VirtualNetwork $testvnet
$NIC01 = New-AzNetworkInterface -Name Srv-Work -ResourceGroupName Test-FW-RG -Location "East us" -Subnet $wsn

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC01.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2019-Datacenter' -Version latest

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
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetwork $testVnet -PublicIpAddress $FWpip

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

Die Anwendungsregel lässt ausgehenden Zugriff auf www.google.com zu.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

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

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändern der primären und sekundären DNS-Adresse für die Netzwerkschnittstelle **Srv-Work**

In diesem Verfahren konfigurieren Sie zu Testzwecken die primäre und sekundäre DNS-Adresse des Servers. Hierbei handelt es sich nicht um eine generelle Azure Firewall-Anforderung.

```azurepowershell
$NIC01.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC01.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC01 | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testen der Firewall

Testen Sie nun die Firewall, um sicherzustellen, dass sie wie erwartet funktioniert.

1. Stellen Sie unter Verwendung von Bastion eine Verbindung mit dem virtuellen Computer **Srv-Work** her, und melden Sie sich an. 

   :::image type="content" source="media/deploy-ps/bastion.png" alt-text="Herstellen einer Verbindung unter Verwendung von Bastion":::

3. Öffnen Sie auf **Srv-Work** ein PowerShell-Fenster, und führen Sie die folgenden Befehle aus:

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

   Die `www.google.com`-Anforderungen sollten erfolgreich sein, und die `www.microsoft.com`-Anforderungen sollten fehlschlagen. Dies zeigt, dass Ihre Firewall-Regeln wie erwartet funktionieren.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

* Sie können DNS-Namen mithilfe des konfigurierten externen DNS-Servers auflösen.
* Sie können zum einzigen zulässigen FQDN navigieren, aber nicht zu anderen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **Test-FW-RG** löschen, wenn Sie sie nicht mehr benötigen. Dadurch werden alle firewallbezogenen Ressourcen gelöscht:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md)