---
title: SQL Server-Failoverclusterinstanz – Azure Virtual Machines | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie auf Azure Virtual Machines eine SQL Server-Failoverclusterinstanz erstellen.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: a758cce85645e72bfd9434a69393133d3da6b57d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60591600"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurieren der SQL Server-Failoverclusterinstanz auf Azure Virtual Machines

In diesem Artikel wird beschrieben, wie Sie eine SQL Server-Failoverclusterinstanz (FCI) unter dem Resource Manager-Modell auf virtuellen Azure-Computern erstellen. Bei dieser Lösung wird [„Direkte Speicherplätze“ in Windows Server 2016 Datacenter Edition\(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) als softwarebasiertes virtuelles SAN verwendet, mit dem der Speicher (Datenträger für Daten) zwischen den Knoten (Azure-VMs) in einem Windows-Cluster synchronisiert wird. S2D ist eine Neuerung in Windows Server 2016.

Im folgenden Diagramm ist die vollständige Lösung auf virtuellen Azure-Computern dargestellt:

![Verfügbarkeitsgruppe](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

In der obigen Abbildung ist Folgendes zu sehen:

- Zwei virtuelle Azure-Computer in einem Windows-Failovercluster. Wenn sich ein virtueller Computer in einem Failovercluster befindet, wird dies auch als *Clusterknoten* bzw. als *Knoten* bezeichnet.
- Jeder virtuelle Computer verfügt mindestens über zwei Datenträger für Daten.
- S2D synchronisiert die Daten auf dem Datenträger und stellt den synchronisierten Speicher als Speicherpool dar.
- Der Speicherpool stellt ein freigegebenes Clustervolume (CSV) für den Failovercluster dar.
- Die SQL Server-FCI-Clusterrolle verwendet das CSV für die Datenträger für Daten.
- Ein Azure Load Balancer für die IP-Adresse der SQL Server-Failoverclusterinstanz.
- Eine Azure-Verfügbarkeitsgruppe enthält alle Ressourcen.

   >[!NOTE]
   >Alle Azure-Ressourcen im Diagramm befinden sich in derselben Ressourcengruppe.

Ausführliche Informationen zu S2D finden Sie unter [„Direkte Speicherplätze“ in Windows Server 2016 Datacenter Edition \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D unterstützt zwei Arten von Architekturen: „konvergent“ und „hyperkonvergent“. Die Architektur in diesem Dokument ist hyperkonvergent. In einer hyperkonvergenten Infrastruktur wird der Speicher auf denselben Servern angeordnet, auf denen die gruppierte Anwendung gehostet wird. In dieser Architektur befindet sich der Speicher auf dem SQL Server-FCI-Knoten.

## <a name="licensing-and-pricing"></a>Lizenzierung und Preise

Auf Azure Virtual Machines können Sie SQL Server lizenzieren, indem Sie VM-Images vom Typ „Nutzungsbasierte Bezahlung“ (Pay-As-You-Go, PAYG) oder „Verwendung Ihrer eigenen Lizenz“ (Bring-Your-Own-License, BYOL) verwenden. Der gewählte Imagetyp bestimmt, wie Ihnen die Gebühren berechnet werden.

Bei der PAYG-Lizenzierung fallen für eine Failoverclusterinstanz (FCI) von SQL Server auf Azure Virtual Machines Gebühren für alle FCI-Knoten an, einschließlich der passiven Knoten. Weitere Informationen finden Sie unter [Virtuelle SQL Server Enterprise-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Kunden mit Enterprise Agreement und Software Assurance verfügen über das Recht, für jeden aktiven Knoten einen kostenlosen passiven FCI-Knoten zu verwenden. Verwenden Sie zum Nutzen dieses Vorteils in Azure VM-Images vom Typ „BYOL“, und verwenden Sie dann sowohl auf den aktiven als auch auf den passiven Knoten der FCI dieselbe Lizenz. Weitere Informationen finden Sie unter [Enterprise Agreement](https://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Einen Vergleich der PAYG- und BYOL-Lizenzierung für SQL Server auf Azure Virtual Machines finden Sie unter [Erste Schritte mit virtuellen SQL-Computern](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Umfassende Informationen zur Lizenzierung von SQL Server finden Sie unter [Preise](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Beispiel für Azure-Vorlage

Sie können die gesamte Lösung in Azure über eine Vorlage erstellen. Ein Beispiel für eine Vorlage ist auf GitHub unter den [Azure-Schnellstartvorlagen](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) verfügbar. Dieses Beispiel wurde nicht für eine bestimmte Workload entworfen und wurde auch nicht entsprechend getestet. Sie können die Vorlage ausführen, um eine SQL Server-FCI mit einer Verbindung für S2D-Speicher mit Ihrer Domäne zu erstellen. Sie können die Vorlage auswerten und für Ihre Zwecke anpassen.

## <a name="before-you-begin"></a>Voraussetzungen

Es gibt einige Dinge, die Sie wissen müssen, und einige Voraussetzungen müssen erfüllt sein, bevor Sie fortfahren können.

### <a name="what-to-know"></a>Erforderliche Informationen
Sie sollten die folgende Technologie verwenden können:

- [Windows-Clustertechnologie](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server-Failoverclusterinstanzen](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Ein wichtiger Unterschied besteht darin, dass wir in einem Azure IaaS-VM-Gastfailovercluster einen einzelnen Netzwerkadapter pro Server (Clusterknoten) und ein einzelnes Subnetz empfehlen. Azure-Netzwerktechnologie bietet physische Redundanz, die zusätzliche Netzwerkkarten und Subnetze in einem Azure IaaS-VM-Gastcluster überflüssig macht. Obwohl im Clustervalidierungsbericht eine Warnung ausgegeben wird, dass die Knoten nur in einem einzigen Netzwerk erreichbar sind, kann diese Warnung für Azure IaaS-VM-Gast-Failovercluster einfach ignoriert werden. 

Außerdem sollten Sie über Grundlagenkenntnisse in Bezug auf die folgende Technologie verfügen:

- [Zusammengeführte Lösung unter Verwendung von „Direkte Speicherplätze“ in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure-Ressourcengruppen](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Derzeit wird die [SQL Server-IaaS-Agent-Erweiterung](virtual-machines-windows-sql-server-agent-extension.md) nicht für SQL Server-Failoverclusterinstanzen (FCI) in Azure unterstützt. Sie sollten die Erweiterung auf virtuellen Computern deinstallieren, die an den FCI beteiligt sind. Diese Erweiterung unterstützt Features wie automatisierte Sicherung und automatisiertes Patchen sowie einige Portalfeatures für SQL. Diese Features sind nach der Deinstallation des Agents nicht für SQL-VMs einsetzbar.

### <a name="what-to-have"></a>Voraussetzungen

Bevor Sie die Anweisungen in diesem Artikel befolgen, sollten Sie über Folgendes verfügen:

- Ein Microsoft Azure-Abonnement
- Eine Windows-Domäne auf virtuellen Azure-Computern
- Ein Konto mit der Berechtigung zum Erstellen von Objekten auf dem virtuellen Azure-Computer
- Ein virtuelles Azure-Netzwerk und ein Subnetz mit einem ausreichend großen IP-Adressraum für die folgenden Komponenten:
   - Beide virtuellen Computer
   - Die IP-Adresse des Failoverclusters
   - Eine IP-Adresse für jede FCI
- DNS-Konfiguration im Azure-Netzwerk mit Verweis auf die Domänencontroller

Wenn diese Voraussetzungen erfüllt sind, können Sie mit dem Erstellen Ihres Failoverclusters fortfahren. Der erste Schritt ist die Erstellung der virtuellen Computer.

## <a name="step-1-create-virtual-machines"></a>Schritt 1: Erstellen von virtuellen Computern

1. Melden Sie sich mit den Daten Ihres Abonnements am [Azure-Portal](https://portal.azure.com) an.

1. [Erstellen Sie eine Azure-Verfügbarkeitsgruppe](../tutorial-availability-sets.md).

   Mit der Verfügbarkeitsgruppe werden virtuelle Computer über Fehler- und Updatedomänen hinweg gruppiert. Mithilfe einer Verfügbarkeitsgruppe wird sichergestellt, dass Ihre Anwendung nicht durch Ausfälle einzelner Komponenten, z.B. des Netzwerkswitchs oder der Stromversorgungseinheit eines Serverracks, beeinträchtigt wird.

   Falls Sie die Ressourcengruppe für Ihre virtuellen Computer noch nicht erstellt haben, können Sie dies beim Erstellen einer Azure-Verfügbarkeitsgruppe nachholen. Führen Sie die folgenden Schritte zum Erstellen der Verfügbarkeitsgruppe aus, wenn Sie das Azure-Portal verwenden:

   - Klicken Sie im Azure-Portal auf **+** , um den Azure Marketplace zu öffnen. Suchen Sie nach der Option **Verfügbarkeitsgruppe**.
   - Klicken Sie auf **Verfügbarkeitsgruppe**.
   - Klicken Sie auf **Create**.
   - Legen Sie auf dem Blatt **Verfügbarkeitsgruppe erstellen** die folgenden Werte fest:
      - **Name**: Ein Name für die Verfügbarkeitsgruppe.
      - **Abonnement**: Ihr Azure-Abonnement.
      - **Ressourcengruppe**: Falls Sie eine vorhandene Gruppe verwenden möchten, können Sie auf **Vorhandene verwenden** klicken und die Gruppe in der Dropdownliste auswählen. Wählen Sie andernfalls die Option **Neu erstellen**, und geben Sie einen Namen für die Gruppe ein.
      - **Standort**: Legen Sie den Standort fest, an dem Sie Ihre virtuellen Computer erstellen möchten.
      - **Fehlerdomänen**: Verwenden Sie den Standardwert (3).
      - **Updatedomänen**: Verwenden Sie den Standardwert (5).
   - Klicken Sie auf **Erstellen**, um die Verfügbarkeitsgruppe zu erstellen.

1. Erstellen Sie die virtuellen Computer in der Verfügbarkeitsgruppe.

   Stellen Sie zwei virtuelle SQL Server-Computer in der Azure-Verfügbarkeitsgruppe bereit. Eine Anleitung finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server im Azure-Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Ordnen Sie beide virtuellen Computer wie folgt an:

   - In derselben Azure-Ressourcengruppe wie Ihre Verfügbarkeitsgruppe
   - In demselben Netzwerk wie Ihr Domänencontroller
   - In einem Subnetz mit einem ausreichend großen IP-Adressraum für beide virtuellen Computer und alle FCIs, die in diesem Cluster unter Umständen verwendet werden sollen
   - In der Azure-Verfügbarkeitsgruppe   

      >[!IMPORTANT]
      >Sie können die Verfügbarkeitsgruppe nicht mehr festlegen oder ändern, nachdem ein virtueller Computer erstellt wurde.

   Wählen Sie über den Azure Marketplace ein Image aus. Sie können ein Marketplace-Image verwenden, das Windows Server und SQL Server oder nur Windows Server enthält. Ausführliche Informationen finden Sie unter [Übersicht über SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-server-iaas-overview.md).

   Die offiziellen SQL Server-Images im Azure-Katalog enthalten eine installierte SQL Server-Instanz sowie die SQL Server-Installationssoftware und den erforderlichen Schlüssel.

   Wählen Sie das richtige Image für die gewünschte Bezahlung für die SQL Server-Lizenz:

   - **Lizenzierung mit nutzungsbasierter Bezahlung**: Der Sekundenpreis für diese Images enthält die SQL Server-Lizenzierung:
      - **SQL Server 2016 Enterprise unter Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard unter Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer unter Windows Server Datacenter 2016**

   - **BYOL (Bring-Your-Own-License)**

      - **{BYOL} SQL Server 2016 Enterprise unter Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard unter Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Entfernen Sie nach dem Erstellen des virtuellen Computers die vorinstallierte eigenständige SQL Server-Instanz. Sie verwenden die vorinstallierten SQL Server-Medien, um die SQL Server-FCI nach der Konfiguration von Failovercluster und S2D zu erstellen.

   Alternativ dazu können Sie auch Azure Marketplace-Images verwenden, die nur das Betriebssystem enthalten. Wählen Sie ein Image vom Typ **Windows Server 2016 Datacenter** aus, und installieren Sie die SQL Server-FCI, nachdem Sie die Konfiguration von Failovercluster und S2D durchgeführt haben. Dieses Image enthält keine SQL Server-Installationsmedien. Speichern Sie die Installationsmedien an einem Speicherort, an dem Sie die SQL Server-Installation für jeden Server ausführen können.

1. Stellen Sie eine Verbindung mit jedem virtuellen Computer per RDP her, nachdem Ihre virtuellen Computer von Azure erstellt wurden.

   Wenn Sie die Verbindung mit einem virtuellen Computer per RDP zum ersten Mal herstellen, wird gefragt, ob Sie zulassen möchten, dass dieser PC über das Netzwerk ermittelt werden kann. Klicken Sie auf **Ja**.

1. Entfernen Sie die SQL Server-Instanz, wenn Sie eines der SQL Server-basierten VM-Images verwenden.

   - Klicken Sie unter **Programme und Funktionen** mit der rechten Maustaste auf **Microsoft SQL Server 2016 (64 Bit)** , und klicken Sie dann auf **Deinstallieren/ändern**.
   - Klicken Sie auf **Entfernen**.
   - Wählen Sie die Standardinstanz aus.
   - Entfernen Sie alle Funktionen unter **Datenbank-Engine-Dienst**. Achten Sie darauf, dass Sie **Freigegebene Funktionen** nicht entfernen. Sehen Sie sich die folgende Abbildung an:

      ![Entfernen von Funktionen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klicken Sie auf **Weiter** und anschließend auf **Entfernen**.

1. <a name="ports"></a>Öffnen Sie die Firewallports.

   Öffnen Sie auf jedem virtuellen Computer die folgenden Ports in der Windows-Firewall.

   | Zweck | TCP-Port | Notizen
   | ------ | ------ | ------
   | SQL Server | 1433 | Dies ist der normale Port für Standardinstanzen von SQL Server. Falls Sie ein Image aus dem Katalog verwendet haben, ist dieser Port automatisch geöffnet.
   | Integritätstest | 59999 | Beliebiger geöffneter TCP-Port. In einem späteren Schritt konfigurieren Sie für den [Integritätstest](#probe) des Lastenausgleichs und den Cluster die Verwendung dieses Ports.  

1. Fügen Sie dem virtuellen Computer Speicher hinzu. Ausführliche Informationen finden Sie im Artikel zum [Hinzufügen von Speicher](../disks-types.md).

   Für beide virtuellen Computer sind jeweils mindestens zwei Datenträger für Daten erforderlich.

   Fügen Sie unformatierte Datenträger an (keine Datenträger mit NTFS-Formatierung).
      >[!NOTE]
      >Wenn Sie Datenträger mit NTFS-Formatierung anfügen, können Sie S2D nur ohne Datenträger-Eignungsprüfung aktivieren.  

   Fügen Sie an jede VM mindestens zwei SSD Premium-Datenträger an. Wir empfehlen Ihnen die Verwendung von Datenträgern, die mindestens vom Typ „P30“ (1 TB) sind.

   Legen Sie die Hostzwischenspeicherung auf **Schreibgeschützt** fest.

   Die in Produktionsumgebungen verwendete Speicherkapazität richtet sich nach Ihrer Workload. Die in diesem Artikel beschriebenen Werte dienen Demonstrations- und Testzwecken.

1. [Fügen Sie die virtuellen Computer Ihrer bereits vorhandenen Domäne hinzu](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Nachdem die virtuellen Computer erstellt und konfiguriert wurden, können Sie den Failovercluster konfigurieren.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Schritt 2: Konfigurieren des Windows-Failoverclusters mit S2D

Im nächsten Schritt konfigurieren Sie den Failovercluster mit S2D. In diesem Schritt führen Sie die folgenden untergeordneten Schritte aus:

1. Hinzufügen des Windows-Failoverclusteringfeatures
1. Überprüfen des Clusters
1. Erstellen des Failoverclusters
1. Erstellen des Cloudzeugen
1. Hinzufügen von Speicher

### <a name="add-windows-failover-clustering-feature"></a>Hinzufügen des Windows-Failoverclusteringfeatures

1. Stellen Sie per RDP zuerst eine Verbindung mit dem ersten virtuellen Computer her, indem Sie ein Domänenkonto verwenden, das Mitglied der Gruppe der lokalen Administratoren ist und über Berechtigungen zum Erstellen von Objekten in Active Directory verfügt. Verwenden Sie dieses Konto für den Rest der Konfiguration.

1. [Fügen Sie das Failoverclusteringfeature jedem virtuellen Computer hinzu](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Führen Sie die folgenden Schritte auf beiden virtuellen Computern aus, um das Failoverclusteringfeature über die UI zu installieren.
   - Klicken Sie im **Server-Manager** auf **Verwalten** und dann auf **Rollen und Features hinzufügen**.
   - Klicken Sie im **Assistenten zum Hinzufügen von Rollen und Features** auf **Weiter**, bis Sie zu **Features auswählen** gelangen.
   - Klicken Sie unter **Features auswählen** auf **Failoverclustering**. Schließen Sie alle erforderlichen Features und Verwaltungstools ein. Klicken Sie auf **Features hinzufügen**.
   - Klicken Sie auf **Weiter** und dann auf **Fertig stellen**, um die Features zu installieren.

   Führen Sie zum Installieren des Failoverclusteringfeatures mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Beachten Sie den Hinweis, dass die nächsten Schritte aus Schritt 3 der Anleitung unter [Zusammengeführte Lösung unter Verwendung von „Direkte Speicherplätze“ in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct) entsprechen.

### <a name="validate-the-cluster"></a>Überprüfen des Clusters

Dieser Leitfaden basiert auf der Anleitung unter [Ausführen der Clustervalidierung](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Führen Sie die Validierung für den Cluster auf der Benutzeroberfläche oder mit PowerShell durch.

Führen Sie die folgenden Schritte auf einem der beiden virtuellen Computer aus, um den Cluster über die Benutzeroberfläche zu validieren.

1. Klicken Sie in **Server-Manager** auf **Tools** und dann auf **Failovercluster-Manager**.
1. Klicken Sie im **Failovercluster-Manager** auf **Aktion** und dann auf **Konfiguration überprüfen...** .
1. Klicken Sie auf **Weiter**.
1. Geben Sie unter **Server oder Cluster auswählen** den Namen der beiden virtuellen Computer ein.
1. Wählen Sie unter **Testoptionen** die Option **Nur ausgewählte Tests ausführen**. Klicken Sie auf **Weiter**.
1. Schließen Sie unter **Testauswahl** alle Tests mit Ausnahme von **Speicher** ein. Sehen Sie sich die folgende Abbildung an:

   ![Durchführen von Validierungstests](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klicken Sie auf **Weiter**.
1. Klicken Sie unter **Bestätigung** auf **Weiter**.

Mit dem **Konfigurationsüberprüfungs-Assistenten** werden die Validierungstests durchgeführt.

Führen Sie zum Validieren des Clusters mit PowerShell das folgende Skript in einer PowerShell-Administratorsitzung auf einem der virtuellen Computer aus.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Erstellen Sie nach dem Validieren des Clusters den Failovercluster.

### <a name="create-the-failover-cluster"></a>Erstellen des Failoverclusters

In diesem Leitfaden wird auf den Abschnitt [Erstellen des Failoverclusters](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster) Bezug genommen.

Für das Erstellen des Failoverclusters benötigen Sie Folgendes:
- Die Namen der virtuellen Computer, die zu Clusterknoten werden
- Einen Namen für den Failovercluster
- Eine IP-Adresse für den Failovercluster. Sie können eine IP-Adresse verwenden, die nicht in demselben virtuellen Azure-Netzwerk und Subnetz wie die Clusterknoten genutzt wird.

Mit dem folgenden PowerShell-Code wird ein Failovercluster erstellt. Aktualisieren Sie das Skript mit den Namen der Knoten (Namen virtueller Computer) und einer verfügbaren IP-Adresse aus dem Azure VNET:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Erstellen eines Cloudzeugen

Ein Cloudzeuge ist eine neue Art von Clusterquorumzeuge, der in einem Azure Storage Blob gespeichert wird. Es ist dann nicht erforderlich, eine separate VM als Host für eine Zeugenfreigabe zu verwenden.

1. [Erstellen Sie einen Cloudzeugen für den Failovercluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Erstellen Sie einen Blobcontainer.

1. Speichern Sie die Zugriffsschlüssel und die Container-URL.

1. Konfigurieren Sie den Failovercluster-Quorumzeugen. Informationen hierzu finden Sie auf der Benutzeroberfläche unter [Cloudzeugen als Quorumzeugen konfigurieren](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Hinzufügen von Speicher

Die Datenträger für S2D müssen leer sein und dürfen keine Partitionen oder anderen Daten enthalten. Führen Sie die [Schritte in diesem Leitfaden](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks) aus, um Datenträger zu bereinigen.

1. [Aktivieren Sie „Direkte Speicherplätze“ \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Mit dem folgenden PowerShell-Code wird „Direkte Speicherplätze“ aktiviert.  

   ```powershell
   Enable-ClusterS2D
   ```

   Im **Failovercluster-Manager** wird jetzt der Speicherpool angezeigt.

1. [Erstellen eines Volumes](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes)

   Mit einer S2D-Funktion können Sie automatisch einen Speicherpool erstellen lassen, wenn Sie die Funktion aktivieren. Sie sind jetzt für das Erstellen eines Volumes bereit. Mit dem PowerShell-Cmdlet `New-Volume` wird der Prozess der Volumeerstellung automatisiert, z.B. das Formatieren, Hinzufügen zum Cluster und Erstellen eines freigegebenen Clustervolumes (CSV). Im folgenden Beispiel wird ein CSV mit 800 GB erstellt.

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Nachdem dieser Befehl abgeschlossen ist, wird ein Volume mit 800 GB als Clusterressource bereitgestellt. Das Volume befindet sich unter `C:\ClusterStorage\Volume1\`.

   Im folgenden Diagramm ist ein freigegebenes Clustervolume mit S2D dargestellt:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Schritt 3: Testen eines Failovers mit dem Failovercluster

Überprüfen Sie im Failovercluster-Manager, ob Sie die Speicherressource auf den anderen Clusterknoten verschieben können. Wenn Sie mit dem **Failovercluster-Manager** eine Verbindung mit dem Failovercluster herstellen und den Speicher von einem Knoten auf den anderen verschieben können, sind Sie bereit zum Konfigurieren der FCI.

## <a name="step-4-create-sql-server-fci"></a>Schritt 4: Erstellen der SQL Server-FCI

Nachdem Sie den Failovercluster und alle Clusterkomponenten konfiguriert haben, einschließlich des Speichers, können Sie die SQL Server-FCI erstellen.

1. Stellen Sie die Verbindung mit dem ersten virtuellen Computer per RDP her.

1. Stellen Sie im **Failovercluster-Manager** sicher, dass sich alle Kernressourcen des Clusters auf dem ersten virtuellen Computer befinden. Verschieben Sie alle Ressourcen auf diesen virtuellen Computer, falls dies erforderlich ist.

1. Suchen Sie nach den Installationsmedien. Wenn für den virtuellen Computer eines der Azure Marketplace-Images verwendet wird, befinden sich die Medien unter `C:\SQLServer_<version number>_Full`. Klicken Sie auf **Setup**.

1. Klicken Sie im **SQL Server-Installationscenter** auf **Installation**.

1. Klicken Sie auf **Neue SQL Server-Failoverclusterinstallation**. Befolgen Sie im Assistenten die Anleitung zum Installieren der SQL Server-FCI.

   Die FCI-Datenverzeichnisse müssen sich in gruppiertem Speicher befinden. Bei S2D ist es kein freigegebener Datenträger, sondern ein Bereitstellungspunkt auf einem Volume auf jedem Server. S2D synchronisiert das Volume zwischen beiden Knoten. Das Volume wird gegenüber dem Cluster als freigegebenes Clustervolume dargestellt. Verwenden Sie den CSV-Bereitstellungspunkt für die Datenverzeichnisse.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Nach Abschluss des Assistenten wird vom Setup eine SQL Server-FCI auf dem ersten Knoten installiert.

1. Nachdem das Setup die FCI erfolgreich auf dem ersten Knoten installiert hat, können Sie per RDP eine Verbindung mit dem zweiten Knoten herstellen.

1. Öffnen Sie das **SQL Server-Installationscenter**. Klicken Sie auf **Installation**.

1. Klicken Sie auf **Knoten einem SQL Server-Failovercluster hinzufügen**. Befolgen Sie die Anleitung im Assistenten, um SQL Server zu installieren und diese Serverinstanz der FCI hinzuzufügen.

   >[!NOTE]
   >Wenn Sie ein Azure Marketplace-Katalogimage mit SQL Server verwendet haben, sind die SQL Server-Tools im Image enthalten. Wenn Sie dieses Image nicht verwendet haben, müssen Sie die SQL Server-Tools separat installieren. Weitere Informationen finden Sie unter [Herunterladen von SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Schritt 5: Erstellen eines Azure Load Balancers

Auf virtuellen Azure-Computern wird für Cluster ein Lastenausgleich für eine IP-Adresse verwendet, die zu einem bestimmten Zeitpunkt auf einem Clusterknoten vorhanden sein muss. In dieser Lösung enthält der Lastenausgleich die IP-Adresse für die SQL Server-FCI.

Weitere Informationen finden Sie unter [Erstellen einer Azure Load Balancer-Instanz](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Erstellen des Lastenausgleichs im Azure-Portal

So erstellen Sie den Lastenausgleich

1. Navigieren Sie im Azure-Portal zu der Ressourcengruppe mit den virtuellen Computern.

1. Klicken Sie auf **+ Hinzufügen**. Durchsuchen Sie den Marketplace nach **Load Balancer**. Klicken Sie auf **Load Balancer**.

1. Klicken Sie auf **Create**.

1. Konfigurieren Sie Folgendes für den Load Balancer (Lastenausgleich):

   - **Name**: Ein Name, mit dem der Lastenausgleich identifiziert wird.
   - **Typ**: Der Lastenausgleich kann entweder öffentlich oder privat sein. Auf einen privaten Lastenausgleich kann aus demselben VNET zugegriffen werden. Für die meisten Azure-Anwendungen kann ein privater Lastenausgleich verwendet werden. Verwenden Sie einen öffentlichen Lastenausgleich, wenn Ihre Anwendung direkten Zugriff auf SQL Server über das Internet benötigt.
   - **Virtuelles Netzwerk**: Dies ist dasselbe Netzwerk wie für die virtuellen Computer.
   - **Subnetz**: Dies ist dasselbe Subnetz wie für die virtuellen Computer.
   - **Private IP-Adresse**: Dies ist die gleiche IP-Adresse, die Sie der Clusternetzwerkressource der SQL Server-FCI zugewiesen haben.
   - **Abonnement**: Ihr Azure-Abonnement.
   - **Ressourcengruppe**: Verwenden Sie dieselbe Ressourcengruppe wie für Ihre virtuellen Computer.
   - **Standort**: Verwenden Sie denselben Azure-Standort wie für Ihre virtuellen Computer.
   Sehen Sie sich die folgende Abbildung an:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurieren des Back-End-Pools für den Lastenausgleich

1. Wechseln Sie zurück zur Azure-Ressourcengruppe mit den virtuellen Computern, und suchen Sie nach dem neuen Lastenausgleich. Es kann sein, dass Sie hierfür die Ansicht mit der Ressourcengruppe aktualisieren müssen. Klicken Sie auf den Lastenausgleich.

1. Klicken Sie auf **Back-End-Pools** und dann auf **+ Hinzufügen**, um einen Back-End-Pool hinzuzufügen.

1. Ordnen Sie den Back-End-Pool mit der Verfügbarkeitsgruppe zu, die die VMs enthält.

1. Aktivieren Sie unter **Zielnetzwerk-IP-Konfigurationen** die Option **VIRTUELLER COMPUTER**, und wählen Sie die virtuellen Computer aus, die als Clusterknoten eingeschlossen werden. Schließen Sie dabei alle virtuellen Computer ein, die die FCI hosten. 

1. Klicken Sie auf **OK**, um den Back-End-Pool zu erstellen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurieren eines Integritätstests für den Lastenausgleich

1. Klicken Sie auf dem Blatt für den Lastenausgleich auf **Integritätstests**.

1. Klicken Sie auf **+ Hinzufügen**.

1. Legen Sie auf dem Blatt **Add health probe**<a name="probe"></a> (Integritätstest hinzufügen) die Parameter für den Integritätstest fest:

   - **Name**: Ein Name für den Integritätstest.
   - **Protokoll:** TCP.
   - **Port:** Legen Sie den Port fest, den Sie [in diesem Schritt](#ports) in der Firewall für den Integritätstest erstellt haben. Im Beispiel dieses Artikels wird der TCP-Port `59999` verwendet.
   - **Intervall**: 5 Sekunden.
   - **Fehlerschwellenwert**: Zwei aufeinanderfolgende Fehler.

1. Klicken Sie auf OK.

### <a name="set-load-balancing-rules"></a>Festlegen der Lastenausgleichsregeln

1. Klicken Sie auf dem Blatt für den Lastenausgleich auf **Lastenausgleichsregeln**.

1. Klicken Sie auf **+ Hinzufügen**.

1. Legen Sie die Parameter für die Lastenausgleichsregeln fest:

   - **Name**: Ein Name für die Lastenausgleichsregeln.
   - **Front-End-IP-Adresse**: Verwenden Sie die IP-Adresse für die Clusternetzwerkressource der SQL Server-FCI.
   - **Port:** Legen Sie den TCP-Port für die SQL Server-FCI fest. Der Standardport der Instanz lautet 1433.
   - **Back-End-Port**: Für diesen Wert wird der gleiche Port verwendet, den Sie als Wert für **Port** angeben, wenn Sie **Floating IP (Direct Server Return)** aktivieren.
   - **Back-End-Pool**: Verwenden Sie den Namen des Back-End-Pools, den Sie zuvor konfiguriert haben.
   - **Integritätstest**: Verwenden Sie den Integritätstest, den Sie zuvor konfiguriert haben.
   - **Sitzungspersistenz**: None (Keine):
   - **Leerlaufzeitüberschreitung (Minuten)** : 4.
   - **Floating IP (Direct Server Return)** : Enabled

1. Klicken Sie auf **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Schritt 6: Konfigurieren des Clusters für den Test

Legen Sie den Parameter für den Clustertestport in PowerShell fest.

Aktualisieren Sie die Variablen im folgenden Skript mit Werten aus Ihrer Umgebung, um den Parameter für den Clustertestport festzulegen. Entfernen Sie die spitzen Klammern `<>` aus dem Skript. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Legen Sie die Werte für Ihre Umgebung im vorherigen Skript fest. Die Werte werden in der folgenden Liste beschrieben:

   - `<Cluster Network Name>`: Name des Windows Server-Failoverclusters für das Netzwerk. Klicken Sie in **Failovercluster-Manager** > **Netzwerke** mit der rechten Maustaste auf das Netzwerk, und klicken Sie dann auf **Eigenschaften**. Der richtige Wert befindet sich auf der Registerkarte **Allgemein** unter **Name**. 

   - `<SQL Server FCI IP Address Resource Name>`: Name der IP-Adressressource der SQL Server-FCI. Klicken Sie in **Failovercluster-Manager** > **Rollen** unter der Rolle „SQL Server-FCI“ unter **Servername** mit der rechten Maustaste auf die IP-Adressressource, und klicken Sie dann auf **Eigenschaften**. Der richtige Wert befindet sich auf der Registerkarte **Allgemein** unter **Name**. 

   - `<ILBIP>`: Die ILB-IP-Adresse. Diese Adresse wird als ILB-Front-End-Adresse im Azure-Portal konfiguriert. Dies ist auch die FCI-IP-Adresse von SQL Server. Sie finden sie im **Failovercluster-Manager** auf der gleichen Eigenschaftenseite, auf der sich der `<SQL Server FCI IP Address Resource Name>` befindet.  

   - `<nnnnn>`: Der Testport, den Sie im Integritätstest des Lastenausgleichs konfiguriert haben. Alle nicht verwendeten TCP-Ports sind zulässig. 

>[!IMPORTANT]
>Die Subnetzmaske für den Clusterparameter muss die TCP/IP-Broadcastadresse sein: `255.255.255.255`.

Nach dem Festlegen des Clustertests werden alle Clusterparameter in PowerShell angezeigt. Führen Sie das folgende Skript aus:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Schritt 7: Testen des FCI-Failovers

Testen Sie das Failover der FCI, um die Clusterfunktionalität zu validieren. Führen Sie folgende Schritte aus:

1. Stellen Sie per RDP eine Verbindung mit einem der SQL Server-FCI-Clusterknoten her.

1. Öffnen Sie den **Failovercluster-Manager**. Klicken Sie auf **Rollen**. Achten Sie darauf, welcher Knoten im Besitz der SQL Server-FCI-Rolle ist.

1. Klicken Sie mit der rechten Maustaste auf die SQL Server-FCI-Rolle.

1. Klicken Sie auf **Verschieben** und dann auf **Bestmöglicher Knoten**.

Unter **Failovercluster-Manager** wird die Rolle angezeigt, und die Ressourcen werden in den Offlinezustand versetzt. Die Ressourcen werden dann verschoben und auf dem anderen Knoten dann wieder in den Onlinezustand versetzt.

### <a name="test-connectivity"></a>Testen der Konnektivität

Melden Sie sich zum Testen der Konnektivität an einem anderen virtuellen Computer in demselben virtuellen Netzwerk an. Öffnen Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit dem SQL Server-FCI-Namen her.

>[!NOTE]
>Bei Bedarf können Sie [SQL Server Management Studio herunterladen](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Einschränkungen

Virtuelle Azure-Computer unterstützen Microsoft Distributed Transaction Coordinator (MSDTC) auf Windows Server 2019 mit Speicher auf freigegebenen Clustervolumes (CSV) und ein [Load Balancer im Tarif „Standard“](../../../load-balancer/load-balancer-standard-overview.md).

Auf virtuellen Azure-Computern wird MSDTC unter Windows Server 2016 und früheren Versionen aus folgenden Gründen nicht unterstützt:

- Die gruppierte MSDTC-Ressource kann nicht für die Verwendung von freigegebenem Speicher konfiguriert werden. Wenn Sie mit Windows Server 2016 eine MSDTC-Ressource erstellen, wird kein freigegebener Speicher für die Verwendung angezeigt, selbst wenn der Speicher vorhanden ist. Dieses Problem wurde in Windows Server 2019 behoben.
- Der Load Balancer im Tarif „Basic“ verarbeitet keine RPC-Ports.

## <a name="see-also"></a>Siehe auch

[Einrichten von S2D mit Remotedesktop (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Zusammengeführte Lösung unter Verwendung von „Direkte Speicherplätze“ in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[„Direkte Speicherplätze“ in Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server support for S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/) (SQL Server-Unterstützung für S2D)
