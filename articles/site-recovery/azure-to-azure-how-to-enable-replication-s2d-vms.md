---
title: Replizieren von Azure-VMs mit direkten Speicherplätzen mit Azure Site Recovery
description: Erfahren Sie, wie Azure-VMs mit direkten Speicherplätzen mithilfe von Azure Site Recovery repliziert werden.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424823"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replizieren von virtuellen Azure-Computern mit direkten Speicherplätzen in eine andere Region

In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung von virtuellen Azure-Computern aktivieren, auf denen „Direkte Speicherplätze“ ausgeführt wird.

>[!NOTE]
>Für Cluster mit „Direkte Speicherplätze“ werden nur absturzkonsistente Wiederherstellungspunkte unterstützt.
>

[Direkte Speicherplätze (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) ist softwaredefinierter Speicher und bietet eine Möglichkeit zum Erstellen von [Gastclustern](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) in Azure.  Ein Gastcluster in Microsoft Azure ist ein Failovercluster, der virtuelle IaaS-Cluster beinhaltet. Für gehostete VM-Workloads kann ein Failover in Gastclustern ausgeführt werden, sodass eine SLA mit einer höheren Verfügbarkeit für Anwendungen erreicht werden kann, als mit einer einzelnen Azure-VM möglich ist. Dies ist nützlich in Szenarien, in denen eine VM eine kritische Anwendung wie SQL oder einen Dateiserver mit horizontaler Skalierung hostet.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Notfallwiederherstellung mit direkten Speicherplätzen

In einem typischen Szenario verfügen Sie über einen VM-Gastcluster in Azure für eine höhere Resilienz einer Anwendung, z. B. Dateiserver mit horizontaler Skalierung. Obwohl dies eine höhere Verfügbarkeit der Anwendung ermöglicht, möchten Sie diese Anwendung dennoch mithilfe von Site Recovery gegen alle Ausfälle auf Regionsebene schützen. Mit Site Recovery werden die Daten von einer Region in einer anderen Azure-Region repliziert, und der Cluster wird bei einem Ausfall in der Notfallwiederherstellungsregion verwendet.

Die folgende Abbildung zeigt einen Azure-VM-Failovercluster mit zwei Knoten, der direkte Speicherplätze verwendet.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Zwei virtuelle Azure-Computer in einem Windows-Failovercluster und jeder virtuelle Computer haben zwei oder mehr Datenträger.
- S2D synchronisiert die Daten auf dem Datenträger und stellt den synchronisierten Speicher als Speicherpool dar.
- Der Speicherpool stellt ein freigegebenes Clustervolume (CSV) für den Failovercluster dar.
- Der Failovercluster verwendet das CSV für die Datenträger.

**Überlegungen zur Notfallwiederherstellung**

1. Wenn Sie einen [Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) für den Cluster einrichten, behalten Sie den Zeugen in der Notfallwiederherstellungsregion bei.
2. Wenn Sie ein Failover der virtuellen Computer in das Subnetz in der Notfallwiederherstellungsregion durchführen, die sich von der Quellregion unterscheidet, muss die IP-Adresse des Clusters nach dem Failover geändert werden.  Um die IP-Adresse des Clusters zu ändern, müssen Sie das [Skript für einen Site Recovery-Wiederherstellungsplan](./site-recovery-runbook-automation.md) verwenden.</br>
[Beispielskript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) zum Ausführen des Befehls im virtuellen Computer unter Verwendung der benutzerdefinierten Skripterweiterung. 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Aktivieren von Site Recovery für den S2D-Cluster:

1. Klicken Sie im Recovery Services-Tresor auf „+Replizieren“.
1. Wählen Sie alle Knoten im Cluster aus, und legen Sie sie als [Multi-VM-Konsistenzgruppe](./azure-to-azure-common-questions.md#multi-vm-consistency) fest.
1. Wählen Sie die Replikationsrichtlinie mit deaktivierter Anwendungskonsistenz aus (es ist nur Unterstützung für Absturzkonsistenz verfügbar).
1. Aktivieren Sie die Replikation.

   ![Screenshot, der zeigt, wo die Replikationseinstellungen konfiguriert werden](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Wenn Sie zu den replizierten Elementen wechseln, wird der Status der beiden virtuellen Computer angezeigt.
3. Beide virtuellen Computer werden geschützt und werden auch als Teil der Multi-VM-Konsistenzgruppe angezeigt.

   ![Screenshot: Die virtuellen Computer werden geschützt und sind Teil der Multi-VM-Konsistenzgruppe.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans
Ein Wiederherstellungsplan unterstützt die Sequenzierung der verschiedenen Ebenen in einer Anwendung mit mehreren Ebenen während eines Failovers. Die Sequenzierung trägt zur Wahrung der Anwendungskonsistenz bei. Beim Erstellen eines Wiederherstellungsplans für eine Webanwendung mit mehreren Ebenen führen Sie die unter [Erstellen eines Wiederherstellungsplans mit Site Recovery](site-recovery-create-recovery-plans.md) beschriebenen Schritte aus.

### <a name="adding-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen

1.  Erstellen Sie einen Wiederherstellungsplan durch Hinzufügen der virtuellen Computer.
2.  Klicken Sie auf „Anpassen“, um die virtuellen Computer zu gruppieren. Standardmäßig gehören alle virtuellen Computer der „Gruppe 1“ an.


### <a name="add-scripts-to-the-recovery-plan"></a>Hinzufügen von Skripts zum Wiederherstellungsplan
Für die korrekte Funktionsweise Ihrer Anwendungen kann es erforderlich sein, nach dem Failover bzw. während eines Testfailovers einige Vorgänge auf den virtuellen Azure-Computern durchzuführen. Sie können einige Vorgänge nach einem Failover automatisieren. Hier werden beispielsweise Load Balancer hinzugefügt, und hier wird die IP-Adresse des Clusters geändert.


### <a name="failover-of-the-virtual-machines"></a>Failover der virtuellen Computer 
Für beide Knoten der VMs muss ein Failover mithilfe des Site Recovery-[Wiederherstellungsplans](./site-recovery-create-recovery-plans.md) ausgeführt werden. 

![storagespacesdirect protection](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers
1.  Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2.  Wählen Sie den erstellten Wiederherstellungsplan aus.
3.  Wählen Sie **Testfailover** aus.
4.  Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um den Prozess für das Testfailover zu starten.
5.  Wenn die sekundäre Umgebung aktiv ist, können Sie Überprüfungen durchführen.
6.  Klicken Sie nach Abschluss der Überprüfungen auf **Testfailover bereinigen** , um die Failoverumgebung zu bereinigen.

Weitere Informationen finden Sie unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ausführen eines Failovers

1.  Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2.  Wählen Sie den Wiederherstellungsplan aus, den Sie für die SAP-Anwendungen erstellt haben.
3.  Wählen Sie **Failover** aus.
4.  Wählen Sie den Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

Weitere Informationen finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen](./azure-to-azure-tutorial-failover-failback.md) zum Ausführen von Failbacks.
