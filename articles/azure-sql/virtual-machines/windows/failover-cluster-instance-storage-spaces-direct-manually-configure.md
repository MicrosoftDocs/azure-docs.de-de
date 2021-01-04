---
title: Erstellen der FCI mit „Direkte Speicherplätze“
description: Verwenden Sie „Direkte Speicherplätze“, um eine Failoverclusterinstanz (FCI) mit SQL Server auf Azure-VMs zu erstellen.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 6ed5e11a8492314e99b9f105d259fa910dcdb77d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357805"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Erstellen einer FCI mit „Direkte Speicherplätze“ (SQL-Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird erläutert, wie Sie mit SQL-Server auf virtuellen Azure-Computern (VMs) eine Failoverclusterinstanz (FCI) mit [Direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) erstellen. „Direkte Speicherplätze“ fungiert hierbei als softwarebasiertes virtuelles Storage Area Network (VSAN), in dem der Speicher (Datenträger) zwischen den Knoten (Azure-VMs) in einem Windows-Cluster synchronisiert wird. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Bewährte Methoden für Cluster](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Übersicht 

[Direkte Speicherplätze (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) unterstützt zwei Arten von Architekturen: „konvergent“ und „hyperkonvergent“. In einer hyperkonvergenten Infrastruktur wird der Speicher auf denselben Servern angeordnet, auf denen die gruppierte Anwendung gehostet wird, sodass sich auf jedem SQL-Server-FCI-Knoten Speicher befindet. 

Im folgenden Diagramm wird die vollständige Lösung veranschaulicht, wobei hyperkonvergentes „Direkte Speicherplätze“ mit SQL Server auf Azure-VMs verwendet wird: 

![Diagramm der vollständigen Lösung mit hyperkonvergentem „Direkte Speicherplätze“](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Das obige Diagramm zeigt die folgenden Ressourcen in derselben Ressourcengruppe:

- Zwei virtuelle Computer in einem Windows Server-Failovercluster. Wenn sich ein virtueller Computer in einem Failovercluster befindet, wird dieser auch als *Clusterknoten* oder *Knoten* bezeichnet.
- Jeder virtuelle Computer verfügt mindestens über zwei Datenträger für Daten.
- Direkte Speicherplätze synchronisiert die Daten auf dem Datenträger und stellt den synchronisierten Speicher als Speicherpool dar.
- Der Speicherpool stellt ein freigegebenes Clustervolume (CSV) für den Failovercluster dar.
- Die SQL Server-FCI-Clusterrolle verwendet das CSV für die Datenträger für Daten.
- Ein Azure Load Balancer für die IP-Adresse der SQL Server-Failoverclusterinstanz.
- Eine Azure-Verfügbarkeitsgruppe enthält alle Ressourcen.

   > [!NOTE]
   > Sie können diese ganze Lösung in Azure über eine Vorlage erstellen. Ein Beispiel für eine Vorlage ist auf der GitHub-Seite [Azure-Schnellstartvorlagen](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) verfügbar. Dieses Beispiel wurde nicht für eine bestimmte Workload entworfen und auch nicht entsprechend getestet. Sie können die Vorlage ausführen, um eine SQL Server-FCI mit einer Verbindung für „Direkte Speicherplätze“-Speicher mit Ihrer Domäne zu erstellen. Sie können die Vorlage auswerten und für Ihre Zwecke anpassen.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die in diesem Artikel aufgeführten Anweisungen ausführen, sollten Sie über Folgendes verfügen:

- Ein Azure-Abonnement. [Kostenlos](https://azure.microsoft.com/free/) einsteigen. 
- [Mindestens zwei vorbereitete Windows Azure-VMs](failover-cluster-instance-prepare-vm.md) in einer [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- Die neueste Version von [PowerShell](/powershell/azure/install-az-ps) 


## <a name="add-the-windows-cluster-feature"></a>Feature zum Hinzufügen von Windows-Clustern

1. Stellen Sie per RDP (Remote Desktop Protocol) eine Verbindung mit dem ersten virtuellen Computer her, indem Sie ein Domänenkonto verwenden, das Mitglied der Gruppe der lokalen Administratoren ist und über Berechtigungen zum Erstellen von Objekten in Active Directory verfügt. Verwenden Sie dieses Konto für den Rest der Konfiguration.

1. Fügen Sie jedem virtuellen Computer Failoverclustering hinzu.

   Führen Sie die folgenden Schritte auf beiden virtuellen Computern aus, um das Failoverclustering über die Benutzeroberfläche zu installieren.

   1. Wählen Sie unter **Server-Manager** die Option **Verwalten** und dann **Rollen und Features hinzufügen** aus.
   1. Wählen Sie im **Assistenten zum Hinzufügen von Rollen und Features** die Option **Weiter** aus, bis Sie zu **Features auswählen** gelangen.
   1. Wählen Sie in **Features auswählen** die Option **Failoverclustering** aus. Schließen Sie alle erforderlichen Features und Verwaltungstools ein. 
   1. Wählen Sie **Features hinzufügen** aus.
   1. Wählen Sie **Weiter** und dann **Fertig stellen** aus, um die Features zu installieren.

   Führen Sie zum Installieren von Failoverclustering mithilfe von PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Weitere Informationen zu den nächsten Schritten finden Sie in den Anweisungen im Abschnitt „Schritt 3: Konfigurieren von Direkte Speicherplätze“ von [Hyperkonvergente Lösung mit Direkte Speicherplätze in Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Überprüfen des Clusters

Führen Sie die Validierung für den Cluster auf der Benutzeroberfläche oder mit PowerShell durch.

Um den Cluster über die Benutzeroberfläche zu validieren, gehen Sie auf einem der beiden virtuellen Computer folgendermaßen vor:

1. Wählen Sie unter **Server-Manager** die Option **Tools** aus, und wählen Sie dann **Failovercluster-Manager** aus.
1. Wählen Sie unter **Failovercluster-Manager** die Option **Aktion** aus, und wählen Sie dann **Konfiguration überprüfen** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie unter **Server oder Cluster auswählen** die Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Testauswahl** alle Tests aus, ausgenommen **Speicher** (siehe folgende Abbildung):

   ![Auswählen von Tests zur Überprüfung des Clusters](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Bestätigung** die Option **Weiter** aus.

    Der **Konfigurationsüberprüfungs-Assistent** führt die Validierungstests aus.

Führen Sie zum Validieren des Clusters mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Erstellen Sie nach dem Validieren des Clusters den Failovercluster.


## <a name="create-failover-cluster"></a>Erstellen des Failoverclusters

Für das Erstellen des Failoverclusters benötigen Sie Folgendes:

- Die Namen der virtuellen Computer, die zu Clusterknoten werden.
- Einen Namen für den Failovercluster.
- Eine IP-Adresse für den Failovercluster. Sie können eine IP-Adresse verwenden, die nicht in demselben virtuellen Azure-Netzwerk und Subnetz wie die Clusterknoten genutzt wird.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 bis 2016](#tab/windows2012)

Mit dem folgenden PowerShell-Befehl wird ein Failovercluster für Windows Server 2012 bis Windows Server 2016 erstellt. Aktualisieren Sie das Skript mit den Namen der Knoten (Namen virtueller Computer) und einer verfügbaren IP-Adresse aus dem virtuellen Azure-Netzwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Mit dem folgenden PowerShell-Befehl wird ein Failovercluster für Windows Server 2019 erstellt.  Aktualisieren Sie das Skript mit den Namen der Knoten (Namen virtueller Computer) und einer verfügbaren IP-Adresse aus dem virtuellen Azure-Netzwerk.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Weitere Informationen finden Sie unter [Failovercluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurieren des Quorums

Konfigurieren Sie die Quorumlösung, die Ihren Geschäftsanforderungen am besten entspricht. Sie können einen [Datenträgerzeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), einen [Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness) oder einen [Dateifreigabezeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) konfigurieren. Weitere Informationen finden Sie unter [Quorum mit SQL Server-VMs](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Hinzufügen von Speicher

Die Datenträger für „Direkte Speicherplätze“ müssen leer sein. Sie dürfen keine Partitionen oder andere Daten enthalten. Befolgen Sie die Anweisungen unter [Bereitstellen von „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives), um die Datenträger zu bereinigen.

1. [Aktivieren von „Direkte Speicherplätze“](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Mit dem folgenden PowerShell-Skript wird „Direkte Speicherplätze“ aktiviert:  

   ```powershell
   Enable-ClusterS2D
   ```

   Im **Failovercluster-Manager** wird jetzt der Speicherpool angezeigt.

1. [Erstellen eines Volumes](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes)

   „Direkte Speicherplätze“ erstellt automatisch einen Speicherpool, wenn Sie „Direkte Speicherplätze“ aktivieren. Sie können jetzt ein Volume erstellen. Das PowerShell-Cmdlet `New-Volume` automatisiert den Volumeerstellungsprozess. Dieser Prozess umfasst Formatieren, Hinzufügen des Volumes zum Cluster und das Erstellen eines CSV. In diesem Beispiel wird ein CSV mit 800 Gigabyte (GB) erstellt:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Wenn Sie den obigen Befehl ausgeführt haben, wird ein Volume mit 800 GB als Clusterressource bereitgestellt. Das Volume befindet sich unter `C:\ClusterStorage\Volume1\`.

   Dieser Screenshot zeigt ein CSV mit „Direkte Speicherplätze“:

   ![Screenshot eines freigegebenen Clustervolumes mit „Direkte Speicherplätze“](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Testen des Failovers des Clusters

Testen Sie das Failover Ihres Clusters. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, und wählen Sie **Weitere Aktionen** > **Hauptclusterressource verschieben** > **Knoten auswählen** und dann den anderen Knoten des Clusters aus. Verschieben Sie die Hauptclusterressource auf alle Knoten des Clusters und dann zurück auf den primären Knoten. Wenn Sie den Cluster erfolgreich auf jeden Knoten verschieben können, können Sie SQL Server installieren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testen des Clusterfailovers durch Verschieben der Hauptressource auf die anderen Knoten":::

## <a name="create-sql-server-fci"></a>Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster und alle Clusterkomponenten einschließlich Speicher konfiguriert haben, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie die Verbindung mit dem ersten virtuellen Computer per RDP her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie alle Ressourcen auf diesen virtuellen Computer, falls dies erforderlich ist.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. Wählen Sie **Setup** aus.

1. Wählen Sie im **SQL Server-Installationscenter** die Option **Installation** aus.

1. Wählen Sie **Neue SQL Server-Failoverclusterinstallation** aus. Befolgen Sie im Assistenten die Anleitung zum Installieren der SQL Server-FCI.

   Die FCI-Datenverzeichnisse müssen sich in gruppiertem Speicher befinden. Bei „Direkte Speicherplätze“ handelt es sich nicht um einen freigegebenen Datenträger, sondern um einen Bereitstellungspunkt für ein Volume auf jedem Server. „Direkte Speicherplätze“ synchronisiert das Volume zwischen beiden Knoten. Das Volume wird dem Cluster als CSV angezeigt. Verwenden Sie den CSV-Bereitstellungspunkt für die Datenverzeichnisse.

   ![Datenverzeichnisse](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Nach Abschluss der Schritte des Assistenten wird vom Setup eine SQL Server-FCI auf dem ersten Knoten installiert.

1. Nachdem das Setup die FCI auf dem ersten Knoten installiert hat, können Sie per RDP eine Verbindung mit dem zweiten Knoten herstellen.

1. Öffnen Sie das **SQL Server-Installationscenter**. Wählen Sie **Installation** aus.

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

- Virtuelle Azure-Computer unterstützen Microsoft Distributed Transaction Coordinator (MSDTC) auf Windows Server 2019 mit Speicher auf CSVs und einen [Standardlastenausgleich](../../../load-balancer/load-balancer-overview.md).
- Datenträger, die als NTFS-formatierte Datenträger angefügt wurden, können nur dann mit „Direkte Speicherplätze“ verwendet werden, wenn die Option für die Datenträgerberechtigung beim Hinzufügen von Speicher zum Cluster deaktiviert ist oder wird. 
- Nur die Registrierung mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) wird unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wenn dies noch nicht geschehen ist, konfigurieren Sie die Konnektivität mit Ihrer FCI mit einem [virtuellen Netzwerknamen und einem Azure-Lastenausgleich](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oder einem [verteilten Netzwerknamen (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Wenn „Direkte Speicherplätze“ nicht die richtige FCI-Speicherlösung für Sie ist, können Sie Ihre FCI stattdessen mithilfe von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) oder [Premium-Dateifreigaben](failover-cluster-instance-premium-file-share-manually-configure.md) erstellen. 

Weitere Informationen finden Sie in der Übersicht zu [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und unter [Bewährte Methoden für die Clusterkonfiguration](hadr-cluster-best-practices.md). 

Weitere Informationen finden Sie unter 
- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)