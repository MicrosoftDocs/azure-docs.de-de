---
title: Microsoft Azure Recovery Services-Agent (MARS) – häufig gestellte Fragen
description: Hierin geht es um häufig gestellte Fragen zum Sichern von Dateien und Ordnern mit Azure Backup.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 1edfaed99e60409774496c5ae75df8be99a8fe1f
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917357"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>Häufig gestellte Fragen – Microsoft Azure Recovery Services-Agent (MARS)

Dieser Artikel beantwortet häufige Fragen zur Sicherung von Daten mit dem Microsoft Azure Recovery Services-Agent (MARS) im [Azure Backup](backup-overview.md)-Dienst.

## <a name="configure-backups"></a>Konfigurieren von Sicherungen

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Wo kann ich die neueste Version des MARS-Agents herunterladen?

Der neueste MARS-Agent, der beim Sichern von Windows Server-Computern, System Center DPM und Microsoft Azure Backup-Server verwendet wird, steht zum [Herunterladen](https://aka.ms/azurebackup_agent) zur Verfügung.

### <a name="where-can-i-download-the-vault-credentials-file"></a>Wo kann ich die Datei mit Tresoranmeldeinformationen herunterladen?

Navigieren Sie im Azure-Portal zu **Eigenschaften** für Ihre VM. Aktivieren Sie unter **Sicherungsanmeldeinformationen** das Kontrollkästchen für **Der neueste Recovery Services-Agent wird bereits verwendet**. Wählen Sie **Herunterladen** aus.

![Anmeldeinformationen herunterladen](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>Wie lange gelten Tresoranmeldeinformationen?

Tresoranmeldeinformationen laufen nach 10 Tagen ab. Wenn die Datei mit den Anmeldeinformationen abläuft, laden Sie die Datei erneut vom Azure-Portal herunter.

### <a name="what-characters-are-allowed-for-the-passphrase"></a>Welche Zeichen sind für die Passphrase zulässig?

Die Passphrase muss Zeichen aus dem ASCII-Zeichensatz mit [ASCII-Werten kleiner als oder gleich 127](/office/vba/language/reference/user-interface-help/character-set-0127) enthalten.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Von welchen Laufwerken kann ich Dateien und Ordner sichern?

Die folgenden Typen von Laufwerken und Volumes können nicht gesichert werden:

* Wechselmedien: Alle Quellen für Sicherungselemente müssen als festes Medium gemeldet werden.
* Schreibgeschützte Volumes: Das Volume muss beschreibbar sein, damit der Volumeschattenkopie-Dienst (VSS) funktioniert.
* Offlinevolumes: Das Volume muss online sein, damit der VSS funktioniert.
* Netzwerkfreigaben: Das Volume muss sich lokal auf dem Server befinden, damit es mit der Onlinesicherung gesichert werden kann.
* Mit BitLocker geschützte Volumes: Das Volume muss entsperrt werden, damit die Sicherung erfolgen kann.
* Dateisystemidentifikation: NTFS ist das einzige unterstützte Dateisystem.

### <a name="what-file-and-folder-types-are-supported"></a>Welche Datei- und Ordnertypen werden unterstützt?

[Erfahren Sie mehr](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) über die Typen von Dateien und Ordnern, die für die Sicherung unterstützt werden.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Kann ich mit dem MARS-Agent Dateien und Ordner auf einer Azure-VM sichern?  

Ja. Azure Backup ermöglicht die Sicherung auf VM-Ebene für Azure VMs mit der VM-Erweiterung für den Azure-VM-Agent. Wenn Sie Dateien und Ordner auf dem Gast-Windows-Betriebssystem auf der VM sichern möchten, können Sie zu diesem Zweck den MARS-Agent installieren.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Kann ich mit dem MARS-Agent Dateien und Ordner auf einem temporären Speicher sichern?

Ja. Installieren Sie den MARS-Agent, und sichern Sie Dateien und Ordner auf dem Gast-Windows-Betriebssystem auf einem temporären Speicher.

* Sicherungen können nicht erfolgreich durchgeführt werden, wenn Daten aus dem temporären Speicher entfernt werden.
* Wenn die temporären Speicherdaten gelöscht wurden, ist nur die Wiederherstellung in einem nicht volatilen Speicher möglich.

### <a name="how-do-i-register-a-server-to-another-region"></a>Wie registriere ich einen Server in einer anderen Region?

Die Sicherungsdaten werden an das Rechenzentrum des Tresors gesendet, bei dem der Server registriert ist. Der einfachste Weg, das Rechenzentrum zu ändern, besteht darin, den Agent zu deinstallieren und neu zu installieren und den Computer dann in einem neuen Tresor in der gewünschten Region zu registrieren.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Unterstützt der MARS-Agent die Deduplizierung von Windows Server 2012?

Ja. Der MARS-Agent konvertiert die deduplizierten Daten bei der Vorbereitung des Sicherungsvorgangs in normale Daten. Anschließend optimiert er die Daten für die Sicherung, verschlüsselt sie und sendet die verschlüsselten Daten an den Tresor.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>Benötige ich Administratorberechtigungen zum Installieren und Konfigurieren des MARS-Agents?

Ja, die Installation des MARS-Agents und die Konfiguration von Sicherungen mithilfe der MARS-Konsole erfordern, dass der Benutzer ein lokaler Administrator auf dem geschützten Server ist.

### <a name="what-is-the-impact-on-mars-agent-backups-of-transferring-the-vault-subscription-to-a-different-azure-ad-directory"></a>Welche Auswirkung hat das Übertragen des Tresorabonnements in ein anderes Azure AD-Verzeichnis auf MARS-Agent-Sicherungen?

Die Änderung des Azure AD-Verzeichnisses hat keine Auswirkung auf MARS-Agent-Sicherungen. 

## <a name="manage-backups"></a>Verwalten von Sicherungen

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Was passiert, wenn ich einen für die Sicherung konfigurierten Windows-Computer umbenenne?

Wenn Sie einen Windows-Computer umbenennen, werden alle derzeit konfigurierten Sicherungen angehalten.

* Sie müssen den neuen Namen des Computers beim Sicherungstresor registrieren.
* Wenn Sie den neuen Namen beim Tresor registrieren, dann ist der erste Vorgang eine *vollständige* Sicherung.
* Wenn Sie Daten wiederherstellen müssen, die mit dem alten Servernamen im Tresor gesichert wurden, verwenden Sie die Option zur Wiederherstellung an einem alternativen Speicherort im Assistenten für die Datenwiederherstellung. [Weitere Informationen](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Wie lang ist die maximale Dateipfadlänge für die Sicherung?

Der MARS-Agent basiert auf NTFS und verwendet die durch die [Windows-API](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths) begrenzte Dateipfadlänge. Wenn die Dateien, die Sie sichern möchten, länger als der zulässige Wert sind, sichern Sie den übergeordneten Ordner oder das Laufwerk.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Welche Zeichen sind in Dateipfaden zulässig?

Der MARS-Agent basiert auf NTFS und erlaubt [unterstützte Zeichen](/windows/win32/FileIO/naming-a-file#naming-conventions) in Dateinamen/Pfaden.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Die Warnung „Azure-Sicherungen wurden für diesen Server nicht konfiguriert“ wird angezeigt.

Diese Warnung tritt auf, wenn die auf dem lokalen Server gespeicherten Sicherungszeitplaneinstellungen nicht den Einstellungen im Sicherungstresor entsprechen, auch wenn Sie eine Sicherungsrichtlinie konfiguriert haben.

* Wenn der Server oder die Einstellungen in einen bekannt guten Zustand wiederhergestellt wurden, kann unter Umständen die Synchronisierung der Sicherungszeitpläne verloren gehen.
* Wenn Sie diese Warnung erhalten, [konfigurieren](backup-azure-manage-windows-server.md) Sie die Sicherungsrichtlinie erneut, und führen Sie dann eine On-Demand-Sicherung durch, um den lokalen Server mit Azure neu zu synchronisieren.

## <a name="manage-the-backup-cache-folder"></a>Verwalten des Cacheordners für die Sicherung

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Welche Mindestgröße gilt für den Cacheordner?

Die Größe des Cacheordners bestimmt die Menge der Daten, die Sie sichern.

* Die Cacheordnervolumes sollten freien Speicherplatz haben, der mindestens 5-10 % der Gesamtgröße der Sicherungsdaten ausmacht.
* Wenn weniger als 5 % Speicherplatz zur Verfügung stehen, vergrößern Sie das Volume, oder verschieben Sie den Cacheordner mit [diesen Schritten](#how-do-i-change-the-cache-location-for-the-mars-agent) auf ein Volume mit ausreichend freiem Speicherplatz.
* Wenn Sie den Windows-Systemstatus sichern, benötigen Sie zusätzlich 30 bis 35 GB freien Speicherplatz auf dem Volume, das den Cacheordner enthält.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Wie überprüfe ich, ob der Ordner „scratch“ gültig ist und darauf zugegriffen werden kann?

1. Standardmäßig befindet sich der Ordner „scratch“ unter `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`.
2. Stellen Sie sicher, dass der Pfad des Speicherorts für den Ordner „scatch“ mit den Werten der folgenden Registrierungsschlüsseleinträge übereinstimmt:

    | Registrierungspfad | Registrierungsschlüssel | Wert |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Neuer Speicherort des Cacheordners* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Neuer Speicherort des Cacheordners* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Wie ändere ich den Cachespeicherort für den MARS-Agent?

1. Führen Sie diesen Befehl in der Eingabeaufforderung mit erhöhten Rechten aus, um die Sicherungs-Engine anzuhalten:

    ```Net stop obengine```
2. Wenn Sie die Systemstatussicherung konfiguriert haben, öffnen Sie die Datenträgerverwaltung, und heben Sie die Einbindung der Datenträger mit Namen im Format `"CBSSBVol_<ID>"` auf.
3. Standardmäßig befindet sich der Ablageordner unter `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`.
4. Kopieren Sie den gesamten `\Scratch`-Ordner auf ein anderes Laufwerk mit ausreichend Speicherplatz. Stellen Sie sicher, dass die Inhalte kopiert, nicht verschoben werden.
5. Aktualisieren Sie die folgenden Registrierungseinträge mit dem Pfad zum neu verschobenen Ablageordner.

    | Registrierungspfad | Registrierungsschlüssel | Wert |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Neuer Speicherort für den Ablageordner* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Neuer Speicherort für den Ablageordner* |

6. Starten Sie die Sicherungs-Engine in einer Eingabeaufforderung mit erhöhten Rechten neu:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Ausführen einer bedarfsgesteuerten Sicherung Nachdem die Sicherung am neuen Speicherort erfolgreich abgeschlossen wurde, können Sie den ursprünglichen Cacheordner entfernen.

### <a name="where-should-the-cache-folder-be-located"></a>Wo sollte der Cacheordner gespeichert werden?

Folgende Speicherorte werden für den Cacheordner nicht empfohlen:

* Netzwerkfreigabe/Wechselmedium: Bei dem Cacheordner muss es sich um einen lokalen Ordner des Servers handeln, der mittels Onlinesicherung gesichert werden soll. Netzwerkspeicherorte oder Wechselmedien wie USB-Laufwerke werden nicht unterstützt.
* Offlinevolumes: Der Cacheordner muss für die erwartete Sicherung über den Azure Backup-Agent online sein.

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Gibt es Cacheordnerattribute, die nicht unterstützt werden?

Die folgenden Attribute oder Kombinationen dieser Attribute werden für den Cacheordner nicht unterstützt:

* Verschlüsselt
* Dedupliziert
* Compressed
* Platzsparend
* Analysepunkt

Der Cacheordner und die Metadaten-VHD verfügen nicht über die erforderlichen Attribute für den Azure Backup-Agent.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Gibt es eine Möglichkeit, die für die Sicherung genutzte Bandbreite anzupassen?

Ja, Sie können die Option **Eigenschaften ändern** im MARS-Agent verwenden, um die Bandbreite und den Zeitpunkt anzupassen. [Weitere Informationen](backup-windows-with-mars-agent.md#enable-network-throttling)

## <a name="restore"></a>Restore

### <a name="manage"></a>Verwalten

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>Kann ich eine Wiederherstellung vornehmen, wenn ich meine Passphrase vergessen habe?

Der Azure Backup-Agent benötigt eine Passphrase (die Sie bei der Registrierung angegeben haben), um die gesicherten Daten während der Wiederherstellung zu entschlüsseln. Überprüfen Sie die folgenden Szenarien, um Ihre Möglichkeiten bei einer verlorenen Passphrase zu verstehen:

| Ursprünglicher Computer <br> *(Quellcomputer, auf dem Sicherungen erstellt wurden)* | Passphrase | Verfügbare Optionen |
| --- | --- | --- |
| Verfügbar |Verloren |Wenn Ihr ursprünglicher Computer (auf dem die Sicherungen erstellt wurden) verfügbar ist und noch bei demselben Recovery Services-Tresor registriert ist, können Sie die Passphrase mithilfe der folgenden [Schritte](./backup-azure-manage-mars.md#re-generate-passphrase) erneut generieren.  |
| Verloren |Verloren |Die Wiederherstellung der Daten ist nicht möglich, oder die Daten sind nicht verfügbar. |

Unter den folgenden Bedingungen ziehen Sie Folgendes in Betracht:

* Wenn Sie den Agent deinstallieren und erneut auf dem gleichen ursprünglichen Computer registrieren, und zwar mit
  * *der gleichen Passphrase*, dann können Sie Ihre gesicherten Daten wiederherstellen.
  * *einer anderen Passphrase*, dann können Sie Ihre gesicherten Daten nicht wiederherstellen.
* Wenn Sie den Agent auf einem *anderen Computer* installieren, und zwar mit
  * der *gleichen Passphrase* (die auf dem ursprünglichen Computer verwendet wurde), dann können Sie Ihre gesicherten Daten wiederherstellen.
  * *einer anderen Passphrase*, dann können Sie Ihre gesicherten Daten nicht wiederherstellen.
* Wenn der ursprüngliche Computer beschädigt ist (Sie also die Passphrase nicht über die MARS-Konsole neu generieren können), Sie den ursprünglichen, vom MARS-Agent verwendeten Ablageordner aber wiederherstellen bzw. darauf zugreifen können, ist die Wiederherstellung ggf. möglich, wenn Sie das Kennwort vergessen haben. Weitere Unterstützung erhalten Sie vom Kundensupport.

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>Wie kann ich die Daten wiederherstellen, wenn ich mein Originalcomputer (auf dem die Sicherungen erstellt wurden) verloren habe?

Wenn Sie über die gleiche Passphrase (die Sie bei der Registrierung angegeben haben) auf dem ursprünglichen Computer verfügen, können Sie die gesicherten Daten auf einem anderen Computer wiederherstellen. Überprüfen Sie die folgenden Szenarien, um Ihre Wiederherstellungsoptionen zu verstehen.

| Ursprünglicher Computer | Passphrase | Verfügbare Optionen |
| --- | --- | --- |
| Verloren |Verfügbar |Sie können den MARS-Agent auf einem anderen Computer mit der gleichen Passphrase, die Sie bei der Registrierung des ursprünglichen Computers angegeben haben, installieren und registrieren. Wählen Sie **Wiederherstellungsoption** > **Anderer Speicherort** aus, um die Wiederherstellung auszuführen. Weitere Informationen finden Sie in [diesem Artikel](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Verloren |Verloren |Die Wiederherstellung der Daten ist nicht möglich, oder die Daten sind nicht verfügbar. |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>Meine Sicherungsaufträge schlagen fehl oder werden schon lange nicht mehr ausgeführt. Der Aufbewahrungszeitraum ist überschritten. Kann ich dennoch eine Wiederherstellung ausführen?

Als Sicherheitsmaßnahme bewahrt Azure Backup den jüngsten Wiederherstellungspunkt auf, auch wenn die Aufbewahrungsfrist überschritten ist. Sobald die Sicherungen wieder aufgenommen werden und neue Wiederherstellungspunkte verfügbar sind, wird der ältere Wiederherstellungspunkt entsprechend der festgelegten Aufbewahrung entfernt.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Was geschieht, wenn ich einen laufenden Wiederherstellungsauftrag abbreche?

Wenn ein laufender Wiederherstellungsauftrag abgebrochen wird, wird der Wiederherstellungsvorgang beendet. Alle vor dem Abbruch wiederhergestellten Dateien verbleiben ohne Rollbacks am konfigurierten Ziel (ursprünglicher oder alternativer Speicherort).

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>Werden die für Dateien, Ordner und Volumes festgelegten ACLs vom MARS-Agent gesichert und wieder hergestellt?

* Die für Dateien, Ordner und Volumes festgelegten ACLs werden vom MARS-Agent gesichert.
* Für die Wiederherstellungsoption „Volumewiederherstellung“ bietet der MARS-Agent eine Option zum Überpringen der Wiederherstellung der ACL-Berechtigungen für die wiederherzustellenden Dateien/Ordner.
* Für die Wiederherstellungsoption für einzelne Dateien und Ordner führt der MARS-Agent die Wiederherstellung mit ACL-Berechtigungen aus (es gibt keine Option zum Überspringen der ACL-Wiederherstellung).

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](tutorial-backup-windows-server-to-azure.md) zum Sichern eines Windows-Computers.
