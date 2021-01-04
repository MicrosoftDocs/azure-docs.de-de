---
title: Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS mit WSFC und freigegebenem Datenträger in Azure | Microsoft-Dokumentation
description: Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen unter Verwendung von Windows Server-Failoverclustering und freigegebene Datenträger in Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd0c2e720736feb9faf129237ba960dedda08c6f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500987"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-shared-disk-on-azure"></a>Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen unter Verwendung von Windows Server-Failoverclustering und freigegebene Datenträger in Azure

> ![Windows-Betriebssystem][Logo_Windows] Windows
>

Wenn Sie über eine SAP-Bereitstellung verfügen, müssen Sie einen internen Lastenausgleich verwenden, um eine Windows-Clusterkonfiguration für SAP Central Services-Instanzen (ASCS/SCS) zu erstellen.

Dieser Artikel behandelt den Wechsel von einer ASCS/SCS-Einzelinstallation zu einer SAP-Multi-SID-Konfiguration. Zu diesem Zweck werden zusätzliche SAP ASCS/SCS-Clusterinstanzen in einem vorhandenen WSFC-Cluster (Windows Server-Failoverclustering) mit freigegebenem Datenträger installiert. Zum Simulieren des freigegebenen Datenträgers wird SIOS verwendet. Wenn dieser Vorgang abgeschlossen ist, haben Sie einen SAP Multi-SID-Cluster konfiguriert.

> [!NOTE]
> Diese Funktion ist nur im Azure Resource Manager-Bereitstellungsmodell verfügbar.
>
>Es besteht eine Höchstgrenze für die Anzahl privater Front-End-IPs für jeden internen Azure-Lastenausgleich.
>
>Dies bedeutet, dass die maximale Anzahl von SAP ASCS/SCS-Instanzen auf einem WSFC-Cluster gleich der maximalen Anzahl privater Front-End-IPs pro internem Azure-Lastenausgleichsmodul ist.
>

Weitere Informationen zu den Grenzwerten für Lastenausgleichsmodule finden Sie im Abschnitt „Private Front-End-IP pro Lastenausgleichsmodul“ unter [Netzwerkgrenzwerte: Azure Resource Manager][networking-limits-azure-resource-manager].

> [!IMPORTANT]
> Floating IP-Adressen werden in IP-Konfigurationen mit zwei NICs in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie haben bereits einen WSFC-Cluster für die Verwendung einer SAP ASCS/SCS-Instanz unter Verwendung einer **Dateifreigabe** konfiguriert, wie in diesem Diagramm dargestellt.

![Hohe Verfügbarkeit für SAP ASCS/SCS-Instanzen][sap-ha-guide-figure-6001]

> [!IMPORTANT]
> Das Setup muss die folgenden Bedingungen erfüllen:
> * Die SAP ASCS/SCS-Instanzen müssen sich den gleichen WSFC-Cluster teilen.  
> * Jede SID eines Datenbank-Verwaltungssystems (DBMS) muss über ihren eigenen dedizierten WSFC-Cluster verfügen.  
> * SAP-Anwendungsserver, die zur gleichen SAP-System-SID gehören, müssen eigene dedizierte VMs aufweisen.  
> * Eine Kombination von Enqueue Replication Server 1 und Enqueue Replication Server 2 im gleichen Cluster wird nicht unterstützt.  

## <a name="sap-ascsscs-multi-sid-architecture-with-shared-disk"></a>Multi-SID-Architektur für SAP ASCS/SCS-Instanzen mit freigegebenem Datenträger

Das Ziel besteht darin, mehrere SAP ABAP ASCS- oder SAP Java SCS-Clusterinstanzen auf dem gleichen WSFC-Cluster zu installieren, wie hier gezeigt:

![Mehrere SAP ASCS/SCS-Clusterinstanzen in Azure][sap-ha-guide-figure-6002]

Weitere Informationen zu den Grenzwerten für Lastenausgleichsmodule finden Sie im Abschnitt „Private Front-End-IP pro Lastenausgleichsmodul“ unter [Netzwerkgrenzwerte: Azure Resource Manager][networking-limits-azure-resource-manager].

