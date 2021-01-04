---
title: Behandeln von Problemen mit dem Azure Backup-Agent
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit der Installation und Registrierung des Azure Backup-Agents behandeln.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 4ae4142652d9d38d5bf384e5a10d6eeb7e3cc608
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993838"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Problembehandlung beim Microsoft Azure Recovery Services-Agent (MARS)

In diesem Artikel erfahren Sie, wie Sie möglicherweise auftretende Fehler bei Konfiguration, Registrierung, Sicherung und Wiederherstellung beheben.

## <a name="basic-troubleshooting"></a>Grundlegendes zur Problembehandlung

Wir empfehlen, die folgenden Prüfungen durchzuführen, bevor Sie mit der Problembehandlung beim Microsoft Azure Recovery Services-Agent (MARS) beginnen:

- [Stellen Sie sicher, dass der MARS-Agent aktuell](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Stellen Sie sicher, dass zwischen dem MARS-Agent und Azure eine Netzwerkverbindung besteht](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Stellen Sie sicher, dass MARS ausgeführt wird (in der Dienstkonsole). Führen Sie bei Bedarf einen Neustart durch, und wiederholen Sie den Vorgang.
- [Stellen Sie sicher, dass am Speicherort des Ablageordners 5-10 % freier Volumespeicherplatz vorhanden ist](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Prüfen Sie, ob Azure Backup von einem anderen Prozess oder einer Antivirensoftware beeinträchtigt wird](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Wenn der Sicherungsauftrag mit Warnungen abgeschlossen wurde, lesen Sie den Abschnitt [Mit Warnungen abgeschlossene Sicherungsaufträge](#backup-jobs-completed-with-warning).
- Wenn geplante Sicherungen fehlschlagen, manuelle Sicherungen jedoch funktionieren, lesen Sie den Abschnitt [Sicherungen werden nicht nach Zeitplan ausgeführt](#backups-dont-run-according-to-schedule).
- Stellen Sie sicher, dass Ihr Betriebssystem über die neuesten Updates verfügt.
- [Stellen Sie sicher, dass nicht unterstützte Laufwerke und Dateien mit nicht unterstützten Attributen von der Sicherung ausgeschlossen werden](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Vergewissern Sie sich, dass die Uhr des geschützten Systems für die richtige Zeitzone konfiguriert ist.
- [Stellen Sie sicher, dass .NET Framework 4.5.2 oder höher auf dem Server installiert ist](https://www.microsoft.com/download/details.aspx?id=30653).
- Wenn Sie Ihren Server erneut bei einem Tresor registrieren möchten:
  - Stellen Sie sicher, dass der Agent auf dem Server deinstalliert und aus dem Portal gelöscht wird.
  - Verwenden Sie dieselbe Passphrase, die ursprünglich zum Registrieren des Servers verwendet wurde.
- Bei Offlinesicherungen müssen Sie sicherstellen, dass Azure PowerShell 3.7.0 auf dem Quellcomputer und dem Zielcomputer für die Sicherungskopie installiert ist, bevor Sie die Sicherung starten.
- Wenn der Backup-Agent auf einem virtuellen Azure-Computer ausgeführt wird, lesen Sie [diesen Artikel](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Es wurden ungültige Tresoranmeldeinformationen angegeben.

**Fehlermeldung**: Es wurden ungültige Tresoranmeldeinformationen angegeben. Die Datei ist entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen für den Wiederherstellungsdienst. (ID: 34513)

| Ursache | Empfohlene Aktionen |
| ---     | ---    |
| **Die Tresor-Anmeldeinformationen sind ungültig** <br/> <br/> Dateien mit Tresoranmeldeinformationen können beschädigt oder abgelaufen sein, oder weisen vielleicht eine andere Dateierweiterung als *.vaultCredentials* auf. (Beispiel: Sie wurden möglicherweise mehr als 48 Stunden vor der Registrierung heruntergeladen.)| [Laden Sie neue Anmeldeinformationen aus dem Recovery Services-Tresor im Azure-Portal herunter](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file). Führen Sie dann nach Bedarf die folgenden Schritte aus: <ul><li> Wenn Sie MARS bereits installiert und registriert haben, öffnen Sie die MMC-Konsole des Microsoft Azure Backup-Agents. Klicken Sie dann im Bereich **Aktionen** auf **Server registrieren**, um die Registrierung mit den neuen Anmeldeinformationen abzuschließen. <br/> <li> Wenn bei der Neuinstallation ein Fehler auftritt, wiederholen Sie den Installationsvorgang mit neuen Anmeldeinformationen.</ul> **Hinweis**: Wenn mehrere Dateien mit Tresor-Anmeldeinformationen heruntergeladen wurden, ist nur die zuletzt heruntergeladene Datei für die nächsten 48 Stunden gültig. Wir empfehlen, eine neue Datei mit Tresor-Anmeldeinformationen herunterzuladen.
| **Proxyserver/Firewall blockiert die Registrierung** <br/>oder <br/>**Keine Internetverbindung** <br/><br/> Wenn Ihr Computer oder Proxyserver nur über eine eingeschränkte Internetverbindung verfügt und Sie nicht für den Zugriff der erforderlichen URLs sorgen, schlägt die Registrierung fehl.| Führen Sie die folgenden Schritte aus:<br/> <ul><li> Stellen Sie zusammen mit Ihrem IT-Team sicher, dass das System über eine Internetverbindung verfügt.<li> Wenn Sie keinen Proxyserver haben, stellen Sie beim Registrieren des Agents sicher, dass die Proxyoption nicht aktiviert ist. [Überprüfen Sie Ihre Proxyeinstellungen](#verifying-proxy-settings-for-windows).<li> Wenn Sie eine Firewall/einen Proxyserver haben, stellen Sie zusammen mit Ihrem Netzwerkteam sicher, dass die folgenden URLs und IP-Adressen Zugriff haben:<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP-Adressen**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Wiederholen Sie nach Abschluss der vorherigen Problembehandlungsschritte den Registrierungsvorgang.<br></br> Wenn Ihre Verbindung über Azure ExpressRoute hergestellt wird, stellen Sie sicher, dass die Einstellungen wie unter [Azure ExpressRoute-Unterstützung](backup-support-matrix-mars-agent.md#azure-expressroute-support) beschrieben konfiguriert sind.
| **Antivirensoftware blockiert die Registrierung** | Wenn Sie auf dem Server Antivirensoftware installiert haben, fügen Sie den Virenscans die erforderlichen Ausschlussregeln für die folgenden Dateien und Ordner hinzu: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Ablageordner. Der Standardspeicherort ist C:\Programme\Microsoft Azure Recovery Services Agent\Scratch. <li> Ordner „Bin“ im Pfad C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Weitere Empfehlungen

- Navigieren Sie zu „C:/Windows/Temp“, und prüfen Sie, ob es mehr als 60.000 oder 65.000 Dateien mit der Erweiterung TMP gibt. Wenn dies der Fall ist, löschen Sie diese Dateien.
- Stellen Sie sicher, dass Datum und Uhrzeit des Computers der lokalen Zeitzone entsprechen.
- Stellen Sie sicher, dass [diese Websites](install-mars-agent.md#verify-internet-access) in Internet Explorer den vertrauenswürdigen Sites hinzugefügt werden.

### <a name="verifying-proxy-settings-for-windows"></a>Überprüfen der Proxyeinstellungen für Windows

1. Laden Sie „PsExec“ von der Seite [Sysinternals](/sysinternals/downloads/psexec) herunter.
1. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten den Befehl `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` aus.

   Dieser Befehl öffnet Internet Explorer.
1. Navigieren Sie zu **Extras** > **Internetoptionen** > **Verbindungen** > **LAN-Einstellungen**.
1. Überprüfen Sie die Proxyeinstellungen für das Systemkonto.
1. Wenn kein Proxy konfiguriert ist, jedoch Details für den Proxy angegeben sind, entfernen Sie die Details ein.
1. Wenn ein Proxy konfiguriert ist und die Details für den Proxy falsch sind, sorgen Sie für eine korrekte Angabe von **Proxy-IP** und **Port**.
1. Schließen Sie Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Herunterladen der Datei mit Tresoranmeldeinformationen nicht möglich

| Fehler   | Empfohlene Aktionen |
| ---     | ---    |
|Fehler beim Herunterladen der Datei mit Tresoranmeldeinformationen. (ID: 403) | <ul><li> Versuchen Sie in einem anderen Browser, die Anmeldeinformationen für den Tresor herunterzuladen, oder führen Sie die folgenden Schritte aus: <ul><li> Starten Sie Internet Explorer. Wählen Sie F12 aus. </li><li> Navigieren Sie zur Registerkarte **Netzwerk**, und löschen Sie den Cache und die Cookies. </li> <li> Aktualisieren Sie die Seite.<br></li></ul> <li> Prüfen Sie, ob das Abonnement deaktiviert/abgelaufen ist.<br></li> <li> Prüfen Sie, ob eine Firewallregel das Herunterladen blockiert. <br></li> <li> Stellen Sie sicher, dass Sie den Grenzwert für den Tresor (50 Computer pro Tresor) nicht überschritten haben.<br></li>  <li> Stellen Sie sicher, dass der Benutzer über die Azure Backup-Berechtigungen verfügt, die erforderlich sind, um Tresoranmeldeinformationen herunterzuladen und einen Server beim Tresor zu registrieren. Informationen finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung in Azure zum Verwalten von Azure Backup-Wiederherstellungspunkten](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Es konnte keine Verbindung zwischen dem Microsoft Azure Recovery Services-Agent und Microsoft Azure Backup hergestellt werden.

| Fehler  | Mögliche Ursache | Empfohlene Aktionen |
| ---     | ---     | ---    |
| <br /><ul><li>Es konnte keine Verbindung zwischen dem Microsoft Azure Recovery Services-Agent und Microsoft Azure Backup hergestellt werden. (ID: 100050) Überprüfen Sie Ihre Netzwerkeinstellungen, und stellen Sie sicher, dass Sie eine Verbindung mit dem Internet herstellen können.<li>(407) Proxyauthentifizierung erforderlich. |Ein Proxy blockiert die Verbindung. |  <ul><li>Navigieren Sie in Internet Explorer zu **Extras** > **Internetoptionen** > **Sicherheit** > **Internet**. Wählen Sie **Stufe anpassen** aus, und scrollen Sie zum Abschnitt **Dateidownload**. Wählen Sie **Aktivieren** aus.<p>Sie müssen möglicherweise auch [URLs und IP-Adressen](install-mars-agent.md#verify-internet-access) zu Ihren vertrauenswürdigen Sites in Internet Explorer hinzufügen.<li>Ändern Sie die Einstellungen dahingehend, dass ein Proxyserver verwendet wird. Geben Sie dann die Proxyserverdetails an.<li> Wenn Ihr Computer über einen eingeschränkten Internetzugang verfügt, stellen Sie sicher, dass die Firewalleinstellungen auf dem Computer oder Proxyserver diese [URLs und IP-Adressen](install-mars-agent.md#verify-internet-access) zulassen. <li>Wenn Sie Antivirensoftware auf dem Server installiert haben, schließen Sie die folgenden Dateien vom Virenscan aus: <ul><li>CBEngine.exe (statt dpmra.exe)<li>CSC.exe: (Diese Datei ist mit dem .NET Framework verknüpft.) Für jede auf dem Server installierte .NET Framework-Version gibt es eine Datei namens „CSC.exe“. Schließen Sie Dateien mit dem Namen „CSC.exe“ für alle .NET Framework-Versionen auf dem betroffenen Server aus. <li>Speicherort des Ablageordners oder Caches. <br>Der Standardspeicherort für den Ablageordner oder der Cachepfad lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“.<li>Ordner „Bin“ im Pfad C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Die angegebene Datei mit Tresoranmeldeinformationen kann nicht verwendet werden, weil sie nicht aus dem Tresor heruntergeladen wird, der diesem Server zugeordnet ist.

| Fehler  | Mögliche Ursache | Empfohlene Aktionen |
| ---     | ---     | ---    |
| Die angegebene Datei mit Tresoranmeldeinformationen kann nicht verwendet werden, weil sie nicht aus dem Tresor heruntergeladen wird, der diesem Server zugeordnet ist. (ID: 100110) Geben Sie die entsprechenden Tresoranmeldeinformationen an. | Die Datei mit den Tresoranmeldeinformationen stammt aus einem anderen Tresor als dem, bei dem dieser Server bereits registriert ist. | Stellen Sie sicher, dass der Zielcomputer und der Quellcomputer beim selben Recovery Services-Tresor registriert sind. Wenn der Zielserver bereits bei einem anderen Tresor registriert wurde, verwenden Sie die Option **Server registrieren**, um die Registrierung beim richtigen Tresor auszuführen.  

## <a name="backup-jobs-completed-with-warning"></a>Mit Warnungen abgeschlossene Sicherungsaufträge

- Wenn der MARS-Agent während der Sicherung Dateien und Ordner durchläuft, können verschiedene Bedingungen auftreten, die dazu führen können, dass die Sicherung als mit Warnungen abgeschlossen gekennzeichnet wird. Während dieser Bedingungen wird ein Auftrag als mit Warnungen abgeschlossen angezeigt. Das stellt zwar kein Problem dar, bedeutet aber, dass mindestens eine Datei nicht gesichert werden konnte. Der Auftrag hat also diese Datei übersprungen, aber alle anderen betreffenden Dateien in der Datenquelle gesichert.

  ![Mit Warnungen abgeschlossener Sicherungsauftrag](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Folgende Bedingungen können dazu führen, dass Dateien bei Sicherungsvorgängen übersprungen werden:
  - Nicht unterstützte Dateiattribute (Beispiele: in einem OneDrive-Ordner, komprimierter Stream, Analysepunkte). Eine vollständige Liste finden Sie in der [Supportmatrix](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Ein Dateisystemproblem
  - Ein anderer störender Prozess (Beispiel: Antivirensoftware, die Handles für Dateien hält, kann den Zugriff auf Dateien durch den MARS-Agent verhindern.)
  - Von einer Anwendung gesperrte Dateien  

- Der Sicherungsdienst kennzeichnet diese Dateien in der Protokolldatei mit der folgenden Namenskonvention als fehlgeschlagen: *LastBackupFailedFilesxxxx.txt* unter dem Ordner *C:\Programme\Microsoft Azure Recovery Service Agent\temp*.
- Sehen Sie sich zur Behebung dieses Problems die Protokolldatei an, um die Art des Problems zu verstehen:

  | Fehlercode             | Ursachen                                             | Empfehlungen                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Die Datei oder das Verzeichnis ist beschädigt und nicht lesbar. | Führen Sie **chkdsk** auf dem Quellvolume aus.                             |
  | 0x80070002, 0x80070003 | Das System kann die angegebene Datei nicht finden.         | [Stellen Sie sicher, dass der Ordner „scratch“ nicht voll ist.](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  Überprüfen Sie, ob das Volume mit dem konfigurierten temporären Speicherbereich vorhanden ist (nicht gelöscht wurde).  <br><br>   [Stellen Sie sicher, dass der MARS-Agent von der auf dem Computer installierten Antivirensoftware ausgeschlossen ist.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Der Zugriff wird verweigert.                                    | [Überprüfen Sie, ob der Zugriff durch Antivirensoftware oder andere Drittanbietersoftware blockiert wird.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Der Zugriff auf die Clouddatei wird verweigert.                | OneDrive-Dateien, Git-Dateien oder andere Dateien, die sich auf dem Computer im Offlinezustand befinden können |

- Sie können [einer vorhandenen Richtlinie Ausschlussregeln hinzufügen](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy), um nicht unterstützte, fehlende oder gelöschte Dateien aus Ihrer Sicherungsrichtlinie auszuschließen, sodass Sicherungen erfolgreich ausgeführt werden.

- Vermeiden Sie das Löschen und erneute Erstellen von geschützten Ordnern mit denselben Namen im Ordner der obersten Ebene. Das Löschen und erneute Erstellen dieser Ordner könnte dazu führen, dass die Sicherung mit Warnungen mit dem folgenden Fehler abgeschlossen wird: *Eine schwerwiegende Inkonsistenz wurde festgestellt. Daher können die Änderungen nicht repliziert werden.*  Wenn Sie Ordner löschen und neu erstellen müssen, sollten Sie diese Aktionen in den Unterordnern des geschützten Ordners der obersten Ebene ausführen.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Fehler beim Festlegen des Verschlüsselungsschlüssels für sichere Sicherungen

| Fehler | Mögliche Ursachen | Empfohlene Aktionen |
| ---     | ---     | ---    |
| <br />Fehler beim Festlegen des Verschlüsselungsschlüssels für sichere Sicherungen. Die Aktivierung wurde nicht vollständig abgeschlossen, aber die Verschlüsselungspassphrase wurde in der folgenden Datei gespeichert. |<li>Der Server ist bereits bei einem anderen Tresor registriert.<li>Beim Konfigurieren wurde die Passphrase beschädigt.| Heben Sie die Registrierung des Servers beim Tresor auf, und registrieren Sie ihn erneut mit einer neuen Passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>Die Aktivierung wurde nicht erfolgreich abgeschlossen.

| Fehler  | Mögliche Ursachen | Empfohlene Aktionen |
|---------|---------|---------|
|<br />Die Aktivierung wurde nicht erfolgreich abgeschlossen. Beim aktuellen Vorgang ist aufgrund eines internen Dienstfehlers [0x1FC07] ein Fehler aufgetreten. Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.     | <li> Der Ablageordner befindet sich auf einem Volume, das nicht genügend Speicherplatz aufweist. <li> Der Ablageordner wurde nicht ordnungsgemäß verschoben. <li> Die Datei „OnlineBackup.KEK“ fehlt.         | <li>Führen Sie ein Upgrade auf die [aktuelle Version](https://aka.ms/azurebackup_agent) des MARS-Agents durch.<li>Verschieben Sie den Ablageordner oder den Cachespeicherort zu einem Volume, auf dem Speicherplatz in Höhe von 5–10 % der Gesamtgröße der Sicherungsdaten verfügbar ist. Um den Cachespeicherort ordnungsgemäß zu verschieben, führen Sie die unter [Häufig gestellte Fragen zum Sichern von Dateien und Ordnern](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder) erläuterten Schritte aus.<li> Stellen Sie sicher, dass die Datei „OnlineBackup.KEK“ vorhanden ist. <br>*Der Standardspeicherort für den Ablageordner oder der Cachepfad lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“* .        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Die Verschlüsselungspassphrase ist nicht richtig konfiguriert.

| Fehler  | Mögliche Ursachen | Empfohlene Aktionen |
|---------|---------|---------|
| <br />Fehler 34506. Die auf diesem Computer gespeicherte Verschlüsselungspassphrase ist nicht richtig konfiguriert.    | <li> Der Ablageordner befindet sich auf einem Volume, das nicht genügend Speicherplatz aufweist. <li> Der Ablageordner wurde nicht ordnungsgemäß verschoben. <li> Die Datei „OnlineBackup.KEK“ fehlt.        | <li>Führen Sie ein Upgrade auf die [neueste Version](https://aka.ms/azurebackup_agent) des MARS-Agents durch.<li>Verschieben Sie den Ablageordner oder den Cachespeicherort zu einem Volume, auf dem Speicherplatz in Höhe von 5–10 % der Gesamtgröße der Sicherungsdaten verfügbar ist. Um den Cachespeicherort ordnungsgemäß zu verschieben, führen Sie die unter [Häufig gestellte Fragen zum Sichern von Dateien und Ordnern](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder) erläuterten Schritte aus.<li> Stellen Sie sicher, dass die Datei „OnlineBackup.KEK“ vorhanden ist. <br>*Der Standardspeicherort für den Ablageordner oder der Cachepfad lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“* .         |

## <a name="backups-dont-run-according-to-schedule"></a>Sicherungen werden nicht nach Zeitplan ausgeführt

Wenn geplante Sicherungen nicht automatisch ausgelöst werden, manuelle Sicherungen jedoch korrekt funktionieren, versuchen Sie es mit den folgenden Aktionen:

- Stellen Sie sicher, dass der Sicherungszeitplan für Windows Server keine Konflikte mit dem Sicherungszeitplan für Azure-Dateien und -Ordner verursacht.

- Stellen Sie sicher, dass der Status der Onlinesicherung auf **Aktiviert** eingestellt ist. Zum Überprüfen des Status führen Sie die folgenden Schritte aus:

  1. Erweitern Sie im Taskplaner die Option **Microsoft**, und wählen Sie **Onlinesicherung** aus.
  1. Doppelklicken Sie auf **Microsoft-OnlineBackup**, und wechseln Sie zur Registerkarte **Trigger**.
  1. Überprüfen Sie, ob der Status auf **Aktiviert** festgelegt ist. Wenn dies nicht der Fall ist, wählen Sie **Bearbeiten**, **Aktiviert** und dann **OK** aus.

- Stellen Sie sicher, dass das für die Ausführung der Aufgabe ausgewählte Benutzerkonto **SYSTEM** oder der Gruppe **Lokale Administratoren** auf dem Server entspricht. Zum Überprüfen des Benutzerkontos wechseln Sie zur Registerkarte **Allgemein**, und überprüfen Sie die **Sicherheitsoptionen**.

- Stellen Sie sicher, dass PowerShell 3.0 oder höher auf dem Server installiert ist. Zum Überprüfen der PowerShell-Version führen Sie den folgenden Befehl aus, und prüfen Sie, ob die Hauptversionsnummer (`Major`) 3 oder höher ist:

  `$PSVersionTable.PSVersion`

- Stellen Sie sicher, dass dieser Pfad in der `PSMODULEPATH`-Umgebungsvariablen angegeben ist:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Wenn die PowerShell-Ausführungsrichtlinie für `LocalMachine` auf `restricted` festgelegt ist, tritt bei dem PowerShell-Cmdlet, das die Sicherungsaufgabe auslöst, möglicherweise ein Fehler auf. Führen Sie die folgenden Befehle im Modus mit erhöhten Rechten aus, um die Ausführungsrichtlinie zu überprüfen und auf `Unrestricted` oder `RemoteSigned` festzulegen:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Stellen Sie sicher, dass keine MSonlineBackup-Dateien des PowerShell-Moduls fehlen oder beschädigt sind. Wenn Dateien fehlen oder beschädigt sind, führen Sie die folgenden Schritte aus:

  1. Kopieren Sie von einem Computer, auf dem der MARS-Agent ordnungsgemäß funktioniert, den Ordner „MSOnlineBackup“ im Pfad „C:\Programme\Microsoft Azure Recovery Services Agent\bin\Modules“.
  1. Fügen Sie die kopierten Dateien auf dem problematischen Computer an demselben Ordnerspeicherplatz (C:\Programme\Microsoft Azure Recovery Services Agent\bin\Modules) ein.

     Wenn bereits ein Ordner „MSOnlineBackup“ auf dem Computer vorhanden ist, fügen Sie die Dateien ein, oder ersetzen Sie alle vorhandenen Dateien.

> [!TIP]
> Um sicherzustellen, dass die vorgenommenen Änderungen durchgängig übernommen werden, starten Sie den Server nach dem Durchführen der vorherigen Schritte neu.

## <a name="resource-not-provisioned-in-service-stamp"></a>Ressource nicht im Dienststempel angegeben

Fehler | Mögliche Ursachen | Empfohlene Aktionen
--- | --- | ---
Fehler beim aktuellen Vorgang aufgrund eines internen Dienstfehlers: „Ressource nicht im Dienststempel angegeben“. Wiederholen Sie den Vorgang nach einiger Zeit. (ID: 230006) | Der geschützte Server wurde umbenannt. | <li> Benennen Sie den Server wieder in den ursprünglichen Namen um, der beim Tresor registriert ist. <br> <li> Registrieren Sie den Server mit dem neuen Namen erneut beim Tresor.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Der Auftrag konnte nicht gestartet werden, weil ein anderer Auftrag ausgeführt wurde.

Wenn Sie in der **MARS-Konsole** unter **Auftragsverlauf** eine Warnmeldung angezeigt wird, die besagt: „Auftrag konnte nicht gestartet werden, da ein anderer Auftrag in Bearbeitung ist“, dann könnte dies auf eine doppelte Instanz des vom Taskplaner ausgelösten Auftrags zurückzuführen sein.

![Der Auftrag konnte nicht gestartet werden, weil ein anderer Auftrag ausgeführt wurde.](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

So lösen Sie das Problem:

1. Starten Sie das Snap-in „Taskplaner“, indem Sie *taskschd. msc* im Fenster „Ausführen“ eingeben.
1. Navigieren Sie im linken Bereich zu **Taskplanerbibliothek** -> **Microsoft** -> **Onlinebackup**.
1. Doppelklicken Sie für jede Aufgabe in dieser Bibliothek auf die Aufgabe, um die Eigenschaften zu öffnen, und führen Sie die folgenden Schritte aus:
    1. Wechseln Sie zur Registerkarte **Einstellungen**.

         ![Registerkarte "Einstellungen"](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Ändern Sie die Option für **Folgende Regel anwenden, falls die Aufgabe bereits ausgeführt wird** in **Keine neue Instanz starten**.

         ![Ändern Sie die Regel so, dass keine neue Instanz gestartet wird.](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Behandlung von Problemen bei der Wiederherstellung

Azure Backup kann das Wiederherstellungsvolume möglicherweise auch nach einigen Minuten nicht einbinden. Möglicherweise erhalten Sie während des Vorgangs auch Fehlermeldungen. Um eine normale Wiederherstellung zu starten, gehen Sie folgendermaßen vor:

1. Brechen Sie den laufenden Bereitstellungsvorgang ab, wenn dieser mehrere Minuten lang ausgeführt wurde.

2. Prüfen Sie, ob Sie über die neueste Version des Backup-Agents verfügen. Wählen Sie zum Ermitteln der Version im Bereich **Aktionen** der MARS-Konsole die Option **Informationen zum Microsoft Azure Recovery Services-Agent** aus. Bestätigen Sie, dass die **Versionsnummer** gleich oder höher als die in [diesem Artikel](https://go.microsoft.com/fwlink/?linkid=229525) erwähnte Version ist. Wählen Sie diesen Link zum [Herunterladen der aktuellen Version](https://go.microsoft.com/fwLink/?LinkID=288905) aus.

3. Wechseln Sie zu **Geräte-Manager** > **Speichercontroller**, und suchen Sie nach **Microsoft iSCSI-Initiator**. Wenn Sie diese Option finden, fahren Sie direkt mit Schritt 7 fort.

4. Wenn der Microsoft iSCSI-Initiator-Dienst nicht vorhanden ist, suchen Sie unter **Geräte-Manager** > **Speichercontroller** nach dem Eintrag **Unbekanntes Gerät** mit der Hardware-ID **ROOT\ISCSIPRT**.

5. Klicken Sie mit der rechten Maustaste auf **Unbekanntes Gerät**, und wählen Sie **Treibersoftware aktualisieren** aus.

6. Aktualisieren Sie den Treiber durch Auswählen der Option **Automatisch nach aktueller Treibersoftware suchen**. Nach Abschluss des Updates sollte **Unbekanntes Gerät** durch **Microsoft iSCSI-Initiator** ersetzt werden:

    ![Screenshot des Geräte-Managers von Azure Backup mit hervorgehobener Option „Speichercontroller“](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Wechseln Sie zu **Task-Manager** > **Dienste (lokal)**  > **Microsoft iSCSI-Initiator-Dienst**:

    ![Screenshot des Task-Managers von Azure Backup mit hervorgehobener Option „Dienste (lokal)“](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Starten Sie den Microsoft iSCSI-Initiator-Dienst neu. Klicken Sie dazu mit der rechten Maustaste auf den Dienst, und wählen Sie **Stop** aus. Klicken Sie dann erneut mit der rechten Maustaste, und wählen Sie **Start** aus.

9. Wiederholen Sie die Wiederherstellung mit der [sofortigen Wiederherstellung](backup-instant-restore-capability.md).

Wenn bei der Wiederherstellung weiterhin ein Fehler auftritt, starten Sie Ihren Server oder Client neu. Wenn Sie keinen Neustart durchführen möchten oder auch nach einem Neustart des Servers ein Fehler bei der Wiederherstellung auftritt, führen Sie die [Wiederherstellung über einen anderen Computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) aus.

## <a name="troubleshoot-cache-problems"></a>Behandeln von Cacheproblemen

Der Sicherungsvorgang schlägt möglicherweise fehl, wenn der Cacheordner (auch als Ablageordner bezeichnet) falsch konfiguriert ist, Voraussetzungen nicht erfüllt oder der Zugriff auf ihn beschränkt ist.

### <a name="prerequisites"></a>Voraussetzungen

Damit MARS-Agent-Vorgänge erfolgreich ausgeführt werden können, muss der Cacheordner die folgenden Anforderungen erfüllen:

- [Stellen Sie sicher, dass am Speicherort des Ablageordners 5–10 % freier Volumespeicherplatz vorhanden ist](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Stellen Sie sicher, dass der Ablageordner gültig ist und auf ihn zugegriffen werden kann](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible).
- [Stellen Sie sicher, dass die Dateiattribute für den Cacheordner unterstützt werden](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported).
- [Stellen Sie sicher, dass der zugeordnete Speicherplatz für Schattenkopien für den Sicherungsvorgang ausreicht](#increase-shadow-copy-storage).
- [Stellen Sie sicher, dass keine anderen Prozesse (z. B. Antivirensoftware) den Zugriff auf den Cacheordner beschränken](#another-process-or-antivirus-software-blocking-access-to-cache-folder).

### <a name="increase-shadow-copy-storage"></a>Vergrößern des Schattenkopiespeichers

Sicherungsvorgänge können fehlschlagen, wenn nicht ausreichend Speicherplatz für Schattenkopien vorliegt, der zum Schutz der Datenquelle erforderlich ist. Zum Beheben dieses Problems erhöhen Sie den Speicherplatz für Schattenkopien auf dem geschützten Volume mit **vssadmin** (siehe unten):

- Überprüfen Sie an der Eingabeaufforderung mit erhöhten Rechten den aktuellen Speicherplatz für Schattenkopien:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Vergrößern Sie mit dem folgenden Befehl den Speicherplatz für Schattenkopien:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Der Zugriff auf den Cacheordner wird durch einen anderen Prozess oder durch Antivirensoftware blockiert

Wenn Sie auf dem Server Antivirensoftware installiert haben, fügen Sie den Virenscans die erforderlichen Ausschlussregeln für die folgenden Dateien und Ordner hinzu:  

- Ablageordner. Der Standardspeicherort ist `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`.
- Der bin-Ordner befindet sich unter `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`.
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Häufige Probleme

In diesem Abschnitt werden häufige Fehler beschrieben, die bei der Verwendung des MARS-Agents auftreten können.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Fehlermeldung | Empfohlene Maßnahme
--|--
Microsoft Azure Recovery Services Agent was unable to access backup checksum stored in scratch location (Der Microsoft Azure Recovery Services-Agent konnte nicht auf die im Ablageordner gespeicherte Sicherungsprüfsumme zugreifen.) | Führen Sie zum Beheben des Fehlers die folgenden Schritte aus, und starten Sie den Server neu. <br/> - [Überprüfen Sie, ob der Ablageordner durch ein Antivirenprogramm oder einen anderen Prozess blockiert wird](#another-process-or-antivirus-software-blocking-access-to-cache-folder).<br/> - [Überprüfen Sie, ob der Ablageordner gültig und für den MARS-Agent zugänglich ist](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible).

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Fehlermeldung | Empfohlene Maßnahme
--|--
Microsoft Azure Recovery Services Agent was unable to access the scratch location to initialize VHD (Der Microsoft Azure Recovery Services-Agent konnte nicht auf den Ablageordner zugreifen, um die virtuelle Festplatte zu initialisieren.) | Führen Sie zum Beheben des Fehlers die folgenden Schritte aus, und starten Sie den Server neu. <br/> - [Überprüfen Sie, ob der Ablageordner durch ein Antivirenprogramm oder einen anderen Prozess blockiert wird](#another-process-or-antivirus-software-blocking-access-to-cache-folder).<br/> - [Überprüfen Sie, ob der Ablageordner gültig und für den MARS-Agent zugänglich ist](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible).

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Fehlermeldung | Empfohlene Maßnahme
--|--
Backup failed due to insufficient storage in volume  where the scratch folder is located (Die Sicherung ist aufgrund von unzureichendem Speicher im Volume des Ablageordners fehlgeschlagen.) | Führen Sie zum Beheben des Problems die folgenden Schritte aus, und wiederholen Sie den Vorgang:<br/>- [Stellen Sie sicher, dass der MARS-Agent die aktuelle Version aufweist](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).<br/> - [Überprüfen und beheben Sie Speicherprobleme, die den temporären Speicherbereich für Sicherungen beeinträchtigen](#prerequisites).

### <a name="salbitmaperror"></a>SalBitmapError

Fehlermeldung | Empfohlene Maßnahme
--|--
Es wurden keine Änderungen in einer Datei gefunden. Dies kann verschiedene Ursachen haben. Wiederholen Sie den Vorgang | Führen Sie zum Beheben des Problems die folgenden Schritte aus, und wiederholen Sie den Vorgang:<br/> - [Stellen Sie sicher, dass der MARS-Agent die aktuelle Version aufweist](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409). <br/> - [Überprüfen und beheben Sie Speicherprobleme, die den temporären Speicherbereich für Sicherungen beeinträchtigen](#prerequisites).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Einzelheiten finden Sie unter [Sichern von Windows Server mit dem Azure Backup-Agent](tutorial-backup-windows-server-to-azure.md).
- Informationen zum Wiederherstellen einer Sicherung finden Sie unter [Wiederherstellen von Dateien auf einem Windows-Computer](backup-azure-restore-windows-server.md).
