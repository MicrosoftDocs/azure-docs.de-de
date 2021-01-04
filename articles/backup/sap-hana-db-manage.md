---
title: Verwalten von gesicherten SAP HANA-Datenbanken für Azure-VMs
description: In diesem Artikel erfahren Sie mehr über allgemeine Aufgaben zum Verwalten und Überwachen von SAP HANA-Datenbanken, die auf virtuellen Azure-Computern ausgeführt werden.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 4c8dc80c7b48217e40d5325b75752e21174ecaae
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95811950"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Verwalten und Überwachen gesicherter SAP HANA-Datenbanken

In diesem Artikel werden allgemeine Aufgaben zur Verwaltung und Überwachung von SAP HANA-Datenbanken beschrieben, die auf einem virtuellen Azure-Computer (Virtual Machine, VM) ausgeführt und mit dem [Azure Backup](./backup-overview.md)-Dienst in einem Azure Backup Recovery Services-Tresor gesichert werden. Sie erfahren, wie Sie Aufträge und Warnungen überwachen, eine bedarfsgesteuerte Sicherung auslösen, Richtlinien bearbeiten, Datenbankschutz beenden und fortsetzen sowie die Registrierung einer VM für Sicherungen aufheben.

Wenn Sie noch keine Sicherungen für Ihre SAP HANA-Datenbanken konfiguriert haben, finden Sie Näheres dazu unter [Informationen zur SAP HANA-Sicherung auf virtuellen Azure-Computern](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Überwachen manueller Sicherungsaufträge im Portal

Azure Backup zeigt alle manuell ausgelösten Aufträge im Azure-Portal im Abschnitt **Sicherungsaufträge** an.

![Abschnitt „Sicherungsaufträge“](./media/sap-hana-db-manage/backup-jobs.png)

Zu den Aufträgen, die in diesem Portal angezeigt werden, gehören Datenbankermittlung und -registrierung sowie Sicherungs- und Wiederherstellungsvorgänge. Geplante Aufträge (einschließlich Protokollsicherungen) werden in diesem Abschnitt nicht angezeigt. Manuell ausgelöste Sicherungen aus den nativen SAP HANA-Clients (Studio/Cockpit/DBA Cockpit) werden hier ebenfalls nicht angezeigt.

![Liste „Sicherungsaufträge“](./media/sap-hana-db-manage/backup-jobs-list.png)

Weitere Informationen zur Überwachung finden Sie unter [Überwachung im Azure-Portal](./backup-azure-monitoring-built-in-monitor.md) und [Überwachen im richtigen Maßstab mithilfe von Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Anzeigen von Sicherungswarnungen

Warnungen sind eine einfache Möglichkeit zum Überwachen von Sicherungen von SAP HANA-Datenbanken. Warnungen unterstützen Sie dabei, sich auf die Ereignisse zu konzentrieren, die Sie am meisten interessieren, ohne den Überblick über die unzähligen von einer Sicherung generierten Ereignisse zu verlieren. Azure Backup ermöglicht es Ihnen, Warnungen festzulegen, und Sie können diese wie folgt überwachen:

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
* Wählen Sie auf dem Tresordashboard die Option **Sicherungswarnungen** aus.

  ![Sicherungswarnungen auf dem Tresordashboard](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Die Warnungen können angezeigt werden:

  ![Liste der Sicherungswarnungen](./media/sap-hana-db-manage/backup-alerts-list.png)

* Wählen Sie die Warnungen aus, um weitere Details anzuzeigen:

  ![Warnungsdetails](./media/sap-hana-db-manage/alert-details.png)

Heute ermöglicht Azure Backup das Senden von Warnungen per E-Mail. Es handelt sich um die folgenden Warnungen:

* Für alle Sicherungsfehler ausgelöst.
* Auf Datenbankebene nach Fehlercode konsolidiert.
* Nur beim ersten Sicherungsfehler einer Datenbank gesendet.

Weitere Informationen zur Überwachung finden Sie unter [Überwachung im Azure-Portal](./backup-azure-monitoring-built-in-monitor.md) und [Überwachen im richtigen Maßstab mithilfe von Azure Monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Verwaltungsvorgänge

Mit Azure Backup wird die Verwaltung einer gesicherten SAP HANA-Datenbank mit einer Vielzahl von Verwaltungsvorgängen vereinfacht, die unterstützt werden. Diese Vorgänge werden in den folgenden Abschnitten ausführlicher erläutert.

### <a name="run-an-on-demand-backup"></a>Ausführen einer On-Demand-Sicherung

Sicherungen werden gemäß dem Richtlinienzeitplan ausgeführt. Eine bedarfsgesteuerte Sicherung können Sie wie folgt ausführen:

1. Wählen Sie im Tresormenü die Option **Sicherungselemente** aus.
2. Wählen Sie unter **Sicherungselemente** den virtuellen Computer aus, auf dem die SAP HANA-Datenbank ausgeführt wird, und wählen Sie dann **Jetzt sichern** aus.
3. Wählen Sie in **Jetzt sichern** den Sicherungstyp aus, den Sie durchführen möchten. Klicken Sie anschließend auf **OK**. Diese Sicherung wird gemäß der diesem Sicherungselement zugeordneten Richtlinie aufbewahrt.
4. Überwachen Sie die Portalbenachrichtigungen. Sie können den Auftragsstatus im Dashboard des Tresors unter **Sicherungsaufträge** > **In Bearbeitung** überwachen. Je nach Größe Ihrer Datenbank kann das Erstellen der ersten Sicherung einige Zeit dauern.

Standardmäßig werden bedarfsgesteuerte Sicherungen 45 Tage lang aufbewahrt.

### <a name="hana-native-client-integration"></a>Integration von nativen HANA-Clients

#### <a name="backup"></a>Backup

Von einem der nativen HANA-Clients (an **Backint**) ausgelöste bedarfsgesteuerte Sicherungen werden auf der Seite **Sicherungselemente** in der Liste der Sicherungen angezeigt.

![Zuletzt durchgeführte Sicherungen](./media/sap-hana-db-manage/last-backups.png)

Sie können [diese Sicherungen auch von der Seite **Sicherungsaufträge** aus überwachen](#monitor-manual-backup-jobs-in-the-portal).

Diese bedarfsgesteuerten Sicherungen werden auch in der Liste der Wiederherstellungspunkte für die Wiederherstellung angezeigt.

![Liste der Wiederherstellungspunkte](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Restore

Von nativen HANA-Clients (unter Verwendung von **Backint**) ausgelöste Wiederherstellungen auf denselben Computer können von der Seite **Sicherungsaufträge** aus [überwacht](#monitor-manual-backup-jobs-in-the-portal) werden.

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Ausführen einer Sicherung des nativen SAP HANA-Clients auf einem lokalen Datenträger für eine Datenbank, für die Azure Backup aktiviert ist

Führen Sie die folgenden Schritte aus, wenn Sie (mit HANA Studio/Cockpit) eine lokale Sicherung einer Datenbank ausführen möchten, die mit Azure Backup gesichert wird:

1. Warten Sie, bis alle vollständigen Sicherungen oder Protokollsicherungen für die Datenbank abgeschlossen sind. Überprüfen Sie den Status in SAP HANA Studio/Cockpit.
2. für die relevante Datenbank.
    1. Heben Sie die Festlegung der backint-Parameter auf. Doppelklicken Sie hierzu auf **systemdb** > **Konfiguration** > **Datenbank auswählen** > **Filter (Protokoll)** .
        * enable_auto_log_backup: Nein
        * log_backup_using_backint: False
        * catalog_backup_using_backint:False
3. Erstellen Sie eine bedarfsgesteuerte vollständige Sicherung der Datenbank.
4. Kehren Sie dann die Schritte um. Aktivieren Sie für dieselbe relevante DB, die oben erwähnt wurde,
    1. die backint-Parameter neu.
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint: True
        1. enable_auto_log_backup: Ja

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Verwalten oder Bereinigen des HANA-Katalogs für eine Datenbank mit aktiviertem Azure Backup

Wenn Sie den Sicherungskatalog bearbeiten oder bereinigen möchten, gehen Sie folgendermaßen vor:

1. Warten Sie, bis alle vollständigen Sicherungen oder Protokollsicherungen für die Datenbank abgeschlossen sind. Überprüfen Sie den Status in SAP HANA Studio/Cockpit.
2. für die relevante Datenbank.
    1. Heben Sie die Festlegung der backint-Parameter auf. Doppelklicken Sie hierzu auf **systemdb** > **Konfiguration** > **Datenbank auswählen** > **Filter (Protokoll)** .
        * enable_auto_log_backup: Nein
        * log_backup_using_backint: False
        * catalog_backup_using_backint:False
3. Bearbeiten Sie den Katalog, und entfernen Sie ältere Einträge.
4. Kehren Sie dann die Schritte um. Aktivieren Sie für dieselbe relevante DB, die oben erwähnt wurde,
    1. die backint-Parameter neu.
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint: True
        1. enable_auto_log_backup: Ja

### <a name="change-policy"></a>Ändern einer Richtlinie

Sie können die zugrunde liegende Richtlinie für ein SAP HANA-Sicherungselement ändern.

* Navigieren Sie im Tresordashboard zur Option **Sicherungselemente**.

  ![Sicherungselemente auswählen](./media/sap-hana-db-manage/backup-items.png)

* Auswählen von **SAP HANA in Azure-VM**

  ![Auswählen von „SAP HANA in Azure-VM“](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Wählen Sie das Sicherungselement aus, dessen zugrunde liegende Richtlinie Sie ändern möchten.
* Wählen Sie die vorhandene Sicherungsrichtlinie aus.

  ![Vorhandene Sicherungsrichtlinie auswählen](./media/sap-hana-db-manage/existing-backup-policy.png)

* Wählen Sie die Richtlinie aus der Liste aus und ändern Sie sie. [Erstellen einer neuen Sicherungsrichtlinie](./backup-azure-sap-hana-database.md#create-a-backup-policy) aus, falls erforderlich.

  ![Richtlinie aus der Dropdownliste auswählen](./media/sap-hana-db-manage/choose-backup-policy.png)

* Speichern der Änderungen

  ![Speichern der Änderungen](./media/sap-hana-db-manage/save-changes.png)

* Eine Richtlinienänderung wirkt sich auf alle zugeordneten Sicherungselemente aus und löst entsprechende Aufträge zum **Konfigurieren des Schutzes** aus.

>[!NOTE]
> Jede Änderung der Aufbewahrungsdauer wird nicht nur auf die neuen Wiederherstellungspunkte angewendet, sondern auch rückwirkend auf alle älteren.
>
> Inkrementelle Sicherungsrichtlinien können für SAP HANA-Datenbanken nicht verwendet werden. Inkrementelle Sicherungen werden bei diesen Datenbanken zurzeit nicht unterstützt.

### <a name="modify-policy"></a>Richtlinie ändern

Bearbeiten Sie die Richtlinie, um Sicherungstypen, Sicherungshäufigkeit und Aufbewahrungsdauer zu ändern.

>[!NOTE]
>Jede Änderung der Aufbewahrungsdauer wird nicht nur auf die neuen Wiederherstellungspunkte angewendet, sondern auch rückwirkend auf alle älteren.

1. Navigieren Sie im Tresordashboard zu **Verwalten > Sicherungsrichtlinien**, und wählen Sie die Richtlinie aus, die Sie bearbeiten möchten.

   ![Auswählen der Richtlinie, die bearbeitet werden soll](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Wählen Sie **Ändern** aus.

   ![„Ändern“ auswählen](./media/sap-hana-db-manage/modify-policy.png)

1. Wählen Sie die Häufigkeit für die Sicherungstypen aus.

   ![Sicherungshäufigkeit auswählen](./media/sap-hana-db-manage/choose-frequency.png)

Eine Richtlinienänderung wirkt sich auf alle zugeordneten Sicherungselemente aus und löst entsprechende Aufträge zum **Konfigurieren des Schutzes** aus.

### <a name="inconsistent-policy"></a>Inkonsistente Richtlinie

Gelegentlich kann ein Vorgang zum Ändern einer Richtlinie zu einer **inkonsistenten** Richtlinienversion für einige Sicherungselemente führen. Dies geschieht, wenn der entsprechende Auftrag zum **Konfigurieren des Schutzes** für das Sicherungselement fehlschlägt, nachdem ein Vorgang zum Ändern der Richtlinie ausgelöst wurde. Es wird in der Ansicht „Sicherungselement“ folgendermaßen angezeigt:

![Inkonsistente Richtlinie](./media/sap-hana-db-manage/inconsistent-policy.png)

Sie können die Richtlinienversion für alle betroffenen Elemente mit einem Mausklick korrigieren:

![Korrigieren der Richtlinienversion](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Beenden des Schutzes für eine SAP HANA-Datenbank

Sie können den Schutz einer SAP HANA-Datenbank auf verschiedene Weise beenden:

* Beenden aller zukünftigen Sicherungsaufträge und Löschen aller Wiederherstellungspunkte
* Beenden aller zukünftigen Sicherungsaufträge und Beibehalten der Wiederherstellungspunkte.

Wenn Sie die Wiederherstellungspunkte beibehalten, sollten Sie Folgendes beachten:

* Alle Wiederherstellungspunkte werden unbegrenzt beibehalten, und die gesamte Bereinigung endet mit der Beendigung des Schutzes unter Beibehaltung der Daten.
* Ihnen werden die geschützte Instanz und der verbrauchte Speicher in Rechnung gestellt. Weitere Informationen finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/).
* Wenn Sie eine Datenquelle löschen, ohne die Sicherungen zu beenden, treten bei neuen Sicherungen Fehler auf.

Gehen Sie wie folgt vor, um den Schutz für eine Datenbank zu beenden:

* Wählen Sie auf dem Tresordashboard die Option **Sicherungselemente** aus.
* Wählen Sie unter **Sicherungsverwaltungstyp** die Option **SAP HANA in Azure-VM** aus.

  ![Auswählen von „SAP HANA in Azure-VM“](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Wählen Sie die Datenbank aus, für die Sie den Schutz beenden möchten:

  ![Auswählen der Datenbank zum Beenden des Schutzes](./media/sap-hana-db-manage/select-database.png)

* Wählen Sie im Datenbankmenü **Sicherung beenden** aus.

  ![Auswählen von „Sicherung beenden“](./media/sap-hana-db-manage/stop-backup.png)

* Wählen Sie im Menü **Sicherung beenden** aus, ob Daten beibehalten oder gelöscht werden sollen. Geben Sie bei Bedarf einen Grund und einen Kommentar ein.

  ![Auswählen der Beibehaltung oder Löschung von Daten](./media/sap-hana-db-manage/retain-backup-data.png)

* Wählen Sie **Sicherung beenden** aus.

### <a name="resume-protection-for-an-sap-hana-database"></a>Fortsetzen des Schutzes für eine SAP HANA-Datenbank

Wenn Sie beim Beenden des Schutzes für die SAP HANA-Datenbank die Option **Sicherungsdaten beibehalten** auswählen, können Sie den Schutz später fortsetzen. Wenn Sie die gesicherten Daten nicht beibehalten, können Sie den Schutz nicht fortsetzen.

So setzen Sie den Schutz für eine SAP HANA-Datenbank fort:

* Öffnen Sie das Sicherungselement, und wählen Sie **Sicherung fortsetzen** aus.

   ![Auswählen der Fortsetzung einer Sicherung](./media/sap-hana-db-manage/resume-backup.png)

* Wählen Sie im Menü **Sicherungsrichtlinie** eine Richtlinie aus, und klicken Sie dann auf **Speichern**.

### <a name="upgrading-from-sdc-to-mdc"></a>Aktualisieren von SDC auf MDC

Erfahren Sie, wie Sie nach einem [Upgrade von SDC auf MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) die Sicherung für eine SAP HANA-Datenbank fortsetzen.

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Aktualisieren von SDC auf MDC ohne SID-Änderung

Erfahren Sie, wie Sie die Sicherung einer SAP HANA-Datenbank fortsetzen, deren [SID sich nach dem Upgrade von SDC auf MDC nicht geändert hat](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Aktualisieren auf eine neue Version in SDC oder MDC

Erfahren Sie, wie Sie die Sicherung einer SAP HANA-Datenbank fortsetzen, deren [Version aktualisiert wird](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Aufheben der Registrierung einer SAP HANA-Instanz

Heben Sie die Registrierung einer SAP HANA-Instanz auf, nachdem Sie den Schutz deaktiviert haben, aber bevor Sie den Tresor löschen:

* Wählen Sie auf dem Tresordashboard unter **Verwalten** die Option **Sicherungsinfrastruktur** aus.

   ![Auswählen von „Sicherungsinfrastruktur“](./media/sap-hana-db-manage/backup-infrastructure.png)

* Wählen Sie den **Sicherungsverwaltungstyp** als **Workload in Azure-VM** aus.

   ![Auswählen des Sicherungsverwaltungstyp als „Workload in Azure-VM“](./media/sap-hana-db-manage/backup-management-type.png)

* Wählen Sie unter **Geschützte Server** die Instanz aus, deren Registrierung Sie aufheben möchten. Um den Tresor zu löschen, müssen Sie die Registrierung aller Server/Instanzen aufheben.

* Klicken Sie mit der rechten Maustaste auf die geschützten Instanz, und wählen Sie dann **Registrierung aufheben** aus.

   ![Auswählen der Aufhebung der Registrierung](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Erneutes Registrieren einer Erweiterung auf der SAP HANA Server-VM

Manchmal kann die Workloaderweiterung auf dem virtuellen Computer aus dem einen oder anderen Grund beeinträchtigt werden. In solchen Fällen schlagen alle auf der VM ausgelösten Vorgänge fehl. Möglicherweise müssen Sie dann die Erweiterung erneut auf der VM registrieren. Bei der Neuregistrierung wird die Workloadsicherungserweiterung erneut auf der VM installiert, damit die Vorgänge fortgesetzt werden können.

Verwenden Sie diese Option mit Vorsicht: Wenn dieser Vorgang auf einer VM mit bereits fehlerfreier Erweiterung ausgelöst wird, wird die Erweiterung dadurch neu gestartet. Dies kann dazu führen, dass alle in Bearbeitung befindlichen Aufträge fehlschlagen. Überprüfen Sie vor dem Auslösen der erneuten Registrierung, ob ein oder mehrere [Symptome](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) vorhanden sind.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Behandeln allgemeiner Probleme beim Sichern von SAP HANA-Datenbanken](./backup-azure-sap-hana-database-troubleshoot.md).
