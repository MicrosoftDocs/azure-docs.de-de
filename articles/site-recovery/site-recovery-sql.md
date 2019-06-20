---
title: Einrichten der Notfallwiederherstellung für SQL Server mit SQL Server und Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung für SQL Server mithilfe von SQL Server und Azure Site Recovery einrichten.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 7725563a80182be8f8c02d94ef1e6cfa382c04d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924851"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Einrichten der Notfallwiederherstellung für SQL Server

In diesem Artikel erfahren Sie, wie Sie das SQL Server-Back-End einer Anwendung mit einer Kombination aus SQL Server-Technologien für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) und [Azure Site Recovery](site-recovery-overview.md) schützen.

Informieren Sie sich zunächst unbedingt über die SQL Server-Funktionen zur Notfallwiederherstellung, einschließlich Failoverclustering, Always On-Verfügbarkeitsgruppen, Datenbankspiegelung und Protokollversand.


## <a name="sql-server-deployments"></a>SQL Server-Bereitstellungen

Viele Workloads nutzen SQL Server als Grundlage, und die Plattform kann zum Implementieren von Datendiensten in Apps wie SharePoint, Dynamics und SAP integriert werden.  SQL Server kann auf verschiedene Arten bereitgestellt werden:

* **Eigenständige SQL Server-Instanz:** SQL Server und alle Datenbanken werden auf einem einzelnen Computer gehostet (physisch oder virtuell). Bei einer Virtualisierung wird Hostclustering verwendet, um die lokale Hochverfügbarkeit sicherzustellen. Es wird keine Hochverfügbarkeit für die Gastebene implementiert.
* **SQL Server-Failoverclustering-Instanzen (Always On-FCI):** Mehrere Knoten mit SQL Server-Instanzen und freigegebenen Datenträgern werden in einem Windows-Failovercluster konfiguriert. Wenn ein Knoten heruntergefahren ist, kann der Cluster ein Failover von SQL Server auf eine andere Instanz ausführen. Dieses Setup wird in der Regel zum Implementieren von Hochverfügbarkeit an einem primären Standort verwendet. Diese Bereitstellung stellt keinen Schutz vor Fehlern oder einem Ausfall auf der Ebene des freigegebenen Speichers dar. Ein freigegebener Datenträger kann per iSCSI, Fibre Channel oder freigegebener VHDX implementiert werden.
* **SQL-AlwaysOn-Verfügbarkeitsgruppen:** Mehrere Knoten sind in einem Shared-Nothing-Cluster mit SQL Server-Datenbanken in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover eingerichtet.

  In diesem Artikel werden die folgenden nativen SQL-Notfallwiederherstellungstechnologien zum Wiederherstellen von Datenbanken an einem Remotestandort verwendet:

* SQL AlwaysOn-Verfügbarkeitsgruppen zur Notfallwiederherstellung für SQL Server 2012 oder 2014 Enterprise-Editionen
* SQL-Datenbankspiegelung im Hochsicherheitsmodus für SQL Server Standard Edition (beliebige Version) oder SQL Server 2008 R2

## <a name="site-recovery-support"></a>Site Recovery-Unterstützung

### <a name="supported-scenarios"></a>Unterstützte Szenarien
Site Recovery kann zum Schützen von SQL Server verwendet werden. Dies ist in der Tabelle unten zusammengefasst.

**Szenario** | **Am sekundären Standort** | **In Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Physischer Server** | Ja | Ja
**Azure** |Nicht verfügbar| Ja

### <a name="supported-sql-server-versions"></a>Unterstützte SQL Server-Versionen
Diese SQL Server-Versionen werden für die unterstützten Szenarien unterstützt:

* SQL Server 2016 Enterprise und Standard
* SQL Server 2014 Enterprise und Standard
* SQL Server 2012 Enterprise und Standard
* SQL Server 2008 R2 Enterprise und Standard

### <a name="supported-sql-server-integration"></a>Unterstützte SQL Server-Integration

Site Recovery kann zur Bereitstellung einer Notfallwiederherstellungslösung mit nativen BCDR-Technologien von SQL Server kombiniert werden. Dies ist in der folgenden Tabelle zusammengefasst:

**Feature** | **Details** | **SQL Server** |
--- | --- | ---
**AlwaysOn-Verfügbarkeitsgruppe** | Mehrere eigenständige Instanzen von SQL Server werden jeweils in einem Failovercluster mit mehreren Knoten ausgeführt.<br/><br/>Datenbanken können in Failovergruppen zusammengefasst werden, die in SQL Server-Instanzen kopiert (gespiegelt) werden können, sodass kein freigegebener Speicher erforderlich ist.<br/><br/>Ermöglicht die Notfallwiederherstellung zwischen einem primären und einem oder mehreren sekundären Standorten. Zwei Knoten können in einem Shared-Nothing-Cluster mit SQL Server-Datenbanken in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover eingerichtet werden. | SQL Server 2016, SQL Server 2014 und SQL Server 2012 Enterprise Edition
**Failoverclustering (Always On-FCI)** | SQL Server nutzt das Windows-Failoverclustering für Hochverfügbarkeit der lokalen SQL Server-Workloads.<br/><br/>Knoten, auf denen Instanzen von SQL Server mit freigegebenen Datenträgern ausgeführt werden, werden in einem Failovercluster konfiguriert. Fällt eine Instanz aus, wird für den Cluster ein Failover zu einer anderen Instanz durchgeführt.<br/><br/>Der Cluster schützt nicht vor Fehlern oder Ausfällen im freigegebenen Speicher. Der freigegebene Datenträger kann mit iSCSI, Fibre Channel oder freigegebenen VHDX-Dateien implementiert werden. | SQL Server Enterprise Editions<br/><br/>SQL Server Standard Edition (auf zwei Knoten beschränkt)
**Datenbankspiegelung (Modus für hohe Sicherheit)** | Schützt eine einzelne Datenbank mittels einer sekundären Kopie. Replikationsmodi mit hoher Sicherheit (synchron) oder mit hoher Leistung (asynchron) verfügbar. Kein Failovercluster erforderlich. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise (alle Editionen)
**Eigenständige SQL Server-Instanz** | SQL Server und die Datenbank werden auf einem einzelnen Server (physisch oder virtuell) gehostet. Bei einem virtuellen Server wird Hostclustering verwendet, um Hochverfügbarkeit zu gewährleisten. Keine Hochverfügbarkeit auf Gastebene. | Enterprise oder Standard

## <a name="deployment-recommendations"></a>Bereitstellungsempfehlungen

Diese Tabelle enthält unsere Empfehlungen für die Integration von SQL Server-BCDR-Technologien in Site Recovery:

| **Version** | **Edition** | **Bereitstellung** | **Lokal zu lokal** | **Lokal zu Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2016, 2014 oder 2012 |Enterprise |Failoverclusterinstanz |Always On-Verfügbarkeitsgruppen |Always On-Verfügbarkeitsgruppen |
|| Enterprise |Always On-Verfügbarkeitsgruppen für Hochverfügbarkeit |Always On-Verfügbarkeitsgruppen |Always On-Verfügbarkeitsgruppen |
|| Standard |Failoverclusterinstanz (FCI) |Site Recovery-Replikation mit lokaler Spiegelung |Site Recovery-Replikation mit lokaler Spiegelung |
|| Enterprise oder Standard |Eigenständig |Site Recovery-Replikation |Site Recovery-Replikation |
| SQL Server 2008 R2 oder 2008 |Enterprise oder Standard |Failoverclusterinstanz (FCI) |Site Recovery-Replikation mit lokaler Spiegelung |Site Recovery-Replikation mit lokaler Spiegelung |
|| Enterprise oder Standard |Eigenständig |Site Recovery-Replikation |Site Recovery-Replikation |
| SQL Server (beliebige Version) |Enterprise oder Standard |Failoverclusterinstanz – DTC-Anwendung |Site Recovery-Replikation |Nicht unterstützt |

## <a name="deployment-prerequisites"></a>Voraussetzungen für die Bereitstellung

* Eine lokale SQL Server-Bereitstellung mit einer unterstützten SQL Server-Version. In der Regel benötigen Sie auch Active Directory für SQL Server.
* Die erforderlichen Komponenten für das gewünschte Bereitstellungsszenario. Erfahren Sie mehr über die Supportanforderungen für die [Replikation in Azure](site-recovery-support-matrix-to-azure.md) und die [lokale Replikation](site-recovery-support-matrix.md) sowie die [Bereitstellungsvoraussetzungen](site-recovery-prereq.md).

## <a name="set-up-active-directory"></a>Einrichten von Active Directory

Richten Sie für den ordnungsgemäßen Betrieb von SQL Server Active Directory am sekundären Wiederherstellungsstandort ein.

