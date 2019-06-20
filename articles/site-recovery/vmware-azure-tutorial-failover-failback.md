---
title: Ausführen eines Failovers und Failbacks für virtuelle VMware-Computer und physische Server während der Notfallwiederherstellung in Azure mit Site Recovery | Microsoft-Dokumentation
description: Informationen zum Ausführen eines Failovers für virtuelle VMware-Computer und physische Server auf Azure und eines Failbacks auf den lokalen Standort während der Notfallwiederherstellung in Azure mithilfe von Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 854e09600440c28f7505a016566ea0f05e994f5f
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418314"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Ausführen eines Failovers und Failbacks für virtuelle VMware-Computer

In diesem Artikel erfahren Sie, wie Sie für einen lokalen virtuellen VMware-Computer (VM) ein Failover auf [Azure Site Recovery](site-recovery-overview.md) ausführen.

Dies ist das fünfte Tutorial einer Reihe zur Einrichtung der Notfallwiederherstellung in Azure für lokale Computer.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der VMware-VM-Eigenschaften, um die Konformität mit Azure-Anforderungen zu bestätigen
> * Ausführen eines Failovers auf Azure

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Wenn Sie sich detailliert über Failover informieren möchten, lesen Sie [Ausführen eines Failovers für virtuelle Computer und physische Server](site-recovery-failover.md).

## <a name="before-you-start"></a>Vorbereitung

Absolvieren Sie die vorherigen Tutorials:

