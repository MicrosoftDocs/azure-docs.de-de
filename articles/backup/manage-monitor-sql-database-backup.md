---
title: Verwalten und Überwachen von SQL Server-Datenbanken auf einer mit Azure Backup gesicherten Azure-VM | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie auf einer Azure-VM ausgeführte SQL Server-Datenbanken verwaltet und überwacht werden.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: d850cb222b0028f862cdba302140ce12af8576db
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492745"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Verwalten und Überwachen gesicherter SQL Server-Datenbanken

In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung von SQL Server-Datenbanken beschrieben, die auf einem virtuellen Azure-Computer (Virtual Machine, VM) ausgeführt und mit dem [Azure Backup](backup-overview.md)-Dienst in einem Azure Backup Recovery Services-Tresor gesichert werden. Sie erfahren, wie Sie Aufträge und Warnungen überwachen, Datenbankschutz beenden und fortsetzen, Sicherungsaufträge ausführen und die Registrierung einer VM für Sicherungen aufheben.

Wenn Sie noch keine Sicherungen für Ihre SQL Server-Datenbanken konfiguriert haben, finden Sie Näheres dazu unter [Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern](backup-azure-sql-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Überwachen manueller Sicherungsaufträge im Portal

Azure Backup zeigt alle manuell ausgelösten Aufträge im Portal **Sicherungsaufträge** an. Zu den Aufträgen, die in diesem Portal angezeigt werden, gehören Datenbankermittlung und -registrierung sowie Sicherungs- und Wiederherstellungsvorgänge.

![Das Portal „Sicherungsaufträge“](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Im Portal **Sicherungsaufträge** werden keine geplanten Sicherungsaufträge angezeigt. Verwenden Sie SQL Server Management Studio, um geplante Sicherungsaufträge zu überwachen, wie im nächsten Abschnitt beschrieben.
>

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

* Beenden aller zukünftigen Sicherungsaufträge und Löschen aller Wiederherstellungspunkte
* Beenden aller zukünftigen Sicherungsaufträge und Beibehalten der Wiederherstellungspunkte.

Wenn Sie die Wiederherstellungspunkte beibehalten, sollten Sie Folgendes beachten:

* Alle Wiederherstellungspunkte werden unbegrenzt beibehalten, und die Bereinigung endet mit der Beendung des Schutzes unter Beibehaltung der Daten.
* Ihnen werden die geschützte Instanz und der verbrauchte Speicher in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/).
* Wenn Sie eine Datenquelle löschen, ohne die Sicherungen zu beenden, treten bei neuen Sicherungen Fehler auf.

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

>
> [!NOTE]
Weitere Informationen zur Option zum Löschen von Daten finden Sie in den folgenden häufig gestellten Fragen: <br/>
* [Was passiert mit den Sicherungen, wenn ich eine Datenbank von einer automatisch geschützten Instanz lösche?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)<br/>
* [Welches Verhalten ergibt sich, wenn ich den Vorgang „Sicherung beenden“ für eine automatisch geschützte Datenbank durchführe?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>Fortsetzen des Schutzes für eine SQL-­Datenbank-Instanz

Wenn Sie beim Beenden des Schutzes für die SQL-Datenbank die Option **Sicherungsdaten beibehalten** auswählen, können Sie den Schutz später fortsetzen. Wenn Sie die Sicherungsdaten nicht beibehalten, können Sie den Schutz nicht fortsetzen.

So setzen Sie den Schutz für eine SQL-­Datenbank fort:

1. Öffnen Sie das Sicherungselement, und wählen Sie **Sicherung fortsetzen** aus.

    ![Auswählen von „Sicherung fortsetzen“, um den Datenbankschutz fortzusetzen](./media/backup-azure-sql-database/resume-backup-button.png)

2. Wählen Sie im Menü **Sicherungsrichtlinie** eine Richtlinie aus, und klicken Sie dann auf **Speichern**.

## <a name="run-an-on-demand-backup"></a>Ausführen einer bedarfsgesteuerten Sicherung

Sie können verschiedene Arten von bedarfsgesteuerten Sicherungen ausführen:

* Vollständige Sicherung
* Vollständige Kopiesicherung
* Differenzielle Sicherung
* Protokollsicherung

Während Sie die Aufbewahrungsdauer für „Vollständige Kopiesicherung“ angeben müssen, wird die Dauer für andere Sicherungstypen automatisch auf 30 Tage ab dem aktuellen Zeitpunkt festgelegt. <br/>
Weitere Informationen finden Sie unter [Typen von SQL Server-Sicherungen](backup-architecture.md#sql-server-backup-types).

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

Manchmal kann die Workload-Erweiterung auf der VM aus irgendeinem Grund beeinträchtigt werden. In solchen Fällen schlagen alle auf der VM ausgelösten Vorgänge fehl. Möglicherweise müssen Sie dann die Erweiterung erneut auf der VM registrieren. Bei der **Neuregistrierung** wird die Workloadsicherungserweiterung erneut auf der VM installiert, damit die Vorgänge fortgesetzt werden können.  <br>

Es wird empfohlen, diese Option mit Vorsicht zu verwenden. Wenn dieser Vorgang auf einer VM mit bereits fehlerfreier Erweiterung ausgelöst wird, wird die Erweiterung dadurch neu gestartet. Dies kann dazu führen, dass alle in Bearbeitung befindlichen Aufträge fehlschlagen. Deshalb sollten Sie vor dem Auslösen der erneuten Registrierung überprüfen, ob ein oder mehrere [Symptome](backup-sql-server-azure-troubleshoot.md#symptoms) vorhanden sind.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Problembehandlung zum Sichern von SQL Server in Azure](backup-sql-server-azure-troubleshoot.md).
