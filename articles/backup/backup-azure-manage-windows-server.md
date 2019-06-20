---
title: Verwalten von Azure Recovery Services-Tresoren und -Servern
description: Verwalten von Aufträgen und Warnungen in einem Azure Recovery Services-Tresor.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/21/2018
ms.author: raynew
ms.openlocfilehash: f7d97866bb3ae895f83fff248ceec2e83dd60bf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688898"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Überwachen und Verwalten von Recovery Services-Tresoren

In diesem Artikel wird erläutert, wie Sie das Dashboard **Übersicht** des Recovery Services-Tresors zum Überwachen und Verwalten Ihrer Recovery Services-Tresore verwenden. Wenn Sie einen Recovery Services-Tresor aus der Liste öffnen, wird das Dashboard **Übersicht** für den ausgewählten Tresor geöffnet. Das Dashboard stellt verschiedene Details zum Tresor bereit. Es gibt *Kacheln*, auf denen Folgendes angezeigt wird: der Status von kritischen Warnungen und allgemeinen Warnmeldungen, in Bearbeitung befindliche und fehlerhafte Sicherungsaufträge sowie die Menge von belegtem lokal redundantem Speicher (LRS) und georedundantem Speicher (GRS). Wenn Sie Azure-VMs in den Tresor sichern, werden auf der Kachel [**Status der Sicherungsvorüberprüfung** alle kritischen oder fehlerhaften Elemente angezeigt](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). In der folgenden Abbildung wird das Dashboard **Übersicht** für **Contoso-vault** angezeigt. Auf der Kachel **Sicherungselemente** wird angezeigt, dass es neun beim Tresor registrierte Elemente gibt.

