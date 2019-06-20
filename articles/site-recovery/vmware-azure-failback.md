---
title: Ausführen eines Failbacks in Azure während der Notfallwiederherstellung von virtuellen VMware-Computern in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie während der Notfallwiederherstellung von virtuellen VMware-Computern und physischen Servern in Azure nach dem Failover in Azure ein Failback auf den lokalen Standort ausführen.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 7773a2f43eb076075be484d92fde31094a2b584b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318120"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Failback von virtuellen VMware-Computern und physischen Servern von Azure zum lokalen Standort

In diesem Artikel wird beschrieben, wie Sie für virtuelle Computer ein Failback von Azure Virtual Machines in die lokale VMware-Umgebung durchführen. Befolgen Sie die Anweisungen in diesem Artikel, um ein Failback Ihrer virtuellen VMware-Computer oder Ihrer physischen Windows-/Linux-Server nach einem Failover vom lokalen Standort nach Azure (beschrieben im Tutorial [Failover in Azure Site Recovery](site-recovery-failover.md)) zurück an den lokalen Standort durchzuführen.

## <a name="prerequisites"></a>Voraussetzungen
- Lesen Sie vorher unbedingt die Informationen zu den [verschiedenen Typen von Failbacks](concepts-types-of-failback.md) und den entsprechenden Einschränkungen.

