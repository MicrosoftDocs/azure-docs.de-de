---
title: Antworten auf häufig gestellte Fragen
description: 'Antworten auf häufig gestellte Fragen zu: Azure Backup-Features wie Azure Recovery Services-Tresoren, zu sicherbaren Elementen sowie zu Funktionsweise, Verschlüsselung und Grenzwerten. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: d85866e490b2c56abb7de1e94cd0ffaa8f714615
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327150"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – häufig gestellte Fragen

In diesem Artikel werden allgemeine Fragen um Azure Backup-Dienst beantwortet.

## <a name="recovery-services-vault"></a>Recovery Services-Tresor

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Gibt es eine Beschränkung hinsichtlich der Anzahl von Tresoren, die in einem Azure-Abonnement erstellt werden können?

Ja. Pro Abonnement können für jede unterstützte Region von Azure Backup bis zu 500 Recovery Services-Tresore erstellt werden. Erstellen Sie ein zusätzliches Abonnement, falls Sie zusätzliche Tresore benötigen.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Gibt es Beschränkungen im Hinblick auf die Anzahl von Servern/Computern, die pro Tresor registriert werden können?

Sie können bis zu 1.000 virtuelle Azure-Computer pro Tresor registrieren. Bei Verwendung des Microsoft Azure Backup-Agents können Sie pro Tresor bis zu 50 MARS-Agents registrieren. Außerdem können Sie 50 MABS-Server/DPM-Server bei einem Tresor registrieren.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Wie viele Datenquellen/Elemente können in einem Tresor geschützt werden?

Sie können bis zu 2.000 Datenquellen/Elemente für alle Workloads (etwa IaaS-VM, SQL, AFS) in einem Tresor schützen.
Wenn Sie beispielsweise bereits 500 virtuelle Computer und 400 Azure-Dateifreigaben im Tresor geschützt haben, können Sie nur maximal 1100 SQL-Datenbanken darin schützen.

### <a name="how-many-policies-can-i-create-per-vault"></a>Wie viele Richtlinien kann ich pro Tresor erstellen?

Sie können nur maximal 200 Richtlinien pro Tresor erstellen.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Wie kann ich Daten von unterschiedlichen Servern im Tresor isolieren, wenn meine Organisation über nur einen Tresor verfügt?

Serverdaten, die Sie zusammen wiederherstellen möchten, sollten beim Einrichten der Sicherung die gleiche Passphrase verwenden. Wenn Sie die Wiederherstellung auf bestimmten Servern isolieren möchten, verwenden Sie eine Passphrase nur für diese Server. So können Sie beispielsweise für die Server der Personalabteilung, für die Server der Buchhaltung und für die Speicherserver jeweils eine eigene Verschlüsselungspassphrase verwenden.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kann ich meinen Tresor zwischen Abonnements verschieben?

