---
title: Erstellen einer FCI mit einer Premium-Dateifreigabe
description: Erstellen Sie auf virtuellen Azure-Computern eine Failoverclusterinstanz (FCI) mit SQL Server mithilfe einer Premium-Dateifreigabe.
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
ms.openlocfilehash: 2fb9677f0874de1fb715082d58a0e354880e654b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358074"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Erstellen einer FCI mit einer Premium-Dateifreigabe (SQL Server auf Azure-VMs)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird erläutert, wie Sie auf virtuellen Azure-Computern eine SQL Server-Failoverclusterinstanz (FCI) mit SQL Server mithilfe einer [Premium-Dateifreigabe](../../../storage/files/storage-how-to-create-premium-fileshare.md) erstellen.

Premium-Dateifreigaben sind SSD-gestützte (Storage Spaces Direct) Dateifreigaben mit durchgängig niedriger Latenz, die für die Verwendung mit Failoverclusterinstanzen für SQL Server 2012 oder höher unter Windows Server 2012 oder höher vollständig unterstützt werden. Premium-Dateifreigaben bieten Ihnen mehr Flexibilität, sodass Sie ohne Ausfallzeiten die Größe der Dateifreigabe ändern und diese skalieren können.

Weitere Informationen finden Sie in der Übersicht über [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Best Practices bei Clustern](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die in diesem Artikel aufgeführten Anweisungen ausführen, sollten Sie über Folgendes verfügen:

- Ein Azure-Abonnement.
- Ein Konto mit Berechtigungen zum Erstellen von Objekten auf virtuellen Azure-Computern und in Active Directory
- [Mindestens zwei vorbereitete virtuelle Azure-Computer mit Windows](failover-cluster-instance-prepare-vm.md) in einer [Verfügbarkeitsgruppe](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) oder unterschiedlichen [Verfügbarkeitszonen](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- Eine [Premium-Dateifreigabe ](../../../storage/files/storage-how-to-create-premium-fileshare.md) basierend auf dem Speicherkontingent Ihrer Datenbank für Ihre Datendateien, die als Clusterlaufwerk verwendet werden kann.
- Die neueste Version von [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Einbinden der Premium-Dateifreigabe

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wechseln Sie dann zu Ihrem Speicherkonto.
1. Navigieren Sie unter **Dateidienst** zu **Dateifreigaben**, und wählen Sie die Premium-Dateifreigabe aus, die Sie als SQL-Speicher verwenden möchten.
1. Klicken Sie auf **Verbinden**, um die Verbindungszeichenfolge für Ihre Dateifreigabe anzuzeigen.
1. Wählen Sie in der Dropdownliste den gewünschten Laufwerkbuchstaben aus, und kopieren Sie dann beide Codeblöcke in Editor.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopieren beider PowerShell-Befehle aus dem Portal zum Herstellen der Verbindung mit der Dateifreigabe":::

1. Stellen Sie eine RDP-Verbindung (Remotedesktopprotokoll) mit der SQL Server-VM her. Nutzen Sie dazu das Konto, das Ihre SQL Server-FCI als Dienstkonto verwenden soll.
1. Öffnen Sie eine administrative PowerShell-Befehlskonsole.
1. Führen Sie die Befehle aus, die Sie zuvor beim Arbeiten im Portal gespeichert haben.
1. Navigieren Sie über den Datei-Explorer oder das Dialogfeld **Ausführen** (WINDOWS+R) zur Freigabe. Verwenden Sie den Netzwerkpfad `\\storageaccountname.file.core.windows.net\filesharename`. Zum Beispiel, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Erstellen Sie in der neu verbundenen Dateifreigabe mindestens einen Ordner, in dem Sie Ihre SQL-Datendateien ablegen können.
1. Wiederholen Sie diese Schritte auf jeder SQL Server-VM, die zum Cluster gehört.

  > [!IMPORTANT]
  > - Erwägen Sie den Einsatz einer gesonderten Dateifreigabe für Sicherungsdateien, um die IOPS- (Input/Output Operations Per Second, Ein-/Ausgabevorgänge pro Sekunde) und Größenkapazität dieser Freigabe für Daten- und Protokolldateien zu reservieren. Sie können für Sicherungsdateien eine Dateifreigaben des Typs Standard oder Premium verwenden.
  > - Wenn Sie unter Windows 2012 R2 oder früher arbeiten, führen Sie die gleichen Schritte aus, um die Dateifreigabe einzubinden, die Sie als Dateifreigabezeugen verwenden. 
  > 


## <a name="add-windows-cluster-feature"></a>Feature zum Hinzufügen von Windows-Clustern

1. Stellen Sie per RDP eine Verbindung mit dem ersten virtuellen Computer her, indem Sie ein Domänenkonto verwenden, das Mitglied der Gruppe der lokalen Administratoren ist und über Berechtigungen zum Erstellen von Objekten in Active Directory verfügt. Verwenden Sie dieses Konto für den Rest der Konfiguration.

1. [Fügen Sie jedem virtuellen Computer Failoverclustering hinzu](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Führen Sie die folgenden Schritte auf beiden virtuellen Computern aus, um das Failoverclustering über die Benutzeroberfläche zu installieren:
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

## <a name="validate-cluster"></a>Validieren des Clusters

Führen Sie die Validierung für den Cluster auf der Benutzeroberfläche oder mit PowerShell durch.

Um den Cluster über die Benutzeroberfläche zu validieren, gehen Sie auf einem der beiden virtuellen Computer folgendermaßen vor:

1. Wählen Sie unter **Server-Manager** die Option **Tools** aus, und wählen Sie dann **Failovercluster-Manager** aus.
1. Wählen Sie unter **Failovercluster-Manager** die Option **Aktion** aus, und wählen Sie dann **Konfiguration überprüfen** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie unter **Server oder Cluster auswählen** die Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen** aus. 
1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Testauswahl** alle Tests aus, ausgenommen **Speicher** und **Direkte Speicherplätze**, wie hier gezeigt:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Auswählen von Tests zur Überprüfung des Clusters":::

1. Wählen Sie **Weiter** aus.
1. Wählen Sie unter **Bestätigung** die Option **Weiter** aus.

Der **Konfigurationsüberprüfungs-Assistent** führt die Validierungstests aus.

Führen Sie zum Validieren des Clusters mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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

Konfigurieren Sie die Quorumlösung, die Ihren Geschäftsanforderungen am besten entspricht. Sie können einen [Datenträgerzeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), einen [Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness) oder einen [Dateifreigabezeugen](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) konfigurieren. Weitere Informationen finden Sie unter [Quorum mit SQL Server-VMs](hadr-cluster-best-practices.md#quorum). 


## <a name="test-cluster-failover"></a>Testen des Failovers des Clusters

Testen Sie das Failover Ihres Clusters. Klicken Sie im **Failovercluster-Manager** mit der rechten Maustaste auf den Cluster, und wählen Sie **Weitere Aktionen** > **Hauptclusterressource verschieben** > **Knoten auswählen** und dann den anderen Knoten des Clusters aus. Verschieben Sie die Hauptclusterressource auf alle Knoten des Clusters und dann zurück auf den primären Knoten. Wenn Sie den Cluster erfolgreich auf jeden Knoten verschieben können, können Sie SQL Server installieren.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testen des Clusterfailovers durch Verschieben der Hauptressource auf die anderen Knoten":::


## <a name="create-sql-server-fci"></a>Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster konfiguriert haben, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie die Verbindung mit dem ersten virtuellen Computer per RDP her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie alle Ressourcen auf diesen virtuellen Computer, falls dies erforderlich ist.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. 

1. Wählen Sie **Setup** aus.

1. Wählen Sie im **SQL Server-Installationscenter** die Option **Installation** aus.

1. Wählen Sie **Neue SQL Server-Failoverclusterinstallation** aus, und befolgen Sie dann die Anweisungen im Assistenten, um die SQL Server-FCI zu installieren.

   Die FCI-Datenverzeichnisse müssen sich auf der Premium-Dateifreigabe befinden. Geben Sie den vollständigen Pfad der Freigabe im folgenden Format ein: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Eine Warnung wird angezeigt, die Sie darüber informiert, dass Sie einen Dateiserver als Datenverzeichnis angegeben haben. Diese Warnung ist erwartungsgemäß. Vergewissern Sie sich zur Vermeidung möglicher Fehler, dass das Benutzerkonto, mit dem Sie beim persistenten Speichern der Dateifreigabe über RDP auf die VM zugegriffen haben, dasselbe Konto ist, das der SQL Server-Dienst verwendet.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Verwenden der Dateifreigabe als SQL-Datenverzeichnisse":::

1. Nach Abschluss der Schritte des Assistenten wird vom Setup eine SQL Server-FCI auf dem ersten Knoten installiert.

1. Nachdem das Setup die FCI auf dem ersten Knoten installiert hat, können Sie per RDP eine Verbindung mit dem zweiten Knoten herstellen.

1. Öffnen Sie das **SQL Server-Installationscenter**, und wählen Sie **Installation** aus.

1. Wählen Sie **Knoten einem SQL Server-Failovercluster hinzufügen** aus. Befolgen Sie die Anweisungen im Assistenten, um SQL Server zu installieren und die Serverinstanz der FCI hinzuzufügen.

   >[!NOTE]
   >Wenn Sie ein Azure Marketplace-Katalogimage mit SQL Server verwendet haben, sind die SQL Server-Tools im Image enthalten. Wenn Sie keines dieser Images verwendet haben, müssen Sie die SQL Server-Tools separat installieren. Weitere Informationen finden Sie unter [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Wiederholen Sie diese Schritte auf allen anderen Knoten, die Sie der SQL Server-Failoverclusterinstanz hinzufügen möchten. 

## <a name="register-with-the-sql-vm-rp"></a>Registrieren beim SQL-VM-RP

Wenn Sie Ihre SQL Server-VM im Portal verwalten möchten, registrieren Sie die VM mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode). Dies ist derzeit der einzige Modus, der mit FCI und SQL Server auf Azure-VMs unterstützt wird. 

Registrieren Sie eine SQL Server-VM im Modus „Lightweight“ mit PowerShell (-LicenseType `PAYG` oder `AHUB`):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurieren von Konnektivität 

Um Datenverkehr ordnungsgemäß an den aktuellen primären Knoten zu leiten, konfigurieren Sie die für Ihre Umgebung geeignete Konnektivitätsoption. Sie können einen [Azure-Lastenausgleich](failover-cluster-instance-vnn-azure-load-balancer-configure.md) erstellen oder bei Verwendung von SQL Server 2019 CU2 (oder höher) und Windows Server 2016 (oder höher) stattdessen das Feature für [verteilte Netzwerknamen](failover-cluster-instance-distributed-network-name-dnn-configure.md) verwenden. 

## <a name="limitations"></a>Einschränkungen

- MS DTC-Transaktionen (Microsoft Distributed Transaction Coordinator) werden unter Windows Server 2016 und früheren Versionen nicht unterstützt. 
- Filestream wird für einen Failovercluster mit einer Premium-Dateifreigabe nicht unterstützt. Um Filestream zu verwenden, stellen Sie den Cluster stattdessen mithilfe von [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) oder von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) bereit.
- Nur die Registrierung mit der SQL-IaaS-Agent-Erweiterung im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) wird unterstützt. 

## <a name="next-steps"></a>Nächste Schritte

Wenn dies noch nicht geschehen ist, konfigurieren Sie die Konnektivität mit Ihrer FCI mit einem [virtuellen Netzwerknamen und einem Azure-Lastenausgleich](failover-cluster-instance-vnn-azure-load-balancer-configure.md) oder einem [verteilten Netzwerknamen (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Wenn Premium-Dateifreigaben nicht die richtige FCI-Speicherlösung für Sie sind, können Sie Ihre FCI mithilfe von [freigegebenen Azure-Datenträgern](failover-cluster-instance-azure-shared-disks-manually-configure.md) oder von [Direkte Speicherplätze](failover-cluster-instance-storage-spaces-direct-manually-configure.md) erstellen. 

Weitere Informationen finden Sie in der Übersicht über [FCI mit SQL Server auf Azure-VMs](failover-cluster-instance-overview.md) und über [Best Practices zur Clusterkonfiguration](hadr-cluster-best-practices.md). 

Weitere Informationen finden Sie unter 
- [Windows-Clustertechnologie](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server-Failoverclusterinstanzen](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
