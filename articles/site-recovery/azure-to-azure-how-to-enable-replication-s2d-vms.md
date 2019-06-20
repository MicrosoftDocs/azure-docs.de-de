---
title: Konfigurieren der Replikation für virtuelle Computer mit „Direkte Speicherplätze“ (S2D) in Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die Replikation virtueller Computer mit S2D zwischen Azure-Regionen mithilfe von Site Recovery konfigurieren.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790299"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replizieren von virtuellen Azure-Computern, die „Direkte Speicherplätze“ verwenden, in einer anderen Azure-Region

In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung von virtuellen Azure-Computern aktivieren, auf denen „Direkte Speicherplätze“ ausgeführt wird.

>[!NOTE]
>Für Cluster mit „Direkte Speicherplätze“ werden nur absturzkonsistente Wiederherstellungspunkte unterstützt.
>

## <a name="introduction"></a>Einführung 
[Direkte Speicherplätze (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) ist ein softwaredefinierter Speicher und bietet eine Möglichkeit zum Erstellen von [Gastclustern](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) in Azure.  Ein Gastcluster in Microsoft Azure ist ein Failovercluster, der virtuelle IaaS-Cluster beinhaltet. Für gehostete VM-Workloads kann ein Failover in den Gastclustern ausgeführt werden, sodass eine SLA mit einer höheren Verfügbarkeit für Anwendungen erreicht werden kann, als diese mit einem einzelnen virtuellen Azure-Computer möglich ist. Dies ist nützlich in Szenarien, in denen ein virtueller Computer eine kritische Anwendung wie SQL oder Scale Out File Server hostet.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Notfallwiederherstellung für virtuelle Azure-Computer mit „Direkte Speicherplätze“
In einem typischen Szenario verfügen Sie über einen VM-Gastcluster in Azure für eine höhere Resilienz einer Anwendung, z.b. Scale Out File Server. Obwohl dies eine höhere Verfügbarkeit der Anwendung ermöglicht, möchten Sie diese Anwendung dennoch mithilfe von Site Recovery gegen alle Ausfälle auf Regionsebene schützen. Mit Site Recovery werden die Daten von einer Region in einer anderen Azure-Region repliziert, und der Cluster wird bei einem Ausfall in der Notfallwiederherstellungsregion verwendet.

In der folgenden Abbildung ist der Failovercluster mit zwei virtuellen Azure-Computern unter Verwendung von „Direkte Speicherplätze“ dargestellt.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Zwei virtuelle Azure-Computer in einem Windows-Failovercluster und jeder virtuelle Computer haben zwei oder mehr Datenträger.
- S2D synchronisiert die Daten auf dem Datenträger und stellt den synchronisierten Speicher als Speicherpool dar.
- Der Speicherpool stellt ein freigegebenes Clustervolume (CSV) für den Failovercluster dar.
- Der Failovercluster verwendet das CSV für die Datenträger.

**Überlegungen zur Notfallwiederherstellung**

1. Wenn Sie einen [Cloudzeugen](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) für den Cluster einrichten, behalten Sie den Zeugen in der Notfallwiederherstellungsregion bei.
2. Wenn Sie ein Failover der virtuellen Computer in das Subnetz in der Notfallwiederherstellungsregion durchführen, die sich von der Quellregion unterscheidet, muss die IP-Adresse des Clusters nach dem Failover geändert werden.  Um die IP-Adresse des Clusters zu ändern, müssen Sie das [Skript für einen ASR-Wiederherstellungsplan](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation) verwenden.</br>
[Beispielskript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) zum Ausführen des Befehls im virtuellen Computer unter Verwendung der benutzerdefinierten Skripterweiterung. 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Aktivieren von Site Recovery für den S2D-Cluster:

1. Klicken Sie im Recovery Services-Tresor auf „+Replizieren“.
1. Wählen Sie alle Knoten im Cluster aus, und legen Sie sie als [Multi-VM-Konsistenzgruppe](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency) fest.
1. Wählen Sie die Replikationsrichtlinie mit deaktivierter Anwendungskonsistenz aus (es ist nur Unterstützung für Absturzkonsistenz verfügbar).
1. Aktivieren Sie die Replikation.

   ![storagespacesdirect protection](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Wenn Sie zu den replizierten Elementen wechseln, wird der Status der beiden virtuellen Computer angezeigt. 
3. Beide virtuellen Computer werden geschützt und werden auch als Teil der Multi-VM-Konsistenzgruppe angezeigt.

   ![storagespacesdirect protection](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans
Ein Wiederherstellungsplan unterstützt die Sequenzierung der verschiedenen Ebenen in einer Anwendung mit mehreren Ebenen während eines Failovers. Die Sequenzierung trägt zur Wahrung der Anwendungskonsistenz bei. Beim Erstellen eines Wiederherstellungsplans für eine Webanwendung mit mehreren Ebenen führen Sie die unter [Erstellen eines Wiederherstellungsplans mit Site Recovery](site-recovery-create-recovery-plans.md) beschriebenen Schritte aus.

### <a name="adding-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen

1.  Erstellen Sie einen Wiederherstellungsplan durch Hinzufügen der virtuellen Computer.
2.  Klicken Sie auf „Anpassen“, um die virtuellen Computer zu gruppieren. Standardmäßig gehören alle virtuellen Computer der „Gruppe 1“ an.


### <a name="add-scripts-to-the-recovery-plan"></a>Hinzufügen von Skripts zum Wiederherstellungsplan
Für die korrekte Funktionsweise Ihrer Anwendungen kann es erforderlich sein, nach dem Failover bzw. während eines Testfailovers einige Vorgänge auf den virtuellen Azure-Computern durchzuführen. Sie können einige Vorgänge nach einem Failover automatisieren. Hier werden beispielsweise Load Balancer hinzugefügt und die IP-Adresse des Clusters geändert.


### <a name="failover-of-the-virtual-machines"></a>Failover der virtuellen Computer 
Für beide Knoten der virtuellen Computer muss unter Verwendung des [ASR-Wiederherstellungsplans](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) ein Failover ausgeführt werden. 

![storagespacesdirect protection](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers
1.  Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2.  Wählen Sie den erstellten Wiederherstellungsplan aus.
3.  Wählen Sie **Testfailover** aus.
4.  Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um den Prozess für das Testfailover zu starten.
5.  Wenn die sekundäre Umgebung aktiv ist, können Sie Überprüfungen durchführen.
6.  Klicken Sie nach Abschluss der Überprüfungen auf **Testfailover bereinigen**, um die Failoverumgebung zu bereinigen.

Weitere Informationen finden Sie unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ausführen eines Failovers

1.  Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2.  Wählen Sie den Wiederherstellungsplan aus, den Sie für die SAP-Anwendungen erstellt haben.
3.  Wählen Sie **Failover** aus.
4.  Wählen Sie den Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

Weitere Informationen finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) zum Ausführen von Failbacks.
