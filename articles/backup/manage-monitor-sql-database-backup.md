---
title: Verwalten und Überwachen von SQL Server-Datenbanken auf einem virtuellen Azure-Computer
description: In diesem Artikel wird beschrieben, wie auf einer Azure-VM ausgeführte SQL Server-Datenbanken verwaltet und überwacht werden.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: b0df22002521c8148cac1200e79aeb0ae5a60546
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021502"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Verwalten und Überwachen gesicherter SQL Server-Datenbanken

In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung von SQL Server-Datenbanken beschrieben, die auf einem virtuellen Azure-Computer (Virtual Machine, VM) ausgeführt und mit dem [Azure Backup](backup-overview.md)-Dienst in einem Azure Backup Recovery Services-Tresor gesichert werden. Sie erfahren, wie Sie Aufträge und Warnungen überwachen, Datenbankschutz beenden und fortsetzen, Sicherungsaufträge ausführen und die Registrierung einer VM für Sicherungen aufheben.

Wenn Sie noch keine Sicherungen für Ihre SQL Server-Datenbanken konfiguriert haben, finden Sie Näheres dazu unter [Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern](backup-azure-sql-database.md).

## <a name="monitor-backup-jobs-in-the-portal"></a>Überwachen von Sicherungsaufträgen im Portal

Azure Backup zeigt alle geplanten und bedarfsgesteuerten Vorgänge im Portal unter **Sicherungsaufträge** an – mit Ausnahme der geplanten Protokollsicherungen, da diese sehr häufig erfolgen können. Zu den in diesem Portal angezeigten Aufträgen gehören Datenbankermittlung und -registrierung, Konfiguration der Sicherung sowie Sicherungs- und Wiederherstellungsvorgänge.

![Das Portal „Sicherungsaufträge“](./media/backup-azure-sql-database/jobs-list.png)

Ausführliche Informationen zu Überwachungsszenarios finden Sie unter [Monitoring in the Azure portal](backup-azure-monitoring-built-in-monitor.md) (Überwachung im Azure-Portal) und [Monitoring using Azure Monitor](backup-azure-monitoring-use-azuremonitor.md) (Überwachung mithilfe von Azure Monitor).  

## <a name="view-backup-alerts"></a>Anzeigen von Sicherungswarnungen

Da Protokollsicherungen alle 15 Minuten stattfinden, kann die Überwachung von Sicherungsaufträgen mühsam sein. Azure Backup erleichtert die Überwachung durch Senden von E-Mail-Warnungen. E-Mail-Warnungen werden:

- Für alle Sicherungsfehler ausgelöst.
- Auf Datenbankebene nach Fehlercode konsolidiert.
- Nur beim ersten Sicherungsfehler einer Datenbank gesendet.