Die vollständige Szene mit zwei hoch verfügbaren SAP-Systemen sieht dann so aus:

![SAP Multi-SID-Setup für hohe Verfügbarkeit mit zwei SAP-System-SIDs][sap-ha-guide-figure-6003]

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a><a name="25e358f8-92e5-4e8d-a1e5-df7580a39cb0"></a> Vorbereiten der Infrastruktur für ein SAP-Multi-SID-Szenario

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
| Erster Clusterknoten für die ASCS/SCS-Instanz |pr1-ascs-0 |10.0.0.10 |
| Zweiter Clusterknoten für die ASCS/SCS-Instanz |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz auf dem DNS-Server

Sie können einen DNS-Eintrag für den virtuellen Hostnamen der ASCS/SCS-Instanz mit den folgenden Parametern erstellen:

| Neuer virtueller SAP ASCS/SCS-Hostname | Zugeordnete IP-Adresse |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Der neue Hostname und die neue IP-Adresse werden im DNS-Manager angezeigt, wie im folgenden Screenshot dargestellt:

![DNS-Manager-Liste, in der der definierte DNS-Eintrag des neuen virtuellen SAP ASCS/SCS-Clusternamens und der TCP/IP-Adresse hervorgehoben ist][sap-ha-guide-figure-6004]

> [!NOTE]
> Die neue IP-Adresse, die Sie dem virtuellen Hostnamen der zusätzlichen ASCS/SCS-Instanz zuweisen, muss mit der neuen IP-Adresse identisch sein, die Sie dem SAP-Azure-Lastenausgleichsmodul zugeordnet haben.
>
>In diesem Szenario ist die IP-Adresse 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Hinzufügen einer IP-Adresse zu einem vorhandenen internen Azure-Lastenausgleichsmodul mit PowerShell

Um mehrere SAP ASCS/SCS-Instanzen auf dem gleichen WSFC-Cluster zu erstellen, verwenden Sie PowerShell, um einem vorhandenen internen Azure-Lastenausgleichsmodul eine IP-Adresse hinzuzufügen. Für jede IP-Adresse sind eigene Lastenausgleichsregeln, ein Testport, ein Front-End-Pool und ein Back-End-Pool erforderlich.

Mit dem folgenden Skript wird einem vorhandenen Lastenausgleichsmodul eine neue IP-Adresse hinzugefügt. Aktualisieren Sie die PowerShell-Variablen für Ihre Umgebung. Das Skript erstellt alle erforderlichen Lastenausgleichsregeln für sämtliche SAP ASCS/SCS-Ports.

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

# Get the Azure virtual network and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add a second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get a new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Get an updated LP FrontendIpConfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add a back-end configuration into an existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get an updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to the back-end pool
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

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Hinzufügen von Datenträgern zu Clustercomputern und Konfigurieren des freigegebenen SIOS-Clusterdatenträgers

Sie müssen für jede zusätzliche SAP ASCS/SCS-Instanz einen neuen freigegebenen Clusterdatenträger hinzufügen. Als gemeinsamer WSFC-Clusterdatenträger für Windows Server 2012 R2 wird aktuell die SIOS DataKeeper-Softwarelösung verwendet.

Gehen Sie folgendermaßen vor:
1. Fügen Sie jedem der Clusterknoten einen weiteren Datenträger oder weitere Datenträger gleicher Größe (als Stripeset) hinzu, und formatieren Sie sie.
2. Konfigurieren Sie Speicherreplikation mit SIOS DataKeeper.

Dieses Verfahren setzt voraus, dass Sie auf den WSFC-Clustercomputern bereits SIOS DataKeeper installiert haben. Wenn Sie dies installiert haben, müssen Sie jetzt die Replikation zwischen den Computern konfigurieren. Im Kapitel [Installieren von SIOS DataKeeper Cluster Edition für den freigegebenen SAP ASCS/SCS-Clusterdatenträger][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios] wird dieser Vorgang beschrieben.  

