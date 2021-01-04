---
title: Erstellen einer hochverfügbaren SAP Multi-SID-Konfiguration in Azure
description: Erfahren Sie, wie Sie eine hochverfügbare SAP NetWeaver-Multi-SID-Konfiguration auf virtuellen Windows-Computern erstellen.
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d9e611ea3bd1243ef93bd5731199c549ed7e431
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951297"
---
# <a name="create-a-high-availability-sap-netweaver-multi-sid-configuration"></a>Erstellen einer hochverfügbaren SAP NetWeaver Multi-SID-Konfiguration

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

Im September 2016 veröffentlichte Microsoft ein Feature, mit dem Sie mithilfe eines internen Azure-Lastenausgleichs mehrere virtuelle IP-Adressen verwalten können. Diese Funktionalität ist im externen Azure-Lastenausgleich bereits vorhanden.

Falls Sie eine SAP-Bereitstellung haben, können Sie einen internen Lastenausgleich verwenden, um eine Windows-Clusterkonfiguration für die SAP ASCS/SCS-Instanz zu erstellen, wie im [Handbuch zum Thema „Hochverfügbarkeit“ von SAP NetWeaver auf virtuellen Windows-Computern][sap-ha-guide] dargelegt.

Dieser Artikel behandelt den Übergang von einer einzelnen ASCS/SCS-Installation zu einer SAP Multi-SID-Konfiguration durch Installation zusätzlicher SAP ASCS/SCS-Clusterinstanzen auf einem vorhandenen Windows Server Failover Clustering (WSFC). Wenn dieser Vorgang abgeschlossen ist, haben Sie einen SAP Multi-SID-Cluster konfiguriert.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
Sie haben bereits einen WSFC-Cluster konfiguriert, der für eine SAP ASCS/SCS-Instanz verwendet wird, wie im [Handbuch zum Thema „Hochverfügbarkeit“ von SAP NetWeaver auf virtuellen Windows-Computern][sap-ha-guide] beschrieben und in diesem Diagramm gezeigt.

