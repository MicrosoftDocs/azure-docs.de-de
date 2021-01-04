---
title: Einrichten der Notfallwiederherstellung für SQL Server mit Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung für SQL Server mithilfe von SQL Server und Azure Site Recovery einrichten.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 1b02b089fea7e883bdc6c58c7a2845af12b50a37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011947"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Einrichten der Notfallwiederherstellung für SQL Server

In diesem Artikel wird beschrieben, wie Sie das SQL Server-Back-End einer Anwendung schützen können. Dazu verwenden Sie eine Kombination aus SQL Server-Technologien für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) und [Azure Site Recovery](site-recovery-overview.md).

Bevor Sie beginnen, stellen Sie sicher, dass Sie die SQL Server-Funktionen für die Notfallwiederherstellung kennen. Diese Funktionen umfassen:

* Failoverclustering
* Always On-Verfügbarkeitsgruppen
* Datenbankspiegelung
* Protokollversand
* Aktive Georeplikation
* Autofailover-Gruppen

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Kombinieren von BCDR-Technologien mit Site Recovery

Die Wahl einer BCDR-Technologie für die Wiederherstellung von SQL Server-Instanzen sollte auf Ihren Anforderungen an Recovery Time Objective (RTO) und Recovery Point Objective (RPO) basieren, wie in der folgenden Tabelle beschrieben. Kombinieren Sie Site Recovery mit dem Failovervorgang der ausgewählten Technologie, um die Wiederherstellung Ihrer gesamten Anwendung zu orchestrieren.

