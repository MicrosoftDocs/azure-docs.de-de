---
title: Konfigurieren von Verfügbarkeitsgruppenlistenern und Lastenausgleich (PowerShell)
description: Es wird beschrieben, wie Sie Verfügbarkeitsgruppenlistener unter dem Azure Resource Manager-Modell konfigurieren, indem Sie ein internes Lastenausgleichsmodul mit einer oder mehreren IP-Adressen verwenden.
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 9337d1c2767923e6dc7c6b267e0c180b460a116e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359420"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Konfigurieren von Always On-Verfügbarkeitsgruppenlistenern – Resource Manager

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Dokument erfahren Sie, wie Sie PowerShell für eine der folgenden Aufgaben nutzen können:
- Erstellen eines Lastenausgleichs
- Hinzufügen von IP-Adressen zu einem Lastenausgleich für SQL Server-Verfügbarkeitsgruppen

Ein Verfügbarkeitsgruppenlistener ist der Name eines virtuellen Netzwerks, mit dem Clients eine Verbindung herstellen, um Zugriff auf die Datenbank zu erhalten. Unter Azure Virtual Machines enthält ein Lastenausgleich die IP-Adresse des Listeners. Mit dem Lastenausgleichsmodul wird Datenverkehr auf die Instanz von SQL Server geleitet, die über den Testport lauscht. Normalerweise wird für eine Verfügbarkeitsgruppe ein interner Load Balancer verwendet. Mit einem internen Azure Load Balancer kann auch eine größere Anzahl von IP-Adressen gehostet werden. Für jede IP-Adresse wird ein bestimmter Testport verwendet. 

Die Möglichkeit zum Zuweisen mehrerer IP-Adressen zu einem internen Lastenausgleichsmodul ist neu in Azure und nur im Resource Manager-Modell verfügbar. Für diese Aufgabe benötigen Sie eine SQL Server-Verfügbarkeitsgruppe, die in Azure Virtual Machines unter dem Resource Manager-Modell bereitgestellt wird. Beide virtuellen SQL Server-Computer müssen der gleichen Verfügbarkeitsgruppe angehören. Mithilfe der [Microsoft-Vorlage](./availability-group-quickstart-template-configure.md) können Sie die Verfügbarkeitsgruppe in Azure Resource Manager automatisch erstellen. Mit dieser Vorlage wird die Verfügbarkeitsgruppe automatisch erstellt, einschließlich des internen Lastenausgleichsmoduls. Alternativ können Sie auch eine [Always On-Verfügbarkeitsgruppe manuell konfigurieren](availability-group-manually-configure-tutorial.md).

Um die Schritte in diesem Artikel ausführen zu können, müssen die Verfügbarkeitsgruppen bereits konfiguriert sein.  

Verwandte Themen:

* [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in einem virtuellen Azure-Computer (GUI)](availability-group-manually-configure-tutorial.md)   
* [Konfigurieren einer VNet-zu-VNet-Verbindung mit Azure Resource Manager und PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>Überprüfen der PowerShell-Version

Die Beispiele in diesem Artikel wurden mit Version 5.4.1 des Azure PowerShell-Moduls getestet.

Vergewissern Sie sich, dass Sie mindestens Version 5.4.1 des PowerShell-Moduls verwenden.

Siehe [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>Konfigurieren der Windows-Firewall

Konfigurieren Sie die Windows-Firewall so, dass der SQL Server-Zugriff zulässig ist. Die Firewallregeln lassen TCP-Verbindungen mit den Ports für die SQL Server-Instanz und den Listenertest zu. Weitere Informationen finden Sie unter [Konfigurieren einer Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access#Anchor_1). Erstellen Sie für den SQL Server-Port und den Testport eine Regel für eingehenden Datenverkehr.

Wenn Sie den Zugriff mit einer Azure-Netzwerksicherheitsgruppe einschränken, stellen Sie sicher, dass die Zulassungsregeln die IP-Adressen des virtuellen SQL Server-Back-End-Computers, die Floating IP-Adressen des Lastenausgleichs für den AG-Listener und die IP-Adresse der Hauptressourcen des Clusters (falls zutreffend) umfassen.

## <a name="determine-the-load-balancer-sku-required"></a>Festlegen der erforderlichen Load Balancer-SKU

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ist in zwei SKUs verfügbar: Basic und Standard. Die Verwendung von Load Balancer Standard wird empfohlen. Wenn die virtuellen Computer in einer Verfügbarkeitsgruppe enthalten sind, kann Load Balancer Basic verwendet werden. Wenn die virtuellen Computer sich in einer Verfügbarkeitszone befinden, ist ein Standardlastenausgleich erforderlich. Für Load Balancer Standard müssen für alle virtuellen Computer Standard-IP-Adressen verwendet werden.

Die aktuelle [Microsoft-Vorlage](./availability-group-quickstart-template-configure.md) für eine Verfügbarkeitsgruppe verwendet Load Balancer Basic mit grundlegenden IP-Adressen.

   > [!NOTE]
   > Sie müssen einen [Dienstendpunkt](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) konfigurieren, wenn Sie einen Standardlastenausgleich und Azure Storage als Cloudzeugen verwenden. 
   > 

In den Beispielen in diesem Artikel wird Load Balancer Standard angegeben. In den Beispielen ist `-sku Standard` im Skript enthalten.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Zum Erstellen einer Load Balancer Basic-Instanz entfernen Sie `-sku Standard` aus der Zeile, über die der Load Balancer erstellt wird. Beispiel:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Beispielskript: Erstellen eines internen Lastenausgleichs mit PowerShell

> [!NOTE]
> Wenn Sie Ihre Verfügbarkeitsgruppe mit der [Microsoft-Vorlage](./availability-group-quickstart-template-configure.md) erstellt haben, wurde der interne Load Balancer bereits erstellt.

Mit dem folgenden PowerShell-Skript wird ein internes Lastenausgleichsmodul erstellt, die Lastenausgleichsregeln werden erstellt und eine IP-Adresse für das Lastenausgleichsmodul wird festgelegt. Öffnen Sie Windows PowerShell ISE, und fügen Sie das Skript im Bereich „Skript“ ein, um es auszuführen. Melden Sie sich mithilfe von `Connect-AzAccount` bei PowerShell an. Verwenden Sie bei mehreren Azure-Abonnements `Select-AzSubscription` , um das Abonnement festzulegen. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a> Beispielskript: Hinzufügen einer IP-Adresse zu einem vorhandenen Lastenausgleich mit PowerShell

Wenn Sie mehrere Verfügbarkeitsgruppen verwenden möchten, fügen Sie dem Load Balancer eine zusätzliche IP-Adresse hinzu. Für jede IP-Adresse sind eine eigene Lastenausgleichsregel, ein Testport und ein Front-End-Port erforderlich.

Der Front-End-Port ist der Port, der von Anwendungen zum Herstellen einer Verbindung mit der SQL Server-Instanz genutzt wird. IP-Adressen für unterschiedliche Verfügbarkeitsgruppen können denselben Front-End-Port verwenden.

> [!NOTE]
> Bei SQL Server-Verfügbarkeitsgruppen wird für jede IP-Adresse ein bestimmter Testport benötigt. Wenn für eine IP-Adresse eines Lastenausgleichsmoduls beispielsweise der Testport 59999 verwendet wird, können keine anderen IP-Adressen des Lastenausgleichsmoduls den Testport 59999 nutzen.

* Informationen zu den Grenzwerten für Load Balancer finden Sie im Abschnitt **Private Front-End-IP pro Load Balancer** unter [Netzwerklimits – Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Informationen zu den Grenzwerten von Verfügbarkeitsgruppen finden Sie unter [Einschränkungen (Verfügbarkeitsgruppen)](/sql/database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability#RestrictionsAG).

Mit dem folgenden Skript wird einem vorhandenen Lastenausgleichsmodul eine neue IP-Adresse hinzugefügt. Das interne Lastenausgleichsmodul verwendet den Listenerport für den Front-End-Port des Lastenausgleichs. Dieser Port kann der Port sein, über den SQL Server lauscht. Für Standardinstanzen von SQL Server lautet der Port 1433. Für die Lastenausgleichsregel einer Verfügbarkeitsgruppe wird eine Floating IP-Adresse (Direct Server Return) benötigt, sodass der Back-End-Port dem Front-End-Port entspricht. Aktualisieren Sie die Variablen für Ihre Umgebung. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>Konfigurieren des Listeners

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Festlegen des Listenerports in SQL Server Management Studio

1. Starten Sie SQL Server Management Studio, und stellen Sie eine Verbindung mit dem primären Replikat her.

1. Navigieren Sie zu **Hochverfügbarkeit mit Always On** > **Verfügbarkeitsgruppen** > **Verfügbarkeitsgruppenlistener**. 

1. Jetzt sollte der Listenername angezeigt werden, den Sie im Failovercluster-Manager erstellt haben. Klicken Sie mit der rechten Maustaste auf den Listenernamen, und wählen Sie **Eigenschaften** aus.

1. Geben Sie im Feld **Port** die Portnummer für den Verfügbarkeitsgruppenlistener an. Verwenden Sie dabei den zuvor verwendeten Wert für $EndpointPort (Standardwert: 1433). Wählen Sie anschließend **OK** aus.

## <a name="test-the-connection-to-the-listener"></a>Testen der Verbindung mit dem Listener

Gehen Sie wie folgt vor, um die Verbindung zu testen:

1. Stellen Sie eine RDP-Verbindung (Remote Desktop Protocol) mit einer SQL Server-Instanz her, die sich im selben virtuellen Netzwerk befindet, aber nicht für das Replikat zuständig ist. Hierbei kann es sich um die andere SQL Server-Instanz im Cluster handeln.

1. Testen Sie die Verbindung mithilfe des **sqlcmd** -Hilfsprogramms. Das folgende Skript stellt beispielsweise über den Listener eine **sqlcmd** -Verbindung mit Windows-Authentifizierung mit dem primären Replikat her:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Geben Sie den Port in der Verbindungszeichenfolge an, wenn der Listener einen anderen Port als den Standardport (1433) verwendet. Mit dem folgenden sqlcmd-Befehl wird beispielsweise eine Verbindung mit einem Listener über Port 1435 hergestellt: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Die sqlcmd-Verbindung wird automatisch mit der SQL Server-Instanz hergestellt, die das primäre Replikat hostet. 

> [!NOTE]
> Vergewissern Sie sich, dass der angegebene Port in der Firewall beider SQL Server geöffnet ist. Beide Server benötigen eine eingehende Regel für den TCP-Port, den Sie verwenden möchten. Weitere Informationen finden Sie unter [Hinzufügen oder Bearbeiten einer Firewallregel](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

## <a name="guidelines-and-limitations"></a>Richtlinien und Einschränkungen

Für Verfügbarkeitsgruppenlistener in Azure mit internem Load Balancer gelten folgenden Richtlinien:

* Bei Verwendung eines internen Load Balancers erfolgt der Zugriff auf den Listener nur innerhalb desselben virtuellen Netzwerks.

* Wenn Sie den Zugriff über eine Azure-Netzwerksicherheitsgruppe Zugriff einschränken, stellen Sie sicher, dass die Zulassungsregeln Folgendes umfassen:
  - Die IP-Adressen der SQL Server-VMs im Back-End
  - Die Floating IP-Adressen des Lastenausgleichs für den Verfügbarkeitsgruppenlistener
  - Die IP-Adresse der Hauptressource des Clusters, falls zutreffend

* Erstellen Sie einen Dienstendpunkt, wenn Sie einen Standardlastenausgleich mit Azure Storage als Cloudzeugen verwenden. Weitere Informationen finden Sie unter [Gewähren des Zugriffs über ein virtuelles Netzwerk](../../../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>Weitere Informationen finden Sie unter

Weitere Informationen finden Sie unter [Manuelles Konfigurieren der Always On-Verfügbarkeitsgruppe auf virtuellen Azure-Computern](availability-group-manually-configure-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-Cmdlets

Verwenden Sie die folgenden PowerShell-Cmdlets, um ein internes Lastenausgleichsmodul für Azure Virtual Machines zu erstellen.

* [New-AzLoadBalancer](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancer) erstellt einen Load Balancer. 
* [New-AzLoadBalancerFrontendIpConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerFrontendIpConfig) erstellt eine Front-End-IP-Konfiguration für einen Load Balancer. 
* [New-AzLoadBalancerRuleConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerRuleConfig) erstellt eine Regelkonfiguration für einen Load Balancer. 
* [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerBackendAddressPoolConfig) erstellt eine Back-End-Adresspoolkonfiguration für einen Load Balancer. 
* [New-AzLoadBalancerProbeConfig](/powershell/module/Azurerm.Network/New-AzureRmLoadBalancerProbeConfig) erstellt eine Testkonfiguration für einen Load Balancer.
* [Remove-AzLoadBalancer](/powershell/module/Azurerm.Network/Remove-AzureRmLoadBalancer) entfernt einen Load Balancer aus einer Azure-Ressourcengruppe.