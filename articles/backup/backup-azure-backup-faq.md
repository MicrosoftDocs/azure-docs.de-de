---
title: Azure Backup – Häufig gestellte Fragen
description: 'Antworten auf häufig gestellte Fragen zu: Azure Backup-Features wie Azure Recovery Services-Tresoren, zu sicherbaren Elementen sowie zu Funktionsweise, Verschlüsselung und Grenzwerten. '
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 479edc11604670189b0accba67aa8f345f6260cd
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551379"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – häufig gestellte Fragen
In diesem Artikel werden allgemeine Fragen um Azure Backup-Dienst beantwortet.

## <a name="recovery-services-vault"></a>Recovery Services-Tresor

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Gibt es eine Beschränkung hinsichtlich der Anzahl von Tresoren, die in einem Azure-Abonnement erstellt werden können?
Ja. Pro Abonnement können für jede unterstützte Region von Azure Backup bis zu 500 Recovery Services-Tresore erstellt werden. Erstellen Sie ein zusätzliches Abonnement, falls Sie zusätzliche Tresore benötigen.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Gibt es Beschränkungen im Hinblick auf die Anzahl von Servern/Computern, die pro Tresor registriert werden können?
Sie können bis zu 1.000 virtuelle Azure-Computer pro Tresor registrieren. Bei Verwendung des Microsoft Azure Backup-Agents können Sie pro Tresor bis zu 50 MAB-Agents registrieren. Sie können 50 MAB-Server/DPM-Server bei einem Tresor registrieren.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Wie kann ich Daten von unterschiedlichen Servern im Tresor isolieren, wenn meine Organisation über nur einen Tresor verfügt?
Serverdaten, die Sie zusammen wiederherstellen möchten, sollten beim Einrichten der Sicherung die gleiche Passphrase verwenden. Wenn Sie die Wiederherstellung auf bestimmten Servern isolieren möchten, verwenden Sie eine Passphrase nur für diese Server. So können Sie beispielsweise für die Server der Personalabteilung, für die Server der Buchhaltung und für die Speicherserver jeweils eine eigene Verschlüsselungspassphrase verwenden.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kann ich meinen Tresor zwischen Abonnements verschieben?
Ja. Informationen zum Verschieben eines Recovery Services-Tresors finden Sie in [diesem Artikel](backup-azure-move-recovery-services-vault.md).

### <a name="can-i-move-backup-data-to-another-vault"></a>Kann ich Sicherungsdaten zu einem anderen Tresor verschieben?
Nein. In einem Tresor gespeicherte Sicherungsdaten können nicht in einen anderen Tresor verschoben werden.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Kann ich nach einer Sicherung von GRS zu LRS wechseln?
Nein. Die Speicheroptionen eines Recovery Services-Tresors können nur geändert werden, bevor Sicherungen gespeichert wurden.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kann ich für virtuelle Computer, die in einem Recovery Services-Tresor gesichert wurden, eine Wiederherstellung auf Elementebene durchführen?
- Die Wiederherstellung auf Elementebene wird für virtuelle Azure-Computer unterstützt, die von der Azure-VM-Sicherung gesichert wurden. Weitere Informationen finden Sie in [diesem Artikel](backup-azure-restore-files-from-vm.md).
- Die Wiederherstellung auf Elementebene wird für Online-Wiederherstellungspunkte lokaler virtueller Computer, die von Azure Backup Server oder System Center DPM gesichert wurden, nicht unterstützt.


## <a name="azure-backup-agent"></a>Azure Backup-Agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Wo finde ich verbreitete Fragen zum Azure Backup-Agent für die Azure-VM-Sicherung?

- Informationen zu den auf virtuellen Azure-Computern ausgeführten Agents finden Sie in diesen [häufig gestellten Fragen](backup-azure-vm-backup-faq.md).
- Informationen zu den Agents für das Sichern von Azure-Dateiordnern finden Sie in diesen [häufig gestellten Fragen](backup-azure-file-folder-backup-faq.md).


## <a name="vmware-and-hyper-v-backup"></a>VMware- und Hyper-V-Sicherung

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kann ich VMware vCenter-Server in Azure sichern?
Ja. Mit Azure Backup Server können Sie VMware vCenter-Server und ESXi-Hosts in Azure sichern.

- [Erfahren Sie mehr](backup-mabs-protection-matrix.md) über unterstützte Versionen.
- [Gehen Sie folgendermaßen vor](backup-azure-backup-server-vmware.md), um einen VMware-Server zu sichern.

### <a name="do-i-need-a-separate-license-to-recover-an-full-on-premises-vmwarehyper-v-cluster"></a>Benötige ich eine separate Lizenz, um einen vollständigen lokalen VMware-/Hyper-V-Cluster wiederherzustellen?
Sie benötigen für den VMware-/Hyper-V-Schutz keine separate Lizenzierung.

