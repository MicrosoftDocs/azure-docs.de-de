---
title: 'Tutorial: Ausführen eines Notfallwiederherstellungsverfahrens für virtuelle Azure-Computer mit Azure Site Recovery'
description: In diesem Tutorial wird unter Verwendung von Site Recovery ein Notfallwiederherstellungsverfahren für virtuelle Azure-Computer ausgeführt, um sie in einer anderen Region wiederherzustellen.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395599"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Tutorial: Ausführen eines Notfallwiederherstellungsverfahrens für virtuelle Azure-Computer

Hier erfahren Sie, wie Sie für virtuelle Azure-Computer, die mit [Azure Site Recovery](site-recovery-overview.md) repliziert werden, ein Notfallwiederherstellungsverfahren ausführen, um sie in einer anderen Region wiederherzustellen. In diesem Artikel führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Überprüfen der VM-Einstellungen vor dem Verfahren
> * Ausführen eines Testfailovers
> * Bereinigen nach dem Verfahren


> [!NOTE]
> Dieses Tutorial enthält die grundlegenden Schritte zum Ausführen eines Notfallwiederherstellungsverfahrens. Wenn Sie ein Verfahren mit umfassendem Infrastrukturtest ausführen möchten, informieren Sie sich über [Netzwerke](azure-to-azure-about-networking.md), [Automatisierung](azure-to-azure-powershell.md) und [Problembehandlung](azure-to-azure-troubleshoot-errors.md) für virtuelle Azure-Computer.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, müssen Sie die Notfallwiederherstellung für mindestens einen virtuellen Azure-Computer aktivieren. Absolvieren Sie hierzu das [erste Tutorial](azure-to-azure-tutorial-enable-replication.md) dieser Reihe.

## <a name="verify-vm-settings"></a>Überprüfen von VM-Einstellungen

1. Wählen Sie im Tresor **Replizierte Elemente** und dann die VM aus.

    ![Option zum Öffnen der Notfallwiederherstellungsseite in den VM-Eigenschaften](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Vergewissern Sie sich auf der Seite **Übersicht**, dass der virtuelle Computer geschützt und fehlerfrei ist.
3. Wählen Sie im Falle eines Testfailovers ein virtuelles Azure-Netzwerk in der Zielregion aus. Der nach dem Failover erstellte virtuelle Computer wird in diesem Netzwerk platziert. 

    - In diesem Tutorial wird beim Ausführen des Testfailovers ein vorhandenes Netzwerk ausgewählt.
    - Es empfiehlt sich, für das Verfahren ein produktionsfremdes Netzwerk auszuwählen, damit IP-Adressen und Netzwerkkomponenten in Produktionsnetzwerken verfügbar bleiben.
   - Sie können auch die Netzwerkeinstellungen vorkonfigurieren, die für das Testfailover verwendet werden sollen. Zu den präzisen Einstellungen, die Sie für jede NIC zuweisen können, zählen Subnetz, private IP-Adresse, öffentliche IP-Adresse, Lastenausgleich und Netzwerksicherheitsgruppe. Diese Methode wird hier zwar nicht verwendet, in [diesen Artikel](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) finden Sie jedoch weitere Informationen dazu.


## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers


1. Wählen Sie auf der Seite **Übersicht** die Option **Testfailover** aus.

    
    ![Schaltfläche „Testfailover“ für das replizierte Element](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. Wählen Sie unter **Testfailover** einen Wiederherstellungspunkt aus. Der virtuelle Azure-Computer in der Zielregion wird unter Verwendung der Daten dieses Wiederherstellungspunkts erstellt.
  
   - **Letzte Verarbeitung**: Verwendet den letzten Wiederherstellungspunkt, der von Site Recovery verarbeitet wurde. Der Zeitstempel wird angezeigt. Es wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
   -  **Aktuell**: Verarbeitet alle Daten, die an Site Recovery gesendet wurden, um einen Wiederherstellungspunkt für jeden virtuellen Computer zu erstellen, bevor das Failover auf diesen erfolgt. Stellt die niedrigste RPO (Recovery Point Objective) bereit, da bei Auslösung des Failovers alle Daten zu Site Recovery repliziert werden.
   - **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover der virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert**: Dient zum Ausführen eines Failovers auf einen bestimmten Wiederherstellungspunkt. Die Option „Benutzerdefiniert“ ist nur verfügbar, wenn Sie ein Failover für einen einzelnen virtuellen Computer ausführen und keinen Wiederherstellungsplan verwenden.

3. Wählen Sie unter **Virtuelles Azure-Netzwerk** das Zielnetzwerk aus, in dem die nach dem Failover erstellten virtuellen Azure-Computer platziert werden sollen. Wählen Sie nach Möglichkeit ein produktionsfremdes Netzwerk und nicht das Netzwerk aus, das beim Aktivieren der Replikation erstellt wurde.

    ![Einstellungsseite für das Testfailover](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Wählen Sie **OK** aus, um das Failover zu starten.
5. Überwachen Sie das Testfailover anhand der Benachrichtigungen.

    ![Statusbenachrichtigung](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![Erfolgsbenachrichtigung](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Nach Abschluss des Failovers wird der in der Zielregion erstellte virtuelle Azure-Computer im Azure-Portal unter **Virtuelle Computer** angezeigt. Vergewissern Sie sich, dass der virtuelle Computer ausgeführt wird, die passende Größe hat und mit dem von Ihnen ausgewählten Netzwerk verbunden ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Wählen Sie auf der Seite **Zusammenfassung** die Option **Testfailover bereinigen** aus.

    ![Schaltfläche zum Starten des Bereinigungsprozesses](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. Erfassen und speichern Sie unter **Testfailoverbereinigung** > **Hinweise** alle Beobachtungen im Zusammenhang mit dem Testfailover. 
3. Aktivieren Sie das Kontrollkästchen **Die Tests sind abgeschlossen.** , um die im Zuge des Testfailovers erstellten virtuellen Computer zu löschen.

    ![Seite mit Bereinigungsoptionen](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Überwachen Sie den Status des Bereinigungsvorgangs anhand der Benachrichtigungen.

    ![Benachrichtigung zum Bereinigungsstatus](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![Erfolgsbenachrichtigung für die Bereinigung](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Notfallwiederherstellungsverfahren ausgeführt, um zu überprüfen, ob das Failover wie erwartet funktioniert. Als Nächstes können Sie ein vollständiges Failover ausprobieren.

> [!div class="nextstepaction"]
> [Ausführen eines Produktionsfailovers](azure-to-azure-tutorial-failover-failback.md)
