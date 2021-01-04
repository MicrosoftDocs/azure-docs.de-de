---
title: 'Tutorial: Sichern Sie Ihren virtuellen Hub mithilfe von Azure PowerShell'
description: In diesem Artikel wird beschrieben, wie Sie ein Azure Virtual WAN in einer Region erstellen, in der Azure Firewall im Hub aktiviert ist.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: e2bcdf947db60fb50b44abba9d6a224768840b28
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656138"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Tutorial: Sichern Sie Ihren virtuellen Hub mithilfe von Azure PowerShell

In diesem Tutorial erstellen Sie eine virtuelle WAN-Instanz mit einem virtuellen Hub in einer Region, und Sie stellen eine Azure Firewall im virtuellen Hub bereit, um die Konnektivität zu sichern. In diesem Beispiel veranschaulichen Sie die sichere Konnektivität zwischen virtuellen Netzwerken. Der Datenverkehr zwischen virtuellen Netzwerken und Site-to-Site-, Point-to-Site- oder ExpressRoute-Zweigstellen wird ebenfalls von Virtual Secure Hub unterstützt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen des virtuellen WANs
> * Bereitstellen der Azure Firewall und Konfigurieren des benutzerdefinierten Routings
> * Testen der Konnektivität

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- PowerShell 7

   Für dieses Tutorial müssen Sie Azure PowerShell lokal unter PowerShell 7 ausführen. Informationen zur Installation von PowerShell 7 finden Sie unter [Migrieren von Windows PowerShell 5.1 zu PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7).
- Az.Network, Version 3.2.0

    Wenn Sie über Az.Network in der Version 3.4.0 oder höher verfügen, müssen Sie ein Downgrade durchführen, um einige der Befehle in diesem Tutorial verwenden zu können. Sie können die Version Ihres Az.Network-Moduls mit dem Befehl `Get-InstalledModule -Name Az.Network` überprüfen. Führen Sie `Uninstall-Module -name az.network` aus, um das Az.Network-Modul zu deinstallieren. Führen Sie `Install-Module az.network -RequiredVersion 3.2.0 -force` aus, um das Modul Az.Network 3.2.0 zu installieren.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Anfängliche Virtual WAN-Bereitstellung

In einem ersten Schritt müssen Sie einige Variablen festlegen und die Ressourcengruppe, die virtuelle WAN-Instanz und den virtuellen Hub erstellen:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Erstellen Sie zwei virtuelle Netzwerke, und verbinden Sie sie als Spokes mit dem Hub:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

An diesem Punkt verfügen Sie über ein voll funktionsfähiges Virtual WAN mit Any-to-Any-Konnektivität. Um die Sicherheit zu erhöhen, müssen Sie jedem virtuellen Hub eine Azure Firewall zur Verfügung stellen. Zur effizienten Verwaltung der Azure Firewall-Instanz des virtuellen WANs können Richtlinien für die Firewall verwendet werden. Daher wird in diesem Beispiel auch eine Firewallrichtlinie erstellt:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Das Aktivieren der Protokollierung von der Azure Firewall zu Azure Monitor ist optional, aber in diesem Beispiel verwenden Sie die Firewall-Protokolle, um nachzuweisen, dass Datenverkehr die Firewall durchquert:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Bereitstellen der Azure Firewall und Konfigurieren des benutzerdefinierten Routings

Jetzt verfügen Sie über eine Azure Firewall im Hub, aber Sie müssen noch das Routing ändern, sodass das virtuelle WAN den Datenverkehr aus den virtuellen Netzwerken und aus den Zweigstellen durch die Firewall leitet. Dies erfolgt in zwei Schritten:

1. Konfigurieren Sie alle virtuellen Netzwerkverbindungen (und Zweigstellenverbindungen, falls es welche gab) so, dass sie an die Routingtabelle `None` weitergegeben werden. Der Effekt dieser Konfiguration ist, dass andere virtuelle Netzwerke und Zweigstellen ihre Präfixe nicht kennen lernen und somit kein Routing aufweisen, um sie zu erreichen.
1. Jetzt können Sie statische Routen in die Routingtabelle `Default` einfügen (in der alle virtuellen Netzwerke und Zweigstellen standardmäßig zugeordnet sind), damit der gesamte Datenverkehr an die Azure Firewall gesendet wird.

> [!NOTE]
> Dies ist die Konfiguration, die bereitgestellt wird, wenn die Konnektivität vom Azure-Portal mit Azure Firewall Manager gesichert wird.

