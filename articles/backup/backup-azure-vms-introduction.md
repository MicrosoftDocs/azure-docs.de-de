---
title: Informationen zur Sicherung von Azure-VMs
description: Erfahren Sie mehr über die Sicherung von Azure-VMs und einige bewährte Methoden.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 93be913182db56941c346ef0cad47f70c0d614c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706838"
---
# <a name="about-azure-vm-backup"></a>Informationen zur Sicherung von Azure-VMs

In diesem Artikel wird beschrieben, wie Azure-VMs (Virtual Machines) mit dem Dienst [Azure Backup](backup-introduction-to-azure-backup.md) gesichert werden.

## <a name="backup-process"></a>Sicherungsprozess

Hier erfahren Sie, wie Azure Backup eine Sicherung für Azure-VMs durchführt:

1. Für Azure-VMs, die zur Sicherung ausgewählt sind, startet Azure Backup einen Sicherungsauftrag gemäß dem von Ihnen angegebenen Sicherungszeitplan.
1. Während der ersten Sicherung wird auf dem virtuellen Computer eine Sicherungserweiterung installiert, wenn die VM ausgeführt wird.
    - Für virtuelle Windows-Computer wird die Erweiterung _VMSnapshot_ installiert.
    - Für virtuelle Linux-Computer wird die Erweiterung _VMSnapshotLinux_ installiert.
1. Bei ausgeführten Windows-VMs erstellt Azure Backup in Koordination mit dem Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS) von Windows eine App-konsistente Momentaufnahme des virtuellen Computers.
    - Backup erstellt standardmäßig vollständige VSS-Sicherungen.
    - Wenn Backup keine App-konsistente Momentaufnahme erstellen kann, wird eine dateikonsistente Momentaufnahme des zugrunde liegenden Speichers erstellt (weil keine Schreibvorgänge der Anwendung stattfinden, solange die VM beendet ist).
1. Für virtuelle Linux-Computer erstellt Backup eine dateikonsistente Sicherung. Zur Erstellung App-konsistenter Momentaufnahmen müssen Sie Pre- und Postskripts manuell anpassen.
1. Nachdem Backup die Momentaufnahme erstellt hat, werden die Daten in den Tresor übertragen.
    - Zur Optimierung der Sicherung werden die einzelnen VM-Datenträger parallel gesichert.
    - Für jeden Datenträger, der gesichert wird, liest Azure Backup die Blöcke auf dem Datenträger und identifiziert und überträgt nur die Datenblöcke, die sich seit der vorherigen Sicherung geändert haben (das Delta).
    - Momentaufnahmedaten werden möglicherweise nicht sofort in den Tresor kopiert. Zu Spitzenzeiten vergehen unter Umständen mehrere Stunden. Bei täglichen Sicherungsrichtlinien beträgt die Gesamtdauer der Sicherung eines virtuellen Computers weniger als 24 Stunden.
 1. An einem virtuellen Windows-Computer werden folgende Änderungen vorgenommen, nachdem Azure Backup darauf aktiviert wurde:
    -   Microsoft Visual C++ 2013 Redistributable(x64) – 12.0.40660 wird auf dem virtuellen Computer installiert.
    -   Der Starttyp des Volumeschattenkopie-Diensts (Volume Shadow Copy Service, VSS) wird von „automatisch“ in „manuell“ geändert.
    -   Der IaaSVmProvider-Windows-Dienst wird hinzugefügt.

1. Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt.