So überwachen Sie Datenbanksicherungswarnungen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Tresordashboard **Warnungen und Ereignisse** aus.

   ![Auswählen von „Warnungen und Ereignisse“](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Wählen Sie unter **Warnungen und Ereignisse** die Option **Sicherungswarnungen** aus.

   ![Auswählen von „Sicherungswarnungen“](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Beenden des Schutzes für eine SQL Server-Datenbank

Sie können die Sicherung einer SQL Server-Datenbank auf verschiedene Arten beenden:

- Beenden aller zukünftigen Sicherungsaufträge und Löschen aller Wiederherstellungspunkte
- Beenden aller zukünftigen Sicherungsaufträge und Beibehalten der Wiederherstellungspunkte.

Wenn Sie die Wiederherstellungspunkte beibehalten, sollten Sie Folgendes beachten:

- Alle Wiederherstellungspunkte werden unbegrenzt beibehalten, und die gesamte Bereinigung endet mit der Beendigung des Schutzes unter Beibehaltung der Daten.
- Ihnen werden die geschützte Instanz und der verbrauchte Speicher in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/).
- Wenn Sie eine Datenquelle löschen, ohne die Sicherungen zu beenden, treten bei neuen Sicherungen Fehler auf. Alte Wiederherstellungspunkte laufen gemäß der Richtlinie ab. Der letzte Wiederherstellungspunkt wird jedoch immer beibehalten, bis Sie die Sicherungen beenden und die Daten löschen.

Gehen Sie wie folgt vor, um den Schutz für eine Datenbank zu beenden:

1. Wählen Sie auf dem Tresordashboard die Option **Sicherungselemente** aus.

2. Wählen Sie unter **Backup Management Type** (Sicherungsverwaltungstyp) die Option **SQL in Azure VM** (SQL Server in Azure-VM) aus.

    ![Auswählen von „SQL Server in Azure-VM“](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Wählen Sie die Datenbank aus, für die Sie den Schutz beenden möchten.

    ![Auswählen der Datenbank zum Beenden des Schutzes](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Wählen Sie im Datenbankmenü **Sicherung beenden** aus.

    ![Auswählen von „Sicherung beenden“](./media/backup-azure-sql-database/stop-db-button.png)

5. Wählen Sie im Menü **Sicherung beenden** aus, ob Daten beibehalten oder gelöscht werden sollen. Geben Sie bei Bedarf einen Grund und einen Kommentar ein.

    ![Beibehalten oder Löschen von Daten im Menü „Sicherung beenden“](./media/backup-azure-sql-database/stop-backup-button.png)

6. Wählen Sie **Sicherung beenden** aus.

> [!NOTE]
>
>Weitere Informationen zur Option zum Löschen von Daten finden Sie im folgenden FAQ:
>
>- [Was passiert mit den Sicherungen, wenn ich eine Datenbank von einer automatisch geschützten Instanz lösche?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Welches Verhalten ergibt sich, wenn ich den Vorgang „Sicherung beenden“ für eine automatisch geschützte Datenbank durchführe?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Fortsetzen des Schutzes für eine SQL-­Datenbank-Instanz

Wenn Sie beim Beenden des Schutzes für die SQL-Datenbank die Option **Sicherungsdaten beibehalten** auswählen, können Sie den Schutz später fortsetzen. Wenn Sie die Sicherungsdaten nicht beibehalten, können Sie den Schutz nicht fortsetzen.

So setzen Sie den Schutz für eine SQL-­Datenbank fort:

1. Öffnen Sie das Sicherungselement, und wählen Sie **Sicherung fortsetzen** aus.

    ![Auswählen von „Sicherung fortsetzen“, um den Datenbankschutz fortzusetzen](./media/backup-azure-sql-database/resume-backup-button.png)

2. Wählen Sie im Menü **Sicherungsrichtlinie** eine Richtlinie aus, und klicken Sie dann auf **Speichern**.

## <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Sie können verschiedene Arten von bedarfsgesteuerten Sicherungen ausführen:

- Vollständige Sicherung
- Vollständige Kopiesicherung
- Differenzielle Sicherung
- Protokollsicherung

Für eine vollständige Sicherung vom Typ „Nur kopieren“ muss eine Aufbewahrungsdauer angegeben werden. Für bedarfsgesteuerte vollständige Sicherungen wird die Aufbewahrungsdauer dagegen automatisch auf 45 Tage ab dem aktuellen Zeitpunkt festgelegt.

Weitere Informationen finden Sie unter [Typen von SQL Server-Sicherungen](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Ändern der Richtlinie

Bearbeiten Sie die Richtlinie, um die Sicherungshäufigkeit oder die Aufbewahrungsdauer zu ändern.

> [!NOTE]
> Jede Änderung der Aufbewahrungsdauer wird nicht nur auf die neuen Wiederherstellungspunkte angewendet, sondern auch rückwirkend auf alle älteren.

Navigieren Sie im Tresordashboard zu **Verwalten** > **Sicherungsrichtlinien**, und wählen Sie die Richtlinie aus, die Sie bearbeiten möchten.

  ![Verwalten von Sicherungsrichtlinien](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Ändern der Sicherungsrichtlinie](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Eine Richtlinienänderung wirkt sich auf alle zugeordneten Sicherungselemente aus und löst entsprechende Aufträge zum **Konfigurieren des Schutzes** aus.

### <a name="inconsistent-policy"></a>Inkonsistente Richtlinie

Manchmal kann ein Vorgang zum Ändern einer Richtlinie zu einer **inkonsistenten** Richtlinienversion für einige Sicherungselemente führen. Dies geschieht, wenn der entsprechende Auftrag zum **Konfigurieren des Schutzes** für das Sicherungselement fehlschlägt, nachdem ein Vorgang zum Ändern der Richtlinie ausgelöst wurde. Es wird in der Ansicht „Sicherungselement“ folgendermaßen angezeigt:

  ![Inkonsistente Richtlinie](./media/backup-azure-sql-database/inconsistent-policy.png)

Sie können die Richtlinienversion für alle betroffenen Elemente mit einem Mausklick korrigieren:

  ![Korrigieren einer inkonsistenten Richtlinie](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Registrierung einer SQL Server-Instanz aufheben

Heben Sie die Registrierung einer SQL Server-Instanz auf, nachdem Sie den Schutz deaktiviert haben, aber bevor Sie den Tresor löschen:

1. Wählen Sie auf dem Tresordashboard unter **Verwalten** die Option **Sicherungsinfrastruktur** aus.  

   ![Auswählen von „Sicherungsinfrastruktur“](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Wählen Sie unter **Verwaltungsserver** **Geschützte Server** aus.

   ![Auswählen von „Geschützte Server“](./media/backup-azure-sql-database/protected-servers.png)

3. Wählen Sie unter **Geschützte Server** den Server aus, dessen Registrierung Sie aufheben möchten. Um den Tresor zu löschen, müssen Sie die Registrierung aller Server aufheben.

4. Klicken Sie mit der rechten Maustaste auf den geschützten Server, und wählen Sie **Registrierung aufheben** aus.

   ![Auswählen von „Löschen“](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Erneutes Registrieren einer Erweiterung auf der SQL Server-VM

Manchmal kann die Workloaderweiterung auf dem virtuellen Computer aus dem einen oder anderen Grund beeinträchtigt werden. In solchen Fällen schlagen alle auf der VM ausgelösten Vorgänge fehl. Möglicherweise müssen Sie dann die Erweiterung erneut auf der VM registrieren. Beim Vorgang **Neuregistrierung** wird die Workloadsicherungserweiterung auf der VM erneut installiert, damit die Vorgänge fortgesetzt werden können. Sie finden diese Option im Recovery Services-Tresor unter **Sicherungsinfrastruktur**.

![Geschützte Server unter „Sicherungsinfrastruktur“](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Verwenden Sie diese Option mit Vorsicht. Wenn dieser Vorgang auf einer VM mit bereits fehlerfreier Erweiterung ausgelöst wird, wird die Erweiterung dadurch neu gestartet. Dies kann dazu führen, dass alle in Bearbeitung befindlichen Aufträge fehlschlagen. Überprüfen Sie vor dem Auslösen der erneuten Registrierung, ob ein oder mehrere [Symptome](backup-sql-server-azure-troubleshoot.md#re-registration-failures) vorhanden sind.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Problembehandlung zum Sichern von SQL Server in Azure](backup-sql-server-azure-troubleshoot.md).
