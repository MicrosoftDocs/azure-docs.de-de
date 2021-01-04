---
title: Failback von virtuellen VMware-Computern/physischen Servern von Azure mit Azure Site Recovery
description: Hier erfahren Sie, wie Sie während der Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure nach dem Failover in Azure ein Failback auf den lokalen Standort ausführen.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004193"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Failback von VMware-VMs zum lokalen Standort

In diesem Artikel wird beschrieben, wie Sie mit [Azure Site Recovery](site-recovery-overview.md) nach einem [Failover](site-recovery-failover.md) von lokalen VMs zu Azure ein Failback für virtuelle Azure-Computer durchführen. Nach dem Failback zum lokalen Standort aktivieren Sie die Replikation, damit die lokalen VMs nach Azure repliziert werden.

## <a name="before-you-start"></a>Vorbereitung

1. Informieren Sie sich über das [VMware-Failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Vergewissern Sie sich, dass Sie die Schritte zum [Vorbereiten des Failbacks](vmware-azure-prepare-failback.md) durchgelesen und durchgeführt haben sowie alle erforderlichen Komponenten bereitgestellt wurden. Zu den Komponenten gehören ein Prozessserver in Azure, ein lokaler Masterzielserver und eine Site-to-Site-VPN-Verbindung (oder privates ExpressRoute-Peering) für das Failback.
3. Vergewissern Sie sich, dass die [Anforderungen](vmware-azure-reprotect.md#before-you-begin) für den erneuten Schutz und das Failback erfüllt sind und Sie das [erneute Schützen von virtuellen Azure-Computern aktiviert](vmware-azure-reprotect.md#enable-reprotection) haben, sodass diese von Azure an den lokalen Standort repliziert werden. Virtuelle Computer müssen sich in einem replizierten Zustand befinden, damit ein Failback möglich ist.




## <a name="run-a-failover-to-fail-back"></a>Ausführen eines Failovers für ein Failback

1. Vergewissern Sie sich, dass Azure-VMs erneut geschützt werden und an den lokalen Standort repliziert werden.
    - Ein virtueller Computer benötigt mindestens einen Wiederherstellungspunkt, damit ein Failback möglich ist.
    - Wenn Sie ein Failback für einen Wiederherstellungsplan durchführen, müssen alle Computer im Plan über mindestens einen Wiederherstellungspunkt verfügen.
2. Wählen Sie im Tresor **Replizierte Elemente** und dann die VM aus. Klicken Sie mit der rechten Maustaste auf die VM und wählen Sie **Nicht geplantes Failover** aus.
3. Überprüfen Sie in **Failover bestätigen** die Failoverrichtung (von Azure).
4. Wählen Sie den Wiederherstellungspunkt aus, den Sie für das Failover verwenden möchten.
    - Es wird empfohlen, den **neuesten** Wiederherstellungspunkt zu verwenden. Der App-konsistente Punkt liegt vor dem aktuellen Zeitpunkt und verursacht einige Datenverluste.
    - Der **neueste** Wiederherstellungspunkt ist ein absturzkonsistenter Wiederherstellungspunkt.
    - Bei Auswahl des **neuesten** Wiederherstellungspunkts wird ein Failover der VM zum letzten verfügbaren Zeitpunkt durchgeführt. Falls in einem Wiederherstellungsplan eine Replikationsgruppe für Multi-VM-Konsistenz enthalten ist, wird für jede VM in der Gruppe ein Failover zum letzten unabhängigen Zeitpunkt durchgeführt.
    - Wenn Sie einen App-konsistenten Wiederherstellungspunkt verwenden, wird für jede VM ein Failover zum letzten verfügbaren Punkt durchgeführt. Wenn ein Wiederherstellungsplan eine Replikationsgruppe aufweist, erfolgt die Wiederherstellung für jede Replikationsgruppe zum allgemein verfügbaren Wiederherstellungspunkt.
5. Das Failover beginnt. Site Recovery fährt die virtuellen Azure-Computer herunter.
6. Überprüfen Sie nach Abschluss des Failovers, ob alles wie erwartet funktioniert. Vergewissern Sie sich, dass die Azure VMs heruntergefahren wurden. 
7. Klicken Sie nach der Überprüfung mit der rechten Maustaste auf die VM und wählen Sie **Commit** aus, um den Failover-Prozess fertig zu stellen. Durch das Committen wird die Azure-VM, für die das Failover durchgeführt wurde, entfernt. 

> [!NOTE]
> Bei Windows-VMs deaktiviert Site Recovery die VMware-Tools während des Failovers. Beim Failback der Windows-VM werden die VMware-Tools wieder aktiviert. 




## <a name="reprotect-from-on-premises-to-azure"></a>Erneutes Schützen von einem lokalen Standort nach Azure

Nach dem Committen des Failbacks werden die Azure VMs gelöscht. Die VM befindet sich wieder am lokalen Standort, ist aber nicht geschützt. Gehen Sie folgendermaßen vor, um die Replikation der VMs nach Azure erneut zu starten:

1. Wählen Sie im Tresor **Replizierte Elemente**, dann die VMs, für die das Failback durchgeführt wurde, und anschließend **Erneut schützen** aus.
2. Geben Sie den Prozessserver an, der zum Senden von Daten zurück an Azure verwendet wird.
3. Wählen Sie **OK** aus, um den Auftrag zum erneuten Schützen zu starten.

> [!NOTE]
> Nach dem Start einer lokalen VM kann es bis zu 15 Minuten dauern, bis der Agent wieder auf dem Konfigurationsserver registriert wurde. Während dieses Zeitraums schlägt das erneute Schützen fehl, und eine zurückgegebene Fehlermeldung gibt an, dass der Agent nicht installiert ist. Warten Sie in diesem Fall einige Minuten, und führen Sie dann das erneute Schützen durch.

## <a name="next-steps"></a>Nächste Schritte

Nachdem der Auftrag zum erneuten Schützen abgeschlossen wurde, wird der lokale virtuelle Computer nach Azure repliziert. Bei Bedarf können Sie [ein weiteres Failover](site-recovery-failover.md) zu Azure ausführen.

