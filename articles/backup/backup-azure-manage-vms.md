---
title: Verwalten und Überwachen von Azure-VM-Sicherungen mit dem Azure Backup-Dienst
description: Erfahren Sie mehr über das Verwalten und Überwachen von Azure-VM-Sicherungen mit dem Azure Backup-Dienst.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: aa953440f03137f3359276bc9e06cb0c73f0ab4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61219087"
---
# <a name="manage-azure-vm-backups"></a>Verwalten von Azure-VM-Sicherungen

In diesem Artikel erfahren Sie mehr über die Verwaltung von Azure-VMs, die mit dem [Azure Backup-Dienst](backup-overview.md) gesichert wurden. Außerdem werden in diesem Artikel die Sicherungsinformationen auf dem Tresordashboard zusammengefasst.


Im Azure-Portal ermöglicht das Dashboard für den Recovery Services-Tresor den Zugriff auf Informationen zum Tresor, z. B.:

* Die letzte Sicherung, die gleichzeitig der letzte Wiederherstellungspunkt ist.
* Die Sicherungsrichtlinie.
* Die Gesamtgröße aller Sicherungsmomentaufnahmen.
* Die Anzahl von virtuellen Computern, die für die Sicherung aktiviert sind.

Sie können Backups über das Dashboard und durch ein Drilldown auf einzelne virtuelle Computer verwalten. Öffnen Sie den Tresor über das Dashboard, um mit dem Sichern von virtuellen Computern zu beginnen.

