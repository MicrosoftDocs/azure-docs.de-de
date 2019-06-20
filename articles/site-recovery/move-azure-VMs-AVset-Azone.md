---
title: Verschieben von virtuellen Azure IaaS-Computern als an Zonen angeheftete virtuelle Computer zu einer anderen Azure-Region mithilfe des Azure Site Recovery-Diensts | Microsoft-Dokumentation
description: Verwenden Sie Azure Site Recovery, um virtuelle Azure IaaS-Computer als an Zonen angeheftete virtuelle Computer in eine andere Azure-Region zu verschieben.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 6e26f9d344a4c11c335d1ea34998c848cbe3598a
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753749"
---
# <a name="move-azure-vms-into-availability-zones"></a>Verschieben virtueller Azure-Computer in Verfügbarkeitszonen
Verfügbarkeitszonen in Azure tragen dazu bei, Ihre Anwendungen und Daten vor Datencenterausfällen zu schützen. Jede Verfügbarkeitszone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region trägt dazu bei, Anwendungen und Daten vor Datencenterausfällen zu schützen. Mit Verfügbarkeitszonen bietet Azure eine Vereinbarung zum Servicelevel (Service Level Agreement, SLA) von 99,99 Prozent für die Betriebszeit virtueller Computer (Virtual Machines, VMs). Verfügbarkeitszonen werden in ausgewählten Regionen unterstützt (siehe [Was sind Verfügbarkeitszonen in Azure?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region)).

Wenn Sie Ihre virtuellen Computer als *Einzelinstanz* in einer bestimmten Region bereitgestellt haben, können Sie die Verfügbarkeit verbessern, indem Sie die virtuellen Computer mithilfe von Azure Site Recovery in eine Verfügbarkeitszone verschieben. Diese Aktion kann weiter kategorisiert werden:

- Verschieben von Einzelinstanz-VMs in Verfügbarkeitszonen in einer Zielregion
- Verschieben von virtuellen Computern in einer Verfügbarkeitsgruppe in Verfügbarkeitszonen in einer Zielregion

> [!IMPORTANT]
> Azure Site Recovery unterstützt aktuell das Verschieben virtueller Computer zwischen Regionen, aber keine Verschiebung innerhalb einer Region.

## <a name="check-prerequisites"></a>Überprüfen der Voraussetzungen

- Überprüfen Sie, ob die Zielregion [Verfügbarkeitszonen unterstützt](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region). Vergewissern Sie sich, dass Ihre gewählte [Kombination aus Quell- und Zielregion unterstützt](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) wird. Treffen Sie eine fundierte Entscheidung hinsichtlich der Zielregion.
- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](azure-to-azure-architecture.md) verstehen.
- Lesen Sie die [Einschränkungen und Anforderungen der Unterstützung](azure-to-azure-support-matrix.md).
- Überprüfen Sie die Kontoberechtigungen. Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator Ihres Abonnements. Sollten Sie nicht der Abonnementadministrator sein, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist. Um die Replikation für einen virtuellen Computer zu aktivieren und schließlich Daten mithilfe von Azure Site Recovery in die Zielregion zu kopieren, benötigen Sie Folgendes:

    1. Berechtigungen zum Erstellen eines virtuellen Computers in Azure-Ressourcen. Die integrierte Rolle *Mitwirkender für virtuelle Computer* verfügt über diese Berechtigungen. Dazu zählen unter anderem:
        - Berechtigung zum Erstellen einer VM in der ausgewählten Ressourcengruppe
        - Berechtigung zum Erstellen einer VM im ausgewählten virtuellen Netzwerk
        - Berechtigung zum Schreiben in das ausgewählte Speicherkonto

    2. Berechtigung zum Verwalten von Azure Site Recovery-Aufgaben. Die Rolle *Site Recovery-Mitwirkender* verfügt über alle erforderlichen Berechtigungen zum Verwalten von Site Recovery-Aktionen in einem Recovery Services-Tresor.

## <a name="prepare-the-source-vms"></a>Vorbereiten der virtuellen Quellcomputer

1. Ihre virtuellen Computer müssen verwaltete Datenträger verwenden, wenn Sie sie mithilfe von Site Recovery in eine Verfügbarkeitszone verschieben möchten. Sie können vorhandene virtuelle Windows-Computer, die nicht verwaltete Datenträger verwenden, für die Verwendung verwalteter Datenträger konvertieren. Gehen Sie hierzu wie unter [Konvertieren einer Windows-VM von nicht verwalteten Datenträgern in verwaltete Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) beschrieben vor. Achten Sie darauf, dass die Verfügbarkeitsgruppe als *Verwaltet* konfiguriert ist.
2. Stellen Sie sicher, dass alle aktuellen Stammzertifikate auf den virtuellen Azure-Computern vorhanden sind, die Sie verschieben möchten. Falls die aktuellen Stammzertifikate nicht vorhanden sind, kann das Kopieren von Daten in die Zielregion aufgrund von Sicherheitseinschränkungen nicht aktiviert werden.

