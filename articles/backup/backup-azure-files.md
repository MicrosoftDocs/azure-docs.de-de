---
title: Sichern von Azure-Dateifreigaben
description: Dieser Artikel enthält ausführliche Informationen zum Sichern und Wiederherstellen von Azure-Dateifreigaben sowie Informationen zu Verwaltungsaufgaben.
services: backup
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 30544a49f49714eeefbf54d70e54275d2cf9a7ef
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243547"
---
# <a name="back-up-azure-file-shares"></a>Sichern von Azure-Dateifreigaben
In diesem Artikel erfahren Sie, wie Sie mithilfe des Azure-Portals [Azure Dateifreigaben](../storage/files/storage-files-introduction.md) sichern und wiederherstellen.

In diesem Artikel lernen Sie Folgendes:
> [!div class="checklist"]
> * Konfigurieren eines Recovery Services-Tresors zum Sichern von Azure Files
> * Ausführen eines bedarfsbasierten Sicherungsauftrags zum Erstellen eines Wiederherstellungspunkts
> * Wiederherstellen von Dateien auf der Grundlage eines Wiederherstellungspunkts
> * Verwalten von Sicherungsaufträgen
> * Beenden des Schutzes für Azure Files
> * Löschen der Sicherungsdaten

## <a name="prerequisites"></a>Voraussetzungen
Um eine Azure-Dateifreigabe sichern zu können, muss sie sich unter einem der [unterstützten Speicherkontotypen](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) befinden. Sobald Sie dies überprüft haben, können Sie Ihre Dateifreigaben schützen.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Einschränkungen beim Sichern von Azure-Dateifreigaben während der Vorschauphase
Die Sicherung für Azure-Dateifreigaben befindet sich in der Vorschauphase. Azure-Dateifreigaben in Speicherkonten vom Typ „Universell V1“ und „Universell V2“ werden unterstützt. Folgende Sicherungsszenarien werden für Azure-Dateifreigaben nicht unterstützt:
- Sie können Azure-Dateifreigaben in Speicherkonten mit aktivierten virtuellen Netzwerken oder aktivierter Firewall nicht schützen.
- Für den Schutz von Azure Files mit Azure Backup ist keine Befehlszeilenschnittstelle verfügbar.
- Die Anzahl geplanter Sicherungen ist auf eine Sicherung pro Tag begrenzt.
- Die Anzahl bedarfsgesteuerter Sicherungen ist auf vier Sicherungen pro Tag begrenzt.
- Verwenden Sie [Ressourcensperren](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) für das Speicherkonto, um das versehentliche Löschen von Sicherungen in Ihrem Recovery Services-Tresor zu verhindern.
- Löschen Sie keine Momentaufnahmen, die mit Azure Backup erstellt wurden. Das Löschen von Momentaufnahmen kann zum Verlust von Wiederherstellungspunkten bzw. zu Wiederherstellungsfehlern führen.
- Löschen Sie keine Dateifreigaben, die durch Azure Backup geschützt sind. In der aktuellen Lösung werden nach dem Löschen der Dateifreigabe alle von Azure Backup erstellten Momentaufnahmen gelöscht, sodass alle Wiederherstellungspunkte verloren gehen.

Die Sicherung für Azure-Dateifreigaben in Speicherkonten mit Replikation vom Typ [ZRS](../storage/common/storage-redundancy-zrs.md) (Zone Redundant Storage, zonenredundanter Speicher) steht momentan nur in den Regionen „USA, Mitte“ (CUS), „USA, Osten (EUS), „USA, Osten 2“ (EUS2), „Europa, Norden“ (NE), „Asien, Südosten“ (SEA), „Europa, Westen“ (WE) und „USA, Westen 2“ (WUS2) zur Verfügung.

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurieren der Sicherung für eine Azure-Dateifreigabe
In diesem Tutorial wird davon ausgegangen, dass Sie bereits eine Azure-Dateifreigabe eingerichtet haben. So sichern Sie Ihre Azure-Dateifreigabe

1. Erstellen Sie einen Recovery Services-Tresor in der Region, in der sich auch die Dateifreigabe befindet. Wenn Sie bereits einen Tresor besitzen, öffnen Sie die Seite „Übersicht“ des Tresors, und klicken Sie auf **Sicherung**.

    ![Auswählen einer Azure-Dateifreigabe als Sicherungsziel](./media/backup-file-shares/overview-backup-page.png)

