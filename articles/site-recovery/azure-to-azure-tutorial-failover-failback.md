---
title: Tutorial zum Ausführen eines Failovers für Azure-VMs in eine sekundäre Region für die Notfallwiederherstellung mit Azure Site Recovery.
description: Dieses Tutorial enthält Informationen dazu, wie Sie mit dem Azure Site Recovery-Dienst zur Notfallwiederherstellung ein Failover für Azure-VMs mit Replikation in einer sekundären Azure-Region ausführen und diese Computer dann erneut schützen.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392777"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Tutorial: Ausführen eines Failovers in eine sekundäre Region für Azure-VMs

Es wird beschrieben, wie Sie ein Failover für Azure-VMs, für die die Notfallwiederherstellung mit [Azure Site Recovery](site-recovery-overview.md) aktiviert ist, in eine sekundäre Region ausführen. Nach dem Failover schützen Sie die VMs in der Zielregion dann erneut, damit sie wieder in der primären Region repliziert werden. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Überprüfen der VM-Einstellungen
> * Ausführen eines Failovers zur sekundären Region
> * Starten des Vorgangs, mit dem die VM wieder in der primären Region repliziert wird


> [!NOTE]
> In diesem Tutorial wird veranschaulicht, wie Sie mit geringem Aufwand ein Failover für VMs ausführen. Falls Sie ein Failover mit den vollständigen Einstellungen durchführen möchten, sollten Sie sich über die Bereiche [Netzwerk](azure-to-azure-about-networking.md), [Automatisierung](azure-to-azure-powershell.md) und [Problembehandlung](azure-to-azure-troubleshoot-errors.md) für Azure-VMs informieren.



## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie die folgenden Schritte ausgeführt haben:

1. Richten Sie die Replikation für einen oder mehrere virtuelle Azure-Computer ein. [Arbeiten Sie das erste Tutorial dieser Reihe durch](azure-to-azure-tutorial-enable-replication.md), falls Sie die Einrichtung noch nicht vorgenommen haben.
2. Wir empfehlen Ihnen, eine [Notfallwiederherstellungsübung](azure-to-azure-tutorial-dr-drill.md) für replizierte VMs durchzuführen. Indem Sie vor dem Ausführen eines vollständigen Failovers einen Übungslauf durchführen, können Sie sicherstellen, dass alles wie erwartet ohne Beeinträchtigung Ihrer Produktionsumgebung funktioniert. 


## <a name="verify-the-vm-settings"></a>Überprüfen der VM-Einstellungen