* **Kleine Unternehmen**: Wenn nur eine geringe Anzahl von Anwendungen und ein einzelner Domänencontroller für den lokalen Standort vorhanden sind und Sie ein Failover für den gesamten Standort durchführen möchten, empfiehlt es sich, den Domänencontroller mithilfe der Site Recovery-Replikation im sekundären Datencenter oder in Azure zu replizieren.
* **Mittlere bis große Unternehmen**: Wenn Sie über eine hohe Anzahl von Anwendung verfügen, eine Active Directory-Gesamtstruktur verwenden und ein anwendungs- oder workloadspezifisches Failover durchführen möchten, empfiehlt sich die Einrichtung eines zusätzlichen Domänencontrollers im sekundären Datencenter oder in Azure. Wenn Sie für die Wiederherstellung an einem Remotestandort Always On-Verfügbarkeitsgruppen verwenden, empfiehlt es sich, einen weiteren zusätzlichen Domänencontroller für den sekundären Standort oder für Azure einzurichten, der für die wiederhergestellte SQL Server-Instanz verwendet werden kann.

Bei den Anweisungen in diesem Artikel wird davon ausgegangen, dass am sekundären Standort ein Domänencontroller verfügbar ist. [weiteren Informationen](site-recovery-active-directory.md) zum Schützen von Active Directory mit Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integration in Always On von SQL Server für die Replikation in Azure

Das müssen Sie tun:

1. Importieren Sie Skripts in Ihr Azure Automation-Konto. Hier sind die Skripts zum Durchführen eines Failovers für die SQL-Verfügbarkeitsgruppe auf einem [mit Resource Manager erstellten virtuellen Computer](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) und einem [klassischen virtuellen Computer](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![In Azure bereitstellen](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Fügen Sie ASR-SQL-FailoverAG als vorausgehende Aktion der ersten Gruppe des Wiederherstellungsplans hinzu.

1. Befolgen Sie die im Skript verfügbaren Anweisungen, um eine Automation-Variable zu erstellen, um den Namen der Verfügbarkeitsgruppen bereitzustellen.

### <a name="steps-to-do-a-test-failover"></a>Schritte zum Testfailover

SQL Always On bietet keine native Unterstützung für ein Testfailover. Daher empfehlen wir Folgendes:

1. Richten Sie [Azure Backup](../backup/backup-azure-arm-vms.md) auf dem virtuellen Computer ein, auf dem das Replikat der Verfügbarkeitsgruppe in Azure gehostet wird.

1. Vor dem Auslösen des Testfailovers des Wiederherstellungsplans stellen Sie den virtuellen Computer aus der im vorherigen Schritt erstellten Sicherung wieder her.

    ![Wiederherstellen von Daten aus der Azure-Sicherung](./media/site-recovery-sql/restore-from-backup.png)

1. [Erzwingen Sie ein Quorum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) auf dem virtuellen Computer, der aus einer Sicherung wiederhergestellt wurde.

1. Aktualisieren Sie die IP-Adresse der Listener in eine IP-Adresse, die im Testfailovernetzwerk verfügbar ist.

    ![Aktualisieren Sie die Listener-IP-Adresse](./media/site-recovery-sql/update-listener-ip.png)

1. Schalten Sie die Listener online.

    ![Onlineschalten des Listeners](./media/site-recovery-sql/bring-listener-online.png)

1. Erstellen Sie einen Lastenausgleich mit einer IP-Adresse, die im Front-End-IP-Pool entsprechend jedem Verfügbarkeitsgruppenlistener erstellt wurde und der über einen virtuellen SQL-Computer im Back-End-Pool verfügt.

     ![Erstellen eines Lastenausgleichs: Front-End-IP-Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Erstellen eines Lastenausgleichs: Back-End-Pool](./media/site-recovery-sql/create-load-balancer2.png)

1. Führen Sie ein Testfailover des Wiederherstellungsplans durch.

### <a name="steps-to-do-a-failover"></a>Schritte zum Failover

Sobald Sie das Skript im Wiederherstellungsplan hinzugefügt haben und den Wiederherstellungsplan durch ein Testfailover validiert haben, können Sie das Failover des Wiederherstellungsplans durchführen.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integration in SQL Server Always On für die Replikation an einem sekundären lokalen Standort

Wenn die SQL Server-Instanz Verfügbarkeitsgruppen für Hochverfügbarkeit (oder eine FCI) verwendet, empfiehlt es sich, auch am Wiederherstellungsstandort Verfügbarkeitsgruppen zu verwenden. Diese Empfehlung gilt für Apps, die keine verteilten Transaktionen verwenden.

1. [Konfigurieren Sie Datenbanken](https://msdn.microsoft.com/library/hh213078.aspx) in Verfügbarkeitsgruppen.
1. Erstellen Sie ein virtuelles Netzwerk am sekundären Standort.
1. Richten Sie eine Site-to-Site-VPN-Verbindung zwischen dem virtuellen Netzwerk und dem primären Standort ein.
1. Erstellen Sie einen virtuellen Computer am Wiederherstellungsstandort, und installieren Sie SQL Server.
1. Erweitern Sie die vorhandenen Always On-Verfügbarkeitsgruppen auf die neue SQL Server-VM. Konfigurieren Sie diese SQL Server-Instanz als asynchrone Replikatkopie.
1. Erstellen Sie einen Verfügbarkeitsgruppenlistener, oder aktualisieren Sie den vorhandenen Listener, sodass er den asynchronen virtuellen Replikatcomputer enthält.
1. Stellen Sie sicher, dass die Anwendungsfarm mit dem Listener konfiguriert ist. Sollte die Farm mit dem Namen des Datenbankservers konfiguriert sein, aktualisieren Sie sie so, dass sie den Listener verwendet. Andernfalls muss dieser Schritt nach dem Failover durchgeführt werden.

Für Anwendungen, die verteilte Transaktionen verwenden, empfiehlt sich die Bereitstellung von [Site-to-Site-Replikation für VMware-Server oder physische Server](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Überlegungen zum Wiederherstellungsplan
1. Fügen Sie dieses Beispielskript der VMM-Bibliothek am primären und sekundären Standort hinzu.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Fügen Sie beim Erstellen eines Wiederherstellungsplans für die Anwendung eine vorausgehende Aktion zum geskripteten Schritt der ersten Gruppe hinzu, der das Skript für das Failover von Verfügbarkeitsgruppen aufruft.

## <a name="protect-a-standalone-sql-server"></a>Schützen einer eigenständigen SQL Server-Instanz

In diesem Szenario empfiehlt es sich, den SQL Server-Computer mithilfe der Site Recovery-Replikation zu schützen. Welche Schritte dafür konkret erforderlich sind, hängt davon ab, ob die SQL Server-Instanz ein virtueller Computer oder ein physischer Server ist und ob Sie Azure oder einen sekundären lokalen Standort als Replikationsziel verwenden möchten. Weitere Informationen zu [Site Recovery-Szenarien](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Schützen eines SQL Server-Clusters (Standard Edition/SQL Server 2008 R2)

Bei einem Cluster mit SQL Server Standard Edition oder SQL Server 2008 R2 empfiehlt es sich, SQL Server mit der Site Recovery-Replikation zu schützen.

### <a name="on-premises-to-on-premises"></a>Lokal zu lokal

* Für eine App, die verteilte Transaktionen verwendet, empfiehlt sich die Bereitstellung von [Site Recovery mit SAN-Replikation](site-recovery-vmm-san.md) für eine Hyper-V-Umgebung bzw. von [VMware-Server/physischem Server in VMware](site-recovery-vmware-to-vmware.md) für eine VMware-Umgebung.
* Nutzen Sie für Nicht-DTC-Anwendungen den obigen Ansatz, um den Cluster als eigenständigen Server wiederherzustellen, indem Sie eine lokale DB-Hochsicherheitsspiegelung verwenden.

### <a name="on-premises-to-azure"></a>Lokal zu Azure

Site Recovery unterstützt bei der Replikation in Azure keine Gastcluster. Für die Standard-Edition von SQL Server steht zudem keine kostengünstige Notfallwiederherstellungslösung zur Verfügung. In diesem Szenario empfiehlt es sich, den lokalen SQL Server-Cluster mit einer eigenständigen SQL Server-Instanz zu schützen und die Wiederherstellung in Azure vorzunehmen.

1. Konfigurieren Sie eine zusätzliche eigenständige SQL Server-Instanz am lokalen Standort.
1. Konfigurieren Sie diese Instanz so, dass sie als Spiegelung für die zu schützenden Datenbanken fungiert. Konfigurieren Sie die Spiegelung im Modus für hohe Sicherheit.
1. Konfigurieren Sie Site Recovery am lokalen Standort für [Hyper-V](site-recovery-hyper-v-site-to-azure.md) oder [virtuelle VMware-Computer/physische Server](site-recovery-vmware-to-azure-classic.md).
1. Verwenden Sie die Site Recovery-Replikation, um die neue SQL Server-Instanz zu Azure zu replizieren. Da es sich hierbei um eine Spiegelkopie mit hoher Sicherheit handelt, wird sie mit dem primären Cluster synchronisiert. Bei der Replikation in Azure wird allerdings die Site Recovery-Replikation verwendet.


![Standardcluster](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Überlegungen zum Failback

Bei SQL Server Standard-Clustern ist für das Failback nach einem nicht geplanten Failover eine SQL-Serversicherung und -wiederherstellung der Spiegelinstanz im ursprünglichen Cluster mit Wiederherstellung der Spiegelung erforderlich.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die [Site Recovery-Architektur](site-recovery-components.md).
