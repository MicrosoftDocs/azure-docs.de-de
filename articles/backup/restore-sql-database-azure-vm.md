---
title: Wiederherstellen gesicherter SQL Server-Datenbanken auf einem virtuellen Azure-Computer mithilfe von Azure Backup | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie SQL Server-Datenbanken wiederherstellen, die auf einem virtuellen Azure-Computer ausgeführt und mit Azure Backup gesichert werden.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: d8ade598e4f1b6331367e8bd04ad59951ef5de8f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242368"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Wiederherstellen von SQL Server-Datenbanken auf virtuellen Azure-Computern

In diesem Artikel wird die Wiederherstellung einer SQL Server-Datenbank beschrieben, die auf einem virtuellen Azure-Computer ausgeführt wird und mithilfe des Diensts [Azure Backup](backup-overview.md) in einem Azure Backup Recovery Services-Tresor gesichert wurde.

Dieser Artikel beschreibt die Wiederherstellung von SQL Server-Datenbanken. Weitere Informationen finden Sie unter [Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Wiederherstellen eines Zeitpunkts oder eines Wiederherstellungspunkts

Azure Backup kann auf virtuellen Azure-Computern ausgeführte SQL Server-Datenbanken wie folgt wiederherstellen:

- Wiederherstellung eines bestimmten Datums oder einer bestimmten Uhrzeit (sekundengenau) mithilfe von Transaktionsprotokollsicherungen. Azure Backup ermittelt automatisch die geeignete vollständige differenzielle Sicherung und die Kette von Protokollsicherungen, die für die Wiederherstellung Ihrer Daten basierend auf dem ausgewählten Zeitpunkt benötigt werden.
- Wiederherstellung einer bestimmten vollständigen oder differenziellen Sicherung, um die Daten eines bestimmten Wiederherstellungspunkts wiederherzustellen.


## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie vor dem Wiederherstellen einer Datenbank Folgendes:

- Sie können die Datenbank auf einer SQL Server-Instanz in derselben Azure-Region wiederherstellen.
- Der Zielserver muss bei demselben Tresor wie die Quelle registriert werden.
- Wenn Sie eine mit TDE verschlüsselte Datenbank in einer anderen SQL Server-Instanz wiederherstellen möchten, müssen Sie zuerst [das Zertifikat auf dem Zielserver wiederherstellen](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Starten Sie vor der Wiederherstellung der Masterdatenbank die SQL Server-Instanz mit der Startoption **-m AzureWorkloadBackup** im Einzelbenutzermodus.
    - Der Wert für **-m** ist der Name des Clients.
    - Die Verbindung kann nur vom angegebenen Clientnamen geöffnet werden.
- Beenden Sie für alle Systemdatenbanken („model“, „master“, „msdb“) vor dem Auslösen der Wiederherstellung den SQL Server-Agent-Dienst.
- Schließen Sie alle Anwendungen, die ggf. versuchen, eine Verbindung mit einer dieser Datenbanken zu verwenden.
- Wenn auf einem Server mehrere Instanzen ausgeführt werden, müssen alle Instanzen aktiv sein. Andernfalls wird der Server nicht in der Liste mit den Zielservern für die Datenbankwiederherstellung angezeigt.

## <a name="restore-a-database"></a>Wiederherstellen einer Datenbank

Zum Wiederherstellen benötigen Sie folgende Berechtigungen:

* Berechtigungen vom Typ **Sicherungsoperator** in dem Tresor, in dem Sie die Wiederherstellung durchführen.
* Zugriff **Mitwirkender (Schreiben)** auf die gesicherte Quell-VM.
* Zugriff **Mitwirkender (Schreiben)** auf die Ziel-VM:
    - Wenn Sie die Daten auf dem gleichen virtuellen Computer wiederherstellen, handelt es sich hierbei um den virtuellen Quellcomputer.
    - Wenn Sie als Ziel für die Wiederherstellung einen anderen Speicherort verwenden, handelt es sich hierbei um den neuen virtuellen Zielcomputer.

Die Wiederherstellung wird wie folgt durchgeführt:
1. Öffnen Sie den Tresor, in dem die SQL Server-VM registriert ist.
2. Wählen Sie im Dashboard des Tresors unter **Nutzung** die Option **Sicherungselemente** aus.
3. Wählen Sie im Menü **Sicherungselemente** unter **Sicherungsverwaltungstyp** die Option **SQL Server in Azure-VM** aus.

    ![Auswählen von „SQL Server in Azure-VM“](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Wählen Sie die wiederherzustellende Datenbank aus.

    ![Auswählen der wiederherzustellenden Datenbank](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Überprüfen Sie das Datenbankmenü. Es enthält folgende Informationen über die Datenbanksicherung:

    * Die ältesten und neuesten Wiederherstellungspunkte
    * Den Protokollsicherungsstatus der letzten 24 Stunden (für Datenbanken in einem vollständigen und massenprotokollierten Wiederherstellungsmodus, die für Transaktionsprotokollsicherungen konfiguriert sind)

6. Wählen Sie **Datenbank wiederherstellen** aus.

    ![Auswählen von „Datenbank wiederherstellen“](./media/backup-azure-sql-database/restore-db-button.png)

7. Geben Sie unter **Wiederherstellungskonfiguration** an, wo die Daten wiederhergestellt werden sollen:
   - **Alternativer Standort:** Die Datenbank wird an einem alternativen Speicherort wiederhergestellt, und die ursprüngliche Quelldatenbank bleibt erhalten.
   - **Datenbank überschreiben:** Die Daten werden auf derselben SQL Server-Instanz wiederhergestellt, auf der sich auch die ursprüngliche Quelle befunden hat. Bei dieser Option wird die ursprüngliche Datenbank überschrieben.

     > [!Important]
     > Wenn die ausgewählte Datenbank zu einer Always On-Verfügbarkeitsgruppe gehört, lässt SQL Server das Überschreiben der Datenbank nicht zu. Nur **Alternativer Speicherort** ist verfügbar.
     >

     ![Menü „Konfiguration wiederherstellen“](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Wiederherstellen an einem alternativen Speicherort

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Zielort der Wiederherstellung** die Option **Alternativer Speicherort** aus.
2. Wählen Sie den SQL Server-Namen und die Instanz aus, in der Sie die Datenbank wiederherstellen möchten.
3. Geben Sie im Feld **Name der wiederhergestellten Datenbank** den Namen der Zieldatenbank ein.
4. Aktivieren Sie ggf. **Überschreiben, wenn die gleichnamige Datenbank bereits in der ausgewählten SQL-Instanz vorhanden ist**.
5. Klicken Sie auf **OK**.

    ![Angeben von Werten für das Menü „Konfiguration wiederherstellen“](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Wählen Sie unter **Wiederherstellungspunkt auswählen** aus, ob Sie [einen bestimmten Zeitpunkt](#restore-to-a-specific-point-in-time) oder einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-restore-point) wiederherstellen möchten.

    > [!NOTE]
    > Die Point-in-Time-Wiederherstellung ist nur für Transaktionsprotokollsicherungen für Datenbanken in einem vollständigen und massenprotokollierten Wiederherstellungsmodus verfügbar.

### <a name="restore-and-overwrite"></a>Wiederherstellen und Überschreiben

1. Wählen Sie im Menü **Wiederherstellungskonfiguration** unter **Zielort der Wiederherstellung** die Option **Datenbank überschreiben** > **OK** aus.

    ![Auswählen von „Datenbank überschreiben“](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Wählen Sie unter **Wiederherstellungspunkt auswählen** die Option **Protokolle (Zeitpunkt)** aus, um [einen bestimmten Zeitpunkt wiederherzustellen](#restore-to-a-specific-point-in-time). Oder wählen Sie **Vollständig und differenziell** aus, um einen [bestimmten Wiederherstellungspunkt](#restore-to-a-specific-restore-point) wiederherzustellen.

    > [!NOTE]
    > Die Point-in-Time-Wiederherstellung ist nur für Transaktionsprotokollsicherungen für Datenbanken in einem vollständigen und massenprotokollierten Wiederherstellungsmodus verfügbar.

### <a name="restore-to-a-specific-point-in-time"></a>Wiederherstellen eines bestimmten Zeitpunkts

Wenn Sie **Protokolle (Zeitpunkt)** als Wiederherstellungstyp ausgewählt haben, gehen Sie folgendermaßen vor:

1.  Öffnen Sie unter **Datum/Uhrzeit für Wiederherstellung** den Kalender. Im Kalender sind Tage, für die Wiederherstellungspunkte vorhanden sind, fett formatiert, und das aktuelle Datum ist hervorgehoben.
1. Wählen Sie ein Datum mit Wiederherstellungspunkten aus. Tage ohne Wiederherstellungspunkte können nicht ausgewählt werden.

    ![Öffnen des Kalenders](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Wenn Sie ein Datum ausgewählt haben, zeigt das Zeitachsendiagramm die verfügbaren Wiederherstellungspunkte in einem fortlaufenden Bereich an.
1. Geben Sie auf der Zeitleiste eine Uhrzeit für die Wiederherstellung an, oder wählen Sie eine Uhrzeit aus. Wählen Sie dann **OK**aus.

    ![Auswählen einer Uhrzeit für die Wiederherstellung](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Aktivieren Sie im Menü **Erweiterte Konfiguration** die Option **Mit NORECOVERY wiederherstellen**, wenn die Datenbank nach der Wiederherstellung nicht in Betrieb gehen werden soll.
1. Wenn Sie den Speicherort für die Wiederherstellung auf dem Zielserver ändern möchten, geben Sie einen neuen Zielpfad ein.
1. Klicken Sie auf **OK**.

    ![Menü „Erweiterte Konfiguration“](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Wählen Sie im Menü **Wiederherstellen** **Wiederherstellen** aus, um den Wiederherstellungsauftrag zu starten.
1. Verfolgen Sie den Fortschritt der Wiederherstellung im Bereich **Benachrichtigungen**, oder wählen Sie im Datenbankmenü die Option **Wiederherstellungsaufträge** aus.

    ![Fortschritt des Wiederherstellungsauftrags](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Wiederherstellen eines bestimmten Wiederherstellungspunkts

Wenn Sie **Vollständig und differenziell** als Wiederherstellungstyp ausgewählt haben, gehen Sie folgendermaßen vor:

1. Wählen Sie aus der Liste einen Wiederherstellungspunkt aus, und wählen Sie **OK** aus, um die Prozedur der Wiederherstellungspunkte abzuschließen.

    ![Auswählen eines vollständigen Wiederherstellungspunkts](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Aktivieren Sie im Menü **Erweiterte Konfiguration** die Option **Mit NORECOVERY wiederherstellen**, wenn die Datenbank nach der Wiederherstellung nicht in Betrieb gehen werden soll.
1. Wenn Sie den Speicherort für die Wiederherstellung auf dem Zielserver ändern möchten, geben Sie einen neuen Zielpfad ein.
1. Klicken Sie auf **OK**.

    ![Menü „Erweiterte Konfiguration“](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Wählen Sie im Menü **Wiederherstellen** **Wiederherstellen** aus, um den Wiederherstellungsauftrag zu starten.
1. Verfolgen Sie den Fortschritt der Wiederherstellung im Bereich **Benachrichtigungen**, oder wählen Sie im Datenbankmenü die Option **Wiederherstellungsaufträge** aus.

    ![Fortschritt des Wiederherstellungsauftrags](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Wiederherstellen von Datenbanken mit zahlreichen Dateien

Wenn die gesamte Zeichenfolgengröße von Dateien in einer Datenbank einen [bestimmten Grenzwert](backup-sql-server-azure-troubleshoot.md#files-size-limit-beyond-which-restore-happens-to-default-path) übersteigt, speichert Azure Backup die Liste mit den Datenbankdateien in einer anderen PIT-Komponente, sodass Sie während des Wiederherstellungsvorgangs den Wiederherstellungszielpfad nicht angegeben können. Die Dateien werden stattdessen am SQL-Standardpfad wiederhergestellt.

  ![Wiederherstellen einer Datenbank mit großer Datei](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>Nächste Schritte

[Verwalten und überwachen](manage-monitor-sql-database-backup.md) Sie SQL Server-Datenbanken, die mit Azure Backup gesichert wurden.