Ja. Informationen zum Verschieben eines Recovery Services-Tresors finden Sie in [diesem Artikel](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>Kann ich Sicherungsdaten zu einem anderen Tresor verschieben?

Nein. In einem Tresor gespeicherte Sicherungsdaten können nicht in einen anderen Tresor verschoben werden.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Kann ich die Einstellung für die Speicherredundanz nach einer Sicherung ändern?

Der Speicherreplikationstyp ist standardmäßig auf „Georedundant“ (GRS) festgelegt. Nachdem Sie die Sicherung konfiguriert haben, ist die Option zum Ändern deaktiviert, und der Typ kann nicht mehr geändert werden.

![Speicherreplikationstyp](./media/backup-azure-backup-faq/storage-replication-type.png)

Wenn Sie die Sicherung bereits konfiguriert haben und von GRS zu LRS wechseln müssen, finden Sie weitere Informationen unter [Wechseln von GRS zu LRS nach dem Konfigurieren einer Sicherung](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup).

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kann ich für virtuelle Computer, die in einem Recovery Services-Tresor gesichert wurden, eine Wiederherstellung auf Elementebene durchführen?

- Die Wiederherstellung auf Elementebene wird für virtuelle Azure-Computer unterstützt, die von der Azure-VM-Sicherung gesichert wurden. Weitere Informationen finden Sie in [diesem Artikel](backup-azure-restore-files-from-vm.md).
- Die Wiederherstellung auf Elementebene wird nicht für Online-Wiederherstellungspunkte lokaler virtueller Computer unterstützt, die von Azure Backup Server (MABS) oder System Center DPM gesichert wurden.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Wie kann ich Daten aus dem Recovery Services-Tresor in eine lokale Umgebung verschieben?

Das direkte Exportieren von Daten aus dem Recovery Services-Tresor in eine lokale Umgebung mithilfe von Data Box wird nicht unterstützt. Die Daten müssen in einem Speicherkonto wiederhergestellt werden und können dann über [Data Box](../databox/data-box-overview.md) oder [Import/Export](../storage/common/storage-import-export-service.md) in eine lokale Umgebung verschoben werden.

## <a name="azure-backup-agent"></a>Azure Backup-Agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Wo finde ich verbreitete Fragen zum Azure Backup-Agent für die Azure-VM-Sicherung?

- Informationen zu den auf virtuellen Azure-Computern ausgeführten Agents finden Sie in diesen [häufig gestellten Fragen](backup-azure-vm-backup-faq.md).
- Informationen zu den Agents für das Sichern von Azure-Dateiordnern finden Sie in diesen [häufig gestellten Fragen](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Allgemeine Sicherung

### <a name="are-there-limits-on-backup-scheduling"></a>Gibt es Beschränkungen bei der Planung der Sicherung?

Ja.

- Sie können Computer mit Windows Server und Windows bis zu dreimal täglich sichern. Sie können die Planungsrichtlinie auf tägliche oder wöchentliche Zeitpläne festlegen.
- Sie können DPM bis zu zweimal täglich sichern. Sie können die Planungsrichtlinie auf tägliche, wöchentliche, monatliche oder jährliche Zeitpläne festlegen.
- Sie sichern Azure-VMs einmal täglich.

### <a name="what-operating-systems-are-supported-for-backup"></a>Welche Betriebssysteme werden für die Sicherung unterstützt?

Azure Backup unterstützt diese Betriebssysteme für die Sicherung von Dateien und Ordnern sowie von Apps, die mithilfe von Azure Backup Server und DPM geschützt werden.

**Betriebssystem** | **SKU** | **Details**
--- | --- | ---
Arbeitsstation | |
Windows 10, 64 Bit | Enterprise, Pro, Home | Auf Computern müssen die neuesten Service Packs und Updates ausgeführt werden.
Windows 8.1, 64 Bit | Enterprise, Pro | Auf Computern müssen die neuesten Service Packs und Updates ausgeführt werden.
Windows 8, 64 Bit | Enterprise, Pro | Auf Computern müssen die neuesten Service Packs und Updates ausgeführt werden.
Windows 7, 64 Bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Auf Computern müssen die neuesten Service Packs und Updates ausgeführt werden.
Server | |
Windows Server 2019, 64 Bit | Standard, Datacenter, Essentials | Mit den neuesten Service Packs/Updates.
Windows Server 2016, 64 Bit | Standard, Datacenter, Essentials | Mit den neuesten Service Packs/Updates.
Windows Server 2012 R2, 64 Bit | Standard, Datacenter, Foundation | Mit den neuesten Service Packs/Updates.
Windows Server 2012, 64 Bit | Datacenter, Foundation, Standard | Mit den neuesten Service Packs/Updates.
Windows Storage Server 2016, 64 Bit | Standard, Workgroup | Mit den neuesten Service Packs/Updates.
Windows Storage Server 2012 R2, 64 Bit | Standard, Workgroup, Essential | Mit den neuesten Service Packs/Updates.
Windows Storage Server 2012, 64 Bit | Standard, Workgroup | Mit den neuesten Service Packs/Updates.
Windows Server 2008 R2 SP1, 64 Bit | Standard, Enterprise, Datacenter, Foundation | Mit den neuesten Updates.
Windows Server 2008, 64 Bit | Standard, Enterprise, Datacenter | Mit den neuesten Updates.

Azure Backup unterstützt keine 32-Bit-Betriebssysteme.

Für Sicherungen von virtuellen Azure-Computern mit Linux unterstützt Azure Backup [die Liste der von Azure unterstützten Distributionen](../virtual-machines/linux/endorsed-distros.md), mit Ausnahme von CoreOS Linux und 32-Bit-Betriebssystem. Andere Bring-Your-Own-Linux-Distributionen sollten funktionieren, sofern der VM-Agent auf dem virtuellen Computer verfügbar ist und Python unterstützt wird.

### <a name="are-there-size-limits-for-data-backup"></a>Gibt es Größenbeschränkungen für die Datensicherung?

Die folgenden Größengrenzwerte gelten:

Betriebssystem/Computer | Größengrenzwert der Datenquelle
--- | ---
Windows 8 oder höher | 54.400 GB
Windows 7 |1\.700 GB
Windows Server 2012 oder höher | 54.400 GB
Windows Server 2008, Windows Server 2008 R2 | 1\.700 GB
Azure VM | Weitere Informationen finden Sie in der [Unterstützungsmatrix für die Sicherung von Azure-VMs](./backup-support-matrix-iaas.md#vm-storage-support).

### <a name="how-is-the-data-source-size-determined"></a>Wie wird die Größe der Datenquelle bestimmt?

In der folgenden Tabelle wird beschrieben, wie die einzelnen Datenquellengrößen bestimmt werden.

**Datenquelle** | **Details**
--- | ---
Volume |Die Datenmenge, die von einem virtuellen Computer mit einem Volume gesichert wird.
SQL Server-Datenbank |Größe einer einzelnen Datenbank, die gesichert wird.
SharePoint | Summe der Inhalts- und Konfigurationsdatenbanken in einer zu sichernden SharePoint-Farm
Exchange |Summe aller Exchange-Datenbanken eines zu sichernden Exchange-Servers
BMR/Systemstatus |Jede einzelne Kopie der BMR oder des Systemstatus des zu sichernden Computers

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Gibt es einen Grenzwert für die Menge der in einem Recovery Services-Tresor gesicherten Daten?

Es gibt keine Beschränkung für die Gesamtmenge der Daten, die in einem Recovery Services-Tresor gesichert werden können. Die einzelnen Datenquellen (neben Azure-VMs) können maximal 54.400 GB groß sein. Weitere Informationen zu Grenzwerten finden Sie im [Abschnitt zu Tresoreinschränkungen in der Unterstützungsmatrix](./backup-support-matrix.md#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Warum ist die Datenmenge, die an den Recovery Services-Tresor übertragen wird, kleiner als die Menge der für die Sicherung ausgewählten Daten?

Daten, die vom Azure Backup-Agent, von DPM oder von Azure Backup Server gesichert werden, werden vor der Übertragung komprimiert und verschlüsselt. Nach Anwendung der Komprimierung und Verschlüsselung sind die Daten im Tresor um 30 bis 40 % kleiner.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kann ich einzelne Dateien für einen Wiederherstellungspunkt im Tresor löschen?

Nein. Für Azure Backup wird das Löschen oder Bereinigen von einzelnen Dateien aus gespeicherten Sicherungen nicht unterstützt.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Werden die übertragenen Sicherungsdaten gelöscht, wenn ich einen Sicherungsauftrag nach dem Starten abbreche?

Nein. Alle Daten, die vor dem Abbrechen des Sicherungsauftrags in den Tresor übertragen wurden, bleiben im Tresor erhalten.

- Azure Backup nutzt einen Prüfpunktmechanismus, um den Sicherungsdaten während des Sicherungsvorgangs von Zeit zu Zeit Prüfpunkte hinzuzufügen.
- Da in den Sicherungsdaten Prüfpunkte vorhanden sind, kann der nächste Sicherungsprozess die Integrität der Dateien überprüfen.
- Der nächste Sicherungsauftrag erfolgt inkrementell bezogen auf die zuvor gesicherten Daten. Inkrementelle Sicherungen übertragen nur neue oder geänderte Daten, wodurch eine bessere Nutzung der Bandbreite zustande kommt.

Wenn Sie einen Sicherungsauftrag für eine Azure-VM abbrechen, werden die übertragenen Daten ignoriert. Beim nächsten Sicherungsauftrag werden die Daten inkrementell bezogen auf den letzten erfolgreichen Sicherungsauftrag übertragen.

## <a name="retention-and-recovery"></a>Aufbewahrung und Wiederherstellung

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Sind die Aufbewahrungsrichtlinien für DPM und Windows-Computer ohne DPM gleich?

Ja, für beide gelten tägliche, wöchentliche, monatliche und jährliche Aufbewahrungsrichtlinien.

### <a name="can-i-customize-retention-policies"></a>Kann ich Aufbewahrungsrichtlinien anpassen?

Ja, es gibt Anpassungsrichtlinien. Beispielsweise können Sie Anforderungen für wöchentliche und tägliche Aufbewahrung, jedoch nicht jährliche und monatliche, konfigurieren.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Kann ich unterschiedliche Zeiten für den Sicherungszeitplan und für Aufbewahrungsrichtlinien verwenden?

Nein. Aufbewahrungsrichtlinien können nur bei den Sicherungspunkten angewendet werden. Die Abbildung zeigt beispielsweise eine Aufbewahrungsrichtlinie für Sicherungen um 12:00 Uhr und 18:00 Uhr.

![Planen von Sicherung und Aufbewahrung](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Dauert die Wiederherstellung eines älteren Datenpunkts länger, wenn eine Sicherung lange Zeit aufbewahrt wird?

Nein. Die Dauer zum Wiederherstellen des ältesten oder neuesten Punkts ist gleich. Jeder Wiederherstellungspunkt verhält sich wie ein vollständiger Punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Wenn jeder Wiederherstellungspunkt sich wie ein vollständiger Punkt verhält, wie wirkt sich dies auf den gesamten abrechenbaren Sicherungsspeicher aus?

Bei typischen Produkten für die langfristige Aufbewahrung werden Sicherungsdaten als vollständige Punkte gespeichert.

- Die vollständigen Punkte sind im Hinblick auf den Speicher *ineffizient*, aber einfacher und schneller wiederherzustellen.
- Inkrementelle Kopien sind *speichereffizient*, jedoch muss eine Datenkette wiederhergestellt werden. Dies hat Auswirkungen auf die Wiederherstellungszeit.

Die Speicherarchitektur von Azure Backup bietet Ihnen die Vorteile beider Ansätze, indem die Daten optimal zur schnellen Wiederherstellung und zu geringen Speicherkosten gespeichert werden. Hierdurch wird sichergestellt, dass die Eingangs- und Ausgangsbandbreite effizient genutzt wird. So werden der Umfang der Datenspeicherung und die benötigte Zeit für die Wiederherstellung der Daten möglichst gering gehalten. Erfahren Sie mehr über [inkrementelle Sicherungen](backup-architecture.md#backup-types).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Gibt es eine Beschränkung für die Anzahl von Wiederherstellungspunkten, die erstellt werden können?

Pro geschützter Instanz können bis zu 9.999 Wiederherstellungspunkte erstellt werden. Geschützte Instanzen sind Computer, Server (physisch oder virtuell) oder Workloads, die in Azure gesichert werden.

- Erfahren Sie mehr über [Sicherung und Wiederherstellung](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Wie oft kann ich in Azure gesicherte Daten wiederherstellen?

Es gibt keine Beschränkung für die Anzahl der Wiederherstellungen aus Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Fallen für mich beim Wiederherstellen von Daten für den ausgehenden Datenverkehr von Azure Kosten an?

Nein. Die Wiederherstellung ist kostenlos, und der ausgehende Datenverkehr wird nicht in Rechnung gestellt.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Was passiert, wenn ich meine Sicherungsrichtlinie ändere?

Wenn eine neue Richtlinie angewendet wird, gelten der Zeitplan und die Aufbewahrungseinstellungen der neuen Richtlinie.

- Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert, um sie gemäß der neuen Richtlinie aufzubewahren.
- Bei einer Verkürzung der Aufbewahrung werden sie zum Löschen im Rahmen der nächsten Bereinigung markiert und demgemäß gelöscht.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Wie lange werden die Daten nach dem Beenden der Sicherung aufbewahrt, wenn die Option zur Aufbewahrung der Sicherungsdaten ausgewählt ist?

Wenn die Sicherungen beendet und die Daten aufbewahrt werden, enden die bestehenden Richtlinienregeln für die Datenbereinigung, und die Daten werden auf unbestimmte Zeit aufbewahrt, bis der Administrator die Löschung veranlasst.

## <a name="encryption"></a>Verschlüsselung

### <a name="is-the-data-sent-to-azure-encrypted"></a>Werden die Daten verschlüsselt an Azure gesendet?

Ja. Daten werden auf dem lokalen Computer mit AES256 verschlüsselt. Die Daten werden über eine sichere HTTPS-Verbindung übertragen. Die in der Cloud übertragenen Daten werden nur zwischen dem Speicher und dem Wiederherstellungsdienst durch eine HTTPS-Verbindung geschützt. Die zwischen dem Wiederherstellungsdienst und dem Computer des Benutzers übertragenen Daten werden durch das iSCSI-Protokoll gesichert. Der iSCSI-Kanal wird durch sicheres Tunneling geschützt.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Werden die Sicherungsdaten auf Azure ebenfalls verschlüsselt?

Ja. Die Daten in Azure sind im Ruhezustand verschlüsselt.

- Für die lokale Sicherung erfolgt eine Verschlüsselung im Ruhezustand über die beim Sichern in Azure bereitgestellte Passphrase.
- Für virtuelle Azure-Computer werden die Daten mit der Speicherdienstverschlüsselung (Storage Service Encryption, SSE) im Ruhezustand verschlüsselt.

Die Sicherungsdaten werden zu keinem Zeitpunkt von Microsoft entschlüsselt.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Wie lang muss der Verschlüsselungsschlüssel zur Verschlüsselung von Sicherungsdaten mindestens sein?

Der vom Microsoft Azure Recovery Services-Agent (MARS) verwendete Verschlüsselungsschlüssel wird von einer Passphrase abgeleitet, die mindestens 16 Zeichen lang sein sollte. Bei virtuellen Azure-Computern gibt es keine Längenbegrenzung für Schlüssel, die von Azure Key Vault verwendet werden.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Was geschieht, wenn ich den Schlüssel verlege? Kann ich die Daten wiederherstellen? Kann Microsoft die Daten wiederherstellen?

Der Schlüssel zum Verschlüsseln der Sicherungsdaten ist nur an Ihrem Standort vorhanden. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn Sie den Schlüssel verlegen, kann Microsoft die gesicherten Daten nicht wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die anderen häufig gestellten Fragen:

- [Gängige Fragen](backup-azure-vm-backup-faq.md) zu Azure VM-Sicherungen
- [Gängige Fragen](backup-azure-file-folder-backup-faq.md) zum Azure Backup-Agent