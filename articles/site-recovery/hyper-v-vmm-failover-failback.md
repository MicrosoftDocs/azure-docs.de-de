---
title: Failover und Failback von Hyper-V-VMs, die während der Notfallwiederherstellung mit Azure Site Recovery in ein sekundäres Rechenzentrum repliziert werden | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie während der Notfallwiederherstellung mit Azure Site Recovery ein Failover von Hyper-V-VMs zu Ihrem sekundären lokalen Standort und ein Failback zum primären Standort ausführen.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 39b2e4f37abe77439410fa4a83e06a0ca7941787
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397998"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Failover und Failback von Hyper-V-VMs, die nach Ihrem sekundären lokalen Standort repliziert werden

Der [Azure Site Recovery](site-recovery-overview.md)-Dienst verwaltet und koordiniert Replikation, Failover und Failback von lokalen Computern sowie virtuellen Azure-Computern (VMs).

In diesem Artikel wird beschrieben, wie ein Failover einer Hyper-V-VM, die in einer System Center Virtual Machine Manager-Cloud (VMM) verwaltet wird, auf einen sekundären VMM-Standort ausgeführt wird. Nach dem Failover erfolgt ein Failback zum lokalen Standort, wenn er verfügbar ist. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Ausführen eines Failovers einer Hyper-V-VM aus einer primären VMM-Cloud auf eine sekundäre VMM-Cloud.
> * Erneutes Schützen vom sekundären Standort zum primären und Ausführen des Failbacks
> * Optionales Starten der Replikation vom primären zurück nach dem sekundären Standort

## <a name="failover-and-failback"></a>Failover und Failback

Failover und Failback weisen drei Phasen auf:

1. **Failover zum sekundären Standort:** Ausführen eines Failovers der Computer vom primären zum sekundären Standort.
2. **Failover vom sekundären Standort:** Replizieren der VMs vom sekundären zum primären Standort und Ausführen eines geplanten Failovers zum Ausführen eines Failbacks.
3. Starten Sie nach dem geplanten Failover optional erneut die Replikation vom primären nach dem sekundären Standort.


## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie unbedingt eine [Notfallwiederherstellungs-Übung](hyper-v-vmm-test-failover.md) durch, um zu überprüfen, ob alles wie erwartet funktioniert.
- Um das Failback abzuschließen, stellen Sie sicher, dass die primären und sekundären VMM-Server mit Site Recovery verbunden sind.



## <a name="run-a-failover-from-primary-to-secondary"></a>Ausführen eines Failovers vom primären auf den sekundären Standort

Sie können ein reguläres oder geplantes Failover für virtuelle Hyper-V-Computer ausführen.

- Verwenden Sie ein reguläres Failover für unerwartete Ausfälle. Beim Ausführen dieses Failovers erstellt Site Recovery einen virtuellen Computer am sekundären Standort und fährt ihn hoch. Datenverlust kann in Abhängigkeit von ausstehenden Daten auftreten, die nicht synchronisiert wurden.
- Ein geplantes Failover kann zu Wartungszwecken oder bei erwarteten Ausfällen verwendet werden. Mit dieser Option vermeiden Sie jeglichen Datenverlust. Wenn ein geplantes Failover ausgelöst wird, werden die Quell-VMs heruntergefahren. Nicht synchronisierte Daten werden synchronisiert, und das Failover wird ausgelöst. 
- 
  In diesem Verfahren erfahren Sie, wie Sie ein reguläres Failover durchführen.


1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf VM > **Failover**.
1. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, wenn Site Recovery versuchen soll, Quell-VMs herunterzufahren, bevor das Failover ausgelöst wird. Site Recovery wird vor dem Auslösen des Failovers auch versuchen, lokale Daten zu synchronisieren, die noch nicht an den sekundären Standort gesendet wurden. Beachten Sie, dass das Failover auch dann fortgesetzt wird, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
2. Sie sollten nun die VM in der sekundären VMM-Cloud sehen.
3. Nachdem Sie die VM überprüft haben, **committen** Sie das Failover. Dadurch werden alle verfügbaren Wiederherstellungspunkte gelöscht.

> [!WARNING]
> **Brechen Sie ein Failover in Bearbeitung nicht ab:** Bevor das Failover gestartet wird, wird die VM-Replikation beendet. Wenn Sie ein Failover in Bearbeitung abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.  


## <a name="reverse-replicate-and-failover"></a>„Umgekehrt replizieren“ und „Failover“

Starten Sie die Replikation vom sekundären nach dem primären Standort, und führen Sie ein Failback auf den primären Standort aus. Sobald die virtuellen Computer wieder am primären Standort ausgeführt werden, können Sie sie im sekundären Standort replizieren.  

 
1. Klicken Sie auf den virtuellen Computer > **Umgekehrt replizieren**.
2. Sobald der Auftrag abgeschlossen ist, klicken Sie auf den virtuellen Computer > **Failover**, überprüfen Sie die Failoverrichtung (von der sekundären VMM-Cloud), und wählen Sie den Quell- und den Zielspeicherort aus. 
4. Initiieren Sie das Failover. Der Fortschritt des Failovers wird auf der Registerkarte **Aufträge** angezeigt.
5. Überprüfen Sie, ob der virtuelle Computer in der primären VMM-Cloud verfügbar ist.
6. Wenn Sie das Replizieren des primären virtuellen Computers zurück nach dem sekundären Standort starten möchten, klicken Sie auf **Umgekehrt replizieren**.

## <a name="next-steps"></a>Nächste Schritte
[Verwenden Sie den Schritt](hyper-v-vmm-disaster-recovery.md) zum Replizieren von virtuellen Hyper-V-Computern an einem sekundären Standort.