1. Vergewissern Sie sich, dass Sie [Azure ordnungsgemäß eingerichtet haben](tutorial-prepare-azure.md) (für die lokale Notfallwiederherstellung virtueller VMware-Computer, virtueller Hyper-V-Computer und physischer Computer in Azure).
2. Bereiten Sie Ihre lokale [VMware](vmware-azure-tutorial-prepare-on-premises.md)- oder [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-Umgebung für die Notfallwiederherstellung vor. Falls Sie die Notfallwiederherstellung für physische Server einrichten möchten, sehen Sie sich die [Unterstützungsmatrix](vmware-physical-secondary-support-matrix.md) an.
3. Richten Sie die Notfallwiederherstellung für [virtuelle VMware-Computer](vmware-azure-tutorial.md), für [virtuelle Hyper-V-Computer](hyper-v-azure-tutorial.md) oder für [physische Computer](physical-azure-disaster-recovery.md) ein.
4. Führen Sie ein [Notfallwiederherstellungsverfahren](tutorial-dr-drill-azure.md) durch, um zu überprüfen, ob alles wie erwartet funktioniert.

## <a name="failover-and-failback"></a>Failover und Failback

Failover und Failback weisen vier Phasen auf:

1. **Failover auf Azure:** Bei einem Ausfall Ihres lokalen primären Standorts wird für Computer ein Failover auf Azure durchgeführt. Nach dem Failover werden auf der Grundlage der replizierten Daten virtuelle Azure-Computer erstellt.
2. **Erneutes Schützen der virtuellen Azure-Computer:** In Azure werden die virtuellen Azure-Computer erneut geschützt, damit sie wieder auf lokalen virtuellen VMware-Computern repliziert werden können. Der lokale virtuelle Computer wird während des erneuten Schützens deaktiviert, um Datenkonsistenz sicherzustellen.
3. **Failover auf lokalen Standort:** Wenn Ihr lokaler Standort wieder verfügbar ist, wird mithilfe eines Failovers ein Failback von Azure durchgeführt.
4. **Erneutes Schützen lokaler virtueller Computer:** Nach dem Failback der Daten werden die lokalen virtuellen Computer, auf die Sie das Failback durchgeführt haben, erneut geschützt und wieder in Azure repliziert.

## <a name="verify-vm-properties"></a>Überprüfen von VM-Eigenschaften

Überprüfen Sie vor dem Ausführen eines Failovers die VM-Eigenschaften, um sich zu vergewissern, dass die VMs den [Azure-Anforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) entsprechen.

Gehen Sie zum Überprüfen der Eigenschaften wie folgt vor:

1. Wählen Sie in **Geschützte Elemente** **Replizierte Elemente** aus, und wählen Sie dann die VM aus, die Sie überprüfen möchten.

2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Informationen zu virtuellen Computern, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Wählen Sie **Eigenschaften** aus, um weitere Details anzuzeigen.

3. Unter **Compute und Netzwerk** können Sie diese Einstellungen nach Bedarf ändern:
    * Azure-Name
    * Ressourcengruppe
    * Zielgröße
    * [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md)
    * Einstellungen für verwaltete Datenträger

4. Sie können Netzwerkeinstellungen anzeigen und ändern, darunter:

    * Das Netzwerk und Subnetz, in dem die Azure-VM nach einem Failover gespeichert werden soll.
    * Die IP-Adresse, die ihr zugewiesen wird.

5. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem virtuellen Computer.

## <a name="run-a-failover-to-azure"></a>Ausführen eines Failovers auf Azure

1. Wählen Sie unter **Einstellungen** > **Replizierte Elemente** den virtuellen Computer aus, für den das Failover ausgeführt werden soll, und wählen Sie dann **Failover** aus.
2. Wählen Sie unter **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
   * **Letzter Zeitpunkt**: Mit dieser Option werden zuerst alle an Site Recovery gesendeten Daten verarbeitet. Sie bietet die niedrigste RPO (Recovery Point Objective), da die nach dem Failover erstellte Azure-VM über alle Daten verfügt, die bei Auslösung des Failovers zu Site Recovery repliziert wurden.
   * **Letzte Verarbeitung**: Mit dieser Option wird das Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Diese Option bietet eine niedrige Recovery Time Objective (RTO), da keine Zeit für die Verarbeitung unverarbeiteter Daten aufgewendet wird.
   * **Letzter anwendungskonsistenter Zeitpunkt**: Mit dieser Option wird ein Failover des virtuellen Computers auf den letzten anwendungskonsistenten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde.
   * **Benutzerdefiniert**: Diese Option gestattet Ihnen das Angeben eines Wiederherstellungspunkts.

3. Wählen Sie **Der Computer wird vor Beginn des Failovers heruntergefahren** aus, um zu versuchen, virtuelle Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.

In einigen Szenarien erfordert ein Failover zusätzliche Verarbeitungsschritte, die etwa 8 bis 10 Minuten dauern können. Bei folgenden Computern kann das Testfailover länger dauern:

* Virtuelle VMware-Computer, auf denen eine Mobility Service-Version vor 9.8 ausgeführt wird
* Physische Server
* VMware-Linux-VMs
* Virtuelle Hyper-V-Computer, die als physische Server geschützt werden
* Virtuelle VMware-Computer mit nicht aktiviertem DHCP-Dienst
* Virtuelle VMware-Computer ohne die folgenden Starttreiber: storvsc, vmbus, storflt, intelide, atapi

> [!WARNING]
> Brechen Sie ein Failover in Bearbeitung nicht ab. Bevor das Failover gestartet wird, wird die VM-Replikation beendet. Wenn Sie ein Failover in Bearbeitung abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.

## <a name="connect-to-failed-over-vm"></a>Herstellen einer Verbindung mit einer VM nach dem Failover

1. Wenn Sie Verbindungen mit Azure-VMs nach dem Failover mithilfe von RDP (Remote Desktop Protocol) und SSH (Secure Shell) herstellen möchten, [überprüfen Sie, ob die Anforderungen erfüllt wurden](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Navigieren Sie nach einem Failover zu dem virtuellen Computer, und stellen Sie zur Überprüfung eine [Verbindung](../virtual-machines/windows/connect-logon.md) her.
3. Verwenden Sie **Wiederherstellungspunkt ändern**, falls Sie nach dem Failover einen anderen Wiederherstellungspunkt verwenden möchten. Wenn Sie das Failover im nächsten Schritt committen, steht diese Option nicht mehr zur Verfügung.
4. Wählen Sie nach der Überprüfung **Committen** aus, um den Wiederherstellungspunkt des virtuellen Computers nach dem Failover fertig zu stellen.
5. Nach dem Committen werden alle anderen verfügbaren Wiederherstellungspunkte gelöscht. Mit diesem Schritt wird das Failover abgeschlossen.

>[!TIP]
> Sollten nach dem Failover Verbindungsprobleme auftreten, lesen Sie das [Handbuch zur Problembehandlung](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Failover müssen die virtuellen Azure-Computer in der lokalen Umgebung erneut geschützt werden. Sobald die virtuellen Computer am lokalen Standort erneut geschützt und repliziert werden, können Sie ein Failback von Azure durchführen, wenn Sie bereit sind.

> [!div class="nextstepaction"]
> [Erneutes Schützen virtueller Azure-Computer](vmware-azure-reprotect.md)
> [Failback von Azure](vmware-azure-failback.md)
