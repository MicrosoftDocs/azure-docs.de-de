---
title: Häufig gestellte Fragen zur Sicherung von Azure-VMs mit Azure Backup
description: Antworten auf häufig gestellte Fragen zur Sicherung von Azure-VMs mit Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sogup
ms.openlocfilehash: 9d4d1db808446cb010e6551bdcec514fc550d802
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966321"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Häufig gestellte Fragen – Sicherung von Azure-VMs

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zur Sicherung von Azure-VMs mit dem Dienst [Azure Backup](backup-introduction-to-azure-backup.md).


## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Welche VM-Images können für die Sicherung aktiviert werden, wenn ich sie erstelle?
Wenn Sie eine VM erstellen, können Sie die Sicherung für VMs aktivieren, die auf [unterstützten Betriebssystemen](backup-support-matrix-iaas.md#supported-backup-actions) ausgeführt werden.

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


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Welche Azure-VMs können mithilfe von Azure Backup gesichert werden?

Überprüfen Sie die [Supportmatrix](backup-support-matrix-iaas.md) auf Supportdetails und -einschränkungen.

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Wird bei bedarfsbasierten Sicherungsaufträgen der gleiche Aufbewahrungszeitplan verwendet wie bei geplanten Sicherungen?
Nein. Geben Sie die Beibehaltungsdauer für einen bedarfsgesteuerten Sicherungsauftrag an. Bei Aufträgen, die über das Portal ausgelöst werden, beträgt die Aufbewahrungsdauer standardmäßig 30 Tage.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ich habe kürzlich für einige virtuelle Computer Azure Disk Encryption aktiviert. Funktionieren meine Sicherungen auch weiterhin?
Gewähren Sie Azure Backup Berechtigungen für den Zugriff auf Key Vault. Geben Sie die Berechtigungen wie im Abschnitt zum **Aktivieren der Sicherung** in der Dokumentation zu [Azure Backup PowerShell](backup-azure-vms-automation.md) in PowerShell ein.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Ich habe VM-Datenträger zu verwalteten Datenträgern migriert. Funktionieren meine Sicherungen auch weiterhin?
Ja, Sicherungen funktionieren reibungslos. Sie müssen keine Neukonfiguration vornehmen.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Warum wird mein virtueller Computer im Assistenten für die Sicherungskonfiguration nicht angezeigt?
Der Assistent zeigt nur VMs an, die sich in derselben Region wie der Tresor befinden und noch nicht gesichert wurden.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Mein virtueller Computer ist heruntergefahren. Soll eine bedarfsgesteuerte oder geplante Sicherung durchgeführt werden?
Ja. Sicherungen werden ausgeführt, wenn ein Computer heruntergefahren wird. Der Wiederherstellungspunkt ist als absturzkonsistent gekennzeichnet.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kann ich einen aktuellen Sicherungsauftrag abbrechen?
Ja. Sie können einen Sicherungsauftrag während des Status **Momentaufnahme wird erstellt...** abbrechen. Ein Auftrag kann nicht abgebrochen werden, wenn eine Datenübertragung von der Momentaufnahme stattfindet.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Ich habe die Sperre für die vom Azure Backup-Dienst erstellte Ressourcengruppe aktiviert (d.h. `AzureBackupRG_<geo>_<number>`) – funktionieren meine Sicherungen auch weiterhin?
Wenn Sie die vom Azure Backup-Dienst erstellte Ressourcengruppe sperren, treten bei Sicherungen Fehler auf, da eine Höchstgrenze von 18 Wiederherstellungspunkten besteht.

Der Benutzer muss die Sperre aufheben und die Wiederherstellungspunkt-Sammlung aus dieser Ressourcengruppe löschen, damit die künftigen Sicherungen erfolgreich sind. [Gehen Sie folgendermaßen vor](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal), um die Wiederherstellungspunkt-Sammlung zu entfernen.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>Berücksichtigt die Sicherungsrichtlinie die Sommerzeit?
Nein. Das Datum und die Uhrzeit auf Ihrem lokalen Computer stimmt mit der aktuellen lokalen Zeit überein (Sommerzeit berücksichtigt). Die festgelegte Zeit für geplante Sicherungen kann aufgrund der Sommerzeit von der lokalen Zeit abweichen.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Wie viele Datenträger kann ich an eine VM anfügen, die durch Azure Backup gesichert wird?
Azure Backup kann VMs mit bis zu 16 Datenträgern sichern. Unterstützung für 16 Datenträger wird in der [sofortigen Wiederherstellung](backup-instant-restore-capability.md) bereitgestellt.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Unterstützt Azure Backup verwaltete SSD Standard-Datenträger?
Azure Backup unterstützt [verwaltete SSD Standard-Datenträger](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Verwaltete SSD-Datenträger stellen eine neue Art von dauerhaftem Speicher für Azure-VMs bereit. Unterstützung für verwaltete SSD-Datenträger wird in der [sofortigen Wiederherstellung](backup-instant-restore-capability.md) bereitgestellt.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Können ich eine VM mit einem Datenträger mit aktivierter Schreibbeschleunigung sichern?
Momentaufnahmen können auf dem Datenträger mit aktivierter Schreibbeschleunigung nicht erstellt werden. Der Azure Backup-Dienst kann den Datenträger mit aktivierter Schreibbeschleunigung jedoch von der Sicherung ausschließen. Der Ausschluss von Datenträgern für VMs mit Datenträgern mit aktivierter Schreibbeschleunigung wird nur für Abonnements unterstützt, für die ein Upgrade auf die sofortige Wiederherstellung durchgeführt wurde.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Ich verfüge über eine VM mit Datenträgern mit Schreibbeschleunigung und installiertem SAP HANA. Wie kann ich diese sichern?
Azure Backup kann den Datenträger mit aktivierter Schreibbeschleunigung nicht sichern, kann ihn aber von der Sicherung ausschließen. Die Sicherung bietet jedoch keine Datenbankkonsistenz, da die Informationen auf dem Datenträger mit aktivierter Schreibbeschleunigung nicht gesichert werden. Sie können Datenträger mit dieser Konfiguration sichern, wenn Sie eine Sicherung des Betriebssystemdatenträgers möchten, sowie Sicherungen von Datenträgern ohne aktivierte Schreibbeschleunigung durchführen.

Wir verfügen über eine private Vorschau zu einer SAP HANA-Sicherung mit einer RPO von 15 Minuten. Diese ist ähnlich wie die Sicherung von SQL-Datenbank aufgebaut und verwendet die Schnittstelle „backInt“ für Drittanbieterlösungen, die durch SAP HANA zertifiziert sind. Wenn Sie interessiert sind, schreiben Sie uns an `AskAzureBackupTeam@microsoft.com` eine E-Mail mit dem Betreff **Sign up for private preview for backup of SAP HANA in Azure VMs** (Registrierung für die private Vorschau zur Sicherung von SAP HANA in Azure-VMs).


## <a name="restore"></a>Restore

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Wie entscheide ich, ob ich nur Datenträger oder eine vollständige VM wiederherstelle?
Bei der Wiederherstellung einer VM handelt es sich gewissermaßen um eine Schnellerstellungsoption für eine Azure-VM. Diese Option ändert die Namen von Datenträgern, Container, die von den Datenträgern verwendet werden, öffentliche IP-Adressen und die Namen von Netzwerkschnittstellen. Die Änderung behält bei der Erstellung einer VM eindeutige Ressourcen bei. Die VM wird nicht zu einer Verfügbarkeitsgruppe hinzugefügt.

Die Option zur Wiederherstellung eines Datenträgers können Sie verwenden, wenn Sie ...
  * ...die VM, die erstellt wird, anpassen möchten. Zum Beispiel, wenn Sie die Größe ändern möchten.
  * ...Konfigurationseinstellungen hinzufügen möchten, die zum Zeitpunkt der Sicherung nicht vorhanden waren.
  * ...die Namenskonvention für Ressourcen steuern möchten, die erstellt werden.
  * ...die VM zu einer Verfügbarkeitsgruppe hinzufügen möchten.
  * ...eine andere Einstellung hinzufügen möchten, die mithilfe von PowerShell oder einer Vorlage konfiguriert werden muss.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kann ich Sicherungen nicht verwalteter VM-Datenträger wiederherstellen, nachdem ich ein Upgrade auf verwaltete Datenträger durchgeführt habe?
Ja, Sie können Sicherungen verwenden, die erstellt wurden, bevor Datenträger von nicht verwaltet auf verwaltet umgestellt wurden.
- Standardmäßig wird bei einem VM-Wiederherstellungsauftrag eine nicht verwaltete VM erstellt.
- Jedoch können Sie Datenträger wiederherstellen und zum Erstellen einer verwalteten VM verwenden.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Wie kann ich eine VM auf einen Punkt vor der Migration der VM zu verwalteten Datenträgern wiederherstellen?
Standardmäßig wird bei einem VM-Wiederherstellungsauftrag eine VM mit nicht verwalteten Datenträgern erstellt. So erstellen Sie einen virtuellen Computer mit verwalteten Datenträgern:
1. [Stellen Sie wieder her auf nicht verwaltete Datenträger](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Konvertieren Sie die wiederhergestellten Datenträger in verwaltete Datenträger](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Erstellen Sie eine VM mit verwalteten Datenträgern](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Hier](backup-azure-vms-automation.md#restore-an-azure-vm) finden Sie weitere Informationen dazu, wie Sie diese Schritte in PowerShell ausführen.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kann ich die VM wiederherstellen, die gelöscht wurde?
Ja. Selbst wenn Sie die VM gelöscht haben, können Sie diese über das entsprechende Sicherungselement im Tresor mithilfe eines Wiederherstellungspunkts wiederherstellen.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Wie wird eine VM in dieselben Verfügbarkeitsgruppen wiederhergestellt?
Bei Azure-VMs mit verwalteten Datenträgern ist die Wiederherstellung in Verfügbarkeitsgruppen durch eine Option in der Vorlage beim Wiederherstellen als verwaltete Datenträger möglich. Diese Vorlage verfügt über den Eingabeparameter **Verfügbarkeitsgruppen**.

### <a name="how-do-we-get-faster-restore-performances"></a>Wie erreichen ich bei Wiederherstellungen eine schnellere Leistung?
Um die Wiederherstellungsleistung zu verbessern, stellen wir auf die Funktion [Sofortige Wiederherstellung](backup-instant-restore-capability.md) um.

## <a name="manage-vm-backups"></a>Verwalten von VM-Sicherungen

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Was geschieht, wenn ich eine Sicherungsrichtlinie ändere?
Die VM wird unter Verwendung der Zeitplan- und Aufbewahrungseinstellungen in der geänderten oder neuen Richtlinie gesichert.

- Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert und gemäß der neuen Richtlinie aufbewahrt.
- Bei einer Verkürzung der Aufbewahrung werden Wiederherstellungspunkte zum Löschen im Rahmen der nächsten Bereinigung markiert und dementsprechend gelöscht.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Wie verschiebe ich eine VM, die durch Azure Backup gesichert wurde, in eine andere Ressourcengruppe?

1. Halten Sie die Sicherung vorübergehend an, und bewahren Sie Sicherungsdaten auf.
2. Verschieben Sie die VM in die Zielressourcengruppe.
3. Aktivieren Sie die Sicherung erneut in demselben oder einem neuen Tresor.

Sie können eine Wiederherstellung der VM mithilfe der verfügbaren Wiederherstellungspunkte durchführen, die vor dem Verschiebevorgang erstellt wurden.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Gibt es einen Grenzwert bei der Anzahl der VMs, die derselben Sicherungsrichtlinie zugeordnet werden können?
Ja. Es gibt den Grenzwert 100 VMs, die über das Portal derselben Sicherungsrichtlinie zugeordnet werden können. Wir empfehlen, dass Sie bei mehr als 100 VMs mehrere Sicherungsrichtlinien mit demselben Zeitplan oder einem anderen Zeitplan erstellen.