- Wenn Sie ein System Center-Kunde sind, verwenden Sie System Center Data Protection Manager (DPM) zum Schützen von VMware-VMs.
- Falls Sie kein System Center-Kunde sind, können Sie Azure Backup Server (nutzungsbasierte Bezahlung) verwenden, um VMware-VMs zu schützen.

## <a name="dpm-and-azure-backup-server-backup"></a>DPM und Azure Backup Server-Sicherungen

### <a name="which-dpm-versions-are-supported"></a>Welche DPM-Versionen werden unterstützt?
Die unterstützten DPM-Versionen sind in der [Unterstützungsmatrix](backup-azure-dpm-introduction.md#prerequisites-and-limitations) zusammengefasst. Es wird empfohlen, die neuesten DPM-Updates zu installieren und die [neueste Version](https://aka.ms/azurebackup_agent) des Azure Backup-Agents auf dem DPM-Server auszuführen.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Kann ich den Server für mehrere Tresore registrieren?
Nein. Ein DPM- oder Azure Backup-Server kann nur für einen Tresor registriert werden.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Kann ich Azure Backup Server verwenden, um eine Bare Metal Recovery-Sicherung (BMR) für einen physischen Server zu erstellen? <br/>
Ja.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kann ich mit DPM Apps in Azure Stack sichern?
Nein. Sie können Azure Stack mit Azure Backup schützen, das Sichern von Apps in Azure Stack mit DPM wird von Azure Backup jedoch nicht unterstützt.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Kann ich System Center DPM zum Sichern von lokalen Workloads in Azure installieren, wenn ich den Azure Backup-Agent zum Schutz meiner Dateien und Ordner installiert habe?
Ja. Sie sollten jedoch zuerst DPM einrichten und dann den Azure Backup-Agent installieren.  Das Installieren der Komponenten in dieser Reihenfolge stellt sicher, dass der Azure Backup-Agent mit DPM verwendet werden kann. Das Installieren des Agents vor DPM wird nicht empfohlen oder unterstützt.

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

Für Sicherungen von virtuellen Azure-Computern mit Linux unterstützt Azure Backup [die Liste der von Azure unterstützten Distributionen](../virtual-machines/linux/endorsed-distros.md), mit Ausnahme von CoreOS Linux und 32-Bit-Betriebssystem. Andere Bring-Your-Own-Linux-Distributionen sollten funktionieren, sofern der VM-Agent auf dem virtuellen Computer verfügbar ist und Python unterstützt wird.


### <a name="are-there-size-limits-for-data-backup"></a>Gibt es Größenbeschränkungen für die Datensicherung?
Die folgenden Größengrenzwerte gelten:

Betriebssystem/Computer | Größengrenzwert der Datenquelle
--- | --- 
Windows 8 oder höher | 54.400 GB
Windows 7 |1.700 GB
Windows Server 2012 oder höher | 54.400 GB
Windows Server 2008, Windows Server 2008 R2 | 1.700 GB
Azure-VM | 16 Datenträger<br/><br/> Datenträger bis 4.095 GB

### <a name="how-is-the-data-source-size-determined"></a>Wie wird die Größe der Datenquelle bestimmt?
In der folgenden Tabelle wird beschrieben, wie die einzelnen Datenquellengrößen bestimmt werden.

**Datenquelle** | **Details**
--- | ---
Volume |Die Datenmenge, die von einem virtuellen Computer mit einem Volume gesichert wird.
SQL Server-Datenbank |Größe einer einzelnen zu sichernden SQL-Datenbank
SharePoint | Summe der Inhalts- und Konfigurationsdatenbanken in einer zu sichernden SharePoint-Farm
Exchange |Summe aller Exchange-Datenbanken eines zu sichernden Exchange-Servers
BMR/Systemstatus |Jede einzelne Kopie der BMR oder des Systemstatus des zu sichernden Computers

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Gibt es einen Grenzwert für die Menge der in einem Recovery Services-Tresor gesicherten Daten?
Es gibt keine Beschränkung für die Menge der Daten, die in einem Recovery Services-Tresor gesichert werden können.

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


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Dauert die Wiederherstellung eines älteren Datenpunkts länger, wenn eine Sicherung lange Zeit aufbewahrt wird? <br/>
 Nein. Die Dauer zum Wiederherstellen des ältesten oder neuesten Punkts ist gleich. Jeder Wiederherstellungspunkt verhält sich wie ein vollständiger Punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Wenn jeder Wiederherstellungspunkt sich wie ein vollständiger Punkt verhält, wie wirkt sich dies auf den gesamten abrechenbaren Sicherungsspeicher aus?
Bei typischen Produkten für die langfristige Aufbewahrung werden Sicherungsdaten als vollständige Punkte gespeichert.

- Die vollständigen Punkte sind im Hinblick auf den Speicher *ineffizient*, aber einfacher und schneller wiederherzustellen.
- Inkrementelle Kopien sind *speichereffizient*, jedoch muss eine Datenkette wiederhergestellt werden. Dies hat Auswirkungen auf die Wiederherstellungszeit.

Die Speicherarchitektur von Azure Backup bietet Ihnen die Vorteile beider Ansätze, indem die Daten optimal zur schnellen Wiederherstellung und zu geringen Speicherkosten gespeichert werden. Hierdurch wird sichergestellt, dass die Eingangs- und Ausgangsbandbreite effizient genutzt wird. So werden der Umfang der Datenspeicherung und die benötigte Zeit für die Wiederherstellung der Daten möglichst gering gehalten. Erfahren Sie mehr über [inkrementelle Sicherungen](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Gibt es eine Beschränkung für die Anzahl von Wiederherstellungspunkten, die erstellt werden können?
Pro geschützter Instanz können bis zu 9.999 Wiederherstellungspunkte erstellt werden. Geschützte Instanzen sind Computer, Server (physisch oder virtuell) oder Workloads, die in Azure gesichert werden.

- Erfahren Sie mehr über [Sicherung und Wiederherstellung](./backup-introduction-to-azure-backup.md#backup-and-retention).
- Erfahren Sie etwas über [geschützte Instanzen](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance).

### <a name="how-many-times-can-i-recovery-data-thats-backed-up-to-azure"></a>Wie oft kann ich in Azure gesicherte Daten wiederherstellen?
Es gibt keine Beschränkung für die Anzahl der Wiederherstellungen aus Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Fallen für mich beim Wiederherstellen von Daten für den ausgehenden Datenverkehr von Azure Kosten an?
Nein. Die Wiederherstellung ist kostenlos, und der ausgehende Datenverkehr wird nicht in Rechnung gestellt.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Was passiert, wenn ich meine Sicherungsrichtlinie ändere?
Wenn eine neue Richtlinie angewendet wird, gelten der Zeitplan und die Aufbewahrungseinstellungen der neuen Richtlinie.

- Bei einer Ausweitung der Aufbewahrung werden bereits vorhandene Wiederherstellungspunkte markiert, um sie gemäß der neuen Richtlinie aufzubewahren.
- Bei einer Verkürzung der Aufbewahrung werden sie zum Löschen im Rahmen der nächsten Bereinigung markiert und demgemäß gelöscht.

## <a name="encryption"></a>Verschlüsselung

### <a name="is-the-data-sent-to-azure-encrypted"></a>Werden die Daten verschlüsselt an Azure gesendet?
Ja. Daten werden auf dem lokalen Computer mit AES256 verschlüsselt. Die Daten werden über eine sichere HTTPS-Verbindung übertragen. Die in der Cloud übertragenen Daten werden nur zwischen dem Speicher und dem Wiederherstellungsdienst durch eine HTTPS-Verbindung geschützt. Die zwischen dem Wiederherstellungsdienst und dem Computer des Benutzers übertragenen Daten werden durch das iSCSI-Protokoll gesichert. Der iSCSI-Kanal wird durch sicheres Tunneling geschützt.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Werden die Sicherungsdaten auf Azure ebenfalls verschlüsselt?
Ja. Die Daten in Azure sind im Ruhezustand verschlüsselt.

- Für die lokale Sicherung erfolgt eine Verschlüsselung im Ruhezustand über die beim Sichern in Azure bereitgestellte Passphrase.
- Für virtuelle Azure-Computer werden die Daten mit der Speicherdienstverschlüsselung (Storage Service Encryption, SSE) im Ruhezustand verschlüsselt.

Die Sicherungsdaten werden zu keinem Zeitpunkt von Microsoft entschlüsselt.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Wie lang muss der Verschlüsselungsschlüssel zur Verschlüsselung von Sicherungsdaten mindestens sein?
Bei Verwendung des Azure Backup-Agents muss der Verschlüsselungsschlüssel mindestens 16 Zeichen lang sein. Bei virtuellen Azure-Computern gibt es keine Längenbegrenzung für Schlüssel, die von Azure Key Vault verwendet werden.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Was geschieht, wenn ich den Schlüssel verlege? Kann ich die Daten wiederherstellen? Kann Microsoft die Daten wiederherstellen?
Der Schlüssel zum Verschlüsseln der Sicherungsdaten ist nur an Ihrem Standort vorhanden. Microsoft behält keine Kopie in Azure und hat keinen Zugriff auf den Schlüssel. Wenn Sie den Schlüssel verlegen, kann Microsoft die gesicherten Daten nicht wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die anderen häufig gestellten Fragen:

- [Gängige Fragen](backup-azure-vm-backup-faq.md) zu Azure VM-Sicherungen
- [Gängige Fragen](backup-azure-file-folder-backup-faq.md) zum Azure Backup-Agent