Beginnen Sie mit dem ersten Schritt, um Ihre virtuellen Netzwerkverbindungen so zu konfigurieren, dass sie an die Routingtabelle `None` weitergegeben werden:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Jetzt können Sie mit dem zweiten Schritt fortfahren, um die statischen Routen zur Routingtabelle `Default` hinzuzufügen. In diesem Beispiel wenden Sie die Standardkonfiguration an, die Azure Firewall Manager beim Sichern der Konnektivität in einem virtuellen WAN generieren würde, aber Sie können die Liste der Präfixe in der statischen Route ändern, um sie an Ihre spezifischen Anforderungen anzupassen:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Testen der Konnektivität

Jetzt verfügen Sie über einen voll funktionsfähigen sicheren Hub. Um die Konnektivität zu testen, benötigen Sie einen virtuellen Computer in jedem virtuellen Spoke-Netzwerk, das mit dem Hub verbunden ist:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

Die Standardkonfiguration in der Firewallrichtlinie besteht darin, alles abzulegen. Daher müssen Sie einige Regeln konfigurieren. Beginnen Sie mit DNAT-Regeln, sodass die getesteten virtuellen Computer über die öffentliche IP-Adresse der Firewall zugänglich sind:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Jetzt können Sie einige Beispielregeln konfigurieren. Definieren Sie eine Netzwerkregel, die SSH-Datenverkehr gestattet, sowie eine Anwendungsregel, die den Internetzugriff auf den vollqualifizierten Domänennamen `ifconfig.co` erlaubt. Diese URL gibt die Quell-IP-Adresse zurück, die sie in der HTTP-Anforderung erkennt:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Bevor Sie tatsächlich Datenverkehr senden, können Sie die effektiven Routen der virtuellen Computer überprüfen. Sie sollten die Präfixe enthalten, die aus dem Virtual WAN (`0.0.0.0/0` plus RFC1918) gelernt wurden, aber nicht das Präfix der anderen Spoke:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Generieren Sie jetzt Datenverkehr von einem virtuellen Computer zum anderen und vergewissern Sie sich, dass er in der Azure Firewall abgelegt wurde. In den folgenden SSH-Befehlen müssen Sie die Fingerabdrücke der virtuellen Computer akzeptieren und das Kennwort angeben, das Sie bei der Erstellung der virtuellen Computer definiert haben. In diesem Beispiel werden Sie fünf ICMP-Echoanforderungspakete von dem virtuellen Computer in Spoke1 an Spoke2 senden, sowie einen TCP-Verbindungsversuch an Port 22 mithilfe des Linux-Hilfsprogramms `nc` durchführen (mit den `-vz`-Flags sendet es einfach eine Verbindungsanforderung und zeigt das Ergebnis an). Sie sollten erkennen, dass beim Ping ein Fehler auftritt und der TCP-Verbindungsversuch an Port 22 erfolgreich ist, da dies von der zuvor konfigurierten Netzwerkregel erlaubt wird:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Sie können auch den Internetdatenverkehr überprüfen. HTTP-Anforderungen über das Hilfsprogramm `curl` an den FQDN, den Sie in der Firewallrichtlinie (`ifconfig.co`) zugelassen haben, sollten funktionieren, bei HTTP-Anforderungen für jedes andere Ziel sollten jedoch Fehler auftreten (in diesem Beispiel testen Sie mit `bing.com`):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

Der einfachste Weg zu überprüfen, ob die Pakete von der Firewall verworfen wurden, ist die Überprüfung der Protokolle. Da Sie die Azure Firewall so konfiguriert haben, dass sie Protokolle an Azure Monitor sendet, können Sie die Kusto-Abfragesprache verwenden, um die relevanten Protokolle von Azure Monitor abzurufen:

> [!NOTE]
> Es kann etwa 1 Minute dauern, bis die Protokolle an Azure Monitor gesendet werden.

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

Im vorherigen Befehl sollten Sie andere Einträge sehen:

* Ihre SSH-Verbindung mit DNAT
* Verworfene ICMP-Pakete zwischen den VMs in den Spokes (10.1.1.4 und 10.1.2.4)
* Zulässige SSH-Verbindungen zwischen den VMs in den Spokes

Hier sehen Sie eine Beispielausgabe, die vom obigen Befehl generiert wird:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Wenn Sie die Protokolle für die Anwendungsregeln sehen (die die zulässigen und verweigerten HTTP-Verbindungen beschreiben) oder die Art und Weise ändern möchten, wie die Protokolle angezeigt werden, können Sie es mit anderen KQL-Abfragen versuchen. Einige Beispiele finden Sie unter [Azure Monitor-Protokolle für Azure Firewall](../firewall/log-analytics-samples.md).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Löschen der Testumgebung können Sie die Ressourcengruppe mit allen enthaltenen Objekten entfernen:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was sind vertrauenswürdige Sicherheitspartner (Vorschau)?](trusted-security-partners.md)