![Die synchrone Spiegelung von DataKeeper für den neuen gemeinsamen SAP ASCS/SCS-Datenträger ist aktiv.][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-the-dbms-cluster"></a>Bereitstellen von virtuellen Computern für SAP-Anwendungsserver und DBMS-Cluster

Um die Vorbereitung der Infrastruktur für das zweite SAP-System abzuschließen, müssen Sie folgende Schritte ausführen:

1. Stellen Sie dedizierte VMs für die SAP-Anwendungsserver bereit, und platzieren Sie jede in einer eigenen dedizierten Verfügbarkeitsgruppe.
2. Stellen Sie dedizierte VMs für den DBMS-Cluster bereit, und platzieren Sie jede in einer eigenen dedizierten Verfügbarkeitsgruppe.

## <a name="install-an-sap-netweaver-multi-sid-system"></a>Installieren eines SAP NetWeaver-Multi-SID-Systems

Eine Beschreibung des gesamten Vorgangs zum Installieren eines zweiten SAP-SID2-Systems finden Sie im Leitfaden [Hochverfügbarkeitsinstallation von SAP NetWeaver mit Windows-Failoverclustern und freigegebenem Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-installation-wsfc-shared-disk].

Das allgemeine Verfahren lautet wie folgt:

1. [Installieren von SAP mit einer ASCS/SCS-Hochverfügbarkeitsinstanz][sap-high-availability-installation-wsfc-shared-disk-install-ascs]  
 In diesem Schritt installieren Sie SAP mit einer hochverfügbaren ASCS/SCS-Instanz auf dem bereits vorhandenen WSFC-Clusterknoten 1.

2. [Ändern des SAP-Profils der ASCS/SCS-Instanz][sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]

3. [Konfigurieren eines Testports][sap-high-availability-installation-wsfc-shared-disk-add-probe-port]  
 In diesem Schritt konfigurieren Sie eine SAP-Clusterressource SAP-SID2-IP-Testport mithilfe von PowerShell. Führen Sie diese Konfiguration auf einem der SAP ASCS/SCS-Clusterknoten aus.

4. Installieren der Datenbankinstanz.  
 Führen Sie zum Installieren des zweiten Clusterknotens die Schritte im SAP-Installationshandbuch aus.

5. Installieren des zweiten Clusterknotens.  
 In diesem Schritt installieren Sie SAP mit einer hoch verfügbaren ASCS/SCS-Instanz auf dem vorhandenen WSFC-Clusterknoten 2. Führen Sie zum Installieren des zweiten Clusterknotens die Schritte im SAP-Installationshandbuch aus.

6. Öffnen Sie Windows-Firewallports für die SAP ASCS/SCS-Instanz, und testen Sie den Port.  
    Öffnen Sie auf beiden Clusterknoten, die für die SAP ASCS/SCS-Instanzen verwendet werden, alle Windows-Firewallports, die von SAP ASCS/SCS verwendet werden. Die Ports für diese SAP ASCS/SCS-Instanzen werden im Kapitel [SAP ASCS/SCS-Ports][sap-net-weaver-ports-ascs-scs-ports] aufgelistet.

    Eine Liste aller anderen SAP-Ports finden Sie unter [TCP/IP-Ports aller SAP-Produkte][sap-net-weaver-ports].  

    Öffnen Sie außerdem den Testport für das interne Azure-Lastenausgleichsmodul, in unserem Szenario 62350. Eine Beschreibung finden Sie [in diesem Artikel][sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port].

8. Installieren des primären SAP-Anwendungsservers auf dem neuen dedizierten virtuellen Computer, wie in den SAP-Installationsanweisungen beschrieben.  

9. Installieren des zusätzlichen SAP-Anwendungsservers auf dem neuen dedizierten virtuellen Computer, wie in den SAP-Installationsanweisungen beschrieben.

10. [Testen des Failovers der SAP ASCS/SCS-Instanz und der SIOS-Replikation][sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]

## <a name="next-steps"></a>Nächste Schritte

- [Netzwerkgrenzwerte: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Mehrere VIPs für Azure Load Balancer][load-balancer-multivip-overview]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png



[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md