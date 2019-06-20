---
title: Sichern von SQL Server-Workloads auf Azure Stack
description: Verwenden Sie Azure Backup Server zum Schützen von SQL Server-Workloads auf Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: fb064c39fa014515fb2a3f4ccc96ce216f2f7b2e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60254480"
---
# <a name="back-up-sql-server-on-stack"></a>Sichern von SQL Server in Stack
Verwenden Sie diesen Artikel, um Microsoft Azure Backup Server (MABS) zum Schutz von SQL Server-Datenbanken auf Azure Stack zu konfigurieren.

Die Verwaltung der Sicherung und Wiederherstellung von SQL-Datenbanken in und aus Azure umfasst drei Schritte:

1. Erstellen einer Sicherungsrichtlinie zum Schutz von SQL Server-Datenbanken
2. Bedarfsgesteuertes Erstellen von Sicherungskopien
3. Wiederherstellen der Datenbank vom Datenträger und aus Azure

## <a name="before-you-start"></a>Vorbereitung

[Installieren und Vorbereiten von Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Erstellen einer Sicherungsrichtlinie zum Schutz von SQL Server-Datenbanken mithilfe von Azure
1. Klicken Sie auf der Azure Backup Server-Benutzeroberfläche auf den Arbeitsbereich **Schutz**.

2. Klicken Sie im Menüband auf **Neu** , um eine neue Schutzgruppe zu erstellen.

    ![Erstellen einer Schutzgruppe](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server startet den Schutzgruppen-Assistenten, der Sie schrittweise durch die Erstellung einer **Schutzgruppe** führt. Klicken Sie auf **Weiter**.

3. Wählen Sie unter **Schutzgruppentyp auswählen** die Option **Server** aus.

    ![Auswählen des Schutzgruppentyp – "Server"](./media/backup-azure-backup-sql/pg-servers.png)

4. Auf dem Bildschirm **Gruppenmitglieder auswählen** zeigt die Liste „Verfügbare Mitglieder“ die verschiedenen Datenquellen an. Klicken Sie auf **+** , um einen Ordner zu erweitern und die Unterordner anzuzeigen. Aktivieren Sie das Kontrollkästchen, um ein Element auszuwählen.

    ![Auswählen einer SQL-Datenbank](./media/backup-azure-backup-sql/pg-databases.png)

    Alle ausgewählten Elemente werden in der Liste „Ausgewählte Elemente“ angezeigt. Nach dem Auswählen der Server oder Datenbanken, die Sie schützen möchten, klicken Sie auf **Weiter**.

5. Geben Sie auf dem Bildschirm **Datenschutzmethode auswählen** einen Namen für die Schutzgruppe an, und aktivieren Sie das Kontrollkästchen **Ich möchte Onlineschutz**.

    ![Datenschutzmethode: kurzfristig auf Datenträger und online in Azure](./media/backup-azure-backup-sql/pg-name.png)

6. Geben Sie auf dem Bildschirm **Kurzfristige Ziele angeben** alle erforderlichen Informationen ein, um Sicherungspunkte auf dem Datenträger zu erstellen, und klicken Sie dann auf **Weiter**.

    Im Beispiel beträgt die **Beibehaltungsdauer** **5 Tage**, und die **Synchronisierungsfrequenz** ist ein Mal alle **15 Minuten**. Dies ist die Sicherungshäufigkeit. Der Wert für **Schnelle vollständige Sicherung** ist auf **20:00** festgelegt.

    ![Kurzfristige Ziele](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Im gezeigten Beispiel wird jeden Tag um 20:00 Uhr ein Sicherungspunkt erstellt, indem die geänderten Daten vom Sicherungspunkt des Vortags um 20:00 Uhr übertragen werden. Dieser Vorgang wird als **Schnelle vollständige Sicherung** bezeichnet. Transaktionsprotokolle werden alle 15 Minuten synchronisiert. Wenn Sie die Datenbank um 21:00 Uhr wiederherstellen müssen, wird der Punkt aus den Protokollen aus dem letzten Punkt der schnellen vollständigen Sicherung erstellt (in diesem Fall 20:00 Uhr).
   >
   >

7. Überprüfen Sie auf dem Bildschirm **Datenträgerzuordnung überprüfen** den gesamten verfügbaren Speicherplatz und den möglichen Speicherplatz. Klicken Sie auf **Weiter**.

8. Wählen Sie unter **Replikaterstellungsmethode auswählen** aus, wie der erste Wiederherstellungspunkt erstellt werden soll. Sie können diese anfängliche Sicherung manuell (nicht über das Netzwerk) übertragen, um nicht zu viel Bandbreite zu belegen, oder die Daten über das Netzwerk senden. Wenn Sie mit der Übertragung der ersten Sicherung warten möchten, können Sie den Zeitpunkt der ersten Übertragung angeben. Klicken Sie auf **Weiter**.

    ![Methode für die anfängliche Replikation](./media/backup-azure-backup-sql/pg-manual.png)

    Die anfängliche Sicherungskopie erfordert eine Übertragung der gesamten Datenquelle (SQL Server-Datenbank) vom Produktionsserver (SQL Server-Computer) zu Azure Backup Server. Der Umfang dieser Daten kann sehr groß sein, und die Übertragung der Daten über das Netzwerk überschreitet möglicherweise die Bandbreite. Aus diesem Grund können Sie wählen, ob Sie die anfängliche Sicherung übertragen: **Manuell** (mithilfe von Wechselmedien), um eine Überlastung der Bandbreite zu vermeiden, oder **Automatisch über das Netzwerk** (zu einem bestimmten Zeitpunkt).

    Sobald die anfängliche Sicherung abgeschlossen ist, werden nur noch inkrementelle Sicherungen basierend auf der anfänglichen Sicherungskopie erstellt. Inkrementelle Sicherungen sind im Allgemeinen klein und lassen sich problemlos über das Netzwerk übertragen.

9. Wählen Sie aus, wann die Konsistenzprüfung ausgeführt werden soll, und klicken Sie auf **Weiter**.

    ![Konsistenzprüfung](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server kann eine Konsistenzprüfung ausführen, um die Integrität des Sicherungspunkts zu prüfen. Hierbei wird die Prüfsumme der Sicherungsdatei auf dem Produktionsserver (in diesem Szenario der SQL Server-Computer) und der gesicherten Daten für diese Datei durch Azure Backup Server berechnet. Wenn ein Konflikt auftritt, wird angenommen, dass die gesicherte Datei auf Azure Backup Server beschädigt ist. Azure Backup Server korrigiert die gesicherten Daten, indem die Datenblöcke gesendet werden, die nicht der Prüfsumme entsprechen. Da Konsistenzprüfungen leistungsintensiv sind, können Sie die Konsistenzprüfung planen oder automatisch ausführen.

10. Um Onlineschutz für die Datenquellen festzulegen, wählen Sie die Datenbanken aus, die Sie mit Azure schützen möchten. Klicken Sie dann auf **Weiter**.

    ![Auswählen der Datenquellen](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Wählen Sie Sicherungszeitpläne und Aufbewahrungsrichtlinien aus, die den Richtlinien ihrer Organisation entsprechen.

    ![Planung und Aufbewahrung](./media/backup-azure-backup-sql/pg-schedule.png)

    In diesem Beispiel werden Sicherungen einmal täglich um 12:00 Uhr und um 20:00 Uhr ausgeführt (unterer Bildschirmbereich).

    > [!NOTE]
    > Es ist eine bewährte Methode, einige kurzfristige Wiederherstellungspunkte auf einem Datenträger zur Hand zu haben, um eine schnelle Wiederherstellung zu ermöglichen. Diese Wiederherstellungspunkte werden für die Wiederherstellung operativer Funktionen verwendet. Azure ist dank hoher SLAs und garantierter Verfügbarkeit eine gute Wahl als Offsitestandort.
    >
    >

    **Bewährte Methode:** Wenn Sie planen, Sicherungen in Azure zu starten, nachdem die lokalen Datenträgersicherungen abgeschlossen wurden, werden die neuesten Datenträgersicherungen immer in Azure kopiert.

12. Wählen Sie den Zeitplan für die Aufbewahrungsrichtlinie. Ausführliche Informationen zur Funktionsweise der Aufbewahrungsrichtlinie finden Sie im Artikel [Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Aufbewahrungsrichtlinie](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In diesem Beispiel:

    * Sicherungen einmal täglich um 12:00 Uhr und um 20:00 Uhr ausgeführt (unterer Bildschirmbereich) und für 180 Tage beibehalten.
    * Die Sicherung am Samstag um 12:00 Uhr wird 104 Wochen lang beibehalten.
    * Die Sicherung am letzten Samstag um 12:00 Uhr wird 60 Monate lang beibehalten.
    * Die Sicherung am letzten Samstag im März um 12:00 Uhr wird 10 Jahre lang beibehalten.
13. Klicken Sie auf **Weiter** , und wählen Sie die geeignete Option zum Übertragen der anfänglichen Sicherung nach Azure aus. Sie können **Automatisch über das Netzwerk** wählen.

14. Nachdem Sie die Richtliniendetails auf dem Bildschirm **Zusammenfassung** überprüft haben, klicken Sie auf **Gruppe erstellen**, um den Workflow abzuschließen. Sie können anschließend auf **Schließen** klicken und den Auftragsfortschritt im Arbeitsbereich „Überwachung“ verfolgen.

    ![Erstellen der Schutzgruppe – in Bearbeitung](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Bedarfsgesteuerte Sicherung einer SQL Server-Datenbank
Anhand der zuvor beschriebenen Schritte wurde eine Sicherungsrichtlinie eingerichtet, ein "Wiederherstellungspunkt" wird jedoch erst bei Ausführung der ersten Sicherung erstellt. Statt darauf zu warten, dass der Scheduler in Aktion tritt, können Sie mithilfe der nachstehenden Schritte manuell einen Wiederherstellungspunkt erstellen.

1. Warten Sie, bis der Status der Datenbank für die Schutzgruppe als **OK** angezeigt wird, bevor Sie den Wiederherstellungspunkt erstellen.

    ![Schutzgruppenmitglieder](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Wiederherstellungspunkt erstellen**.

    ![Erstellen eines Onlinewiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wählen Sie **Onlineschutz** im Dropdownmenü aus, und klicken Sie auf **OK**, um die Erstellung eines Wiederherstellungspunkts in Azure zu starten.

    ![Wiederherstellungspunkt erstellen](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Zeigen Sie den Status des Auftrags im Arbeitsbereich **Überwachung** an.

    ![Konsole für die Überwachung](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Wiederherstellen einer SQL Server-Datenbank aus Azure
Die folgenden Schritte sind erforderlich, um eine geschützte Entität (SQL Server-Datenbank) aus Azure wiederherzustellen.

1. Öffnen Sie die Azure Backup Server-Verwaltungskonsole. Navigieren Sie zum Arbeitsbereich **Wiederherstellung**, in dem die geschützten Server angezeigt werden. Suchen Sie nach der erforderlichen Datenbank (in diesem Fall "ReportServer$MSDPM2012"). Wählen Sie eine Zeit für **Wiederherstellung von** aus, die als ein **Online**punkt angegeben ist.

    ![Auswählen eines Wiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klicken Sie mit der rechten Maustaste auf den Datenbanknamen und anschließend auf **Wiederherstellen**.

    ![Wiederherstellen aus Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS zeigt die Details zum Wiederherstellungspunkt an. Klicken Sie auf **Weiter**. Um die Datenbank zu überschreiben, wählen Sie den Wiederherstellungstyp **In ursprünglicher Instanz von SQL Server wiederherstellen**aus. Klicken Sie auf **Weiter**.

    ![Wiederherstellung am ursprünglichen Speicherort](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In diesem Beispiel stellt MABS die Datenbank in einer anderen SQL Server-Instanz oder in einem eigenständigen Netzwerkordner wieder her.

4. Auf dem Bildschirm **Wiederherstellungsoptionen angeben** können Sie Optionen auswählen, z.B. „Netzwerk-Bandbreiteneinschränkung“, um die Bandbreitenbelegung bei der Wiederherstellung zu drosseln. Klicken Sie auf **Weiter**.

5. Im Bildschirm **Zusammenfassung** werden alle bisher konfigurierten Wiederherstellungsoptionen angezeigt. Klicken Sie auf **Wiederherstellen**.

    Der Wiederherstellungsstatus zeigt an, dass die Datenbank wiederhergestellt wird. Sie können auf **Schließen** klicken, um den Assistenten zu schließen und den Fortschritt im Arbeitsbereich **Überwachung** zu verfolgen.

    ![Auslösen der Wiederherstellung](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Nach Abschluss der Wiederherstellung ist die wiederhergestellte Datenbank anwendungskonsistent.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie im Artikel [Sichern von Dateien und Anwendungen](backup-mabs-files-applications-azure-stack.md).
Weitere Informationen finden Sie im Artikel [Sichern von SharePoint auf Azure Stack](backup-mabs-sharepoint-azure-stack.md).