![Architektur der Sicherung von virtuellen Azure-Computern](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Verschlüsseln von Azure-VM-Sicherungen

Wenn Sie Azure-VMs mit Azure Backup sichern, werden ruhende VMs mit der Speicherdienstverschlüsselung (SSE) verschlüsselt. Azure Backup kann auch virtuelle Azure-Computer sichern, die mit Azure Disk Encryption verschlüsselt wurden.

**Verschlüsselung** | **Details** | **Unterstützung**
--- | --- | ---
**Azure-Datenträgerverschlüsselung** | Azure Disk Encryption verschlüsselt sowohl Betriebssystemdatenträger als auch sonstige Datenträger für Azure-VMs.<br/><br/> Azure Disk Encryption kann in BitLocker-Verschlüsselungsschlüssel (BEK) integriert werden, die in einem Schlüsseltresor als Geheimnisse geschützt werden. Azure Disk Encryption kann auch in Azure Key Vault-Schlüssel für die Schlüsselverschlüsselung (Key Encryption Keys, KEK) integriert werden. | Azure Backup unterstützt die Sicherung verwalteter und nicht verwalteter Azure-VMs, die nur mit BEKs oder sowohl mit BEKs als auch mit KEKs verschlüsselt sind.<br/><br/> Sowohl BEKs als auch KEKs werden gesichert und verschlüsselt.<br/><br/> Da KEKs und BEKs gesichert werden, können Benutzer mit den erforderlichen Berechtigungen bei Bedarf Schlüssel und Geheimnisse im Schlüsseltresor wiederherstellen. Diese Benutzer können auch die verschlüsselte VM wiederherstellen.<br/><br/> Verschlüsselte Schlüssel und Geheimnisse können von nicht dafür autorisierten Benutzern oder von Azure nicht gelesen werden.
**SSE** | Mit SSE bietet Azure Storage eine Verschlüsselung im Ruhezustand, indem Daten vor der Speicherung automatisch verschlüsselt werden. Azure Storage entschlüsselt auch Daten vor dem Abrufen. | Azure Backup verwendet SSE zur Verschlüsselung ruhender Azure-VMs.

Backup unterstützt die Sicherung verwalteter und nicht verwalteter Azure-VMs, die nur mit BEKs oder sowohl mit BEKs als auch mit KEKs verschlüsselt sind.

Die gesicherten BEKs (Geheimnisse) und KEKs (Schlüssel) werden verschlüsselt. Sie können nur gelesen und verwendet werden, wenn sie von autorisierten Benutzern im Schlüsseltresor wiederhergestellt werden. Weder nicht autorisierte Benutzer noch Azure können gesicherte Schlüssel oder Geheimnisse lesen oder verwenden.

BEKs werden auch gesichert. Wenn die BEKs verloren gehen, können autorisierte Benutzer die BEKs im Schlüsseltresor wiederherstellen und so auch die verschlüsselten VMs wiederherstellen. Nur Benutzer mit den entsprechenden Berechtigungen können verschlüsselte VMs oder Schlüssel und Geheimnisse sichern und wiederherstellen.

## <a name="snapshot-creation"></a>Erstellung der Momentaufnahme

Azure Backup erstellt Momentaufnahmen gemäß des Sicherungszeitplans.

- **Virtuelle Windows-Computer:** Für virtuelle Windows-Computer erstellt der Backup-Dienst in Zusammenarbeit mit VSS eine App-konsistente Momentaufnahme der VM-Datenträger.

  - Standardmäßig führt Azure Backup vollständig VSS-Sicherungen durch. [Weitere Informationen](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
  - Um die Einstellung so zu ändern, dass Azure Backup Sicherungen von VSS-Kopien erstellt, legen Sie über eine Eingabeaufforderung den folgenden Registrierungsschlüssel fest:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.

- **Virtuelle Linux-Computer:** Um App-konsistente Momentaufnahmen der Linux-VMs zu erstellen, verwenden Sie das Linux-Framework für vorher und nachher auszuführende Skripts (Pre- und Post-Skripts), um Ihre eigenen benutzerdefinierten Skripts zu schreiben und Konsistenz zu gewährleisten.

  - Azure Backup ruft nur die von Ihnen geschriebenen Pre- und Post-Skripts auf.
  - Wenn die Pre-Skripts und Post-Skripts erfolgreich ausgeführt werden, markiert Azure Backup den Wiederherstellungspunkt als anwendungskonsistent. Allerdings sind Sie bei Verwendung benutzerdefinierter Skripts letztendlich für die Anwendungskonsistenz verantwortlich.
  - [Erfahren Sie mehr](backup-azure-linux-app-consistent.md) über die Konfiguration von Skripts.

### <a name="snapshot-consistency"></a>Konsistenz von Momentaufnahmen

In der folgenden Tabelle werden die verschiedenen Typen der Konsistenz von Momentaufnahmen erläutert:

**Momentaufnahme** | **Details** | **Wiederherstellung**: | **Aspekt**
--- | --- | --- | ---
**Anwendungskonsistent** | Anwendungskonsistente Sicherungen erfassen Speicherinhalte und ausstehende E/A-Vorgänge. App-konsistente Momentaufnahmen verwenden einen VSS Writer (oder Pre-/Post-Skripts für Linux), um die Konsistenz der App-Daten zu gewährleisten, ehe eine Sicherung erfolgt. | Wenn Sie die Wiederherstellung einer VM mit einer anwendungskonsistenten Momentaufnahme durchführen, wird die VM hochgefahren. Es kommt weder zu einer Beschädigung noch zum Verlust von Daten. Die Apps starten in einem konsistenten Zustand. | Windows: Alle VSS-Writer wurden erfolgreich abgeschlossen.<br/><br/> Linux: Pre-/Post-Skripts sind konfiguriert und erfolgreich abgeschlossen.
**Dateisystemkonsistent** | Dateisystemkonsistente Sicherungen bieten Konsistenz, indem eine Momentaufnahme aller Dateien gleichzeitig erstellt wird.<br/><br/> | Wenn Sie die Wiederherstellung einer VM mit einer dateisystemkonsistenten Momentaufnahme durchführen, wird die VM hochgefahren. Es kommt weder zu einer Beschädigung noch zum Verlust von Daten. Apps müssen einen eigenen Reparaturmechanismus implementieren, um sicherzustellen, dass wiederhergestellte Daten konsistent sind. | Windows: Fehler bei einigen VSS-Writern <br/><br/> Linux: Standardmäßig (wenn Pre-/Post-Skripts nicht konfiguriert oder bei ihnen Fehler aufgetreten sind)
**Absturzkonsistent** | Absturzkonsistente Momentaufnahmen treten in der Regel auf, wenn eine Azure-VM zum Zeitpunkt der Sicherung heruntergefahren wird. Nur die Daten, die zum Zeitpunkt der Sicherung bereits auf dem Datenträger vorhanden sind, werden erfasst und gesichert.<br/><br/> Ein absturzkonsistenter Wiederherstellungspunkt garantiert keine Datenkonsistenz für das Betriebssystem oder die App. | Es gibt keine Garantien, aber normalerweise wird die VM hochgefahren. Anschließend erfolgt eine Datenträgerprüfung, um Beschädigungen zu beheben. Alle Daten im Arbeitsspeicher oder Schreibvorgänge, die vor dem Absturz nicht vollständig auf den Datenträger übertragen wurden, gehen verloren. Apps implementieren ihre eigene Datenüberprüfung. Beispielsweise kann eine Datenbank-App das Transaktionsprotokoll für die Überprüfung verwenden. Falls das Transaktionsprotokoll Einträge enthält, die nicht in der Datenbank vorhanden sind, führt die Datenbanksoftware ein Transaktionenrollback durch, bis die Daten konsistent sind. | VM wird heruntergefahren.

## <a name="backup-and-restore-considerations"></a>Überlegungen zu Sicherung und Wiederherstellung

**Aspekt** | **Details**
--- | ---
**Datenträger** | Die Sicherung der VM-Datenträger erfolgt parallel. Bei einem virtuellen Computer mit vier Datenträgern versucht der Backup-Dienst beispielsweise, alle vier Datenträger parallel zu sichern. Die Sicherung erfolgt inkrementell (nur geänderte Daten).
**Zeitplanung** |  Um den Sicherungsdatenverkehr zu reduzieren, sichern Sie verschiedene virtuelle Computer zu unterschiedlichen Tageszeiten, und achten Sie darauf, dass keine Überschneidungen auftreten. Wenn Sie VMs gleichzeitig sichern, entstehen Engpässe im Datenverkehr.
**Vorbereiten von Sicherungen** | Berücksichtigen Sie den zur Vorbereitung der Sicherung erforderlichen Zeitaufwand. Zur Vorbereitungszeit gehören das Installieren oder Aktualisieren der Sicherungserweiterung sowie das Auslösen einer Momentaufnahme in Übereinstimmung mit dem Sicherungszeitplan.
**Datenübertragung** | Beachten Sie, wieviel Zeit Azure Backup benötigt, um die inkrementellen Änderungen aus der vorherigen Sicherung zu berechnen.<br/><br/> Bei einer inkrementellen Sicherung berechnet Azure Backup die Prüfsumme des Blocks, um die Änderungen zu ermitteln. Wenn ein Block geändert wird, wird er für die Übertragung in den Tresor gekennzeichnet. Der Dienst analysiert die identifizierten Blöcke und versucht, die Menge der zu übertragenden Daten weiter zu minimieren. Nach dem Evaluieren aller geänderten Blöcke überträgt Azure Backup die Änderungen in den Tresor.<br/><br/> Zwischen der Momentaufnahme und dem Kopiervorgang in den Tresor kann es zu einer Verzögerung kommen.<br/><br/> Zu Spitzenzeiten kann es bis zu acht Stunden dauern, bis Sicherungen verarbeitet werden. Bei der täglichen Sicherung beträgt die Sicherungsdauer eines virtuellen Computers weniger als 24 Stunden.
**Erste Sicherung** | Die Gesamtdauer der Sicherung von weniger als 24 Stunden gilt für inkrementelle Sicherungen, aber möglicherweise nicht für die erste Sicherung. Wieviel Zeit für die erste Sicherung benötigt wird, hängt von der Datenmenge und dem Zeitpunkt der Sicherungsausführung ab.
**Wiederherstellen der Warteschlange** | Azure Backup verarbeitet Wiederherstellungsaufträge von mehreren Speicherkonten gleichzeitig, und Wiederherstellungsanforderungen werden in eine Warteschlange eingereiht.
**Wiederherstellen einer Kopie** | Beim Wiederherstellungsvorgang werden Daten zuerst aus dem Tresor in das Speicherkonto kopiert.<br/><br/> Die gesamte Wiederherstellungszeit hängt von den E/A-Vorgängen pro Sekunde (IOPS) und dem Durchsatz des Speicherkontos ab.<br/><br/> Wählen Sie ein Speicherkonto aus, das nicht mit den Schreib- und Lesevorgängen anderer Anwendungen geladen ist, um die Zeit für das Kopieren zu verringern.

### <a name="backup-performance"></a>Backupleistung

Diese gängigen Szenarien können die gesamte Sicherungsdauer beeinflussen:

- **Hinzufügen eines neuen Datenträgers zu einer geschützten Azure-VM:** Wenn eine inkrementelle Sicherung einer VM durchgeführt und ein neuer Datenträger hinzugefügt wird, erhöht dies die Dauer der Sicherung. Die Gesamtdauer der Sicherung könnte aufgrund der ersten Replikation des neuen Datenträgers und der Deltareplikation vorhandener Datenträger mehr als 24 Stunden betragen.
- **Fragmentierte Datenträger:** Sicherungsvorgänge sind schneller, wenn Datenträgeränderungen zusammenhängend sind. Wenn die Änderungen über einen Datenträger verteilt und fragmentiert werden, verläuft die Sicherung langsamer.
- **Datenträgeränderungen:** Wenn geschützte Datenträger, für die eine inkrementelle Sicherung durchgeführt wird, täglich eine Änderungsrate von mehr als 200GB aufweisen, kann der Sicherungsvorgang eine lange Zeit (mehr als acht Stunden) in Anspruch nehmen.
- **Backup-Versionen:** In der neuesten Version von Backup (namens „Instant Restore“) wird ein besser optimierter Prozess als der Prüfsummenvergleich zum Identifizieren der Änderungen verwendet. Aber wenn Sie Instant Restore verwenden und eine Sicherungsmomentaufnahme gelöscht haben, geht die Sicherung wieder zum Prüfsummenvergleich über. In diesem Fall wird der Sicherungsvorgang 24 Stunden überschreiten (oder mit einem Fehler abgebrochen).

## <a name="best-practices"></a>Bewährte Methoden

Berücksichtigen Sie beim Konfigurieren von VM-Sicherungen die folgenden bewährten Methoden:

- Ändern Sie die in einer Richtlinie festgelegten Standardzeiten des Zeitplans. Wenn die Standardzeit in der Richtlinie beispielsweise auf 0:00 Uhr festgelegt ist, setzen Sie sie um einigen Minuten herauf, damit die Ressourcen optimal verwendet werden.
- Für die Sicherung von virtuellen Computern, die Storage Premium verwenden, sollten Sie die neueste Version von Azure Backup ([Instant Restore](backup-instant-restore-capability.md)) verwenden. Wenn Sie nicht die neueste Version ausführen, belegt Backup ungefähr 50 Prozent des gesamten Speicherplatzes. Der Backup-Dienst benötigt diesen Speicherplatz, um die Momentaufnahme in dasselbe Speicherkonto zu kopieren und in den Tresor zu übertragen.
- Wenn Sie VMs in einem einzigen Tresor wiederherstellen, sollten Sie unbedingt verschiedene [„Allgemein v2“-Speicherkonten](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) verwenden, um sicherzustellen, dass das Zielspeicherkonto nicht gedrosselt wird. Beispielsweise muss jeder virtuelle Computer über ein anderes Speicherkonto verfügen. Wenn also 10 virtuelle Computer wiederhergestellt werden, verwenden Sie 10 verschiedene Speicherkonten.
- Die Wiederherstellungen von einer „Allgemein v1“-Speicherebene (Momentaufnahme) sind in wenigen Minuten abgeschlossen, da sich die Momentaufnahme auf demselben Speicherkonto befindet. Wiederherstellungen von der „Allgemein v2“-Speicherebene (Tresor) können Stunden dauern. In Fällen, in denen die Daten im „Allgemein v1“-Speicher verfügbar sind, sollten Sie das Feature [Instant Restore](backup-instant-restore-capability.md) für schnellere Wiederherstellungen verwenden. (Wenn die Daten in einem Tresor wiederhergestellt werden müssen, dauert es länger.)
- Die maximale Anzahl von Datenträgern pro Speicherkonto hängt davon ab, wie stark Anwendungen, die auf einer IaaS-VM (Infrastructure-as-a-Service) ausgeführt werden, auf den Datenträger zugreifen. Wenn 5 bis 10 Datenträger oder mehr in einem einzelnen Speicherkonto vorhanden sind, sollten Sie grundsätzlich die Last ausgleichen, indem Sie einige Datenträger in getrennte Speicherkonten verschieben.

## <a name="backup-costs"></a>Sicherungskosten

Für Azure-VMs, die mit Azure Backup gesichert werden, gelten [Azure Backup-Preise](https://azure.microsoft.com/pricing/details/backup/).

Die Abrechnung erfolgt erst nach dem erfolgreichen Abschluss der ersten Sicherung. Zu diesem Zeitpunkt beginnt die Abrechnung für den Speicher und die geschützten VMs. Die Abrechnung erfolgt so lange, wie Sicherungsdaten für den virtuellen Computer in einem Tresor gespeichert werden. Wenn Sie den Schutz für einen virtuellen Computer beenden, aber Sicherungsdaten für den virtuellen Computer in einem Tresor vorhanden sind, wird die Abrechnung fortgesetzt.

Die Abrechnung für eine VM endet erst, nachdem der Schutz beendet wurde und alle Sicherungsdaten gelöscht wurden. Wenn der Schutz beendet wird und es keine aktiven Sicherungsaufträge gibt, wird die Größe der letzten erfolgreichen VM-Sicherung als Größe der geschützten Instanz für die monatliche Rechnung verwendet.

Die Berechnung der Größe der geschützten Instanz basiert auf der *tatsächlichen* Größe des virtuellen Computers. Die VM-Größe ist die Summe aller Daten auf dem virtuellen Computer mit Ausnahme des temporären Speichers. Die Preise basieren auf den tatsächlich auf den Datenträgern gespeicherten Daten, nicht auf der maximal unterstützten Größe für jeden Datenträger, der dem virtuellen Computer angefügt ist.

Gleichermaßen basiert die Abrechnung der Sicherungsspeicherung auf der Menge der in Azure Backup gespeicherten Daten, d.h. auf der Summe der tatsächlichen Daten an jedem Wiederherstellungspunkt.

Beispiel: Sie verwenden einen virtuellen Computer der Größe „A2 Standard“ sowie zwei zusätzliche Datenträger mit einer maximalen Größe von jeweils 4TB. In der folgenden Tabelle sind die auf jedem Datenträger tatsächlich gespeicherten Daten aufgeführt:

**Datenträger** | **Max. Größe** | **Tatsächliche vorhandene Daten**
--- | --- | ---
Betriebssystem-Datenträger | 4095 GB | 17 GB
Lokaler/temporärer Datenträger | 135 GB | 5 GB (bei der Sicherung nicht enthalten)
Datenträger 1 | 4095 GB | 30 GB
Datenträger 2 | 4095 GB | 0 GB

Die tatsächliche Größe der VM beträgt in diesem Fall 17 GB + 30 GB + 0 GB = 47 GB. Diese Größe der geschützten Instanz (47GB) dient als Basis für die monatliche Rechnung. Mit zunehmender Datenmenge auf dem virtuellen Computer ändert sich entsprechend auch die Größe der geschützten Instanz, die für die Abrechnung verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

[Bereiten Sie jetzt die Azure-VM-Sicherung vor](backup-azure-arm-vms-prepare.md).
