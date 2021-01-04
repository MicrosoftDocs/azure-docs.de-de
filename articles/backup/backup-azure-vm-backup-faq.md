---
title: 'Häufig gestellte Fragen (FAQ): Sichern virtueller Azure-Computer'
description: In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen zum Sichern von virtuellen Azure-Computern mit dem Azure Backup-Dienst.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 0f4f990654cc23fde7cf1ad2e37ba1ada76d94e3
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324787"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Häufig gestellte Fragen – Sicherung von Azure-VMs

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Sicherung von Azure-VMs mit dem Dienst [Azure Backup](./backup-overview.md).

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Welche VM-Images können für die Sicherung aktiviert werden, wenn ich sie erstelle?

Wenn Sie eine VM erstellen, können Sie die Sicherung für VMs aktivieren, die [unterstützte Betriebssysteme](backup-support-matrix-iaas.md#supported-backup-actions) ausführen.

### <a name="why-initial-backup-is-taking-lot-of-time-to-complete"></a>Warum dauert die erste Sicherung so lange?

Die erste Sicherung ist immer eine vollständige Sicherung, und die Dauer hängt von der Größe der Daten sowie vom Zeitpunkt der Sicherungsverarbeitung ab. <br>
Informationen zur Verbesserung der Sicherungsleistung finden Sie unter [Bewährte Methoden](./backup-azure-vms-introduction.md#best-practices), [Überlegungen zu Sicherung und Wiederherstellung](./backup-azure-vms-introduction.md#backup-and-restore-considerations) und [Backupleistung](./backup-azure-vms-introduction.md#backup-performance).<br>
Die Gesamtdauer der Sicherung von weniger als 24 Stunden gilt für inkrementelle Sicherungen, aber möglicherweise nicht für die erste Sicherung.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Sind die Sicherungskosten in den VM-Kosten enthalten?

Nein. Die Sicherungskosten werden separat zu den Kosten einer VM berechnet. Erfahren Sie mehr über die [Preise für Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Welche Berechtigungen werden zum Aktivieren der Sicherung für eine VM benötigt?

Wenn Sie ein „VM-Mitwirkender“ sind, können Sie die Sicherung der VM aktivieren. Wenn Sie eine benutzerdefinierte Rolle verwenden, benötigen Sie die folgenden Berechtigungen zum Aktivieren der Sicherung der VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Stellen Sie sicher, dass Sie in der Ressourcengruppe für den Recovery Services-Tresor über Schreibberechtigungen verfügen, wenn der Recovery Services-Tresor und die VM unterschiedliche Ressourcengruppen aufweisen.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Wird bei bedarfsbasierten Sicherungsaufträgen der gleiche Aufbewahrungszeitplan verwendet wie bei geplanten Sicherungen?

Nein. Geben Sie die Beibehaltungsdauer für einen bedarfsgesteuerten Sicherungsauftrag an. Bei Aufträgen, die über das Portal ausgelöst werden, beträgt die Aufbewahrungsdauer standardmäßig 30 Tage.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ich habe kürzlich für einige virtuelle Computer Azure Disk Encryption aktiviert. Funktionieren meine Sicherungen auch weiterhin?

Gewähren Sie Azure Backup Berechtigungen für den Zugriff auf die Key Vault-Instanz. Geben Sie die Berechtigungen wie im Abschnitt zum **Aktivieren der Sicherung** in der Dokumentation zu [Azure Backup PowerShell](backup-azure-vms-automation.md) in PowerShell ein.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Ich habe VM-Datenträger zu verwalteten Datenträgern migriert. Funktionieren meine Sicherungen auch weiterhin?

Ja, Sicherungen funktionieren reibungslos. Sie müssen keine Neukonfiguration vornehmen.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Warum wird mein virtueller Computer im Assistenten für die Sicherungskonfiguration nicht angezeigt?

Der Assistent zeigt nur VMs an, die sich in derselben Region wie der Tresor befinden und noch nicht gesichert wurden.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Mein virtueller Computer ist heruntergefahren. Soll eine bedarfsgesteuerte oder geplante Sicherung durchgeführt werden?

Ja. Sicherungen werden ausgeführt, wenn ein Computer heruntergefahren wird. Der Wiederherstellungspunkt ist als absturzkonsistent gekennzeichnet.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kann ich einen aktuellen Sicherungsauftrag abbrechen?

Ja. Sie können den Sicherungsauftrag im Status **Momentaufnahme wird erstellt** abbrechen. Ein Auftrag kann nicht abgebrochen werden, wenn eine Datenübertragung von der Momentaufnahme stattfindet.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Ich habe für die vom Azure Backup-Dienst erstellte Ressourcengruppe eine Sperre aktiviert (z. B. `AzureBackupRG_<geo>_<number>`). Funktionieren meine Sicherungen auch weiterhin?

Wenn Sie die vom Azure Backup-Dienst erstellte Ressourcengruppe sperren, treten bei Sicherungen Fehler auf, da ein Limit von 18 Wiederherstellungspunkten besteht.

Entfernen Sie die Sperre, und löschen Sie die Wiederherstellungspunktsammlung aus dieser Ressourcengruppe, damit zukünftige Sicherungen erfolgreich ausgeführt werden. [Führen Sie diese Schritte aus](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal), um die Wiederherstellungspunktsammlung zu entfernen.

### <a name="i-have-a-lock-at-the-resource-group-level-that-contains-all-the-resources-related-to-my-virtual-machine-will-my-backup-work"></a>Ich verfüge über eine Sperre auf Ressourcengruppenebene, die alle Ressourcen meines virtuellen Computers umfasst. Funktioniert meine Sicherung?

Azure Backup erstellt eine separate Ressourcengruppe im Format `AzureBackupRG_<geo>_<number>`, um ResourcePointCollections-Objekte zu speichern. Da sich diese Ressourcengruppe im Besitz des Diensts befindet, führt das Sperren bei Sicherungen zu einem Fehler. Sperren können nur auf von Kunden erstellte Ressourcengruppen angewandt werden.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Unterstützt Azure Backup verwaltete SSD Standard-Datenträger?

Ja, Azure Backup unterstützt [verwaltete SSD Standard-Datenträger](../virtual-machines/disks-types.md#standard-ssd).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Können ich eine VM mit einem Datenträger mit aktivierter Schreibbeschleunigung sichern?

Momentaufnahmen können nur auf Datenträgern für Daten mit aktivierter Schreibbeschleunigung aktiviert werden, nicht auf Betriebssystemdatenträgern. Daher können nur Datenträger mit aktivierter Schreibbeschleunigung geschützt werden.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Ich verfüge über eine VM mit Datenträgern mit Schreibbeschleunigung und installiertem SAP HANA. Wie kann ich diese sichern?

Azure Backup kann Datenträger mit aktivierter Schreibbeschleunigung sichern. Die Sicherung bietet jedoch keine Datenbankkonsistenz.

Azure Backup bietet eine Streamingsicherungslösung für SAP HANA-Datenbanken mit einer RPO von 15 Minuten. Der Dienst ist Backint-zertifiziert durch SAP und bietet native Sicherungsunterstützung durch Verwendung der nativen APIs von SAP HANA. Erfahren Sie mehr über [das Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Mit welcher maximalen Verzögerung muss ich bei der Startzeit der Sicherungskopie im Vergleich zu der geplanten Sicherungszeit rechnen, die ich in meiner VM-Sicherungsrichtlinie eingerichtet habe?

Die geplante Sicherung wird innerhalb von 2 Stunden nach der geplanten Sicherungszeit ausgelöst. Wenn beispielsweise für 100 VMs eine Sicherungsstartzeit von 2:00 Uhr geplant ist, werden spätestens um 4:00 Uhr die Sicherungsaufträge für alle 100 VMs ausgeführt. Wenn geplante Sicherungen aufgrund eines Ausfalls angehalten und dann fortgesetzt oder wiederholt wurden, beginnt eine Sicherung möglicherweise außerhalb dieses geplanten Zeitfensters von zwei Stunden.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Welches der kürzeste zulässige Aufbewahrungszeitraum für einen täglichen Sicherungspunkt?

Die Sicherungsrichtlinie von Azure Virtual Machines unterstützt einen Mindestzeitraum für die Aufbewahrung zwischen 7 und 9999 Tagen. Jede Änderung einer vorhandenen VM-Sicherungsrichtlinie mit weniger als sieben Tagen erfordert ein Update, um die Mindestaufbewahrungsdauer von sieben Tagen einzuhalten.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Was passiert, wenn ich im Namen einer VM oder VM-Ressourcengruppe die Groß- und Kleinschreibung ändere?

Wenn Sie die Groß- und Kleinschreibung im Namen Ihrer VM oder VM-Ressourcengruppe ändern, ändert sich die Schreibweise des Namens des Sicherungselements nicht. Dies ist ein erwartetes Verhalten in Azure Backup. Im Sicherungselement ändert sich die Groß- und Kleinschreibung nicht, aber im Back-End wird sie aktualisiert.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Kann ich an eine VM angefügte Datenträger selektiv sichern oder wiederherstellen?

Azure Backup unterstützt jetzt die selektive Datenträgersicherung und -wiederherstellung mithilfe der Azure Virtual Machine-Sicherungslösung. Weitere Informationen finden Sie unter [Selektives Sichern und Wiederherstellen von Datenträgern auf Azure-VMs](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Bleiben verwaltete Identitäten erhalten, wenn während der Sicherung eine Mandantenänderung auftritt?

Wenn [Mandantenänderungen](/azure/devops/organizations/accounts/change-azure-ad-connection) auftreten, müssen Sie [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) deaktivieren und erneut aktivieren, damit Sicherungen wieder funktionieren.

## <a name="restore"></a>Restore

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Wie entscheide ich, ob ich nur Datenträger oder eine vollständige VM wiederherstelle?

Bei der Wiederherstellung einer VM handelt es sich gewissermaßen um eine Schnellerstellungsoption für eine Azure-VM. Diese Option ändert die Namen von Datenträgern, Container, die von den Datenträgern verwendet werden, öffentliche IP-Adressen und die Namen von Netzwerkschnittstellen. Die Änderung behält bei der Erstellung einer VM eindeutige Ressourcen bei. Die VM wird nicht zu einer Verfügbarkeitsgruppe hinzugefügt.

Die Option zur Wiederherstellung eines Datenträgers können Sie verwenden, wenn Sie ...

- ...die VM, die erstellt wird, anpassen möchten. Zum Beispiel, wenn Sie die Größe ändern möchten.
- ... Konfigurationseinstellungen hinzufügen möchten, die zum Zeitpunkt der Sicherung nicht vorhanden waren.
- ...die Namenskonvention für Ressourcen steuern möchten, die erstellt werden.
- ...die VM zu einer Verfügbarkeitsgruppe hinzufügen möchten.
- ...eine andere Einstellung hinzufügen möchten, die mithilfe von PowerShell oder einer Vorlage konfiguriert werden muss.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kann ich Sicherungen nicht verwalteter VM-Datenträger wiederherstellen, nachdem ich ein Upgrade auf verwaltete Datenträger durchgeführt habe?

Ja, Sie können Sicherungen verwenden, die erstellt wurden, bevor Datenträger von nicht verwaltet auf verwaltet umgestellt wurden.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Wie kann ich eine VM auf einen Punkt vor der Migration der VM zu verwalteten Datenträgern wiederherstellen?

Der Wiederherstellungsvorgangs bleibt gleich. Wenn der Wiederherstellungspunkt ein Zeitpunkt ist, zu dem der virtuelle Computer über nicht verwaltete Datenträger verfügte, können Sie [Datenträger als nicht verwaltete Datenträger wiederherstellen](tutorial-restore-disk.md#unmanaged-disks-restore). Wenn die VM zu diesem Zeitpunkt über verwaltete Datenträger verfügte, können Sie [Datenträger als verwaltete Datenträger wiederherstellen](tutorial-restore-disk.md#managed-disk-restore). Anschließend können Sie [einen virtuellen Computer aus diesen Datenträgern erstellen](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Hier](backup-azure-vms-automation.md#restore-an-azure-vm) finden Sie weitere Informationen dazu, wie Sie diese Schritte in PowerShell ausführen.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Was geschieht mit den in der Wiederherstellung enthaltenen Datenträgern, wenn die VM bei der Wiederherstellung nicht erstellt wird?

Bei der Wiederherstellung einer verwalteten VM werden die Datenträger auch dann wiederhergestellt, wenn bei der VM-Erstellung ein Fehler auftritt.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Kann ich eine gelöschte VM wiederherstellen?

Ja. Selbst wenn Sie die VM gelöscht haben, können Sie diese über das entsprechende Sicherungselement im Tresor mithilfe eines Wiederherstellungspunkts wiederherstellen.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Wie stelle ich eine VM in denselben Verfügbarkeitsgruppen wieder her?

Bei Azure-VMs mit verwalteten Datenträgern ist die Wiederherstellung in Verfügbarkeitsgruppen durch eine Option in der Vorlage zur Wiederherstellung als verwaltete Datenträger möglich. Diese Vorlage verfügt über den Eingabeparameter **Verfügbarkeitsgruppen**.

### <a name="how-do-we-get-faster-restore-performances"></a>Wie erreichen ich bei Wiederherstellungen eine schnellere Leistung?

Die Funktion zur [sofortigen Wiederherstellung](backup-instant-restore-capability.md) ermöglicht schnellere Sicherungen und sofortige Wiederherstellungen aus den Momentaufnahmen.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Was geschieht, wenn wir die Key Vault-Einstellungen für die verschlüsselte VM ändern?

Nachdem Sie die Schlüsseltresoreinstellungen für die verschlüsselte VM geändert haben, funktionieren Sicherungen weiterhin mit dem neuen Satz von Informationen. Nach der Wiederherstellung von einem Wiederherstellungspunkt vor der Änderung müssen Sie jedoch die Geheimnisse in einem Schlüsseltresor wiederherstellen, bevor Sie die VM daraus erstellen können. Weitere Informationen finden Sie in [diesem Artikel](./backup-azure-restore-key-secret.md).

Für Vorgänge wie Geheimnis-/Schlüsselrollover ist dieser Schritt nicht erforderlich, und nach der Wiederherstellung kann derselbe Schlüsseltresor verwendet werden.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Kann ich auf den virtuellen Computer nach dessen Wiederherstellung zugreifen, die erforderlich war, weil ein virtueller Computer die Beziehung zum Domänencontroller unterbrochen hat?

Ja, dieser Zugriff ist möglich. Weitere Informationen finden Sie in [diesem Artikel](./backup-azure-arm-restore-vms.md#post-restore-steps).

### <a name="why-restore-operation-is-taking-long-time-to-complete"></a>Warum dauert der Wiederherstellungsvorgang so lange?

Die gesamte Wiederherstellungszeit hängt von den Eingabe-/Ausgabevorgängen pro Sekunde (IOPS) und dem Durchsatz des Speicherkontos ab. Wenn das Zielspeicherkonto mit anderen anwendungsbezogenen Lese- und Schreibvorgängen beschäftigt ist, kann sich dies auf die gesamte Wiederherstellungszeit auswirken. Wählen Sie zum Verbessern des Wiederherstellungsvorgangs ein Speicherkonto ohne andere Anwendungsdaten aus.

## <a name="manage-vm-backups"></a>Verwalten von VM-Sicherungen

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Was geschieht, wenn ich eine Sicherungsrichtlinie ändere?

Die VM wird unter Verwendung der Zeitplan- und Aufbewahrungseinstellungen in der geänderten oder neuen Richtlinie gesichert.

- Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert und gemäß der neuen Richtlinie aufbewahrt.
- Bei einer Verkürzung der Aufbewahrung werden Wiederherstellungspunkte zum Löschen im Rahmen der nächsten Bereinigung markiert und dementsprechend gelöscht.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Wie verschiebe ich eine VM, die durch Azure Backup gesichert wurde, in eine andere Ressourcengruppe?

1. Halten Sie die Sicherung vorübergehend an, und bewahren Sie Sicherungsdaten auf.
2. Gehen Sie folgendermaßen vor, um mit Azure Backup konfigurierte virtuelle Computer zu verschieben:

   1. Ermitteln Sie den Speicherort Ihres virtuellen Computers.
   2. Suchen Sie eine Ressourcengruppe mit dem folgenden Namensmuster: `AzureBackupRG_<location of your VM>_1`. Beispiel: *AzureBackupRG_westus2_1*
   3. Aktivieren Sie im Azure-Portal die Option **Ausgeblendete Typen anzeigen**.
   4. Suchen Sie die Ressource mit dem Typ **Microsoft.Compute/restorePointCollections** und dem Namensmuster `AzureBackup_<name of your VM that you're trying to move>_###########`.
   5. Löschen Sie diese Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.
   6. Nach Abschluss des Löschvorgangs können Sie den virtuellen Computer verschieben.

3. Verschieben Sie die VM in die Zielressourcengruppe.
4. Nehmen Sie die Sicherung wieder auf.

Sie können eine Wiederherstellung der VM mithilfe der verfügbaren Wiederherstellungspunkte durchführen, die vor dem Verschiebevorgang erstellt wurden.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Was geschieht, nachdem ich eine VM in eine andere Ressourcengruppe verschoben habe?

Nachdem eine VM in eine andere Ressourcengruppe verschoben wurde, gilt sie für Azure Backup als neue VM.

Nachdem Sie die VM in eine neue Ressourcengruppe verschoben haben, können Sie die VM entweder im gleichen oder in einem anderen Tresor wieder schützen. Da es sich für Azure Backup um eine neue VM handelt, wird sie separat in Rechnung gestellt.

Die Wiederherstellungspunkte der alten VM können bei Bedarf wiederhergestellt werden. Wenn Sie diese Sicherungsdaten nicht benötigen, können Sie den Schutz der alten VM durch „Daten löschen“ beenden.

### <a name="is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy"></a>Gibt es einen Grenzwert bei der Anzahl von VMs, die derselben Sicherungsrichtlinie zugeordnet werden können?

Ja. Im Portal können maximal 100 VMs derselben Sicherungsrichtlinie zugeordnet werden. Wir empfehlen, dass Sie bei mehr als 100 VMs mehrere Sicherungsrichtlinien mit demselben Zeitplan oder einem anderen Zeitplan erstellen.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Wie kann ich die Aufbewahrungseinstellungen für meine Sicherungen anzeigen?

Derzeit können Sie die Aufbewahrungseinstellungen auf der Ebene eines Sicherungselements (VM) auf der Grundlage der Sicherungsrichtlinie anzeigen, die der VM zugewiesenen ist.

Eine Möglichkeit, die Aufbewahrungseinstellungen für Ihre Sicherungen anzuzeigen, besteht darin, zum [Dashboard](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) des Sicherungselements für Ihre VM im Azure-Portal zu navigieren. Wenn Sie den Link zu ihrer Sicherungsrichtlinie auswählen, können Sie die Aufbewahrungsdauer aller täglichen, wöchentlichen, monatlichen und jährlichen Aufbewahrungspunkte anzeigen, die mit der VM verbunden sind.

Sie können auch den [Sicherungs-Explorer](./monitor-azure-backup-with-backup-explorer.md) verwenden, um die Aufbewahrungseinstellungen für alle Ihre VMs in einer zentralisierten Benutzeroberfläche anzuzeigen. Navigieren Sie von einem beliebigen Recovery Services-Tresor aus zum Sicherungs-Explorer, wechseln Sie zur Registerkarte **Sicherungselemente**, und wählen Sie die erweiterte Ansicht aus, um detaillierte Aufbewahrungsinformationen für jede VM anzuzeigen.