1. Wählen Sie im Tresor **Replizierte Elemente** und dann die VM aus.

    ![Option zum Öffnen der VM-Eigenschaften auf der Übersichtsseite](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Überprüfen Sie auf der Seite mit der **Übersicht** für die VM, ob sie geschützt und fehlerfrei ist, bevor Sie ein Failover ausführen.
    ![Seite zum Überprüfen der Eigenschaften und des Zustands einer VM](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Überprüfen Sie vor dem Starten des Failovers Folgendes:
    - Auf der VM wird ein unterstütztes [Windows](azure-to-azure-support-matrix.md#windows)- oder [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage)-Betriebssystem ausgeführt.
    - Die VM erfüllt die Anforderungen in Bezug auf [Compute](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [Speicher](azure-to-azure-support-matrix.md#replicated-machines---storage) und [Netzwerk](azure-to-azure-support-matrix.md#replicated-machines---networking).

## <a name="run-a-failover"></a>Ausführen eines Failovers


1. Wählen Sie auf der Seite **Übersicht** für die VM die Option **Failover** aus.

    ![Schaltfläche „Failover“ für das replizierte Element](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. Wählen Sie unter **Failover** einen Wiederherstellungspunkt aus. Der virtuelle Azure-Computer in der Zielregion wird anhand der Daten dieses Wiederherstellungspunkts erstellt.
  
   - **Letzte Verarbeitung**: Verwendet den letzten Wiederherstellungspunkt, der von Site Recovery verarbeitet wurde. Der Zeitstempel wird angezeigt. Es wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
   -  **Aktuell**: Verarbeitet alle Daten, die an Site Recovery gesendet wurden, um einen Wiederherstellungspunkt für jeden virtuellen Computer zu erstellen, bevor das Failover dafür ausgeführt wird. Ist mit dem niedrigsten RPO-Wert (Recovery Point Objective) verbunden, da bei Auslösung des Failovers alle Daten in Site Recovery repliziert werden.
   - **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover der virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert**: Dient zum Ausführen eines Failovers auf einen bestimmten Wiederherstellungspunkt. Die Option „Benutzerdefiniert“ ist nur verfügbar, wenn Sie ein Failover für einen einzelnen virtuellen Computer ausführen und keinen Wiederherstellungsplan verwenden.

    > [!NOTE]
    > Wenn Sie einer VM nach dem Aktivieren der Replikation einen Datenträger hinzugefügt haben, werden für die Wiederherstellungspunkte die Datenträger angezeigt, die für die Wiederherstellung verfügbar sind. Beispielsweise wird ein Replikationspunkt, der vor dem Hinzufügen eines zweiten Datenträgers erstellt wurde, als „1 von 2 Datenträgern“ angezeigt.

4. Wählen Sie **Der Computer wird vor Beginn des Failovers heruntergefahren** aus, wenn Site Recovery versuchen soll, den Quellcomputer herunterzufahren, bevor das Failover gestartet wird. Mit dem Herunterfahren können Sie sicherzustellen, dass keine Daten verloren gehen. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. 

    ![Seite mit Failovereinstellungen](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Wählen Sie **OK** aus, um das Failover zu starten.
4. Überwachen Sie das Failover über die Benachrichtigungen.

    ![Statusbenachrichtigung](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![Erfolgsmeldung](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Nach dem Failover wird die in der Zielregion erstellte Azure-VM unter **Virtuelle Computer** angezeigt. Stellen Sie sicher, dass der virtuelle Computer ausgeführt wird und über die richtige Größe verfügt. Wenn Sie einen anderen Wiederherstellungspunkt für den virtuellen Computer verwenden möchten, wählen Sie auf der Seite **Grundlagen** die Option **Wiederherstellungspunkt ändern** aus.
6. Wenn Sie mit dem virtuellen Computer, für den das Failover ausgeführt wurde, zufrieden sind, wählen Sie auf der Übersichtsseite die Option **Commit** aus, um das Failover abzuschließen.

    ![Schaltfläche „Commit“](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. Wählen Sie unter **Commit** die Option **OK** aus, um den Vorgang zu bestätigen. Mit „Commit“ werden alle verfügbaren Wiederherstellungspunkte für die VM in Site Recovery gelöscht, und Sie können den Wiederherstellungspunkt nicht mehr ändern.

8. Überwachen Sie den Status des Commitvorgangs in den Benachrichtigungen.

    ![Benachrichtigung über Status des Commitvorgangs](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![Erfolgsmeldung für Commit](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery führt nach dem Failover keine Bereinigung der Quell-VM durch. Sie müssen diesen Schritt manuell ausführen.


## <a name="reprotect-the-vm"></a>Erneutes Schützen der VM

Nach dem Failover schützen Sie die VM in der sekundären Region erneut, damit die Replikation zurück in die primäre Region erfolgt. 

1. Vergewissern Sie sich vor dem Starten, dass der **Status** der VM *Commit für Failover ausgeführt* lautet.
2. Stellen Sie sicher, dass Sie auf die primäre Region zugreifen können und über die Berechtigungen zum Erstellen von VMs in dieser Region verfügen.
3. Wählen Sie auf der Seite **Übersicht** der VM die Option **Erneut schützen** aus.

   ![Schaltfläche zum Aktivieren des erneuten Schützens für eine VM](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. Überprüfen Sie unter **Erneut schützen** die Replikationsrichtung (von sekundärer in primäre Region) und die Zieleinstellungen für die primäre Region. Als neu markierte Ressourcen werden von Site Recovery während des Vorgangs zum erneuten Schützen erstellt.

     ![Seite mit Einstellungen für erneutes Schützen](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Wählen Sie **OK** aus, um den Vorgang zum erneuten Schützen zu starten. Der Vorgang sendet anfängliche Daten an den Zielort und repliziert dann Deltainformationen für die virtuellen Computer auf dem Ziel.
7. Überwachen Sie den Status des erneuten Schützens in den Benachrichtigungen. 

    ![Benachrichtigung zum Status des erneuten Schützens](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![Erfolgsmeldung zum erneuten Schützen](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Failover aus der primären in die sekundäre Region ausgeführt und mit der Replikation der VMs zurück in die primäre Region begonnen. Sie können nun ein Failback aus der sekundären zurück in die primäre Region durchführen.

> [!div class="nextstepaction"]
> [Ausführen eines Failbacks auf die primäre Region](azure-to-azure-tutorial-failback.md)
