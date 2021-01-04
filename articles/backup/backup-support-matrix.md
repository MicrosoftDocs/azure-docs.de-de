---
title: Matrix zur Azure Backup-Unterstützung
description: Enthält eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen für den Azure Backup-Dienst.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: 70a81b42ad2fb417902a627afb1651d572844648
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548510"
---
# <a name="support-matrix-for-azure-backup"></a>Supportmatrix für Azure Backup

Sie können [Azure Backup](backup-overview.md) zum Sichern von Daten in der Microsoft Azure-Cloudplattform verwenden. Dieser Artikel enthält eine Zusammenfassung der allgemeinen Unterstützungseinstellungen und Einschränkungen für Azure Backup-Szenarios und -Bereitstellungen.

Andere Support-Matrizen sind verfügbar:

- Unterstützungsmatrix für die [Sicherung virtueller Azure-Computer](backup-support-matrix-iaas.md)
- Unterstützungsmatrix für die Sicherung mit [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Unterstützungsmatrix für die Sicherung mit dem [Microsoft Azure Recovery Services (MARS)-Agent](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Unterstützung für Tresore

Azure Backup verwendet Recovery Services-Tresore, um Sicherungen für die folgenden Workloadtypen zu orchestrieren und zu verwalten: Azure-VMs, SQL in Azure-VMs, SAP HANA in Azure-VMs, Azure-Dateifreigaben und lokale Workloads mit Azure Backup Agent, Azure Backup Server und System Center DPM. Außerdem werden die Recovery Services-Tresore verwendet, um gesicherte Daten für diese Workloads zu speichern.

In der folgenden Tabelle werden die Features von Recovery Services-Tresoren beschrieben:

**Feature** | **Details**
--- | ---
**Tresore im Abonnement** | Bis zu 500 Recovery Services-Tresore pro Abonnement.
**Computer in einem Tresor** | Bis zu 2.000 Datenquellen für alle Workloads (z. B. virtuelle Azure-Computer, SQL Server-VM, MABS-Server usw.) können in einem einzigen Tresor geschützt werden.<br><br>Bis zu 1.000 virtuelle Azure-Computer pro Tresor.<br/><br/> Bis zu 50 MABS-Server können in einem einzigen Tresor registriert werden.
**Datenquellen** | Die maximale Größe einer einzelnen [Datenquelle](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) ist 54.400 GB. Diese Beschränkung gilt nicht für Sicherungen von Azure-VMs. Es gilt keine Beschränkung der Datenmenge, die in einem Tresor gesichert werden kann.
**Sicherungen im Tresor** | **Virtuelle Azure-Computer:** Einmal täglich<br/><br/>**Per DPM/MABS geschützte Computer:** Zweimal pro Tag.<br/><br/> **Computer mit direkter Sicherung per MARS-Agent:** Dreimal pro Tag.
**Sicherungen zwischen Tresoren** | Die Sicherung erfolgt innerhalb einer Region.<br/><br/> Sie benötigen einen Tresor in jeder Azure-Region, die VMs enthält, die Sie sichern möchten. Eine Sicherung in eine andere Region ist nicht möglich.
**Verschieben von Tresoren** | Sie können Tresore zwischen Abonnements oder Ressourcengruppen innerhalb desselben Abonnements [verschieben](./backup-azure-move-recovery-services-vault.md). Das Verschieben von Tresoren zwischen Regionen wird hingegen nicht unterstützt.
**Daten zwischen Tresoren verschieben** | Das Verschieben von gesicherten Daten zwischen Tresoren wird nicht unterstützt.
**Tresorspeichertyp ändern** | Sie können den Speicherreplikationstyp für einen Tresor anpassen (entweder georedundanter Speicher oder lokal redundanter Speicher), bevor Sicherungen gespeichert werden. Nachdem Sicherungsvorgänge im Tresor begonnen haben, kann der Replikationstyp nicht mehr geändert werden.
**Zonenredundanter Speicher (ZRS)** | Verfügbar in den Regionen „Vereinigtes Königreich, Süden (UKS)“ und „Asien, Südosten (SEA)“

## <a name="on-premises-backup-support"></a>Unterstützung von lokalen Sicherungen

Im Folgenden werden die Informationen zur Unterstützung beim Sichern von lokalen Computern aufgeführt:

**Computer** | **Gesicherte Elemente** | **Location** | **Funktionen**
--- | --- | --- | ---
**Direkte Sicherung von Windows-Computer mit MARS-Agent** | Dateien, Ordner, Systemstatus | Sicherung im Recovery Services-Tresor | Drei Sicherungen pro Tag<br/><br/> Keine App-fähige Sicherung<br/><br/> Wiederherstellungsdatei, -ordner, -volume
**Direkte Sicherung von Linux-Computer mit MARS-Agent** | Sicherung wird nicht unterstützt
**Sicherung mit DPM** | Dateien, Ordner, Volumes, Systemstatus, App-Daten | Sichern im lokalen DPM-Speicher. Der DPM führt dann die Sicherung in einem Tresor durch. | App-fähige Momentaufnahmen<br/><br/> Vollständige Granularität für Sicherung und Wiederherstellung<br/><br/> Linux-Unterstützung für VMs (Hyper-V/VMware)<br/><br/> Oracle wird nicht unterstützt
**Sicherung mit MABS** | Dateien, Ordner, Volumes, Systemstatus, App-Daten | Sichern in den lokalen MABS-Speicher. MABS erstellt die Sicherung dann im Tresor. | App-fähige Momentaufnahmen<br/><br/> Vollständige Granularität für Sicherung und Wiederherstellung<br/><br/> Linux-Unterstützung für VMs (Hyper-V/VMware)<br/><br/> Oracle wird nicht unterstützt

## <a name="azure-vm-backup-support"></a>Support für die Azure-VM-Sicherung

### <a name="azure-vm-limits"></a>Azure-VM-Grenzwerte

**Begrenzung** | **Details**
--- | ---
**Azure-VM-Datenträger** | Weitere Informationen finden Sie in der [Unterstützungsmatrix für die Sicherung von Azure-VMs](./backup-support-matrix-iaas.md#vm-storage-support).
**Größe des Azure-VM-Datenträgers** | Die Größe einzelner Datenträger kann bis zu 32 TB und maximal 256 TB für alle Datenträger in einer VM betragen.

### <a name="azure-vm-backup-options"></a>Optionen für Azure-VM-Sicherungen

Im Folgenden werden die Informationen zur Unterstützung beim Sichern von Azure-VMs aufgeführt:

**Computer** | **Gesicherte Elemente** | **Location** | **Funktionen**
--- | --- | --- | ---
**Azure-VM-Sicherung mit VM-Erweiterung** | Gesamte VM | Sicherung in einem Tresor | Die Erweiterung ist installiert, wenn Sie die Sicherung für eine VM aktivieren.<br/><br/> Eine Sicherung pro Tag<br/><br/> App-fähige Sicherung für Windows-VMs, dateikonsistente Sicherung für Linux-VMs. Sie können die App-Konsistenz für Linux-Computer mit benutzerdefinierten Skripts konfigurieren.<br/><br/> Wiederherstellen von VMs oder Datenträgern<br/><br/> Die Sicherung einer Azure-VM an einem lokalen Speicherort ist nicht möglich
**Azure-VM-Sicherung mit MARS-Agent** | Dateien, Ordner, Systemstatus | Sicherung in einem Tresor | Drei Sicherungen pro Tag<br/><br/> Wenn Sie spezifische Dateien oder Ordner anstelle der gesamten VM sichern möchten, kann der MARS-Agent neben der VM-Erweiterung ausgeführt werden.
**Azure-VM mit DPM** | Dateien, Ordner, Volumes, Systemstatus, App-Daten | Die Sicherung erfolgt im lokalen Speicher der Azure-VM, auf der DPM ausgeführt wird. Der DPM führt dann die Sicherung in einem Tresor durch. | App-fähige Momentaufnahmen<br/><br/> Vollständige Granularität für Sicherung und Wiederherstellung.<br/><br/> Linux-Unterstützung für VMs (Hyper-V/VMware).<br/><br/> Oracle wird nicht unterstützt.
**Azure-VM mit MABS** | Dateien, Ordner, Volumes, Systemstatus, App-Daten | Die Sicherung erfolgt im lokalen Speicher der Azure-VM, auf der MABS ausgeführt wird. MABS erstellt die Sicherung dann im Tresor. | App-fähige Momentaufnahmen<br/><br/> Vollständige Granularität für Sicherung und Wiederherstellung.<br/><br/> Linux-Unterstützung für VMs (Hyper-V/VMware).<br/><br/> Oracle wird nicht unterstützt.

## <a name="linux-backup-support"></a>Unterstützung für Linux-Sicherungen

Im Folgenden werden die Informationen zur Unterstützung beim Sichern von Linux-Computern aufgeführt:

**Sicherungstyp** | **Linux (von Azure unterstützt)**
--- | ---
**Direkte Sicherung lokaler Linux-Computer** | Wird nicht unterstützt. Der MARS-Agent kann nur auf Windows-Computern installiert werden.
**Verwendung der Agent-Erweiterung zum Sichern von Azure-VMs, auf denen Linux ausgeführt wird** | App-konsistente Sicherung mit [benutzerdefinierten Skripts](backup-azure-linux-app-consistent.md)<br/><br/> Wiederherstellung auf Dateiebene.<br/><br/> Wiederherstellung, indem über einen Wiederherstellungspunkt oder Datenträger eine VM erstellt wird.
**Verwendung von DPM zum Sichern lokaler Computer unter Linux** | Dateikonsistente Sicherung von Linux-Gast-VMs unter Hyper-V und VMware<br/><br/> VM-Wiederherstellung von Hyper-V- und VMware-Linux-Gast-VMs
**Verwendung von MABS zum Sichern lokaler Computer unter Linux** | Dateikonsistente Sicherung von Linux-Gast-VMs unter Hyper-V und VMware<br/><br/> VM-Wiederherstellung von Hyper-V- und VMware-Linux-Gast-VMs
**Verwendung von MABS oder DPM zum Sichern virtueller Azure-Computer unter Linux** | Wird nicht unterstützt.

## <a name="daylight-saving-time-support"></a>Unterstützung für die Sommerzeit

Azure Backup unterstützt keine automatische Uhrzeitanpassung an die Sommerzeit für die Sicherungen von Azure-VMs. Die Sicherung wird also nicht entsprechend um eine Stunde verschoben. Um sicherzustellen, dass die Sicherung zur gewünschten Zeit erfolgt, müssen die Sicherungsrichtlinien bei Bedarf manuell angepasst werden.

## <a name="disk-deduplication-support"></a>Unterstützung für Datenträgerdeduplizierung

Unterstützung für die Datenträgerdeduplizierung:

- Die Datenträgerdeduplizierung wird lokal unterstützt, wenn Sie DPM oder MABS zum Sichern von Hyper-V-VMs verwenden, auf denen Windows ausgeführt wird. Windows Server führt die Datendeduplizierung (auf Hostebene) auf den virtuellen Festplatten (VHDs) durch, die als Sicherungsspeicher an die VM angefügt sind.
- Die Deduplizierung wird für Sicherungskomponenten in Azure nicht unterstützt. Wenn DPM und MABS in Azure bereitgestellt werden, können an die VM angefügte Speicherdatenträger nicht dedupliziert werden.

## <a name="security-and-encryption-support"></a>Unterstützung für Sicherheit und Verschlüsselung

Azure Backup unterstützt die Verschlüsselung für Daten während der Übertragung und im Ruhezustand.

### <a name="network-traffic-to-azure"></a>Netzwerkdatenverkehr für Azure

- Sicherungsdatenverkehr von den Servern zum Recovery Services-Tresor wird per AES 256 (Advanced Encryption Standard) verschlüsselt.
- Sicherungsdaten werden über eine sichere HTTPS-Verbindung übertragen.

### <a name="data-security"></a>Datensicherheit

- Sicherungsdaten werden im Recovery Services-Tresor in verschlüsselter Form gespeichert.
- Werden die Daten von lokalen Servern mit dem MARS-Agent gesichert, werden die Daten vor dem Hochladen nach Azure Backup mit einer Passphrase verschlüsselt und erst nach dem Herunterladen von Azure Backup entschlüsselt.
- Beim Sichern von Azure-VMs müssen Sie die Verschlüsselung *auf dem virtuellen Computer* einrichten.
- Azure Backup unterstützt den Azure Disk Encryption-Dienst, der auf virtuellen Windows-Computern BitLocker und auf virtuellen Linux-Computern **dm-crypt** verwendet.
- Auf dem Back-End nutzt Azure Backup die [Speicherdienstverschlüsselung von Azure](../storage/common/storage-service-encryption.md), mit der ruhende Daten geschützt werden.

**Computer** | **Während der Übertragung** | **Ruhende Daten**
--- | --- | ---
**Lokale Windows-Computer ohne DPM/MABS** | ![Ja][green] | ![Ja][green]
**Virtuelle Azure-Computer** | ![Ja][green] | ![Ja][green]
**Lokale Windows-Computer oder Azure-VMs mit DPM** | ![Ja][green] | ![Ja][green]
**Lokale Windows-Computer oder Azure-VMs mit MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Unterstützung für die Komprimierung

Azure Backup unterstützt die Komprimierung des Sicherungsdatenverkehrs. In der folgenden Tabelle finden Sie eine Zusammenfassung.

- Für Azure-VMs liest die VM-Erweiterung die Daten über das Speichernetzwerk direkt aus dem Azure-Speicherkonto, sodass es nicht erforderlich ist, diesen Datenverkehr zu komprimieren.
- Wenn Sie DPM oder MABS verwenden, können Sie Bandbreite sparen, indem Sie die Daten vor der Sicherung komprimieren.

**Computer** | **Komprimierung für MABS/DPM (TCP)** | **Komprimierung für Tresor (HTTPS)**
--- | --- | ---
**Direkte Sicherung lokaler Windows-Computer** | Nicht verfügbar | ![Ja][green]
**Sicherung von Azure-VMs mit der VM-Erweiterung** | Nicht verfügbar | Nicht verfügbar
**Sicherung von lokalen/Azure-Computern mit MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Grenzwerte für die Aufbewahrung

**Einstellung** | **Einschränkungen**
--- | ---
**Maximale Anzahl von Wiederherstellungspunkten pro geschützter Instanz (Computer oder Workload)** | 9\.999
**Maximale Ablaufzeit für einen Wiederherstellungspunkt** | Keine Begrenzung
**Maximale Sicherungshäufigkeit für DPM/MABS** | Alle 15 Minuten für SQL Server<br/><br/> Stündlich für andere Workloads
**Maximale Sicherungshäufigkeit für Tresor** | **Lokale Windows-Computer oder Azure-VMs mit dem MARS-Agent:** Dreimal pro Tag<br/><br/> **DPM/MABS:** Zweimal pro Tag<br/><br/> **Azure VM-Sicherung:** Einmal pro Tag
**Aufbewahrungszeitraum des Wiederherstellungspunkts** | Täglich, wöchentlich, monatlich, jährlich
**Maximaler Aufbewahrungszeitraum** | Abhängig von der Sicherungshäufigkeit
**Wiederherstellungspunkte auf DPM-/MABS-Datenträger** | 64 für Dateiserver, 448 für App-Server <br/><br/>Unbegrenzte Bandwiederherstellungspunkte für lokale Computer mit DPM

## <a name="cross-region-restore"></a>Regionsübergreifende Wiederherstellung

In Azure Backup wurde ein Feature zur regionsübergreifenden Wiederherstellung hinzugefügt, um die Datenverfügbarkeit und Resilienz zu verbessern. Damit erhalten Sie die vollständige Kontrolle und können Daten in einer sekundären Region wiederherstellen. Weitere Informationen zu diesem Feature finden Sie im Artikel [Festlegen der regionsübergreifenden Wiederherstellung](backup-create-rs-vault.md#set-cross-region-restore). Dieses Feature wird für die folgenden Verwaltungstypen unterstützt:

| Sicherungsverwaltungstyp | Unterstützt                                                    | Unterstützte Regionen |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Ja.   Wird für verschlüsselte VMs und VMs mit Datenträgern mit weniger als 4 TB unterstützt | Alle öffentlichen Azure-Regionen  |
| SQL/SAP HANA | Ja                                                          | Alle öffentlichen Regionen außer Frankreich |
| MARS-Agent/lokal  | Nein                                                           | N/V               |
| AFS (Azure-Dateifreigaben)                 | Nein                                                           | –               |

## <a name="next-steps"></a>Nächste Schritte

- [Schauen Sie sich die Unterstützungsmatrix](backup-support-matrix-iaas.md) für die Sicherung von Azure-VMs an.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