![Hohe Verfügbarkeit für SAP ASCS/SCS-Instanzen][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Zielarchitektur

Das Ziel besteht darin, mehrere SAP ABAP ASCS- oder SAP Java SCS-Clusterinstanzen auf dem gleichen WSFC-Cluster zu installieren, wie hier gezeigt:

![Mehrere SAP ASCS/SCS-Clusterinstanzen in Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Es besteht eine Höchstgrenze für die Anzahl privater Front-End-IPs für jedes interne Azure-Lastenausgleichsmodul.
>
>Dies bedeutet, dass die maximale Anzahl von SAP ASCS/SCS-Instanzen auf einem WSFC-Cluster gleich der maximalen Anzahl privater Front-End-IPs pro internem Azure-Lastenausgleichsmodul ist.
>

Weitere Informationen zu den Grenzwerten für Lastenausgleichsmodule finden Sie im Abschnitt „Private Front-End-IP pro Lastenausgleichsmodul“ unter [Netzwerkgrenzwerte: Azure Resource Manager][networking-limits-azure-resource-manager].

Die vollständige Szene mit zwei hoch verfügbaren SAP-Systemen sieht dann so aus:

![SAP Multi-SID-Setup für hohe Verfügbarkeit mit zwei SAP-System-SIDs][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Das Setup muss die folgenden Bedingungen erfüllen:
> - Die SAP ASCS/SCS-Instanzen müssen sich den gleichen WSFC-Cluster teilen.
> - Jede DBMS-SID muss über ihren eigenen dedizierten WSFC-Cluster verfügen.
> - SAP-Anwendungsserver, die zur gleichen SAP-System-SID gehören, müssen eigene dedizierte VMs aufweisen.


## <a name="prepare-the-infrastructure"></a>Vorbereiten der Infrastruktur
Um Ihre Infrastruktur vorzubereiten, können Sie mit den folgenden Parametern eine zusätzliche SAP ASCS/SCS-Instanz installieren:

| Parametername | Wert |
| --- | --- |
| SAP ASCS/SCS-SID |pr1-lb-ascs |
| Interner Load Balancer für das SAP-DBMS | PR5 |
| Name des virtuellen SAP-Hosts | pr5-sap-cl |
| IP-Adresse des virtuellen SAP ASCS/SCS-Hosts (zusätzliche IP-Adresse für den Azure-Lastenausgleich) | 10.0.0.50 |
| SAP ASCS/SCS-Instanznummer | 50 |
| ILB-Testport für die zusätzliche SAP ASCS/SCS-Instanz | 62350 |

> [!NOTE]
> Bei SAP ASCS/SCS-Clusterinstanzen ist für jede IP-Adresse ein eindeutiger Testport erforderlich. Wenn für eine IP-Adresse eines internen Azure-Lastenausgleichsmoduls beispielsweise der Testport 62300 verwendet wird, können keine anderen IP-Adressen des Lastenausgleichsmoduls den Testport 62300 nutzen.
>
>Für unsere Zwecke verwenden wir Testport 62350, da Testport 62300 bereits reserviert ist.

Sie können eine zusätzliche SAP ASCS/SCS-Instanz auf dem vorhandenen WSFC-Cluster mit zwei Knoten installieren:

| Rolle für virtuellen Computer | Hostname für virtuellen Computer | Statische IP-Adresse |
| --- | --- | --- |
| 1\. Clusterknoten für ASCS/SCS-Instanz |pr1-ascs-0 |10.0.0.10 |
| 2\. Clusterknoten für ASCS/SCS-Instanz |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz auf dem DNS-Server

Sie können einen DNS-Eintrag für den virtuellen Hostnamen der ASCS/SCS-Instanz mit den folgenden Parametern erstellen:

| Neuer virtueller SAP ASCS/SCS-Hostname | Zugeordnete IP-Adresse |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Neuer Hostname und neue IP-Adresse werden, wie im folgenden Screenshot ersichtlich ist, im DNS-Manager angezeigt:

![DNS-Manager-Liste, in der der definierte DNS-Eintrag des neuen virtuellen SAP ASCS/SCS-Clusternamens und der TCP/IP-Adresse hervorgehoben ist][sap-ha-guide-figure-6004]

Das Verfahren zum Erstellen eines DNS-Eintrags ist ebenfalls ausführlich im [Haupthandbuch zum Thema „Hochverfügbarkeit“ von SAP NetWeaver auf virtuellen Windows-Computern][sap-ha-guide-9.1.1] beschrieben.

> [!NOTE]
> Die neue IP-Adresse, die Sie dem virtuellen Hostnamen der zusätzlichen ASCS/SCS-Instanz zuweisen, muss mit der neuen IP-Adresse identisch sein, die Sie dem SAP-Azure-Lastenausgleichsmodul zugeordnet haben.
>
>In diesem Szenario ist die IP-Adresse 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Hinzufügen einer IP-Adresse zu einem vorhandenen internen Azure-Lastenausgleichsmodul mit PowerShell

Um mehrere SAP ASCS/SCS-Instanzen auf dem gleichen WSFC-Cluster zu erstellen, verwenden Sie PowerShell, um einem vorhandenen internen Azure-Lastenausgleichsmodul eine IP-Adresse hinzuzufügen. Für jede IP-Adresse sind eigene Lastenausgleichsregeln, ein Testport, ein Front-End-Pool und ein Back-End-Pool erforderlich.

Mit dem folgenden Skript wird einem vorhandenen Lastenausgleichsmodul eine neue IP-Adresse hinzugefügt. Aktualisieren Sie die PowerShell-Variablen für Ihre Umgebung. Das Skript erstellt alle erforderlichen Lastenausgleichsregeln für alle SAP ASCS /SCS-Ports.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Nach Ausführung des Skripts werden die Ergebnisse im Azure-Portal angezeigt, wie im folgenden Screenshot gezeigt:

![Neuer Front-End-IP-Pool im Azure-Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Hinzufügen von Datenträgern zu Clustercomputern und Konfigurieren des gemeinsamen SIOS-Clusterdatenträgers

Sie müssen für jede zusätzliche SAP ASCS/SCS-Instanz einen neuen gemeinsamen Clusterdatenträger hinzufügen. Als gemeinsamer WSFC-Clusterdatenträger für Windows Server 2012 R2 wird aktuell die SIOS DataKeeper-Softwarelösung verwendet.

Gehen Sie folgendermaßen vor:
1. Fügen Sie jedem der Clusterknoten einen weiteren Datenträger oder weitere Datenträger gleicher Größe (als Stripeset) hinzu, und formatieren Sie sie.
2. Konfigurieren Sie Speicherreplikation mit SIOS DataKeeper.

Dieses Verfahren setzt voraus, dass Sie auf den WSFC-Clustercomputern bereits SIOS DataKeeper installiert haben. Wenn Sie dies installiert haben, müssen Sie jetzt die Replikation zwischen den Computern konfigurieren. Das Verfahren ist ausführlich im [Haupthandbuch zum Thema „Hochverfügbarkeit“ von SAP NetWeaver auf virtuellen Windows-Computern][sap-ha-guide-8.12.3.3] beschrieben.  

![Die synchrone Spiegelung von DataKeeper für den neuen gemeinsamen SAP ASCS/SCS-Datenträger ist aktiv.][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Bereitstellen von virtuellen Computern für SAP-Anwendungsserver und DBMS-Cluster

Um die Vorbereitung der Infrastruktur für das zweite SAP-System abzuschließen, müssen Sie folgende Schritte ausführen:

1. Stellen Sie dedizierte VMs für SAP-Anwendungsserver bereit, und platzieren Sie sie in ihrer eigenen dedizierten Verfügbarkeitsgruppe.
2. Stellen Sie dedizierte VMs für DBMS-Cluster bereit, und platzieren Sie sie in ihrer eigenen dedizierten Verfügbarkeitsgruppe.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Installieren des zweiten SAP SID2-NetWeaver-Systems

Der vollständige Vorgang der Installation eines zweiten SAP SID2-Systems wird im [Haupthandbuch zum Thema „Hochverfügbarkeit“ von SAP NetWeaver auf virtuellen Windows-Computern][sap-ha-guide-9] beschrieben.

Das allgemeine Verfahren lautet wie folgt:

1. [Installieren des ersten SAP-Clusterknotens][sap-ha-guide-9.1.2]  
 In diesem Schritt installieren Sie SAP mit einer hoch verfügbaren ASCS/SCS-Instanz auf dem **VORHANDENEN WSFC-Clusterknoten 1**.

2. [Ändern des SAP-Profils der ASCS/SCS-Instanz][sap-ha-guide-9.1.3]

3. [Konfigurieren eines Testports][sap-ha-guide-9.1.4]  
 In diesem Schritt konfigurieren Sie eine SAP-Clusterressource SAP-SID2-IP-Testport mithilfe von PowerShell. Führen Sie diese Konfiguration auf einem der SAP ASCS/SCS-Clusterknoten aus.

4. [Installieren der Datenbankinstanz][sap-ha-guide-9.2].  
 In diesem Schritt installieren Sie DBMS auf einem dedizierten WSFC-Cluster.

5. [Installieren des zweiten Clusterknotens][sap-ha-guide-9.3].  
 In diesem Schritt installieren Sie SAP mit einer hoch verfügbaren ASCS/SCS-Instanz auf dem vorhandenen WSFC-Clusterknoten 2.

6. Öffnen Sie Windows-Firewallports für die SAP ASCS/SCS-Instanz und den Testport.  
 Öffnen Sie auf beiden Clusterknoten, die für die SAP ASCS/SCS-Instanzen verwendet werden, alle Windows-Firewallports, die von SAP ASCS/SCS verwendet werden. Diese Ports sind im [Haupthandbuch zum Thema „Hochverfügbarkeit“ von SAP NetWeaver auf virtuellen Windows-Computern][sap-ha-guide-8.8] aufgelistet.  
 Öffnen Sie außerdem den Testport für das interne Azure-Lastenausgleichsmodul, in unserem Szenario 62350.

7. [Ändern des Starttyps der Windows-Dienstinstanz für SAP ERS][sap-ha-guide-9.4]

8. [Installieren des primären SAP-Anwendungsservers][sap-ha-guide-9.5] auf dem neuen dedizierten virtuellen Computer

9. [Installieren des zusätzlichen SAP-Anwendungsservers][sap-ha-guide-9.6] auf dem neuen dedizierten virtuellen Computer

10. [Testen des Failovers der SAP ASCS/SCS-Instanz und der SIOS-Replikation][sap-ha-guide-10]

## <a name="next-steps"></a>Nächste Schritte

- [Netzwerkgrenzwerte: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Mehrere VIPs für Azure Load Balancer][load-balancer-multivip-overview]
- [SAP NetWeaver auf virtuellen Windows-Computern – Handbuch zum Thema „Hochverfügbarkeit“][sap-ha-guide]