2. Wählen Sie im Menü **Sicherungsziel** unter **Was möchten Sie sichern?** die Option „Azure-Dateifreigabe“.

    ![Auswählen einer Azure-Dateifreigabe als Sicherungsziel](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Klicken Sie auf **Sicherung**, um die Azure-Dateifreigabe für Ihren Recovery Services-Tresor zu konfigurieren.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/set-backup-goal.png)

    Wenn der Tresor der Azure-Dateifreigabe zugeordnet wurde, wird das Menü „Sicherung“ geöffnet, und Sie werden zur Auswahl eines Speicherkontos aufgefordert. Im Menü werden alle unterstützten Speicherkonten in der Region des Tresors angezeigt, die noch keinem Recovery Services-Tresor zugeordnet sind.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/list-of-storage-accounts.png)

4. Wählen Sie in der Liste der Speicherkonten ein Speicherkonto aus, und klicken Sie auf **OK**. Azure durchsucht das Speicherkonto nach Dateifreigaben, die gesichert werden können. Falls Sie die Dateifreigaben erst kürzlich hinzugefügt haben und diese nicht in der Liste angezeigt werden, warten Sie einen Moment, bis die Dateifreigaben angezeigt werden.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/discover-file-shares.png)

5. Wählen Sie in der Liste **Dateifreigaben** mindestens eine zu sichernde Dateifreigabe aus, und klicken Sie auf **OK**.

6. Nach dem Auswählen der Dateifreigaben wechselt das Menü „Sicherung“ zur **Sicherheitsrichtlinie**. Wählen Sie in diesem Menü entweder eine vorhandene Sicherheitsrichtlinie aus, oder erstellen Sie eine neue, und klicken Sie dann auf **Sicherung aktivieren**.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/apply-backup-policy.png)

    Nach dem Einrichten einer Sicherungsrichtlinie wird eine Momentaufnahme der Dateifreigaben zum geplanten Zeitpunkt erstellt, und der Wiederherstellungspunkt wird für den ausgewählten Zeitraum aufbewahrt.

## <a name="create-an-on-demand-backup"></a>Erstellen einer bedarfsgesteuerten Sicherung
Gelegentlich empfiehlt es sich, eine Sicherungsmomentaufnahme oder einen Wiederherstellungspunkt außerhalb der geplanten Zeiten in der Sicherungsrichtlinie zu erstellen. Häufig wird eine bedarfsgesteuerte Sicherung direkt nach dem Konfigurieren der Sicherungsrichtlinie erstellt. Basierend auf den Zeitplan in der Sicherungsrichtlinie, kann es Stunden oder Tage dauern, bis eine Momentaufnahme erstellt wird. Initiieren Sie eine bedarfsgesteuerte Sicherung, um Ihre Daten zu schützen, bevor die Sicherungsrichtlinie in Kraft tritt. Die Erstellung einer bedarfsgesteuerten Sicherung ist häufig erforderlich, bevor Sie geplante Änderungen an den Dateifreigaben vornehmen.

### <a name="to-create-an-on-demand-backup"></a>So erstellen Sie eine bedarfsgesteuerte Sicherung

1. Öffnen Sie den Recovery Services-Tresor, der die Wiederherstellungspunkte der Dateifreigaben enthält, und klicken Sie auf **Sicherungselemente**. Die Liste der Sicherungselementtypen wird angezeigt.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/list-of-backup-items.png)

2. Wählen Sie in der Liste die Option **Azure Storage (Azure Files)** aus. Die Liste der Azure-Dateifreigaben wird angezeigt.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Wählen Sie in der Liste der Azure-Dateifreigaben die gewünschte Dateifreigabe aus. Das Menü „Sicherungselemente“ wird für die ausgewählte Dateifreigabe geöffnet.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/backup-item-menu.png)

