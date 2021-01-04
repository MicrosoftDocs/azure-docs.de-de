---
title: Sichern von SQL Server in Azure als eine DPM-Workload
description: Eine Einführung in die Sicherung von SQL Server-Datenbanken mithilfe des Azure Backup-Diensts
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 592a51051a0d02a6c1d491db0fe559e2e62babb2
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327048"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Sichern von SQL Server in Azure als eine DPM-Workload

Dieser Artikel führt Sie durch die Konfigurationsschritte für die Sicherung von SQL Server-Datenbanken mithilfe von Azure Backup.

Für die Sicherung von SQL Server-Datenbanken in Azure benötigen Sie ein Azure-Konto. Wenn Sie dies noch nicht haben, können Sie in nur wenigen Minuten ein kostenloses Konto erstellen. Weitere Informationen finden Sie unter [Erstellen Ihres kostenlosen Azure-Kontos](https://azure.microsoft.com/pricing/free-trial/).

Zum Sichern und Wiederherstellen einer SQL Server-Datenbank in Azure führen Sie folgende Schritte aus:

1. Erstellen Sie eine Sicherungsrichtlinie zum Schutz von SQL Server-Datenbanken in Azure.
1. Erstellen Sie bedarfsgesteuerte Sicherungskopien in Azure.
1. Wiederherstellen der Datenbank aus Azure

>[!NOTE]
>DPM 2019 UR2 unterstützt SQL Server-Failoverclusterinstanzen (FCI) unter Verwendung von freigegebenen Clustervolumes (CSV).<br><br>
>Der Schutz von [Failoverclusterinstanzen mit „Direkte Speicherplätze“ (SQL Server auf Azure-VMs)](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md) und [Failoverclusterinstanzen mit freigegebenen Azure-Datenträgern (SQL Server auf Azure-VMs)](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) wird mit diesem Feature unterstützt. Der DPM-Server muss auf dem virtuellen Azure-Computer bereitgestellt werden, um die auf virtuellen Azure-Computern bereitgestellte SQL-FCI-Instanz schützen zu können. 

## <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

* Wenn Sie über eine Datenbank mit Dateien auf einer Remotedateifreigabe verfügen, werden die darauf enthaltenen Daten nicht geschützt und ein Fehler mit der ID 104 ausgegeben. Der Schutz von SQL Server-Daten auf einer Remotedateifreigabe wird von DPM nicht unterstützt.
* Datenbanken, die auf SMB-Remotefreigaben gespeichert sind, können von DPM nicht geschützt werden.
* Stellen Sie sicher, dass die [Replikate der Verfügbarkeitsgruppe als schreibgeschützt konfiguriert sind](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Sie müssen das Systemkonto **NTAuthority\System** der Systemadministratorgruppe in SQL Server explizit hinzufügen.
* Wenn Sie für eine teilweise eigenständige Datenbank eine Wiederherstellung an einem anderen Speicherort durchführen, müssen Sie sicherstellen, dass für die SQL-Zielinstanz die Funktion für [eigenständige Datenbanken](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) aktiviert wurde.
* Wenn Sie für eine Filestream-Datenbank eine Wiederherstellung an einem anderen Speicherort durchführen, müssen Sie sicherstellen, dass für die SQL-Zielinstanz die Funktion für [Filestream-Datenbanken](/sql/relational-databases/blob/enable-and-configure-filestream) aktiviert wurde.
* Schutz für SQL Server AlwaysOn:
  * Verfügbarkeitsgruppen werden von DPM beim Ausführen von Abfragen beim Erstellen von Schutzgruppen erkannt.
  * Von DPM wird ein Failover erkannt und der Schutz der Datenbank fortgesetzt.
  * Mehrere Standorte umfassende Clusterkonfigurationen für eine Instanz von SQL Server werden von DPM unterstützt.
* Wenn Sie Datenbanken schützen, für die die Funktion "AlwaysOn" verwendet wird, gelten für DPM folgende Einschränkungen:
  * Die Sicherungsrichtlinie für Verfügbarkeitsgruppen, die in SQL Server ausgehend von den Sicherungspräferenzen festgelegt wurde, wird von DPM wie folgt berücksichtigt:
    * Sekundär bevorzugen: Sicherungen müssen für ein sekundäres Replikat ausgeführt werden, es sei denn, das primäre Replikat ist als einziges Replikat online. Wenn mehrere sekundäre Replikate verfügbar sind, wird der Knoten mit der höchsten Sicherungspriorität für die Sicherung ausgewählt. Wenn nur das primäre Replikat verfügbar ist, muss die Sicherung für das primäre Replikat stattfinden.
    * Nur sekundäre: Die Sicherung darf nicht für das primäre Replikat ausgeführt werden. Wenn nur das primäre Replikat online ist, darf keine Sicherung ausgeführt werden.
    * Primär: Sicherungen müssen immer für das primäre Replikat ausgeführt werden.
    * Beliebiges Replikat: Sicherungen können für ein beliebiges Verfügbarkeitsreplikat in der Verfügbarkeitsgruppe ausgeführt werden. Der Knoten, von dem aus die Sicherung erfolgen soll, basiert auf den Sicherungsprioritäten für die einzelnen Knoten.
  * Beachten Sie Folgendes:
    * Sicherungen können für jedes lesbare Replikat erfolgen, d h. für ein primäres, ein synchrones sekundäres oder ein asynchrones sekundäres Replikat.
    * Wenn ein Replikat von der Sicherung ausgeschlossen ist, etwa weil **Replikat ausschließen** aktiviert oder das Replikat als nicht lesbar gekennzeichnet wurde, wird dieses Replikat unter keiner der Optionen für die Sicherung ausgewählt.
    * Wenn mehrere Replikate verfügbar und lesbar sind, wird der Knoten mit der höchsten Sicherungspriorität für die Sicherung ausgewählt.
    * Bei einem Sicherungsfehler auf dem ausgewählten Knoten ist der Sicherungsvorgang fehlerhaft.
    * Die Wiederherstellung am ursprünglichen Speicherort wird nicht unterstützt.
* Sicherungsprobleme bei SQL Server 2014 oder höher:
  * SQL Server 2014 wurde durch eine neue Funktion zum Erstellen einer [Datenbank für lokale SQL Server-Instanzen in Windows Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) erweitert. Diese Konfiguration kann nicht mithilfe von DPM geschützt werden.
  * Die Sicherungseinstellung „Sekundär bevorzugen“ verursacht bei Verwendung der Option „SQL AlwaysOn“ einige bekannte Probleme. Von DPM wird immer eine Sicherung für das sekundäre Replikat ausgeführt. Wenn kein sekundäres Replikat gefunden wird, tritt bei der Sicherung ein Fehler auf.

## <a name="before-you-start"></a>Vorbereitung

Vergewissern Sie sich zunächst, dass für den Schutz von Workloads mit Azure Backup alle [Voraussetzungen](backup-azure-dpm-introduction.md#prerequisites-and-limitations) erfüllt sind. Hier sind einige der erforderlichen Aufgaben:

* Erstellen eines Sicherungstresors.
* Herunterladen der Tresoranmeldeinformationen.
* Installieren des Azure-Sicherungs-Agents.
* Registrieren des Servers beim Tresor.

## <a name="create-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Erstellen Sie als Erstes eine Sicherungsrichtlinie, um SQL Server-Datenbanken in Azure zu schützen:

1. Wählen Sie auf dem Data Protection Manager-Server (DPM) den Arbeitsbereich **Schutz** aus.
1. Wählen Sie **Neu** aus, um eine Schutzgruppe zu erstellen.

    ![Erstellen einer Schutzgruppe](./media/backup-azure-backup-sql/protection-group.png)
1. Lesen Sie auf der Startseite den Leitfaden zum Erstellen einer Schutzgruppe. Wählen Sie **Weiter** aus.
1. Wählen Sie **Server**.

    ![Auswählen des Schutzgruppentyps „Server“](./media/backup-azure-backup-sql/pg-servers.png)
1. Erweitern Sie den virtuellen SQL Server-Computer, auf dem sich die zu sichernden Datenbanken befinden. Daraufhin werden die Datenquellen des Servers angezeigt, die gesichert werden können. Erweitern Sie **Alle SQL-Freigaben**, und wählen Sie die Datenbanken aus, die Sie sichern möchten. In diesem Beispiel werden die Datenbanken „ReportServer$MSDPM2012“ und „ReportServer$MSDPM2012TempDB“ ausgewählt. Wählen Sie **Weiter** aus.

    ![Auswählen einer SQL Server-Datenbank](./media/backup-azure-backup-sql/pg-databases.png)
1. Benennen Sie die Schutzgruppe, und wählen Sie anschließend **Ich möchte Onlineschutz** aus.

    ![Auswählen einer Datenschutzmethode (kurzfristiger Datenträgerschutz oder Azure-Onlineschutz)](./media/backup-azure-backup-sql/pg-name.png)
1. Geben Sie auf der Seite **Kurzfristige Ziele angeben** alle erforderlichen Informationen ein, um Sicherungspunkte für den Datenträger zu erstellen.

    In diesem Beispiel wird *Beibehaltungsdauer* auf **5 Tage** festgelegt. Die **Synchronisierungsfrequenz** für die Sicherung wird auf *15 Minuten* festgelegt. Der Wert für **Schnelle vollständige Sicherung** wird auf *20:00* festgelegt.

    ![Einrichten kurzfristiger Ziele für den Sicherungsschutz](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > In diesem Beispiel wird täglich um 20:00 Uhr ein Sicherungspunkt erstellt. Die Daten, die sich seit dem letzten Sicherungspunkt (also seit 20:00 Uhr des Vortags) geändert haben, werden übertragen. Dieser Vorgang wird als **Schnelle vollständige Sicherung** bezeichnet. Die Transaktionsprotokolle werden zwar alle 15 Minuten synchronisiert, falls die Datenbank jedoch um 21:00 Uhr wiederhergestellt werden muss, werden zur Erstellung des Punkts die Protokolle seit dem letzten vollständigen Sicherungspunkt (in diesem Beispiel: 20:00 Uhr) wiedergegeben.
   >
   >

1. Wählen Sie **Weiter** aus. DPM zeigt den gesamten verfügbaren Speicherplatz an. Außerdem wird der potenzielle Speicherplatzverbrauch angezeigt.

    ![Einrichten der Datenträgerzuordnung](./media/backup-azure-backup-sql/pg-storage.png)

    DPM erstellt standardmäßig ein Volume pro Datenquelle (SQL Server-Datenbank). Das Volume wird für die Erstsicherungskopie verwendet. In dieser Konfiguration wird der DPM-Schutz durch die Verwaltung logischer Datenträger (Logical Disk Manager, LDM) auf maximal 300 Datenquellen (SQL Server-Datenbanken) beschränkt. Um diese Einschränkung zu umgehen, wählen Sie **Daten im DPM-Speicherpool zusammenstellen** aus. Mit dieser Option verwendet DPM ein einzelnes Volume für mehrere Datenquellen. Dadurch kann DPM bis zu 2.000 SQL Server-Datenbanken schützen.

    Wenn Sie **Volumes automatisch erweitern** auswählen, kann DPM auf die zunehmende Größe des Sicherungsvolumens durch wachsende Produktionsdaten reagieren. Wenn Sie die Option **Volumes automatisch erweitern** nicht auswählen, beschränkt DPM den Sicherungsspeicher auf die Datenquellen in der Schutzgruppe.

1. Administratoren können die Erstsicherung mithilfe der Option **Automatisch über das Netzwerk** automatisch über das Netzwerk übertragen und die gewünschte Übertragungszeit auswählen. Alternativ kann die Option **Manuell** verwendet werden, um die Sicherung manuell zu übertragen. Wählen Sie **Weiter** aus.

    ![Auswählen der Replikaterstellungsmethode](./media/backup-azure-backup-sql/pg-manual.png)

    Für die Erstsicherungskopie muss die gesamte Datenquelle (SQL Server-Datenbank) übertragen werden. Die Sicherungsdaten werden vom Produktionsserver (SQL Server-Computer) zum DPM-Server verschoben. Bei umfangreichen Sicherungen kommt es unter Umständen zu einer Überlastung der Bandbreite, wenn die Daten über das Netzwerk übertragen werden. Aus diesem Grund können Administratoren die Option **Manuell** auswählen, um die Erstsicherung mithilfe von Wechselmedien zu übertragen. Alternativ können sie die Option **Automatisch über das Netzwerk** verwenden und die Daten zu einem bestimmten Zeitpunkt automatisch über das Netzwerk übertragen.

    Im Anschluss an die Erstsicherung werden inkrementelle Sicherungen auf der Grundlage der Erstsicherungskopie erstellt. Inkrementelle Sicherungen sind im Allgemeinen klein und lassen sich problemlos über das Netzwerk übertragen.

1. Wählen Sie aus, wann eine Konsistenzprüfung ausgeführt werden soll. Wählen Sie **Weiter** aus.

    ![Auswählen des Zeitpunkts für eine Konsistenzprüfung](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kann eine Konsistenzprüfung ausführen, um die Integrität des Sicherungspunkts zu überprüfen. Hierbei wird die Prüfsumme der Sicherungsdatei auf dem Produktionsserver (in diesem Beispiel: der SQL Server-Computer) und der gesicherten Daten für diese Datei in DPM berechnet. Sollte bei der Überprüfung ein Konflikt gefunden werden, wird davon ausgegangen, dass die gesicherte Datei in DPM beschädigt ist. DPM sendet daraufhin die von dem Prüfsummenkonflikt betroffenen Datenblöcke, um die gesicherten Daten zu korrigieren. Da die Konsistenzprüfung ein ressourcenintensiver Vorgang ist, können Administratoren wählen, ob die Prüfung zu einem bestimmten Zeitpunkt oder automatisch ausgeführt werden soll.

1. Wählen Sie die Datenquellen aus, die in Azure geschützt werden sollen. Wählen Sie **Weiter** aus.

    ![Auswählen von Datenquellen, die in Azure geschützt werden sollen](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Administratoren können Sicherungszeitpläne und Aufbewahrungsrichtlinien auswählen, die den Richtlinien ihrer Organisation entsprechen.

    ![Auswählen von Zeitplänen und Aufbewahrungsrichtlinien](./media/backup-azure-backup-sql/pg-schedule.png)

    In diesem Beispiel werden Sicherungen täglich um 12:00 Uhr und um 20:00 Uhr erstellt.

    > [!TIP]
    > Speichern Sie einige kurzfristige Wiederherstellungspunkte auf Ihrem Datenträger, um eine schnelle Wiederherstellung zu ermöglichen. Diese Wiederherstellungspunkte werden für die Wiederherstellung operativer Funktionen verwendet. Azure ist dank hoher SLAs und garantierter Verfügbarkeit eine gute Wahl für einen Offsitestandort.
    >
    > Verwenden Sie DPM, um Azure-Sicherungen im Anschluss an die lokalen Datenträgersicherungen zu planen. Bei dieser Vorgehensweise wird die aktuelle Datenträgersicherung in Azure kopiert.
    >

1. Wählen Sie den Zeitplan für die Aufbewahrungsrichtlinie. Weitere Informationen zur Funktionsweise der Aufbewahrungsrichtlinie finden Sie im Artikel [Verwenden von Azure Backup als Ersatz für Ihre Bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Auswählen einer Aufbewahrungsrichtlinie](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    In diesem Beispiel:

    * Sicherungen werden täglich um 12:00 Uhr und um 20:00 Uhr erstellt. Sie werden 180 Tage aufbewahrt.
    * Die Sicherung von Samstag, 12:00 Uhr, wird 104 Wochen aufbewahrt.
    * Die Sicherung von 12:00 Uhr am letzten Samstag des Monats wird 60 Monate aufbewahrt.
    * Die Sicherung von 12:00 Uhr am letzten Samstag im März wird zehn Jahre aufbewahrt.

    Wählen Sie nach der Wahl einer Aufbewahrungsrichtlinie **Weiter** aus.

1. Wählen Sie aus, wie die Erstsicherungskopie an Azure übertragen werden soll.

    * Bei der Option **Automatisch über das Netzwerk** werden die Daten gemäß Ihrem Sicherungszeitplan an Azure übertragen.
    * Weitere Informationen zur **Offlinesicherung** finden Sie in der [Übersicht über die Offlinesicherung](offline-backup-overview.md).

    Wählen Sie nach der Wahl eines Übertragungsmechanismus **Weiter** aus.

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Richtliniendetails. Wählen Sie anschließend **Gruppe erstellen** aus. Sie können **Schließen** auswählen und den Auftragsstatus im Arbeitsbereich **Überwachung** verfolgen.

    ![Status der Schutzgruppenerstellung](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Bedarfsgesteuertes Erstellen von Sicherungskopien einer SQL Server-Datenbank

Ein Wiederherstellungspunkt wird erstellt, wenn die erste Sicherung durchgeführt wird. Die Erstellung eines Wiederherstellungspunkts kann auch manuell ausgelöst werden, anstatt auf die Ausführung des Schedulers zu warten:

1. Vergewissern Sie sich in der Schutzgruppe, dass der Status der Datenbank **OK** lautet.

    ![Schutzgruppe mit Datenbankstatus](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Wiederherstellungspunkt erstellen** aus.

    ![Erstellen eines Onlinewiederherstellungspunkts](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Wählen Sie im Dropdownmenü die Option **Onlineschutz** aus. Wählen Sie anschließend **OK** aus, um die Erstellung eines Wiederherstellungspunkts in Azure zu starten.

    ![Starten der Erstellung eines Wiederherstellungspunkts in Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Der Status des Auftrags kann im Arbeitsbereich **Überwachung** verfolgt werden.

    ![Anzeigen des Auftragsstatus im Arbeitsbereich „Überwachung“](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Wiederherstellen einer SQL Server-Datenbank aus Azure

So stellen Sie eine geschützte Entität (beispielsweise eine SQL Server-Datenbank) aus Azure wieder her:

1. Öffnen Sie die DPM-Serververwaltungskonsole. Navigieren Sie zum Arbeitsbereich **Wiederherstellung**, um die von DPM gesicherten Server anzuzeigen. Wählen Sie die Datenbank aus (in diesem Beispiel: ReportServer$MSDPM2012). Wählen Sie unter **Wiederherstellungszeit** einen Wert aus, die mit **Online** endet.

    ![Wählen Sie einen Wiederherstellungspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Klicken Sie mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Wiederherstellen** aus.

    ![Wiederherstellen einer Datenbank aus Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM zeigt die Details zum Wiederherstellungspunkt an. Wählen Sie **Weiter** aus. Um die Datenbank zu überschreiben, wählen Sie den Wiederherstellungstyp **In ursprünglicher Instanz von SQL Server wiederherstellen** aus. Wählen Sie **Weiter** aus.

    ![Wiederherstellen einer Datenbank am ursprünglichen Speicherort](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    In diesem Beispiel ermöglicht DPM die Wiederherstellung der Datenbank in einer anderen SQL Server-Instanz oder in einen eigenständigen Netzwerkordner.
1. Auf der Seite **Wiederherstellungsoptionen angeben** können Sie die Wiederherstellungsoptionen auswählen. So können Sie beispielsweise **Netzwerk-Bandbreiteneinschränkung** auswählen, um die von der Wiederherstellung beanspruchte Bandbreite zu drosseln. Wählen Sie **Weiter** aus.
1. Auf der Seite **Zusammenfassung** wird die aktuelle Wiederherstellungskonfiguration angezeigt. Wählen Sie **Wiederherstellen** aus.

    Der Wiederherstellungsstatus zeigt, dass die Datenbank wiederhergestellt wird. Sie können **Schließen** auswählen, um den Assistenten zu schließen und den Fortschritt im Arbeitsbereich **Überwachung** zu verfolgen.

    ![Starten des Wiederherstellungsprozesses](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Nach Abschluss der Wiederherstellung ist die wiederhergestellte Datenbank mit der Anwendung konsistent.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Azure Backup – häufig gestellte Fragen](backup-azure-backup-faq.md).