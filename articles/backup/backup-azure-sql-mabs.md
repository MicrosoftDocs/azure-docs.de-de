---
title: Azure Backup für SQL Server-Workloads mit Azure Backup Server
description: Eine Einführung in die Sicherung von SQL Server-Datenbanken mithilfe von Azure Backup Server
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 72b2368979f0c9e546e1c7ef7fc462bf1d64c947
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60782071"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Sichern von SQL Server in Azure mit Azure Backup Server
Dieser Artikel führt Sie durch die Konfigurationsschritte für die Sicherung von SQL Server-Datenbanken mithilfe von Microsoft Azure Backup Server (MABS).

Die Verwaltung der Sicherung und Wiederherstellung von SQL-Datenbanken in und aus Azure umfasst drei Schritte:

1. Erstellen einer Sicherungsrichtlinie zum Schutz von SQL Server-Datenbanken mithilfe von Azure
2. Bedarfsgesteuertes Erstellen von Sicherungskopien in Azure
3. Wiederherstellen der Datenbank aus Azure

## <a name="before-you-start"></a>Vorbereitung
Bevor Sie beginnen, stellen Sie sicher, [dass Azure Backup Server installiert und vorbereitet wurde](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Erstellen einer Sicherungsrichtlinie zum Schutz von SQL Server-Datenbanken mithilfe von Azure
1. Klicken Sie auf der Azure Backup Server-Benutzeroberfläche auf den Arbeitsbereich **Schutz**.
2. Klicken Sie im Menüband auf **Neu** , um eine neue Schutzgruppe zu erstellen.

    ![Erstellen einer Schutzgruppe](./media/backup-azure-backup-sql/protection-group.png)
3. MABS zeigt den Startbildschirm mit Hinweisen zum Erstellen einer **Schutzgruppe**an. Klicken Sie auf **Weiter**.
4. Wählen Sie **Server**.

    ![Auswählen des Schutzgruppentyp – "Server"](./media/backup-azure-backup-sql/pg-servers.png)
5. Erweitern Sie den SQL Server-Computer, auf dem die zu sichernden Datenbanken vorhanden sind. MABS zeigt verschiedene Datenquellen, die auf diesem Server gesichert werden können. Erweitern Sie **Alle SQL-Freigaben** , und wählen Sie die Datenbanken aus, die Sie sichern möchten (in diesem Fall wurden die Datenbanken "ReportServer$MSDPM2012" und "ReportServer$MSDPM2012TempDB" ausgewählt). Klicken Sie auf **Weiter**.

    ![Auswählen einer SQL-Datenbank](./media/backup-azure-backup-sql/pg-databases.png)
6. Geben Sie einen Namen für die Schutzgruppe an, und aktivieren Sie das Kontrollkästchen **Ich möchte Onlineschutz** .

    ![Datenschutzmethode – kurzfristig auf Datenträger & online in Azure](./media/backup-azure-backup-sql/pg-name.png)
7. Geben Sie auf dem Bildschirm **Kurzfristige Ziele angeben** alle erforderlichen Informationen ein, um Sicherungspunkte auf dem Datenträger zu erstellen.

    In diesem Beispiel wurde die **Beibehaltungsdauer** auf *5 Tage* und die **Synchronisierungshäufigkeit** auf einmal alle *15 Minuten*festgelegt. Dieser Wert gibt die Häufigkeit der Sicherung an. Der Wert für **Schnelle vollständige Sicherung** ist auf *20:00* festgelegt.

    ![Kurzfristige Ziele](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Jeden Tag um 20:00 Uhr wird (in diesem Beispiel) ein Sicherungspunkt erstellt, indem die seit dem Sicherungspunkt am Vortag um 20:00 Uhr geänderten Daten übertragen werden. Dieser Vorgang wird als **Schnelle vollständige Sicherung** bezeichnet. Die Transaktionsprotokolle werden alle 15 Minuten synchronisiert. Falls um 21:00 Uhr die Datenbank wiederhergestellt werden muss, wird der Punkt erstellt, indem die Protokolle seit dem letzten vollständigen Sicherungspunkt (in diesem Fall 20:00 Uhr) wiedergegeben werden.
   >
   >

8. Klicken Sie unten auf der Seite auf **Weiter**

    MABS zeigt den verfügbaren Gesamtspeicherplatz sowie die wahrscheinliche Festplattenspeicherbelegung an.

    ![Datenträgerzuordnung](./media/backup-azure-backup-sql/pg-storage.png)

    MABS erstellt standardmäßig ein Volume pro Datenquelle (SQL Server-Datenbank), das zum Erstellen der anfänglichen Sicherungskopie verwendet wird. Bei diesem Ansatz ist der MABS-Schutz durch die Verwaltung logischer Datenträger auf maximal 300 Datenquellen (SQL Server-Datenbanken) beschränkt. Um diese Einschränkung zu umgehen, wählen Sie die Option **Daten im DPM-Speicherpool zusammenstellen**aus. Bei Auswahl dieser Option verwendet MABS ein einzelnes Volume für mehrere Datenquellen, sodass MABS bis zu 2000 SQL-Datenbanken schützen kann.

    Wenn die Option **Volumes automatisch erweitern** ausgewählt wird, kann MABS der Anforderung nach größeren Sicherungsvolumes aufgrund steigender Produktionsdaten Rechnung tragen. Wenn Sie die Option **Volumes automatisch erweitern** nicht auswählen, beschränkt MABS den verwendeten Sicherungsspeicher auf die Datenquellen in der Schutzgruppe.
9. Administratoren können diese anfängliche Sicherung manuell (nicht über das Netzwerk) übertragen, um nicht zu viel Bandbreite zu belegen, oder die Daten über das Netzwerk senden. Sie können auch konfigurieren, zu welchem Zeitpunkt die anfängliche Übertragung stattfinden kann. Klicken Sie auf **Weiter**.

    ![Methode für die anfängliche Replikation](./media/backup-azure-backup-sql/pg-manual.png)

    Die anfängliche Sicherungskopie erfordert eine Übertragung der gesamten Datenquelle (SQL Server-Datenbank) vom Produktionsserver (SQL Server-Computer) zu MABS. Der Umfang dieser Daten kann sehr groß sein, und die Übertragung der Daten über das Netzwerk überschreitet möglicherweise die Bandbreite. Aus diesem Grund können Administratoren auswählen, die anfängliche Sicherung zu übertragen: **Manuell** (mithilfe von Wechselmedien), um eine Überlastung der Bandbreite zu vermeiden, oder **Automatisch über das Netzwerk** (zu einem bestimmten Zeitpunkt).

    Sobald die anfängliche Sicherung abgeschlossen ist, werden nur noch inkrementelle Sicherungen basierend auf der anfänglichen Sicherungskopie erstellt. Inkrementelle Sicherungen sind im Allgemeinen klein und lassen sich problemlos über das Netzwerk übertragen.
10. Wählen Sie aus, wann die Konsistenzprüfung ausgeführt werden soll, und klicken Sie auf **Weiter**.

    ![Konsistenzprüfung](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS kann eine Konsistenzprüfung durchführen, um die Integrität des Sicherungspunkts zu prüfen. Hierbei wird die Prüfsumme der Sicherungsdatei auf dem Produktionsserver (in diesem Szenario der SQL Server-Computer) und der gesicherten Daten für diese Datei auf MABS berechnet. Im Falle eines Konflikts wird davon ausgegangen, dass die gesicherte Datei auf MABS beschädigt ist. MABS korrigiert die gesicherten Daten, indem die Datenblöcke gesendet werden, die nicht der Prüfsumme entsprechen. Da die Konsistenzprüfung ein ressourcenintensiver Vorgang ist, haben Administratoren die Möglichkeit, diese zeitlich zu planen oder automatisch ausführen zu lassen.
11. Um Onlineschutz für die Datenquellen festzulegen, wählen Sie die Datenbanken aus, die Sie mit Azure schützen möchten. Klicken Sie dann auf **Weiter**.

    ![Auswählen der Datenquellen](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratoren können Sicherungszeitpläne und Aufbewahrungsrichtlinien auswählen, die den Richtlinien ihrer Organisation entsprechen.

    ![Planung und Aufbewahrung](./media/backup-azure-backup-sql/pg-schedule.png)

    In diesem Beispiel werden Sicherungen einmal täglich um 12:00 Uhr und um 20:00 Uhr ausgeführt (unterer Bildschirmbereich).

    > [!NOTE]
    > Es ist eine bewährte Methode, einige kurzfristige Wiederherstellungspunkte auf einem Datenträger zur Hand zu haben, um eine schnelle Wiederherstellung zu ermöglichen. Diese Wiederherstellungspunkte werden für die „Wiederherstellung operativer Funktionen“ verwendet. Azure ist dank hoher SLAs und garantierter Verfügbarkeit eine gute Wahl als Offsitestandort.
    >
    >

    **Bewährte Methode:** Stellen Sie sicher, dass Azure Backup-Sicherungen so geplant sind, dass sie nach Abschluss der lokalen Datenträgersicherungen mithilfe von DPM ausgeführt werden. Auf diese Weise wird sichergestellt, dass die neueste Datenträgersicherung nach Azure kopiert wird.

13. Wählen Sie den Zeitplan für die Aufbewahrungsrichtlinie. Ausführliche Informationen zur Funktionsweise der Aufbewahrungsrichtlinie finden Sie im Artikel [Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Aufbewahrungsrichtlinie](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In diesem Beispiel:

    * Sicherungen einmal täglich um 12:00 Uhr und um 20:00 Uhr ausgeführt (unterer Bildschirmbereich) und für 180 Tage beibehalten.
    * Die Sicherung am Samstag um 12:00 Uhr wird 104 Wochen lang beibehalten.
    * Die Sicherung am letzten Samstag um 12:00 Uhr wird 60 Monate lang beibehalten.
    * Die Sicherung am letzten Samstag im März um 12:00 Uhr wird 10 Jahre lang beibehalten.
14. Klicken Sie auf **Weiter** , und wählen Sie die geeignete Option zum Übertragen der anfänglichen Sicherung nach Azure aus. Sie können zwischen den Optionen **Automatisch über das Netzwerk** und **Offlinesicherung** wählen.

    * **Automatisch über das Netzwerk** werden die Sicherungsdaten gemäß dem für die Sicherung ausgewählten Zeitplan nach Azure übertragen.
    * Die Funktionsweise der Option **Offlinesicherung** wird unter [Workflow zur Offlinesicherung in Azure Backup](backup-azure-backup-import-export.md)beschrieben.

    Wählen Sie die geeignete Übertragungsmethode zum Senden der anfänglichen Sicherungskopie an Azure, und klicken Sie auf **Weiter**.
15. Nachdem Sie die Richtliniendetails auf dem Bildschirm **Zusammenfassung** überprüft haben, klicken Sie auf die Schaltfläche **Gruppe erstellen**, um den Workflow abzuschließen. Sie können anschließend auf die Schaltfläche **Schließen** klicken und den Auftragsfortschritt im Arbeitsbereich "Überwachung" verfolgen.

    ![Erstellen der Schutzgruppe – in Bearbeitung](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Bedarfsgesteuerte Sicherung einer SQL Server-Datenbank
Anhand der zuvor beschriebenen Schritte wurde eine Sicherungsrichtlinie eingerichtet, ein "Wiederherstellungspunkt" wird jedoch erst bei Ausführung der ersten Sicherung erstellt. Statt darauf zu warten, dass der Scheduler in Aktion tritt, können Sie mithilfe der nachstehenden Schritte manuell einen Wiederherstellungspunkt erstellen.

1. Warten Sie, bis der Status der Datenbank für die Schutzgruppe als **OK** angezeigt wird, bevor Sie den Wiederherstellungspunkt erstellen.

    ![Schutzgruppenmitglieder](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Wiederherstellungspunkt erstellen**.

    ![Erstellen eines Onlinewiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Wählen Sie im Dropdownmenü die Option **Onlineschutz**, und klicken Sie auf **OK**. Dies löst die Erstellung eines Wiederherstellungspunkts in Azure aus.

    ![Wiederherstellungspunkt erstellen](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Sie können den Auftragsfortschritt im Arbeitsbereich **Überwachung** verfolgen. Dort wird ein in Bearbeitung befindlicher Auftrag angezeigt, ähnlich wie in der nächsten Abbildung.

    ![Konsole für die Überwachung](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Wiederherstellen einer SQL Server-Datenbank aus Azure
Die folgenden Schritte sind erforderlich, um eine geschützte Entität (SQL Server-Datenbank) aus Azure wiederherzustellen.

1. Öffnen Sie die Verwaltungskonsole auf dem DPM-Server. Navigieren Sie zum Arbeitsbereich **Wiederherstellung** , in dem die mit DPM gesicherten Server angezeigt werden. Suchen Sie nach der erforderlichen Datenbank (in diesem Fall "ReportServer$MSDPM2012"). Wählen Sie unter **Wiederherstellung aus** eine Uhrzeit aus, die auf **Online** endet.

    ![Auswählen eines Wiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Klicken Sie mit der rechten Maustaste auf den Datenbanknamen und anschließend auf **Wiederherstellen**.

    ![Wiederherstellen aus Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM zeigt die Details zum Wiederherstellungspunkt an. Klicken Sie auf **Weiter**. Um die Datenbank zu überschreiben, wählen Sie den Wiederherstellungstyp **In ursprünglicher Instanz von SQL Server wiederherstellen**aus. Klicken Sie auf **Weiter**.

    ![Wiederherstellung am ursprünglichen Speicherort](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In diesem Beispiel erlaubt DPM die Wiederherstellung der Datenbank auf eine andere SQL Server-Instanz oder in einen eigenständigen Netzwerkordner.
4. Auf dem Bildschirm **Wiederherstellungsoptionen angeben** können Sie Optionen auswählen, z.B. „Netzwerk-Bandbreiteneinschränkung“, um die Bandbreitenbelegung bei der Wiederherstellung zu drosseln. Klicken Sie auf **Weiter**.
5. Im Bildschirm **Zusammenfassung** werden alle bisher konfigurierten Wiederherstellungsoptionen angezeigt. Klicken Sie auf **Wiederherstellen**.

    Der Wiederherstellungsstatus zeigt an, dass die Datenbank wiederhergestellt wird. Sie können auf **Schließen** klicken, um den Assistenten zu schließen und den Fortschritt im Arbeitsbereich **Überwachung** zu verfolgen.

    ![Auslösen der Wiederherstellung](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Nach Abschluss der Wiederherstellung ist die wiederhergestellte Datenbank anwendungskonsistent.

### <a name="next-steps"></a>Nächste Schritte:
•    [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md)