4. Klicken Sie im Menü „Sicherungselemente“ auf **Jetzt sichern**. Da es sich um einen bedarfsgesteuerten Sicherungsauftrag handelt, ist dem Wiederherstellungspunkt keine Aufbewahrungsrichtlinie zugeordnet. Das Dialogfeld **Jetzt sichern** wird geöffnet. Geben Sie den Tag an, bis zu dem der Wiederherstellungspunkt aufbewahrt werden soll.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Wiederherstellen aus der Sicherung einer Azure-Dateifreigabe
Wenn Sie eine gesamte Dateifreigabe oder einzelne Dateien oder Ordner auf der Grundlage eines Wiederherstellungspunkts wiederherstellen müssen, navigieren Sie zu „Sicherungselemente“, wie im vorherigen Abschnitt erläutert. Wählen Sie **Restore Share** (Freigabe wiederherstellen), um für eine gesamte Dateifreigabe eine Zeitpunktwiederherstellung durchzuführen. Wählen Sie in der Liste der angezeigten Wiederherstellungspunkte einen Punkt aus, um die aktuelle Dateifreigabe zu überschreiben oder den Punkt als alternative Dateifreigabe in der gleichen Region wiederherzustellen.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Wiederherstellen einzelner Dateien oder Ordner aus einer Sicherung von Azure-Dateifreigaben
Azure Backup bietet die Option, einen Wiederherstellungspunkt im Azure-Portal zu durchsuchen. Klicken Sie zum Wiederherstellen der gewünschten Datei oder des gewünschten Ordners auf der Seite „Sicherungselemente“ auf „Dateiwiederherstellung“, und treffen Sie in der Liste der Wiederherstellungspunkte Ihre Auswahl. Wählen Sie das Wiederherstellungsziel aus, und klicken Sie dann auf **Datei auswählen**, um den Wiederherstellungspunkt zu durchsuchen. Wählen Sie die gewünschte Datei bzw. den gewünschten Ordner aus, und klicken Sie auf **Wiederherstellen**.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Verwalten der Sicherungen von Azure-Dateifreigaben