![Vollständige Dashboardansicht mit Schieberegler](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Anzeigen von virtuellen Computern auf dem Dashboard

So zeigen Sie virtuelle Computer auf dem Tresordashboard an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Menü „Hub“ die Option **Durchsuchen** aus. Geben Sie in der Liste mit den Ressourcen **Recovery Services**ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Recovery Services-Tresore**.

    ![Erstellen eines Recovery Services-Tresors](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Klicken Sie mit der rechten Maustaste auf den Tresor, und wählen Sie **An Dashboard anheften** aus.
4. Öffnen Sie das Dashboard des Tresors.

    ![Öffnen des Tresor-Dashboards und des Blatts „Einstellungen“](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Wählen Sie auf der Kachel **Sicherungselemente** die Option **Virtuelle Azure-Computer** aus.

    ![Öffnen der Kachel „Sicherungselemente“](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. Auf dem Blatt  **Sicherungselemente** können Sie die Liste der geschützten virtuellen Computer anzeigen. In diesem Beispiel schützt der Tresor einen virtuellen Computer: demobackup.  

    ![Anzeigen des Blatts „Sicherungselemente“](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Im Dashboard für Tresorobjekte können Sie Sicherungsrichtlinien ändern, eine On-Demand-Sicherung durchführen, den Schutz von virtuellen Computern beenden und wieder aufnehmen, Sicherungsdaten löschen, Wiederherstellungspunkte anzeigen und eine Wiederherstellung durchführen.

    ![Dashboard „Sicherungselemente“ mit Blatt „Einstellungen“](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Verwalten der Sicherungsrichtlinie für einen virtuellen Computer

So verwalten Sie eine Sicherungsrichtlinie:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Öffnen Sie das Dashboard des Tresors.
2. Wählen Sie auf der Kachel **Sicherungselemente** die Option **Virtuelle Azure-Computer** aus.

    ![Öffnen der Kachel „Sicherungselemente“](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. Auf dem Blatt  **Sicherungselemente** können Sie die Liste der geschützten virtuellen Computer und den Status der letzten Sicherung mit dem Zeitpunkt der letzten Wiederherstellung anzeigen.

    ![Anzeigen des Blatts „Sicherungselemente“](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Auf dem Dashboard für Tresorelemente können Sie eine Sicherungsrichtlinie auswählen.

   * Wählen Sie zum Ändern von Richtlinien eine andere Richtlinie und dann **Speichern** aus. Die neue Richtlinie wird sofort auf den Tresor angewendet.

     ![Auswählen einer Sicherungsrichtlinie](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung
Sie können eine On-Demand-Sicherung eines virtuellen Computers ausführen, nachdem Sie dessen Schutz konfiguriert haben. Beachten Sie dabei Folgendes:

- Wenn die erste Sicherung ansteht, erstellt die On-Demand-Sicherung eine vollständige Kopie des virtuellen Computers im Recovery Services-Tresor.
- Nachdem die erste Sicherung durchgeführt wurde, werden bei einer On-Demand-Sicherung nur Änderungen der vorherigen Momentaufnahme an den Recovery Services-Tresor gesendet. Spätere Sicherungen erfolgen also immer inkrementell.
- Die Aufbewahrungsdauer für eine On-Demand Sicherung ist der Aufbewahrungswert, den Sie beim Auslösen des Sicherungsvorgangs festlegen.

So lösen Sie eine On-Demand-Sicherung aus:

1. Wählen Sie auf dem[Dashboard für die Tresorelemente](#view-vms-on-the-dashboard) unter **Geschütztes Element** die Option **Sicherungselement** aus.

    ![Die Option „Jetzt sichern“](./media/backup-azure-manage-vms/backup-now-button.png)

2. Wählen Sie unter **Sicherungsverwaltungstyp** die Option **Virtueller Azure-Computer** aus. Das Blatt **Sicherungselemente (Virtueller Azure-Computer)** wird angezeigt.
3. Wählen Sie einen virtuellen Computer und dann **Jetzt sichern** aus, um eine On-Demand-Sicherung zu erstellen. Das Blatt **Jetzt sichern** wird angezeigt.
4. Geben Sie im Feld **Sicherung beibehalten bis** ein Datum ein, bis zu dem die Sicherung beibehalten werden soll.

    ![Der Kalender „Jetzt sichern“](./media/backup-azure-manage-vms/backup-now-check.png)

5. Wählen Sie **OK** aus, um die Sicherung auszuführen.

Wählen Sie auf dem Tresordashboard die Kachel **Sicherungsaufträge** aus, um den Auftragsstatus nachzuverfolgen.

## <a name="stop-protecting-a-vm"></a>Beenden des Schutzes für eine VM

Sie haben zwei Möglichkeiten, den Schutz einer virtuellen Maschine zu beenden:

- Beenden aller zukünftigen Sicherungsaufträge und Löschen aller Wiederherstellungspunkte In diesem Fall kann der virtuelle Computer nicht wiederhergestellt werden.
- Beenden aller zukünftigen Sicherungsaufträge und Beibehalten der Wiederherstellungspunkte: Sie können den virtuellen Computer bei Bedarf wiederherstellen, müssen jedoch für das Beibehalten der Wiederherstellungspunkte im Tresor bezahlen. Weitere Informationen finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Wenn Sie eine Datenquelle löschen, ohne die Sicherungen zu beenden, treten bei neuen Sicherungen Fehler auf. Alte Wiederherstellungspunkte laufen gemäß der Richtlinie ab. Ein letzter Wiederherstellungspunkt wird jedoch immer beibehalten, bis Sie die Sicherungen beenden und die Daten löschen.
>

So beenden Sie den Schutz für einen virtuellen Computer:

1. Wählen Sie auf dem [Dashboard für die Tresorelemente](#view-vms-on-the-dashboard) die Option **Sicherung beenden** aus.
2. Wählen Sie aus, ob die Sicherungsdaten beibehalten oder gelöscht werden sollen, und bestätigen Sie Ihre Auswahl. Geben Sie bei Bedarf einen Kommentar ein. Falls Sie den Namen des Elements nicht kennen, zeigen Sie mit dem Cursor auf das Ausrufezeichen, um den Namen anzuzeigen.

    ![Schutz beenden](./media/backup-azure-manage-vms/retain-or-delete-option.png)

     Sie erhalten einer Benachrichtigung, wenn die Sicherungsaufträge beendet wurden.

## <a name="resume-protection-of-a-vm"></a>Fortsetzen des Schutzes einer VM

Wenn Sie beim Beenden des virtuellen Computers die Sicherungsdaten beibehalten, können Sie den Schutz später fortsetzen. Wenn Sie die Sicherungsdaten löschen, können Sie den Schutz nicht fortsetzen.

So setzen Sie den Schutz für einen virtuellen Computer fort:

1. Wählen Sie auf dem [Dashboard für die Tresorelemente](#view-vms-on-the-dashboard) die Option **Sicherung fortsetzen** aus.

2. Führen Sie die Schritte unter [Verwalten von Sicherungsrichtlinien](#manage-backup-policy-for-a-vm) aus, um die Richtlinie für den virtuellen Computer zuzuweisen. Sie müssen nicht die ursprüngliche Schutzrichtlinie des virtuellen Computers auswählen.
3. Wenn Sie die Sicherungsrichtlinie auf dem virtuellen Computer angewendet haben, wird die folgende Meldung angezeigt:

    ![Meldung über erfolgreichen Schutz eines virtuellen Computers](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Sie können die Sicherungsdaten eines virtuellen Computers während des Auftrags **Sicherung beenden** oder nach Abschluss des Sicherungsauftrags löschen. Bevor Sie vor dem Löschen der Sicherungsdaten Folgendes:

- Möglicherweise ist es von Vorteil, mit dem Löschen der Wiederherstellungspunkte mehrere Tage oder Wochen zu warten.
- Im Gegensatz zum Wiederherstellen von Wiederherstellungspunkten können Sie beim Löschen von Sicherungsdaten keine bestimmten Wiederherstellungspunkte zum Löschen auswählen. Wenn Sie sich Ihre Sicherungsdaten löschen, werden alle zugehörigen Wiederherstellungspunkte gelöscht.

Wenn Sie den Sicherungsauftrag des virtuellen Computers beenden oder deaktivieren, können Sie die Sicherungsdaten löschen:


1. Wählen Sie auf dem [Dashboard für die Tresorelemente](#view-vms-on-the-dashboard) die Option **Sicherungsdaten löschen** aus.

    ![Auswählen von „Sicherung löschen“](./media/backup-azure-manage-vms/delete-backup-buttom.png)

1. Geben Sie den Namen des Sicherungselements ein, um zu bestätigen, dass Sie die Wiederherstellungspunkte löschen möchten.

    ![Bestätigen Sie, dass Sie die Wiederherstellungspunkte löschen möchten.](./media/backup-azure-manage-vms/item-verification-box.png)

1. Wählen Sie **Löschen** aus, um die Sicherungsdaten des Elements zu löschen. Sie erhalten einer Benachrichtigung, dass die Sicherungsdaten gelöscht wurden.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Sichern einer Azure-VM über die VM-Einstellungen](backup-azure-vms-first-look-arm.md).
- Erfahren Sie mehr über das [Wiederherstellen virtueller Azure-Computer](backup-azure-arm-restore-vms.md).
- Erfahren Sie mehr über das [Überwachen von Azure-VM-Sicherungen](backup-azure-monitor-vms.md).