> [!WARNING]
> Wenn Sie entweder die [Migration abgeschlossen](migrate-overview.md#what-do-we-mean-by-migration), den virtuellen Computer in eine andere Ressourcengruppe verschoben oder den virtuellen Azure-Computer gelöscht haben, ist danach kein Failback möglich. Wenn Sie den Schutz des virtuellen Computers deaktivieren, ist kein Failback möglich.

> [!WARNING]
> Für einen geschützten physischen Server unter Windows Server 2008 R2 SP1, für den ein Failover auf Azure ausgeführt wurde, kann kein Failback ausgeführt werden.

> [!NOTE]
> Wenn für VMware-VMs ein Failover erfolgt ist, ist kein Failback auf einen Hyper-V-Host möglich.


- Führen Sie vor dem Fortfahren alle Schritte für das erneute Schützen aus, damit sich die virtuellen Computer in einem replizierten Zustand befinden und Sie ein Failover zurück zu einem lokalen Standort initiieren können. Weitere Informationen finden Sie unter [Erneutes Schützen von Azure zu lokal](vmware-azure-reprotect.md).

- Stellen Sie sicher, dass die vCenter-Instanz verbunden ist, bevor Sie ein Failback ausführen. Andernfalls führt das Trennen und erneute Anfügen von Datenträgern an den virtuellen Computer zu einem Fehler.

- Beim Failover zu Azure ist der lokale Standort möglicherweise nicht verfügbar und daher kann der Konfigurationsserver nicht verfügbar oder heruntergefahren sein. Während des erneuten Schützens und des Failbacks sollte der lokale Konfigurationsserver ausgeführt werden und einen fehlerfreien verbundenen Zustand aufweisen. 

- Während des Failbacks muss der virtuelle Computer in der Konfigurationsserverdatenbank vorhanden sein, ansonsten ist das Failback nicht erfolgreich. Stellen Sie sicher, dass Sie die regelmäßigen geplanten Sicherungen des Servers durchführen. Im Notfall müssen Sie den Server mit der ursprünglichen IP-Adresse wiederherstellen, damit das Failback funktioniert.

- Der Masterzielserver sollte vor dem Auslösen des erneuten Schützens/Failbacks keine Momentaufnahmen enthalten.

## <a name="overview-of-failback"></a>Übersicht über das Failback
Nach einem Failover zu Azure erfolgt das Failback zu Ihrem lokalen Standort anhand der folgenden Schritte:

1. [Schützen Sie die virtuellen Computer in Azure erneut](vmware-azure-reprotect.md), damit sie mit der Replikation auf virtuelle VMware-Computer an Ihrem lokalen Standort beginnen. Im Rahmen dieses Vorgangs müssen Sie auch folgende Schritte ausführen:

    * Richten Sie ein lokales Masterziel ein. Verwenden Sie ein Windows-Masterziel für virtuelle Windows-Computer und ein [Linux-Masterziel](vmware-azure-install-linux-master-target.md) für virtuelle Linux-Computer.
    * Einrichten eines [Prozessservers](vmware-azure-set-up-process-server-azure.md)
    * Beginnen Sie mit dem [erneuten Schützen](vmware-azure-reprotect.md), um den lokalen virtuellen Computer zu deaktivieren und die Azure-VM-Daten mit den lokalen Datenträgern zu synchronisieren.

2. Initiieren Sie nach der Replikation Ihrer virtuellen Azure-Computer am lokalen Standort ein Failover von Azure zum lokalen Standort.

3. Nachdem Ihre Daten per Failback zurückgeführt wurden, schützen Sie die lokalen virtuellen Computer erneut, damit diese mit der Replikation nach Azure beginnen.

Sehen Sie sich das folgende Video zum Failback zu einem lokalen Standort an, um eine kurze Übersicht zu erhalten:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Schritte für das Failback

> [!IMPORTANT]
> Bevor Sie das Failback beginnen, stellen Sie sicher, dass Sie den erneuten Schutz der virtuellen Computer abgeschlossen haben. Die virtuellen Computer müssen geschützt sein und den Integritätsstatus **OK** aufweisen. Informationen zum erneuten Schutz der virtuellen Computer finden Sie unter [Erneuter Schutz](vmware-azure-reprotect.md).

1. Wählen Sie den virtuellen Computer auf der Seite der replizierten Elemente aus. Klicken Sie mit der rechten Maustaste, um **Ungeplantes Failover** auszuwählen.
2. Überprüfen Sie in **Failover bestätigen** die Failoverrichtung (von Azure). Wählen Sie dann den Wiederherstellungspunkt aus (den neuesten oder den neuesten app-konsistenten Wiederherstellungspunkt), der für das Failover verwendet werden soll. Der App-konsistente Punkt liegt vor dem aktuellen Zeitpunkt und verursacht einige Datenverluste.
3. Während des Failovers fährt Site Recovery die virtuellen Computer auf Azure herunter. Vergewissern Sie sich, dass das Failback wie erwartet abgeschlossen wurde, und überprüfen Sie anschließend, ob die virtuellen Computer in Azure heruntergefahren wurden.
4. Ein **Commit** ist erforderlich, um den virtuellen Computer, für den das Failover durchgeführt wurde, aus Azure zu entfernen. Klicken Sie mit der rechten Maustaste auf das geschützte Element, und wählen Sie dann **Commit** aus. Ein Auftrag entfernt die virtuellen Computer, für die in Azure ein Failover ausgeführt wurde.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Auf welchen Wiederherstellungspunkt kann ich für die virtuellen Computer ein Failback durchführen?

Während des Failbacks haben Sie für das Failback der virtuellen Computer bzw. des Wiederherstellungsplans zwei Möglichkeiten.

- Wenn Sie den letzten Verarbeitungspunkt auswählen, wird für alle virtuellen Computer ein Failover zum letzten verfügbaren Zeitpunkt durchgeführt. Falls im Wiederherstellungsplan eine Replikationsgruppe enthalten ist, wird für jeden virtuellen Computer der Replikationsgruppe ein Failover zu ihrem unabhängigen letzten Zeitpunkt durchgeführt.

  Sie können das Failback eines virtuellen Computers erst ausführen, wenn er mindestens einen Wiederherstellungspunkt aufweist. Ein Failback für einen Wiederherstellungsplan ist erst möglich, wenn alle beteiligten virtuellen Computer mindestens einen Wiederherstellungspunkt aufweisen.

  > [!NOTE]
  > Bei einem letzten Wiederherstellungspunkt handelt es sich um einen absturzkonsistenten Wiederherstellungspunkt.

- Wenn Sie den anwendungskonsistenten Wiederherstellungspunkt auswählen, wird bei einem Failback für einen einzelnen virtuellen Computer die Wiederherstellung des letzten verfügbaren anwendungskonsistenten Wiederherstellungspunkts durchgeführt. Bei einem Wiederherstellungsplan mit einer Replikationsgruppe erfolgt für jede Replikationsgruppe die Wiederherstellung für den gemeinsam verfügbaren Wiederherstellungspunkt.
Anwendungskonsistente Wiederherstellungspunkte können zeitlich weiter zurück liegen, und es kann zu einem Verlust von Daten kommen.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Was geschieht mit VMware-Tools nach dem Failback?

Bei einem virtuellen Windows-Computer deaktiviert Site Recovery die VMware-Tools während des Failovers. Im Verlauf des Failbacks eines virtuellen Windows-Computers werden die VMware-Tools wieder aktiviert. 


## <a name="reprotect-from-on-premises-to-azure"></a>Erneutes Schützen von einem lokalen Standort nach Azure
Nach Abschluss des Failbacks und dem Initiieren des Commits werden die wiederhergestellten virtuellen Computer in Azure gelöscht. Nun befindet sich Ihr virtueller Computer wieder am lokalen Standort, er ist jedoch nicht geschützt. Führen Sie daher folgende Schritte aus, um die Replikation zu Azure erneut zu starten:

1. Wählen Sie unter **Tresor** > **Einstellung** > **Replizierte Elemente** die virtuellen Computer aus, für die das Failback durchgeführt wurde, und wählen Sie dann **Erneut schützen** aus.
2. Geben Sie den Wert des Prozessservers an, der zum Zurücksenden von Daten an Azure verwendet werden muss.
3. Wählen Sie **OK** aus, um den Auftrag zum erneuten Schützen zu starten.

> [!NOTE]
> Nachdem ein lokaler virtueller Computer gestartet wurde, dauert es eine Weile, bis der Agent auf dem Konfigurationsserver registriert wird (bis zu 15 Minuten). Während dieses Zeitraums schlägt das erneute Schützen fehl, und eine zurückgegebene Fehlermeldung gibt an, dass der Agent nicht installiert ist. Warten Sie einige Minuten, und versuchen Sie dann noch einmal, das erneute Schützen durchzuführen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem der Auftrag zum erneuten Schützen abgeschlossen ist, wird der virtuelle Computer zurück nach Azure repliziert, und Sie können ein [Failover](site-recovery-failover.md) ausführen, um die virtuellen Computer wieder nach Azure zu verschieben.