![Dashboard des Recovery Services-Tresors](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Die Voraussetzungen für diesen Artikel sind: ein Azure-Abonnement, ein Recovery Services-Tresor und mindestens ein für den Tresor konfiguriertes Sicherungselement.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Öffnen eines Recovery Services-Tresors

Öffnen Sie den Tresor, um Warnungen zu überwachen oder Verwaltungsdaten zu einem Recovery Services-Tresor anzuzeigen.

1. Melden Sie sich unter Verwendung Ihres Azure-Abonnements beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie im Portal auf **Alle Dienste**.

   ![Öffnen der Liste mit den Recovery Services-Tresoren – Schritt 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. Geben Sie im Dialogfeld **Alle Dienste** **Recovery Services** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wenn die Option **Recovery Services-Tresore** angezeigt wird, klicken Sie darauf, um die Liste mit den Recovery Services-Tresoren in Ihrem Abonnement anzuzeigen.

    ![Erstellen eines Recovery Services-Tresors – Schritt 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Klicken Sie in der Liste mit den Tresoren auf einen Tresor, um dessen Dashboard **Übersicht** zu öffnen.

    ![Dashboard des Recovery Services-Tresors](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Das Dashboard „Übersicht“ stellt Warnungen und Sicherungsauftragsdaten auf Kacheln bereit.

## <a name="monitor-backup-jobs-and-alerts"></a>Überwachen von Sicherungsaufträgen und Warnungen

Das Dashboard **Übersicht** des Recovery Services-Tresors enthält Kacheln für Überwachungs- und Nutzungsinformationen. Auf den Kacheln im Abschnitt „Überwachung“ werden kritische Warnungen und allgemeine Warnmeldungen sowie in Bearbeitung befindliche und fehlerhaften Aufträge angezeigt. Klicken Sie auf eine bestimmte Warnung oder einen Auftrag, um das Menü „Sicherungswarnungen“ oder „Sicherungsaufträge“, nach diesem Auftrag oder dieser Warnung gefiltert, zu öffnen.

![Aufgaben im Sicherungsdashboard](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

Im Abschnitt „Überwachung“ werden die Ergebnisse vordefinierter Abfragen für **Sicherungswarnungen** und **Sicherungsaufträge** angezeigt. Die Kacheln des Abschnitts „Überwachung“ enthalten aktuelle Informationen über:

* Kritische Warnungen und allgemeine Warnmeldungen für Sicherungsaufträge (in den letzten 24 Stunden)
* Status der Vorüberprüfung für Azure-VMs – Umfassende Informationen zum Vorüberprüfungsstatus finden Sie im [Sicherungsblog zum Status der Sicherungsvorüberprüfung](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* Die Sicherungsaufträge, die gerade ausgeführt werden, und Aufträge, die fehlgeschlagen sind (in den letzten 24 Stunden).

Die Kacheln des Abschnitts „Nutzung“ enthalten folgende Informationen:

* Die Anzahl der für den Tresor konfigurierten Sicherungselemente.
* Der vom Tresor belegte Azure-Speicher (aufgeteilt nach LRS und GRS).

Klicken Sie auf die Kacheln (außer „Sicherungsspeicher“), um das zugehörige Menü zu öffnen. In der Abbildung oben werden auf der Kachel „Sicherungswarnungen“ drei kritische Warnungen angezeigt. Durch Klicken auf die Zeile „Kritische Warnungen“ auf der Kachel „Sicherungswarnungen“ werden die Sicherungswarnungen, gefiltert nach kritischen Warnungen, angezeigt.

![Menü „Sicherungswarnungen“, gefiltert nach kritischen Warnungen](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Das Menü „Sicherungswarnungen“ im Bild oben ist gefiltert nach: Status ist „Aktiv“, Schweregrad ist „Kritisch, und die Zeit sind die letzten 24 Stunden.

## <a name="manage-backup-alerts"></a>Verwalten von Sicherungswarnungen

Klicken Sie im Menü „Recovery Services-Tresor“ auf **Sicherungswarnungen**, um in das Menü „Sicherungswarnungen“ zu gelangen.

![Sicherungswarnungen](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Im Bericht „Sicherungswarnungen“ sind die Warnungen für den Tresor aufgelistet.

![Sicherungswarnungen](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alerts

Die Liste „Sicherungswarnungen“ enthält die ausgewählten Informationen für die gefilterten Warnungen. Im Menü „Sicherungswarnungen“ können Sie nach kritischen Warnungen oder allgemeinen Warnmeldungen filtern.

| Warnstufe | Ereignisse, die Warnungen generieren |
| ----------- | ----------- |
| Kritisch | Sie erhalten kritische Warnungen, wenn: Sicherungsaufträge fehlschlagen, wenn Wiederherstellungsaufträge fehlschlagen und wenn Sie den Schutz auf einem Server beenden, aber die Daten beibehalten.|
| Warnung | Allgemeine Warnmeldungen werden angezeigt, wenn: Sicherungsaufträge mit Warnungen abgeschlossen werden, z.B. wenn weniger als 100 Dateien aufgrund von Beschädigungen nicht gesichert wurden oder wenn mehr als 1.000.000 Dateien erfolgreich gesichert wurden). |
| Information | Derzeit werden keine Informationsmeldungen verwendet. |

### <a name="viewing-alert-details"></a>Anzeigen von Warnungsdetails

Der Bericht „Sicherungswarnungen“ verfolgt acht Details zu jeder Warnung. Verwenden Sie die Schaltfläche **Spalten auswählen**, um die Details im Bericht zu bearbeiten.

![Sicherungswarnungen](./media/backup-azure-manage-windows-server/backup-alerts.png)

Standardmäßig werden alle Details, im Bericht angezeigt, mit Ausnahme von **Zeitpunkt des letzten Auftretens**.

* Warnung
* Sicherungselement
* Geschützter Server
* Severity
* Duration
* Erstellungszeit
* Status
* Zeitpunkt des letzten Auftretens

### <a name="change-the-details-in-alerts-report"></a>Ändern der Details im Bericht „Warnungen“

1. Zum Ändern der Berichtsinformationen klicken Sie im Menü **Sicherungswarnungen** auf **Spalten auswählen**.

   ![Sicherungswarnungen](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Das Menü **Spalten auswählen** wird geöffnet.

2. Wählen Sie im Menü **Spalten auswählen** die Details aus, die im Bericht angezeigt werden sollen.

    ![Menü „Spalten auswählen“](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Klicken Sie auf **Fertig**, um die Änderungen zu speichern und das Menü „Spalten auswählen“ zu schließen.

   Wenn Sie Änderungen vornehmen, die Änderungen aber nicht beibehalten möchten, klicken Sie auf **Zurücksetzen**, um die zur letzten gespeicherten Konfiguration zurückzukehren.

### <a name="change-the-filter-in-alerts-report"></a>Ändern des Filters im Bericht „Warnungen“

Verwenden Sie das Menü **Filter**, um Schweregrad, Status, Startzeit und Endzeit für die Warnungen zu ändern.

> [!NOTE]
> Durch Bearbeiten des Filters für die Sicherungswarnungen werden die kritischen Warnungen oder allgemeinen Warnmeldungen im Dashboard „Übersicht“ des Tresors nicht geändert.
>  

1. Zum Ändern des Filters für die Sicherungswarnungen klicken Sie im Menü „Sicherungswarnungen“ auf **Filter**.

   ![Menü „Filter“ auswählen](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Das Menü „Filter“ wird angezeigt.

   ![Menü „Filter“ auswählen](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Bearbeiten Sie Schweregrad, Status, Startzeit oder Endzeit, und klicken Sie zum Speichern der Änderungen auf **Fertig**.

## <a name="configuring-notifications-for-alerts"></a>Konfigurieren von Benachrichtigungen für Warnungen

Konfigurieren Sie Benachrichtigungen, um E-Mails zu generieren, wenn eine Warnmeldung oder kritische Warnung auftritt. Sie können E-Mail-Benachrichtigungen jede Stunde oder direkt bei Eintreten einer bestimmten Warnung senden.

   ![Warnungen filtern](./media/backup-azure-manage-windows-server/configure-notification.png)

E-Mail-Benachrichtigungen sind standardmäßig aktiviert. Klicken Sie auf **Aus**, um die E-Mail-Benachrichtigungen zu beenden.

Wählen Sie im Steuerelement **Benachrichtigen** die Option **Pro Warnung** aus, wenn keine Gruppierung erfolgen soll, oder wenn Sie nur über wenige Elemente verfügen, die Warnungen generieren können. Jede Warnung führt zu einer Benachrichtigung (Standardeinstellung), und es wird sofort eine Lösungs-E-Mail gesendet.

Bei Auswahl von **Stündliche Übersicht** wird eine E-Mail an die Empfänger gesendet, die die in der letzten Stunde generierten, nicht aufgelösten Warnungen erläutert. Nach einer Stunde wird jeweils eine Lösungs-E-Mail gesendet.

Wählen Sie den Schweregrad der Warnung aus (kritische Warnung oder allgemeine Warnmeldung), bei der eine E-Mail-Adresse generiert werden soll. Derzeit werden keine Informationsmeldungen verwendet.

## <a name="manage-backup-items"></a>Verwalten von Sicherungselementen

In einem Recovery Services-Tresor werden viele Arten von Sicherungsdaten gespeichert. [Erfahren Sie mehr](backup-overview.md#what-can-i-back-up) über die Elemente, die Sie sichern können. Um die verschiedenen Server, Computer, Datenbanken und Workloads zu verwalten, klicken Sie auf die Kachel **Sicherungselemente**, um den Inhalt des Tresors anzuzeigen.

![Kachel „Sicherungselemente“](./media/backup-azure-manage-windows-server/backup-items.png)

Die Liste der Sicherungselemente wird geöffnet, organisiert nach Sicherungsverwaltungstyp.

![Liste der Sicherungselemente](./media/backup-azure-manage-windows-server/list-backup-items.png)

Um einen bestimmten Typ einer geschützten Instanz zu untersuchen, klicken Sie auf das Element in der Spalte „Sicherungsverwaltungstyp“. In der Abbildung oben sind beispielsweise zwei virtuelle Azure-Computer dargestellt, die in diesem Tresor geschützt werden. Indem Sie auf **Virtuellen Azure-Computer** klicken, wird die Liste der geschützten virtuellen Computer in diesem Tresor geöffnet.

![Liste der Sicherungstypen](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Die Liste der virtuellen Computer enthält nützliche Daten: die zugeordnete Ressourcengruppe, die vorherige [Sicherungsvorüberprüfung](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/), der Status der letzten Sicherung und das Datum des letzten Wiederherstellungspunkts. Über die Auslassungspunkte in der letzten Spalte wird das Menü zum Auslösen allgemeiner Aufgaben geöffnet. Die Spalten enthalten für die einzelnen Sicherungstypen unterschiedliche hilfreiche Daten.

![Liste der Sicherungstypen](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Verwalten von Sicherungsaufträgen

Auf der Kachel **Sicherungsaufträge** im Dashboard des Tresors wird die Anzahl der Aufträge angezeigt, die in den letzten 24 Stunden ausgeführt wurden oder fehlgeschlagen sind. Die Kachel bietet einen Einblick in das Menü „Sicherungsaufträge“.

![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Um weitere Details zu den Aufträgen anzuzeigen, klicken Sie auf **In Bearbeitung** oder **Fehler** um das Menü „Sicherungsaufträge“ zu öffnen und nach dem jeweiligen Status zu filtern.

### <a name="backup-jobs-menu"></a>Menü„Sicherungsaufträge“

Im Menü **Sicherungsaufträge** werden Informationen zu Elementtyp, Vorgang, Status, Startzeit und Dauer angezeigt.  

Um das Menü „Sicherungsaufträge“ im Hauptmenü des Tresors zu öffnen, klicken Sie auf **Sicherungsaufträge**.

![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Die Liste der Sicherungsaufträge wird geöffnet.

![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Im Menü „Sicherungsaufträge“ wird der Status für alle Vorgänge für alle Sicherungstypen für die letzten 24 Stunden angezeigt. Verwenden Sie **Filter**, um den Filter zu ändern. Die Filter werden in den folgenden Abschnitten erläutert.

So ändern Sie die Filter:

1. Klicken Sie im Menü „Sicherungsaufträge“ des Tresors auf **Filter**.

   ![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Das Menü „Filter“ wird geöffnet.

   ![Sicherungselemente aus Einstellungen](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Wählen Sie die Filtereinstellungen aus, und klicken Sie auf **Fertig**. Die gefilterte Liste wird basierend auf den neuen Einstellungen aktualisiert.

#### <a name="item-type"></a>Elementtyp

Der Elementtyp ist der Sicherungsverwaltungstyp der geschützten Instanz. Es gibt vier Arten. Weitere Informationen finden Sie in der folgende Liste. Sie können alle Elementtypen oder nur einem Elementtyp anzeigen. Sie können nicht zwei oder drei Elementtypen auswählen. Die verfügbaren Elementtypen sind:

* Alle Elementtypen
* Virtueller Azure-Computer
* Dateien und Ordner
* Azure Storage
* Azure-Workload

#### <a name="operation"></a>Vorgang

Sie können einen Vorgang oder alle Vorgänge anzeigen. Sie können nicht zwei oder drei Vorgänge auswählen. Die verfügbaren Vorgänge sind:

* Alle Vorgänge
* Register
* Konfigurieren der Sicherung
* Backup
* Restore
* Deaktivieren einer Sicherung
* Löschen von Sicherungsdaten

#### <a name="status"></a>Status

Sie können alle Statusangaben oder einen Status anzeigen. Sie können nicht zwei oder drei Statusangaben auswählen. Die verfügbaren Statusangaben sind:

* Alle Statusangaben
* Abgeschlossen
* In Bearbeitung
* Fehler
* Canceled
* Abgeschlossen mit Warnungen

#### <a name="start-time"></a>Startzeit

Der Tag und die Uhrzeit, zu der die Abfrage beginnt. Der Standardwert ist ein 24-Stunden-Zeitraum.

#### <a name="end-time"></a>Endzeit

Der Tag und die Uhrzeit, zu der die Abfrage endet.

### <a name="export-jobs"></a>Aufträge exportieren

Verwenden Sie **Aufträge exportieren** zum Erstellen einer Tabelle, die alle Auftragsmenüinformationen enthält. Die Tabelle verfügt über ein Blatt, das eine Zusammenfassung aller Aufträge enthält, sowie einzelne Blätter für jeden Auftrag.

Um die Auftragsinformationen in eine Tabelle zu exportieren, klicken Sie auf **Aufträge exportieren**. Der Dienst erstellt eine Tabelle mit dem Namen des Tresors und dem Datum, aber Sie können den Namen ändern.

## <a name="monitor-backup-usage"></a>Überwachen der Sicherungsverwendung

Auf der Kachel „Sicherungsspeicher“ im Dashboard wird der in Azure genutzte Speicher angezeigt. Die Speicherverwendung wird für Folgendes bereitgestellt:

* Cloud-LRS-Speicherverwendung des Tresors
* Cloud-GRS-Speicherverwendung des Tresors


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Wie lange dauert es, bis der Auftragsstatus des Azure Backup-Agents im Portal angezeigt wird?
Es kann bis zu 15 Minuten dauern, bis der Status des Azure Backup-Agent-Auftrags im Azure-Portal angezeigt wird.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Wie lange dauert es, bis eine Warnung ausgelöst wird, wenn ein Sicherungsauftrag nicht erfolgreich ist?
Innerhalb von 20 Minuten nach dem Azure-Sicherungsfehler wird eine Warnung ausgelöst.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Kann es vorkommen, dass eine E-Mail nicht gesendet wird, wenn Benachrichtigungen konfiguriert sind?
Ja. In den folgenden Situationen werden keine Benachrichtigungen gesendet.

* Stündliche Benachrichtigungen wurden konfiguriert, und eine Warnung wird ausgelöst und innerhalb dieser Stunde gelöst.
* Ein Auftrag wurde abgebrochen.
* Bei einem zweiten Sicherungsauftrag tritt ein Fehler auf, weil der ursprüngliche Sicherungsauftrag noch ausgeführt wird.

## <a name="troubleshooting-monitoring-issues"></a>Problembehandlung bei der Überwachung

**Problem:** Aufträge und/oder Warnungen vom Azure Backup-Agent werden im Portal nicht angezeigt.

**Schritte zur Problembehandlung:** Der Prozess ```OBRecoveryServicesManagementAgent``` sendet den Auftrag und die Warnungsdaten an den Azure Backup-Dienst. Gelegentlich kann es bei diesem Prozess zu Unterbrechungen kommen, oder er kann ungewollt beendet werden.

1. Um sicherzustellen, dass der Prozess nicht gerade ausgeführt wird, öffnen Sie den **Task-Manager**, und überprüfen Sie, ob ```OBRecoveryServicesManagementAgent``` gerade ausgeführt wird.

2. Wenn der Prozess nicht ausgeführt wird, öffnen Sie die **Systemsteuerung**, und durchsuchen Sie die Liste der Dienste. Starten Sie den **Microsoft Azure Recovery Services Management-Agent** bzw. starten Sie ihn neu.

    Weitere Informationen finden Sie in den Protokollen unter:<br/>
   Beispiel: `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Nächste Schritte
* [Wiederherstellen von Windows-Servern oder Windows-Clients aus Azure](backup-azure-restore-windows-server.md)
* Weitere Informationen zu Azure Backup finden Sie unter [Azure Backup – Übersicht](backup-introduction-to-azure-backup.md)
* Besuchen Sie das [Azure Backup-Forum](https://go.microsoft.com/fwlink/p/?LinkId=290933)