3. Installieren Sie alle aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf den Computern vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung die Standardprozesse für Windows- und Zertifikatupdates in Ihrer Organisation durch.

4. Halten Sie sich bei virtuellen Linux-Computern an die Anleitung des Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste für den virtuellen Computer abzurufen.
5. Vergewissern Sie sich, dass Sie keinen Authentifizierungsproxy verwenden, um die Netzwerkkonnektivität für virtuelle Computer zu steuern, die Sie migrieren möchten.

6. Wenn der virtuelle Computer, den Sie verschieben möchten, keinen Zugriff auf das Internet hat und einen Firewallproxy zum Steuern des ausgehenden Zugriffs verwendet, überprüfen Sie die Anforderungen unter [Konfigurieren der ausgehenden Netzwerkkonnektivität](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

7. Ermitteln Sie das Quellnetzwerklayout und die Ressourcen, die Sie derzeit für die Überprüfung verwenden (einschließlich Lastenausgleichsmodule, Netzwerksicherheitsgruppen und öffentliche IP-Adresse).

## <a name="prepare-the-target-region"></a>Vorbereiten der Zielregion

1. Vergewissern Sie sich, dass Ihr Azure-Abonnement die Erstellung von virtuellen Computern in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich ggf. an den Support, um das erforderliche Kontingent zu aktivieren.

2. Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um VMs zu unterstützen, die so groß wie die Quell-VMs sind. Wenn Sie Site Recovery verwenden, um Daten in die Zielregion zu kopieren, wird für den virtuellen Zielcomputer die gleiche oder eine möglichst ähnliche Größe gewählt.

3. Erstellen Sie für jede im Quellnetzwerklayout identifizierte Komponente eine Zielressource. Dadurch wird sichergestellt, dass Ihre virtuellen Computer nach der Umstellung auf die Zielregion über alle Funktionen und Features verfügen, die auch in der Quellregion verfügbar waren.

    > [!NOTE]
    > Azure Site Recovery erkennt und erstellt automatisch ein virtuelles Netzwerk und Speicherkonto, wenn Sie die Replikation für den virtuellen Quellcomputer aktivieren. Sie können diese Ressourcen auch vorab erstellen und sie beim Aktivieren der Replikation des virtuellen Computers zuweisen. Alle anderen Ressourcen müssen jedoch wie weiter unten angegeben manuell in der Zielregion erstellt werden.

     In den folgenden Dokumenten erfahren Sie, wie Sie die gängigsten, für Sie relevanten Netzwerkressourcen auf der Grundlage der Konfiguration des virtuellen Quellcomputers erstellen:

    - [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Load Balancer](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Öffentliche IP-Adresse](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Informationen zu anderen Netzwerkkomponenten finden Sie in der [Dokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network) zum Netzwerk.

    > [!IMPORTANT]
    > Achten Sie darauf, am Ziel einen zonenredundanten Lastenausgleich zu verwenden. Weitere Informationen finden Sie unter [Load Balancer Standard und Verfügbarkeitszonen](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. [Erstellen Sie manuell ein nicht für die Produktion vorgesehenes Netzwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in der Zielregion, wenn Sie die Konfiguration vor der Umstellung auf die Zielregion testen möchten. Diese Vorgehensweise wird empfohlen, da die Produktionsumgebung dadurch möglichst wenig beeinträchtigt wird.

## <a name="enable-replication"></a>Aktivieren der Replikation
In diesem Abschnitt erfahren Sie, wie Sie unter Verwendung von Azure Site Recovery die Replikation von Daten in der Zielregion aktivieren, bevor Sie sie schließlich in Verfügbarkeitszonen verschieben.

> [!NOTE]
> Diese Schritte gelten für einen einzelnen virtuellen Computer. Sie können jedoch auf mehrere virtuelle Computer ausgeweitet werden. Navigieren Sie zum Recovery Services-Tresor, und wählen Sie **+ Replizieren** und anschließend die relevanten virtuellen Computer aus.

1. Wählen Sie im Azure-Portal die Option **Virtuelle Computer** und anschließend den virtuellen Computer aus, den Sie in Verfügbarkeitszonen verschieben möchten.
2. Wählen Sie unter **Vorgänge** die Option **Notfallwiederherstellung** aus.
3. Wählen Sie unter **Notfallwiederherstellung konfigurieren** > **Zielregion** die Zielregion für die Replikation aus. Vergewissern Sie sich, dass diese Region Verfügbarkeitszonen [unterstützt](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region).

    ![Auswählen der Zielregion](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Klicken Sie auf **Weiter: Erweiterte Einstellungen**.
5. Wählen Sie die entsprechenden Werte für Zielabonnement, Ziel-VM-Ressourcengruppe und virtuelles Netzwerk aus.
6. Wählen Sie im Abschnitt **Verfügbarkeit** die Verfügbarkeitszone aus, in die Sie den virtuellen Computer verschieben möchten. 
   > [!NOTE]
   > Wenn die Option für Verfügbarkeitsgruppe oder -zone nicht angezeigt wird, vergewissern Sie sich, dass die [Voraussetzungen](#prepare-the-source-vms) erfüllt sind und die [Vorbereitung](#prepare-the-source-vms) der virtuellen Quellcomputer abgeschlossen ist.
  
    ![Auswahloptionen für die Wahl einer Verfügbarkeitszone](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Klicken Sie auf **Replikation aktivieren**. Dadurch wird ein Auftrag gestartet, um die Replikation des virtuellen Computers zu aktivieren.

## <a name="check-settings"></a>Überprüfen der Einstellungen

Nach Abschluss des Replikationsauftrags können Sie den Replikationsstatus überprüfen, die Replikationseinstellungen ändern und die Bereitstellung testen.

1. Wählen Sie im VM-Menü die Option **Notfallwiederherstellung** aus.
2. Sie können die Replikationsintegrität und die erstellten Wiederherstellungspunkte sowie die Quell- und Zielregionen auf der Karte überprüfen.

   ![Replikationsstatus](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Testen der Konfiguration

1. Wählen Sie im VM-Menü die Option **Notfallwiederherstellung** aus.
2. Wählen Sie das Symbol **Testfailover** aus.
3. Wählen Sie unter **Testfailover** einen Wiederherstellungspunkt für das Failover aus:

   - **Letzte Verarbeitung**: Führt ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt aus, der vom Site Recovery-Dienst verarbeitet wurde. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
   - **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover aller virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert**: Wählen Sie einen beliebigen Wiederherstellungspunkt aus.

3. Wählen Sie das virtuelle Azure-Testzielnetzwerk aus, in das die virtuellen Azure-Computer zum Testen der Konfiguration verschoben werden sollen. 

    > [!IMPORTANT]
    > Wir empfehlen, für das Testfailover ein separates Azure-VM-Netzwerk zu verwenden und nicht das Produktionsnetzwerk in der Zielregion, in das Sie Ihre virtuellen Computer verschieben möchten.

4. Wählen Sie zum Testen der Verschiebung **OK** aus. Wählen Sie zum Überwachen des Status den virtuellen Computer aus, um dessen Eigenschaften anzuzeigen. Alternativ können Sie unter „<Tresorname>“ > **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** den Auftrag **Testfailover** auswählen.
5. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Stellen Sie sicher, dass die VM ausgeführt wird, die passende Größe hat und mit dem entsprechenden Netzwerk verbunden ist.
6. Wenn Sie den beim Testen der Verschiebung erstellten virtuellen Computer löschen möchten, wählen Sie für das replizierte Element die Option **Testfailover bereinigen** aus. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Test.

## <a name="move-to-the-target-region-and-confirm"></a>Verschieben in die Zielregion und Bestätigen

1.  Wählen Sie im VM-Menü die Option **Notfallwiederherstellung** aus.
2. Wählen Sie das Symbol **Failover** aus.
3. Wählen Sie unter **Failover** die Option **Neueste** aus. 
4. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, die Quell-VM herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt. 
5. Überprüfen Sie nach Abschluss des Auftrags, ob der virtuelle Computer wie erwartet in der Azure-Zielregion angezeigt wird.
6. Klicken Sie unter **Replizierte Elemente** mit der rechten Maustaste auf die VM und dann auf **Commit**. Dadurch wird die Verschiebung in die Zielregion abgeschlossen. Warten Sie, bis der Commitauftrag abgeschlossen ist.

## <a name="discard-the-resource-in-the-source-region"></a>Verwerfen der Ressourcen in der Quellregion

Navigieren Sie zu dem virtuellen Computer. Wählen Sie **Replikation deaktivieren** aus. Dadurch wird der Prozess zum Kopieren der Daten für den virtuellen Computer beendet.  

> [!IMPORTANT]
> Führen Sie diesen Schritt aus, damit Ihnen nach der Verschiebung keine Gebühren für die Site Recovery-Replikation in Rechnung gestellt werden. Die Quellreplikationseinstellungen werden automatisch bereinigt. Beachten Sie, dass die im Rahmen der Replikation installierte Site Recovery-Erweiterung nicht entfernt wird und manuell gelöscht werden muss.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Verfügbarkeit eines virtuellen Azure-Computers durch Verschieben in eine Verfügbarkeitsgruppe oder -zone erhöht. Nun können Sie die Notfallwiederherstellung für den verschobenen virtuellen Computer festlegen.

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung nach der Migration](azure-to-azure-quickstart.md)