Sie können auf der Seite **Sicherungsaufträge** verschiedene Verwaltungsaufgaben für Dateifreigabesicherungen ausführen:
- [Überwachen von Aufträgen](backup-azure-files.md#monitor-jobs)
- [Erstellen einer neuen Richtlinie](backup-azure-files.md#create-a-new-policy)
- [Beenden des Schutzes für eine Dateifreigabe](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Fortsetzen des Schutzes für eine Dateifreigabe](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Löschen von Sicherungsdaten](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Überwachen von Aufträgen

Sie können den Status aller Aufträge auf der Seite **Sicherungsaufträgen** überwachen.

So öffnen Sie die Seite **Sicherungsaufträge**

- Öffnen Sie den zu überwachenden Recovery Services-Tresor, und klicken Sie im Menü des Recovery Services-Tresors auf **Aufträge** und anschließend auf **Sicherungsaufträge**.

   ![Auswählen des zu überwachenden Auftrags](./media/backup-file-shares/open-backup-jobs.png)

    Die Liste der Sicherungsaufträge und der Status dieser Aufträge werden angezeigt.

    ![Auswählen des zu überwachenden Auftrags](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Erstellen einer neuen Richtlinie

Sie können im Recovery Services-Tresor über **Sicherungsrichtlinien** eine neue Richtlinie zum Sichern von Azure-Dateifreigaben erstellen. Alle beim Konfigurieren der Dateifreigabensicherung erstellten Richtlinien werden mit dem Richtlinientyp „Azure-Dateifreigabe“ angezeigt.

So zeigen Sie die vorhandenen Sicherungsrichtlinien an

- Öffnen Sie den gewünschten Recovery Services-Tresor, und klicken Sie im Menü des Recovery Services-Tresors auf **Sicherungsrichtlinien**. Alle Sicherungsrichtlinien werden aufgeführt.

   ![Auswählen des zu überwachenden Auftrags](./media/backup-file-shares/list-of-backup-policies.png)

So erstellen Sie eine neue Sicherungsrichtlinie

1. Klicken Sie im Menü „Recovery Services-Tresor“ auf **Sicherungsrichtlinien**.
2. Klicken Sie in der Liste der Sicherungsrichtlinien auf **Hinzufügen**.

   ![Auswählen des zu überwachenden Auftrags](./media/backup-file-shares/new-backup-policy.png)

3. Wählen Sie im Menü **Hinzufügen** die Option **Azure-Dateifreigabe**. Das Menü „Sicherungsrichtlinie“ für Azure-Dateifreigaben wird geöffnet. Geben Sie den Namen für die Richtlinie, die Sicherungshäufigkeit und die Aufbewahrungsdauer für die Wiederherstellungspunkte an. Klicken Sie auf „OK“, wenn Sie die Richtlinie definiert haben.

   ![Auswählen des zu überwachenden Auftrags](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Beenden des Schutzes für eine Azure-Dateifreigabe

Wenn Sie sich für das Beenden des Schutzes für eine Azure-Dateifreigabe entscheiden, werden Sie gefragt, ob Sie die Wiederherstellungspunkte beibehalten möchten. Der Schutz für Azure-Dateifreigaben kann auf zwei Arten beendet werden:

- Beenden aller zukünftigen Sicherungsaufträge und Löschen aller Wiederherstellungspunkte oder
- Beenden aller zukünftigen Sicherungsaufträge und Beibehaltung der Wiederherstellungspunkte

Unter Umständen fallen für die Aufbewahrung der Wiederherstellungspunkte im Speicher Gebühren an, da die von Azure Backup erstellten zugrundeliegenden Momentaufnahmen beibehalten werden. Der Vorteil der Beibehaltung von Wiederherstellungspunkten ist, dass Sie die Dateifreigabe bei Bedarf später wiederherstellen können. Informationen zu den Kosten, die durch die Beibehaltung der Wiederherstellungspunkte entstehen, finden Sie in der Preisübersicht. Wenn Sie alle Wiederherstellungspunkte löschen, kann die Dateifreigabe nicht mehr wiederhergestellt werden.

So beenden Sie den Schutz für eine Azure-Dateifreigabe

1. Öffnen Sie den Recovery Services-Tresor, der die Wiederherstellungspunkte der Dateifreigaben enthält, und klicken Sie auf **Sicherungselemente**. Die Liste der Sicherungselementtypen wird angezeigt.

   ![Klicken auf „Sicherung“, um die Azure-Dateifreigabe mit dem Tresor zu verknüpfen](./media/backup-file-shares/list-of-backup-items.png)

2. Wählen Sie in der Liste **Backup Management Type** (Sicherungsverwaltungstyp) den Eintrag **Azure Storage (Azure Files)** aus. Die Liste der Sicherungselemente für „Azure Storage (Azure Files)“ wird angezeigt.

   ![Klicken auf ein Element, um ein weiteres Menü zu öffnen](./media/backup-file-shares/azure-file-share-backup-items.png)

3. Wählen Sie in der Liste der Sicherungselemente (Azure Storage (Azure Files)) das Sicherungselement aus, für das der Schutz beendet werden soll.

4. Klicken Sie in den Azure-Dateifreigabeelementen auf das Menü **Mehr** und dann auf **Sicherung beenden**.

   ![Klicken auf ein Element, um ein weiteres Menü zu öffnen](./media/backup-file-shares/stop-backup.png)

5. Klicken Sie im Menü „Sicherung beenden“ auf **Beibehalten** oder auf **Sicherungsdaten löschen** und dann auf **Sicherung beenden**.

   ![Klicken auf ein Element, um ein weiteres Menü zu öffnen](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Fortsetzen des Schutzes für eine Azure-Dateifreigabe

Wenn beim Beenden des Schutzes für die Dateifreigabe die Option „Sicherungsdaten beibehalten“ ausgewählt wurde, kann der Schutz fortgesetzt werden. Wenn die Option **Sicherungsdaten löschen** ausgewählt wurde, kann der Schutz für die Dateifreigabe nicht fortgesetzt werden.

Wenn Sie den Schutz für die Dateifreigabe fortsetzen möchten, wechseln Sie zum Sicherungselement, und klicken Sie auf „Sicherung fortsetzen“. Die Sicherungsrichtlinie wird geöffnet, und Sie können eine Richtlinie Ihrer Wahl zum Fortsetzen der Sicherung auswählen.

   ![Auswählen des zu überwachenden Auftrags](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Löschen von Sicherungsdaten

Sie können die Sicherung einer Dateifreigabe während des Auftrags zum Beenden der Sicherung oder jederzeit nach Beenden des Schutzes löschen. Es kann gegebenenfalls sogar von Vorteil sein, mit dem Löschen der Wiederherstellungspunkte mehrere Tage oder Wochen zu warten. Im Gegensatz zur Wiederherstellung von Wiederherstellungspunkten können Sie beim Löschen von Sicherungsdaten keine bestimmten Wiederherstellungspunkte auswählen, die gelöscht werden sollen. Wenn Sie sich für die Löschung Ihrer Sicherungsdaten entscheiden, werden alle dem Element zugeordneten Wiederherstellungspunkte gelöscht.

Beim folgenden Verfahren wird davon ausgegangen, dass der Sicherungsauftrag für den virtuellen Computer beendet wurde. Nach dem Beenden des Sicherungsauftrags sind die Optionen „Sicherung fortsetzen“ und „Sicherungsdaten löschen“ auf dem Dashboard für die Sicherungselemente verfügbar. Klicken Sie auf „Sicherungsdaten löschen“, und geben Sie den Namen der Dateifreigabe ein, um den Löschvorgang zu bestätigen. Geben Sie optional einen Grund oder einen Kommentar für den Löschvorgang an.

## <a name="see-also"></a>Siehe auch
Weitere Informationen zu Azure-Dateifreigaben finden Sie in den folgenden Artikeln:
- [Fragen zum Sichern von Azure Files](backup-azure-files-faq.md)
- [Problembehandlung beim Sichern von Azure-Dateifreigaben](troubleshoot-azure-files.md)
