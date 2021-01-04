---
title: Tutorial zum Failback von Azure-VMs in eine primäre Region während der Notfallwiederherstellung mit Azure Site Recovery.
description: Tutorial zum Failback von Azure-VMs in eine primäre Region mit Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393886"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Tutorial: Ausführen des Failbacks einer Azure-VM in die primäre Region

Nach dem Failover einer Azure-VM in eine sekundäre Azure-Region folgen Sie diesem Tutorial, um mit [Azure Site Recovery](site-recovery-overview.md) das Failback der Azure-VMs in die primäre Azure-Region durchzuführen.  In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> 
> * Informieren Sie sich über die Voraussetzungen.
> * Ausführen eines Failbacks für den virtuellen Computer in der sekundären Region
> * Erneutes Schützen der primären virtuellen Computer zurück zur sekundären Region.
> 
> [!NOTE]
> In diesem Tutorial wird gezeigt, wie Sie mit minimalen Schritten ein Failback durchführen. Wenn Sie ein Failover mit vollständigen Einstellungen durchführen möchten, informieren Sie sich über Azure-VM-[Netzwerke](azure-to-azure-about-networking.md), [Automatisierung](azure-to-azure-powershell.md) und [Problembehandlung](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten Sie folgende Schritte ausgeführt haben:

1. [Einrichten der Replikation](azure-to-azure-tutorial-enable-replication.md) für mindestens eine Azure-VM und Ausführen der [Übungen zur Notfallwiederherstellung](azure-to-azure-tutorial-dr-drill.md).
2. [Failover des virtuellen Computers](azure-to-azure-tutorial-failover-failback.md) von der primären Region in eine sekundäre Region und erneutes Schützen durch Replizieren des virtuellen Computers von der sekundären Region in die primäre Region. 
3. Vergewissern Sie sich außerdem, dass die primäre Region verfügbar ist und dass Sie darin neue Ressourcen erstellen und auf diese zugreifen können.

## <a name="fail-back-to-the-primary-region"></a>Ausführen eines Failbacks auf die primäre Region

Nachdem virtuelle Computer erneut geschützt wurden, können Sie bei Bedarf ein Failback zur primären Region ausführen.

1. Wählen Sie im Tresor **Replizierte Elemente** und dann die VM aus.

2. Überprüfen Sie auf der Seite „Übersicht“ des virtuellen Computers, ob der virtuelle Computer fehlerfrei und die Synchronisation abgeschlossen ist, bevor Sie ein Failover ausführen. Der virtuelle Computer sollte sich in einem *geschützten* Zustand befinden.

    ![Seite „Übersicht“ des virtuellen Computers mit virtuellem Computer in geschütztem Zustand](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Wählen Sie auf der Seite „Übersicht“ die Option **Failover** aus. Da dieses Mal kein Testfailover erfolgt, werden Sie zur Bestätigung aufgefordert.

    [Seite, auf der Sie bestätigen, dass ein Failover ohne Testfailover ausgeführt werden soll](./media/azure-to-azure-tutorial-failback/no-test.png)

4. Beachten Sie unter **Failover** die Richtung von der sekundären zur primären Region, und wählen Sie einen Wiederherstellungspunkt aus. Der virtuelle Azure-Computer im Ziel (primäre Region) wird anhand der Daten aus diesem Punkt erstellt.
   - **Letzte Verarbeitung** : Verwendet den letzten Wiederherstellungspunkt, der von Site Recovery verarbeitet wurde. Der Zeitstempel wird angezeigt. Es wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
   -  **Aktuell** : Verarbeitet alle Daten, die an Site Recovery gesendet wurden, um einen Wiederherstellungspunkt für jeden virtuellen Computer zu erstellen, bevor das Failover auf diesen erfolgt. Stellt die niedrigste RPO (Recovery Point Objective) bereit, da bei Auslösung des Failovers alle Daten zu Site Recovery repliziert werden.
   - **Letzter anwendungskonsistenter Zeitpunkt** : Diese Option führt ein Failover der virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert** : Dient zum Ausführen eines Failovers auf einen bestimmten Wiederherstellungspunkt. Die Option „Benutzerdefiniert“ ist nur verfügbar, wenn Sie ein Failover für einen einzelnen virtuellen Computer ausführen und keinen Wiederherstellungsplan verwenden.

    > [!NOTE]
    > Wenn Sie ein Failover für einen virtuellen Computer ausführen, dem Sie einen Datenträger hinzugefügt haben, nachdem die Replikation für diesen virtuellen Computer aktiviert haben, zeigen die Replikationspunkte die Datenträger an, die zur Wiederherstellung zur Verfügung stehen. Beispielsweise wird ein Replikationspunkt, der erstellt wurde, bevor Sie einen zweiten Datenträger hinzugefügt haben, als „1 von 2 Datenträgern“ angezeigt.

4. Wählen Sie **Der Computer wird vor Beginn des Failovers heruntergefahren** aus, wenn Site Recovery versuchen soll, den Quellcomputer herunterzufahren, bevor das Failover gestartet wird. Mit dem Herunterfahren können Sie sicherzustellen, dass keine Daten verloren gehen. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. 

    ![Seite der Failovereinstellungen](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Wählen Sie **OK** aus, um das Failover zu starten.
4. Überwachen Sie das Failover mit Benachrichtigungen.

    ![Benachrichtigung zum Fortschritt des Failovers](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Benachrichtigung zum Erfolg des Failovers](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Erneutes Schützen virtueller Computer

Nachdem Sie das Failover der virtuellen Computer in die primäre Region ausgeführt haben, müssen Sie sie erneut schützen, sodass sie wieder in die sekundäre Region repliziert werden.

1. Wählen Sie auf der Seite **Übersicht** für den virtuellen Computer die Option **Erneut schützen** aus.

    ![Schaltfläche für den erneuten Schutz von der primären Region aus](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Überprüfen Sie die Zieleinstellungen für die primäre Region. Als neu markierte Ressourcen werden von Site Recovery während des Vorgangs zum erneuten Schützen erstellt.
3. Wählen Sie **OK** aus, um den Vorgang zum erneuten Schützen zu starten. Der Vorgang sendet anfängliche Daten an den Zielort und repliziert dann Deltainformationen für die virtuellen Computer an das Ziel.

     ![Seite mit den Einstellungen für die Replikation](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Überwachen Sie den Fortschritt des erneuten Schutzes in Benachrichtigungen. 

    ![Benachrichtigung zum Fortschritt des erneuten Schutzes](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Benachrichtigung zum Fortschritt des erneuten Schutzes](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bei virtuellen Computern mit verwalteten Datenträgern bereinigt Site Recovery nach Abschluss des Failbacks und Einrichten des erneuten Schutzes für die Replikation der virtuellen Computer von der primären zur sekundären Region automatisch die Computer in der sekundären Notfallwiederherstellungsregion. Sie brauchen die virtuellen Computer und NICs in der sekundären Region nicht manuell zu löschen. Virtuelle Computer mit nicht verwalteten Datenträgern werden nicht bereinigt.

Wenn Sie die Replikation nach einem Failback vollständig deaktivieren, werden die von Site Recovery geschützten Computer bereinigt. In diesem Fall werden auch Datenträger für virtuelle Computer ohne verwaltete Datenträger bereinigt. 
 
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie das Failback von virtuellen Computern von der sekundären Region zurück in die primäre Region durchgeführt. Dies ist der letzte Schritt im Prozess, der die Aktivierung der Replikation für einen virtuellen Computer, Übungen zur Notfallwiederherstellung, das Failover von der primären Region in die sekundäre Region und schließlich das Failback umfasst.

> [!div class="nextstepaction"]
> Testen Sie nun die Notfallwiederherstellung in Azure für einen [lokalen virtuellen Computer](vmware-azure-tutorial-prepare-on-premises.md).

