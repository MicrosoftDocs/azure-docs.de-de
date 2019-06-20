---
title: Behandeln von Problemen mit dem Azure Backup-Agent
description: Behandeln von Problemen mit der Installation und Registrierung des Azure Backup-Agents
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: f36442c5e26391f410eeb5e39a7485da7199bdad
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243448"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Problembehandlung beim Microsoft Azure Recovery Services-Agent (MARS)

Erfahren Sie, wie Sie möglicherweise auftretende Fehler bei der Konfiguration, Registrierung, Sicherung und Wiederherstellung beheben.

## <a name="basic-troubleshooting"></a>Grundlegendes zur Problembehandlung

Wir empfehlen, dass Sie die nachstehende Prüfung durchführen, bevor Sie mit der Problembehandlung beim Microsoft Azure Recovery Services-Agent (MARS) beginnen:

- [Sicherstellen, dass der Microsoft Azure Recovery Services-Agent (MARS) auf dem neuesten Stand ist](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Sicherstellen, dass zwischen dem MARS-Agent und Azure Netzwerkkonnektivität besteht](https://aka.ms/AB-A4dp50)
- Vergewissern Sie sich, dass Microsoft Azure Recovery Services ausgeführt wird (auf der Dienstkonsole). Führen Sie einen Neustart durch, und wiederholen Sie den Vorgang, falls dies erforderlich ist.
- [Sicherstellen, dass am Speicherort des Ablageordners 5 - 10% freier Volumespeicherplatz vorhanden ist](https://aka.ms/AB-AA4dwtt)
- [Überprüfen, ob ein anderer Prozess oder Antivirensoftware in Azure Backup eingreift](https://aka.ms/AB-AA4dwtk)
- [Geplante Sicherung nicht erfolgreich, manuelle Sicherung erfolgreich](https://aka.ms/ScheduledBackupFailManualWorks)
- Sicherstellen, dass Ihr Betriebssystem über die neuesten Updates verfügt
- [Sicherstellen, dass nicht unterstützte Laufwerke und Dateien mit nicht unterstützten Attributen aus der Sicherung ausgeschlossen werden](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Vergewissern Sie sich, dass die **Systemuhr** des geschützten Systems auf die richtige Zeitzone festgelegt ist. <br>
- [Stellen Sie sicher, dass auf dem Server mindestens .NET Framework Version 4.5.2 oder höher installiert ist.](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Wenn Sie **Ihren Server erneut in einem Tresor registrieren** möchten: <br>
  - Stellen Sie sicher, dass der Agent auf dem Server deinstalliert und aus dem Portal gelöscht wurde. <br>
  - Verwenden Sie dieselbe Passphrase, die ursprünglich zum Registrieren des Servers verwendet wurde. <br>
- Sorgen Sie bei einer Offlinesicherung dafür, dass Azure PowerShell, Version 3.7.0, sowohl auf dem Quell- als auch auf dem Zielcomputer installiert ist, bevor Sie mit dem Offlinesicherungsvorgang beginnen.
- [Aspekte, wenn der Backup-Agent auf einem virtuellen Azure-Computer ausgeführt wird](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>Es wurden ungültige Tresoranmeldeinformationen angegeben.

| Fehlerdetails | Mögliche Ursachen | Empfohlene Aktionen |
| ---     | ---     | ---    |
| **Fehler** </br> *Es wurden ungültige Tresoranmeldeinformationen angegeben. Die Datei ist entweder beschädigt oder enthält nicht die aktuellen Anmeldeinformationen für den Wiederherstellungsdienst. (ID: 34513)* | <ul><li> Die Tresoranmeldeinformationen sind ungültig (d.h. sie wurden mehr als 48 Stunden vor der Registrierung heruntergeladen).<li>Der MARS-Agent kann keine Dateien in das temporäre Windows-Verzeichnis herunterladen. <li>Die Tresoranmeldeinformationen befinden sich an einem Netzwerkspeicherort. <li>TLS 1.0 ist deaktiviert.<li> Ein konfigurierter Proxyserver blockiert die Verbindung. <br> |  <ul><li>Laden Sie die neuen Tresoranmeldeinformationen herunter. (**Hinweis**: Wenn zuvor mehrere Dateien mit Tresoranmeldeinformationen heruntergeladen wurden, ist nur die zuletzt heruntergeladene Datei innerhalb von 48 Stunden gültig.) <li>Starten Sie **IE** > **Einstellungen** > **Internetoptionen** > **Sicherheit** > **Internet**. Wählen Sie als Nächstes **Stufe anpassen** aus, und scrollen Sie zum Abschnitt zum Dateidownload. Wählen Sie anschließend **Aktivieren** aus.<li>Möglicherweise müssen Sie diese Websites auch in IE unter [Vertrauenswürdige Sites](https://docs.microsoft.com/azure/backup/backup-configure-vault#verify-internet-access) hinzufügen.<li>Ändern Sie die Einstellungen dahingehend, dass ein Proxyserver verwendet wird. Geben Sie dann die Proxyserverdetails an. <li> Passen Sie Datum und Uhrzeit an die Ihres Computers an.<li>Wenn Sie eine Fehlermeldung erhalten, die darauf hinweist, dass Dateidownloads unzulässig sind, befindet sich wahrscheinlich viele Dateien im Verzeichnis „C:/Windows/Temp“.<li>Navigieren Sie zu „C:/Windows/Temp“, und prüfen Sie, ob es mehr als 60.000 oder 65.000 Dateien mit der Erweiterung TMP gibt. Wenn dies der Fall ist, löschen Sie diese Dateien.<li>Vergewissern Sie sich, dass .NET Framework 4.6.2 installiert ist. <li>Wenn Sie TLS 1.0 aufgrund von PCI-Compliance deaktiviert haben, lesen Sie die Informationen auf dieser [Seite zur Problembehandlung](https://support.microsoft.com/help/4022913). <li>Wenn Sie eine Antivirensoftware auf dem Server installiert haben, schließen Sie die folgenden Dateien von der Antivirenprüfung aus: <ul><li>CBengine.exe<li>CSC.exe (Diese Datei ist mit dem .NET Framework verknüpft. Auf dem Server ist eine Datei namens „CSC.exe“ für jede .NET-Version installiert. Schließen Sie sämtliche mit .NET Framework-Versionen verknüpfte Dateien mit dem Namen „CSC.exe“ auf dem betroffenen Server aus. <li>Speicherort des Ablageordners oder Caches <br>*Der Standardspeicherort für den Ablageordner oder der Pfad für den Cachespeicherort lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“* .<br><li>Der Ordner „Bin“ C:\Program Files\Microsoft Azure Recovery Services Agent\Bin

## <a name="unable-to-download-vault-credential-file"></a>Herunterladen der Datei mit Tresoranmeldeinformationen nicht möglich

| Fehlerdetails | Empfohlene Aktionen |
| ---     | ---    |
|Fehler beim Herunterladen der Datei mit Tresoranmeldeinformationen. (ID: 403) | <ul><li> Versuchen Sie, die Tresoranmeldeinformationen über einen anderen Browser herunterzuladen, oder führen Sie die folgenden Schritte aus: <ul><li> Starten Sie Internet Explorer, und drücken Sie F12. </li><li> Wechseln Sie zur Registerkarte **Netzwerk**, um den Internet Explorer-Cache und Cookies zu löschen. </li> <li> Aktualisieren Sie die Seite.<br>(ODER)</li></ul> <li> Prüfen Sie, ob das Abonnement deaktiviert/abgelaufen ist.<br>(ODER)</li> <li> Prüfen Sie, ob eine Firewallregel das Herunterladen der Datei mit den Tresoranmeldeinformationen blockiert. <br>(ODER)</li> <li> Stellen Sie sicher, dass Sie den Grenzwert für den Tresor (50 Computer pro Tresor) nicht überschritten haben.<br>(ODER)</li>  <li> Stellen Sie sicher, dass der Benutzer über die Azure Backup-Berechtigungen zum Herunterladen von Tresoranmeldeinformationen und zum Registrieren des Servers beim Tresor verfügt. Informationen dazu finden Sie in [diesem Artikel](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Es konnte keine Verbindung zwischen dem Microsoft Azure Recovery Services-Agent und Microsoft Azure Backup hergestellt werden.

| Fehlerdetails | Mögliche Ursachen | Empfohlene Aktionen |
| ---     | ---     | ---    |
| **Fehler** <br /><ol><li>*Die Verbindung des Microsoft Azure Recovery Services-Agents mit Microsoft Azure Backup konnte nicht hergestellt werden. (ID: 100050) Überprüfen Sie Ihre Netzwerkeinstellungen, und stellen Sie sicher, dass Sie eine Verbindung mit dem Internet herstellen können.*<li>*(407) Proxyauthentifizierung erforderlich* |Der Proxyserver blockiert die Verbindung. |  <ul><li>Starten Sie **IE** > **Einstellungen** > **Internetoptionen** > **Sicherheit** > **Internet**. Wählen Sie als Nächstes **Stufe anpassen** aus, und scrollen Sie zum Abschnitt zum Dateidownload. Wählen Sie **Aktivieren** aus.<li>Möglicherweise müssen Sie diese Websites auch in IE unter [Vertrauenswürdige Sites](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins) hinzufügen.<li>Ändern Sie die Einstellungen dahingehend, dass ein Proxyserver verwendet wird. Geben Sie dann die Proxyserverdetails an.<li> Wenn Ihr Computer über einen eingeschränkten Internetzugang verfügt, stellen Sie sicher, dass die Firewalleinstellungen auf dem Computer oder Proxy diese [URLs](backup-configure-vault.md#verify-internet-access) und [IP-Adressen](backup-configure-vault.md#verify-internet-access) zulassen. <li>Wenn Sie eine Antivirensoftware auf dem Server installiert haben, schließen Sie die folgenden Dateien von der Antivirenprüfung aus. <ul><li>CBEngine.exe (statt dpmra.exe)<li>CSC.exe: (Diese Datei ist mit dem .NET Framework verknüpft.) Auf dem Server ist eine Datei namens „CSC.exe“ für jede .NET-Version installiert. Schließen Sie sämtliche mit .NET Framework-Versionen verknüpfte Dateien mit dem Namen „CSC.exe“ auf dem betroffenen Server aus. <li>Speicherort des Ablageordners oder Caches <br>*Der Standardspeicherort für den Ablageordner oder der Pfad für den Cachespeicherort lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“* .<li>Der Ordner „Bin“ C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Fehler beim Festlegen des Verschlüsselungsschlüssels für sichere Sicherungen

| Fehlerdetails | Mögliche Ursachen | Empfohlene Aktionen |
| ---     | ---     | ---    |
| **Fehler** <br />*Fehler beim Festlegen des Verschlüsselungsschlüssels für sichere Sicherungen. Die Aktivierung wurde nicht erfolgreich abgeschlossen, doch die Verschlüsselungspassphrase wurde in der folgenden Datei gespeichert.* |<li>Der Server ist bereits bei einem anderen Tresor registriert.<li>Beim Konfigurieren wurde die Passphrase beschädigt.| Heben Sie die Registrierung des Servers beim Tresor auf, und registrieren Sie ihn erneut mit einer neuen Passphrase.

## <a name="the-activation-did-not-complete-successfully"></a>Die Aktivierung wurde nicht erfolgreich abgeschlossen.

| Fehlerdetails | Mögliche Ursachen | Empfohlene Aktionen |
|---------|---------|---------|
|**Fehler** <br />*Die Aktivierung wurde nicht erfolgreich abgeschlossen. Beim aktuellen Vorgang ist aufgrund eines internen Dienstfehlers [0x1FC07] ein Fehler aufgetreten. Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support.*     | <li> Der Ablageordner befindet sich auf einem Volume, das nicht genügend Speicherplatz aufweist. <li> Der Ablageordner wurde nicht ordnungsgemäß an einen anderen Speicherort verschoben. <li> Die Datei „OnlineBackup.KEK“ fehlt.         | <li>Führen Sie ein Upgrade auf die [neueste Version](https://aka.ms/azurebackup_agent) des MARS-Agents durch.<li>Verschieben Sie den Ablageordner oder den Cachespeicherort in ein Volume, auf dem Speicherplatz in Höhe von 5–10 % der Gesamtgröße der Sicherungsdaten verfügbar ist. Um den Cachespeicherort ordnungsgemäß zu verschieben, führen Sie die Schritte unter [Fragen zum Azure Backup-Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup) durch.<li> Stellen Sie sicher, dass die Datei „OnlineBackup.KEK“ vorhanden ist. <br>*Der Standardspeicherort für den Ablageordner oder der Pfad für den Cachespeicherort lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“* .        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Die Verschlüsselungspassphrase ist nicht richtig konfiguriert.

| Fehlerdetails | Mögliche Ursachen | Empfohlene Aktionen |
|---------|---------|---------|
|**Fehler** <br />*Fehler 34506. Die für diesen Computer gespeicherte Verschlüsselungspassphrase ist nicht ordnungsgemäß konfiguriert*.    | <li> Der Ablageordner befindet sich auf einem Volume, das nicht genügend Speicherplatz aufweist. <li> Der Ablageordner wurde nicht ordnungsgemäß an einen anderen Speicherort verschoben. <li> Die Datei „OnlineBackup.KEK“ fehlt.        | <li>Führen Sie ein Upgrade auf die [neueste Version](https://aka.ms/azurebackup_agent) des MARS-Agents durch.<li>Ändern Sie den Speicherort für den Ablageordner oder Cache in ein Volume, auf dem Speicherplatz von 5-10 % der Gesamtgröße der Sicherungsdaten zur Verfügung steht. Um den Cachespeicherort ordnungsgemäß zu verschieben, führen Sie die Schritte unter [Fragen zum Azure Backup-Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup) durch.<li> Stellen Sie sicher, dass die Datei „OnlineBackup.KEK“ vorhanden ist. <br>*Der Standardspeicherort für den Ablageordner oder der Pfad für den Cachespeicherort lautet „C:\Programme\Microsoft Azure Recovery Services Agent\Scratch“* .         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Sicherungen werden nicht gemäß dem Zeitplan ausgeführt.
Wenn geplante Sicherungen nicht automatisch ausgelöst werden, während manuelle Sicherungen problemlos funktionieren, führen Sie die folgenden Aktionen aus

- Stellen Sie sicher, dass der Sicherungszeitplan für Windows Server mit dem Sicherungszeitplan für Azure-Dateien und -Ordner nicht in Konflikt steht.
- Wechseln Sie zu **Systemsteuerung** > **Verwaltung** > **Taskplaner**. Erweitern Sie **Microsoft**, und wählen Sie **Onlinesicherung** aus. Doppelklicken Sie auf **Microsoft-OnlineBackup**, und wechseln Sie zur Registerkarte **Trigger**. Vergewissern Sie sich, dass der Status der Aufgabe **Aktiviert** ist. Wenn dies nicht der Fall ist, wählen Sie **Bearbeiten** aus, aktivieren Sie das Kontrollkästchen **Aktiviert**, und klicken Sie auf **OK**. Navigieren Sie auf der Registerkarte **Allgemein** zu **Sicherheitsoptionen**, und stellen Sie sicher, dass das für die Ausführung der Aufgabe ausgewählte Benutzerkonto **SYSTEM** oder die Gruppe **Lokale Administratoren** auf dem Server ist.

- Überprüfen Sie, ob PowerShell 3.0 oder höher auf dem Server installiert ist. Um die PowerShell-Version zu überprüfen, führen Sie den folgenden Befehl aus, und überprüfen Sie, ob der *Hauptteil* der Versionsnummer mindestens 3 ist.

  `$PSVersionTable.PSVersion`

- Überprüfen Sie, ob der folgende Pfad Teil der Umgebungsvariable *PSMODULEPATH* ist.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Wenn die PowerShell-Ausführungsrichtlinie für *LocalMachine* auf „Eingeschränkt“ festgelegt ist, tritt bei dem PowerShell-Cmdlet, das die Sicherungsaufgabe auslöst, möglicherweise ein Fehler auf. Führen Sie die folgenden Befehle im Modus mit erhöhten Rechten aus, um die Ausführungsrichtlinie zu überprüfen und auf *Unrestricted* oder *RemoteSigned* festzulegen.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

> [!TIP]
> Um sicherzustellen, dass die vorgenommenen Änderungen durchgängig übernommen werden, starten Sie den Server nach dem Durchführen der oben genannten Schritte neu.


## <a name="troubleshoot-restore-issues"></a>Problembehandlung bei der Wiederherstellung

Azure Backup kann das Wiederherstellungsvolume möglicherweise auch nach einigen Minuten nicht einbinden. Sie erhalten außerdem möglicherweise während des Vorgangs Fehlermeldungen. Um eine normale Wiederherstellung zu starten, gehen Sie folgendermaßen vor:

1.  Brechen Sie den laufenden Einbindungsvorgang ab, falls dieser bereits mehrere Minuten lang ausgeführt wird.

2.  Überprüfen Sie, ob Sie über die neueste Version des Backup-Agents verfügen. Um die Version herauszufinden, wählen Sie im Bereich **Aktionen** der MARS-Konsole die Option **About Microsoft Azure Recovery Services Agent** (Info zum Microsoft Azure Recovery Services-Agent) aus. Bestätigen Sie, dass die **Versionsnummer** gleich oder höher als die in [diesem Artikel](https://go.microsoft.com/fwlink/?linkid=229525) erwähnte Version ist. Sie können die neueste Version [hier](https://go.microsoft.com/fwLink/?LinkID=288905) herunterladen.

3.  Wechseln Sie zu **Geräte-Manager** > **Speichercontroller**, und suchen Sie **Microsoft iSCSI-Initiator**. Wenn Sie diese Option nicht finden können, fahren Sie direkt mit Schritt 7 fort.

4.  Wenn der Microsoft iSCSI-Initiator-Dienst nicht vorhanden ist, suchen Sie unter **Geräte-Manager** > **Speichercontroller** nach dem Eintrag **Unbekanntes Gerät** mit der Hardware-ID **ROOT\ISCSIPRT**.

5.  Klicken Sie mit der rechten Maustaste auf **Unbekanntes Gerät**, und wählen Sie **Treibersoftware aktualisieren** aus.

6.  Aktualisieren Sie den Treiber durch Auswählen der Option **Automatisch nach aktueller Treibersoftware suchen**. Nach Abschluss des Updates sollte **Unbekanntes Gerät** durch **Microsoft iSCSI-Initiator** ersetzt werden, wie nachstehend dargestellt.

    ![Screenshot des Geräte-Managers von Azure Backup mit hervorgehobener Option „Speichercontroller“](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Wechseln Sie zu **Task-Manager** > **Dienste (lokal)**  > **Microsoft iSCSI-Initiator-Dienst**.

    ![Screenshot des Task-Managers von Azure Backup mit hervorgehobener Option „Dienste (lokal)“](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Starten Sie den Microsoft iSCSI-Initiator-Dienst neu. Klicken Sie dazu mit der rechten Maustaste auf den Dienst, wählen Sie **Beenden** aus, klicken Sie noch einmal mit der rechten Maustaste, und wählen Sie **Starten** aus.

9.  Wiederholen Sie die Wiederherstellung mit der [**sofortigen Wiederherstellung**](backup-instant-restore-capability.md).

Wenn bei der Wiederherstellung weiterhin ein Fehler auftritt, starten Sie Ihren Server oder Client neu. Wenn Sie keinen Neustart durchführen möchten oder auch nach einem Neustart des Servers ein Fehler bei der Wiederherstellung auftritt, führen Sie die Wiederherstellung über einen anderen Computer aus. Führen Sie die Schritte in [diesem Artikel](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) aus.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie die Einzelheiten zum [Sichern von Windows Server mit dem Azure Backup-Agent](tutorial-backup-windows-server-to-azure.md).
* Informationen zum Wiederherstellen einer Sicherung finden Sie im Artikel zum [Wiederherstellen von Dateien auf einem Windows-Computer](backup-azure-restore-windows-server.md).
