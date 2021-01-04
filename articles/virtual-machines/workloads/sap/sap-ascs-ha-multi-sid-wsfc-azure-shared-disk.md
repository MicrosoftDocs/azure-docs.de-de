---
title: Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS mit WSFC und freigegebenem Azure-Datenträger | Microsoft-Dokumentation
description: Multi-SID-Hochverfügbarkeit für eine SAP ASCS/SCS-Instanz mit WSFC und freigegebenem Azure-Datenträger
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
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43abdd1db2e8e24033332f99c583e30efbf64a00
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957400"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen mit Windows Server-Failoverclustering und freigegebenem Azure-Datenträger

> ![Windows-Betriebssystem][Logo_Windows] Windows
>

In diesem Artikel wird der Übergang von einer einzelnen ASCS/SCS-Installation zu einer SAP Multi-SID-Konfiguration durch die Installation zusätzlicher SAP ASCS/SCS-Clusterinstanzen in einem vorhandenen WSFC-Cluster (Windows Server-Failoverclustering) mit freigegebenem Azure-Datenträger behandelt. Wenn dieser Vorgang abgeschlossen ist, haben Sie einen SAP Multi-SID-Cluster konfiguriert.

## <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

Derzeit können Sie Azure SSD Premium-Datenträger als freigegebenen Azure-Datenträger für die SAP ASCS/SCS-Instanz verwenden. Es gelten die folgenden Einschränkungen:

-  Ein [Azure Ultra-Datenträger](../../disks-types.md#ultra-disk) wird nicht als freigegebener Azure-Datenträger für SAP-Workloads unterstützt. Derzeit ist es nicht möglich, Azure-VMs mithilfe eines Azure Ultra-Datenträgers in einer Verfügbarkeitsgruppe zu platzieren.
-  Ein [freigegebener Azure-Datenträger](../../disks-shared.md) mit SSD Premium-Datenträgern wird nur für VMs in einer Verfügbarkeitsgruppe unterstützt. Bei der Bereitstellung in Verfügbarkeitszonen wird dies nicht unterstützt. 
-  Der Wert [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) des freigegebenen Azure-Datenträgers bestimmt, wie viele Clusterknoten den freigegebenen Datenträger verwenden können. In der Regel werden für SAP ASCS/SCS-Instanzen zwei Knoten im Windows-Failovercluster konfiguriert. Daher muss der Wert für `maxShares` auf zwei festgelegt werden.
-  Alle VMs im SAP ASCS/SCS-Cluster müssen in derselben [Azure-Näherungsplatzierungsgruppe](../../windows/proximity-placement-groups.md) (Proximity Placement Group, PPG) bereitgestellt werden.   
   Zwar können Sie VMs im Windows-Cluster in einer Verfügbarkeitsgruppe mit freigegebenem Azure-Datenträger ohne PPG bereitstellen, doch gewährleistet PPG die physische Nähe von freigegebenen Azure-Datenträgern und Cluster-VMs und erzielt somit eine geringere Latenz zwischen den VMs und der Speicherebene.    

Weitere Informationen zu Einschränkungen für freigegebene Azure-Datenträger finden Sie im Abschnitt [Einschränkungen](../../disks-shared.md#limitations) der entsprechenden Dokumentation.  

> [!IMPORTANT]
> Beachten Sie beim Bereitstellen des SAP ASCS/SCS-Windows-Failoverclusters mit freigegebenem Azure-Datenträger, dass die Bereitstellung mit einem einzelnen freigegebenen Datenträger in nur einem Speichercluster erfolgt. Probleme mit dem Speichercluster, in dem der freigegebene Azure-Datenträger bereitgestellt wird, beeinträchtigen Ihre SAP ASCS/SCS-Instanz.  

> [!IMPORTANT]
> Das Setup muss die folgenden Bedingungen erfüllen:
> * Jede SID eines Datenbank-Verwaltungssystems (DBMS) muss über ihren eigenen dedizierten WSFC-Cluster verfügen.  
> * SAP-Anwendungsserver, die zur gleichen SAP-System-SID gehören, müssen eigene dedizierte VMs aufweisen.  
> * Eine Kombination von Enqueue Replication Server 1 und Enqueue Replication Server 2 im gleichen Cluster wird nicht unterstützt.  


## <a name="supported-os-versions"></a>Unterstützte Betriebssystemversionen

Sowohl Windows Server 2016 als auch Windows Server 2019 werden unterstützt (verwenden Sie die neuesten Datacenter-Images).

Aus folgenden Gründen wird dringend empfohlen, **Windows Server 2019 Datacenter** zu verwenden:
- Der Windows 2019-Failoverclusterdienst ist Azure-fähig.
- Durch die Überwachung auf geplante Azure-Ereignisse werden Integration und Kenntnisse über die Azure-Hostwartung sowie verbesserte Benutzerfreundlichkeit erzielt.
- Es kann der Name des verteilten Netzwerks (Standardoption) verwendet werden. Dadurch ist keine dedizierte IP-Adresse für den Namen des Clusternetzwerks erforderlich. Außerdem muss diese IP-Adresse nicht auf dem internen Azure Load Balancer konfiguriert werden. 

## <a name="architecture"></a>Aufbau

Sowohl Enqueue Replication Server 1 (ERS1) als auch Enqueue Replication Server 2 (ERS2) werden in einer Multi-SID-Konfiguration unterstützt.  Eine Kombination von ERS1 und ERS2 im gleichen Cluster wird nicht unterstützt. 

1. Das erste Beispiel zeigt zwei SAP-SIDs mit ERS1-Architektur. Hierbei gilt Folgendes:

   - SAP-SID1 wird auf einem freigegebenen Datenträger mit ERS1 bereitgestellt. Die ERS-Instanz ist auf dem lokalen Host und dem lokalen Laufwerk installiert.
     SAP-SID1 verfügt über eine eigene (virtuelle) IP-Adresse (SID1 (A)SCS IP1), die auf dem internen Azure Load Balancer konfiguriert wird.

   - SAP-SID2 wird auf einem freigegebenen Datenträger mit ERS1 bereitgestellt. Die ERS-Instanz ist auf dem lokalen Host und dem lokalen Laufwerk installiert.
     SAP-SID2 verfügt über eine eigene (virtuelle) IP-Adresse (SID2 (A)SCS IP2), die ebenfalls auf dem internen Azure Load Balancer konfiguriert wird.

![Hochverfügbarkeit für SAP ASCS/SCS-Instanzen – zwei Instanzen mit ERS1-Konfiguration][sap-ha-guide-figure-6007]

2. Das zweite Beispiel zeigt zwei SAP-SIDs mit ERS2-Architektur. Hierbei gilt Folgendes: 

   - SAP-SID1 mit ERS2 ist ebenfalls gruppiert und wird auf dem lokalen Laufwerk bereitgestellt.  
     SAP-SID1 verfügt über eine eigene (virtuelle) IP-Adresse (SID1 (A)SCS IP1), die auf dem internen Azure Load Balancer konfiguriert wird.
     SAP ERS2, der vom SAP-SID1-System verwendet wird, verfügt über eine eigene (virtuelle) IP-Adresse (SID1 ERS2 IP2), die auf dem internen Azure Load Balancer konfiguriert wird.

   - SAP-SID2 mit ERS2 ist ebenfalls gruppiert und wird auf dem lokalen Laufwerk bereitgestellt.  
     SAP-SID2 verfügt über eine eigene (virtuelle) IP-Adresse (SID2 (A)SCS IP3), die auf dem internen Azure Load Balancer konfiguriert wird.
     SAP ERS2, der vom SAP-SID2-System verwendet wird, verfügt über eine eigene (virtuelle) IP-Adresse (SID2 ERS2 IP4), die auf dem internen Azure Load Balancer konfiguriert wird.

   In diesem Fall gibt es eine Gesamtzahl von vier virtuellen IP-Adressen:  
   - SID1 (A)SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A)SCS IP3
   - SID2 ERS2 IP4

![Hochverfügbarkeit für SAP ASCS/SCS-Instanzen – zwei Instanzen mit ERS1- und ERS2-Konfiguration][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Infrastrukturelle Vorbereitung

Es wird nun zusätzlich zur **vorhandenen gruppierten** SAP ASCS/SCS-Instanz **PR1** eine neue SAP-SID **PR2** installiert.  

### <a name="host-names-and-ip-addresses"></a>Hostnamen und IP-Adressen

| Hostnamenrolle | Hostname | Statische IP-Adresse | Verfügbarkeitsgruppe | Näherungsplatzierungsgruppe |
| --- | --- | --- |---| ---|
| Erster ASCS-/SCS-Cluster des Clusterknotens |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| Zweiter ASCS-/SCS-Cluster des Clusterknotens |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| Name des Clusternetzwerks | pr1clust |10.0.0.42 (**nur** für Win 2016-Cluster) | – | – |
| Name des ASCS-Clusternetzwerks von **SID1** | pr1-ascscl |10.0.0.43 | – | – |
| Name des ERS-Clusternetzwerks von **SID1** (**nur** für ERS2) | pr1-erscl |10.0.0.44 | – | – |
| Name des ASCS-Clusternetzwerks von **SID2** | pr2-ascscl |10.0.0.45 | – | – |
| Name des ERS-Clusternetzwerks von **SID2** (**nur** für ERS2) | pr1-erscl |10.0.0.46 | – | – |

### <a name="create-azure-internal-load-balancer"></a>Erstellen eines internen Azure Load Balancer

SAP ASCS, SAP SCS und der neue SAP ERS2 verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein [Lastenausgleich](../../../load-balancer/load-balancer-overview.md) erforderlich. Es wird dringend empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. 

Sie müssen dem vorhandenen Lastenausgleich eine Konfiguration für die zweite ASCS/SCS/ERS-Instanz der SAP-SID **PR2** hinzufügen. Die Konfiguration für die erste SAP-SID **PR1** sollte bereits vorhanden sein.  

**(A)SCS PR2 [Instanznummer 02]**
- Frontendkonfiguration
    - Statische ASCS/SCS-IP-Adresse **10.0.0.45**
- Backendkonfiguration  
    Bereits vorhanden. Die VMs wurden dem Back-End-Pool während der Konfiguration für SAP-SID **PR1** bereits hinzugefügt.
- Testport
    - Port 620 **nr** [**62002**]. Lassen Sie die Standardoptionen für das Protokoll (TCP), das Intervall (5) und den Fehlerschwellenwert (2) unverändert.
- Lastenausgleichsregeln
    - Wenn Sie Load Balancer Standard verwenden, wählen Sie HA-Ports aus.
    - Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
        - 32 **nr** TCP [**3202**]
        - 36 **nr** TCP [**3602**]
        - 39 **nr** TCP [**3902**]
        - 81 **nr** TCP [**8102**]
        - 5 **nr** 13 TCP [**50213**]
        - 5 **nr** 14 TCP [**50214**]
        - 5 **nr** 16 TCP [**50216**]
        - Ordnen Sie die ASCS-Front-End-IP-Adresse, den Integritätstest und den vorhandenen Back-End-Pool für **PR2** zu.  

    - Stellen Sie sicher, dass „Leerlauftimeout (Minuten)“ auf den Höchstwert 30 festgelegt ist und dass „Floating IP (Direct Server Return)“ aktiviert ist.

**ERS2 PR2 [Instanznummer 12]** 

Da Enqueue Replication Server 2 (ERS2) ebenfalls gruppiert ist, muss die virtuelle IP-Adresse von ERS2 zusätzlich zur oben angegebenen SAP ASCS/SCS-IP-Adresse ebenfalls in Azure ILB konfiguriert werden. Dieser Abschnitt gilt nur, wenn Sie die Enqueue Replication Server 2-Architektur für **PR2** verwenden.  
- Neue Front-End-Konfiguration
    - Statische SAP ERS2-IP-Adresse **10.0.0.46**

- Backendkonfiguration  
  Die VMs wurden dem ILB-Back-End-Pool bereits hinzugefügt.  

- Neuer Testport
    - Port 621 **nr** [**62112**]. Lassen Sie die Standardoptionen für das Protokoll (TCP), das Intervall (5) und den Fehlerschwellenwert (2) unverändert.

- Neue Lastenausgleichsregeln
    - Wenn Sie Load Balancer Standard verwenden, wählen Sie HA-Ports aus.
    - Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
        - 32 **nr** TCP [**3212**]
        - 33 **nr** TCP [**3312**]
        - 5 **nr** 13 TCP [**51212**]
        - 5 **nr** 14 TCP [**51212**]
        - 5 **nr** 16 TCP [**51212**]
        - Ordnen Sie die ERS2-Front-End-IP-Adresse, den Integritätstest und den vorhandenen Back-End-Pool für **PR2** zu.  

    - Stellen Sie sicher, dass „Leerlauftimeout (Minuten)“ auf den Höchstwert (z. B. 30) festgelegt ist und dass „Floating IP (Direct Server Return)“ aktiviert ist.


### <a name="create-and-attach-second-azure-shared-disk"></a>Erstellen und Anfügen eines zweiten freigegebenen Azure-Datenträgers

Führen Sie den folgenden Befehl auf einem der Clusterknoten aus. Sie müssen die Werte für Ihre Ressourcengruppe, die Azure-Region, die SAP-SID usw. anpassen.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Formatieren des freigegebenen Datenträgers mit PowerShell
1. Rufen Sie die Datenträgernummer ab. Führen Sie die PowerShell-Befehle auf einem der Clusterknoten aus:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formatieren Sie den Datenträger. In diesem Beispiel handelt es sich um Datenträger Nr. 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Vergewissern Sie sich, dass der Datenträger jetzt als Clusterdatenträger sichtbar ist.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Registrieren Sie den Datenträger im Cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz

1. Erstellen Sie einen DNS-Eintrag für den virtuellen Hostnamen der neuen SAP ASCS/SCS-Instanz im Windows-DNS-Manager.  
   Die IP-Adresse, die Sie dem virtuellen Hostnamen in DNS zuweisen, muss der IP-Adresse entsprechen, die Sie in Azure Load Balancer zugewiesen haben.  

   ![Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der IP-Adresse][sap-ha-guide-figure-6009]
 
   _Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der IP-Adresse_

2. Wenn Sie SAP Enqueue Replication Server 2 verwenden, bei dem es sich ebenfalls um eine Clusterinstanz handelt, müssen Sie in DNS auch einen virtuellen Hostnamen für ERS2 reservieren. 
   Die IP-Adresse, die Sie dem virtuellen Hostnamen für ERS2 in DNS zuweisen, muss der IP-Adresse entsprechen, die Sie in Azure Load Balancer zugewiesen haben.  

   ![Festlegen des DNS-Eintrags für den virtuellen SAP ERS2-Clusternamen und der IP-Adresse][sap-ha-guide-figure-6010]
 
   _Festlegen des DNS-Eintrags für den virtuellen SAP ERS2-Clusternamen und der IP-Adresse_

3. Wählen Sie zum Definieren der IP-Adresse, die dem virtuellen Hostnamen zugewiesen ist, die Option **DNS-Manager** > **Domäne**.

   ![Neuer virtueller Name und IP-Adresse für SAP ASCS/SCS- und ERS2-Clusterkonfiguration][sap-ha-guide-figure-6011]

   _Neuer virtueller Name und TCP/IP-Adresse für SAP ASCS/SCS- und ERS2-Clusterkonfiguration_

## <a name="sap-installation"></a>SAP-Installation 

### <a name="install-the-sap-first-cluster-node"></a>Installieren des ersten SAP-Clusterknotens

Führen Sie das beschriebene SAP-Installationsverfahren aus. Stellen Sie sicher, dass Sie bei der Option zum Starten der Installation für „Erster Clusterknoten“ die Konfigurationsoption „Freigegebener Clusterdatenträger“ auswählen.  
Wählen Sie den neu erstellten freigegebenen Datenträger aus.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Ändern des SAP-Profils der ASCS/SCS-Instanz

Wenn Sie Enqueue Replication Server 1 ausführen, fügen Sie den SAP-Profilparameter `enque/encni/set_so_keepalive` wie nachfolgend beschrieben hinzu. Dieser Profilparameter verhindert das Schließen von Verbindungen zwischen SAP-Workprozessen und dem Enqueue-Server, wenn diese sich zu lange im Leerlauf befinden. Der SAP-Parameter ist für ERS2 nicht erforderlich. 

1. Fügen Sie diesen Profilparameter dem Profil der SAP ASCS/SCS-Instanz hinzu, wenn ERS1 verwendet wird.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Stellen Sie sowohl für ERS1 als auch für ERS2 sicher, dass die `keepalive`-Parameter des Betriebssystems wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.   

2. Starten Sie die SAP ASCS/SCS-Instanz neu, um die Änderungen der SAP-Profilparameter zu übernehmen.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Konfigurieren des Testports für die Clusterressource

Nutzen Sie die Testfunktionalität des internen Lastenausgleichs, damit die gesamte Clusterkonfiguration mit Azure Load Balancer funktioniert. Der interne Azure Load Balancer verteilt in der Regel die eingehende Workload gleichmäßig auf die beteiligten virtuellen Computer.

Allerdings funktioniert dies bei einigen Clusterkonfigurationen nicht, da nur eine Instanz aktiv ist. Die andere Instanz ist passiv und kann daher keine Workload annehmen. Eine Testfunktion hilft, wenn der interne Azure Load Balancer erkennt, welche Instanz aktiv ist, und nur die aktive Instanz zum Ziel hat.  

> [!IMPORTANT]
> In dieser Beispielkonfiguration wird **ProbePort** auf „620 **Nr**“ festgelegt. Für die SAP ASCS-Instanz mit der Nummer **02** lautet der Port „620 **02**“.
> Sie müssen die Konfiguration entsprechend Ihren SAP-Instanznummern und der SAP-SID anpassen.

Zum Hinzufügen eines Testports führen Sie das folgende PowerShell-Modul auf einer der Cluster-VMs aus:

- Im Fall der SAP ASC/SCS-Instanz mit der Instanznummer **02**. 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Bei Verwendung von ERS2 mit der Instanznummer **12**, der gruppiert ist. Der Testport für ERS1 muss nicht konfiguriert werden, da er nicht gruppiert ist.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 Der Code für die Funktion `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` sieht wie folgt aus:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Fortfahren mit der SAP-Installation

1. Installieren Sie die Datenbankinstanz, indem Sie das im SAP-Installationshandbuch beschriebene Verfahren ausführen.  
2. Installieren Sie SAP auf dem zweiten Clusterknoten, indem Sie die im SAP-Installationshandbuch beschriebenen Schritte ausführen.  
3. Installieren Sie die SAP PAS-Instanz (primärer Anwendungsserver) auf dem virtuellen Computer, den Sie als Host für den PAS vorgesehen haben.   
   Führen Sie das im SAP-Installationshandbuch beschriebene Verfahren aus. Es gibt keine Abhängigkeiten in Azure.
4. Installieren Sie zusätzliche SAP-Anwendungsserver auf den virtuellen Computern, die als Hosts von SAP-Anwendungsserverinstanzen festgelegt wurden.  
   Führen Sie das im SAP-Installationshandbuch beschriebene Verfahren aus. Es gibt keine Abhängigkeiten in Azure. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testen des SAP ASCS-/SCS-Instanzfailovers
Bei den beschriebenen Failovertests wird davon ausgegangen, dass SAP ASCS auf Knoten A aktiv ist.  

1. Vergewissern Sie sich, dass für das SAP-System ein erfolgreiches Failover von Knoten A auf Knoten B durchgeführt werden kann. In diesem Beispiel wird der Test für SAP-SID **PR2** ausgeführt.  
   Stellen Sie sicher, dass jede SAP-SID erfolgreich auf den anderen Clusterknoten verschoben werden kann.   
   Verwenden Sie eine der folgenden Optionen, um ein Failover der Clustergruppe „SAP \<SID\>“ vom Clusterknoten A auf den Clusterknoten B zu initiieren:
    - Failovercluster-Manager  
    - Failovercluster-PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Starten Sie Clusterknoten A unter dem Windows-Gastbetriebssystem neu. Dadurch wird ein automatisches Failover der Clustergruppe „SAP \<SID\>“ vom Knoten A auf den Knoten B initiiert.  
3. Starten Sie Clusterknoten A im Azure-Portal neu. Dadurch wird ein automatisches Failover der Clustergruppe „SAP \<SID\>“ vom Knoten A auf den Knoten B initiiert.  
4. Starten Sie Clusterknoten A mit Azure PowerShell neu. Dadurch wird ein automatisches Failover der Clustergruppe „SAP \<SID\>“ vom Knoten A auf den Knoten B initiiert.

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-infrastructure-wsfc-shared-disk]
* [SAP NetWeaver HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-installation-wsfc-shared-disk]


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

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

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