Bereitstellungstyp | BCDR-Technologie | Erwartete RTO für SQL Server | Erwartete RPO für SQL Server |
--- | --- | --- | ---
SQL Server auf einem virtuellen IaaS-Computer (Infrastructure-as-a-Service-VM) in Azure oder lokal.| [AlwaysOn-Verfügbarkeitsgruppe](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | Die Zeit, die es dauert, das sekundäre Replikat zum primären zu machen. | Weil die Replikation zum sekundären Replikat asynchron ist, tritt ein gewisser Datenverlust auf.
SQL Server auf einer Azure-IaaS-VM oder auf einem lokalem System.| [Failoverclustering (Always On-FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | Die Zeit, die das Failover zwischen Knoten benötigt. | Da Always On freigegebenen Speicher verwendet, ist dieselbe Ansicht der Speicherinstanz bei einem Failover verfügbar.
SQL Server auf einer Azure-IaaS-VM oder auf einem lokalem System.| [Datenbankspiegelung (Hochleistungsmodus)](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | Die erforderliche Zeit, um den Dienst zu erzwingen, der den Spiegelserver als betriebsbereiten Standbyserver nutzt. | Die Replikation ist asynchron. Bei der Spiegeldatenbank kann im Vergleich zur Prinzipaldatenbank zu etwas Verzögerung auftreten. Die Verzögerung ist in der Regel gering. Sie kann jedoch sehr groß werden, wenn das System des Prinzipals oder Spiegelservers stark ausgelastet ist.<br/><br/>Der Protokollversand kann eine Ergänzung der Datenbankspiegelung darstellen. Es ist eine günstige Alternative zur asynchronen Datenbankspiegelung.
SQL als Platform-as-a-Service (PaaS) in Azure.<br/><br/>Dieser Bereitstellungstyp umfasst Einzeldatenbanken und Pools für elastische Datenbanken. | Aktive Georeplikation | 30 Sekunden nach dem Auslösen des Failovers.<br/><br/>Wenn das Failover für eine sekundäre Datenbank aktiviert ist, werden alle anderen sekundären Datenbanken automatisch mit der neuen primären Datenbank verknüpft. | RPO von fünf Sekunden.<br/><br/>Die aktive Georeplikation verwendet die Always On-Technologie von SQL Server. Sie repliziert asynchron durchgeführte Transaktionen der primären Datenbank zu einer sekundären Datenbank mithilfe der Momentaufnahmenisolation.<br/><br/>Die sekundären Daten haben niemals partielle Transaktionen.
SQL als PaaS ist mit aktiver Georeplikation auf Azure konfiguriert.<br/><br/>Dieser Bereitstellungstyp umfasst verwaltete Instanzen, Pools für elastische Datenbanken und Einzeldatenbanken. | Autofailover-Gruppen | RTO von einer Stunde. | RPO von fünf Sekunden.<br/><br/>Autofailover-Gruppen stellen die Gruppensemantik zusätzlich zur aktiven Georeplikation bereit. Es wird jedoch der gleiche asynchrone Replikationsmechanismus verwendet.
SQL Server auf einer Azure-IaaS-VM oder auf einem lokalem System.| Replikation mit Azure Site Recovery | Die RTO beträgt in der Regel weniger als 15 Minuten. Weitere Informationen finden Sie in der [SLA für Site Recovery](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Eine Stunde für die Anwendungskonsistenz und fünf Minuten für die Absturzkonsistenz. Wenn Sie nach einer niedrigeren RPO suchen, verwenden Sie andere BCDR-Technologien.

> [!NOTE]
> Wenn Sie SQL-Workloads mit Site Recovery schützen, müssen Sie einige wichtige Überlegungen anstellen:
> * Site Recovery ist anwendungsunabhängig. Site Recovery kann jede Version von SQL Server schützen, die auf einem unterstützten Betriebssystem bereitgestellt wird. Weitere Informationen finden Sie in der [Unterstützungsmatrix für die Notfallwiederherstellung](vmware-physical-azure-support-matrix.md#replicated-machines) replizierter Computer.
> * Sie können Site Recovery für eine Bereitstellung unter Azure, Hyper-V, VMware oder in einer physischen Infrastruktur verwenden. Halten Sie sich an die [Anweisungen](#how-to-help-protect-a-sql-server-cluster) am Ende des Artikels, um einen SQL Server-Cluster mit Site Recovery zu schützen.
> * Stellen Sie sicher, dass die Datenänderungsrate auf dem Computer innerhalb der [Site Recovery-Grenzwerte](vmware-physical-azure-support-matrix.md#churn-limits) liegt. Die Änderungsrate wird in pro Sekunde geschriebenen Bytes gemessen. Bei Computern, auf denen Windows ausgeführt wird, können Sie diese Änderungsrate anzeigen, indem Sie im Task-Manager die Registerkarte **Leistung** auswählen. Beachten Sie die Schreibgeschwindigkeit für jeden Datenträger.
> * Site Recovery unterstützt die Replikation von Failoverclusterinstanzen in direkten Speicherplätzen. Weitere Informationen finden Sie unter [Replizieren von virtuellen Azure-Computern, die „Direkte Speicherplätze“ verwenden, in einer anderen Azure-Region](azure-to-azure-how-to-enable-replication-s2d-vms.md).
> 
> Wenn Sie die SQL-Workload zu Azure migrieren, empfiehlt es sich, die [Leistungsrichtlinien für SQL Server auf Azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) anzuwenden.

## <a name="disaster-recovery-of-an-application"></a>Notfallwiederherstellung einer Anwendung

Site Recovery orchestriert das Testfailover und das Failover Ihrer gesamten Anwendung mithilfe von Wiederherstellungsplänen.

Es gibt einige Voraussetzungen, um sicherzustellen, dass Ihr Wiederherstellungsplan vollständig Ihren Bedürfnissen angepasst ist. Für jede Bereitstellung einer SQL Server-Instanz ist normalerweise eine Active Directory-Bereitstellung erforderlich. Und Sie benötigen eine Verbindung für Ihre Logikschicht.

### <a name="step-1-set-up-active-directory"></a>Schritt 1: Einrichten von Active Directory

Richten Sie für den ordnungsgemäßen Betrieb von SQL Server Active Directory am sekundären Wiederherstellungsstandort ein.

* **Kleines Unternehmen**: Sie verfügen über eine kleine Anzahl von Anwendungen und einen einzelnen Domänencontroller für den lokalen Standort. Wenn Sie ein Failover für den gesamten Standort ausführen möchten, verwenden Sie die Site Recovery-Replikation. Dieser Dienst repliziert den Domänencontroller im sekundären Rechenzentrum oder in Azure.
* **Mittleres bis großes Unternehmen**: Möglicherweise müssen Sie zusätzliche Domänencontroller einrichten.
  - Wenn Sie über eine hohe Anzahl von Anwendungen verfügen, eine Active Directory-Gesamtstruktur verwenden und ein anwendungs- oder workloadspezifisches Failover durchführen möchten, richten Sie einen weiteren Domänencontroller im sekundären Rechenzentrum oder in Azure ein.
  -  Wenn Sie für die Wiederherstellung an einem Remotestandort Always On-Verfügbarkeitsgruppen verwenden, richten Sie einen weiteren Domänencontroller am sekundären Standort oder in Azure ein. Dieser Domänencontroller wird für die wiederhergestellte SQL Server-Instanz verwendet.

Die Anweisungen in diesem Artikel setzen voraus, dass am sekundären Standort ein Domänencontroller verfügbar ist. Weitere Informationen finden Sie unter [Einrichten der Notfallwiederherstellung für Active Directory und DNS](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Schritt 2: Sicherstellen der Konnektivität mit anderen Ebenen

Sobald die Datenbankebene in der Azure-Zielregion ausgeführt wird, stellen Sie sicher, dass Konnektivität mit der Anwendungs- und Webebene besteht. Führen Sie vorab die notwendigen Schritte durch, um die Konnektivität mit einem Testfailover zu überprüfen.

Die folgenden Beispiele helfen Ihnen, zu verstehen, wie Sie Anwendungen unter Konnektivitätsaspekten entwickeln können:

* [Entwickeln Sie eine Anwendung für die Cloud-Notfallwiederherstellung](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Notfallwiederherstellungsstrategien für Pool für elastische Datenbanken](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Schritt 3: Interoperabilität mit Always On, aktive Georeplikation und Gruppen für automatisches Failover

Die BCDR-Technologien Always On, aktive Georeplikation und Autofailover-Gruppen haben sekundäre Replikate von SQL Server-Instanzen, die in der Azure-Zielregion ausgeführt werden. Der erste Schritt für das Anwendungsfailover besteht darin, dieses Replikat als primäres Replikat anzugeben. Dieser Schritt setzt voraus, dass Sie bereits einen Domänencontroller im sekundären Rechenzentrum haben. Der Schritt mag nicht notwendig sein, wenn Sie sich für ein Autofailover entscheiden. Erst nachdem das Datenbankfailover abgeschlossen ist, sollten Sie es für Ihre Web- oder Anwendungsebenen ausführen.

> [!NOTE]
> Wenn Sie die SQL-Computer mit Site Recovery geschützt haben, müssen Sie nur eine Wiederherstellungsgruppe mit diesen Computern erstellen und ihre Failover in den Wiederherstellungsplan einfügen.

[Erstellen Sie einen Wiederherstellungsplan](site-recovery-create-recovery-plans.md) mit Anwendungs- und Webebene auf virtuellen Computern. In den folgenden Schritten wird gezeigt, wie ein Failover der Datenbankebene hinzugefügt wird:

1. Importieren Sie die Skripts zum Durchführen eines Failovers für die SQL-Verfügbarkeitsgruppe sowohl auf einen [mit Resource Manager erstellten virtuellen Computer](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) als auch einen [klassischen virtuellen Computer](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importieren Sie die Skripts in Ihr Azure Automation-Konto.

    [![Bild eines „Bereitstellung in Azure“-Logos](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Fügen Sie das ASR-SQL-FailoverAG-Skript als vorausgehende Aktion der ersten Gruppe des Wiederherstellungsplans hinzu.

1. Befolgen Sie die im Skript verfügbaren Anweisungen, um eine Automation-Variable zu erstellen. Diese Variable gibt den Namen der Verfügbarkeitsgruppen an.

### <a name="step-4-conduct-a-test-failover"></a>Schritt 4: Führen Sie ein Testfailover aus

Einige BCDR-Technologien wie SQL Always On unterstützten Testfailover nicht nativ. Wir empfehlen den folgenden Ansatz *nur, wenn Sie ihn in solche Technologien integrieren möchten*.

1. Richten Sie [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) auf dem virtuellen Computer ein, auf dem das Replikat der Verfügbarkeitsgruppe in Azure gehostet wird.

1. Vor dem Auslösen des Testfailovers des Wiederherstellungsplans stellen Sie den virtuellen Computer aus der im vorherigen Schritt erstellten Sicherung wieder her.

    ![Screenshot, der das Fenster für die Wiederherstellung einer Konfiguration aus Azure Backup anzeigt](./media/site-recovery-sql/restore-from-backup.png)

1. [Erzwingen Sie ein Quorum](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) auf dem virtuellen Computer, der aus einer Sicherung wiederhergestellt wurde.

1. Aktualisieren Sie die IP-Adresse des Listeners in eine IP-Adresse, die im Testfailovernetzwerk verfügbar ist.

    ![Screenshot des Regelnfensters und Eigenschaftendialogfelds für IP-Adressen](./media/site-recovery-sql/update-listener-ip.png)

1. Schalten Sie den Listener online.

    ![Screenshot des Fensters mit der Bezeichnung „Content_AG“, das Servernamen und Status anzeigt](./media/site-recovery-sql/bring-listener-online.png)

1. Stellen Sie sicher, dass der Lastenausgleich im Failovernetzwerk über eine IP-Adresse aus dem Front-End-IP-Adresspool verfügt, der jedem Verfügbarkeitsgruppenlistener und dem virtuellen SQL Server-Computer im Back-End-Pool entspricht.

     ![Screenshot des Fensters mit dem Titel „SQL-AlwaysOn-LB – Frontend-IP-Pool“](./media/site-recovery-sql/create-load-balancer1.png)

    ![Screenshot des Fensters mit dem Titel „SQL-AlwaysOn-LB – Backend-IP-Pool“](./media/site-recovery-sql/create-load-balancer2.png)

1. Fügen Sie in späteren Wiederherstellungsgruppen ein Failover Ihrer Anwendungsebene gefolgt von Ihrer Webeebene für diesen Wiederherstellungsplan hinzu.

1. Führen Sie ein Testfailover des Wiederherstellungsplans durch, um das End-to-End-Failover Ihrer Anwendung zu prüfen.

## <a name="steps-to-do-a-failover"></a>Schritte zum Failover

Nachdem Sie das Skript in Schritt 3 hinzugefügt und in Schritt 4 überprüft haben, können Sie ein Failover des Wiederherstellungsplans durchführen, den Sie in Schritt 3 erstellt haben.

Die Failoverschritte für Anwendungs- und Webebene sollten sowohl bei dem Testfailover als auch den Failover-Wiederherstellungsplänen identisch sein.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Schützen eines SQL Server-Clusters

Bei einem Cluster mit SQL Server Standard Edition oder SQL Server 2008 R2 empfiehlt es sich, SQL Server mit der Site Recovery-Replikation zu schützen.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure zu Azure und lokale Bereitstellung auf Azure

Site Recovery unterstützt bei der Replikation in einer Azure-Region keine Gastcluster. Für die SQL Server Standard Edition steht zudem keine kostengünstige Notfallwiederherstellungslösung zur Verfügung. In diesem Szenario empfiehlt es sich, den SQL Server-Cluster mit einer eigenständigen SQL Server-Instanz am primären Standort zu schützen und die Wiederherstellung am sekundären Standort vorzunehmen.

1. Konfigurieren Sie eine zusätzliche eigenständige SQL Server-Instanz in der primären Azure-Region oder an einem lokalen Standort.

1. Konfigurieren Sie diese Instanz so, dass sie als Spiegelung für die zu schützenden Datenbanken fungiert. Konfigurieren Sie die Spiegelung im Modus für hohe Sicherheit.

1. Konfigurieren Sie Site Recovery am primären Standort für [Azure-](azure-to-azure-tutorial-enable-replication.md), [Hyper-V-](./hyper-v-azure-tutorial.md) oder [VMware-VMs und physische Server](./vmware-azure-tutorial.md).

1. Verwenden Sie die Site Recovery-Replikation, um die neue SQL Server-Instanz zum sekundären Standort zu replizieren. Da es sich hierbei um eine Spiegelkopie mit hoher Sicherheit handelt, wird sie mit dem primären Cluster synchronisiert. Bei der Replikation wird allerdings die Site Recovery-Replikation verwendet.

   ![Abbildung eines Standardclusters, die die Beziehung und den Ablauf zwischen einem primären Standort, Site Recovery und Azure zeigt](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Überlegungen zum Failback

Bei SQL Server Standard-Clustern erfordert das Failback nach einem nicht geplanten Failover eine SQL Server-Sicherung und -Wiederherstellung. Dieser Vorgang wird von der Spiegelinstanz zum ursprünglichen Cluster durchgeführt, wobei die Spiegelung wiederhergestellt wird.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Wie wird SQL Server bei der Verwendung mit Site Recovery lizenziert?

Die Site Recovery-Replikation für SQL Server wird unter dem Vorteil der Notfallwiederherstellung von Software Assurance abgedeckt. Diese Abdeckung gilt für alle Site Recovery-Szenarien: lokal für die Azure-Notfallwiederherstellung und regionsübergreifend für die Azure-IaaS-Notfallwiederherstellung. Weitere Informationen finden Sie unter [Azure Site Recovery-Preise](https://azure.microsoft.com/pricing/details/site-recovery/).

### <a name="will-site-recovery-support-my-sql-server-version"></a>Unterstützt Site Recovery meine SQL Server-Version?

Site Recovery ist anwendungsunabhängig. Site Recovery kann jede Version von SQL Server schützen, die auf einem unterstützten Betriebssystem bereitgestellt wird. Weitere Informationen finden Sie in der [Unterstützungsmatrix für die Notfallwiederherstellung](vmware-physical-azure-support-matrix.md#replicated-machines) replizierter Computer.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Site Recovery-Architektur](./azure-to-azure-architecture.md).
* Erfahren Sie für SQL Server in Azure mehr über [hochverfügbare Lösungen](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) für die Wiederherstellung in einer sekundären Azure-Region.
* Erfahren Sie für SQL-Datenbank mehr über Optionen für [Geschäftskontinuität](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) und [Hochverfügbarkeit](../azure-sql/database/high-availability-sla.md) für die Wiederherstellung in sekundären Azure-Regionen.
* Erfahren Sie für SQL Server-Computer an lokalen Standorten mehr über die [Hochverfügbarkeitsoptionen](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) für die Wiederherstellung in Azure Virtual Machines.