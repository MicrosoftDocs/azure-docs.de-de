---
title: Erstellen einer FCI mit freigegebenen Azure-Datenträgern
description: Verwenden Sie freigegebene Azure-Datenträger, um eine Failoverclusterinstanz (FCI) mit SQL Server in Azure Virtual Machines zu erstellen.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: df50583e650d3d44e702c0f7d1596f2a733a4445
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556385"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Erstellen einer FCI mit freigegebenen Azure-Datenträgern (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird erläutert, wie Sie auf virtuellen Azure-Computern (Azure Virtual Machines) eine SQL Server-Failoverclusterinstanz (FCI) mit SQL Server mithilfe freigegebener Azure-Datenträger erstellen. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 


## <a name="prerequisites"></a>Voraussetzungen 

Bevor Sie die in diesem Artikel aufgeführten Anweisungen ausführen, sollten Sie über Folgendes verfügen:

- Ein Azure-Abonnement. [Kostenlos](https://azure.microsoft.com/free/) einsteigen. 
- [Zwei oder mehr virtuelle Windows Azure-Computer](failover-cluster-instance-prepare-vm.md). Es werden sowohl [Verfügbarkeitsgruppen](../../../virtual-machines/windows/tutorial-availability-sets.md) als auch [Näherungsplatzierungsgruppen](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) (proximity placement groups, PPGs) unterstützt. Wenn Sie eine PPG verwenden, müssen alle Knoten in derselben Gruppe vorhanden sein.
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- Die neueste Version von [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-azure-shared-disk"></a>Hinzufügen eines freigegebenen Azure-Datenträgers
Stellen Sie einen verwalteten SSD Premium-Datenträger mit aktivierter Funktion für freigegebene Datenträger bereit. Legen Sie `maxShares` auf den Wert fest, der **mit der Anzahl der Clusterknoten übereinstimmt**, damit der Datenträger für alle FCI-Knoten freigegeben werden kann. 

Fügen Sie einen freigegebenen Azure-Datenträger hinzu, indem Sie folgendermaßen vorgehen: 


1. Speichern Sie das folgende Skript als *SharedDiskConfig.json*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. Führen Sie *SharedDiskConfig.json* mithilfe von PowerShell aus: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Initialisieren Sie für jeden virtuellen Computer die angefügten freigegebenen Datenträger als GUID-Partitionstabelle (GPT), und formatieren Sie diese als NTFS (New Technology File System), indem Sie den folgenden Befehl ausführen: 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>Erstellen des Failoverclusters

Für das Erstellen des Failoverclusters benötigen Sie Folgendes:

- Die Namen der virtuellen Computer, die zu Clusterknoten werden.
- Einen Namen für den Failovercluster.
- Eine IP-Adresse für den Failovercluster. Sie können eine IP-Adresse verwenden, die nicht in demselben virtuellen Azure-Netzwerk und Subnetz wie die Clusterknoten genutzt wird.


# <a name="windows-server-2012-2016"></a>[Windows Server 2012 bis 2016](#tab/windows2012)

Mit dem folgenden PowerShell-Skript wird ein Failovercluster erstellt. Aktualisieren Sie das Skript mit den Namen der Knoten (Namen virtueller Computer) und einer verfügbaren IP-Adresse aus dem virtuellen Azure-Netzwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Mit dem folgenden PowerShell-Skript wird ein Failovercluster erstellt. Aktualisieren Sie das Skript mit den Namen der Knoten (Namen virtueller Computer) und einer verfügbaren IP-Adresse aus dem virtuellen Azure-Netzwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Weitere Informationen finden Sie unter [Failovercluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurieren des Quorums

Konfigurieren Sie die Quorumlösung, die Ihren Geschäftsanforderungen am besten entspricht. Sie können einen [Datenträgerzeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), einen [Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness) oder einen [Dateifreigabezeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) konfigurieren. Weitere Informationen finden Sie unter [Quorum mit SQL Server-VMs](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Validieren des Clusters
Führen Sie die Validierung für den Cluster auf der Benutzeroberfläche oder mit PowerShell durch.

Um den Cluster über die Benutzeroberfläche zu validieren, gehen Sie auf einem der beiden virtuellen Computer folgendermaßen vor:

1. Wählen Sie unter **Server-Manager** die Option **Tools** aus, und wählen Sie dann **Failovercluster-Manager** aus.
1. Wählen Sie unter **Failovercluster-Manager** die Option **Aktion** aus, und wählen Sie dann **Konfiguration überprüfen** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie unter **Server oder Cluster auswählen** die Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Testauswahl** alle Tests aus, *ausgenommen* den Test **Speicher**.

## <a name="test-cluster-failover"></a>Testen des Failovers des Clusters

Testen Sie das Failover Ihres Clusters. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, und wählen Sie **Weitere Aktionen** > **Hauptclusterressource verschieben** > **Knoten auswählen** und dann den anderen Knoten des Clusters aus. Verschieben Sie die Hauptclusterressource auf alle Knoten des Clusters und dann zurück auf den primären Knoten. Wenn Sie den Cluster erfolgreich auf jeden Knoten verschieben können, können Sie SQL Server installieren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testen des Clusterfailovers durch Verschieben der Hauptressource auf die anderen Knoten":::

## <a name="create-sql-server-fci"></a>Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster und alle Clusterkomponenten einschließlich Speicher konfiguriert haben, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie mithilfe von RDP (Remotedesktopprotokoll) eine Verbindung mit dem ersten virtuellen Computer her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie alle Ressourcen auf diesen virtuellen Computer, falls dies erforderlich ist.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. 

1. Wählen Sie **Setup** aus.

1. Wählen Sie im **SQL Server-Installationscenter** die Option **Installation** aus.

1. Wählen Sie **Neue SQL Server-Failoverclusterinstallation** aus. Befolgen Sie im Assistenten die Anleitung zum Installieren der SQL Server-FCI.

Die FCI-Datenverzeichnisse müssen sich auf freigegebenen Azure-Datenträgern befinden. 

1. Nach Durchführung der Anweisungen im Assistenten wird vom Setup eine SQL Server-FCI auf dem ersten Knoten installiert.

1. Nachdem das Setup die FCI auf dem ersten Knoten installiert hat, können Sie per RDP eine Verbindung mit dem zweiten Knoten herstellen.

1. Öffnen Sie das **SQL Server-Installationscenter**, und wählen Sie **Installation** aus.

1. Wählen Sie **Knoten einem SQL Server-Failovercluster hinzufügen** aus. Befolgen Sie die Anweisungen im Assistenten, um SQL Server zu installieren und die Serverinstanz der FCI hinzuzufügen.

   >[!NOTE]
   >Wenn Sie ein Azure Marketplace-Katalogimage, das SQL Server enthält, verwendet haben, sind die SQL Server-Tools im Image enthalten. Wenn Sie keines dieser Images verwendet haben, müssen Sie die SQL Server-Tools separat installieren. Weitere Informationen finden Sie unter [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >

## <a name="register-with-the-sql-vm-rp"></a>Registrieren beim SQL-VM-RP

Wenn Sie Ihre SQL Server-VM im Portal verwalten möchten, registrieren Sie die VM mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode). Dies ist derzeit der einzige Modus, der mit FCI und SQL Server auf Azure-VMs unterstützt wird. 


Registrieren einer SQL Server-VM im Modus „Lightweight“ mit PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurieren von Konnektivität 

Um Datenverkehr ordnungsgemäß an den aktuellen primären Knoten zu leiten, konfigurieren Sie die für Ihre Umgebung geeignete Konnektivitätsoption. Sie können einen [Azure-Lastenausgleich](failover-cluster-instance-vnn-azure-load-balancer-configure.md) erstellen oder bei Verwendung von SQL Server 2019 CU2 (oder höher) und Windows Server 2016 (oder höher) stattdessen das Feature für [verteilte Netzwerknamen](failover-cluster-instance-distributed-network-name-dnn-configure.md) verwenden. 

## <a name="limitations"></a>Einschränkungen

- Nur die Registrierung mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) wird unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wenn dies noch nicht geschehen ist, konfigurieren Sie die Konnektivität mit Ihrer FCI mit einem [virtuellen Netzwerknamen und einem Azure-Lastenausgleich](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oder einem [verteilten Netzwerknamen (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Wenn freigegebene Azure-Datenträger nicht die richtige FCI-Speicherlösung für Sie sind, können Sie Ihre FCI stattdessen mithilfe von [Premium-Dateifreigaben](failover-cluster-instance-premium-file-share-manually-configure.md) oder [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) erstellen. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und unter [Bewährte Methoden für die Clusterkonfiguration](hadr-cluster-best-practices.md).

Weitere Informationen finden Sie unter 
- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)