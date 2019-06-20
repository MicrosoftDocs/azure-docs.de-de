---
title: Häufig gestellte Fragen zum Sichern von Dateien und Ordnern mit Azure Backup
description: Hierin geht es um häufig gestellte Fragen zum Sichern von Dateien und Ordnern mit Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: dacurwin
ms.openlocfilehash: 56dc87b1cdf36d761c46133004a05f8fa225a091
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808299"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Häufig gestellte Fragen zum Sichern von Dateien und Ordnern 

Dieser Artikel enthält Antworten auf häufige Fragen zur Sicherung von Dateien und Ordnern mit dem Microsoft Azure Recovery Services (MARS)-Agent im [Azure Backup](backup-overview.md)-Dienst.

## <a name="general"></a>Allgemein

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>Warum benötigt der MARS-Agent .NET Framework 4.5.2 oder höher?

Eine neue Funktionalität der [Sofortigen Wiederherstellung](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) benötigt .NET Framework 4.5.2 oder höher.

## <a name="configure-backups"></a>Konfigurieren von Sicherungen

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Wo kann ich die neueste Version des MARS-Agents herunterladen? 
Der neueste MARS-Agent, der beim Sichern von Windows Server-Computern, System Center DPM und Microsoft Azure Backup-Server verwendet wird, steht zum [Herunterladen](https://aka.ms/azurebackup_agent) zur Verfügung. 

### <a name="how-long-are-vault-credentials-valid"></a>Wie lange gelten Tresoranmeldeinformationen?
Tresoranmeldeinformationen laufen nach 48 Stunden ab. Wenn die Datei mit den Anmeldeinformationen abläuft, laden Sie die Datei erneut vom Azure-Portal herunter.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Von welchen Laufwerken kann ich Dateien und Ordner sichern? 

Die folgenden Typen von Laufwerken und Volumes können nicht gesichert werden:

* Wechselmedien: Alle Quellen für Sicherungselemente müssen als festes Medium gemeldet werden.
* Schreibgeschützte Volumes: Das Volume muss beschreibbar sein, damit der Volumeschattenkopie-Dienst (VSS) funktioniert.
* Offlinevolumes: Das Volume muss online sein, damit der VSS funktioniert.
* Netzwerkfreigaben: Das Volume muss sich lokal auf dem Server befinden, damit es mit der Onlinesicherung gesichert werden kann.
* Mit BitLocker geschützte Volumes: Das Volume muss entsperrt werden, damit die Sicherung erfolgen kann.
* Dateisystemidentifikation: NTFS ist das einzige unterstützte Dateisystem.

### <a name="what-file-and-folder-types-are-supported"></a>Welche Datei- und Ordnertypen werden unterstützt?

Die folgenden Typen werden unterstützt:

* Verschlüsselt
* Komprimiert
* Platzsparend
* Komprimiert und geringe Dichte
* Feste Links: Nicht unterstützt, wird übersprungen
* Analysepunkt: Nicht unterstützt, wird übersprungen
* Verschlüsselt und geringe Dichte: Nicht unterstützt, wird übersprungen
* Komprimierter Datenstrom: Nicht unterstützt, wird übersprungen
* Analysepunkte, einschließlich DFS-Links und Verknüpfungspunkte


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kann ich mit dem MARS-Agent Dateien und Ordner auf einer Azure-VM sichern?  
Ja. Azure Backup ermöglicht die Sicherung auf VM-Ebene für Azure VMs mit der VM-Erweiterung für den Azure-VM-Agent. Wenn Sie Dateien und Ordner auf dem Gast-Windows-Betriebssystem auf der VM sichern möchten, können Sie zu diesem Zweck den MARS-Agent installieren. 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kann ich mit dem MARS-Agent Dateien und Ordner auf einem temporären Speicher sichern? 
Ja. Installieren Sie den MARS-Agent, und sichern Sie Dateien und Ordner auf dem Gast-Windows-Betriebssystem auf einem temporären Speicher. - Sicherungen können nicht erfolgreich durchgeführt werden, wenn Daten aus dem temporären Speicher entfernt werden.
- Wenn die temporären Speicherdaten gelöscht wurden, ist nur die Wiederherstellung in einem nicht volatilen Speicher möglich.

### <a name="how-do-i-register-a-server-to-another-region"></a>Wie registriere ich einen Server in einer anderen Region?

Die Sicherungsdaten werden an das Rechenzentrum des Tresors gesendet, bei dem der Server registriert ist. Der einfachste Weg, das Rechenzentrum zu ändern, besteht darin, den Agent zu deinstallieren und neu zu installieren und den Computer dann in einem neuen Tresor in der gewünschten Region zu registrieren.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Unterstützt der MARS-Agent die Deduplizierung von Windows Server 2012?
Ja. Der MARS-Agent konvertiert die deduplizierten Daten bei der Vorbereitung des Sicherungsvorgangs in normale Daten. Anschließend optimiert er die Daten für die Sicherung, verschlüsselt sie und sendet die verschlüsselten Daten an den Tresor.

## <a name="manage-backups"></a>Verwalten von Sicherungen

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Was passiert, wenn ich einen für die Sicherung konfigurierten Windows-Computer umbenenne?

Wenn Sie einen Windows-Computer umbenennen, werden alle derzeit konfigurierten Sicherungen angehalten.

- Sie müssen den neuen Namen des Computers beim Sicherungstresor registrieren.
- Wenn Sie den neuen Namen beim Tresor registrieren, dann ist der erste Vorgang eine *vollständige* Sicherung.
- Wenn Sie Daten wiederherstellen müssen, die mit dem alten Servernamen im Tresor gesichert wurden, verwenden Sie die Option zur Wiederherstellung an einem alternativen Speicherort im Assistenten für die Datenwiederherstellung. [Weitere Informationen](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Wie lang ist die maximale Dateipfadlänge für die Sicherung?
Der MARS-Agent basiert auf NTFS und verwendet die durch die [Windows-API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths) begrenzte Dateipfadlänge. Wenn die Dateien, die Sie sichern möchten, länger als der zulässige Wert sind, sichern Sie den übergeordneten Ordner oder das Laufwerk.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Welche Zeichen sind in Dateipfaden zulässig?

Der MARS-Agent basiert auf NTFS und erlaubt [unterstützte Zeichen](/windows/desktop/FileIO/naming-a-file#naming-conventions) in Dateinamen/Pfaden.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Die Warnung „Azure-Sicherungen wurden für diesen Server nicht konfiguriert“ wird angezeigt.
Diese Warnung tritt auf, wenn die auf dem lokalen Server gespeicherten Sicherungszeitplaneinstellungen nicht den Einstellungen im Sicherungstresor entsprechen, auch wenn Sie eine Sicherungsrichtlinie konfiguriert haben.
- Wenn der Server oder die Einstellungen in einen bekannt guten Zustand wiederhergestellt wurden, kann unter Umständen die Synchronisierung der Sicherungszeitpläne verloren gehen.
- Wenn Sie diese Warnung erhalten, [konfigurieren](backup-azure-manage-windows-server.md) Sie die Sicherungsrichtlinie erneut, und führen Sie dann eine On-Demand-Sicherung durch, um den lokalen Server mit Azure neu zu synchronisieren.


## <a name="manage-the-backup-cache-folder"></a>Verwalten des Cacheordners für die Sicherung

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Welche Mindestgröße gilt für den Cacheordner?
Die Größe des Cacheordners bestimmt die Menge der Daten, die Sie sichern.
- Die Cacheordnervolumes sollten freien Speicherplatz haben, der mindestens 5-10 % der Gesamtgröße der Sicherungsdaten ausmacht.
- Wenn weniger als 5-10 % Speicherplatz zur Verfügung stehen, vergrößern Sie das Volume, oder verschieben Sie den Cacheordner auf ein Volume mit ausreichend freiem Speicherplatz.
- Wenn Sie den Windows-Systemstatus sichern, benötigen Sie zusätzlich 30-35 GB freien Speicherplatz auf dem Volume, das den Cacheordner enthält.
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Wie ändere ich den Cachespeicherort für den MARS-Agent?


1. Führen Sie diesen Befehl in der Eingabeaufforderung mit erhöhten Rechten aus, um die Sicherungs-Engine anzuhalten:

    ```PS C:\> Net stop obengine```

2. Verschieben Sie nicht die Dateien! Kopieren Sie den Cacheordner stattdessen auf ein anderes Laufwerk mit ausreichend Speicherplatz.
3. Aktualisieren Sie die folgenden Registrierungseinträge mit dem Pfad zum neuen Cacheordner.<br/>

    | Registrierungspfad | Registrierungsschlüssel | Wert |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Neuer Speicherort des Cacheordners* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Neuer Speicherort des Cacheordners* |

4. Starten Sie die Sicherungs-Engine in einer Eingabeaufforderung mit erhöhten Rechten neu:

    ```PS C:\> Net start obengine```

5. Nachdem die Sicherung am neuen Speicherort erfolgreich abgeschlossen wurde, können Sie den ursprünglichen Cacheordner entfernen.


### <a name="where-should-the-cache-folder-be-located"></a>Wo sollte der Cacheordner gespeichert werden?

Folgende Speicherorte werden für den Cacheordner nicht empfohlen:

* Netzwerkfreigabe/Wechselmedium: Bei dem Cacheordner muss es sich um einen lokalen Ordner des Servers handeln, der mittels Onlinesicherung gesichert werden soll. Netzwerkspeicherorte oder Wechselmedien wie USB-Laufwerke werden nicht unterstützt
* Offlinevolumes: Der Cacheordner muss für die erwartete Sicherung über den Azure Backup-Agent online sein.

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Gibt es Cacheordnerattribute, die nicht unterstützt werden?
Die folgenden Attribute oder Kombinationen dieser Attribute werden für den Cacheordner nicht unterstützt:

* Verschlüsselt
* Dedupliziert
* Komprimiert
* Platzsparend
* Analysepunkt

Der Cacheordner und die Metadaten-VHD verfügen nicht über die erforderlichen Attribute für den Azure Backup-Agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Gibt es eine Möglichkeit, die für die Sicherung genutzte Bandbreite anzupassen?
 
Ja, Sie können die Option **Eigenschaften ändern** im MARS-Agent verwenden, um die Bandbreite und den Zeitpunkt anzupassen. [Weitere Informationen](backup-configure-vault.md#enable-network-throttling)**.

## <a name="restore"></a>Restore

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Was geschieht, wenn ich einen laufenden Wiederherstellungsauftrag abbreche?

Wenn ein laufender Wiederherstellungsauftrag abgebrochen wird, wird der Wiederherstellungsvorgang beendet. Alle vor dem Abbruch wiederhergestellten Dateien verbleiben ohne Rollbacks am konfigurierten Ziel (ursprünglicher oder alternativer Speicherort).


## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](tutorial-backup-windows-server-to-azure.md) zum Sichern eines Windows-Computers.
